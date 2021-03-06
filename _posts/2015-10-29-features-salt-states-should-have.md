---
layout: post
title: Features Salt States Should Have
categories:
  - tech
tags:
  - saltstack
  - features
description: What salt states should include to be considered a mergable feature.
comments: true
published: true
---

One thing I have learned over the year of managing a Salt code base is to make sure you provide your salt developers a good framework for implementing monitoring, log aggregation and tests along with their states.

The solution I implemented is to allow the developers to present those configurations in the pillars as shown in the example below.

Potentially you should enforce that these items be present when a new state is created. Some basic things that I would suggest that should be implemented with new states are...

1. Monitoring configs
2. Log aggregation configs
3. Tests

Below illustrates how item 1 and 2 can be implemented.

## Example Pillar definitions for shared services that states can utilize

**pillar/top.sls**

{% highlight yaml %}
{% raw %}
...
'G@roles:apache':
  - match: compound
  - apache
{% endraw %}
{% endhighlight %}

**pillar/apache.sls**

{% highlight yaml %}
apache:
  sensu_checks:
    - name: apache_process
      command: check-process.rb apache

  splunk_monitors:
    - name: apache_logs
      monitor: /var/log/(httpd|apache2)/access*log
      index: main
      sourcetype: "apache:access"
  firewall:
    ...    
{% endhighlight %}

It does clutter up the pillars a bit but what it does allow us to do is to find these configurations by looping through the pillar data structure and implement them all in the same way. It also allows us to audit the different rules and checks in place because we know every configuration item will be under a predefined name 'sensu_checks' and 'splunk_monitors'.

## Example shared service configuration file

**splunkforwarder/files/inputs.conf**

{% highlight yaml %}
{% raw %}
{%- for pillar_key, data in pillar.iteritems() -%}
{%- for monitor in salt['pillar.get']('%s:splunk_monitors'|format(pillar_key), {}) -%}
[monitor://{{ monitor.name }}]
{%- for key, value in monitors.iteritems() %}
{% if value %}{{ key }} = {{ key }}{% endif -%}
{% endfor %}
{% endfor -%}
{%- endfor -%}
{% endraw %}
{% endhighlight %}

As for tests thats a post for another time!
