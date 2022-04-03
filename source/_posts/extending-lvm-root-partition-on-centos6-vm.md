---
title: CentOS 6 虚拟机扩容 LVM 根分区
tags:
  - CentOS, LVM, 扩容, 根分区
categories:
  - '*Nix'
date: 2019-2-28 17:48:35
keywords: CentOS, LVM, 扩容, 根分区, 虚拟机
description: 单位有一台基于 CentOS 6.3 搭建的 Cacti 监控系统，使用了很长一段时间了，最后发现 / 根分区空间已经满了，无法再提供服务，于是有了扩容的需求，以下是扩容过程：
---

环境介绍：
- VMware 5.5
- CentOS 6.3 64-bit
- / 根分区为 LVM
- 原磁盘大小 40GB
- 扩容后磁盘大小： 50GB

查看原来的磁盘文件系统空间状态， 根分区已经 100% 爆了！
```shell
[root@localhost ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       38G   38G     0 100% /
tmpfs                 246M     0  246M   0% /dev/shm
/dev/sda1             485M   32M  428M   7% /boot
```

在 VMware 中对原磁盘扩容到 50GB, 查看磁盘 sda 已经扩展到 50GB
**安全起见，在扩容前，建议对数据进行备份。**
```shell
[root@localhost ~]# fdisk /dev/sda -l

Disk /dev/sda: 53.7 GB, 53687091200 bytes
255 heads, 63 sectors/track, 6527 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00026a39

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          64      512000   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              64        5222    41430016   8e  Linux LVM
```
在本文的例子中，磁盘 /dev/sda 已经扩展至 53.7 GB 而且当前只有两个主分区，所以我们后面就直接创建主分区 sda3 来帮助扩容。

查看 PV, VG, LV 状态，大小还没有变化
```shell
[root@localhost ~]# pvs
  PV         VG       Fmt  Attr PSize  PFree
  /dev/sda2  VolGroup lvm2 a--  39.51g    0
[root@localhost ~]# vgs
  /etc/lvm/archive/.lvm_localhost.localdomain_18508_1413645104: write error failed: No space left on device
  Volume group "VolGroup" metadata archive failed.
  /etc/lvm/backup/VolGroup.tmp: write error failed: No space left on device
  Backup of volume group VolGroup metadata failed.
  VG       #PV #LV #SN Attr   VSize  VFree
  VolGroup   1   2   0 wz--n- 39.51g    0
[root@localhost ~]# lvs
  LV      VG       Attr     LSize   Pool Origin Data%  Move Log Copy%  Convert
  lv_root VolGroup -wi-ao--  38.54g
  lv_swap VolGroup -wi-ao-- 992.00m
```

接下来我们需要将扩展出未曾使用的 10GB 空间创建一个新的主分区 **sda3** 来给逻辑卷扩容用。
```shell
[root@localhost ~]# fdisk /dev/sda

WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 3
First cylinder (5222-6527, default 5222):
Using default value 5222
Last cylinder, +cylinders or +size{K,M,G} (5222-6527, default 6527):
Using default value 6527
```

然后修改分区类型为 8e 并写入分区表保存
```shell
Command (m for help): t
Partition number (1-4): 3
Hex code (type L to list codes): 8e
Changed system type of partition 3 to 8e (Linux LVM)

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

重新扫描磁盘检查分区情况
```shell
[root@localhost ~]# partx -v -a /dev/sda
device /dev/sda: start 0 size 104857600
gpt: 0 slices
dos: 4 slices
# 1:      2048-  1026047 (  1024000 sectors,    524 MB)
# 2:   1026048- 83886079 ( 82860032 sectors,  42424 MB)
# 3:  83886080-104856254 ( 20970175 sectors,  10736 MB)
# 4:         0-       -1 (        0 sectors,      0 MB)
BLKPG: Device or resource busy
error adding partition 1
BLKPG: Device or resource busy
error adding partition 2
added partition 3
```
分区 1/2 的报错可以忽略，我们需要看到的是分区 3 已经添加成功，如果看不到分区 3 或者添加失败需要重启使之生效。

创建新的 PV
```shell
[root@localhost ~]# pvcreate /dev/sda3
  Writing physical volume data to disk "/dev/sda3"
  Physical volume "/dev/sda3" successfully created
```

找到当前系统所使用的卷组名
```shell
[root@localhost ~]# vgdisplay
  --- Volume group ---
  VG Name               VolGroup
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               39.51 GiB
  PE Size               4.00 MiB
  Total PE              10114
  Alloc PE / Size       10114 / 39.51 GiB
  Free  PE / Size       0 / 0
  VG UUID               2JrSeG-4Is7-ocIQ-zVrN-eha1-gYJQ-L1UnPn

  /etc/lvm/archive/.lvm_localhost.localdomain_15862_675165963: write error failed: No space left on device
  Volume group "VolGroup" metadata archive failed.
  /etc/lvm/backup/VolGroup.tmp: write error failed: No space left on device
  Backup of volume group VolGroup metadata failed.
```
**VolGroup** 就是它了！

接下来 **vgextend** 扩容VG：
```shell
[root@localhost ~]# vgextend VolGroup /dev/sda3
  /etc/lvm/archive/.lvm_localhost.localdomain_17322_173527374: write error failed: No space left on device
  Volume group "VolGroup" metadata archive failed.
  Internal error: Attempt to unlock unlocked VG #orphans.
```
结果因为剩余空间为零扩容失败，囧， 删除掉一些文件再试。

```shell
[root@localhost ~]# vgextend VolGroup /dev/sda3
  Volume group "VolGroup" successfully extended
```

用 **pvscan** 来查看 PV 空间使用情况来决定谁给谁加以及加多少空间。
```shell
[root@localhost ~]# pvscan
  PV /dev/sda2   VG VolGroup   lvm2 [39.51 GiB / 0    free]
  PV /dev/sda3   VG VolGroup   lvm2 [10.00 GiB / 10.00 GiB free]
  Total: 2 [49.50 GiB] / in use: 2 [49.50 GiB] / in no VG: 0 [0   ]
```

在本文例子中我们需要增加 / 根分区的空间，用 **df** 命令来查看根分区所挂载的逻辑卷。
```shell
[root@localhost ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       38G  8.3G   28G  23% /
tmpfs                 246M     0  246M   0% /dev/shm
/dev/sda1             485M   32M  428M   7% /boot
```

所以我们可以看到 / 根分区是挂载在 **VolGroup-lv_root** 卷组下面的 **root** 逻辑卷

扩容 root 逻辑卷
```shell
[root@localhost ~]# lvextend -L+10G /dev/mapper/VolGroup-lv_root  /dev/sda3
  Extending logical volume lv_root to 48.54 GiB
  Insufficient free space: 2560 extents needed, but only 2559 available

[root@localhost ~]# lvextend -L+9.99G /dev/mapper/VolGroup-lv_root  /dev/sda3
  Rounding size to boundary between physical extents: 9.99 GiB
  Extending logical volume lv_root to 48.53 GiB
  Logical volume lv_root successfully resized
```

文件系统扩容:
```shell
[root@localhost ~]# resize2fs /dev/mapper/VolGroup-lv_root
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/mapper/VolGroup-lv_root is mounted on /; on-line resizing required
old desc_blocks = 3, new_desc_blocks = 4
Performing an on-line resize of /dev/mapper/VolGroup-lv_root to 12722176 (4k) blocks.
The filesystem on /dev/mapper/VolGroup-lv_root is now 12722176 blocks long.
```
扩容完毕！
```shell
[root@localhost ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       48G  8.4G   37G  19% /
tmpfs                 246M     0  246M   0% /dev/shm
/dev/sda1             485M   32M  428M   7% /boot
```


















