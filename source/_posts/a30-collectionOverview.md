---
title: scala集合概述
toc: true
date: 2018-06-08 22:28:19
tags: [集合,List,Set,Map]
categories: scala学习
---

## 可变与不可变

scala的集合包括Set、List和Map。三种集合的特征和Java中的一样。scala为每种集合都提供了可变和不可变两种版本，分别位于scala.collection.mutable或scala.collection.immutable包下。scala建议使用尽量使用不可变集合，尤其是在多线程环境下。scala默认的集合就是不变的版本。

可变集合没什么好说的，java中的集合就是。来看一个不可变集合的实例：
```scala
  val c1 = Set("Blue", "Green", "Red")
  var c2 = c1

  c2 += "Black"

  println("c2: " + c2)
  println("c1: " + c1)
```

代码中先创建了一个不可变的Set实例并将之赋给一个val变量c1。而后又将immutable Set实例通过c1赋给了var变量c2。c2调用了immutable Set的“+”方法，“+”方法的作用是在原集合的基础上添加元素并创建一个新的实例。所以现在c2是另一个Set实例了。看下程序输出结果：
```text
c2: Set(Blue, Green, Red, Black)
c1: Set(Blue, Green, Red)
```

## 集合的使用

主要是看一下集合的几个常用方法。至于其他的方法有机会再单独解释。

### Set

#### filter()方法
filter()方法用来从Set中过滤获取含有指定特征的元素。示例代码如下：

```scala
val colors1 = Set("Blue", "Green", "Red", "yellow")
val filteredSet = colors1 filter (_ contains "l")
println(filteredSet mkString ",")
```
上面的代码从一个Set中过滤出了含有字符“l”的元素，而后调用mkString方法将过滤结果拼接打印出来。看一下结果：

```text
Blue,yellow
```

#### ++()方法
++()方法用来拼接两个Set。示例代码如下：
```scala
val colors1 = Set("Blue", "Green", "Red", "yellow")
val colors2 = Set("White", "Black")

val combinedSet = colors1 ++ colors2
println(combinedSet mkString ",")
```
看一下结果：
```text
Red,Blue,White,Black,Green,yellow
```

#### map()方法
map()方法可以对Set的每个元素进行处理，最后返回一个新的集合。示例代码如下：

```scala
  val colors1 = Set("Blue", "Green", "Red", "yellow")

  val colorSet = colors1.map("color is : " + _)

  colorSet.foreach(println)
```

这里打印Set元素时使用了内置的迭代器foreach。执行结果：
```text
color is : Blue
color is : Green
color is : Red
color is : yellow
```

### Map
#### filterKeys()方法
filterKeys()方法用来对Map的key进行过滤，示例代码如下：
```scala
val roster = Map("Jack" -> "Solider", "Jane" -> "Teacher")
val filteredMap = roster.filterKeys.(_ contains ("Jac"))
println("Filtered Size : " + filteredMap.size)
```

执行结果如下：
```text
Filtered Size : 1
```

#### filter()方法
也可以使用filter()方法来执行过滤，不过filter()方法中的函数值操作的对象是Key-Value对。看一下代码：

```scala
  val roster = Map("Jack" -> "Solider", "Jane" -> "Teacher")
  val filteredMap = roster.filter {
    ele =>
      val (k, v) = ele
      k contains ("Jac")
  }
  println("Filtered Size : " + filteredMap.size)
```
执行结果和上面的filterKeys方法是一样的，就不重复贴了。

#### get()方法
get()方法用来从Map中取值，不过返回值的类型是Optional[T]，示例代码如下：
```scala
val roster = Map("Jack" -> "Solider", "Jane" -> "Teacher")
println("Jack" +  " is a " + (roster.get("Jack").get )
```
说实话，我不太喜欢Optional，还好scala不需要让我忍受这个东西，它还提供了另一种更简洁的获取kv对的方式：
```scala
val roster = Map("Jack" -> "Solider", "Jane" -> "Teacher")
println("Jack" +  " is a " + roster("Jack") )
```
在上面的代码里我们直接在Map对象后面使用了一个括号来取值。这是使用了Map的一个工厂方法apply。

#### update()方法
update()方法可以用来“更新”Map对象。因为我们用的Map是一个immutable Map的对象，它的“更新”并不会影响当前的对象，而是返回一个新的对象。
```scala
val roster = Map("Jack" -> "Solider", "Jane" -> "Teacher")
val updatedRoster = roster.updated("Jane", "Nurse")
println("Jane" +  " is a " + roster("Jane") )
println("Jane" +  " is a " + updatedRoster("Jane") )
```
执行结果如下：
```text
Jane is a Teacher
Jane is a Nurse
```

### List
在scala中Set和Map都有可变和不变两种实现，但是List只有不变的实现。scala中的List实现有些类似于java的LinkedList。scala的List的大部分操作都是围绕head和tail执行的。

如下是一个List使用的示例，我们要访问List示例的第一个元素可以使用list.head()方法也可以直接执行list(0)：
```scala
val list1 = List("Red", "Yellow", "Blue")
println(list1.head)
println(list1(0))
```

执行结果是：
```text
Red
Red
```

#### ::()方法
这个方法用于在List前添加元素。a :: list可以读作“在List前添加a”。示例代码如下：
```scala
val list1 = List("Red", "Yellow", "Blue")
val list2 = "Black"::list1
println(list1.mkString(","))
println(list2.mkString(","))
```
执行结果如下：
```text
Red,Yellow,Blue
Black,Red,Yellow,Blue
```

#### :::()方法
这个方法用于将一个List实例添加到另一个List实例前面。list ::: listA 可以读作“在listA前添加list”。示例代码如下：

```scala
  val list1 = List("Red", "Yellow", "Blue")
  val list2 = "Black" :: list1
  val combinedList = list2 ::: list1

  println(list1.mkString(","))
  println(list2.mkString(","))
  println(combinedList.mkString(","))
```
执行结果如下：

```text
Red,Yellow,Blue
Black,Red,Yellow,Blue
Black,Red,Yellow,Blue,Red,Yellow,Blue
```

请注意：以上两个List的方法操作对象都是操作符后的List实例。

#### forall()和exists()
用forall()方法可以检查是否所有元素都满足某一特定条件。而想了解是否存在某些元素满足特定条件，可以用exists()。示例代码如下：
```scala
val list1 = List("Red", "Yellow", "Blue")
println(list1 forall (_ contains ("R")))
println(list1 exists (_ contains ("R")))
```
执行结果如下：

```text
false
true
```