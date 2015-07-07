---
title: URL中多个问号的问题
author: James Tang
layout: post
permalink: /notes/how-web-server-process-multiple-question-mark-in-query/
categories:
  - Notes
tags:
  - URL
---
在PHPChina论坛看一到一个关于&#8221;t.php??a=b&#8221;这样的问题。 根据[RFC3986][1]这应该是不准确的，但服务器会怎样处理呢？

经过测试，Apache2, 与Nginx处理一致， 都是从第一个问号开始处理，之后的问号当作Query的健或值来处理，如“t.php???a=1?b=2&?c=3”的QUERY_STRING为&#8221;??a=1?b=2&?c=3&#8243;，因此PHP会处理为这样：

<pre class="brush:plain">array(2) {
  ["??a"]=>
  string(5) "1?b=2"
  ["?c"]=>
  string(1) "3"
}
</pre>

但PHP5.4内置的Web server将前面几个连续的问号处理为一个，因此得到这样的QUERY_STRING: &#8220;a=1?b=2&?c=3&#8243;。

 [1]: http://tools.ietf.org/html/rfc3986#section-3