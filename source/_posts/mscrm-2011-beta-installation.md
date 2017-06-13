---
title: 微软 CRM 2011 Beta 快速安装图解
tags:
  - Beta
  - MSCRM
id: 85
categories:
  - MSCRM
date: 2011-09-19 15:19:36
---

微软在9月17日在网站上放出了 CRM 2011 （也就是CRM5.0）的 beta 版本，我就先来个简单的快读安装图解，一睹为快，下个月会写点新版 CRM 的使用感受。

下载：

[Microsoft Dynamics CRM 2011 Beta](http://www.microsoft.com/downloads/details.aspx?FamilyID=0c7dcc45-9d41-4e2e-8126-895517b4274c&amp;displayLang=zh-cn)

[Microsoft Dynamics CRM 2011 Software Development Kit (SDK)](http://www.microsoft.com/downloads/info.aspx?na=40&amp;p=3&amp;SrcDisplayLang=zh-cn&amp;SrcCategoryId=&amp;SrcFamilyId=0c7dcc45-9d41-4e2e-8126-895517b4274c&amp;u=http%3a%2f%2fgo.microsoft.com%2ffwlink%2f%3fLinkID%3d200082)

[Microsoft Dynamics CRM 2011 Beta Implementation Guide](http://www.microsoft.com/downloads/info.aspx?na=40&amp;p=2&amp;SrcDisplayLang=zh-cn&amp;SrcCategoryId=&amp;SrcFamilyId=0c7dcc45-9d41-4e2e-8126-895517b4274c&amp;u=http%3a%2f%2fgo.microsoft.com%2ffwlink%2f%3fLinkID%3d200050)

<!--more-->

界面一览

[![CRM2011_20](http://winotes.net/wp-content/uploads/CRM2011_20-300x180.jpg)](http://winotes.net/wp-content/uploads/CRM2011_20.jpg)
<div id="more">**1\. 自动更新安装程序；**</div>
<div>[![CRM2011_0](http://winotes.net/wp-content/uploads/CRM2011_0-300x223.jpg)](http://winotes.net/wp-content/uploads/CRM2011_0.jpg)</div>
<div>**2\. 输入安装 Key，在 ReadMe 文件里面有， beta 版本试用有效期是 270 天；**</div>
<div>[![CRM2011_1](http://winotes.net/wp-content/uploads/CRM2011_1-300x219.jpg)](http://winotes.net/wp-content/uploads/CRM2011_1.jpg)</div>
<div>**3\. 新的组件需求；**</div>
<div>[![CRM2011_2](http://winotes.net/wp-content/uploads/CRM2011_2-300x219.jpg)](http://winotes.net/wp-content/uploads/CRM2011_2.jpg)</div>
<div></div>
<div>[![CRM2011_3](http://winotes.net/wp-content/uploads/CRM2011_3-300x219.jpg)](http://winotes.net/wp-content/uploads/CRM2011_3.jpg) [![CRM2011_4](http://winotes.net/wp-content/uploads/CRM2011_4-300x219.jpg)](http://winotes.net/wp-content/uploads/CRM2011_4.jpg)</div>
<div> **4\. 选择目录，继续；**</div>
<div>[![CRM2011_5](http://winotes.net/wp-content/uploads/CRM2011_5-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_5.jpg)</div>
<div></div>
<div>**5\. 新的角色：前端、后端、部署管理；**</div>
<div>[![CRM2011_6](http://winotes.net/wp-content/uploads/CRM2011_6-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_6.jpg)</div>
<div> **6\. 和 4.0 一样的选择 AD 用户和组创建位置；**</div>
<div>[![CRM2011_7](http://winotes.net/wp-content/uploads/CRM2011_7-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_7.jpg) [![CRM2011_8](http://winotes.net/wp-content/uploads/CRM2011_8-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_8.jpg) [![CRM2011_9](http://winotes.net/wp-content/uploads/CRM2011_9-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_9.jpg) [![CRM2011_10](http://winotes.net/wp-content/uploads/CRM2011_10-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_10.jpg) [![CRM2011_11](http://winotes.net/wp-content/uploads/CRM2011_11-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_11.jpg)</div>
<div>**7\. 创建一个 CRM 组织；**</div>
<div>[![CRM2011_12](http://winotes.net/wp-content/uploads/CRM2011_12-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_12.jpg)</div>
<div></div>
<div></div>
<div> **8\. 选择报表服务器；**</div>
<div></div>
<div>[![CRM2011_13](http://winotes.net/wp-content/uploads/CRM2011_13-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_13.jpg) [![CRM2011_14](http://winotes.net/wp-content/uploads/CRM2011_14-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_14.jpg) [![CRM2011_15](http://winotes.net/wp-content/uploads/CRM2011_15-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_15.jpg)</div>
<div> **9\. 安装前需求全部满足，开始安装；**</div>
<div>[![CRM2011_16](http://winotes.net/wp-content/uploads/CRM2011_16-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_16.jpg) [![CRM2011_17](http://winotes.net/wp-content/uploads/CRM2011_17-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_17.jpg) [![CRM2011_18](http://winotes.net/wp-content/uploads/CRM2011_18-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_18.jpg)</div>
<div></div>
<div> **10\. 如果分开部署，这里要安装 Reporting Extensions for SSRS Setup；**</div>
<div>[![CRM2011_19](http://winotes.net/wp-content/uploads/CRM2011_19-300x259.jpg)](http://winotes.net/wp-content/uploads/CRM2011_19.jpg)</div>