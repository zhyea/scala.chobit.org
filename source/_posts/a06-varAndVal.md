---
title: var和val变量
toc: true
date: 2018-04-24 22:06:20
tags: 变量
categories: scala学习
---

## 变量声明

前面我们提过，在scala中声明变量使用var和val都是可行的。其中val是value（值）的简写，var指的是variant（变量）。

用val声明的变量是不可变的，被初始化后值就固定下来，不可以再被修改（这类似于java中的final关键字）。用var声明的变量是可变的，可以任意修改。  

在scala解释器中声明变量实例如下：

```bash
scala> var myVar:String = "my var string"
myVar: String = my var string

scala> myVar = "update my var"
myVar: String = update my var

scala> val myVal:String = "my val string"
myVal: String = my val string

scala> myVal = "update my val"
<console>:12: error: reassignment to val
       myVal = "update my val"

```

可以看到修改var变量是没有问题的。修改val变量时报错了，因为val变量是不可变的。  

仍然要注意这里说的不可变是针对变量本身而言，而非变量所指向的实例。比如说如果定义了一个StringBuffer的val变量如val buffer = new StringBuffer()，那么变量buffer再执行诸如buffer.append(“robin”)这样的操作来修改buffer实例也是可行的。  

在scala编程中应尽量优先使用val。这可以提升不变性和函数式风格。

## 变量类型声明

前面我们在声明变量时都指定了变量类型：  

```scala
val myVal:String = "my val string"
```
不过在声明变量时，变量类型不是必需要指定的，像下面这样的写法也是完全没问题的：  

```scala
val myVal = "my val string"
var myInt = 10
```
声明变量时，如没有指定变量类型，scala也可以根据变量的初始值来推断出变量的类型。因此，声明变量时如没有指定变量类型，就必须为其指定初始值，不然就会报错：

```bash
scala> var myVar;
<console>:1: error: '=' expected but ';' found.
var myVar;

```

## 声明多个变量

scala也支持一次声明多个变量，像下面这样：
```scala
scala> var a, b = 100
a: Int = 100
b: Int = 100
```

示例代码中同时声明了a， b两个变量，并为这两个变量同时赋值为100。