---
title: 偏函数
toc: true
date: 2018-05-16 07:01:16
tags: 偏函数
categories: scala探索
---

偏函数名字已经说明了很多东西：在执行上偏函数是存在偏颇的。具体来说偏函数有这样几类特征：

1. 偏函数的参数只有一个
1. 偏函数不会处理参数类型范围内的所有值，对不处理的值会直接抛出错误
1. 偏函数的字面量语法由包围在花括号中的一个或多个case语句构成（普通函数花括号不是必需的）

从第二个特征可以看出偏函数到底“偏”在哪里了。

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

至于为什么会抛出这个异常，因为scala的语言太过简洁，有必要看一下对这个类的字节码文件反编译后的结果，其中主要是看一下偏函数find的实现。使用scalac完成编译后，可以看到生成了三个字节码文件：
> MyFunc$$anonfun$1.class  
> MyFunc$.class  
> MyFunc.class

使用jd-gui打开MyFunc$.class可以看到find的实现：
```java
 this.find = new AbstractPartialFunction.mcLI.sp() {
     public static final long serialVersionUID = 0L;

     public final <A1, B1> B1 applyOrElse(A1 x1, Function1<A1, B1> default) {
         int i = x1;
         Object localObject;
         if (i > 5) localObject = "It's Target.";
         else
             localObject = default.apply(BoxesRunTime.boxToInteger(x1));
         return localObject;
     }

     public final boolean isDefinedAt(int x1) {
         int i = x1;
         boolean bool;
         if (i > 5) bool = true;
         else
             bool = false;
         return bool;
     }
 };
```


## 参考资料

> - [偏函数](https://blog.csdn.net/yuanguangyu1221/article/details/71307664)
> - [scala中的Partial Function](https://zhuanlan.zhihu.com/p/20832218?refer=insight)