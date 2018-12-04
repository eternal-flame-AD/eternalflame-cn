---
title: 群晖NAS cloud sync的数据加密功能~
author: Eternal-flame-AD
layout: post
identifier: bd19e20bc657ad130e7ce253ffbc1f23
date: 2018-04-09T13:29:37+00:00
categories:
  - 技术

---
今天配置了群晖上的Cloud sync，可以把我的NAS上的数据自动备份到百度云，但云端备份使用了RSA加密，可以说很用心~（早知道这么方便本地就直接JBOD了，现在是RAID 1，不过云端多一份备份也是以防万一，譬如整个NAS丢了之类的。。）唯一不太爽的是文件名没有加密，不过也不是很在意这个了

平时可以使用NAS上存储的密钥进行同步，如果出现所有本地备份丢失的情况，可以使用群晖官方提供的解密工具加载自己的私钥解密百度云上的备份~

随李彦宏用数据做什么事情吧= =

 ![](/images/TIM截图20180409212532.jpg)