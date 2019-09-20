---
title: '[Bioinfo] Nextflow framework'
author: Zhenguo Zhang
date: '2019-09-19'
slug: bioinfo-nextflow-framework
categories:
  - Computing
  - Biology
tags:
  - Programming
---

Recently, I was introduced to an amazing pipeline-writing framework --
[nextflow](https://www.nextflow.io/). It has the following features:

1. it abstracts a pipeline, which can be written in any language
and run on many computing platforms such as Linux Slurm, PBS, AWS, etc.

2. it is composed of many processes, the executing order of which
is determined by the dependencies of the input and output channels
of each process.

3. the nextflow language is extension of the [Groovy](http://groovy-lang.org/documentation.html)
programming language, which is a programming language for Java
virtual machine. The language syntax is most similar to Python.

4. it allows parallel processing and caching (thus resuming from middle
processes).

The key component here is connecting different processes using
channels, so that the input and output of a process can be well
coordinated between processes.

This seems a promising pipeline-writing program, but it needs some
time to learn the programming language.
