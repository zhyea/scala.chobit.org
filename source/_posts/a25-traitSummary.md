---
title: Trait
toc: true
date: 2018-05-28 22:26:54
tags: trait
categories: scala学习
---

## 概述

不知道大家对java的接口是如何理解的。在我刚接触到接口这个概念的时候，我将接口理解为一系列规则的集合，认为接口是对类的行为规范。不过现在又有了想的看法：如果说类是对具体事物的抽象，那么接口则是对类的又一层抽象，在某种程度上扩充了类的适用范围。  

和java的接口比起来，scala的trait可能更具体一些。正如trait这个单词的本意一样，它指的是一种特质：如果认为类有某种特质就混入对应的trait好了。在scala中，类是对现实某一类事物的建模，而Trait则是对已有模型的补充。  

我自己以为，trait这个概念理解起来要比接口更容易一些。

看个例子吧：狗是人类的好朋友，它在我们孤单的时候默默地陪伴。用scala可以这样定义：

```scala

trait HumanFriend {

  val name: String

  def accompany() = println(s"My Friend $name accompanies with me. ")

}

class Dog(val name: String) extends HumanFriend
```

我们先定义了一个名为人类的朋友（HumanFriend）的trait，朋友的主要特征就是陪伴，所以在这个trait里面定义了一个方法accompany并做了实现。Dog（狗）具有人类的好朋友这个特质，所以可以混入trait HumanFriend。在scala中继承某个trait，通常也称为混入某个trait。

在trait HumanFriend中定义了一个val变量name。这个name没有默认值，我们可以认为它是抽象的，可以等到其他类混入这个trait时再为其赋值。

混入trait的类可以直接调用trait的方法：
```scala
  new Dog("tom").accompany()
```

一个类混入trait后，我们可以认为其实例就是trait的引用：
```scala
  val tom: HumanFriend= new Dog("tom")
```

一个trait会被编译为一个接口和一个抽象类。trait中定义的未赋值的val变量会被表示为一个抽象方法。

## 方法重写

混入trait的类可以重写trait的方法：
```scala
class Dog(val name: String) extends HumanFriend {

  override def accompany(): Unit = println(s"$name accompany with me.")
  
}
```
重写时需要使用override关键字。

## 多继承

一个类混入trait的数量是任意的。用关键字**with**就可以混入更多的trait。比如，狗除了是人类的好朋友，还是忠诚的卫士，会保护我们：

```scala
trait Guard {

  val name: String

  def guard() = println(s" $name guards me!")
}

class Dog(val name: String) extends HumanFriend with Guard

object MyTrait extends App {

  val tom = new Dog("tom")

  tom.accompany()

  tom.guard()

}
```

Dog类同时混入了HumanFriend和Guard两个trait，所以它可以调用这两个trait的方法。

## 抽象方法

前面提过，scala中的trait可以理解为是java中接口和抽象类的组合。那么在接口中定义抽象类也是可以的了。现在我们将HumanFriend中的accompany方法改为抽象方法：

```scala

trait HumanFriend {

  val name: String

  def accompany()

}

class Dog(val name: String) extends HumanFriend {

  override def accompany(): Unit = println(s"$name accompanies with me.")

}
```
抽象方法需要在子类中提供实现。子类实现抽象方法时override关键字是可选的，但是建议最好加上。

## 构造顺序

在网上看到了这段关于trait的构造顺序的描述，觉得有点儿意思就顺路转了过来。

trait也可以有构造器，由字段的初始化和其他特征体中的语句构成。这些语句在任何混入该trait的对象在构造时都会被执行。

构造器的执行顺序：

1. 调用超类的构造器；
1. trait构造器在超类构造器之后、类构造器之前执行；
1. trait由左到右被构造；
1. 每个trait当中，父trait先被构造；
1. 如果多个trait共有一个父trait，父trait不会被重复构造
1. 所有trait被构造完毕，子类被构造。

构造器的顺序是类的线性化的反向。