---
title: 模式变量和模式常量
toc: true
date: 2018-06-28 22:18:32
tags: [模式变量,模式常量]
categories: scala学习
---

在进行模式匹配时可以使用模式常量和模式变量。

模式匹配中的占位符即为模式变量。

关于模式常量可以看下下面的示例：

```scala
  class Sample {

    val MIN = 0

    def process(input: Int) {
      input match {
        case MIN => println("You matched min")
        case _ => println("Unreachable!!")
      }
    }
  }

  new Sample().process(0)
  new Sample().process(10)
```

代码中的“MIN”就是一个模式常量。执行结果如下：

```text
You matched min
Unreachable!!
```


按照约定，Scala中模式变量要以小写字母开头，常量要以大写字母开头。所以这里一定要注意大小写，下面是一个错误的示范：

```scala
  class Sample {

    val max = 100
    val MIN = 0

    def process(input: Int) {
      input match {
        case max => println("Don't try this")
        case MIN => println("You matched min")
        case _ => println("Unreachable!!")
      }
    }
  }


  new Sample().process(100)
  new Sample().process(0)
  new Sample().process(10)
```

在解释器里可以清楚地看到warning信息：

```bash
scala>   class Sample {
     |
     |     val max = 100
     |     val MIN = 0
     |
     |     def process(input: Int) {
     |       input match {
     |         case max => println("Don't try this")
     |         case MIN => println("You matched min")
     |         case _ => println("Unreachable!!")
     |       }
     |     }
     |   }
<console>:18: warning: patterns after a variable pattern cannot match (SLS 8.1.1)
If you intended to match against value max in class Sample, you must use backticks, like: case `max` =>
               case max => println("Don't try this")
                    ^
<console>:19: warning: unreachable code due to variable pattern 'max' on line 18
               case MIN => println("You matched min")
                                  ^
<console>:20: warning: unreachable code due to variable pattern 'max' on line 18
               case _ => println("Unreachable!!")
                                ^
<console>:19: warning: unreachable code
               case MIN => println("You matched min")
                                  ^
defined class Sample
```

警告信息提示因为错误的使用了模式值“max”，导致其后的匹配代码均不可达。看下执行结果：

```bash
scala>   new Sample().process(100)
Don't try this

scala>   new Sample().process(0)
Don't try this

scala>   new Sample().process(10)
Don't try this

```

可以看到程序可以执行，但是无论我们输入什么值，都只会匹配到第一个匹配项“max”。

如果偏要使用这个值的话可以通过object或类的实例来引用这个变量：

```scala
  class Sample {
    val max = 100
    val MIN = 0

    def process(input: Int) {
      input match {
        case this.max => println("You matched max")
        case MIN => println("You matched min")
        case _ => println("Unreachable!!")
      }
    }
  }
```

使用class的实例this来引用了val变量max。这时执行代码结果是正常的：

```text
You matched max
You matched min
Unreachable!!
```