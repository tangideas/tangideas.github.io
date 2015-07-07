---
title: Introduction to eZ Publish
author: James Tang
layout: post
permalink: /code/introduction-to-ez-publish/
categories:
  - Code
tags:
  - CMS
  - eZ Publish
  - PHP
---
[eZ Publish][1]是一个PHP开发的CMS，由Norway的一家公司开发，有社区版和企业版。eZ Publish在中国的很少有人知道，故在此简单介绍。eZ Publish的架构有其卓越之处，在我看来主要体现在其内容模型的抽象。与比较有名的Drupal相比，其最大的特点是它更像一个完整的产品，而Drupal更像一个框架。

## 1. 总体结构

eZ Publish有总体结构如下图所示，该图源自其[官方文档][2]。

[<img src="http://tangobean.com/wp-content/uploads/2012/06/libs_kernel_modules_doc.png" alt="" title="libs_kernel_modules_doc" width="400" height="302" class="alignnone size-full wp-image-426" />][3]

最底层是基础库，中间的内核实现eZ Publish的抽象功能，如ORM, eZ数据类型接口等，最上层是模块。

## 2. 内容模型

内容模型是eZ Publish的精髓所在， 在eZ Publish中所有数据都抽象为内容，包括图片、文件和用户。在Drupal及其它一些CMS中都支持动态属性，在Drupal中需要一个叫做CCK的模块来实现。但eZ Publish在此基础上作了进一步的抽象，引入了类和对象的概念。内容结构的定义称为Content Class，Content Class由多个Content Attribute组成，Content Attribute有属于不同的DataType, DataType也是eZ Publish中的一个抽象概念。eZ Publish提供一些常用的DataType, 也可以根据需要扩展。

实际内容在层次结构来组织，如定义了Content Class“文件夹”, “文章”等，“文件夹”下可以有子“文件夹”和“文章”等其它内容，如下图所示。

<div id="attachment_427" style="width: 665px" class="wp-caption alignnone">
  <a href="http://tangobean.com/wp-content/uploads/2012/06/ez-content-hierarchy.png"><img src="http://tangobean.com/wp-content/uploads/2012/06/ez-content-hierarchy.png" alt="" title="ez-content-hierarchy" width="655" height="336" class="size-full wp-image-427" /></a>
  
  <p class="wp-caption-text">
    某一内容下的子项
  </p>
</div>

每一个内容实例至少与一个节点(Node)关联，每个Node有一个访问路经，叫做位置(Location），用户通过Node ID访问内容，如：

<pre class="brush:plain">http://ez.lab/index.php/content/view/full/2

</pre>

访问节点ID为2的内容,前天URL的详细信息，请参考：　<http://doc.ez.no/eZ-Publish/Technical-manual/4.5/Concepts-and-basics/URL-translation>

### 数据库结构

下图是eZ Publish内容模型对应的数据库结构：

<div id="attachment_428" style="width: 650px" class="wp-caption alignnone">
  <a href="http://tangobean.com/wp-content/uploads/2012/06/ez-content-diagram.png"><img src="http://tangobean.com/wp-content/uploads/2012/06/ez-content-diagram-1024x872.png" alt="" title="ez-content-diagram" width="640" height="545" class="size-large wp-image-428" /></a>
  
  <p class="wp-caption-text">
    内容模型相关主要关系
  </p>
</div>

## 3. 扩展

扩展eZ Publish功能的方式是通过Extension（扩展）来实现，所有扩展都放在extension目录下。第个扩展一可以有模板(design)，自定义模板操作符（autoloads),类库(classes), 模块(modules), 配置(settings)等，请参考<http://share.ez.no/learn/ez-publish/an-introduction-to-developing-ez-publish-extensions>.

## 4. 配置

eZ Publish的配置非常多，所有默认配置在settings目录下， 而且不同的模块可以有自己的配置或者重写默认配置。  
配置文件是ini格式，在PHP中通过eZINI类来调用指定的配置文件：

<pre class="brush:php">$ini = eZINI::instance('mysetting.ini');

//get value of 'Key' under 'GeneralSetting' section in mysetting.ini file.
$key = $ini->variable('GeneralSetting', 'Key');
</pre>

## 5. Siteaccess

Siteaccess是eZ Publish中另一个重要概念，一个Siteaccess相当于一个站点，不同的siteaccess可以通过不同的路径或不同的域名来访问。并且不同的Siteaccess通过配置使用不同的Design, 或者不同的数据库等。在eZ Publish中没有前后台之后，只是Siteaccess不同而已。

更多信息：<http://doc.ez.no/eZ-Publish/Technical-manual/3.6/Concepts-and-basics/Configuration/Site-management>

## 6. Design

eZ Publish的模板最大的特点是，在模板中可以直接获取数据并实现业务逻辑，而不是采用常见是MVC模式。不知道为何要这样设计，我认为这是eZ Publish最大的问题之一。下面是一个简单示例:

<pre class="brush:html">{def $global_layout_object = fetch( 'content', 'tree', hash( 'parent_node_id', 1,
 'limit', 1,
 'class_filter_type', include,
 'class_filter_array', array( 'global_layout' ) ) )}

{if $global_layout_object}


<div class="border-content">
  {attribute_view_gui attribute=$global_layout_object[0].data_map.page}
  
</div>
{/if}
</pre>

一个Siteaccess可以使用多个Design, 当调用一个模板文件时，eZ按特定顺序查找该文件，其顺序在site.ini中指定：

<pre class="brush:plain">[DesignSettings]
SiteDesign=mydesign
AdditionalSiteDesignList[]
AdditionalSiteDesignList[]=mybasedesign
AdditionalSiteDesignList[]=ezwebin
AdditionalSiteDesignList[]=base
AdditionalSiteDesignList[]=standard
</pre>

当在调用模板page/block.tpl时，优先查找mydesign, 如果没有找到则在mybasedesign中查找,一直到standard如果没有找到则出错。

<pre class="brush:php">$tpl = eZTemplate::factory();
$tpl->setVariable('block', $block );
$Result['content'] = $tpl->fetch('design:page/block.tpl');
</pre>

在Extension中通过PHP调用模板。

eZ Publish的模板还有override概念，可以对特定内容指定不同的模板，这也是通过配置实现。对于大部分内容默认调用node/view/full.tpl模板，但如果要对Content Class为banner的内容应用另一个模板，则可以在siteaccess的override.ini配置中这样指定新的模板:

<pre class="brush:plain">[full_banner]
Source=node/view/full.tpl
MatchFile=full/banner.tpl
Subdir=templates
Match[class_identifier]=banner
</pre>

更多有关模板信息请参考: [eZ Publish templates][4]

## 7. 总结

eZ Publish采用OOP实现，总体架构比较精良，但可扩展性仅限于CMS，对于业务逻辑较为复杂的应用eZ Publish很难胜任。另外我个人最不喜欢的是其模板语言，太多逻辑信赖于在模板中获取数据，即不利于逻辑与模板分离，与不便于调试。另外，由于每个扩展都可以有配置并重写配置，所以在开发项目时，多个模板的配置管理也容易变得混乱。还有，更不要将任何数据都用eZ Publish内容模型来存储，只用它来存储“内容”，必要时要设计自己的数据库关系来实现，因为从上面的内容模型关系可以看出，内容的查询需要连多个表并十分低效，为了提高性能需要采用多种缓存。 

eZ Publish仅仅是一个CMS，所以不要试图用它做CMS之外的事情。

 [1]: http://ez.no/ "eZ Publish"
 [2]: http://doc.ez.no/eZ-Publish/Technical-manual/4.5/Concepts-and-basics/The-internal-structure-of-eZ-Publish
 [3]: http://tangobean.com/wp-content/uploads/2012/06/libs_kernel_modules_doc.png
 [4]: http://doc.ez.no/eZ-Publish/Technical-manual/4.5/Templates