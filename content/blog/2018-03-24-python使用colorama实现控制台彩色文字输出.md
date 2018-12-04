---
title: python使用colorama实现控制台彩色文字输出
author: Eternal-flame-AD
layout: post
identifier: 2c010c70a608620272cdcc8e73cf0f50
date: 2018-03-24T13:51:25+00:00
featured_image: Python_logo_and_wordmark.svg_.png
categories:
  - Python
  - 技术
  - 日常
tags:
  - Python
  - 控制台

---
为了之后写代码方便，打算写一个[日志模块][1]，需要实现在控制台上展示不同颜色的文字区分日志紧急度，于是发现了这个colorama模块（[Github][2]）~

colorama可以实现跨平台的前/背景彩色输出，同时还可以加入调暗等文字效果，十分方便

<!--more-->

使用示例：

{{< highlight python >}}
import colorama  
from colorama import Fore, Back, Style  
print(Fore.RED + "some red text")  
print(Back.GREEN + "and with a green background")  
print(Style.DIM + "and in dim text")  
print(Style.RESET_ALL)  
print("back to normal now")
{{< / highlight >}}

同时也可以设置自动RESET：

{{< highlight python >}}
from colorama import init  
init(autoreset=True)  
print(Fore.RED + "some red text")  
print("automatically back to default color again")
{{< / highlight >}}
有这些内置颜色：

Fore: BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE, RESET.  
Back: BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE, RESET.  
Style: DIM, NORMAL, BRIGHT, RESET_ALL
 [1]: https://github.com/eternal-flame-AD/eflog
 [2]: https://github.com/tartley/colorama