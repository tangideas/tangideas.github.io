---
title: PHP mysqlnd error
author: James Tang
layout: post
permalink: /code/php-mysqlnd-error/
categories:
  - Code
tags:
  - Linux
---
My php is compiled with the following configuration on CentOS5.5:

<pre class="brush:plain">./configure --with-mysql=mysqlnd --with-mysqli=mysqlnd ...
</pre>

When I try to connecting to MySQL, the following error occurred. 

## Error

<pre class="brush:plain">Warning: mysql_connect(): [2002] No such file or directory (trying to 
connect via unix:///tmp/mysql.sock) in Command line code on line 1
</pre>

## Solution

Find out the exact location of mysql.sock, then set the exact path in php.ini as:

<pre class="brush:plain">mysqli.default_socket=/path/to/mysql.sock
mysql.default_socket=/path/to/mysql.sock
pdo_mysql.default_socket=/path/to/mysql.sock
</pre>

In my situation, the mysql.sock is located at **/var/lib/mysql**.

Reference: http://dev.mysql.com/doc/refman/5.5/en/problems-with-mysql-sock.html