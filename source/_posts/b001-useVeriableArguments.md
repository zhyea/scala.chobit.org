---
title: 使用变长参数
toc: true
date: 2018-04-28 21:37:35
tags:
categories: scala技巧
---


在Java中是可以使用变长参数的，如下面的方法：
```java
public void check(String... args) {
    for (String tmp : args) {
        System.out.println(tmp);
    }
}
```
在scala中也可以使用变长参数。和java一样，也是只有最后一个参数可以接收可变长度的参数。使用方式是在参数类型后使用特殊符号“*”，如下面的max()方法：
```scala
def max(values: Int*) = values.foldLeft(0) {
    java.lang.Math.max
}
```
foldLeft是TraversableOnce类的方法。上面的代码实现了一个从一组整型数值中获取最大值的方法。  

不过和java不同的是，我们不能将一个数组中的元素当做变长参数直接传递给方法，来个测试案例看看：  

```bash
scala> def max(values: Int*) = values.foldLeft(0) {Math.max}
max: (values: Int*)Int

scala> println(max(2, 5, 3, 7, 1, 6))
7

scala> println(max( Array(2, 5, 3, 7, 1, 6)))
<console>:13: error: type mismatch;
 found   : Array[Int]
 required: Int
       println(max( Array(2, 5, 3, 7, 1, 6)))

```
可以看到在给max方法传递多个整型值作为参数时可以正常执行。但是将一个整型数组作为参数就报错了。  

如果想使用数组中的元素作为变长参数，可以将数组展开成为离散值：这里需要使用“:_*”符号组合。测试看看：
```bash
scala> println(max( Array(2, 5, 3, 7, 1, 6):_*))
7
```
这次执行成功了。