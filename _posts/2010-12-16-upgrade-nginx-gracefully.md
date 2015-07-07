---
title: Upgrade Nginx Gracefully
author: James Tang
layout: post
permalink: /server/upgrade-nginx-gracefully/
categories:
  - Server
tags:
  - nginx
---
It&#8217;s important to keep your Nginx server up when you try to upgrade your Nginx to a newer version. I tried following steps successfully.

1. Check the compile configuration for previous version:

<pre class="brush:bash">/usr/local/nginx/sbin/nginx -V
</pre>

2. Configure the new version according to the previous configuration and your need.

3. Compile and install

<pre class="brush:bash">make &#038; make install
</pre>

4. Send USR2(12) signal to the master process:

<pre class="brush:bash">kill -USR2 `cat /usr/local/nginx/logs/nginx.pid`
</pre>

A new file name nginx.pid.oldbin appeared in /usr/local/nginx/logs/. 

5. Send WINCH(28) signal to master process in order to shutdown the worker process gracefully:

<pre class="brush:bash">kill -WINCH `cat /usr/local/nginx/logs/nginx.pid.oldbin`
</pre>

6. Send QUIT signal to shutdown the master process:

<pre class="brush:bash">kill -QUIT `cat /usr/local/nginx/logs/nginx.pid.oldbin`
</pre>

Now the nginx server should have been upgraded gracefully.