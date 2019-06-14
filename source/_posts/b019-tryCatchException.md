---
title: 异常处理
toc: true
date: 2018-06-28 22:17:48
tags: [try,catch,异常]
categories: scala技巧
---

下面是一段异常处理的代码：

```scala
  try {
    println("start....")
    throw new RuntimeException
  } catch {
    case re: RuntimeException => println("this is RuntimeException")
    case e: Exception => println("this is Exception")
  }
```
scala中没有多重catch block。而是使用模式匹配来逐级匹配异常并进行处理。

看下执行结果：

```txt
start....
this is RuntimeException
```

还可以catch Error：

```scala
  try {
    println("start....")
    throw new OutOfMemoryError()
  } catch {
    case re: RuntimeException => println("this is RuntimeException")
    case e: Exception => println("this is Exception")
    case error: Error => println("this is an error")
  }
```

结果如下：

```txt
start....
this is an error
```

当然也可以捕捉Throwable，但是在模式匹配中使用通配符却是一个更好地选项：

```scala
  try {
    println("start....")
    throw new Throwable()
  } catch {
    case re: RuntimeException => println("this is RuntimeException")
    case e: Exception => println("this is Exception")
    case error: Error => println("this is an error")
    case _ => println("We catched something.")
  }
```

执行结果：

```text
start....
We catched something.
```
