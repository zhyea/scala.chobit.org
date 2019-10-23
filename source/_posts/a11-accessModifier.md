---
title: 访问修饰符
toc: true
date: 2018-04-29 06:38:56
tags:
categories: scala学习
---


先简单介绍下scala访问修饰符的几个特性，稍后再详细解释：  

* 如果不指定访问修饰符，默认为public；
* 较之Java，scala对protected的定义更加严格；
* scala可以对可见性进行细粒度的控制。

## 默认访问修饰符

如果没有修饰符，scala会默认把类、字段、方法的访问修饰符当做public。如果要将之调整为private或protected，只需在前面添加对应的修饰符关键字即可。就如下面的程序：

```scala
class Microwave{
  def start() = println("started")
  def stop() = println("stopped")
  private def turnTable() = println("turning table")
}
val microwave = new Microwave
microwave.start()

microwave.turnTable()//这里错了
```

在上面的代码里start和stop两个方法默认为public类型，可以通过任意Microwave实例访问。turnTable被显示定义为private，这样就不能在Microwave类外部访问它。执行这段代码，就会如注释处声明的一样，会在该处报错：
```text
 method turnTable in class Microwave cannot be accessed in com.zhyea.MyApp.Microwave
  microwave.turnTable()
```

## protected修饰符

在scala里，用protected修饰的成员只对本类和派生类可见，同一个包内的其他的类不可见。而且派生类只可以访问本类实例的protected成员。  
可以通过一个例子看一下：

```scala
package com.zhyea.auto

class Vehicle {
  protected def checkEngine() = println("checked engine")
}


class Car extends Vehicle {

  def start() {
    checkEngine()
  }

  def tow(car: Car) {
    car.checkEngine()
  }

  def tow(vehicle: Vehicle) {
    vehicle.checkEngine() //会报错
  }
}


class GasStation {
  def fillGas(vehicle: Vehicle) {
    vehicle.checkEngine() //会报错
  }

  def fillGas(car: Car) {
    car.checkEngine() //会报错
  }
}
```

在这段代码里，Vehicle的checkEngine()方法是protected型的，Car是Vehicle类的派生类。

根据scala对protected修饰符的要求：

* 在Car类自定义方法start()中访问父类Vehicle的checkEngine()方法不会报错；
* 在Car类自定义方法tow()中通过Car类实例访问父类Vehicle的checkEngine()方法不会报错；
* 在Car类自定义方法tow()中通过Vehicle类实例访问Vehicle的checkEngine()方法会报错；
* 在同一个包内的GasStation类中通过Car或Vehicle实例访问checkEngine()方法会报错

这些错误在编译期就会报出：

```text
method checkEngine in class Vehicle cannot be accessed in com.zhyea.auto.Vehicle
 Access to protected method checkEngine not permitted because 
 prefix type com.zhyea.auto.Vehicle does not conform to class Car in package auto where the access take place
    vehicle.checkEngine() //会报错
    
method checkEngine in class Vehicle cannot be accessed in com.zhyea.auto.Vehicle
 Access to protected method checkEngine not permitted because
 enclosing class GasStation in package auto is not a subclass of 
 class Vehicle in package auto where target is defined
    vehicle.checkEngine() //会报错

method checkEngine in class Vehicle cannot be accessed in com.zhyea.auto.Car
 Access to protected method checkEngine not permitted because
 enclosing class GasStation in package auto is not a subclass of 
 class Vehicle in package auto where target is defined
    car.checkEngine() //会报错
```

## 细粒度访问控制

scala对待protected比Java更加严格，但另一方面它提供了更多的灵活性和更细粒度的访问规则。

使用private和protected时可以指定额外的参数，使用方式如：private[AccessQualifier]。AccessQualifier可以是this，也可以是其它的类名或包名。  
对于修饰符private[AccessQualifier]可以这样理解：除了自己和AccessQualifier所表示范围内的类，该修饰符修饰的成员对其他所有类的可见性都是private。并且这个概念也是可以递推的，也就是说，如果AccessQualifier是一个类，那么private成员对于AccessQualifier的AccessQualifier也是可见的。

看一个细粒度访问控制的例子：
```scala
package society {

  package professional {

    class Executive {
      private[professional] var workDetails = null
      private[society] var friends = null
      private[this] var secret = null

      def help(another: Executive) {
        println(another.workDetails)
        println(this.secret)
        println(another.secret) //会报错
      }
    }

  }

  package social {

    class Acquaintance {
      def socialize(person: professional.Executive) {
        println(person.friends)
        println(person.workDetails) //会报错
      }
    }

  }
}
```
这段代码里面有一个嵌套包的使用：可以看到，在society包里又声明了professional和social两个包。而且使用包的方式也和常见的不一样：一般的包声明是点号分隔，文件头声明的方式。

在类Executive的定义中，三个私有成员变量有着不同的作用范围。workDetails对包professional内的类可见，friends对society包内的类可见，而secret只对当前实例可见。

所以编译这段代码，在标记报错的地方会报错：
```text
value secret is not a member of com.zhyea.society.professional.Executive
        println(another.secret) //会报错

value workDetails in class Executive cannot be accessed in com.zhyea.society.professional.Executive
        println(person.workDetails) //会报错
```

Executive的成员secret只对当前实例（this）可见，对Executive的其他实例不可见。workDetails对包professional内的类可见，对society包内的类不可见。因此会报错。
