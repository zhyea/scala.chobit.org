---
title: unapply方法
toc: false
date: 2018-06-11 22:13:58
tags: [unapply,提取器]
categories: scala技巧
---

对于unapply方法，先来看一个实例：

```scala
object Coder {

  def unapply(str: String): Option[String] = {
    val idx = str.indexOf("-")
    val name = str.substring(0, idx)
    val lang = str.substring(idx + 1)
    Some(s"$name is a $lang coder.")
  }

}

object MyApp extends App {

  val Coder(s) = "robin-scala"

  println(s)
}
```

这已经是一段完整的程序了。如下是执行结果：
```text
robin is a scala coder.
```

注意下程序的第14行（val Coder(s) = "robin-scala"），就是在这一行调用了unapply方法，其意义等价于如下代码：
```scala
val s = Coder.unapply("robin-scala").get
```

unapply方法的使用在这里看起来怪怪的：“Coder(s)”的写法看起来像是调用构造器，但是实际上并不是调用构造方法，而是从一个字符串提取出一些信息。

我们再来看另一个例子：

```scala
class Coder(val name: String, val age: Int)

object Coder {

  def apply(name: String, age: Int) = new Coder(name, age)

  def unapply(c: Coder): Option[(String, Int)] = Some((c.name, c.age))

}

object MyApp extends App {

  val robin = Coder("robin", 27)

  val Coder(name, age) = robin

  println(s"$name is $age years old.")
}
```
在这个例子中，我们定义了Coder的伴生类和伴生对象。在伴生对象中创建了一个apply方法和一个unapply方法。在场景object MyApp中使用object Coder的apply方法创建了一个class Coder的实例robin，然后又通过Coder的unapply方法取出了实例robin的name和age两个属性。

大家应该能看出来，在scala中apply和unapply方法是有默认的特殊意义的：
* apply方法和unapply方法的作用在某种意义上是相反的；
* apply方法通常用于创建实例；
* unapply方法通常用于从实例中提取出实例的属性。

从上面的两个例子，我们可以推断出unapply方法有如下特征：unapply通常需要定义在object中，作用是将实例的一些信息提取出来；其适用场景多发生在模式匹配中；因为模式匹配可能失败，所以unapply方法的返回值类型一般为Option[]。

来看一下unapply在模式匹配中的使用：
```scala

class Coder(val name: String, val lang: String)

object Coder {

  def unapply(c: Coder): Option[(String, String)] = Some((c.name, c.lang))

}

object MyApp extends App {

  val c = new Coder("robin", "scala")

  c match {
    case Coder(name, "scala") => println(s"$name is a scala coder.")
    case Coder(name, "java") => println(s"$name is a java coder.")
    case _ => println(s"Maybe this is a coder")
  }
}
```

unapply方法返回值也可以是Boolean类型。这种方式适用于只需要对属性进行检查，而不必提取的场景。来看一个示例：
```scala
object IsElder {
  def unapply(age: Int): Boolean = age > 36
}


object MyApp extends App {

  val num = 18

  num match {
    case n @ IsElder() => println(s"$n is greater than 36")
    case n => println(s"$n is lower than 36")
  }
  
}
```

前面的几个示例中unapply方法返回的值的长度都是固定的，不过某些情况下需要返回一些不固定长度的返回值，此时可以使用容器来存储返回值，但是更好的方案是使用unapplySeq方法。来看一个示例：
```scala

class Coder(val name: String, val langs: String*)

object Coder {

  def unapplySeq(c: Coder): Option[Seq[String]] = {
    val buffer = ListBuffer(c.name)
    buffer ++= c.langs.toBuffer
    Some(buffer)
  }

}


object MyApp extends App {

  val robin = new Coder("robin", "java", "scala", "python")
  val tom = new Coder("tom", "java", "scala")


  def foo(coder: Coder) = coder match {
    case Coder(name, lang1, lang2, lang3) => println(s"$name works with 3 languages: $lang1, $lang2, $lang3")
    case Coder(name, lang1, lang2) => println(s"$name works with 2 languages: $lang1, $lang2")
  }

  foo(robin)
  foo(tom)

}
```

关于unapply方法和提取器暂时先了解这些内容好了。