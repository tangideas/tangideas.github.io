---
title: Add a new disk to volume group
author: James Tang
layout: post
permalink: /server/add-a-new-disk-to-volume-group/
categories:
  - Server
tags:
  - Linux
  - LVM
---
Extensible is one advantage of [LVM][1], we can extend the logical volumes easily and do not have to umount partitions and move existing data.

1. Create one or more partitions for the new disk, e.g. /dev/sdb.

<pre class="brush:bash">fdisk /dev/sdb
</pre>

2. Format the partitions, e.g. /dev/sdb1.

<pre class="brush:bash">mkfs -t ext4 -c /dev/sdb1
</pre>

3. Change the system id for partitions, e.g. /dev/sdb1, to Linux LVM(8e)

[<img src="http://tangobean.com/wp-content/uploads/2012/07/disk-partition-system-id-e1342013234634.png" alt="" title="disk-partition-system-id" width="486" height="184" class="alignnone size-full wp-image-477" />][2]

4. Initial partition for LVM

<pre class="brush:bash">pvcreate /dev/sdb1
</pre>

5. Add partition to existing volume group, e.g. vg_01.

<pre class="brush:bash">vgextend vg_01 /dev/sdb1
</pre>

6. Extend the size of the logical volume.

Extend the size of lv01 by the amount of free space on /dev/sdb1:

<pre class="brush:bash">lvextend /dev/vg_01/lv01 /dev/sdb1
</pre>

Or, extend specified size:

<pre class="brush:bash">lvextend -L +50G /dev/vg_01/lv01
</pre>

7. Online resize the logical volume.

<pre class="brush:bash">resize2fs /dev/vg_01/lv01
</pre>

Done.

 [1]: http://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux) "LVM"
 [2]: http://tangobean.com/wp-content/uploads/2012/07/disk-partition-system-id.png