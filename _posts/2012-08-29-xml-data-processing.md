---
title: XML数据处理杂记
author: James Tang
layout: post
permalink: /code/xml-data-processing/
categories:
  - Code
tags:
  - PHP
  - XML
  - XML Schema
  - xmllint
  - XQilla
  - Zorba
---
XML之所以应用广泛应该是因为其通用性，Web service、跨平台数据转换等经常采用XML格式。最近采用XML作为中间格式实现了一个较大数据量的迁移(与下面示例无关)，在此总结一下大致用法，当然这里只涉及XML相关的部分，忽略了sed, awk之类的辅助工具。希望您能指出用法的不当之处，或更好的方法。

<a href="http://lib.fwso.cn/xml-process-demo.tar.gz" target="_blank">完整示例</a>

## 1. XML Schema &#8211; 定义数据结构

XML Schema与DTD类似，都是用于定义XML文档结构，但比DTD更加灵活、更加强大。有趣的是XSD其本身也采用XML描述，因此XSD的结构是用DTD来定义(http://www.w3.org/2001/XMLSchema.dtd)。

blog_post.xsd示例文档:

<pre class="brush:xml">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;xs:schema xmlns="http://tangobean.com" 
    xmlns:fwso="http://tangobean.com" 
    elementFormDefault="qualified" 
    targetNamespace="http://tangobean.com" 
    xmlns:xs="http://www.w3.org/2001/XMLSchema"&gt;
    &lt;xs:element name="posts"&gt;
        &lt;xs:complexType&gt;
            &lt;xs:sequence minOccurs="0" maxOccurs="unbounded"&gt;
                &lt;xs:element name="post" type="blogPost" /&gt;
            &lt;/xs:sequence&gt;
        &lt;/xs:complexType&gt;
        &lt;xs:unique name="uniqueIssueID"&gt;
            &lt;xs:selector xpath="fwso:post" /&gt;
            &lt;xs:field xpath="fwso:id" /&gt;
        &lt;/xs:unique&gt;
    &lt;/xs:element&gt;
    &lt;xs:complexType name="blogPost"&gt;
        &lt;xs:sequence&gt;
            &lt;xs:element name="id" type="xs:integer" /&gt;
            &lt;xs:element name="title" type="xs:string" /&gt;
            &lt;xs:element name="author" type="xs:string" /&gt;
            &lt;xs:element name="body" type="xs:string" /&gt;
            &lt;xs:element name="publish_time" type="timestamp" /&gt;
            &lt;xs:element name="tags" minOccurs="0" maxOccurs="unbounded"&gt;
                &lt;xs:complexType&gt;
                    &lt;xs:sequence minOccurs="0" maxOccurs="unbounded"&gt;
                        &lt;xs:element name="tag" type="xs:string" /&gt;
                    &lt;/xs:sequence&gt;
                &lt;/xs:complexType&gt;
            &lt;/xs:element&gt;
        &lt;/xs:sequence&gt;
    &lt;/xs:complexType&gt;
    &lt;xs:simpleType name="timestamp"&gt;
        &lt;xs:restriction base="xs:string"&gt;
            &lt;xs:pattern value="[1-9][0-9]{9}" /&gt;
        &lt;/xs:restriction&gt;
    &lt;/xs:simpleType&gt;
&lt;/xs:schema&gt;
</pre>

该XSD定义了一个简单的博客文章的结构，integer, string等类型是XSD定义的数据类型，XSD可以自定义数据类型，如该示例中的timestamp。

## 2. xmllint &#8211; XML文档验证

xmllint事实上是一个XML解析工具，可以用于XML文档格式验证、格式化等。这里使用xmllint来验证XML文档是否符合XSD的定义：

<pre class="brush:bash">xmllint -noout -schema blog_post.xsd posts.xml
</pre>

如果验证通过，则会输出：

<pre class="brush:plain">posts.xml validates
</pre>

否则会输出对应的错误，类似：

<pre class="brush:plain">posts.xml:11: element newtag: Schemas validity error : Element '{http://tangobean.com}newtag': This element is not expected. Expected is ( {http://tangobean.com}tags ).
posts.xml:13: element post: Schemas validity error : Element '{http://tangobean.com}post': Duplicate key-sequence ['210'] in unique identity-constraint '{http://tangobean.com}uniqueIssueID'.
posts.xml fails to validate
</pre>

post.xml示例内容如下：

<pre class="brush:xml">&lt;?xml version="1.0"?&gt;
&lt;posts xmlns="http://tangobean.com"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://tangobean.com blog_post.xsd"&gt;
    &lt;post&gt;
        &lt;id&gt;210&lt;/id&gt;
        &lt;title&gt;blog post 1&lt;/title&gt;
        &lt;author&gt;James Tang&lt;/author&gt;
        &lt;body&gt;&lt;![CDATA[&lt;h1&gt;what&#39;s this&lt;/h1&gt;&lt;p&gt;goes here&lt;/p&gt;]]&gt;&lt;/body&gt;
        &lt;publish_time&gt;1346221680&lt;/publish_time&gt;
        &lt;newtag&gt;hello&lt;/newtag&gt;
    &lt;/post&gt;
    &lt;post&gt;
        &lt;id&gt;210&lt;/id&gt;
        &lt;title&gt;blog post 2&lt;/title&gt;
        &lt;author&gt;James Tang&lt;/author&gt;
        &lt;body&gt;&lt;![CDATA[&lt;h1&gt;what&#39;s this&lt;/h1&gt;&lt;p&gt;goes here&lt;/p&gt;]]&gt;&lt;/body&gt;
        &lt;publish_time&gt;1346221680&lt;/publish_time&gt;
        &lt;tags&gt;
            &lt;tag&gt;tag1&lt;/tag&gt;
            &lt;tag&gt;tag2&lt;/tag&gt;
        &lt;/tags&gt;
    &lt;/post&gt;
&lt;/posts&gt;
</pre>

## 3. xQeury &#8211; 转换数据

xQuery能够非常方便地将其它格式的XML文档转换成目标的XML格式。

blog_post.xquery示例如下：

<pre class="brush:plain">xquery version "1.0";

declare default element namespace "http://tangobean.com"
declare namespace fwso="http://tangobean.com";
declare variable $fwso:oldDoc as xs:string := "olddoc.xml";

declare variable $fwso:oldTagDoc as xs:string := "oldtagdoc.xml";

declare function fwso:list-posts($maindoc as xs:string, $tagdoc as xs:string) {
        let $doc := doc($maindoc)
        let $seq := $doc//article
        let $tags := doc($tagdoc)

        for $item in $seq
            let $id := data($item/article_id)
            let $ptags := $tags//tag[article_id=$id]

            return 
           &lt;post&gt;
            &lt;id&gt;{$id}&lt;/id&gt;
            &lt;title&gt;{data($item/article_title)}&lt;/title&gt;
            &lt;author&gt;{data($item/article_author)}&lt;/author&gt;
            &lt;body&gt;{data($item/article_content)}&lt;/body&gt;
            &lt;publish_time&gt;{data($item/published)}&lt;/publish_time&gt;
            {if (count($ptags) gt 0)
            then
            &lt;tags&gt;
                {for $tag in $ptags
                    return &lt;tag&gt;{data($tag/tag_name)}&lt;/tag&gt;
                }    
            &lt;/tags&gt;
            else
                &#39;&#39;
            }
           &lt;/post&gt;
};

&lt;posts
xmlns="http://tangobean.com"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://tangobean.com blog_post.xsd"
&gt;
    {fwso:list-posts($fwso:oldDoc, $fwso:oldTagDoc)}
&lt;/posts&gt;
</pre>

## 4. XQilla/Zorba &#8211; xQuery解析器

<pre class="brush:bash">xqilla blog_post.xquery > posts.xml
</pre>

或

<pre class="brush:bash">zorba -f -q blog_post.xquery > posts.xml
</pre>

## 5. 参考

  1. http://www.w3.org/TR/xmlschema11-1/
  2. http://www.w3.org/TR/xmlschema11-2/
  3. http://www.w3.org/TR/xquery/
  4. http://www.zorba-xquery.com/html/index
  5. http://xqilla.sourceforge.net/HomePage