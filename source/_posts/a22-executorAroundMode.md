---
title: Executor Aroud模式
toc: false
date: 2018-05-20 07:15:02
tags:
categories: scala学习
---


我们访问资源需要关注对资源的锁定、对资源的申请和释放，还要考虑可能遇到的各种异常。这些事项本身与代码的逻辑操作无关，但我们不能遗漏。也就是说需要在进入方法时获取资源，退出方法时释放资源。这种处理就进入了Execute Around模式的范畴。

在scala里可以用函数值实现这种模式。下面是一个示例，使用Resource类演示了事务的开启和释放：

```scala
class Resource private() {
  println("Starting transaction...")

  private def cleanUp() {
    println("Ending transaction...")
  }

  def op1 = println("Operation 1")
  def op2 = println("Operation 2")
  def op3 = println("Operation 3")
}

object Resource {
  def use(codeBlock: Resource => Unit) {
    val resource = new Resource 
    try {
      codeBlock(resource)
    } finally {
      resource.cleanUp()
    }
  }
}
```
这段代码里将Resource类的构造器标记为private，这样就只能在Resource类内部和它的伴生类中创建实例。因为只能在这两个地方创建实例，就可以保证按照确定的方式使用这个类的对象，即可以保证其行为是按照确定的方式执行。cleanUp()方法也被标记为private，确保不会被意外调用。Resource类第一行的print语句是具体事务操作的占位符。调用构造函数时，事务启动；调用cleanUp()函数时，事务终结。此外Resource类中还准备了一些实例方法，如op1()、op2()等。

在伴生对象里有一个默认public的方法use，它接收一个函数值作为参数。use()方法创建了一个Resource的实例，在try和finally块的保护之下，把这个实例传给了给定的函数值。在finally块里，调用了Resource私有实例方法cleanUp()。

看一下是如何使用Resource类的：

```scala
Resource.use { resource =>
  resource.op1
  resource.op2
  resource.op3
  resource.op1
}
```
代码执行结果是：

```text
Starting transaction...
Operation 1
Operation 2
Operation 3
Operation 1
Ending transaction...
```

调用Resource的伴生对象的use方法时，会自动创建一个Resource实例，等到传递的函数值执行结束后，会自动调用cleanUp方法释放占用的资源。

上面模式的一个变体是Loan（借贷）模式。如果想确保非内存资源得到确定性释放，就可以使用这个模式。可以这样认为：这种资源密集型的对象是借给你的，用过之后应该立即归还。

下面是一个Loan模式的例子：

```scala
import java.io._

def writeToFile(fileName: String)(codeBlock: PrintWriter => Unit) = {
  val writer = new PrintWriter(new File(fileName))
  try {
    codeBlock(writer)
  } finally {
    writer.close()
  }
}
```
现在调用writeToFile()将一些内容写入文件：

```scala
writeToFile("output.txt") { writer => writer write "hello from Scala" }
```

这行这段代码会创建一个名为output.txt的文件，并在文件中写入一行文字“hello from Scala”。

作为writeToFile()方法的使用者，我们不必操心文件的关闭。在代码块里，这个文件是借给我们用的。我们可以用得到的PrintWriter实例进行写操作，一旦从这个块返回，方法就会自动关闭文件。