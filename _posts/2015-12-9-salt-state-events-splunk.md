---
layout: post
title: How to Get SaltStack State Events Into Log Aggregation
categories:
  - tech
tags:
  - saltstack
  - stateevents
  - tcpreturner
description: Describes how to setup a salt-master to forward minion state.sls and state.highstate events to a log aggreator
comments: true
published: true
---

## Intro

If you want to start getting an idea of the success of salt state results in your environment a good option is to use this custom returner that will return each salt state result as an event to a log aggregation tool like Splunk or ELK. Once you get these events into one of these tools you can start getting some valuable insight into the execution success of states.

## Example Salt State Event...

{% highlight json %}
{
    "comment": "Service SplunkForwarder is already enabled, and is dead",
    "fun_args": null,
    "jid": "20151209123456789",
    "name": "SplunkForwarder",
    "start_time": "19:30:37.613000",
    "minion_id": "server01",
    "fun": "state.highstate",
    "result": false,
    "state_id": "splunkforwarder",
    "duration": 199.0,
    "__run_num__": 1,
    "changes": {"SplunkForwarder": false},
    "state_name": "service_|-splunkforwarder_|-SplunkForwarder_|-running"
}
{% endhighlight %}

## Analyze the Event

You get some interesting fields here, the first one is the **result** field. This field will show you either true for the state executed successfully or false for the state failed to execute properly. In the example above it failed. Now you'll want to look at the **state_id**, which is the name you gave the state in your sls file. This allows you to pinpoint the issue to a specific state. Next you can view the **comment** and the **changes** fields they will give you details about the failure.

With this level of detail being captured by your log aggregator you can create some pretty nifty dashboards. For example a simple Fail/Success gauge and a table of top 20 failed states.

![Splunk SaltState Dashboard]({{ site.url }}/images/salt-events-splunk.png.jpg =100x20)

## How the State Event Returner Works

When the salt-minions run a state they create a job the job has an **ID** and **return** data (state results) and some other information about the state run. When a job is done the salt-minion job gets forwarded to the salt-master's job cache. When the salt-master receives a job cache event it runs the custom tcp_returner module. The module checks to see if the job function is a **state.sls** or a **state.highstate**. If it is it gets the state results from the **return** and loops over each state sends it to the log aggregation tool.

## Setting Up the TCP Returner Module

There a few steps to set this up...

1. Enable TCP capture on your log aggregation tool
2. Install tcp_returner.py returner module to your salt-masters
3. Configure your salt-masters to use the tcp_returner

### Enable TCP capture on your log aggregation tool

If your using something like Splunk you can create a TCP data input like the following...

{% highlight bash %}
vim /opt/splunk/etc/apps/search/local/inputs.conf
{% endhighlight %}

{% highlight bash %}
[tcp://0.0.0.0:6000]
connection_host = none
host = salt-master01.acme.com
source = salt-master01.acme.com
sourcetype = salt:master:events
{% endhighlight %}

So you need to define the port that the Splunk server will listen on for TCP events. The host and source name of the sending server. And the sourcetype for easier searching.

### Install tcp_returner.py returner module to your salt-masters

You can get a copy the returner from my gist https://gist.github.com/ryancurrah/0328cecce0cc81bfe705.

Create a folder for the returner.

{% highlight bash %}
mkdir -p /srv/salt/modules/returners
{% endhighlight %}

Copy the returner from the gist.

{% highlight bash %}
vim /srv/salt/modules/returners/tcp_returner.py
{% endhighlight %}

### Configure your salt-masters to use the tcp_returner

Setup your salt-master to use the returner.

{% highlight bash %}
vim /etc/salt/master
{% endhighlight %}

Append the following config to your salt-master (Don't forget to salt this :/).

{% highlight bash %}
extension_modules: /srv/salt/modules
event_return: tcp_return
returner.tcp_return.host: 10.0.0.7
returner.tcp_return.port: 6000
{% endhighlight %}

Restart your salt-master.

{% highlight bash %}
service salt-master restart
{% endhighlight %}

If all is good you can view state events starting to appear in your log aggregation tool. Or you can manually generate some events by running a state. If not run the salt-master in debug mode and check that the returner is running correctly by looking at the salt-master log.

Well hope this helps some of you to do some analytics on your states in your environment. If you have any questions let me know!
