---
title: Android入门开发(二)---Java面向对象
tags:
  - Java
  - Android
comments: true
date: 2017-03-12 22:57:01
categories: 编程开发
permalink:
---

# 面向对象

**终极目标**

```
--- 消除重复代码
```

## 如何学习

1. 掌握语法

2. 训练思维方式
   2.1 先确定谁来做，再考虑怎么做
   2.2 先考虑整体，再局部

   ```
   例如：如以面向对象考虑设计一部电梯
   ```

   - 先有一部电梯
     - 考虑电梯有哪些行为：上、下、停、开门、关门等
       - 电梯属性：载重多少，长宽高

   ★ 2.3 先抽象，后具体

   多做训练、对比

3. 熟悉设计原则
   开放注入原则等

4. 掌握设计模式（前人经验总结）

<!--more-->

# 创建类、对象及对象方法和引用

## 定义类

```java
class 类名{
	属性; //成员变量，描述类的状态
	方法; //成员方法/函数，描述类的行为
}
```

## 生成对象的方法

```java
类名 对象名 = new 类名();
```

### java数据类型

```
- 基本数据类型
- 引用数据类型
```

![生成对象方法](http://olvboulzy.bkt.clouddn.com/generated%20_objec_fun.jpg)	
对象名属于引用数据类型
堆内存和栈内存的使用：堆（heap）内存存放对象本体，栈（stack）内存存放对象的引用---对象名
类和对象的关系：类是抽象概念，对象是具体的个人

### 对象的使用方法

- 对象.变量
- 对象.函数
  说明：属性都是有主的

### 匿名对象

```
即没有名字的对象，一次性的
new Dog().jump();
```

## 函数重载

```
同一个类中，返回值相同，函数同名，但参数列表不同
根据传参不同判断是执行哪个函数
```

```java
class A{
	void funA(){
		System.out.println("没有参数的funA()");
	}
	
	void funA(int i){
		System.out.println("有一个参数的funA()");
	}
}
```

主函数

```java
class Test{
	public static void main(String args[]){
		A a = new A();
		a.funA();
		a.fun(10);
	}
}
```

### 构造函数的作用

构造函数不算成员函数

- 无返回值的定义
- 构造函数名字和类名必须相同
- 如未自定义，编译时会自动生成无参且方法体为空的构造函数
- 使用new时会调用构造函数

# this的使用

this相当于人称代词---“我”，是调用函数的对象

## 调用成员变量和成员函数

```java
class person{
	String name;
	
	void talk(String name){
		System.out.println("my name is " + this.name);
	}
}
```

说明：this.name表示成员变量name而非参数name

```java
class person{
	String name;
	int age;
	
	person(){
		System.out.println("无参的构造函数");
	}
	
	person(String name,int age,String address){
		this.name = name;
		this.age = age;
		this.address = address;
	}
	
	void talk(String name){
		System.out.println("my name is " + this.name);
	}
}
```

## 调用构造函数

```java
class person{
	String name;
	int age;
	
	person(){
		System.out.println("无参的构造函数");
	}
	person(String name,int age){
		this.name = name;
		this.age = age;
	}
	
	person(String name,int age,String address){
		this(name,age);
		this.address = address;
	}
	
	void talk(String name){
		System.out.println("my name is " + this.name);
	}
}
```
**注：** 对this()构造函数的调用必须是第一条语句！

# static关键字的作用

## 静态成员变量语法特点

1. 可直接用类名来调用或对象名调用

```java
class Person{
	static int i;
}
```

主函数

```java
class Test{
	public static void main(String args[]){
		Person.i = 10;
	}
}
```

1. 所有对象使用的静态变量都是同一个
   ---静态变量属于类，任何该类生成的对象所使用的静态变量都是同一个
   ![静态成员变量](http://olvboulzy.bkt.clouddn.com/static_member.jpg)

## 静态函数语法特点

静态函数不能使用非静态的成员变量
静态函数当中不能使用this

```java
class Person{
	static int i;
	
	static void fun(){
		System.out.println("");
	}
}
```

## 静态代码块语法特点

没有名字，默认装载类时执行，一般是为静态变量赋初始值,如下：
```java
static{
	System.out.println("静态代码块");
}
```

# 继承（扩展）
- Java只支持单继承
- 只继承成员变量和成员函数
- 使用继承是为了减少重复代码
```java
class Person{
  String name;
  int age;

  void eat(){
      System.out.println("吃");
  }
}

class Student extends Person{
	//Student继承了Person类，即拥有了Person的成员变量和成员函数
}
```
# 子类实例化

在子类的构造函数当中，必须调用父类的构造函数。
原因：子类继承父类无法继承构造函数，可能产生重复代码，为解决这个问题，要求必须调用super()
```java
class Person{
  String name;
  int age;
  Person(){
      System.out.println("Person的无参构造函数");
  }
  void eat(){
      System.out.println("吃");
  }
}
```

```java
class Student extends Person{
  Student(){
      super(); //调用父类无参构造函数
      System.out.println("");
  }
}
```

# 函数覆写（override）

子类对父类构造函数的重写

1. 在具有父子关系的两个类中
2. 父类和子类各有一个函数，这两个函数的定义（返回值类型、函数名和参数列表都相同）

```java
class Person{
	String name;
	int age;
	Person(){
		System.out.println("Person的无参构造函数");
	}
	void eat(){
		System.out.println("吃");
	}
}

class Student extends Person{
	String address;
	
	void eat(){
		System.out.println("好好吃");
	}
}
```

使用super调用父类成员函数

```
---super.父类成员函数名();
```

# 抽象类和抽象函数

面向对象 -> 先抽象后具体

## 抽象函数语法特征

 ---只有**函数定义**，没有**函数体**的函数
```java
abstract void fun(); //抽象函数fun()
```
## 抽象类语法特征
abstract定义
- 抽象类不能生成对象 （原因：如果调用抽象函数无法执行--无函数体）
- 一个类中包含抽象函数，该类必须被声明为抽象类
- 一个类中没有抽象函数，该类可以被声明为抽象类

## 抽象类作用
就是用来被继承的，子类可以生成对象
可以拥有构造函数

```java
abstract class Person{
	abstract void eat(); //抽象函数
}
```

```java
class Chinese extends Person{
	void eat(){ //复写Person的抽象函数
		System.out.println("筷子吃饭");
	}
}
```

```java
class Test{
	public static void main(String args[]){
		Person p = new Chinese(); //向上转型
		p.eat(); //调用eat方法，结果打印出“筷子吃饭”
	}
}
```

## 为什么要使用抽象类

```java
class Printer{
	void open(){
		System.out.println("open");
	}
	
	void close(){
		System.out.println("close");
	}
	
	void print(){
		//不声明为抽象类，方法体为空，要求覆写
	}
}
```

```java
class HPPrinter extends Printer{
	//如果忘记覆写print，程序编译不会报错，但结果不对
}
```

改正后

```java
abstract class Printer{
	void open(){
		System.out.println("open");
	}
	
	void close(){
		System.out.println("close");
	}
	
	abstract void print(); //抽象函数，如子类不覆写该方法将会报错，避免语义错误
}
```

> 编程希望语法和语义的错误能达到统一。

# 对象转型

**对象转型是多态性的体现**

1. 向上转型

将子类的对象赋值给父类的引用
```java
Student s = new Student;
Person p = s;
```
一个引用能够调用哪些成员（变量和函数），取决于这个引用的类型。
一个引用调用的是哪一个方法，取决于这个引用所指向的对象。

```java
class Person{
	String name;
	int age;
	
	void introduce(){
		System.out.println("我的名字是：" + name + "，我的年龄是：" + age);
	}
}
```

```java
class Student extends Person{
	String address;
	void study(){
		System.out.println("我在学习");
	}
	
	void introduce(){
		super.introduce();
		System.out.println("我的家在：" + address);
	}
}
```

```java
class Test{
	public static void main(String args[]){
		Student s = new Student();
		Person p = s; //s对象向上转型为Person
		
		p.name = "张三"; //调用person的成员  
		p.age = 20;
		
		p.introduce(); //调用的是Student的成员函数
		p.study();
	}
}
```

2. 向下转型

将父类的对象赋值给子类的引用
```java
Student s1 = new Student();
Person p = s1;			//先向上转型
Student s2 = (Student)p; //再向下强制转型
```
----

**注：**

对象转型十分重要，需要在未来慢慢体会。

----

