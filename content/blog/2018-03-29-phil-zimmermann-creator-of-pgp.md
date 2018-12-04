---
title: 【PGP简介】（1） Phil Zimmermann – Creator of PGP | Phil Zimmermann——PGP之父
author: Eternal-flame-AD
layout: post
date: 2018-03-29T14:58:04+00:00
categories:
  - 学习
  - 技术
  - 日常

---
打算开天坑啦~这次打算开一个PGP简介的系列，会从简介和历史讲起，之后会介绍一些常用PGP工具的使用，其中具体技术实现上只打算触及原理介绍和使用方法，不打算涉及过于复杂的细节上的实现，<s>适合用来拉人入坑</s>~

[<img class="alignnone aligncenter" src="https://philzimmermann.com/images/photos/PRZ_closeup.jpg" />][1]

Phil Zimmermann，Pretty Good Privacy(PGP)软件的开创者，个人网站：（[中文版][2] [英文版][3]）。他在1991年开发了PGP软件的第一个版本，据说这样一个名字是受到一家名为“Ralph&#8217;s Pretty Good Grocery”的果蔬店的启发而来，当时Phil Zimmermann作为一名反核人士，打算利用自己开发的PGP来保证同样反核的人们能够在互联网上安全的交换信息。因为这个目的，所以Phil Zimmermann没有打算通过售卖PGP的授权获得任何利润，而是直接将PGP及其源代码发布在了各大BBS上，让互联网上的每个人都有权使用、检查和修改其中的代码。

Phil Zimmermann的PGP很快便引起了极大的反响，也有很多其它国家的人们开始使用PGP软件，然而这一事件却违反了当时美国出口法令：在当时，美国规定超过40位的加密算法都是禁止出口的，而PGP使用的加密算法最低为128位，因此Phil Zimmermann当时收到了长达三年的刑事调查，在调查期间，Phil Zimmermann使用了非常有趣的方法规避这样一个问题：他将所有PGP的源码出版成了书籍，这样每个人只要购买这本书，将书上的代码输入到计算机里，就可以编译出PGP软件，而Phil传播PGP的代码也因为这本书的出版，变成了收到言论自由保护的行为。三年之后，当局放弃了对Phil的指控。

<p style="text-align: center;">
  <img class="aligncenter" src="https://philzimmermann.com/images/photos/PRZ_b-b-b-bad.jpg" />当局放弃对Phil的指控后，Phil拍的照片= =
</p>

2010年，Symantec购买了PGP的授权，将PGP应用于自己的加密工具([Gateway Email Encryption][4])中，Symantec的PGP工具的源代码依然开放（[链接][5]），同时另一个开源分支（和Symantec无关）OpenPGP也在积极收到维护，小透明写的这组文章主要是针对OpenPGP进行介绍~

 [1]: https://philzimmermann.com/images/photos/PRZ_closeup.jpg
 [2]: https://philzimmermann.com/ZH/background/index.html
 [3]: https://philzimmermann.com/EN/background/index.html
 [4]: https://www.symantec.com/products/gateway-email-encryption
 [5]: https://www.symantec.com/connect/downloads/symantec-pgp-desktop-peer-review-source-code