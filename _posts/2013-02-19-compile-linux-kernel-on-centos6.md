---
title: Compile Linux Kernel on CentOS6
author: James Tang
layout: post
permalink: /server/compile-linux-kernel-on-centos6/
categories:
  - Server
tags:
  - Kernel
  - Linux
---
I have tried to compile Linux kernel 2.6.34.14 on CentOS6.3, whose default kernel is 2.6.32, successfully. It&#8217;s just for fun, you can follow the following simple steps if you are interested.

The CentOS6.3 runs on VMware, with 1 virtual CPU and 1GB RAM.

## 1. Fetch kernel source

Download the source of one kernel version from [kernel.org][1]. I selected stable version 2.6.34.14 but not the latest one.

## 2. Configure kernel

Second, extract the source archive and configure the kernel before compiling. There are many different way to configure the kernel, one simplest way is just copy the configuration for current kernel. The configuration file is located at /boot directory, like /boot/config-2.6.32-279.el6.i686. Just copy it to the kernel source directory as **.config**, and run following command:

<pre class="brush:plain">make oldconfig
</pre>

<a href="http://lib.fwso.cn/linux-kernel-config-2.6.34.14" target="_blank">linux-kernel-config-2.6.34.14</a>

## 3. Compile

Now compile the kernel just as compiling general applications:

<pre class="brush:plain">make
</pre>

## 4. Install the new kernel

cp arch/x86/boot/bzImage to /boot directory, you can rename it, and install the kernel modules:

<pre class="brush:plain">make modules_install
</pre>

Then make initrd with following command:

<pre class="brush:plain">mkinitrd /boot/initramfs-2.6.34.14.fwso.img 2.6.34.14.fwso
</pre>

*2.6.34.14.fwso* is my compiled kernel version, it should be changed according to your configuration.

If mkinitrd does not exist, install dracut package:

<pre class="brush:plain">yum install -y dracut
</pre>

Finally, configure your grub in order to boot from the new kernel.

Good luck, enjoy:)

 [1]: http://kernel.org/