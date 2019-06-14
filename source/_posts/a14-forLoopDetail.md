---
title: for循环详述
toc: true
date: 2018-05-01 13:12:49
tags:
categories: scala学习
---

scala的for循环能做的事情很多，这里简单介绍下。

## 遍历集合元素

示例代码如下：  
```scala
  val list = List(1, 2, 3)

  for (e <- list) {
    println(s"Current value is: $e")
  }
```
这里有些类似于java中的增强循环。代码中先创建了一个List集合，而后使用for循环遍历输出集合中的元素。  

代码中的“e<-list”结构被称为“生成器”，在每次迭代时，变量e会被初始化为List集合中的一个元素。e为val变量。  

scala的for表达式支持所有的集合类型，比如数组、Range、List、Seq等。  

## 过滤

在遍历集合元素时，如不想处理某些元素，可以使用if语句（if语句也被称为卫述语句）来执行过滤。

示例代码如下：
```scala
  for (e <- 0 to 6; if e % 2 == 0) {
    println(s"Current value is: $e")
  }
```
代码中使用生成器“e <- 0 to 6”生成了一个0~6的整型Range；在遍历过程中，使用if语句筛选出了整形序列中所有值为偶数的元素。看下执行结果：
```text
Current value is: 0
Current value is: 2
Current value is: 4
Current value is: 6
```
如果有必要的话，可以添加多个过滤器，只需要再添加一个if语句即可。比如下面的代码过滤出了整型序列中值为偶数，且大于3的元素：
```scala
  for (e <- 0 to 6
       if e % 2 == 0
       if e > 3) {
    println(s"Current value is: $e")
  }
```
结果如下：
```text
Current value is: 4
Current value is: 6
```

## 多重循环

for表达式支持多重循环。下面是个实例：
```scala
  val list = List("com", "zhyea")

  for (e <- list; c <- e) {
    println(s"$c of $e")
  }
```
这段代码一重循环遍历了字符串集合中的每个元素，而后在二重循环中遍历了一个字符串元素的每个字符。看下执行结果：
```text
c of com
o of com
m of com
z of zhyea
h of zhyea
y of zhyea
e of zhyea
a of zhyea
```
上面代码中的两个循环间使用“;”做了分隔，因为下面这种写法在scala中是不被允许的：
```scala
  for (e <- list
    c <- e) {
    println(s"$c of $e")
  }
```
因为scala编译器不会推断在小括号内省略掉的“;”号。要想省略两个生成器中的“;”，可以使用花括号替换for循环的小括号：
```scala
  for {e <- list
       c <- e} {
    println(s"$c of $e")
  }
```
使用花括号和使用小括号是等价的，处理逻辑上并无不同。

## 绑定中间变量

在for循环表达式中，scala允许创建一个变量保存中间值。看下示例代码：
```scala
  val list = List("com", "zhyea")
  for {e <- list
       c <- e
       index = e.indexOf(c)} {
    println(s"Index of $c in $e is $index")
  }
```
在上面的for循环表达式中创建了一个index变量保存每个字符在字符串的索引位置。这里的index是val变量，只是不需要使用val声明。  

如果将index变量移动到for循环的循环体中，则val声明不可省略。

## 生成新集合  

在for循环中可以使用yield关键字返回一个新的集合。通常新的集合的类型和循环的集合类型一致。

这个实例在scala解释器中写比较直观些：
```bash
scala> val nums = for(i<-List("com", "zhyea", "robin")) yield i
nums: List[String] = List(com, zhyea, robin)
```

下面是一个返回值和循环的集合类型不严格一致的例子：
```bash
scala> val nums = 0 to 6
nums: scala.collection.immutable.Range.Inclusive = Range(0, 1, 2, 3, 4, 5, 6)

scala> val nums = for(i<- 0 to 6)yield i
nums: scala.collection.immutable.IndexedSeq[Int] = Vector(0, 1, 2, 3, 4, 5, 6)
```
