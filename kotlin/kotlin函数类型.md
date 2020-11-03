# **kotlin函数类型**

### **函数类型**

函数类型的含义：Kotlin 使用类似 `(Int) -> String` 的一系列`函数类型`来处理函数的声明： `val onClick: () -> Unit = ……`。

这些类型具有与函数签名相对应的特殊表示法，即它们的参数和返回值：

- 所有函数类型都有一个圆括号括起来的参数类型列表以及一个返回类型：`(A, B) -> C` 表示接受类型分别为 `A` 与 `B` 两个参数并返回一个 `C` 类型值的函数类型。 参数类型列表可以为空，如 `() -> A`。[`Unit` 返回类型](https://www.kotlincn.net/docs/reference/functions.html#返回-unit-的函数)不可省略。
- 函数类型可以有一个额外的*接收者*类型，它在表示法中的点之前指定： 类型 `A.(B) -> C` 表示可以在 `A` 的接收者对象上以一个 `B` 类型参数来调用并返回一个 `C` 类型值的函数。 [带有接收者的函数字面值](https://www.kotlincn.net/docs/reference/lambdas.html#带有接收者的函数字面值)通常与这些类型一起使用。
- [挂起函数](https://www.kotlincn.net/docs/reference/coroutines.html#挂起函数)属于特殊种类的函数类型，它的表示法中有一个 *suspend* 修饰符 ，例如 `suspend () -> Unit` 或者 `suspend A.(B) -> C`。

函数类型表示法可以选择性地包含函数的参数名：`(x: Int, y: Int) -> Point`。 这些名称可用于表明参数的含义。

> 如需将函数类型指定为[可空](https://www.kotlincn.net/docs/reference/null-safety.html#可空类型与非空类型)，请使用圆括号：`((Int, Int) -> Int)?`。
>
> 函数类型可以使用圆括号进行接合：`(Int) -> ((Int) -> Unit)`
>
> 箭头表示法是右结合的，`(Int) -> (Int) -> Unit` 与前述示例等价，但不等于 `((Int) -> (Int)) -> Unit`。

还可以通过使用[类型别名](https://www.kotlincn.net/docs/reference/type-aliases.html)给函数类型起一个别称：

```
typealias ClickHandler = (Button, ClickEvent) -> Unit
```

### 函数类型实例化

有几种方法可以获得函数类型的实例：

- 使用函数字面值的代码块，采用以下形式之一：    

  - [lambda 表达式](https://www.kotlincn.net/docs/reference/lambdas.html#lambda-表达式与匿名函数): `{ a, b -> a + b }`,
  - [匿名函数](https://www.kotlincn.net/docs/reference/lambdas.html#匿名函数): `fun(s: String): Int { return s.toIntOrNull() ?: 0 }`

  [带有接收者的函数字面值](https://www.kotlincn.net/docs/reference/lambdas.html#带有接收者的函数字面值)可用作带有接收者的函数类型的值。

  代码如下

  ```
  private var mListener1 : ((Int , String) -> Unit)? = null
  private var mListener2 : ((Int , String) -> Unit)? = fun(position :Int,str :String){}//匿名函数
  private var mListener3 : ((Int , String) -> Unit)? = {position : Int,str : String -> }//lambda表达式
  ```

  

- 使用已有声明的可调用引用：    

  - 顶层、局部、成员、扩展[函数](https://www.kotlincn.net/docs/reference/reflection.html#函数引用)：`::isOdd`、 `String::toInt`，
  - 顶层、成员、扩展[属性](https://www.kotlincn.net/docs/reference/reflection.html#属性引用)：`List<Int>::size`，
  - [构造函数](https://www.kotlincn.net/docs/reference/reflection.html#构造函数引用)：`::Regex`

  这包括指向特定实例成员的[绑定的可调用引用](https://www.kotlincn.net/docs/reference/reflection.html#绑定的函数与属性引用自-11-起)：`foo::toString`。

- 使用实现函数类型接口的自定义类的实例：

```
class IntTransformer: (Int) -> Int {
    override operator fun invoke(x: Int): Int = TODO()
}

val intFunction: (Int) -> Int = IntTransformer()
```

带与不带接收者的函数类型*非字面*值可以互换，其中接收者可以替代第一个参数，反之亦然。例如，`(A, B) -> C` 类型的值可以传给或赋值给期待 `A.(B) -> C` 的地方，反之亦然：

```
val repeatFun: String.(Int) -> String = { times -> this.repeat(times) }
val twoParameters: (String, Int) -> String = repeatFun // OK

fun runTransformation(f: (String, Int) -> String): String {
    return f("hello", 3)
}
val result = runTransformation(repeatFun) // OK

```

> 请注意，默认情况下推断出的是没有接收者的函数类型，即使变量是通过扩展函数引用来初始化的。 如需改变这点，请显式指定变量类型。

### 函数类型实例调用

函数类型的值可以通过其 [`invoke(……)` 操作符](https://www.kotlincn.net/docs/reference/operator-overloading.html#invoke)调用：`f.invoke(x)` 或者直接 `f(x)`。

如果该值具有接收者类型，那么应该将接收者对象作为第一个参数传递。 调用带有接收者的函数类型值的另一个方式是在其前面加上接收者对象， 就好比该值是一个[扩展函数](https://www.kotlincn.net/docs/reference/extensions.html)：`1.foo(2)`，

例如：

```
val stringPlus: (String, String) -> String = String::plus
val intPlus: Int.(Int) -> Int = Int::plus

println(stringPlus.invoke("<-", "->"))
println(stringPlus("Hello, ", "world!")) 

println(intPlus.invoke(1, 1))
println(intPlus(1, 2))
println(2.intPlus(3)) // 类扩展调用

打印结果
<-->
Hello, world!
2
3
5
```

