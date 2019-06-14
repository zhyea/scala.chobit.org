---
title: 以冒号结尾的方法
toc: false
date: 2018-06-11 06:43:51
tags: [colon,冒号]
categories: scala技巧
---

这里说的是一个命名约定：如果方法以冒号（:）结尾，则调用目标是运算符后面的实例。

比如下面这个例子：

```scala
class Cow {
  def ^(moon: Moon) = println("Cow jumped over the moon")
}

class Moon {
  def ^:(cow: Cow) = println("This cow jumped over the moon too")
}

val cow = new Cow
val moon = new Moon

cow ^ moon
cow ^: moon
```

看一下执行结果：

```text
Cow jumped over the moon
This cow jumped over the moon too
```

