---
title: 'APC: undefined symbol: pcre_exec in Unknown on line 0'
tags:
  - APC
  - PECL
  - PHP
  - Yum
categories:
  - '*Nix'
date: 2013-05-12 22:46:45
---

APC是一个类似 eaccelerator 的免费PHP加速器，但你安装通过 yum 安装之后有可能会看到这样的错误：

Problem: /usr/lib/php/modules/apc.so: undefined symbol: pcre_exec in Unknown on line 0

以下是解决方法：
1. 先复制 /etc/php.d/apc.ini 文件到临时目录 /tmp
```bash
cp /etc/php.d/apc.ini /tmp
```
2. 移除原来的安装包
```bash
yum remove php-pecl-apc
```
3. 用 pecl 来安装 apc
```bash
pecl install apc
```
4. 再把 apc.ini 复制回去
```bash
mv /tmp/apc.ini /etc/php.d/apc.ini
```