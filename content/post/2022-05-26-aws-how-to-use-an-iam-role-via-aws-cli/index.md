---
title: '[AWS] How to use an IAM role via AWS CLI'
author: Zhenguo Zhang
date: '2022-05-26'
slug: aws-how-to-use-an-iam-role-via-aws-cli
categories:
  - Computing
  - Software
  - Tips
tags:
  - AWS
description: ''
featured_image: ''
images: []
comment: yes
---

There are situations that one may have multiple AWS
accounts (or IAM roles) and need switch among them sometimes.
This is doable via the AWS website by clicking the
username button at the top-right and then click
*switch role*.

How to do this in the command line?

To switch IAM roles via AWS CLI, one can do the
following:

### Edit the file *~/.aws/credentials*

The file *~/.aws/credentials* stores the AWS credentials.
It may include multiple profiles, in
the following format:

```yaml
[default]
aws_access_key_id = AAAAHHHSJJJKKJJDD
aws_secret_access_key = GGSHHHKKJeesDLAJkkEDJLSJDALjjzzJDLJsdsAJ 

[project_role]
aws_access_key_id = HJJKKJJKKKKLSGGGS
aws_secret_access_key = KKssdhhJJJJslalkKKKYYYGHHHHGGSSLooaPPsxs
```

So this file contatins two profiles: default and
project_role, which may correspond to different
accounts. If the same credentials for the different
accounts, one can keep only one profile here.

### Edit the file *~/.aws/config*

Then in *~/.aws/config*, one need to add a
corresponding profile using the credentials
and IAW roles. As an example below:

```yaml
[default]
region = us-east-1
output = table

[profile project]
output = table
region = us-west-1
role_arn = arn:aws:iam::1234567891012:role/new_role
# specify the profile name in ~/.aws/credentials
source_profile = project_role
```

In the above file, the *default* profile uses the
default setting in the credentials, and the
*project* profile uses the credentials set in
the profile *project_role*. DO NOT forget the
keyword 'profile' when defining a new profile.

Here the parameter *role_arn* provides the arn
for the IAM role in a different account, and it
can be found by searching *IAM* service, then
*Roles* after switching to that IAM role in
the AWS web console. It may show something like
*arn:aws:sts::123456789012:assumed-role/<role-name>/<session-id>*

Here the *role-name* and the account numbers are needed
to set the parameter *role_arn* as above.

To learn more about the config file setting, one
can refer to [here](https://docs.aws.amazon.com/cli/latest/topic/config-vars.html).

### Switch the role in command line

With the above settings done, we can use the following
command to swith roles on the fly.

```bash
# use the profile 'project'
aws --profile project s3 ls
# get account info
aws --profile project sts get-caller-identity
```

As you can see, we just need to provide a parameter
*--profile* to specify the new IAM role, which will
allow us to access the resources assigned to this
role.

## References

1. [Using an IAM role in the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-role.html).

2. [How to switch role in aws cli](https://serverfault.com/questions/933078/how-to-switch-role-in-aws-cli).

3. [AWS CLI configuration variables](https://docs.aws.amazon.com/cli/latest/topic/config-vars.html).

