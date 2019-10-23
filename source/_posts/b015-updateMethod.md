---
title: update方法
toc: false
date: 2018-06-11 22:13:57
tags: update
categories: scala技巧
---

之前有介绍过apply方法，比如下面的代码：
```scala
foo(arg1, arg2, ...)
```
如果foo不是一个函数或者方法, 那么这个表达式等同于调用apply方法。但是如果它出现在赋值语句的左侧, 即 foo(arg1, arg2, ...) = value, 那么调用的就是update方法。

看个具体的例子：

```scala

  class Coder(name: String) {

    def update(key: String, value: String) = println(s"$name's  $key is $value.")

  }


  val robin = new Coder("robin")

  robin("job") = "coder"
```

执行结果如下：
```text
robin's  job is coder.
```

如示例中所示，update方法通常可用于键值对或“索引->值”的赋值与更新。
