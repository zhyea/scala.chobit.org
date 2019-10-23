---
title: Curry化
toc: true
date: 2018-05-18 07:11:16
tags: [柯里化,Curry化]
categories: scala学习
---

## 概述

Curry化也称柯里化。以Haskell发明人 Haskell Curry 命名。

scala里的curry化可以把函数从接收多个参数转换成接收多个参数列表。也就是说我们写的多参数函数不再只是这样子的：
```scala
def foo(a: Int, b: Int, c: Int) {???}
```
而是可以这样子的：
```scala
foo(1)(2)(3)
```
或者这样的：
```scala
foo(1){2}{3}
```
甚至是这样子的：
```scala
foo{1}{2}{3}
```

来看个实例好了，下面的代码中定义了一个inject方法。inject方法有三个参数，两个Int型的参数，一个函数值参数。方法体中通过传入的函数对前两个Int型参数进行运算：
```scala
  def inject(a: Int, b: Int, op: (Int, Int) => Int): Int = {
    op(a, b)
  }

  inject(2, 3, (a, b) => a + b)
```
在代码的最后一行还写了调用inject方法的一个示例。

有的时候可能会觉得把普通参数和函数值参数放在一起有点儿怪怪的。现在用curry化的方法将函数值参数独立开来：
```scala
  def inject(a: Int, b: Int)(op: (Int, Int) => Int): Int = {
    op(a, b)
  }

  inject(2, 3) { (a, b) => a + b }
```
可以看到不管是在定义方式还是在使用方式上，代码都清爽了许多。

## 分析

从方法的角度来看，Curry化只是一个语法糖，对方法的功能没有任何影响。    

将方法转为函数使用时，Curry化的影响就出现了。在scala解释器里看一下：
```bash
scala> def inject(a: Int, b: Int, op: (Int, Int) => Int): Int = {
     |     op(a, b)
     |   }
inject: (a: Int, b: Int, op: (Int, Int) => Int)Int

scala> inject _
res0: (Int, Int, (Int, Int) => Int) => Int = <function3>
```
我们在解释器里def了没有做Curry化的inject方法，并用“_”将inject方法转为函数。此时inject方法转成的函数是一个有三个参数的普通函数。  

再来看看Curry化后的inject方法转成的函数是怎样的：
```bash
scala>   def inject(a: Int, b: Int)(op: (Int, Int) => Int): Int = {
     |     op(a, b)
     |   }
inject: (a: Int, b: Int)(op: (Int, Int) => Int)Int

scala> inject _
res1: (Int, Int) => ((Int, Int) => Int) => Int = <function2>
```

将两次生成的函数值对象res0和res1放在一起比较一下：
```bash
Curry化之前： res0: (Int, Int, (Int, Int) => Int) => Int = <function3>
Curry化之后： res1: (Int, Int) => ((Int, Int) => Int) => Int = <function2>
```

inject方法Curry化之前转成的函数res0是一个三参数函数，继承自trait Function3；Curry化之后转成的函数res1是一个双参数函数，继承自trait Function2。  

用ABC来表示下inject方法的参数也许更直观些。inject方法最初为：inject(A, B, C)； curry化以后的结构是为：inject(A, B)(C)。inject方法curry化以后对应的函数res1为：
> (A, B)=> (C)=> Int

可以看到res1实质上是被分解多个函数，inject方法的每个参数列表都对应一个函数，几个函数按参数列表顺序从右到左逐层嵌套，最左侧的参数列表即为最外侧的函数的参数。  

以上的内容说的是对方法进行Curry化。Scala也支持对函数进行Curry化。scala的多参数函数trait，包括Function2 ~ FuntionN都有一个curried方法，用来将函数的每个参数进行curry化。下面的代码是Function3的curried方法的实现：
```scala
  def curried: T1 => T2 => T3 => R = {
    (x1: T1) => (x2: T2) => (x3: T3) => apply(x1, x2, x3)
  }
```
在curried方法中，Function3的每个参数都被拆分成一个独立的参数列表，每个参数列表对应的函数逐层嵌套形成了新的函数。

调用下前面的函数res1的curried方法：
```bash
scala> res1.curried
res2: Int => (Int => (((Int, Int) => Int) => Int)) = <function1>
```
res1调用curried方法后生成的新函数res2类型为Function1，并且函数体又多了一层嵌套。

## 总结

Curry化的本质是将多参数函数转为多个单参数函数的级联，以便于进行lambda运算——这是官方的说法。  

说下自己的理解：Curry化更多的情况下是一种语法糖而已，和部分应用函数一样，在scala中并不是一种支撑性的角色。我们可以将多个单参数函数组合在一起，也可以使用函数递归来解决一些特定的问题，但是不必要将这种用法称为curry化。当然，这只是我的一偏之见。如有不同意见，欢迎一起讨论。

