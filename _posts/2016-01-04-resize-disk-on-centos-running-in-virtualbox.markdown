---
layout: post
title: 增加运行在虚拟机中打CentOS的磁盘空间 
date: 2016-01-04
comments: false
tags: os
archive: false
---
最近，运行在VirtualBox中的CentOS在编译kudu代码时，报无可用空间错误，之前分派的40G硬盘空间不够用了，于是就需要增大其磁盘空间。

虚拟机中安装的CentOS采用LVM进行磁盘管理，所以我在虚拟机中增加一块盘片，挂载到系统中；

> 对磁盘进行分区 ： ``fdisk /dev/sdc``
> 格式化加入的磁盘 ：`` mkfs.ext4 /dev/sdc1``
> 创建逻辑卷 ： ``pvcreate /dev/sdc1``
> 将逻辑卷加入到vg_q组中 ： ``vgextend vg_q /dev/sdc1``
> 把新磁盘打所有空间投射到改组 ： ``lvresize -l +100%FREE /dev/mapper/vg_q-lv_root``
> 重新做一下系统 ： ``resize2fs /dev/mapper/vg_q-lv_root``


搞定！使用``df -hl``命令查看，空间已经增大！
