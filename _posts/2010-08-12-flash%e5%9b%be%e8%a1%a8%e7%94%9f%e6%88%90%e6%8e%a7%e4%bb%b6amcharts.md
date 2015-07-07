---
title: Flash图表生成控件:amCharts
author: James Tang
excerpt: |
  |
    amCharts 是我用过的最好用的免费Flash图表生成控件。amCharts可以生成2D/3D柱状图、2D/3D饼图、拆线图、面积图、雷达图、气泡图、股价图等。amCharts配置文件采用XML格式，数据文件可以使用CSV和XML格式，能够方便地与任何开发语言集成。
layout: post
permalink: /code/flash%e5%9b%be%e8%a1%a8%e7%94%9f%e6%88%90%e6%8e%a7%e4%bb%b6amcharts/
categories:
  - Code
tags:
  - Chart
  - Flash
  - 图表
---
[amCharts][1] 是我用过的最好用的免费Flash图表生成控件。amCharts可以生成2D/3D柱状图、2D/3D饼图、拆线图、面积图、雷达图、气泡图、股价图等。amCharts配置文件采用XML格式，数据文件可以使用CSV和XML格式，能够方便地与任何开发语言集成。

### 2D/3D柱状图

[<img class="alignnone size-medium wp-image-48" title="bar" src="http://tangobean.com/wp-content/uploads/2010/08/bar-300x196.png" alt="" width="300" height="196" />][2]

[<img class="alignnone size-medium wp-image-47" title="bar-3d" src="http://tangobean.com/wp-content/uploads/2010/08/bar-3d-300x196.png" alt="" width="300" height="196" />][3]

### 饼图

[<img class="alignnone size-medium wp-image-51" title="pie" src="http://tangobean.com/wp-content/uploads/2010/08/pie-300x209.png" alt="" width="300" height="209" />][4]

[<img class="alignnone size-medium wp-image-50" title="pie-3d" src="http://tangobean.com/wp-content/uploads/2010/08/pie-3d-300x175.png" alt="" width="300" height="175" />][5]

### 折线/面积图

[<img class="alignnone size-medium wp-image-49" title="line" src="http://tangobean.com/wp-content/uploads/2010/08/line-300x187.png" alt="" width="300" height="187" />][6]

[<img class="alignnone size-medium wp-image-46" title="area" src="http://tangobean.com/wp-content/uploads/2010/08/area-300x200.png" alt="" width="300" height="200" />][7]

### 股价图

[<img class="alignnone size-medium wp-image-45" title="stock" src="http://tangobean.com/wp-content/uploads/2010/08/stock-300x204.png" alt="" width="300" height="204" />][8]

[<img class="alignnone size-medium wp-image-52" title="stock2" src="http://tangobean.com/wp-content/uploads/2010/08/stock2-300x190.png" alt="" width="300" height="190" />][9]

### 一段PHP颜色随机生成程序

在用程序生成Flash图表数据时，需要对每组数据生成不同的颜色，下面是一段随机生成颜色的程序。

<pre class="brush:php">class Color {

    public static function getRandomHexColors ($counts)
    {

        $colors = array();

        while ($counts &gt; 0) {

            $color = self::getRandomHexColor();

            if (!in_array($color, $colors)) {
                $colors[] = $color;
            }

            $counts--;
        }

        return $colors;

    }

    public static function getRandomHexColor () {

        return sprintf('%02X%02X%02X', mt_rand(0, 255), mt_rand(0, 255), mt_rand(0, 255));

    }
}</pre>

 [1]: http://www.amcharts.com/
 [2]: http://tangobean.com/wp-content/uploads/2010/08/bar.png
 [3]: http://tangobean.com/wp-content/uploads/2010/08/bar-3d.png
 [4]: http://tangobean.com/wp-content/uploads/2010/08/pie.png
 [5]: http://tangobean.com/wp-content/uploads/2010/08/pie-3d.png
 [6]: http://tangobean.com/wp-content/uploads/2010/08/line.png
 [7]: http://tangobean.com/wp-content/uploads/2010/08/area.png
 [8]: http://tangobean.com/wp-content/uploads/2010/08/stock.png
 [9]: http://tangobean.com/wp-content/uploads/2010/08/stock2.png