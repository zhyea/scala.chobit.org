---
title: 字符串和多行字符串
toc: true
date: 2018-04-25 06:38:43
tags: 字符串
categories: scala学习
---


scala中的字符串类就是java中的java.lang.String类。不过scala也为String提供了一个富封装类：scala.runtime.RichString。  

scala可以将java.lang.String类自动转换为scala.runtime.RichString类。这样就可以非常方便地使用capitalize()、lines()和reverse()等方法相对较便捷的方法。  

在scala里，对于字符串的支持中最显著的一个特性是对于heredoc的支持，也就是对多行文本的支持。在scala里创建多行字符串非常容易，只要将多行字符串放在三个双引号里就行。在下面的代码里创建了一个四行的字符串：  
```scala
var info =
  """
     这里是robin的信息：
          robin的姓名：robin zhang；
          robin的年龄：29；
          robin的个人主页：zhyea.com
  """

println(info)
```
scala会将三个双引号间的内容保持原样输出。所以这样声明的字符串也被称为原始字符串。需要注意一点：在原始字符串中使用转义字符也不会被转义，仍然会原样输出。在scala解释器中演示一下：
```bash
scala> val info =
     | """
     |    My name is "robin" \n
     | Hello World!
     | """
info: String =
"
   My name is "robin" \n
Hello World!
"

scala> println(info)

   My name is "robin" \n
Hello World!
```
我在原始字符串中使用了空格缩进、双引号和转义字符“\n”，在输出的时候这些都原样保留下来了。  

RichString提供的方法中比较常用的是stripMargin。这个方法需要配合管道符“|”一起使用。如果管道符用在多行字符串每行的起始位置，stripMargin()方法会去掉先导管道符“|”前所有的空白或控制字符。如果出现在其他地方，而不是每行的开始，就会保留管道符。看个实例：
```bash
scala> val info =
     | """
     |     |I'm trying to use method stripMargin.
     | """
info: String =
"
I'm trying to use method stripMargin.
"

scala> println(info.stripMargin)

I'm trying to use method stripMargin.

```

如果出于某种原因，管道符号“|”有特殊的用途，可以用stripMargin的重载方法来选择其他字符作为每行字符串的先导字符