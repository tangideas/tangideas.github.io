---
title: Tools for testing REST API
author: James Tang
layout: post
permalink: /notes/tools-for-testing-rest-api-1/
categories:
  - Notes
tags:
  - API
  - curl
  - REST
---
## 1. CURL

curl是一个很强大很实用的工具：

<pre class="brush:bash">curl -v -X POST -H "Content-Type: application/json" -H "Accept: application/json" -d '{"id":1, "method":"message.query", "jsonrpc":"2.0", "params": {"user_id":9} }' http://tangobean.com/services/json-rpc
</pre>

## 2. REST Client

[WizTools.org RESTClient][1]是一个用Java写的工具， Swing界面，使用比较方便：

[<img src="http://tangobean.com/wp-content/uploads/2012/03/RESTClient-wizTools.png" alt="" title="RESTClient-wizTools" width="730" height="669" class="alignnone size-full wp-image-367" />][2]

## 3. WFetch

Windows下可以使用微软的[WFetch][3], 功能比较齐全，但感觉不是很好用。

[<img src="http://tangobean.com/wp-content/uploads/2012/03/wfetch.jpg" alt="" title="wfetch" width="804" height="864" class="alignnone size-full wp-image-368" />][4]

 [1]: https://code.google.com/p/rest-client/downloads/list "WizTools.org RESTClient"
 [2]: http://tangobean.com/wp-content/uploads/2012/03/RESTClient-wizTools.png
 [3]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=21625
 [4]: http://tangobean.com/wp-content/uploads/2012/03/wfetch.jpg