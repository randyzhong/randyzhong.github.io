---
title: 解决VirtualBox在Windows7中无法安装USB驱动等问题
tags:
  - VirtualBox
  - 安装
  - 虚拟化
  - 证书
id: 156
categories:
  - 虚拟化
date: 2013-03-19 09:32:50
---

操作系统：Windows 7 Professional with SP1 32-bit

VirtualBox：VirtualBox-4.2.10-84105-Win.exe

安装的时候选择默认设置，VirtualBox USB Support 和 VirtualBox Networking 都选了，

[![VirtualBox-Driver-1](http://winotes.net/wp-content/uploads/VirtualBox-Driver-1-300x233.jpg)](http://winotes.net/wp-content/uploads/VirtualBox-Driver-1.jpg)

前面一切正常，到安装虚拟 USB 和网卡驱动的时候，跳出来这个窗口提示驱动程序证书未受信任，需要确认是否继续安装，可是到想去点 Install 安装的时候，却发现变成了 Not Responding 无法响应状态，安装过程无法继续，只好关掉驱动安装窗口退出。试过更换 VirtualBox 的版本，症状依旧。

[![VirtualBox-Driver-2](http://winotes.net/wp-content/uploads/VirtualBox-Driver-2-300x138.jpg)](http://winotes.net/wp-content/uploads/VirtualBox-Driver-2.jpg)

&nbsp;

<!--more-->

经过一番摸索，找到了解决之道：

1.  因为是卡在不受信任的驱动安装步骤，所以要想办法把这个驱动变成受信任的；
2.  决定是否受信实际上是看改驱动的证书是否在机器的受信颁发机构里面 (Trusted Publisher)；
3.  重新运行VirtualBox-4.2.10-84105-Win.exe，直到跳出驱动安装提示，不理，打开我的电脑，找到 VirtualBox 的安装目录下的 drivers 文件夹，双击打开其中 USB\device\VBoxUSB.cat 文件，在常规 (General) 标签卡中选择查看签名(View Signature) -&gt; 查看证书 (View Certificate);
4.  在该证书的详细(Details)标签卡中选择复制到文件 (Copy to File)，导出成 .DER 文件；
5.  开始，运行 mmc，选择 Certificates -&gt; Computer account，在 Trusted Publisher 中导入刚刚的 .DER 证书文件，确认 Oracle 证书已经导入；
[![VirtualBox-Driver-3](http://winotes.net/wp-content/uploads/VirtualBox-Driver-3-300x73.jpg)](http://winotes.net/wp-content/uploads/VirtualBox-Driver-3.jpg)

&nbsp;

退出安装程序，再重新跑一遍即可，问题解决。