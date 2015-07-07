---
title: Erlang初体验
author: James Tang
layout: post
permalink: /code/erlang-quick-introduction/
categories:
  - Code
tags:
  - erlang
---
## 数据类型

Erlang的数据类型与其它语言，如C、 PHP，有很多不同之处。

### 整型</h2> 

Erlang可以处理任意大小的整型，其大小仅受限于内存大小。代价是处理比固定大小的整形处理慢一点。

不同进制表示方式为Base#Value,Base为进制，如:

10进制:

<pre class="brush:plain">-100 
0 
100
</pre>

2进制:

<pre class="brush:plain">2#1011
</pre>

16进制:

<pre class="brush:plain">16#E1
</pre>

4进制:

<pre class="brush:plain">4#11
</pre>

### 浮点

<pre class="brush:plain">123.45
</pre>

<pre class="brush:plain">1.234E10
</pre>

<pre class="brush:plain">1.234E-10
</pre>

### Atom

Atom,不知道翻译成原子类型还是什么好：

> Atoms start with a lowercase letter or are delimited by single quotes. Letters, digits, the &#8220;at&#8221; symbol(@), the full stop(.), and underscore(_) are valid characters if the atom starts with a lowercase letter. Any character code is allowed within an atom if the atom is encapsulated by single quotes.

但是full stop(.)好像并不能出现在最后一个字符，至少在erlang shell中不能这样：

<pre class="brush:plain">abc..
</pre>

对于Atom类型的操作仅有比较.

### 布尔类型

布尔值由atoms true或false表示。

### Tuple

Tuple是组合类型，元素由{}组织在一起, 如：

<pre class="brush:plain">{abc,[1,2,3],true,123,{4,5}}
</pre>

### 列表(List)</h2> 

列表与tuple有点相似，由[]组织元素，如：

<pre class="brush:plain">[1, 2, {3, 4}, [5, 6], abc].
</pre>

### 字符与字符串

字符由整型表示，而字符串则用列表表示。

字符前面需要加入$符号。

<pre class="brush:plain">$A.
</pre>

&#8220;Hello, world&#8221;表示为:

<pre class="brush:plain">[72, 101,108, 108, 111, 32, 87, 111, 114, 108, 100].
</pre>

## 变量

变量以大写字母或下划线开头，并之后仅能使用字母、整型、下划线组成。变量在使用产不需要声明。 

Erlang中给变量赋值叫做绑定(bound)值，并且一旦绑定之后不可以改变。

Erlang中也没有传值，不能传递变量的引用，如C的指针。

## 模式匹配

模式匹配(Pattern matching)用于：

  * 给变量赋值
  * 控制程序流执行
  * 从组合数据类型中提取数据

下划线只起占位作用，并不保存值， 下划线开头的变量叫做&#8221;don&#8217;t care&#8221;变量, 即使该变量在程序中没有被用到，但编译器也不报错。

<pre class="brush:plain">{person, Name, _, _email} = {person, "James", 100010, "james@fwso.cn"}.
</pre>

## 函数与模块

函数在模块中定义， 函数名为atom,如数的数量叫做arity。 “->”将函数头部与函数主体分开。

模块的文件后缀为.erl.

<pre class="brush:plain">% Demo module of Erlang
-module(demo).
-export([double/1, multi/2, area/1]).

double(Value) ->
    times(Value, 2).

multi(Value, Value2) ->
    Value * Value2.

times(X, Y) ->
    X * Y.

area({square, Side}) -> 
    Side * Side;

area({triangle, A, B, C}) ->
    S = (A + B + C)/2,
    math:sqrt(S*(S-A)*(S-B)*(S-C)).

</pre>

Notes of *Programming Erlang, by Francesco Cesarini & Simon Thompson*.