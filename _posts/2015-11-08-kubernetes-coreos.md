---
layout: post
title: CoreOS w/ Kubernetes Install Guide for VirtualBox
categories: 
  - containers
  - tech
tags: 
  - kubernetes
  - coreos
description: How install Kubernetes on CoreOS.
comments: true
published: true
---



## This guide will introduce you to bootstrapping a Kubernetes cluster on CoreOS with VirtualBox.

Requires 4 VM Instances

- 1x Kubernetes Master
- 3x Kubernetes Nodes

The meat of the installation is taken care of by a CoreOS tool called **cloud config**.

**Cloud config** is a configuration tool similar to cloud-init or a very basic implementation of a config mgmt tool.  Where all the post install configurations are defined in a yaml file. The cloud config files used in this guide will setup networking, download and install kubernetes, configure etcd, set the ssh public key for the user 'core', and set the default password for the user 'core' to 'coreos'.

Download the CoreOS ISO <https://coreos.com/os/docs/latest/booting-with-iso.html>

Create 4 VMs with the following instructions

    - Name them 'coreos-x' where x is a number between 1-4)
    - 1GB or more of RAM (CoreOS ISO won't boot if less than 1GB RAM assigned)
    - x1 or more vCPU's
    - x1 HD with 8GB's or more
    - x2 NICS, One set as host-only (For internal/private cluster communication) and the other set as NAT (For internet access)


> Note: 
> NAT can have any network address just make sure DHCP is on

Host-only network info must be as follows:

- **Network Address** 192.168.122.0
- **Subnet Address** 255.255.255.0
- **Default Gateway** 192.168.122.1


### Bootstrap a Master server

Using coreos-1 VM boot off the ISO image
Download the Master server cloud config yaml file off a webserver (I used github gist)

{% highlight bash %}
sudo wget -O cloud-config.yaml http://git.io/vswiF
{% endhighlight %}


Verify your cloud config yaml is valid
    
{% highlight bash %}
sudo coreos-cloudinit -validate --from-file cloud-config.yaml
{% endhighlight %}
    
    
Start the CoreOS install
    
{% highlight bash %}
sudo coreos-install -d /dev/sda -c cloud-config.yml
{% endhighlight %}

Shutdown the VM and remove/detach the CD drive

Startup the VM after about 5-10 minutes Kubernetes Master will have been bootstrapped and you should see the following files in /opt/bin

{% highlight bash %}
core@master ~ $ ls -ailh /opt/bin
total 109M
521218 drwxr-xr-x 2 root root 4.0K Aug 23 02:45 .
521217 drwxr-xr-x 3 root root 4.0K Aug 23 02:43 ..
521222 -rwxr-xr-x 1 root root  39M Aug 23 02:44 kube-apiserver
521223 -rwxr-xr-x 1 root root  32M Aug 23 02:45 kube-controller-manager
521224 -rwxr-xr-x 1 root root  18M Aug 23 02:45 kube-scheduler
521220 -rw-r--r-- 1 root root 1.7K Aug 23 02:43 kube-serviceaccount.key
521225 -rwxr-xr-x 1 root root  20M Aug 23 02:45 kubectl
521221 -rwxr-xr-x 1 root root 2.0M Aug 23 02:43 setup-network-environment
521219 -rwxr-xr-x 1 root root  164 Aug 23 02:43 wupiao
{% endhighlight %}


You can now start using the **kubectl** client


### Bootstrap the first Node server
Using coreos-2 VM boot off the ISO image
Download the Node server cloud config yaml file off a webserver (I used github gist)
    
{% highlight bash %}
sudo wget -O cloud-config.yaml http://git.io/vswMW
{% endhighlight %}


Verify your cloud config yaml is valid

{% highlight bash %}
sudo coreos-cloudinit -validate --from-file cloud-config.yaml
{% endhighlight %}


Start the CoreOS install
    
{% highlight bash %}
sudo coreos-install -d /dev/sda -c cloud-config.yml
{% endhighlight %}


Shutdown the VM and remove/detach the CD drive
Startup the VM after about 5-10 minutes Kubernetes Master will have been bootstrapped and you should see the following files in /opt/bin
    
{% highlight bash %}
core@node01 /etc/systemd/system $ ls -ailh /opt/bin/
total 73M
521218 drwxr-xr-x 2 root root 4.0K Aug 23 04:14 .
521217 drwxr-xr-x 3 root root 4.0K Aug 23 04:13 ..
521221 -rwxr-xr-x 1 root root  17M Aug 23 04:14 kube-proxy
521223 -rwxr-xr-x 1 root root  20M Aug 23 04:15 kubectl
521222 -rwxr-xr-x 1 root root  35M Aug 23 04:14 kubelet
521220 -rwxr-xr-x 1 root root 2.0M Aug 23 04:13 setup-network-environment
521219 -rwxr-xr-x 1 root root  209 Aug 23 04:13 wupiao
{% endhighlight %}



You can now start using the **kubectl** client
Try the **kubectl get nodes** command to view joined nodes on the master

{% highlight bash %}
core@master ~ $ kubectl get nodes
NAME       LABELS                            STATUS
10.0.2.9   kubernetes.io/hostname=10.0.2.9   Ready
{% endhighlight %}


### Bootstrap the second Node server
Using coreos-3 VM boot off the ISO image
Download the Node server cloud config yaml file off a webserver (I used github gist)
    
{% highlight bash %}
sudo wget -O cloud-config.yaml http://git.io/vswQZ

Verify your cloud config yaml is valid
    
{% highlight bash %}
sudo coreos-cloudinit -validate --from-file cloud-config.yaml
{% endhighlight %}
    

Start the CoreOS install

{% highlight bash %}
sudo coreos-install -d /dev/sda -c cloud-config.yml
{% endhighlight %}

    
Shutdown the VM and remove/detach the CD drive
Startup the VM after about 5-10 minutes Kubernetes Master will have been bootstrapped and you should see the following files in /opt/bin
    
{% highlight bash %}
core@node01 /etc/systemd/system $ ls -ailh /opt/bin/
total 73M
521218 drwxr-xr-x 2 root root 4.0K Aug 23 04:14 .
521217 drwxr-xr-x 3 root root 4.0K Aug 23 04:13 ..
521221 -rwxr-xr-x 1 root root  17M Aug 23 04:14 kube-proxy
521223 -rwxr-xr-x 1 root root  20M Aug 23 04:15 kubectl
521222 -rwxr-xr-x 1 root root  35M Aug 23 04:14 kubelet
521220 -rwxr-xr-x 1 root root 2.0M Aug 23 04:13 setup-network-environment
521219 -rwxr-xr-x 1 root root  209 Aug 23 04:13 wupiao
{% endhighlight %}

    
You can now start using the **kubectl** client
Try the **kubectl get nodes** command to view joined nodes on the master
    
    
{% highlight bash %}
core@master ~ $ kubectl get nodes
NAME       LABELS                            STATUS
10.0.2.10   kubernetes.io/hostname=10.0.2.10   Ready
{% endhighlight %}


### Bootstrap the third Node server
Using coreos-4 VM boot off the ISO image
Download the Node server cloud config yaml file off a webserver (I used github gist)
    
{% highlight bash %}
sudo wget -O cloud-config.yaml http://git.io/vswQ8
{% endhighlight %}
    
    
Verify your cloud config yaml is valid

{% highlight bash %}
sudo coreos-cloudinit -validate --from-file cloud-config.yaml
{% endhighlight %}

    
Start the CoreOS install

{% highlight bash %}
sudo coreos-install -d /dev/sda -c cloud-config.yml
{% endhighlight %}

    
Shutdown the VM and remove/detach the CD drive
Startup the VM after about 5-10 minutes Kubernetes Master will have been bootstrapped and you should see the following files in /opt/bin
    
{% highlight bash %}
core@node01 /etc/systemd/system $ ls -ailh /opt/bin/
total 73M
521218 drwxr-xr-x 2 root root 4.0K Aug 23 04:14 .
521217 drwxr-xr-x 3 root root 4.0K Aug 23 04:13 ..
521221 -rwxr-xr-x 1 root root  17M Aug 23 04:14 kube-proxy
521223 -rwxr-xr-x 1 root root  20M Aug 23 04:15 kubectl
521222 -rwxr-xr-x 1 root root  35M Aug 23 04:14 kubelet
521220 -rwxr-xr-x 1 root root 2.0M Aug 23 04:13 setup-network-environment
521219 -rwxr-xr-x 1 root root  209 Aug 23 04:13 wupiao
{% endhighlight %}

    
You can now start using the **kubectl** client
Try the **kubectl get nodes** command to view joined nodes on the master         
    
{% highlight bash %}
core@master ~ $ kubectl get nodes
NAME       LABELS                            STATUS
10.0.2.9   kubernetes.io/hostname=10.0.2.9   Ready
10.0.2.10   kubernetes.io/hostname=10.0.2.10   Ready
10.0.2.11   kubernetes.io/hostname=10.0.2.11   Ready
{% endhighlight %}



## Sources
- My cloud config gist <https://gist.github.com/ryancurrah/adb533cc1099c8774670>
- Intro to Kubernetes <https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes>
- Cloud Config Docs <https://coreos.com/os/docs/latest/cloud-config.html>
- Install CoreOS to Disk Docs <https://coreos.com/os/docs/latest/installing-to-disk.html>
- CoreOS ISO Image <https://coreos.com/os/docs/latest/booting-with-iso.html>
- Setting route scope for private NIC to 'link' <http://www.freedesktop.org/software/systemd/man/systemd.network.html>
- CoreOS Bare Metal Install Docs <http://stevieholdway.tumblr.com/post/90167512059/coreos-bare-metal-iso-install-tutorial>
- Systemd Replacement Unit Fles <https://wiki.archlinux.org/index.php/Systemd#Replacement_unit_files>
- Kubernetes CoreOS Docs <https://github.com/kubernetes/kubernetes/blob/master/docs/getting-started-guides/coreos/coreos_multinode_cluster.md>
