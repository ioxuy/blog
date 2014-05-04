title: 折腾Kali Burg 笔记
date: 2014-05-04 00:07:51
tags: [Kali,Linux,]
categories: Linux
toc: false

---

　　今天给没事折腾了下Burg，实在是看烦了GRUB2了。把过程记录下，免得以后到处找资料。  
　　有两种方法：　
  
---
##第一种  
1. 添加源
```sh  
deb http://ppa.launchpad.net/bean123ch/burg/ubuntu lucid main
deb-src http://ppa.launchpad.net/bean123ch/burg/ubuntu lucid main
deb http://ppa.launchpad.net/ingalex/super-boot-manager/ubuntu oneiric main
```  
2. 不能忘的  

    apt-get udpate  
3. 要添加公钥  
```sh
gpg --keyserver keyserver.ubuntu.com --recv 55708F1EE06803C //下载密钥  
gpg --export --armor 55708F1EE06803C5 | sudo apt-key add-  //添加  
```  
4. 安装brug  

    apt-get install burg burg-themes burg-emu   
5. 检测系统生成配置文件  

    burg-install /dev/sda  
6. 更新  

    update-burg  
#####可以在`/etc/default/brug`里面把`GRUB_DISABLE_LINUX_RECOVERY="true"`前面的`#`去掉了在更新。就没有救援模式了  
7. 如果又不想用了，卸载办法  

    apt-get remove --purge burg burg-themes burg-emu  
这是最简单的办法了，第二种要麻烦些，我用的是第二种办法。我要掉开机启动的时候"GRUB Loading"的字样删除掉，那就只有编译安装了  

---  
###第二种  

1.编译环境准备
```sh
 apt-get install autoconf automake bison flex make gcc ruby python gettext libfreetype6-dev  
 apt-get insatll bzr
```  
2.获取源码  

    bzr branch lp:burg  
3.删除“GRUB Loading”字样  
a. 先来个简单的把内核加载提示`Loading, please wait...`删除掉  
它在`/usr/share/initramfs-tools/init`注释掉即可  
b. 删除字样，位置在下载的Burg源文件里面  
```sh
          "./boot/i386/pc/boot.S"   行  382:
           原来-> notification_string: .asciz "GRUB "
           改为 -> notification_string: .asciz ""

           "./boot/i386/pc/diskboot.S" 行 323:
           原来-> notification_string: .asciz "loading"
           改为 -> notification_string: .asciz ""

           "./boot/i386/pc/diskboot.S" 行 325:
           原来 -> notification_step: .asciz "."
           改为 -> notification_step: .asciz ""

           "./boot/i386/pc/diskboot.S" 行 326:
           原来 -> notification_done: .asciz "\r\n"
           改为 -> notification_done: .asciz ""

           "./boot/sprc64/ieee1275/boot.S" 行 71:
           原来->grub_name:.asciz "GRUB "
           改为->grub_name:.asciz ""

           "./boot/sprc64/ieee1275/diskboot.S"行 37:
           原来->notification_string:.asciz "Loading kernel"
           改为->notification_string:.asciz ""

           "./boot/sprc64/ieee1275/diskboot.S" 行 40:
           原来->notification_step:.asciz "."
           改为->notification_step:.asciz ""

           "./boot/sprc64/ieee1275/diskboot.S" 行 43:
           原来->notification_done:.asciz "\r\n"
           改为->notification_done:.asciz ""  
```
##这个办法也可以用来源码编译Grub2##
4.编译安装  
```sh
cd burg
./autogen.sh
mkdir $HOME/burg_pc ##存放编译文件目录
cd $HOME/burg_pc
$HOME/burg/configure --with-platform=pc --prefix=$HOME/burg_install --disable-werror  ##burg_install 是安装目录  $HOME/burg 是源文件目录 --disable-werror 我这里是必须加上
make
make install
$HOME/burg_install/sbin/burg-install /dev/sda
$HOME/burg_install/sbin/burg-mkconfig -o /boot/burg/burg.cfg
```  
基本完成了，这样是没有主题的，下面下载主题安装  
5.下载主题  
[主题下载](https://code.google.com/p/burg/downloads/detail?name=burg-themes_20100607.zip&can=2&q=)  
解压到`/boot/burg/` 
6.建立配置文件  
`touch $HOME/burg_install/etc/default/burg`，在把下面例子拷贝进去，酌情更改下就OK了，别忘了更改后`$HOME/burg_install/sbin/burg-mkconfig -o /boot/burg/burg.cfg`在执行一遍。例子：  
```sh
# If you change this file, run 'burg-mkconfig -o /boot/burg/burg.cfg' afterwards to update
# /boot/burg/burg.cfg.

GRUB_DEFAULT=0
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""

# Uncomment to disable graphical terminal (grub-pc only)
#GRUB_TERMINAL=console

# If you want to enable the save default function, uncomment the following
# line, and set GRUB_DEFAULT to saved.
#GRUB_SAVEDEFAULT=true

# The resolution used on graphical terminal
# note that you can use only modes which your graphic card supports via VBE
# you can see them in real GRUB with the command `vbeinfo'
# In the boot menu, use hotkey 'r' to popup a resolution selection menu.
GRUB_GFXMODE=saved

# Uncomment if you don't want GRUB to pass "root=UUID=xxx" parameter to Linux
#GRUB_DISABLE_LINUX_UUID=true

# Uncomment to disable generation of recovery mode menu entries
GRUB_DISABLE_LINUX_RECOVERY="true"

# Uncomment to get a beep at grub start
#GRUB_INIT_TUNE="480 440 1"

# GRUB_THEME's value can be 'saved' or a specific BURG theme name, you can also
# set it to the pathname of a GRUB2 theme file.
# In the boot menu, use hotkey 't' to popup a theme selection menu
GRUB_THEME=ubuntu

# GRUB_FOLD's value can be 'saved', 'true' or 'false'.
# In the boot menu, use hotkey 'F7' to show the full list, 'f' to toggle
# between folding modes.
GRUB_FOLD=saved

# Add user with burg-adduser, then use GRUB_USERS to config authentication.
# The following example means user1 can boot Ubuntu, no password is needed to
# boot Windows, user1 amd user2 can boot other OS. Superusers can boot any OS
# and use hotkeys like `c' to enter console mode.
#GRUB_USERS="*=user1,user2:ubuntu=user1:windows="

# For a complete list of supported variables, refer to this wiki page:
# http://code.google.com/p/burg/wiki/ConfigurationVariables
```  
###参考  
[ How to compile and install BURG using source code ](https://code.google.com/p/burg/wiki/ManualInstall)  
[深入理解 GNU GRUB - 02 boot.S 2.4 boot.S详细注释](http://blog.csdn.net/cppgp/article/details/6361020)  
[Ubuntu小私房(3)--Uubutnu启动美化大变身](http://incyanggan.iteye.com/blog/1918399)  
[教你如何编译安装grub2做一个纯粹的quiet boot](http://hi.baidu.com/sxsloin/item/df94fdd526dbd01821e2501c)