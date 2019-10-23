---
title: 偏函数
toc: true
date: 2018-05-08 06:46:47
tags: 偏函数
categories: scala学习
---


## 概述 

scala的部分应用函数英文名为Partial Apply Function。此外，还有一个英文名为Partial Function的概念，即偏函数。

普通的函数会处理输入参数的全量的值，而偏函数只会处理输入参数中部分特定的值。  

偏函数在实现上有如下特征：  

* 需要显示声明类型，类型为PartialFunction[-A, +B]，其中A为输入类型，B为输出类型；
* 输入参数只有一个，且输入类型已在PartialFunction的类型声明中指出，所以在声明偏函数的时候不需要像声明普通函数一样指明参数列表；
* 通常由包围在花括号中的一个或多个case语句构成，且花括号不可省略。

下面是一个偏函数的示例：

```scala
object MyFunc {

  val find: PartialFunction[Int, String] = {
    case num if num > 5 => "It's Target."
  }

  def main(args: Array[String]): Unit = {
    println(find(6))
    println(find(3))
  }

}
```

上面的代码中定义了一个偏函数find，这个函数只处理大于5的值。执行代码find(6)可以正常处理，执行find(3)会报scala.MatchError：

```text
It's Target.
Exception in thread "main" scala.MatchError: 3 (of class java.lang.Integer)
	at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:248)
	at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:246)
	at com.zhyea.MyFunc$$anonfun$1.applyOrElse(MyFunc.scala:5)
	at com.zhyea.MyFunc$$anonfun$1.applyOrElse(MyFunc.scala:5)
```

## 组合运算

scala提供了几个组合方法可以将两个偏函数组合在一起使用。常用的组合方法有：orElse、andThen和compose，现在分别看一下。

### orElse

orElse方法是在trait PartialFunction中定义的方法。使用orElse方法，可以在无法匹配一个偏函数的时候继续尝试匹配下一个。下面是一个使用orElse组合偏函数判断奇偶的例子：
```scala
object MyFunc {

  val findOdd: PartialFunction[Int, String] = {
    case num if num % 2 != 0 => s"$num is odd."
  }

  val findEven: PartialFunction[Int, String] = {
    case num if num % 2 == 0 => s"$num is even."
  }

  val check = findEven orElse findOdd

  def main(args: Array[String]): Unit = {
    print(check(9))
    print(check(10))
  }

}
```
两个偏函数使用orElse方法组合后形成一个新的偏函数，并赋值给变量check。

代码中的check函数的作用等同于如下的代码：
```scala
  def check(num: Int) = {
    num match {
      case _ if (num % 2 == 0) => s"$num is even."
      case _ if (num % 2 != 0) => s"$num is odd."
    }
  }
```

程序的执行结果如下：
```text
9 is odd.
10 is even.
```

使用orElse方法还可以直接组合case语句：
```scala
  def check = findOdd orElse {
    case num if num % 2 == 0 => s"$num is even."
  }
```
这里的case语句被scala直接包装为偏函数来处理了。

### andThen

如果说orElse方法是将两个偏函数并联在一起，那么andThen方法就是将两个偏函数串联在了一起。经过andThen串联后，上一个函数的输出内容即是下一个函数的输入内容。最初的输入参数必须能够匹配所有被andThen串联在一起的偏函数，否则就会报scala.MatchError错误。

来看一个实例：
```scala
object MyFunc extends App {

  val findOdd: PartialFunction[Int, Int] = {
    case num if num % 2 != 0 => println(s"$num is odd."); num
  }

  val findGreaterThanTen: PartialFunction[Int, String] = {
    case num if num > 10 => s"$num is greater than 10."
  }

  def check = findOdd andThen findGreaterThanTen

  println(check(17))
  println(check(7))

}
```

代码中的两个偏函数被andThen方法串联起来构成了check函数，check函数要求输入的整型数值满足findOdd即为奇数，且满足findGreaterThanTen即大于10。  

在验证部分输入了两个数值17和7。为check函数输入17可以正常执行，输入7则会在执行到findGreaterThanTen时报错：
```text
17 is odd.
17 is greater than 10.
7 is odd.
scala.MatchError: 7 (of class java.lang.Integer)
	at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:248)
	at scala.PartialFunction$$anon$1.apply(PartialFunction.scala:246)
	at com.zhyea.MyFunc$$anonfun$2.applyOrElse(MyFunc.scala:9)
	at com.zhyea.MyFunc$$anonfun$2.applyOrElse(MyFunc.scala:9)
```

andThen方法并不是只在trait PartialFunction中定义了，而是继承自trait Function1并重写的。所以也可以使用andThen方法串联一个普通函数——只要输入类型匹配即可。看个实例：
```scala
  val findOdd: PartialFunction[Int, Int] = {
    case num if num % 2 != 0 => println(s"$num is odd."); num
  }

  val func = (num: Int) => s"$num hasn't been checked."

  def check = findOdd andThen func

  println(check(17))
  println(check(7))
```

执行后输出结果如下：
```text
17 is odd.
17 hasn't been checked.
7 is odd.
7 hasn't been checked.
```
同样的andThen也可以组合case语句。不过懒得再写了， 有兴趣的话可以自己试试看。

### compose

compose方法也是trait PartialFunction继承自Function1的。PartialFunction甚至没有override这个方法。  

compose方法的作用的也是将两个偏函数串联在一起。不过compose串联的方向和andThen串联的方向相反。  

再用compose串联一下前面创建的findOdd和findGreaterThanTen两个偏函数：
```scala
object MyFunc extends App {

  val findOdd: PartialFunction[Int, Int] = {
    case num if num % 2 != 0 => println(s"$num is odd."); num
  }

  val findGreaterThanTen: PartialFunction[Int, Int] = {
    case num if num > 10 => println(s"$num is greater than 10."); num
  }

  def check = findOdd compose findGreaterThanTen

  println(check(17))

}
```

看下输出结果：
```text
17 is greater than 10.
17 is odd.
17
```

通过结果可以看出来使用compose组合后，先执行的是findGreaterThanTen函数。使用andThen组合时，先执行的则是findOdd函数。从trait Function1的定义中可以很清楚地看清这两个方法的区别：
```scala
 def compose[A](g: A => T1): A => R = { x => apply(g(x)) }

 def andThen[A](g: R => A): T1 => A = { x => g(apply(x)) }
```

一个是apply(g(x))、一个是g(apply(x))，正好是相反的。

因为compose是在trait Function1实现的，所以它也可以用来组合普通函数。同样因为PartialFunction继承自Function1，所以compose也可以组合case语句。

## 总结

简单说下我对偏函数的理解：如果说普通函数是一个细粒度的操作，那么偏函数又对一个操作做了更细粒度的切分——将一个操作的不同分支切分了出来。   

示例中使用的几个函数都比较简单，所以使用组合方法组合显得有些意义不大。但是假想一个函数的业务比较复杂时，而又存在多个分支，如果这些分支的业务都写在一个函数内，那就不如使用偏函数恰当了。