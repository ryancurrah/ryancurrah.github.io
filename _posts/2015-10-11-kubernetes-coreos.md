---
layout: post
title: Batching Remote Executions with Fabric in Parallel
categories: 
  - sysadmin
  - tech
tags: 
  - fabric
  - python
  - ssh
  - remoteexecution
description: How to batch remote executions using Fabric.
comments: true
published: true
---

## Batching SSH Remote Execution with Fabric

I know Fabric isnt a new project but I wanted to write about batching remote executions. The reason why I resorted 
to Fabric is because SaltStack remote execution has been unreliable for us. It took a few tries but eventually I was 
able to get it right. Theres a couple things to consider when running executions against a large set of hosts.

1. Unexpcted prompts
2. Sudo
3. Parallel executions
4. Batching executions
5. Skipping unresponsive hosts


**Prompts** - When running an execution there is a chance you may get an unexpected prompted, instead of hanging the execution Fabric has an option to exit on prompts. <http://docs.fabfile.org/en/latest/usage/env.html#abort-on-prompts>

**Sudo** - Automatically entering passwords using sudo there is an option. <http://docs.fabfile.org/en/latest/api/core/operations.html#fabric.operations.sudo>

**Parallel executions** - If you have a large set of hosts to execute against you will want to run the executions in parallel. <http://docs.fabfile.org/en/latest/usage/env.html#parallel>

**Batching executions** - In order to not overrun your processor or remote systems with large executions you can specify the max pool size. <http://docs.fabfile.org/en/latest/usage/fab.html#cmdoption-z>

**Skipping unresponsive hosts** - If a host is unrepsonive you may want to skip it. <http://docs.fabfile.org/en/latest/usage/env.html#skip-bad-hosts>


### Create a hosts file
**hosts.txt**
{% highlight python %}
host1
host2
host3
host4
host5
host5
host5
{% endhighlight %}


### Example Fabfile

**statesls_fabfile.py**
{% highlight python %}
from fabric.api import *


def set_hosts():
    env.hosts = open('hosts.txt', 'r').read().split()


def commvault_join():
    sudo('sudo salt-call state.sls commvault.join')

{% endhighlight %}
