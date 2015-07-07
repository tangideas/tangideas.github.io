---
title: Load additional JavaScript dynamicaly
author: James Tang
layout: post
permalink: /code/load-additional-javascript-dynamicaly/
categories:
  - Code
tags:
  - JavaScript
---
Sometime we need to analyze a page with additional JavaScript libraries, jQeury for example, so it&#8217;s helpful to load the javascript dynamically. In this case you can run similar script as following demo shows on browser javascript console:

<pre class="brush:javascript">var js = document.createElement('script');
js.setAttribute('type', 'text/javascript');
js.setAttribute('src', 'http://youdomain.com/path-to.js');
document.head.appendChild(js);
</pre>