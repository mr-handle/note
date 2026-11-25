# Kotlin

github: <https://github.com/JetBrains/kotlin>

## 安装

IntelliJ IDEA 和 Android Studio 内置了 Kotlin 语言支持，完全不需要也不建议手动安装

但下面还是介绍下手动安装

访问上面提到的github地址，下载编译器并解压：`kotlin-compiler-2.4.21.zip`，这是操作系统平台通用的

```sh
# 将kotlin编译器目录添加到PATH
export KOTLIN_HOME=/path/to/kotlinc
export PATH=$PATH:${KOTLIN_HOME}/bin

# 测试
kotlin -version
```

## 第一个 Kotlin 程序

- 新建文件Main.kt，内容如下

```kotlin
fun main() {
    println("Hello, world!!!")
}
```

- 编译

```sh
kotlinc Main.kt -include-runtime -d Main.jar
```

- 运行

```sh
java -jar Main.jar
```

## 主方法

```kotlin
fun main() {
    println("Hello, world!!!")
}
```

## 变量

数据类型：Boolean、Int、Float、Double、String

```kotlin
// 定义常量
// val 变量名: 数据类型 = 变量值
val count: Int = 2

// 定义变量
// var 变量名: 数据类型 = 变量值
var count: Int = 2

// 不写变量类型，通过类型推断确定变量的数据类型
val count = 2


println("count: $count")
```

## 定义函数/方法

函数/方法只是叫法不一样，写在类中的函数叫做方法

### 无参无返回值函数

```kotlin
// 无参无返回值函数
fun 函数名() {
    // 函数体
}

// 调用函数
函数名()
```

### 无参有返回值函数

```kotlin
// 如果不指定返回类型，默认是Unit，相当于java中的void/Void
fun 函数名() : 返回类型 {
    // 函数体
    return statement
}

// 调用函数
val result = 函数名()
```

### 有参有返回值函数

有参无返回值函数就少写了返回类型，不再写出来了

```kotlin
// 有参有返回值函数
fun 函数名(参数名1: 参数类型, 参数名2: 参数类型, ...) : 返回类型 {
    // 函数体
    return statement
}

// 指定默认参数值
fun 函数名(参数名1: 参数类型 = 默认值, 参数名2: 参数类型, ...) : 返回类型 {
    // 函数体
    return statement
}

// 调用函数
val result = 函数名(参数1, 参数2, ...)

// 可以不同的顺序传递实参
val result = 函数名(参数名2 = 参数值, 参数名1 = 参数值, ...)

// lambda
val sum = { x: Int, y: Int -> x + y }

sum(1, 2)

// 如果一个函数的‌最后一个参数‌是一个 Lambda 表达式（函数类型），可以将该 Lambda 移到圆括号 () 之外
// 如果该函数‌只有‌这一个 Lambda 参数，那么圆括号可以完全省略
```

### 函数简写

对于一些简单函数，可以省略花括号和返回值类型

```kotlin
// 传统写法
fun sum(x: Int, y: Int): Int {
    return x + y
}

// 省略花括号
fun sum1(x: Int, y: Int): Int = x + y

// 省略花括号 + 返回值类型，自动推断返回类型
fun sum2(x: Int, y: Int) = x + y
```

### 内部函数

```kotlin
fun outer() {
    val x = 666
    fun inner() {
        // 可以访问外部函数中的变量
        println(x)
        // 还可以无限套娃
    }
    // 内部函数的作用域是受限的，只能在函数内部使用
    inner()
}
```

### 函数类型变量

- 定义

```kotlin
val myFun1: (Int, Int) -> Int

// 无返回值要显式指定Unit
val myFun2: (Int) -> Unit
```

- 初始化

```kotlin
val myFun1: (Int, Int) -> Int

myFun1 = fun(x: Int, y: Int): Int {
    return x + y
}

// 返回固定值的简写
myFun1 = fun(x: Int, y: Int): Int = 666

// 直接引用现成的函数来初始化函数类型变量
fun main() {
    val myFun1: (Int, Int) -> Int = ::sum
}

fun sum(x: Int, y: Int): Int {
    return x + y
}
```

- 调用函数

```kotlin
println(myFun1(1, 5))
```

### 函数类型参数

接收函数类型参数的函数称为高阶函数

```kotlin
fun main() {
    val myFun4 = fun(x: Int) {
        println(x)
    }

    myFun3(myFun4)
}

// 函数可以接收函数作为参数
fun myFun3(myFun4: (Int) -> Unit) {
    println(myFun4(666))
}
```

### 函数作为函数返回类型

```kotlin
val myFun6: (Int) -> ((Long) -> Int)

// 为了提高可读性，可以起别名
typealias myFun5 = (Long) -> Int

fun main() {
    val myFun6: (Int) -> myFun5
}
```

由于函数可以作为变量且函数可以接收函数作为参数

可以无限套娃了

### lambda表达式

```kotlin
// 普通写法
val myFun1: (Int) -> String = { p ->
    // 最后一行作为返回
    "parameter: $p"
}

val myFun1: (Int) -> String = {
    // 如果只有一个参数，可以不指定形参，用默认名称为it
    // 最后一行作为返回
    "parameter: $it"
}

println(myFun1(666))


// 多个参数要指定形参名称才能使用
val myFun2: (Int, Int) -> Int = { x, y ->
    x + y
}

// 假设不使用第一个参数，可以用下划线来表示
val myFun2: (Int, Int) -> Int = { _, y ->
    0 + y
}
```

#### 高阶函数调用

```kotlin
fun test(afun: (Int) -> Unit) {
    print(afun(666))
}

// 普通写法
test({
    println("parameter: $it")
})

// 如果一个函数的‌最后一个参数‌是函数类型，可以将该参数移到圆括号之外
test() {
    println("parameter: $it")
}

// 如果该函数‌只有‌这一个函数类型参数，由于括号里面已经没有其它参数了，那么括号也可以省略
test {
    println("parameter: $it")
}
```

#### return写法

lambda中，不能直接使用return返回结果，需要使用标签

```kotlin
// 可以自定义标签名称@
val myFun1: (Int) -> String = myLabel@{
    if (it < 0) {
        // 返回是@标签名称
        return@myLabel "$it < 0"
    }
    "$it >= 0"
}
println(myFun1(-666))


fun main() {
    // 高阶函数调用尾随lambda时，默认标签名称是函数名
    test {
        if (it < 0) {
            return@test "$it < 0"
        }
        "$it >= 0"
    }
}

fun test(afun: (Int) -> String) {
    println(afun(-666))
}
```
