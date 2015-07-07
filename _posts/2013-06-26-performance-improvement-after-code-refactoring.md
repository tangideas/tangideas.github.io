---
title: Performance improvement after code refactoring
author: James Tang
layout: post
permalink: /code/performance-improvement-after-code-refactoring/
categories:
  - Code
  - Notes
tags:
  - PHP
  - Refactoring
---
I took over an existing project recently. Unfortunately, the code is difficult to understand and maintain: the code was written in procedural with many duplicated codes. It&#8217;s really painful to add new features, even modify a simple testing condition, I have to scroll up and down in a single code file with more than thousands of lines. So the primary objctive of refactoring is to improve the code design, in order to make it more easier to understand and maintain. I spent a few days to analyze the existing code, and rewritten the code in object-oriented paradigm.

After refactoring, I tested and compared the performance of both codes. The result is encouraging, the new code is running more stable and has a overall improvement in executing time. For this project is not providing simple web page services, the executing time highly depends on the specific user request context, so it&#8217;s difficult to write testing cases for benchmark. For this reason, I extracted the statistical executing time from real running environment for both version of code.

I will call the old version code and the new version code as V1 and V2 respectively. Following data and plots show the response times for server running at relatively low and high load. Every set of data was generated in one hour.

### Low load situation

V1 response time

<table width="534" border="0" cellspacing="0" cellpadding="0">
  <colgroup> <col width="206" /> <col width="116" /> <col span="2" width="106" /> </colgroup> <tr>
    <td width="206" height="18">
      Resp. Time Range(ms)
    </td>
    
    <td width="116">
      Resp Count
    </td>
    
    <td width="106">
      Percentage
    </td>
    
    <td width="106">
      Accu. Per.
    </td>
  </tr>
  
  <tr>
    <td height="18">
      0-10
    </td>
    
    <td>
           334,970
    </td>
    
    <td align="right">
      4.72%
    </td>
    
    <td align="right">
      4.72%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      10-20
    </td>
    
    <td>
         1,383,553
    </td>
    
    <td align="right">
      19.48%
    </td>
    
    <td align="right">
      24.19%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      20-30
    </td>
    
    <td>
        3,511,391
    </td>
    
    <td align="right">
      49.43%
    </td>
    
    <td align="right">
      73.62%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      30-40
    </td>
    
    <td>
         1,534,209
    </td>
    
    <td align="right">
      21.60%
    </td>
    
    <td align="right">
      95.22%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      40-50
    </td>
    
    <td>
           129,206
    </td>
    
    <td align="right">
      1.82%
    </td>
    
    <td align="right">
      97.04%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      50-60
    </td>
    
    <td>
            45,071
    </td>
    
    <td align="right">
      0.63%
    </td>
    
    <td align="right">
      97.67%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      60-70
    </td>
    
    <td>
            30,191
    </td>
    
    <td align="right">
      0.42%
    </td>
    
    <td align="right">
      98.10%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      70-80
    </td>
    
    <td>
            15,793
    </td>
    
    <td align="right">
      0.22%
    </td>
    
    <td align="right">
      98.32%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      80-90
    </td>
    
    <td>
             8,785
    </td>
    
    <td align="right">
      0.12%
    </td>
    
    <td align="right">
      98.44%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      90-100
    </td>
    
    <td>
            6,728
    </td>
    
    <td align="right">
      0.09%
    </td>
    
    <td align="right">
      98.54%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      100-110
    </td>
    
    <td>
             5,266
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      98.61%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      110-120
    </td>
    
    <td>
             4,149
    </td>
    
    <td align="right">
      0.06%
    </td>
    
    <td align="right">
      98.67%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      120-130
    </td>
    
    <td>
             3,402
    </td>
    
    <td align="right">
      0.05%
    </td>
    
    <td align="right">
      98.72%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      130-140
    </td>
    
    <td>
             3,135
    </td>
    
    <td align="right">
      0.04%
    </td>
    
    <td align="right">
      98.76%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      140-150
    </td>
    
    <td>
             3,155
    </td>
    
    <td align="right">
      0.04%
    </td>
    
    <td align="right">
      98.81%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      150-160
    </td>
    
    <td>
             3,116
    </td>
    
    <td align="right">
      0.04%
    </td>
    
    <td align="right">
      98.85%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      160-170
    </td>
    
    <td>
             2,908
    </td>
    
    <td align="right">
      0.04%
    </td>
    
    <td align="right">
      98.89%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      170-180
    </td>
    
    <td>
             3,280
    </td>
    
    <td align="right">
      0.05%
    </td>
    
    <td align="right">
      98.94%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      180-190
    </td>
    
    <td>
             3,961
    </td>
    
    <td align="right">
      0.06%
    </td>
    
    <td align="right">
      98.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      190-200
    </td>
    
    <td>
             4,786
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      99.06%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      200-300
    </td>
    
    <td>
            35,690
    </td>
    
    <td align="right">
      0.50%
    </td>
    
    <td align="right">
      99.56%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      300-400
    </td>
    
    <td>
            17,350
    </td>
    
    <td align="right">
      0.24%
    </td>
    
    <td align="right">
      99.81%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      400-500
    </td>
    
    <td>
             1,630
    </td>
    
    <td align="right">
      0.02%
    </td>
    
    <td align="right">
      99.83%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      500-600
    </td>
    
    <td>
             1,028
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.84%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      600-700
    </td>
    
    <td>
             1,187
    </td>
    
    <td align="right">
      0.02%
    </td>
    
    <td align="right">
      99.86%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      700-800
    </td>
    
    <td>
               623
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.87%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      800-900
    </td>
    
    <td>
               150
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.87%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      900-1000
    </td>
    
    <td>
                46
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.87%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      1000+
    </td>
    
    <td>
             9,080
    </td>
    
    <td align="right">
      0.13%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
</table>

[<img src="http://tangobean.com/wp-content/uploads/2013/06/16-v1-500x258.jpg" alt="16-v1" width="500" height="258" class="alignnone size-medium wp-image-767" />][1]

[<img src="http://tangobean.com/wp-content/uploads/2013/06/16-v1-accu-500x287.jpg" alt="16-v1-accu" width="500" height="287" class="alignnone size-medium wp-image-768" />][2]

V2 response time

<table width="524" border="0" cellspacing="0" cellpadding="0">
  <colgroup> <col width="206" /> <col span="3" width="106" /> </colgroup> <tr>
    <td width="206" height="18">
      Resp. Time Range(ms)
    </td>
    
    <td width="106">
      Resp Count
    </td>
    
    <td width="106">
      Percentage
    </td>
    
    <td width="106">
      Accu. Per.
    </td>
  </tr>
  
  <tr>
    <td height="18">
      0-10
    </td>
    
    <td align="right">
      20,258
    </td>
    
    <td align="right">
      0.28%
    </td>
    
    <td align="right">
      0.28%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      10-20
    </td>
    
    <td align="right">
      4,475,788
    </td>
    
    <td align="right">
      61.01%
    </td>
    
    <td align="right">
      61.29%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      20-30
    </td>
    
    <td align="right">
      2,623,493
    </td>
    
    <td align="right">
      35.76%
    </td>
    
    <td align="right">
      97.05%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      30-40
    </td>
    
    <td align="right">
      76,568
    </td>
    
    <td align="right">
      1.04%
    </td>
    
    <td align="right">
      98.09%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      40-50
    </td>
    
    <td align="right">
      30,340
    </td>
    
    <td align="right">
      0.41%
    </td>
    
    <td align="right">
      98.51%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      50-60
    </td>
    
    <td align="right">
      12,895
    </td>
    
    <td align="right">
      0.18%
    </td>
    
    <td align="right">
      98.68%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      60-70
    </td>
    
    <td align="right">
      5,498
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      98.76%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      70-80
    </td>
    
    <td align="right">
      5,129
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      98.83%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      80-90
    </td>
    
    <td align="right">
      8,560
    </td>
    
    <td align="right">
      0.12%
    </td>
    
    <td align="right">
      98.94%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      90-100
    </td>
    
    <td align="right">
      10,141
    </td>
    
    <td align="right">
      0.14%
    </td>
    
    <td align="right">
      99.08%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      100-110
    </td>
    
    <td align="right">
      9,741
    </td>
    
    <td align="right">
      0.13%
    </td>
    
    <td align="right">
      99.21%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      110-120
    </td>
    
    <td align="right">
      7,714
    </td>
    
    <td align="right">
      0.11%
    </td>
    
    <td align="right">
      99.32%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      120-130
    </td>
    
    <td align="right">
      5,519
    </td>
    
    <td align="right">
      0.08%
    </td>
    
    <td align="right">
      99.39%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      130-140
    </td>
    
    <td align="right">
      3,668
    </td>
    
    <td align="right">
      0.05%
    </td>
    
    <td align="right">
      99.44%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      140-150
    </td>
    
    <td align="right">
      2,297
    </td>
    
    <td align="right">
      0.03%
    </td>
    
    <td align="right">
      99.48%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      150-160
    </td>
    
    <td align="right">
      1,615
    </td>
    
    <td align="right">
      0.02%
    </td>
    
    <td align="right">
      99.50%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      160-170
    </td>
    
    <td align="right">
      1,176
    </td>
    
    <td align="right">
      0.02%
    </td>
    
    <td align="right">
      99.51%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      170-180
    </td>
    
    <td align="right">
      918
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.53%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      180-190
    </td>
    
    <td align="right">
      666
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.53%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      190-200
    </td>
    
    <td align="right">
      489
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.54%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      200-300
    </td>
    
    <td align="right">
      14,112
    </td>
    
    <td align="right">
      0.19%
    </td>
    
    <td align="right">
      99.73%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      300-400
    </td>
    
    <td align="right">
      18,831
    </td>
    
    <td align="right">
      0.26%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      400-500
    </td>
    
    <td align="right">
      131
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      500-600
    </td>
    
    <td align="right">
      126
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      600-700
    </td>
    
    <td align="right">
      77
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      700-800
    </td>
    
    <td align="right">
      27
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      800-900
    </td>
    
    <td align="right">
      25
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      900-1000
    </td>
    
    <td align="right">
      30
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      1000+
    </td>
    
    <td align="right">
      288
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
</table>

[<img src="http://tangobean.com/wp-content/uploads/2013/06/16-v2-500x258.jpg" alt="16-v2" width="500" height="258" class="alignnone size-medium wp-image-769" />][3]

[<img src="http://tangobean.com/wp-content/uploads/2013/06/16-v2-accu-500x287.png" alt="16-v2-accu" width="500" height="287" class="alignnone size-medium wp-image-770" />][4]

### High load situation

V1 response time

<table width="524" border="0" cellspacing="0" cellpadding="0">
  <colgroup> <col width="206" /> <col span="3" width="106" /> </colgroup> <tr>
    <td width="206" height="18">
      Resp. Time Range(ms)
    </td>
    
    <td width="106">
      Resp Count
    </td>
    
    <td width="106">
      Percentage
    </td>
    
    <td width="106">
      Accu. Per.
    </td>
  </tr>
  
  <tr>
    <td height="18">
      0-10
    </td>
    
    <td align="right">
      125,181
    </td>
    
    <td align="right">
      0.99%
    </td>
    
    <td align="right">
      0.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      10-20
    </td>
    
    <td align="right">
      2,003,708
    </td>
    
    <td align="right">
      15.83%
    </td>
    
    <td align="right">
      16.82%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      20-30
    </td>
    
    <td align="right">
      766,219
    </td>
    
    <td align="right">
      6.05%
    </td>
    
    <td align="right">
      22.87%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      30-40
    </td>
    
    <td align="right">
      2,759,891
    </td>
    
    <td align="right">
      21.80%
    </td>
    
    <td align="right">
      44.68%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      40-50
    </td>
    
    <td align="right">
      2,793,375
    </td>
    
    <td align="right">
      22.07%
    </td>
    
    <td align="right">
      66.74%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      50-60
    </td>
    
    <td align="right">
      1,460,567
    </td>
    
    <td align="right">
      11.54%
    </td>
    
    <td align="right">
      78.28%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      60-70
    </td>
    
    <td align="right">
      711,881
    </td>
    
    <td align="right">
      5.62%
    </td>
    
    <td align="right">
      83.91%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      70-80
    </td>
    
    <td align="right">
      351,485
    </td>
    
    <td align="right">
      2.78%
    </td>
    
    <td align="right">
      86.68%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      80-90
    </td>
    
    <td align="right">
      174,165
    </td>
    
    <td align="right">
      1.38%
    </td>
    
    <td align="right">
      88.06%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      90-100
    </td>
    
    <td align="right">
      88,230
    </td>
    
    <td align="right">
      0.70%
    </td>
    
    <td align="right">
      88.76%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      100-110
    </td>
    
    <td align="right">
      48,826
    </td>
    
    <td align="right">
      0.39%
    </td>
    
    <td align="right">
      89.14%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      110-120
    </td>
    
    <td align="right">
      30,767
    </td>
    
    <td align="right">
      0.24%
    </td>
    
    <td align="right">
      89.39%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      120-130
    </td>
    
    <td align="right">
      20,802
    </td>
    
    <td align="right">
      0.16%
    </td>
    
    <td align="right">
      89.55%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      130-140
    </td>
    
    <td align="right">
      15,563
    </td>
    
    <td align="right">
      0.12%
    </td>
    
    <td align="right">
      89.67%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      140-150
    </td>
    
    <td align="right">
      12,479
    </td>
    
    <td align="right">
      0.10%
    </td>
    
    <td align="right">
      89.77%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      150-160
    </td>
    
    <td align="right">
      10,540
    </td>
    
    <td align="right">
      0.08%
    </td>
    
    <td align="right">
      89.86%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      160-170
    </td>
    
    <td align="right">
      9,458
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      89.93%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      170-180
    </td>
    
    <td align="right">
      8,818
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      90.00%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      180-190
    </td>
    
    <td align="right">
      8,447
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      90.07%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      190-200
    </td>
    
    <td align="right">
      8,763
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      90.14%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      200-300
    </td>
    
    <td align="right">
      78,584
    </td>
    
    <td align="right">
      0.62%
    </td>
    
    <td align="right">
      90.76%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      300-400
    </td>
    
    <td align="right">
      49,607
    </td>
    
    <td align="right">
      0.39%
    </td>
    
    <td align="right">
      91.15%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      400-500
    </td>
    
    <td align="right">
      12,389
    </td>
    
    <td align="right">
      0.10%
    </td>
    
    <td align="right">
      91.25%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      500-600
    </td>
    
    <td align="right">
      9,764
    </td>
    
    <td align="right">
      0.08%
    </td>
    
    <td align="right">
      91.32%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      600-700
    </td>
    
    <td align="right">
      10,466
    </td>
    
    <td align="right">
      0.08%
    </td>
    
    <td align="right">
      91.41%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      700-800
    </td>
    
    <td align="right">
      8,982
    </td>
    
    <td align="right">
      0.07%
    </td>
    
    <td align="right">
      91.48%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      800-900
    </td>
    
    <td align="right">
      7,357
    </td>
    
    <td align="right">
      0.06%
    </td>
    
    <td align="right">
      91.54%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      900-1000
    </td>
    
    <td align="right">
      5,113
    </td>
    
    <td align="right">
      0.04%
    </td>
    
    <td align="right">
      91.58%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      1000+
    </td>
    
    <td align="right">
      1,066,330
    </td>
    
    <td align="right">
      8.42%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
</table>

[<img src="http://tangobean.com/wp-content/uploads/2013/06/22-v1-500x258.jpg" alt="22-v1" width="500" height="258" class="alignnone size-medium wp-image-771" />][5]

[<img src="http://tangobean.com/wp-content/uploads/2013/06/22-v1-accu-500x287.jpg" alt="22-v1-accu" width="500" height="287" class="alignnone size-medium wp-image-772" />][6]

V2 response time

<table width="524" border="0" cellspacing="0" cellpadding="0">
  <colgroup> <col width="206" /> <col span="3" width="106" /> </colgroup> <tr>
    <td width="206" height="18">
      Resp. Time Range(ms)
    </td>
    
    <td width="106">
      Resp Count
    </td>
    
    <td width="106">
      Percentage
    </td>
    
    <td width="106">
      Accu. Per.
    </td>
  </tr>
  
  <tr>
    <td height="18">
      0-10
    </td>
    
    <td align="right">
      11,619
    </td>
    
    <td align="right">
      0.09%
    </td>
    
    <td align="right">
      0.09%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      10-20
    </td>
    
    <td align="right">
      3,423,981
    </td>
    
    <td align="right">
      27.37%
    </td>
    
    <td align="right">
      27.46%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      20-30
    </td>
    
    <td align="right">
      7,841,554
    </td>
    
    <td align="right">
      62.68%
    </td>
    
    <td align="right">
      90.14%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      30-40
    </td>
    
    <td align="right">
      862,265
    </td>
    
    <td align="right">
      6.89%
    </td>
    
    <td align="right">
      97.03%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      40-50
    </td>
    
    <td align="right">
      119,273
    </td>
    
    <td align="right">
      0.95%
    </td>
    
    <td align="right">
      97.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      50-60
    </td>
    
    <td align="right">
      54,839
    </td>
    
    <td align="right">
      0.44%
    </td>
    
    <td align="right">
      98.43%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      60-70
    </td>
    
    <td align="right">
      23,996
    </td>
    
    <td align="right">
      0.19%
    </td>
    
    <td align="right">
      98.62%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      70-80
    </td>
    
    <td align="right">
      12,736
    </td>
    
    <td align="right">
      0.10%
    </td>
    
    <td align="right">
      98.72%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      80-90
    </td>
    
    <td align="right">
      13,441
    </td>
    
    <td align="right">
      0.11%
    </td>
    
    <td align="right">
      98.83%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      90-100
    </td>
    
    <td align="right">
      17,846
    </td>
    
    <td align="right">
      0.14%
    </td>
    
    <td align="right">
      98.97%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      100-110
    </td>
    
    <td align="right">
      17,427
    </td>
    
    <td align="right">
      0.14%
    </td>
    
    <td align="right">
      99.11%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      110-120
    </td>
    
    <td align="right">
      15,161
    </td>
    
    <td align="right">
      0.12%
    </td>
    
    <td align="right">
      99.23%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      120-130
    </td>
    
    <td align="right">
      11,330
    </td>
    
    <td align="right">
      0.09%
    </td>
    
    <td align="right">
      99.32%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      130-140
    </td>
    
    <td align="right">
      7,900
    </td>
    
    <td align="right">
      0.06%
    </td>
    
    <td align="right">
      99.38%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      140-150
    </td>
    
    <td align="right">
      5,229
    </td>
    
    <td align="right">
      0.04%
    </td>
    
    <td align="right">
      99.43%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      150-160
    </td>
    
    <td align="right">
      3,720
    </td>
    
    <td align="right">
      0.03%
    </td>
    
    <td align="right">
      99.46%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      160-170
    </td>
    
    <td align="right">
      2,581
    </td>
    
    <td align="right">
      0.02%
    </td>
    
    <td align="right">
      99.48%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      170-180
    </td>
    
    <td align="right">
      1,994
    </td>
    
    <td align="right">
      0.02%
    </td>
    
    <td align="right">
      99.49%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      180-190
    </td>
    
    <td align="right">
      1,592
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.51%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      190-200
    </td>
    
    <td align="right">
      1,210
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.51%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      200-300
    </td>
    
    <td align="right">
      31,230
    </td>
    
    <td align="right">
      0.25%
    </td>
    
    <td align="right">
      99.76%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      300-400
    </td>
    
    <td align="right">
      25,771
    </td>
    
    <td align="right">
      0.21%
    </td>
    
    <td align="right">
      99.97%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      400-500
    </td>
    
    <td align="right">
      1,069
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      99.98%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      500-600
    </td>
    
    <td align="right">
      575
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.98%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      600-700
    </td>
    
    <td align="right">
      164
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.98%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      700-800
    </td>
    
    <td align="right">
      64
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      800-900
    </td>
    
    <td align="right">
      19
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      900-1000
    </td>
    
    <td align="right">
      1
    </td>
    
    <td align="right">
      0.00%
    </td>
    
    <td align="right">
      99.99%
    </td>
  </tr>
  
  <tr>
    <td height="18">
      1000+
    </td>
    
    <td align="right">
      1,807
    </td>
    
    <td align="right">
      0.01%
    </td>
    
    <td align="right">
      100.00%
    </td>
  </tr>
</table>

[<img src="http://tangobean.com/wp-content/uploads/2013/06/22-v2-500x258.jpg" alt="22-v2" width="500" height="258" class="alignnone size-medium wp-image-773" />][7]

[<img src="http://tangobean.com/wp-content/uploads/2013/06/22-v2-accu-500x287.jpg" alt="22-v2-accu" width="500" height="287" class="alignnone size-medium wp-image-774" />][8]

The plots show the V1 was fluctuant at high load situation, and V2 keep consistent at both situation. 

This post is just a experience note, not intended to compare procedural and OOP, both paradigm can be used to write excellent code, but I personally prefer OOP and believe it&#8217;s easier to write maintainable code.

 [1]: http://tangobean.com/wp-content/uploads/2013/06/16-v1.jpg
 [2]: http://tangobean.com/wp-content/uploads/2013/06/16-v1-accu.jpg
 [3]: http://tangobean.com/wp-content/uploads/2013/06/16-v2.jpg
 [4]: http://tangobean.com/wp-content/uploads/2013/06/16-v2-accu.png
 [5]: http://tangobean.com/wp-content/uploads/2013/06/22-v1.jpg
 [6]: http://tangobean.com/wp-content/uploads/2013/06/22-v1-accu.jpg
 [7]: http://tangobean.com/wp-content/uploads/2013/06/22-v2.jpg
 [8]: http://tangobean.com/wp-content/uploads/2013/06/22-v2-accu.jpg