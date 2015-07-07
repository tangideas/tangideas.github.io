---
title: 在JavaScript中使用in操作符
author: James Tang
layout: post
permalink: /code/%e5%9c%a8javascript%e4%b8%ad%e4%bd%bf%e7%94%a8in%e6%93%8d%e4%bd%9c%e7%ac%a6/
categories:
  - Code
tags:
  - JavaScript
  - Operator
---
今天在阅读jQuery源码的时候看到在if中使用in操作符，发现原来in不仅仅是用在for中，看来对JavaScript的了解实在太少了:)

<del datetime="2011-07-12T11:48:36+00:00">对我来说最有用的功能是像PHP中的in_array一样使用</del>，其用途可以类似于PHP中的isset(感谢<a href="http://www.ballsache.com/blog/" target="_blank">@littlexiang</a>纠正)，如下面的代码所示：

<pre class="brush:javascript">var arr = [1, 2, 3, 4, 5];
var obj = {
			"4": true, 
			"6":false, 
			"9":true, 
			"11":true
		};

if ( 2 in arr) {
	alert('2 in arr');
} else {
	alert('2 NOT in arr');
}

if ( 4 in obj) {
	alert('4 in obj');
} else {
	alert('4 NOT in obj');
}

if ( 6 in obj) {
	alert('6 in obj');
} else {
	alert('6 NOT in obj');
}

if ( 21 in arr) {
	alert('21 in arr');
} else {
	alert('21 NOT in arr');
}

if ( 2 in obj) {
	alert('2 in obj');
} else {
	alert('2 NOT in obj');
}


if ( 0 in arr) {
	alert('0 in arr');
} else {
	alert('0 NOT in arr');
}

if ( 5 in arr) {
	alert('5 in arr');
} else {
	alert('5 NOT in arr');
}
</pre>

For more information, please refer to: [Using the ‘In’ Operator to Iterate Through Arrays and Objects][1]

 [1]: http://www.caveofprogramming.com/javascript-2/javascript-in-using-the-in-operator-to-iterate-through-arrays-and-objects/