---
title: 快速体验Scala - 交互式编程
toc: true
date: 2018-04-21 20:14:21
tags: 交互式解释器
categories: scala学习
---

scala提供的交互式解释器也是一种快速体验scala的一种方式。  

## 使用交互式解释器

在安装好scala开发环境后，打开命令行工具，输入scala就可以进入scala解释器：
```bash
robin@robin-PC MINGW64 ~
$ scala
Welcome to Scala 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_25).
Type in expressions for evaluation. Or try :help.

scala>

```
输入“:quit”或“:q”可以退出scala解释器。   
接下来我们使用scala的交互式解释器来了解一些scala的基础概念。  

## 定义变量  

scala的变量有两种类型：var和val。其中，val变量也可以称为常量，val定义的变量赋值后不可修改，类似于Java中final标记的变量。var变量可以随意赋值修改。  
```bash
scala> val msg = "Hello World!"
msg: String = Hello World!

```
代码中声明了一个val变量msg。声明变量时没有显式指明类型，但是解释器也没有报错。scala是可以根据变量的值推断出变量的类型的。  
当然也可以显式指明变量类型，像下面这样：  
```bash
scala> val msg:String = "Hello World!"
msg: String = Hello World!

```
## 定义函数  

在上一节我们已经在HelloWorld类中定义过一个main方法了，现在我们在交互式解释器中再定义一个新的函数:
```bash
scala> def hello(name:String){
     | println(s"Hello $name!")
     | println("End")
     | }

```
这里是在上一节定义的main函数：
```scala
    def main(args: Array[String]) = println("Hello World!")
```
我们比较一下这两个函数，可以看出scala函数定义的一些规则：  
* scala函数以def关键字定义
* 函数的参数列表中，参数名称和参数类型以“:”分隔 
* 方法体需要用花括号括起来，但是只有一行时，可以不使用花括号  
* 方法的返回类型可以不必声明
* 等号不是必需的

大体上就是这样。scala函数定义的规则还有很多，这里只是管中窥豹，简单说下。  

另外，在上面的代码中我们可以看到每一行语句末尾都没有使用分号，这是scala和java一个很大的不同。在scala中，分号是可选的。若一行中只有一个语句，分号可以不填；若一行中有多个语句，分号则是必填。

