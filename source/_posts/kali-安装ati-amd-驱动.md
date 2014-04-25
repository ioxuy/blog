title: Kali 安装ATI AMD 驱动
date: 2014-04-25 16:01:51
tags: [Kali,ATI,Linux]
toc: false
---


　　今天有空了,来写下Kali 1.0.6 安装ATI驱动的方法,我是使用的Lenovo G470的本本,安装Kali后是使用的Intel 的核显。我是有强迫症的人当然要折腾了，下面看方法：  
　　**注：我是安装的64位的版本，以下都是在此基础上的**  
先看下版本  
```
uname -a
```
Output:  
```
Linux Redi0 3.12-kali1-amd64 #1 SMP Debian 3.12.6-2kali1 (2014-01-06) x86_64 GNU/Linux

```    
##Setp 1 添加源  
```
 cp /etc/apt/sources.list /etc/apt/sources.list.bak  //备份是个好习惯  
 leafpad /etc/apt/sources.list
```  
* deb http://mirrors.neusoft.edu.cn/kali/ kali main non-free contrib
* deb-src http://mirrors.neusoft.edu.cn/kali/ kali main non-free contrib
* deb http://mirrors.neusoft.edu.cn/kali-security kali/updates main contrib non-free  


```
apt-get update
```  
##Setp 2 安装库  
```
apt-get install firmware-linux-nonfree 
apt-get install amd-opencl-icd 
apt-get install linux-headers-$(uname -r)
```  
##Setp 3 安装fglrx  
```
apt-get install fglrx-atieventsd fglrx-driver fglrx-control fglrx-modules-dkms -y
```  
出来弹窗Yes or 确定 就行了  
**测试**  
```
fglrxinfo 
fgl_glxgears
```  
Output  
```
root@Redi0:~# fglrxinfo 
display: :0.0  screen: 0
OpenGL vendor string: Advanced Micro Devices, Inc.
OpenGL renderer string: AMD Radeon HD 6300M Series
OpenGL version string: 4.3.12618 Compatibility Profile Context 13.251

```  

![fgl_glxgears][1]


如果没有问题就可以生成xorg.conf文件。  
```
aticonfig --initial -f
```  
##Setp 4 更新Grub.conf  
```
vi /boot/grub/grub.conf
```  
![ /boot/grub/grub.conf][2]


添加 radeon.modeset=0  
就你这样:
linux   /boot/vmlinuz-3.12-kali1-amd64 root=UUID=259f3a55-4068-4177-b6f8-09e656c135fe ro  quiet radeon.modeset=0  
UUID都是不一样的不用管  
##Setp 5 重启  
```
reboot
```  
重启后  
```
amdcccle
```  
![amdcccle][3]


应用程序>系统工具>首选项 也能找到  
####参考  
[Install AMD ATI proprietary driver (fglrx) in Kali Linux 1.0.6 running Kernel version 3.12.6 ][4]


  [1]: http://ww4.sinaimg.cn/large/8ea95026gw1efrujxfgd6j20qn0dpq5e.jpg
  [2]: http://ww4.sinaimg.cn/large/8ea95026gw1efruz3b7zdj20sx0drtd8.jpg
  [3]: http://ww4.sinaimg.cn/large/8ea95026gw1efrvcwq0joj20te0datbx.jpg
  [4]: http://www.blackmoreops.com/2014/03/05/install-amd-ati-proprietary-fglrx-driver-kali-linux-1-0-6/