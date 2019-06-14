---
title: 运算符和优先级
toc: true
date: 2018-04-23 21:42:01
tags: [运算符,优先级]
categories: scala学习
---

## 运算符  

从语法上来说scala是没有运算符的。scala的运算符实际上是方法名，如1 + 2实际上就是1.+(2)。这些方法之所以能像运算符一样使用，是因为scala的一个特性：  
> 如果方法的参数小于等于1个的话，那么“.”和括号就都是可选的  

既然scala的运算符本质上是一个方法，那就说明我们可以自己实现或者重载这些所谓的运算符。下面的代码是一个复数做加法运算的示例，复数有实部和虚部，计算时需要分别处理：
```scala
class Complex(val real: Int, val imaginary: Int) {
  def +(operand: Complex): Complex = {
    new Complex(real + operand.real, imaginary + operand.imaginary)
  }

  override def toString(): String = {
    real + (if (imaginary < 0) "" else "+") + imaginary + "i"
  }
}

val c1 = new Complex(1, 2)
val c2 = new Complex(2, -3)
val sum = c1 + c2

println("(" + c1+ ")+ (" + c2 + ")=" + sum)
```
在代码的第一行创建了一个名为Complex的类、定义了一个有两个参数的构造器。  

在+方法里创建了一个新的Complex实例作为计算结果。结果的实部和虚部分别对应两个运算数实部和虚部之和。计算时c1+c2实际上就是c1.+(c2)。

看下上面程序的执行结果：
```bash
(1+2i)+ (2-3i)=3-1i
```

## 运算符的优先级

既然scala中看起来像运算符的东西都是方法，那么和运算符相关的一些内容，比如算数优先级，scala是怎样处理的呢？  

scala中确实没有算数优先级、但是它定义了方法优先级：方法的优先级由方法名的第一个字符决定，如果表达式里有两个相同优先级的方法，那么左边的方法优先级更高。下面从低到高列出了scala中方法名首字符的优先级：

```text
|
^
&
< >
=  !
: 
+   - 
*  /  % 
所有其他特殊字符
```

我们在之前的Complex类中再添加一个乘法运算来演示下scala运算符的优先级：
```scala
  def *(operand: Complex): Complex = {
    println("Calling *")
    new Complex(real * operand.real - imaginary * operand.imaginary, real * operand.imaginary + imaginary * operand.real)
  }
```

调用代码：
```scala
val c1 = new Complex(1, 4)
val c2 = new Complex(2, -3)
val c3 = new Complex(2, 2)

println(c1 + c2 * c3)
```

执行结果：
```text
Calling *
Calling +
11+2i
```

可以看到*()方法先被执行了。
