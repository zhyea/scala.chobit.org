---
title: 使用注释
toc: true
date: 2018-04-22 21:53:24
tags: [comment, 注释]
categories: scala学习
---


简单说下scala中的注释。scala的注释和java的注释是一样的，同样有多行注释和单行注释，使用方法也是一样的。  

这里是一段示例代码：
```scala
object HelloWorld {

  /**
    * 这里是多行注释
    *
    * 这里是main方法
    */
  def main(args: Array[String]): Unit = {
    // 这里是单行注释
    print("Hello World!")
  }
  
}
```
就是这样，没有什么需要解释的地方。




