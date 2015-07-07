---
title: Drupal架构与实现分析
author: James Tang
layout: post
permalink: /code/drupal-architecture-and-implement-analysis/
categories:
  - Code
tags:
  - Architecture
  - Drupal
  - PHP
---
<blockquote class="post-top-quote">
  <p>
    不尽知用兵之害者，则不能尽知用兵之利——孙武《孙子兵法》
  </p>
</blockquote>

Drupal也算是架构精良的PHP开源CMS，它最大的优点是可以灵活性高，不仅可以用于实现一般的CMS功能，还可以容易的实现其它应用，如Drupal官方所说，可以实现wiki, blog甚至商城等.它最大的问题是灵活性太高，如果没有根据实际情况进行设计与管理，后果会很糟糕。

先看一下Drupal的总体架构，下图是Drupal官方的信息流图，详细参见：http://drupal.org/getting-started/before/overview。

[<img src="http://tangobean.com/wp-content/uploads/2012/03/drupal_flow_0.gif" alt="" title="Drupal information flow" width="528" height="562" class="alignnone size-full wp-image-380" />][1]

从图上看起来非常清晰明了，不过这并没有体现出Drupal的架构方式。在我看来Drupal的基本架构就是Drupal核心+模块+模板，所谓的Drupal核心除模块之外的最基础的代码，全部在includes目录下面，另外Drupal有几个核心模块，如System, User, Node等。

Drupal的处理流程如下：

  * 系统初始化
  * 路径解析（路由）
  * 页面内容渲染
  * 页面布局及block渲染
  * 生成页面

## 1. Hook

Drupal几乎是纯面向过程的架构，这或许让很多习惯OOP的人有点惊讶，这或许也是Drupal变得难以管理（源码，模块）的原因。Hook是Drupal架构实现的关键,模块通过种hook与Drupal进行交互，这种方式被称为基于过程的AOP<sup>[1]</sup>。

大多数情况下，Drupal通过module\_invoke\_all()来调用指定的hooks, 如下面代码将调用所有MODULE\_NAME\_cron()这样的函数:

<pre class="brush:php">module_invoke_all('cron');
</pre>

下面是module\_invoke\_all()的源码，参数通过func\_get\_args函数获得，返回值是可选的。顺便提一下，在Drupal及其模块中经常用到func\_get\_args函数。

<pre class="brush:php">function module_invoke_all() {
  $args = func_get_args();
  $hook = $args[0];
  unset($args[0]);
  $return = array();
  foreach (module_implements($hook) as $module) {
    $function = $module .'_'. $hook;
    $result = call_user_func_array($function, $args);
    if (isset($result) &#038;&#038; is_array($result)) {
      $return = array_merge_recursive($return, $result);
    }   
    else if (isset($result)) {
      $return[] = $result;
    }   
  }

  return $return;
}
</pre>

module\_invoke\_all()并不是唯一的调用hook的方式，另一个比较常用的是drupal\_alter，hook\_menu\_alter()、hook\_mail\_alter等就是通过该函数实现的。drupal\_alter()与module\_invoke\_all()的主要差别是drupal_alter()是通过传递参数引用给模块，让模块可以个性数据。

<pre class="brush:php">drupal_alter('menu', $callbacks);
</pre>

<pre class="brush:php">drupal_alter('form', $data, $form_id);
</pre>

drupal_alter()的源码片段:

<pre class="brush:php">function drupal_alter($type, &#038;$data) {
  //省略
  foreach (module_implements($type .'_alter') as $module) {
    $function = $module .'_'. $type .'_alter';
    call_user_func_array($function, $args);
  }
}
</pre>

除了drupal\_alter()与module\_invoke\_all()之外，模块也可以自定义调用hook的逻辑，就像hook\_nodeapi的实现一样，如下面的代码所示。但它们都需要调用module_implements()来得知有哪些模块实现了该hook。

<pre class="brush:php">function node_invoke_nodeapi(&#038;$node, $op, $a3 = NULL, $a4 = NULL) {
  $return = array();
  foreach (module_implements('nodeapi') as $name) {
    $function = $name .'_nodeapi';
    $result = $function($node, $op, $a3, $a4);
    if (isset($result) &#038;&#038; is_array($result)) {
      $return = array_merge($return, $result);
    }    
    else if (isset($result)) {
      $return[] = $result;
    }    
  }
  return $return;
}
</pre>

## 2. 模块

模块主要用于扩展系统功能，一般都会实现一个或多个hook，如hook\_menu是最常见的，但这并不是必须的。是简单的模块只需要包含MODULE\_NAME.module与MODULE\_NAME.info即可，MODULE\_NAME.install用于处理模块安装及卸载相关的逻辑。

所有模块信息都保存在system表中，system同时也保存模板信息。system.status为0表示模块未启用，system.weight有时候很重要，比如模块A和B同时实现hook_x(), 但A必须在B之后执行，则可以通过weight来控制。weight值越大越迟加载，因为模块的加载顺序如下：

<pre class="brush:sql">SELECT name, filename, throttle FROM {system} WHERE type = 'module' AND status = 1 ORDER BY weight ASC, filename ASC
</pre>

throttle用于控制模块是否被加载，不过几乎不用它。

## 3. Form

Drupal定义了一套很详细的API用于定义表单及相关处理逻辑，包括元素默认值、验证、显示结构及顺序等，并提供了默认的显示结构与样式（虽然这很不实用）。

第一个表单对应一个函数，也称为form\_id， Drupal通过drupal\_get\_form($form\_id)来获得该表单的定义并渲染HTML。如下面代码所示：

<pre class="brush:php">function user_login(&#038;$form_state) {
  global $user;

  // If we are already logged on, go to the user page instead.
  if ($user->uid) {
    drupal_goto('user/'. $user->uid);
  }

  // Display login form:
  $form['name'] = array('#type' => 'textfield',
    '#title' => t('Username'),
    '#size' => 60,
    '#maxlength' => USERNAME_MAX_LENGTH,
    '#required' => TRUE,
  );

  $form['name']['#description'] = t('Enter your @s username.', array('@s' => variable_get('site_name', 'Drupal')));
  $form['pass'] = array('#type' => 'password',
    '#title' => t('Password'),
    '#description' => t('Enter the password that accompanies your username.'),
    '#required' => TRUE,
  );
  $form['#validate'] = user_login_default_validators();
  $form['submit'] = array('#type' => 'submit', '#value' => t('Log in'), '#weight' => 2);

  return $form;
}
</pre>

这是用户登录的表单,form\_id就是user\_login， 这里有一个参数$form\_state，但这并不是定义表单所必须的，通过drupal\_get\_form(&#8216;user\_login&#8217;,$form\_state)获得该表单并在页面显示。默认情况下表单的action就是当前页面，但可以过来#action来指定其它路径，但#action指向的页面必须调用drupal\_get\_form($form\_id),如drupal\_get\_form(&#8216;user\_login&#8217;,$form\_state)，否则表单将无法被处理，除非不使用Drupal的方式而是手动来处理表单内容。

第一个表单在渲染之前，Drupal都会自动加上一个form\_build\_id。

<pre class="brush:html">&lt;input type="hidden" name="form_build_id" id="form-cb48b9e32a4a25f8b1e483153f2c7b06" value="form-cb48b9e32a4a25f8b1e483153f2c7b06" /&gt;
</pre>

form\_build\_id的主要作用是用于表单缓存，很多时候表单缓存中包含一些跟踪上下文信息，所以如果表单的form\_build\_id被个性或超时就不能被正确处理。默认情况下，Drupal的缓存是保存在数据库中，表单的缓存则保存在cache_form表中。

## 4. Menu

Drupal的Menu非常重要，它用于定义用户可以访问的路径，模块通过实现hook\_menu()来新的路径，每个路径对应一个页面。hook\_menu()返回一个定义了menu的数组，如下所示：

<pre class="brush:php">$items['node/%node'] = array(
    'title' => 'View',
    'page callback' => 'node_page_view',
    'page arguments' => array(1),
    'access callback' => 'node_access',
    'access arguments' => array('view', 1),
    'type' => MENU_CALLBACK,
  );
</pre>

该数组定义了路径的页面回调函数，访问权限等。

这些信息并不是第次请求都会查找实现了hook\_menu的函数，而是缓存在menu\_router表中。路径的层次关系等信息刚是缓存在cache_menu表中。

详细文档请参考：http://drupal.org/node/102338

## 5. 模板

类似于hook\_menu，hook\_theme用于定义模板，模板的定义信息叫做theme registry, 一般都是保存在缓存中，所以每次更新都要更新缓存。

theme()是Drupal模板系统的关键函数，用于渲染HTML。Drupal的模板并非都是模板文件，还可以是函数，一般以theme_开头。

详细文档请参考：http://drupal.org/documentation/theme

## 6. 内容与用户

CMS当然离不开内容与内容管理，Drupal中内容的关键概念是节点，但它并不是在Drupal核心中实现，而是在Node核心模块中实现。所有内容都被看作节点，节点有不同类型，不同类型在总体结构上是一致的。主要的三个数据库关系表分别是node, node\_revisions, node\_type。另外涉及单个节点权限管理的表是node_access。

用户及用户管理部分主要有三个主要定义：用户(user), 用户组(user roles)及相关权限(permission)。

## 7. 系统变量

Drupal的系统变量以键值对的形式保存在variable表中，也可以在settings.php中配置。variable\_get()、variable\_set()、variable_del()分别用于获取、设置及删除系统变量。

## 8. 缓存

Drupal的默认缓存保存在数据库中，缓存表在cache\_开头。如果需要以其它方式保存缓存数据，如保存到memcache中，只需要实现cache\_set(), cache\_get(), cache\_clear\_all()函数，并将cache\_inc系统变量设置为自定义缓存处理的PHP文件路径即可。

## 9. 总结

Hook系统让Drupal具有良好的扩展性，同时也容易让调试与维护变得很困难。我认为如果不仅仅是用Drupal做一个像个人博客一样的简单网站，就应该知道：

1. Drupal虽然能做很多东西，但最好只用来做CMS相关的事，不用的东西彻底砍掉，免得浪费资源。  
2. 仔细分析设计模块与模板，特别是hook的使用一定要慎重，配置要统一且简单，不然调试将变得异常困难。  
3. 不要依赖社区的模块，包括Drupal安装包自带的，大多模块考虑太多的通用性，而不是性能。  
4. 手动打补丁，不要使用自动更新。  
5. 必要时修改Drupal内核。

## 10. 参考

1. Programming language trade-off: http://www.garfieldtech.com/blog/language-tradeoffs  
2. Architectural priorities: http://www.garfieldtech.com/blog/architectural-priorities  
3. The Drupal Overview: http://drupal.org/getting-started/before/overview  
4. Form API Reference: http://api.drupal.org/api/drupal/developer!topics!forms\_api\_reference.html/6

 [1]: http://tangobean.com/wp-content/uploads/2012/03/drupal_flow_0.gif