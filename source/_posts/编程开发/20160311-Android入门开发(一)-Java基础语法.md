---
title: Java基础语法
tags:
  - Java
comments: true
date: 2016-03-11 10:50:51
categories: 编程开发
permalink:
---

由于毕设自己挖坑要做硬件又要做APP，逼得我捡起Java，在此将学习笔记贴来作为整理记忆。
该笔记大致分为：

1. Java基础语法
2. Java面向对象
3. Java常用工具类

<!--more-->

# 变量

## 变量类型

说明：

- Java字符使用Unicode字符集，原ASCII字符集是7位二进制，Unicode使用统一二进制0xFFFFFF
- byte < short < int < long < float < double
  ![变量类型](http://olvboulzy.bkt.clouddn.com/data_type.jpg)

## 变量命名

说明：应以**字母**、**美元符号**、**下划线**开头，且**对大小写敏感**，如下：

```java
 int age = 10;
 int Age = 20;
 int $age = 30;
 int _age = 40;
```

# 运算符

说明：
A&B 和 A&&B 区别，当A为false时，不管B不运算

```java
public class Test{
	public static void main(String args[]){
		int i = 5 ;
		boolean e = i > 6 && i++ > 7;
		System.out.println(e);
		System.out.println(i);
	}
}
```

输出 e=false i=5

# 程序运行流程

1. 顺序结构
2. 分支结构
   if...else...
   switch...case...
3. 循环结构
   for
   while

# 数组

数据类型分为基本数据类型和引用数据类型
数组和对象属于引用数据类型

```java
class Test{
	public static void main(String args[]){
		//数组的静态定义法
		int arr [] = {5,2,7,9,0};
		//数组的动态声明法
		int arr [] = new int[10];//元素类型为int型默认是0，boolean型默认为false，char型默认为空字符
		
		//二维数组的定义方法
		int arr [][] = {{1,2,3},{4,5,6},{7,8,9}};
		
		for(int i = 0;i <arr.length;i++){
			for(int j = 0;j < arr[i].length;j++){//防止出现数组越界异常
				System.out.println(arr[i][j]);
			}
		}
		//动态定义
		int arr [][] = new int[3][5];
	}
}
```

- 数组一旦声明，长度即确定

