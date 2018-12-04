---
title: 通过HTTPS中间人绕过SNI-based censorship
author: Eternal-flame-AD
layout: post
identifier: 3706f6dcf9b7266132ec2a7956d618bf
date: 2018-09-19T02:56:41+00:00
categories:
  - Golang
  - 技术
tags:
  - GFW
  - Golang
  - HTTP代理
  - SNI
  - TLS

---
最近墙似乎又升级了，增加了基于SNI的检测，导致P站不翻墙完全不能访问，症状是Firefox建立的所有TCP链接均在TLS Client Hello发送后被Reset，之前写的[mkhosts][1]工具也不好使了：因为直接建立TCP是能通的，但是真正访问目标网站的时候因为要发送明文的SNI，因此被拦截。

本来这么一想解决方法就是简单粗暴——别发SNI不就好了，但后来发现并不简单，没有哪个浏览器允许用户使用任何方式直接调整TLS握手的时候的参数的。

看来这样，完全在浏览器里解决问题是不太现实了，我本来想着是不是可以把握手的时候浏览器发送的SNI修改一下，但每次浏览器都无法建立TLS链接，使用wireshark抓包发现在Handshake Finished阶段对端发出Alert Decryption Error，无法完成握手。查询[rfc][2]发现自己Too Young Too Simple，Handshake Finished阶段服务器端要签名整个握手阶段报文的hash来确定握手没有被修改，所以这条路又凉了。

最后只好退一步编写一个中间人来截获浏览器的TLS连接，再向目标服务器建立另一个修改过的链接，然后把两边的TLS通道相互copy一下。

上一个演示代码（Github Gist 国内现在好像也凉了 可能是因为上面有一些你懂得证据吧），利用[elazarl/goproxy][3] 来建立本地HTTP代理，截获其中的CONNECT请求（浏览器会通过HTTP CONNECT动词尝试经过代理服务器建立和对端服务器的连接，我们在这里不要直接链接对方服务器而是自己签发对方域名的证书，然后做一次修改的握手，复制两边TLS通道内传递的消息）修改的握手部分主要要注意ServerName不要传递，跳过默认证书验证（因为那是基于ServerName的，你没有给ServerName是过不了的），使用内置的crypto/x509写一个自定义的验证函数（再次吐槽 node里面只要`checkServerIdentity: (host, cert)=>tls.checkServerIdentity(realhost, cert)`一句话就好了，go写了这么长hhhh）：

{{< gist eternal-flame-AD b848f2b917a430b955645ae6cd883f4a>}}

安装[ca证书][4]后，编译运行代码，浏览器设置http代理http://127.0.0.1:8080/ （HTTPS也要走这个代理）即可

&nbsp;

有个朋友当时跟小透明说要不要把这个完善一下做成项目，小透明后来想了一下觉得还是不了，一是因为SNI审查不会是常态，应该会过去的，二是整个思路其实很清晰很简单也不复杂，程序员应该很容易就能写出来而且更加符合自己的需求，而对于普通人这些方法想比××上网来的太不稳定太复杂了，也没有太大价值。就当作一次尝试啦～

**更新：已经写成项目了，在[这里](https://www.github.com/eternal-flame-AD/go-pixiv)**

 [1]: https://github.com/eternal-flame-AD/mkhosts
 [2]: https://tools.ietf.org/html/rfc5246#section-7.4.9
 [3]: https://github.com/elazarl/goproxy
 [4]: https://github.com/elazarl/goproxy/raw/master/ca.pem