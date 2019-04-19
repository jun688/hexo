---
title: 磁盘阵列RAID
tags:
  - linux
originContent: ''
categories:
  - linux
toc: true
date: 2018-12-12 16:24:26
---

#### 1.磁盘阵列 raid (存储数据 备份数据)
   把多个单个的磁盘从逻辑上组成一个大磁盘，进行存储数据和备份数据。
   * 1.1 硬raid
        
   * 1.2 软raid
        1） 用软件实现raid功能
        2） mdadm-2.6.9-3.el5.rpm
            #which mdadm
            #rpm -qa /sbin/mdadm
       3）根据制作raid设备时使用级别的不同，功能也不同
       4）级别：
           raid0:至少需要2块单独的物理磁盘（容量，接口，转速相等）
                 /dev/sda /dev/sdb raid0 /dev/md0
                 /dev/sdc /dev/sdd raid0 /deb/md1
                 交替存储，读写速度快，完整性差。

           raid1:至少需要2块单独的物理磁盘（容量，接口，转速相等）
                 一份数据存储两份，每份磁盘都存储一份，存储速度慢，完整性好。

           raid01: raid0+raid1
                   
           raid10: raid1+raid0
             
           raid5: 至少需要3块单独物理磁盘
                  要有单独的一块盘来记录写在其他盘上的数据信息
                   /dev/sda    /dev/sdb    /dev/sdc
                      1G         1G          1G
                  raid5 = /dev/md0=(3-1)*1G   //储存容量
            
            例：用/dev/hdd1-hdd4作级别是raid5的阵列设备，备用设备hdd4,
                要开机挂载此设备，挂载到/md0目录里，让所有用户都可以向
                /md0目录下存数据，但u1用户只能存500M数据。
                1.分区 /dev/hdd1 /dev/hdd2 /dev/hdd3 /dev/hdd4
                2.修改标识
                  l:查看标识
                  t:修改标识为fd
                3.#partprobe  //更新分区表
                4.#mdadm -C /dev/md0 -l5 -n3 --spare-devices=1 /dev/hdd1 /dev/hdd2 /dev/hdd3 /dev/hdd4 
                 #mdadm -C /dev/md0 -l级别 -n磁盘数 --sparce-dervices=备用盘个数 /dev/hdd{1,2,3,4}
 
                   -C:创建raid设备
                   -l:raid级别
                   -n:raid设备磁盘数
                   --spare-devices = 1 :1块备用
                    
                 5.#cat /proc/mdstat    //查看磁盘阵列是否做完
                  
                 6.#mkfs.ext3 /dev/md0   //格式化
                  
                 7.#mount /dev/md0 /newmd0/ //挂载 

                 8.#mdadm -Ds > /etc/mdadm.conf   //扫描raid设备
                 
                 9.#cat /proc/mdstat  //查看raid设备状态 
                  
                 10.#mdadm -S /dev/md0 //停止raid设备

                 11.#mdadm -As /dev/md0  //启动raid设备                   



#### 2. PV
   * 2.1分区
      创建逻辑卷，在逻辑卷分区，改标识t->8e   
   * 2.2查看pv设备
      #pvscan    //查看当前是否有PV设备
   * 2.3创建pv
         #pvcreate /dev/sda5 /dev/sda5
         #pvcreate /dev/sda{5,6}
   * 2.4 查看pv详细信息
       #pvdisplay /dev/sda5
   * 2.5删除pv
      #pvremove /dev/sda6
  
#### 3. VG
    * 3.1 创建VG
      #vgcreate myvg /dev/sda5 /dev/sda6 //建myvg卷组，把pv放进来
    * 3.2 查看vg设备
      #vgscan
    * 3.3 查看vg详细信息
      #vgdisplay myvg
    * 3.4 从vg里移除pv
      #vgreduce myvg /dev/sda6 //把指定的pv从vg中移除
    * 3.5 往vg里加pv
      #vgextend myvg /dev/sda6 //把pv追加到已有的卷组里
    * 3.6 删除vg
      #vgremoce myvg
#### 4.LV  LV容量从现有的VG里来
    * 4.1 查看LV设备
      #lvscan
    * 4.2 创建
      #lvcreate -n lv1 -l 25 myvg  //用指定PE的个数来创建lv 
         -l:用PE个数来指定 25*4M
      #lvcreate -n lv2 -L 500M  myvg  //使用容量来创建lv
         -L:指定容量
    * 4.3 查看lv详细信息
      #lvdisplay /dev/myvg/lv1
    * 4.4 删除lv
      #lvremove /dev/myvg/lv2
    * 4.5 格式化
      #mkfs.ext3 /dev/myvg/lv2 
    * 4.6 在线扩展lv
      #lvresize -l +25 /dev/myvg/lv1 //把lv增大25PE
    * 4.7 更新lv
      #resize2fs  /dev/myvg/lv1
    * 4.8 缩小lv
        #umount /lv1
        #resize2fs /dev/myvg/lv1 100M //把lv1缩小到100M
        #e2fsck -f /dev/myvg/lv1
   * 4.9 把lv去掉25M 
      #lvresize -l -25 /dev/myvg/lv1
       
#### 5. LV快照: 
     * 5.1 创建快照
        #lvcreate -n lv2s -l 25 -s /dev/vg2/lv2 
     * 5.2 查看快照信息
       #lvs
     * 5.3移除快照
        #lvremove /dev/vg2/lv2s
>    注：  可以挂载快照设备，也可以挂载源lv
         在挂载源lv设备时，只要源数据不发生改变，快照设备和源lv设备共享所有PE,
         当源设备里的数据发生改变时，会把数据的原始内容搬到快照空间里。
         挂载快照设备时，源数据保存在源LV设备里，我所有的操作都在快照空间里执行，
         只要数据不超过快照空间的大小就可以，一旦超过快照失效。