---
title: RAC学习记录
date: 2017-05-23 15:00:00
tags: [oracle,RAC,cluster]
categories: 数据库
---
# RAC理论基础
在RAC术语中，主机通常叫做“节点”。
每个主机的硬件配置应该一样：
 - 每个主机至少需要两块网卡，一个用于集群内部私有通信---Private NIC，另一个网卡用于对外提供服务，如提供用户数据查询---公共网卡（Public NIC）；
 - 每个主机还要有一个HBA卡，通过光纤连接到存储设备
 - 除公共和私有IP，每个节点还有VIP（Virtual IP），宕机时使用
     共享存储
 - 多实例，单一数据 架构
 - 所有节点共享一个数据库
 - 数据文件、联机日志、参数文件、控制文件必须存放在共享存储上
 - 保证每个节点都能访问这个存储设备
     <!--more-->

# 安装步骤
## 服务器准备
- 操作系统及补丁包
  要求：OEL 使用 ASMlib、其他Linux使用udev
      内存：2G以上、SWAP 4G 、硬盘 30G以上
- 创建所需组、用户和软件home目录
   oracle - oinstall dba
    grid   - asmadmin
- 若部署GNS，需提前设置
- 设置存储空间

## 安装Oracle Grid Infrastructure
- 包括Oracle Clusterware和Oracle ASM
  11gR2 整合了Clusterware

ASM安装是个重点
如使用OEL系统，自带asmlib但还是要安装asm-support（系统光盘中有）
如使用非OEL系统，有两种解决方案：
1. 安装对应内核的asmlib和asm-support，一般只适合较老版本Linux（Oracle对非自家Linux不提供支持，oracleasm最新支持到oracleasm-2.6.18-238.9.1.el5）
2. 使用linux自带设备管理器udev，其中又有两种情形：
  2.1 6.0版本以前的可以用scsi_id -g -u -s


1.确认在所有RAC节点上已经安装了必要的UDEV包
```shell
[root@rh2 ~]# rpm -qa|grep udev
udev-095-14.21.el5
```
2.通过scsi_id获取设备的块设备的唯一标识名，假设系统上已有LUN sdc-sdp
```shell
for i in c d e f g h i j k l m n o p ;
do
echo "sd$i" "`scsi_id -g -u -s /block/sd$i` ";
done
```

sdc 1IET_00010001
sdd 1IET_00010002
sde 1IET_00010003
sdf 1IET_00010004
sdg 1IET_00010005
sdh 1IET_00010006
sdi 1IET_00010007
sdj 1IET_00010008
sdk 1IET_00010009
sdl 1IET_0001000a
sdm 1IET_0001000b
sdn 1IET_0001000c
sdo 1IET_0001000d
sdp 1IET_0001000e 

以上列出于块设备名对应的唯一标识名

3.创建必要的UDEV配置文件，

首先切换到配置文件目录
```shell
[root@rh2 ~]# cd /etc/udev/rules.d
```
定义必要的规则配置文件
```shell
[root@rh2 rules.d]# touch 99-oracle-asmdevices.rules 
[root@rh2 rules.d]# cat 99-oracle-asmdevices.rules
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010001", NAME="ocr1", OWNER="grid", GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010002", NAME="ocr2", OWNER="grid", GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010003", NAME="asm-disk1",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010004", NAME="asm-disk2",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010005", NAME="asm-disk3",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010006", NAME="asm-disk4",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010007", NAME="asm-disk5",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010008", NAME="asm-disk6",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_00010009", NAME="asm-disk7",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_0001000a", NAME="asm-disk8",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_0001000b", NAME="asm-disk9",  OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_0001000c", NAME="asm-disk10", OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_0001000d", NAME="asm-disk11", OWNER="grid",  GROUP="asmadmin", MODE="0660"
KERNEL=="sd*", BUS=="scsi", PROGRAM=="/sbin/scsi_id -g -u -s %p", RESULT=="1IET_0001000e", NAME="asm-disk12", OWNER="grid",  GROUP="asmadmin", MODE="0660"
```
Result 为/sbin/scsi_id -g -u -s %p的输出--Match the returned string of the last PROGRAM call. This key may be
used in any following rule after a PROGRAM call.
按顺序填入刚才获取的唯一标识名即可

OWNER为安装Grid Infrastructure的用户，在11gr2中一般为grid，GROUP为asmadmin
MODE采用0660即可

NAME为UDEV映射后的设备名，
建议为OCR和VOTE DISK创建独立的DISKGROUP，为了容易区分将该DISKGROUP专用的设备命名为ocr1..ocrn的形式
其余磁盘可以根据其实际用途或磁盘组名来命名

4. 将该规则文件拷贝到其他节点上
```shell
[root@rh2 rules.d]# scp 99-oracle-asmdevices.rules Other_node:/etc/udev/rules.d
```
5.在所有节点上启动udev服务，或者重启服务器即可
```shell
[root@rh2 rules.d]# /sbin/udevcontrol reload_rules
[root@rh2 rules.d]# /sbin/start_udev
Starting udev:                                            [  OK  ]
```
6.检查设备是否到位
```shell
[root@rh2 rules.d]# cd /dev
[root@rh2 dev]# ls -l ocr*
brw-rw---- 1 grid asmadmin 8, 32 Jul 10 17:31 ocr1
brw-rw---- 1 grid asmadmin 8, 48 Jul 10 17:31 ocr2

[root@rh2 dev]# ls -l asm-disk*
brw-rw---- 1 grid asmadmin 8,  64 Jul 10 17:31 asm-disk1
brw-rw---- 1 grid asmadmin 8, 208 Jul 10 17:31 asm-disk10
brw-rw---- 1 grid asmadmin 8, 224 Jul 10 17:31 asm-disk11
brw-rw---- 1 grid asmadmin 8, 240 Jul 10 17:31 asm-disk12
brw-rw---- 1 grid asmadmin 8,  80 Jul 10 17:31 asm-disk2
brw-rw---- 1 grid asmadmin 8,  96 Jul 10 17:31 asm-disk3
brw-rw---- 1 grid asmadmin 8, 112 Jul 10 17:31 asm-disk4
brw-rw---- 1 grid asmadmin 8, 128 Jul 10 17:31 asm-disk5
brw-rw---- 1 grid asmadmin 8, 144 Jul 10 17:31 asm-disk6
brw-rw---- 1 grid asmadmin 8, 160 Jul 10 17:31 asm-disk7
brw-rw---- 1 grid asmadmin 8, 176 Jul 10 17:31 asm-disk8
brw-rw---- 1 grid asmadmin 8, 192 Jul 10 17:31 asm-disk9
```

  2.2 6.0版本以后的-g失效,需使用

1.
```shell
#首先确认是 Linux 6.0以上版本
[root@vrh6 dev]# cat /etc/issue          
Oracle Linux Server release 6.2
Kernel \r on an \m
```
2.
```shell
#添加记录到/etc/scsi_id.config

echo "options=--whitelisted --replace-whitespace"  >> /etc/scsi_id.config
```
3. ​
```shell
#确认哪些块设备需要udev绑定

[root@vrh6 dev]# ls -l sd*
brw-rw----. 1 root disk 8,  0 Jun 30 09:29 sda
brw-rw----. 1 root disk 8,  1 Jun 30 09:29 sda1
brw-rw----. 1 root disk 8,  2 Jun 30 09:29 sda2
brw-rw----. 1 root disk 8, 16 Jun 30 09:29 sdb
brw-rw----. 1 root disk 8, 32 Jun 30 09:29 sdc
brw-rw----. 1 root disk 8, 48 Jun 30 09:29 sdd
brw-rw----. 1 root disk 8, 64 Jun 30 09:29 sde
brw-rw----. 1 root disk 8, 80 Jun 30 09:29 sdf
```
例如在本实例中 sdb-> sdf的块设备需要绑定

4. 将 b->f的编号放入for 循环中，例如：
```shell
# AUTO UDEV RULE BY Maclean Liu 2012/06/30
for i in b c d e f ;
do
echo "KERNEL==\"sd*\", BUS==\"scsi\", PROGRAM==\"/sbin/scsi_id --whitelisted --replace-whitespace --device=/dev/\$name\", RESULT==\"`/sbin/scsi_id --whitelisted --replace-whitespace --device=/dev/sd$i`\", NAME=\"asm-disk$i\", OWNER=\"grid\", GROUP=\"asmadmin\", MODE=\"0660\""      
done
```
就会生成sdb->sdf 设备绑定的RULE，在将这些RULE写入到/etc/udev/rules.d/99-oracle-asmdevices.rules中

也可以直接利用以下脚本 ，写出RULE到99-oracle-asmdevices.rules
```shell
# AUTO UDEV RULE BY Maclean Liu 2012/06/30
for i in b c d e f ;
do
echo "KERNEL==\"sd*\", BUS==\"scsi\", PROGRAM==\"/sbin/scsi_id --whitelisted --replace-whitespace --device=/dev/\$name\", RESULT==\"`/sbin/scsi_id --whitelisted --replace-whitespace --device=/dev/sd$i`\", NAME=\"asm-disk$i\", OWNER=\"grid\", GROUP=\"asmadmin\", MODE=\"0660\""      >> /etc/udev/rules.d/99-oracle-asmdevices.rules
done
```
5. 之后运行用root运行/sbin/start_udev  即可

> 注：

> 使用vmware，需要在主机vmx文件中加入： disk.EnableUUID = "TRUE"，否则UUID出不来

## 安装Oracle RAC

# 学会使用集群验证实用程序（CVU）



未完。