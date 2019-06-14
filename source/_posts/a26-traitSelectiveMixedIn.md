---
title: trait选择性混入
toc: true
date: 2018-05-29 21:54:15
tags: trait
categories: scala学习
---


## 描述

上一节有说过如何在类级别混入trait，还举了一个人类的朋友Dog的例子。代码是这样的：

```scala
trait HumanFriend {

  val name: String

  def accompany() = println(s"My Friend $name accompanies with me. ")

}

class Dog(val name: String) extends HumanFriend
```

继续从这个例子展开来。并不是所有的狗都是人类的朋友，比如藏獒，这玩意儿又蠢又笨又凶狠，肯定不能算是很多人的好朋友了。我们更希望在创建一个Dog的实例后，再选择性地为其混入HumanFriend这个特质。也就是在对象级别混入trait。来看下代码：

```scala
trait HumanFriend {

  val name: String

  def accompany() = println(s"My Friend $name accompanies with me. ")

}

class Dog(val name: String)

object MyTrait extends App {

  val tom = new Dog("tom") with HumanFriend

  tom.accompany()

}
```

这次没有为Dog类混入HumanFriend trait，而是在创建Dog的实例tom的时候混入了HumanFriend trait。虽然是在对象级别混入的，Dog实例tom也可以调用HumanFriend的accompany方法。如果此时Dog的实例没有混入HumanFriend，却还是调用了accompany方法，就会无法完成编译。

另外，关于HumanFriend的抽象成员变量name还得提一句。在这个例子里，我们为Dog类也提供了一个成员变量name，所以在为其实例混入HumanFriend trait的时候就没有报错。但是如果一个没有名为name的成员变量的类混入HumanFriend时，就得提供相应的实现了：

```scala
class Lion

object MyTrait extends App {

  val simba = new Lion with HumanFriend {
    override val name: String = "Simba"
  }

  simba.accompany()
}
```

Lion类没有任何成员变量，在为其实例混入HumanFriend trait的时候就得提供抽象成员变量name的具体实现了。

看下执行结果：
```text
My Friend Simba accompanies with me. 
```

## 使用

依赖trait的选择性混入的能力，我们可以很容易地实现功能的增强，或者说是装饰器模式。来看个案例。

假设我们要对一个人进行审查，包括信用记录、收支记录、犯罪记录和工作记录等。但是我们并不会总是都要审查所有的内容，比如要买房时会审查信用记录和收支记录，申请出境时会审查犯罪记录和工作记录。

想想该怎么做：最简单的思路是为每种审查创建一个方法，然后在需要的时候将这些方法组合起来。更常用的方案是创建一个审查接口，为每种审查内容创建一个类，并实现审查方法。为最基础的审查，比如信用记录、收支记录等创建一个基础类还是可行的，但是如果要为每一种复合审查（如买房或申请出境等）都创建一个类就会面临一个问题：类膨胀。scala可以让我们避免这种问题，使用trait混入实例可以实现面对不同的审查提供不同的处理方案这样的灵活性。

首先我们需要定义一个抽象类Checker，实现一些在审查中的通用的行为：

```scala
abstract class Check {
  def check(): String = "Checked Application Details..."
}
```

然后为每一类基础性的审查创建一个trait

```scala
trait CreditCheck extends Check { //信用审查
  override def check(): String = "Checked Credit..." + super.check()
}

trait BalanceCheck extends Check{//收支记录审查
  override def check(): String = "Checked Balance..." + super.check() 
}

trait EmploymentCheck extends Check {//工作记录审查
  override def check(): String = "Checked Employment..." + super.check()
}

trait CriminalRecordCheck extends Check {//犯罪记录审查
  override def check(): String = "Check Criminal Records..." + super.check()
}
```

这段代码里有一个有趣的地方：就是这里的几个trait都继承自一个抽象类。让trait继承抽象类有两个用处：

1. 这些trait可以使用Check的方法；
1. 这些trait只能混入继承自Check的类。

在每个trait里，都重写了check方法，在重写的同时也引用了Check类的方法，这也可以说是对Check类的修饰或增强。

trait里，通过super调用的方法会经历一个延迟绑定的过程。这个调用并不是对父类的调用，而是对其左边混入的trait的调用——如果这个trait已经是混入的最左trait，那么这个调用就会解析成混入这个trait的类的方法。具体如何可以看一下下面的例子是如何实现的。

来看一个买房申请：
```scala
  val apartmentApplication = new Check with CreditCheck with BalanceCheck

  println(apartmentApplication.check())
```

在创建Check实例的同时混入了两个trait：CreditCheck和BalanceCheck。同样的也可以这样实现出境申请：

```scala
  val exitApplication = new Check with CriminalRecordCheck with EmploymentCheck

  println(exitApplication.check())
```

可以看出，如果想要按照不同的组合进行检查的话，只需要按照希望的组合将trait混入即可。

看一下执行结果：

```text
Checked Balance...Checked Credit...Checked Application Details...
Checked Employment...Check Criminal Records...Checked Application Details...
```

可以看到，是从最右的trait开始调用check()方法。然后，顺着super.check()，将调用传递到其左边的trait。最左的trait调用的是实例的check()方法。

在Scala里，trait是一个强有力的工具，可以用它混入横切关注点。使用它们可以以较低的成本创建出高度可扩展的代码。无需创建一个拥有大量类和接口的层次结构，就可以快速地把必要的代码投入使用。