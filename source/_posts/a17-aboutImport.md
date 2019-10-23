---
title: 关于import
toc: false
date: 2018-05-05 22:56:26
tags:
categories: scala学习
---

介绍几个import相关的特性

* 特性一: 用import com.zhyea._这种格式，可以导入包下所有的成员

* 特性二: 与java不同，scala在任何地方都可以使用import，比如类内、方法内：

```scala
object MyApp {
    def main(args: Array[String]): Unit = {
        import com.zhyea.service._
        val service = new MyService
    }
}
```
这种方式的好处在于，可以在一定作用域范围内使用导入
 
* 特性三: 选择器、重命名、隐藏

 ```scala
import com.zhyea.service.{ MyService } \\仅仅导入com.zhyea.service包下的MyService类，其它不导入

import com.zhyea.service.{ MyService => MyServiceImpl } \\将导入的类进行重命名

import com.zhyea.service.{ MyService => _, _ } \\导入com.zhyea.service包下所有的类，但是隐藏掉MyService类
```
 
* 特性四: 隐式导入

每个scala程序默认都会隐式导入以下几个包下所有的成员

> import java.lang._
import scala._
import Predef._