---
title: '[Nextflow] Nextflow AWS options: where they take effect'
author: Zhenguo Zhang
date: '2026-07-25'
slug: nextflow-aws-options-where-they-take-effect
categories:
  - Computing
  - Software
tags:
  - AWS
  - Nextflow
description: 'A guide to understanding how Nextflow AWS config options map to S3 transfer layers between the head node and AWS Batch job containers.'
featured_image: 'featured.jpg'
images: ["featured.jpg"]
comment: true
---

When running Nextflow pipelines on AWS Batch, you might expect that changing S3 configuration options under `aws.client.*` (such as `maxConnections`) would tune the data transfer performance during task input/output staging. However, Nextflow's documentation is unclear about how these options affect S3 data transfer. You might change parameters like `aws.client.maxConnections` only to find they have absolutely no effect on input data staging by Nextflow tasks.

By leveraging AI's power to analyze the Nextflow codebase, I finally dug into the code and figured out why: S3 data transfers actually run across two entirely different environments—the **Head Node** running Nextflow and the **EC2 Execution Instances** (started by AWS Batch for running individual tasks).

---

### The Two S3 Transfer Layers

Data staging is divided into two separate processes running on different machines:

| Layer | Environment (Machine) | Run by | Governed by | Key Operations |
|---|---|---|---|---|
| **Head Node Transfer** | Head Node (Nextflow host) | Nextflow JVM (AWS SDK v2 CRT) | `aws.client.*` | `publishDir`, writing `.command.*`, `bin/` upload, gathering outputs |
| **In-Container Staging** | EC2 Instance (Batch container) | `aws s3` CLI / `s5cmd` (Classic) | `aws.batch.*` + subset of `aws.client.*` | Staging inputs, unstaging outputs |

*Note: Options set for one layer do not propagate to the other unless explicitly translated.*

---

### How Staging Works in Classic Mode

In classic (non-Fusion) mode, Nextflow writes the staging script from the head node, but the EC2 container does the actual staging work:
1. `AwsBatchScriptLauncher` embeds bash snippets into the `.command.run` script.
2. Inside the container, the Batch executor downloads the runner script: `aws s3 cp .command.run - | bash`.
3. The script defines download/upload functions (`nxf_s3_download`, `nxf_s3_upload`) using the container's `aws` CLI.
4. Input files are staged: `downloads=(...)` lists files, and `nxf_parallel` runs transfers concurrently.
5. The container runs the user task.
6. Output files are unstaged: `uploads=(...)` runs to push results back to S3.

---

### Which Options Reach the EC2 Container?

Most `aws.client.*` settings only configure the head node's JVM S3 client and **never** reach the EC2 instance. The following options do **not** affect in-container staging in classic mode:
* `minimumPartSize`, `multipartThreshold`
* `maxConnections`, `maxConcurrency`, `targetThroughputInGbps`
* `maxDownloadHeapMemory`, `maxNativeMemory`
* `maxErrorRetry`, `connectionTimeout`, `socketTimeout`, `proxy*`

Instead, the only options that affect the EC2 instance's S3 CLI transfers are those explicitly translated into CLI flags by `S3BashLib`:

| Option | Source Scope | Effect on EC2 Instance / Staging Script |
|---|---|---|
| `maxParallelTransfers` | `aws.batch` | Caps concurrency in the `nxf_parallel` script function (default: 4) |
| `delayBetweenAttempts` | `aws.batch` | Controls the retry sleep duration in `nxf_cp_retry` |
| `maxTransferAttempts` | `aws.batch` | Sets retry count in `nxf_cp_retry` and defines `AWS_MAX_ATTEMPTS` |
| `cliPath` | `aws.batch` | Defines the path to the `aws` binary and sets the `--region` flag |
| `retryMode` | `aws.batch` | Injects the `AWS_RETRY_MODE` environment variable |
| `s5cmdPath` | `aws.batch` | Uses `s5cmd` instead of `aws s3` inside the container |
| `forceGlacierTransfer`| `aws.batch` | Adds `--force-glacier-transfer` to `aws s3 cp` downloads |
| `storageClass` | `aws.client` | Adds `--storage-class` to `aws s3 cp` uploads |
| `storageEncryption` | `aws.client` | Adds `--sse` to `aws s3 cp` uploads |
| `storageKmsKeyId` | `aws.client` | Adds `--sse-kms-key-id` to `aws s3 cp` uploads |
| `s3Acl` | `aws.client` | Adds `--acl` to `aws s3 cp` uploads |
| `requesterPays` | `aws.client` | Adds `--request-payer requester` |
| `debug` | `aws.client` | Appends the `--debug` flag to CLI calls |

---

### Controlling Transfer Rates

A common issue on small EC2 instances is memory or network exhaustion during staging. Currently, **Nextflow does not provide an option to inject arbitrary CLI flags** (such as `--max-bandwidth`) into the generated S3 CLI commands. 

If you need fine-grained transfer rate control to prevent instances from crashing, you must configure this outside of Nextflow (for instance, via custom S3 configurations or instance-level limits on the execution host).

---

### A Note on Fusion Mode

When Fusion is enabled, **no** `aws s3` CLI staging occurs inside the container. Fusion mounts S3 directly as a POSIX filesystem on the EC2 instance and does its own transfers. Staging is therefore governed by Fusion/wave settings instead of standard Nextflow `aws` S3 CLI configs.
