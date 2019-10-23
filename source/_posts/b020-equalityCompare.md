---
title: 判断相等：eq()、equal()和==
toc: true
date: 2019-10-23 12:17:48
tags: [eq,equal,相等,等号]
categories: scala技巧
---


scala中提供了三个常用的判断相等的方法，分别是eq()、equal()和==。其中eq()和equal()两个方法是判断相等与否的具体实现，而“==”是对二者的封装。  

看下scala API的定义：

> final def eq(arg0: AnyRef): Boolean
> Tests whether the argument (that) is a reference to the receiver object (this).

> def equals(arg0: Any): Boolean
> The equality method for reference types.

> final def ==(arg0: Any): Boolean
> The expression x == that is equivalent to if (x eq null) that eq null else x.equals(that).

从API定义可以看出：

1. eq()方法是检查**引用**是否相等（是否是同一个引用对象）
1. equal()方法是检查**值**是否相等
1. “==”方法通常都是调用`equals()`方法，只有在比较的对象是null的情况下，才会调用会调用`eq()`方法
1. eq()方法和“==”方法都是**final**方法，不可以被重写，而equals()方法可以被重写

看一个曾经举过的例子：


```scala
val str1 = "hello"
val str2 = "hello"
val str3 = new String("hello")

println(str1 == str2) // Equivalent to Java's str1.equals(str2)
println(str1 eq str2) // Equivalent to Java's str1 == str2
println(str1 == str3)
println(str1 eq str3)
```
如下是执行结果：

```shell
true
true
true
false
```

根据执行结果可以验证如上的说法。

不过存在一些例外的情况：如果是进行数组（Array）对象的比较，希望判断两个数组的值是否相等，使用equals()方法可能无法得到预期的结果，此时需要使用sameElement()方法。

看个示例：

```shell
scala>

scala> val arr1 = Array("a", "b", "c")
arr1: Array[String] = Array(a, b, c)

scala> val arr2 = Array("a", "b", "c")
arr2: Array[String] = Array(a, b, c)

scala> arr1 == arr2
res1: Boolean = false

scala> arr1.equals(arr2)
res0: Boolean = false

scala> arr1.sameElements(arr2)
res3: Boolean = true
```

比较了两个元素一致的数组，使用“==”或equals()方法均会返回false，只有使用sameElements方法进行比较才会返回true。

List, Set, Map对象不存在类似的问题，直接使用“==”或equals()方法进行比较即可，使用sameElements()方法时，编辑器甚至还会建议使用equals()方法替换。

看个相同值Map对象比较的示例：

```shell
scala> val m1 = Map("a"->"1", "b"->"2")
m1: scala.collection.immutable.Map[String,String] = Map(a -> 1, b -> 2)

scala> val m2 = Map("a"->"1", "b"->"2")
m2: scala.collection.immutable.Map[String,String] = Map(a -> 1, b -> 2)

scala> m1 == m2
res4: Boolean = true

scala> m1.equals(m2)
res5: Boolean = true

scala> m1.sameElements(m2)
res6: Boolean = true

scala> val m3 = Map(1->"a", 2->"b")
m3: scala.collection.immutable.Map[Int,String] = Map(1 -> a, 2 -> b)

scala> val m4 = Map(1->"a", 2->"b")
m4: scala.collection.immutable.Map[Int,String] = Map(1 -> a, 2 -> b)

scala> m3 == m4
res7: Boolean = true
```
这里先后比较了key类型为字符串和整型数字的Map，返回值均和预期一致。

比较有趣的是sameElements()方法还可以进行“跨类型”对象的比较，比如Array对象和Set对象的比较：

```shell
scala> val a1 = Array("a", "b", "c")
a1: Array[String] = Array(a, b, c)

scala> val s1 = Set("a", "b", "c")
s1: scala.collection.immutable.Set[String] = Set(a, b, c)

scala> a1 sameElements s1
res8: Boolean = true

scala> val s2 = Set("a", "c", "b")
s2: scala.collection.immutable.Set[String] = Set(a, c, b)

scala> val s3 = scala.collection.mutable.TreeSet("a", "c", "b")
s3: scala.collection.mutable.TreeSet[String] = TreeSet(a, b, c)

scala> println(s1)
Set(a, b, c)

scala> println(s2)
Set(a, c, b)

scala> println(s3)
TreeSet(a, b, c)

scala> a1 sameElements s2
res9: Boolean = false

scala> a1 sameElements s3
res14: Boolean = true
```
虽然Set是一个“无序”集合，但只要最终输出顺序和Array一致，使用sameElements方法进行比较就会返回true。