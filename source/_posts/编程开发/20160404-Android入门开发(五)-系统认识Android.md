---
title: Android入门开发---系统认识Android
tags:
   - Android
date: 2016-04-04 20:33:50
categories: 编程开发
---
补了好多Java基础知识，

> - [Java 基础语法]()
> - [Java 面向对象]()
> - [Java 常用工具类]()
> - [Java 类集框架]()

终于可以开始碰Android，我主要参考郭霖的[《第二行代码》](http://www.ituring.com.cn/book/1841)来写的读书笔记。毕竟他的[《第一行代码》](http://www.ituring.com.cn/book/1473)被热推，不过由于Android更新太快，里面很多东西都过时，刚好作者十分勤奋，2016年底推出《第二行代码》，迅速学起来。

- 工具 [Android Studio 中文站](http://www.android-studio.org/) 

<!--more-->

# 基础知识
## 系统架构
1. Linux内核层
   为各种硬件提供底层驱动，如显示驱动、音频驱动、照相机驱动、蓝牙驱动、WiFi驱动、电源管理等；
2. 系统运行库层
   特性支持，如SQLite(嵌入式关系型数据库)库提供数据库支持，OpenGL|ES库提供3D绘图支持，Webkit库提供浏览器内核支持
   核心库，可用Java编写Android应用
   Dalvik/ART虚拟机
3. 应用框架层
   应用程序可用API
4. 应用层
   应用程序就是这层...

   ![Android的系统架构](http://images.cnitblog.com/blog/473657/201301/18203746-970e2cbe223e4c1c9ca129e7a2feb6c6.jpg)

## 四大组件
1. 活动（Activity）
   应用程序门面，看得到的东西
2. 服务（Service）
   后台运行
3. 广播接收器（Broadcast Receiver）
   顾名思义
4. 内容提供器（Content Provider）
   程序共享数据

# Android项目结构
**Project模式**
1. .gradle和.idea
   存放Android Studio自动生成文件
2. app
   项目代码、资源
3. build
   编译自动生成文件
4. gradle
   包含gradle wrapper配置文件
5. gitgnore
   用于版本控制
6. build.gradle
   全局gradle构建脚本
7. gradle.properties
   gradle配置文件，影响gradle编译脚本
8. gradlew和gradlew.bat
   用于命令行执行gradle命令
9. HellWorld.iml
   IntelliJ IDEA项目(Android Studio基于该项目开放)自动生成
10. local.properties
  本机Android SDK路径
11. setting.gradle
   用于指定项目中所有引入模块

**app目录详解**
1. build
   编译自动生成
2. libs
   第三方jar包存放处
3. androidTest
   自动化测试用
4. java
   放置所以Java代码
5. res
   项目使用到的资源（如图片、布局、字符串等）都存放在这
6. AndroidManifest.xml
   配置文件，程序定义四大组件需要在这注册，还可以添加程序声明
7. test
   Unit Test测试用例
8. .gitignore
   版本控制
9. app.iml
   自动生成
10. build.gradle
  app模块的gradle构建脚本
11. proguard-rules.pro
   指定项目代码混淆规则，防止破解

**res目录详解**
- 所有drawable开头的文件夹都是存放图片的
- 所有mipmap开头的文件夹都是存放应用图标的（为更好兼容各种设备，mipmap开头文件夹会有很多）
- 所有values开头的文件夹都是存放字符串、样式、颜色等配置
- layout文件夹存放布局文件

# 日志工具使用
日志工具对开发提供极大帮助，Android的日志工具类是Log（android.util.Log）,提供5个方法来打印日志：
- Log.v()
   打印意义最小的日志信息---verbose
- Log.d()
   打印调试信息---debug
- Log.i()
   打印比较重要数据，可帮助分析用户行为---info
- Log.w()
   打印警告，可能有潜在风险---warn
- Log.e()
   打印错误---error

**过滤器**
Google分析工具---Firebase
 当程序打印出成百上千行日志的时候会迫切需要过滤器。