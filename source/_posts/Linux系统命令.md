---
title: Linux系统命令
tags:
  - linux
originContent: "1.#cat /etc/redhat-release\n\n2.#uname -r： 内核版本号\n\n3.#who ：当前登录用户\n\n4.#hostname ：主机名查看和修改\n\n5.#pwd :当前目录\n\n6.#su - 用户名  ： “ -”切换用户身份和切换用户环境\n\n7. ctrl+l:清除当前窗口的命令\n\n8. 命令行窗口操作：\n    1)ctrl + \"-\":缩小\n    2）ctrl+shift+\"++\":放大\n\n9.#usernadd 用户名: 添加用户\n\n10.#passwd 用户名: 修改添加帐号的密码\n\n11 . /bin: 一般用户命令\n     /sbin:管理员命令\n     /lib:库文件\n     /selinux:Selinux设置和配置文件\n\t  /usr:源码包\n     /boot:引导文件\n     /lost+found:检测磁盘文件的生成的碎片文件\n     /opt:第三方软件默认安装目录\n     /srv /proc：虚拟目录，不占用磁盘空间，文件存放在内存中,服务在运行过程中调用的文件\n     /var:日志文件\n     /dev:设备文件\n     /mnt:移动设备挂载目录\n     /tftpboot:tftp根目录\n             \n12.命令语法格式：命令名 空格 [选项] 空格 [文件或目录名]\n\n13.cd .. 上一级目录\n   cd ../..:上一级的上一级\n   cd ~:回上一级目录\n      \n14.网卡配置\n   #ifconfig 192.168.2.41/24        #/24是mask的简写\n   #ifconfig 192.168.2.41 netmask 255.255.255.0\n   #ifconfig 192.168.2.41    \n\n15.#ping -c 4 192.168.2.41  #ping 4次\n\n16.#mii-tool etho  //网线是否接通\n\n17.#iptables -L //查看防火墙\n\n18. #which  文件名   //查看软件位置\n\n19.rpm -qf /usr/bin/vncviewer #查看文件是有那个文件提供的\n\n20.#mount //查看系统挂载了哪些设备\n\n21.“｜”管道：前边命令的输出结果作为后面命令的输入结果\n    #ls | grep vnc\n\n22. #rpm -q 软件名                  //查看软件是否安装过\n\n23. #rpm -ivh   软件包名称   //安装软件包\n  \n24.#vncviewer ip(远程登录的服务器ip地址) //远程连接\n\n25. 粘贴: 选择当前粘贴的内容，按鼠标滑轮\n\n26.#umount 设备名   //卸载\n\n27.#mount -o loop /root/xxx.iso   /mnt //挂载iso文件\n\n28. #fdisk -l //查看磁盘分区\n\n29. 网卡配置永久IP\n     1)图形：#system-config-network\n     2)修改配置文件：/etc/sysconfig/network-scripts/ifcfg-eth0\n\n30.vim中显示行： set nu\n\n31.#ifdown eth0 //停用网卡\n\n32.#ifup eth0 //启用网卡"
categories:
  - linux
toc: true
date: 2018-12-11 18:51:12
---

```language
1.#cat /etc/redhat-release

2.#uname -r： 内核版本号

3.#who ：当前登录用户

4.#hostname ：主机名查看和修改

5.#pwd :当前目录

6.#su - 用户名  ： “ -”切换用户身份和切换用户环境

7. ctrl+l:清除当前窗口的命令

8. 命令行窗口操作：
    1)ctrl + "-":缩小
    2）ctrl+shift+"++":放大

9.#usernadd 用户名: 添加用户

10.#passwd 用户名: 修改添加帐号的密码

11 . /bin: 一般用户命令
     /sbin:管理员命令
     /lib:库文件
     /selinux:Selinux设置和配置文件
	  /usr:源码包
     /boot:引导文件
     /lost+found:检测磁盘文件的生成的碎片文件
     /opt:第三方软件默认安装目录
     /srv /proc：虚拟目录，不占用磁盘空间，文件存放在内存中,服务在运行过程中调用的文件
     /var:日志文件
     /dev:设备文件
     /mnt:移动设备挂载目录
     /tftpboot:tftp根目录
             
12.命令语法格式：命令名 空格 [选项] 空格 [文件或目录名]

13.cd .. 上一级目录
   cd ../..:上一级的上一级
   cd ~:回上一级目录
      
14.网卡配置
   #ifconfig 192.168.2.41/24        #/24是mask的简写
   #ifconfig 192.168.2.41 netmask 255.255.255.0
   #ifconfig 192.168.2.41    

15.#ping -c 4 192.168.2.41  #ping 4次

16.#mii-tool etho  //网线是否接通

17.#iptables -L //查看防火墙

18. #which  文件名   //查看软件位置

19.rpm -qf /usr/bin/vncviewer #查看文件是有那个文件提供的

20.#mount //查看系统挂载了哪些设备

21.“｜”管道：前边命令的输出结果作为后面命令的输入结果
    #ls | grep vnc

22. #rpm -q 软件名                  //查看软件是否安装过

23. #rpm -ivh   软件包名称   //安装软件包
  
24.#vncviewer ip(远程登录的服务器ip地址) //远程连接

25. 粘贴: 选择当前粘贴的内容，按鼠标滑轮

26.#umount 设备名   //卸载

27.#mount -o loop /root/xxx.iso   /mnt //挂载iso文件

28. #fdisk -l //查看磁盘分区

29. 网卡配置永久IP
     1)图形：#system-config-network
     2)修改配置文件：/etc/sysconfig/network-scripts/ifcfg-eth0

30.vim中显示行： set nu

31.#ifdown eth0 //停用网卡

32.#ifup eth0 //启用网卡
```
