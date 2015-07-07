---
title: onbeforeunload与onunload事件略谈
author: James Tang
excerpt: window.onbeforeunload与window.onunload事件都是在用户离开页面时触发。比如在编辑Gmail邮件时，如果关闭确认消息；有些垃圾网站也用它来不断地弹出新网页，这算是这两个事件常用的情形之一。
layout: post
permalink: /notes/onbeforeunload%e4%b8%8eonunload%e4%ba%8b%e4%bb%b6%e7%95%a5%e8%b0%88/
categories:
  - Notes
tags:
  - JavaScript
---
window.onbeforeunload与window.onunload事件都是在用户离开页面时触发，但window.onbeforeunload是在window.onunload之前执行。下面是主流浏览器对这两个事件的支持情况。

<table>
  <tr>
    <td>
       
    </td>
    
    <td>
      Firefox3.6
    </td>
    
    <td>
      Chrome5
    </td>
    
    <td>
      IE8
    </td>
    
    <td>
      Opera10.6
    </td>
  </tr>
  
  <tr>
    <td>
      onbeforeunload
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      不支持
    </td>
  </tr>
  
  <tr>
    <td>
      onunload
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      不支持
    </td>
  </tr>
  
  <tr>
    <td>
      在onunload中执行window.open
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      不支持
    </td>
    
    <td>
      支持
    </td>
    
    <td>
      不支持
    </td>
  </tr>
</table>

### 基本用法

下面是这两个事件的基本用法：

<pre class="brush:javascript">window.onbeforeunload = function () {
	return "Something you want to say";
};

window.onunload = function () {
	//window.open("http://tangobean.com");
	//alert("Ok");
	//More Logic ...
};</pre>

### 触发条件

<table>
  <tr>
    <td>
    </td>
    
    <td>
      onbeforeunload
    </td>
    
    <td>
      onunload
    </td>
  </tr>
  
  <tr>
    <td>
      关闭浏览器窗口
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
  
  <tr>
    <td>
      关闭页面标签窗口
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
  
  <tr>
    <td>
      输入新地址
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
  
  <tr>
    <td>
      从书签打开新页面
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
  
  <tr>
    <td>
      刷新/前进/后退
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
  
  <tr>
    <td>
      脚本重定向
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
  
  <tr>
    <td>
      Submit表单
    </td>
    
    <td>
      +
    </td>
    
    <td>
      +
    </td>
  </tr>
</table>

当以上事件发生时，浏览器会弹出以下或类型的窗口，如果确定，onunload事件被触发，如果取消则继续停留在当前页面。

[<img src="http://tangobean.com/wp-content/uploads/2010/08/confirm_leave_page.png" alt="" title="confirm_leave_page" width="438" height="161" class="alignnone size-full wp-image-63" />][1]

 [1]: http://tangobean.com/wp-content/uploads/2010/08/confirm_leave_page.png