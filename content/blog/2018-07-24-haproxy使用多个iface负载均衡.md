---
title: HAProxy使用多个iface负载均衡
author: Eternal-flame-AD
layout: post
date: 2018-07-24T11:19:55+00:00
categories:
  - Openwrt应用

---
因为家里软路由是两条宽带上游，所以希望自己路由器上运行的的HAProxy能够在两个路由器上均分负载，需求大致是这样：

{{< highlight INI >}}
eth0.2
+---------------+               +-----------------+
|               +--------------->                 |
|   HAProxy     |               |    Backend      |
|               +--------------->                 |
+---------------+               +-----------------+
                     eth0.3
{{< /highlight >}}

原来查了HAProxy的文档竟然没有看出来怎么做，后来在stackoverflow上找了很久发现server句是可以使用source参数的，这个小透明觉得文档写的不是很清楚，故在这里记录一下，大致写法是这样：

{{< highlight INI >}}
server back1 1.2.3.4:31646 source 0.0.0.0 interface eth0.2
server back2 1.2.3.4:31646 source 0.0.0.0 interface eth0.3
{{< /highlight >}}

&nbsp;