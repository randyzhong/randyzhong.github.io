---
title: >-
  Virtual Server Undo Disks - I am never going to use you again (Can't Merge
  Disks - Parent Disk is a part of a Saved State)
id: 79
categories:
  - 虚拟化
date: 2009-05-07 17:10:48
tags:
---

在把 Virtual Server 2005 的虚拟机迁过去 Windows Server 2008 Hyper-V 以后,原来有些虚拟机开了 undo disk 的,发现在 Hyper-V 中没有合并选项,于是又重新安装了 Virtual Server 2005 SP1, 但是合并的时候失败,出错提示如下:

**"Virtual Server was unable to commit the changes made during the current session of "_[VPC NAME]_". The cause of this problem is that the parent virtual hard disk is a part of a saved state. Your changes will be kept for the next time you start this virtual machine."**

<!--more-->

解决方法如下:

If you search google for this, you come up with almost nothing.  But there's a [critical post right here](http://groups.google.com/group/microsoft.public.virtualserver/browse_thread/thread/329d9fed504435e1/5aeb3f7c0b2640f2?lnk=st&amp;q=Virtual+Server+was+unable+to+commit+the+changes+made+during+the+current+session+of+.+The+cause+of+this+problem+is+that+the+parent+virtual+hard+disk+is+a+part+of+a+saved+state.+Your+changes+will+be+kept+for+the+next+time+you+start+this+virtual+machine.&amp;rnum=1&amp;hl=en&amp;fwc=2) about it.  It spells out exactly what you need to do, but foolishly I ignored it and kept looking for a better solution, since I didn't understand the concept of "inspecting" a virtual disk.

The basic steps are:

1.  Rename the undo disks to *.vhd.  This turns them into a virtual drive in the eyes of Virtual Server.
2.  Click on the Inspect option in the Virtual Disk section of Virtual Server.  There should be an option to then merge the disk.
3.  Select a new disk to merge into.  If you try merging into the original parent, Virtual Server will just give you the above error again.
4.  Take your newly merged virtual disk and replace it in the virtual machine (i.e. replace the parent that was couldn't be merged to before).