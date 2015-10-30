---
layout: post
title: Max Open Files for High Load Services
categories: 
  - os
tags: 
  - limits
  - maxopenfiles
description: Describes how to set max open files safely for service like rabbitmq or saltstack
comments: true
published: true
---

One thing that is normally looked over when configuring a virtual machine for a service like RabbitMQ or SaltStack is the max open files limits. The nature in which these services operate with a high ammount of open sockets we got to make sure the max open files limit is suffcient.

If you want to see what your services process limits are you can cat out the /proc/**pid**/limits file.

Make sure you know whjat your virtual machines maximum number of open files is to make sure you don't over allocate open files to the rabbitmq or salt-master processes. If you do and thoose services hit the max you won't be able to instatiate a pam session to login to box.

{% highlight bash %}
cat /proc/sys/fs/file-max
379261
{% endhighlight %}

Now create limits.d configuration file for this services user, in this case the rabbitmq user.

{% highlight bash %}
cat /etc/limits.d/rabbmitmq.conf
rabbitmq    soft    nofile    150000
rabbitmq    hard    nofile    150000
{% endhighlight %}

Restart the rabbitmq process...

{% highlight bash %}
service rabbitmq-server restart
{% endhighlight %}

Make sure the new limits are in place...

{% highlight bash %}
cat /proc/7654/limits
Limit                     Soft Limit           Hard Limit           Units
Max cpu time              unlimited            unlimited            seconds
Max file size             unlimited            unlimited            bytes
Max data size             unlimited            unlimited            bytes
Max stack size            8388608              unlimited            bytes
Max core file size        0                    unlimited            bytes
Max resident set          unlimited            unlimited            bytes
Max processes             29818                29818                processes
Max open files            150000               150000               files      <<<<<<<<<<---------
Max locked memory         65536                65536                bytes
Max address space         unlimited            unlimited            bytes
Max file locks            unlimited            unlimited            locks
Max pending signals       29818                29818                signals
Max msgqueue size         819200               819200               bytes
Max nice priority         0                    0
Max realtime priority     0                    0
Max realtime timeout      unlimited            unlimited            us
{% endhighlight %}
