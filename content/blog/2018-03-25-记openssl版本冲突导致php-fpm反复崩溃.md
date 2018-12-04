---
title: 记openssl版本冲突导致PHP-FPM反复崩溃
author: Eternal-flame-AD
layout: post
identifier: e592a676d1f925eb0e08035fbc5abb7b
date: 2018-03-25T01:59:57+00:00
categories:
  - PHP
  - 技术
  - 日常

---
之前在部署服务器PHP-FPM时，发现web应用经常会出现502情况，具体有以下表现：

<!--more-->

  * Nextcloud检测不到网络连接，服务器基本信息面板第一次访问502，刷新后可以显示，提示&#8221;检查服务器配置时出错，请查看日志以获取更多信息&#8221;，但Nextcloud日志里并无任何消息
  * WordPress几乎所有管理员页面均出现第一次访问502，仪表板甚至一直502
  * Mediawiki绝大多数功能正常，上传文件时502

之后去查看了PHP-FPM的错误日志，找到了如下的提示：

{{< highlight INI >}}
[23-Mar-2018 21:38:50] NOTICE: [pool www] child 5508 started
  
[23-Mar-2018 21:38:50] WARNING: [pool www] child 5504 exited on signal 11 (SIGSEGV) after 106.266723 seconds from start
  
[23-Mar-2018 21:38:50] NOTICE: [pool www] child 5510 started
  
[23-Mar-2018 21:38:55] WARNING: [pool www] child 5370 exited on signal 11 (SIGSEGV) after 1011.828673 seconds from start
  
[23-Mar-2018 21:38:55] NOTICE: [pool www] child 5512 started
  
[23-Mar-2018 21:38:58] WARNING: [pool www] child 5512 exited on signal 11 (SIGSEGV) after 2.966123 seconds from start

{{< / highlight >}}

发现PHP-FPM的child出现了频繁崩溃的情况，正是因为child崩溃导致nginx出现502错误

因为崩溃没有指明原因，正好我之前编译PHP时手滑，配置curl时忘了开启https支持，被Nextcloud日志明确指出，所以一开始我一直在想也许是curl还有问题，但折腾了好久，后来PHP版本也降级了仍然没有解决问题。

后来一次偶然去查看syslog时终于找到了罪魁祸首：

{{< highlight INI >}}
Mar 23 09:15:02 vultr kernel: [917417.083117] php[22852]: segfault at 7fd414540bc0 ip 00007fd414540bc0 sp 00007ffdb0d4d858 error 15 in libssl.so.1.1[7fd41453d000+4000]
{{< / highlight >}}

这时我终于想起编译PHP时给出的一条警告，内容大致是openssl版本可能存在冲突，因为我之前使用apt安装了一个旧版本（1.0.2g）的openssl，但编译时出现找不到evp.h头文件的问题，于是我从openssl.org下载了一个1.1.0g的版本自行编译安装（prefix选择了/usr/local/openssl），之后PHP可以正常编译（除了给出上述警告 我就没管它= =），但运行时便出现了PHP-FPM崩溃的问题。/usr/local/lib下同时存在两个版本的libssl.so（PHP加载了1.1版本）。现在问题已经比较明确是OPENSSL版本冲突导致问题（也许是静态库使用了1.0.2g，动态库使用了1.1.0g？）于是：

{{< highlight bash >}}
apt-get remove openssl
cd /path/to/openssl/source/
./config --prefix /usr/local/openssl && make && make install
ls /usr/local/lib/libssl*
/usr/local/lib/libssl.a /usr/local/lib/libssl.so /usr/local/lib/libssl.so.1.1{{< / highlight >}}

重新编译PHP安装，警告消失，也不再出现502错误