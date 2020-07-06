---
layout: post
title:  "Kotlin 学习记录（Lambda & 空类型）"
date:   2019-07-15
excerpt: "浙大成功软件实习、学习"
tag:
- Kotlin

---



<center><H2><b> Kotlin 学习记录 </b></H2></center><br>

```
本文只记录重要的或者与C/C++、Java 出入较大的内容
```

## Lambda 表达式

### 语法

  无参数的情况

```kotlin
  val fun1 = {9} //定义了一个返回了Int的函数
  
  //该定义参照了情况3
  //传入一个Lambda来表示的形式参数，该形参为一个无参返回Int的函数
  var fun11 : (()->(Int)) ->Int = {initfunc -> initfunc()} 
  
  var value00 = fun11(fun1)
  println("value00 => $value00")
```

  有参数的情况

```kotlin
  var value1 = 9
  val fun2:(Int,Int)->Double = {a,b-> (a-b).toDouble()}
  var value2 = fun2(value1,3)
  println("value2 => $value2")//输出：value2 => 6.0
  
  //等价形式如下
  var fun3 = {a:Int,b:Int -> a+b}
  var value3 = fun3(3,6)
  
  println("value3 => $value3")//输出：value3 => 9
  //3. lambda表达式作为函数中的参数的时候，这里举一个例子：
  //  fun test(a : Int, 参数名 : (参数1 ： 类型，参数2 : 类型, ... ) -> 表达式返	 //回类型){
  //     ...
  //  }
  //
  
  //下面两个新形式都可以作为value计算的表达式，fun与var功能似乎一样了？NO
  //三种形式定义该test函数，fun声明，var声明，匿名函数。
  //fun test(a:Int,b:Int,add:(Int,Int)->Int):Int{ return add(a,b)}//是一个函数
  //var test: (Int,Int,(Int,Int)->Int) -> Int = {a,b,add -> add(a,b)}//该变量是lambda变量
  
  var test = fun(a:Int,b:Int,add:(Int,Int)->Int):Int{ return add(a,b)} //是一个函数
  
  println("value4 = $value4")
```

  ​		`lambda`表达式总是被大括号括着

  ​		定义完整的`Lambda`表达式如上面实例中的语法2，它有其完整的参数类型标注，与表达式返回值。当我们把一些类型标注省略的情况下，就如上面实例中的语法2的另外一种类型。当它推断出的返回值类型不为`Unit`时，它的返回值即为->符号后代码段中的最后一个表达式的类型（如同if-else语句块中的返回值一样）

  ​		当函数的参数仅有一个Lambda表达式的时候可以**省略参数的那个小括号**

### it

- it不是关键字
- it在高阶函数中的lambda表达式的参数只有一个的时候可以使用`it`来使用此参数。`it`可表示**单个参数的隐式名称**

  例子：

```kotlin
   fun test(num1 : Int, bool : (Int) -> Boolean) : Int{
     return if (bool(num1)){ num1 } else 0
  }
  
  println(test(10,{it > 5})) // {} 代表这是一个Lambda表达式，无{}会使得编译器不认识it
  println(test(4,{it > 5}))
```

  

### _

  在使用`Lambda`表达式的时候，可以用下划线表示未使用的参数，表示不处理这个参数。

```kotlin
  val map = mapOf<String,String>("key1" to "value1","key2" to  "value2","key3" to  "value3")
  
  map.forEach{
      key,value -> println("$key '-' $value")
  }
  map.forEach{
          _,value -> println(" '-' $value")
  }
```

  

### 匿名函数

```kotlin
  val test1 = fun (x:Int,y:Int) = x+y; //单表达式函数可以 = 替换 {}
  val test2 = fun (x:Int,y:Int) : Int= x+y;
  val test3 = fun (x:Int,y:Int) : Int{
      return x+y
  }
  println(test1(1,3))
  println(test2(2,3))
  println(test3(3,3))
  
  /**
  //这是错的，fun的需要返回值为Unit，但你给了个Int
      val test4= fun(x:Int,y:Int) {
          return x+y
      } 
  */
```

  

### 带接收者的函数字面值

1. 匿名函数作为接收者类型

   匿名函数语法允许直接指定函数字面值的接收者类型，如果你需要使用带接收者的函数类型声明一个变量。

```kotlin
val iop = fun  Int.(other:Int):Int = this + other
println(20.iop(2)) //上面的this指的是左边的20 或者是 上面fun后的第一个Int

```

​    

2. Lambda表达式作为接收者类型

   要用Lambda表达式作为接收者类型的前提是**接收着类型可以从上下文中推断出来

```kotlin

class HTML{
	fun body(){
		println("This is HTML body")
	}
}

fun  html(init: HTML.() -> Unit): HTML{
	val html = HTML()
	html.init()
	return html
}

html {
	body()
}
```

​    

### 闭包

闭包，可以函数中包含函数。

- 携带状态

```kotlin
让函数返回一个函数，并携带状态值

fun test5(a:Int):()->Int{
    println("")
    var b = 3
    return fun():Int{
        println("b=> $b")
        b++
        return b + a
    }
}

var t = test5(3)
println(t()) // 每次执行t函数的时候，b变量的值都是保留了上次执行结束的值，
			 // 因此，叫做携带状态值
println(t())
println(t())
```

- 引用外部变量，并改变外部变量的值

```kotlin
var sum = 0
val arr = arrayOf(1,2,3,4,5,6,7,8,9,10,11)
arr.filter { it<7 }.forEach{sum += it}
//arr.filter { it<7 }.forEach ({sum += it})

println(sum)
```





## 可空类型、空安全、非空断言

1. 判空的方法，if-else / ?. 判断

   ```kotlin
   var str : String? = "12346"
   str = null
   
   println(str?.length) //输出null
   ```

2. 当一个函数/方法有返回值时，如果方法中的代码使用`?.`去返回一个值，那么方法的返回值的类型后面也要加上`?`符号

3. let操作符

   let操作符作用：当使用?.符号时验证时忽略掉null

   ```
   val arrTest : Array<Int?> = arrayOf(1,2,null,3,null,5,6,null)
   
   // 传统写法
   for (index in arrTest) {
       if (index == null){
           continue
       }
       println("index => $index")
   }
   
   // let写法
   for (index in arrTest) {
       index?.let { println("index => $it") }
   }
   
   /**
       index => 1
       index => 2
       index => 3
       index => 5
       index => 6
   */
   ```

   

4. Evils操作符

   安全性操作符有三种：`?:` /` !!` / `as?`

   `?:`

   ```kotlin
   val testStr : String? = null
   
   var length = 0
   
   // 例： 当testStr不为空时，输出其长度，反之输出-1
   
   // 传统写法
   length = if (testStr != null) testStr.length else -1
   
   // ?: 写法
   length = testStr?.length ?: -1
   
   println(length)
   ```

   `!!`

   ```
   val testStr : String? = null
   println(testStr!!.length)
   //如果变量为空，使用!!修饰，运行时会抛出空指针异常
   ```

   `as`

   使用`as`进行强制转换，在不能转换时会抛出异常，而使用`as?`则会返回`null`，但不会抛出异常。