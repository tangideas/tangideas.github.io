---
title: 'Bar Chart with R: a demo'
author: James Tang
layout: post
permalink: /notes/bar-chart-with-r-a-demo/
categories:
  - Notes
tags:
  - Chart
  - R
---
It&#8217;s very common to visualize comparison data sets with bar chart. Below is such a data set stored in a file, named &#8220;benchmark-result.csv&#8221; for example.

<pre class="brush:plain">System Benchmarks Index Values,               HPCloud,     AWS-Micro
Dhrystone 2 using register variables,          2315.6,      782.1
Double-Precision Whetstone,                     578.9,      195.3
Execl Throughput,                               724.0,       48.1
File Copy 1024 bufsize 2000 maxblocks,         1515.5,      260.8
File Copy 256 bufsize 500 maxblocks,            991.5,      169.4
File Copy 4096 bufsize 8000 maxblocks,         2321.8,      599.4
Pipe Throughput,                               1031.0,       68.4
Pipe-based Context Switching,                   506.9,       35.2
Process Creation,                               770.3,       60.6
Shell Scripts (1 concurrent),                  1024.6,      114.1
Shell Scripts (8 concurrent),                   973.5,      103.7
System Call Overhead,                          1235.3,       56.0
System Benchmarks Index Score ,                1044.2,      127.7
</pre>

Now launch to R shell, and run following scripts:

1. Load data:

<pre class="brush:bash">benchmark = read.table('/home/james/benchmark-result.csv', header=TRUE, sep=',')
</pre>

2. Create matrix:

<pre class="brush:bash">benchmark.matrix = as.matrix(benchmark[2:3])
</pre>

3. Set row names:

<pre class="brush:bash">rownames(benchmark.matrix) &lt;- benchmark[,1]
</pre>

4. Open graphics device for PNG:

<pre class="brush:bash">png(filename="/home/james/benchmark-result-barchart.png", width=800, height=520, unit="px")
</pre>

5. Generate the bar chart:

<pre class="brush:bash">barplot(t(benchmark.matrix), beside=TRUE, legend=TRUE, ylab="Index Value", cex.names=.75)
</pre>

6. Close graphics device:

<pre class="brush:bash">dev.off()
</pre>

Done.