---
title: 辅助DNS服务器搭建
tags:
  - linux
originContent: ''
categories:
  - linux
toc: true
date: 2018-12-12 16:43:13
---

#### 1. 辅助DNS服务器搭建
    用IP是192.168.2.168 作plj.com域的辅助DNS服务器
	 * 1.1. rpm -ivh bind
	 * 1.2. ping -c 4 192.168.2.51
		#iptables -F //清防火墙
		#setenforce 0
	 * 1.3.从DNS服务器编辑自己的主配置文件
		#vim /var/named/chroot/etc/named.conf
		options {
		   directory  "/var/named/";
		}

		zone "plj.com" {
		   type  slave;
		   file  "slaves/plj.com.zone"; //相对路径/var/named/saleve/数据库文件，数据库文件名可以自己给定
		   masters {192.168.2.51;}; //主DNS服务器IP
		}

	 * 1.4.修改主DNS服务器配置文件，允许谁从我这边下载数据库文件。 named.conf
		zone "plj.com" {
		  type masters;
		  file "plj.com.zone";
		  allow-transfer{ 192.168.2.168;192.168.2.51;}; //用来指定辅助DNS的IP,谁做辅助DNS服务器
		}

	 * 1.5.配置主数据库文件 /var/named/chroot/var/named/
		plj.com.   	    IN   NS  dns.plj.com.
		dns.plj.com.    IN    A  192.168.2.51
		plj.com.        IN   NS slave1.plj.com.
		slave1.plj.com. IN    A 192.168.2.168
		plj.com.        IN   NS slave2.plj.com.
		slave1.plj.com. IN    A 192.168.2.50

	 * 1.6.把辅助DNS主机的named服务开启

	 * 1.7.客户端配置
		#vim /etc/resolv.conf
		nameserver 192.168.2.51
		nameserver 192.168.2.132
	  
	 * 1.8.测试
	   51 service named stop
	   client  host www.plj.com

#### 2. 事务签名
     防止别的辅助DNS服务器下载主DNS服务器数据库文件
    * 2.1 生成密钥
	   master dns
	   #dnssec-keygen -a hmac-md5 -b 512 -n host jing
	   -a:算法
	   -b:长度
	   -n:类型
	   名字
	   #cat Kjing.+157+45353.private >> /var/named/chroot/etc/named.conf  

    * 2.2 制作密钥文件  vim /var/named/chroot/etc/named.conf 
	    key "rndckey" {
           algorithm hmac-md5;  //密钥算法
           secret "Y3lkWdmcSEaTWbicn1loDQ==";  //key
       };
	   
    * 2.3 调用密钥文件 vim /var/named/chroot/etc/named.conf 
    	zone "zyj.com" {
			type master;
			file "zyj.com.zone";
			allow-transfer{key rndckey;}; //调用密钥,密钥文件名与制作文件名相同
		};
		
    * 2.4 配置辅助DNS配置文件 vim /var/named/chroot/etc/named.conf 
	    zone "zyj.com" {
           type  slave;
           file  "slaves/zyj.com.zone";
           masters {192.168.0.10 key rndckey;};   //密钥文件名与主密钥文件名相同

		};

#### 3.分离解析
     不同客户端访问相同主机名时，给出不同的解析结果
	 view "视图名称"{
	    
	 };
	 
	 192.168.2.51  plj.com
	 
	 client 192.168.2.22   www.plj.com  192.168.2.200
	 client 192.168.2.202  www.plj.com  192.168.2.210
	 
	 
	 * 3.1 编辑主DNS的配置文件 vim /var/named/chroot/etc/named.conf
	  第一种写法：
	   view "pc200" {
			match-clients {192.168.0.200;};
			zone "zyj.com" {
				type master;
				file "zyj.com.zone";
				allow-transfer{key rndckey;};
			};
		};
		view "pc210" {
			match-clients {any;};
			zone "zyj.com" {
				type master;
				file "zyj3.com.zone";
				allow-transfer{key rndckey;};
			};
		};
		第二种写法
		acl "hosts1" {192.168.0.18;};
		view "pc200" {
			match-clients { hosts1;};
			zone "zyj.com" {
				type master;
				file "zyj.com.zone";
				allow-transfer{key rndckey;};
			};
		};
	* 3.2 编辑主数据库文件 /var/named/chroot/var/named/zyj.com.zone
         	www             IN        A    192.168.0.200
			
			
			
			
#### 4.搭建网站服务器
    用IP是192.168.2.51的主机作网站服务，并让客户端通过www.plj.com访问到网站的首页文件
    
   * 4.1 配置文件中常用的参数
        /etc/httpd/  工作目录
		httpd.conf   主配置文件
		/conf/：自己的配置文件
		/conf.d/:联合自己其他服务的配置文件
		/logs/：日志文件 
		        access.log 访问日志
				error_log  错误日志
		/modules/:模块文件
		/run/:存放PID号
		
		httpd.conf配置参数：
			ServerTokens OS :提供网站服务软件的版本
			ServerRoot "/etc/httpd" :服务跟目录
			pidFile run/httpd.pid:服务的PID号文件
			Timeout 120：超时单位秒
			keepAlive Off/On：是否开启长连接,一次建立连接可以访问多个页面
			MaxKeepAliveRequests 100:长连接的最大请求数
			KeepAliveTimeout 15:长连接的超时时间
			
			网站服务器运行方式：
				  进程方式：默认
				 <IfModule prefork.c>
					 StartServers       8   //开启进程数
					 MinSpareServers    5   //最小空闲服务
					 MaxSpareServers   20   //最大空闲服务
					 ServerLimit      256   //每个进程能接受多少个并发请求
					 MaxClients       256   //客户端最大请求连接数
					 MaxRequestsPerChild  4000 //最大请求数，每个进程累计处理达到4000，自动杀死
				 </IfModule>
			
				线程方式：
				 <IfModule worker.c>
					 StartServers         2
					 MaxClients         150
					 MinSpareThreads     25
					 MaxSpareThreads     75
					 ThreadsPerChild     25
					 MaxRequestsPerChild  0
				 </IfModule>
		    服务启动脚本：/etc/sysconfig/httpd
		    改动服务器运行方式：vim /etc/sysconfig/httpd
			 	 HTTPD=/usr/sbin/httpd.worker
           
			 Listen 80:监听端口
		     记录服务名，端口号文件：/etc/services
			    注：#elinks --dump http://www.zyj.com //命令行测试域名主页
			 LoadModule :加载模块
			 Include conf.d/*.conf //包含文件
			 User apache  :所有者
			 Group apache ：所属组
			 ServerAdmin root@localhost //管理员邮箱
			 ServerName:指定网站服务器主机名
			 DocumentRoot "/var/www/html":指定网页文件存放目录

			<Directory />  //网页文件夹目录/var/www/html
			     Options FollowSymLinks
			     AllowOverride None
		    </Directory>
			
			<Directory "/var/www/html"> 
		         Options Indexes FollowSymLinks 
				 AllowOverride None
				 Order allow,deny  //设置访问权限，默认是拒绝所有人访问
				 Order deny,allow  //设置访问权限，默认是允许所有人访问
				 Allow from 192.168.2.168 //只允许2.168访问
		         Allow from all  //所有客户端访问
		     </Directory>
			 
			 DirectoryIndex :指定网站首页文件名
			 LogFormat:记录日志格式
			 CustomLog:指定日志存放位置和格式
			 Alias:目录别名
			 
			 
   * 4.2 用户个人主页
         访问web服务器上本地用户家目录下的网页文件。
		 http://www.plj.com/~plj  //访问家目录 ~plj代表家目录 
		 4.2.1 添加本地用户  
		      #useradd 用户名
		 4.2.2 修改httpd.conf 开启用户个人主页功能
		      1.注释 #UserDir disable
			  2.开启 UserDir public_html //指定个人主页存放路径 /home/plj/public_html
			  
		4.2.3 开启访问权限
			<Directory /home/*/public_html>   //发布个人主页目录
				//可以在目录下执行什么样的文件类型
				AllowOverride FileInfo AuthConfig Limit
				Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
				//设置访问网页文件的方式
				<Limit GET POST OPTIONS>   //上传文件使用方式
					Order allow,deny
					Allow from all
				</Limit>
				<LimitExcept GET POST OPTIONS>  //除了这几种方式
					Order deny,allow
					Deny from all
				</LimitExcept>
			</Directory>
		4.2.4 从启服务，让刚才的配置生效
		    #service httpd restart
		4.2.5 #su - plj
		    #mkdir public_html
			#cd public_html
			#echo "xxxxx" > index.html
			#su - root
			#chmod o+rx  /home/plj
		4.2.6 测试
		    http://www.plj.com/~plj
			#elinks --dump http://www.plj.com/~plj
			
   * 4.3 虚拟主机
       4.3.1 基于域名的虚拟主机   通过主机名区分
	          1. 配置DNS
			     在DNS的正向区域文件里写A记录
				  www.plj.com.       IN  	A 		192.168.2.51
				  bbs.plj.com.	  	 IN		A		192.168.2.51
			  2. 编辑/etc/httpd/conf/httpd.conf,发布虚拟主机
			      <VirtualHost>
				      虚拟主机参数
				  </VirtualHost>
			   开启NameVirtualHost 192.168.0.10:80   //指定虚拟主机是基于域名的
			   
			    <VirtualHost 192.168.0.10:80>
					ServerName www.zyj.com
					DocumentRoot /www
					ErrorLog logs/www.zyj.com-error_log
					CustomLog logs/www.zyj.com-access_log 
				</VirtualHost>
				<VirtualHost 192.168.0.10:80>
					ServerName bbs.zyj.com
					DocumentRoot /bbs
					ErrorLog logs/bbs.zyj.com-error_log
					CustomLog logs/bbs.zyj.com-access_log  common
				</VirtualHost>
			  3. 创建虚拟主机存放网页的目录，要与httpd.conf配置文件中的一致
			     #mkdir  /www
				 #mkdir  /bbs
				 #echo "www"  > /www/index.html
				 #echo "bbs" > /bbs/index.html
			  4. 从启服务
			     #service httpd restart
			  5. 客户端测试
			    
				注：测试一直到欢迎页的原因  selinux
				  #setenforce 0  //关闭selinux
				  #getenforce    //查看
				  #vim /etc/sysconfig/selinux  //配置文件修改
				    
	   4.3.2 基于IP的虚拟主机     通过IP地址区分
	          1. 有两块网卡
			     #ifconfig eth0 
			  2. 编辑主配置文件httpd.conf
			     注释NameVirtualHost 192.168.0.10:80
				<VirtualHost 192.168.0.10:80>
					ServerName 192.168.0.100
					DocumentRoot /100
					ErrorLog logs/100.zyj.com-error_log
					CustomLog logs/100.zyj.com-access_log  common
				</VirtualHost>
			     
	   4.3.3 基于端口的虚拟主机   通过端口区分
	          1. 修改主配置文件，监听8080端口
			     Listen 8080
				 <VirtualHost 192.168.0.10:8080>
					ServerName  www.plj.com
					DocumentRoot /8080
					ErrorLog logs/8080.zyj.com-error_log
					CustomLog logs/8080.zyj.com-access_log
				</VirtualHost>
   * 4.4 服务的访问控制
		  Order allow,deny  默认拒绝所有客户端访问
		  Allow from 192.168.2.168
		  allow from all

		  Order deny,allow  默认允许所有客户端访问
		  deny from 192.168.2.168
		  deny from all
   * 4.5 用户认证
        访问网站网页文件时需要提供用户名和密码 才可以访问。
		http://www.plj.com:8080  /admin username password
		file:///usr/share/doc/httpd-2.2.3/migration.html
		 4.5.1 修改主配置文件 httpd.conf
			<VirtualHost 192.168.0.10:8080>
					ServerName  www.plj.com
					DocumentRoot /8080
					ErrorLog logs/8080.zyj.com-error_log
					CustomLog logs/8080.zyj.com-access_log 
					<Directory "/admin">  //访问目录限制
					   Order allow,deny
					   allow from 192.168.2.51
					   AuthType  Basic   //指定认证类型，所有浏览器都支持
					   AuthName "自己起名"  //指定认证域的名称
					   AuthDBUserFile "/var/www/***.txt"   //指定保存认证用户名和密码文件存放位置
					   require  valid-user   //设置用户访问权限,valid-user:有效用户
					</Directory>
			</VirtualHost>
			
			4.5.2 创建用户认证文件
			     #rpm -qf /usr/bin/htpasswd
				 #htpasswd -c /var/www/***.txt           admin    （回车输入密码）
				                保存密码和文件存放位置   用户名
								
				 -c:第一次创建认证文件
				 [root@localhost www]# htpasswd -D /www/user.txt admin1 //删除认证用户
				 Deleting password for user admin1
				 [root@localhost ~]# htpasswd -c /www/user.txt admin
				 New password: 
				 Re-type new password: 
				 Adding password for user admin
				 
			4.5.3 定义别名
				Alias /students "/var/www/html/share/students"
				<Directory "/var/www/html/share/students/">
					Options Indexes MultiViews
					AllowOverride None
					Order allow,deny
					Allow from all
				</Directory>	
           
			 4.5.4 给脚本目录起别名
				ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
				<Directory "/var/www/cgi-bin">
					AllowOverride None
					Options None
					Order allow,deny
					Allow from all
				</Directory>


   