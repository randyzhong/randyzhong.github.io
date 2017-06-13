---
title: 使用 URL Rewrite 重定向 Http 请求至 Https
tags:
  - HTTPS
  - IIS
  - Rewrite
id: 23
categories:
  - IIS
  - 操作系统
date: 2010-10-21 09:48:37
---

&nbsp;

公司部署了个新的 IIS 站点，需要强制在 Https 下面访问，但是又不希望关掉80端口的 Http 请求，这样对用户体验来讲不好，最好是能强制所有的 Http 请求都会被重定向到 Https 上面去，显然， IIS 自带的 "HTTP Redirect" 已经不能满足这个需求，我们这里使用 IIS 7.0 下面的重定向扩展 URL Rewrite Module 来做。

1. [http://www.iis.net/download/URLRewrite](http://www.iis.net/download/URLRewrite) 到这里下载，有分 32 和 64 位版本。

<!--more-->

2\. 安装完后，可以在 IIS 站点管理的根目录下面看到多了个 URL Rewrite。

[![url-rewrite-1](http://winotes.net/wp-content/uploads/url-rewrite-1-298x300.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-1.jpg)

&nbsp;

3\. 双击打开，你会看到一个规则配置的页面。

[![url-rewrite-2](http://winotes.net/wp-content/uploads/url-rewrite-2-300x122.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-2.jpg)

&nbsp;

4\. 点击 "Add Rules" 添加规则，选择默认的"Blank Rule" 作为模板。

[![url-rewrite-3](http://winotes.net/wp-content/uploads/url-rewrite-3-300x200.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-3.jpg)

&nbsp;

5\. 在规则名字这一栏输入 "Redirect to HTTPS"。

[![url-rewrite-4](http://winotes.net/wp-content/uploads/url-rewrite-4-300x148.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-4.jpg)

&nbsp;

6\. 展开 "Match URL" 这个小三角，按如下设定：
**Requested URL:** Matches the Pattern
**Using:** Regular Expressions
**Pattern:** (.*)

[![url-rewrite-5](http://winotes.net/wp-content/uploads/url-rewrite-5-300x147.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-5.jpg)

&nbsp;

7.在"Conditions"这里，点击 "Add..."。

[![url-rewrite-6](http://winotes.net/wp-content/uploads/url-rewrite-6-300x207.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-6.jpg)

&nbsp;

8\. 按以下设定输入：
**Condition Input**: {HTTPS}
**Check if input string**: Matches the Pattern
**Pattern**: ^OFF$

[![url-rewrite-7](http://winotes.net/wp-content/uploads/url-rewrite-7.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-7.jpg)

&nbsp;

9\. 完成后入下图所示。

[![url-rewrite-8](http://winotes.net/wp-content/uploads/url-rewrite-8-300x145.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-8.jpg)

&nbsp;

10\. 最后一步就是重定向的设置了。

**Action Type**: Redirect
**Redirect URL**: [https://{HTTP_HOST}/{R:1](https://%7Bhttp_host%7D/%7BR:1)}
**Redirect Type**: See Other (303)

[![url-rewrite-9](http://winotes.net/wp-content/uploads/url-rewrite-9-300x247.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-9.jpg)

11\. 点击右边的 "Apply" 应用，设置完成

&nbsp;

[![url-rewrite-10](http://winotes.net/wp-content/uploads/url-rewrite-10.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-10.jpg) [![url-rewrite-11](http://winotes.net/wp-content/uploads/url-rewrite-11-300x189.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-11.jpg)

&nbsp;

补充： 因为本地的 IIS 还有个 Web Service 需要调用 http://localhost/directory/service.asmx，如上设置后，会被重定向去 http://localhost/directory/service.asmx，而由于SSL证书匹配的关系，这个Web Service调用会出错，所以我后来又再加上了个例外条件，使得本地的 localhost 调用请求不再转向 HTTPS。

12\. 在"Conditions"这里，点击 "Add..."。

按以下设定输入，然后点击 Move Up 使其成为第一个条件
**Condition Input**: {HTTPS_HOST}
**Check if input string**: Does Not Matches the Pattern
**Pattern**: ^(localhost)

[![url-rewrite-12](http://winotes.net/wp-content/uploads/url-rewrite-12-300x198.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-12.jpg) [![url-rewrite-13](http://winotes.net/wp-content/uploads/url-rewrite-13-300x213.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-13.jpg)

&nbsp;

13\. 最后设定完成如图。

14\. 在 web.config 里面你也可以看到最终的设置结果

[![url-rewrite-14](http://winotes.net/wp-content/uploads/url-rewrite-14-300x163.jpg)](http://winotes.net/wp-content/uploads/url-rewrite-14.jpg)

&nbsp;

&nbsp;