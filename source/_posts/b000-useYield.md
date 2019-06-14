---
title: 使用yield关键字
toc: true
date: 2018-04-28 21:36:56
tags:
categories: scala技巧
---

## 基础用法

使用yield关键字可以存储for循环返回值。每次循环时，返回值都会存储到一个结构中。在循环完成后，会将结构中的所有值一起返回。存储返回值的结构，和循环值的结构一致。也就是说如果循环的是一个数组返回的就是数组，循环的是一个List返回的就是一个List，循环的是一个Map返回的就是Map。  

来看一个例子：
```bash
scala> val result = for {i <- List(1, 2, 3, 4)} yield i
result: List[Int] = List(1, 2, 3, 4)

scala> val result = for {i <- Array(1, 2, 3, 4)} yield i
result: Array[Int] = Array(1, 2, 3, 4)
```

在示例中，我们先后循环了一个List和一个整型数组。循环的返回值也分别是List和整型数组。  

使用yield关键字还可以对返回值做进一步的加工：
```bash
scala> val result = for {i <- Array(1, 2, 3, 4)} yield i*2
result: Array[Int] = Array(2, 4, 6, 8)
```
在使用yield关键字的同时，对每个返回值都做了乘以2的处理。所以最后返回的数组中的每个值都是循环值的2倍。

使用yield关键字还可以修改集合中值的类型，如下：
```bash
scala> val result = for {i <- Array(1, 2, 3, 4)} yield i*2.0
result: Array[Double] = Array(2.0, 4.0, 6.0, 8.0)
```
原来循环的是一个整型数值数组，返回的则是Double值数组。  

这里yield的用法有些类似于集合的map方法，所不同的是map方法是修改集合的原始值，yield则是返回一个新集合。  

## 结合卫述语句

在scala中if语句常被作为卫述语句。卫述语句是指被用来检查关键的先决条件的合法性并在检查未通过的情况下立即终止当前代码块的执行的语句。

yield结合卫述语句可以对循环值进行过滤处理。看一下示例代码：
```bash
scala> val result = for {i <- Array(1, 2, 3, 4) if(i%2==0)} yield i
result: Array[Int] = Array(2, 4)
```
这里使用if语句筛选出了循环的数组中所有的偶数。




