---
title: Python练手小程序-中科大网络课堂视频下载
tags:
  - Python
categories:
  - Python
date: 2017-08-28 11:46:31
keywords: Python, 练习，中科大，知乎
---

#### 所谓人生苦短，我用 Python
作为一个编程小白，我也开始了学习 Python 的过程。之前在知乎上爬贴，看到个有意思的回答，[大家都用 Python 来做什么啊？ - Tsing 的回答](https://www.zhihu.com/question/20799742/answer/99491808#)，答主用了一小段代码拿到了学校研究生网络课堂在线视频的真实地址，答案里面的代码段关键部分被处理过，我看到评论里面好多人在求，我就想自己试试看能不能摸索出来，折腾了几天，终于从白板写出了第一个 Python 小程序。

<!--more-->

以下是[源代码](https://github.com/randyzhong/my-python-learning/blob/master/ustc-wlkt-downloader/ustc-wlkt-downloader.py):
``` python
# -*- coding: utf-8 -*-
# http://wlkt.ustc.edu.cn/ (USTC CERNET IP Only)
# Public course only
# If online course is set to private or restricted for Xiaonei only, you have to run this downloader from USTC CERNET IP, or you'll not get the real video source URL.
# Reference: https://www.zhihu.com/question/20799742/answer/99491808

import requests
import urllib
from bs4 import BeautifulSoup

s = requests.Session() 
link = "http://wlkt.ustc.edu.cn/video/detail_3752_0.htm" # This is a public video
req = s.get(link)
html = req.content
soup = BeautifulSoup(html, "html.parser")
videourl1 = soup.find(id="videourl1")['value']
htopicid = soup.find(id="htopicid")['value']
header = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.101 Safari/537.36',
           'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8',
           'X-Requested-With': 'XMLHttpRequest',
           'Host': 'wlkt.ustc.edu.cn',
           'Origin': 'http://wlkt.ustc.edu.cn',
           'Referer': 'http://wlkt.ustc.edu.cn/video/detail_3752_0.htm'}
payload = urllib.urlencode({'videourl1': videourl1,'hid_topicid': htopicid})
url = requests.post("http://wlkt.ustc.edu.cn/ajaxprocess.php?menu=getvideourl",data=payload, headers=header)
print ("The real video URL is： http://wlkt.ustc.edu.cn/mp4.php?file=" + url.content[14:])
```

这里面最大的坑其实还是校内外IP限制的问题，在中科大的网络课堂视频里面，只有一小部分可以在外网访问，大部分都限制只允许校内教育网IP访问, 如果你在校外，因为后台服务器限制，返回来的只有 xiaowai 几个字，除非你有中科大校内代理，否则这个没法破。我找的这个例子是对公网开放的，权当拿来自己练手。
