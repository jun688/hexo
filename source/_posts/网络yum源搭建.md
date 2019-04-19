---
title: 网络yum源搭建
tags:
  - linux
originContent: ''
categories:
  - linux
toc: true
date: 2018-12-12 16:20:18
---

### 1.网络yum源（c/s）

s 192.168.2.18 (server)
  1)#rpm -q vsftpd //提供ftp服务
   匿名默认访问的目录  /var/ftp  //如果共享，则把文件拷贝到这个目录
  2)新建一个目录，把光盘挂载到新建的目录下即可
    注：#fuser -mv /mnt  //查看谁在使用，无法卸载
        #fuser -ik /mnt  //杀死所有使用者进程
  #mkdir /var/ftp/iso
  #mount /dev/cdrom /var/ftp/iso

c 192.168.2.0/24 (client)
  1)保证能访问共享
    #ping -c 3 192.168.2.45
    #ftp 192.168.2.45
  2)添加配置文件
```language
   [root@localhost yum.repos.d]# cat iso.repo 
   [rhel-serverftp]
   name=yum server ip is 192.168.2.45
   baseurl=ftp://192.168.2.45/iso/Server/  //指定软件仓库的位置
   enabled=1
   gpgcheck=0
```

   制作iso文件 #cat /dev/cdrom > /rhel5.iso
   #mount -o loop /rhel5.iso /mnt   //挂载镜象文件
   
  
### 2.源码包安装
```language
  #rpm -qa | grep gcc
  #rpm -qa | grep make
  #rpm -qa | grep "c++"
  #yum -y grouplist | grep "开发工具" "开发库"
```

  * 1)解压
```language
    #tar -zxvf httpd-2.2.9.tar.gz -C /usr/src/
  * 2)配置
   #./configure --prefix=/usr/local/sourcehttpd
   #./configure --help //查看帮助
```

   --prefix:指定把软件安装到什么目录，如果不指定，则默认安装到/usr/local/目录下
   在解压的目录下生成Makefile/Makefile.in文件，放置默认配置信息。
  * 3)编译
    #make clean //清除配置生成的临时文件
    #make 
  * 4)安装
   #make install

   #netstat -utnlp | grep :3306 //查看端口号

   mysql数据库服务软件包安装
```language
   #cd /usr/local/mysqld/bin
   #./mysql_install_db --user=mysql //初始化数据库
   #./mysqld_safe &  启动服务,在后台运行
   #./mysql 登录数据库服务器
```

  无法登录时
    1.修改配置文件 /usr/local/mysql-5.0/support-files/
```language
      #cp  my-medium.cnf   /etc/my.cnf
      #vim /etc/my.cnf
      #jobs   //查看后台运行的进程
      #kill -9 %1  //杀死后台进程
      #export PATH=/usr/local/mysqld/bin/:$PATH
        // 全局添加自定义变量（当前有效）
      #vim /etc/rc.local 
       //把export PATH=/usr/local/mysqld/bin/:$PATH添加到配置文件中，
           永远有效
```

   * 5)源码包卸载

    1).#cd /usr/src/mysql-5.0.1
       #make uninstall

    2).直接删除安装目录
       #rm -rf /usr/local/mysqld

    3).升级源码包（打补丁） xxx.patch
       #cd /usr/local/mysqld
       #patch -p1 < xxx.patch // 1代表几级目录
