---
title: 方法的点和括号是可选的
toc: false
date: 2018-05-03 22:04:13
tags:
categories: scala技巧
---


在scala中，如果方法只有0或1个参数，点和括号就是可选的；如果方法的参数多于1个，就必须使用括号，但是点仍然是可选的。   

比如我们常见的 1 + 2，实际上就是在执行 1.+(2) 。  

这样做的优势也很明显：代码读起来更加自然，在展现上不至于显得杂乱。

下面是一段示例代码：
```scala
  class Teacher(val name: String) {

    def teach(subject: String) = println(s"$name teaches $subject.")

    def teach(subject: String, student: String*) = println(s"$name teaches ${student.mkString(",")} to learn $subject")

  }

  val teacher = new Teacher("Mr.Chang")

  teacher teach "Mathematics"

  teacher teach ("Mathematics", "robin")
```

执行结果如下：
```text
Mr.Chang teaches Mathematics.
Mr.Chang teaches robin to learn Mathematics
```