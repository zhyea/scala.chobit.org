---
title: 循环和循环控制
toc: true
date: 2018-04-29 09:40:46
tags:
categories: scala学习
---


## 循环类型  
scala中的循环类型和java差不多，大体上有三种：  
* while循环
* do{}while循环
* for循环

每样循环都写一段示例代码来看一下。  

### while循环  
代码如下：
```scala
  var i = 0
  while (i < 6) {
    println(s"Value of i: $i")
    i += 1
  }
```
执行结果如下：
```text
Value of i: 0
Value of i: 1
Value of i: 2
Value of i: 3
Value of i: 4
Value of i: 5
```
### do{}while循环  
代码：
```scala
  var i = 0
  do {
    println(s"Value of i: $i")
    i += 1
  } while (i < 6)
```
执行结果
```text
Value of i: 0
Value of i: 1
Value of i: 2
Value of i: 3
Value of i: 4
Value of i: 5
```
### for循环  
看一个示例：
```scala
  for (i <- 0 to 5) {
    println(s"Value of i $i")
  }
```
执行结果：
```text
Value of i 1
Value of i 2
Value of i 3
Value of i 4
Value of i 5
```

在上面代码中的循环结构里，"i <- 0 to 5"是一个生成器表达式。在这里的每次循环中都会创建一个新的val变量i（而非是给i赋值），用生成器表达式产生出来的连续值进行初始化。  

生成器表达式“1 to 3”生成的连续值包含了下界[1]和上界[3]，如果用until方法替换to方法后，就可以排除上界。to和until都是scala的RichInt类的方法。

这里使用1 to 3等价于1.to(3)，即数字1调用了整型的to方法。在scala中，如果方法有0或1个参数，点和括号是可以丢掉的。如果方法的参数多于一个，那么就必须保留括号，但是点仍是可选的。以一个非常常见的表达式为例：1 + 2在scala中实际上就是1.+(2)，+也是一个方法而非是简单的符号。

我们已经知道1 to 3返回的使一个连续值(Range)，那么也可以考虑使用Range的foreach方法来实现循环。代码如下：
```scala
  (1 to 3).foreach(i => println(s"Value of i $i"))
```

for循环的功能非常丰富，值得多多解释一下，所以会另写一篇进行说明。

## 循环控制 
通常的循环控制语句就是**continue**和**break**。但是这两个关键字在scala中是不存在的。不过我们可以使用一些技巧来实现这两种控制语句。  

先来看一个Java的例子：
```java
for(int i=0; i<6; i++){
    if(i==2){
        continue;
    }
    if(i==4){
        break;
    }
    System.out.println("Value of i:" + i);
}
```
代码要做的事情非常简单，不需要多做解释。  

要用scala实现这个逻辑，简单点儿的思路是用if判断句来替换continue，用一个bool变量控制循环来替换break。代码如下：
```scala
  var i: Int = 0
  var break: Boolean = false
  while (i < 6 && !break) {
    if (i != 2) {
      if (i == 4) {
        break = true
      }
      println(s"Value of i: $i")
    }
    i += 1
  }
```
这段代码实现了前面那段Java代码的功能。期间我们还是使用了两个var变量。这两个var变量一个用来提供循环值，一个用来实现break功能中止循环。scala不鼓励使用var变量。我们尝试去掉这两个var变量。  

要去掉循环值变量可以考虑将所有的循环值放入一个集合，然后使用for循环来处理。  
要去掉break控制变量，可以考虑主动抛出异常来中止当前代码块。

根据这个思路来做一下实现：  
```scala
  try {
    for (i <- 0 to 6) {
      i match {
        case 2 =>
        case 4 => throw new RuntimeException
        case _ => println(s"Value of i: $i");
      }
    }
  } catch {
    case e: RuntimeException =>
  }
```
在修改后的代码里使用模式匹配替换了if判断，并且演示了scala处理异常的方案。  
这里用抛出异常来实现break的方式scala中有提供具体的实现方案，即在scala.util.control包中定义的break控制结构，用法如下：
```scala
  val b = new scala.util.control.Breaks

  b.breakable {
    for (i <- 0 to 6) {
      i match {
        case 2 =>
        case 4 => b.break()
        case _ => println(s"Value of i: $i");
      }
    }
  }
```

思路还可以继续展开，比如用递归来替换循环：
```scala
  def myLoop(i: Int): Unit = {
    if (i > 6) ()
    else if (i == 2) myLoop(i + 1)
    else if (i != 4) {
      println(s"Value of i: $i");
      myLoop(i + 1)
    }
  }

  myLoop(0)
```
在函数化编程中使用递归函数来实现循环是非常常见的一种方法，并且这样做很函数式。