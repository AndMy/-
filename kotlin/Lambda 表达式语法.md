### Lambda 表达式语法

### Lambda表达式的特点

Lambda 表达式的完整语法形式如下：

```
val sum: (Int, Int) -> Int = { x: Int, y: Int -> x + y }
```

要学习`Lambda`表达式语法，必先了解其特点。我在这里先总结出`Lambda`表达式的一些特征。在下面讲解到`Lambda`语法与实践时大家就明白了。即：

> - `Lambda`表达式总是被大括号括着
> - 其参数(如果存在)在 `->` 之前声明(参数类型可以省略)
> - 函数体(如果存在)在 `->` 后面。

#### Lambda语法

为了让大家彻底的弄明白`Lambda`语法，我这里用三种用法来讲解。并且举例为大家说明

*语法如下：*

```
1. 无参数的情况 ：
val/var 变量名 = { 操作的代码 }

2. 有参数的情况
val/var 变量名 : (参数的类型，参数类型，...) -> 返回值类型 = {参数1，参数2，... -> 操作参数的代码 }

可等价于
   // 此种写法：即表达式的返回值类型会根据操作的代码自推导出来。
val/var 变量名 = { 参数1 ： 类型，参数2 : 类型, ... -> 操作参数的代码 }

3. lambda表达式作为函数中的参数的时候，这里举一个例子：
fun test(a : Int, 参数名 : (参数1 ： 类型，参数2 : 类型, ... ) -> 表达式返回类型){
   ...
}
```

*实例讲解：*

- 无参数的情况

  ```
      // 源代码
     fun test(){ println("无参数") }
    
      // lambda代码
      val test = { println("无参数") }
  
      // 调用
      test()  => 结果为：无参数
  ```

- 有参数的情况,这里举例一个两个参数的例子，目的只为大家演示

  ```
      // 源代码
      fun test(a : Int , b : Int) : Int{
          return a + b
      }
  
      // lambda
      val test : (Int , Int) -> Int = {a , b -> a + b}
      // 或者
      val test = {a : Int , b : Int -> a + b}
      
      // 调用
      test(3,5) => 结果为：8
  ```

- lambda表达式作为函数中的参数的时候

  ```
      // 源代码
      fun test(a : Int , b : Int) : Int{
          return a + b
      }
  
      fun sum(num1 : Int , num2 : Int) : Int{
          return num1 + num2
      }
  
      // 调用
      test(10,sum(3,5)) // 结果为：18
  
      // lambda
      fun test(a : Int , b : (num1 : Int , num2 : Int) -> Int) : Int{
          return a + b.invoke(3,5)
      }
  
      // 调用
      test(10,{ num1: Int, num2: Int ->  num1 + num2 })  // 结果为：18
  ```

最后一个的实现可能大家难以理解，但请不要迷茫，你继续看下去，在下面的实践和高阶函数中会为大家介绍。

经过上面的实例讲解与语法的介绍，我们对其作出一个总结：

> 1. `lambda`表达式总是被大括号括着。
> 2. 定义完整的`Lambda`表达式如上面实例中的语法2，它有其完整的参数类型标注，与表达式返回值。当我们把一些类型标注省略的情况下，就如上面实例中的语法2的另外一种类型。当它推断出的返回值类型不为'Unit'时，它的返回值即为`->`符号后面代码的最后一个（或只有一个）表达式的类型。
> 3. 在上面例子中语法3的情况表示为：`高阶函数`，当`Lambda`表达式作为其一个参数时，只为其表达式提供了参数类型与返回类型，所以，我们在调用此高阶函数的时候我们要为该`Lambda`表达式写出它的具体实现。

1. `invoke()`函数：表示为通过`函数变量`调用自身，因为上面例子中的变量`b`是一个匿名函数。

### 3、Lambda实践

学会了上面讲解的语法只是，相信您已能大致的编写且使用`lambda`表达式了，不过只会上面简单的语法还不足以运用于实际项目中复杂的情况。下面从几个知识点讲解`Lambda`实践的要点。

### 3.1、it

> - `it`并不是`Kotlin`中的一个关键字(保留字)。
> - `it`是在当一个高阶函数中`Lambda`表达式的参数只有一个的时候可以使用`it`来使用此参数。`it`可表示为**单个参数的隐式名称**，是`Kotlin`语言约定的。

例1：

```
val it : Int = 0  // 即it不是`Kotlin`中的关键字。可用于变量名称
```

例2：单个参数的隐式名称

```
// 这里举例一个语言自带的一个高阶函数filter,此函数的作用是过滤掉不满足条件的值。
val arr = arrayOf(1,3,5,7,9)
// 过滤掉数组中元素小于2的元素，取其第一个打印。这里的it就表示每一个元素。
println(arr.filter { it < 5 }.component1())   
```

例2这个列子只是给大家`it`的使用，`filter`高阶函数，在后面的[Kotlin——高级篇（四）：集合（Array、List、Set、Map）基础](https://www.cnblogs.com/Jetictors/p/9237108.html)章节中会为大家详细讲解，这里不多做介绍。下面为我们自己写一个高阶函数去讲解`it`。关于高阶函数的定义与使用请参见[Kotlin——从无到有系列之高级篇（二）：高阶函数详解](https://www.cnblogs.com/Jetictors/p/8647888.html#)这篇文章。

例3：

```
 fun test(num1 : Int, bool : (Int) -> Boolean) : Int{
   return if (bool(num1)){ num1 } else 0
}

println(test(10,{it > 5}))
println(test(4,{it > 5}))
```

输出结果为：

```
10
0
```

代码讲解：上面的代码意思是，在高阶函数`test`中，其返回值为`Int`类型，`Lambda`表达式以`num1`位条件。其中如果`Lambda`表达式的值为`false`的时候返回0，反之返回`num1`。故而当条件为`num1 > 5`这个条件时，当调用`test`函数，`num1 = 10`返回值就是10，`num1 = 4`返回值就是0。

### 3.2、下划线（_）

> 在使用`Lambda`表达式的时候，可以用下划线(`_`)表示未使用的参数，表示不处理这个参数。

同时在遍历一个`Map`集合的时候，这当非常有用。

举例：

```
val map = mapOf("key1" to "value1","key2" to "value2","key3" to "value3")

map.forEach{
     key , value -> println("$key \t $value")
}

// 不需要key的时候
map.forEach{
    _ , value -> println("$value")
}
```

输出结果：

```
key1 	 value1
key2 	 value2
key3 	 value3
value1
value2
value3
```

### 3.3 匿名函数

> - 匿名函数的特点是可以明确指定其返回值类型。
> - 它和常规函数的定义几乎相似。他们的区别在于，匿名函数没有函数名。

例：

```
            fun test(x : Int , y : Int) : Int{                  fun(x : Int , y : Int) : Int{
常规函数：      return x + y                        匿名函数：      return x + y
            }                                                   }
```

在前面的[Kotlin——初级篇（七）：函数（方法）基础总结](https://www.cnblogs.com/Jetictors/p/8506941.html)我们讲解过单表达式函数。故而，可以简写成下面的方式。

```
常规函数 ： fun test(x : Int , y : Int) : Int = x + y
匿名函数 ： fun(x : Int , y : Int) : Int = x + y
```

从上面的两个例子可以看出，匿名函数与常规函数的区别在于一个有函数名，一个没有。

实例演练：

```
val test1 = fun(x : Int , y : Int) = x + y  // 当返回值可以自动推断出来的时候，可以省略，和函数一样
val test2 = fun(x : Int , y : Int) : Int = x + y
val test3 = fun(x : Int , y : Int) : Int{
    return x + y
}

println(test1(3,5))
println(test2(4,6))
println(test3(5,7))
```

输出结果为：

```
8
10
12
```

从上面的代码我们可以总结出`匿名函数`与`Lambda`表达式的几点区别：

> 1. 匿名函数的参数传值，总是在小括号内部传递。而`Lambda`表达式传值，可以有省略小括号的简写写法。
> 2. 在一个不带`标签`的`return`语句中，匿名函数时返回值是返回自身函数的值，而`Lambda`表达式的返回值是将包含它的函数中返回。

### 3.4、带接收者的函数字面值

> 在`kotlin`中，提供了指定的*接受者对象*调用`Lambda`表达式的功能。在函数字面值的函数体中，可以调用该接收者对象上的方法而无需任何额外的限定符。它类似于`扩展函数`，它允你在函数体内访问接收者对象的成员。

- **匿名函数作为接收者类型**

匿名函数语法允许你直接指定函数字面值的接收者类型，如果你需要使用带接收者的函数类型声明一个变量，并在之后使用它，这将非常有用。

例：

```
val iop = fun Int.( other : Int) : Int = this + other
println(2.iop(3))
```

输出结果为：

```
5
```

- **Lambda表达式作为接收者类型**

> 要用Lambda表达式作为接收者类型的前提是**接收着类型可以从上下文中推断出来**。

例：这里用官方的一个例子做说明

```
class HTML {
    fun body() { …… }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()  // 创建接收者对象
    html.init()        // 将该接收者对象传给该 lambda
    return html
}


html {       // 带接收者的 lambda 由此开始
    body()   // 调用该接收者对象的一个方法
}
```

### 3.5 闭包

> - 所谓`闭包`，即是函数中包含函数，这里的函数我们可以包含(`Lambda`表达式，匿名函数，局部函数，对象表达式)。我们熟知，函数式编程是现在和未来良好的一种编程趋势。故而`Kotlin`也有这一个特性。
> - 我们熟知，`Java`是不支持闭包的，`Java`是一种面向对象的编程语言，在`Java`中，`对象`是他的一等公民。`函数`和`变量`是二等公民。
> - `Kotlin`中支持闭包，`函数`和`变量`是它的一等公民，而`对象`则是它的二等公民了。

实例：看一段`Java`代码

```
public class TestJava{

    private void test(){
        private void test(){        // 错误，因为Java中不支持函数包含函数

        }
    }

    private void test1(){}          // 正确，Java中的函数只能包含在对象中+
}
```

实例：看一段`Kotlin`代码

```
fun test1(){
    fun test2(){   // 正确，因为Kotlin中可以函数嵌套函数
        
    }
}
```

下面我们讲解`Kotlin`中几种闭包的表现形式。

#### 3.5.1、携带状态

例：让函数返回一个函数，并携带状态值

```
fun test(b : Int): () -> Int{
    var a = 3
    return fun() : Int{
        a++
        return a + b
    }
}

val t = test(3)
println(t())
println(t())
println(t())
```

输出结果：

```
7
8
9
```

#### 3.5.2、引用外部变量，并改变外部变量的值

例：

```
var sum : Int = 0
val arr = arrayOf(1,3,5,7,9)
arr.filter { it < 7  }.forEach { sum += it }

println(sum)
```

输出结果：

```
9
```

#### 3.6 在`Android`开发中为`RecyclerView`的适配器编写一个`Item`点击事件

```
class TestAdapter(val context : Context , val data: MutableList<String>)
    : RecyclerView.Adapter<TestAdapter.TestViewHolder>(){

    private var mListener : ((Int , String) -> Unit)? = null

    override fun onBindViewHolder(holder: TestViewHolder?, position: Int) {
        ...
        holder?.itemView?.setOnClickListener {
            mListener?.invoke(position, data[position])
        }

    }

    override fun onCreateViewHolder(parent: ViewGroup?, viewType: Int): TestViewHolder {
        return TestViewHolder(View.inflate(context,layoutId,parent))
    }

    override fun getItemCount(): Int {
        return data.size
    }

    fun setOnItemClickListener(mListener : (position : Int, item : String) -> Unit){
        this.mListener = mListener
    }

    inner class TestViewHolder(itemView : View) : RecyclerView.ViewHolder(itemView)
}

// 调用
TestAdapter(this,dataList).setOnItemClickListener { position, item ->
        Toast.makeText(this,"$position \t $item",Toast.LENGTH_SHORT).show()
    }
```

## 总结

`Lambda`表达式是为我们减少了大量的代码，但是`Lambda`表达式是为后面的高阶函数章节打下基础，虽然在这篇文章中也提到了高阶函数，但是都是最基础的，在下一节中会为大家介绍自定义高阶函数与`Kotlin`自身中常用的高阶函数讲解。

在这一章节中，讲述了`Lambda`的语法、使用。以及`Lambda`表达式的一些特性与实践操作。当然还包含了`匿名函数`这一知识点。其中最重要的当属`Lambda`的实践操作。如果你看完这篇文章还不甚理解，请在仔细的阅读一遍并实际代码演练，因为在后面的高阶函数章节还会遇到。