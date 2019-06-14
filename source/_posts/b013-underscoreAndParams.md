---
title: 下划线和参数
toc: false
date: 2018-05-19 22:01:50
tags: 下划线
categories: scala技巧
---

在Scala里，下划线（_）可以表示函数值的参数。如果某个参数在函数里仅使用一次，就可以用下划线表示。每次在函数里用下划线，都表示随后的参数。

```scala
val arr = Array(1, 2, 3, 4, 5)
println("Sum of all values in array is " + (0 /: arr) { (sum, elem) => sum + elem })
```
上面的代码里使用了“/:”方法。这个方法的意思大致上是从数组的左侧开始迭代运算。这里是用来计算数组中所有元素的和。因为在函数值中sum和elem只用了一次，所以这个函数也可以这样写：

```scala
val arr = Array(1, 2, 3, 4, 5)
println("Sum of all values in array is " + (0 /: arr) {_+_} )
```
其中第一个“_”就是sum，即用来迭代运算的值；第二个“_”表示的是数组中的元素。这里是一处让人比较纠结的地方，这样做会让代码过于简洁，有时甚至让人费解。如果能在不失可读性又足够简洁的地方使用就最好了，来看一下下面的例子：
```scala
val negativeNumberExists = arr.exists { _ < 0 }
println("Array has negative number? " + negativeNumberExists)
```

这里的代码用来查找数组中是否有小于0的元素。

在某些有意义的地方，这种简洁还可以更进一步。比如说我们想从一个数组中找出最大值。从这个需求出发，我们会一步步地简化实现的函数。最开始是这个样子的，我们使用了“/:”方法：

```scala
def max(a: Int, b: Int) = {
  if (a > b)
    a
  else
    b
}
println("Max value in array is " + (Int.MinValue /: arr) { (large, ele) => max(large, ele) })
```

用下划线表示函数值参数来做一次简化：

```scala
println("Max value in array is " + (Int.MinValue /: arr) { max(_, _) })
```

“_”不仅可以表示一个参数，还可以表示整个参数列表。所以可以继续简化成这样：

```scala
println("Max value in array is " + (Int.MinValue /: arr) { max _ })
```

在上面的代码里，“\_”表示整个参数列表。如果只是把参数传递给下层的方法，甚至可以省下使用“_”。上面的代码可以最终简化为：

```scala
println("Max value in array is " + (Int.MinValue /: arr) { max })
```

可以看到，依赖类型推断，scala的简洁到了丧心病狂（无贬义）的程度。这也给开发者留下了一个难题：需要适当克制自己简化代码的欲望，留下适当的可读性，不让代码变成天书。
