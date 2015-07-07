---
title: Character Sets and Unicode Tips
author: James Tang
layout: post
permalink: /notes/character-sets-and-unicode-tips/
categories:
  - Notes
tags:
  - Charset
  - JavaScript
  - PHP
  - Unicode
---
## 1. Character sets

最熟悉也最简单的字符集莫过于ASCII，如小写字母a对应的编码是97。ASCII采用7位来编码，Extend-ASCII采用8位来编码，所以分别只用表示127和255个字符。如果要编码更多的字符当然需要更多的位来实现，因此需要制定不同的编码来表示不同的语言，如果中文的GB2312。Windows的字符编码称为Code pages，所以经常看GB2312也经常表示为CP936，也有的称为WINDOWS-936。

不同的字符集的编码范围及显示都不一样，一般都只能特定的语言，所以如果程序（如浏览器）对文本采用了不同的字符编码时，就会出现所谓的乱码。Windows下默认的编码显示为ANSI, 事实上ANSI并不是一个具体的字符编码。无论是英文版、中文版、日文版Windows,默认编码都是ANSI，但实际上对应不同的编码：

> Note ANSI code pages can be different on different computers, or can be changed for a single computer, leading to data corruption. For the most consistent results, applications should use Unicode, such as UTF-8 or UTF-16, instead of a specific code page.

### 示例

在Windows下创建一个文件cn.txt，输入中文并采用Windows默认编码，然后在Linux用cat查看，将看到中文显示为乱码，因为Linux shell默认编码是UTF-8, 用file命令查看cn.txt：

<pre class="brush:bash">file cn.txt
</pre>

显示结果为：

<pre class="brush:plain">cn.txt: ISO-8859 text, with CRLF line terminators
</pre>

或者

<pre class="brush:bash">file -bi cn.txt
</pre>

结果为：

<pre class="brush:plain">text/plain; charset=iso-8859-1
</pre>

事实上结果是错误的，如果试图用iconv转换为UTF-8：

<pre class="brush:bash">iconv -f ISO-8859-1 -t UTF-8 cn.txt -o cn2.txt
</pre>

cn2.txt中文依然是乱码。

由于cn.txt是在中文Windows下创建，其编码实际上是GB2312，所以需要将-f参数改为GB2312/WINSOWS-936/CP936/GBK,所以这样才能正确转换：

<pre class="brush:bash">iconv -f WINDOWS-936 -t UTF-8 cn.txt -o cn2.txt
</pre>

再用file查看，发现除了显示为UTF-8编码外，还显示换行符是CRLR，这是windows的默认换行符：

<pre class="brush:plain">cn2.txt: UTF-8 Unicode text, with CRLF line terminators
</pre>

## 2. Unicode

由于国际化的需要，很多内容需要同时表示多种语言的字符，因此需要一种统一的字符编码，Unicode就是为此而诞生。

> Unicode provides a unique number for every character, no matter what the platform, no matter what the program, no matter what the language.

Unicode定义了几乎所有文字的字符（还包括一此没有使用的）编码及其表现(representation)，以及Unicode的实现方式UTF-8、UTF-16、UTF-32。

Unicode将中文、日文、韩文的编码统称为CJK Unified Ideographs，编码范围为U+4E00-U+9FCC，可以在[这里][1]查询汉字的Unicode编码。

## 3. UTF-8

UTF-8也许是目前应用最广的Unicode实现方式，其编码规则如下：

| 编码范围                | 二进制序列                               |
| ------------------- | ----------------------------------- |
| 0000 0000-0000 007F | 0xxxxxxx                            |
| 0000 0080-0000 07FF | 110xxxxx 10xxxxxx                   |
| 0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx          |
| 0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx |

可以看出UTF-8兼容ASCII字符编码；从CJK的编码范围可知，中文编码是三个字节，“中”的Unicode编码为U+4E2D，故其UTF-8表示是0xE4B8AD。

### 示例

创建utf8.txt并输入以下内容:

<pre class="brush:plain">a中国
</pre>

用xxd命令查看其16进制内容：

<pre class="brush:bash">xxd utf8.txt
</pre>

输入如下：

<pre class="brush:plain">61e4 b8ad e59b bd0a
</pre>

0x61是小写字母a, 0x0A是换行符：

<pre class="brush:plain">0x61 = 01100001 = 97(a)
0x0A = 00001010 = 10(new line)
0xE4B8AD = 1110 0100 10111000 10101101 => 0100 1110 0010 1101 = 4E2D(中)
0xE59BBD = 1110 0101 10011011 10111101 => 0101 0110 1111 1101 = 56FD(国)
</pre>

### Work with Regular expression

在正则中有时需要匹配中文字符，不同语言表示有点不同：

JavaScript示例：

<pre class="brush:javascript">var str = 'abc中国def';
str.match('\u4e2d')
str.match('中')
</pre>

PHP示例：

<pre class="brush:php">$str = 'abc中国def';
preg_match('/中/', $str, $match);//UTF-8 file
preg_match('/\x{4e2d}/u', $str, $match);
</pre>

## 4. Conclusion

GB2312/GBK是用两个字节编码中文，而UTF-8需要3个字符，所以不需要考虑国际化因素，使用GB2312可以节约一点空间，对于网页来说可以减少传输内容，节约一点带宽资源。但在国际化的大环境中，UTF-8还是首选。

## 5. Referecne

1. [ Windows Code page identifiers][2].  
2. [Character encoding(Wikipedia)][3].  
3. [RFC3629, UTF-8][4].  
4. [Unicode official site][5].  
5. [Unicode 6.1.0][6].  
6. [Unicode 6.1 Character Code Charts][7].

 [1]: http://www.unicode.org/charts/unihan.html
 [2]: http://msdn.microsoft.com/en-us/library/windows/desktop/dd317756(v=vs.85).aspx "Windows Code page identifiers"
 [3]: http://en.wikipedia.org/wiki/Character_encoding
 [4]: http://tools.ietf.org/html/rfc3629
 [5]: http://www.unicode.org/
 [6]: http://www.unicode.org/versions/Unicode6.1.0/
 [7]: http://www.unicode.org/charts/