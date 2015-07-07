---
title: Comparison of HP Cloud xsmall and AWS EC2 micro
author: James Tang
layout: post
permalink: /server/comparison-of-hp-cloud-xsmall-and-aws-ec2-micro/
categories:
  - Server
tags:
  - AWS
  - Cloud
  - EC2
  - HP Cloud
  - Linux
  - PHP
---
Both HP Cloud and AWS provides free trial for their basic services([HP Cloud xsmall][1] and [AWS EC2 micro][2]) for testing purpose. This post did some rough benchmarks on both platform targeted for web application, the server may not be carefully optimized.

## 1. Basic Information

<table>
  <tr>
    <th>
    </th>
    
    <th>
      AWS EC2 Micro
    </th>
    
    <th>
      HP Cloud xSmall
    </th>
  </tr>
  
  <tr>
    <th>
      CPU
    </th>
    
    <td>
      1-2 <a href="http://aws.amazon.com/ec2/faqs/#What_is_an_EC2_Compute_Unit_and_why_did_you_introduce_it">EC2 Compute Unit</td> 
      
      <td>
        1 HP Cloud Compute Units
      </td></tr> 
      
      <tr>
        <th>
          RAM
        </th>
        
        <td>
          613MB
        </td>
        
        <td>
          1GB
        </td>
      </tr>
      
      <tr>
        <th>
          IO Performance
        </th>
        
        <td>
          Low
        </td>
        
        <td>
          (Low)*
        </td>
      </tr>
      
      <tr>
        <th>
          Price
        </th>
        
        <td>
          $0.020/hr
        </td>
        
        <td>
          $0.035/hr
        </td>
      </tr>
      
      <tr>
        <th>
          OS(many OS available)
        </th>
        
        <td>
          Ubuntu 12.10 x64
        </td>
        
        <td>
          CentOS 6.3 x64
        </td>
      </tr></table> 
      
      <h2>
        2. UnixBench
      </h2>
      
      <p>
        <a href="http://code.google.com/p/byte-unixbench/">UnixBench</a> was used as the benchmark tool.
      </p>
      
      <p>
        Comparison diagram:<br /> <a href="http://tangobean.com/wp-content/uploads/2013/03/aws-hpcloud1.png"><img src="http://tangobean.com/wp-content/uploads/2013/03/aws-hpcloud1-500x325.png" alt="aws-hpcloud" width="500" height="325" class="alignnone size-medium wp-image-591" /></a>
      </p>
      
      <p>
        Benchmark result of AWS EC2 Micro:
      </p>
      
      <pre class="brush:plain">
BYTE UNIX Benchmarks (Version 5.1.3)

   System: ip-10-120-25-175: GNU/Linux
   OS: GNU/Linux -- 3.5.0-21-generic -- #32-Ubuntu SMP Tue Dec 11 18:51:59 UTC 2012
   Machine: x86_64 (x86_64)
   Language: en_US.utf8 (charmap="UTF-8", collate="UTF-8")
   CPU 0: Intel(R) Xeon(R) CPU E5645 @ 2.40GHz (4000.1 bogomips)
          Hyper-Threading, x86-64, MMX, Physical Address Ext, SYSENTER/SYSEXIT, SYSCALL/SYSRET
   10:48:54 up 10:07,  1 user,  load average: 0.00, 0.01, 0.05; runlevel 2

------------------------------------------------------------------------
Benchmark Run: Sat Mar 09 2013 10:48:54 - 11:18:36
1 CPU in system; running 1 parallel copy of tests

Dhrystone 2 using register variables        9126922.6 lps   (10.0 s, 7 samples)
Double-Precision Whetstone                     1074.0 MWIPS (21.0 s, 7 samples)
Execl Throughput                                206.9 lps   (29.8 s, 2 samples)
File Copy 1024 bufsize 2000 maxblocks        103291.3 KBps  (30.1 s, 2 samples)
File Copy 256 bufsize 500 maxblocks           28033.1 KBps  (30.1 s, 2 samples)
File Copy 4096 bufsize 8000 maxblocks        347680.6 KBps  (30.1 s, 2 samples)
Pipe Throughput                               85116.7 lps   (10.1 s, 7 samples)
Pipe-based Context Switching                  14082.6 lps   (10.1 s, 7 samples)
Process Creation                                763.3 lps   (30.1 s, 2 samples)
Shell Scripts (1 concurrent)                    483.7 lpm   (60.2 s, 2 samples)
Shell Scripts (8 concurrent)                     62.2 lpm   (60.4 s, 2 samples)
System Call Overhead                          83998.3 lps   (10.1 s, 7 samples)

System Benchmarks Index Values               BASELINE       RESULT    INDEX
Dhrystone 2 using register variables         116700.0    9126922.6    782.1
Double-Precision Whetstone                       55.0       1074.0    195.3
Execl Throughput                                 43.0        206.9     48.1
File Copy 1024 bufsize 2000 maxblocks          3960.0     103291.3    260.8
File Copy 256 bufsize 500 maxblocks            1655.0      28033.1    169.4
File Copy 4096 bufsize 8000 maxblocks          5800.0     347680.6    599.4
Pipe Throughput                               12440.0      85116.7     68.4
Pipe-based Context Switching                   4000.0      14082.6     35.2
Process Creation                                126.0        763.3     60.6
Shell Scripts (1 concurrent)                     42.4        483.7    114.1
Shell Scripts (8 concurrent)                      6.0         62.2    103.7
System Call Overhead                          15000.0      83998.3     56.0
                                                                   ========
System Benchmarks Index Score                                         127.7

</pre>
      
      <p>
        Benchmark result of HP Cloud xSmall:
      </p>
      
      <pre class="brush:plain">
BYTE UNIX Benchmarks (Version 5.1.3)

   System: server-1362490335-az-1-region-a-geo-1: GNU/Linux
   OS: GNU/Linux -- 2.6.32-279.19.1.el6.x86_64 -- #1 SMP Wed Dec 19 07:05:20 UTC 2012
   Machine: x86_64 (x86_64)
   Language: en_US.utf8 (charmap="UTF-8", collate="UTF-8")
   CPU 0: QEMU Virtual CPU version 0.14.0 (5333.5 bogomips)
          x86-64, MMX, Physical Address Ext, SYSCALL/SYSRET
   08:05:42 up 2 days, 18:32,  1 user,  load average: 0.00, 0.00, 0.00; runlevel 3


------------------------------------------------------------------------
Benchmark Run: Fri Mar 08 2013 08:05:42 - 08:33:53
1 CPU in system; running 1 parallel copy of tests


Dhrystone 2 using register variables       27022690.5 lps   (10.0 s, 7 samples)
Double-Precision Whetstone                     3184.2 MWIPS (9.9 s, 7 samples)
Execl Throughput                               3113.1 lps   (30.0 s, 2 samples)
File Copy 1024 bufsize 2000 maxblocks        600120.0 KBps  (30.0 s, 2 samples)
File Copy 256 bufsize 500 maxblocks          164090.4 KBps  (30.0 s, 2 samples)
File Copy 4096 bufsize 8000 maxblocks       1346652.0 KBps  (30.0 s, 2 samples)
Pipe Throughput                             1282607.3 lps   (10.0 s, 7 samples)
Pipe-based Context Switching                 202747.4 lps   (10.0 s, 7 samples)
Process Creation                               9705.7 lps   (30.0 s, 2 samples)
Shell Scripts (1 concurrent)                   4344.3 lpm   (60.0 s, 2 samples)
Shell Scripts (8 concurrent)                    584.1 lpm   (60.0 s, 2 samples)
System Call Overhead                        1852906.6 lps   (10.0 s, 7 samples)

System Benchmarks Index Values               BASELINE       RESULT    INDEX
Dhrystone 2 using register variables         116700.0   27022690.5   2315.6
Double-Precision Whetstone                       55.0       3184.2    578.9
Execl Throughput                                 43.0       3113.1    724.0
File Copy 1024 bufsize 2000 maxblocks          3960.0     600120.0   1515.5
File Copy 256 bufsize 500 maxblocks            1655.0     164090.4    991.5
File Copy 4096 bufsize 8000 maxblocks          5800.0    1346652.0   2321.8
Pipe Throughput                               12440.0    1282607.3   1031.0
Pipe-based Context Switching                   4000.0     202747.4    506.9
Process Creation                                126.0       9705.7    770.3
Shell Scripts (1 concurrent)                     42.4       4344.3   1024.6
Shell Scripts (8 concurrent)                      6.0        584.1    973.5
System Call Overhead                          15000.0    1852906.6   1235.3
                                                                   ========
System Benchmarks Index Score                                        1044.2
</pre>
      
      <p>
        It seems that the HP Cloud xSmall is more powerful than AWS EC2 micro. Besides the RAM and CPU difference, this may also caused by the different virtualization solution of both platform.
      </p>
      
      <h2>
        3. Apache benchmark(ab) for static page
      </h2>
      
      <p>
        Web server: Nginx 1.2.7<br /> PHP(PHP-FPM): 5.4.12
      </p>
      
      <h3>
        Benchmark with static page
      </h3>
      
      <p>
        Static page benchmark with n=1000 and c=100:
      </p>
      
      <table>
        <tr>
          <th>
          </th>
          
          <th>
            AWS EC2 micro
          </th>
          
          <th>
            HP Cloud xSmall
          </th>
        </tr>
        
        <tr>
          <th>
            Requests per second
          </th>
          
          <td>
            107.12
          </td>
          
          <td>
            10.47
          </td>
        </tr>
        
        <tr>
          <th>
            Time per request
          </th>
          
          <td>
            933.491
          </td>
          
          <td>
            9554.962
          </td>
        </tr>
        
        <tr>
          <th>
            Time per request(all concurrent)
          </th>
          
          <td>
            9.335
          </td>
          
          <td>
            95.550
          </td>
        </tr>
        
        <tr>
          <th>
            Transfer rate
          </th>
          
          <td>
            9936.57
          </td>
          
          <td>
            31.64
          </td>
        </tr>
      </table>
      
      <h3>
        Benchmark with WordPress home page
      </h3>
      
      <p>
        Another benchmark with WordPress home page was run on both server, with -n of 1000.
      </p>
      
      <p>
        AWS EC3 micro result:
      </p>
      
      <table>
        <tr>
          <th>
          </th>
          
          <th>
            c=100
          </th>
          
          <th>
            c=200
          </th>
          
          <th>
            c=500
          </th>
          
          <th>
            c=600
          </th>
        </tr>
        
        <tr>
          <th>
            Requests per second
          </th>
          
          <td>
            113.69
          </td>
          
          <td>
            161.25
          </td>
          
          <td>
            117.59
          </td>
          
          <td>
            73.77
          </td>
        </tr>
        
        <tr>
          <th>
            Time per request
          </th>
          
          <td>
            879.603
          </td>
          
          <td>
            1240.280
          </td>
          
          <td>
            4251.989
          </td>
          
          <td>
            8133.916
          </td>
        </tr>
        
        <tr>
          <th>
            Time per request(all concurrent)
          </th>
          
          <td>
            8.796
          </td>
          
          <td>
            6.201
          </td>
          
          <td>
            8.504
          </td>
          
          <td>
            13.557
          </td>
        </tr>
        
        <tr>
          <th>
            Transfer rate
          </th>
          
          <td>
            5087.52
          </td>
          
          <td>
            7314.16
          </td>
          
          <td>
            5325.26
          </td>
          
          <td>
            3306.62
          </td>
        </tr>
      </table>
      
      <p>
        HP Cloud xSmall Benchmark result:
      </p>
      
      <table>
        <tr>
          <th>
          </th>
          
          <th>
            c=100
          </th>
          
          <th>
            c=200
          </th>
          
          <th>
            c=500
          </th>
          
          <th>
            c=600
          </th>
        </tr>
        
        <tr>
          <th>
            Requests per second
          </th>
          
          <td>
            62.57
          </td>
          
          <td>
            57.19
          </td>
          
          <td>
            54.67
          </td>
          
          <td>
            51.81
          </td>
        </tr>
        
        <tr>
          <th>
            Time per request
          </th>
          
          <td>
            1598.324
          </td>
          
          <td>
            3496.824
          </td>
          
          <td>
            9146.226
          </td>
          
          <td>
            11581.078
          </td>
        </tr>
        
        <tr>
          <th>
            Time per request(all concurrent)
          </th>
          
          <td>
            15.983
          </td>
          
          <td>
            17.484
          </td>
          
          <td>
            18.292
          </td>
          
          <td>
            19.302
          </td>
        </tr>
        
        <tr>
          <th>
            Transfer rate
          </th>
          
          <td>
            2755.85
          </td>
          
          <td>
            2513.70
          </td>
          
          <td>
            2416.69
          </td>
          
          <td>
            2279.40
          </td>
        </tr>
      </table>
      
      <p>
        Notice the results of HP Cloud xSmall, the static page results were far more lower than the WordPress home page, this may be caused by the low performance of I/O.
      </p>
      
      <h2>
        4. Conclusion
      </h2>
      
      <p>
        It has no doubt that AWS EC2 Micro was sufficient for personal website with low traffic. Although the UnixBench result of HP Cloud xSmall was good than AWS EC2 Micro, but the overall performance for web application was not satisfactory. Considering the maturity and reliability of platform, AWS EC2 Micro must be a good choice for personal web site.
      </p>

 [1]: https://www.hpcloud.com/free-trial
 [2]: http://aws.amazon.com/ec2/instance-types/