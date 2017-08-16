---
title: 使用 URL Rewrite 重定向 Http 请求至 Https
tags:
  - IIS
  - Rewrite
categories:
  - Microsoft
date: 2010-10-21 09:48:37
---

公司部署了个新的 IIS 站点，需要强制在 Https 下面访问，但是又不希望关掉80端口的 Http 请求，这样对用户体验来讲不好，最好是能强制所有的 Http 请求都会被重定向到 Https 上面去，显然， IIS 自带的 "HTTP Redirect" 已经不能满足这个需求，我们这里使用 IIS 7.0 下面的重定向扩展 URL Rewrite Module 来做。

1. [http://www.iis.net/download/URLRewrite](http://www.iis.net/download/URLRewrite) 到这里下载，有分 32 和 64 位版本。
2. 安装完后，可以在 IIS 站点管理的根目录下面看到多了个 URL Rewrite。
{% asset_img url-rewrite-1.jpg url-rewrite-1 %}
3. 双击打开，你会看到一个规则配置的页面。
{% asset_img url-rewrite-2.jpg url-rewrite-2 %}
4. 点击 "Add Rules" 添加规则，选择默认的"Blank Rule" 作为模板。
{% asset_img url-rewrite-3.jpg url-rewrite-3 %}
5. 在规则名字这一栏输入 "Redirect to HTTPS"。
{% asset_img url-rewrite-4.jpg url-rewrite-4 %}
6. 展开 "Match URL" 这个小三角，按如下设定：
**Requested URL:** Matches the Pattern
**Using:** Regular Expressions
**Pattern:** (.\*)
{% asset_img url-rewrite-5.jpg url-rewrite-5 %}
7. 在 "Conditions" 这里，点击 "Add..."。
{% asset_img url-rewrite-6.jpg url-rewrite-6 %}
8. 按以下设定输入：
**Condition Input**: {HTTPS}
**Check if input string**: Matches the Pattern
**Pattern**: ^OFF$
{% asset_img url-rewrite-7.jpg url-rewrite-7 %}
9. 完成后入下图所示。
{% asset_img url-rewrite-8.jpg url-rewrite-8 %}
10. 最后一步就是重定向的设置了。
 - **Action Type**: Redirect
 - **Redirect URL**: [https://{HTTP_HOST}/{R:1](https://%7Bhttp_host%7D/%7BR:1)}
 - **Redirect Type**: See Other (303)
{% asset_img url-rewrite-9.jpg url-rewrite-9 %}

11. 点击右边的 "Apply" 应用，设置完成。
{% asset_img url-rewrite-10.jpg url-rewrite-10 %}
{% asset_img url-rewrite-11.jpg url-rewrite-11 %}
> 补充： 因为本地的 IIS 还有个 Web Service 需要调用http://localhost/directory/service.asmx， 如上设置后，会被重定向去 http://localhost/directory/service.asmx， 而由于SSL证书匹配的关系，这个Web Service调用会出错，所以我后来又再加上了个例外条件，使得本地的 localhost 调用请求不再转向 HTTPS。
12. 在"Conditions"这里，点击 "Add..."。
13. 按以下设定输入，然后点击 Move Up 使其成为第一个条件
    - **Condition Input**: {HTTPS_HOST}
    - **Check if input string**: Does Not Matches the Pattern
    - **Pattern**: ^(localhost)
{% asset_img url-rewrite-12.jpg url-rewrite-12 %}
14. 最后设定完成如图。
{% asset_img url-rewrite-13.jpg url-rewrite-13 %}
15. 在 web.config 里面你也可以看到最终的设置结果
{% asset_img url-rewrite-14.jpg url-rewrite-14 %}