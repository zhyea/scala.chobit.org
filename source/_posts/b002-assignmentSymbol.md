---
title: scala赋值符号“=”
toc: true
date: 2018-04-29 09:15:04
tags:
categories: scala技巧
---


scala的赋值运算和java的赋值运算有着很大的不同。如a=b这样的赋值运算，在Java中的返回值是a的值，在scala中返回的则是Unit。Unit是值类型，全局只存在唯一的值，即()，通常Unit只用来声明函数或方法的返回值，其他场景基本是没有意义的。这样就很容易导致一些错误地使用，比如a=b=c这样的赋值运算在java中是绝对可以的，但是在scala中使用就会报错。  

看一段代码：
```scala
var a, b, c = 1
a =  b=c
println(a)
```

上面这段代码还没有执行就在IDE中提示错误了，勉强执行下看看会报什么错：

```text
Error:(6, 9) type mismatch;
 found   : Unit
 required: Int
  a = b = c
```

报了类型不匹配的错误，因为在第一行代码中scala推断出a是一个Int型的变量，后又将b=c的运算结果——一个Unit值——赋值给它，所以会报错了。

即使只在scala中看赋值运算符“=”号也是一个比较奇怪的东西：scala中的大部分运算符都是方法，但“=”不是方法，在默认情况下不可以这样使用：a.=(1)。对于这个问题，有心人可以深究下。

