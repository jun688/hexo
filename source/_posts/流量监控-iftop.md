---
title: '[流量监控] iftop'
tags:
  - 服务器
originContent: >-
  > linux命令行下，一款实时网络流量监控软件，正如yum仓对他的描述:“Command line tool that displays
  bandwidth usage on an interface”


  安装指南

  ```language

  yum install iftop

  ```

  如果你的yum仓里找不到这款软件，那么你可以来这里直接下载rpm包安装，https://www.rpmfind.net/linux/rpm2html/search.php?query=iftop&system=Extras


  使用指南

  命令就是这么简单粗暴，更多的个性定制可以查看help


  效果说明


  底部的TX和RX，分别指目前实时的发送(Transmit)带宽和接收(Receive)带宽，中间的部分显示的 => 和 <= ，表示的是流量方向 => 为发
  送， <= 为接收。


  白色底纹表示总量的占比比例。
categories:
  - 服务器
toc: true
date: 2018-12-06 11:47:16
---

> linux命令行下，一款实时网络流量监控软件，正如yum仓对他的描述:“Command line tool that displays bandwidth usage on an interface”

#### 安装指南
```language
yum install iftop
```
如果你的yum仓里找不到这款软件，那么你可以来这里直接下载rpm包安装，https://www.rpmfind.net/linux/rpm2html/search.php?query=iftop&system=Extras

#### 使用指南
命令就是这么简单粗暴，更多的个性定制可以查看help

#### 效果说明
![](/images/image2018-11-25_20-26-40-3.png)
底部的TX和RX，分别指目前实时的发送(Transmit)带宽和接收(Receive)带宽，中间的部分显示的 => 和 <= ，表示的是流量方向 => 为发 送， <= 为接收。

白色底纹表示总量的占比比例。