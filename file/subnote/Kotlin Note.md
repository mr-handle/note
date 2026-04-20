# Kotlin Note

- 主方法

```kotlin
fun main() {
    println("Hello, world!!!")
}
```

- 数据类型：Boolean、Int、Float、Double、String

- 变量声明

```kotlin
// 定义常量：val 变量名: 数据类型 = 变量值
val count: Int = 2

// 定义变量：var 变量名: 数据类型 = 变量值
var count: Int = 2

// 通过类型推断确定变量的数据类型
val count = 2


println("count: $count")
```

- 定义方法

```kotlin
// 无参无返回值方法
fun 方法名() {
    // 方法体
}

// 调用方法
方法名()

// 无参有返回值方法
// 如果不指定返回类型，默认是Unit，相当于java中的Void
fun 方法名() : 返回类型 {
    // 方法体
    return statement
}


// 调用方法
val result = 方法名()

// 有参有返回值方法
fun 方法名(参数名1: 参数类型, 参数名2: 参数类型, ...) : 返回类型 {
    // 方法体
    return statement
}

// 指定默认参数值
fun 方法名(参数名1: 参数类型 = 默认值, 参数名2: 参数类型, ...) : 返回类型 {
    // 方法体
    return statement
}

// 调用方法
val result = 方法名(参数1, 参数2, ...)

// 可以不同的顺序传递实参
val result = 方法名(参数名2 = 参数值, 参数名1 = 参数值, ...)
```
