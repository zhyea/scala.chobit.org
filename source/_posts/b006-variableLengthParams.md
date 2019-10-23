---
title: 可变长度参数
toc: false
date: 2018-05-02 21:53:46
tags:
categories: scala技巧
---

scala支持可变长度参数。在scala中，可变长度参数的特征如下：
* 在参数列表中处于最后面；
* 标识方法为在参数类型后放一个星号。

在scala解释器中写了一个示例：
```bash
scala> def foo(str:String*) = str.foreach{println}
foo: (str: String*)Unit

scala> foo("com", "zhyea", "robin")
com
zhyea
robin
```
在代码中定义了一个foo方法，它的参数长度就是可变的，方法体的定义是逐行输出每个参数。在随后的验证代码中可以看到结果和预期是一致的。  

我们知道，在java中，变长参数是被作为数组来处理的。至于在scala中是否也是一样，则需要进行验证。  

对刚才的示例代码做了些调整来进行验证：
```bash
scala> def foo(str: String*) = println(str.getClass)
foo: (str: String*)Unit

scala> foo("zhyea")
class scala.collection.mutable.WrappedArray$ofRef

scala> foo("com", "zhyea")
class scala.collection.mutable.WrappedArray$ofRef

scala> def foo(nums:Int*) = println(nums.getClass)
foo: (nums: Int*)Unit

scala> foo(1, 2, 3)
class scala.collection.mutable.WrappedArray$ofInt
```

可以看到，在scala中变长参数的类型是scala.collection.mutable.WrappedArray的ofXXX子类，并不是一个数组结构。

再来看看给变长参数的方法传递一个数组结构会有什么样的反应：
```bash
scala>  def foo(str: String*) = println(str.getClass)
foo: (str: String*)Unit

scala> foo(Array("com", "zhyea", "robin"))
<console>:13: error: type mismatch;
 found   : Array[String]
 required: String
       foo(Array("com", "zhyea", "robin"))

```
可以看到报错了：类型不匹配，需要的是String，传递是String数组。  

如果我们确实需要把一个类型匹配的数组传递给这个方法，此时我们可以使用“:\_*”标识符。“:\_*”作为一个整体，告诉编译器你希望将某个参数当作参数序列处理。  

看下演示：
```bash
scala> def foo(nums:Int*) = nums.foreach(println)
foo: (nums: Int*)Unit

scala> foo(Array(1,2,3) :_*)
1
2
3
```
注意一下，“:\_\*”不只是能标识数组，还可以标识其他的集合，比如List、Seq、Range等。
```bash
scala> foo(1 to 3 :_\*)
1
2
3
```
“1 to 3”是一个Range结果，使用“:\_\*”标识后依然可以被变长参数方法调用。