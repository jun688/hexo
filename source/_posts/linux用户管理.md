---
title: linux用户管理
tags:
  - linux
originContent: "#### 1.用户管理\n  1）添加\n  ```bash\n     #useradd/adduser [选项] 用户名\n       -g 指定用户的初始组\n       -G 指定用户的附加组\n       -d 指定用户的家目录\n       -c 设置用户的描述信息\n       -s 指定用户使用的shell 默认是/bin/bash  查看shell:/etc/shells\n       -M 不添加家目录\n  ```\n     \n      #useradd test  不加选项时 按系统的默认配置添加帐号\n      有如下特性：\n       * 1.在/home目录下生成与用户同名的目录作为用户的家目录\n       * 2.uid,gid是系统内当前用户的最大uid,gid号+1\n       * 3.shell是 /bin/bash   /sbin/nologin:非交互式的shell\n       * 4.会创建与用户同名的组作为用户的初始组\n     \n     注:用户名不能重名，不能使用特殊字符或关键字，区分大小写，具有描述性       \n       添加用户时当指定的初始组不存在，无法添加用户\n       添加用户指定初始组时，不会在创建一个与用户同名的组，并且创建的组是初始组，用户成员列表不会显示\n  \n  2）修改\n    ```bash\n      #usermod [选项]  用户名\n      #passwd 用户名  //修改用户密码\n    ```\n\n  3）删除\n     ```bash\n      #userdel -r  用户名 \n      //删除用户时把用户的家目录一起删除，不加-r则只删除用户，用户家目录还在。\n      注:如果用户初始组为空，删除用户时把初始组也删除，如果初始组非空，则不删除初始组\n     ```\n\n  4）查看\n  \n    /etc/passwd:保存帐号信息文件\n    root  :    x     :     0      :  0  :     root   : /root : /bin/bash\n    用户名:密码占位符:用户编号uid : gid :用户描述信息: 家目录: shell\n    uid范围:0-65535\n    0   : root\n    1-99:系统帐号\n    100-499:服务帐号\n    500+:普通帐号uid范围\n     \n   shell:工具，命令解释器\n   查看shell:/etc/shells \n\n#### 2.用户组管理\n    ```bash\n      #groups //查看登录的用户属于哪些组，第一列为用户当前所在的组\n           [root@localhost ~]# groups\n            root bin daemon sys adm disk wheel\n  \n       #newgrp 组名  //切换组\n            [root@localhost ~]# groups\n            bin root  daemon sys adm disk wheel\n  \n       #exit //退回到上一个组\n\n       #groups：查看用户组信息，单独使用时，是登录用户查看自己的用户信息\n       #groups username:管理员查看指定用户的用户组信息\n       #newgrp groupname:用户切换到指定的用户组里，用户exit退出当前所在的组\n   ```\n\n      /etc/group: 保存用户组信息文件\n\n\n   ```bash   \n    [root@localhost ~]# head -1 /etc/group\n   ```\n\n  root    :       x        : 0 :     root\n  用户组名:用户组密码占位符:gid:用户组成员列表\n  注：如果某一个组是用户的初始组，用户名不会显示在组成员列表里\n\n#### 3.配置文件\n /etc/defualt/useradd :添加用户使用的默认配置文件\n /etc/skel\n:保存用户登录初始化配置文件,每次新建用户时都会把当前目录下的文件复制到用户的家目录\n /var/spool/mail:邮件存放目录\n \n ```bash\n[root@localhost var]# cat -b /etc/default/useradd \n     1  # useradd defaults file\n     2  GROUP=100    ＃用户初始组\n     3  HOME=/home   ＃家目录\n     4  INACTIVE=-1  ＃密码有效期，-1永不过期\n     5  EXPIRE=      ＃帐号过期时间\n     6  SHELL=/bin/bash  //sheel\n     7  SKEL=/etc/skel  ＃初始化登录文件\n     8  CREATE_MAIL_SPOOL=yes  #默认建立一个邮件帐号\n\n\n[root@localhost ~]# grep -v \"^#\" /etc/login.defs | grep -v \"^$\"\n//添加用户默认密码配置\n\t1  MAIL_DIR        /var/spool/mail\n\t2  PASS_MAX_DAYS   99999  //密码最大有效天数\n\t3  PASS_MIN_DAYS   0      //修改密码最小间隔天数\n\t4  PASS_MIN_LEN    5      //密码最小长度\n\t5  PASS_WARN_AGE   7     //密码过期警告有效天数\n\t6  UID_MIN                   500  //uid最小值\n\t7  UID_MAX                 60000  //uid最大值\n\t8  GID_MIN                   500  //gid最小值\n\t9  GID_MAX                 60000  // gid最大值\n\t10 CREATE_HOME     yes        //建立用户时是否建家目录\n\t11 UMASK           077        //用户家目录权限\n\t12 USERGROUPS_ENAB yes        //删除用户时把初始组删除\n\t13 MD5_CRYPT_ENAB yes         //用户密码的加密方式\n```\n\n#### 4.修改用户密码\n ```bash\n #passwd   //更改当前登录的用户密码\n #passwd [选项] 用户名  //更改username的密码\n  -d : 清除用户密码\n  -l : 锁密码 \n  -u : 解锁密码\n  ```\n 密码存放文件:/etc/shadow\n\n ```bash\n [root@localhost ~]# grep \"root\" /etc/shadow\n ```\n    root  :$1$0T9ZqyFK$W9HPAfeRqhjnKLi1gWgRv/:  15824(1970-01-01)  : 0 : 99999 : 7 : : :\n    用户名:密码:最后一次修改密码日期:修改密码最小间隔时间:密码最大有效天数:密码过期前的警告天数:密码过期后的宽限时间:帐号的失效日期:保留位\n```bash\n #chage [options] user  === #passwd [options] user\n   Options:\n  -d, --lastday LAST_DAY        set last password change to LAST_DAY\n  -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE\n  -h, --help                    display this help message and exit\n  -I, --inactive INACTIVE       set password inactive after expiration\n                                to INACTIVE\n  -l, --list                    show account aging information\n  -m, --mindays MIN_DAYS        set minimum number of days before password\n                                change to MIN_DAYS\n  -M, --maxdays MAX_DAYS        set maximim number of days before password\n                                change to MAX_DAYS\n  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS\n  ```\n 例: 1.强制用户首次登录时修改密码\n    ```bash\n     [root@localhost ~]# chage -d 0 test5\n     [root@localhost ~]# grep \"test5\" /etc/shadow\n     test5:$1$RgYHNFnU$fPIRey.h68qPM1jXVB9761:0:0:99999:7:::\n     ```\n\n  /etc/shadow :存放用户组密码文件\n  ```bash\n  [root@localhost ~]# grep \"test8\" /etc/gshadow\n  ```\n  test8    :     !      :              :\n  用户组名 : 用户组密码 : 用户组管理员 : 组成员列表         \n\n\n  #gpasswd 用户组名  //修改用户组密码\n  ```bash\n   [root@localhost ~]# gpasswd test8\n      Changing the password for group test8\n      New Password: \n      Re-enter new password: \n   [root@localhost ~]# grep \"test8\" /etc/gshadow\n     test8:$1$x/4tD/bt$0l6vM3jnXqPGP0/xhS6cC/::\n   [root@localhost ~]# gpasswd -M test1,test2 test8 //把test1,test2添加到test8组中，当前的用户覆盖之前的\n          -a:追加方式添加组成员,一次只能添加一个\n          -A:添加组管理员\n          -d:删除组成员\n  \n  [root@localhost ~]# gpasswd -A test1 test8  //往test8组中添加组管理员test1\n  [root@localhost ~]# grep \"^test\" /etc/gshadow\n   test:!::\n   test1:!::\n   test4:!::\n   test5:!::\n   test8:$1$x/4tD/bt$0l6vM3jnXqPGP0/xhS6cC/:test1:test1,test2,test3,test4\n  \n\n   [root@localhost ~]# gpasswd -A test test8   //非组成员添加为组管理员\n                                                ?怎么删除非组成员的管理员\n   [root@localhost ~]# grep \"^test\" /etc/gshadow\n    test:!::\n    test1:!::\n    test4:!::\n    test5:!::\n    test8:$1$x/4tD/bt$0l6vM3jnXqPGP0/xhS6cC/:test:test1,test2,test3,test4\n```\n    #groupdel 组名 //删除用户组 ,不能删除用户的主组\n   \n\n#### 5.修改文件夹的所有者和所属组\n```bash\n    #chown  所有者名字:所属组名字\n  [root@localhost ~]# chown test1:test5 /home/test/test.txt \n  [root@localhost ~]# ll /home/test/test.txt \n  -rw-rw-r-- 1 test1 test5 0 May  5 15:48 /home/test/test.txt\n   \n   [root@localhost ~]# chown test2 /home/test/test.txt  //单独修改所有者\n   [root@localhost ~]# ll /home/test/test.txt \n   -rw-rw-r-- 1 test2 test5 0 May  5 15:48 /home/test/test.txt\n\n   [root@localhost ~]# chown :teacher /home/test/test.txt   //单独修改所属组\n   [root@localhost ~]# ll /home/test/test.txt \n   -rw-rw-r-- 1 test2 teacher 0 May  5 15:48 /home/test/test.txt\n\n   \n   #chgrp 所属组名 文件夹/文件 :改文件或目录的所属组\n   [root@localhost ~]# chgrp test1 /home/test/test.txt \n   [root@localhost ~]# ll /home/test/test.txt \n   -rw-rw-r-- 1 test2 test1 0 May  5 15:48 /home/test/test.txt\n\n  ```"
categories:
  - linux
toc: true
date: 2018-12-12 14:56:42
---

#### 1.用户管理
  1）添加
  ```bash
     #useradd/adduser [选项] 用户名
       -g 指定用户的初始组
       -G 指定用户的附加组
       -d 指定用户的家目录
       -c 设置用户的描述信息
       -s 指定用户使用的shell 默认是/bin/bash  查看shell:/etc/shells
       -M 不添加家目录
  ```
     
      #useradd test  不加选项时 按系统的默认配置添加帐号
      有如下特性：
       * 1.在/home目录下生成与用户同名的目录作为用户的家目录
       * 2.uid,gid是系统内当前用户的最大uid,gid号+1
       * 3.shell是 /bin/bash   /sbin/nologin:非交互式的shell
       * 4.会创建与用户同名的组作为用户的初始组
     
     注:用户名不能重名，不能使用特殊字符或关键字，区分大小写，具有描述性       
       添加用户时当指定的初始组不存在，无法添加用户
       添加用户指定初始组时，不会在创建一个与用户同名的组，并且创建的组是初始组，用户成员列表不会显示
  
  2）修改
    ```bash
      #usermod [选项]  用户名
      #passwd 用户名  //修改用户密码
    ```

  3）删除
     ```bash
      #userdel -r  用户名 
      //删除用户时把用户的家目录一起删除，不加-r则只删除用户，用户家目录还在。
      注:如果用户初始组为空，删除用户时把初始组也删除，如果初始组非空，则不删除初始组
     ```

  4）查看
  
    /etc/passwd:保存帐号信息文件
    root  :    x     :     0      :  0  :     root   : /root : /bin/bash
    用户名:密码占位符:用户编号uid : gid :用户描述信息: 家目录: shell
    uid范围:0-65535
    0   : root
    1-99:系统帐号
    100-499:服务帐号
    500+:普通帐号uid范围
     
   shell:工具，命令解释器
   查看shell:/etc/shells 

#### 2.用户组管理
    ```bash
      #groups //查看登录的用户属于哪些组，第一列为用户当前所在的组
           [root@localhost ~]# groups
            root bin daemon sys adm disk wheel
  
       #newgrp 组名  //切换组
            [root@localhost ~]# groups
            bin root  daemon sys adm disk wheel
  
       #exit //退回到上一个组

       #groups：查看用户组信息，单独使用时，是登录用户查看自己的用户信息
       #groups username:管理员查看指定用户的用户组信息
       #newgrp groupname:用户切换到指定的用户组里，用户exit退出当前所在的组

      /etc/group: 保存用户组信息文件

    [root@localhost ~]# head -1 /etc/group
   ```

  root    :       x        : 0 :     root
  用户组名:用户组密码占位符:gid:用户组成员列表
  注：如果某一个组是用户的初始组，用户名不会显示在组成员列表里

#### 3.配置文件
 /etc/defualt/useradd :添加用户使用的默认配置文件
 /etc/skel
:保存用户登录初始化配置文件,每次新建用户时都会把当前目录下的文件复制到用户的家目录
 /var/spool/mail:邮件存放目录
 
 ```bash
[root@localhost var]# cat -b /etc/default/useradd 
     1  # useradd defaults file
     2  GROUP=100    ＃用户初始组
     3  HOME=/home   ＃家目录
     4  INACTIVE=-1  ＃密码有效期，-1永不过期
     5  EXPIRE=      ＃帐号过期时间
     6  SHELL=/bin/bash  //sheel
     7  SKEL=/etc/skel  ＃初始化登录文件
     8  CREATE_MAIL_SPOOL=yes  #默认建立一个邮件帐号


[root@localhost ~]# grep -v "^#" /etc/login.defs | grep -v "^$"
//添加用户默认密码配置
	1  MAIL_DIR        /var/spool/mail
	2  PASS_MAX_DAYS   99999  //密码最大有效天数
	3  PASS_MIN_DAYS   0      //修改密码最小间隔天数
	4  PASS_MIN_LEN    5      //密码最小长度
	5  PASS_WARN_AGE   7     //密码过期警告有效天数
	6  UID_MIN                   500  //uid最小值
	7  UID_MAX                 60000  //uid最大值
	8  GID_MIN                   500  //gid最小值
	9  GID_MAX                 60000  // gid最大值
	10 CREATE_HOME     yes        //建立用户时是否建家目录
	11 UMASK           077        //用户家目录权限
	12 USERGROUPS_ENAB yes        //删除用户时把初始组删除
	13 MD5_CRYPT_ENAB yes         //用户密码的加密方式
```

#### 4.修改用户密码
 ```bash
 #passwd   //更改当前登录的用户密码
 #passwd [选项] 用户名  //更改username的密码
  -d : 清除用户密码
  -l : 锁密码 
  -u : 解锁密码
  ```
 密码存放文件:/etc/shadow

 ```bash
 [root@localhost ~]# grep "root" /etc/shadow
 ```
    root  :$1$0T9ZqyFK$W9HPAfeRqhjnKLi1gWgRv/:  15824(1970-01-01)  : 0 : 99999 : 7 : : :
    用户名:密码:最后一次修改密码日期:修改密码最小间隔时间:密码最大有效天数:密码过期前的警告天数:密码过期后的宽限时间:帐号的失效日期:保留位
```bash
 #chage [options] user  === #passwd [options] user
   Options:
  -d, --lastday LAST_DAY        set last password change to LAST_DAY
  -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -h, --help                    display this help message and exit
  -I, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -l, --list                    show account aging information
  -m, --mindays MIN_DAYS        set minimum number of days before password
                                change to MIN_DAYS
  -M, --maxdays MAX_DAYS        set maximim number of days before password
                                change to MAX_DAYS
  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS
  ```
 例: 1.强制用户首次登录时修改密码
    ```bash
     [root@localhost ~]# chage -d 0 test5
     [root@localhost ~]# grep "test5" /etc/shadow
     test5:$1$RgYHNFnU$fPIRey.h68qPM1jXVB9761:0:0:99999:7:::
     ```

  /etc/shadow :存放用户组密码文件
  ```bash
  [root@localhost ~]# grep "test8" /etc/gshadow
  ```
  test8    :     !      :              :
  用户组名 : 用户组密码 : 用户组管理员 : 组成员列表         


  #gpasswd 用户组名  //修改用户组密码
  ```bash
   [root@localhost ~]# gpasswd test8
      Changing the password for group test8
      New Password: 
      Re-enter new password: 
   [root@localhost ~]# grep "test8" /etc/gshadow
     test8:$1$x/4tD/bt$0l6vM3jnXqPGP0/xhS6cC/::
   [root@localhost ~]# gpasswd -M test1,test2 test8 //把test1,test2添加到test8组中，当前的用户覆盖之前的
          -a:追加方式添加组成员,一次只能添加一个
          -A:添加组管理员
          -d:删除组成员
  
  [root@localhost ~]# gpasswd -A test1 test8  //往test8组中添加组管理员test1
  [root@localhost ~]# grep "^test" /etc/gshadow
   test:!::
   test1:!::
   test4:!::
   test5:!::
   test8:$1$x/4tD/bt$0l6vM3jnXqPGP0/xhS6cC/:test1:test1,test2,test3,test4
  

   [root@localhost ~]# gpasswd -A test test8   //非组成员添加为组管理员
                                                ?怎么删除非组成员的管理员
   [root@localhost ~]# grep "^test" /etc/gshadow
    test:!::
    test1:!::
    test4:!::
    test5:!::
    test8:$1$x/4tD/bt$0l6vM3jnXqPGP0/xhS6cC/:test:test1,test2,test3,test4
```
    #groupdel 组名 //删除用户组 ,不能删除用户的主组
   

#### 5.修改文件夹的所有者和所属组
```bash
    #chown  所有者名字:所属组名字
  [root@localhost ~]# chown test1:test5 /home/test/test.txt 
  [root@localhost ~]# ll /home/test/test.txt 
  -rw-rw-r-- 1 test1 test5 0 May  5 15:48 /home/test/test.txt
   
   [root@localhost ~]# chown test2 /home/test/test.txt  //单独修改所有者
   [root@localhost ~]# ll /home/test/test.txt 
   -rw-rw-r-- 1 test2 test5 0 May  5 15:48 /home/test/test.txt

   [root@localhost ~]# chown :teacher /home/test/test.txt   //单独修改所属组
   [root@localhost ~]# ll /home/test/test.txt 
   -rw-rw-r-- 1 test2 teacher 0 May  5 15:48 /home/test/test.txt

   
   #chgrp 所属组名 文件夹/文件 :改文件或目录的所属组
   [root@localhost ~]# chgrp test1 /home/test/test.txt 
   [root@localhost ~]# ll /home/test/test.txt 
   -rw-rw-r-- 1 test2 test1 0 May  5 15:48 /home/test/test.txt

  ```