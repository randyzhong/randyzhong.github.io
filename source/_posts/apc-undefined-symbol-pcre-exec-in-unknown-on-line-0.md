---
title: 'APC: undefined symbol: pcre_exec in Unknown on line 0'
tags:
  - APC
  - PECL
  - PHP
  - Yum
id: 189
categories:
  - '*Nix'
date: 2013-05-12 22:46:45
---

APC是一个类似 eaccelerator 的免费PHP加速器，但你安装通过 yum 安装之后有可能会看到这样的错误：

Problem: /usr/lib/php/modules/apc.so: undefined symbol: pcre_exec in Unknown on line 0

以下是解决方法：

1\. 先复制 /etc/php.d/apc.ini 文件到临时目录 /tmp
<pre class="toolbar:2 toolbar-overlay:false toolbar-hide:false toolbar-delay:false show-title:false show-lang:2 striped:false marking:false ranges:false nums:false nums-toggle:false wrap-toggle:false plain:false show-plain:3 plain-toggle:false copy:false popup:false lang:sh decode:true">cp /etc/php.d/apc.ini /tmp</pre>
2\. 移除原来的安装包
<pre class="toolbar:2 toolbar-overlay:false toolbar-hide:false toolbar-delay:false show-title:false show-lang:2 striped:false marking:false ranges:false nums:false nums-toggle:false wrap-toggle:false plain:false show-plain:3 plain-toggle:false copy:false popup:false lang:ps decode:true">yum remove php-pecl-apc</pre>
3\. 用 pecl 来安装 apc
<pre class="toolbar:2 toolbar-overlay:false toolbar-hide:false toolbar-delay:false show-title:false show-lang:2 striped:false marking:false ranges:false nums:false nums-toggle:false wrap-toggle:false plain:false show-plain:3 plain-toggle:false copy:false popup:false lang:ps decode:true">pecl install apc</pre>
4\. 再把 apc.ini 复制回去
<pre class="toolbar:2 toolbar-overlay:false toolbar-hide:false toolbar-delay:false show-title:false show-lang:2 striped:false marking:false ranges:false nums:false nums-toggle:false wrap-toggle:false plain:false plain-toggle:false copy:false popup:false lang:ps decode:true crayon-selected">mv /tmp/apc.ini /etc/php.d/apc.ini</pre>