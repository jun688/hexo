---
title: '[TCP抓包] tcpflow'
tags:
  - 服务器
originContent: >-
  > tcpflow是一款抓包工具，这个抓包工具与tcpdump不同的是它是以流为单位显示数据内容，而tcpdump以包为单位显示数据。我们平常
  会经常分析HTTP数据，用tcpflow会更便捷。 tcpflow看起来更像wireshark的命令行版本，他可以直接从网卡抓包，并且解析出来，支持http
  ftp这些L7协议，也支持tcp udp抓包，
  还可以从tcpdump中读取东西，因此是一个非常好的看包工具，有了这个东西以后再也不用把线上服务器上的dump抓下来再用 wireshark打开了。


  下方选用的修改版，所以编译后，man帮助指令得不到正确的反馈，请参看:
  http://www.circlemud.org/jelson/software/tcpflow/tcpflow.1.html


  #### 安装指南

  如果没有安装unzip，或者提示Can't find pcap.h，则安装libpcap-devel包即可。

  ```language
   yum -y install libpcap-devel unzip
  ```


  然后下载压缩包，继续安装

  ```language

  wget "https://github.com/mukhin/tcpflow/archive/master.zip" -O
  tcpflow-master.zip

  unzip tcpflow-master.zip

  cd tcpflow-master

  ./configure

  make

  make install

  cd ..

  rm -rf tcpflow-master*

  ```

  #### 使用指南

  ```language

  # 抓取网卡eth3，发往主机192.168.111.111的报文

  tcpflow -t -cp host 192.168.111.111 -i eth3
   
  # 抓取目标端口为3306，内容包含"update "的报文

  tcpflow -t -cp dst port 3306|grep -i "update "

  ```

  过滤器表达式，同tcpdump，不熟悉的可以查看
  http://www.circlemud.org/jelson/software/tcpflow/tcpflow.1.html ，FILTERING
  EXPRESSIONS 部分。


  #### 效果说明


  ![](/images/image2018-11-25_20-38-52-1.png)

  发起了一次 wget 请求，同时监听了 db.master 这个域名(即192.168.3.90)


  得到两次tcp交互，一次为蓝色，可以看到：


  这是一次从192.168.4.32 发起的请求，在请求index.html页面 

  UA表名是linux的指令 wget v1.12

  另一次为黄色，可以看到:


  - 192.168.3.90地址反馈说，500错误，内部服务器错误

  - 这台机器上安装的web应用软件是Tengine，但是没有显示版本

  - web服务器上用到的PHP版本是7.0.13
categories:
  - 服务器
toc: true
date: 2018-12-06 11:38:30
---

> tcpflow是一款抓包工具，这个抓包工具与tcpdump不同的是它是以流为单位显示数据内容，而tcpdump以包为单位显示数据。我们平常 会经常分析HTTP数据，用tcpflow会更便捷。 tcpflow看起来更像wireshark的命令行版本，他可以直接从网卡抓包，并且解析出来，支持http ftp这些L7协议，也支持tcp udp抓包， 还可以从tcpdump中读取东西，因此是一个非常好的看包工具，有了这个东西以后再也不用把线上服务器上的dump抓下来再用 wireshark打开了。

下方选用的修改版，所以编译后，man帮助指令得不到正确的反馈，请参看: http://www.circlemud.org/jelson/software/tcpflow/tcpflow.1.html

#### 安装指南
如果没有安装unzip，或者提示Can't find pcap.h，则安装libpcap-devel包即可。
```language
 yum -y install libpcap-devel unzip
```

然后下载压缩包，继续安装
```language
wget "https://github.com/mukhin/tcpflow/archive/master.zip" -O tcpflow-master.zip
unzip tcpflow-master.zip
cd tcpflow-master
./configure
make
make install
cd ..
rm -rf tcpflow-master*
```
#### 使用指南
```language
# 抓取网卡eth3，发往主机192.168.111.111的报文
tcpflow -t -cp host 192.168.111.111 -i eth3
 
# 抓取目标端口为3306，内容包含"update "的报文
tcpflow -t -cp dst port 3306|grep -i "update "
```
过滤器表达式，同tcpdump，不熟悉的可以查看 http://www.circlemud.org/jelson/software/tcpflow/tcpflow.1.html ，FILTERING EXPRESSIONS 部分。

#### 效果说明
![](/images/image2018-11-25_20-38-52-1.png)
发起了一次 wget 请求，同时监听了 db.master 这个域名(即192.168.3.90)

得到两次tcp交互，一次为蓝色，可以看到：

这是一次从192.168.4.32 发起的请求，在请求index.html页面 
UA表名是linux的指令 wget v1.12
另一次为黄色，可以看到:

- 192.168.3.90地址反馈说，500错误，内部服务器错误
- 这台机器上安装的web应用软件是Tengine，但是没有显示版本
- web服务器上用到的PHP版本是7.0.13