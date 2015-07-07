---
title: 在Linux下使用宋体
author: James Tang
layout: post
permalink: /server/%e5%9c%a8linux%e4%b8%8b%e4%bd%bf%e7%94%a8%e5%ae%8b%e4%bd%93/
categories:
  - Server
tags:
  - Linux
---
操作系统：CentOS5.5

1. 将Windows下的字体文件(如simsun.ttc, tahoma.ttf, tahomabd.ttf等) Copy到/usr/share/fonts/chinese/TrueType/simsun

2. 编辑/etc/X11/fs/config, 加入字体路径

<pre class="brush:plain"># where to look for fonts
catalogue = /usr/share/X11/fonts/misc:unscaled,
    /usr/share/X11/fonts/75dpi:unscaled,
    /usr/share/X11/fonts/100dpi:unscaled,
    /usr/share/X11/fonts/Type1,
    /usr/share/X11/fonts/TTF,
    /usr/share/fonts/default/Type1,
    ,   
    /usr/share/fonts/chinese/misc:unscaled,
    /usr/share/fonts/chinese/misc,
    /usr/share/fonts/chinese/TrueType/simsun, #字体路径
    /usr/share/fonts/chinese/TrueType
</pre>