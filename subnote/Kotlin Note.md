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

## 语法

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

// lambda
val sum = { x: Int, y: Int -> x + y }

sum(1, 2)

// 如果一个函数的‌最后一个参数‌是一个 Lambda 表达式（函数类型），可以将该 Lambda 移到圆括号 () 之外
// 如果该函数‌只有‌这一个 Lambda 参数，那么圆括号可以完全省略
```
