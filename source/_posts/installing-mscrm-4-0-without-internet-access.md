---
title: Installing MSCRM 4.0 without Internet Access
tags:
  - MSCRM
categories:
  - Microsoft
  - MSCRM
date: 2008-01-08 09:59:15
---

If you are installing CRM 4.0 on a machine without Internet access (eg. a VPC running only on a Loopback Adapter) you might run into some problems when CRM tries to download pre-requisite packages. Our DVD version (not out yet - it's being 'manufactured') ships with these prerequisites but the downloads do not.
The simplest way is to install each of the Pre-Req's manually. The best place to search for these is [download.microsoft.com](http://download.microsoft.com/). I'll refrain from posting links as these redistributable packages are always being updated.
The Visual C++ Redistributable Package presents us with a slightly different challenge. The package is unable to detect it's installed state so CRM always installs it. So we need to create a Redist folder, subfolders for i386/amd64, a VCRedist folder and then place the VC++ install files there. Your full folder structure should look like this:

*   \Redist\i386\VCRedist\vcredist_x86.exe
*   \Redist\i386\VCRedist\vcredist_x64.exe
*   \Redist\amd64\VCRedist\vcredist_x64.exe
*   \Server\i386\SetupServer.exe
*   \Server\amd64\SetupServer.exe
*   \Client\SetupClient.exe
*   \DMWizard\SetupDMClient.exe
*   \Exchange\i386\setupexchange.exe
*   \Exchange\amd64\setupexchange.exe
I added this setup.exes to the list to show the folder structure for the Server, Client, DM and Router (the Exchange folder). Note that the Redist folder is a peer to the Server and Client.

***
中文版本 ：
自己上 MS 的网站上去下载 VC++ Runtime
新建一个目录，比如 MSCRM4, 在该目录下再建立如下目录
<span style="color: #ff0000">MSCRM4\Redist\i386\VCRedist\vcredist_x86.exe</span>
<span style="color: #ff0000">MSCRM4\Redist\i386\VCRedist\vcredist_x64.exe</span>
<span style="color: #ff0000">MSCRM4\Redist\amd64\VCRedist\vcredist_x64.exe</span>
<span style="color: #0000ff">MSCRM4\Server\i386\SetupServer.exe</span>
<span style="color: #0000ff">MSCRM4\Server\amd64\SetupServer.exe</span>
MSCRM4\Client\SetupClient.exe
MSCRM4\DMWizard\SetupDMClient.exe
MSCRM4\Exchange\i386\setupexchange.exe
MSCRM4\Exchange\amd64\setupexchange.exe

> 红色的就是表示 VC++ runtime 文件所要放置的位置
> 蓝色的表示 MS CRM4.0 Server 的安装程序所在位置

因为CRM4的安装程序需要上网去更新一些组件，如果你的服务器不能上网的话，就请参照此目录结构建立文件夹和放置正确的文件，这样就能跳过网上下载安装。