---
title: 基于Cobbler建立网络安装环境
author: James Tang
layout: post
permalink: /server/network-installation-with-cobbler/
categories:
  - Server
tags:
  - Cobbler
  - Kickstart
  - Linux
---
Linux系统为CentOS6.2, 该配置没有包含DNS支持.

## 1. Add EPEL yum repository:

<pre class="brush:plain">[EPEL]
name=EPEL
baseurl=http://mirror01.idc.hinet.net/EPEL/6/x86_64/
gpgcheck=0
</pre>

## 2. Install required packages

<pre class="brush:bash">yum install redhat-rpm-config dhcp tftp-server httpd cobbler cobbler-web pykickstart
</pre>

## 3. Stop firewall or add ports to iptables configuration:

<pre class="brush:bash">service iptables stop
</pre>

OR

<pre class="brush:plain">-A INPUT -p icmp --icmp-type any -j ACCEPT
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# LOCALHOST
-A INPUT -i lo -j ACCEPT

# SSH
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
# DNS - TCP/UDP
-A INPUT -m state --state NEW -m udp -p udp --dport 53 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 53 -j ACCEPT
# DHCP
-A INPUT -m state --state NEW -m udp -p udp --dport 68 -j ACCEPT
# TFTP - TCP/UDP
-A INPUT -m state --state NEW -m tcp -p tcp --dport 69 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 69 -j ACCEPT
# NTP
-A INPUT -m state --state NEW -m udp -p udp --dport 123 -j ACCEPT
# HTTP/HTTPS
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
# Syslog for cobbler
-A INPUT -m state --state NEW -m udp -p udp --dport 25150 -j ACCEPT
# Koan XMLRPC ports
-A INPUT -m state --state NEW -m tcp -p tcp --dport 25151 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 25152 -j ACCEPT

#-A INPUT -j LOG
-A INPUT -j REJECT --reject-with icmp-host-prohibited

COMMIT
</pre>

## 4. Disable SELinux or 

<pre class="brush:bash">setsebool -P httpd_can_network_connect true
</pre>

## 5. Run cobbler check and fixed the configrations:

<pre class="brush:bash">cobbler check
</pre>

The output looks like this:

<pre class="brush:plain">The following are potential configuration items that you may want to fix:

1 : The 'server' field in /etc/cobbler/settings must be set to something other than localhost, or kickstarting features will not work.  This should be a resolvable hostname or IP for the boot server as reachable by all machines that will use it.
2 : For PXE to be functional, the 'next_server' field in /etc/cobbler/settings must be set to something other than 127.0.0.1, and should match the IP of the boot server on the PXE network.
3 : some network boot-loaders are missing from /var/lib/cobbler/loaders, you may run 'cobbler get-loaders' to download them, or, if you only want to handle x86/x86_64 netbooting, you may ensure that you have installed a *recent* version of the syslinux package installed and can ignore this message entirely.  Files in this directory, should you want to support all architectures, should include pxelinux.0, menu.c32, elilo.efi, and yaboot. The 'cobbler get-loaders' command is the easiest way to resolve these requirements.
4 : change 'disable' to 'no' in /etc/xinetd.d/rsync
5 : debmirror package is not installed, it will be required to manage debian deployments and repositories
6 : ksvalidator was not found, install pykickstart
7 : The default password used by the sample templates for newly installed machines (default_password_crypted in /etc/cobbler/settings) is still set to 'cobbler' and should be changed, try: "openssl passwd -1 -salt 'random-phrase-here' 'your-password-here'" to generate new one
8 : fencing tools were not found, and are required to use the (optional) power management features. install cman or fence-agents to use them

Restart cobblerd and then run 'cobbler sync' to apply changes.

</pre>

## 6. Change default root password

Generate password:

<pre class="brush:bash">openssl passwd -1
</pre>

Edit /etc/cobbler/settings and change the value of default\_password\_crypted to the generated password.

## 7. Change the server and next_server to you server IP(e.g. 192.168.2.10) in /etc/cobbler/settting

## 8. Configure /etc/cobbler/dhcp.template

Sample snippet:

<pre class="brush:plain">ddns-update-style interim;

allow booting;
allow bootp;

ignore client-updates;
set vendorclass = option vendor-class-identifier;

subnet 192.168.2.0 netmask 255.255.255.0 {
     option routers             192.168.2.2;
     option domain-name-servers 10.141.136.10,10.106.0.20;
     option subnet-mask         255.255.255.0;
     range dynamic-bootp        192.168.2.100 192.168.2.254;
     filename                   "/pxelinux.0";
     default-lease-time         21600;
     max-lease-time             43200;
     next-server                $next_server;
}

#for dhcp_tag in $dhcp_tags.keys():
#...
</pre>

## 9. Start services

<pre class="brush:plain">service httpd start
service dhcpd start
service xinetd start
service cobblerd start
</pre>

## 10. Import DVD/ISO

<pre class="brush:bash">mount -o loop /home/james/CentOS-6.2-x86_64-bin-DVD1.iso /media/dvd1
</pre>

<pre class="brush:bash">cobbler import --path=/media/dvd1 --arch=x86_64 --name=centos6.2-x86_64
</pre>

Add the second DVD if exists:

<pre class="brush:bash">rsync -a /media/dvd2 /var/www/cobbler/ks_mirror/centos6.2-x86_64 --exclude-from=/etc/cobbler/rsync.exclude --progress
</pre>

<pre class="brush:bash">createrepo -c cache -s sha --update --groupfile `ls /var/www/cobbler/ks_mirror/centos6.2-x86_64/repodata/*-comps.xml` /var/www/cobbler/ks_mirror/centos6.2-x86_64
</pre>

## 11. (Optional) configure web interface

See: [http://cobbler.github.com/manuals/2.2.3/6\_-\_Web_Interface.html][1]

## 12. Create profiles

Kickstart sample snippet:

<pre class="brush:plain">#platform=x86, AMD64, or Intel EM64T
#version=DEVEL
# Firewall configuration
firewall --enabled --http --ssh
# Install OS instead of upgrade
install
# Use network installation
url --url="http://192.168.2.10/cobbler/ks_mirror/centos6.2-x86_64/"
# If any cobbler repo definitions were referenced in the kickstart profile, include them here.
repo --name=source-1 --baseurl=http://192.168.2.10/cobbler/ks_mirror/centos6.2-x86_64
# Root password
rootpw --iscrypted $1$b..dmuva$EK/LkmCmaGPQ.RG5.mWVV.
# System authorization information
auth  --useshadow  --passalgo=sha512
# Use text mode install
text
# System keyboard
keyboard us
# System language
lang en_US
# SELinux configuration
selinux --enforcing
# Do not configure the X Window System
skipx
# Installation logging level
logging --level=info
# Reboot after installation
reboot
# System timezone
timezone  Asia/Chongqing
# Network information
network  --bootproto=static --device=eth0 --gateway=192.168.2.1 --ip=192.168.2.100 --nameserver=192.168.2.99 --netmask=255.255.255.0 --onboot=on
# System bootloader configuration
bootloader --location=mbr
# Partition clearing information
clearpart --all  

# Clear the Master Boot Record
zerombr
# Allow anaconda to partition the system as needed
autopart

%post

set -x -v
exec 1>/root/ks-post.log 2>&#038;1

# Start yum configuration 
wget "http://192.168.2.10/cblr/svc/op/yum/profile/centos6.2-x86_64" --output-document=/etc/yum.repos.d/cobbler-config.repo
wget "http://192.168.2.10/repo/scripts/switch-remote.sh" --output-document=/etc/yum.repos.d/switch-remote.sh
cd /etc/yum.repos.d
switch-remote.sh disable
# End yum configuration

cd /root

#TODO web server install and configuration

wget "http://192.168.2.10/repo/nginx-php-packages.tar.bz2" 
tar xjf nginx-php-package.tar.bz2
cd nginx-php-package
./install > install.log 2>&#038;1

%end

%packages
@mail-server
@mysql-client
@web-server

%end
</pre>

<pre class="brush:bash">cobbler profile add --name=centos6.2-x86_64-web-server --distro=centos6.2-x86_64 --kickstart=/var/lib/cobbler/kickstarts/web-server.ks
</pre>

## 13. Now install other server with PXE

[<img src="http://tangobean.com/wp-content/uploads/2012/09/cobbler-1.png" alt="" title="cobbler-1" width="569" height="346" class="alignnone size-full wp-image-547" />][2]

## 14. Reference

1. <http://cobbler.github.com/manuals/2.2.3/>

 [1]: http://cobbler.github.com/manuals/2.2.3/6_-_Web_Interface.html
 [2]: http://tangobean.com/wp-content/uploads/2012/09/cobbler-1.png