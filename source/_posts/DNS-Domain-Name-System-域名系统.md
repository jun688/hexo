---
title: DNS (Domain Name System)域名系统
tags:
  - linux
originContent: ''
categories:
  - linux
toc: true
date: 2018-12-12 16:39:30
---

#### 1.DNS服务的功能
   1.1 把主机名解析到IP地址  正向解析
   1.2 把IP地址解析到主机名  反向解析
   
#### 2.DNS域名空间树形接口
                          .（根域）13台根域服务器
						  |
	 org     net   edu  gov   mil   hk   com   cn     顶级域
                                          |
                                 baidu   163   jd     二级域
                                          |
                                    ftp  www mail 	 三级域

	com   商业机构
	gov   政府部门
	int   国际组织
	mil   军事部门
	net   网络服务组织
	org   其他组织
	edu   教育机构

#### 3. 安装提供服务的软件包
	[root@localhost ~]# rpm -qa | grep bind  
	ypbind-1.19-12.el5_6.1
	bind-devel-9.3.6-20.P1.el5
	bind-libs-9.3.6-20.P1.el5
	bind-utils-9.3.6-20.P1.el5
	bind-9.3.6-20.P1.el5
	bind-chroot-9.3.6-20.P1.el5			

   安装bind后并不生成/etc/named.conf文件。
   在red hat enterprise linux 5中，bind程序的默认虚拟目录是/var/named/chroot.
   对虚拟目录而言，/etc/named.conf实际上是/var/named/chroot/etc/named.conf.
   
 #### 4. DNS的启动、停止、测试
    #service named start
	#service named stop
	#service named restart
	在bind中还提供了一个专门用于远程配置DNS守护进程的命令rndc,
	r代表remote,使用rndc可以执行启动、停止、重载和转储等操作
	#rndc  stop  //停止DNS服务
	#rndc status  //查看named进程的配置情况
	
#### 5. 测试DNS服务器
   5.1 nslookup
      #nslookup
	  >192.168.0.l2
   5.2 host
      #host  www.subasafly.com
	  #host  www.subasafly.com.zone
	  #host  192.168.0.12
   5.3 dig
     #dig www.subasafly.com.zone
   5.4 ping
      #ping www.subasafly.com.zone
	  
#### 6. DNS服务器配置
```language
[root@localhost ~]# rndc-confgen
# Start of rndc.conf
key "rndckey" {
        algorithm hmac-md5;
        secret "Y3lkWdmcSEaTWbicn1loDQ==";
};

options {
        default-key "rndckey";
        default-server 127.0.0.1;
        default-port 953;
};
# End of rndc.conf

# Use with the following in named.conf, adjusting the allow list as needed:
# key "rndckey" {
#       algorithm hmac-md5;
#       secret "Y3lkWdmcSEaTWbicn1loDQ==";
# };
# 
# controls {
#       inet 127.0.0.1 port 953
#               allow { 127.0.0.1; } keys { "rndckey"; };
# };
# End of named.conf
```
