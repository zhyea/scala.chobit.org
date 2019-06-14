---
title: 单例、独立对象和伴生对象
toc: true
date: 2018-04-27 21:37:43
tags: [单例,对象,伴生对象]
categories: scala学习
---

## scala单例

单例模式大家都很熟悉了，简单地说就是使用单例模式的类一个类只能有一个实例。  

在scala中创建单例对象非常简单，创建类时使用object关键字替换class即可。使用object声明的单例类无法初始化，所以不能向它的主构造函数传递参数。  

使用object的时候可以直接用“object名.方法名”这样的形式来调用object中定义的方法。因此感觉着scala的单例类从某种意义上更像Java中只提供静态方法的工具类。  

再来用Scala实现一个工具类Math，在这个类中提供一个计算绝对值的方法abs()：
```scala
object Math {

  def abs(num: Int): Int = {
    if (num < 0) -num else num
  }

}
```
使用时确实很像java的静态方法：
```scala
Math.abs(-1)
```

接下来将这个Math类生成的class文件转译为Java类，看看否和猜想的一样：对应的Java类是一个只提供静态方法的类。

编译Math类后看到生成了两个文件Math$.class和Math.class。看样子scala一个object类对应着两个Java类。  
用jd-gui反编译查看生成的Math$.class：
```java
public final class Math${

  public static final  MODULE$;

  static{
    new ();
  }

  public int abs(int num){
    return num < 0 ? -num : num;
  }

  private Math$(){
    MODULE$ = this;
  }
}
```
可以看出这里就是一个常见的单例类的定义。  
再来看看Math.class反编译出来的内容：
```java
public final class Math{
  public static int abs(int paramInt){
    return Math..MODULE$.abs(paramInt);
  }
}
```
这里倒是一个工具类了，在这个工具类中是调用了Math$类的单例提供具体方法的实现。  

从反编译出来的内容看来：scala的object中包含了一个单例对象，而不仅仅是一个提供了静态方法的工具类定义。  

既然提到了静态方法就不妨多说两句：scala中没有静态方法和静态成员，静态成员会破坏scala所支持的完整的面向对象模型；static也不是scala中的关键字。所以在scala中定义一个名为static的变量是完全没问题的：
```bash
scala> val static:Int=1
static: Int = 1
```

## 独立对象

前面我们演示的scala object对象Math，无需绑定其他类就可以独立使用，所以这样定义的object也被称为独立对象。  

注意，这里说的是无需绑定其他类，而不是无需引用（import）其他类。绑定的概念产生在伴生对象中。  

换句话说就是scala使用object声明的对象分为两种：独立对象和伴生对象。  

## 伴生对象

scala伴生对象是绑定到指定类上的对象。伴生对象和绑定的类共享同一个名字，绑定的类也被称为伴生类。在scala里，伴生类和伴生对象没有界限，它们互相可以访问彼此的private 方法和private 属性。    

每个类都可以有伴生对象。伴生对象和伴生类写在同一个文件中，伴生对象在scala中很常见，它们提供了在类一级进行操作的便捷方法。从某种方面来说，伴生对象还可以作为scala缺少静态成员的变通措施。

下面用伴生对象的概念修改下前面定义的Math类：
```scala
class Math private() {
  def abs(num: Int): Int = {
    if (num < 0) -num else num
  }
}


object Math {

  private val m = new Math

  def abs(num: Int) = m.abs(num)

}
```

在修改后的代码中有同时名为Math的一个类和一个object。为了保证Math类的单例特性，还使用private修饰了Math类的主构造函数，这样只能由伴生对象创建Math类的实例（不使用private来修饰主构造函数也可以，这里只是为了保证单例的严密性）。   

使用伴生对象还有另外一点好处：可以不使用new关键字就能创建伴生类的实例。再来修改Math类来看看：    
```scala
class Math private() {
  def abs(num: Int): Int = {
    if (num < 0) -num else num
  }
}


object Math {
  def apply() = new Math
}


object Client {

  def main(args: Array[String]): Unit = {
    Math().abs(-1)
  }

}
```
在Client的main方法中使用“Math()”这样的方式创建了一个Math类的对象。这里主要依赖了apply方法，它是scala提供的语法糖。上面的代码在调用Math()的时候，其实就是在调用object Math.apply()方法。通过这种方式可以让实例的创建和获取更加容易。



