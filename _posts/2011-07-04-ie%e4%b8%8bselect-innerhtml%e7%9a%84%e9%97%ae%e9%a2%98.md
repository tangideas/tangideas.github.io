---
title: IE下Select InnerHTML的问题
author: James Tang
layout: post
permalink: /notes/ie%e4%b8%8bselect-innerhtml%e7%9a%84%e9%97%ae%e9%a2%98/
categories:
  - Notes
tags:
  - IE
  - JavaScript
  - 问题
---
在IE下，包括IE8, 9，不能使用innerHTML对select设置option内容，如下面的代码无法得到预期的结果：

<pre class="brush:javascript">document.getElementById('mySelectId').innerHTML = '&lt;option value="new_value">New value&lt;/option>';
</pre>

这个问题在2003年就已经发布在微软支持网站上，但到IE9依然存在这个问题，微软似乎并不把它当成一个问题，详细请参考：http://support.microsoft.com/kb/276228，该文档提出了几种解决方案。

我倾向于采用文章中提到的第三种方法来替代innerHTML:

<pre class="brush:javascript">var daysNode = document.getElementById('mySelectId');

function updateOptions () {

	while ( daysNode.firstChild ) {
		daysNode.removeChild( daysNode.firstChild );
	}

	for (var i = 1; i &lt; 10; i++) {
		var options = document.createElement('option');
		options.setAttribute('value', i);
		options.appendChild(document.createTextNode(i));
		daysNode.appendChild(options);
	}
}
</pre>

完整示例请参考：http://code.google.com/p/mixedlab/source/browse/trunk/javascript/ie\_select\_dom.html