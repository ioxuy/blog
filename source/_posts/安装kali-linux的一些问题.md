title: 安装Kali Linux的一些问题
date: 2014-04-21 18:10:38
tags: [Kali,Linux]
author: Redi0
---
##Detecting Network Hardware
------
  在安装Kali的时候出现这个错误
  用Ese回到Debain Installer main menu选择Execute a shell开个Shell用Nano编辑
  ```sh
  nano /bin/check-missing-fireware
  ```
  在#！/bin/bash下面添加exit 0  
  > \#!/bin/sh  
  > exit 0  
  > set -e  
  > . /usr/share/debconf/confmodule  
  > ..............  
    
    
  - 也可以试着在启动的时候在加启动参数  
  
```sh  
 netcfg/enable=false
```  
  #### 当然我这样是失败了，请看下面。如果上面成功的就忽略吧  
----------

## "Grub package failed to install into /targe.Without Grub boot loader,the installed system will not boot"
--------------
   我这样失败过后重新回到启动界面选择**Live (amd64)**启动  
* 选择左上角**Application** > **System Tools** > **Install Kali Linux**  
* 安装过程就不说了,参考[Kali Linux官方文档](http://cn.docs.kali.org/category/installing-kali-linux-cn)，当Copy data disk过后就会出现上面的**Grub package failed to install into /targe.Without Grub boot loader,the installed system will not boo**错误.  
* 这里要用**Esc键**回到Debian installer main menu找到**continue without boot loader**跳过安装Grub完成系统的安装.  
* 完成后暂时不要重启**Alt + F1**这是应用程序菜单找到**Accessories** > **Terminal**  
```sh
mount -t ext4 /dev/sda8 /mnt  **sda8是/分区所在
mount --bind /proc /mnt/proc
mount --bind /dev /mnt/dev
mount --bind /sys /mnt/sys
chroot /mnt  /bin/bash
```
###添加源  
```sh
nano /etc/apt/sources.list
```  
这是国内源  
- deb http://ftp.sjtu.edu.cn/debian wheezy main non-free contrib  
- deb-src http://ftp.sjtu.edu.cn/debian wheezy main non-free contrib  
- deb http://ftp.sjtu.edu.cn/debian wheezy-proposed-updates main non-free contrib  
- deb-src http://ftp.sjtu.edu.cn/debian wheezy-proposed-updates main non-free contrib  
- deb http://ftp.sjtu.edu.cn/debian-security wheezy/updates main non-free contrib  
- deb-src http://ftp.sjtu.edu.cn/debian-security wheezy/updates main non-free contrib  
- deb http://mirrors.163.com/debian wheezy main non-free contrib  
- deb-src http://mirrors.163.com/debian wheezy main non-free contrib  
- deb http://mirrors.163.com/debian wheezy-proposed-updates main non-free contrib  
- deb-src http://mirrors.163.com/debian wheezy-proposed-updates main non-free contrib  
- deb-src http://mirrors.163.com/debian-security wheezy/updates main non-free contrib  
- deb http://mirrors.163.com/debian-security wheezy/updates main non-free contrib  
  

```sh
apt-get update
apt-get install grub-pc
```  
####OK 这样就算好了！！！
