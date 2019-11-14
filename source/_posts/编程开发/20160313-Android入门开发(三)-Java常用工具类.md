---
title: Java常用工具类
tags:
  - Java
comments: true
date: 2016-03-13 13:16:45
categories: 编程开发
permalink:
---
# 前言

本文主要讨论Java常用工具类。

<!--more-->
# 包与权限问题
- 软件包概念
  ---把类放在不同文件夹中，即为Java类提供了命名空间
```java
package 包名;
```
**注：**
- 编译方法：javac -d . xxx.java
  -d参数是根据包名生成文件夹 . 表当前目录
- 一个类的全名应该是“包名” + “.” + “类名”
  执行方法：java 包名.类名
- 包名的命名规范（习惯）：
  1. 要求所有字母小写
  2. 包名一般是域名反写

- 访问权限
- public：公共权限
- 声明为public权限后，类名必须和文件名相同
  - 不同包中一个类要访问另外一个类时，该类及该类成员变量或成员函数必须为public权限

- private：私有权限
- 只能修饰变量和函数、内部类
  - 只能在当前类使用

- default：包级别访问权限
- 默认访问权限
  - 同一个包中可访问

- protected：受保护权限
- 只能修饰变量和函数
  - 允许跨包继承使用，即必须是子类才能使用
## 权限排序
public > protected > default > private

- 导入类import
```java
import 包名.类名
```
如果子类和父类不在同一个包中，子类可以继承到父类中的default权限的成员变量和成员函数，但是由于权限不足，无法使用
# 接口相关
- 使用interface定义
```java
interface USB{	//接口定义
	void read();//抽象方法且权限为public
	
	void write();
}
```

- 接口当中的方法都是抽象方法

```java
class USBPhone implements USB{//implements是对USB接口的实现（类似继承）
	public void read(){	//覆写抽象方法
		System.out.println("读");
	}
	
	public void write(){
		System.out.println("写");
	}
}
```

- 接口当中的方法都是public权限
```java
class Test{
  public static void main(String args[]){
      USBPhone usbphone = new USBPhone();
      USB usb = usbphone; //向上转型为USB引用

      usb.read();//调用read方法
      usb.write();
  }
}
```
-  一个类可以实现多个接口

```java
class Phone implements USB,WiFi{ //实现多个接口用逗号隔开即可

}
```

- 一个接口可以继承多个接口

```java
interface A{
	void funA();
}

interface B{
	void funB();
}

interface C extends A,B{ //C类继承了A、B两个接口
	void funC();
}
```
- 接口应用

（22、29集）

# 异常

中断正常指令流的事件，程序运行时出处而非编译时出处。

## 异常的分类

Throwable --  Exception	-- RuntimeException

```java
					-- 。。。
	  --  Error
```

- 查开发文档
  checkException //需要对异常进行处理，不然无法编译通过
  uncheckException 

## 异常处理

try{

```
//异常处理
```

}catch(Exception e){

```
e.printStackTrace();
```

}finally{

```
//异常出口，IO流需要用
```

}

## 抛出异常

- throw

```java
class User{
	private int age;
	
	public void setAge(int age){
		if(age<0){
			RuntimeException e = new RuntimeException("年龄不能为负数");//运行时异常
			//Exception e = new Exception("");//属于checkException必须try...catch或声明throws
			throw e; //抛出异常，如无try..catch则程序阻止
			
		}
		this.age = age;
	}
}
```

- throws

```java
class User{
	private int age;
	
	public void setAge(int age) throws Exception{ //调用setAge时处理异常
		if(age<0){
			Exception e = new Exception("");//属于checkException必须try...catch或声明throws
			throw e; //抛出异常，如无try..catch则程序阻止
			
		}
		this.age = age;
	}
}

class Test{
	public static void main(String args[]){
		User user = new User();
		try{
			user.setAge(-20);
		}
		catch(Exception e){
			System.out.println(e);
		}
	}
}
```
# IO相关
## IO操作的目标

从源中读取数据以及将数据写入到数据目的地

## IO的分类

- 输入/输出
- 字节/字符
- 节点/处理

- 字节流核心类
```
InpuStream							OutputStream	抽象类
int read(byte[]b,int off,int len)
总多少数据		比特形式数组   开头    长度	
void write(byte[]b,int off,int len)
```

```
FileInputStream		FileOutputStream
```

```java
import java.io.*;//导入类

class Test{
	public static void main(String args[]){
		FileInputStream fis = null;//声明输入流的引用
		try{
			fis = new FileInputStream("这里写文件路径/xxx.txt");
			byte [] buffer = new byte[100];//声明一个大小为100的比特型数组
			fis.read(buffer,0,buffer.length);//调用read()将输入流存入数组
			for(int i = 0;i<buffer.length;i++){
				System.out.println("buffer[i]");//打印该数组
			}
			
			//String s = new String(buffer);//将字节还原成字符串
			// s = s.trim();//调用String对象的trim方法，去掉这个字符串首尾空格和空字符
			//System.out.println(s);
		}catch(Exception e){
			System.out.println(e);
		}
	}
}
```

## 读取文件和写入文件的方法

```java
import java.io.*;//导入类

class Test{
	public static void main(String args[]){
		FileInputStream fis = null;//声明输入流的引用
		
		FileOutputStream fos = null;//声明输出流的引用
		try{
			fis = new FileInputStream("这里写输入文件路径/xxx.txt");
			
			fos = new FileOutputStream("这里写输出文件路径/xxx.txt");
			
			byte [] buffer = new byte[100];//声明一个大小为100的比特型数组
			int temp = fis.read(buffer,0,buffer.length);//调用read()将输入流存入数组
			//read()方法返回值是一共读取多少数据存入temp中
			fos.write(buffer,0,temp);//buffer就像一个桶，做中间传递用
		}catch(Exception e){
			System.out.println(e);
		}
	}
}
```

## 大文件读写方法

循环读取，每次读一部分。写入类似
当read()方法读完会返回-1

```java
import java.io.*;//导入类

class Test{
	public static void main(String args[]){
		FileInputStream fis = null;//声明输入流的引用
		
		FileOutputStream fos = null;//声明输出流的引用
		try{
			fis = new FileInputStream("这里写输入文件路径/xxx.txt");
			
			fos = new FileOutputStream("这里写输出文件路径/xxx.txt");
			
			byte [] buffer = new byte[1024];//声明一个大小为1024的比特型数组
			
			while(true){	//借用循环写入
				int temp = fis.read(buffer,0,buffer.length);//调用read()将输入流存入数组
				if (temp == -1){
					break;
				}
				fos.write(buffer,0,temp);//buffer就像一个桶，做中间传递用
			}
			
		}catch(Exception e){
			System.out.println(e);
		}
		finally{
			try{
				fis.close();	//调用close()方法在读入写入完后关闭流
				fos.close();	//且由于close()方法也会抛出异常，需要捕获
			}
			catch(Exception e){
				System.out.println(e);
			}
		}
	}
}
```

## 字符流使用方法

- 读写文件时，以字符为基础
  字节输入流：Reader（类似InputStream是抽象类） <-- FileReader （类似FileInputStream）
  int read(char [] c,int off,int len);
  字节输出流：Writer  <-- FileWriter
  void write(char [] c,int off,int len);

## BufferedReader

读取一行数据
声明：public String readLine() throws IOException
生成BufferedReader对象的方法
BufferedReader in = new BufferedReader(newFileReader("foo.in"));

```java
import java.io.*;

class Test{
	public static void main(String args[]){
		FileReader fileReader = null;
		BufferedReader bufferedReader = null;
		
		try{
			fileReader = new FileReader("文件路径");
			bifferedReader = new BufferedReader(fileReader);
		//	String line = bufferedReader.readLine();//只读取一行
		//	System.out.println(line);
			String line = null;
			while(true){	//读取所有行
				line = bufferedReader.readLine();
				if(line == null){
					break;
				}
				System.out.println(line);
			}
		}catch(Exception e){
			System.out.println(e);
		}finally{
			try{
				bifferedReader.close();
				fileReader.close();
				
			}catch(Exception e){
				System.out.println(e);
			}
		}
	}
}
```

# “装饰者”设计模式

设计模式这部分目前还未理解，暂留。

# 内部类

```java
class A{
	int i;
	
	class B{	//B作为内部类可以使用A的成员变量和成员函数
		int j;
		
		int funB(){
			int result = i + j;	//i是外部类的成员变量A.this.i
			
			return result;
		}
	}
}
```

编译后会生成A.class和A$B.class两个文件

```java
class Test{
	public static void main(String args[]){
		A a = new A();	//生成外部类对象
		
		A.B b = new A().new B();	//生成内部类对象
	}
	
}
```

## 匿名内部类

interface A{

```
public void doSomething();
```

}

class B{	

```
public void fun(A a){	//需要A类型对象作为参数
	System.out.println("B类的fun函数");
	a.doSomething();
}
```

}

class AImpl implements A{	//实现doSomething()方法

```
public void doSomething(){
	System.out.println("doSomething");
}
```

}

如要调用B类的fun()方法时，首先应该生成AImpl对象，然后向上转型为A类型，再作为参数传进fun()

```java
class Test{
	public static void main(String args[]){
		AImpl al = new AImpl();
		A a = al; //向上转型
		
		B b = new B();
		b.fun(a);
	}
	
}

结果： B类的fun函数
		doSomething
		
若使用匿名内部类
​```java
​```java
class Test{
	public static void main(String args[]){
		
		B b = new B();
		b.fun(new A(){	//对比完整A的实现类，几乎相同，只是没有名字
						//即内部类覆写doSomething()方法
			public void doSomething(){
				System.out.println("匿名内部类");
			}
		});
	}
}
```

# 进程和线程

多进程：
  操作系统同时运行多个任务
多线程：
  同一应用程序中多个顺序流同时执行


## 多线程程序运行方法

### 定义线程方法

1. 定义一个线程类，它继承Thread并重写其中的方法run(),run()称为线程体
   注：由于Java只支持单继承，用这种方法定义的类不能再继承其他类。

```java
class FirstThread extends Thread{
	public void run(){
		for(int i = 0;i<100;i++){
			System.out.println("FirstThread-->"+i);
		}
	}
}

class Test{
	public static void main(String args[]){
		//生成线程类对象
		FirstThread ft= new FirstThread();
		//启动线程，不是使用run()
		ft.start();	//从Thread继承而来的方法,开启一个线程去执行
		//若这里使用ft.run();则非使用多线程交替
		for(int i = 0; i < 100; i++){
			System.out.println("main-->"+i);
		}
	}
}
```

2. 提供一个实现接口Runnable的类作为线程的目标对象，在初始化一个Thread类或者Thread子类的线程对象时，把目标对象传递给这个线程实例，由该目标对象提供线程体。

```java
class RunnableImpl implements Runnable{
	public void run(){
		for(int i = 0; i < 100;i++){
			System.out.println("Runnable -->"+i);
		}
	}
}

class Test{
	public static void main(String args[]){
		//生成一个Runnable接口实现类的对象
		RunnableImpl ri = new RunnableImpl();
		//生成一个Thread对象，并将Runnable接口实现类的对象作为参数传递给该Thread对象
		Thread t = new Thread(ri);
		//通知Thread对象，执行start方法
		t.start();
	}
}
```

对比方法1，多使用方法2

## 线程简单控制方法

### 中断线程

```
- Thread.sleep();
- Thread.yield();	//自动让出CPU
```

### 设置优先级

```
- getPriority();
- setPriority();
```

```java
class RunnableImpl implements Runnable{
	public void run(){
		for(int i = 0; i < 100;i++){
			System.out.println("Runnable -->"+i);
		}
	}
}

class Test{
	public static void main(String args[]){
		//生成一个Runnable接口实现类的对象
		RunnableImpl ri = new RunnableImpl();
		//生成一个Thread对象，并将Runnable接口实现类的对象作为参数传递给该Thread对象
		Thread t = new Thread(ri);
		t.setPriority(Thread.MAX_PRIORITY);//设置为最大优先级10，最小优先级1MIN_PRIORITY
		//通知Thread对象，执行start方法
		t.start();
		System.out.println(t.getPriority());//获取线程优先级
	}
}
```

## 多线程共用同一份数据

需要使用到同步代码块，否则数据会出现错误

```java
class MyThread impements Runnable{
	int i = 100;
	public void run(){
		while(true){
			synchronized(this){	//同步代码块，this相当于互斥量
				System.out.println(Thread.currentThread().getName() + i);
				i--;
				Thread.yield();
				if(i < 0){
					bread;
				}
				
			}
		}
	}
}
```