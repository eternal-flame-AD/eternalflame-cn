---
title: 使用Python抓取rejet作品官博
author: Eternal-flame-AD
layout: post
identifier: 47798cd3f7d6966cc95cde49c47d1850
date: 2018-10-05T08:53:24+00:00
categories:
  - Python
  - 技术

---
今天又被安利了R社的另一个IP——「幽幻ロマンチカ」，还是和剑君一样养眼的画风～～ 不过小透明不会日语再也啃不动了QWQ

于是打算搜刮一波图来舔，打开[官博][1]之后发现竟然和剑君的是一个套路（也许R社太懒两个作品的官博用的就是同一套CMS换了个主题hhhh）：

「幽幻ロマンチカ」的URL前缀是：

http://rejetweb.jp/yuroma/blog/

剑君的是：

http://rejetweb.jp/kengakimi/blog/

所以就翻出了几个月前自己用来下剑君官博的图的脚本，把前缀的配置提出来就可以支持这个作品了～

代码传到了Github Gist上，完全使用正则表达式来匹配，因为是一次性的活所以小透明就怎么方便怎么来了～

{{< gist eternal-flame-AD 0fde3f3598e58039d1fecc0f4ed87086 >}}

&nbsp;

 [1]: http://rejetweb.jp/yuroma/blog/