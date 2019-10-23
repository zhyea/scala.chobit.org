---
title: 部分应用函数
toc: false
date: 2018-05-07 06:50:33
tags:
categories: scala学习
---


scala的部分应用函数（也可以成为偏应用函数），是一种特殊的表达式。使用部分应用函数让我们在调用一个方法时可以不必提供全部的参数，只需要提供部分参数，或者完全不提供参数。

先来看一段代码：
```scala
import java.util.Date

object PartialApplyFunc {

  def main(args: Array[String]): Unit = {
    
    val date = new Date()

    log(date, "message1")
    log(date, "message2")
    log(date, "message2")
  }

  def log(date: Date, message: String): Unit = {
    println(s"$date : $message")
  }

}
```
在这段代码中，连续调用了log方法三次，每次传入的参数中date是相同的，message是不同的。  
要避免每次都输入这个相同的参数date可以考虑使用部分应用函数。部分应用函数要解决的问题就是：在一个比较小的作用域内连续调用一个已有方法或函数时，避免重复输入相同的参数。  

用部分应用函数重构下刚才的代码：
```scala
import java.util.Date

object PartialApplyFunc {

  def main(args: Array[String]): Unit = {

    val date = new Date()

    val logWithDateBound = log(date, _: String) //需要提供的参数必须使用通配符，不可用参数名

    logWithDateBound("message1")
    logWithDateBound("message2")
    logWithDateBound("message2")
  }

  def log(date: Date, message: String): Unit = {
    println(s"$date : $message")
  }

}
```

重构后的代码中定义了一个logWithDateBound函数，并为这个函数绑定了第一个date参数，然后用下划线(\_)替换缺失的参数列表。

看下执行结果：
```text
Mon May 07 07:29:05 CST 2018 : message1
Mon May 07 07:29:05 CST 2018 : message2
Mon May 07 07:29:05 CST 2018 : message2
```
通过反编译后的代码查看下logWithBound的类型：
```java
    Function1 logWithDateBound = new AbstractFunction1() { 
      public static final long serialVersionUID = 0L;
      private final Date date$1;
      public final void apply(String x$1) { PartiallyFunc..MODULE$.log(this.date$1, x$1); }
    };
```

在反编译后的代码中看到logWithDateBound的类型是Function1，apply方法参数类型为String，可知logWithDateBound函数是一个类型为(String)=>Unit函数值。

再来总结一次：部分应用函数是在已有函数的基础上，提供部分默认参数，未提供默认参数的地方使用“\_”替代，从而创建出一个函数值，在使用这个函数值（部分应用函数）的时候，只需提供“\_”部分对应的参数即可。

关于部分应用函数还可以多想想的一点就是与在函数或方法中指定参数默认值的区别。

部分应用函数的作用就体现在“部分”两个子上。定义部分应用函数时，提供全部参数或者是不提供任何参数都有画蛇添足的嫌疑。