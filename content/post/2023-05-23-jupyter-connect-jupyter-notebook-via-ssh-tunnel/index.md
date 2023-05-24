---
title: '[Jupyter] Connect Jupyter notebook via SSH Tunnel'
author: Zhenguo Zhang
date: '2023-05-23'
slug: jupyter-connect-jupyter-notebook-via-ssh-tunnel
categories:
  - Computing
  - Tips
tags:
  - Jupyter
  - SSH
description: ''
featured_image: ''
images: []
comment: yes
---

One can install Jupyter notebook on an AWS EC2 instance via the following command

```bash
pip install jupyterlab
```

And then start it via the command

```bash
jupyter-lab --no-browser
```

However, to access it from a client computer may need some tweaking in both
AWS EC2 instance and the SSh connection.

## AWS EC2 setting

- Make sure the EC2 instance's inbound port in security groups include the jupyter
  server's port, normally 8888
  
## SSH setting

- use the following command or alike connect to the EC2 instance
  ```bash
  # change port number accordingly
  ssh -i "key.pem" ubuntu@ec2-ip-address -L localhost:8888:localhost:8888
  ```
  
  This command opens a SSH tunnel, when one tries to access the port 8888 at the
  client machine, it will be forwarded to the remote EC2 instance in the same
  port 8888.
  
- after successully connecting to it, run the following command:
  ```bash
  tmux new -s jupyter
  jupyter-lab --no-browser
  ```
  
  These commands will start a jupyter server on the EC2 instance within the tmux
  session (this will avoid the stop of the server when connection is disrupted)
  
- now in the browser input 'localhost:8888' to access the jupyter server. You may
  need password or token to access it, depending on jupyter server configuration.
  

This ssh tunnel forwarding is essential when AWS EC2 instance is run in a private
network, but may not be needed for the ones with public IPs.

## References

- AWS jupyter tunnel: https://gist.github.com/jakechen/faf0500132d46d83517004bbfedbe5de

- Jupyter notebook configuration: https://docs.aws.amazon.com/dlami/latest/devguide/setup-jupyter-config.html

- AWS jupyter setup: https://towardsdatascience.com/setting-up-and-using-jupyter-notebooks-on-aws-61a9648db6c5
