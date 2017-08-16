---
title: 修改idx/sub字幕的字幕显示顺序
tags:
  - Vobsub
categories:
  - 数码影音
date: 2005-04-02 22:50:50
---

如何修改idx/sub字幕的字幕显示顺序,使中文字幕排在最前面？

编辑.idx文件

找到
\# Language index in use

langidx: 0

把langidx: 0 的数字改成中文字幕的index no
比如在这个文件里面
\# Chinese

id: zh, index: 2

那就改成langidx: 2，保存。

后来发现这样还不行，摸索一番，发现VSFilter/Vobsub在注册表里面还有相关设置，

注册表里面的设置是优先的，位置在

`HKEY_CURRENT_USER\Software\Gabest\VSFilter\PreferedLanguages`

里面有一个名为Langx的字串值，修改Lang0 的键值为 "Chinese" 才能使默认显示为中文

如果是Vobsub2.23及其以前版本在

`HKEY_CURRENT_USER\Software\Gabest\Vobsub`

VSFilter2.3x则在

`HKEY_CURRENT_USER\Software\Gabest\VSFilter`
