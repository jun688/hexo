---
title: '[全局监控] dstat'
tags:
  - 服务器
originContent: "> dstat 是一个可以取代vmstat，iostat，netstat和ifstat这些命令的多功能产品。dstat克服了这些命令的局限并增加了一些另外的功能， 增加了监控项，也变得更灵活了。dstat可以很方便监控系统运行状况并用于基准测试和排除故障。\n\ndstat可以让你实时地看到所有系统资源，例如，你能够通过统计IDE控制器当前状态来比较磁盘利用率，或者直接通过网络带宽数值来比 较磁盘的吞吐率(在相同的时间间隔内)。\n\ndstat将以列表的形式为你提供选项信息并清晰地告诉你是在何种幅度和单位显示输出。这样更好地避免了信息混乱和误报。更重要的 是，它可以让你更容易编写插件来收集你想要的数据信息，以从未有过的方式进行扩展。\n\ndstat的默认输出是专门为人们实时查看而设计的，不过你也可以将详细信息通过CSV输出到一个文件，并导入到Gnumeric或者Excel生 成表格中。\n\n安装指南\n```language\nyum install dstat\n```\n如果你的yum仓里找不到这款软件，那么你可以来这里直接下载rpm包安装，https://www.rpmfind.net/linux/rpm2html/search.php?query=dstat&system=centos\n\n使用指南\n```language\ndstat -tlcdmsnp --tcp\n```\n效果说明\n![](/images/image2018-11-25_20-30-37-2.png)\n\n-tlcdmsnp 的参数我们可以拆解为:\n|参数|说明|\n|-|-|\n|-t\t|显示该行数据监控时的当前时间|\n|-l      |显示负载统计量|\n|-c\t|显示CPU使用率（包括usr，sys，idl等值）|\n|-d\t|显示磁盘读写率|\n|-m\t|显示内存使用率(包括used，buffer，cache，free值)|\n|-s\t|显示交换分区使用率|\n|-n\t|显示带宽使用量|\n|-p\t|显示进程统计量(包括run，blk，new值)|\n|--tcp   |显示tcp请求的状态统计量|"
categories:
  - 服务器
toc: true
date: 2018-12-06 11:43:59
---

> dstat 是一个可以取代vmstat，iostat，netstat和ifstat这些命令的多功能产品。dstat克服了这些命令的局限并增加了一些另外的功能， 增加了监控项，也变得更灵活了。dstat可以很方便监控系统运行状况并用于基准测试和排除故障。

dstat可以让你实时地看到所有系统资源，例如，你能够通过统计IDE控制器当前状态来比较磁盘利用率，或者直接通过网络带宽数值来比 较磁盘的吞吐率(在相同的时间间隔内)。

dstat将以列表的形式为你提供选项信息并清晰地告诉你是在何种幅度和单位显示输出。这样更好地避免了信息混乱和误报。更重要的 是，它可以让你更容易编写插件来收集你想要的数据信息，以从未有过的方式进行扩展。

dstat的默认输出是专门为人们实时查看而设计的，不过你也可以将详细信息通过CSV输出到一个文件，并导入到Gnumeric或者Excel生 成表格中。

#### 安装指南
```language
yum install dstat
```
如果你的yum仓里找不到这款软件，那么你可以来这里直接下载rpm包安装，https://www.rpmfind.net/linux/rpm2html/search.php?query=dstat&system=centos

#### 使用指南
```language
dstat -tlcdmsnp --tcp
```
#### 效果说明
![](/images/image2018-11-25_20-30-37-2.png)

-tlcdmsnp 的参数我们可以拆解为:
|参数|说明|
|-|-|
|-t	|显示该行数据监控时的当前时间|
|-l      |显示负载统计量|
|-c	|显示CPU使用率（包括usr，sys，idl等值）|
|-d	|显示磁盘读写率|
|-m	|显示内存使用率(包括used，buffer，cache，free值)|
|-s	|显示交换分区使用率|
|-n	|显示带宽使用量|
|-p	|显示进程统计量(包括run，blk，new值)|
|--tcp   |显示tcp请求的状态统计量|