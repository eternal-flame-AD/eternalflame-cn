---
title: 在openwrt/lede上部署dnscrypt-proxy，强化家庭网络dns安全
author: Eternal-flame-AD
layout: post
identifier: b26d3d40cf9f5655cdb6c8308cba9864
date: 2018-07-24T11:04:54+00:00
categories:
  - Openwrt应用
  - 技术
signature: dnscypt-proxy + dnsmasq

---
自从部署了透明代理之后，整个人都变得宅了很多（家里的wifi太美好了）。但一直有个问题让我很烦恼——有部分网站因为不在gfwlist中，对应域名的dns查询并没有走代理，存在一个可能被跟踪的隐患，而如果全部将dns查询代理出去，有很多国内网站会出现解析到国外的问题。后来做了一些研究，使用dnsmasq+dnscrypt-proxy的方法解决了这个问题，将大致方法记录如下：

首先我们要分析一下自己的需求——国内域名直接解析（我也不在乎了，国内网站很多连https都懒得上我也不觉得有什么隐私可言），国外域名使用DNS-over-TLS或DNS-over-HTTPS进行解析。也就是我们需要几个工具：

  1. 一个国内域名表 我找到了[felixonmars/dnsmasq-china-list][1]这个很赞的项目~里面有一个较全的国内域名列表，大小有1.48M，实测基本能涵盖对速度有要求的网站（视频网站等），有少部分流量较少的域名没有涵盖（个人博客等）但问题不是很大，“国内域名”的定义是有国内CDN节点或NS服务器在国内，这两个标准正好符合我的需求~~~送个小心心~~，小透明也写了个脚本跟踪自己的国内域名记录，会不时发PR给原作者更新~
  2. DoH/DoT代理，这里使用了[dnscrypt-proxy][2]（注意这里没有使用软件包中的dnscrypt，实测不是很好用所以自己部署的）
  3. 分流工具，使用Openwrt自带的dnsmasq

步骤如下：

<li style="list-style-type: none;">
  <ol>
    <li>
      下载中国域名配置 <pre># wget https://github.com/felixonmars/dnsmasq-china-list/raw/master/accelerated-domains.china.conf
# sed -i 's/114.114.114.114/&lt;你使用的国内DNS服务器&gt;/' accelerated-domains.china.conf</pre>
    </li>
    
    <li>
      将中国域名配置加入到dnsmasq配置中，在/etc/dnsmasq.conf的文件尾加入一行： <pre>conf-file=/path/to/your/accelerated-domains.china.conf</pre>
    </li>
    
    <li>
      下载dnscrypt-proxy：这一步推荐在pc上做，在<a href="https://github.com/jedisct1/dnscrypt-proxy/releases">发布页</a>下载对应架构的二进制后使用upx压缩（实测可以从10M+压缩到不到2M，空间多的请随意），使用scp或其它工具将最终的的二进制传到路由器/usr/sbin/下，加执行权限 <pre># wget https://github.com/jedisct1/dnscrypt-proxy/releases/download/2.0.16/dnscrypt-proxy-linux_arm64-2.0.16.tar.gz
# tar -xvf dnscrypt-proxy-linux_arm64-2.0.16.tar.gz
# cd dnscrypt-proxy-linux_arm64-2.0.16
# upx --lzma dnscrypt-proxy
# cp dnscrypt-proxy /usr/sbin
# chmod +x /usr/sbin/dnscrypt-proxy</pre>
    </li>
    
    <li>
      添加dnscrypt-proxy配置文件，这一步因为要改动的比较多也推荐在PC上做（估计vim大佬也不会看小透明说到这里直接自己上手了），先将刚刚下载的压缩包里的example-dnscrypt-proxy.toml复制出来，改名为dnscrypt-proxy.toml，初始配置注释很全面，这里小透明只说必须的改动，大佬们可以自己修改呐~ <ol>
        <li>
          L36，修改dnscrypt监听端口，为了不和dnsmasq冲突修改一个端口号
        </li>
        <li>
          L165，初始用来解析DoH服务器域名的一个普通dns，默认dns国内用不了，可以用1.1.1.1:53或者114.114.114.114:53
        </li>
      </ol>
      
      <p>
        修改完成后将保存的dnscrypt-proxy.toml拷贝到路由器/etc/config下</li> 
        
        <li>
          配置init.d，开机自启，这里有一个已经写好的<a href="https://github.com/etam/DNS-over-HTTPS-for-OpenWRT/blob/master/dnscrypt-proxy">文件</a>，拷贝到/etc/init.d/下即可，之后启用，运行dnscrypt-proxy <pre># cd /etc/init.d
# wget https://github.com/etam/DNS-over-HTTPS-for-OpenWRT/raw/master/dnscrypt-proxy
# ./dnscrypt-proxy enable
# ./dnscrypt-proxy start</pre>
        </li>
        
        <li>
          配置dnsmasq，修改默认DNS服务器到dnscrypt-proxy，这一步可以直接在LuCl里做，比用终端来的快，修改默认dns服务器为127.0.0.1#<你的dnscrypt监听端口>，切记dnsmasq的端口号用的是#分隔不是:，填错会导致dnsmasq无法启动，删除所有其他上游dns服务器，在WAN接口配置里“忽略远端通告的DNS服务器”
        </li>
        <li>
          验证，可以使用dnsmasq日志或者dnscrypt-proxy日志检查查询是否被正确分配
        </li></ol> </li> </ol> 
        
        <p>
          <!--more-->
        </p>

 [1]: https://github.com/felixonmars/dnsmasq-china-list
 [2]: https://github.com/jedisct1/dnscrypt-proxy