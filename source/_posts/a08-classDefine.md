---
title: 类定义和构造器
toc: true
date: 2018-04-26 06:05:15
tags: 类定义
categories: scala学习
---

这次会通过与Java比较的方式来说明scala是如何创建类的。

## 类定义

假设我们要定义一个学生类，先来看一下使用Java是如何定义的：
```java
public class Student {
    
    private String name;
    
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```
很基础的一个Bean定义，大概用了27行代码。  

再来看一下如何用scala定义这个学生类：
```scala
class Student(val name: String, val age: Int)
```
是的，只用一行就完成了类的定义，连大括号都不需要。并且在类定义的时候就完成了类构造器的创建。这里和类定义一起实现的构造器也叫作主构造器。  

## 构造器

除了主构造器，我们也可以在类的内部添加新的构造器。  

现在我们为Student类新增一个类成员变量gender，并添加一个新的构造函数：

```scala
class Student(val name: String, val age: Int) {

  var gender: Short = _

  def this(name: String, age: Int, gender: Short) {
    this(name, age)
    this.gender = gender
  }
}
```
区别于主构造函数，在类内部新定义的构造函数被称为副构造函数。  

稍稍关注下副构造函数的实现：如果有主构造函数的话，那么副构造函数的第一行必须是主构造函数或者其它副构造函数的调用。这一点和java继承父类时有点相似。  

此外还值得注意的就是成员变量gender的声明，把这一行单独拎出来看看吧：  
```scala
var gender: Short = _
```
这里比较有趣的是初始化赋值，赋的值是一个“\_”——下划线。在这里“\_”代表相应类型的默认值。对于Int，它的值是0；对于Double，它的值是0.0；对于String，它的值就是null。通过使用“\_”，可以很方便地为var成员变量设置初始默认值。  

不过不能为val成员使用“_”，因为val成员不允许修改，所以必须显式指定初始值。  

如果构造器中的成员变量没有使用var或val修饰，将被视为类内部私有的val变量。  

另外，放到类定义中的所有可执行语句或表达式都会被视为类的构造器的组成部分。如下面的代码：
```scala
class Student(val name: String, val age: Int) {

  println("Create a new student instance.")
  
}

new Student("robin", 20)
```
这段代码在定义Student类以后，又创建了一个Student实例。看下执行结果：
```scala
Create a new student instance.

Process finished with exit code 0
```
在创建实例的时候输出了类定义中的print语句，因为这段print语句是构造器的一部分。