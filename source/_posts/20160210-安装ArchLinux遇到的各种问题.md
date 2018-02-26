---
title: 安装ArchLinux遇到的各种问题
date: 2016-02-10 20:10:33
categories: 系统运维
tags: Linux
---
![](http://olvboulzy.bkt.clouddn.com/ArchLinuxproblems1.png?imageView2/0/q/75|watermark/2/text/aHR0cDovL2Jpbmx2LnRvcA==/font/YXJpYWw=/fontsize/260/fill/I0VGRUZFRg==/dissolve/100/gravity/South/dx/10/dy/10)
## 废话一下
不知为何，踏入计算机领域以来最兴奋的就是装系统了==。传说会装ArchLinux的才是真的会装系统，怀着好奇心开始了Arch的安装。。。不得不说确实麻烦点，但好在有那么多具有奉献精神的程序员们，Arch拥有丰富的wiki指南，遇到问题去wiki看看一般都有答案了。这里我仅记录下自己在安装过程遇到的问题和我的解决办法，防止忘记，OK

<!--more-->

## 参考文档：

*   [中文wiki](http://bbs.archlinuxcn.org/viewtopic.php?id=1037)


### 用U盘刻录后开机无法进入安装

官方下载的镜像肯定没问题，我曾尝试用原来刻windows用的软碟通，刻录是完成了可就是进不去U盘，引导文件也有，无语

#### 解决办法：

使用USBWrite再次写入即可安装。忘记对比和软碟通做的有什么区别，哪位如果尝试的话希望留言

### 引导GRUB双系统无法完成

UEFI模式下 Linux会在ESP（启动分区）加入vmlinuz等，是Linux启动的微内核…，开始我以为这东西没用，就全删了，结果蛋疼折腾一晚上。。。。
每次在grub-install写入grub.cfg时报错
情况1：”Failed to connect to lvmetad: No such file or directory.  Falling back to internal scanning. “

#### 解决办法：

``` bash
Pacman -S device-mapper linux lvm2
```
重新安装相关启动内核，参考[wiki](https://bbs.archlinux.org/viewtopic.php?id=159415)

### 写grub.cfg

参考[wiki](https://wiki.archlinux.org/index.php/GRUB_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29#.E5.85.B6.E4.BB.96.E7.B3.BB.E7.BB.9F.E4.B8.8D.E8.83.BD.E8.87.AA.E5.8A.A8.E.8F.91.E7.8E.B0Arch_Linux)
这个折腾了我比较久，因为我是Win8.1和Arch双系统，Arch最大特点就是可定制化，说白了就是基本都要自己干，以前装Ubuntu的时候，双系统可以自动引导，Arch就。。。恩，要有耐心
手动创建过程如下：（如果grub-install一直报错建议手动添加，还能增加理解）
基本的GRUB配置文件使用如下选项:
(hdX,Y) 是X磁盘的Y分区,分区从1开始计数,磁盘从0开始计数.
set default=N设定用户选择超时时间过后的默认启动项
set timeout=M设定用户选择超时时间(秒).
menuentry “title” {entry options}设置一个名为title的启动项
set root=(hdX,Y)设定启动分区(kernel和GRUB模组所在磁盘),/boot没被要求独占一个分区,有可能就是root分区下的一个文件夹
示例配置如下:

/boot/grub/grub.cfg
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span>
</pre></td><td class="code"><pre><span class="line"># Config file for GRUB - The GNU GRand Unified Bootloader&#10;# /boot/grub/grub.cfg&#10;&#10;# DEVICE NAME CONVERSIONS&#10;#&#10;#  Linux           Grub&#10;# -------------------------&#10;#  /dev/fd0        (fd0)&#10;#  /dev/sda        (hd0)&#10;#  /dev/sdb2       (hd1,2)&#10;#  /dev/sda3       (hd0,3)&#10;#&#10;&#10;# Timeout for menu&#10;set timeout=5&#10;&#10;# Set default boot entry as Entry 0&#10;set default=0&#10;&#10;# (0) Arch Linux&#10;menuentry &#34;Arch Linux&#34; &#123;&#10;  set root=(hd0,1)&#10;  linux /vmlinuz-linux root=/dev/sda3 ro&#10;  initrd /initramfs-linux.img&#10;&#125;&#10;&#10;## (1) Windows&#10;#menuentry &#34;Windows&#34; &#123;&#10;#  set root=(hd0,3)&#10;#  chainloader +1&#10;#&#125;</span>
</pre></td></tr></table></figure>


### 解决字体问题

网页中文字体模糊 安装什么泉字体即可 
``` bash
yaourt -S
```
WPS缺少字体symbol等，下载Ubuntu的字体包.deb，然后解压出来找到字体文件cp -r到/usr/share/fonts/下即可

### 主题问题

gonme的主题可以去gonme-look.org下载，然后cp -r 到 /usr/share/themes/下然后快捷键 alt+F2输入r再回车即可通过gonme-tools更换

### 安装软件

发现软件管理器的一个安装脚本PK。。。老是报错，也是醉了

### gonme shell extension

插件用火狐浏览器可以直接装，两个字 方便！！

### 联网问题

设置Networkmanager时需注意它和wifi-menu之间配置会冲突，容易引起开机时wifi无法连接问题，只能取其一将其中一个开机启动禁用即可。
``` bash
systemctl disable xxx.service...
```

### 字体显示问题

好像是我更新的时候挂了，就像这样![](http://img.blog.csdn.net/20170309143059043?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDM2MTM1Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



#### 解决办法：

一般字体出问题无法显示都可以用这个方法----恢复到未打补丁的包
To restore the unpatched packages, reinstall the originals:
``` bash
pacman -S --asdeps freetype2 libxft cairo fontconfig
```