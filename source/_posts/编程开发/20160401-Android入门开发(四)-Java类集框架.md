---
title: Android入门开发(四)-Java类集框架
tags:
  - Java
  - Android
comments: true
date: 2016-04-01 21:17:23
categories: 编程开发
permalink: 
---
这节略难，很多不太理解，先记下来。
<!--more-->
# 定义
-  一组类和接口
-  位于java.util包当中
-  主要用户存储和管理对象
-  主要分为三大类：集合、列表和映射
-  集合set
       集合中的对象**不按特定的方式排序**，并且**没有重复对象**
   - 列表list
     集合中对象按照索引位置**排序**，可以**有重复对象**
   - 映射map
     集合中每个元素包含一个键对象和一个值对象，键不可重复，但键值可以---键值对

```java
//列表的用法
import java.util.List;
import java.util.ArrayList;

public class Test{
	public static void main(String args[]){
		ArrayList arrayList = new ArrayList();//数组长度可扩展
		//ArrayList<String> arrayList = new ArrayList<String>();
		// arrayList中存放数据类型为String --- 泛型机制
		arrayList.add("a");
		arrayList.add("b");
		arrayList.add("c");
		
		int a = arrayList.size();//取其长度
		String s = arrayList.get(1);//取出第一个数据
		System.out.println(s);
		String s = arrayList.get(1);//下标越界异常
		arrayList.remove(1);//删除1号
	}
}
```

# Collection接口
类集框架分为三个层次，最顶层的是Collection接口和Iterator接口，第二层是Set接口和它的实现类HashSet


|         接口方法          |      说明       |
| :-------------------: | :-----------: |
| boolean add(Object o) |  向集合中加入一个对象   |
|     void clear()      |  删除集合当中的所有对象  |
|   boolean isEmpty()   |   判断集合是否为空    |
|   remove(Object o)    | 从集合中删除一个对象的引用 |
|      int size()       |  返回集合中元素的数目   |

- Set和HashSet使用方法
  Set和HashSet继承了Collection接口

```java
import java.util.Set;
import java.util.HashSet;
public class Test{
	public static void main(String args[]){
		//HashSet<String> hashSet = new HashSet<String>();
		//Set<String> set = hashSet; //向上转型
		
		Set<String> set = new HashSet<String>();
		set.add("a"); //添加元素
		set.add("b");
		set.add("c");
		set.add("d");
		
		int i = set.size(); //统计长度，如有重复元素会忽略
		System.out.println("clear之前set对象的长度是："+ i );
		set.clear();
		int j = set.size(); //clear后长度变成0
		System.out.println(i);
	}
}
```

- Iterator迭代器


# 映射Map
- put()方法

- get()方法

# equals函数
包含在object类下，object类是所有类的父类，故所有类都继承equals方法。

**双等号操作符与equals方法比较**
- “==”操作符
  判断两个引用（栈内存中）是否指向堆内存中同一个地址

```java
class User{
	String name;
	int age;
}

class test{
	public static void main(String[] args){
		User u1 = new User();
		User u2 = new User();
		User u3 = u1;

		boolean b1 = u1 == u2;
		boolean b2 = u1 == u3;

		System.out.print(b1);//false
		System.out.print(b2);//true
	}
}
```
- equals
  equals比较两个对象**内容**是否相等
  条件：
  1. 对象类型相同（可以使用instanceof操作符进行比较）；
  2. 两个对象的成员变量值完全相同。

```java
class User{
	String name;
	int age;

	public boolean equals(Object obj){
		if(this = obj){ //this是调用equals的对象，为User类型
			return true;
		}

		boolean b = obj instanceof User; //判断obj是否为User类型
		if(b){
			User u = (User)obj;//向下转型为User类型

			if(this.age == u.age && this.name.equals(u.name)){
				return true;
			}else{
				return false;
			}
		}else{
			return false;
		}
	}
}
class Test{
	public static void main(String[] args) {
		User u1 = new User();
		User u2 = new User();
		User u3 = new User();
		
		boolean b1 = u1.equals(u2);//比较u1、u2
		System.out.print(b1);
		/* Object类关于equals源代码
		public boolean equals(Object obj){
			return (this == obj);
		}
		 */	
		
		u1.name = "zhangsan";
		u1.age = 12;

		u2.name = "lisi";
		u2.age = 12;

		u3.name = "zhangsan";
		u2.age = 12;

		System.out.print(u1.equals(u2)); //false
		System.out.print(u1.equals(u3)); //true
		System.out.print(u1.equals(u3)); //true
	}
}
```



