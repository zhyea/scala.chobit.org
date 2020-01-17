---
title: scala等于符号“==”
toc: true
date: 2018-04-29 09:27:28
tags:
categories: scala技巧
---


在java中，基本类型和引用类型对==运算有着不同的解释方式：

* 对于基本类型来说，==运算比较的就是两边表达式的值；
* 对于引用类型来说，==运算比较的是两边对象的引用而非是对象的值；在Java中比较对象的值使用的是equals方法。

在scala中没有对象，不论类型如何，==运算都是面向值的。这一点由scala的Any类（scala中所有的类都是由它派生而来）提供了保证：它将==()方法实现为final的方法。因此在scala中进行值的比较就可以简单使用==运算，如果还有一些特殊化的比较，就需要改写equals方法了。在scala中如果想执行对于引用的比较可以使用eq()方法。看一下下面这段程序：

```scala
val str1 = "hello"
val str2 = "hello"
val str3 = new String("hello")

println(str1 == str2) // Equivalent to Java's str1.equals(str2)
println(str1 eq str2) // Equivalent to Java's str1 == str2
println(str1 == str3)
println(str1 eq str3)
```

程序的执行结果：

```text
true
true
true
false
```

结果很清楚了。

最后总结一下吧，在scala中：

1. == 运算是对值进行比较，但是不可修改
1. equals 运算也是对值进行比较，且给开发者提供了实现自定义比较逻辑的空间
1. eq方法则是对引用进行比较
