---
title: 'chmod(): Operation not permitted'
author: James Tang
layout: post
permalink: /code/chmod-operation-not-permitted/
categories:
  - Code
  - Server
tags:
  - CentOS
  - Linux
---
PHP项目中除了在Web服务器中运行的程序外，可能还会有直接运行的脚本，他们可能共享一些数据或代码，但当如果运行的用户不同（都不是root）时，在调用chmod()时就会出现chmod(): Operation not permitted这样的错误。

事实上这并不是PHP的问题，而是与系统有关, 我的系统是CentOS 5.6。在CentOS中， chmod, chown只能对属于执行该命令的用户的文件进行操作。对于Fedora, Redhat应该是一样的。