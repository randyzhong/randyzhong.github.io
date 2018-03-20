---
title: 腾讯公益404页面现已支持 https
tags:
  - Web, 404, https, 腾讯
categories:
  - 网站相关
date: 2018-03-16 16:48:35
keywords: Web, 404, https, 腾讯
description: 公益404页面是由腾讯公司员工志愿者自主发起的互联网公益活动。但之前都不支持 https，这两天因为打算将本站的国内镜像站点挂到 Coding.net，然后启用全站 https，这才发现不知道啥时候腾讯公益404页面现已支持 https。
---

### 公益 404 页面介绍
[公益404页面](http://www.qq.com/404/)是由腾讯公司员工志愿者自主发起的互联网公益活动。

网站只需要在自己的404页面中嵌入一段简单的代码，就能通过互联网来迅速传播失踪儿童信息，从而提高找回失踪儿童的概率。失踪儿童信息来自宝贝回家寻子网。

之前不支持 https，这两天因为打算将本站的国内镜像站点挂到 Coding.net，然后启用全站 https，这才发现不知道啥时候腾讯公益404页面现已支持 https。

### 公益 404 页面接入方法
> 注意事项
如果一个 404 页面的内容小于 512B，IE 会认为该 404 页面不够友好，在 IE 下将不会成功返回该 404 错误页面

复制以下 js 代码，嵌入到您的 404 页面，可以自适应移动设备。

旧版本的嵌入代码：
```html
<script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8"></script>
```

新版本的嵌入代码：
```html
<script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" charset="utf-8"></script>
```

### 结语
可以看到，新的 js 引用源并没有写死是 http 还是 https 协议，这样写的好处就是它会根据你自己网站的情况来自动匹配。
本站已启用全站 https，所以最终的引用地址就会是
[**https**://qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js](https://qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js)
不管怎样，还是要给鹅厂一个赞。
