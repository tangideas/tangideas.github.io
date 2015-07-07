---
title: 在Windows下安装Nginx+PHP
author: James Tang
layout: post
permalink: /code/%e5%9c%a8windows%e4%b8%8b%e5%ae%89%e8%a3%85nginxphp/
categories:
  - Code
tags:
  - nginx
  - PHP
  - Windows
---
在Windows下安装PHP运行环境主要是为了方便开发和页面的调试。

## 安装

其实安装过得非常简单，下面只描述简单步骤。

  * 1. 下载nginx，如果没有特殊需求，下载最新版本，解压即可。
  * 2. 下载PHP，我建议下载zip压缩包，因为对于nginx没有必要下载安装版本。
  * 3. 配置nginx与在Linux下一样
  * 4. 配置PHP，跟Linux下也一样

## 启动与停止

1. 启动Nginx:

<pre class="brush:shell">D:/nginx-1.0.4/nginx.exe -p D:/nginx-1.0.4
</pre>

2. 启动PHP-CGI

<pre class="brush:shell">D:/php/php-cgi.exe -b 127.0.0.1:9001 -c D:/php/php.ini
</pre>

为了方便可以写到一个批处理文件中，这里用到了一个RunHiddenConsole，可以在这里下载:

<pre class="brush:plain">@echo off
REM Windows 下无效
REM set PHP_FCGI_CHILDREN=5

REM 每个进程处理的最大请求数，或设置为 Windows 环境变量
set PHP_FCGI_MAX_REQUESTS=1000
 
echo Starting PHP FastCGI...
REM RunHiddenConsole C:/php5/php-cgi.exe -b 127.0.0.1:9000 -c C:/php5/php.ini
D:/nginx-1.0.4/RunHiddenConsole.exe D:/php/php-cgi.exe -b 127.0.0.1:9001 -c D:/php/php.ini

echo Starting nginx...
D:/nginx-1.0.4/RunHiddenConsole.exe D:/nginx-1.0.4/nginx.exe -p D:/nginx-1.0.4
exit
</pre>

3. 停止Nginx

<pre class="brush:plain">D:/nginx-1.0.4/nginx.exe -p D:\nginx-1.0.4 -s stop
</pre>

4. 停止PHP-CGI

<pre class="brush:plain">taskkill /F /IM php-cgi.exe &gt; nul
</pre>

同样可以写到一个批处理文件中：

<pre class="brush:plain">@echo off
echo Stopping nginx...
REM taskkill /F /IM nginx.exe > nul
D:/nginx-1.0.4/nginx.exe -p D:\nginx-1.0.4 -s stop

echo Stopping PHP FastCGI...
taskkill /F /IM php-cgi.exe > nul
</pre>

## Nginx配置的路径问题

由于在Windows下文件路径可以用&#8221;\&#8221;， 也可以用&#8221;\\&#8221;， 也可以用&#8221;/&#8221;作为路径做分隔符。但&#8221;\&#8221;最容易引发问题，所以要尽量避免使用。

例如下面的配置可以正常运行

<pre class="brush:plain">root   G:\ezdemo;
</pre>

但是这样

<pre class="brush:plain">root G:\tweb;
</pre>

就会出现问题，访问程序时会出现500错误。

在logs/error.log里面会发现这类似这样的错误日志：

<pre class="brush:plain">2011/06/16 22:07:28 [crit] 4756#5412: *2 GetFileAttributesEx() "G:	web" failed (123: The filename, directory name, or volume label syntax is incorrect), client: 127.0.0.1, server: php.lab, request: "GET / HTTP/1.1", host: "php.lab"
2011/06/16 22:13:38 [crit] 4756#5412: *5 CreateFile() "G:	pweb/test.html" failed (123: The filename, directory name, or volume label syntax is incorrect), client: 127.0.0.1, server: php.lab, request: "GET /test.html HTTP/1.1", host: "php.lab"
</pre>

注意到G:后而有一个tab空格，你应该明白怎么回事了:)