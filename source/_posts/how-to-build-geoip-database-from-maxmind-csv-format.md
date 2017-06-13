---
title: 如何从Maxmind的CSV数据源生成 GeoIP 的二进制数据库
tags:
  - GeoIP
  - MaxMind
id: 259
categories:
  - '*Nix'
  - 实用工具
date: 2015-06-25 10:40:09
---

所谓GeoIP，就是通过来访者的IP， 定位他的经纬度，国家/地区，省市，甚至街道等位置信息。

网上能下载到的 geoip 可能都比较旧了，下面我们来自己生成最新的GeoIP 的二进制数据库 (geoipdb.bin, geoipdb.bin)

#### 1\. 环境准备

我们需要一台 Linux, 这里我们使用 Ubuntu

然后再安装基本的编译工具
<pre class="">apt-get install build-essential zip</pre>

#### 2\. 下载最新的 GeoIP 数据库并将其解压缩

<pre class="">wget http://www.maxmind.com/download/geoip/database/GeoIPCountryCSV.zip
unzip GeoIPCountryCSV.zip</pre>

#### 3\. 下载 gcsv2bin 的转换工具

<pre class="">http://people.netfilter.org/acidfu/geoip/tools/gcsv2bin.tar.gz
tar xzvf gcsv2bin.tar.gz</pre>

#### 4\. 修改源码，编译工具

<pre class="">cd gcsvbin
nano gcsvbin.c #找到 COUNTRYCOUNT，将其改到 275或者更大
make
</pre>
[caption id="attachment_261" align="alignnone" width="300"][![Change_Countrycount](http://winotes.net/wp-content/uploads/Change_Countrycount-300x149.jpg)](http://winotes.net/wp-content/uploads/Change_Countrycount.jpg) Change Countrycount[/caption]

#### 5\. 生成 geoipdb.bin 和geoipdb.bin

<pre class="">./gcsv2bin ../GeoIPCountryWhois.csv</pre>