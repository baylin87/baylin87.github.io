---
title: OpenStack初步
tags:
  - OpenStack
  - 虚拟化
comments: true
date: 2017-05-09 11:13:21
categories: 系统运维
permalink:
---

# 基本概念

## 一型虚拟化

虚拟化平台直接安装在物理机上，如ESXi、Xen



## 二型虚拟化

物理机安装操作系统，操作系统上再安装虚拟化软件，如KVM

<!--more-->

### KVM

KVM有一个内核模块---kvm.ko ，只用于管理**虚拟CPU**和**内存**。

IO虚拟化，如存储和网络设备虚拟化交给Linux内核和Qemu

#### Libvirt

---KVM管理工具

包含 

  1、后台daemon程序libvirtd

服务程序，接收和处理API请求

  2、API库

用于开发基于Libvirt的高级工具，如virt-manager

  3、命令行工具virsh

KVM命令行工具