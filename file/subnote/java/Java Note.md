# Java Note

## Java基础

- 文件夹名称一律用小驼峰，并且单个单词的文件夹名称一律用单数形式
    - 项目名用英文单词小写加`-`连接符定义，和maven的项目坐标名保持风格一致
- 变量命名：变量名+变量类型，这样可以快速知道变量属于什么类型，并且不用输入那么多字母就弹出来代码补全了，省的都是时间啊
- 用来存储对象的变量称为引用变量
- 方法名有final修饰，表示此方法是终结方法，不能被子类重写
- 可变长参数在一个方法中最多只能有一个，并且必须放在最后
- Java编译器自动引入java.lang
- `方法的签名`（signature）: 要完整地描述一个方法，需要指出`方法名`以及`参数类型`
    - 返回类型不是方法签名的一部分。也就是说， 不能有两个名字相同、参数顺序和类型也相同，但是返回不同类型值的方法
- VO（View Object）：视图对象，前端和控制层之间的数据传输对象
- DTO（Data Transfer Object）：数据传输对象，控制层与服务层之间的数据传输对象
- DO（Domain Object）：领域对象，就是从现实世界中抽象出来的有形或无形的业务实体
- PO（Persistent Object）：持久化对象，它跟持久层（通常是关系型数据库）的数据结构形成一一对应的映射关系
    - 如果持久层是关系型数据库，那么，数据表中的每个字段（或若干个）就对应PO的一个（或若干个）属性
- 不用public、protected、private修饰的字段和方法就是包作用域。位于同一个包的类，可以访问包作用域的字段和方法

- 定义数据字典/下拉选项/枚举时，0不要作为all，而是用具体的表的字段值，让前端来增加下拉all，当用户选择all时，传null给后端就行了

### JDK

- `JRE` 是 Java 运行时环境（Java Runtime Environment）
    - 它是运行已编译 Java 程序所需的所有内容的集合，包括 Java 虚拟机（JVM），Java 类库，java 命令和其他的一些基础构件
    - 但是，它不能用于创建新程序
- `JDK` 是 Java Development Kit 缩写，它是功能齐全的 Java SDK
    - 它拥有 JRE 所拥有的一切
    - 还有编译器（javac）和工具（如 javadoc 和 jdb）
    - 它能够创建和编译程序

#### 安装JDK

Oracle JDK的协议：jdk17+都是NFTC协议：免费使用 + 更新3年，3年后如果想要获得更新就要付费订阅了，当然可以选择不更新嘿嘿

GraalVM Community Edition（CE）：GPLv2 + Classpath Exception协议，开源免费，虽然没有内置javafx，但是通过maven依赖引入javafx后，进行编译可以通过

Oracle GraalVM：GFTC（GraalVM Free Terms and Conditions）协议，免费使用 + 更短更新周期（与Oracle JDK 的NFTC类似）

Liberica Native Image Kit：LibericaJDK + GraalVM CE，对spring框架做了优化，如果需要编译javafx应用，需要下载完全版，标准版编译会报错的，跟GraalVM CE有点区别

##### 1.windows安装jdk

- 1.下载jdk压缩包xxx.zip，解压到指定目录（解压后bin文件夹所在目录为jdk根目录，假设为`D:\jdk\xxx`）
- 2.新增环境变量`JAVA_HOME：D:\jdk\xxx`
- 3.`Path`中添加：`%JAVA_HOME%\bin`

##### 2.linux安装jdk

- 1.下载jdk压缩包xxx.tar.gz，解压到指定目录

```sh
# 新建目录
mkdir -p 指定目录

tar -zxvf xxx.tar.gz -C 指定目录
```

- 2.设置环境变量（解压后bin文件夹所在目录为jdk根目录，假设为`/opt/jdk/xxx`）

```sh
# /etc/profile 是系统级的shell启动配置文件，它作用于所有用户的登录shell
# 启动一个交互式非登录shell（比如打开一个新的终端窗口），这时会读取 ~/.bashrc，目前笔者用的是这个
# 当通过SSH登录远程服务器，或在图形界面登录后启动一个shell，会读取~/.bash_profile
# 增加/删除环境变量并重新加载配置文件后，打开一个新的终端窗口
# 如果环境变量是定义在~/.bashrc，则该环境变量是生效的
# 如果环境变量是定义在~/.bash_profile，则该环境变量不会生效，直到重启才会生效
# 下面以/etc/profile为例，其它两个文件类似

# 1.先备份/etc/profile（或~/.bashrc或~/.bash_profile）
cp /etc/profile /etc/profile.bk

# 2.编辑profile文件
vi /etc/profile

# 3.在profile文件末尾添加
export JAVA_HOME=/opt/jdk/xxx
export PATH=$PATH:${JAVA_HOME}/bin
```

- 3.重新加载配置文件

```sh
source /etc/profile
```

- 4.测试

```sh
java -version
```

### Java源文件

- 若将多个类的声明放在一个文档中，只能有一个类声明为公有类

```java
public class A {}

class B {}
```

- Java源文件名必须与文档中公有类名一致（区分大小写）

```java
// 文件名必须为 A.java
public class A {}

class B {}
```

### 构造方法

- 调用构造方法的具体处理步骤：
    - 1.如果构造方法的第一行调用了另一个构造方法，则基于所提供的参数执行后者
    - 2.否则，
        - a）所有字段初始化为默认值（0、false或null）
        - b）按照在类声明中出现的顺序，执行字段初始化和初始化块
    - 3.执行构造方法主体代码

- java25开始，构造方法中，super(...) 或 this(...) 调用不是必须作为第一条语句出现了，可以在此之前做一些字段校验、初始化了

```java
public class Employee {
    // 1. 执行字段声明的初始化
    private Long id = 1L;

    {
        // 2. 执行初始化块的初始化
        id = 2L;
    }

    public Employee() {
        // 3. 执行构造方法的初始化
        id = 3L;
    }
}

@Test
public void test5() {
    // 3
    System.out.println(new Employee().getId());
}
```

### 静态工厂方法（static factory method）

对于类而言，为了让客户端获取它自身的一个实例，最传统的方法就是提供一个公有的构造方法

此外，还可以提供一个公有的静态工厂方法，它只是一个返回类的实例的静态方法

注意，静态工厂方法并不直接对应于设计模式（Design Pattern）中的工厂方法

例如：

```java
public static Boolean valueOf(boolean b) {
    return (b ? TRUE : FALSE);
}
```

- 相比于构造方法
    - 静态工厂方法顾名思义
    - 不必在每次调用它们的时候都创建一个新对象
    - 可以返回原返回类型的任何子类型的对象
    - 所返回的对象的类可以随着每次调用而发生变化，这取决于静态工厂方法的参数值
    - 方法返回的对象所属的类，在编写包含该静态工厂方法的类时可以不存在，例如JDBC API
    - 缺点：类如果只有静态工厂方法，而不含公有的或者受保护的构造方法，就不能被子类化

- 一些常见的静态工厂方法的名称
    - from，类型转换方法，它只有单个参数，返回该类型的一个相对应的实例
    - of，聚合方法，带有多个参数，返回该类型的一个实例，把它们合并起来
    - valueOf，比 from 和 of 更烦琐的一种替代方法
    - instance或getInstance，返回的实例是通过方法的参数（如果有）来描述的，但是不能说与参数具有同样的值
    - create或newInstance，像instance或getInstance，但能够确保每次调用都返回一个新的实例
    - getType，Type表示静态工厂方法所返回的对象类型，像getInstance一样，但是在静态工厂方法处于不同的类中的时候使用
    - newType，Type表示静态工厂方法所返回的对象类型，像newInstance一样，但是在静态工厂方法处于不同的类中的时候使用
    - type，getType 和 newType的简版，如：`Collections.list(...)`

### 构建器（Builder）

构造方法和静态工厂方法有个共同的局限性：它们都不能很好地扩展到大量的可选参数

- 这种场景，虽然可以用重叠构造器（telescoping constructor）
    - 第一个构造方法只有必要的参数
    - 第二个构造方法有一个可选参数
    - 第三个构造方法有两个可选参数
    - 依此类推，最后一个构造方法包含所有可选的参数

```java
public class Pet {
    // 必要字段
    private final String name;

    // 可选字段
    private final Integer age;
    private final BigDecimal weight;

    private Pet(String name) {
        this(name, 1);
    }

    private Pet(String name, Integer age) {
        this(name, age, new BigDecimal("1.0"));
    }
    private Pet(String name, Integer age, BigDecimal weight) {
        this.name = name;
        this.age = age;
        this.weight = weight;
    }
}
```

重叠构造器可行，但是当有许多参数的时候，代码会很难编写，并且仍然较难以阅读

一长串类型相同的参数可能还会导致一些微妙的错误，如不小心颠倒了参数顺序

- 为此，还有第二种代替办法，即JavaBeans模式
    - 先调用一个无参构造器来创建对象
    - 然后再调用 setter方法来设置每个必要的参数，以及每个相关的可选参数

```java
@Setter
public class Pet {
    // 必要字段
    private String name;

    // 可选字段
    private Integer age;
    private BigDecimal weight;
}
```

- Java Beans模式不足之处
    - 在构造过程中Java Bean可能处于不一致的状态，使用处于不一致状态的对象导致的问题，调试定位十分困难
    - 使得把类做成不可变的可能性不复存在，需要付出额外的努力来确保它的线程安全

综上，有了第三种替代方法，它既能保证像重叠构造器模式那样的安全性，也能保证像JavaBeans模式那么好的可读性

- 那就是使用构建器，它是构建器设计模式的一种形式
    - 定义
        - 首先，创建一个静态内部类Builder，Builder的字段跟外部类一致
        - 然后，根据外部类的必要字段定义Builder的构造方法
        - 然后，根据外部类的可选字段定义字段设置方法
        - 然后，定义一个build方法，通关外部类的私有构造方法，创建外部类的实例并返回
        - 最后，在外部类定义一个私有构造方法，参数是Builder实例，根据Builder实例的字段初始化所有外部类的同名字段
    - 使用
        - 先使用必要参数创建一个Builder实例
        - 再使用Builder实例，调用字段设置方法，设置可选参数
        - 最后调用build方法生成外部类的实例（通常是不可变的实例）

```java
public class Pet {
    // 必要字段
    private final String name;

    // 可选字段
    private final Integer age;
    private final BigDecimal weight;

    public static class Builder {
        // 必要字段
        private final String name;

        // 可选字段
        private Integer age;
        private BigDecimal weight;

        // Builder构造方法，初始化必要字段
        public Builder(String name) {
            this.name = name;
        }

        // 可选字段通过设值方法设置，返回builder本身
        // 这样可以把调用链接起来，得到一个流式API
        public Builder age(Integer age) {
            this.age = age;
            return this;
        }

        public Builder weight(BigDecimal weight) {
            this.weight = weight;
            return this;
        }

        // 内部类Builder的build方法创建一个外部类实例并返回
        public Pet build() {
            return new Pet(this);
        }
    }

    // 外部类的私有构造方法，传一个Builder实例作为参数
    private Pet(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.weight = builder.weight;
    }

    // 静态工厂方法，可在工厂方法中扩展逻辑
    // 封装性和灵活性比用Builder的构造方法更好
    // 链式调用更自然：Pet.builder(...).age(...).weight(...).build();
    public static Builder builder(String name) {
        return new Builder(name);
    }
}

// 使用
Pet pet = new Pet.Builder("dog")
                 .age(1)
                 .weight(new BigDecimal("15.2"))
                 .build();
```

构建器也适用于类层次结构，抽象类有抽象类的构建器，具体类有具体类的构建器

总之类的构造方法和静态工厂方法中具有多个参数，设计这种类时，构建器就是一种不错的选择，特别是当大多数参数都是可选或者类型相同的时候

### 修饰符的顺序

- 根据《Java 语言规范》的推荐，修饰符应当按照以下标准顺序进行排列：
    - Annotations
    - 访问权限修饰符：public / protected / private
    - abstract
    - static
    - final
    - transient
    - volatile
    - synchronized
    - native
    - default
    - strictfp

### 类路径

- 类路径就是JVM在运行时用来查找类文件(.class)和资源文件（*.properties、*.xml等）的一组目录或jar文件列表
- 类路径是一组路径的集合，它包括
    - 当前目录（.）
    - 某个自定义目录，里面包含了各种类文件或资源文件或jar文件
    - jar文件
        - `/path/to/file.jar`
        - `/path/to/archives/*`，会被展开，将`/path/to/archives`里面的jar/zip文件加入到类路径

- 根目录：类路径中，类型为目录的都是根目录
- 执行`java -jar file.jar`时，JVM会忽略-cp，并且类路径只来自：
    - file.jar 本身
    - file.jar 的MANIFEST里的Class-Path字段（如果有）
    - Spring Boot Launcher构造的虚拟classpath（如果是fat jar）
        - BOOT-INF/classes + BOOT-INF/lib/*.jar

#### 执行Java程序

##### 通过指定类路径和主类执行Java程序

如果我们要执行一个jar包，就可以把jar包放到类路径中，这样JVM会自动在xxx.jar文件里去搜索主类执行Java程序

```sh
# .表示当前路径，假设主类路径为：./com/handle/HelloWorld.class
java -classpath . com.handle.HelloWorld

# ./HelloWorld.jar 表示当前目录下的HelloWorld.jar文件，假设其包含主类：com.handle.HelloWorld.class
java -classpath ./HelloWorld.jar com.handle.HelloWorld
```

##### 通过`java -jar file.jar`执行Java程序

```sh
java -jar file.jar
```

### ClassName.class.getResource

常用于加载与当前类同包的资源、加载classpath根下的资源（以`/`开头）

```java
ClassName.class.getResource(resourceName)
```

- ClassName.class.getResource的相对路径是以"/"结尾的

- 如果resourceName是以`/`开头
    - 如果类路径中`有`类型为目录的路径，则相对路径为类路径中的第一个类型为目录的路径（classpath根）
    - 如果类路径中`无`类型为目录的路径，
        - 如执行`java -cp file.jar MainClass`或`java -jar file.jar`，则相对路径为jar文件内部的虚拟根目录，用url表示为：`jar:file:/path/to/file.jar!/`

        - 如果是执行`java -jar file.jar`，并且file.jar是Spring Boot的可执行jar文件，则用url表示相对路径为：`jar:file:/path/to/file.jar!/BOOT-INF/classes/`

- 如果resourceName非以`/`开头，则相对路径为ClassName类所在包的目录
    - 如果ClassName.class在一个目录中，则用url表示相对路径为：`file:/path/to/directory/`
    - 如果ClassName.class在一个jar文件中，则用url表示相对路径为：`jar:file:/path/to/file.jar!/path/to/directory/`

### ClassName.class.getClassLoader().getResource

常用于加载classpath根下的资源

和ClassName.class.getResource("/" + resourceName)功能一样

```java
// resourceName不能以`/`开头
ClassName.class.getClassLoader().getResource(resourceName)
```

### 二进制知识

#### 原码、反码、补码

- 原码：正数，它的绝对值转换成的二进制数；负数，它的绝对值转换成的二进制数，然后最高位置1

- 反码：正数的反码与原码相同；负数的反码为其原码`除符号位`外`按位取反`

- 补码：正数的补码与原码相同；负数的补码为其反码加1

- 正数的二进制数为其原码，负数的二进制数为其补码

```java
// 原码 10000000 00000000 00000000 00000010
// 反码 11111111 11111111 11111111 11111101
// 补码 11111111 11111111 11111111 11111110
String b = Integer.toBinaryString(-2); 
```

#### 进制转换

- 一个字节8位，存放二进制范围0B00000000～0B11111111

- 一个字节8位，分成2组，每组4位，得到2位的16进制数，范围0X00~0XFF

- Java中，byte、short、int、long分别占1、2、4、8个字节，char占2个字节

##### 二进制转十进制

```java
// 分别计算二进制每一位的值，然后求和就得到10进制的数值了
// 从右往左数位数，每一位的值为2^(位数-1)次方 * 该位的二进制值
// 0B0000 1010
// 2^(8-1) * 0 + 2^(7-1) * 0 + 2^(6-1) * 0+ 2^(5-1) * 0 + 2^(4-1) * 1 +2^(3-1) * 0 + 2^(2-1) * 1 + 2^(1-1) * 0
// 0 + 0 + 0 + 0 + 8 + 0 + 2 + 0 = 10
int a = 0B00001010;
// 10
System.out.println(a);
```

##### 二进制转16进制

```java
// 二进制数每4位为一组，分别计算每一组的值，然后组合起来就是16机制的值了
// 从右往左数位数，每一位的值为2^(位数-1)次方 * 该位的二进制值
// 0B0000 1010
// 0000为一组：2^(4-1) * 0 +2^(3-1) * 0 + 2^(2-1) * 0 + 2^(1-1) * 0 = 0 + 0 + 0 + 0 = 0
// 1010为一组：2^(4-1) * 1 +2^(3-1) * 0 + 2^(2-1) * 1 + 2^(1-1) * 0 = 8 + 0 + 2 + 0 = 10
// 组合起来得到16进制：0X0A
int a = 0B00001010;
// 0000000a
System.out.println(HexFormat.of().toHexDigits(a));
```

#### 十进制转其它进制

```java
int a = 10;
// 二进制   1010
System.out.println(Integer.toBinaryString(a)); 
// 八进制   12
System.out.println(Integer.toOctalString(a));  
// 十六进制 a
System.out.println(Integer.toHexString(a));    
```

#### byte[] 和十六进制字符串互转

- Java 8及以前版本，String用char数组存储，每个char元素2字节，转换的实质为一个1字节的byte元素，转换为一个2字节的char元素
- Java 9之后，String用byte数组存储，转换的实质为一个1字节的byte元素，转换为一个1字节的byte元素

##### 用Java自带方法转换

```java
// byte[]转换为16进制字符串
byte[] datas = {15, 15};
// 00001111 00001111，（0x0f0f) 结果为f0f（byte数组首个元素高4位为0，被去掉了) 
String hexString = new BigInteger(1, datas).toString(16);

// 推荐
// 0f0f
String hexString2 = HexFormat.of().formatHex(datas);

// byte[]转换为16进制字符串
String s = "hello world";
String hex = HexFormat.of().formatHex(s.getBytes(StandardCharsets.UTF_8));
// 68656c6c6f20776f726c64
System.out.println(hex);

// 16进制字符串转换为byte[]
byte[] bytes = HexFormat.of().parseHex(hex);
// hello world
System.out.println(new String(bytes, StandardCharsets.UTF_8));
```

##### 引入第三方库转换

```groovy
implementation 'commons-codec:commons-codec:1.17.1'
```

```java
byte[] datas = {15, 15};
// byte[] 转 16进制字符串
// 0f0f，当byte数组首个元素高4位为0时也会保留
String hexString = Hex.encodeHexString(datas);

// 16进制字符串 转 byte[]
byte[] datas = Hex.decodeHex(hexString);
```

- Hex.encodeHexString 底层代码剖析

```java
public String encodeHexString(final byte[] data) {
    char[] DIGITS_UPPER = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'};
    final int outlength = data.length;
    // 一个字节用两位16进制表示，out长度为data长度乘以2
    final char[] out = new char[outlength << 1];
    for (int i = 0, j = 0; i < outlength; i++) {
    // 取高4位，无符号右移四位，得到16进制高位
    // 0x41 = 0100 0001
    // 0xF0 = 1111 0000
    // 0x40 = 0100 0000
    // 0x04 = 0000 0100
    // 相当于 out[j++] = DIGITS_UPPER[data[i] / 16];
    out[j++] = DIGITS_UPPER[(0xF0 & data[i]) >>> 4];
    // 直接取低4位，得到16进制低位
    // 0x41 = 0100 0001
    // 0x0F = 0000 1111
    // 0x01 = 0000 0001 = 1
    // 相当于 out[j++] = DIGITS_UPPER[data[i] % 16];
    out[j++] = DIGITS_UPPER[0x0F & data[i]];
    }
    return new String(out);
}
```

#### 移位运算

对byte和short类型进行移位时，会首先转换为int再进行位移

如果移位位数大于等于该数据类型的位数（如对int类型的数据移位32位）

会先计算`该数据类型的位数 % 移位位数`，得到真正的移位位数，再进行移位操作

- 左移`<<`， 低位补0，十进制数m左移n位相当于m乘以2的n次方

```java
int m = 1;       // 00000000 00000000 00000000 00000001 = 1
int r = m << 1;  // 00000000 00000000 00000000 00000010 = 2
```

- 右移`>>`，高位补符号位，十进制数m右移n位相当于m除以2的n次方

```java
int m = 2;       // 00000000 00000000 00000000 00000010 = 2
int r = m >> 1;  // 00000000 00000000 00000000 00000001 = 1
```

- 无符号右移`>>>`，高位补0，注意，`没有无符号左移`！

```java
int m = -2;        // 11111111 11111111 11111111 11111110 = -2
int r = m >>> 1;   // 01111111 11111111 11111111 11111111 = 2147483647
```

#### 位运算

位运算是按位进行与（&）、或（|）、非（!）和异或（~，异或运算的规则是，如果两个数不同，结果为1，否则为0）的运算

### 运算优先级

在Java的计算表达式中，运算优先级从高到低依次是：

- `()`
- `! ~ ++ --`
- `* / %`
- `+ -`
- `<< >> >>>`
- `&`
- `|`
- `+= -= *= /=`

### `==`比较运算符

- 基本数据类型，`==`比较的是`值`

```java
Assertions.assertTrue(3 == 3);
```

- 复合数据类型，`==`比较的是`地址`

```java
String a = new String("handle");
String b = new String("handle");
Assertions.assertFalse(a == b);
```

- 复合数据类型，`equals`在没有覆写的情况下，比较的也是`地址`

```java
@Getter
@Setter
@ToString
@AllArgsConstructor
public class User {
  private String name;
}

User a = new User("handle");
User b = new User("handle");
Assertions.assertFalse(a == b);
```

### 继承

- 继承写法：
    - 子类名 extends 父类名
    - 子类名 implements 父接口名
    - 子接口名 extends 父接口名

- 子类拥有父类所有的字段和方法（包括私有字段和私有方法）

- 父类中的私有字段和方法子类是无法访问，只是拥有，严禁定义与父类重名的字段

- 子类不能继承父类的静态属性，但可以对父类静态属性操作

- 任何class的构造方法，第一行语句必须是调用父类的构造方法。如果没有明确地调用父类的构造方法，编译器会帮我们自动加一句super();

- 从Java 15开始，允许使用sealed修饰class，并通过permits明确写出能够从该class继承的子类名称

- 如果一个父类的方法本身不需要实现任何功能，仅仅是为了定义方法签名，目的是让子类去覆写它，那么，可以把父类的方法声明为抽象方法；
    - 因为这个抽象方法本身是无法执行的，所以，父类也无法被实例化，因而这个父类也必须申明为抽象类

- 如果一个抽象类没有字段，所有方法全部都是抽象方法，就可以把该抽象类改写为接口

- 接口主要用于对类的行为进行约束，实现了某个接口就具有了对应的行为

- 抽象类主要用于代码复用，强调的是所属关系

#### 接口

```java
public interface InterfaceName {
    // 接口中的字段只能是public static final类型的，不能被修改且必须有初始值
    // 可以不写public static final
    type fieldName = value;

    // 接口中的抽象方法默认是public abstract，可以不写public abstract
    returnType abstractName(...);

    // Java 8引入的default方法用于提供接口方法的默认实现，可以在实现类中被覆盖
    default void defaultMethodName() {
        // todo
    }

    // Java 8引入的static方法无法在实现类中被覆盖，只能通过接口名.staticMethodName(...)直接调用，类似于类中的静态方法
    // static方法通常用于定义一些通用的、与接口相关的工具方法，一般很少用
    static void staticMethodName() {
        // todo
    }

    // Java 9引入的private方法可以用于在接口内部共享代码，不对外暴露
    // 私有静态方法，可以被static和default方法调用
    private static void methodName() {
        // todo
    }

    // 私有实例方法，只能被default方法调用
    private void methodName() {
        // todo
    }
}
```

#### 继承关系判断

```java
// 判断实例是否为某个类型：（子）实例 instanceof （父）类型
Assertions.assertTrue("handle" instanceof Object);

  // 判断能子类型能否向上转型：父类型类实例.isAssignableFrom(子类型类实例);
Assertions.assertTrue(Object.class.isAssignableFrom(Integer.class));
```

##### instanceof模式匹配

- 从Java16开始，判断instanceof后，可以直接转型为指定变量，避免再次强制转型。

```java
 Object object = "hello";
if (object instanceof String s) {
    // 可以直接使用变量s:
    System.out.println(s.toUpperCase());
}
```

### 定义常量

```java
public class C() {
    // 定义类常量
    private final double PI =3.14;

    // 定义局部常量
    public void fun() {
        final double PI =3.14;
    }
}
```

### 静态方法中获取所在类实例

```java
Class clazz = MethodHandles.lookup().lookupClass();
```

### 打印对象内存地址

```java
Object object = new Object();
// 方法1，推荐
String hexAddress1 = Integer.toHexString(System.identityHashCode(object));

// 方法2，前提是没有重写toString()
String hexAddress2 = object.toString().substring(object.toString().indexOf("@") + 1);
```

### 拷贝

- 引用拷贝：两个不同的引用指向同一个对象

- 浅拷贝：
    - 浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点）
    - 如果原对象内部的字段是引用类型的话，直接复制内部对象的引用地址

- 深拷贝：完全复制整个对象，包括这个对象所包含的内部对象

![拷贝对比](/images/拷贝对比.png)

### hashCode()

hashCode() 的作用是获取哈希码，这个哈希码的作用是确定该对象在哈希表中的索引位置

### 操作文件

#### File

- 新建文件

```java
// 新建文件
File file = new File("/path/to/newfile.suffix");
if (file.createNewFile()) {
    // 删除文件
    if (file.delete()) {
        System.out.println("new file had been deleted!");
    }
}

// 新建临时文件，指定文件名、后缀和文件路径
File file = File.createTempFile("temp", ".txt", new File("/path/to/directory"));
// 在JVM退出时自动删除该文件
file.deleteOnExit();
```

- 新建目录

```java
File directory = new File("/path/to/directory");
if (!directory.exists()) {
    // 创建当前File对象表示的目录，父目录必须存在
    // directory.mkdir();
    // 创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来
    directory.mkdirs();
    
    File subdirectory = new File("/path/to/directory/subdirectory");
    // 先删除子目录，子目录不为空时无法删除父目录
    subdirectory.delete();

    // 删除父目录
    directory.delete();
}
```

- 遍历目录

```java
File file = new File("/path/to/directory");

// 列出所有文件和子目录,但不包括子目录下的文件和目录
// 遍历文件名
String[] list = file.list();
for (String item : list) {
    System.out.println("filename: " + item);
}

// 遍历文件
File[] files = file.listFiles();
for (File item : files) {
    System.out.println("file: " + item);
}

// 过滤文件
File[] filterFiles = file.listFiles(new FilenameFilter() {
    @Override
    public boolean accept(File dir, String name) {
        // 返回true表示接受该文件
        return name.endsWith(".yaml");
    }
});

// 遍历过滤出来的文件
for (File item : filterFiles) {
    System.out.println("filter: " + item);
}
```

- 获取文件路径

```java
// 表示JVM的当前工作目录，和System.getProperty("user.dir")一样
// idea：项目根目录
// java -jar file.jar：执行这条命令时所在的目录
String path = new File("").getCanonicalPath();

// 路径分隔符，不同系统平台有不同的值，Windows是"\"，Linux是"/"
System.out.println(File.separator);

File file = new File("..");

// getPath()，返回构造方法传入的路径，不会解析"."、".."，不会解析符号链接，不做任何规范化
// 输出：..
System.out.println(file.getPath());

// getAbsolutePath()，返回绝对路径，不会解析"."、".."，不会解析符号链接，不做任何规范化
// 输出：/path/to/userDir/..
System.out.println(file.getAbsolutePath());

// getCanonicalPath()，它和绝对路径类似，但是返回的是规范路径,就是解析"."、".."和符号链接，得到标准的绝对路径
// 输出：/path/to
System.out.println(file.getCanonicalPath());
```

#### 读取文件

- 读取resources目录下的文件

```java
// 通过class.getResourceAsStream，要加前缀/
InputStream input = Xxx.class.getResourceAsStream("/fileName");

// 通过classLoader.getResourceAsStream，不需要加前缀
InputStream input = Xxx.class.getClassLoader().getResourceAsStream("fileName");

// 通过classLoader.getResource，不需要加前缀
URL url = Xxx.class.getClassLoader().getResource("fileName");
```

- 复制文件

```java
try (InputStream inputStream = Application.class.getClassLoader().getResourceAsStream(fileName);
    BufferedInputStream in = new BufferedInputStream(inputStream);
    BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream(outputFileName));) {
    int length = 0;
    byte[] buffer = new byte[1024];
    while (-1 != (length = in.read(buffer))) {
        out.write(buffer, 0, length);
    }
}
```

### Java数据类型

#### 基本数据类型和对应的包装类型

|基本数据类型|占字节数|对应的包装类型|
|:-|:-|:-|
|byte|1|Byte|
|short|2|Short|
|int|4|Integer|
|long|8|Long|
|float|4|Float|
|double|8|Double|
|boolean|Java语言规范中没有定义，由具体的JVM决定|Boolean|
|char|2|Character|

Byte,Short,Integer,Long这4种包装类默认创建了相应类型的[-128，127]的缓存数据

Character创建了[0,127]的缓存数据

Boolean创建了Boolean.TRUE、Boolean.FALSE

对于Integer，可以通过JVM参数`-XX:AutoBoxCacheMax=<size>`修改缓存上限，但不能修改下限-128

```java
// 除了Boolean，可以通过"包装类型名称.SIZE" 获得该基本数据类型占用的位数
// 除了Boolean，可以通过"包装类型名称.BYTES"获得该基本数据类型占用的字节数
Assertions.assertEquals(16, Character.SIZE);
Assertions.assertEquals(2, Character.BYTES);
```

装箱过程是通过调用包装器的 `valueOf` 方法实现的，而拆箱过程是通过调用包装器的 `xxxValue` 方法实现的

#### String

##### intern方法

- jdk1.7起，方法的功能是：尝试将这个字符串对象放入字符串常量池
    - 如果字符串常量池中已经有了，则不会再放入，返回已有的字符串常量池中的对象的地址
    - 如果字符串常量池中没有，则会把对象的引用地址复制一份（jdk1.6是复制对象），放入字符串常量池，并返回字符串常量池中的引用地址

```java
// 在堆中创建"hello"对象，s1指向堆中它
// new String("hello")的参数"hello"作为字面量，被加入到字符串常量池（字符串常量池创建对象"hello"）
String s1 = new String("hello");
// 字符串常量池已经有"hello"了，intern()不会再把它加入池中
s1.intern();
// s2指向字符串常量池中的"hello"
String s2 = "hello";
// 由于s1指向堆中的对象，s2指向字符串常量池中的对象，所以它们不相等，结果是false
System.out.println(s1 == s2);

// 1. 在堆中创建StringBuilder对象
// 2. 在堆中创建"a"对象
// 3. 字符串常量池中创建对象"a"
// 4. 在堆中创建"b"对象
// 5. 字符串常量池中创建对象"b"
// 6. 在堆中创建"ab"对象（StringBuilder的toString()方法，会调用new String()，但是由于new String()的参数不是字面量，所以字符串常量池不会创建"ab"对象）
// 7. s3指向堆中的"ab"对象
String s3 = new String("a") + new String("b");
// 检查常量池是否已有 "helloworld"对象，发现没有，于是把s3的引用加入到字符串常量池中
s3.intern();
// s4指向字符串常量池中的"ab"对象
String s4 = "ab";
// 由于s3指向堆中的对象，和s4指向字符串常量池中的对象，它们指向的是同一个地址，所以结果是true
System.out.println(s3 == s4);
```

#### BigDecimal

使用`new BigDecimal(String val)`构造方法来创建对象，精度不会丢失

使用`BigDecimal.valueOf(double val)`静态方法创建对象，当数值有效位数很多的时候，还是会丢失精度
    - 因为此方法内部其实执行了Double.toString，按double的实际能表达的精度对尾数进行了截断

- 等值比较应该用compareTo()方法，而不是equals()方法；因为equals()方法会比较值和精度，而compareTo()方法比较的时候会忽略精度

```java
BigDecimal bigDecimal = new BigDecimal("1234567890.1234567890");
// 精确输出：1234567890.1234567890
System.out.println(bigDecimal);

BigDecimal bigDecimal2 = BigDecimal.valueOf(1234567890.1234567890);
// 精度丢失：1234567890.1234567
System.out.println(bigDecimal2);
```

- RoundingMode.HALF_UP，四舍五入，在大量运算时，结果偏向大数，使得误差产生积累进而产生系统误差

- RoundingMode.HALF_EVEN，四舍六入五成双，在大量运算时，它使舍入后的结果误差的均值趋于零
    - 小于等于4时舍去（1.1->1，-1.1->-1）
    - 大于等于6时进1（1.6->2，-1.6->-2）
    - 等于5时
        - 如果5后面还有数
            - 并且为0，舍去（2.50->2，-2.50->-2）
            - 并且不为0，进1（2.51->3，-2.51->-3）
        - 如果5后面没有数
            - 5前面为奇数，进1（5.5->6，-5.5->-6）
            - 5前面为偶数，舍去（2.5->2，-2.5->-2）

- 除法计算要指定结果精度

```java
BigDecimal b1 = new BigDecimal("2.0");
BigDecimal b2 = new BigDecimal("0.3");
// 结果四舍五入，保留3位小数
BigDecimal result = b1.divide(b2, 3, RoundingMode.HALF_UP);
Assertions.assertEquals("6.667", result.toString());
```

#### 枚举

- 枚举:所有的枚举类型都是`Enum`类的子类。在比较两个枚举类型的值时， 永远不需要调用equals, 而直接使用`==`。
- 如果需要的话， 可以在枚举类型中添加一些构造器、方法和域
- 构造器默认是`private`，只在构造枚举常量的时候被调用

```java
@Getter
public enum ColorEnum {
    RED("red"), GREEN("green"), BLUE("blue");

    private String value;

    private ColorEnum(String value) {
        this.value = value;
    }
}
```

#### 泛型

泛型一般有三种使用方式：泛型接口、泛型类、泛型方法

##### 泛型接口

- 定义泛型接口

```java
public interface InterfaceName<T> {
    T methodName(T t);
}
```

- 实现泛型接口

```java
// 实现类不指定类型
public class ClassName<T> implements InterfaceName<T> {
    @Override
    public T methodName(T t) {
        return t;
    }
}

// 实现类指定类型
public class ClassName implements InterfaceName<String> {
    @Override
    public String methodName(String s) {
        return s;
    }
}
```

##### 泛型类

- 定义泛型类

```java
public class ClassName<T> {
    private T fieldName;

    public ClassName(T fieldName) {
        this.fieldName = fieldName;
    }

    public T getFieldName() {
        return fieldName;
    }
}
```

- 创建泛型类的对象
  
```java
泛型类名<具体类型> objectName = new 泛型类名<>();
```

##### 泛型方法

- 定义泛型方法

```java
public class ClassName {
    // 静态泛型方法
    public static <T> void staticMethodName(T t) {
        System.out.println(t);
    }

    // 实例泛型方法
    public <T> T methodName(T t) {
        return t;
    }
}
```

- 使用泛型方法

```java
ClassName.staticMethodName("static");
System.out.println(new ClassName().methodName("instance"));
```

##### 通配符类型(wildcard type)

- 在Java库中， 使用变量E表示集合的元素类型
- K和V分别表示表的关键字与值的类型
- T(需要时还可以用临近的字母U和S)表示“任意类型”

##### 类型变量的限定（子类型限定）

- `<T extends SupperType>`，表示T应该是绑定类型的子类型，T和绑定类型可以是类，也可以是接口

- 一个类型变量或通配符可以有多个限定，限定类型用`&`分隔，而逗号用来分隔类型变量。例如：`T extends Comparable & Serializable`

- 在Java的继承中，可以根据需要拥有多个接口超类型，但限定中至多有一个类
- 如果用一个类作为限定，它必须是限定列表中的第一个
- 为了提高效率，应该将标签接口（即没有方法的接口）放在边界列表的末尾

##### 通配符的超类型限定

- `<? super BoundingType>`，表示这个通配符限制为绑定类型的所有超类型。

- 直观地讲，带有超类型限定的通配符可以向泛型对象写入，带有子类型限定的通配符可以从泛型对象读取。

##### 无限定通配符<?>

#### 数组

##### Arrays.toString()

- 不用for循环快速打印一维数组

```java
Integer[] array = new Integer[]{1, 2, 3};
// [1, 2, 3]
System.out.println(Arrays.toString(array));
```

##### Arrays.deepToString()

- 不用for循环快速打印多维数组

```java
Integer[][] array = new Integer[][]{{1, 2, 3}, {4, 5, 6}};
// [[1, 2, 3], [4, 5, 6]]
System.out.println(Arrays.deepToString(array));
```

##### Arrays.sort()

- 数组排序

```java
Integer[] array = new Integer[]{1, 3, 2};
Arrays.sort(array);
// [1, 2, 3]
System.out.println(Arrays.toString(array));
```

#### List

- 定义集合类型的变量时，应该尽可能地使用接口类型，而不要使用具体的实现类型。

```java
List<Integer> list = ArrayList<>();
```

- 以集合作为参数的任何方法，参数应该尽可能地使用接口类型，而不要使用具体的实现类型。

```java
// 推荐
public void function(List<Integer> list) {}

// 不推荐
public void function(ArrayList<Integer> list) {}
```

- 生成List

```java
// List.of生成
List<Integer> list = List.of(1, 2, 3);

// Stream.of生成
List<Integer> list = Stream.of(1, 2, 3).toList();

// IntStream.rangeClosed生成
List<Integer> list = IntStream.rangeClosed(1, 3).boxed().toList();

// Stream.iterate：依次生成一系列值
List<Integer> list = Stream.iterate(0, n -> n + 1)
    .limit(3)
    .collect(Collectors.toList());
```

- 删除列表元素

```java
List<Integer> list = IntStream.of(1, 2, 3).boxed().collect(Collectors.toList());

// 方法1
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    if (Objects.equals(iterator.next(), 2)) {
        iterator.remove();
    }
}

// 方法2
list.removeIf(item -> Objects.equals(item, 2));

System.out.println(list);
```

#### 队列

- 队列的方法

|操作|throw Exception|返回false或null|
|:-|:-|:-|
|添加元素到队尾|add(E e)|boolean offer(E e)|
|取队首元素并删除|E remove()|E poll()|
|取队首元素但不删除|E element()|E peek()|

注意：不要把null添加到队列中，否则poll()方法返回null时，很难确定是取到了null元素还是队列为空

- LinkedList

LinkedList即实现了List接口，又实现了Queue接口，但是，在使用的时候，如果我们把它当作List，就获取List的引用，如果我们把它当作Queue，就获取Queue的引用

```java
// 这是一个List:
List<String> list = new LinkedList<>();
// 这是一个Queue:
Queue<String> queue = new LinkedList<>();
```

#### 双端队列

|操作|Queue|Deque|
|:-|:-|:-|
|添加元素到队尾|add(E e) / offer(E e)|addLast(E e) / offerLast(E e)|
|取队首元素并删除|E remove() / E poll()|E removeFirst() / E pollFirst()|
|取队首元素但不删除|E element() / E peek()|E getFirst() / E peekFirst()|
|添加元素到队首|无|addFirst(E e) / offerFirst(E e)|
|取队尾元素并删除|无|E removeLast() / E pollLast()|
|取队尾元素但不删除|无|E getLast() / E peekLast()|

注意：Deque接口实际上扩展自Queue，因此，Queue提供的add()/offer()方法在Deque中也可以使用，但是，使用Deque，推荐总是明确调用offerLast()/offerFirst()或者pollFirst()/pollLast()方法，这样更加顾名思义。

Deque是一个接口，它的实现类有ArrayDeque和LinkedList。

```java
// 推荐用ArrayDeque
Deque<Integer> deque = new ArrayDeque<>();
Deque<String> deque = new LinkedList<>();
```

#### 栈

用Deque可以实现栈的功能，注意只调用push()/pop()/peek()方法，避免调用Deque的其他方法

|操作|Deque|
|:-|:-|
|入栈|push(E)/addFirst(E)|
|出栈|pop()/removeFirst()|
|取栈顶元素但不弹出|peek()/peekFirst()|

#### Map

Stream的Collectors.toMap当value为null时会抛异常，解决办法：使用collect，直接调用map中的put()方法

```java
List<String> list = Arrays.asList("张三", "李四", "王五", null);
Map<String, String> hashMap = list.stream().collect(HashMap::new, (map, item) -> map.put(item, item), HashMap::putAll);
// {null=null, 李四=李四, 张三=张三, 王五=王五}
System.out.println(hashMap);
```

#### HashMap

HashMap的结构简单理解为数组+链表/红黑树

数组中的每一个元素就是一个桶（bucket）

每个桶刚开始是一个链表，后面随着链表变长可能会转为红黑树

链表/红黑树里面存的是键值对：Node<K,V>

当把键值对加入到HashMap时，HashMap

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    // AbstractMap和Map接口先不管它

    //  初始化容量，必须是2的n次方，默认16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    // 最大容量不能超过2³⁰
    static final int MAXIMUM_CAPACITY = 1 << 30;

    // 当元素数量达到容量的75%时触发扩容
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    // 默认当某个桶里的链表长度 ≥ 8 时，HashMap会尝试把链表转成红黑树
    static final int TREEIFY_THRESHOLD = 8;

    // 默认当树节点数量减少到 ≤ 6 时，红黑树会退化回普通链表
    static final int UNTREEIFY_THRESHOLD = 6;

    // 默认只有当HashMap的整体容量 ≥ 64 时，桶内链表才允许树化
    // 也就是说当HashMap的整体容量 ≥ 64 时，并且桶内链表长度 ≥ 8 时，才会将该链表转化为红黑树 
    static final int MIN_TREEIFY_CAPACITY = 64;

    // Node内部类，简单把Node当成存放键值对的链表就行了，Map.Entry是一个接口，先不管它
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;
        // 省略一些方法
    }

    // 红黑树
    // TreeNode<K,V> 继承了 LinkedHashMap.Entry<K,V>
    // LinkedHashMap.Entry <K,V>继承了 HashMap.Node<K,V>
    // TreeNode其实是Node的子类
    static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;

        final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab, int h, K k, V v)；
    }

    // LinkedHashMap.Entry<K,V>继承了HashMap.Node<K,V>
    static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }

    // HashMap数组，每个元素是一个Node<K,V>，代表链表或红黑树
    transient Node<K,V>[] table;

    // entrySet字段用来存储entrySet()方法返回的视图对象
    // 为了避免每次调用entrySet()方法都new一个新的视图对象
    // HashMap在第一次调用时创建一个EntrySet对象，缓存在entrySet字段里，后续调用直接返回entrySet
    transient Set<Map.Entry<K,V>> entrySet;

    // HashMap当前存储的键值对数量
    transient int size;

    // HashMap的“结构性修改计数器”
    // 每当HashMap的结构发生变化（增加、删除、扩容），modCount就会+1
    // 它的主要作用是让迭代器在遍历过程中检测到HashMap是否被外部修改，从而触发fail‑fast
    transient int modCount;

    // HashMap的扩容阈值，HashMap在put新元素之后，发现size超过threshold才扩容
    // 值为：capacity * loadFactor
    int threshold;

    // HashMap 的“负载因子”，用于决定扩容阈值（threshold）
    final float loadFactor;

    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    // 待分析
    // 先调用对象的hashCode()方法，得到一个“哈希值”，并通过内部散列函数对这个哈希值再做一次简单的转换（比如取余），决定这条数据应该放进数组的哪一个桶
    // 如果该桶当前是空的，就直接将键值对添加到这个桶的Node<K,V>对象中
    // 如果该桶中已经有其他元素，HashMap会在这个桶的Node<K,V>对象（链表或红黑树）中逐个比较
    // 如果key的哈希值Node<K,V>对象中当前元素的key的哈希值不同，继续比对Node<K,V>对象中的下一个元素
    // 如果key的哈希值Node<K,V>对象中当前元素的key的哈希值相同，则会进一步调用equals()方法来检查这两个对象是否“相等”
    // 如果equals()返回true，说明HashMap中已存在与当前key相同的Node<K,V>对象的元素，HashMap将其value更新
    // 如果equals()返回false，说明HashMap中还没有该键值对，会将该键值对加入到Node<K,V>对象（链表或红黑树）中

    final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        // n存放HashMap数组长度
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            // 如果HashMap数组为null或长度为0，初始化HashMap数组
            n = (tab = resize()).length;
        // 计算索引，将HashMap数组的元素（也就是链表/红黑树头节点）赋给p
        // 由于长度n为2的幂，(n - 1) & hash 和 hash % n等价，但是前者更快
        // & 是按位与，用来取低位
        if ((p = tab[i = (n - 1) & hash]) == null)
            // 如果HashMap数组对应索引位置的元素为null
            // 新建链表
            tab[i] = newNode(hash, key, value, null);
        else {
            // 如果HashMap数组对应索引位置的元素不为null
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                // 如果p的哈希值和put的参数key的哈希值相同
                // 并且p.key和key相等
                // 将p赋给e
                e = p;
            else if (p instanceof TreeNode)
                // 如果p是红黑树，走TreeNode.putTreeVal方法
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                // 如果以上二者都不是
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        // 如果p的下一节点为null，则创建节点并将其赋给p.next
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            // 如果链表符合条件，转成红黑树
                            // 当前链表索引binCount，因为上一步创建了新节点了所以当前节点不是最后一个节点，TREEIFY_THRESHOLD - 1
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }

    // 将符合条件的链表转成红黑树
    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
}
```

#### TreeMap

TreeMap以Key的顺序来进行排序，TreeMap的Key必须实现Comparable接口，如果作为Key的class没有实现Comparable接口，那么，必须在创建TreeMap时同时指定一个自定义排序算法

```java
Map<Integer, String> map = new TreeMap<>();
map.put(6, "赵六");
map.put(3, "张三");
map.put(5, "王五");
map.put(4, "李四");
List<String> sortNames = new ArrayList<>(map.values());
// [张三, 李四, 王五, 赵六]
System.out.println(sortNames);
```

#### HashSet

下面简单分析一下HashSet源码中的关键字段和方法

```java
// 声明了一个HashMap类型的字段
transient HashMap<E,Object> map;

// 声明了一个Object类型的常量字段
static final Object PRESENT = new Object();

// Set<Type> set = new HashSet<>()实际上是new了一个HashMap并赋给map字段
public HashSet() {
    map = new HashMap<>();
}

// add方法实际上是调用HashMap类型的map字段的put方法，key是要添加的元素，value是PRESENT
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

#### Concurrent 集合

| interface | non-thread-safe         | thread-safe                              |
|:--------- |:----------------------- |:---------------------------------------- |
| List      | ArrayList               | CopyOnWriteArrayList                     |
| Map       | HashMap                 | ConcurrentHashMap                        |
| Set       | HashSet / TreeSet       | CopyOnWriteArraySet                      |
| Queue     | ArrayDeque / LinkedList | ArrayBlockingQueue / LinkedBlockingQueue |
| Deque     | ArrayDeque / LinkedList | LinkedBlockingDeque                      |

使用这些并发集合与使用非线程安全的集合类完全相同。以`ConcurrentHashMap`为例：

```java
Map<String, String> map = new ConcurrentHashMap<>();
// 在不同的线程读写:
map.put("A", "1");
map.put("B", "2");
String a = map.get("A");
```

`java.util.Collections`工具类还提供了一个旧的线程安全集合转换器，可以这么用：

```java
Map unsafeMap = new HashMap();
Map safeMap = Collections.synchronizedMap(unsafeMap);
```

但是它实际上是用一个包装类包装了非线程安全的`Map`，然后对所有读写方法都用`synchronized`加锁，这样获得的线程安全集合的性能比`java.util.concurrent`集合要低很多，所以不推荐使用。

#### Comparable接口和Comparator接口

Comparable接口出自java.lang包，它有一个 compareTo(Object obj)方法

Comparator接口出自java.util包，它有一个compare(Object obj1, Object obj2)方法

#### 日期/时间

##### Date/Calendar

```java
// 获取当前时间1
Date date1 = new Date();
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println(simpleDateFormat.format(date1));

// 获取当前时间2
Calendar calendar = Calendar.getInstance();
Date date2 = calendar.getTime();
System.out.println(simpleDateFormat.format(date2));
```

##### TimeZone

```java
// 列出系统支持的所有时区ID
String[] availableIDs = TimeZone.getAvailableIDs();
System.out.println(Arrays.toString(availableIDs));

// 当前时区
TimeZone timeZone = TimeZone.getDefault();
System.out.println(timeZone);

// GMT+08:00、Asia/Shanghai都是有效的时区ID
TimeZone timeZone1 = TimeZone.getTimeZone("GMT+08:00");
TimeZone timeZone2 = TimeZone.getTimeZone("Asia/Shanghai");
```

##### Timestamp

- 创建Timestamp

```java
Timestamp timestamp = Timestamp.from(Instant.now());

// 获取时间戳
long timestamp1 = new Date().getTime();
long timestamp2 = System.currentTimeMillis();
long timestamp3 = Instant.now().toEpochMilli();
```

##### Instant

```java
// 获取当前时刻
Instant instant1 = Instant.now();
Instant instant2 = new Date().toInstant();
Instant instant3 = Calendar.getInstance().toInstant();
Instant instant4 = OffsetDateTime.now().toInstant();
Instant instant5 = ZonedDateTime.now().toInstant();
```

##### OffsetDateTime

- 某一时刻的值，具有不变性，用来做持久化和网络传输

- 简单理解为OffsetDateTime = LocalDateTime + ZoneOffset

- ZoneOffset可以看作偏移量，正负18

- 创建OffsetDateTime

```java
OffsetDateTime offsetDateTime = OffsetDateTime.now();

OffsetDateTime offsetDateTime = ZonedDateTime.now().toOffsetDateTime();
```

##### ZonedDateTime

- 用来做显示

- 简单理解为ZonedDateTime = LocalDateTime + ZoneId

- ZoneId确定了偏移量如何改变的规则（比如由于夏令时的存在，同一个地点（ZoneOffset），同一个时间，在不同日期的显示的结果是不一样的，用OffsetDateTime来显示是不对的）

- 创建ZonedDateTime

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now();

ZonedDateTime zonedDateTime = OffsetDateTime.now().toZonedDateTime();

// 显示为中国北京时间
zonedDateTime = zonedDateTime.withZoneSameInstant(ZoneId.of("Asia/Shanghai"));
```

##### 互转工具类

```java
@NoArgsConstructor(access = AccessLevel.PRIVATE)
public final class DateTimeUtil {
    public static long ZonedDateTimeToTimestamp(ZonedDateTime zonedDateTime) {
        // zonedDateTime.toEpochSecond() * 1000会丢失毫秒值，不建议使用
        return zonedDateTime.toInstant().toEpochMilli();
    }

    public static Date ZonedDateTimeToDate(ZonedDateTime zonedDateTime) {
        long timestamp = ZonedDateTimeToTimeStamp(zonedDateTime);
        return new Date(timestamp);
    }

    public static Calendar ZonedDateTimeToCalendar(ZonedDateTime zonedDateTime) {
        long timestamp = ZonedDateTimeToTimeStamp(zonedDateTime);
        Calendar calendar = Calendar.getInstance();
        calendar.clear();
        calendar.setTimeZone(TimeZone.getTimeZone(zonedDateTime.getZone().getId()));
        calendar.setTimeInMillis(timestamp);
        return calendar;
    }

    public static ZonedDateTime CalendarToZonedDateTime(Calendar calendar) {
        Instant instant = calendar.toInstant();
        return instant.atZone(calendar.getTimeZone().toZoneId());
    }
}
```

#### Record类型

java16开始，可以使用Record类型

- record 声明的类
    - 是一个final类，不能继承其它类，但是可以实现一个或多个接口
    - 所有字段是`private final`的
    - 自带：包含所有字段的构造器、各个字段的访问器（`实例.字段名()`）、equals()，hashCode() 方法以及 toString() 方法

```java
public record Pet(
        // 在此声明字段
        String name,
        int age
) {
    // 可选的静态字段
    public static final Pet DEFAULT_PET = new Pet("dog", 1);

    // 可选的紧凑构造器 (Compact Constructor)，无需写参数列表和赋值语句（编译器会自动处理）
    // 可以写一些参数校验逻辑
    public Pet {
        if (Objects.isNull(name) || name.isBlank()) {
            throw new IllegalArgumentException("Name cannot be empty");
        } else {
            name = name.trim();
        }
        if (age < 0 || age > 50) {
            throw new IllegalArgumentException("Age cannot be negative or greater than 50");
        }
    }

    // 可选的实例方法
    public boolean isAdultPet() {
        return age > 1;
    }

    // 可选的静态方法
    public static Pet getDefaultPet() {
        return DEFAULT_PET;
    }
}
```

### try-with-resource的新写法

java9开始，在 try-with-resources 语句中可以使用 effectively-final 变量（在初始化后从未更改的变量）

但是像下面的例子，传统的写法更合适，因为FileInputStream和FileOutputStream的受检异常FileNotFoundException反而要另外处理了

```java
@Test
public void test() throws FileNotFoundException {
    BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream("demo.txt"));
    BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(new FileOutputStream("demo.txt"));
    try (bufferedInputStream; bufferedOutputStream) {

    } catch (IOException exception) {
        throw new RuntimeException(exception.getLocalizedMessage());
    }
}
```

### var关键字

java10开始，定义局部变量时可以用var关键字

```java
var string = "hello world";
var list = Stream.iterate(0, n -> n + 1).limit(3).collect(Collectors.toList());
```

### switch语句

java14开始，switch的新写法

switch 表达式中就多了一个关键字`yield`，用于跳出 switch 块，主要用于返回一个值

在使用 yield 时，需要有 default 条件

```java
String weather = "sunny";
switch (weather) {
    case "sunny" -> System.out.println("晴天");
    case "raining" -> System.out.println("雨天");
}
```

#### switch模式匹配

java21开始，switch-case可以使用模式匹配

```java
static String patternMatchingWithSwitch(Object object) {
    // switch-case 使用类型模式来进行匹配
    return switch (object) {
        case Long number    -> "Long Object of " + number;
        case String string  -> "String Object of " + string;
        default             -> "Unknown Object of " + object;
    };
}
```

### 文本块

java15开始，可以使用String的文本块写法

```java
// \ : 表示行尾不引入换行符
// \s：表示单个空格

// 输出：
// line 1: hello world
// line 2
String string = """
            line 1: he\
            llo\sworld
            line 2
            """;
```

### 进程API

java9开始，可以使用ProcessHandle进程API获取进程信息

```java
// 获取当前正在运行的 JVM 的进程
ProcessHandle currentProcess = ProcessHandle.current();
// 输出进程的 id
System.out.println(currentProcess.pid());
// 输出进程的信息
System.out.println(currentProcess.info());
```

### 多线程

#### synchronized

synchronized 同步语句块的实现使用的是 monitorenter 和 monitorexit 指令

其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置

如果有第二个monitorexit 指令，这是为了保证锁在同步代码块代码出现异常的情况下锁能被正确释放

synchronized 修饰的方法并没有 monitorenter 指令和 monitorexit 指令，取而代之的是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法

不过，两者的本质都是对对象监视器 monitor 的获取

- 修饰实例方法，锁当前对象实例

```java
public synchronized void method() {
    // todo
}
```

- 修饰静态方法，锁当前类的class实例

```java
public synchronized static void method() {
    // todo
}
```

- 修饰代码块（锁指定对象/类的class实例）

```java
// 尽量不要使用 synchronized(String a) 因为 JVM 中，字符串常量池具有缓存功能
synchronized(object/clazz) {
    // todo
}
```

- 加锁二次判断

```java
private static volatile ObjectMapper objectMapper;

// 要二次判断，加锁前判断一次，加锁后判断一次
public static void methodName() {
    if (Objects.isNull(objectMapper)) {
        synchronized (ClassName.class) {
            if (Objects.isNull(objectMapper)) {
                objectMapper = applicationContext.getBean(ObjectMapper.class);
            }
        }
    }
}
```

#### ReentrantLock

ReentrantLock 实现了 Lock 接口，是一个可重入且独占式的锁，和 synchronized 关键字类似

不过，ReentrantLock 更灵活、更强大，增加了轮询、超时、中断、公平锁和非公平锁等高级功能

synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API

synchronized和ReentrantLock都是不可中断锁，但是ReentrantLock的lockInterruptibly()和tryLock(long time, TimeUnit unit) 是可中断的

公平锁 : 锁被释放之后，先申请的线程先得到锁。性能较差一些，因为公平锁为了保证时间上的绝对顺序，上下文切换更频繁。

非公平锁：锁被释放之后，后申请的线程可能会先获取到锁，是随机或者按照其他优先级排序的。性能更好，但可能会导致某些线程永远无法获取到锁。

notifyAll()方法，会通知所有处于等待状态的线程

signalAll()方法，只会唤醒注册在该Condition实例中的所有等待线程

#### ReentrantReadWriteLock

适合读多写少的场景

#### StampedLock

适合读多写少的场景，但它是不可重入锁，性能比ReentrantReadWriteLock好

#### CountDownLatch

一个同步辅助类，在完成一组正在其它线程中执行的操作之前，它允许一个或多个线程一直等待

适用于需要等待一组任务完成后再继续执行的场景，比如主线程等待多个子线程执行完毕

计数器达到零后，它不能再被重用

```java
/**
 * 模拟场景：所有学生离开教室后，班长才能锁门
 */
@Test
public void test() {
    int counter = 5;

    // 1.创建CountDownLatch，设置计数值
    CountDownLatch countDownLatch = new CountDownLatch(counter);

    for (int i = 0; i < counter; i++) {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " 号学生离开教室");
            // 2.CountDownLatch计数值减一
            countDownLatch.countDown();
        }, String.valueOf(i)).start();
    }

    try {
        // 3.等待直到CountDownLatch计数值为0
        countDownLatch.await();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println(Thread.currentThread().getName() + " 所有学生离开教室，班长锁门");
}
```

#### CyclicBarrier

一个同步辅助类，它允许一组线程互相等待，直到到达某个公共屏障点

主要应用场景和 CountDownLatch 类似，但是功能比 CountDownLatch 更加复杂和强大

适用于多个线程需要在某个点进行协调，然后一起继续执行的场景，比如并行任务之间的阶段性同步

所有线程都到达屏障点并继续执行后，屏障会重置以供下一次使用

```java
/**
 * 模拟场景：收集七颗龙珠才能召唤神龙
 */
@Test
public void test() {
    int counter = 7;
    // 创建CyclicBarrier，指定目标障碍数，并且指定目标障碍数达到后做什么
    CyclicBarrier cyclicBarrier = new CyclicBarrier(counter, () -> {
        System.out.println("集齐七颗龙珠，召唤神龙");
    });

    for (int i = 0; i < counter; i++) {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " 星龙珠已收集");
            try {
                // 等待
                cyclicBarrier.await();
            } catch (InterruptedException | BrokenBarrierException e) {
                e.printStackTrace();
            }
        }, String.valueOf(i)).start();
    }
}
```

#### Semaphore

一个计数信号量，用来控制同时访问特定资源的线程数量

通常用于那些资源有明确访问数量限制的场景比如限流（仅限于单机模式，实际项目中推荐使用 Redis +Lua 来做限流）

```java
/**
 * 模拟场景：6辆车，停3个车位
 */
@Test
public void test() {
    int counter = 3;
    // 1.创建Semaphore，指定许可数
    Semaphore semaphore = new Semaphore(counter);

    for (int i = 0; i < 6; i++) {
        new Thread(() -> {
            try {
                // 2.获取许可（抢占车位）
                semaphore.acquire();
                System.out.println(Thread.currentThread().getName() + " 车抢到车位");
                
                // 设置随机停车时间
                TimeUnit.MILLISECONDS.sleep((long) Math.random()* 100);
                
                System.out.println(Thread.currentThread().getName() + " --车离开车位--");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                // 3.释放许可
                semaphore.release();
            }
        }, String.valueOf(i)).start();
    }
}
```

#### FutureTask

```java
// Thread构造方法只能传Runnable，Runnable的run()方法无返回值
// 因为FutureTask实现了RunnableFuture（RunnableFuture扩展了Runnable）
// 并且FutureTask的构造方法可以传Callable，Callable的call()方法有返回值
// 因此可以用Thread的构造方法传FutureTask，FutureTask的构造方法传Callable，调用FutureTask的get()方法实现结果返回
FutureTask<String> futureTask = new FutureTask<String>(() -> "hello world");
new Thread(futureTask).start();
try {
    System.out.println(futureTask.get());
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```

#### CompletableFuture

- `xxx()`：表示该方法将继续在已有的线程中执行；
- `xxxAsync()`：表示将异步在线程池中执行。
- `xxx()`有对应的异步方法`xxxAsync()`
- 通常而言，名称中不带Async 的方法和它的前一个任务一样，在同一个线程中运行
- CompletableFuture的静态工厂方法

- CompletableFuture的静态方法

|方法名|描述|
|:-|:-|
|runAsync(Runnable runnable)|使用ForkJoinPool.commonPool()作为它的线程池执行异步代码，无返回值|
|runAsync(Runnable runnable, Executor executor)|使用指定的thread pool执行异步代码，无返回值|
|supplyAsync(`Supplier<U>` supplier)|使用ForkJoinPool.commonPool()作为它的线程池执行异步代码，有返回值|
|supplyAsync(`Supplier<U>` supplier, Executor executor)|使用指定的thread pool执行异步代码，有返回值|

- CompletableFuture的实例方法

|方法名|描述|
|:-|:-|
|exceptionally|执行异步代码发生异常时执行，如果有返回值，可以指定一个异常发生时的返回值，另外需要注意exceptionally方法会返回一个新的CompletableFuture，更推荐链式调用的方式定义exceptionally方法|
|whenComplete|future完成或异常时执行|
|handle|可以处理正常的返回结果和异常，并返回一个新的结果，如果在链式调用的时候抛出异常，则可以在最后使用handle来接收|
|thenRun|无返回值的future执行成功后执行|
|thenAccept|有返回值的future执行成功后执行|
|runAfterBoth|等待两个无返回值的future完成，然后执行|
|thenAcceptBoth|等待两个有返回值的future完成，然后执行|
|thenCompose|step1.thenCompose() 将 step1 的结果作为输入，对两个异步操作进行流水线，返回新的CompletableFuture，阻塞|
|thenApply|step1.thenApply() 将 step1 的结果作为输入，同一个CompletableFuture，不阻塞|
|thenCombine|两个有返回值的future完成，然后合并其结果|
|join|获取future返回结果，阻塞|
|acceptEither|两个future任意一个执行完毕，无返回值|
|applyToEither|两个future任意一个执行完毕，有返回值|
|runAfterEither|两个future任意一个执行完毕，无返回值|
|allOf|等待所有future都执行完毕，无返回值|
|anyOf|任意个future只要一个成功，返回值类型为Object|
|complete(T t)|完成异步执行，并返回future的结果|
|completeExceptionally(Throwable ex)|抛出future执行异常|

```java
// 需要定义exceptionally或whenComplete打印异常日志，不然如果不调用future.get()异常将会丢失
// exceptionally和whenComplete定义其一就好了
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    System.out.println(Thread.currentThread().getName() + " 线程执行了");
    int a = 1 / 0;
    return "success";
}, executor).exceptionally((exception) -> {
    // exceptionally方法会返回一个新的CompletableFuture，更建议用链式调用
    log.error("error", exception);
    // 更建议在exceptionally中打印异常日志
    // 然后可以抛出异常或者指定一个return返回值
    // 当抛出异常时，在whenComplete中，异常可以被拿到
    // 当指定一个返回值时，在whenComplete中，异常将丢失
    // throw new RuntimeException(exception);
    return "executed error";
}).whenComplete((result, exception) -> {
    if (Objects.nonNull(exception)) {
        log.error("error", exception);
    }
});
// System.out.println(future.get());
```

#### ForkJoin

```java
class AaccumulateTask extends RecursiveTask<Integer> {
    // 设置拆分阈值，只要大于阈值，就继续拆分
    private static final int THRESHOLD = 10;

    // 拆分开始值
    private int beginValue;

    // 拆分结束值
    private int endValue;

    // 计算结果
    private int result;

    public AaccumulateTask(int beginValue, int endValue) {
        this.beginValue = beginValue;
        this.endValue = endValue;
    }

    @Override
    protected Integer compute() {
        if ((endValue - beginValue) <= THRESHOLD) {
            for (int i = beginValue; i <= endValue; i++) {
            result += i;
            }
        } else {
            int middleValue = (beginValue + endValue) / 2;
            // 拆分左边
            AaccumulateTask leftTask = new AaccumulateTask(beginValue, middleValue);
            // 拆分右边
            AaccumulateTask rightTask = new AaccumulateTask(middleValue + 1, endValue);

            leftTask.fork();
            rightTask.fork();

            // 合并结果
            result = leftTask.join() + rightTask.join();
        }
        return result;
    }
 
}
public class ForiJoinDemoTest {
    /**
     * 计算从0加到100的和 
     */
    @Test
    public void test() {
        try(ForkJoinPool forkJoinPool = new ForkJoinPool()) {
            ForkJoinTask<Integer> forkJoinTask = forkJoinPool.submit(new AaccumulateTask(0, 100));;
            Integer result = forkJoinTask.get();
            System.out.println(result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

#### 线程池

- 处理器核的数目：cpuNumbers = Runtime.getRuntime().availableProcessors();
- CPU利用率：cpuUsage = `(0, 1]`，通常设置为0.8 ~ 0.9
    - 如果设为 1.0，意味着 CPU 将全速运转，没有任何空闲周期来处理操作系统中断、后台进程或其他突发的高优先级任务，这会导致系统响应变慢甚至假死
    - 设置为0.8 ~ 0.9，既能保证 CPU 资源被充分利用（不浪费钱），又能保留 10%-20% 的缓冲空间，防止因瞬间流量洪峰导致 CPU 飙升到 100% 从而引发系统雪崩
- 线程等待时间：waitTime = 线程运行总时间 - 线程计算时间
- 线程计算时间：computeTime
- 建议线程池大小：`threadSize = cpuNumbers * cpuUsage * (1 + waitTime / computeTime)`
    - 可以通过VisualVM 来查看 waitTime / computeTime 比例
    - CPU 密集型任务，几乎没有等待，waitTime约等于0，CPU利用率又期望达到100%，公式就变成了threadSize = cpuNumbers + 1
        - 加1是因为即使是 CPU 密集型任务，线程偶尔也会因为以下原因暂停
            - 缺页中断 (Page Fault)： 需要从内存加载数据
            - 上下文切换： 操作系统调度
            - 如果线程数严格等于核心数，一旦某个线程因为缺页中断暂停了，CPU 就会空闲下来
            - 多加的那“1”个线程，就是为了在这个空隙中填补上来，确保 CPU 始终有事可做
    - I/O 密集型任务，会有大量等待，waitTime / computeTime就会很大，CPU利用率需要留缓冲，就完全套用上面的公式了
        - 一个简单并且适用面比较广的公式就是：threadSize = 2 * cpuNumbers
- 建议设置线程大小上限：maxThreadSize = 100，不知道这个说法是参考哪里的了，还是套用上面的公式来计算吧

##### 自定义线程池

```java
// 1.常驻线程数量
int corePoolSize = Runtime.getRuntime().availableProcessors();
// 2.最大线程数量
int maximumPoolSize = 100;
// 3.非常驻线程空闲时存活时间
long keepAliveTime = 15;
// 4.时间单位
TimeUnit unit = TimeUnit.SECONDS;
// 5.阻塞队列
// 常驻线程都在执行任务了，这时候再添加的任务，就会放到阻塞队列等待执行；
// 如果阻塞队列也满了，这时候再添加的任务，就会创建新的线程去处理任务；
// 如果这时候达到了最大线程数，就会走拒绝策略了
BlockingQueue<Runnable> workQueue = new ArrayBlockingQueue<>(corePoolSize);
// 6.线程工厂,一般用默认即可
ThreadFactory threadFactory = Executors.defaultThreadFactory();
// 7.拒绝策略，正在执行的线程数量达到了最大线程数，并且阻塞队列也满了，这时候再添加任务，就会走拒绝策略了
// 四种拒绝策略：AbortPolicy 抛异常拒绝处理新任务（默认策略）；CallerRunsPolicy哪个线程提交的任务由哪个线程处理，如果该线程已经结束则丢弃该任务；
// DiscardPolicy 不做处理，丢弃新任务；DiscardOldestPolicy丢弃阻塞队列里面等待时间最长的任务
RejectedExecutionHandler handler = new ThreadPoolExecutor.AbortPolicy();

try (
// 创建自定义线程池
ThreadPoolExecutor executorService = 
    new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, threadFactory, handler)) {
    
for (int i = 0; i < 20; i++) {
    int count = i;
    // 任务提交到线程池执行
    executorService.submit(() -> {
        System.out.println(Thread.currentThread().getName() + " 执行了任务" + count);
    });
    }
}
```

##### 优雅关闭线程池

```java
void shutdownAndAwaitTermination(ExecutorService pool) {
    // Disable new tasks from being submitted
    pool.shutdown();
    try {
        // Wait a while for existing tasks to terminate
        if (!pool.awaitTermination(60, TimeUnit.SECONDS)) {
            // Cancel currently executing tasks
            pool.shutdownNow();
            // Wait a while for tasks to respond to being cancelled
            if (!pool.awaitTermination(60, TimeUnit.SECONDS)) {
                log.warn("Pool did not terminate");
            }
        }
    } catch (InterruptedException ex) {
        // (Re-)Cancel if current thread also interrupted
        pool.shutdownNow();
        // Preserve interrupt status
        Thread.currentThread().interrupt();
    }
}
```

##### 线程池处理异常

- executorService.execute默认不会吞掉异常

- executorService.submit默认会吞掉异常，如果发生了异常，调用future.get时会抛出异常

- 因此可以在创建线程池的时候重写afterExecute方法，手动调用future.get来达到executorService无论调用哪个方法执行线程，都默认抛出异常的效果

```java
ThreadPoolExecutor executorService =
    new ThreadPoolExecutor(12, 24, 30, TimeUnit.SECONDS, new ArrayBlockingQueue<>(12), Executors.defaultThreadFactory(),
    new ThreadPoolExecutor.AbortPolicy()) {
    // 这个方法只对executor.submit和executor.execute方法的异常处理有效
    // 如果是用CompletableFuture执行，还是需要定义它的exceptionally或whenComplete打印异常日志
    // 不然如果不调用future.get()异常还是会丢失
    @Override
    public void afterExecute(Runnable runnable, Throwable throwable) {
        super.afterExecute(runnable, throwable);
        if (Objects.isNull(throwable) && runnable instanceof Future<?> future && future.isDone()) {
            try {
                // 如果发生了异常，调用future.get时会抛出异常
                future.get();
            } catch (CancellationException cancellationException) {
                throwable = cancellationException;
            } catch (ExecutionException executionException) {
                throwable = executionException.getCause();
            } catch (InterruptedException interruptedException) {
                Thread.currentThread().interrupt();
            }
        }
        if (throwable != null) {
            log.error(Thread.currentThread().getName() + " execute error", throwable);
        }
    }
};
```

##### 虚拟线程池

虚拟线程（Virtual Thread）是 JDK 而不是 OS 实现的轻量级线程(Lightweight Process，LWP），由 JVM 调度。

许多虚拟线程共享同一个操作系统线程，虚拟线程的数量可以远大于操作系统线程的数量

虚拟线程适用于 I/O 密集型任务，但不适用于计算密集型任务，因为密集型计算始终需要 CPU 资源作为支持

虚拟线程属于非常轻量级的资源，因此，用时创建，用完就扔，可以不用池化虚拟线程

```java
@Test
public void test() {
    ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();
    executor.execute(() -> System.out.println("hello virtual thread"));
    executor.execute(() -> System.out.println(Thread.currentThread().isVirtual()));
    executor.close();
}
```

###### Spring Boot配置虚拟线程

- 配置文件

```yaml
spring:
    threads:
        virtual:
            # 对@Async、@Scheduled注解的方法使用虚拟线程，自定义的ThreadPoolTaskExecutor是不会生效的
            enabled: true
    main:
        # This ensures that the JVM is kept alive, even if all threads are virtual threads
        keep-alive: true
```

- 自定义一个AsyncTaskExecutor，以便在Spring MVC中使用

```java
@Slf4j
@Setter
@ConfigurationProperties("thread.pool.task.executor")
@Configuration
public class ThreadPoolConfiguration {
    private int maxPoolSize;

    @Bean("applicationTaskExecutor")
    public SimpleAsyncTaskExecutor applicationTaskExecutor() {
        // 不能像ThreadPoolTaskExecutor那样自定义异常处理，只可以设置最大线程数、线程名称前缀
        SimpleAsyncTaskExecutor simpleAsyncTaskExecutor = new SimpleAsyncTaskExecutor("application-");
        simpleAsyncTaskExecutor.setVirtualThreads(true);
        simpleAsyncTaskExecutor.setConcurrencyLimit(maxPoolSize);
        return simpleAsyncTaskExecutor;
    }
}
```

- 使用虚拟线程

```java
// 使用的是自定义的虚拟线程，线程名称前缀是自定义的前缀：pplication-
@Resource(name = "applicationTaskExecutor")
private SimpleAsyncTaskExecutor simpleAsyncTaskExecutor;

@Test
public void test() {
    simpleAsyncTaskExecutor.execute(() -> System.out.println("hello virtual thread"));
    simpleAsyncTaskExecutor.execute(() -> System.out.println(Thread.currentThread().isVirtual()));
}

// 使用的是spring.threads.virtual.enabled=true配置的虚拟线程
// 线程名称前缀是：tomcat-handler-
@Async
public void asyncMethod() {
    // todo
}
```

##### Spring的线程池

- 自带优雅关闭线程池的骚操作

###### ThreadPoolTaskExecutor

- 自定义线程池配置

```yaml
thread:
    pool:
        task:
            executor:
                corePoolSize: 6
                maxPoolSize: 12
                queueCapacity: 18
```

- 读取配置并注入ThreadPoolTaskExecutor

```java
@Slf4j
@Setter
@ConfigurationProperties("thread.pool.task.executor")
@Configuration
public class ThreadPoolConfiguration {
    private int corePoolSize;

    private int maxPoolSize;

    private int queueCapacity;

    @Bean
    public ThreadPoolTaskExecutor threadPoolTaskExecutor() {
        ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor() {
            // 这个方法只对executor.submit和executor.execute方法的异常处理有效
            // 如果是用CompletableFuture执行，还是需要定义它的exceptionally或whenComplete打印异常日志
            // 不然如果不调用future.get()异常还是会丢失
            @Override
            public void afterExecute(Runnable runnable, Throwable throwable) {
                super.afterExecute(runnable, throwable);
                if (Objects.isNull(throwable) && runnable instanceof Future<?> future && future.isDone()) {
                    try {
                        // 如果发生了异常，调用future.get时会抛出异常
                        future.get();
                    } catch (CancellationException cancellationException) {
                        throwable = cancellationException;
                    } catch (ExecutionException executionException) {
                        throwable = executionException.getCause();
                    } catch (InterruptedException interruptedException) {
                        Thread.currentThread().interrupt();
                    }
                }
                if (throwable != null) {
                    log.error(Thread.currentThread().getName() + " execute error", throwable);
                }
            }
        };
        threadPoolTaskExecutor.setCorePoolSize(corePoolSize);
        threadPoolTaskExecutor.setMaxPoolSize(maxPoolSize);
        threadPoolTaskExecutor.setQueueCapacity(queueCapacity);
        return threadPoolTaskExecutor;
    }
}
```

- 使用demo

```java
@Slf4j
@SpringBootTest
public class ApplicationTest {
    @Resource
    private ThreadPoolTaskExecutor executor;

    @Test
    public void test() {
        // CompletableFuture需要定义exceptionally或whenComplete打印异常日志，不然如果不调用future.get()异常还是会丢失
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " 线程执行了");
            int a = 1/0;
            return "hello ThreadPoolTaskExecutor";
        }, executor);
        // future = future.exceptionally((exception) -> {
        //     log.error("error", exception); 
        //     // throw new RuntimeException(exception);
        // 经测试如果不抛异常而是设置一个返回值，future.get()不会抛出异常
        //     return "333";
        // });
        future.whenComplete((result, exception) -> {
            if (Objects.nonNull(exception)) {
                log.error("error", exception);
            }
        });
        // future.get();

        executor.submit(() -> {
            int i = 1/0;
        });
        executor.execute(() -> {
            int i = 1/0;
        });
    }
}
```

#### ThreadLocal

- ThreadLocal在本线程中定义、更新、删除，缺点：数据不共享

```java
ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 0);
try {
    // 业务逻辑
    threadLocal.set(threadLocal.get() + 1);
} finally {
    threadLocal.remove();
}
```

- InheritableThreadLocal，在父线程中定义，子线程可以获取到，缺点：使用线程池会有问题，父线程更新值后，子线程获取不到更新值

```java
InheritableThreadLocal<Integer> inheritableThreadLocal = new InheritableThreadLocal<>();
// threadLocal.set(1);

// 创建线程池
ExecutorService threadPool = Executors.newSingleThreadExecutor();

// 使用
// 第一次设置inheritableThreadLocal
inheritableThreadLocal.set(1);

threadPool.execute(() -> {
    System.out.println("第一次获取inheritableThreadLocal：" + inheritableThreadLocal.get());
});

TimeUnit.SECONDS.sleep(1);

// 第二次设置inheritableThreadLocal
inheritableThreadLocal.set(inheritableThreadLocal.get() + 1);

threadPool.execute(() -> {
    System.out.println("第二次获取inheritableThreadLocal：" + inheritableThreadLocal.get());
});
```

- TransmittableThreadLocal，InheritableThreadLocal的增强版，使用线程池：父线程更新值后，子线程也可以获取到更新值

- TransmittableThreadLocal需要添加依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>transmittable-thread-local</artifactId>
    <version>2.14.5</version>
</dependency>
```

- 使用

```Java
@Test
public void testTransmittableThreadLocal() throws InterruptedException {
    // 创建TransmittableThreadLocal
    TransmittableThreadLocal<String> transmittableThreadLocal = new TransmittableThreadLocal<>();
    // 创建线程池
    ExecutorService threadPool = Executors.newSingleThreadExecutor();
    // 线程池用TtlExecutors.getTtlExecutorService包装一下
    threadPool = TtlExecutors.getTtlExecutorService(threadPool);

    // 使用
    // 第一次设置transmittableThreadLocal
    transmittableThreadLocal.set(Thread.currentThread().getName() + " first");

    threadPool.execute(() -> {
        System.out.println("第一次获取transmittableThreadLocal：" + transmittableThreadLocal.get());
    });

    TimeUnit.SECONDS.sleep(1);

    // 第二次设置transmittableThreadLocal
    transmittableThreadLocal.set(Thread.currentThread().getName() + " second");

    threadPool.execute(() -> {
        System.out.println("第二次获取transmittableThreadLocal：" + transmittableThreadLocal.get());
    });
}
```

#### CAS

CAS 即比较并替换（Compare And Swap）是实现并发算法时常用到的一种技术

CAS 操作包含三个操作数——内存位置、预期原值及新值

执行 CAS 操作的时候，将内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值，否则，处理器不做任何操作

ABA 问题： CAS 操作本身存在 ABA 问题（一个值从 A 变为 B，再变回 A，CAS 检查时会认为值没有变过）

在某些场景下，如果值的变化历史很重要，可能需要使用 AtomicStampedReference 来解决

自旋逻辑： compareAndSwapInt 方法本身只执行一次比较和交换操作，并立即返回结果。

因此，为了确保操作最终成功（在值符合预期的情况下），我们需要在代码中显式地实现自旋逻辑（如 while(true) 循环），不断尝试直到 CAS 操作成功

CPU 消耗： 长时间的自旋会消耗 CPU 资源

在竞争激烈或条件长时间不满足的情况下，可以考虑加入更复杂的退避策略（如 Thread.sleep() 或 LockSupport.parkNanos()）来优化

#### AQS （AbstractQueuedSynchronizer ，抽象队列同步器）

AQS 是一个抽象类，定义了资源获取和释放的通用流程，而具体的资源获取逻辑则由具体同步器通过重写模板方法来实现

AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。

如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是基于 CLH 锁 （Craig, Landin, and Hagersten locks） 进一步优化实现的。

CLH 锁 对自旋锁进行了改进，是基于单链表的自旋锁。

在多线程场景下，会将请求获取锁的线程组织成一个单向队列，每个等待的线程会通过自旋访问前一个线程节点的状态，前一个节点释放锁之后，当前节点才可以获取锁

- AQS 中使用的 等待队列 是 CLH 锁队列的变体，是一个双向队列，会暂时获取不到锁的线程将被加入到该队列中，CLH 变体队列和原本的 CLH 锁队列的区别主要有两点

    - 由 自旋 优化为 自旋 + 阻塞
        - 自旋操作的性能很高，但大量的自旋操作比较占用 CPU 资源，因此在 CLH 变体队列中会先通过自旋尝试获取锁，如果失败再进行阻塞等待

    - 由 单向队列 优化为 双向队列
        - 在 CLH 变体队列中，会对等待的线程进行阻塞操作，当队列前边的线程释放锁之后，需要对后边的线程进行唤醒，因此增加了 next 指针，成为了双向队列

### jdbc

#### jdbc连接数据库

```java
final String url="jdbc:oracle:thin:@//127.0.0.1/handle";
final String userName="root";
final String password="root";
Connection connection=null;
Statement statement=null;
try {
    // 1.装载驱动器
    Class.forName("oracle.jdbc.OracleDriver");
} catch(ClassNotFoundException e) {
    log.error("", e);
}
try {
    // 2.创建连接
    connection=DriverManager.getConnection(url,userName,password);
    // 3.创建Statement对象用来操作数据库
    statement=connection.createStatement();
    // 4.执行sql语句
    ResultSet resultSet=statement.executeQuery("select * from user");
    // 5.处理执行结果
    while(resultSet.next()) { 

    }
    resultSet.close();
} catch(SQLException e) {
    log.error("", e);
}
try {
    // 6.关闭资源
    if (statement!=null) { 
        statement.close();
    }
    if(connection!=null) {
        connection.close();
    }
} catch(SQLException e) {
    log.error("", e);
}
```

#### ResultSet获取行列数

- 获取列数

```java
ResultSet resultSet=statement.executeQuery("select id, name from user");
int columnNumbers = resultSet.getMetaData().getColumnCount();
```

- 获取行数

```java
ResultSet resultSet=statement.executeQuery("select id, name from user");
int rowNumbers = resultSet.getInt(1);
```

### 判null和判空语句

- 判断对象是否为null

```java
if (null == object) {}
if (null != object) {}
if (Objects.isNull(object)) {}
if (Objects.nonNull(object)) {}
```

- 判断字符串对象s是否为空串

```java
if (Objects.isNull(s) || "".equals(s)) {}
if (Objects.isNull(s) || s.isEmpty()) {}
if (Objects.nonNull(s) && !"".equals(s)) {}
if (Objects.nonNull(s) && !s.isEmpty()) {}
```

- 集合判空

```java
if (Objects.isNull(list) || list.isEmpty()) {}
if (Objects.nonNull(list) && !list.isEmpty()) {}
```

### excel转pdf

```java
public void  els2pdf(String els,String pdf) throws Throwable{ 
    File dFile = new File(pdf);
    if (dFile.exists()) {
        dFile.delete();
    }
    ComThread.InitSTA();
    ActiveXComponent excelApp = new ActiveXComponent("Excel.Application"); //构建ActiveX组件实例
    try { 
    excelApp.setProperty("Visible",false);//设置excelApp不可见
    Dispatch workbooks = excelApp.getProperty("Workbooks").toDispatch();

    Dispatch workbook = Dispatch.invoke(
    workbooks,//要执行操作的对象
    "Open", //要执行的操作名称
    Dispatch.Method,//Dispatch.Method、Dispatch.Put、Dispatch.Get三种，指定让此方法可以执行put、set、call操作
    new Object[]{//对象数组，Method时为三个参数，Get时没有参数，Put时为两个参数
    els,
    new Variant(false),
    new Variant(false)
    },
    new int[3]//一般为new int[1]
    ).toDispatch(); 
    Dispatch.invoke(workbook, "SaveAs", Dispatch.Method, new Object[] { 
        pdf, 
        new Variant(57), 
        new Variant(false), 
        new Variant(57), 
        new Variant(57), 
        new Variant(false),
        new Variant(true), 
        new Variant(57),
        new Variant(true),
        new Variant(true), 
        new Variant(true)
        },
        new int[1]); 
        Dispatch.call(workbook, "Close",new Variant(false));
     } 
   catch (Exception e) 
   {
       System.out.println("========Error:Operation fail:" + e.getMessage()); 
   } finally {
       if (excelApp != null) {
           excelApp.invoke("Quit", new Variant[] {});
       }
   }
   ComThread.Release(); 
} 
```

### 类型转换

#### List与数组互转

- List转数组

```java
List<Integer> list = List.of(1, 2, 3);

// 如果传入的数组不够大，那么List内部会创建一个新的刚好够大的数组，填充后返回
// 如果传入的数组大小比list大小还要大，那么填充完list的元素后，位于数组后面的没被填充到的元素一律填充null
Integer[] array = list.toArray(new Integer[0]);

// 推荐更简洁的写法
Integer[] array = list.toArray(Integer[]::new);
```

- 数组转List

```java
Integer[] array = {1, 2, 3};

// 不支持修改数组
List<Integer> list0 = Arrays.asList(array);

// 不支持修改数组
List<Integer> list1 = List.of(array);

// 支持修改数组，数据量不大时适用
List<Integer> list2 = new ArrayList<>(Arrays.asList(array));

// 支持修改数组，数据量大时推荐使用
List<Integer> list3 = new ArrayList<>(array.length);
Collections.addAll(list3, array);

// 支持修改数组
List<Integer> list4 = Arrays.stream(array).collect(Collectors.toList());

// 如果是基本数据类型数组，则的到的list只有一个元素，即array2
int[] array2 = {1, 2, 3};
List list5 = Arrays.asList(array2);
```

#### String与byte[]互转

- String 转 byte[]

```java
byte[] input ="handle".getBytes(StandardCharsets.UTF_8);
```

- byte[] 转 String

```java
byte[] input = { -24, -128, -127, -27, -123, -83 };
String s = new String(input, StandardCharsets.UTF_8);
```

### foreach遍历顺序

1. 对于数组，foreach按顺序从数组的第一个元素遍历到最后一个元素；
2. 对于Iterable容器，则依照迭代器的遍历顺序

### 默认域初始化

- 如果在构造器中没有显式地给域赋予初值，那么就会被自动地赋为默认值： 数值为0、布尔值为false、对象引用为null。

- 如果在编写一个类时没有编写构造器，那么系统就会提供一个无参数构造器。这个构造器将所有的实例域设置为默认值。

- 仅当类没有提供任何构造器的时候，系统才会提供一个默认的构造器。如果类中提供了至少一个构造器，但是没有提供无参数的构造器，则在构造对象时如果没有提供参数就会被视为不合法。

- 如果希望所有域被赋予默认值，可以采用下列格式

```java
public ClassName () {}
```

- 在执行构造器之前，先执行赋值操作。当一个类的所有构造器都希望把相同的值赋予某个特定的实例域时，这种方式特别有用。可以在类定义中， 直接将一个值赋给任何域。

```java
class Person {
    private String name = "";
}
```

- 初始值不是常量值，可以调用方法对域进行初始化：

```java
class Person {
    private static int nextId;
    private int id = assignId();

    private static int assignId() {
        return nextId++;
    }
}
```

- 在一个类的声明中，可以包含多个代码块。只要构造类的对象，这些块就会被执行。在这个示例中，无论使用哪个构造器构造对象，id域都在对象初始化块中被初始化。首先运行初始化块，然后才运行构造器的主体部分。

```java
class Person {
    private static int nextId;
    private int id;

    // 初始化块
    {
        id = nextId++;
    }
}
```

### 匿名类

匿名类和你熟悉的Java局部类（块中定义的类）差不多，但匿名类没有名字。它允许你同时
声明并实例化一个类。换句话说，它允许你随用随建。

```java
Thread t = new Thread(new Runnable() {
    public void run(){
        System.out.println("Hello world");
    }
});
```

### Lambda表达式

- `Lambda`的基本语法

```java
// 单表达式（单语句）
(parameters) -> expression

// 多语句
(parameters) -> { 
    statement1; 
    statement2; 
    statementn; 
}
```

- 无参，单表达式（单语句）

```java
// 隐式返回
() -> "Handle"

// 显式返回
() -> {return "Handle";}
```

- 有参，单表达式（单语句）

```java
// 声明参数类型
(String s) -> s.length()

// 不声明参数类型
(s) -> s.length()

// 不声明参数类型，仅单个参数可以这么写
s -> s.length()
```

- 如果一个Lambda的主体是一个语句表达式，它就和一个参数列表类型、顺序一样，且返回void的函数描述符兼容

```java
// 尽管list.add(s)返回boolean，但是这个写法也是合法的
Consumer<String> c = s -> list.add(s);
```

- Lambda中使用局部变量

```java
// port必须隐式为final或显式声明为final
int port = 8888;
Runnable r = () -> System.out.println(port);
```

- Lambda方法引用

```java
List<Integer> list = IntStream.rangeClosed(1, 3).boxed().collect(Collectors.toList());
list.forEach(System.out::println);
```

- 构造函数引用

```java
Supplier<String> integer = String::new;
```

- 比较器

```java
Comparator.nullsFirst(Integer::compareTo);
Comparator.comparing(String::valueOf, Comparator.nullsFirst(Integer::compareTo));
```

- 复合Lambda

```java
// 比较器复合
students.sort(Comparator.comparing(Student::getClassName)
    .reversed()
    .thenComparing(Student::getAge));

// 谓词复合
predicate = predicate.negate().and(...).or(...)

// 函数复合
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;

// g(f(x))
Function<Integer, Integer> h = f.andThen(g);
// f(g(x))
Function<Integer, Integer> h2 = f.compose(g);
```

#### 函数式接口

- 函数式接口就是只定义一个抽象方法的接口

- 函数式接口可以有默认方法

- 函数式接口不允许抛出受检异常

- `@FunctionalInterface` 注解表明此接口是函数式接口

- Java 8中的常用函数式接口

|函数式接口|函数描述符|原始类型特化|
|:-|:-|:-|
|`Predicate<T>`|T -> boolean|IntPredicate, LongPredicate, DoublePredicate|
|`Consumer<T>`|T -> void|IntConsumer, LongConsumer, DoubleConsumer|
|`Function<T,R>`|T -> R|`IntFunction<R>`, `LongFunction<R>`, `DoubleFunction<R>`,`ToIntFunction<T>`, `ToLongFunction<T>` `ToDoubleFunction<T>`, IntToLongFunction, IntToDoubleFunction, LongToIntFunction, LongToDoubleFunction|
|`Supplier<T>`|() -> T|BooleanSupplier, IntSupplier, LongSupplier, DoubleSupplier|
|`UnaryOperator<T>`|T -> T|IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator|
|`BinaryOperator<T>`|(T,T) -> T|IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator|
|`BiPredicate<L,R>`|(L,R) -> boolean||
|`BiConsumer<T,U>`|(T,U) -> void|`ObjIntConsumer<T>`, `ObjLongConsumer<T>`, `ObjDoubleConsumer<T>`|
|`BiFunction<T,U,R>`|(T, U) -> R|`ToIntBiFunction<T, U>`, `ToLongBiFunction<T, U>`, `ToDoubleBiFunction<T, U>`|

### Stream

- 中间操作：中间操作会返回另一个流

- 终端操作：终端操作会从流的流水线生成结果。其结果是任何不是流的值。

#### 构建流

```java
// 空流
Stream<String> emptyStream = Stream.empty();

// 由值创建流
Stream<String> stream = Stream.of("a", "b", "c");

// 由数组创建流
int[] numbers = {1, 2, 3};
IntStream stream = Arrays.stream(numbers);

// 由文件生成流
Stream<String> lines = Files.lines(Paths.get("data.txt"), Charset.defaultCharset());

// 由函数生成流
List<Integer> list = Stream.iterate(1, n -> n + 1).limit(3).collect(Collectors.toList());
List<Double> list = Stream.generate(Math::random).limit(3).collect(Collectors.toList());
```

#### 规约

```java
// 统计流中元素个数
long count = numbers.stream().count();

// 使用收集器统计流中元素个数
long count = numbers.stream().collect(Collectors.counting());

// 对流中所有的元素求和，初始值为0
int sum = numbers.stream().reduce(0, (a, b) -> a + b);

// 使用方法引用
int sum = numbers.stream().reduce(0, Integer::sum);

int totalCalories = menu.stream().collect(Collectors.reducing(0, Dish::getCalories, (i, j) -> i + j));

// 考虑流中没有任何元素的情况。reduce操作无法返回其和，因为它没有初始值
Optional<Integer> sum = numbers.stream().reduce((a, b) -> (a + b)); 

// IntStream、DoubleStream和LongStream，分别将流中的元素特化为int、long和double，从而避免了暗含的装箱成本。
// 每个接口都包含常用数值归约的新方法，比如对数值流求和的sum，max、min、average等
int sum = IntStream.rangeClosed(1, 3).sum();

// 使用收集器汇总
int totalCalories = menu.stream().collect(Collectors.summingInt(Dish::getCalories));

OptionalInt max = IntStream.rangeClosed(1, 3).max();

OptionalInt min = IntStream.rangeClosed(1, 3).min();

OptionalDouble average = IntStream.rangeClosed(1, 3).average();

// 使用收集器计算平均值
double avgCalories = menu.stream().collect(Collectors.averagingInt(Dish::getCalories));

// 最大值
Optional<Integer> max = numbers.stream().reduce(Integer::max);

// 最小值
Optional<Integer> min = numbers.stream().reduce(Integer::min);

// 使用收集器计算最大值
Comparator<Dish> dishCaloriesComparator = Comparator.comparingInt(Dish::getCalories); 
Optional<Dish> mostCalorieDish = menu.stream().collect(Collectors.maxBy(dishCaloriesComparator));

// 使用收集器一次获取个数、和、最大最小、平均值
IntSummaryStatistics menuStatistics = menu.stream().collect(Collectors.summarizingInt(Dish::getCalories));

// 使用收集器连接字符串
String shortMenu = menu.stream().map(Dish::getName).collect(Collectors.joining());
```

#### 分组 Collectors.groupingBy

```java
Map<Dish.Type, List<Dish>> dishesByType = menu.stream().collect(Collectors.groupingBy(Dish::getType));
```

```java
public enum CaloricLevel { DIET, NORMAL, FAT }
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel = menu.stream().collect(
    Collectors.groupingBy(dish -> {
        if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
        }
        else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
        }
        else {
            return CaloricLevel.FAT;
        }
})); 
```

- 多级分组
  
  ```java
  Map<Dish.Type, Map<CaloricLevel, List<Dish>>> dishesByTypeCaloricLevel = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, Collectors.groupingBy(dish -> {
          if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
          }
          else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
          }
          else {
            return CaloricLevel.FAT;
          }
  })));
  ```

- 按子组收集数据
  
  ```java
  // 每类菜有多少个
  Map<Dish.Type, Long> typesCount = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, counting()));
  
  // 查找每个子组中热量最高的Dish
  Map<Dish.Type, Optional<Dish>> mostCaloricByType = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, maxBy(comparingInt(Dish::getCalories))));
  
  // 查找每个子组中热量最高的Dish, 把收集器的结果转换为另一种类型
  Map<Dish.Type, Dish> mostCaloricByType = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, collectingAndThen(maxBy(comparingInt(Dish::getCalories)), Optional::get)));
  
  // 每类菜肴热量总和 
  Map<Dish.Type, Integer> totalCaloriesByType = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, summingInt(Dish::getCalories))); 
  
  // 每种类型的Dish，菜单中都有哪些CaloricLevel
  Map<Dish.Type, Set<CaloricLevel>> caloricLevelsByType = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, mapping(dish -> { 
          if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
          }
          else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
          }
          else {
            return CaloricLevel.FAT;
            }
        },toSet())));
  
  // 每种类型的Dish，菜单中都有哪些CaloricLevel 指定set类型为HashSet
  Map<Dish.Type, Set<CaloricLevel>> caloricLevelsByType = menu.stream().collect(
      Collectors.groupingBy(Dish::getType, mapping(dish -> { 
          if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
          }
          else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
          }
          else {
            return CaloricLevel.FAT;}
        },toCollection(HashSet::new) ))); 
  ```

#### 分区 partitioningBy

分区是分组的特殊情况:最多可以分为两组——true是一组，false是一组。

```java
// 用分区找出所有的素食菜肴
Map<Boolean, List<Dish>> partitionedMenu = menu.stream().collect(partitioningBy(Dish::isVegetarian)); 
List<Dish> vegetarianDishes = partitionedMenu.get(true);

// 或者用筛选找出所有的素食菜肴
List<Dish> vegetarianDishes = menu.stream().filter(Dish::isVegetarian).collect(toList()); 

// 二级分区
Map<Boolean, Map<Dish.Type, List<Dish>>> vegetarianDishesByType =menu.stream().collect(
    partitioningBy(Dish::isVegetarian,groupingBy(Dish::getType)));

// 二级分区
Map<Boolean, Dish> mostCaloricPartitionedByVegetarian = menu.stream().collect(
    partitioningBy(Dish::isVegetarian, collectingAndThen(
        maxBy(comparingInt(Dish::getCalories)),Optional::get))); 
```

### 作用域值（ScopedValue）

Java25开始，可以使用作用域值

作用域值适用于不可变的值上下文共享（如用户 ID、链路追踪 ID），取代ThreadLocal，彻底杜绝内存泄漏问题

如果业务需要可变状态共享（如事务状态、临时计算结果），仍需使用传统的 ThreadLocal

```java
public class ScopedValueDemo {
    // 定义作用域值常量
    private static final ScopedValue<String> CONTEXT_SCOPED_VALUE = ScopedValue.newInstance();

    public void process(String userId) {
        // 使用where绑定值到作用域值常量，绑定后的值为只读
        // 使用run（无返回值）/call（有返回值）定义处理逻辑，可通过作用域值常量的get方法获取绑定的值
        // 绑定的值出作用域（run/call结束)后自动清理
        ScopedValue.where(CONTEXT_SCOPED_VALUE, userId)
                .run(() -> {
                    String value = CONTEXT_SCOPED_VALUE.get();
                    // 虚拟线程会自动继承父线程的作用域值
                    Thread.startVirtualThread(() -> {
                        System.out.println("The userId getted from SCOPED_VALUE is:" + value);
                    });
                });
    }
}
```

### 紧凑源文件和实例main方法

Java25开始，紧凑源文件和实例main方法转正

```java
// 创建一个类文件，然后直接这么写
public class NewMainThreadDemo {
    void main() {
        // todo
    }
}

// 或者这么写，这种写法开头不能有包声明
void main() {
    // todo
}
```

### 模块

在项目中创建一个模块描述文件（module-info.java），就可以将项目声明为一个模块

- 在maven项目中，`module-info.java`要放在`src/main/java`目录下

```java
// 声明一个模块
module com.handle.hellofx {
    // requires声明依赖的模块
    requires javafx.controls;
    requires javafx.fxml;

    // opens 包 to 模块，允许模块访问包的特定成员
    opens com.handle.hellofx to javafx.fxml;

    // 将包公开，允许其他模块在编译和运行时访问该包里的public类和接口
    exports com.handle.hellofx;
}
```

### 导入模块声明

java25开始，无论是否模块化，都可以简洁地导入整个模块的所有导出包，而无需逐个声明包的导入

```java
// 可以直接使用Stream了，而无需导入Stream包
import module java.base;

public class ImportModuleDemo {
    void main() {
        List<Integer> list = Stream.iterate(0, n -> n + 1).limit(3).collect(Collectors.toList());
        System.out.println(list);
    }
}
```

### 外部函数和内存API

java22开始，可以使用如下方式调用C方法

- 创建文件math.c

```c
int max(int x, int y)
{
    return (x > y) ? x : y;
}
```

- 编译为动态链接库（so文件）

```sh
gcc -shared -o math.so math.c
```

- 在java中调用math的max方法

```java
// 1. 获取原生链接器
Linker linker = Linker.nativeLinker();

// Arena.ofConfined()，仅限创建它的线程访问，不可跨线程
try (Arena arena = Arena.ofConfined()) {
    String libPath = "/path/to/math.so";
    String methodName = "max";
    // 2. 查找并加载本地库中的 "max" 函数符号
    // 如果使用linker.defaultLookup()，将查找操作系统原生提供的标准库，资源将会伴随整个JVM生命周期
    SymbolLookup thirdPartyLookup = SymbolLookup.libraryLookup(libPath, arena);
    MemorySegment methodAddress =
            thirdPartyLookup.find(methodName).orElseThrow(() -> new NoSuchElementException("find method exception: " + methodName));

    // 3. 定义C方法的签名：int max(int x, int y)
    FunctionDescriptor functionDescriptor = FunctionDescriptor.of(ValueLayout.JAVA_INT, ValueLayout.JAVA_INT, ValueLayout.JAVA_INT);

    // 4. 创建方法句柄 (MethodHandle)
    MethodHandle methodHandle = linker.downcallHandle(methodAddress, functionDescriptor);

    try {
        // 5. 调用C方法
        int result = (int) methodHandle.invoke(40, 30);
        System.out.println("Max value: " + result);
    } catch (Throwable throwable) {
        throw new RuntimeException("invoke method " + methodName + " exception", throwable);
    }
}
```

### 正则表达式

- 字符串精确匹配实际上用处不大，用`String.equals()`就可以做到。大多数情况下，我们想要的匹配规则是模糊匹配
- 要注意正则表达式在Java代码中是一个字符串，`A*`对应的字符串是`"A*"`
- 使用括号把一个子规则括起来，如`learn\\s(java|php|go)`，匹配字符串`learn java`、`learn php`或`learn go`

#### 特殊字符

- 如果正则表达式有特殊字符，那就需要用`\`转义，如`\&`用来匹配字符`&`;

- 在java字符串中，用`\\`表示`\`，如`\\&`用来匹配字符`&`

|正则特殊字符|原义|正则转义写法|正则转义Java写法|转义说明|
|:-|:-|:-|:-|:-|
|`?`|0个或1个字符|`\?`|`\\?`|匹配字符`?`|
|`.`|任意1个字符|`\.`|`\\.`|匹配字符`.`|
|`+`|至少1个字符|`\+`|`\\+`|匹配字符`+`|
|`*`|任意个字符|`\*`|`\\*`|匹配字符`*`|
|`^`|字符串开头|`\^`|`\\^`|匹配字符`^`|
|`$`|字符串结束|`\$`|`\\$`|匹配字符`$`|
|`-`|至，如A-Z|`\-`|`\\-`|匹配字符`-`|
|`\|`|或|`\\|`|`\\\|`|匹配字符`\|`|
|`(`|分组匹配开始，组成正则(...)|`\(`|`\\(`|匹配字符`(`|
|`)`|分组匹配结束，组成正则(...)|`\)`|`\\)`|匹配字符`)`|
|`[`|匹配范围内的字符开始，组成正则[...]|`\[`|`\\[`|匹配字符`[`|
|`]`|匹配范围内的字符结束，组成正则[...]|`\]`|`\\]`|匹配字符`]`|
|`{`|修饰符，组成正则`{n,m}`或`{n}`或`{n,}`,其中n,m都包括|`\{`|`\\{`|匹配字符`{`|
|`}`|修饰符，组成正则`{n,m}`或`{n}`或`{n,}`,其中n,m都包括|`\}`|`\\}`|匹配字符`}`|

#### 单个字符的匹配规则

|正则表达式|规则|可以匹配|
|:-|:-|:-|
|`A`|指定字符|`A`|
|`\u548c`|指定Unicode字符|`和`|
|`.`|任意字符|`a`，`b`，`&`，`0`|
|`\d`|数字0~9|`0`~`9`|
|`\D`|非数字|`a`，`A`，`&`，`_`，……|
|`\w`|大小写字母，数字和下划线|`a`~ `z`，`A`~ `Z`，`0`~`9`，`_`|
|`\W`|非`\w`|`&`，`@`，`中`，……|
|`\s`|空格、Tab键|空格，Tab|
|`\S`|非`\s`|`a`，`A`，`&`，`_`，……|

#### 多个字符的匹配规则

|正则表达式|规则|可以匹配|
|:-|:-|:-|
|`A*`|任意个字符|空，`A`，`AA`，`AAA`，……|
|`A+`|至少1个字符|`A`，`AA`，`AAA`，……|
|`A?`|0个或1个字符|空，`A`|
|`A{n}`|n个字符|`AAA`|
|`A{m, n}`|m-n个字符|`AA`，`AAA`|
|`A{n, }`|至少n个字符|`AA`，`AAA`，`AAAA`，……|
|`A{0, n}`|最多n个字符|空，`A`，`AA`，`AAA`|

#### 复杂匹配规则

|正则表达式|规则|可以匹配|
|:-|:-|:-|
|^|开头|字符串开头|
|$|结尾|字符串结束|
|[ABC]|[…]内任意字符|A，B，C|
|[A-F0-9xy]|指定范围的字符|`A`，……，`F`，`0`，……，`9`，`x`，`y`|
|[^A-F]|指定范围外的任意字符|非`A`~`F`|
|AB\|CD\|EF|AB或CD或EF|`AB`，`CD`，`EF`|

### 异常

![异常类层次结构图概览](/images/异常类层次结构图概览.png)

- Exception：程序本身可以处理的异常，可以通过 catch 来进行捕获
    - 分为Checked Exception和Unchecked Exception，其中受检异常，必须处理
    - 除了RuntimeException及其子类以外，其他的Exception类及其子类都属于受检异常
    - 一般来说，只有当这个异常是业务逻辑的一部分，并且调用方必须处理它时，才会使用Checked Exception
- Error：程序本身无法处理的错误，不建议通过catch捕获，这些异常发生时，JVM一般会选择线程终止

- 当某个方法抛出了异常时，如果当前方法没有捕获异常，异常就会被抛到上层调用方法，直到遇到某个try {...} catch {...} finally {}被捕获为止

```java
public class Throwable implements Serializable {
    // 返回异常的详细信息
    public String getMessage() {
        return detailMessage;
    }

    // 返回异常的本地化信息
    // 默认返回的信息和getMessage()相同，Throwable的子类可以覆写此方法生成本地化信息
    public String getLocalizedMessage() {
        return getMessage();
    }

    public String getMessage() {
        return detailMessage;
    }

    // 返回异常的简要描述
    public String toString() {
        String s = getClass().getName();
        String message = getLocalizedMessage();
        return (message != null) ? (s + ": " + message) : s;
    }

    // 打印异常及其回溯信息到标准错误流
    public void printStackTrace() {
        printStackTrace(System.err);
    }
}
```

#### 捕获异常

- 捕获异常使用`try {...} catch {...} finally {}`语句，把可能发生异常的代码放到try中，然后使用catch捕获对应的Exception及其子类

- 多个catch语句只有一个能被执行

- 存在多个catch的时候，子类异常的catch必须写在前面

- 如果某些异常的处理逻辑相同，但是异常本身不存在继承关系，可以用`|`合并到一起

```java
try {
    // do something
} catch (UnsupportedEncodingException e) {
    // do something else
} catch (IOException | NumberFormatException e) {
    // do something other
} finally {
    // do something must be done
}
```

#### 抛出异常

- 用 `throw new 异常名` 抛出异常

- 为了能追踪到完整的异常栈，在构造异常的时候，把原始的Exception实例传进去，新的Exception就可以持有原始Exception信息
  
  ```java
  try {
  // do something
  } catch (NullPointerException e) {
      throw new IllegalArgumentException(e);
  }
  ```

- 如果在try或者catch语句块中抛出异常，JVM会先执行finally，然后抛出异常

- 如果在执行finally语句时抛出异常，在catch中准备抛出的异常就被屏蔽不会再被抛出了，这时候可以先用origin变量保存原始异常，然后调用Throwable.addSuppressed()，把原始异常添加进来，最后在finally抛出。绝大多数情况下，在finally中不要抛出异常

```java
Exception origin = null;
try {
    // do something
} catch (Exception e) {
    origin = e;
    throw e;
} finally {
    Exception e = new IllegalArgumentException();
    if (origin != null) {
        e.addSuppressed(origin);
    }
    throw e;
}
```

#### 自定义异常

在一个大型项目中，可以自定义新的异常类型，但是，保持一个合理的异常继承体系是非常重要的。

一个常见的做法是自定义一个BaseException作为“根异常”，然后，派生出各种业务类型的异常。

BaseException需要从一个适合的Exception派生，通常建议从RuntimeException派生

```java
public class BaseException extends RuntimeException {
    public BaseException() {
        super();
    }

    public BaseException(String message) {
        super(message);
    }

    public BaseException(String message, Throwable cause) {
        super(message, cause);
    }

    public BaseException(Throwable cause) {
        super(cause);
    }
}
```

#### try-with-resources

- 适用于实现 java.lang.AutoCloseable或者 java.io.Closeable 的对象
- catch 或 finally 块在声明的资源关闭后运行

### 加密

#### 编码算法

##### URL编码

- URL编码是浏览器发送数据给服务器时使用的编码，它通常附加在URL的参数部分

- 如果字符是`A~Z`，`a~z`，`0~9`以及`-`、`_`、`.`、`*`，则保持不变；

- 如果是其他字符，先转换为UTF-8编码，然后对每个字节以%XX表示

```java
String encoded = URLEncoder.encode("中文!", StandardCharsets.UTF_8);
// %E4%B8%AD%E6%96%87%21
System.out.println(encoded);

String decoded = URLDecoder.decode(encoded, StandardCharsets.UTF_8);
// 中文!
System.out.println(decoded);
```

##### Base64编码

- URL编码是对字符进行编码，表示成%XX的形式，而Base64编码是对二进制数据(在Java中相当于byte[])进行编码，表示成文本格式

- 相较于二进制数据转成16进制得到的字符串，转成Base64编码得到的字符串的长度更短，占用更少的存储

```java
byte[] input = new byte[] { -28, -72, -83 };

String b64encoded = Base64.getEncoder().encodeToString(input);
// 5Lit
System.out.println(b64encoded);

byte[] output = Base64.getDecoder().decode(b64encoded);
// [-28, -72, -83]
System.out.println(Arrays.toString(output));
```

- 因为标准的Base64编码会出现`+`、`/`和`=`，所以不适合把Base64编码后的字符串放到URL中。一种针对URL的Base64编码，是可以在URL中使用的Base64编码，它仅仅是把`+`变成`-`，`/`变成`_`

```java
byte[] input = new byte[] {1, 2, 127, 0};

String b64encoded = Base64.getUrlEncoder().encodeToString(input);
// AQJ_AA==
System.out.println(b64encoded);

byte[] output = Base64.getUrlDecoder().decode(b64encoded);
// [1, 2, 127, 0]
System.out.println(Arrays.toString(output));
```

#### 哈希算法

- 用途
    - 1.校验下载文件是否已被篡改
    - 2.存储用户口令的哈希

```java
// 1.使用哈希算法SHA-512，创建一个MessageDigest实例
MessageDigest md = MessageDigest.getInstance("SHA-512");
// 2.（反复）调用update输入数据
md.update("Hello World".getBytes(StandardCharsets.UTF_8));
// 3.获取摘要
byte[] result = md.digest();
// 字符串长度128: 2c74fd17edafd80e8447b0d46741ee243b7eb74dd2149a0ab1b9246fb30382f27e853d8585719e0e67cbda0daa8f51671064615d645ae27acb15bfb1447f459b
System.out.println(HexFormat.of().formatHex(result));
```

#### BouncyCastle库

- 依赖

```groovy
implementation 'org.bouncycastle:bcprov-jdk18on:1.79'
```

Java标准库提供了一系列常用的哈希算法，如果还不满足，BouncyCastle库提供了更多的哈希算法可供使用

```java
// 1.注册BouncyCastle
Security.addProvider(new BouncyCastleProvider());
// 2.使用哈希算法RipeMD160，创建一个MessageDigest实例
MessageDigest md = MessageDigest.getInstance("RipeMD160");
// 3.（反复）调用update输入数据
md.update("Hello World".getBytes(StandardCharsets.UTF_8));
// 4.获取摘要
byte[] result = md.digest();
// 字符串长度40: a830d7beb04eb7549ce990fb7dc962e499a27230
System.out.println(HexFormat.of().formatHex(result));
```

#### Hmac算法

- 存储用户的哈希口令时，要加盐存储，让彩虹表失效

- Hmac算法是一种基于密钥（相当于盐）的消息认证码算法，是一种更安全的消息摘要算法，可以用Hmac算法取代原有的自定义的加盐算法

- Hmac算法总是和某种哈希算法配合起来用

- Hmac本质上就是把密钥混入摘要的算法。验证此哈希时，除了原始的输入数据，还要提供密钥

- 保证安全，通过Java标准库的KeyGenerator生成一个安全的随机的密钥

- 工具类：[HmacUtils](/java/HmacUtils.java)

```java
// 生成密钥和哈希
HmacUtils.EncryptionBO encryptionBO = HmacUtils.getEncryptionBO("hello world");

// 核实输入是否正确
boolean b = HmacUtils.verifyInput(encryptionBO.getSecretKey(), "hello world", encryptionBO.getHash());
System.out.println(b);
```

#### 对称加密算法

- 对称加密算法就是传统的用一个密码进行加密和解密，例如压缩和解压缩，就是用对称加密算法

- 对称加密算法的密钥长度是固定的

- 密钥长度直接决定加密强度，而工作模式和填充模式可以看成是对称加密算法的参数和格式选择
    - 工作模式决定了加密算法如何处理超过一个块（block）的数据
        - ECB (Electronic Codebook)：电子密码本模式。每个块独立加密，简单但不安全，因为相同的明文块会产生相同的密文块。
        - CBC (Cipher Block Chaining)：密文分组链接模式。每个块在加密前与前一个块的密文进行 XOR 运算。提高了安全性，但需要一个初始向量（IV）。
        - CFB (Cipher Feedback)：密文反馈模式。将加密结果反馈到加密过程。支持加密小于一个块的消息。
        - OFB (Output Feedback)：输出反馈模式。类似 CFB，但不会引入加密结果反馈，不容易出现传播错误。
        - CTR (Counter)：计数器模式。将一个递增的计数器应用到加密过程。支持并行加密，非常高效。
    - 填充模式决定了如何处理不满一个块的数据
        - PKCS5Padding：常见的填充方式，将缺少的字节填充为相同的值，适用于块大小为 8 字节（64 位）的加密算法。例如，需要填充 3 个字节，则填充值为 0x03 0x03 0x03。
        - PKCS7Padding：与 PKCS5Padding 类似，适用于任意块大小（如 16 字节块的 AES），AES 这样的块大小为 16 字节的加密算法，使用 PKCS7Padding 是一个很好的选择
        - NoPadding：不填充数据，要求输入数据必须是块大小的整数倍。如果不满足则需要手动处理。

```java
/**
 * 对称加密算法测试
 */
@Test
public void test() throws NoSuchAlgorithmException, InvalidAlgorithmParameterException, NoSuchPaddingException, IllegalBlockSizeException,
        BadPaddingException, InvalidKeyException {
    String input = "hello, world!";
    AesUtil.AesBo aesBO = AesUtil.getAesBO();
    System.out.println(aesBO.getSecretKey().length());
    String encrypt = AesUtil.encrypt(aesBO.getSecretKey(), aesBO.getIv(), input);
    System.out.println("Encrypt: " + encrypt);
    String decrypt = AesUtil.decrypt(aesBO.getSecretKey(), aesBO.getIv(), encrypt);
    System.out.println("Decrypt: " + decrypt);
}
```

#### 口令加密算法

- 由于对称加密算法的密钥长度是固定的，如果想要设置为自定义长度（如6位或8位），则需要把用户输入的口令和一个安全随机的口令采用杂凑后计算出固定长度的真正密钥

```java
/**
 * 口令加密算法测试
 */
@Test
public void test2() throws GeneralSecurityException {
    // 各自生成KeyPair:
    KeyPair keyPair1 = DhUtil.generateKeyPair();
    KeyPair keyPair2 = DhUtil.generateKeyPair();

    // 双方交换各自的PublicKey生成自己的本地密钥
    String secretKey1 = DhUtil.generateSecretKey(keyPair1.getPrivate(), Base64.getEncoder().encodeToString(keyPair2.getPublic().getEncoded()));
    String secretKey2 = DhUtil.generateSecretKey(keyPair2.getPrivate(), Base64.getEncoder().encodeToString(keyPair1.getPublic().getEncoded()));

    System.out.println("Secret key1: " + secretKey1);
    System.out.println("Secret key2: " + secretKey2);
    System.out.println(secretKey1.equals(secretKey2));
    System.out.println(secretKey1.length());
}
```

#### 密钥交换算法

密钥交换算法即DH算法，本质就是双方各自生成自己的私钥和公钥，私钥仅对自己可见，然后交换公钥，并根据自己的私钥和对方的公钥，生成最终的密钥

DH算法通过数学定律保证了双方各自计算出的密钥是相同的

后续将使用密钥进行AES加解密通信

密钥交换算法不能防止中间人攻击

```java
/**
 * 密钥交换测试
 */
@Test
public void test2() throws GeneralSecurityException {
    // 各自生成KeyPair:
    KeyPair keyPair1 = DhUtil.generateKeyPair();
    KeyPair keyPair2 = DhUtil.generateKeyPair();

    // 双方交换各自的PublicKey生成自己的本地密钥
    String secretKey1 = DhUtil.generateSecretKey(keyPair1.getPrivate(), Base64.getEncoder().encodeToString(keyPair2.getPublic().getEncoded()));
    String secretKey2 = DhUtil.generateSecretKey(keyPair2.getPrivate(), Base64.getEncoder().encodeToString(keyPair1.getPublic().getEncoded()));

    System.out.println("Secret key1: " + secretKey1);
    System.out.println("Secret key2: " + secretKey2);
    System.out.println(secretKey1.equals(secretKey2));
    System.out.println(secretKey1.length());
}
```

#### 非对称加密算法

非对称加密就是加密和解密使用的不是相同的密钥：只有同一个公钥-私钥对才能正常加解密（可以用公钥加密-用私钥解密，或者用私钥加密-用公钥解密）

非对称加密算法不能防止中间人攻击

```java
/**
 * 非对称加密/解密
 */
@Test
public void test() throws GeneralSecurityException {
    String message = "Hello world!";
    String encrypt = RsaUtil.encrypt(message);
    System.out.println("encrypt: " + encrypt);
    String decrypt = RsaUtil.decrypt(encrypt);
    System.out.println("decrypt: " + decrypt);

    String privateKey = RsaUtil.getPrivateKey();
    PrivateKey privateKey1 = RsaUtil.restorePrivateKey(privateKey);
    String s = Base64.getEncoder().encodeToString(privateKey1.getEncoded());
    System.out.println("privateKey: " + s);
    System.out.println(s.equals(privateKey));

    String publicKey = RsaUtil.getPublicKey();
    PublicKey publicKey1 = RsaUtil.restorePublicKey(publicKey);
    String s1 = Base64.getEncoder().encodeToString(publicKey1.getEncoded());
    System.out.println("publicKey: " + s1);
    System.out.println(s1.equals(publicKey));
}
```

#### 签名算法

```java
/**
 * 对消息的哈希进行签名和验证
 */
@Test
public void test() throws NoSuchAlgorithmException, InvalidKeyException, SignatureException {
    String message = "Hello world!";
    HmacUtil.EncryptionBO encryptionBO = HmacUtil.getEncryptionBO(message);
    String hashMessage = encryptionBO.getHash();
    String sign = SignatureUtil.sign(hashMessage);
    System.out.println(SignatureUtil.isValidSignature(sign, hashMessage));
}
```

#### 数字证书

- 数字证书就是集合了多种密码学算法，用于实现数据加解密、身份认证、签名等多种功能的一种安全标准

- 数字证书可以防止中间人攻击，因为它采用链式签名认证，即通过根证书（Root CA）去签名下一级证书，这样层层签名，直到最终的用户证书

- 而Root CA证书内置于操作系统中，所以，任何经过CA认证的数字证书都可以对其本身进行校验，确保证书本身不是伪造的

- 在Java程序中，数字证书存储在一种Java专用的key store文件中，JDK提供了一系列命令来创建和管理keystore

```sh
# 创建一个keystore
# -storepass：指定keystore口令
# -genkeypair：生成公钥/私钥对
# -keyalg：指定加密算法
# -keysize：指定密钥长度/位数
# -sigalg：指定签名算法
# -validity：指定有效天数
# -alias：指定密钥和证书别名，一个keystore文件中可以存储多个不同的密钥对。为了区分它们，每一个存入的条目都必须有一个独一无二的别名
# -storetype：指定keystore的类型，直接设置为PKCS12就可以，它是通用的
# -keystore：指定keystore名称
# dname 最重要的CN=www.sample.com指定了Common Name，如果证书用在HTTPS中，这个名称必须与域名完全一致
# CN (Common Name)：通用名称。这是最重要的字段！如果是本地测试，填localhost；如果是正式网站或服务，必须填写你的域名（如www.handle.com）或服务器的 IP 地址。
# OU (Organization Unit)：组织单位名称（比如具体的部门，如 IT Dept）。
# O (Organization)：组织或公司全称（如 MyCompany Inc.）。
# L (Locality)：城市或地区名称（如 Beijing）。
# ST (State/Province)：省份或州（如 Beijing 或 Guangdong）。
# C (Country)：国家的双字母代码（中国为 CN，美国为 US）。
# 此命令在当前目录创建一个my.keystore文件，并存储创建成功的一个私钥和一个证书到文件中
# 有了keystore存储的数字证书，我们就可以进行加解密和签名
keytool -storepass 'changeit' \
    -genkeypair \
    -keyalg RSA \
    -keysize 2048 \
    -sigalg SHA256withRSA \
    -validity 9999 \
    -alias vaultServer \
    -storetype PKCS12 \
    -keystore vaultServer.p12 \
    -dname 'CN=www.example.com, OU=Handle Studio, O=Handle Studio, L=Shenzhen, ST=Guangdong, C=CN'

# 从keystore（包含私钥和公钥证书），导出公钥证书（.crt证书）
# -exportcert：指定操作（导出证书）
# -alias：keystore文件里其实可以存放好几对不同的密钥和证书，需要通过别名来指定具体要导出哪一个
# -keystore 用来指定要从哪个keystore文件导出公钥证书
# -file，指定提取出来的公钥证书的名称路径
# 不加 -rfc（默认情况）：keytool 导出的是 DER 格式（二进制编码），加上 -rfc：keytool 导出的是 PEM 格式（Base64 编码的 ASCII 文本）
# 用记事本打开就能清晰看到以 -----BEGIN CERTIFICATE----- 开头、-----END CERTIFICATE----- 结尾的可读文本
# .pem、.crt、.cer 这些后缀的文件，只要它们内部是这种带 BEGIN/END CERTIFICATE 的文本格式，它们在本质上都是完全一样的，可以随意互换后缀名使用
# -storepass：keystore口令，.p12是个加密的保险箱，想要打开它并读取里面的内容，必须提供正确密码
keytool -exportcert -alias vaultServer -keystore vaultServer.p12 -file vaultServer.crt -rfc -storepass 'changeit'

# 或用openssl提取完整的公钥证书链
openssl pkcs12 -in vaultServer.p12 -nokeys -out publicKey.pem

# keytool无法导出私钥，我们需要使用 openssl导出不带密码的私钥
openssl pkcs12 -in vaultServer.p12 -nocerts -nodes -out privateKey.pem

# 用openssl直接创建公钥和私钥文件
# req -x509：直接输出一个自签名的 X.509 证书，而不是生成 CSR
# -newkey rsa:2048：同时生成一个新的 2048 位 RSA 私钥
# nodes：(no DES) 表示不对生成的私钥文件进行加密（即不需要输入密码），这在服务器自动化配置时非常方便
# -keyout server.key：指定生成的私钥文件名
# -out server.crt：指定生成的公钥证书文件名
# -days 365：设置证书的有效期为 9999 天
# -subj：自动填写证书的主体信息，避免交互式输入
openssl req -x509 \
    -newkey rsa:2048 \
    -nodes \
    -keyout privateKey.pem \
    -out publicKey.pem \
    -days 9999 \
    -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=Handle Studio/OU=Handle Studio/CN=www.example.com"

# 由公钥文件和私钥文件生成keystore
# -in server.crt：指定公钥证书文件
# -inkey server.key：指定私钥文件
# -out keystore.p12：指定生成的密钥库文件名
# -name myalias：为你的证书指定一个别名（alias）
# -passout 'pass:yourpassword'：keystore口令，如果没有这个选项参数
# 则执行命令后，终端会提示你设置一个 Export Password。请牢记这个密码，它就是后续 Spring Boot 配置中的 key-store-password
openssl pkcs12 -export -in server.crt -inkey server.key -out keystore.p12 -name myalias -passout 'pass:yourpassword'
```

- 使用数字证书

[CertificateUtil](/java/CertificateUtil.java)

```java
@Test
public void test() throws GeneralSecurityException {
    String password = "handle123";
    String message = "hello world";
    String certificateAlias = "mycert";
    // 读取KeyStore
    KeyStore keyStore = CertificateUtil.loadKeyStore(ApplicationTest.class, "/my.keystore", password);
    // 读取私钥
    PrivateKey privateKey = (PrivateKey) keyStore.getKey(certificateAlias, password.toCharArray());
    // 读取证书
    X509Certificate certificate = (X509Certificate) keyStore.getCertificate(certificateAlias);
    // 读取公钥
    PublicKey publicKey = certificate.getPublicKey();

    System.out.println(publicKey.getAlgorithm());
    System.out.println(privateKey.getAlgorithm());

    // 加密
    String encrypted = CertificateUtil.encrypt(publicKey, message);
    System.out.println("encrypted: " + encrypted);

    // 解密
    String decrypted = CertificateUtil.decrypt(privateKey, encrypted);
    System.out.println("decrypted: " + decrypted);

    // 签名
    String sign = CertificateUtil.sign(privateKey, certificate, message);
    System.out.println("signature: " + sign);

    // 验证签名
    boolean verified = CertificateUtil.verify(certificate, message, sign);
    System.out.println("verify: " + verified);
}
```

### 日志

#### self4j

自定义生成的log文件路径

1. 定义LOG_HOME

```java
/**
 * 指定log文件路径为项目根路径
 */
public class LogHomeConfiguration extends PropertyDefinerBase {
    private static final String LOG_HOME = ApplicationUtils.getApplicationHome(LogHomeConfiguration.class);

    @Override
    public String getPropertyValue() {
        return LOG_HOME;
    }
}
```

2.logback.xml定义LOG_HOME

```xml
<define name="LOG_HOME" class="com.handle.application.configuration.LogHomeConfiguration"/>
```

#### Logback

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 定义变量 -->
    <define name="LOG_HOME" class="com.handle.config.configuration.LogHomeConfiguration" />

    <!-- output to console -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!--时间 日志级别 线程 全限定类名.方法名 消息 换行-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss} %5level [%thread] %logger.%M -- %msg%n</pattern>
            <!--日志编码，不分大小写-->
            <charset>utf-8</charset>
        </encoder>
    </appender>

    <!-- output to file -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{50}.%M -- %msg%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <file>${LOG_HOME}/log/output.log</file>
        <!-- If true, events are appended at the end of an existing file. -->
        <append>true</append>
        <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
            <fileNamePattern>${LOG_HOME}/log/output%d{yyyy-MM-dd}-%i.log</fileNamePattern>
        </rollingPolicy>
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <maxFileSize>50MB</maxFileSize>
        </triggeringPolicy>
    </appender>

    <!-- 日志级别，默认info：trace < debug < info < warn < error，不分大小写 -->
    <root level="INFO">
        <!-- 指定打印日志的appender -->
        <appender-ref ref="console" />
        <appender-ref ref="FILE" />
    </root>

    <!-- 根据特殊需求指定局部日志级别，可以是包名或全限定类名，日志级别不分大小写 -->
    <logger name="com.handle.application.mapper" level="debug"/>
</configuration>
```

### ProcessBuilder

ProcessBuilder 是 Java 中用于创建和管理操作系统进程（即执行外部命令行程序）的核心类

相比早期的 Runtime.exec()，它提供了更灵活、更强大的进程控制能力

每个 ProcessBuilder 实例都会管理一组进程属性

当调用 start() 方法时，它会利用这些属性创建一个新的子进程，并返回一个 Process 对象来让你与这个子进程进行交互

```java
// 命令参数列表
List<String> command = new ArrayList<>();
command.add("ffprobe");
command.add("-i");
command.add("/path/to/input.mp4");

ProcessBuilder processBuilder = new ProcessBuilder(command);
// 合并错误流和标准流， 这样错误流和标准流就都能通过Process.getInputStream()读取
// getInputStream() 和 getOutputStream() 的命名是站在父进程（Java 程序）的角度来定义的
// 子进程的输出是父进程的输入，父进程的输出是子进程的输入
processBuilder.redirectErrorStream(true);
try {
    Process process = processBuilder.start();
    // 打印命令输出，这里是阻塞读，当父线程不用向子线程写数据的时候适用，如果是双向交互，则要开线程读 + 主线程 waitFor
    try (BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()))) {
        String line;
        while (null != (line = reader.readLine())) {
            System.out.println(line);
        }
    }
    // 阻塞等待子线程退出
    int exitCode = process.waitFor();
    if (0 == exitCode) {
        System.out.println("command executed success!");
    } else {
        System.out.println("command executed failed，exit code: " + exitCode);
    }
} catch (IOException | InterruptedException e) {
    throw new RuntimeException(e);
}
```

### JavaFX

- 父pom

```xml
<properties>
    <!--java8之后用maven.compiler.release标签代替maven.compiler.source标签和maven.compiler.target标签-->
    <maven.compiler.release>25</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven-compiler-plugin.version>3.15.0</maven-compiler-plugin.version>
    <javafx.applicationName>app</javafx.applicationName>
    <javafx.version>25.0.3</javafx.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-controls</artifactId>
            <version>${javafx.version}</version>
        </dependency>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-fxml</artifactId>
            <version>${javafx.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven-compiler-plugin.version}</version>
                <configuration>
                    <!--java8之后用release标签代替source标签和target标签-->
                    <release>25</release>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.openjfx</groupId>
                <artifactId>javafx-maven-plugin</artifactId>
                <version>0.0.8</version>
                <executions>
                    <execution>
                        <!-- Default configuration for running with: mvn clean javafx:run -->
                        <id>default-cli</id>
                        <configuration>
                            <launcher>${javafx.applicationName}</launcher>
                            <jlinkZipName>${javafx.applicationName}</jlinkZipName>
                            <jlinkImageName>${javafx.applicationName}</jlinkImageName>
                            <noManPages>true</noManPages>
                            <stripDebug>true</stripDebug>
                            <noHeaderFiles>true</noHeaderFiles>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

- 子pom

```xml
<properties>
    <javafx.applicationName>应用名称</javafx.applicationName>
</properties>

<dependencies>
    <dependency>
        <groupId>org.openjfx</groupId>
        <artifactId>javafx-controls</artifactId>
    </dependency>
    <dependency>
        <groupId>org.openjfx</groupId>
        <artifactId>javafx-fxml</artifactId>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-maven-plugin</artifactId>
            <configuration>
                <mainClass>主类全限定类名</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 运行程序

当直接运行继承了javafx.application.Application的类的main方法时，IDE（idea和eclipse）会无法正确加载 JavaFX 的模块路径（Module Path），导致报错

由于 IDE （idea和eclipse）对普通 Java 主类和 JavaFX 主类的底层启动机制完全不同

如果直接运行继承了 javafx.application.Application 的主类，IDEA 会尝试使用 Java 9+ 的 Module Path（模块路径） 来启动它

但是，Maven 的依赖管理机制默认将 JavaFX 的 jar 包放在 Classpath（类路径） 中，而不是 Module Path 中

这就导致：你的代码在 Classpath 里，但 JavaFX 框架期望在 Module Path 里

于是启动报错：“Error: JavaFX runtime components are missing, and are required to run this application”

下面的方法解决了运行和调试的问题

但是还有一个坑，目前还没解决：如果使用模块的话就不用添加上面的VM选项了，但是mybatis的mapper识别不了

##### 解决方案一

使用`mvn javafx:run`可以直接运行程序，缺点是无法进行调试，可通过配置idea远程调试来解决

缺点是每次调试都要执行两个应用（原本的javafx应用和调试应用）

- javafx-maven-plugin插件加上远程调试的选项参数

```xml
<plugin>
    <groupId>org.openjfx</groupId>
    <artifactId>javafx-maven-plugin</artifactId>
    <configuration>
        <options>
            <option>-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5005</option>
        </options>
    </configuration>
</plugin>
```

- 添加远程调试配置
    - 添加"Remote JVM Debug"
    - Debugger mode设置为：`Attach to remote JVM`
    - Host设置为localhost，跟插件的选项参数的ip要一致
    - Port设置为5005，跟插件的选项参数的端口要一致
    - 最终自动生成的Command line arguments for remote JVM内容为`-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005`
    - 点击应用-OK

- 在终端或者通过idea的maven插件工具执行javafx:run，将会看到程序被挂起

- 然后运行刚刚的远程调试配置，当程序运行到断点处即可看到效果

##### 解决方案二

用 idea 原生 Application 执行器启动，通过添加VM选项参数指定javafx库位置来启动程序，正常进行调试

```sh
# 要先下载并解压好javafx-sdk
# 在idea编辑运行设置，添加VM选项
--module-path "/path/to/javafx-sdk/lib" --add-modules javafx.controls,javafx.fxml

# 使用idea，也可以File -> Settings -> Appearance & Behavior -> Path Variables，添加JAVAFX_PATH变量并指定值为“/path/to/javafx-sdk/lib”
# 然后在idea编辑运行设置，添加VM选项
--module-path ${JAVAFX_PATH} --add-modules javafx.controls,javafx.fxml

# 下面的VM选项有问题
# java.lang.module.FindException: Module javafx.fxml not found，先用上面的方法指定模块路径吧
--module-path "/path/to/org/openjfx/javafx-controls/25.0.3;/path/to/org/openjfx/javafx-fxml/25.0.3" --add-modules javafx.controls,javafx.fxml
```

但是这样做添加的javafx的maven依赖又多此一举了

##### 解决方案三

用自带javafx的jdk啊，但缺点是你运行的时候也必须用自带javafx的jre！

##### 解决方案四

把项目变成模块

##### 终极方案

用一个普通的 Java 类作为“跳板”来启动JavaFX

这时IDEA 会把项目当成一个标准的 Java Application 来处理

IDEA 自动把 Maven 依赖（包括 JavaFX 的模块）正确挂载到类路径和模块路径中

```java
public class Main {
    public static void main(String[] args) {
        Application.launch(FxApplication.class, args);
    }
}
```

#### JavaFX的Scene Graph

笔记的内容来自：<https://fxdocs.github.io/docs/html5/>

![JavaFxSceneGraph](image/JavaFxSceneGraph.png)

- JavaFX的结构是一个树形结构
    - 顶层Stage，代表本地操作系统的窗体
    - Scene是JavaFX scene graph的一个容器，每个Stage在某个时间只能有一个关联的Scene
    - 所有JavaFX scene graph的元素代表了Node对象，有3种类型的Node：root, branch and leaf
        - root是唯一没有父node并且直接放置到scene中的node
        - branch有子node，leaf没有子node
        - 除了root，其它node只能有一个父node
        - 一个附属当前可见scene的活动的node，只能被JavaFX应用线程修改

- 下面是一个 "Hello World" scene graph的示例图

![JavaFxHelloWorldSceneGraph](image/JavaFxHelloWorldSceneGraph.png)

- 对应的代码实现如下

```java
public class HelloWorld extends Application {
    @Override
    public void start(Stage stage) throws Exception {
        Text leaf = new Text("hello world");
        StackPane root = new StackPane(leaf);
        Scene scene = new Scene(root, 720, 480);
        stage.setScene(scene);
        stage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}
```

#### 获取屏幕尺寸

```java
// 获取主屏幕的物理边界
Rectangle2D bounds = Screen.getPrimary().getBounds();

// 获取主屏幕的可视边界（排除任务栏等系统UI）
Rectangle2D visualBounds = Screen.getPrimary().getVisualBounds();

double visualWidth = visualBounds.getWidth();
double visualHeight = visualBounds.getHeight();
```

#### 控件

##### ChoiceBox

青春版的单选下拉框，选项不可编辑，且下拉框的位置会改变

```java
public class ChoiceBoxDemo extends Application {
    // ChoiceBox的项可以用最简单的String类型
    // 也可以用复制的类型，如Pair（一个通用的键值对容器）
    private static final ChoiceBox<Pair<String, String>> CHOICE_BOX = new ChoiceBox<>();

    // 定义EMPTY_PAIR对象避免CHOICE_BOX.getValue()空指针异常
    private static final Pair<String, String> EMPTY_PAIR = new Pair<>("", "");

    @Override
    public void start(Stage primaryStage) {
        Label label = new Label("Size:");

        CHOICE_BOX.setPrefWidth(200);
        initChoice();

        Button saveButton = new Button("Save");
        saveButton.setOnAction(event -> {
            System.out.println("saving " + CHOICE_BOX.getValue());
        });

        HBox hBox = new HBox(label, CHOICE_BOX, saveButton);
        hBox.setSpacing(10.0);
        hBox.setAlignment(Pos.CENTER);
        hBox.setPadding(new Insets(40));

        Scene scene = new Scene(hBox);
        primaryStage.setScene(scene);
        primaryStage.setTitle("ChoiceBoxDemo");
        primaryStage.show();
    }

    private void initChoice() {
        List<Pair<String, String>> list = new ArrayList<>();
        list.add(new Pair<>("小", "100"));
        list.add(new Pair<>("中", "200"));
        list.add(new Pair<>("大", "300"));

        // 当使用复杂对象做ChoiceBox的项时，由于界面上只能显示文本（字符串）
        // 就要用到StringConverter了
        CHOICE_BOX.setConverter(new StringConverter<Pair<String, String>>() {
            // 对象 → 字符串（用于显示）
            @Override
            public String toString(Pair<String, String> pair) {
                return pair.getKey();
            }

            // 字符串 → 对象（用于还原），这里用不到就不写逻辑了
            @Override
            public Pair<String, String> fromString(String string) {
                return null;
            }
        });

        CHOICE_BOX.getItems().add(EMPTY_PAIR);
        CHOICE_BOX.getItems().addAll(list);
        // 默认值
        CHOICE_BOX.setValue(EMPTY_PAIR);
    }
}
```

##### ComboBox

完整版的单选下拉框，选项可编辑，且下拉框的位置固定，感觉更友好，高级操作如状态值的颜色编码形状渲染等也要用到它

```java
public class ComboBoxDemo extends Application {
    // ComboBox的项可以用最简单的String类型
    // 也可以用复制的类型，如Pair（一个通用的键值对容器）
    private static final ComboBox<Pair<String, String>> COMBO_BOX = new ComboBox<>();

    // 定义EMPTY_PAIR对象避免COMBO_BOX.getValue()空指针异常
    private static final Pair<String, String> EMPTY_PAIR = new Pair<>("", "");

    @Override
    public void start(Stage primaryStage) {
        Label label = new Label("Size:");

        COMBO_BOX.setPrefWidth(200);
        initComboBox();

        Button saveButton = new Button("Save");
        saveButton.setOnAction(event -> {
            System.out.println("saving " + COMBO_BOX.getValue());
        });

        HBox hBox = new HBox(label, COMBO_BOX, saveButton);
        hBox.setSpacing(10.0);
        hBox.setAlignment(Pos.CENTER);
        hBox.setPadding(new Insets(40));

        Scene scene = new Scene(hBox);
        primaryStage.setScene(scene);
        primaryStage.setTitle("ComboBoxDemo");
        primaryStage.show();
    }

    private void initComboBox() {
        List<Pair<String, String>> list = new ArrayList<>();
        list.add(new Pair<>("小", "100"));
        list.add(new Pair<>("中", "200"));
        list.add(new Pair<>("大", "300"));

        COMBO_BOX.getItems().add(EMPTY_PAIR);
        COMBO_BOX.getItems().addAll(list);
        // 默认值
        COMBO_BOX.setValue(EMPTY_PAIR);


        //  JavaFX 的虚拟化渲染设计核心思想是
        // 控件不会为每个数据项创建一个 UI 节点，而是只创建少量可复用的 Cell，通过不断调用 updateItem() 来"换皮"显示不同数据
        // 定义一个"工厂"，通过ListView<Pair<String, String>来生产ListCell<Pair<String, String>>
        // 这里也可以用StringConverter，显示优先级：Callback > StringConverter > toString()
        Callback<ListView<Pair<String, String>>, ListCell<Pair<String, String>>> factory = listView -> new ListCell<>() {
            @Override
            protected void updateItem(Pair<String, String> item, boolean empty) {
                super.updateItem(item, empty);
                if (empty) {
                    setText("");
                } else {
                    // 设置Pair的key作为ListCell的文本
                    setText(item.getKey());
                }
            }
        };
        // 下拉列表里每个选项怎么显示
        COMBO_BOX.setCellFactory(factory);
        // 选中后，按钮上显示什么，如果不设置，则会调用选项的toString方法
        COMBO_BOX.setButtonCell(factory.call(null));
    }
}
```

### GUI

#### JFrame

```java
public class MainFrame extends JFrame {

    private final float scale = 1.5F;

    public MainFrame() {

        // 设置标题栏的文本
        this.setTitle("Home");

        // 设置框架的图标
        URL url = this.getClass().getClassLoader().getResource("static/icons/favicon.png");
        Image img = new ImageIcon(url).getImage();
        this.setIconImage(img);

        // 设置框架的位置居中和大小
        Dimension dimension = this.getSystemResolution();
        this.setBounds((int) (dimension.width * (1 - 1 / scale) / 2), (int) (dimension.height* (1 - 1 / scale) / 2), (int) (dimension.width / scale), (int) (dimension.height / scale));

        // 框架的大小是否允许用户改变
        this.setResizable(false);

        // 设置框架大小
        // this.setSize(DEFAULT_WIDTH, DEFAULT_HEIGHT);

        // 将框架设置为最大
        // this.setExtendedState(Frame.MAXIMIZED_BOTH);

        // 让窗口系统控制窗口的位置
        // this.setLocationByPlatform(true);

        // 设置框架的位置
        // this.setLocation(400, 200);

        // 关闭所有框架装饰（按钮、标题栏、图标）,设置后， 框架显示中将没有标题栏或关闭按钮这样的装饰。
        // this.setUndecorated(false);

    }

    public Dimension getSystemResolution() {
        return Toolkit.getDefaultToolkit().getScreenSize();
    }
}
```

#### 设置框架图标

```java
// 方法1
try(InputStream inputStream = this.getClass().getClassLoader().getResourceAsStream("static/icons/favicon.png")) {
    Image image = ImageIO.read(inputStream);
    this.setIconImage(image);
} catch (IOException e) {
    e.printStackTrace();
}

// 方法2
URL url = this.getClass().getClassLoader().getResource("static/icons/favicon.png");
try {
    Image image = ImageIO.read(url.openStream());
    this.setIconImage(image);
} catch (IOException e) {
    e.printStackTrace();
}

// 方法3 推荐
URL url = this.getClass().getClassLoader().getResource("static/icons/favicon.png");
Image img = new ImageIcon(url).getImage();
this.setIconImage(img);
```

#### 添加组件到容器

```java
// 方法1
// 将组件添加到内容窗格中,自动调用contentPane.add(component)
frame.add(component);

// 方法2
// 获取内容窗格
Container contentPane = frame.getContentPane();
// 将组件添加到内容窗格中
contentPane.add(component);
```

### 序列化/反序列化

Jackson 是基于 JSON 文本序列化（反射解析字段 + 文本转换）的独立框架

它不依赖 JDK 原生的序列化机制，只关心对象的结构（无参构造、Getter/Setter），无论类是否实现了 Serializable 接口，Jackson 都能正常工作

但在实际的企业级开发中，很多开发者依然会选择让实体类（如VO）实现 Serializable

这主要是出于通用性和规范性的考虑

如果该对象未来需要用于微服务间的 RPC 调用（如 Dubbo）、消息队列传输（如 RabbitMQ/Kafka）或分布式缓存（如 Redis/Ehcache）

这些底层框架依赖的是 Java 原生的序列化机制，此时就必须实现 Serializable 接口

- JSON和XML这种属于文本类序列化方式（比如后端返回前端的json），这样的实体类可以不实现可序列化接口
- 如果实体对象需要转为字节（通常是二进制字节流）的形式传输（数据存储/网络传输），就必须实现可序列化接口
- 当实体类的所有属性都已经实现了可序列化接口时，实体类可以不实现序列化接口
- serialVersionUID 起版本控制的作用。
    - 反序列化时，会检查 serialVersionUID 是否和当前类的 serialVersionUID 一致
    - 如果 serialVersionUID 不一致则会抛出 InvalidClassException 异常

```java
public class ClassName implements Serializable {
    // 推荐手动指定，生成一个64位的哈希字段
    private static final long serialVersionUID = -6898003192839424203L;

    // 如果不手动指定，编译器会生成默认的 serialVersionUID = 1L
    private static final long serialVersionUID = 1L;
}
```

- 对于不想进行序列化的变量，可以使用`transient`关键字修饰
- `transient`只能修饰变量，不能修饰类和方法。
- `transient`修饰的变量，在反序列化后变量值将会被置成类型的默认值。
- `static`变量因为不属于任何对象，无论有没有`transient`关键字修饰，均不会被序列化

### XML

XML是可扩展标记语言（eXtensible Markup Language）的缩写，它是是一种数据表示格式，可以描述非常复杂的数据结构，常用于传输和存储数据。

XML有几个特点：一是纯文本，默认使用UTF-8编码，二是可嵌套，适合表示结构化数据。如果把XML内容存为文档，那幺它就是一个XML文档，例如`book.xml`。此外，XML内容经常通过网络作为消息传输。

#### XML的结构

1. 首行必定是`<?xml version="1.0"?>`，可以加上可选的编码。如 `<?xml version="1.0" encoding="UTF-8"?>`

2. 紧接着，是文档定义类型（DTD：Document Type Definition），DTD是可选的。如`<!DOCTYPE note SYSTEM "book.dtd">`

3. 接下来是XML的文档内容，一个XML文档有且仅有一个根元素，根元素可以包含任意个子元素，元素可以包含属性，例如，`<isbn lang="CN">1234567</isbn>`包含一个属性`lang="CN"`，且元素必须正确嵌套。如果是空元素，可以用`<tag/>`表示。

4. 如果使用了`<`、`>`以及引号等标识符，如果内容出现了特殊符号，需要使用`&???;`表示转义。

常见的特殊字符如下：

|字符|表示|
|:-|:-|
|<|`&lt;`|
|>|`&gt;`|
|&|`&amp;`|
|"|`&quot;`|
|'|`&apos;`|

格式正确的XML（Well Formed）是指XML的格式是正确的，可以被解析器正常读取。而合法的XML是指，不但XML格式正确，而且它的数据结构可以被DTD或者XSD验证。

DTD文档可以指定一系列规则，例如：

- 根元素必须是`book`
- `book`元素必须包含`name`，`author`等指定元素
- `isbn`元素必须包含属性`lang`
- ...

如何验证XML文档的正确性呢？最简单的方式是通过浏览器验证。可以直接把XML文档拖拽到浏览器窗口，如果格式错误，浏览器会报错。

和结构类似的HTML不同，浏览器对HTML有一定的“容错性”，缺少关闭标签也可以被解析，但XML要求严格的格式，任何没有正确嵌套的标签都会导致错误。

XML是一个技术体系，除了我们经常用到的XML文档本身外，XML还支持：

- DTD和XSD：验证XML结构和数据是否有效；
- Namespace：XML节点和属性的名字空间；
- XSLT：把XML转化为另一种文本；
- XPath：一种XML节点查询语言；
- ...

实际上，XML的这些相关技术实现起来非常复杂，在实际应用中很少用到，通常了解一下就可以了。

### JSON

JSON作为数据传输的格式，有几个显着的优点：

- JSON只允许使用UTF-8编码，不存在编码问题；
- JSON只允许使用双引号作为key，特殊字符用`\`转义，格式简单；
- 浏览器内置JSON支持，如果把数据用JSON发送给浏览器，可以用JavaScript直接处理。

因此，JSON适合表示层次结构，因为它格式简单，仅支持以下几种数据类型：

- 键值对：`{"key": value}`
- 数组：`[1, 2, 3]`
- 字符串：`"abc"`
- 数值（整数和浮点数）：`12.34`
- 布尔值：`true`或`false`
- 空值：`null`

### Servlet

#### 1. Servlet 组件

#### 2. Filter 组件

JavaEE的Servlet规范还提供了一种Filter组件，即过滤器，它的作用是，在HTTP请求到达Servlet之前，可以被一个或多个Filter预处理，类似打印日志、登录检查等逻辑，完全可以放到Filter中。

编写Filter时，必须实现`Filter`接口，在`doFilter()`方法内部，要继续处理请求，必须调用`chain.doFilter()`。最后，用`@WebFilter`注解标注该Filter需要过滤的URL。这里的`/*`表示所有路径。

多个Filter会组成一个链，每个请求都被链上的Filter依次处理。如果一定要给每个Filter指定顺序，就必须在`web.xml`文档中对这些Filter再配置一遍。

```java
// 过滤所有url
@WebFilter(urlPatterns = "/*")
public class EncodingFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {

        request.setCharacterEncoding("UTF-8");
        response.setCharacterEncoding("UTF-8");
        // 如果还要继续处理请求，必须调用chain.doFilter()
        chain.doFilter(request, response);
    }

}
```

#### 3. Listener 组件

1. **ServletContextListener**：一个Web服务器可以运行一个或多个WebApp，对于每个WebApp，Web服务器都会为其创建一个全局唯一的`ServletContext`实例，`ServletContext`实例最大的作用就是设置和共享全局信息；此外，`ServletContext`还提供了动态添加Servlet、Filter、Listener等功能，它允许应用进程在运行期间动态添加一个组件，虽然这个功能不是很常用；
2. `HttpSessionListener`：监听HttpSession的创建和销毁事件；
3. `ServletRequestListener`：监听ServletRequest请求的创建和销毁事件；
4. `ServletRequestAttributeListener`：监听ServletRequest请求的属性变化事件（即调用`ServletRequest.setAttribute()`方法）；
5. `ServletContextAttributeListener`：监听ServletContext的属性变化事件（即调用`ServletContext.setAttribute()`方法）；

- 任何标注为`@WebListener`，且实现了特定接口的类会被Web服务器自动初始化。
- 可以把初始化数据库连接池等工作放到`contextInitialized()`回调方法中，把清理资源的工作放到`contextDestroyed()`回调方法中，因为Web服务器保证在`contextInitialized()`执行后，才会接受用户的HTTP请求

```java
@WebListener
public class Applistener implements ServletContextListener {

    private final Logger LOGGER = LoggerFactory.getLogger(getClass());

    // 在此初始化WebApp,例如打开数据库连接池等
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        LOGGER.info("WebApp Initialized....");
    }

    // 在此清理WebApp,例如关闭数据库连接池等
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        LOGGER.info("WebApp Destroyed!");
    }

}
```

### Tomcat

- 1 下载tomcat压缩包.zip，解压到任意目录，不用配置环境变量
- 2 启动Tomcat：找到并运行tomcat根目录/bin/startup.bat，出现Server startup in...时表示启动成功，可以最小化Tomcat小黑窗但不要关闭，有且只能有一个Tomcat运行
- 3 编写.jsp文档mainWeb.jsp，保存到WebPage目录下，PS：在Tomcat开启的状态下也可以往目录中放.jsp文档
- 4 .jsp文档放到Tomcat 的JSP 默认目录：tomcat根目录/webapps/ROOT
- 5 .jsp文档放到自定新的JSP的Web服务目录：打开tomcat根目录/conf/server.xml文档，在</Host>的前面加入

```xml
<!-- 其中"/web"为虚拟目录，对应的为服务目录为"E:/Code/Java/workspace/LWeb/WebContent/WebPage"，可自行定义，修改server.xml后要重启Tomcat -->
<Context path="/web" docBase="E:/Code/Java/workspace/LWeb/WebContent/WebPage" debug="0" reloadable="true"></Context>
```

- 6 访问：
    1. 默认目录访问：`http://localhost:8080`
    2. 自定目录访问：`http://localhost:8080/web/mainWeb.jsp`
    必要时将localhost:8080替换成服务器的ip地址
- 7 关闭Tomcat：找到并运行tomcat根目录/bin/shutdown.bat

### 打包部署

#### jlink

java9开始，可以使用jlink打包项目，甚至可以生成最小自包含jre的项目包

java9开始，jdk默认不包含jre了，可以使用jlink生成包含指定模块的jre

#### 生成包含指定模块的jre

- linux系统

```sh
# 1.打开终端
# 2.进入某个目录，生成的jre会在这个目录下
cd 指定目录

# 3.生成jre
jlink --module-path jmods --add-modules java.xml --output jre

# 生成jre，包含所有模块（实际上算是jdk的副本了）
# 使用ALL-MODULE-PATH 时，必须显式提供 --module-path 参数
jlink --module-path $JAVA_HOME/jmods --add-modules ALL-MODULE-PATH --output jre
```

- windows系统

```sh
# 1.打开cmd（权限不够时用管理员身份打开cmd）
# 2.进入某个目录，生成的jre会在这个目录下
cd 指定目录

# 3.生成jre
jlink.exe --module-path jmods --add-modules java.xml --output jre
```

#### jpackage

java16开始，可以使用jpackage将项目打包成Linux的deb和rpm，windows的exe，以及macOS的pkg和dmg

```sh
# 将jar打包：生成一个目录，包含bin目录和lib目录
# jpackage会根据当前jdk将全部模块导出生成一个jre，直接双击bin目录的二进制文件即可运行该应用程序
# --type app-image：生成一个目录
# --name -n：生成的目录名称
# --input -i：要打包的文件所在的目录，该目录的所有文件都会打包进应用程序镜像
# --main-class com.example.Main：如果jar包的 MANIFEST.MF 中已经指定了 Main-Class，这个参数可以省略（如springboot插件生成的fat jar）
# --main-jar fat.jar：应用程序主jar（包含主类）的文件名
# --dest output：输出目录，可以不写，默认是当前目录（默认时在当前目录生成一个"--name"选项设置的值为名称的目录）
# --app-version：应用程序/安装包版本
# --java-options：指定程序运行时的jvm选项，一次只能指定一个
jpackage --type app-image \
    --name demo-app \
    --input input \
    --main-class com.example.Main \
    --main-jar demo.jar \
    --dest . \
    --app-version "1.0.0" \
    --java-options "-Xms8m" \
    --java-options "-Xmx128m" \
    --java-options "-XX:MetaspaceSize=16m" \
    --java-options "-XX:MaxMetaspaceSize=256m" \
    --java-options "-XX:+UseSerialGC" \
    --java-options "-Dprism.order=sw"
```

#### 生成可执行文件graalvm

- 父pom

```xml
<properties>
    <native.maven.plugin.version>1.1.1</native.maven.plugin.version>
</properties>

<build>
    <pluginManagement>
        <plugins>
            <!--锁定插件版本和通用配置-->
            <plugin>
                <groupId>org.graalvm.buildtools</groupId>
                <artifactId>native-maven-plugin</artifactId>
                <version>${native.maven.plugin.version}</version>
                <extensions>true</extensions>
                <executions>
                    <execution>
                        <id>build-native</id>
                        <goals>
                            <!--compile-no-fork目标在package阶段构建本机可执行文件，避免启动maven的第二个生命周期-->
                            <goal>compile-no-fork</goal>
                        </goals>
                        <phase>package</phase>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
<profiles>
    <profile>
        <id>native</id>
        <build>
            <plugins>
                <!-- 在这里引用插件，子模块激活该 profile 时就会生效 -->
                <plugin>
                    <groupId>org.graalvm.buildtools</groupId>
                    <artifactId>native-maven-plugin</artifactId>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

- 子pom

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.graalvm.buildtools</groupId>
            <artifactId>native-maven-plugin</artifactId>
            <configuration>
                <!--指定主类-->
                <mainClass>com.handle.nativeImage.Application</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```

- 用native-image工具生成可执行文件

```sh
# graalvm的bin目录下的native-image工具，依赖本地工具链：header files for the C library, glibc-devel, zlib, gcc, and/or libstdc++-static
# archlinux直接安装base-devel就行了，笔者发现自己的电脑上已经有zlib了，不知道什么时候安装的
sudo pacman -S base-devel [zlib]

# 如果是windows系统要先关闭`360`
# target目录下生成的so文件是编译过程中的中间产物（共享库），是给链接器用的，运行时不需要
# 生成的二进制文件是一个完全独立的本地可执行文件，不再需要目标机器上有jre
mvn -Pnative native:compile

# 如果报Execution of ..\jdk-xxx\bin\native-image.cmd @target\tmp\native-image-xxxxxxxxxx.args returned non-zero result
native-image @target\tmp\native-image-xxxxxxx.args
```

## Java高级

### JVM

![JVM大致结构模型](/images/JVM大致结构模型.png)

- 类加载器子系统
    - 加载阶段
        - 引导类加载器
        - 扩展类加载器
        - 应用类加载器
    - 链接阶段
        - 验证
        - 准备
        - 解析
    - 初始化阶段
- 运行时数据区
    - 堆（1:2），线程共享，线程之间通过TLAB隔离
        - 新生代（8:1:1），MinorGC
            - Eden
            - S0
            - S1
        - 老年代Tenured/Old generation，MajorGC
    - 方法区
        - 元空间(永久代)
            - 类型信息
            - 域信息
            - 方法信息
            - 运行时常量池
                - string constants
                - numberic constants
                - class references
                - field references
                - method references
                - name and type
                - invoke dynamic
    - 虚拟机栈，线程私有
        - 栈帧
            - 局部变量表
            - 操作数栈
            - 动态链接
            - 方法返回地址
            - 一些附加信息
    - 本地方法栈，线程私有
    - 程序计数器，线程私有
- 执行引擎
    - 解释器
    - JIT编译器
        - 中间代码生成器
        - 代码优化器
        - 目标代码生成器
    - 垃圾收集器
- 本地方法接口
- 本地方法库

#### 类加载器

类加载器是JVM执行类加载机制的前提

类加载器的作用：类加载器是Java的核心组件，所有的类都是由类加载器进行加载的，类加载器负责通过各种方式将类的二进制数据流读入JVM，转为一个与目标类对应的Class实例，然后交给JVM进行链接、初始化等 操作。

因此，类加载器在整个加载阶段，只能影响到类的加载，而无法改变类的链接和初始化行为。

至于加载的类是否可以运行，则由执行引擎决定

- 类加载的分类：
    - 显式加载：
        - 使用Class.forName("全限定类名")
        - 使用xxx.getClassLoader().loadClass("全限定类名")
        - 使用ClassLoader.getSystemClassLoader().loadClass("全限定类名")
    - 隐式加载：不在代码中直接调用类加载器的方法加载类，而是通过JVM自动加载到内存中
        - 在加载某个类的字节码文件时，该类的字节码文件引用了另外一个类的对象，此时额外引用的类将通过JVM自动加载到内存中

- 类的唯一性：
    - 对于任意一个类，都需要由加载它的类加载器和这个类本身一同确认其在JVM中的唯一性
    - 每一个类加载器，都拥有一个独立的类名称空间：比较两个类是否相等，只有在这两个类是同一个类加载器加载的前提下才有意义。
    - 即使这两个类源自同一个字节码文件，被同一个虚拟机加载，只要加载它们的类加载器不同，这两个类就必定不相等

- 命名空间
    - 每个类加载器都有自己的命名空间，命名空间有该加载器及所有的父加载器所加载的类组成
    - 在同一个命名空间中，不会出现全限定类名相同的两个类
    - 在不同的命名空间中，可能出现全限定类名相同的两个类
    - 在大型应用中，往往借助这一特性来运行同一个类的不同版本

- 类加载机制的三个基本特征
    - 双亲委派机制。但不是所有类加载都遵循这个机制，有的时候，引导类加载器所加载的类型，是可能要加载用户代码的，如JDK内部的ServiceProvider/ServiceLoader机制，用户可以在标准API框架上，提供自己的实现，JDK也需要提供些默认的实现。例如，JNDI、JDBC、文件系统、Cipher等很多方面，都是利用的这种机制，这种情况就不会用双亲委派机制去加载，而是利用上下文加载器
    - 可见性，子类加载器可以访问父类加载器加载的类型，但是反过来是不允许的。不然，因为缺少必要的隔离，我们就没办法利用类加载器去实现容器的逻辑
    - 单一性，父加载器加载过的类型，就不会在子加载器中重复加载。但是，“兄弟”类加载器间，同一类型仍然可以被加载多次，因为互相并不可见

##### 类加载器的分类

分为引导类加载器和自定义类加载器两种

JVM规范将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器

除了引导类加载器外，自定义类加载器都应该有自己的“父类”加载器（实际上并非继承关系，而是包含着“父类”加载器的引用）

##### 引导类加载器

这个类加载器是用C/C++实现的，嵌套在JVM内部，并不继承java.lang.ClassLoader，没有父加载器

用来加载Java的核心库（rt.jar或sun.boot.class.path路径下的内容，如java、javax、sun等开头的类），提供JVM自身需要的类

加载扩展类加载器和应用类加载器，并指定为它们的父加载器

##### 扩展类加载器

Java语言编写，是sun.misc.Launcher的内部类ExtClassLoader，间接继承于ClassLoader

父类加载器为引导类加载器

加载`java.ext.dirs`系统属性指定的目录中的类库，或者`JDK家目录/jre/lib/ext`中的类库。如果用户创建的jar放在此目录下，也会由扩展类加载器加载

##### 应用类加载器

Java语言编写，是sun.misc.Launcher的内部类AppClassLoader，间接继承于ClassLoader

父类加载器为扩展类加载器

加载环境变量classpath或系统属性`java.class.path`指定路径下的类库

我们的应用程序的类加载器默认是应用类加载器

是用户自定义的类加载器的默认父类加载器

通过ClassLoader的getSystemClassLoader()方法可以获取到该类加载器

##### 自定义类加载器

在日常开发中，类的加载几乎是由上述三种类加载器想回配合执行的。必要时，还可以自定义类加载器

自定义类加载器要继承ClassLoader

自定义类加载器可以实现类库的动态加载，加载源可以是本地jar包，也可以是网络上的远程资源

自定义类加载器还可以实现绝妙的插件机制，如OSGI组件框架，Eclipse的插件机制。其为应用程序提供了一种动态增加新功能的机制，这种机制无需重新打包发布应用程序就能实现

同时，自定义类加载器能够实现应用隔离，如Tomcat、Spring等中间件和组件框架都在内部实现了自定义类加载器，并通过它们隔离不同的组件模块

###### 实现方式

- 继承ClassLoader
    - 重写loadClass()方法，loadClass()内部会调用findClass()方法，重写它可能破坏双亲委派机制，不破坏的话，就要保留双亲委派的代码，造成冗余
    - 重写findClass()方法，推荐
- 编写好自定义类加载器后，便可以在程序中调用loadClass()方法来实现类加载

```java
@Test
public void test() throws ClassNotFoundException {
    MyClassLoader myClassLoader = new MyClassLoader("字节码文件的所在目录");
    String className = "全限定类名";
    Class<?> clazz = myClassLoader.loadClass(className);
    System.out.println(clazz.getClassLoader().getClass().getName());
    System.out.println(clazz.getClassLoader().getParent().getClass().getName());
}

public class MyClassLoader extends  ClassLoader {
    private String bytecodePath;

    public MyClassLoader(String bytecodePath) {
        this.bytecodePath = bytecodePath;
    }

    @Override
    public Class<?> loadClass(String name) throws ClassNotFoundException {
        // 如果类名为某个类，就不走双亲委派，自己加载
        if ("全限定类名".equals(name)) {
            return findClass(name);
        }
        return super.loadClass(name);
    }

    @Override
    protected Class<?> findClass(String className) throws ClassNotFoundException {
        String fileName = bytecodePath + className.replace(".", "/") + ".class";
        try (BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream(fileName));
             ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream()) {
            bufferedInputStream.transferTo(byteArrayOutputStream);
            byte[] bytes = byteArrayOutputStream.toByteArray();
            // 这里还可以对bytes做一些操作，如解密
            return defineClass(className, bytes, 0, bytes.length);
        } catch (IOException e) {
            throw new ClassNotFoundException(className);
        }
    }
}
```

##### 获取类加载器的途径

```java
// 通过某个类的Class实例获取类加载器
clazz.getClassLoader()

// 获取当前线程上下文的类加载器，默认情况下它就是应用类加载器
Thread.currentThread().getContextClassLoader()

// 获取应用类加载器
ClassLoader.getSystemClassLoader()

// jdk21
// jdk.internal.loader.ClassLoaders$AppClassLoader
ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
// jdk.internal.loader.ClassLoaders$PlatformClassLoader
ClassLoader extClassLoader = systemClassLoader.getParent();
// null
ClassLoader bootstrapClassLoader = extClassLoader.getParent();
```

数组类的Class对象，不是由类加载器创建的，而是在运行期间JVM根据需要自动创建的

对于数组类的类加载器来说，是通过clazz.getClassLoader()返回的，与数组的元素类型的加载器是一样的

如果数组当中的元素类型是基本数据类型，这个数组类是没有类加载器的

```java
UserVo[] arr = new UserVo[5];
// class [Lcom.handle.demo.vo.UserVo;
System.out.println(arr.getClass());
// jdk.internal.loader.ClassLoaders$AppClassLoader
System.out.println(arr.getClass().getClassLoader());
```

#### 类的生命周期

在Java中的数据类型分为基本数据类型和引用数据类型

基本数据类型由JVM预先定义，引用数据类型则需要进行类的加载

类的生命周期包括如下7个阶段

- 1.加载
- 2.链接
    - 1.验证
    - 2.准备
    - 3.解析
- 3.初始化
- 4.使用
- 5.卸载

##### 1. 加载阶段

将Java类的字节码文件加载到JVM中，并在内存中构建出Java类的原型（类模板对象）：

通过类的全名，获取类的二进制数据流

解析类的二进制数据流为方法区内的数据结构（Java类模板）

在堆中创建该类的java.lang.Class实例，指向方法区该类的的类模板数据

java.lang.Class的构造方法是私有的，只有JVM可以创建该类的实例，它是访问类型元数据的接口

###### 数组类的加载

数组类本身并不是由类加载器负责创建，而是由JVM在运行时根据需要而直接创建的

数组的元素类型仍然需要类加载器去创建

- 创建数组类过程：
    - 1.如果数组的元素类型是引用类型，就遵循定义的加载过程递归加载和创建数组的元素类型
    - 2.JVM使用指定的元素类型和数组维度来创建新的数组类

如果数组的元素类型是引用类型，数组类的可访问性就由元素类型的可访问性决定；否则将被缺省定义为public

##### 2. 链接阶段

###### 1.验证环节

当类加载到JVM后，就开始链接操作，验证是链接操作的第一步

目的是保证加载的字节码是合法、合理并符合规范的

- 大体上JVM要做以下检查
    - 格式验证：
        - 魔数检查、版本检查、长度检查（数据中的每一项是否都有正确的长度）
        - 和加载阶段一起执行。验证通过之后，类加载器才会将类的二进制数据信息加载到方法区中
        - 格式验证之外的验证操作将会在方法区进行
    - 语义验证：
        - 是否有父类（除了Object都应该有父类）
        - 是否继承了final的类或重写了final方法
        - 非抽象类是否实现了所有抽象方法或接口方法
        - 是否存在不兼容的方法（如定义的方法的签名一样，让JVM无从下手调度；abstract方法定义为final）
    - 字节码验证：
        - 在字节码指令的执行过程中，是否会跳转到一条不存在的指令位置
        - 操作数类型是否合理（方法传参类型是否正确、变量赋值类型是否正确）
        - Code属性的StackMapTable（栈映射帧）属性就是在这个阶段，用于检测在特定的字节码处，其局部变量表和操作数栈是否有着正确的数据类型
        - 但是100%准确地判断一段字节码是否可以被安全执行是无法实现的，该过程只是尽可能检查出可以预知的问题
        - 如果没通过这个阶段的检查，JVM也不会正确装载这个类
        - 如果通过了这个阶段的检查，也不能说明这个类是完全没问题的
    - 符号引用验证：符号引用的直接引用是否存在
        - 在解析环节才会执行

###### 2.准备环节

为类的静态变量分配内存，并将其初始化为默认值

这里不包括用static final修饰的基本数据类型（以及用字面量方式定义的字符串类型）的类的静态常量，因为它们在编译的时候，就分配了一个指向常量池的字面量了，准备环节会显式赋值

类的静态变量会分配在方法区中

|类型|默认值|
|:-|:-|
|byte|(byte)0|
|short|(short)0|
|int|0|
|long|0L|
|float|0.0F|
|double|0.0|
|boolean|boolean内部实现是int，int默认值是0,故boolean的默认值就是false|
|char|\u0000|
|referenct|null|

###### 3.解析环节

将类、接口、字段、方法的符号引用转为直接引用，也就是它们在内存中的指针或者偏移量

符号引用就是一些字面量的引用，和JVM的内部数据结构及内存布局无关

字节码文件中就通过常量池进行了大量的符号引用。但是在程序运行时，只有符号引用是不够的，JVM需要明确知道符号引用指向的具体位置

解析环节往往会伴随着JVM在执行完初始化之后再执行

- 关于CONSTANT_String的解析
    - 当在Java源代码中直接使用字符串常量时，就会在字节码文件常量池中出现CONSTANT_String，它表示字符串常量，并且会引用常量池的一个CONSTANT_UTF8的常量项
    - 在运行时常量池中，会维护一张字符串拘留表（intern），它保存所有出现过的字符串常量，并且没有重复项
    - 只要以CONSTANT_String形式出现的字符串都会在这张表中
    - 使用String的intern()方法可以得到一个字符串在拘留表中的引用
    - 因为拘留表中没有重复项，所以任何字面量相同的字符串调用intern()方法的返回总是相等的

##### 3. 初始化阶段

为类的静态变量赋正确的初始值

初始化阶段是类装载的最后一个阶段

如果前面的步骤都没有问题，就表示类可以顺利装载到系统中，此时，才会开始执行字节码指令（如类的静态成员的赋值语句以及static语句块的字节码指令）

- 初始化阶段的重要工作是执行类的初始化方法（`<clinit>()`）
    - 该方法仅能由Java编译器生成并由JVM调用，程序员无法自定义一个同名的方法，更无法直接在Java程序中调用该方法
    - 它是由类的静态成员的赋值语句以及static语句块合并生成的

在加载一个类之前，JVM总是试图加载该类的父类，因此父类的`<clinit>()`总是在子类的`<clinit>()`之前被调用

- Java编译器并不会为所有的类都生成`<clinit>()`初始化方法，如下的类就不会生成：
    - 类中没有声明任何的类静态变量，也没有静态代码块
    - 类中声明了类静态变量，但是没有显式赋值的初始化语句以及静态代码块
    - 类中包含基本数据类型或String类型的静态常量，并且这些常量是直接在声明时用字面量方式显式赋值初始化的，这中情况在准备环节就完成了初始化

###### `<clinit>()`的线程安全性

JVM会在内部确保`<clinit>()`在多线程环境中的安全性

如果有多个线程同时去初始化一个类，那么只会有一个线程去执行这个类的`<clinit>()`方法

正是因为`<clinit>()`是线程安全的，因此如果在一个类的`<clinit>()`方法中有耗时很长的操作，就可能造成多个线程阻塞，引发死锁。并且这种死锁是很难发现的，因为看起来它们并没有可用的锁信息（`<clinit>()`的访问标识只有static，没有synchornized）

如果之前的线程成功加载了类，则在队列中等待的线程就没有机会再执行`<clinit>()`方法了。当需要使用这个类的时候，JVM会直接返回给它已经加载好的类信息

###### 类的初始化情况：主动使用和被动使用

- 主动使用
    - JVM规定，一个类或接口在初次使用前必须进行初始化，这里的“使用”，是指主动使用
    - 如果出现如下的情况，则会对类进行初始化操作（之前的加载、验证、准备已经完成）
        - 当创建一个类的实例时，如使用new关键字，或者通过反射、克隆、反序列化
        - 当调用类的静态方法时，即当使用了invokestatic指令时
        - 当使用类、接口的静态字段时（final修饰的特殊考虑），如getstatic或putstatic
        - 当使用java.lang.reflect包中的方法反射类的方法时，如Class.forName("全限定类名")
        - 当初始化子类时，如果发现父类还没有进行初始化，则需要先触发父类的初始化
        - 如果一个接口定义了default方法，那么直接实现或者间接实现该接口的类初始化时，该接口要在其之前被初始化
        - 当JVM启动时，用户需要指定一个要执行的主类，虚拟机会先初始化这个主类
            - 说明：JVM启动时通过引导类加载器加载一个初始类，这个类在调用主类的main方法之前被链接和初始化。这个方法的执行将一次导致所需类的加载、链接和初始化
        - 当初次调用MethodHandle实例时，初始化该MethodHandle指向的方法所在的类（涉及解析REF_getStatic、REF_putStatic、REF_invokeStatic方法句柄对应的类）
    - 当JVM初始化一个类时要求它的所有父类都已经初始化，但是这条规则不适用于接口
        - 在初始化一个类时，并不会先初始化它所实现的接口
        - 在初始化一个接口时，并不会先初始化它的父接口
        - 因此，一个父接口并不会因为它的子接口或者实现类的初始化而初始化。而是只有当程序首次使用这个接口的静态字段时，才会导致该接口的初始化

- 被动使用
    - 除了以上情况属于主动使用，其他的情况均属于被动使用。被动使用不会引起类的初始化
        - 也就是说，并不是在代码中出现的类，就一定会被加载或者初始化
    - 当访问一个静态字段时，只有真正声明这个字段的类才会被初始化
        - 比如当通过子类引用父类的静态变量，不会导致子类初始化
    - 通过数组定义类引用，不会触发此类的初始化
    - 引用常量不会触发此类或接口的初始化，因为常量在准备环节就已经被显式赋值了
    - 调用ClassLoader类的loadClass()方法加载一个类，并不是对类的主动使用，不会导致类的初始化

- 如下代码可以知道接口是否初始化了

```java
public interface ClinitService {
    // 查看字节码可以知道OBJECT的初始化代码在ClinitService的clinit中
    // 并且只要OBJECT初始化了，就会输出下面的内容
    // 也就是说只要输出了下面的内容，就说明ClinitService的clinit方法被调用了，即ClinitService接口初始化了
    Object OBJECT = new Object() {
        {
            System.out.println("调用了ClinitService的clinit方法");
        }
    };
}
```

##### 4. 类的使用

开发人员在程序中访问和调用类的静态成员信息，或者使用new关键字创建类的对象

##### 5. 类的卸载

- 类、类的加载器、类的实例之间的引用关系

在类加载器的内部实现中，用一个Java集合来存放所加载类的引用

类的Class实例总是会引用它的类加载器，调用getClassLoader()方法就能获得它的类加载器

类的对象总是引用这个类的Class实例，调用getClass()方法就能获取它所属的类的Class实例

此外，所有Java类都有一个静态属性class，它引用这个类的Class实例

![引用关系](/images/引用关系.png)

- 类的生命周期

当一个类被加载、链接和初始化后，它的生命周期就开始了

当一个类的Class实例不再被引用，即不可触及时，Class实例就会结束生命周期，这个类在方法区的数据也会被卸载，从而结束这个类的生命周期

一个类合适结束生命周期，取决于代表它的Class实例何时结束生命周期

- 类的卸载
    - 引导类加载器加载的类型在整个运行期间是不可能被卸载的
    - 被扩展类加载器和应用类加载器加载的类型在运行期间不太可能被卸载，因为扩展类加载器的实例或应用类加载器的实例基本上在整个运行期间总能直接或间接访问得到，它们不可达的可能性极小
    - 被开发人员自定义的类加载器的实例加载的类型只有在很简单的上下文环境中才能被卸载，而且一般还要借助于强制调用虚拟机的垃圾收集功能才可以做到
    - 可以预想，复杂点的应用场景中（比如开发人员自定义类加载器实例的时候采用缓存的策略以提高系统性能），被加载的类型在运行期间是几乎不可能被卸载的（至少卸载的时间是不确定的）
    - 综上，一个已经加载的类型被卸载的几率很小，至少被卸载的时间是不确定的。因此，开发人员在开发的时候，不应该在对虚拟机的类型卸载做任何假设的前提下，来实现系统中的特定功能

#### 字节码文件

一个Java源文件(.java)经过编译器编译之后便会生成一个（或多个，如果一个源文件里面定义了多个类）字节码文件(.class)

字节码文件是一种二进制的类文件，它的内容是JVM的指令

##### 字节码文件的格式

- 字节码文件格式采用一种类似于C语言结构体的方式进行数据存储，这种结构只有两种数据类型：无符号数和表
    - 无符号数属于基本的数据类型，以u1、u2、u4、u8来分别代表1、2、4、8个字节的无符号数。无符号数可以用来描述数字、索引引用、数量值或按照UTF-8编码构成字符串值

    - 表是由多个无符号数或其它表作为数据项构成的复合数据类型，所有表都习惯性地以“_info”结尾。表用于描述有层次关系的复合结构的数据，整个字节码文件本质上就是一张表。由于表没有固定长度，所以通常会在其前面加上个数说明

```c
ClassFile {
    u4             magic;// 魔数，用于识别字节码文件格式（是否合法）
    u2             minor_version;// 编译的小版本号
    u2             major_version;// 编译的大版本号
    u2             constant_pool_count;// 常量池计数器
    cp_info        constant_pool[constant_pool_count-1]; // 常量池表
    u2             access_flags;// 访问标识
    u2             this_class;// 类索引，值为常量池的索引（可以理解成数组下标）
    u2             super_class;// 父类索引，值为常量池的索引（可以理解成数组下标）
    u2             interfaces_count;// 接口计数器
    u2             interfaces[interfaces_count];// 接口集合
    u2             fields_count;// 字段计数器
    field_info     fields[fields_count];// 字段表
    u2             methods_count;// 方法计数器
    method_info    methods[methods_count];// 方法表
    u2             attributes_count;// （class文件）属性计数器
    attribute_info attributes[attributes_count];// （class文件）属性表
}
```

##### 常量池

###### 全限定名

`全类名`com.handle.test.Demo中的`.`替换成`/`，变成com/handle/test/Demo，就是`全限定名`，为了使连续的多个全限定名之间不产生混淆，在使用时最后一般加`;`，表示全限定名结束

###### 简单名称

没有类型和参数修饰的方法或字段名称

```java
public class UserVo {
    private String name;

    public String getName() {
        return name;
    }
}
// 简单名称分别是name和getName
```

###### 描述符

作用：描述字段的数据类型、方法的参数列表（包括数量、类型以及顺序）和返回值

基本数据类型及void类型都用一个大写字符（单词的首字母）来表示

对象类型用字符L加对象的全限定名来表示

long类型用`J`表示(L被对象类型前缀占用了)

boolean类型用`Z`表示(B被byte类型占用了)

数组类型用`[`表示，一个`[`代表一维，如int[][]的描述符为`[[I`

描述方法时，按照先参数列表，后返回值的顺序描述，参数列表按照方法的参数声明顺序放在`()`内，如`int sum(int x, int y)`的描述符为`(I[I) I`

###### 常量池表

- 常量池计数器的计数是从1开始的，因此对应的常量池表的长度是[常量池计数器-1]

- 这是为了满足后面某些指向常量池的索引值的数据在特定情况下需要表达“不引用任何一个常量池表的项的含义”，这种情况就可以用索引值0来表示

- 常量池表中，主要存放各种`字面量（Literal）`和`符号引用（Symbolic Reference）`

- 字面量包含文本字符串和声明为final的常量值

- 符号引用包含类和接口的全限定名、字段的名称和描述符、方法的名称和描述符

- 常量池表中每一项都具备相同的特征，第一个字节（tag byte）作为类型标记，用于确定该项的格式，如下表的标识列

```c
CONSTANT_Utf8_info {
    u1 tag;// 标识，值为1
    u2 length;// 字符串长度
    u1 bytes[length];// 字符串内容
}

CONSTANT_Methodref_info {
    u1 tag;// 标识，值为10
    u2 class_index;// 指向声明此方法的类的描述符，值为常量池的索引（可以理解成数组下标）
    u2 name_and_type_index;// 指向此方法的名称及类型（形参类型及顺序、返回值类型）的描述符，值为常量池的索引（可以理解成数组下标）
}
```

|类型|标识|描述|
|:-|:-|:-|
|CONSTANT_Utf8|1|UTF-8编码的字符串|
|CONSTANT_Integer|3|整型字面量|
|CONSTANT_Float|4|浮点型字面量|
|CONSTANT_Long|5|长整型字面量|
|CONSTANT_Double|6|双精度浮点型字面量|
|CONSTANT_Class|7|类或接口的符号引用|
|CONSTANT_String|8|字符串类型字面量|
|CONSTANT_Fieldref|9|字段的符号引用|
|CONSTANT_Methodref|10|类中方法的符号引用|
|CONSTANT_InterfaceMethodref|11|接口中方法的符号引用|
|CONSTANT_NameAndType|12|字段或方法的符号引用|
|CONSTANT_MethodHandle|15|方法句柄|
|CONSTANT_MethodType|16|方法类型|
|CONSTANT_Dynamic|17||
|CONSTANT_InvokeDynamic|18||
|CONSTANT_Module|19||
|CONSTANT_Package|20||

##### 访问标识

|Flag Name|Value|Interpretation|
|:-|:-|:-|
|ACC_PUBLIC|0x0001|Declared public; may be accessed from outside its package.|
|ACC_FINAL|0x0010|Declared final; no subclasses allowed.|
|ACC_SUPER|0x0020|Treat superclass methods specially when invoked by the invokespecial instruction.|
|ACC_INTERFACE|0x0200|Is an interface, not a class.|
|ACC_ABSTRACT|0x0400|Declared abstract; must not be instantiated.|
|ACC_SYNTHETIC|0x1000|Declared synthetic; not present in the source code.|
|ACC_ANNOTATION|0x2000|Declared as an annotation interface.|
|ACC_ENUM|0x4000|Declared as an enum class.|
|ACC_MODULE|0x8000|Is a module, not a class or interface.|

##### 接口集合

每一个元素的值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Class_info结构的数据

```c
CONSTANT_Class_info {
    u1 tag;
    u2 name_index;
}
```

接口表长度为interfaces_count

interfaces[i]的i的范围是`[0,interfaces_count)`，接口表各元素表示的接口顺序（索引从小到大）和对应源代码中实现的接口顺序一样（从左往右）

##### 字段表

描述接口或类中声明的字段（静态字段和实例字段）

不包含从父类或者实现的接口中继承而来的字段

有可能包含源代码中不存在的字段，如在内部类中为了保持对外部类的访问性，会自动添加指向外部类实例的字段

字段表每个元素都是field_info结构的数据

```c
field_info {
    u2             access_flags;// 字段访问标识
    u2             name_index; // 字段名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u2             descriptor_index;// 字段描述符索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u2             attributes_count;// 字段属性计数器
    attribute_info attributes[attributes_count];// 字段属性集合，具体参考属性表章节笔记
}
```

###### 字段访问标识

|Flag Name|Value|Interpretation|
|:-|:-|:-|
|ACC_PUBLIC|0x0001|Declared public; may be accessed from outside its package.|
|ACC_PRIVATE|0x0002|Declared private; accessible only within the defining class and other classes belonging to the same nest.|
|ACC_PROTECTED|0x0004|Declared protected; may be accessed within subclasses.|
|ACC_STATIC|0x0008|Declared static.|
|ACC_FINAL|0x0010|Declared final; never directly assigned to after object construction.|
|ACC_VOLATILE|0x0040|Declared volatile; cannot be cached.|
|ACC_TRANSIENT|0x0080|Declared transient; not written or read by a persistent object manager.|
|ACC_SYNTHETIC|0x1000|Declared synthetic; not present in the source code.|
|ACC_ENUM|0x4000|Declared as an element of an enum class.|

###### 字段描述符

描述字段的数据类型

|FieldType term|Type|Interpretation|
|:-|:-|:-|
|B|byte|signed byte|
|C|char|Unicode character code point in the Basic Multilingual Plane, encoded with UTF-16|
|D|double|double-precision floating-point value|
|F|float|single-precision floating-point value|
|I|int|integer|
|J|long|long integer|
|L`ClassName;`|reference|an instance of class ClassName|
|S|short|signed short|
|Z|boolean|true or false|
|`[`|reference|one array dimension|

##### 方法表

- 只描述当前类或接口中声明的方法，不包括从父类或父接口继承的方法

- 有可能出现由编译器自动添加的方法，如
    - 类（接口）初始化方法:<clinit>()，静态代码块的内容就会整合到这个方法中
    - 实例初始化方法:<init>()，非静态代码块、构造方法的内容就会整合到这个方法中

- 方法表每个元素都是method_info结构的数据

```c
method_info {
    u2             access_flags;// 方法访问标识
    u2             name_index;// 方法名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u2             descriptor_index;// 方法描述符索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u2             attributes_count;// 方法属性计数器
    attribute_info attributes[attributes_count];// 方法属性集合，具体参考属性表章节笔记
}
```

###### 方法访问标识

|Flag Name|Value|Interpretation|
|:-|:-|:-|
|ACC_PUBLIC|0x0001|Declared public; may be accessed from outside its package.|
|ACC_PRIVATE|0x0002|Declared private; accessible only within the defining class and other classes belonging to the same nest.|
|ACC_PROTECTED|0x0004|Declared protected; may be accessed within subclasses.|
|ACC_STATIC|0x0008|Declared static.|
|ACC_FINAL|0x0010|Declared final; must not be overridden.|
|ACC_SYNCHRONIZED|0x0020|Declared synchronized; invocation is wrapped by a monitor use.|
|ACC_BRIDGE|0x0040|A bridge method, generated by the compiler.|
|ACC_VARARGS|0x0080|Declared with variable number of arguments.|
|ACC_NATIVE|0x0100|Declared native; implemented in a language other than the Java programming language.|
|ACC_ABSTRACT|0x0400|Declared abstract; no implementation is provided.|
|ACC_STRICT|0x0800|In a class file whose major version number is at least 46 and at most 60: Declared strictfp.|
|ACC_SYNTHETIC|0x1000|Declared synthetic; not present in the source code.|

##### 属性表

- 字节码文件、字段表、方法表、方法表的Code属性都可以有自己的属性表

- 字节码文件的属性信息通常被用于Java虚拟机的验证和运行，以及Java程序的调试

- 属性表每个元素都是attribute_info结构的数据

```c
// 这是通用的属性结构，除了attribute_name_index和attribute_length是都有外
// 属性表info部分，根据不同属性（名）对应着不同的项
attribute_info {
    u2 attribute_name_index;// 属性名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u4 attribute_length;// 属性长度（字节）
    u1 info[attribute_length];// 属性表，不同属性（名）对应着不同的项
}

// 这是ConstantValue属性的结构
ConstantValue_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 constantvalue_index;
}

// 这是StackMapTable的结构
StackMapTable_attribute {
    u2              attribute_name_index;
    u4              attribute_length;
    u2              number_of_entries;
    stack_map_frame entries[number_of_entries];// 这里的stack_map_frame又是另一种结构
}

// 这是Code的结构
Code_attribute {
    u2 attribute_name_index;// 属性名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u4 attribute_length;// 属性长度（字节）
    u2 max_stack;// 操作数栈深度最大值
    u2 max_locals;// 局部变量表最大长度
    u4 code_length;// 字节码指令长度
    u1 code[code_length];// 字节码指令（操作码 [操作数]）集合，操作码一般是一个字节，操作数为两个字节（如果有），操作数的值也是常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u2 exception_table_length;// 异常表长度
    {   u2 start_pc;// 字节码指令集合（code[code_length]）的开始索引（try块开始位置）
        u2 end_pc;// 字节码指令集合（code[code_length]）的结束索引（try块结束位置）
        u2 handler_pc;// 字节码指令集合（code[code_length]）的catch异常处理索引（catch块开始位置）
        u2 catch_type;// catch的异常类型
    } exception_table[exception_table_length];// 异常表
    u2 attributes_count;// Code属性计数器
    attribute_info attributes[attributes_count];// Code属性的属性表（集合），例如LineNumberTable、LocalVariableTable
}
// 行号表
LineNumberTable_attribute {
    u2 attribute_name_index;// 属性名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u4 attribute_length;// 属性长度（字节）
    u2 line_number_table_length;// 行号表长度
    {   u2 start_pc;// 字节码指令集合（code[code_length]）的开始索引，源文件一行代码对应多条字节码指令，每条指令占1到多个字节
        u2 line_number; // 源文件代码行号
    } line_number_table[line_number_table_length];// 行号表集合
}

// 局部变量表
LocalVariableTable_attribute {
    u2 attribute_name_index;// 属性名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u4 attribute_length;// 属性长度（字节）
    u2 local_variable_table_length;// 局部变量表长度
    {   u2 start_pc;// 变量的作用域在字节码指令集合（code[code_length]）的开始索引
        u2 length;// 变量的作用域长度（字节），变量的作用域为code[start_pc]到code[start_pc + length -1]
        u2 name_index; // 变量名，即源代码中定义的变量名
        u2 descriptor_index;// 变量的描述符（指明变量的类型）索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
        u2 index;// 变量在局部变量表集合中的索引（槽，一个槽4个字节），通常从方法形参开始，按照变量的定义顺序索引从0依次递增（非静态方法索引0位置是this变量，其它变量索引从1开始）
    } local_variable_table[local_variable_table_length]; // 局部变量表集合
}

// 源文件
SourceFile_attribute {
    u2 attribute_name_index;// 属性名索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
    u4 attribute_length;// 属性长度，官方指定必须是2
    u2 sourcefile_index;// 源文件名称索引，值为常量池的索引（可以理解成数组下标），指向的是CONSTANT_Utf8_info结构的数据
}
```

###### 属性（名）

- java21有30种属性（名），每一种属性又对应着不同的属性表结构，如下表

|Attribute|Location|
|:-|:-|
|SourceFile|ClassFile|
|InnerClasses|ClassFile|
|EnclosingMethod|ClassFile|
|SourceDebugExtension|ClassFile|
|BootstrapMethods|ClassFile|
|Module, ModulePackages, ModuleMainClass|ClassFile|
|NestHost, NestMembers|ClassFile|
|Record|ClassFile|
|PermittedSubclasses|ClassFile|
|ConstantValue|field_info|
|Code|method_info|
|Exceptions|method_info|
|RuntimeVisibleParameterAnnotations, RuntimeInvisibleParameterAnnotations|method_info|
|AnnotationDefault|method_info|
|MethodParameters|method_info|
|Synthetic|ClassFile, field_info, method_info|
|Deprecated|ClassFile, field_info, method_info|
|Signature|ClassFile, field_info, method_info, record_component_info|
|RuntimeVisibleAnnotations, RuntimeInvisibleAnnotations|ClassFile, field_info, method_info, record_component_info|
|LineNumberTable|Code|
|LocalVariableTable|Code|
|LocalVariableTypeTable|Code|
|StackMapTable|Code|
|RuntimeVisibleTypeAnnotations, RuntimeInvisibleTypeAnnotations|ClassFile, field_info, method_info, Code, record_component_info|

##### 字节码指令

Java虚拟机的指令由一个字节长度的、代表着某种特定操作含义的数字（操作码，opcode），

以及跟随其后的零个或多个代表此操作所需参数的操作数（operand）所构成，即`操作码 [操作数]`

由于JVM采用面向操作数栈而不是寄存器的结构，所以大多数的指令都不包含操作数，只有一个操作码

###### 带数据类型的字节码指令

对于大部分与数据类型相关的字节码指令，它们的操作码助记符中都有特殊的字符来表明专门为哪种数据类型服务，如`iload`

至于byte、short、char、boolean类型的数据都会转成int再进行操作

|操作码助记符前缀|描述|
|:-|:-|
|i|代表对`int`类型的数据进行操作|
|l|代表对`long`类型的数据进行操作|
|f|代表对`float`类型的数据进行操作|
|d|代表对`double`类型的数据进行操作|
|a|代表对`引用`类型的数据进行操作|

###### 局部变量压入操作数栈指令

|指令（助记符）|描述|
|:-|:-|
|xload_n|x=(i,l,f,d,a)，n=[0,3]，表示将局部变量表中，第n个索引位置的变量压入操作数栈|
|xload n|x=(i,l,f,d,a)，n=正整数，当n大于3时，`xload_n`不再适用，就要用到这个指令了|

###### 常数压入操作数栈指令

将常数n压入操作数栈，根据数据类型和入栈内容的不同，又可以分为const系列、push系列和ldc系列的指令

|指令（助记符）|描述|
|:-|:-|
|iconst_n|n=[-1,5]，PS：n=-1时，指令为iconst_m1|
|lconst_n|n=[0,1]|
|fconst_n|n=[0,2]|
|dconst_n|n=[0,1]|
|bipush_n|n=[-128,127]，byte int，接收1字节整数作为参数|
|sipush_n|n=[-32768,-32767]，short int，接收2字节整数作为参数|
|ldc index|接收1字节的index，它是指向常量池中的int、float或者String的索引|
|ldc_w w_index|接收2字节的w_index，它是指向常量池中的int、float或者String的索引，能支持的常量池索引范围大于ldc index|
|ldc2_w w_index|接收2字节的w_index，它是指向常量池中的long或者double的索引|
|aconst_null|将null压入操作数栈|

##### 操作数栈出栈存到局部变量表

用于将操作数栈中栈顶元素弹出后，装入局部变量表的指定位置，用于给局部变量赋值

|指令（助记符）|描述|
|:-|:-|
|xstore_n|x=(i,l,f,d,a)，n=[0,3]，表示从操作数栈弹出一个值，并把它赋值给局部变量表索引为n位置的变量|
|xstore n|x=(i,l,f,d,a)，n为1个字节的正整数，表示从操作数栈弹出一个值，并把它赋值给局部变量表索引为n位置的变量|

##### 算术指令

用于对两个操作数栈上的值进行某种特定运算，并把结果重新压入操作数栈

|指令（助记符）|描述|
|:-|:-|
|xadd|x=(i,l,f,d)，加法指令|
|xsub|x=(i,l,f,d)，减法指令|
|xmul|x=(i,l,f,d)，乘法指令|
|xdiv|x=(i,l,f,d)，除法指令|
|xrem|x=(i,l,f,d)，取余指令，remainder|
|xneg|x=(i,l,f,d)，取反指令，negation|
|iinc|自增指令，`iinc index by number`局部变量表索引为index的值加number|
|xshl|x=(i,l)，左移指令|
|xshr|x=(i,l)，右移指令|
|xushl|x=(i,l)，无符号左移指令|
|xushr|x=(i,l)，无符号右移指令|
|xand|x=(i,l)，按位与或指令|
|por|p=(i,l)，按位或指令|
|pxor|p=(i,l)，按位异或指令|

##### 比较指令

操作数栈栈顶两个元素出栈，比较大小，然后将比较结果入栈

- 假设栈顶元素为v2, 第二个元素为v1
    - 若`v1=v2`, 比较结果为0
    - 若`v1>v2`，比较结果为1
    - 若`v1<v2`，比较结果为-1

|指令（助记符）|描述|
|:-|:-|
|lcmp|l表示long|
|xcmpl|x=(f,d)，如果遇到NaN值，结果为-1，l表示lesser|
|xcmpg|x=(f,d)，如果遇到NaN值，结果为1，g表示greater|

##### 类型转换指令

###### 自动类型转换

将两种不同的数值类型（基本数据类型）进行相互转换

一般用于实现代码中的显式类型转换

或者用来处理字节码指令集中数据类型相关指令无法与数据类型一一对应的问题（如byte转long）

从byte、short、char到int实际上是没有相关的指令的，直接将它们看成是int了

简化为：int->long->float->double

|指令（助记符）|描述|
|:-|:-|
|i2l|int转为long|
|i2f|int转为float，可能损失精度|
|i2d|int转为double|
|l2f|long转为float，可能损失精度|
|l2d|long转为double，可能损失精度|
|f2d|float转为double|

###### 强制类型转换

可能会发生上限溢出、下限溢出和精度丢失的问题

当float转为byte时是没有相关指令的，生成f2i和i2b两条指令来完成，long转为byte同理也是生成两条指令

- 当将一个浮点值转为整型（int或long）时
    - 如果浮点值为NaN，则转换结果为0
    - 如果浮点值不是无穷大，则向零舍入取整，获得整数v
        - 如果v在int或long的范围内，则转换结果是v
        - 如果v不在int或long的范围内，则根据v的符号转为int或long能表示的最大正负值
    - 如果浮点值是无穷大，则根据其符号转为int或long能表示的最大正负值

- 当将一个double转为float时
    - 如果转换结果的绝对值太小无法用float表示，将返回float类型的正负零
    - 如果转换结果的绝对值太大无法用float表示，将返回float类型的正负无穷大
    - 如果是double类型的NaN，则转为float类型的NaN

|指令（助记符）|描述|
|:-|:-|
|i2b|int转为byte|
|i2s|int转为short|
|i2c|int转为char|
|l2i|long转为int|
|f2i|float转为int|
|f2l|float转为long|
|d2i|double转为int|
|d2l|double转为long|
|d2f|double转为float|

##### 对象的创建与访问指令

###### 对象创建指令

|操作码助记符|描述|
|:-|:-|
|new index|创建类实例，接收一个操作数，为指向常量池的索引，表示创建的类型，执行完成后，将对象的引用压入操作数栈|
|newarray|创建基本类型数组|
|anewarray|创建引用类型数组|
|multianewarray|创建二维以上数组|

###### 字段访问指令

|指令（助记符）|描述|
|:-|:-|
|getstatic index|访问类静态字段，index为指向常量池的Fieldref索引，作用是获取Fieldref指定的对象或值，并压入操作数栈|
|putstatic index|访问类静态字段，index为指向常量池的Fieldref索引，作用是设置Fieldref指定的对象或值，并将类的引用和字段要设置的值弹出操作数栈|
|getfield index|访问类实例字段，index为指向常量池的Fieldref索引，作用是获取Fieldref指定的对象或值，并压入操作数栈|
|putfield index|访问类实例字段，index为指向常量池的Fieldref索引，作用是设置Fieldref指定的对象或值，并将类的引用和字段要设置的值弹出操作数栈|

###### 数组操作指令

b表示byte和boolean

|指令（助记符）|描述|
|:-|:-|
|xaload|x=(b,s,i,l,f,d,c,a)，把栈顶数组索引arrayIndex和栈顶顺位第2个元素即数组引用arrayReference都出栈，将array[arrayIndex]入栈|
|xastore|x=(b,s,i,l,f,d,c,a)，把栈顶的数值v，栈顶顺位第2个元素即数组索引arrayIndex和栈顶顺位第3个元素即数组引用arrayReference都出栈，并将v赋给array[arrayIndex]|
|arraylength|把栈顶的数组引用出栈，获取数组长度并压入操作数栈|

###### 类型检查指令

|指令（助记符）|描述|
|:-|:-|
|instanceof constant_pool_index|constant_pool_index为指向CONSTANT_Class结构的常量池索引，判断给定对象是否为某个类的实例，将判断结果压入操作数栈|
|checkcast constant_pool_index|constant_pool_index为指向CONSTANT_Class结构的常量池索引，检查是否可以类型强转，如果可以，checkcast指令不改变操作数栈，如果不可以，抛出ClassCastException|

##### 方法指令

###### 方法调用指令

|指令（助记符）|描述|
|:-|:-|
|invokeinterface|用于调用接口方法，在运行时搜索由特定对象所实现的这个接口方法，并找出适合的方法进行调用，如：IUserService userService = new UserServiceImpl(); userService.getUserById(userId);getUserById指向的是IUserService中的方法，就会用这个指令|
|invokevirtual|用于调用对象的实例方法，根据对象的实际类型进行分派（虚方法分派），支持多态。如UserServiceImpl userService = new UserServiceImpl(); userService.getUserById(userId);getUserById指向的是UserServiceImpl中的方法，这个方法可能是被子类重写过的，就会用这个指令|
|invokespecial|用于调用一些需要特殊处理的实例方法，包括构造器方法（<init>）、私有方法和父类方法，这些方法都是静态类型绑定的（这些方法不存在重写，具有确定性），不会在调用时动态派发|
|invokestatic|用于调用类静态方法，也是静态绑定的|
|invokedynamic|用于调用动态绑定的方法，jdk1.7后新增的指令。在运行时解析调用点限定符所引用的方法，并执行该方法。前4调指令的分派逻辑固化在JVM内部，而此指令的分派逻辑由用户设定的引导方法决定|

###### 方法返回指令

将当前方法操作数栈栈顶元素弹出，并将这个元素压入调用者方法的操作数栈中

如果当前方法是synchronized方法，还会执行一个隐含的monitorexit指令，推出临界区

最后，会丢弃当前方法的整个栈帧，恢复调用者方法的栈帧，并将控制权转交给调用者方法

|指令（助记符）|返回类型|
|:-|:-|
|return|void|
|ireturn|int（byte,short,char,boolean）|
|lreturn|long|
|freturn|float|
|dreturn|double|
|areturn|reference|

##### 操作数栈管理指令

这些指令属于通用型，无需指明数据类型

只要将dup和_x的系数相加，结果即为需要插入的位置，如dup_x2为1+2, 即栈顶3个slot下面

|指令（助记符）|描述|
|:-|:-|
|pop|将栈顶的1个slot数值出栈|
|pop2|将栈顶的2个slot数值出栈，如1个double类型数值|
|dup|复制栈顶的1个slot的数值并压入栈顶，如1个int或1个引用|
|dup2|复制栈顶的2个slot的数值并压入栈顶，如1个long，或2个int|
|dup_x1|复制栈顶的1个slot的数值并压入栈顶往栈底的2个位置，假设复制前栈顶为索引0（索引往栈底递增）, 则复制后插入的位置为索引2的位置，相当于原来的索引2开始往栈底的slot索引都加1|
|dup_x2||
|dup2_x1|复制栈顶的2个slot的数值并压入栈顶往栈底的3个位置，假设复制前栈顶为索引0（索引往栈底递增）, 则复制后插入的位置为索引3的位置，相当于原来的索引3开始往栈底的slot索引都加2|
|dup2_x2||
|swap|将栈顶的两个slot的数值交换，JVM没有提供交换两个8字节的数据类型（long、double）数值的指令|
|nop|字节码为0x00, 表示什么都不做，可用于调式、占位等|

##### 控制转移指令

###### 条件跳转指令

弹出栈顶元素，测试它`（跟0比较或跟null比较）`是否满足某一条件，`比较结果不入栈`，如果满足条件，则跳转到指定位置，否则继续执行下一条指令

这些指令都接收2个字节的操作数，用于计算跳转的位置

byte、short、char、boolean类型的条件分支比较操作，都是用int类型的比较指令完成

long、float、double类型的条件分支比较操作，则会先执行相应类型的比较指令，将运算结果（int类型，-1, 0, 1）压入操作数栈，然后再执行int类型的条件分支比较操作

|指令（助记符）|描述|
|:-|:-|
|ifeq|当栈顶int类型数值`等于0`时跳转|
|ifne|当栈顶int类型数值`不等于0`时跳转|
|iflt|当栈顶int类型数值`小于0`时跳转|
|ifle|当栈顶int类型数值`小于等于0`时跳转|
|ifgt|当栈顶int类型数值`大于0`时跳转|
|ifge|当栈顶int类型数值`大于等于0`时跳转|
|ifnull|当栈顶元素`为null`时跳转|
|ifnonnull|当栈顶元素`不为null`时跳转|

###### 比较条件跳转指令

相当于比较指令+条件跳转指令二合一

弹出栈顶的两个元素进行比较，`比较结果不入栈`。条件成立则跳转，否则继续执行下一条指令

i前缀表示int类型的比较（包括byte、short、char、boolean），a前缀表示对象引用的比较

这些指令都接收2个字节的操作数，用于计算跳转的位置

假设栈顶元素为v2, 栈顶顺位第二个元素为v1

|指令（助记符）|描述|
|:-|:-|
|if_icmpeq|`v1=v2`时跳转|
|if_icmpne|`v1!=v2`时跳转|
|if_icmplt|`v1<v2`时跳转|
|if_icmple|`v1<=v2`时跳转|
|if_icmpgt|`v1>v2`时跳转|
|if_icmpge|`v1>=v2`时跳转|
|if_acmpeq|`v1=v2`时跳转|
|if_acmpne|`v1!=v2`时跳转|

###### 多条件分支跳转指令

用于switch条件跳转

tableswitch要求case值是连续的，它内部只存放起始值和终止值，以及若干个跳转偏移量，通过给定的操作数index，可以立即定位到跳转偏移量位置，因此效率比较高

lookupswitch内部存放着各个离散的case-offset对，每次执行要搜索全部的case-offset对，找到匹配的case值，并根据对应的offset计算跳转地址，因此效率低

lookupswitch出于效率考虑，会将case-offset对安照case值大小进行排序

|指令（助记符）|描述|
|:-|:-|
|tableswitch|case值连续时|
|lookupswitch|case值不连续时|

###### 无条件跳转指令

jsr、jsr_w和ret主要用于try-finally语句，由于try-finally已经用异常表来表示了，现在已经逐渐被JVM废弃

|指令（助记符）|描述|
|:-|:-|
|goto|接收2个字节的操作数，用于指定跳转的位置|
|goto_w|接收4个字节的操作数，用于指定跳转的位置，可以表示更大的地址范围|
|jsr|接收2个字节的操作数，并将jsr下一条指令地址压入操作数栈|
|jsr_w|接收4个字节的操作数，并将jsr下一条指令地址压入操作数栈|
|ret|返回到指定的局部变量所给出的指令位置（一般与jsr、jsr_w联合使用）|

##### 异常处理指令

###### 抛出异常指令

除了显式抛出异常外，JVM规范还规定了许多运行时异常会在其它指令检测到异常情况时自动抛出

|指令（助记符）|描述|
|:-|:-|
|athrow|显式抛出异常|

###### 异常处理与异常表

在JVM中，处理异常（catch语句）不是由字节码指令来实现的（早期使用jsr、jsr_w、ret指令），而是用异常表来完成的

如果一个方法定义了try-catch或try-finally的异常处理，就会创建一个异常表

- 异常表保存了每个异常的处理信息，如
    - 起始位置
    - 结束位置
    - 程序计数器记录的代码处理的偏移地址
    - 被捕获的异常类在常量池中的索引

当一个异常被抛出时，JVM会在当前的方法里找一个匹配的处理，如果没有找到，这个方法会强制结束并弹出当前栈帧，并且异常会重新抛给上层的调用者方法（将异常实例压入调用者方法的操作数栈上）。如果在所有栈帧弹出前仍然没有找到合适的异常处理，这个线程将终止。如果这个异常在最后一个非守护线程里抛出（比如main线程），将会导致JVM自己终止

如果异常处理最终匹配了所有异常类型，代码就会继续执行。如果方法没有抛出异常，也会执行finally块，在return前，跳到finally块执行

##### 同步控制指令

JVM支持两种同步结构：方法级的同步和方法内部一段指令序列的同步，这两种同步都是使用monitor来支持的

###### 方法级的同步

方法级的同步是隐式的，即无需通过字节码指令来控制，它实现在方法调用和返回操作中

JVM可以从方法表的访问标识ACC_SYNCHRONIZED得知一个方法是否声明为同步方法，如果是同步方法，会在方法调用前进行加锁

当同步方法执行完毕（无论是正常结束还是抛出异常），由JVM释放锁

###### 方法内指定指令序列的同步

在JVM中，任何对象都有一个监视器与之相关联，用来判断对象是否被锁定，当监视器被持有后，对象处于锁定状态

monitorenter和monitorexit在执行时，都会在操作数栈栈顶压入对象，之后的锁定和释放都是针对这个对象的监视器进行的

- 线程使用monitorenter指令请求进入同步代码块
    - 如果当前对象的监视器计数器为0,则允许进入
    - 如果当前对象的监视器计数器为1,则判断持有当前监视器的线程是否为自己
        - 如果是，则允许进入
        - 如果不是，则等待

|指令（助记符）|描述|
|:-|:-|
|monitorenter|进入同步块|
|monitorexit|退出同步块|

##### 字节码解析工具

- 直接用vscode的16进制编辑器打开看

- jclasslib软件或idea的jclasslib插件

- javap指令

```sh
# -v:Print additional information（包括行号、局部变量表、反汇编的字段以及字节码指令等信息）
# -p:Show all classes and members
javap -v -p 字节码文件名.class

# 输出到txt文件
javap -v 字节码文件名.class >somename.txt
```

#### 局部变量表

在方法执行时，JVM使用局部变量表完成方法参数的传递

程序计算的结果可以缓存在局部变量表中

JVM将局部变量表当成一个数组，依次存放this指针（仅非静态方法）、方法的参数以及方法中定义的局部变量

除了long和double类型的值占据两个单元（槽，一个槽4个字节），其余类型仅占一个单元

#### 操作数栈

操作数栈 主要作为方法调用的中转站使用，用于存放方法执行过程中产生的中间计算结果。另外，计算过程中产生的临时变量也会放在操作数栈中。

每当为方法分配栈帧时，JVM需要开辟一块额外空间作为操作数栈，存放用于计算的操作数及其返回结果

执行每一条指令之前，JVM要求该指令的操作数已经入栈

在执行指令时，JVM会将该指令的操作数弹出，并将执行指令的结果重新入栈

操作数栈也是一个槽4个字节，做为一个单元

#### 动态链接

在 Class 文件中，方法调用以符号引用的形式存在于常量池。

为了执行调用，这些符号引用必须被转换为内存中的直接引用。

这个转换过程分为两种情况：对于静态方法、私有方法等在编译期就能确定版本的方法，这个转换在类加载的解析阶段就完成了，这称为静态解析。

而对于需要根据对象实际类型才能确定具体实现的虚方法（这是实现多态的基础），这个转换过程则被推迟到程序运行期间，由动态链接来完成。

因此，动态链接的核心作用是在运行时解析虚方法的调用点，将其链接到正确的方法版本上

#### 堆

对象头包括两部分：标记字段（Mark Word）和类型指针（Klass Word）

标记字段还存放了对象的年龄信息、哈希码、锁状态等

Hotspot 遍历所有对象时，按照年龄从小到大对其所占用的大小进行累加，当累加到某个年龄时，所累加的大小超过了 Survivor 区的一半，则取这个年龄和 MaxTenuringThreshold 中更小的一个值，作为新的晋升年龄阈值

java.lang.OutOfMemoryError: GC Overhead Limit Exceeded：当 JVM 花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。

java.lang.OutOfMemoryError: Java heap space :假如在创建新的对象时, 堆内存中的空间不足以存放新创建的对象, 就会引发此错误。

##### 对象的内存布局

在 Hotspot 虚拟机中，对象在内存中的布局可以分为 3 块区域：对象头（Header）、实例数据（Instance Data）和对齐填充（Padding）

- 对象头包括两部分信息
    - 标记字段（Mark Word）：用于存储对象自身的运行时数据， 如哈希码（HashCode）、GC 分代年龄、锁状态标志、线程持有的锁、偏向线程 ID、偏向时间戳等
    - 类型指针（Klass pointer）：对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例
- 实例数据是对象真正存储的有效信息，也是在程序中所定义的各种类型的字段内容
- 对齐填充不是必然存在的，也没有什么特别的含义，仅仅起占位作用。
    - 因为 Hotspot 虚拟机的自动内存管理系统要求对象起始地址必须是 8 字节的整数倍，换句话说就是对象的大小必须是 8 字节的整数倍。
    - 而对象头部分正好是 8 字节的倍数（1 倍或 2 倍），因此，当对象实例数据部分没有对齐时，就需要通过对齐填充来补全

##### 对象的访问定位

建立对象就是为了使用对象，我们的 Java 程序通过栈上的 reference 数据来操作堆上的具体对象。对象的访问方式由虚拟机实现而定，目前主流的访问方式有：使用句柄、直接指针

使用句柄来访问的最大好处是 reference 中存储的是稳定的句柄地址，在对象被移动时只会改变句柄中的实例数据指针，而 reference 本身不需要修改。

使用直接指针访问方式最大的好处就是速度快，它节省了一次指针定位的时间开销。

###### 句柄

Java 堆中将会划分出一块内存来作为句柄池，reference 中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与对象类型数据各自的具体地址信息
![句柄](/images/句柄.png)

###### 直接指针

如果使用直接指针访问，reference 中存储的直接就是对象的地址
![直接指针](/images/直接指针.png)

#### 方法区

存放：类的元数据（结构/字段/方法信息）、方法的字节码（原始指令序列）、运行时常量池（字面量与符号引用）

字符串常量池和静态变量从jdk1.7开始移到java堆了

jit代码缓存放到了独立的code cache区域

- 永久代 (PermGen) 替换为元空间的理由
    - 整个永久代有一个 JVM 本身设置的固定大小上限，无法进行调整（也就是受到 JVM 内存的限制），而元空间使用的是本地内存，受本机可用内存的限制，虽然元空间仍旧可能溢出，但是比原来出现的几率会更小
    - 元空间里面存放的是类的元数据，这样加载多少类的元数据就不由 MaxPermSize 控制了, 而由系统的实际可用空间来控制，这样能加载的类就更多了
    - 合并 HotSpot 和 JRockit 的代码时, JRockit 从来没有一个叫永久代的东西, 合并之后就没有必要额外的设置这么一个永久代的地方了
    - 永久代会为 GC 带来不必要的复杂度，并且回收效率偏低

##### 运行时常量池

运行时常量池是每个类独有的，由 Class 文件中的常量池转换而来，用于存放编译期生成的各种字面量和对类型、字段、方法的符号引用

字面量包括整数、浮点数和字符串字面量。常见的符号引用包括类符号引用、字段符号引用、方法符号引用、接口方法符号

##### 字符串常量池（String Intern Pool）

字符串常量池 是 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建

字符串常量池从jdk1.7开始不在方法区中了，移到java堆了

将字符串常量池移动到堆中主要是因为永久代（方法区实现）的 GC 回收效率太低，只有在整堆收集 (Full GC)的时候才会被执行 GC。Java 程序中通常会有大量的被创建的字符串等待回收，将字符串常量池放到堆中，能够更高效及时地回收字符串内存。

字符串常量池底层是一个StringTable，本质是一个固定长度的数组+链表结构的HashTable，key是字符串内容的hash，value是对象引用，指向堆中的字符串对象

字符串常量池里存放的是指向堆中某个String对象的引用

- 保证字符串变量s指向的是字符串常量池中的数据的两种方式
    - 1.字面量定义的方式：String s = "hello";
    - 2.调用intern方法：String s = xxxString.intern();

```sh
# 字符串常量池是一个哈希表
# 设置字符串常量池大小（jdk21默认65536，最小128）
-XX:StringTableSize=65536

# 打印字符串常量池统计信息
-XX:+PrintStringTableStatistics
```

#### 直接内存

直接内存是一种特殊的内存缓冲区，并不在 Java 堆或方法区中分配的，而是通过 JNI 的方式在本地内存上分配的

避免 Java 堆与 Native 堆来回复制数据，显著提高 I/O 性能，减少垃圾回收对应用的影响

#### JVM的运行时参数

##### 标准参数选项（以-开头）

这类参数选项比较稳定，后续版本基本不会变化

```sh
# 列出所有的标准参数
java --help

# 使用标准参数选项例子
java -version
```

##### -X参数选项

这类参数选项比较稳定，但是后续版本可能会变更

```sh
# 列出所有的-X参数选项
java -X

# 使用-X参数选项例子
# 采用解释器+即时编译器的混合模式执行程序
-Xmixed
```

##### -XX参数选项

是使用得最多的参数类型，这类选项属于实验性，不稳定

- 布尔类型格式

```sh
# 启用选项
-XX:+选项

# 禁用选项（有的选项默认是启用的，可以禁用）
-XX:-选项
```

- 非布尔类型格式(key-value类型)

```sh
# 数值类型格式
-XX:选项=数值

# 非数值类型格式
-XX:选项=字符串
```

- 列出所有的-XX参数名称和默认值

```sh
# 包括diagnostic和Experimental的参数
-XX:+PrintFlagsFinal -XX:+UnlockDiagnosticVMOptions -XX:+UnlockExperimentalVMOptions
```

##### 添加JVM参数选项

```sh
# 运行jar包
java [JVM参数选项1 JVM参数选项2 ... JVM参数选项n] -jar /path/to/file.jar

# Java程序运行过程中
# 设置布尔类型的JVM参数选项
jinfo -flag [+/-]JVM参数选项名称 进程id

# 设置非布尔类型的JVM参数选项
jinfo -flag JVM参数选项名称=值 进程id
```

##### 指定堆（新生代+老年代）大小

- 默认情况下，堆的初始内存大小为电脑的内存大小/64，堆的最大内存大小为电脑的内存大小/4

- 通常将堆的初始内存大小和堆的最大内存大小设置为相同的值，其目的是垃圾收集清理后不需要重新分隔机算堆区的大小，从而提高性能

- 获取默认堆内存大小，并计算电脑内存大小

```java
// 获取初始的堆内存大小
long initialHeapSize = Runtime.getRuntime().totalMemory();
// 获取最大堆内存大小
long maxHeapSize = Runtime.getRuntime().maxMemory();

System.out.println("初始堆内存大小: " + initialHeapSize / (1024 * 1024) + " MB");
System.out.println("最大堆内存大小: " + maxHeapSize / (1024 * 1024) + " MB");

System.out.println("电脑内存大小: " + initialHeapSize * 64 / (1024 * 1024 * 1024)  + " GB");
System.out.println("电脑内存大小: " + maxHeapSize * 4 / (1024 * 1024 * 1024)  + " GB");
```

```sh
# 指定堆的最小内存和初始内存(单位：g、m、k)
# memory start
-Xms4g

# 仅仅设置堆的初始内存(单位：g、m、k)，如果后面出现-Xms，则后者设置的堆初始内存生效
-XX:InitialHeapSize=2g

# 指定堆的最大内存(单位：g、m、k)，下面两种写法都行
# memory max
-Xmx8g

-XX:MaxHeapSize=8g
```

##### 指定新生代的大小

```properties
# 方法1
-XX:NewSize=512m
-XX:MaxNewSize=1024m

# 方法2（NewSize，MaxNewSize设置为一致），这种方式很少用，通常都是指定堆大小和老年代/新生代的比例的方式
-Xmn512m

# 方法3，设置老年代/新生代内存的比例，在Xms=Xmx并且设置了Xmn的情况下，该参数不需要进行设置，如果设置了也是-Xmn指定的新生代大小生效
# 表示老年代占n（默认是2），新生代占1，新生代占整个堆的1/(1+n)
-XX:NewRatio=n

# 表示eden/两个survivor的比例，默认8:1:1
# 但是如果不显式指定比例，就算加上-XX:-UseAdaptiveSizePolicy，默认的比例也是不生效的
# 如果显示指定比例，就算-XX:+UseAdaptiveSizePolicy，也是手动指定的比例生效
-XX:SurvivorRatio=8

# 关闭（关闭用减号，开启用加号）自适应的内存分配策略
# 建议启用，不要手动指定SurvivorRatio
-XX:-UseAdaptiveSizePolicy
```

##### 指定对象晋升到老年代的年龄阈值

```properties
# 默认是15，一般不会去改
-XX:MaxTenuringThreshold=15
```

##### 空间分配担保

- 在Minor GC前，JVM会检查老年代最大可用的连续空间是否大于新生代所有对象的总空间
    - 如果大于，进行一次Minor GC（安全）
    - 如果小于，JVM则继续检查老年代最大可用的连续空间是否大于历次晋升到老年代的对象的平均大小
        - 如果大于，进行一次Minor GC（有风险）
        - 如果小于，进行一次Full GC

```properties
# 是否设置空间分配担保（jdk6 update24后这个参数不会再影响jvm的空间分配担保策略，相当于这个值无论设置与否都是true）
-XX:HandlePromotionFailure
```

##### 指定元空间的大小

对于一个64位的服务器端JVM来说，默认的-XX:MetaspaceSize值为21MB

这就是初始的高水位线，一旦触及这个水位线，将触发Full GC并卸载没用的类，以及这些类对应的类加载器，然后这个高水位线将会重置

新的高水位线的值取决于GC后释放了多少元空间内存

如果释放的内存不足，那么在不超过-XX:MaxMetaspaceSize时，适当提高该值

如果释放的内存过多，则适当降低该值

如果初始化的高水位线设置过低，上述高水位线调整情况会发生多次

为了避免频繁Full GC，建议将初始高水位线设置为一个相对较高的值

```properties
# 首次触发 Full GC 的元空间的阈值，非元空间初始大小
-XX:MetaspaceSize=1024m

# 元空间的最大大小，默认值为unlimited，只受系统内存的限制，一般不做改动
-XX:MaxMetaspaceSize=2048m

# 禁用类卸载
-XX:+NoClassGC

# 允许类卸载（默认开启），-XX:+NoClassGC优先级更高
-XX:+ClassUnloading

# 打印类加载信息
-XX:+TraceClassLoading

# 打印类卸载信息
-XX:+TraceClassUnloading
```

##### 记录GC活动

###### GC相关的参数选项

```sh
# 打印GC简要信息1
-XX:+PrintGC

# 打印GC简要信息2
-verbose:gc

# 打印GC简要信息3
-Xlog:gc

# 打印GC详情1
-XX:+PrintGCDetails

# 打印进程执行时间，不能单独使用，要加上打印GC的参数一起使用，如-XX:+PrintGCDetails
-XX:+PrintGCTimeStamps

# 打印系统时间（包括时区信息），不能单独使用，要加上打印GC的参数一起使用，如-XX:+PrintGCDetails
-XX:+PrintGCDateStamps

# 打印GC详情2
-Xlog:gc*

# 输出GC信息到指定文件
-Xlog:gc:文件名

# 输出GC详情到控制台，并且包含时间信息
-Xlog:gc*:stdout:time

# 把GC日志写入到一个文件中去，而不是打印到标准输出中
-Xloggc:/.../gc.log

# 启用GC日志文件的自动转储
-XX:+UseGCLogFileRotation 

# GC日志文件的循环数目
-XX:NumberOfGCLogFiles=1 

# GC日志文件的大小
-XX:GCLogFileSize=100m

# 打印GC时线程的停顿时间
-XX:+PrintGCApplicationStoppedTime

# 每次GC前和GC后，都打印堆信息
-XX:+printHeapAtGC
```

###### GC日志格式

- GC日志中有三个时间
    - user：CPU执行用户态代码（核心之外）所使用的时间
    - sys：进程在内核态消耗的CPU时间，即在内核执行系统调用或等待系统事件所使用的CPU时间
    - real：此次GC事件从开始到结束所用的时间
        - 由于多核的存在，一般的GC事件中，real是小于sys+user的
        - 如果real大于sys+user，则应用可能存在IO负载非常重或是CPU不够用

![GC日志规律](/images/gclogregular.png)

![MinorGC日志](/images/minorgc.png)

![FullGC日志](/images/fullgc.png)

- 日志中的垃圾收集器
    - DefNew：Seriall收集器（Default New Generation）
    - ParNew：ParNew收集器
    - PSYoung：Parallel Scanvenge收集器
    - 老年代和新生代同理

- 日志中的新生代总容量，是整个新生代的9/10,因为幸存者区有一个是空着的，没算进去
- 日志中的堆内存总容量=9/10新生代+老年代，因此是小于初始化的内存大小的
- 日志中的Full GC（GC发生的原因）
    - Metadata GC Threshold：元空间不够用了
    - Ergonomics：JVM自适应调整导致的GC
    - System：调用了System.gc()方法

##### 逃逸分析

在JDK 6u23版本之后，HotSpot默认开启了逃逸分析

```properties
# 开启逃逸分析
-XX:+DoEscapeAnalysis

# 查看逃逸分析的筛选结果
-XX:+PrintEscapeAnalysis
```

##### 同步省略

JIT编译器借助逃逸分析来判断同步块所使用的锁对象是否只能够被一个线程访问，而没有被发布到其他线程，

如果是，那么JIT编译器在编译这个同步块的时候就会取消对这部分代码的同步，

这个取消同步的过程就叫同步省略，也叫锁消除

##### 标量替换

- 标量是指无法再分解成更小的数据的数据

- 在Java中，基本数据类型的数据就是标量，引用数据类型的数据就是聚合量

- 在JIT阶段，如果分析到对象未逃逸，就会把这个对象拆解成若干个标量，这个过程就是标量替换（它是栈上分配的一种特例）

```properties
# 开启标量替换（默认打开），允许将（未逃逸）对象打散分配在栈上
-XX:+EliminateAllocations
```

##### OOM相关的JVM参数选项

```sh
# 在OOM的时候生成dump文件
-XX:+HeapDumpOnOutOfMemoryError 

# 在Full GC发生前生成dump文件（OOM之前肯定会FULL GC，所以可能会看到多个dump文件）
-XX:+HeapDumpBeforeFullGC

# 不指定-XX:HeapDumpPath，则在当前目录下生成dump文件
# 指定一个目录就行，如果指定一个文件，OOM时将覆盖该文件内容
-XX:HeapDumpPath=/path

# 当OOM时要执行的命令或脚本的路径，比如重启服务
-XX:OnOutOfMemoryError="echo OOM"
-XX:OnOutOfMemoryError=/path/to/file.sh
```

##### 其它jvm设置

```sh
# 设置栈内存大小
-Xss1024K

# 禁止hotspot执行System.gc()，默认
-XX:+DisableExplicitGC

# 指定代码缓存大小
-XX:InitialCodeCacheSize=512m
-XX:ReservedCodeCacheSize=512m

# 启用代码缓存刷新，让JVM放弃一些被编译的代码
# 避免代码缓存被占满时JVM切换到只解释执行的情况
-XX:+UseCodeCacheFlushing

# 开启逃逸分析
-XX:+DoEscapeAnalysis

# 开启偏向锁
-XX:+UseBiasedLocking

# 开启使用大页面
-XX:+UseLargePages

# 开启TLAB空间（TLAB在Eden空间中，每个线程有自己的TLAB空间）
-XX:+UseTLAB

# 设置TLAB的大小
-XX:TLABSize=1m

# 打印TLAB的使用情况
-XX:+PrintTLAB

# 设置TLAB空间占Eden空间的百分比，默认TLAB仅占整个Eden空间的1%
# 一旦对象在TLAB空间分配内存失败，JVM就会使用加锁机制，在Eden空间分配内存
-XX:TLABWasteTargePercent

# 查看所有参数的默认初始值
-XX:+PrintFlagsInitial

# 查看所有参数的最终值（可能存在修改，不再是初始值）
# =的是默认值，:=的是赋值过的
-XX:+PrintFlagsFinal

# 查看已经被用户或者JVM设置过的详细的参数名称和值
-XX:+PrintCommandLineFlags

# 设置可使用的最大直接内存，默认与堆空间最大值（-Xmx参数值）一致
# java进程使用的内存可以简单看成等于java堆+直接内存
-XX:MaxDirectMemorySize=1g

# 方法被调用超过n次后，触发JIT编译
-XX:CompileThreshold=n

# 当超过一定的时间限度，如果方法的调用次数仍然不足以让它提交给即使编译器编译，这个方法的调用计数器就会被减少一半
# 这个过程称为方法调用计数器热度的衰减
# 而这段时间就称为半衰周期
# 热度衰减的动作是在垃圾收集时顺便进行的
# 关闭热度衰减
-XX:-UseCounterDecay

# 设置半衰周期的时间，单位是秒
-XX:CounterHalfLifeTime=60

# 完全采用解释器模式执行程序
-Xint

# 完全采用即时编译器模式执行程序，如果即时编译出问题，解释器会介入执行
-Xcomp

# 采用解释器+即时编译器的混合模式执行程序
-Xmixed

# java14开始，启用可以在空指针异常时获取详细的调用信息
# 更往后的版本这个选项是默认开启了的
-XX:+ShowCodeDetailsInExceptionMessages
```

##### 建议的jvm选项参数整合

垃圾收集器的选项参数根据具体的垃圾收集器来设置

```sh
# 根据具体物理内存情况，将 -Xms 和 -Xmx 设为一致，避免调整开销
# 指定堆的最小内存和初始内存(单位：g、m、k)
# memory start
-Xms4g

# 指定堆的最大内存(单位：g、m、k)，下面两种写法都行
# memory max
-Xmx4g

# OOM异常终止时自动导出dump文件
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dumpfile.hprof

# (JDK 8u20+) 尝试识别并共享底层 char[] 数组相同的 String 对象，以减少内存占用
# 适用于存在大量重复字符串的场景
-XX:+UseStringDeduplication

# 禁止代码中显式调用 System.gc()，避免人为触发不必要的 Full GC，默认
-XX:+DisableExplicitGC

# java25开始，可以启用紧凑对象头，减少堆内存占用
-XX:+UseCompactObjectHeaders
```

#### 垃圾收集

##### 相关概念

- JVM在进行垃圾收集时，并非每次都对新生代、老年代和方法区的内存进行收集，大多数时候收集的都是新生代的内存

- 部分收集
    - 新生代收集（Minor GC/Young GC）：新生代（Eden/S0、S1）的垃圾收集
    - 老年代收集（Major GC/Old GC）：老年代的垃圾收集
        - 除了并行收集器（如CMS GC）有只单独收集老年代的收集策略，老年代收集经常伴随至少一次的新生代收集
        - 老年代收集后，如果内存还是不足，就报OOM了
- 混合收集（Mixed GC）：整个新生代以及部分老年代的垃圾收集
    - G1 GC就会混合收集
- 整堆收集（Full GC）：整个Java堆和方法区的垃圾收集

- `System.gc()`(方法内部调用`Runtime.getRuntime().gc()`)，系统会建议执行`Full GC`，但是不是必然执行

- 内存溢出（OOM）：没有空闲内存，并且垃圾收集也无法提供更多内存

- 内存泄漏（Memory Leak）：狭义上，对象不会再被程序用到，但是GC又不能将其回收的情况；广义上，不规范的对象声明和定义，导致其生命周期变得很长最终导致OOM的情况

- 垃圾收集的并行：多个垃圾收集线程并行工作，但此时的用户线程处于等待状态

- 垃圾收集的串行：单个垃圾收集线程进行工作，但此时的用户线程处于等待状态

- 垃圾收集的并发：垃圾收集线程和用户线程同时执行（不一定是并行，可能交替执行），垃圾收集线程执行时不会停顿用户线程的执行

###### 安全点

- 程序执行时并非在所有地方都能停顿下来开始GC，只有在特定的位置才可以，这些位置称为`安全点（Safe Point）`

- 安全点的选择很重要，如果太少可能导致GC等待的时间太长，如果太多可能导致运行时的性能问题

- 通常选择一些执行时间较长的指令作为安全点，如方法调用、循环跳转和异常跳转等

- 如何在GC发生时，检查所有线程都跑到最近的安全点停下来呢？
    - 抢先式中断（目前没有虚拟机采用了）：先中断所有线程，如果还有线程不在安全点，就将线程恢复让其跑到安全点
    - 主动式中断：设置一个中断标志，各线程运行到安全点的时候主动轮询这个标志，如果为真，则将自己中断挂起

###### 安全区域

- 在一段代码片段中，对象的引用关系不会发生变化，在这个区域中的任何位置开始GC都是安全的，这个区域就是`安全区域（Safe Region）`

- 安全区域可以看作时可扩展了的安全点

- 安全点机制保证了程序执行时，在不太长的时间内就会遇到可进入的GC安全点

- 在程序“不执行”时，如线程处于Sleep或Blocked状态时，无法响应JVM的中断请求，走到安全点去中断挂起，JVM也不太可能等待线程被唤醒。这种情况，就需要安全区域来解决了

- 机制
    - 当线程运行到安全区域的代码时，首先表示已经进入了安全区域，如果这段时间内发生GC，JVM会忽略进入安全区域状态的线程
    - 当线程即将离开安全区域时，会检查JVM是否已经完成GC，如果完成了，则继续执行，否则线程必须等待直到收到可以离开安全区域的信号为止

###### 强引用、软引用、弱引用和虚引用

特别注意，在程序设计中一般很少使用弱引用与虚引用，使用软引用的情况较多，这是因为软引用可以加速 JVM 对垃圾内存的回收速度，可以维护系统的运行安全，防止内存溢出（OutOfMemory）等问题的产生

|引用类型|GC是否回收|描述|
|:-|:-|:-|
|强引用|不回收|使用new操作符创建一个新的对象，并将其赋值给一个变量，这个变量就成为指向该对象的一个强引用|
|软引用|内存不足才回收，回收后还是不够，就会抛出OOM|通常用来实现内存敏感的缓存|
|弱引用|发现即回收，只能生存到下一次垃圾收集发生|和软引用一样，适合保存可有可无的缓存数据|
|虚引用|如果一个对象只有虚引用，那么它和没有引用几乎一样，随时可能被回收，虚引用不能单独使用，无法通过虚引用来获取被引用的对象，试图通过调用get方法获得对象时，总是返回null|对象回收跟踪，比如能在这个对象被回收时收到一个系统通知|
|终结器引用|jdk17之后可以忘了finalize()方法了，只做了解|在GC时，终结器引用入队。由Finalizer线程通过终结器引用找到对象并调用它的finalize()方法，第二次GC时才能回收被引用对象|

- 强引用的对象只要是可达的，垃圾收集器就永远不会回收被引用的对象

- 而软引用、弱引用和虚引用的可达的的对象，在一定条件下，也都是可以被回收的

```java
// 创建一个对象并建立软引用
SoftReference<UserVo> softReference = new SoftReference<>(new UserVo("handle"));
// 从软引用中获得对象
System.out.println(softReference.get());

// 创建一个对象，建立弱引用
WeakReference<UserVo> weakReference = new WeakReference<>(new UserVo("handle"));
// 从弱引用中获得对象
System.out.println(weakReference.get());

// WeakHashMap的键是弱引用
WeakHashMap<String, String> weakHashMap = new WeakHashMap<>();
weakHashMap.put(new String("k1"), "v1");
System.out.println(weakHashMap);

// 虚引用必须和引用队列一起使用
// 当垃圾收集器准备回收一个对象时，如果发现它还有虚引用
// 就会在回收对象后，将这个虚引用加入到引用队列中，以通知应用程序对象的回收情况
// 由于虚引用可以跟踪对象的回收时间，因此可以将一些资源释放的操作放置在虚引用中执行和记录
ReferenceQueue<UserVo> phantomQueue = new ReferenceQueue<>();
PhantomReference<UserVo> phantomReference = new PhantomReference<>(new UserVo("handle"), phantomQueue);
// 虚引用的get方法总是返回null，无法通过虚引用获取对象
System.out.println(phantomReference.get());
```

###### GC的性能指标

- 吞吐量（throughput）：CPU用于运行用户代码的时间与CPU总消耗时间的比值，即吞吐量=运行用户代码时间/（运行用户代码时间+垃圾收集时间）

- 暂停时间（pause time）：一个时间段内应用程序暂停，让GC线程执行的时间

##### 垃圾收集算法

- 垃圾标记阶段
    - 引用计数算法（Python）
        - 缺点：循环引用问题。解决：手动解除循环引用、使用弱引用
    - 可达性分析算法（Java、C#）
        - 根集合（GC Roots）：一组必须活跃的引用
        - 思路：按照从上到下的方式搜索被根对象集合所连接的目标对象是否可达
        - 可以作为GC Root的元素
            - 虚拟机栈中引用的对象
            - 本地方法栈内引用的对象
            - 方法区中类静态属性引用的对象
            - 方法区中常量引用的对象
                - 如字符串常量池里的引用
            - 所有被同步所持有的对象
            - 虚拟机内部的引用
                - 基本数据类型对应的Class对象
                - 一些常驻的异常对象（NullPointerException、OutOfMemoryError）
                - 系统类加载器
            - 反映虚拟机内部情况的JMXBean、JVMTI中注册的回调、本地代码缓存等

- 垃圾清除阶段
    - 标记-清除算法（标记可达对象，在对象的Header中记录，然后遍历堆内存的对象，如果某个对象的Header中没有标记其为可达对象，则将其清除）
    - 标记-复制算法
    - 标记-压缩算法

- 分代收集算法：新生代用标记-复制算法，老年代用标记-清除或标记-压缩算法

- 增量收集算法：垃圾收集线程每次只收集一部分，与用户线程交替执行

- 分区算法（G1）：将整个堆空间划分成连续的不同小区间，每个小区间都独立使用和独立回收，每次回收若干个小区间

##### 垃圾收集器

- 串行收集器：Serial、Serial Old

- 并行收集器：ParNew、Parrallel Scavenge、Parrallel Old

- 并发收集器：CMS（jdk14中已删除）、G1

- 新生代收集器：Serial、ParNew、Parrallel Scavenge

- 老年代收集器：Serial Old、Parrallel Old、CMS（jdk14中已删除）

- 整堆收集器：G1

- 垃圾收集器组合
    - Serial和Serial Old
    - Serial和CMS（jdk8中废弃，但是还能用，jdk9中开始不能用）
    - ParNew和CMS
    - ParNew和Serial Old（jdk8中废弃，但是还能用，jdk9中开始不能用）
    - Parrallel Scavenge和Parrallel Old
    - Parrallel Scavenge和Serial Old（jkd14中废弃，但是还能用）
    - CMS和Serial Old：CMS失败时Serial Old接手垃圾收集
    - 只用G1

###### Serial GC

- Serial收集器是最基本，历史最悠久的垃圾收集器

- Serial收集器还提供用于执行老年代垃圾收集的Serial Old

- 他们是HotSpot中Client模式下默认的新生代和老年代垃圾收集器

- Serial和Serial Old都采用串行回收（单CPU，单线程完成收集工作）和“Stop the World”机制

- Serial采用标记-复制算法，Serial Old采用标记-压缩算法

- Serial Old主要的两个用途
    - 与新生代垃圾收集器Parrallel Scavenge配合使用
    - 作为老年代垃圾收集器CMS的后备垃圾收集方案

- 在单核CPU的时候才有优势，对于交互较强的应用而言，这种收集器是不能接受的，一般在java web应用程序中也不会采用串行垃圾收集器

```sh
# Serial GC垃圾收集器，新生代用Serial GC，老年代用Serial Old GC
-XX:+UseSerialGC
```

###### ParNew GC

- Par是Parrallel的缩写，New表示收集新生代的垃圾

- 可以看作是Serial收集器的多线程版本，采用标记-复制算法和STW机制

- 是很多JVM运行在Server模式下，新生代默认的收集器

```sh
# ParNew垃圾收集器，新生代使用ParNew GC，不影响老年代
-XX:+UseParNewGC

# 指定垃圾收集线程数量，默认和CPU相同的线程数
-XX:ParrallelGCThreads=2
```

###### Parallel GC

- Parallel Scavenge和Parallel Old也是并行的垃圾回收器，也使用STW机制

- Parallel Old是jdk1.6时提供的用于代替Serial Old的老年代垃圾收集器

- Parallel Scavenge采用标记-复制算法，Parallel Old采用标记-压缩算法

- 和ParNew不同的是，它是`吞吐量优先`的垃圾收集器，以及它有自适应调节策略（动态内存调整分配）

- 高吞吐量可以高效率地利用CPU时间，尽快完成程序的运算任务，主要适合在后台运算而不需要太多交互的任务。因此，常见在服务器环境中使用，例如批处理、订单处理、工资支付、科学计算

- jdk8中默认是此垃圾收集器

```sh
# 新生代用Parallel Scavenge，老年代用Parallel Old，与UseParallelOldGC互相激活
-XX:+UseParallelGC

# 新生代用Parallel Scavenge，老年代用Parallel Old，与UseParallelGC互相激活
-XX:+UseParallelOldGC

# 垃圾收集器的线程数，一般最好与CPU相等
# 默认情况下，当CPU数量小于8时，它等于CPU数量
# 当CPU数量大于8时，它等于3+[5*CPU数量]/8
-XX:ParallelGCThreads=8

# 垃圾收集器最大停顿的毫秒时间（STW的时间），慎用
-XX:MaxGCPauseMillis=20

# 垃圾收集时间占总时间的比例=1/(n+1)，这里指的是这个n的值，取值范围(0,100)，默认99, 也就是垃圾收集时间不超过1%
-XX:GCTimeRatio=99

# 开启Parallel Scavenge的自适应调节策略
# 新生代的大小、Eden和Survivor的比例、晋升老年代的年龄等参数会被自动调整，以达到在堆大小、吞吐量和停顿时间之间的平衡点
# 在手动调优比较困难的场合，可以开启它，仅指定虚拟机的最大堆、目标吞吐量和停顿时间，让虚拟机自己完成调优工作
-XX:+UseAdaptiveSizePolicy
```

###### CMS（Concurrent Mark Sweep）

- jdk1.5时推出的第一款真正意义上的并发收集器，第一次实现了垃圾收集线程和用户线程同时工作

- CMS的关注点时尽可能缩短垃圾收集时用户线程的`停顿时间（低延迟）`

- CMS采用标记-清除算法和STW机制，对`老年代`垃圾进行收集

- 工作原理
    - 初始标记：用户线程短暂暂停，标记出GC Roots能直接关联到的对象，一旦标记完成就恢复用户线程。由于直接关联的对象比较少，所以这个阶段很快完成

    - 并发标记：从GC Roots的直接关联对象开始遍历整个对象图，这个过程耗时较长但是不需要停顿用户线程，可以与垃圾收集线程并发执行

    - 重新标记：由于在并发标记阶段中，用户线程和垃圾收集线程同时运行或交叉运行，因此为了修正并发标记期间，因用户线程继续执行导致标记产生变动的那部分对象的标记记录，这个阶段的停顿时间通常比初始标记阶段稍长，但也远比并发标记阶段花的时间短

    - 并发清除：清理标记阶段判断的已死亡的对象，释放内存。由于不需要移动存活对象，所以这个阶段也可以与用户线程并发执行

- 由于在垃圾收集阶段用户线程没有中断，因此，CMS不能像其它收集器那样等到老年代几乎满了再进行收集，而是当堆内存使用率达到某个阈值时，就要开始进行垃圾收集，以确保CMS垃圾收集期间，用户线程有足够的内存使用。要是CMS垃圾收集期间，预留的内存无法满足程序需要，就会出现一次“Concurrent Mode Failure”失败，这时虚拟机将启动后备预案：临时启用Serial Old收集器来重新进行老年代的垃圾收集，这样停顿时间就很长了

- 弊端
    - 会产生内存碎片，在无法分配大对象的情况下，提前触发Full GC
    - 占用一部分CPU线程，导致应用程序变慢，吞吐量降低
    - 在并发标记阶段如果产生新的垃圾，无法及时回收

```sh
# CMS垃圾收集器，将激活-XX:+UseParNewGC
# 形成组合：新生代ParNew，老年代CMS和SerialOld（备用）
-XX:+UseConcMarkSweepGC

# 堆（指老年代）内存使用率的阈值，一旦到达，CMS便会进行垃圾收集
# jdk5及以前默认值时68，jdk6及以上默认值是92
# 如果内存增长缓慢，可以设置一个稍大的值，降低CMS频率
# 如果内存增长较快，则应该设置为稍小的值，避免频繁Serial Old，进而有效降低Full GC次数
-XX:CMSlnitiatingOccupancyFraction=92

# 在执行完Full GC后对内存进行压缩整理避免内存碎片产生，但是会让停顿时间变长
-XX:+UseCMSCompactAtFullCollection

# 在执行多少次Full GC后对内存空间进行压缩整理
-XX:CMSFullGCsBeforeCompaction=3

# CMS的线程数量，默认（ParallelGCThreads + 3）/ 4
-XX:ParallelCMSThreads=2
```

###### G1（Garbage First） GC

- G1是一个并行收集器，它把堆内存分割为很多不相关的且物理上不连续的区域（Region），使用不同的区域来表示Eden、S0、S1, 老年代，Humongous（G1新增的内存区域，存放超过1.5个region的大对象）

- 设置Humongous的原因：对于堆中的大对象，默认直接分配到老年代，但是如果它是一个短期存在的大对象，就会堆垃圾收集器造成负面影响。为此G1划分出Humongous专门存放大对象。如果1个H区装不下一个大对象，G1会寻找连续的H区来存储。为了能找到连续的H区，有时候不得不启动Full GC。G1的大多数行为都把H区作为老年代的一部分来看待

- G1有计划地避免在整个Java堆中进行全区域的垃圾收集。G1跟踪各个区域里面的垃圾堆积的价值大小（收集所获得的空间大小以及所需时间的经验值），在后台维护一个优先列表，每次根据允许的收集时间，优先收集价值最大的区域

- G1是面向服务端应用的垃圾收集器，主要针对配备多核CPU及大容量内存的机器，以`极高概率满足GC停顿时间的同时，还兼具高吞吐量`的性能特征

- HotSpot垃圾收集器里，除了G1外，其它的都使用内置的JVM线程执行GC的多线程操作，而G1可以采用应用线程承担后台运行的GC工作，即当JVM的GC线程处理速度慢时，系统会调用应用程序线程帮助加速垃圾回收过程

- jdk1.7版本正式启用G1，jdk9为默认的垃圾收集器

- 在下面的情况下，使用G1可能比CMS好：
    - 超过50%的Java堆被活动数据占用
    - 对象分配频率或年代提升频率变化很大
    - GC停顿时间过长（长于0.5-1s）

- G1特点
    - 兼具并行与并发
    - 分代型垃圾收集器，兼顾新生代和老年代
    - 垃圾收集以区域为基本单位，区域之间是标记-复制算法，整体上可以看作是标记-压缩算法，都能避免内存碎片
    - 每次根据允许的收集时间，优先收集价值最大的区域，保证在有限的时间内获取尽可能高的收集效率
        - 相比CMS，G1未必能做到CMS在最好的情况下的停顿，但是比CMS最差情况下的停顿要好很多

- G1提供了3种垃圾回收模式：YoungGC、Mixed GC和Full GC，在不同条件下触发

- G1 GC垃圾收集过程主要包括如下三个环节
    - 新生代GC，是一个并行的独占式收集器，当Eden区用尽时开始这个过程
    - （新生代GC和）老年代并发标记过程（Concurrent Marking），当堆内存使用达到一定值（默认45%）时，开始这个过程
    - 混合收集（Mixed GC），标记完成就马上开始这个过程，一次只需要回收一部分老年代的Region，它和新生代一起被回收
    - Full GC（如果需要，单线程、独占式、高强度的Full GC还是继续存在的，它针对GC的评估失败提供了一种失败保护机制，即强力回收）

- G1的设计原则就是简化JVM性能调优，只需要以下三步即可完成调优
    - 开启G1垃圾收集器
    - 设置堆的最大内存
    - 设置最大的停顿时间

```sh
# 启用G1垃圾收集器
-XX:+UseG1GC

# 设置每个Region的大小，值是2的幂，范围1MB-32MB之间，默认是堆内存的1/2000
# 目标是根据最小的Java堆大小划分出约2048个区域
-XX:G1HeapRegionSize=1m

# 期望达到的最大GC停顿时间（JVM会尽力实现，但不保证达到），默认是200ms
-XX:MaxGCPauseMillis=200

# 并行（会STW）的垃圾收集线程数, 最多是8
-XX:ParallelGCThreads=8

# 并发的垃圾收集线程数，一般为ParallelGCThreads的1/4左右
-XX:ConcGCThreads

# 触发并发GC周期的Java堆占用率阈值，超过这个值就触发GC，默认45
-XX:InitiatingHeapOccupancyPercent=45

# G1就不要用-Xmn和-XX:NewRatio来设置新生代了
# 新生代占用整个堆内存的最小百分比（默认5%）
-XX:G1NewSizePercent
# 新生代占用整个堆内存的最大百分比（默认60%）
-XX:G1MaxNewSizePercent

# 保留内存区域，防止Survivor中的to区溢出
-XX:G1ReservePercent=10

# 设置堆占用率的百分比（0-100），达到这个数值的时候触发全局并发标记
# 默认45%，值为0表示间断进行全局并发标记
-XX:InitiatingHeapOccupancyPercent

# 设置老年代的region被回收时的对象占比，默认85%
# 只有老年代的region中存活的对象占用达到了这个值，才会在Mixed GC中被回收
-XX:G1MixedGCLiveThresholdPercent

# 在全局并发标记结束后，可以知道所有的区有多少空间要被回收
# 每次Young GC之后再次发生Mixed GC之前，会检查垃圾占比是否达到这个值
# 只有达到了，下次才会发生Mixed GC
-XX:G1HeapWastePercent

# 一次全局并发标记之后，最多执行Mixed GC的次数，默认8
-XX:G1MixedGCCountTarget

# 设置Mixed GC收集周期中要收集的老年代region数的上限，默认是Java堆的10%
-XX:G1OldGCSetRegionThresholdPercent
```

###### ZGC

- ZGC采用标记-复制算法，不过 ZGC 对该算法做了重大改进

```sh
# 使用ZGC垃圾收集器，并启用分代ZGC功能
-XX:+UseZGC -XX:+ZGenerational
```

###### Shenandoah GC

```sh
# java15开始，可以使用Shenandoah GC
# java25开始，可以使用分代 Shenandoah GC
-XX:+UseShenandoahGC -XX:ShenandoahGCMode=generational
```

###### 查看默认的垃圾收集器

```sh
# 查看命令行相关参数
-XX:+PrintCommandLineFlags

# 查看具体垃圾收集器参数的值
jinfo -flag 垃圾收集器参数（如：UseG1GC） 进程id
```

###### 垃圾收集器组合

有线连接的都是可以组合来用，虚线表示过时的组合

![垃圾收集器组合](/images/gc.png)

#### 性能调优

##### 性能调优目的

减少GC频率，减少Full GC出现次数，以较小的内存获得较低的延迟或较高的吞吐量

##### 性能调优指标

对于web应用来说，响应时间和吞吐量是最看重的两个指标

- 1.响应时间
- 2.吞吐量
- 3.并发数
- 4.堆内存占用

##### 性能调优步骤

- 1.性能监控
    - GC频繁
    - CPU Load过高
    - OOM
    - 内存泄漏
    - 死锁
    - 程序响应时间较长
- 2.性能分析
    - 打印GC日志
    - 命令行工具：jstack，jmap，jinfo等
    - dump出堆文件，使用内存分析工具分析
    - 使用jconsole，jvisualvm来查看jvm状态
- 3.性能性能调优
    - 适当增加内存，根据业务背景选择垃圾回收器
    - 优化代码，控制内存使用
    - 增加机器，分散节点压力
    - 合理设置线程池线程数量
    - 使用中间件提高程序效率，如缓存，消息队列等

##### 性能调优工具

###### jps(JVM Process Status）

```sh
# jps（Java Process Status）查看当前正在运行的java进程信息
# -q：只显示进程id
# -l：显示完整的进程名称（如主类全限定类名、jar包的完整路径）
# -m：显示进程启动时传递给main方法的参数
# -v：显示进程启动时指定的JVM参数
# hostid：由hostname[:port]构成，适用于监控远程主机上的java程序
# 如果要监控远程主机上的java程序，还需要安装jstatd，但是要考虑到安全问题
# 比如虽然设置了可信主机或网络的访问，但是也容易受到ip地址欺诈攻击
# 除了-q只能单独使用外其它参数可以组合使用
# 如果进程使用了-XX:-UsePerfData参数，则jpa、jstat将无法获取到该进程
jps -lmv
```

###### jstat（JVM Statistics Monitoring Tool）

jstat：JVM Statistics Monitoring Tool

作用：查看JVM统计信息（进程内存使用和垃圾收集信息）

用法：`jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]`

- option：选项，可以用jstat -options查看所有的选项

|序号|选项|描述|
|:-|:-|:-|
|1|-class|显示类加载/卸载的信息，如：类加载/卸载数量，占用空间，消耗的时间等|
|2|-compiler|显示jit编译器编译过的方法、耗时等信息|
|3|-gc|显示垃圾收集相关的信息，如Eden、两个Survivor、老年代、元空间等的容量，已用空间、GC时间合计等信息|
|4|-gccapacity|显示内容与-gc基本相同，但输出主要关注java堆各个区域使用到的最大、最小空间|
|5|-gccause|显示内容与-gcutil一样，但是会额外输出导致最后一次或当前正在发生的GC的原因|
|6|-gcmetacapacity|显示元空间使用到的最大、最小空间|
|7|-gcnew|显示新生代GC信息|
|8|-gcnewcapacity|显示内容与-gcnew基本相同，但主要关注使用到的最大、最小空间|
|9|-gcold|显示老年代GC信息|
|10|-gcoldcapacity|显示内容与-gcold基本相同，但主要关注使用到的最大、最小空间|
|11|-gcutil|显示内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比|
|12|-printcompilation|显示已经被jit编译的方法|

- `-t`：在输出的信息前加上时间（从进程启动开始算起），单位为秒
    - 可以比较进程的启动时间和总GC时间，或者两次测量的间隔时间，得出GC时间占运行时间的比例
        - 如果该比例超过20%，说明目前堆的压力较大
        - 如果该比例超过90%，说明堆里几乎没有可用空间了，随时可能OOM

- `-h`：在周期性数据输出时，输出多少行后输出一行表头

- vmid：虚拟机id，也就是进程id

- interval：用于指定输出统计数据的周期，即查询间隔，单位为毫秒

- count：用于指定查询的总次数

```sh
jstat [指定option] [-t] -h[指定行数] 进程id [指定interval] [指定count]
```

- `-gc`的表头描述
    - S0C：幸存者0区容量
    - S1C：幸存者1区容量
    - S0U：幸存者0区已使用的空间
    - S1U：幸存者1区已使用的空间
    - EC：伊甸园区容量
    - EU：伊甸园区已使用的空间
    - OC：老年代容量
    - OU：老年代已使用的空间
        - 可以每隔一段较长时间获取多组-gc数据，每一组看OU最小值
            - 如果这些值呈上涨趋势，说明老年代使用的空间在不断上涨
            - 意味着无法回收的对象在不断增加，有可能存在内存泄漏
    - MC：方法区容量
    - MU：方法区已使用的空间
    - CCSC：压缩类的容量
    - CCSU：压缩类已使用的空间
    - YGC：Young GC发生的次数
    - YGCT：Young GC花费的时间
    - FGC：Full GC发生的次数
    - FGCT：Full GC花费的时间
    - GCT：总的GC时间（Young GC花费的时间 + Full GC花费的时间）

###### jinfo (Configuration Info for Java)

jinfo：Configuration Info for Java

作用：实时查看和修改JVM配置参数

用法：`jinfo <option> <pid>`

- option：选项，可以用jinfo命令查看所有的选项

|序号|选项|描述|
|:-|:-|:-|
|1|no option|输出全部的JVM参数和系统属性|
|2|-flag name|输出对应名称的JVM参数值|
|3|-flag [+/-] name|开启或关闭对应名称的JVM参数，只有被标记为manageable的参数才可以被动态修改|
|4|-flag name=value|设置对应名称的JVM参数的值|
|5|-flags|输出全部（赋值过）的参数，Non-default的就是赋值过的参数|
|6|-sysprops|输出系统属性|

```sh
# 查看java进程某个参数的值（最终值）
jinfo -flag 参数名（比如NewRatio） 进程id

# 查看标记为manageable的JVM参数
java -XX:+PrintFlagsFinal -version | grep manageable
```

###### jmap(Memory Map for Java)

jmap将访问堆中所有对象，为保证在此过程中不被应用线程干扰，jmap需要借助安全点机制

让所有线程停留在不改变堆中数据的状态。所以jmap导出的堆快照是安全点位置的，这可能导致基于堆快照的分析结果存在偏差。

如果对象的生命周期在两个安全点之间，那么加上live的子选项将无法探知这些对象

如果某个线程长时间无法跑到安全点，jmap将一直等下去

jmap：JVM Memory Map

作用：导出内存映像文件、内存使用情况和查看系统的类加载信息等

用法：`jmap option <pid>`

|序号|选项|描述|
|:-|:-|:-|
|1|-clstats|输出类加载统计信息|
|2|-dump|生成java堆的存储快照：dump文件|
|3|-finalizerinfo|查看被放入Finalization Queue，等待Finalizer线程执行finalize()方法的对象信息|
|4|-histo|输出堆空间中对象的统计信息，包括类、实例数量和合计容量|

- `-dump`还有自己的子选项

|序号|选项|描述|
|:-|:-|:-|
|1|live|只dump堆中存活的对象，如果也指定了all选项，live生效|
|2|all|dump堆中所有的对象，如果没有指定live和all选项，它是默认的|
|3|format=b|二进制格式|
|4|file=文件名|dump堆中的对象到指定文件|
|5|gz=压缩级别数字|如果指定了此选项，则将dump数据以指定的压缩级别压缩为gz，1最快，9压缩比最高|

```sh
jmap -dump:<dump-options> <pid>

jmap -dump:live,format=b,file=heap.bin <pid>
```

- `-histo`也有自己的子选项

|序号|选项|描述|
|:-|:-|:-|
|1|live|只统计存活的对象，如果也指定了all选项，live生效|
|2|all|统计所有的对象，如果没有指定live和all选项，它是默认的|
|3|file=文件名|dump出堆统计信息到指定文件|
|4|parallel=number|堆审查所使用的并行线程数量，默认为0,由JVM自己决定；1表示只用一个线程，不并行；其它数值，JVM将尝试用指定的线程数，但是最终可能会少于指定的线程数|

```sh
jmap -histo[:[<histo-options>]] <pid>

jmap -histo:live,file=/tmp/histo.data <pid>
```

- 导出内存映像文件（dump文件）

```sh
# 手动导出，指定了format=b，文件名推荐.hprof二进制文件格式
jmap -dump:format=b,file=dumpfile.hprof 进程id

# 生产环境导出比较关注的是存活的对象，因此这个命令使用比较多
jmap -dump:live,format=b,file=dumpfile.hprof 进程id

# OOM异常终止时自动导出
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=dumpfile.hprof
```

###### jstack(Stack Trace for Java)

jstack：JVM Stack Trace

作用：生成JVM中指定进程当前时刻的线程快照（每一条线程正在执行的方法堆栈的集合），可用于定位线程长时间停顿的原因

用法：`jstack [-l][-e] <pid>`

|序号|选项|描述|
|:-|:-|:-|
|1|-l|除堆栈外，还显示关于锁的附加信息|
|2|-e|除堆栈外，还显示关于线程的附加信息|

###### jcmd

作用：可以用来实现除了jstat之外所有命令的功能

用法：
    - `jcmd <pid | main class> <command ...|PerfCounter.print|-f file>`
    - `jcmd -l`

```sh
# 列出所有的JVM进程
jcmd -l

# 列出支持该进程的所有命令
jcmd pid help

# 对指定进程执行指定命令
jcmd pid 具体命令
```

###### jstatd

上述的一些命令行工具（jps、jstat），也可以对远程计算机进行监控，只需配合使用jstatd工具

jstatd是一个RMI服务端程序，它的作用相当于代理服务器，建立本地计算机与远程监控工具的通信

jstatd服务器将本机的Java应用程序信息传递到远程计算机

作用：远程主机信息收集

###### jconsole

作用：对正在执行的JVM进程的内存、线程（可以检测死锁）和类等的监控，是一个基于JMX（java management extensions）的GUI性能监控工具

###### VisualVM

官网：<https://visualvm.github.io/>

VisualVM 集成了多个jdk命令行工具的功能，是一个功能强大的多合一故障诊断和性能监控的可视化工具

作用：可用于显示JVM进程、进程配置和环境信息（jps、jinfo）；监视应用程序的CPU、GC、堆、方法区及线程的信息（jstat、jstack）等，甚至代替jconsole

- 推荐安装的插件：Visual GC

- 主要功能
    - 生成/读取堆内存快照
    - 生成/读取线程快照
    - CPU和内存抽样分析，进行快照
    - 查看JVM参数和系统属性
    - 查看运行中的虚拟机进程
    - 实时监控程序资源
    - JMX代理连接
    - 远程环境监控

- 远程连接Java服务
    - 1.确定远程服务器的ip
    - 2.添加JMX（通过JMX技术具体监控远端服务器的哪个Java进程）
    - 3.修改bin/catalina.sh文件，连接远程的tomcat
    - 4.在.../conf中添加jmxremote.access和jmxremote.password文件
    - 5.将服务器地址改为公网ip地址
    - 6.设置阿里云安全策略和防火墙策略
    - 7.启动tomcat，查看tomcat启动日志和端口监听
    - 8.JMX中输入端口号、用户名、密码进行登录

- 程序结束不了，看下是不是出现了死锁，看cpu哪个方法占用的时间多
- Full GC多，或者OOM了，就看内存那些实例占用多

###### MAT

MAT：Memory Analyzer Tool，是一款功能强大的Java堆内存分析器，用于查找内存泄漏及查看内存消耗情况

官网：<https://eclipse.dev/mat/>

作用：可生成/分析dump文件，查看堆内存信息，特别是生成内存泄漏报表，方便定位问题和分析问题

- 这些内存信息包括
    - 所有的对象信息，包括对象实例、成员变量、存储于栈中的基本类型值和存储于堆中的其它对象的引用值
    - 所有的类信息，包括类加载器、类名称、父类、静态变量等
    - GCRoot到所有的这些对象的引用路径
    - 线程信息，包括线程的调用栈及此线程的线程局部变量（TLS）

- 浅堆（Shallow Heap）：一个对象所消耗的内存，对象的基本类型字段+引用类型字段（每个4个字节）+对象头，最后还可能向8字节对齐

- 保留集（Retained Set）：对象A的保留集指当对象A被垃圾回收后，可以被释放的所有的对象集合（包括对象A本身），即对象A的保留集可以被认为是只能通过对象A直接或间接访问到的所有对象的集合。通俗地说，就是指仅被对象A所持有的对象的集合

- 深堆（Retained Heap）：对象的保留集中所有对象的浅堆大小之和

- 对象实际大小：一个对象所能触及的所有对象的浅堆大小之和

- 支配树（Dominator Tree）：在对象引用图中，所有指向对象B的路径都经过对象A，则认为对象A支配对象B。如果对象A是离对象B最近的一个支配对象，则认为对象A为对象B的直接支配者，它有以下性质：
    - 对象A的子树（所有被对象A支配的对象集合）表示对象A的保留集，即深堆
    - 如果对象A支配对象B，那么对象A的直接支配者也支配对象B
    - 支配树的边与对象引用图的边不直接对应

- OQL(Object Query Language),类SQL的查询语音，可以进行对象的查找和筛选

```sql
# from可以指定类名、正则或对象地址
select * from java.util.Vector
select * from "com\.handle\..*"
select * from 0x123456

# objects：以对象的形式显示结果集的项
select objects v.elementData from from java.util.Vector v
select objects s.value from from java.lang.String s

# as retained set：得到所得对象的保留集
select as retained set * from java.lang.String

# 去重
select distince objects classof(s) from java.lang.String s

select * from char[] s where s.@length>10
select * from java.lang.String s where toString(s) like ".*handle.*"
select * from java.lang.String s where s.value!=null
# 数组长度>15, 深堆>1000字节的所有Vector对象
select * from java.util.Vector v where v.elementData.@length>15 and v.@retainedHeapSize>1000

# 访问属性和方法
select toString(f.path.value) from java.io.File f
select s.toString() from java.lang.String s

# 所有的Vector对象及其子类型
select * from instanceof java.util.Vector
```

###### Arthas（阿尔萨斯）

阿里巴巴开源的Java命令行诊断工具，于服务器上在线排除问题，无需重启，动态跟踪Java代码，实时监控JVM状态

官网：<https://arthas.aliyun.com/>

```sh
# 启动方式1，启动后选择JVM进程数字
./as.sh

# 启动方式2，启动后选择JVM进程数字
java -jar arthas-boot.jar

# 启动方式3，先jps查看JVM进程id
java -jar arthas-boot.jar 进程id

# 退出当前客户端
quit/exit

# 退出所有客户端，并关闭arthas
stop/shutdown

# 卸载
rm -rf ~/.arthas/
rm -rf ~/logs/arthas
```

- 常用命令

```sh
# 查看指定命令的帮助
dashboard -h

# 每隔1秒打印一次数据面板，打印3次
dashboard -i 1000 -n 3

# 查看线程信息，不加参数默认按cpu使用率排序展示
thread

# 查看JVM进程的系统属性
sysprop

# dump出活的对象到文件
heapdump --live /tmp/dump.hprof

# 查看JVM已加载的类信息
# -d：输出当前类的详细信息
sc -d com.handle.Application

# 查看已加载类的方法信息
# -d：展示每个方法的详细信息
sm -d com.handle.Application [方法名]

# 反编译指定已加载类的源码
jad com.handle.Application [方法名]

# 编译.java文件生成.class
# mc 命令有可能失败。如果编译失败可以在本地编译好.class文件，再上传到服务器
# -c：指定 classloader
# -d：指定输出目录
 mc --classloader org.springframework.boot.loader.LaunchedURLClassLoader /tmp/UserController.java -d /tmp
 
# 加载外部的.class文件
retransform /tmp/MathGame.class

# 查看 classloader 的继承树，urls，类加载信息
# -t：打印所有 ClassLoader 的继承树
classloader -t

# 方法执行监控
# -c：统计周期，默认值为 60 秒
monitor -c 30 fully.qualified.ClassName methodName

# 方法执行数据观测
# 能观察到的范围为：返回值、抛出异常、入参
# 观察表达式，默认值：{params, target, returnObj}
watch fully.qualified.ClassName methodName {params, returnObj}

# 方法内部调用路径，并输出方法路径上的每个节点上耗时
# -n：执行次数，默认值为 100
trace -n 10 fully.qualified.ClassName methodName

# 输出当前方法被调用的调用路径
# -n：执行次数
stack -n 10 fully.qualified.ClassName methodName

# 方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测
# -t：表明希望记录下所指定的类的方法的每次执行情况
# -n：指定记录的次数
tt -t -n 3 fully.qualified.ClassName methodName

# 启动profiler
# 默认情况下，生成的是cpu的火焰图，即event为cpu。可以用--event参数指定其他性能分析模式
profiler start

# 获取已采集的sample的数量
profiler getSamples

# 查看profiling状态
profiler status

# 停止profiler
# 默认情况下，结果是 Flame Graph 格式的 html 文件，也可以用 -o 或 --format 参数指定其他内容格式，包括 flat、traces、collapsed、flamegraph、tree、jfr
profiler stop --format flamegraph

# 通过浏览器查看arthas-output下面的profiler结果
# http://localhost:3658/arthas-output/
```

###### JMC（JDK Mission Control）

官网：<https://www.oracle.com/java/technologies/jdk-mission-control.html>

JMC是对Java应用程序进行管理、监视、概要分析和故障排除的工具套件，内置了Java Flight Recorder

它采用取样，而不是传统的代码植入技术，对性能的影响极小，完全可以开着JMC做压测

- 如果是连接远程服务器，要开JMX，然后才能在JMC连接

```sh
-Dcom.sun.management.jmxremote.port=${YOUR PORT}
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
-Djava.rmi.server.hostname=${YOUR HOST/IP}
```

- JFR（Java Flight Recorder）
    - 是JMC的其中一个组件
    - 它能以极低的性能开销收集JVM的性能数据
    - JMC能够对它收集的数据进行高效、详细的分析

- JFR使用前，服务器要加参数

```sh
-XX:+UnlockCommercialFeature
-XX:+FlightRecorder
```

- 当启用JFR时，它将记录JVM进程运行过程中发生的一系列事件，共有4种事件类型
    - 瞬时事件（Instant Event），用户关心的是它们发生与否，例如异常、线程启动事件
    - 持续事件（Duration Event），用户关心的是它们的持续时间，如垃圾回收事件
    - 计时事件（Timed Event），是时长超出指定阈值的持续事件
    - 取样事件（Sample Event），是周期性取样的事件
        - 如方法抽样，即每隔一段时间统计各个线程的栈轨迹。如果在这些抽样取得的栈轨迹中存在一个反复出现的方法，那么可以推测该方法是热点方法

###### TProfiler

官网：<https://github.com/alibaba/TProfiler>，已经停更

TProfiler最重要的特性就是能够统计出指定时间段内JVM的top method，这些top method极有可能就是造成JVM性能瓶颈的元凶

###### 其它工具

Btrace、YourKit、JProbe、String Insight

建议是VisualVM、MAT和Arthas掌握就行

#### 火焰图（Flame Graphs）

在追求极致性能的场景下，了解程序运行过程中CPU在干什么很重要，火焰图就是一种非常直观的展示CPU在程序整个生命周期过程中时间分配的工具，能显示出调用栈中的CPU消耗瓶颈

- y轴是函数调用栈，x轴是方法调用时的CPU时间占比，一个个方框就是一个个方法

#### 内存泄漏

- 长生命周期的对象持有短生命周期对象的引用，如定义集合类型变量为类的静态变量，集合中的元素就不会被回收
- 单例对象持有外部对象的引用，这个外部对象也不会被回收
- 外部类的实例方法返回内部类的实例对象，这个内部类对象被长期引用，那么由于内部类持有外部类的实例对象，导致外部类的实例对象不会被回收
- 连接（数据库连接、网络连接、IO等）没有关闭，导致相关资源无法被回收
- 变量不合理的作用域，导致不会被回收或长时间没被回收
- 当一个对象被存储进哈希集合（如HashMap、HashSet）后，改变对象中参与计算哈希值的字段，导致无法从哈希集合中删除该对象，造成内存泄漏
- 缓存泄漏，没有用弱引用的结构存放缓存对象，数据量大导致启动慢或卡死，甚至直接OOM
- 监听器和回调

#### 一些指令

```sh
# -g：Generate all debugging info（如在字节码文件中生成局部变量表信息）
javac -g xxx.java
```

#### 系统变量和环境变量

```java
// 获取（操作系统）环境变量
System.getenv("JAVA_HOME");

// 设置环境变量，这种方式设置的环境变量仅在当前命令行会话中有效。一旦关闭该会话或重新启动系统，环境变量将失效
MY_VARIABLE=myValue java -jar myApp.jar

// 获取（JVM）系统变量
System.getProperty("java.home");

// 设置系统变量，这种方式设置的系统变量仅在当前Java进程的生命周期内有效。一旦Java程序停止并重新启动，系统变量将失效
System.setProperty("myVariable", "myValue");

// 设置系统变量，这种方式设置的系统变量仅在当前Java进程的生命周期内有效。一旦Java程序停止并重新启动，系统变量将失效
java -DmyVariable=myValue -jar myApp.jar

// 指定文件名和路径编码。处理非 ASCII 字符文件名时很重要
java -Dsun.jnu.encoding=UTF-8 myApp.jar
```

### JMM（Java 内存模型）

![jmm](/images/jmm.png)

volatile 关键字可以保证变量的可见性，将变量声明为 volatile ，这就指示 JVM，这个变量是共享且不稳定的，每次使用它都到主存中进行读取

volatile 还有一个重要的作用就是防止 JVM 的指令重排序，在对这个变量进行读写操作的时候，会通过插入特定的 内存屏障 的方式来禁止指令重排序

volatile 关键字能保证数据的可见性，但不能保证数据的原子性。synchronized 关键字两者都能保证

### 对象创建过程

#### 1.类加载检查

虚拟机遇到一条 new 指令时，首先将去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已被加载过、解析和初始化过。

如果没有，那必须先执行相应的类加载过程

#### 2.分配内存

在类加载检查通过后，接下来虚拟机将为新生对象分配内存。

对象所需的内存大小在类加载完成后便可确定，为对象分配空间的任务等同于把一块确定大小的内存从 Java 堆中划分出来。

分配方式有 “指针碰撞” 和 “空闲列表” 两种，选择哪种分配方式由 Java 堆是否规整决定，而 Java 堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定。

##### 指针碰撞

适用场合：堆内存规整（即没有内存碎片）的情况下

原理：用过的内存全部整合到一边，没有用过的内存放在另一边，中间有一个分界指针，只需要向着没用过的内存方向将该指针移动对象内存大小位置即可

使用该分配方式的 GC 收集器：Serial, ParNew

##### 空闲列表

适用场合：堆内存不规整的情况下

原理：虚拟机会维护一个列表，该列表中会记录哪些内存块是可用的，在分配的时候，找一块儿足够大的内存块儿来划分给对象实例，最后更新列表记录。

使用该分配方式的 GC 收集器：CMS

##### 内存分配并发问题

- 虚拟机采用两种方式来保证线程安全：
    - CAS+失败重试
    - TLAB：为每一个线程预先在 Eden 区分配一块儿内存，JVM 在给线程中的对象分配内存时，首先在 TLAB 分配，当对象大于 TLAB 中的剩余内存或 TLAB 的内存已用尽时，再采用上述的 CAS 进行内存分配

#### 3.初始化零值

内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值（不包括对象头），

这一步操作保证了对象的实例字段在 Java 代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对应的零值。

#### 4.设置对象头

初始化零值完成之后，虚拟机要对对象进行必要的设置，例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄等信息

这些信息存放在对象头中。 另外，根据虚拟机当前运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式。

#### 5.执行init方法

在上面工作都完成之后，从虚拟机的视角来看，一个新的对象已经产生了，

但从 Java 程序的视角来看，对象创建才刚开始，<init> 方法还没有执行，所有的字段都还为零。

所以一般来说，执行 new 指令之后会接着执行 <init> 方法，把对象按照程序员的意愿进行初始化，这样一个真正可用的对象才算完全产生出来。

### 设计模式

#### 单例模式

单例模式有很多中写法，一般静态内部类写法和饿汉式写法就能应付绝大多数情况了

- 懒汉式写法（线程不安全）

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (Objects.isNull(instance)) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

- 懒汉式写法（线程安全），也叫双重校验锁写法

```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (Objects.isNull(instance)) {
            synchronized (Singleton.class) {
                if (Objects.isNull(instance)) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

- 懒汉式写法（线程安全），也叫静态内部类写法
    - 类的加载遵循“按需加载”的原则
    - 仅仅加载外部类，或者调用外部类的其他方法，都不会触发静态内部类的加载
    - 对于静态内部类，JVM 只有在主动使用它时才会去加载它

```java
public class Singleton {
    private Singleton() {}

    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

- 饿汉式写法（线程安全）
    - JVM 保证类加载的线程安全：在 JVM 中，类的加载、链接和初始化过程本身就是线程安全的
    - 当多个线程同时首次访问 Singleton.getInstance() 时，JVM 会保证 Singleton 类只被加载和初始化一次

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

- 枚举写法，不但线程安全还能防⽌反序列化重新创建新的对象

```java
public enum Singleton {
    INSTANCE;
    // 根据需要定义其它方法
}
```

#### 迭代器模式

迭代器（Iterator）模式，提供⼀种⽅法访问⼀个容器（container）对象中各
个元素，⽽⼜不需暴露该对象的内部细节

以ArrayList为例，其继承信息如下图

![Iterator](image/Iterator.png)

- ArrayList定义了一个内部类Itr，并实现了Iterator接口的方法

- ArrayList实现了Iterable接口的iterator()方法，该方法返回一个Itr对象，从而通过Itr对象遍历元素

#### 代理模式

##### 静态代理

静态代理在编译时就将接口、实现类、代理类这些都变成了一个个实际的 class 文件

静态代理对目标对象的每个方法的增强都要手动完成
    - 非常不灵活：比如接口一旦新增加方法，目标对象和代理对象都要进行修改
    - 且麻烦：需要对每个目标类都单独写一个代理类

- 1.定义一个接口及其实现类

```java
public interface UserService {
    void save(String name);
}

public class UserServiceImpl implements UserService {
    @Override
    public void save(String name) {
        System.out.println("save user: " + name);
    }
}
```

- 2.创建代理类并同样实现该接口

```java
public class UserServiceProxy implements UserService {
    private final UserService userService;

    public UserServiceProxy(UserService userService) {
        this.userService = userService;
    }

    @Override
    public void save(String name) {
        // 调用方法前添加自己的操作
        System.out.println("before method " + "save");
        // 调用被代理对象的方法
        userService.save(name);
        // 调用方法后添加自己的操作
        System.out.println("after method " + "save");
    }
}
```

- 3.使用代理对象调用方法

```java
UserService userService = new UserServiceImpl();
UserServiceProxy proxy = new UserServiceProxy(userService);
proxy.save("handle");
```

##### 动态代理

从JVM角度来说，动态代理是在运行时动态生成类字节码，并加载到JVM中。

###### JDK原生动态代理

缺点：只能代理实现了接口的类

核心：InvocationHandler接口和Proxy类

- 1.定义一个接口及其实现类

```java
public interface UserService {
    void save(String name);
}

public class UserServiceImpl implements UserService {
    @Override
    public void save(String name) {
        System.out.println("save user: " + name);
    }
}
```

- 2.实现InvocationHandler接口并重写invoke方法，在invoke方法中我们会调用被代理对象的方法并自定义一些处理逻辑；

```java
public class UserInvocationHandler implements InvocationHandler {
    // 被代理对象
    private final Object target;

    public UserInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 调用方法前添加自己的操作
        System.out.println("before method " + method.getName());
        // 调用被代理对象的方法
        Object result = method.invoke(target, args);
        // 调用方法后添加自己的操作
        System.out.println("after method " + method.getName());
        return result;
    }
}
```

- 3.通过`Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)`方法创建代理对象

```java
// 被代理对象
UserService userService = new UserServiceImpl();
// 代理对象
UserService proxyInstance = (UserService) Proxy.newProxyInstance(
    userService.getClass().getClassLoader(),
    userService.getClass().getInterfaces(), 
    new UserInvocationHandler(userService)
);
// 代理对象调用方法
proxyInstance.save("Tom");
```

###### CGLIB动态代理

Maven依赖

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>${cglib.version}</version>
</dependency>
```

CGLIB是一个基于ASM的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成

缺点：CGLIB通过继承方式实现代理，因此不能代理终结类和终结方法

核心：MethodInterceptor接口和Enhancer类

- 1.定义一个类

```java
public class UserServiceImpl {
    public void save(String name) {
        System.out.println("save user: " + name);
    }
}
```

- 2.实现MethodInterceptor接口并重写intercept方法，intercept用于增强被代理类的方法

```java
public class UserMethodInterceptor implements MethodInterceptor {
    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        // 调用方法前添加自己的操作
        System.out.println("before method " + method.getName());
        // 调用被代理对象的方法
        Object result = proxy.invokeSuper(obj, args);
        // 调用方法后添加自己的操作
        System.out.println("after method " + method.getName());
        return result;
    }
}
```

- 3.通过Enhancer类的create()创建代理类

```java
// 创建被代理对象
UserServiceImpl userService = new UserServiceImpl();
  
Enhancer enhancer = new Enhancer();
enhancer.setClassLoader(userService.getClass().getClassLoader());
enhancer.setSuperclass(userService.getClass());
enhancer.setCallback(new UserMethodInterceptor());
// 创建代理对象
UserServiceImpl proxyInstance = (UserServiceImpl) enhancer.create();

// 代理对象调用方法
proxyInstance.save("Tom");
```

- 4.Java 17及以上版本，运行需要添加如下jvm参数

```jvm
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/sun.net.util=ALL-UNNAMED
```

#### 观察者模式

- 对象间⼀对多的依赖关系
    - 一：被观察者，subject
    - 多：观察者，observer
- 所有观察者实现相同的接口，因此都有一个同名方法，该方法用来更新观察者的状态（通知）
- 被观察者内部保存观察者对象
- 当被观察者的状态发⽣改变时，遍历内部保存的观察者对象，执行同名方法（观察者得到通知并被⾃动更新）

```java
// 观察者接口
public interface Observer {
    void update();
}

// A观察者
public class AObserverImpl implements Observer {
    @Override
    public void update() {
        System.out.println("A观察者被通知更新");
    }
}

// B观察者
public class BObserverImpl implements Observer {
    @Override
    public void update() {
        System.out.println("B观察者被通知更新");
    }
}

// 被观察者
public class Subject {
    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer observer) {
        observers.add(observer);
    }
    public void advise() {
        for (Observer item : observers) {
            item.update();
        }
    }
}

// 测试
public void test() {
    AObserverImpl aObserver = new AObserverImpl();
    BObserverImpl bObserver = new BObserverImpl();
    Subject subject = new Subject();
    subject.addObserver(aObserver);
    subject.addObserver(bObserver);
    subject.advise();
}
```

#### 装饰器模式

装饰器模式（Decorator Pattern）的核心思想是：在不修改原有类代码的情况下，动态地给一个对象添加一些额外的职责。它比生成子类更为灵活

当一个类的功能需要频繁变动，或者需要以各种方式组合功能时，装饰器模式就该上场了

```java
// 基类
public class Food {
    private String foodName;

    public Food() {

    }
    public Food(String foodName) {
        this.foodName = foodName;
    }

    public String make() {
        return foodName;
    }
}

// 装饰器1
public class Bread extends Food{
    private Food basicFood;

    public Bread(Food basicFood) {
        this.basicFood = basicFood;
    }

    @Override
    public String make() {
        return basicFood.make() + "+面包";
    }
}

// 装饰器2
public class Vegetable extends Food{
    private Food basicFood;

    public Vegetable(Food basicFood) {
        this.basicFood = basicFood;
    }

    @Override
    public String make() {
        return basicFood.make() + "+蔬菜";
    }
}

public void test() {
    Food food = new Bread(new Vegetable(new Food("炸鸡")));
    // 先调用最外层Bread的make方法：return basicFood.make() + "+面包"
    // Bread的basicFood这里是Vegetable，调用Vegetable的make方法：basicFood.make() + "+蔬菜"
    // Vegetable的basicFood这里是Food，调用Food的make方法：返回foodName，即"炸鸡"
    // 然后返回炸鸡+蔬菜
    // 最后返回炸鸡+蔬菜+面包
    System.out.println(food.make());
}
```

#### 责任链模式

按照链的顺序执⾏⼀个个处理⽅法，链上的每⼀个任务都持有它后⾯那个任务的对象引⽤

以⽅便⾃⼰这段执⾏完成之后，调⽤其后⾯的处理逻辑

```java
public interface Filter {
    void filtering();
}

public abstract class AbstractFilter implements Filter{
    private Filter filter;

    public AbstractFilter(Filter filter) {
        this.filter = filter;
    }

    @Override
    public void filtering() {
        System.out.println("invoke " + this.getClass().getSimpleName());
        if (Objects.nonNull(filter)) {
            filter.filtering();
        }
    }
}

public class XFilter extends AbstractFilter{
    public XFilter(Filter filter) {
        super(filter);
    }
}

public class YFilter extends AbstractFilter{
    public YFilter(Filter filter) {
        super(filter);
    }
}

public class ZFilter extends AbstractFilter{
    public ZFilter(Filter filter) {
        super(filter);
    }
}

public class FilterChain {
    public void runChain() {
        ZFilter zFilter = new ZFilter(null);
        YFilter yFilter = new YFilter(zFilter);
        XFilter xFilter = new XFilter(yFilter);
        xFilter.filtering();
    }
}

public class ResponsibilityChainTest {
    @Test
    public void test() {
        new FilterChain().runChain();
    }
}
```

## 项目管理工具

### Maven

#### 安装Maven

- 1.添加环境变量：MAVEN_HOME=`maven根目录`

- 2.环境变量Path追加：`%MAVEN_HOME%\bin`

- 3.修改`MAVEN_HOME\conf\settings.xml` 配置文件
  
```xml
<!-- 1.设置本地仓库路径 -->
<localRepository>...\repository</localRepository>
  
<mirrors>
    <!-- 2.设置下载jar包的镜像地址 -->
    <mirror>
        <id>alimaven</id>
        <mirrorOf>*</mirrorOf>
        <name>aliyun maven</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
    <mirror>
      <id>maven-default-http-blocker</id>
      <!-- 匹配所有外部的、且使用 HTTP 协议的仓库 -->
      <mirrorOf>external:http:*</mirrorOf>
      <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
      <!-- http://0.0.0.0/ 在这里没有任何实际的网络请求意义，它只是一个占位符 -->
      <!-- 在 Maven 的 <mirror> 配置中，<url> 是一个必填标签。如果不填，Maven 在解析 XML 配置文件时就会报错。-->
      <!-- 但是，这个特定的拦截器配置（maven-default-http-blocker）根本不需要去下载任何东西，它的唯一目的就是拦截。 -->
      <url>http://0.0.0.0/</url>
      <!-- 表示对于匹配到的仓库，Maven 会直接阻断请求，不会去下载依赖 -->
      <blocked>true</blocked>
    </mirror>
</mirrors>  
  
<profiles>
    <!-- 3.设置默认jdk -->
    <profile>
        <id>jdk-21</id>
        <activation>
            <jdk>21</jdk>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <maven.compiler.source>21</maven.compiler.source>
            <maven.compiler.target>21</maven.compiler.target>
            <encoding>UTF-8</encoding>
        </properties>
    </profile>
</profiles>
```

#### Idea配置Maven

- File->Settings->Build,Execution,Deployment->Build Tools->Maven
    - Maven home path，指定Maven家目录
    - User settings file，勾上Override，选择Maven的settings.xml配置文件路径
    - Use settings from .mvn/maven.config，取消勾选
    - 点击Runner子选项，勾上Skip Tests
    - Importing，Automatically download，都不要勾选，如果需要看源码可以逐个手动下载

#### scope

|依赖范围|编译有效|测试有效|运行有效|打包有效|例子|
|:-|:-|:-|:-|:-|:-|
|compile|true|true|true|true|spring-core|
|test|false|true|false|false|junit|
|provided|true|true|false|false|lombok|
|runtime|false|true|true|true|数据库驱动|
|system|true|true|false|false|本地maven仓库之外的类库，基本用不到|

#### 依赖的传递性

- maven依赖默认是有传递性的

- maven依赖的optional属性说明
    -Flagging the dependency as optional prevents it from being transitively applied to other modules that use your project

```xml
<dependencies>
    <dependency>
        <groupId>some-groupId</groupId>
        <artifactId>some-artifactId</artifactId>
        <!-- 防止传递依赖 -->
        <optional>true</optional>
    </dependency>
</dependencies>
```

#### Maven的profile

profile用来为不同环境（dev/test/prod）或不同构建场景准备不同的配置集，在构建时按需启用

在settings.xml和pom.xml中都可以定义profile，定义后在ide的maven面板中看到

- 在settings.xml中定义profile

```xml
<profile>
    <id>jdk-21</id>
    <activation>
        <jdk>21</jdk>
        <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <encoding>UTF-8</encoding>
    </properties>
</profile>
```

- 在pom.xml文件中定义配置文件

```xml
<profiles>
    <profile>
        <id>development</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <environment>dev</environment>
        </properties>
    </profile>
    <profile>
        <id>production</id>
        <properties>
            <environment>prod</environment>
        </properties>
    </profile>
</profiles>
```

- 使用命令行参数激活构建设置

```sh
mvn clean install -P production
```

#### maven生命周期

maven的生命周期有三类：clean、default、site，它们相互独立

每类生命周期都包含了多个阶段，并且这些阶段是有序的，也就是说，后面的阶段依赖于前面的阶段。当执行某个阶段的时候，会先执行它前面的阶段

```sh
# 执行 Maven 生命周期的命令格式
mvn 阶段 [阶段2...阶段n]
mvn clean install
```

##### clean生命周期

共包含3个阶段：

- pre-clean
- clean
- post-clean

##### default生命周期（也成为构建生命周期）

default生命周期是在没有任何关联插件的情况下定义的，是Maven的主要生命周期，用于构建应用程序，共包含23个阶段，下面列出常见的几个阶段：

- validate，验证项目是否正确，并且所有必要的信息可用于完成构建过程
- compile，编译项目的源代码
- test，使用合适的单元测试框架（Junit 就是其中之一）运行测试
- package，获取已编译的代码并将其打包成可分发的格式，例如 JAR、WAR 或 EAR 文件
- verify，运行任何检查以验证打的包是否有效并符合质量标准
- install，将package安装到maven的本地仓库
- deploy，将package上传到远程仓库中

##### site生命周期

site 生命周期的目的是建立和发布项目站点，共包含 4 个阶段：

- pre-site，执行一些需要在生成站点文档之前完成的工作
- site，生成项目文档和站点信息
- post-site，执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
- site-deploy，将生成的站点文档部署到特定的服务器上

#### maven插件

- Maven本质上是⼀个插件框架，它的核⼼并不执⾏任何具体的构建任务，所有这些任务都交给插件来完成

- 可以将Maven插件理解为一组任务的集合

- 每个插件会有⼀个或者多个⽬标，每个任务对应了⼀个插件⽬标（goal）

- 用户可以通过命令行直接运行指定插件的目标，也可以将插件目标挂载到构建生命周期，随着生命周期运行

##### 调⽤Maven插件⽬标

- 将插件⽬标与⽣命周期阶段（lifecycle phase）绑定

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

- 直接在命令⾏指定要执⾏的插件⽬标

```sh
mvn spring-boot:repackage
```

#### 创建Maven继承/聚合工程

继承：在父工程中统一声明版本信息，是一种依赖管理的版本简化

聚合：通过父工程统一构建子工程，它会优化构建顺序，是一种构建管理的简化

- 创建一个maven项目作为父工程，只留下pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 1.填写父工程坐标 -->
    <groupId>com.handle</groupId>
    <artifactId>application</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- 2.定义打包方式为pom -->
    <packaging>pom</packaging>

    <!-- 3.填写子工程名称（可选，如果子工程不想聚合给父工程统一管理则可以不写） -->
    <modules>
        <module>子工程名称1</module>
        <module>子工程名称n</module>
    </modules>

    <!-- 4.依赖声明，可以被子工程继承 -->
    <dependencyManagement>
        <dependencies>
            ...
        </dependencies>
    </dependencyManagement>

     <!-- 5.插件声明，可以被子工程继承 -->
    <pluginManagement>
        <plugins>
            ...
        </plugins>
    </pluginManagement>
</project>
```

- 创建一个maven项目作为子工程，修改pom文件，继承父工程

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 1.填写父工程坐标以继承父工程 -->
    <parent>
        <groupId>com.handle</groupId>
        <artifactId>application</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <!-- 2.填写子工程名称 -->
    <artifactId>子工程名称</artifactId>

    <!-- 3.填写依赖，继承自父工程的依赖不用写version和scope -->
    <dependencies>
        <dependency>
            <groupId>...</groupId>
            <artifactId>...</artifactId>
        </dependency>
    </dependencies>
</project>
```

##### BOM（Bill of Materials，物料清单）

- 有一个项目P，它只有一个`pom.xml`文件，并且里面声明了`<packaging>pom</packaging>`
    - 其他项目不是通过 `<parent>`继承它，而是通过依赖声明引入它，那么这个项目P就扮演了BOM的角色
        - 它的核心作用是统一版本管理，但是不能管理插件版本和配置
        - 必须在 `<dependencyManagement>`标签内声明，并且必须指定`<type>pom</type>` 和 `<scope>import</scope>`
        - 在`<pluginManagement>`里面的插件声明是无效的
        - 这种方式告诉 Maven 将该 POM 中的依赖管理规则导入到当前项目中
    - 其它项目通过`<parent>`继承它，那么这个项目P就扮演了普通父POM的角色
        - 它的核心作用除了版本管理，还可以用于传递插件配置、项目属性、全局变量等，是一种强绑定关系的继承
        - 通过`<parent>`引入父POM，一个Maven项目中只能有一个直接父POM
            - 可以定义一个父POM，继承spring-boot-start-parent
            - 然后让其它项目继承这个父POM
        - 在`<pluginManagement>`里面的插件声明是有效的，并且主要就是使用父POM这种机制进行插件版本和配置的管理

#### 打包时跳过测试

- 1.命令方式

```sh
mvn clean package -Dmaven.test.skip=true
```

- 2.插件方式

```xml
<build>
    <plugins>
        <!-- maven 打包时跳过测试 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>${maven.surefire.plugin.version}</version>
            <configuration>
                <skip>true</skip>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 安装远程仓库（私服）

- 解压安装包 `nexus-3.33.0-01-win64.zip`

- 添加环境变量 `..\nexus-3.33.0-01-win64\nexus-3.33.0-01\bin`

- 配置地址、端口、上下文路径 `..\nexus-3.33.0-01-win64\nexus-3.33.0-01\etc\nexus-default.properties`

- 以系统管理员身份打开`cmd`

- 安装 `nexus /install`

- 启动

- 后台启动 ，看不到实时日志`nexus /start`

- 实时启动，可以看到日志，不能关闭cmd窗口 `nexus /run`

- 输入 `http://localhost:8001` 访问，如果默认端口已经改了以新的端口为准

- 查看初始密码 `..\nexus-3.33.0-01-win64\sonatype-work\nexus3\admin.password`

- 输入账号 `admin` 及上面文档的密码登录

- 修改密码

- 设置jar包保存路径
  
    ![Blob Stores](/images/BlobStores.png "Blob Stores")

- 设置`maven`配置文档`..\apache-maven-3.6.3\conf\settings.xml` server的id和repository的id要一致

- 设置maven登录私服的账号密码
  
  ```xml
  <server>
      <id>releases</id>
      <username>admin</username>
      <password>Nexus**..</password>
  </server>
  <server>
      <id>snapshots</id>
      <username>admin</username>
      <password>Nexus**..</password>
  </server>
  <server>
      <id>nexus</id>
      <username>admin</username>
      <password>Nexus**..</password>
  </server>
  ```

- 设置私服镜像，镜像id要和server标签的id一致
  
  ```xml
  <mirrors>
      <mirror>
          <id>nexus</id>
          <url>http://localhost:8001/repository/maven-public/</url>
          <mirrorOf>*</mirrorOf>
      </mirror>
  </mirrors>  
  ```

- 设置pom.xml上传jar包到私服的配置，repository的id要和server的id一致

```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <url>
            http://localhost:8001/repository/maven-releases/
        </url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <url>
            http://localhost:8001/repository/maven-snapshots/
        </url>
    </snapshotRepository>
</distributionManagement>
```

- 设置`..\apache-maven-3.6.3\conf\settings.xml` 从私服下载jar包的配置

```xml
<profiles>
    <profile>
        <id>nexus</id>
        <repositories>
            <repository>
                <id>central</id>
                <url>http://localhost:8001/repository/maven-public/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>central</id>
                <name>nexus repositories</name>
                <url>http://localhost:8001/repository/maven-public/</url>
            </pluginRepository>
        </pluginRepositories>
    </profile>
</profiles>

<!-- 激活 profile -->
<activeProfiles>
    <!-- nexus 为上面定义的 profile id -->
    <activeProfile>nexus</activeProfile>
</activeProfiles>
```

- 卸载 `nexus /uninstall`

- maven 安装本地 jar 到 本地 repository

```cmd
mvn install:install -file -Dfile=d:\sqljdbc-4.1.5605.jar -Dpackaging=jar -DgroupId=com.microsoft.sqlserver -DartifactId=sqljdbc -Dversion=4.1.5605
```

#### pom.xml 文档设置

- 设置基本信息
  
  ```xml
  <properties>
      <maven.compiler.source>21</maven.compiler.source>
      <maven.compiler.target>21</maven.compiler.target>
      <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  </properties>
  ```

- 设置打包时生成source.jar
  
  ```xml
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-source-plugin</artifactId>
      <version>3.2.1</version>
      <!-- 指定插件的 goal -->
      <executions>
          <execution>
              <id>attach-sources</id>
              <goals>
                  <goal>jar</goal>
              </goals>
              <!-- 将 goal 和 maven 生命周期的 package 阶段绑定 -->
              <phase>package</phase>
          </execution>
      </executions>
  </plugin>
  ```

- 设置打包时生成javadoc.jar
  
  ```xml
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-javadoc-plugin</artifactId>
      <version>3.3.0</version>
      <!-- 指定插件的 goal -->
      <executions>
          <execution>
              <id>attach-javadocs</id>
              <goals>
                  <goal>jar</goal>
              </goals>
              <!-- 将 goal 和 maven 生命周期的 package 阶段绑定 -->
              <phase>package</phase>
              <!-- configuration 标签用来设置插件的参数 -->
              <configuration>
                  <!-- Specifies the encoding name of the source files. -->
                  <!-- If not specificed, the encoding value will be the value of the file.encoding system property. -->
                  <encoding>UTF-8</encoding>
  
                  <!-- Specifies the encoding of the generated HTML files-->
                  <!--  If not specificed, the docencoding value will be UTF-8. -->
                  <docencoding>UTF-8</docencoding>
                  <!-- Specifies the HTML character set for this document. -->
                  <!-- If not specificed, the charset value will be the value of the docencoding parameter. -->
                  <charset>UTF-8</charset>
  
                  <tags>
                      <!-- define @date tag -->
                      <tag>
                          <name>date</name>
                          <!-- define @date tag for all places -->
                          <placement>a</placement>
                          <head>created time:</head>
                      </tag>
                  </tags>
              </configuration>
          </execution>
      </executions>
  </plugin>
  ```

#### 依赖冲突解决

- 先定义先使用
- 路径最近原则（直接声明使用）
- 手动排除依赖

### maven 选项参数

```sh
# -T，指定线程数，有两种写法
# 第一种写法，指定一个整数，即线程数，如：4
# 第二种写法，指定一个N（可以是整数或浮点数），然后加后缀C，表示线程数=N * CPU核心数，如：2C/2.5C
mvn -T 1C [其它选项参数]
```

#### maven 常见问题及解决方案

##### 1. maven 控制台日志乱码

- 查看 maven 默认编码：`mvn -v`

- 设置maven默认编码：`-Dfile.encoding=GBK`
  
    ![设置maven默认编码](/images/设置maven默认编码.png "设置maven默认编码")

### Maven Daemon

- 官网：<https://github.com/apache/maven-mvnd>

- 可以看作是maven的增强版，内嵌了maven，只需将maven的命令mvn改成mvnd即可

#### 安装Maven Daemon

- 1.下载zip压缩包

- 2.添加环境变量：MVND_HOME=`Maven Daemon根目录`

- 3.环境变量Path追加：`%MVND_HOME%\bin`

- 4.修改`MVND_HOME\conf\mvnd.properties` 配置文件，设置maven的settings.xml文件位置为`MVND_HOME\mvn\conf\settings.xml`

```sh
# 相当于maven的-T，如果不指定，默认为系统的CPU线程数-1
# 个人觉得，在开发的时候至少预留三个线程比较好，一个给系统，一个给ide，一个给正在启动的java程序
# 改完后，启动java程序就会生效，如打印：Using the SmartBuilder implementation with a thread count of 3
mvnd.threads = 0.6C

# 指定maven的settings.xml文件位置
maven.settings=/path/to/settings.xml
```

- 5.修改`MVND_HOME\mvn\conf\settings.xml` 配置文件

- 6.测试`mvnd -v`

#### Idea配置Maven Daemon

- IntelliJ IDEA 2024.3 (Community Edition)还没支持Maven Daemon，通过Maven Helper插件来使用

- 点击`File->Settings->Other Settings->Maven Helper`
    - 勾上`Use a Terminal to run goals, with a custom mvn command`
    - 并指定命令为`mvnd`，然后关闭再打开Idea，如果不行就直接指定命令的全路径为`MVND_HOME\bin\mvnd.cmd`

- 使用：右键->Run Maven选择对应指令执行即可

#### mvnd常用命令

```sh
# 停止mvnd daemon线程
mvnd --stop

mvnd --status
```

### Gradle

- 官网：<https://gradle.org/>

#### 安装Gradle

- 参考Spring Boot支持的Gradle版本

- 参考Idea支持的Gradle版本：`idea_home\plugins\gradle\lib`

- 参考Gradle支持的jdk版本，需要先安装jdk并配置好

- 下载完整版（包含了文档和源码）

- 修改maven下载源
    - 在`GRADLE_HOME/init.d`目录下创建init.gradle文件，内容如下

- gradle-8.x版本这样写

```groovy
allprojects {
    // 项目依赖的下载地址
    repositories {
        mavenLocal()
        maven {
            name "aliyun";
            url "https://maven.aliyun.com/repository/public"
        }
        mavenCentral()
    }
    // build.gradle构建脚本使用的依赖的下载地址，一般是各种插件的下载地址
    buildscript {
        repositories {
            mavenLocal()
            maven {
                name "aliyun";
                url "https://maven.aliyun.com/repository/public"
            }
            mavenCentral()
        }
    }
}
```

- gradle-9.x版本这样写

```groovy
settingsEvaluated { settings ->
    settings.dependencyResolutionManagement {
        // Android项目会将这里的仓库整合到settings.gradle的仓库配置
        // Java项目还是相当于全局配置
        repositoriesMode.set(RepositoriesMode.PREFER_SETTINGS)
        // 项目依赖的下载地址
        repositories {
            mavenLocal()
            maven {
                name "aliyun";
                url "https://maven.aliyun.com/repository/public"
            }
            google()
            mavenCentral()
        }
    }
}

allprojects {
    // build.gradle构建脚本使用的依赖的下载地址，一般是各种插件的下载地址
    buildscript {
        repositories {
            mavenLocal()
            maven {
                name "aliyun";
                url "https://maven.aliyun.com/repository/public"
            }
            google()
            mavenCentral()
        }
    }
}
```

- 配置环境变量
    - `GRADLE_HOME`
    - `GRADLE_USER_HOME`
        - gradle的本地仓库路径和gradle wrapper的缓存路径、缓存的路径
        - 下载的依赖会存放在`GRADLE_USER_HOME/caches/modules-2/files-2.1`目录下
        - 除了依赖目录还有别的目录，建议不要指定为maven的repository路径，避免目录混乱
    - `M2_HOME`，maven家目录，gradle默认会查找`~/.m2/repository`作为maven本地仓库路径，修改了maven本地仓库路径的话才需要定义

- 配置Path：`GRADLE_HOME\bin`

- 测试：`gradle -v`

#### IDEA配置Gradle

- Settings->Build,Execution,Deployment->Gradle
    - Gradle user home，指定一个路径作为gradle的本地仓，gradle和maven的结构不一样，不要设置为maven本地仓库的路径
    - Build and run using，设置为IntelliJ IDEA，IDEA会对纯Java项目做优化，启动更快，如果构建有问题再改回默认Gradle
    - Run test using，设置为IntelliJ IDEA，不然用默认Gradle的话初始化测试都要等好几秒了，浪费时间
    - Distribution，设置为Local installation，并选择本地安装的gradle家目录
    - Gradle JVM，设置为本地安装的JDK即可

#### Gradle项目目录结构

- Gradle项目默认目录结构和Maven项目的一致，都是基于约定大于配置

- gradlew、gradlew.bat执行的是gradle/wrapper里面指定版本的gradle指令，不是本地安装的gradle的指令

- 如果要执行的是本地安装的gardle的指令，gradle/wrapper，gradlew、gradlew.bat，这三个玩意是用不到的，是可以删了的

- project-root
    - build，存放编译后的字节码，打成的包、测试报告等，类似maven的target目录
    - gradle，封装包装器文件夹
        - wrapper
            - gradle-wrapper.jar
            - gradle-wrapper.properties
    - src
        - main
            - java
            - resources
            - webapp，war工程才有这个目录
                - WEB-INF
                    - web.xml
                - index.jsp
        - test
            - java
            - resources
    - gradlew，包装器在非Windows系统的启动脚本
    - gradlew.bat，包装器在Windows系统的启动脚本
    - build.gradle，构建脚本，类似maven的pom.xml
    - settings.gradle，配置文件，定义项目和子项目名称信息，一个项目只能有一个此文件（子项目是没有这个文件的）

#### Gradle依赖

##### Gradle依赖类型

- implementation不支持依赖传递，api支持依赖传递
- 除了模块间依赖引用其它情况优先使用implementation

|依赖类型|说明|用例|
|:-|:-|:-|
|compileOnly|for dependencies that are necessary to compile your production code but shouldn’t be part of the runtime classpath，Java插件提供，只在编译期需要|Lombok|
|runtimeOnly (supersedes runtime)|only used at runtime, not for compilation，Java插件提供，只在运行期需要|数据库驱动|
|implementation (supersedes compile)|used for compilation and runtime，Java插件提供，编译、运行都需要|spring-context|
|testCompileOnly|same as compileOnly except it’s for the tests，Java插件提供，只在测试类编译期需要||
|testRuntimeOnly|test equivalent of runtimeOnly，Java插件提供，只在测试类运行期需要||
|testImplementation|test equivalent of implementation，Java插件提供，测试类编译、运行都需要||
|providedCompile|war插件提供支持，编译、测试需要，运行时由容器提供支持，无需打入war包中|servlet-api、jsp-api|
|api|Java Library Plugin offers two additional configurations,for dependencies that are required for compiling both the module and any modules that depend on it，java-library插件提供，支持依赖传递，编译和运行期需要||
|compileOnlyApi|Java Library Plugin offers two additional configurations,for dependencies that are required for compiling both the module and any modules that depend on it，java-library插件提供，被依赖模块和依赖模块，编译期需要，运行期不需要||

##### Gradle依赖写法

- 直接依赖

```groovy
dependencies {
    // 简写
    implementation 'org.springframework.boot:spring-boot:3.3.5'

    // 遍历所有仓库用最新版本，属于动态版本声明，不建议使用
    implementation 'org.springframework.boot:spring-boot:+'

    // 全写
    implementation group: 'org.springframework.boot', name: 'spring-boot', version: '3.3.5'
}
```

- 项目依赖

```groovy
dependencies {
    // 这个项目要在setting.gradle中声明才可以
    implementation project(':subproject01')
}
```

- 本地依赖

```groovy
dependencies {
    // 假设项目根目录下有个lib文件夹存放了各种jar包
    implementation files('lib/xxx1.jar', 'lib/xxx2.jar')
    implementation fileTree('dir':'lib', includes:['*.jar'], excludes:['*.war'])
}
```

```groovy
// 实际上是dependencies({})的另一种写法
dependencies {
    
    // Use JUnit Jupiter for testing.
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.3'

    implementation 'org.springframework.boot:spring-boot:3.3.5'
}
```

#### Gradle依赖冲突解决

- 默认采用最新版本（官方默认的方式就够了）

- exclude排除依赖

```groovy
dependencies {
    implementation('org.springframework.boot:spring-boot:3.3.5') {
        // 支持如下排除写法
        exclude group: 'org.slf4j'
        exclude module: 'slf4j-api'
        exclude group: 'org.slf4j',module: 'slf4j-api'
    }
    // 排除后手动引入
    implementation 'org.slf4j:slf4j-api:1.4.0'
}
```

- 不允许依赖传递，不建议使用

```groovy
dependencies {
    implementation('org.springframework.boot:spring-boot:3.3.5') {
        transitive(false)
    }
}
```

- 强制使用某个版本，官方推荐

```groovy
dependencies {
    // 加两个英文感叹号
    implementation 'org.slf4j:slf4j-api:1.4.0!!'

    // 或者这种写法
    implementation('org.slf4j:slf4j-api:1.4.0') {
        version {
            strictly("1.4.0")
        }
    }
}
```

- 配置遇到依赖冲突的时候，构建失败

```groovy
configuration.all() {
    Configuration configuration -> configuration.resolutionStrategy.failOnVersionConflict()
}
```

#### Gradle插件

##### 脚本插件

- 本质是一个脚本文件，它是模块化的基础，可以通过它来做依赖的版本管控

- 自定义一个xxx.gradle文件，然后在build.gradle引入进来

```groovy
apply from: "xxx.gradle"
```

##### 内部插件

- 对象插件，就是实现了org.gradle.api.Plugin接口的插件，每个Java Gradle插件都有一个plugin id

```groovy
// dsl写法
plugins {
    id 'java'
}

// apply的map具名参数写法，value可以是插件id、插件的全限定类名、插件的类名（插件所在类的包已经被引入的情况）
apply plugin:'java'

// apply的闭包写法
apply {
    plugin 'java'
}
```

##### 第三方插件

```groovy
// 这种写法必须保证插件已经被托管了，否则要用传统方式
plugins {
    id 'org.springframe.boot' version '2.4.1'
}

// 传统方式，要求这个buildscript要在文件开头
buildscript {
    ext {
        // 定义版本
        v="3.2.0"
    }
    repositories {
        // 插件从哪里下载
    }
    dependencies {
        // 引入插件插件gav
        classpath("g:a:${v}")
    }
}

apply plugin: "io.spring.dependency-management"
```

##### 用户自定义插件

- 创建buildSrc目录，它是Gradle默认的插件目录
    - 新建gradle模块，模块名为buildSrc
    - 只保留里面的build文件夹、src目录和build.gradle文件
    - 项目根目录settings.gradle中对buildSrc的声明删了

```groovy
// 实现接口
class MyPlugin implements Plugin<Project> {
    void apply(Project project) {
        // todo
    }
}
// 应用插件
apply plugin: MyPlugin
```

#### Gradle常用指令

- gradle指令要在含有build.gradle的目录执行

|指令|说明|
|:-|:-|
|gradle clean|删除build目录|
|gradle classes|编译业务代码和配置文件|
|gradle test|编译测试代码，生成测试报告|
|gradle build|构建项目，默认会执行gradle classes、gradle test，以及打包|
|gradle build -x test|跳过测试构建项目|

#### Gradle单项目

##### 创建Gradle项目

- Idea新建项目选择Gradle进行创建

- 命令行创建

```sh
gradle init
```

##### settings.gradle

- Settings file to define build name and subprojects

```groovy
rootProject.name = 'hello-world'
```

##### build.gradle

- Build script of the project

```groovy
plugins {
    id 'java'
    //  This plugin provides useful defaults and Gradle tasks
    id 'org.springframework.boot' version '3.2.0'
}

// 提供版本管理支持，填写依赖的时候可以省略版本号
apply plugin: 'io.spring.dependency-management'

// 项目名称和版本，项目名在settings.gradle中定义了
group = 'com.handle'
version = '1.0-SNAPSHOT'

// 依赖
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

#### Gradle聚合项目

- 新建gradle项目，删掉src目录

- build.gradle

```groovy
plugins {
    //  This plugin provides useful defaults and Gradle tasks
    // 包含了io.spring.dependency-management，其作用是提供版本管理支持，填写依赖的时候可以省略版本号
    // 在根项目中只声明不应用，在子项目再应用，这里起到的是插件版本统一管理的作用
    id 'org.springframework.boot' version '3.2.4' apply false
}

// 提供版本管理支持，填写依赖的时候可以省略版本号
apply plugin: 'io.spring.dependency-management'

subprojects {
    apply {
        // 如果不是核心插件，父项目的build.gradle声明过的插件才能应用到子项目
        plugin('java')
        plugin('org.springframework.boot')
        plugin('io.spring.dependency-management')
    }

    group = 'com.handle'
    version = '1.0-SNAPSHOT'

    ext {
        // 在此处指定依赖版本
        cglibVersion = '3.3.0'
    }

    // 子项目通用依赖都在这里声明
    dependencies {
        // 使用指定的依赖版本
        implementation "cglib:cglib:${cglibVersion}"

        compileOnly 'org.projectlombok:lombok'

        // Junit依赖
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
        testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }

    tasks.named('test') {
        // Use JUnit Platform for unit tests.
        useJUnitPlatform()
    }
}
```

- 子项目测试类写法

```java
// 需要指定主启动类，否则会报错
@SpringBootTest(classes = Application.class)
public class ApplicationTest {
    @Test
    public void test() {
        System.out.println("test success!!!!!!");
    }
}
```

#### 发布Maven BOM

```groovy
plugins {
    // BOM插件
    id 'java-platform'
    // 发布插件
    id 'maven-publish'
}

group = 'com.handle'
version = '1.0-SNAPSHOT'

dependencies {
    constraints {
        // 这些依赖会生成到BOM的dependencyManagement里面
        api 'com.alibaba.cloud:spring-cloud-alibaba-dependencies:2023.0.0.0-RC1'
        api 'org.springframework.cloud:spring-cloud-dependencies:2023.0.0'
        api 'org.springframework.boot:spring-boot-dependencies:3.2.0'
    }
}

publishing {
    publications {
        // 自定义发布名称为bom
        bom(MavenPublication) {
            // 发布成BOM
            from components.javaPlatform
        }
    }
}
```

#### 使用Maven BOM

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0' apply false
}

apply plugin: 'io.spring.dependency-management'

group = 'com.handle'
version = '1.0-SNAPSHOT'

dependencyManagement {
    imports {
        // 引入自定义BOM
        mavenBom 'com.handle:java-one-dependencies:1.0-SNAPSHOT'
    }
}

dependencies {
    // 不用写版本号
    implementation 'org.projectlombok:lombok'
}
```

#### 打包部署

##### 非模块打包

###### 生成最小的jre

```groovy
// 非模块打包，不能有module-info.java文件
plugins {
    id("org.beryx.runtime") version "1.13.1"
}

application {
    mainClass = 'com.handle.package2exe.demo.Application'
}
runtime {
    // --strip-debug：去掉所有模块中的调试信息，从而减小生成的JRE的大小
    // --compress：压缩生成的JRE。2表示最高级别的压缩，但这也会增加构建时间
    // --no-header-files：不包含头文件。减少不必要的文件，进一步减小JRE的大小
    // --no-man-pages：不包含手册页。进一步减小JRE的大小
    options = ['--strip-debug', '--compress', 'zip-6', '--no-header-files', '--no-man-pages']
    // 依赖的模块，建议先创建module-info.java文件确认依赖哪些模块，然后再复制到这里来，最后删掉module-info.java文件
    modules = ['javafx.controls']
    // 指定生成的jre位置
    distDir = file("${projectDir}/build/${project.name}/jre")
}
```

###### 打包成exe

- 1.build.gradle

```groovy
plugins {
    // 打包插件
    id "edu.sc.seis.launch4j" version "3.0.6"
}

launch4j {
    // 唯一需要指定的是mainClassName
    mainClassName = 'com.handle.package2exe.demo.Application'
    icon = "${projectDir}/icon/logo.ico"
    // 输出路径
    outputDir = "${projectDir}/build/${project.name}"
    // 生成的exe文件相同目录下，放置jre（文件夹），不配置Java运行环境也可以运行
    bundledJrePath = "./jre"
}
```

- 2.执行createExe任务

- 3.build/launch4j目录下就是打包生成的依赖和exe文件

###### jre和exe整合

```groovy
plugins {
    // 生成exe文件和jar依赖文件夹
    id("edu.sc.seis.launch4j") version "3.0.6"
    // 非模块打包，不能有module-info.java文件
    id("org.beryx.runtime") version "1.13.1"
}

application {
    mainClass = 'com.handle.package2exe.demo.Application'
}

launch4j {
    // 唯一需要指定的是mainClassName
    mainClassName = 'com.handle.package2exe.demo.Application'
    icon = "${projectDir}/icon/logo.ico"
    // 输出路径
    outputDir = "${projectDir}/build/${project.name}"
    // 生成的exe文件相同目录下，放置jre（文件夹），不配置Java运行环境也可以运行
    bundledJrePath = "./jre"
}

runtime {
    options = ['--strip-debug', '--compress', 'zip-6', '--no-header-files', '--no-man-pages']
    // 依赖的模块，建议先创建module-info.java文件确认依赖哪些模块，然后再复制到这里来，最后删掉module-info.java文件
    modules = ['javafx.controls']
    // 指定生成的jre位置，不知道为什么不生效，只能通过复制任务复制到输出目录了
    distDir = file("${projectDir}/build/${project.name}/jre")
}

// 复制jre文件夹到输出目录
tasks.register('copyJre', Copy) {
    from "${projectDir}/build/jre"
    into "${projectDir}/build/${project.name}/jre"
    doLast {
        def sourceDirectory = file("${projectDir}/build/jre")
        sourceDirectory.deleteDir()
    }
}

tasks.copyJre.dependsOn('jre')
tasks.createExe.dependsOn('copyJre')
```

##### 模块打包

执行jlink任务，最终会在build/image/bin目录下生成项目名对应的文件和bat文件，双击该bat文件即可运行程序

- 创建module-info.java文件

```java
// 模块的名称，它的命名规范与包一致
module com.handle.package2exe.demo {
    // 依赖的模块，按需导入
    requires javafx.controls;
    // 必须导出，否则运行报错
    exports com.handle.package2exe.demo;
}
```

- 配置buil.gradle文件

```groovy
plugins {
    // 模块打包，需要有module-info.java文件
    id("org.beryx.jlink") version "3.1.1"
}

application {
    // 指定主类和主模块
    mainClass = 'com.handle.package2exe.demo.Application'
    mainModule = 'com.handle.package2exe.demo'
}

jlink {
    // --strip-debug：去掉所有模块中的调试信息，从而减小生成的JRE的大小
    // --compress：压缩生成的JRE。2表示最高级别的压缩，但这也会增加构建时间
    // --no-header-files：不包含头文件。减少不必要的文件，进一步减小JRE的大小
    // --no-man-pages：不包含手册页。进一步减小JRE的大小
    options = ['--strip-debug', '--compress', '2', '--no-header-files', '--no-man-pages']
    mergedModule {
        // 主模块以来的模块都填写
        requires 'javafx.controls'
    }
    launcher {
        // 会在build/image/bin目录下生成项目名对应的文件和bat文件
        name = "${project.name}"
        // 双击运行bat文件后控制台窗口自动关闭
        noConsole = true
    }
}
```

#### Groovy

- 官网：<https://groovy-lang.org/>

- Groovy兼容Java语法

- 当Groovy的源文件定义了类的时候，这个类实际上实现了GroovyObject

- 当Groovy的源文件没有定义类的时候，这个源文件会被转换为扩展了Script的类
    - 这个类将作为脚本使用
    - 这个类使用源文件名作为类名
    - 源文件的内容被打包进run方法
    - 这个类会加入一个main方法使得可以外部执行该脚本

- 在Groovy的源文件中可以混合类的定义和脚本定义，此时不能定义一个和文件同名的类

- Groovy默认类、方法和字段是public修饰的

- Groovy使用def定义变量和方法，不建议使用具体的数据类型

- 无论变量、属性、方法、闭包的参数还是方法的返回值，类型都是可有可无的，都是在给变量赋值的时候才决定它们的类型

- Groovy基本类型也是对象，可以直接调用对象对应的方法

##### Groovy属性

- 属性赋值
    - 对象.属性名=属性值
    - 对象的setter方法
    - 具名构造器
- 属性读取
    - 对象.属性名
    - 对象["属性名"]
    - 对象的getter方法
- 对类的属性的操作本质还是通过getter、setter方法完成的

##### Groovy方法

- 方法声明
    - 参数类型、返回值类型可以省略
    - return关键字可以省略，默认使用方法最后一句的返回值作为方法的返回值
- 方法调用，在不导致二义性的时候，`()`可以省略

##### Groovy字符串

- 单引号，作为字符串常量使用，没有运算能力

- 双引号，可通过`${}`引用变量，有运算能力

- 三引号，模板字符串，支持字符串换行

## 单元测试

### Junit5

- 依赖

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>${junit.version}</version>
    <scope>test</scope>
</dependency>
```

#### Junit5常用注解

##### @BeforeEach/@AfterEach

可简单理解为在每个测试方法执行前/后执行

```java
@BeforeEach
public void init() {
    // todo
}
```

##### @BeforeAll/@AfterAll

可简单理解为在所有测试方法执行前/后执行，对于一个测试类只执行一次，并且必须注解在静态方法上

```java
@BeforeAll
public static void initStatic() {
    // todo
}
```

##### @ActiveProfiles

一般作用于测试类上， 用于声明生效的 Spring 配置文件

```java
// 指定在 RANDOM_PORT 上启动应用上下文，并激活 "test" profile
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test")
@Slf4j
public abstract class ApplicationTest {
    // todo
}
```

##### @WithMockUser

@WithMockUser 是 Spring Security Test 模块提供的注解，用于在测试期间模拟一个已认证的用户。

可以方便地指定用户名、密码、角色（authorities）等信息，从而测试受安全保护的端点或方法。

```java
// 指定在 RANDOM_PORT 上启动应用上下文，并激活 "test" profile
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test")
@Slf4j
public abstract class ApplicationTest {
    @Test
    // 测试数据将回滚
    @Transactional 
    // 模拟一个名为 "test-user"，拥有 TEACHER 角色和 read 权限的用户
    @WithMockUser(username = "test-user", authorities = { "ROLE_TEACHER", "read" }) 
    public void test() throws Exception {
        // ... 测试逻辑 ...
        // 这里可以调用需要 "ROLE_TEACHER" 权限的服务方法
    }
}
```

#### Junit5执行顺序

- 1.@BeforeAll
- 2.循环以下步骤
    - 2.1 @BeforeEach
    - 2.2 @Test
    - 2.3 @AfterEach
- 3.@AfterAll

### Mock

- @Resource/@Autowired为真实调用

#### @MockBean

- 如果没有制定规则，返回默认值（基本数据类型为0，对象为null）
- 如果制定了规则，就按照规则走

```java
when(someService.someMethod(parameter...)).thenReturn(someValue);
```

#### SpyBean

如果制定了规则就按照规则走，没有制定规则就按照真实调用走

## Lombok

- maven依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${lombok.version}</version>
    <scope>provided</scope>
</dependency>
```

- gradle依赖

```groovy
compileOnly 'org.projectlombok:lombok'
// 加上这个注解告诉Gradle在编译过程中使用Lombok的注解处理器，不然编译会报错
annotationProcessor 'org.projectlombok:lombok'
```

- 常用注解

```java
@Getter
@Setter
@ToString
@NoArgsConstructor(access = AccessLevel.PRIVATE)
@Slf4j
public class AccountPo {}
```

## EasyExcel

```java
// 同步读，headRowNumber(0)：标题行和数据行都读到列表中
List<Map<Integer, String>> listMap = EasyExcel.read(inputStream).sheet().headRowNumber(0).doReadSync();
```

## Mybatis

### 完全配置类整合

```java
@Configuration
public class MybatisConfiguration {
    // SqlSessionFactoryBean的getObject方法会创建sqlSessionFactory，将其加入到IOC容器
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();

        // 设置连接池
        sqlSessionFactoryBean.setDataSource(dataSource);

        Configuration configuration = new Configuration();
        configuration.setMapUnderscoreToCamelCase(true);
        configuration.setLogImpl(Slf4jImpl.class);
        configuration.setAutoMappingBehavior(AutoMappingBehavior.FULL);
        // 设置settings
        sqlSessionFactoryBean.setConfiguration(configuration);

        // 设置别名
        sqlSessionFactoryBean.setTypeAliasesPackage("com.handle.pojo");

        PageInterceptor pageInterceptor = new PageInterceptor();
        Properties properties = new Properties();
        properties.setProperty("helperDialect", "postgresql");
        pageInterceptor.setProperties(properties);
        // 添加插件，如PageHelper
        sqlSessionFactoryBean.addPlugins(pageInterceptor);

        return sqlSessionFactoryBean;
    }
    
    // mapper代理对象加入到IOC容器
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        // 指定mapper.java和mapper.xml所在的共同包
        mapperScannerConfigurer.setBasePackage("com.handle.mapper");
        return mapperScannerConfigurer;
    }
}
```

### 配置mapper包扫描

- 1.mapper.xml的名称和mapper.java的名称必须相同

- 2.mapper.xml的文件夹结构和mapper.java的包名一致

```xml
<mappers>
    <package name="com.handle.application.dao" />
</mappers>
```

### 列名和属性映射

#### 1.开启驼峰式映射

```xml
<settings>
    <!-- account_id accountId 自动映射 -->
    <setting name="mapUnderscoreToCamelCase" value="true" />
</settings>
```

#### 2.定义别名

```sql
select account_id as accountId from Account

-- 可以省略as
select account_id accountId from Account
```

#### 3.resultMap自定义映射

```xml
<!-- 1.定义 -->
<resultMap id="AccountMap" type="com.handle.Application.po.Account">
    <!-- 1.1定义主键映射关系 -->
    <id column="account_id" property="accountId" />
    <!-- 1.2定义普通列映射关系 -->
    <result column="account_name" property="accountName"/>
</resultMap>

<!-- 2.使用 -->
<select id="queryAccountById" resultMap="AccountMap">
    select account_id from Account
</select>
```

### 表映射

```xml
<settings>
    <!-- 设置对于resultMap标签，无论有没有嵌套（association/collection）都自动映射result标签的列名和属性 -->
    <setting name="autoMappingBehavior" value="FULL" />
</settings>
```

#### 一对一

从表实体包含主表实体的对象引用，一个账号对应一个用户

- 用户实体

```java
@Getter
@Setter
public class User {
    private Long userId;

    private String userName;
}
```

- 账户实体

```java
@Getter
@Setter
public class Account {
    private Long accountId;

    private Long userId;

    private BigDecimal money;

    // 一对一，属性中包含对方对象
    private User user;
}
```

```xml
<resultMap id="accountUserMap" type="com.handle.application.Account">
    <id column="account_id" property="accountId" />
    <result column="user_id" property="userId" />
    <result column="money" property="money" />
    <association column="user_id" property="user" javaType="com.handle.application.User">
        <id column="user_id" property="userId" />
        <result column="user_name" property="userName" />
    </association>
</resultMap>
```

#### 一对多

主表实体包含从表实体的集合引用，一个用户有多个账户

- 用户实体

```java
@Getter
@Setter
public class User {
    private Long userId;

    private String userName;

    // 一对多，属性中包含对方对象集合
    private List<Account> accounts;
}
```

- 账户实体

```java
@Getter
@Setter
public class Account {
    private Long accountId;

    private Long userId;

    private BigDecimal money;
}
```

```xml
<resultMap id="userAccountMap" type="com.handle.application.User">
    <id column="user_id" property="userId" />
    <result column="user_name" property="userName" />
    <collection property="accounts" ofType="com.handle.application.Account">
        <id column="account_id" property="accountId" />
        <result column="user_id" property="userId" />
        <result column="money" property="money" />
    </collection>
</resultMap>
```

#### 多对多

一个角色可以赋予给多个用户，一个用户可以拥有多个角色，这里只展示前者（跟一对多一样）

- 用户实体

```java
@Getter
@Setter
public class User {
    private Long userId;

    private String userName;
}
```

- 角色实体

```java
@Getter
@Setter
public class Role {
    private Long roleId;

    private String roleName;

    private List<User> users;
}
```

```xml
<resultMap id="someName" type="com.handle.application.Role">
    <id column="role_id" property="roleId" />
    <result column="role_name" property="roleName" />
    <collection property="users" ofType="com.handle.application.User">
        <id column="user_id" property="userId" />
        <result column="user_name" property="userName" />
    </collection>
</resultMap>
```

### 主键

#### 获取插入数据的主键

- 自增长主键

```xml
<insert id="insertAccount" useGeneratedKeys="true" keyColumn="account_id" keyProperty="accountId">
    <!-- insert sql -->
</insert>
```

- 非自增长主键

```xml
<insert id="insertAccount">
    <selectKey order="BEFORE" resultType="java.lang.Integer" keyProperty="accountId">
        <!-- 查询下一个主键序列号的sql -->
    </selectKey>
    <!-- insert sql -->
</insert>
```

### 需要转义的符号

也可以用`<![CDATA[特殊符号]]>`，无需进行转义

|符号|原符号|替换符号|
|:-|:-|:-|
|小于|<|`&lt;`|
|小于等于|<=|`&lt;=`|
|大于|>|`&gt;`|
|大于等于|>=|`&gt;=`|
|不等于|<>|`&lt;&gt;`|
|与|&|`&amp;`|
|单引号|'|`&apos;`|
|双引号|"|`&quot;`|

### 动态SQL

#### `if`标签

- 字符串判空

```xml
<if test="id != null and id != ''">
    and id = #{id}
</if>
```

- 字符串判等

```xml
<if test="name != null and name == 'handle'.toString()">
    and id = #{id}
</if>
```

- 集合判空

```xml
<if test="ids != null and ids.size() > 0">
    and id in
    <foreach collection="ids" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</if>
<if test="ids != null and !ids.isEmpty()">
    and id in
    <foreach collection="ids" item="item" open="(" separator="," close=")">
        #{item}
    </foreach>
</if>
```

- 布尔值判断

```xml
<if test="true == deleteFlag">
</if>
```

#### `foreach`标签

- collection，方法为单个List类型的参数时，如果不指定别名，则foreach的collection属性可填list或collection

- item，用来获取集合的每个元素

- open，遍历之前要添加的字符串

- separator，两次遍历之间添加的分隔符

- close，遍历之后要添加的字符串

```xml
<foreach collection="集合变量名称" item="item" index="index" open="(" separator="," close=")">
    #{item}
</foreach>
```

##### 批量插入写法

```xml
insert into account (account_name, gender) values
    <foreach collection="accounts" item="item" separator=",">
        (#{item.accountName}, #{item.gender})
    </foreach>
```

##### 批量更新写法

- 1.jdbcUrl设置允许执行多语句

```properties
jdbc.jdbcUrl=jdbc:postgresql://localhost:5432/handle?allowMultiQueries=true
```

- 2.编写sql

```xml
<foreach collection="accounts" item="item">
    update account 
    set account_name=#{item.accountName},gender=#{item.gender}
    where account_id = #{item.accountId};
</foreach>
```

#### `where`标签

- 只有在一个以上的`if`条件满足的情况下才去插入`where`子句

- 自动去掉开头、末尾多余的`and`和`or`

```xml
<select id="findActiveBlogLike" resultType="Blog">
    select * from blog
    <where>
        <if test="state != null">
            state = #{state}
        </if>
        <if test="author != null and author.name != null">
            and author_name like #{author.name}
        </if>
    </where>
</select>
```

#### `set`标签

- 用于动态包含需要更新的列
- 会动态前置`set`关键字
- 消除无关的逗号

```xml
<update id="updateAccount" parameterType="java.lang.String">
    update account
    <set>
        <if test="username != null">username=#{username},</if>
        <if test="password != null">password=#{password}</if>
    </set>
    where id=#{id}
</update>
```

#### choose标签

```xml
<choose>  
    <when test="username != null and username != ''">  
        user_name=#{username} 
    </when >  
    <otherwise>
        user_name is null
    </otherwise>  
</choose>  

```

#### sql标签

- 提取重复的sql片段

```xml
<!-- 1.定义 -->
<sql id="baseSql">
    select * from account
</sql>

<select id="queryAccountById">
    <!-- 2.引用 -->
    <include refid="baseSql" />
    where account_id=#{id}
</select>
```

#### in 条件大于1000

- 待验证

```xml
(
    id in
    <foreach collection="ids" item="item" index="index" open="(" separator="," close=")">
        <if test="index % 999 == 998">) or id in (</if>
        #{item}
    </foreach>
)
```

- 更直观

```xml
(1, id) in
<foreach collection="ids" item="item" index="index" open="(" separator=',' close=")">
    (1, #{item})
</foreach>
```

### Mybatis中的延迟加载

一个用户对应多个账户，多个账号对应一个用户，一个账号对应一个用户

- 在查询用户时，用户下的账户信息什么时候使用才什么时候查询
- 在查询账户时，账户所属的用户信息应该随着账户查询时一起查询出来
- 延迟加载（懒加载）：在真正使用数据是才发起查询，不用的时候不查询（一对多、多对多表关系）
- 立即加载：不管用不用，只要一调用方法，马上发起查询（多对一、一对一表关系）

#### 开启延迟加载

- SqlMapConfig.xml

```xml
<settings>
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```

- 一对一表关系开启延迟加载

- IUserDAO.xml

```xml
<mapper namespace="com.handle.dao.IUserDAO">
    <select id="queryUserById" resultType="com.handle.entity.User">
        select user_id as userId, user_name as userName from user where user_id=#{userId}
    </select>
</mapper>
```

- IAccountDAO.xml

`association`元素添加select标签

```xml
<mapper namespace="com.handle.dao.IAccountDAO">
    <resultMap id="accountUserMap" type="com.handle.entity.Account">
        <id property="accountId" column="account_id"/>
        <result property="userId" column="user_id"/>
        <result property="money" column="money"/>
        <association property="user" column="user_id" javaType="com.handle.entity.User" select="com.handle.dao.IAccountDao.queryUserById">
            <id property="userId" column="user_id"/>
            <result property="userName" column="user_name"/>
        </association>
    </resultMap>
</mapper>
```

- 一对多表关系开启延迟加载

- IAccountDAO.xml

```xml
<mapper namespace="com.handle.dao.IAccountDAO">
    <select id="queryAccountByUserId" resultType="com.handle.entity.Account">
        select account_id as accountId, user_id as userId, money from account where account_id=#{accountId}
    </select>
</mapper>
```

- IUserDAO.xml

`collection`元素添加select标签

```xml
<mapper namespace="com.handle.application.IUserDAO">
    <resultMap id="userAccountMap" type="com.handle.entity.User">
        <id property="userId" column="user_id"/>
        <result property="userName" column="user_name"/>
        <collection property="accounts" javaType="com.handle.entity.Account" select="com.handle.dao.IAccountDAO.queryAccountByUserId">
            <id property="accountId" column="account_id"/>
            <result property="userId" column="user_id"/>
            <result property="money" column="money"/>
        </collection>
    </resultMap>
</mapper>
```

### mybatis中的一级缓存和二级缓存

#### 一级缓存

指的是SqlSession对象的缓存。当我们执行查询后，查询的结果会存入到SqlSession为我们提供的一块区域中。该区域是一个Map。当我们再次查询同样的数据，mybatis会先去SqlSession中查询是否有，有的话直接拿出来用。

- 一级缓存默认是开启的
- 当调用SqlSession的添加、修改、删除，提交，关闭等方法时，会清空一级缓存
- 也可以通过SqlSession的clearCache方法清空缓存。
- 当SqlSession对象消失时，一级缓存也不存在了。

#### 二级缓存

指的是SqlSessionFactory对象的缓存。同一个SqlSessionFactory对象创建的SqlSession共享其缓存。二级缓存存放的是数据，不是（同一个结果集）对象

- 配置文件配置支持二级缓存，SqlMapConfig.xml

```xml
<settings>
    <setting name="cacheEnabled" value="true">
</settings>
```

- 配置当前映射文件支持二级缓存，mapper.xml

```xml
<mapper namespace="com.handle.application.IUserDAO">
    <cache/>
</mapper>
```

- 配置当前操作支持二级缓存

```xml
<mapper namespace="com.handle.application.IUserDAO">
    <select id="queryUserById" resultType="com.handle.entity.User" useCache="true>
        select user_id as userId, user_name as userName from user where user_id=#{userId}
    </select>
</mapper>
```

### `mybatis`注解开发

#### 常规开发

```java
public interface IUserDAO {
    // 传统用as做字段-属性映射
    @Select("select user_id as userId, user_name as userName from user where user_id=#{userId}")
    User queryUserById(Long userId);

    @Select("select * from user where user_id=#{userId}")
    // mybatis支持@Results做字段-属性映射。如果Results里面的映射还想应用到别的方法，可以指定Results的id属性，然后在对应的方法上使用注解@ResultMap(value={"userMap"})
    @Results(id="userMap", value={
        @Result(id=true, property="userId", column="user_id"),
        @Result(id=false, property="userName", column="user_name")
    })
    User queryUserById(Long userId);

    @Select("select * from user limit 10")
    @ResultMap(value={"userMap"})
    List<User> queryUsers();

    @Insert("insert into user (user_id,user_name) values (#{userId}, #{userName})")
    int addUser(User user);

    @Update("update user set user_name = #{userName} where user_id=#{userId}")
    int updateUser(User user);

    @Delete("delete from user where user_id=#{userId}")
    int deleteUser(Long userId);
}
```

#### 一对一表关系注解开发

- IUserDAO.java

```java
public interface IUserDAO {
    @Select("select user_id as userId, user_name as userName from user where user_id=#{userId}")
    User queryUserById(Long userId);
}
```

- IAccountDAO.java

```java
public interface IAccountDAO {
    @Select("select * from account")
    @Results(id = "accountMap", value = {
        @Result(id = true,  property = "accountId", column = "account_id"),
        @Result(id = false,  property = "userId", column = "user_id"),
        @Result(id = false,  property = "money", column = "money"),
        @Result(id = false,  property = "user", column = "user_id", one = @One(select = "com.handle.dao.IUserDAO.queryUserById"), fetchType = FetchType.EAGER)
    })
    List<Account> queryAll();
}

```

#### 一对多表关系注解开发

- IAccountDAO.java

```java
public interface IAccountDAO {
    @Select("select account_id as accountId, user_id as userId, money from account where user_id=#{userId}")
    List<Account> queryAccountByUserId(Long userId);
}
```

- IUserDAO.java

```java
public interface IUserDAO {
    @Select("select * from user")
    @Results(id = "userAccountMap", value = {
        @Result(id = true,  property = "userId", column = "user_id"),
        @Result(id = false,  property = "userName", column = "user_name"),
        // 在支持延迟加载的情况下fetchType = FetchType.LAZY可以延迟加载
        @Result(id = false,  property = "accounts", column = "user_id", many = @Many(select = "com.handle.dao.IAccountDAO.queryAccountByUserId"), fetchType = FetchType.EAGER)
    })
    List<Account> queryAll();
}

```

#### mybatis注解开启二级缓存

- 配置文件配置支持二级缓存，SqlMapConfig.xml

```xml
<settings>
    <setting name="cacheEnabled" value="true">
</settings>
```

- DAO添加`@CacheNamespace(blacking = true)`

```java
@CacheNamespace(blacking = true)
public interface IAccountDAO {
    @Select("select account_id as accountId, user_id as userId, money from account where user_id=#{userId}")
    List<Account> queryAccountByUserId(Long userId);
}
```

### `#{参数名}` 和 `${参数名}` 的区别

1) #{参数名}：结果等于参数值加‘’号，即‘参数值’，功能为PreparedStatement的参数占位符，防sql注入

2) ${参数名}：结果等于参数值，功能为Statement对象的字符串拼接SQL，不防sql注入

### 模糊查询

```xml
<select id="queryUserByName" parameterType="java.lang.String" resultType="User">
    select id, name from user where name like "%" #{name} "%"
</select>
```

### 插入数据后返回id

```xml
<!-- keyProperty为实体类属性名称，keyColumn为数据库字段名称，resultType为返回值类型，AFTER表示执行插入语句后，再执行获取id的操作 -->
<select id="addUser" parameterType="com.handle.application.domain.UserDO">
    <selectKey keyProperty="id" keyColumn="id" resultType="java.lang.Integer" order="AFTER">
        select last_insert_id()
    </selectKey>
    insert into user (id, name) values (null, #{name})
</select>
```

### DAO接口返回值

当DAO接口返回单个对象数据时，如果sql的结果为空，则返回值为null

```java
// 当没有用户id为userId的记录时，user为null
UserDO user = userDAO.queryUser(userId);
```

当DAO接口返回集合对象数据时，如果sql的结果为空，则返回长度为0的空集合

```java
// 当没有用户年龄为userAge的记录时，users不为null，其长度为0
List<UserDO> users = userDAO.queryUsers(userAge);
```

### Executor

- MyBatis 有三种基本的 Executor 执行器：
    - SimpleExecutor： 每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象。
    - ReuseExecutor： 执行 update 或 select，以 sql 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后，不关闭 Statement 对象，而是放置于 Map<String, Statement>内，供下一次使用。简言之，就是重复使用 Statement 对象。
    - BatchExecutor：执行 update（没有 select，JDBC 批处理不支持 select），将所有 sql 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理。与 JDBC 批处理相同。

作用范围：Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内。

在 MyBatis 配置文件中，可以指定默认的 ExecutorType 执行器类型，也可以手动给 DefaultSqlSessionFactory 的创建 SqlSession 的方法传递 ExecutorType 类型参数

### MyBatis插件

MyBatis 仅可以编写针对 ParameterHandler、 ResultSetHandler、 StatementHandler、 Executor 这 4 种接口的插件

MyBatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法

具体就是 InvocationHandler 的 invoke() 方法，当然，只会拦截那些你指定需要拦截的方法。

实现 MyBatis 的 Interceptor 接口并复写 intercept() 方法，然后再给插件编写注解，指定要拦截哪一个接口的哪些方法即可

最后在配置文件中配置你编写的插件

### IDEA插件

- MyBatisX

## PageHelper

官网：<https://github.com/pagehelper/Mybatis-PageHelper>

### pom

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <!-- 23年最高版本，已经好久没更新，推荐使用mybatis-plus分页 -->
    <version>2.1.0</version>
</dependency>
```

### 配置

```properties
# 配置使用默认就行
pagehelper.propertyName=propertyValue
pagehelper.reasonable=false
pagehelper.defaultCount=true
```

### 使用

```java
PageHelper.startPage(pageNumber, pageSize);
// PageHelper.startPage后面必须紧接查询语句
List<UserDO> users = userDAO.queryUsers(userAge);

// 获取总数
long total = new PageInfo<>(users).getTotal();
```

## Mybatis-Plus

可以快速进行单表的CRUD

- 依赖，需要参考mybatis-spring-boot-starter的版本，避免版本冲突

```xml
<!--父项目-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-bom</artifactId>
            <version>3.5.9</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<!--子项目-->
<!-- spring-boot场景启动器 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-spring-boot3-starter</artifactId>
</dependency>
<!-- 插件依赖，配置插件需要用到 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-jsqlparser</artifactId>
</dependency>
```

- 配置文件

```conf
# mybatis-plus配置
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.slf4j.Slf4jImpl
```

- 配置类

```java
@Configuration
public class ApplicationConfiguration {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        // 乐观锁插件，在更新的时候比较版本字段，并且做版本自增
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        // 防止全表删除和更新的拦截器
        interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
        // 如果配置多个插件, 切记分页最后添加
            // 如果有多数据源可以不配具体类型, 否则都建议配上具体的 DbType
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
        return interceptor;
    }
}
```

- service写法，service层提供了批量数据的操作

```java
// IService提供了一半的方法
public interface AccountService extends IService<AccountPo> {}

// ServiceImpl提供了另一半的方法
public class AccountServiceImpl extends ServiceImpl<AccountDao, AccountPo> implements AccountService {}
```

- mapper写法，mapper层提供了单个数据的操作

```java
// BaseMapper指定的实体名要跟表名一样，否则实体的类上要加注解@TableName指定表名
public interface AccountDao extends BaseMapper<AccountPo> {}
```

### 分页

- 1.配置分页插件

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    // 如果配置多个插件, 切记分页最后添加
    // 如果有多数据源可以不配具体类型, 否则都建议配上具体的 DbType
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
    return interceptor;
}
```

- 2.使用mybatis-plus自带的查询方法分页

```java
// 2.1
Page<AccountPo> page = new Page<>(1, 5);
List<AccountPo> accounts = accountDao.selectList(page, null);
long total = page.getTotal();

// 2.1使用自定义的查询方法分页
// 这种分页方式的page参数可以为null
List<AccountPo> selectByGender(IPage<?> page, @Param("gender") boolean gender);

IPage<AccountPo> page = new Page<>(1, 5);
List<AccountPo> accounts = accountDao.selectByGender(page, false);
long total = page.getTotal();
```

- 3.使用自定义的查询方法分页

```java
// 这种返回类型为list的分页方式的page参数可以为null
List<AccountPo> selectByGender(IPage<?> page, @Param("gender") boolean gender);

IPage<AccountPo> page = new Page<>(1, 5);
List<AccountPo> accounts = accountDao.selectByGender(page, false);
long total = page.getTotal();
```

### 注解

- @TableName，指定实体类关联的表名，不加此注解的时候实体类名作为表名（忽略大小写）

- @TableId，当实体类的属性名和表的主键名不一样时，添加此注解进行关联，并且可以指定插入数据时的id分配算法（默认雪花算法，可以在配置文件统一设置id分配算法）

- @TableField，当实体类的属性名和表的列名不一样时，添加此注解进行关联
    - exist，当表没有这个字段时，必须设置为false

```java
@TableName("account")
public class AccountPo {
    @TableId("id")
    private Long id;

    @TableField("name")
    private String name;
}
```

### 逻辑删除

```properties
# 逻辑删除配置
mybatis-plus.global-config.db-config.logic-delete-field=deleted
mybatis-plus.global-config.db-config.logic-delete-value=true
mybatis-plus.global-config.db-config.logic-not-delete-value=false
```

### 乐观锁

- 1.配置拦截器

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
}
```

- 2.配置注解

```java
public class AccountPo {
    @Version
    private Integer version;
}
```

### 拦截器

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    // 乐观锁插件，在更新的时候比较版本字段，并且做版本自增
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    // 防止全表删除和更新的拦截器
    interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
    // 如果配置多个插件, 切记分页最后添加
        // 如果有多数据源可以不配具体类型, 否则都建议配上具体的 DbType
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.POSTGRE_SQL));
    return interceptor;
}
```

### 代码生成器

- 依赖

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>3.5.8</version>
    <scope>compile</scope>
</dependency>
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.8</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
</dependency>
```

- 生成代码

```java
String projectPath = "..\\mybatis-plus-generator";

FastAutoGenerator.create("jdbc:postgresql://localhost:5432/databaseName", "username", "password")
    // 全局配置
    .globalConfig(builder -> {
    // 设置作者
    builder.author("handle")
    // 指定java文件输出目录
    .outputDir(projectPath + "/src/main/java")
    .disableOpenDir()
    ;
    })
    // 包配置
    .packageConfig(builder ->
    // 设置父包名
    builder.parent("com.handle.mybatisPlusGenerator")
    // 设置实体类包名
    .entity("pojo.po")
    // 设置Service接口包名
    .service("service")
    // 设置Service实现类包名
    .serviceImpl("service.impl")
    // 设置Mapper接口包名
    .mapper("dao")
    // xml文件输出目录
    .pathInfo(Collections.singletonMap(OutputFile.xml, projectPath + "/src/main/resources/mapper/com/handle/mybatisPlusGenerator/dao")) 
    )
    // 策略配置
    .strategyConfig(builder ->
    // 设置需要生成的表名，多张表用英文逗号隔开
    builder.addInclude("tableName")
        .entityBuilder()
        .disableSerialVersionUID()
        // 启用Lombok
        .enableLombok()
        // 启用字段注解
        .enableTableFieldAnnotation()
        .convertFileName(entityName -> entityName + "Po")
        .controllerBuilder()
        .disable()
        .serviceBuilder()
        .convertServiceFileName(entityName -> entityName + "Service")
        .mapperBuilder()
        .convertMapperFileName(entityName -> entityName + "Dao")
        .convertXmlFileName(entityName -> entityName + "Dao")
    )
    // 使用Freemarker引擎模板，默认的是Velocity引擎模板
    .templateEngine(new FreemarkerTemplateEngine())
    // 执行生成
    .execute();
```

## Spring

- 启动时Spring扫描所有XML配置或注解
- 把每个Bean的信息解析成BeanDefinition对象，存到Map中
- BeanFactory利用反射机制实例化Bean对象，并递归填充其属性

### IOC

读取配置文件声明的组件（Bean）信息，转成BeanDefinition对象，然后根据BeanDefinition对象反射创建Bean对象，并管理Bean对象的生命周期
<br/>
作用：削减计算机程序的耦合（降低代码之间的依赖关系）

#### IOC容器

- `BeanFactory`：它在构建核心容器时，创建对象采用`延迟加载`的方式，什么时候根据id获取对象，什么时候才真正创建对象。`多例对象适用`。
- `ApplicationContext`：它在构建核心容器时，创建对象采用`立即加载`的方式，只要一读取完配置文件就马上创建配置文件中配置的对象。`单例对象适用`。

##### ApplicationContext的三个常用实现类

- `AnnotationConfigApplicationContext`，读取注解创建容器
- `ClassPathXmlApplicationContext`，加载类路径下的配置文件
- `FileSystemXmlApplicationContext`，加载磁盘任意路径下的配置文件（必须有访问权限）

#### IOC注解

##### 自定义类使用的注解

- @Component，当不方便区分是哪一层时使用
- @Controller
- @Service
- @Repository
<br/>

注：当注解的value属性不写时，默认为所注解的类的类名首字母小写

##### 第三方类使用的注解

###### @Bean

- 在配置类的方法上添加此注解，把当前方法的返回值存入spring的ioc容器中

- 当注解的方法有参数时，spring框架会去容器中查找是否有可用的bean对象，查找的方式和@AutoWired一样

```java
@Configuration
public class ApplicationContextConfiguration {
    // name属性用于指定bean的id，不指定时默认是当前方法名
    @Bean(name = "dataSource")
    public DruidDataSource druidDataSource() {
        return new DruidDataSource();
    }
}
```

##### 引用其它Bean

```java
@Configuration
public class ApplicationContextConfiguration {
    @Bean(name = "dataSource")
    public DruidDataSource druidDataSource() {
        return new DruidDataSource();
    }    
    
    @Bean
    public JdbcTemplate jdbcTemplate() {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        // 使用场景1，由于dataSource是用@Bean创建的，所以这里可以直接调用
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
    
    // 使用场景2，通过声明形参调用，IOC容器必须要有对应类型的组件，否则会报错
    // 当有多个实例的时候，形参名要和对应的Bean的id一致；或者指定@Qualifier属性和对应的Bean的id一致
    // 2.1形参名要和对应的Bean的id一致
    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
    
    // 2.2@Qualifier属性和对应的Bean的id一致
    @Bean
    public JdbcTemplate jdbcTemplate(@Qualifier("dataSource") DataSource dataSource) {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
}
```

#### 配置类注解

##### @Configuration

- 表示所注解的类是一个配置类，可替代Spring的bean.xml配置文件
- 类似配置包扫描、读取配置文件、定义Bean等功能，bean.xml中可以做，配置类中一样也可以做

```java
@Configuration
// 1.配置包扫描
@ComponentScan(basePackages = {"com.handle.application"})
// 2.读取配置文件
@PropertySource(value = {"classpath:jdbc.properties"})
public class ApplicationConfiguration {
    /**
     * 3.定义Bean
     */
    @Bean
    public DruidDataSource dataSource() {
        return new DruidDataSource();
    }
}
```

##### @Import

- 用于导入其他的配置类

- 注解所在类是父配置类

- 其value属性指定的类都是子配置类，子配置类不用写@Configuration

```java
@Configuration
@Import(SubConfiguration.class)
public class MainConfiguration {}

public class SubConfiguration {}
```

##### 加载配置类的方式

- 1.配置类作为创建AnnotationConfigApplicationContext对象的参数加载，如果有多个配置类其关系为并列关系，这些配置类可不写@Configuration

```java
public class SubConfiguration1 {}

public class SubConfiguration2 {}

public class ApplicationTest {
    @Test
    public void test() {
        ApplicationContext context = new AnnotationConfigApplicationContext(SubConfiguration1.class, SubConfiguration2.class);
    }
}
```

- 2.其它的配置类作为子配置类，在主配置类用@Import注解导入，然后把主配置类作为创建AnnotationConfigApplicationContext对象的参数加载

```java
@Import(value = {SubConfiguration1.class, SubConfiguration2.class})
public class MainConfiguration {}

public class SubConfiguration1 {}

public class SubConfiguration2 {}

public class ApplicationTest {
    @Test
    public void test() {
        ApplicationContext context = new AnnotationConfigApplicationContext(MainConfiguration.class);
    }
}
```

#### 配置包扫描的注解

- @ComponentScan

- 指定spring创建容器时要扫描的包，相当于在bean.xml配置了：

```xml
<context:component-scan base-package="package1,package2,..." />
```

- 该注解指定的包中含有配置类，也会扫描这些配置类，这时不用写@Import(会被扫描的配置类名.class)

```java
// DataSourceConfiguration会被扫描到，不用写@Import(DataSourceConfiguration.class)
@Configuration
@ComponentScan(basePackages = {"com.handle.application"})
public class ApplicationConfiguration {}


package com.handle.application.configuration;

@Configuration
public class DataSourceConfiguration {}
```

#### 配置文件注解

##### @PropertySource

- encoding，指定配置文件编码格式，如果是`.properties`文件并且文件内容有中文需要指定编码格式

- 用于加载指定的配置文件

- person.yaml

```yaml
user01:
    name: 张三
```

- 将配置文件中person.name的值封装到Person对象

```java
@Component
@PropertySource(value = "classpath:person.yaml")
@ConfigurationProperties(prefix = "user01")
public class Person {
    private String name;
}
```

##### @ImportResource
  
- 作用：导入Spring配置文件(springConfig.xml)
  
- 用法：@Configuration + @ImportResource("classpath:xxx.xml") 一起用

例子：读取spring.xml文件中的Pet

1. 定义宠物类

```java
@Getter
@Setter
@ToString
public class Pet {
    private String name;

    private int age;
}
```

2.设置spring.xml配置Pet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="pet" class="com.handle.entities.Pet">
        <property name="name" value="cat"></property>
        <property name="age" value="2"></property>
    </bean>
</beans>
```

3.定义配置类

```java
@Configuration
@ImportResource("classpath:spring.xml")
public class MainConfiguration {}
```

#### DI注解

Spring的三大依赖注入方式：构造器注入、字段注入、Setter注入

构造器注入适合处理必需的依赖项

而Setter注入则更适合可选的依赖项，这些依赖项可以有默认值或在对象生命周期中动态设置

```java
// 构造器注入，依赖字段可以定义为final（官方推荐）
@Controller
public class MyController {
    private final MyService myService;

    // 在只有一个构造器的情况下，Spring会自动把它当成需要注入的构造器,写不写@Autowired都可以
    public MyController(MyService myService) {
        this.orderService = myService;
    }
}

// 字段注入
@Component
public class MyController {
    @Autowired
    private MyService myService;
}

// Setter注入
@Controller
public class XxxController {
    private MyService myService;

    @AutoWired
    public void setMyService(MyService myService) {
        this.myService = myService;
    }
}
```

- 工具类的静态方法使用Spring Bean

```java
// 方法1
// 使用SpringBeanHolder，它对所有工具类都适用，然后在工具类里面获取Spring Bean并调用其实例方法
@Component
public class SpringBeanHolder implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        SpringBeanHolder.applicationContext = applicationContext;
    }

    public static <T> T getBean(Class<T> clazz) {
        return applicationContext.getBean(clazz);
    }

    public static <T> T getBean(String name, Class<T> clazz) {
        return applicationContext.getBean(name, clazz);
    }
}

// 这里就不用声明为Spring Bean了
public final class JacksonUtil {
    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return SpringBeanHolder.getBean(ObjectMapper.class).writeValueAsString(value);
    }
}

// 或者将SpringBeanHolder.getBean提取出来放到一个方法里
public final class JacksonUtil {
    private static volatile ObjectMapper objectMapper;

    private static ObjectMapper getObjectMapper() {
        if (Objects.isNull(objectMapper)) {
            synchronized (JacksonUtil.class) {
                if (Objects.isNull(objectMapper)) {
                    objectMapper = SpringBeanHolder.getBean(ObjectMapper.class);
                }
            }
        }
        return objectMapper;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return getObjectMapper().writeValueAsString(value);
    }
}

// 方法2，实例字段注入 + @PostConstruct赋值给static实例，注入的Spring Bean是final的，推荐
@Component
public final class JacksonUtil {
    private static JacksonUtil instance;

    private final ObjectMapper OBJECT_MAPPER;

    // 作为Spring管理的工具类，定义为包级构造器更合适
    JacksonUtil(ObjectMapper objectMapper) {
        this.OBJECT_MAPPER = objectMapper;
    }

    @PostConstruct
    public void init() {
        JacksonUtil.instance = this;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.readValue(content, valueTypeRef);
    }
}

// 方法3，实例字段注入 + @PostConstruct赋值给static字段，注入的Spring Bean不是final的，不推荐
@Component
public final class JacksonUtil {
    private static ObjectMapper objectMapper;

    private final ObjectMapper INJECTED_OBJECT_MAPPER;

    // 作为Spring管理的工具类，定义为包级构造器更合适
    JacksonUtil(ObjectMapper objectMapper) {
        this.INJECTED_OBJECT_MAPPER = objectMapper;
    }

    @PostConstruct
    public void init() {
        JacksonUtil.objectMapper = this.INJECTED_OBJECT_MAPPER;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return objectMapper.writeValueAsString(value);
    }
}
```

##### @AutoWired

- 其required属性默认为true，这时如果匹配失败，则会报错；改为false，这时如果匹配失败，则注入的值为null，会造成其所注解的成员变量为null导致的空指针异常

- 工作流程：根据类型查找Spring容器中的Bean
    - 找不到类型匹配的Bean，报错
    - 找到唯一类型匹配的Bean，注入
    - 找到多个类型匹配的Bean
    - 有@Qualifier注解，根据@Qualifier指定的名称作为Bean的id进行匹配
        - 找到，注入
        - 找不到，报错
    - 无@Qualifier注解，根据@AutoWired所注解的成员变量名作为Bean的id进行匹配
        - 找到，注入
        - 找不到，报错

```java
@Controller
public class XxxController {
    // 使用场景1，在成员变量上
    @AutoWired
    private XxxService xxxService;
}

@Controller
public class XxxController {
    private XxxService xxxService;

    // 使用场景2，在构造方法上
    // 在只有一个构造器的情况下，Spring会自动把它当成需要注入的构造器,写不写@Autowired都可以
    @AutoWired
    public XxxController(XxxService xxxService) {
        this.xxxService = xxxService;
    }
}

@Controller
public class XxxController {
    private XxxService xxxService;

    // 使用场景3，在set方法上
    @AutoWired
    public void setXxxService(XxxService xxxService) {
        this.xxxService = xxxService;
    }
}
```

##### @Qualifier

- 在按照类型注入的基础上（有多个匹配的实例），再按照名称注入

- 在给类成员注入时需要和`@Autowired`一起使用

- 在给方法参数注入时可以单独使用，用于该参数类型有多个实例的情况

```java
@Configuration
public class ApplicationConfiguration {
    @Bean(name = "queryRunner")
    public QueryRunner queryRunner(@Qualifier("dataSource2") DataSource dataSource) {
        return new QueryRunner(dataSource);
    }
}
```

##### @Primary

解决同一类型存在多个Bean实例的注入问题

在Bean定义时（例如使用 @Bean 或类注解）添加 @Primary 注解

表示该 Bean 是首选的注入对象。

当进行 @Autowired 注入时，如果没有使用 @Qualifier 指定名称，Spring 将优先选择带有 @Primary 的 Bean

```java
// 将 UserServiceImpl1 设为首选注入对象
@Primary 
@Service
public class UserServiceImpl1 implements UserService {}

@Service
public class UserServiceImpl2 implements UserService {}

@Controller
public class UserController {
    // 自动注入 UserServiceImpl1
    @AutoWired
    private UserService userService;
}
```

##### @Resource

- jdk11以上或jdk8以下版本需要导入依赖

```xml
<dependency>
    <groupId>jakarta.annotation</groupId>
    <artifactId>jakarta.annotation-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

- 根据名称进行匹配

- 如果没有指定name属性，先根据所注解的成员变量名查找Spring容器中的Bean
    - 没有对应的Bean，会再根据类型进行查找

- 只能注解在字段和Setter方法上，不支持构造器注入

```java
@Controller
public class XxxController {
    @Resource(name = "xxxService")
    private XxxService xxxService;
}
```

##### @Value

- @AutoWired、@Qualifier和@Resource都只能注入其他bean类型的数据，而@Value用来注入基本类型和String类型的数据

- 此注解所在的类必须是一个组件

- 作用在字段上，可以使用Spring的spel表达式，也可以用来注入配置文件中的属性值

- 只能读取单个值，不能读取集合

- 假设配置文件application.yaml存在配置

```yaml
user01:
    name: 张三
```

- 则应该这样注入

```java
@Component
public class UserDO {
    // 使用场景1
    @Value("${user01.name:默认值")
    private String name;
}

@Configuration
@PropertySource(value = "classpath:person.yaml")
public class ApplicationConfiguration {
    // 使用场景2
    @Bean
    public Person person(@Value("${user01.name:默认值") private String name) {
        Person person = new Person();
        person.setName(name);
        return person
    }
}
```

#### 周期方法注解

周期方法必须为公有方法，无参、无返回：`public void xxx()`

- @PostConstruct，指定初始化方法

```java
@PostConstruct
public void init() {}
```

- @PreDestroy，指定销毁方法

```java
@PreDestroy
public void destroy() {}
```

- 也可以通过Bean注解的属性指定初始化方法和销毁方法

```java
@Configuration
public class ApplicationContextConfiguration {
    @Bean(name = "druidDataSource", initMethod = "init", destroyMethod = "destroy")
    public DruidDataSource dataSource() {
        return new DruidDataSource();
    }
}
```

#### 作用域注解

- @Scope，指定bean的作用域，值SCOPE_SINGLETON、SCOPE_PROTOTYPE，默认单例

```java
// 使用场景1
@Scope(scopeName = ConfigurableBeanFactory.SCOPE_SINGLETON)
@Component
public void xxxClass() {}

// 使用场景2
@Configuration
public class ApplicationConfiguration {
    @Bean(name = "queryRunner")
    @Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
    public QueryRunner queryRunner(DataSource dataSource) {
        return new QueryRunner(dataSource);
    }
}
```

#### xml方式配置Bean(了解)

- Account.java

```java
@Getter
@Setter
@ToString
public class Account {
    private Integer id;

    private String name;
}
```

- spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="account" class="com.handle.application.entity.Account">
        <property name="id" value="001"></property>
        <property name="name" value="handle"></property>
    </bean>

    <!-- 定义bean的别名 -->
    <alias name="account" alias="account1" />
</beans>
```

- test

```java
@Test
public void test() {
    ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
    ApplicationContext context2 = new FileSystemXmlApplicationContext("...\\spring.xml");
    ApplicationContext context3 = new AnnotationConfigApplicationContext(ApplicationConfiguration.class);
    Account a = context.getBean("account1", Account.class);
    log.info(a.toString());
}
```

##### spring对bean的管理

- 创建 bean 的三种方式

```xml
    <!-- 1）使用默认构造函数创建：bean标签里面只有id 和 class 属性，如果类没有默认构造函数，则无法创建对象 -->
    <bean id="accountService" class="com.handle.learn.service.AccountServiceImpl"></bean>

    <!-- 2）使用某个类（普通工厂）中的方法创建对象，并存入spring容器 -->
    <bean id="instanceFactory" class="com.handle.learn.factory.InstanceFactory"></bean>
    <bean id="accountService2" factory-bean="instanceFactory" factory-method="getAccountService"></bean>

    <!-- 3）使用某个类（普通工厂）中的静态方法创建对象，并存入spring容器 -->
    <bean id="accountService3" class="com.handle.learn.factory.StaticFactory" factory-method="getAccountService"></bean>
```

- bean 对象的作用范围

```xml
<!-- bean标签的 scope 属性指定 bean 的作用范围，取值有：
    1）singleton：单例（默认）
    2）prototype：多例
    3）request：作用于web应用的请求范围
    4）session：作用于web应用的会话范围
    5）global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，相当于session
-->
<bean id="accountService4" class="com.handle.learn.service.AccountServiceImpl" scope="singleton"></bean>
```

- bean 对象的生命周期

```xml
<!--
    1）单例对象
        出生：当容器创建时出生
        活着：只要容器还在，对象一直存活
        死亡：容器销毁，对象死亡
        总结：单例对象的生命周期和容器相同
    2）多例对象
        出生：使用对象时spring框架才开始创建
        活着：对象只要是在使用过程中就一直活着
        死亡：当对象长时间不用，且没有别的对象引用时，由java的垃圾收集器回收
-->
<bean id="accountService5" class="com.handle.learn.service.AccountServiceImpl" scope="singleton" init-method="init" destroy-method="destroy"></bean>
```

- spring 的依赖注入（Dependency Injection）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <!-- IOC 的作用：降低程序间的耦合（依赖关系）
            依赖关系的管理：交给 Spring 来维护
            当前类需要用到其他类的对象，由 Spring 提供，我们只需在配置文件中说明
            依赖关系的维护：
                就称之为依赖注入
            能注入的数据有三类：
                1）基本数据类型和String
                2）其他bean类型（在配置文件中或者注解配置过的bean）
                3）复杂类型/集合类型
            注入的方式有三种：
                1）使用构造函数注入
                2）使用set方法注入（常用）
                3）使用注解注入
    -->

    <!-- 1）使用构造函数注入 
            使用标签：constructor-arg，标签在bean标签内部
            标签constructor-arg的属性：
                1）type：指定数据类型，该类型是构造函数中某个或某些参数的类型
                2）index：指定给构造函数中指定索引位置的参数赋值，索引从0开始
                3）name：指定给构造函数中指定名称的参数赋值（最常用）
                以上三个属性用于给指定构造函数中的指定参数赋值
                4）value：指定基本数据类型和String类型的数据
                5）ref：指定其他bean类型数据（在spring的ioc核心容器中出现过的bean对象）
            优点：在获取bean对象时，注入数据是必须的操作，否则对象无法创建
            缺点：改变了bean对象的实例化方式，使得在创建对象时，如果用不到这些数据也必须提供
    -->
    <bean id="accountService6" class="com.handle.learn.service.AccountServiceImpl">
        <constructor-arg name="parameterName1" value="123456"></constructor-arg>
        <constructor-arg name="parameterName2" ref="now"></constructor-arg>
    </bean>
    <bean id="now" class="java.util.Date"></bean>

    <!-- 2）使用set方法注入 
            涉及的标签：property，标签在bean标签内部
            标签property的属性：
                1）name：指定注入时调用的set方法名称（不是类属性名称）
                2）value：指定基本数据类型和String类型的数据
                3）ref：指定其他bean类型数据（在spring的ioc核心容器中出现过的bean对象）
            优点：创建对象没有明确限制，可以直接使用默认构造函数
            缺点：如果有某个成员必须有值，则获取对象时有可能set方法没有执行
    -->
    <bean id="accountService6" class="com.handle.learn.service.AccountServiceImpl">
        <property name="parameterName1" value="123456"></property>
        <property name="parameterName2" ref="now"></property>
    </bean>
    <bean id="now" class="java.util.Date"></bean>

    <!-- 用于给list结构集合注入的标签有：list、array、set，这些标签可以互换 -->
    <!-- array的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myArray">
            <array>
                <value>a</value>
                <value>b</value>
            </array>
        </property>
    </bean>

    <!-- list的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myList">
            <list>
                <value>a</value>
                <value>b</value>
            </list>
        </property>
    </bean>

    <!-- set的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="mySet">
            <set>
                <value>a</value>
                <value>b</value>
            </set>
        </property>
    </bean>

    <!-- 用于给map结构集合注入的标签有：map、props，这些标签可以互换 -->
    <!-- map的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myMap">
            <map>
                <entry key="dog" value="1"></entry>
                <entry key="cat">
                    <value>2</value>
                </entry>
            </set>
        </property>
    </bean>

    <!-- property的注入 -->
    <bean id="accountService" class="com.handle.application.service.AccountServiceImpl">
        <property name="myPropertys">
            <props>
                <prop key="username">tomcat</prop>
            </props>
        </property>
    </bean>
</beans>

```

### AOP

- 依赖：spring-aop、spring-aspects、aspectjweaver

```xml
<!-- 使用spring的功能一般都会导入spring-context，里面包含了spring-aop，一般不单独导入spring-aop -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
<!-- spring-aspects，里面包含了aspectjweaver -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>${spring.version}</version>
</dependency>
```

#### AOP相关术语

- JoinPoint连接点：被拦截的点（方法）

- PointCut切入点：被增强的点（方法），没被增强的只算连接点，不算切入点

- Advice通知/增强：拦截到连接点之后要做的事情，分为前置通知，后置通知，异常通知，最终通知，环绕通知

- Aspect切面：切入点和通知（引介）的结合

- Weaving织入：把通知应用到被代理对象从而创建新的代理对象的过程

- Introduction引介：一种特殊的通知，在不修改类代码的前提下，引介可以在运行期为类动态地添加一些方法或属性

- Target目标对象：被代理对象

- Proxy代理：一个类被AOP织入后，就产生一个代理类

#### 切入点表达式写法

- execution(访问修饰符 返回类型 包名.类名.方法名(参数列表))

- 访问修饰符：public/private

- 返回类型：基本数据类型/引用类型/void

- 只有同时不考虑访问修饰符和返回类型，二者才可一起写成`*`

- 包名可以使用通配符
    - 单层模糊，一级写一个`*`：`*.handle.*.service.*`
    - 多层模糊：`com..impl`，
    - `..`不能在开头，错误写法：`..impl`；正确写法`*..impl`

- 类名和方法名也可以使用*来实现通配：`*Impl`

- 参数列表
    - 具体参数，基本数据类型直接写类型名，引用类型写全限定类名，如：int，java.lang.String
    - 模糊参数，参数列表还可以使用通配符表示任意类型，但是只匹配有参数的方法
    - 任意参数：`..`，但是无参数还是建议用具体写法`()`

- 全通配写法：`* *..*.*(..)`，实际开发不会这么写

- 实际开发中通常切入到业务层实现类下的所有方法：`* com.handle.application.service.impl.*.*(..)`

#### 注解方式配置AOP

##### 切点表达式复用

###### 当前类复用

```java
// 1.定义一个public void的无参切点表达式方法
// 2.@Pointcut指定切点表达式
@Pointcut("execution(* com.handle.springAopDemo.service.impl.*.*(..))")
public void pointcut() {}

// 3.增强注解中引用：切点表达式方法名（必须加括号）
@Before("pointcut()")
public void before() {}
```

###### 全局复用

```java
public class ApplicationPointcut {
    // 1.定义一个public void的无参切点表达式方法
    // 2.@Pointcut指定切点表达式
    @Pointcut("execution(* com.handle.springAopDemo.service.impl.*.*(..))")
    public void pointcut() {}
}

public class LogAspect {
    // 3.增强注解中引用：全限定类名.切点表达式方法名（必须加括号）
    @Before("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public void before() {}
}
```

- 所有的增强方法，都可以根据需要添加参数JoinPoint，获取目标方法的信息

```java
@Before("pointcut()")
public void before(JoinPoint joinPoint) {
    String targetMethodName = joinPoint.getSignature().getName();
    System.out.println(targetMethodName);
}
```

- @AfterReturning的方法还可以获取目标方法的返回值

```java
// returning指定接收返回值的形参名
@AfterReturning(pointcut = "pointcut()", returning = "result")
public void afterReturning(JoinPoint joinPoint , Object result) {}
```

- @AfterThrowing的方法还可以获取目标方法的异常信息

```java
// throwing指定接收异常信息的形参名
@AfterThrowing(pointcut = "pointcut()", throwing = "t")
public void afterThrowing(Throwable t) {}
```

##### 配置切面类

```java
@Slf4j
// 表示当前类是一个切面类，里面可以定义各种通知
@Aspect
@Component
public class LogAspect {
    @Before("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public void before(JoinPoint joinPoint) {
        log.info("2.执行before");
    }

    // returning指定接收返回值的形参名
    @AfterReturning(pointcut = "com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()", returning = "result")
    public void afterReturning(Object result) {
        log.info("3.执行afterReturning");
    }

    // throwing指定接收异常信息的形参名
    @AfterThrowing(pointcut = "com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()", throwing = "t")
    public void afterThrowing(Throwable t) {
        log.info("3.执行afterThrowing");
    }

    @After("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public void after() {
        log.info("4.执行after");
    }

    /**
     * Spring框架提供了一个接口ProceedingJointPoint，该接口有一个proceed()方法，调用此方法相当于调用了切入点方法。
     * 该接口可以作为环绕通知的方法参数，在程序执行时，Spring框架会提供该接口的实现类以供使用。
     * Spring中的环绕通知，是Spring框架提供的一种可以在代码中手动控制增强方法何时执行的方式。
     */
    @Around("com.handle.springAopDemo.pointcut.ApplicationPointcut.pointcut()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object result = null;
        try {
            // 此处可以写前置通知代码
            log.info("1.执行aroundBefore");
            // 得到切入点方法执行所需参数
            Object[] args = joinPoint.getArgs();
            // 调用切入点方法
            result = joinPoint.proceed(args);
            // 此处可以写后置通知代码
            log.info("5.执行aroundAfterReturning");
        } catch (Throwable t) {
            // 此处可以写异常通知代码
            log.error("5.执行aroundThrowing");
            throw new RuntimeException(t);
        } finally {
            // 此处可以写最终通知代码
            log.info("6.执行aroundFinally");
        }
        return result;
    }
}
```

##### 支持注解配置（@Aspect声明的组件）

```java
// 跟xml配置方式的<aop:aspectj-autoproxy />一样
@EnableAspectJAutoProxy
public class ApplicationConfiguration {}
```

#### xml方式配置AOP（了解）

- xml方式配置

```xml
<!-- 1.配置包含切入点（方法）的Service对象 -->
<bean id="accountService" class="com.handle.application.service.impl.AccountServiceImpl" />

<!-- 2.配置通知类Logger的对象 -->
<bean id="logger" class="com.handle.application.logger.Logger" />

<!-- 3.配置AOP -->
<aop:config>
    <!-- 7.aop:pointcut写在aop:config标签内，并且在所有aop:pointcut标签前，则变成所有切面使用-->
    <aop:pointcut id="pointcut02" expression="execution(public void com.handle.application.service.impl.AccountServiceImpl.saveAccount())" />

    <!-- 4.配置切面，id为切面的唯一标识，ref为第二部配置的bean的id -->
    <aop:ascept id="logAdvice" ref="logger">
        <!-- 5.配置通知的类型为前置通知，并且建立通知方法和切入点（方法）的关联 -->
        <aop:before method="printLog" pointcut="execution(public void com.handle.application.service.impl.AccountServiceImpl.saveAccount())" />

        <!-- 6.第二种方法配置切入点表达式 -->
        <aop:before method="printLog" pointcut-ref="pointcut01" />

        <!-- aop:pointcut写在aop:ascept标签内只能是该切面使用-->
        <aop:pointcut id="pointcut01" expression="execution(public void com.handle.application.service.impl.AccountServiceImpl.saveAccount())" />
    </aop:ascept>
</aop:config>
```

#### Spring 5/Spring 6切面执行顺序

- 1.@Around环绕前
    - 2.@Before
        - 3.切入点
        - 4.@AfterReturning/（异常时，执行@AfterThrowing）
    - 5.@After
- 6.@Around环绕后/（异常时，执行@Around方法的catch代码，然后执行@Around方法的finally代码）

#### 切面优先级

```java
// 值越小，优先级越高，可以简单地把值当成执行顺序理解
@Order(1)
@Aspect
@Component
public class LogAspect {
}
```

### TX

- 不可重复读：一个事务读到另一个事务已经提交的update的数据，导致一个事务中多次查询结果不一致
- 幻读：一个事务读到另一个事务已经提交的insert的数据，导致一个事务中多次查询结果不一致

#### 注解配置事务管理器

```java
// 1.开启事务注解的支持
@EnableTransactionManagement
@Configuration
public class TransactionManagerConfiguration {
    @Bean
    public TransactionManager transactionManager(DataSource dataSource) {
        // 2.定义基于连接池的事务管理器实现（内部进行事务的操作）
        return new DataSourceTransactionManager(dataSource);
    }
}

// 3.添加事务用@Transactional，注解在类上时，给该类的所有方法添加事务；注解在方法上时，给该方法添加事务，并且覆盖类上的注解定义。
@Transactional
@Service
public class AccountService {
    @Autowired
    private AccountDao accountDao;
 
    // 4.也可以定义在方法上
    // @Transactional
    public void updateAccount(Long id, String name, boolean gender) {
        accountDao.updateNameById(name, id);
        accountDao.updateGenderById(gender, id);
    }
}
```

#### @Transactional的属性（事务的属性）

- readOnly，只读，默认false，当在类上添加事务注解时，如果该类有纯查询方法，可以在该查询方法上再次添加事务注解，设置为只读模式，提高效率

- timeout，事务超时时间（秒），默认-1（永不超时），超过时间会回滚事务和抛出异常

- rollbackFor，指定遇到什么异常才回滚，开发项目必须设置，一般设置为`Exception.class`，所有异常都回滚

- noRollbackFor，指定遇到什么异常不回滚（在回滚异常范围内，控制某个异常不回滚）

- isolation，事务隔离级别，根据使用场景设置，一般`Isolation.READ_COMMITTED`

- propagation，事务的传播行为（事务之间调用，如何影响子事务，事务的传播行为是设置到子事务的。如当一个事务里面包含另一个事务时）。
    - 增删改操作一般用默认的`Propagation.REQUIRED`，查询操作一般用`Propagation.SUPPORTS`
    - `Propagation.SUPPORTS`，如果父方法存在事务，则加入，否则自己以非事务方式执行
    - `Propagation.REQUIRED`，默认值，如果父方法有事务，加入；如果没有，就新建事务自己独立
    - `Propagation.REQUIRES_NEW`，无论父方法是否有事务，都新建事务自己独立
    - 在同一个类中，对注解@Transactional的方法调用，事务传播行为不会生效。因为Spring框架是使用代理模式实现事务机制的，但是同一个类中的方法调用不经过代理，而是通过对象方法的调用，不会被代理捕获，也就不产生事务传播行为的效果。

#### xml配置事务管理器

```xml
<bean id="txManager" class="...TransactionManager">

<aop:config>
    <!-- 配置通用切入点表达式 -->
    <aop:pointcut id="pointcut01" expression="execution(* com.handle.service.impl.*.*(..))" />
    <aop:aspect id="txAdvice" ref="txManager">
        <!-- 配置前置通知：开启事务 -->
        <aop:before method="beginTransaction", pointcut-ref="pointcut01" />
        <!-- 配置后置通知：提交事务 -->
        <aop:after-returning method="commitTransaction", pointcut-ref="pointcut01" />
        <!-- 配置异常通知：回滚事务 -->
        <aop:before method="rollbackTransaction", pointcut-ref="pointcut01" />
        <!-- 配置最终通知：释放连接 -->
        <aop:before method="releaseTransaction", pointcut-ref="pointcut01" />
    </aop:aspect>
</aop:config>
```

#### JdbcTemplate

- jdbcTemplate的增删改都是用update方法，不同的只有执行的sql

### Test

- 依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${spring.test.version}</version>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>${junit.jupiter.version}</version>
</dependency>
```

- 使用

```java
// 1.spring-test的注解，指定配置类，这个配置类可以不用写@Configuration
@SpringJUnitConfig(value = ApplicationConfiguration.class)
public class ApplicationTest {
    // 2.注入组件
    @Autowired
    private CalculatorService calculatorService;

    @Test
    public void test() {
        // 3.使用组件
        BigDecimal result = calculatorService.divide(new BigDecimal("2.2"), new BigDecimal("0.2"));
        System.out.println(result);
    }
}
```

### Spring Bean生命周期

- 反射创建bean实例
- bean属性赋值
- 初始化
    - 初始化前置处理：BeanPostProcessor接口的实现类对象调用postProcessBeforeInitialization()方法
    - 如果有定义@PostConstruct标记的方法，则执行
    - 如果实现了InitializingBean接口，则执行afterPropertiesSet()方法
    - 如果配置了initMethod()方法，则执行
    - 初始化后置处理：BeanPostProcessor接口的实现类对象调用postProcessAfterInitialization()方法
- 使用
- 销毁
    - 如果有定义@PreDestroy标记的方法，则执行
    - 如果实现了DisposableBean接口，则执行destroy()方法
    - 如果配置了destroyMethod()方法，则执行

## Spring MVC

### 组件

- 1.DispatcherServlet，所有请求都经过其处理分发，通过web.xml配置使其生效

- 2.HandlerMapping，内部缓存handler（controller方法）和handler访问路径数据，被DispatcherServlet调用，用于查找路径对应的handler，需要加入到IOC容器

- 3.HandlerAdapter，处理转换请求参数和响应数据，DispatcherServlet通过HandlerAdapter间接调用handler，HandlerAdapter是DispatcherServlet和handler之间的适配器，需要加入到IOC容器

- handler，是Controller的方法，用来接收请求参数，调用业务，最终返回响应

- ViewResolver，通过配置前缀和后缀，简化模板视图页面查找，对于前后端分离项目，后端只返回json数据，不返回页面，因此它不是必须的

### 初始化

#### 核心配置类

```java
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration {
    @Bean
    public RequestMappingHandlerMapping handlerMapping() {
        return new RequestMappingHandlerMapping();
    }
    
    @Bean
    public RequestMappingHandlerAdapter handlerAdapter() {
        return new RequestMappingHandlerAdapter();
    }
}
```

#### @EnableWebMvc

- 等同于xml配置中的<mvc:annotation-driven>

- 相当于添加了HandlerMapping、HandlerAdapter和json转换器

- 最终走MvcNamespaceHandler中的AnnotationDrivenBeanDefinitionParser的parse方法
    - 自动添加HandlerMapping
    - 自动添加HandlerAdapter
        - 在addRequestBodyAdvice方法中添加json处理器JsonViewRequestBodyAdvice
        - 在addResponseBodyAdvice方法中添加json处理器JsonViewResponseBodyAdvice

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration {}
```

#### WebMvcConfigurer

Spring MVC标准化配置类

- 除了handlerMapping、handlerAdapter、json转换器可以用@EnableWebMvc注解添加外，如果想要设置其它的mvc组件（如视图解析器），不用一个个写@Bean来添加，可以实现WebMvcConfigurer，里面提供了很多组件的默认实现方法，根据需要覆写相应方法即可添加

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration implements WebMvcConfigurer{
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/views/", ".jsp");
    }
}
```

#### 初始化类

- 初始化两个容器（所以至少两个配置类）
    - root容器是父容器，放service、mapper、mybatis等组件
    - web容器是子容器，放controller、web相关组件，web容器在FrameworkServlet中的createWebApplicationContext方法创建，通过setParent方法维护容器的父子关系
- 每当web项目启动，就会自动调用WebApplicationInitializer接口的onStartup方法，因此可以在这个方法里面定义一些初始化工作，如初始化IOC容器、DispatcherServlet

- AbstractAnnotationConfigDispatcherServletInitializer间接实现了WebApplicationInitializer

- 因此可以定义一个初始化类，继承AbstractAnnotationConfigDispatcherServletInitializer，做一些初始化工作

```java
public class SpringMvcInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    // 指定Root IOC容器配置类，设置service层、mapper层的IOC容器的配置
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }

    // 指定Web IOC容器配置类，设置springmvc相关的如HandlerMapping、HandlerAdapter、Controller层的IOC容器的配置
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] {SpringMvcConfiguration.class};
    }
    
    // 配置SpringMVC内部自带servlet的访问地址，处理所有请求
    @Override
    protected String[] getServletMappings() {
        return new String[] {"/"};
    }
}
```

### 路径设置

#### @RequestMapping

- 将路径注册到HandlerMapping，最终建立请求url和处理请求的方法之间的对应关系

- method，请求方式，常用RequMethod.GET/RequMethod.POST，请求方式不对报405异常

```java
// 一级请求路径
@RequestMapping("/application")
public class ApplicationController { 
    // 二级请求路径
    @RequestMapping("/hello")
    public String hello() {
        return "hello world";
    }
}
```

#### @GetMapping

只能用在方法上，同@RequestMapping("/application", method=RequMethod.GET)

#### @PostMapping

只能用在方法上，同@RequestMapping("/application", method=RequMethod.POST)

#### @CrossOrigin

同源策略：比较访问方和被访问方的域名（协议、IP、端口）是否一样，不一样的话浏览器会阻止访问，除非被访问方设置了允许跨域访问

```java
// 允许其它源访问这个controller，浏览器不拦截，也可加在handler上
@CrossOrigin
@RequestMapping("/application")
@RestController
public class ApplicationController { 
}
```

### 参数接收

#### 直接接收

- 形参列表参数名设置为和请求参数名一样

- 使用实体对象接收时，定义一个和请求参数名称对应属性的实体类，并且定义get/set方法即可

- 一个请求参数名包含多个值时，必须用集合接收，并且加注解@RequestParam

- 前端不传值也不会报错

```java
@GetMapping("/getAccount")
@ResponseBody
// url：localhost:8080/application/getAccount?name=handle&age=18
public String getAccount(String name, int age) {
    return "name = " + name + ", age = " + age;
}
```

#### @RequestParam

获取请求参数，把请求中指定名称的参数的值赋给控制器方法中的形参

- name/value，设置为和请求参数名一样，如果形参名和请求参数名一样，可以不写此属性

- required，前端是否必须传递此参数，为true时不传值报400异常

- defaultValue，可指定默认值

- 一个请求参数名包含多个值时，用集合接收即可

```java
 @GetMapping("/user/getRequestParams")
 public Map<String, Object> getRequestParams(
    @RequestParam("age") Integer age,
    @RequestParam("interest") List<String> interests,
    @RequestParam Map<String, String> allRequestParams) {
        Map<String, Object> map = new HashMap<>();
        map.put("age", age);
        map.put("interest", interests);
        map.put("allRequestParams", allRequestParams);
        return map;
    }
```

请求url：`localhost:8080/application/getRequestParams?age=18&interest=basketball&interest=movie`

方法返回：`{"interest": ["basketball","movie"],"allRequestParams": {"age": "18", "interest": "basketball"},"age": 18}`

#### @PathVariable

获取路径变量

```java
 @GetMapping("/user/{id}/{name}/getPathVariables")
 public Map<String, Object> getPathVariables(
    @PathVariable("id") Integer id,
    @PathVariable("name") String name,
    @PathVariable Map<String, String> allPathVariables) {
        Map<String, Object> map = new HashMap<>();
        map.put("id", id);
        map.put("name", name);
        map.put("allPathVariables", allPathVariables);
        return map;
    }
```

#### @RequestBody

获取请求体

系统会使用HttpMessageConverter或者自定义的HttpMessageConverter将请求的body中的json字符串转换为java对象

- 由于java原生api只支持路径参数和param参数（request.getParameter("key")），不支持json，所以用@RequestBody接收json数据时，需要导入json处理的依赖，并且在HandlerAdapter配置json转换器

```java
// json数据处理，必须用此注解，它会加入json处理器
// 相当于它给HandlerAdapter配置了json转换器
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration {}

@PostMapping("/user/getRequestBody")
public Map<String, Object> getRequestBody(@RequestBody String content) {
     Map<String, Object> map = new HashMap<>();
     map.put("content", content);
     return map;
}
```

#### @RequestHeader

获取请求头

```java
@PostMapping("/user/getHeaders")
public Map<String, Object> getHeaders(
    @RequestHeader("Host") String host,
    @RequestHeader Map<String, String> allHeaders) {
     Map<String, Object> map = new HashMap<>();
     map.put("host", host);
     map.put("allHeaders", allHeaders);
     return map;
}
```

#### @CookieValue

获取 cookie 值

```java
// 1.设置cookie
@PostMapping("/setCookies")
public String setCookies(HttpServletResponse response) {
    Cookie cookie = new Cookie("userName", "handle");
    // set to -1 will be treated as a session cookie by the browser
    // expires in 7 days
    cookie.setMaxAge(7 * 24 * 60 * 60); 
    response.addCookie(cookie);
    // 加密传输到server，https连接有效
    cookie.setSecure(true);
    // it is not accessible to the client scripts
    // For example, you can not use the Document.cookie property to access HttpOnly cookies in JavaScript
    // This is one way to secure a cookie from being changed by malicious code or cross-site scripting (XSS) attacks
    cookie.setHttpOnly(true);
    // The Path attribute specifies a URL path for which the cookie should be sent to the server. 
    // By explicitly setting the Path directive, the cookie will be delivered to the specified URL and all of its subdirectories
    // By default, if no path is specified, a cookie is only sent to the server for the URL that was used to set it in the browser.
    // global cookie accessible every where for the current domain
    cookie.setPath("/"); 
    return "set cookie success";
}

// 2.获取cookie
@PostMapping("/getCookies")
public Map<String, Object> getCookies(
    @CookieValue(value = "id", defaultValue = "426353") id,
    @CookieValue Cookie cookie,
    HttpServletRequest request) {
    Cookie[] cookies = request.getCookies();
    if (cookies != null) {
        return Arrays.stream(cookies)
                .map(c -> c.getName() + "=" + c.getValue()).collect(Collectors.joining(", "));
    }

    Map<String, Object> map = new HashMap<>();
    map.put("id", id);
    map.put("cookie", cookie);
    map.put("cookies", cookies);
    return map;
}

// 3.删除cookie
@PostMapping("/deleteCookies")
public String deleteCookies(HttpServletResponse response) {
    // To delete a cookie, you need to create a new instance of the Cookie class with the same name 
    // and the Max-Age directive to 0
    // and add it again to the response
    Cookie cookie = new Cookie("userName", null);
    cookie.setMaxAge(0); 
    response.addCookie(cookie);
    cookie.setSecure(true);
    cookie.setHttpOnly(true);
    cookie.setPath("/"); 
    return "delete cookie success";
}
```

#### @RequestAttribute

- 获取 request 的属性的值

```java
@Controller
@RequestMapping("/application")
public class ApplicationController {
    @GetMapping("/gotoDestination")
    public String gotoDestination(HttpServletRequest request) {
        // 设置request属性值
        request.setAttribute("code", "200");
        request.setAttribute("message", "success");

        // 转发到/destination
        return "forward:/application/destination";
    }

    @ResponseBody
    @GetMapping("/destination")
    public Map<String, Object> getDestination(
        @RequestAttribute("message") String message, HttpServletRequest request) {
        Map<String, Object> map = new HashMap<>();
        map.put("code", request.getAttribute("code"));
        map.put("message", message);

        // {"code":"200", "message":"success"}
        return map;
    }
}
```

#### @MatrixVariable

- 获取矩阵变量的值

```java
/**
 * 路径变量配置
 */
@Configuration(proxyBeanMethods = false)
public class ApplicationConfiguration {
    @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void configurePathMath(PathMatchConfigurer configurer) {
                UrlPathHelper urlPathHelper = new UrlPathHelper();
                // 不移除路径变量中分号后面的内容（矩阵变量）
                urlPathHelper.setRemoveSemicolonContent(false);

                configurer.setUrlPathHelper(urlPathHelper);
            }
        };
    }
}
```

```java
/**
 *  获取矩阵变量的值
 */
@GetMapping("/user/{bossAge}/{empAge}/getMatrixVariables")
public Map<String, Object> getMatrixVariables(
    @MatrixVariable(value = "age", pathVar = "bossAge") Integer bossAge,
    @MatrixVariable(value = "age", pathVar = "empAge") List<Integer> empAge
) {
    Map<String, Object> map = new HashMap<>();
    map.put("bossAge", bossAge);
    map.put("empAge", empAge);
    return map;
}
```

请求：localhost:8080/application/user/boss;age=23/emp;age=18;age=19,20,21/getMatrixVariables
响应：

```json
{
    "bossAge":23,
    "empAge":[18,19,20,21]
}
```

#### Spring MVC 原生对象获取

- 只要在对应的控制器方法添加形参即可获取到

```java
@RequestMapping("/hello")
public String hello(HttpServletRequest request, 
                    HttpServletResponse response,
                    HttpSession session,
                    InputStream inputStream,
                    OutputStream outputStream,
                    Reader reader,
                    Writer writer
                    ) {
    return "hello world";
}
```

- ServletContext获取

ServletContext是最大的配置文件，全局最大共享域，核心api为getRealPath

```java
// 1.DI获取
@Autowired
private ServletContext servletContext;

// 2.request和session获取
@RequestMapping("/hello")
public String hello(HttpServletRequest request, 
                    HttpSession session
                    ) {
    ServletContext servletContext = request.getServletContext();
    ServletContext servletContext2 = session.getServletContext();                      
    return "hello world";
}
```

#### 共享域对象操作

- 原生共享域：request、session、servletContext

- springmvc提供的request级别的共享域：model、modelMap、map、modelAndView

```java
@Autowired
private ServletContext servletContext;

@RequestMapping("/hello")
public ModelAndView hello(HttpServletRequest request, 
                    HttpSession session,
                    Model model, 
                    ModelMap modelMap,
                    Map<String, Object> map
                    ) {
    request.setAttribute("key", "value");
    model.addAttribute("key", "value");
    modelMap.addAttribute("key", "value");
    map.put("key", "value");

    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("key", "value");
    modelAndView.setViewName("视图（页面）名称");
    return modelAndView;
}
```

### 结果响应

#### @ResponseBody

- 结果（json数据）放入响应体直接返回，不走视图解析器，转发和重定向都失效

- 在类上注解

```Java
@ResponseBody
public class ApplicationController {}
```

- 在方法上加注解

```java
public class ApplicationController {
    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "hello world";
    }
}
```

#### @RestController

@RestController = @Controller + @ResponseBody

#### 转发和重定向

- 转发只能转发到项目下的资源

- 重定向可以重定向到项目下的资源，也可以重定向到项目外的资源

```java
@Controller
@RequestMapping("/application")
public class ApplicationController {
    @ResponseBody
    @GetMapping("/destination")
    public String destination() {
        return "destination";
    }

    @GetMapping("/forward")
    public String forward() {
        // 转发到/destination
        return "forward:/application/destination";
    }

    @GetMapping("/redirect")
    public String redirect() {
        // 重定向到/destination，项目内资源
        return "redirect:/application/destination";
        // 重定向到百度，项目外资源
        return "redirect:http://www.baidu.com";
    }
}
```

#### 静态资源

- MvcNamespaceHandler解析default-servlet-handler，由DefaultServletHandlerBeanDefinitionParser的parse方法处理
    - 往IOC容器添加了DefaultServletHttpRequestHandler（可以把它当成另一个专门处理静态资源的HandlerMapping）
        - handleRequest方法中，当没有找到handler时，对请求进行了转发，去找静态资源

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration implements WebMvcConfigurer{
    /**
     * 开启静态资源查找，同<mvc:default-servlet-handler />
     * dispatcherServlet根据请求路径调用handlerMapping查找有没有对应的handler，如果没有，再找静态资源
     */
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
}
```

### 全局异常处理

- @ControllerAdvice，异常处理器，全局异常发生，会走此类的handler逻辑，可以返回视图、转发和重定向

- @RestControllerAdvice = @ControllerAdvice + @ResponseBody，返回json

```java
// 1.声明全局异常处理类
@RestControllerAdvice
public class GlobalExceptionHandler {
    // 2.声明异常处理方法
    @ExceptionHandler(RuntimeException.class)
    public ResultVo<String> runtimeExceptionHandler(RuntimeException exception) {
        String errorMessage = exception.getLocalizedMessage();
        if (StringUtils.isBlank(errorMessage)) {
            errorMessage = getErrorMessage(exception);
        }
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), errorMessage);
    }
 
    @ExceptionHandler(Exception.class)
    public ResultVo<String> runtimeExceptionHandler(Exception exception) {
        String errorMessage = exception.getLocalizedMessage();
        if (StringUtils.isBlank(errorMessage)) {
            errorMessage = getErrorMessage(exception);
        }
        return ResultVo.failure(ResultCodeEnum.BAD_REQUEST.getCode(), errorMessage);
    }
 
    public static String getErrorMessage(Exception exception){
        String errorMessage = exception.getLocalizedMessage();
        if (StringUtils.isBlank(errorMessage)) {
            StringWriter stringWriter = new StringWriter();
            PrintWriter printWriter = new PrintWriter(stringWriter);
            exception.printStackTrace(printWriter);
            errorMessage = stringWriter.toString();
            if (errorMessage.length() > 500) {
                errorMessage = errorMessage.substring(0, 500);
            }
        }
        return errorMessage;
    }
}
```

### Spring MVC拦截器

- DispatcherServlet的doDispatch方法内
    - getHandler，获取handlerMapping
    - getHandlerAdapter，获取handlerAdapter
    - applyPreHandle，执行拦截器的preHandle
        - 正序遍历拦截器集合，依次调用preHandle
    - handle，调用handler
    - applyPostHandle，执行拦截器的postHandle
        - 倒序遍历拦截器集合，依次调用postHandle
    - processDispatchResult
        - triggerAfterCompletion，执行拦截器的afterCompletion
            - 倒序遍历拦截器集合，依次调用afterCompletion
- 1.定义拦截器

```java
public class LoginInterceptor implements HandlerInterceptor {
    // 在执行控制器的handler方法前执行
    // 形参中的handler就是控制器的handler方法
    // 可用来编码格式设置，登录保护，权限处理
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 返回true，放行，返回false，不放行
        return true;
    }
    
    // 在控制器的handler方法执行完毕后执行
    // preHandle返回false或handler报错不执行
    // 可用来处理结果，敏感词检查
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {
        // todo
    }
    
    // 整体处理完毕后执行
    // 可用来做响应计数
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception e) {
        // todo
    }
}
```

- 2.将拦截器加入spring mvc的IOC容器

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.handle.application")
public class SpringMvcConfiguration implements WebMvcConfigurer{
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                // 可设置只拦截指定地址，用通配符*表示任意一层，**表示任意多层
                .addPathPatterns("/account/**")
                // 可设置排除拦截地址，排除拦截地址应该在拦截地址范围内
                .excludePathPatterns("/account/getAccountById");
        // 可添加多个拦截器，先添加的优先级高
        registry.addInterceptor(new LogInterceptor());
    }
}
```

### 参数校验

- Bean Validation 是一套定义 JavaBean 参数校验标准的规范 (JSR 303, 349, 380)，它提供了一系列注解，可以直接用于 JavaBean 的属性上，从而实现便捷的参数校验
    - JSR 303 (Bean Validation 1.0): 奠定了基础，引入了核心校验注解（如 @NotNull、@Size、@Min、@Max 等），定义了如何通过注解的方式对 JavaBean 的属性进行校验，并支持嵌套对象校验和自定义校验器。
    - JSR 349 (Bean Validation 1.1): 在 1.0 基础上进行扩展，例如引入了对方法参数和返回值校验的支持、增强了对分组校验（Group Validation）的处理。
    - JSR 380 (Bean Validation 2.0): 拥抱 Java 8 的新特性，并进行了一些改进，例如支持 java.time 包中的日期和时间类型、引入了一些新的校验注解（如 @NotEmpty, @NotBlank等）。

Bean Validation 本身只是一套规范（接口和注解），我们需要一个实现了这套规范的具体框架来执行校验逻辑

目前，Hibernate Validator 是 Bean Validation 规范最权威、使用最广泛的参考实现

需要注意的是：所有的注解，推荐使用 JSR 注解，即`javax.validation.constraints`，而不是`org.hibernate.validator.constraints`

- springboot依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

- 非 SpringBoot 项目需要自行引入相关依赖包
    - Hibernate Validator 6.x 及更高版本实现了 Bean Validation 2.0 (JSR 380)

```xml
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>8.0.1.Final</version>
</dependency>
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator-annotation-processor</artifactId>
    <version>8.0.1.Final</version>
</dependency>
```

#### 验证请求体

当 Controller 方法使用 @RequestBody 注解来接收请求体并将其绑定到一个对象时，可以在该参数前添加 @Valid 注解来触发对该对象的校验。

如果验证失败，它将抛出MethodArgumentNotValidException。

##### 定义实体类

```java
@Getter
@Setter
@ToString
public class User {
    private Long id;

    @NotBlank
    private String name;

    @Length(min = 6, max = 16)
    private String password;

    @Min(1)
    private int age;

    @Email
    private String email;

    @Past
    private LocateDate birthday;
}
```

##### 定义控制器

```java
@RequestMapping("/user")
@RestController
public class UserController {
    @PostMapping("/updateUser")
    public void updateUser(@RequestBody @Valid User user) {
        // todo
    }
}
```

```java
// 下面的@Validated注解是之前的笔记，应该是不规范的，没有验证过，先留着吧
@RequestMapping("/user")
@RestController
public class UserController {
    // 还可以捕捉校验错误信息，BindingResult必须紧挨着校验对象
    @PostMapping("/updateUser")
    public void updateUser(@Validated @RequestBody User user, BindingResult result) {
        if (result.hasErrors()) {
            // todo
        }
    }
}
```

#### 验证请求参数

- 对于直接映射到方法参数的简单类型数据（如路径变量 @PathVariable 或请求参数 @RequestParam），校验方式略有不同：
    - 在 Controller 类上添加 `@Validated` 注解：这个注解是 Spring 提供的（非 JSR 标准），它使得 Spring 能够处理方法级别的参数校验注解。这是必需步骤。
    - 将校验注解直接放在方法参数上：将 @Min, @Max, @Size, @Pattern 等校验注解直接应用于对应的 @PathVariable 或 @RequestParam 参数。

```java
// 在类上添加@Validated注解
@Validated
@RequestMapping("/user")
@RestController
public class UserController {
    @PostMapping("/getUserByName")
    public void getUserByName(
        @RequestParam("name")
        @NotBlank(message = "姓名不能为空")
        @Size(max = 10, message = "姓名长度不能超过 10")
        String name) {
        // todo
    }
}
```

## Spring Boot

- 依赖

```xml
<!-- 适用于聚合项目 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring.boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- 适用于单体项目 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
</parent>
```

### 启动类

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        // 加载配置，创建IOC容器，启动内置web服务器
        SpringApplication.run(Application.class, args);
    }
}
```

### 根据需要修改主启动类主函数

```java
/**
 * 启动项目后会生成存放 pid 的 application.pid 文档
 * pid存放到指定目录：在application.properties(yaml)配置文档中添加spring.pid.file=/var/log/app.pid
 */
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(Application.class);
    // 生成进程pid文档
    app.addListeners(new ApplicationPidFileWriter());

    // 关闭banner
    app.setBannerMode(Banner.Mode.OFF);
    app.run(args);
}
```

### 常用注解

#### @SpringBootApplication

- @SpringBootApplication = @SpringBootConfiguration + @EnableAutoConfiguration + @ComponentScan

- @SpringBootConfiguration = @Configuration

- @EnableAutoConfiguration 自动加载其它的配置类

- @ComponentScan，默认扫描当前类所在包及其子包

#### `@ConfigurationProperties`

- 作用：配置绑定，将类中所有属性和配置文件中的相关配置进行绑定，默认从application.properties(.yaml)文件中获取值，不用写@Value
  
- 属性：prefix：取前缀为xxx的属性

- 此注解所在的类必须是一个组件，在类上注解

- 可以给集合类型赋值

- 如果是properties文件，并且内容有中文时需要用@PropertySource指定编码格式，并且文件名不能是application.properties

假设配置文件application.yaml存在配置

```yaml
user01:
    name: 张三
```

##### ConfigurationProperties用法1

- @Component + @ConfigurationProperties

```java
@Component
@ConfigurationProperties(prefix = "user01")
@Setter
public class UserDO {
    private String name;
}
```

##### ConfigurationProperties用法2

- @Configuration + @ConfigurationProperties

```java
@Configuration
@ConfigurationProperties(prefix = "user01")
@Setter
public class UserDO {
    private String name;
}
```

##### ConfigurationProperties用法3

- @Configuration + @ConfigurationProperties

```java
@Configuration
public class ApplicationConfiguration {
    // 会自动将配置文件中对应的属性设置到UserDO，然后加入到IOC容器
    @ConfigurationProperties(prefix = "user01")
    @Bean
    public UserDO userDO() {
        return new UserDO();
    }
}
```

##### ConfigurationProperties用法4

- @Configuration + @EnableConfigurationProperties + @ConfigurationProperties

```java
@Setter
@ConfigurationProperties(prefix = "user01")
public class UserDO {
    private String name;
}

@Configuration
@EnableConfigurationProperties(UserDO.class)
public class MainConfiguration {}
```

#### `@ImportResource`

- 导入spring的xml配置文件，让其生效
- 可以标注在主启动类上

#### `@Mapper`

- 在DAO接口上注解

```java
@Mapper
public interface UserDAO {
 List<User> findAll();
}
```

#### `@MapperScan`

如果嫌每个DAO接口都加@Mapper注解太麻烦，可以在主启动类上面加上注解@MapperScan，就会自动扫描指定的包下的DAO

```java
@MapperScan("com.handle.application.dao")
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

#### `@InitBinder`

为当前控制器注册一个属性编辑器，只对当前Controller有效，参数webDataBinder是用于表单到方法的数据绑定的

#### 自定义数据库连接属性并配置

- /config/jdbc.properties

```properties
jdbc.mysql.driver = com.mysql.cj.jdbc.Driver
jdbc.mysql.url = jdbc:mysql://localhost:3306/hr?serverTimezone=GMT%2B8
jdbc.mysql.username = root
jdbc.mysql.password = mysql123
```

- 从jdbc.properties文件获取属性

```java

// @PropertySource：加载指定配置文件,第二個路徑為另一個jdbc.properties在磁盤上（jar包外）的路徑

// @ConstructorBinding 构造函数注入只能提取application.properties(.yaml)文件中的值
// 想要提取别的配置文件中的值，需要通过@Component，同时必须设置getter和setter
@Getter
@ConstructorBinding
@PropertySource(value = {"classpath:config/jdbc.properties", "file:${spring.profiles.path}/jdbc.properties"}, ignoreResourceNotFound = true)
@ConfigurationProperties(prefix = "jdbc.mysql")
public class JdbcProperties {
    private String driver;

    private String url;

    private String username;

    private String password;

    public JdbcProperties(String driver, String url, String username, String password) {
        this.driver = driver;
        this.url = url;
        this.username = username;
        this.password = password;
    }
}
```

- 从properties类的对象获取属性来配置连接池

```java
@Configuration
@EnableConfigurationProperties(JdbcProperties.class)
public class JdbcConfig {
    // @Bean 将方法的返回值添加到容器之中,并且容器中这个组件的id就是方法名
    // 此处作为参数的JdbcProperties jdbcProperties 直接从容器中捞到值
    @Bean
    public DruidDataSource dataSource(JdbcProperties properties) {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(properties.getDriver());
        druidDataSource.setUrl(properties.getUrl());
        druidDataSource.setUsername(properties.getUsername());
        druidDataSource.setPassword(properties.getPassword());
        return druidDataSource;
    }

}
```

### 测试

- maven依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

- 测试类

```java
@SpringBootTest
public class ApplicationTest {
    @Autowired
    private User user;
 
    @Test
    public void test() {
    System.out.println(user);
    }
}

```

### 获取应用所在目录

- 用ApplicationHome

```java
// 底层代码还是调用了getProtectionDomain，但做了修正，更可靠稳定
ApplicationHome home = new ApplicationHome(Application.class);

// java -jar file.jar为：/path/to/file.jar
// ide为：/path/to/target/classes
File jarFile = home.getSource();

// java -jar file.jar为：/path/to
// ide为：/path/to/target/classes，已经确认，不是输出错了
File dir = home.getDir();
```

- 用getProtectionDomain

```java
// java -jar file.jar为：/path/to/app.jar
// ide为：/path/to/project/target/classes
String jarPath = new File(
    Application.class.getProtectionDomain()
        .getCodeSource()
        .getLocation()
        .toURI()
).getAbsolutePath();
```

### 整合AOP

- 导入如下依赖就可以用@Aspect注解声明切面了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### 整合声明式事务

- 导入如下依赖就可以用@Transactional注解声明事务了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

### 整合Spring MVC

- 导入如下依赖就可以了，Spring MVC组件、全局异常和拦截器和以前一样使用

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### 整合Mybatis

- 依赖

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
<!-- 其包含了spring-boot-starter-jdbc -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
</dependency>
```

- 启动类

```java
// mapper.java所在包
@MapperScan("com.handle.application.dao")
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 配置文件

```conf
# 数据库设置，千万不要设置到hikari目录下
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/handle
spring.datasource.username=postgres
spring.datasource.password=postgres123

# 指定mapper.xml文件位置
mybatis.mapper-locations=classpath:mapper/**/*.xml
# 设置实体别名的包位置
mybatis.type-aliases-package=com.handle.application.pojo
# 设置表字段名-实体属性自动映射：account_id accountId 自动映射
mybatis.configuration.map-underscore-to-camel-case=true
# 设置对于resultMap标签，无论有没有嵌套（association/collection）都自动映射result标签的列名和属性
mybatis.configuration.auto-mapping-behavior=full
# 设置日志输出实现
mybatis.configuration.log-impl=org.apache.ibatis.logging.slf4j.Slf4jImpl
```

### 打包

- spring boot原文：
    - The spring-boot-starter-parent POM includes <executions> configuration to bind the repackage goal.
    - If you do not use the parent POM, you need to declare this configuration yourself.

- 依赖

```xml
<build>
    <!-- 声明插件版本，供子模块使用（不用写version），从而实现版本锁定 -->
    <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring.boot.version}</version>
                    <!-- 子项目只需添加groupId、artifactId，executions对所有子项目生效 -->
                    <executions>
                        <execution>
                            <goals>
                                <goal>repackage</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
            </plugins>
    </pluginManagement>
</build>
```

#### 发布

- 1.复制jre、可执行jar文档、run.bat（springboot项目看需求创建config文档夹，里面放application.properties配置文档），到同一目录下即可
- 2.运行run.bat快速启动可执行jar文档

### 命令启动

```sh
java -jar [选项] [参数] <jar文件名>
```

- 指定系统属性-D<属性名>=<属性值>，可以通过System.getProperty()在应用程序获取该属性值
    - 指定服务器端口：`-Dserver.port=80`
    - 指定要激活的配置文件：`-Dspring.profiles.active=<dev/test/prod>`

- 指定JVM参数`-X`
    - 设置最小堆内存：`-Xms1024m`
    - 设置最大堆内存：`-Xmx1024m`

### Spring Boot项目安装ssl证书，用https访问

```yaml
server:
    port: 8443
    ssl:
        enabled: true
        # 记得将keystore文件复制到resources目录下
        key-store: classpath:yourKeystore.p12
        key-store-password: changeit
        key-store-type: PKCS12
        # keystore文件只包含一个证书的时候可以不用写别名
        key-alias: yourKeystoreAlias
```

## Spring AI

Spring AI的版本选择也是要考虑跟Spring Boot版本兼容的

### ChatModel 和 ChatClient

ChatModel 和 ChatClient 是两个核心的抽象，它们分别代表了底层交互接口和高级应用封装

简单来说，ChatModel 是直接与底层大语言模型（LLM）通信的基础设施

而 ChatClient 则是基于 ChatModel 构建的高级 API，旨在简化复杂 AI 应用的开发

- ChatModel（底层对话模型）：
    - 它是直接与具体大语言模型交互的底层接口，提供基础的 call() 和 stream() 方法
    - 工作原理是接收 Prompt 作为输入，发送给后端大模型，并接收 ChatResponse 作为输出
    - 适合简单的模型交互场景或需要精细控制底层参数的模型实验

- ChatClient（高级聊天客户端）：
    - 它是基于 ChatModel 构建的高级封装，提供了流畅的链式 API（Fluent API）
    - 类似于应用程序开发中的“服务层”，它将与 LLM 及其他组件（如提示词模板、聊天记忆、RAG 组件等）交互的复杂性隐藏在背后，开发者可以快速组装一整套 AI 交互流程
    - 适合快速构建标准化的复杂 AI 服务和业务接口

- bom

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-bom</artifactId>
    <version>${spring.ai.bom.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

### 以Ollama的chat模型为例使用Spring AI

- 依赖

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-ollama</artifactId>
</dependency>
```

- 配置

```properties
# 强制服务器端用UTF-8编码处理对话
# 只对请求生效，对响应不生效的
server.servlet.encoding.enabled=true
server.servlet.encoding.force=true
server.servlet.encoding.charset=UTF-8

# ollama的本地服务器地址
spring.ai.ollama.base-url=http://localhost:11434
# 使用的模型名称，ollma list命令列出的模型名称复制过来就行了
spring.ai.ollama.chat.model=gemma4:e4b-it-qat
```

- 控制器，使用ChatClient

```java
@RestController
class MyController {

    private final ChatClient chatClient;

    //  Spring AI自动装配了一个ChatClient.Builder
    public MyController(ChatClient.Builder chatClientBuilder) {
        this.chatClient = chatClientBuilder.build();
    }

    @GetMapping("/ai")
    String generation(String userInput) {
        return this.chatClient.prompt()
            .user(userInput)
            // 发送一个请求到AI模型
            .call()
            // 将AI模型的响应以String返回
            .content();
    }
}
```

- 控制器，使用ChatModel

```java
@RestController
public class ApplicationController {
    @Autowired
    private OllamaChatModel chatModel;

    @GetMapping("/ai/generate")
    public Map<String, String> generate(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return Map.of("generation", this.chatModel.call(message));
    }

    //  produces = "text/html;charset=utf-8"将返回的数据用utf-8编码
    @GetMapping(value = "/ai/generateStream", produces = "text/html;charset=utf-8")
    public Flux<String> generateStream(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return chatModel.stream(message);
        // 下面这种写法是等价的
        // Prompt prompt = new Prompt(new UserMessage(message));
        // return chatModel.stream(prompt).map(response -> response.getResult().getOutput().getText());
    }
}
```

### 以OpenAI API为例使用Ollama的chat模型

Ollama还兼容OpenAI接口，使用demo如下

- maven依赖

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-openai</artifactId>
</dependency>
```

- 配置

```properties
# ollama本身没有apiKey，但是又不能为空，随便填个值上去就行了
# 对于其它模型供应商的apiKey，应该设置到vault或者环境变量中，然后通过占位符获取如${API_KEY}
# 这里springai有个问题，官方文档里面说spring.ai.openai.chat.api-key是会覆盖spring.ai.openai.api-key的
# 但是启动的时候，创建openAiSdkAudioSpeechModel bean时，还是会读spring.ai.openai.api-key，启动报错
#spring.ai.openai.chat.api-key=ollama
spring.ai.openai.api-key=ollama
# ollama的本地服务器地址，只要不是openai的，貌似都要加v1
spring.ai.openai.chat.base-url=http://localhost:11434/v1
# 使用的模型名称，ollma list命令列出的模型名称复制过来就行了
spring.ai.openai.chat.model=gemma4:e2b-it-qat
```

```java
@RestController
public class ApplicationController {
    @Autowired
    private OpenAiChatModel chatModel;

    private final ChatClient chatClient;

    @Autowired
    public ApplicationController(OpenAiChatModel chatModel) {
        this.chatClient = ChatClient.create(chatModel);
    }

    @GetMapping("/chatModelCall")
    public Map<String, String> chatModelCall(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        // Use the model with thinking-capable models
        ChatResponse response = chatModel.call(new Prompt(message));

        AssistantMessage assistantMessage = response.getResult().getOutput();

        // Access the reasoning process from metadata
        String reasoning = Optional.ofNullable(assistantMessage)
                                   .map(AbstractMessage::getMetadata)
                                   .map(map -> map.get("reasoningContent"))
                                   .map(reasoningContent -> {
                                       if (reasoningContent instanceof String s) {
                                           return s;
                                       }
                                       return null;
                                   })
                                   .orElse("");

        // Get the final answer
        String answer = Optional.ofNullable(assistantMessage).map(AssistantMessage::getText).orElse("");

        Map<String, String> map = new HashMap<>();
        map.put("Reasoning", reasoning);
        map.put("Answer", answer);
        map.put("Timestamp", String.valueOf(Instant.now().toEpochMilli()));
        return map;
    }

    @GetMapping(value = "/chatModelStream", produces = "text/html;charset=utf-8")
    public Flux<String> chatModelStream(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return chatModel.stream(message);
    }

    @GetMapping("/chatClientCall")
    public Map<String, String> chatClientCall(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        // Use the model with thinking-capable models
        ChatResponse response = chatClient.prompt().user(message).call().chatResponse();

        AssistantMessage assistantMessage = response.getResult().getOutput();

        // Access the reasoning process from metadata
        String reasoning = Optional.ofNullable(assistantMessage)
                                   .map(AbstractMessage::getMetadata)
                                   .map(map -> map.get("reasoningContent"))
                                   .map(reasoningContent -> {
                                       if (reasoningContent instanceof String s) {
                                           return s;
                                       }
                                       return null;
                                   })
                                   .orElse("");

        // Get the final answer
        String answer = Optional.ofNullable(assistantMessage).map(AssistantMessage::getText).orElse("");

        Map<String, String> map = new HashMap<>();
        map.put("Reasoning", reasoning);
        map.put("Answer", answer);
        map.put("Timestamp", String.valueOf(Instant.now().toEpochMilli()));
        return map;
    }

    @GetMapping(value = "/chatClientStream", produces = "text/html;charset=utf-8")
    public Flux<String> chatClientStream(@RequestParam(value = "message", defaultValue = "讲个笑话给我听") String message) {
        return chatClient.prompt().user(message).stream().content();
    }
}
```

### chat模型和embedding模型

spring的官方文档只说了ollama的chat 模型是openai api兼容的

因此如果要使用ollama的embedding模型，目前最好还是使用ollama api吧

### 方法作为工具来调用

工具调用分为两种：信息检索和采取行动

信息检索：如获取当前日期时间和天气，然后模型润色后返回

采取行动：执行模型生成的计划，如发送邮件

有两种工具声明的方式，注解方式和编程方式（一步一步创建必要对象）

下面只介绍注解的方式

#### `@Tool`注解

- 属性
    - name：工具名称，如果不指定，会使用方法名
    - description：工具描述，让模型了解该工具是干嘛的和如何使用该工具
        - 如果不指定，会使用方法名，设置详细的描述是非常有必要并且至关重要
    - returnDirect：工具调用结果是直接返回给客户端还是传给模型
    - resultConverter：ToolCallResultConverter实现，用来转换工具调用结果为String类型的对象返回给模型

- 注解的方法可以是静态方法或实例方法

- 注解的方法的可以是任何可见性（public, protected, package-private, or private）

- 注解的方法可以有0个或任意参数，参数类型可以是绝大多数类型

- 注解的方法可以没有返回类型（void）或绝大多数返回类型
    - 如果有返回类型，则该返回类型必须是可序列化的，因为返回值会被序列化传给模型

- 注解的方法的所属类可以是顶级类或内部类，这个类可以是任何可见性，只要实例化的地方是可访问的就行

- 注解的方法的所属类只要它是一个Spring bean，它就支持AOT 编译，不用额外为GraalVM编译器做配置
    - 否则，你需要在这个类加上注解`@RegisterReflection(memberCategories = MemberCategory.INVOKE_DECLARED_METHODS)`
    - 可以根据需要决定是否将其定义为Spring bean

- 注解的方法的参数类型和返回值类型限制，不可以是如下类型
    - Optional
    - 异步类型 (e.g. CompletableFuture, Future)
    - 响应式类型 (e.g. Flow, Mono, Flux)
    - 函数式类型(e.g. Function, Supplier, Consumer)
        - 其实通过使用基于函数式的工具规范来实现，是可以支持函数式类型的

```java
@Component
publci class DateTimeTools {
    @Tool(description = "Get the current date and time in the user's timezone")
    public String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }
}
```

#### `@ToolParam`注解

`@Tool`注解的方法的参数还可以添加`@ToolParam`注解

用于提供额外的参数信息，如参数描述，是可选还是必须，默认是必须的

- 属性
    - description：参数描述，可以让模型更好地了解如何去使用
    - required：参数是可选还是必须，默认是必须的

- 参数如果注解了`@Nullable`，它将被认为是可选的，除非用`@ToolParam`标识为必须了

```java
class DateTimeTools {
    @Tool(description = "Set a user alarm for the given time")
    void setAlarm(@ToolParam(description = "Time in ISO-8601 format") String time) {
        LocalDateTime alarmTime = LocalDateTime.parse(time, DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("Alarm set for " + alarmTime);
    }
}
```

#### 将工具添加到ChatClient

如果使用的是`@Tool`注解方式声明的工具

当调用ChatClient时，给tools()方法传一个该工具类的实例就可以了

```java
ChatClient.create(chatModel)
    .prompt("What day is tomorrow?")
    .tools(new DateTimeTools())
    .call()
    .content();
```

##### ToolCallback

在底层，ChatClient会为工具类实例的每一个`@Tool`注解的方法生成一个ToolCallback并传给模型

当然你也可以使用ToolCallbacks工具类手动完成这一步

```java
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());

ChatClient.create(chatModel)
    .prompt("What day is tomorrow?")
    .tools(dateTimeTools)
    .call()
    .content();
```

除此之外tools()方法还接收ToolCallbackProvider实例

##### 将默认工具添加到ChatClient

defaultTools()指定的默认工具，对于同一个ChatClient.Builder创建的ChatClient是通用的，因此使用要特别小心

当同时用defaultTools()和tools()指定时，后者会覆盖前者

```java
ChatModel chatModel = ...
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(new DateTimeTools())
    .build();
```

#### 将工具添加到ChatModel

```java
ChatModel chatModel = ...
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(dateTimeTools)
    .build();
Prompt prompt = new Prompt("What day is tomorrow?", chatOptions);
chatModel.call(prompt);
```

##### 将默认工具添加到ChatModel

```java
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
ChatModel chatModel = OllamaChatModel.builder()
    .ollamaApi(OllamaApi.builder().build())
    .options(ToolCallingChatOptions.builder()
            .toolCallbacks(dateTimeTools)
            .build())
    .build();
```

### 函数式作为工具来调用

- 限制，以下类型不支持
    - 基本数据类型
    - Optional
    - 集合类型(e.g. List, Map, Array, Set)
    - 异步类型(e.g. CompletableFuture, Future)
    - 响应式类型 (e.g. Flow, Mono, Flux)

- WeatherService

```java
public class WeatherService implements Function<WeatherRequest, WeatherResponse> {
    public WeatherResponse apply(WeatherRequest request) {
        return new WeatherResponse(30.0, Unit.C);
    }
}

public enum Unit { C, F }
public record WeatherRequest(String location, Unit unit) {}
public record WeatherResponse(double temp, Unit unit) {}
```

- 定义ToolCallback bean

```java
@Configuration(proxyBeanMethods = false)
class WeatherTools {
    WeatherService weatherService = new WeatherService();

    @Bean
    ToolCallback currentWeather() {
        return FunctionToolCallback.builder("currentWeather", weatherService::getWeather)
        .description("Get the weather in location")
        .inputType(WeatherRequest.class)
        .build();
    }
}
```

- 使用ToolCallback bean

```java
@Autowired
ToolCallback currentWeather;

// Pass it to ChatClient at request time
ChatClient.create(chatModel)
    .prompt("What's the weather like in Copenhagen?")
    .tools(currentWeather)
    .call()
    .content();

// Or register as a default tool for all requests via the builder
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(currentWeather)
    .build();
```

### Tool Callback

ToolCallback接口提供一种方式，来定义一个可以被ai模型调用的工具，它包括了定义和执行的逻辑

你可以从 MCP Client (using the Model Context Protocol)或ChatClient (to build a modular agentic application)定义一个ToolCallback

Spring AI提供了两个内置实现：MethodToolCallback和FunctionToolCallback

ToolCallback提供的方法如下：

```java
public interface ToolCallback {
    /**
     * Definition used by the AI model to determine when and how to call the tool.
     */
    ToolDefinition getToolDefinition();

    /**
     * Metadata providing additional information on how to handle the tool.
     */
    ToolMetadata getToolMetadata();

    /**
     * Execute tool with the given input and return the result to send back to the AI model.
     */
    String call(String toolInput);

    /**
     * Execute tool with the given input and context, and return the result to send back to the AI model.
     */
    String call(String toolInput, ToolContext tooContext);
}
```

### Tool Definition

ToolDefinition接口提供必要的信息以便于ai模型去了解工具的能力，这些必要信息包括工具名称，描述，入参结构

每个ToolCallback实现类必须提供一个ToolDefinition实例来定义工具

ToolDefinition包含以下方法

```java
public interface ToolDefinition {
    /**
     * The tool name. Unique within the tool set provided to a model.
     */
    String name();

    /**
     * The tool description, used by the AI model to determine what the tool does.
     */
    String description();

    /**
     * The schema of the parameters used to call the tool.
     */
    String inputSchema();
}
```

ToolDefinition.Builder可以让你使用默认的实现DefaultToolDefinition构建一个ToolDefinition

```java
ToolDefinition toolDefinition = ToolDefinition.builder()
    .name("currentWeather")
    .description("Get the weather in location")
    .inputSchema("""
        {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string"
                },
                "unit": {
                    "type": "string",
                    "enum": ["C", "F"]
                }
            },
            "required": ["location", "unit"]
        }
    """)
    .build();
```

#### Method Tool Definition

从方法构建工具时，会自动生成ToolDefinition

当然也可以手动完成，代码如下：

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolDefinition toolDefinition = ToolDefinitions.from(method);
```

从方法生成的ToolDefinition包含了方法名和工具名

方法名作为工具描述，json结构作为方法入参

如果方法有`@Tool`注解，则工具名和工具描述会从注解里面提取（如果设置了）

如果你想要显式声明一部分或全部属性，可以使用ToolDefinition.Builder构建一个自定义的ToolDefinition实例

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolDefinition toolDefinition = ToolDefinitions.builder(method)
    .name("currentDateTime")
    .description("Get the current date and time in the user's timezone")
    .inputSchema(JsonSchemaGenerator.generateForMethodInput(method))
    .build();
```

#### Function Tool Definition

由于编程式的写法没有记笔记，可以参考spring ai的 Functions as Tools

#### Result Conversion

- ToolCallResultConverter接口

```java
@FunctionalInterface
public interface ToolCallResultConverter {
    /**
     * Given an Object returned by a tool, convert it to a String compatible with the
     * given class type.
     */
    String convert(@Nullable Object result, @Nullable Type returnType);
}
```

- 使用自定义ToolCallResultConverter

```java
class CustomerTools {
    @Tool(description = "Retrieve customer information", resultConverter = CustomToolCallResultConverter.class)
    Customer getCustomerInfo(Long id) {
        return customerRepository.findById(id);
    }
}
```

#### Tool Context

toolContext选项如果同时出现在默认选项和运行时选项中，则会将其合并，并且运行时选项优先级更高

- 在工具方法中声明ToolContext参数

```java
class CustomerTools {
    @Tool(description = "Retrieve customer information")
    Customer getCustomerInfo(Long id, ToolContext toolContext) {
        return customerRepository.findById(id, toolContext.getContext().get("tenantId"));
    }
}
```

- 在toolContext()方法里面设置ToolContext

```java
// 使用ChatModel
ChatModel chatModel = ...

String response = ChatClient.create(chatModel)
        .prompt("Tell me more about the customer with ID 42")
        .tools(new CustomerTools())
        .toolContext(Map.of("tenantId", "acme"))
        .call()
        .content();

// 使用ChatModel
ChatModel chatModel = ...
ToolCallback[] customerTools = ToolCallbacks.from(new CustomerTools());
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(customerTools)
    .toolContext(Map.of("tenantId", "acme"))
    .build();
Prompt prompt = new Prompt("Tell me more about the customer with ID 42", chatOptions);
chatModel.call(prompt);
```

#### Return Direct

returnDirect设置为true，工具调用后直接返回，而不是返回给模型

```java
class CustomerTools {
    @Tool(description = "Retrieve customer information", returnDirect = true)
    Customer getCustomerInfo(Long id) {
        return customerRepository.findById(id);
    }
}
```

#### Tool Execution

工具执行是使用提供的入参调用工具并返回结果的一个流程

ToolCallingManager负责工具执行的生命周期

```java
public interface ToolCallingManager {
    /**
     * Resolve the tool definitions from the model's tool calling options.
     */
    List<ToolDefinition> resolveToolDefinitions(ToolCallingChatOptions chatOptions);

    /**
     * Execute the tool calls requested by the model.
     */
    ToolExecutionResult executeToolCalls(Prompt prompt, ChatResponse chatResponse);

}
```

如果使用了任意的Spring AI Spring Boot Starters，DefaultToolCallingManager是自动配置好的ToolCallingManager实现

当然你可以自己定制ToolCallingManager bean

```java
@Bean
ToolCallingManager toolCallingManager() {
    return ToolCallingManager.builder().build();
}
```

- Spring AI支持3种管理工具执行生命周期的方式
    - 推荐的方式是通过ChatClient管理的Framework-Controlled Tool Execution
    - 需要自定义工具调用循环的时候，用Advisor-Controlled Tool Execution
    - 需要全程手动管理的时候，用User-Controlled Tool Execution

#### Exception Handling

当工具调用失败时，可以捕获ToolExecutionException来处理错误

- ToolExecutionExceptionProcessor可以用来处理ToolExecutionException，有两种处理方式
    - 生成错误信息返回给AI模型
    - 抛出一次给调用者处理

```java
@FunctionalInterface
public interface ToolExecutionExceptionProcessor {
    /**
     * Convert an exception thrown by a tool to a String that can be sent back to the AI
     * model or throw an exception to be handled by the caller.
     */
    String process(ToolExecutionException exception);
}
```

如果你使用任何的Spring AI Spring Boot Starters, DefaultToolExecutionExceptionProcessor是自动配置好的默认的ToolExecutionExceptionProcessor实现

- 有两种方式设置DefaultToolExecutionExceptionProcessor的错误处理方式

```properties
spring.ai.tools.throw-exception-on-error=false
```

```java
@Bean
ToolExecutionExceptionProcessor toolExecutionExceptionProcessor() {
    return new DefaultToolExecutionExceptionProcessor(true);
}
```

如果定义了自己的ToolCallback实现，取保错误发生时抛出ToolExecutionException

## Spring Security

Spring Security提供3大功能：认证、授权和防御常见攻击

认证 (Authentication)： 是验证用户的身份的凭据（例如用户名/用户ID和密码），通过这个凭据，系统得以知道用户是谁

授权 (Authorization)： 发生在认证之后，授予了什么权限，有权限干什么

### 身份验证方案

Session-Cookie + OAuth2

#### Session-Cookie方案

1. 用户成功登录系统后，服务器为用户创建一个Session并存储起来（Redis），然后返回给客户端具有SessionID的Cookie

2. 当用户向后端发起请求的时候会把SessionID带上，后端对其和数据库（Redis）中的SessionID进行比对

要确保客户端开启了Cookie，还要注意Session的过期时间

如果客户端禁用了Cookie，可以对SessionID进行一次加密，然后通过放到请求参数或者请求体的方式传给后端

- Cookie无法防止CSRF（跨站请求伪造）攻击，而Token可以
    - 如果点击了非法链接，会拿到Cookie干坏事
    - Token存放在浏览器的localStorage，非法链接拿不到这个Token

- 无论Cookie还是Token都无法避免XSS（Cross Site Scripting，跨站脚本攻击，为了跟层叠样式表（Cascading Style Sheets，CSS）的缩写区别）攻击

#### OAuth2（Opaque Token）

OAuth2由3个部分组成：
    - OAuth2 Resource Server（也就是后端业务微服务）
    - OAuth2 Client（前端+后端共同完成获取token，返回前端sessionid的整个登录流程，先这么理解吧）
    - OAuth2 Authorization Server（授权服务器，可以当成一个微服务）

### Spring Session

- 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

- 配置文件

```properties
# 自定义登录用户和密码
spring.security.user.name=admin
spring.security.user.password=123
```

- 自定义配置类

```java
// 开启SpringSecurity的自定义配置（在SpringBoot项目中可以省略此注解）
//@EnableWebSecurity
@Configuration
public class WebSecurityConfiguration {
    // 用这种方式创建的用户将会覆盖配置文件设置的用户信息
    @Bean
    public UserDetailsService userDetailsService() {
        // 创建基于内存的用户信息管理器
        InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
        // 创建UserDetails对象，用于管理用户名、用户密码、用户角色、用户权限等内容
        manager.createUser(User.withDefaultPasswordEncoder().username("user").password("123").roles("USER").build());
        return manager;
    }
}
```

### 基于数据库的认证

- 1.实现UserDetailsService并添加到IOC容器

```java
@Component
public class DatabaseUserDetailsManager implements UserDetailsService {
    @Resource
    private AccountDao accountDao;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        LambdaQueryWrapper<AccountPo> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(AccountPo::getName, username);
        AccountPo accountPo = accountDao.selectOne(queryWrapper);
        if (Objects.isNull(accountPo)) {
        throw new UsernameNotFoundException(username);
        }
        return new User(accountPo.getName(), accountPo.getPassword(), accountPo.getEnabled(), true,
        true, true, new ArrayList<GrantedAuthority>());
    }
}

```

### 自定义配置

```java
@Configuration
public class WebSecurityConfiguration {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
        // 开启授权保护
        httpSecurity.authorizeHttpRequests(authorize -> 
        // 对所有请求开启授权保护
        authorize.anyRequest()
        // 已认证的请求会自动授权
        .authenticated());

        // 使用表单授权方式
        httpSecurity.formLogin(form -> 
        form.loginPage("/login")
        // 无需授权即可访问
        .permitAll()
        // 自定义登录页面表单的用户名参数名、密码参数名（默认是username、password）
        .usernameParameter("username")
        .passwordParameter("password")
        // 校验失败时跳转的地址
        .failureUrl("login?failure")
        // 认证成功时的处理
        .successHandler(new LoginAuthenticationSuccessHandler())
        // 认证失败时的处理
        .failureHandler(new LoginAuthenticationFailureHandler())
        );
        // 登出成功时的处理
        httpSecurity.logout(logout -> logout.logoutSuccessHandler(new MyLogoutSuccessHandler()));
        // 请求未认证时的处理
        httpSecurity.exceptionHandling(exception -> exception.authenticationEntryPoint(new MyAuthenticationEntryPoint()));

        // 设置一个账号最多允许同时登录的上限，并自定义超时退出
        httpSecurity.sessionManagement(session -> session.maximumSessions(1).expiredSessionStrategy(new MySessionInformationExpiredStrategy()));
        //  httpSecurity.cors()
        return httpSecurity.build();
    }
}
```

#### 在handler中获取用户信息

```java
@GetMapping("/userInfo")
@ResponseBody
public void userInfo() {
    SecurityContext context = SecurityContextHolder.getContext();
    Authentication authentication = context.getAuthentication();
    String username = authentication.getName();
    Object principal = authentication.getPrincipal();
    Object credentials = authentication.getCredentials();
    Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
}
```

### 身份认证

#### PasswordEncoder

- PasswordEncoder接口有三个方法

```java
public interface PasswordEncoder {
    String encode(CharSequence rawPassword);

    boolean matches(CharSequence rawPassword, String encodedPassword);

    default boolean upgradeEncoding(String encodedPassword) {
        return false;
    }
}
```

- 密码保存

```java
// 创建默认的DelegatingPasswordEncoder
PasswordEncoder passwordEncoder =
    PasswordEncoderFactories.createDelegatingPasswordEncoder();

// 自定义DelegatingPasswordEncoder
String idForEncode = "bcrypt";
Map encoders = new HashMap<>();
encoders.put(idForEncode, new BCryptPasswordEncoder());
encoders.put("noop", NoOpPasswordEncoder.getInstance());
encoders.put("pbkdf2", Pbkdf2PasswordEncoder.defaultsForSpringSecurity_v5_5());
encoders.put("pbkdf2@SpringSecurity_v5_8", Pbkdf2PasswordEncoder.defaultsForSpringSecurity_v5_8());
encoders.put("scrypt", SCryptPasswordEncoder.defaultsForSpringSecurity_v4_1());
encoders.put("scrypt@SpringSecurity_v5_8", SCryptPasswordEncoder.defaultsForSpringSecurity_v5_8());
encoders.put("argon2", Argon2PasswordEncoder.defaultsForSpringSecurity_v5_2());
encoders.put("argon2@SpringSecurity_v5_8", Argon2PasswordEncoder.defaultsForSpringSecurity_v5_8());
encoders.put("sha256", new StandardPasswordEncoder());

PasswordEncoder passwordEncoder =
    new DelegatingPasswordEncoder(idForEncode, encoders);

// 设置默认PasswordEncoder
DelegatingPasswordEncoder.setDefaultPasswordEncoderForMatches(PasswordEncoder);

// 编码密码
UserDetails user = User.withDefaultPasswordEncoder()
    .username("user")
    .password("password")
    .roles("user")
    .build();
System.out.println(user.getPassword());
// {bcrypt}$2a$10$dXJ3SW6G7P50lGmMkkmwe.20cQQubK3.HZWzG3YB1tlRy.fqvM/BG

// 复用builder编码多个用户的密码
UserBuilder users = User.withDefaultPasswordEncoder();
UserDetails user = users
    .username("user")
    .password("password")
    .roles("USER")
    .build();
UserDetails admin = users
    .username("admin")
    .password("password")
    .roles("USER","ADMIN")
    .build();

// 上面的编码方式密码还是暴露在内存和字节码里面
// 为此可以 Encode with Spring Boot CLI
spring encodepassword password
{bcrypt}$2a$10$X5wFBtLrL/kHcmrOGGTrGufsBX8CJ0WpQpF3pgeuxBB/H73BK1DW6
```

- 密码校验，需要调整参数让校验时间大约1秒左右

```java
// Create an encoder with strength 16
BCryptPasswordEncoder encoder = new BCryptPasswordEncoder(16);
String result = encoder.encode("myPassword");
assertTrue(encoder.matches("myPassword", result));

// Argon2是the winner of the Password Hashing Competition，它需要消耗大内存
// 依赖BouncyCastle库
// Create an encoder with all the defaults
Argon2PasswordEncoder encoder = Argon2PasswordEncoder.defaultsForSpringSecurity_v5_8();
String result = encoder.encode("myPassword");
assertTrue(encoder.matches("myPassword", result));

// Pbkdf2 is a good choice when FIPS certification is required
// Create an encoder with all the defaults
Pbkdf2PasswordEncoder encoder = Pbkdf2PasswordEncoder.defaultsForSpringSecurity_v5_8();
String result = encoder.encode("myPassword");
assertTrue(encoder.matches("myPassword", result));

// SCrypt也需要消耗大内存
// Create an encoder with all the defaults
SCryptPasswordEncoder encoder = SCryptPasswordEncoder.defaultsForSpringSecurity_v5_8();
String result = encoder.encode("myPassword");
assertTrue(encoder.matches("myPassword", result));

// 以下是Password4j-based Password Encoders
// Spring Security 7.0介绍了可选的基于Password4j库的密码编码器，它们都是线程安全的
// 这些编码器中的流行编码器提供了额外的操作，当你需要指定配置或充分利用Password4j的优化时它们更加有用

// 官方推荐新的应用用Argon2
// 默认配置的Argon2Password4jPasswordEncoder，编码后的密码包含了salt
PasswordEncoder encoder = new Argon2Password4jPasswordEncoder();
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// 自定义配置的Argon2Password4jPasswordEncoder，编码后的密码包含了salt
Argon2Function argon2Fn = Argon2Function.getInstance(65536, 3, 4, 32,
    Argon2.ID);
PasswordEncoder encoder = new Argon2Password4jPasswordEncoder(argon2Fn);
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// 默认配置的BCryptPasswordEncoder，编码后的密码包含了salt
PasswordEncoder encoder = new BCryptPasswordEncoder();
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// 自定义配置的BCryptPasswordEncoder，编码后的密码包含了salt
BcryptFunction bcryptFn = BcryptFunction.getInstance(12);
PasswordEncoder encoder = new BcryptPassword4jPasswordEncoder(bcryptFn);
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// ScryptPassword4jPasswordEncoder旨在抵御硬件暴力攻击，编码后的密码包含了salt
// 默认配置的ScryptPassword4jPasswordEncoder
PasswordEncoder encoder = new ScryptPassword4jPasswordEncoder();
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// 自定义配置的ScryptPassword4jPasswordEncoder
ScryptFunction scryptFn = ScryptFunction.getInstance(32768, 8, 1, 32);
PasswordEncoder encoder = new ScryptPassword4jPasswordEncoder(scryptFn);
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// PBKDF2是一个密钥派生函数，用于抵御字典攻击和暴力破解攻击，使其计算成本很高
// 官网说编码后的密码不包含salt
// 但是又说编码后的格式为{salt}:{hash}， both salt and hash are Base64 encoded
// 等使用的时候再验证一下吧
// 默认配置的Pbkdf2Password4jPasswordEncoder
PasswordEncoder encoder = new Pbkdf2Password4jPasswordEncoder();
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// 自定义配置的Pbkdf2Password4jPasswordEncoder
PBKDF2Function pbkdf2Fn = PBKDF2Function.getInstance(Hmac.SHA256, 100000, 256);
PasswordEncoder encoder = new Pbkdf2Password4jPasswordEncoder(pbkdf2Fn);
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// BalloonHashingPassword4jPasswordEncoder旨在抵抗时间-内存权衡攻击和侧信道攻击
// 官网说编码后的密码不包含salt
// 但是又说编码后的格式为{salt}:{hash}， both salt and hash are Base64 encoded
// 等使用的时候再验证一下吧
// 默认配置的BalloonHashingPassword4jPasswordEncoder
PasswordEncoder encoder = new BalloonHashingPassword4jPasswordEncoder();
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();

// 自定义配置的BalloonHashingPassword4jPasswordEncoder
BalloonHashingFunction ballooningHashingFn =
 BalloonHashingFunction.getInstance("SHA-256", 1024, 3, 4, 3);
PasswordEncoder encoder = new BalloonHashingPassword4jPasswordEncoder(ballooningHashingFn);
String result = encoder.encode("myPassword");
assertThat(encoder.matches("myPassword", result)).isTrue();
```

- 密码保存格式

```sh
# id：编码password的PasswordEncoder标识
# encodedPassword：原始密码编码后得到的密码
{id}encodedPassword

# 例
{sha256}97cde38028ad898ebc02e690819fa220e88c62e0699403e94fff291cfffaf8410849f27605abcbc0
```

#### 密码存储配置

Spring Security 默认使用 DelegatingPasswordEncoder，但是开发者通过Spring bean可以自定义PasswordEncoder

```java
@Bean
public static NoOpPasswordEncoder passwordEncoder() {
    return NoOpPasswordEncoder.getInstance();
}
```

#### 密码修改配置

```java
// 导航/.well-known/change-password将重定向到/change-password
http
    .passwordManagement(Customizer.withDefaults())

// 也可以自定义，导航/.well-known/change-password将重定向到/update-password
http
    .passwordManagement((management) -> management
        .changePasswordPage("/update-password")
    )
```

#### 密码强度检测

可以使用Spring Security 的DaoAuthenticationProvider

也可以使用Have I Been Pwned API的CompromisedPasswordChecker（需要自己提供一个bean：HaveIBeenPwnedRestApiPasswordChecker）

当用户输入的密码较弱时，可以通过AuthenticationFailureHandler处理CompromisedPasswordException来执行自定义的逻辑，比如重定向到/reset-password

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests(authorize -> authorize
            .anyRequest().authenticated()
    )
    .formLogin((login) -> login
        .failureHandler(new CompromisedPasswordAuthenticationFailureHandler())
    );
    return http.build();
}

@Bean
public CompromisedPasswordChecker compromisedPasswordChecker() {
    return new HaveIBeenPwnedRestApiPasswordChecker();
}

static class CompromisedPasswordAuthenticationFailureHandler implements AuthenticationFailureHandler {

    private final SimpleUrlAuthenticationFailureHandler defaultFailureHandler = new SimpleUrlAuthenticationFailureHandler(
   "/login?error");

    private final RedirectStrategy redirectStrategy = new DefaultRedirectStrategy();

    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response,
        AuthenticationException exception) throws IOException, ServletException {
        if (exception instanceof CompromisedPasswordException) {
            this.redirectStrategy.sendRedirect(request, response, "/reset-password");
            return;
        }
        this.defaultFailureHandler.onAuthenticationFailure(request, response, exception);
    }

}
```

### 授权

Spring Security提供了基于请求的授权和基于方法的授权两种

### 防范漏洞利用

#### CSRF

Cross Site Request Forgery，跨站点请求伪造

浏览器有一个机制：只要访问某个域名，浏览器就会自动附带该域名的Cookie（如里面包含了sessionId）

比如你登录了bank.com，但是没有登出

然后浏览恶意页面evil.com，这个页面的表单内容模仿了bank.com的转钱表单的内容（或者它就是一个让你以为是bank.com的钓鱼网站），表单提交将发请求到bank.com，然后引诱你点击

不幸的是你点击了提交（甚至是不用你点击直接js脚本自动完成了），浏览器看到请求目标是bank.com，就自动带上bank.com的Cookie了，然后你账户的钱没了

这就是CSRF，一句话概括就是CSRF让你的浏览器带着你的Cookie去做你没打算做的事

Spring提供了两种机制来防范：Synchronizer Token Pattern和Specifying the SameSite Attribute on your session cookie

这两种机制都要求前提是Safe Methods be Read-only： HTTP GET, HEAD, OPTIONS, and TRACE methods should not change the state of the application

##### Synchronizer Token Pattern

由于浏览器的同源策略（Same-Origin Policy）规定，跨站请求不能读取hidden input和设置自定义Header

因此对于改变应用状态的请求，可以生成一个随机的CSRF Token，放在HTTP parameter或HTTP header中

```html
<!-- hidden input，随表单一起提交 -->
<input type="hidden"
 name="_csrf"
 value="4bfd1575-3ad1-4d21-96c7-4ef2d9f86721"/>

<!-- 设置header，随请求一起提交，推荐 -->
X-CSRF-Token: 4bfd1575-3ad1-4d21-96c7-4ef2d9f86721
```

##### Specifying the SameSite Attribute on your session cookie

Spring Security不直接控制session cookie的创建，也就是不对SameSite attribute提供支持

可以通过Spring Session设置SameSite attribute

- SameSite有两种属性
    - Strict：任何跨站请求都不带Cookie
    - Lax：top-level navigations and the method is read-only除外，其它跨站请求都不带Cookie

```sh
Set-Cookie: JSESSIONID=randomid; Domain=bank.example.com; Secure; HttpOnly; SameSite=Lax
```

#### HTTP Headers

##### Default Security HTTP Response Headers

```sh
# Spring Security默认禁止缓存
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0

# Spring Security默认禁止content type嗅探，这时候需要手动指定content type
X-Content-Type-Options: nosniff

# 将站点标记为HSTS主机的一种方法是将主机预加载到浏览器中
# 另一种方法就是添加Strict-Transport-Security到响应头
# max-age=31536000，命令浏览器一年内都将域名作为HSTS主机对待
# includeSubDomains：也将子域名域名作为HSTS主机对待
# preload：命令浏览器将其作为HSTS主机预加载
Strict-Transport-Security: max-age=31536000 ; includeSubDomains ; preload
# 禁用在iframe内渲染页面
X-Frame-Options: DENY

# 有些浏览器内置了过滤反射XSS攻击的支持
# 该过滤器在主流浏览器中已被弃用，目前OWASP的建议是显式地将报头设置为0
X-XSS-Protection: 0
```

##### Content Security Policy (CSP)

```sh
# 当试图从非script-src声明的地址载脚本时，将会被user-agent阻止
# 如果一个web应用程序违反了声明的安全策略，将指示user-agent向report-uri指定的URL发送违反报告
Content-Security-Policy: script-src https://trustedscripts.example.com; report-uri /csp-report-endpoint/

# 只发送违反报告，不阻止加载脚本，通常在实验或开发场景使用
Content-Security-Policy-Report-Only: script-src 'self' https://trustedscripts.example.com; report-uri /csp-report-endpoint/
```

##### Referrer Policy

```sh
# 指示浏览器让目的地知道用户以前所在的源
Referrer-Policy: same-origin
```

##### Feature Policy

```sh
# Feature Policy是一种允许web开发人员选择性地启用、禁用和修改浏览器中某些api和web特性的行为的机制
Feature-Policy: geolocation 'self'
```

##### Permissions Policy

```sh
# Permissions Policy是一种允许web开发人员选择性地启用、禁用和修改浏览器中某些api和web特性的行为的机制
# 这描述感觉是官方复制粘贴的，具体应该跟Feature Policy有点类似
Permissions-Policy: geolocation=(self)
```

###### Clear Site Data

```sh
# 任何浏览器端数据（cookie，本地存储等）都可以在HTTP响应包含以下header信息时被删除
# 这是在注销时执行的一个很好的清理操作
Clear-Site-Data: "cache", "cookies", "storage", "executionContexts"
```

#### HTTP Requests

所有基于http的通信，包括静态资源，都应该使用TLS来保护

作为一个框架，Spring Security不处理HTTP连接，因此不直接提供对HTTPS的支持

但是，它确实提供了许多有助于使用HTTPS的特性

##### Redirect to HTTPS

##### Strict Transport Security

Spring Security默认开启了对Strict Transport Security的支持

##### Proxy Server Configuration

spring boot的开发者可以用server.forward-headers-strategy属性进行配置

### 多线程支持

```java
SecurityContext context = SecurityContextHolder.createEmptyContext();
Authentication authentication =
    UsernamePasswordAuthenticationToken.authenticated("user","doesnotmatter", AuthorityUtils.createAuthorityList("ROLE_USER"));
context.setAuthentication(authentication);

SimpleAsyncTaskExecutor delegateExecutor =
    new SimpleAsyncTaskExecutor();
DelegatingSecurityContextExecutor executor =
    new DelegatingSecurityContextExecutor(delegateExecutor, context);

Runnable originalRunnable = new Runnable() {
    public void run() {
        // invoke secured service
    }
};

executor.execute(originalRunnable);
```

### Jackson 3 support

```java
ClassLoader loader = getClass().getClassLoader();
JsonMapper mapper = JsonMapper.builder()
        .addModules(SecurityJacksonModules.getModules(loader))
        .build();

// ... use JsonMapper as normally ...
SecurityContext context = new SecurityContextImpl();
// ...
String json = mapper.writeValueAsString(context);

```

- 自定义类校验处理

```java
ClassLoader loader = getClass().getClassLoader();
BasicPolymorphicTypeValidator.Builder builder = BasicPolymorphicTypeValidator.builder()
        .allowIfSubType(MyCustomType.class);
JsonMapper mapper = JsonMapper.builder()
        .addModules(SecurityJacksonModules.getModules(loader, builder))
        .build();
```

### Spring Security体系结构

![Spring Security架构](/images/Spring%20Security架构.png)

- 如果想要让Spring Security忽略某些请求，可以定义一个没有没有Filter的SecurityFilterChain

- 一般使用HttpSecurity声明过滤器实例

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // CsrfFilter
            .csrf(Customizer.withDefaults())
            // BasicAuthenticationFilter
            .httpBasic(Customizer.withDefaults())
            // UsernamePasswordAuthenticationFilter
            .formLogin(Customizer.withDefaults())
            // AuthorizationFilter
            .authorizeHttpRequests((authorize) -> authorize
                .anyRequest().authenticated()
            );

        return http.build();
    }

}
```

```properties
# 打印请求调用的过滤器
logging.level.org.springframework.security=trace

# 打印DefaultSecurityFilterChain的过滤器
logging.level.o.s.s.web.DefaultSecurityFilterChain=debug
```

#### 将过滤器添加到过滤器链中

- HttpSecurity有三个方法可以添加过滤器

```java
// adds your filter before another filter
addFilterBefore(Filter, Class<?>) 

// adds your filter after another filter
addFilterAfter(Filter, Class<?>) 

// replaces another filter with your filter
addFilterAt(Filter, Class<?>) 
```

- 为了确定自定义过滤器在过滤器链中的位置，可以考虑以下几个关键的事件
    - 1.SecurityContext is loaded from the session
    - 2.Request is protected from common exploits; secure headers, CORS, CSRF
    - 3.Request is authenticated
    - 4.Request is authorized
- 参考以下经验

|If your filter is a(n)|Then place it after|As these events have already occurred|
|:-|:-|:-|
|exploit protection filter|SecurityContextHolderFilter|1|
|authentication filter|LogoutFilter|1, 2|
|authorization filter|AnonymousAuthenticationFilter|1, 2, 3|

##### 创建过滤器

除了自己写一个过滤器实现类，还可以扩展OncePerRequestFilter

OncePerRequestFilter是每个请求只调用一次的过滤器的基类

它提供了一个带有HttpServletRequest和HttpServletResponse参数的doFilterInternal方法

###### 普通方式创建过滤器

```java
import java.io.IOException;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import org.springframework.security.access.AccessDeniedException;

public class TenantFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;

        String tenantId = request.getHeader("X-Tenant-Id"); (1)
        boolean hasAccess = isUserAllowed(tenantId); (2)
        if (hasAccess) {
            filterChain.doFilter(request, response); (3)
            return;
        }
        throw new AccessDeniedException("Access denied"); (4)
    }

}
```

###### 以spring bean的方式创建过滤器

当你将过滤器声明为一个spring bean时，spring boot会自动注册它

这将导致这个过滤器会以不同的顺序被调用两次，一次是容器调用，另一次是Spring Security调用

因此，过滤器一般不作为spring bean

如果仍然要将过滤器定义成spring bean，你需要通过声明一个FilterRegistrationBean bean， 并且设置它的enabled属性为false来告诉Spring Boot，不要用容器注册它

```java
@Bean
public FilterRegistrationBean<TenantFilter> tenantFilterRegistration(TenantFilter filter) {
    FilterRegistrationBean<TenantFilter> registration = new FilterRegistrationBean<>(filter);
    registration.setEnabled(false);
    return registration;
}
```

##### 添加到过滤器链（SecurityFilterChain）

AnonymousAuthenticationFilter是过滤器链中，身份认证的最后一个过滤器

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        // ...
        .addFilterAfter(new TenantFilter(), AnonymousAuthenticationFilter.class);
    return http.build();
}
```

##### 对Spring Security的过滤器进行自定义

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    BasicAuthenticationFilter basic = new BasicAuthenticationFilter();
    // ... configure

    http
    // 如果过滤器被添加两次，将会抛出异常，比如这里BasicAuthenticationFilter被添加了两次
    .httpBasic(Customizer.withDefaults())
    // ... on no! BasicAuthenticationFilter is added twice!
    .addFilterAt(basic, BasicAuthenticationFilter.class);

    return http.build();
}
```

- 在无法重新配置HttpSecurity以不添加特定过滤器的情况下，通常可以通过调用其DSL的disable方法来禁用Spring Security过滤器

```java
.httpBasic((basic) -> basic.disable())
```

#### 处理安全异常

![ExceptionTranslationFilter](/images/ExceptionTranslationFilter.png)

##### ExceptionTranslationFilter

ExceptionTranslationFilter允许转化AccessDeniedException和AuthenticationException 到http响应中

- ExceptionTranslationFilter的逻辑如下

```java
try {
    filterChain.doFilter(request, response);
} catch (AccessDeniedException | AuthenticationException ex) {
    if (!authenticated || ex instanceof AuthenticationException) {
    startAuthentication();
    } else {
    accessDenied();
    }
}
```

如果不抛出AccessDeniedException或AuthenticationException异常, ExceptionTranslationFilter将不做任何事情

##### RequestCache

当一个获取资源的请求需要身份认证，但是还没进行身份认证时，就需要先保存请求信息，等身份认证通过后重新请求

在Spring Security中，通过使用RequestCache的实现来完成这一功能

ExceptionTranslationFilter检测到AuthenticationException后，在将用户重定向到登录端点之前，用RequestCache保存请求信息

身份认证后，RequestCacheAwareFilter使用RequestCache获取请求信息，重新请求

默认使用HttpSessionRequestCache

```java
// 自定义RequestCache，当参数continue存在时，才检测HttpSession来保存请求信息
@Bean
DefaultSecurityFilterChain springSecurity(HttpSecurity http) throws Exception {
    HttpSessionRequestCache requestCache = new HttpSessionRequestCache();
    requestCache.setMatchingRequestParameterName("continue");
    http
    // ...
    .requestCache((cache) -> cache
    .requestCache(requestCache)
    );
    return http.build();
}
```

##### 禁止保存请求

不希望在会话中存储用户未经身份验证的请求的原因有很多

您可能希望将该存储空间卸载到用户的浏览器上，或者将其存储在数据库中

或者您可能希望关闭此功能，因为您总是希望将用户重定向到主页，而不是他们在登录前试图访问的页面

可以通过NullRequestCache实现禁止保存请求

```java
@Bean
SecurityFilterChain springSecurity(HttpSecurity http) throws Exception {
    RequestCache nullRequestCache = new NullRequestCache();
    http
        // ...
        .requestCache((cache) -> cache
            .requestCache(nullRequestCache)
        );
    return http.build();
}
```

#### 记录日志

Spring Security在debug和trace日志级别，对所有安全相关的事件提供了全面的日志记录

可以通过添加如下配置来记录所有的安全事件

```properties
logging.level.org.springframework.security=TRACE
```

```xml
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- ... -->
    </appender>
    <!-- ... -->
    <logger name="org.springframework.security" level="trace" additivity="false">
        <appender-ref ref="Console" />
    </logger>
</configuration>
```

### 身份认证体系结构

#### SecurityContextHolder

![SecurityContextHolder](/images/SecurityContextHolder.png)

SecurityContextHolder是Spring Security存储身份验证详细信息的地方

Spring Security并不关心SecurityContextHolder是如何填充的

如果SecurityContextHolder包含值，则使用该值作为当前认证的用户

- 指示用户已经过身份验证的最简单方法是直接设置SecurityContextHolder

```java
// 为了避免多线程之间的竞争，你应该创建一个新的SecurityContext
// 而不是使用SecurityContextHolder.getContext().setAuthentication(authentication)
SecurityContext context = SecurityContextHolder.createEmptyContext();
// 这里用简单的TestingAuthenticationToken做例子
// 生产场景更加通用的是UsernamePasswordAuthenticationToken(userDetails, password, authorities)
Authentication authentication =
    new TestingAuthenticationToken("username", "password", "ROLE_USER");
context.setAuthentication(authentication);

SecurityContextHolder.setContext(context);
```

- 通过访问SecurityContextHolder获取有关已验证主体的信息

```java
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
String username = authentication.getName();
Object principal = authentication.getPrincipal();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```

SecurityContextHolder使用ThreadLocal来存储身份认证信息

你还可以通过配置SecurityContextHolder的策略决定它的存储方式
    - 对于独立应用程序，你可能使用SecurityContextHolder.MODE_GLOBAL
    - 对于其它应用程序，你可能想让子线程拥有同样的身份认证信息，使用SecurityContextHolder.MODE_INHERITABLETHREADLOCAL
    -默认 SecurityContextHolder.MODE_THREADLOCAL
有两种方式设置策略的方法：设置系统属性和使用SecurityContextHolder的静态方法

#### SecurityContext

可以从SecurityContextHolder获得SecurityContext，它包含了一个Authentication对象

#### Authentication

- 在Spring Security中，Authentication接口有两个主要用途
    - 作为AuthenticationManager的输入，以给出用户提供的身份验证凭据。在此场景中使用时，isAuthenticated（）返回false
    - 表示当前经过身份验证的用户，可以从SecurityContext获得它

- Authentication包含
    - principal：表示用户，当使用username/password进行身份验证时，通常是UserDetails的一个实例
    - credentials：通常是密码，在许多情况下，在用户通过身份验证后清除该属性，以确保它不会泄露
    - authorities：GrantedAuthority实例是授予用户的高级权限，如角色和范围

它还配备了一个AdditionalRequiredFactorsBuilder，允许您修改现有的身份验证实例，并可能将其与另一个实例合并

这在从一个身份验证步骤（如表单登录）获取授权并将其应用于另一个验证步骤（如一次性令牌登录）的场景中非常有用，如下例

```java
Authentication lastestResult = authenticationManager.authenticate(authenticationRequest);
Authentication previousResult = SecurityContextHolder.getContext().getAuthentication();
if (previousResult != null && previousResult.isAuthenticated()) {
    lastestResult = lastestResult.toBuilder()
    .authorities((a) -> a.addAll(previous.getAuthorities()))
    .build();
}
```

#### GrantedAuthority

GrantedAuthority实例是授予用户的高级权限，如角色和范围

你可以通过Authentication.getAuthorities()方法获得GrantedAuthority实例

此方法提供了GrantedAuthority对象的集合

毫无疑问，GrantedAuthority是授予主体的权限，这些权限通常是"角色",如ROLE_ADMINISTRATOR或ROLE_HR_SUPERVISOR

这些角色后面为web授权、方法授权和域对象授权配置的

Spring Security的其他部分解释这些权限并期望它们存在

当使用基于用户名/密码的身份验证时，GrantedAuthority实例通常由UserDetailsService加载

通常，GrantedAuthority对象是应用程序范围的权限，它们并不特定于给定的域对象

因此，您不太可能使用GrantedAuthority来表示对Employee对象号54的权限

因为如果有数千个这样的权限，您将很快耗尽内存（或者，至少会导致应用程序花费很长时间来验证用户）

当然，Spring Security是专门为处理这一常见需求而设计的，但是您应该使用项目的域对象安全功能来实现这一目的

#### AuthenticationManager

AuthenticationManager是定义Spring Security的过滤器如何执行身份验证的API

Authentication是由调用AuthenticationManager的控制器（即Spring Security的Filters实例）返回，然后设置到SecurityContextHolder的身份验证

如果你没有集成Spring Security的Filters实例，你可以直接设置SecurityContextHolder，而不需要使用AuthenticationManager

虽然AuthenticationManager的实现可以是任何东西，但最常见的实现是ProviderManager

##### Customize the AuthenticationManager

两种自定义方式

###### Configure global AuthenticationManagerBuilder

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    // ...
    return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService() {
    // Return a UserDetailsService that caches users
    // ...
    }

    @Autowired
    public void configure(AuthenticationManagerBuilder builder) {
    builder.eraseCredentials(false);
    }

}
```

###### Configure local AuthenticationManager for Spring Security

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
    .authorizeHttpRequests((authorize) -> authorize
        .anyRequest().authenticated()
    )
    .httpBasic(Customizer.withDefaults())
    .formLogin(Customizer.withDefaults())
    .authenticationManager(authenticationManager());

    return http.build();
    }

    private AuthenticationManager authenticationManager() {
    DaoAuthenticationProvider authenticationProvider = new DaoAuthenticationProvider(userDetailsService());
    authenticationProvider.setPasswordEncoder(passwordEncoder());

    ProviderManager providerManager = new ProviderManager(authenticationProvider);
    providerManager.setEraseCredentialsAfterAuthentication(false);

    return providerManager;
    }

    private UserDetailsService userDetailsService() {
    UserDetails userDetails = User.withDefaultPasswordEncoder()
    .username("user")
    .password("password")
    .roles("USER")
    .build();

    return new InMemoryUserDetailsManager(userDetails);
    }

    private PasswordEncoder passwordEncoder() {
    return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }

}
```

#### ProviderManager

![ProviderManager](/images/ProviderManager.png)

ProviderManager是AuthenticationManager最常用的实现

ProviderManager委托给一系列的AuthenticationProvider实例

每个AuthenticationProvider都有机会表明身份验证应该是成功的、失败的，或者表明它不能做出决定，并允许下游的AuthenticationProvider做出决定

如果配置的AuthenticationProvider实例都不能进行身份验证，则身份验证失败，并产生一个ProviderNotFoundException，这是一个特殊的AuthenticationException，表明ProviderManager没有配置为支持传递给它的Authentication类型

默认情况下，ProviderManager尝试从成功的身份验证请求返回的Authentication对象中清除任何敏感凭据信息

这可以防止信息（如密码）在HttpSession中保留的时间超过所需的时间

你可以复制这些敏感信息对象或禁用ProviderManager的eraseCredentialsAfterAuthentication属性来避免清理带来的影响

没有为UserDetails使用缓存机制的应用程序应该特别考虑实现CredentialsContainer

这种方法有助于降低与在内存中保留敏感信息相关的风险，这些信息可能容易受到内存转储等攻击向量的攻击

但是编写自己的AuthenticationProvider实现的用户应该在那里创建并返回一个适当的Authentication对象，减去任何敏感数据，而不是使用这个接口

```java
public class MyUserDetails implements UserDetails, CredentialsContainer {

    private String username;

    private String password;

    // UserDetails implementation...

    @Override
    public void eraseCredentials() {
        this.password = null; // Securely dereference the password field
    }

}
```

#### AuthenticationProvider

您可以将多个AuthenticationProvider实例注入到ProviderManager中

每个AuthenticationProvider执行特定类型的身份验证

例如，DaoAuthenticationProvider支持基于用户名/密码的身份验证，而JwtAuthenticationProvider支持验证JWT令牌

#### Request Credentials with AuthenticationEntryPoint

AuthenticationEntryPoint用于发送从客户端请求凭据的HTTP响应

有时，客户机主动包含凭据（如用户名和密码）来请求资源

在这些情况下，Spring Security不需要提供从客户机请求凭据的HTTP响应，因为它们已经包含在内

在其他情况下，客户端向未授权访问的资源发出未经身份验证的请求

在这种情况下，使用AuthenticationEntryPoint的实现从客户端请求凭据

AuthenticationEntryPoint实现可能会执行重定向到登录页面（LoginUrlAuthenticationEntryPoint）、使用WWW-Authenticate响应头（BasicAuthenticationEntryPoint）或采取其他操作

#### AbstractAuthenticationProcessingFilter

![AbstractAuthenticationProcessingFilter](/images/AbstractAuthenticationProcessingFilter.png)

authenticationprocessingfilter用作验证用户凭据的基本过滤器。在对凭证进行身份验证之前，Spring Security通常通过使用AuthenticationEntryPoint请求凭证

接下来，AbstractAuthenticationProcessingFilter可以对提交给它的任何身份验证请求进行身份验证

## Spring Cloud

### Spring Cloud 组件

- 1.服务注册与发现，Etcd、Consul、Nacos

- 2.服务调用，LoadBalancer、OpenFeign

- 3.分布式事务，LCN、Hmily、Seata

- 4.服务熔断和降级，resilience4j、sentinel

- 5.服务链路追踪，Micrometer Tracing

- 6.服务网关：Gateway

- 7.服务配置：Consul、Nacos

### CAP

- C，一致性
- A，高可用
- P，分区容错性

### Eureka

#### Eureka服务器

- maven dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

- 应用配置

```properties
server.port=7001

# eureka服务器的实例名称
eureka.instance.hostname=eureka7001.com

# 是否注册到eureka注册中心
eureka.client.register-with-eureka=false

# false表示自己就是注册中心，自己的职责就是维护服务实例，并不需要去检索服务
eureka.client.fetch-registry=false

# 与eureka服务器交互的地址查询服务和注册服务都需要依赖这个地址
# 单机版配置
# eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka

eureka.client.service-url.defaultZone=http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka

# 关闭Eureka自我保护，使得不可用服务及时被剔除
eureka.server.enable-self-preservation=false

# 扫描失效服务的间隔时间（毫秒）
eureka.server.eviction-interval-timer-in-ms=3000
```

- 主启动类

```java
@SpringBootApplication
@EnableEurekaServer
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

#### Eureka客户端

- maven dependency

```xmln
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

- 应用配置

```properties
server.port=8001

# 作为注册进Eureka服务器的应用名称
spring.application.name=cloud-payment-service

# Eureka客户端配置

# 定义服务实例名称
eureka.instance.instance-id=payment8001

# 访问路径可以显示ip地址
eureka.instance.prefer-ip-address=true

# 是否注册到eureka服务器
eureka.client.register-with-eureka=true

# 是否从eureka服务器抓取已有的注册信息，集群必须设置为true才能配合ribbon使用负载均衡
eureka.client.fetch-registry=true

# Eureka服务器地址，即Eureka服务器的eureka.client.service-url.defaultZone

# eureka单机版
# eureka.client.service-url.defaultZone=http://localhost:7001/eureka

# eureka集群版
eureka.client.service-url.defaultZone=http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka

# Eureka客户端向服务器发送心跳的时间间隔（秒）
eureka.instance.lease-renewal-interval-in-seconds=1
# Eureka服务器在收到最后一次心跳后等待的时间上限（秒），超时剔除服务
eureka.instance.lease-expiration-duration-in-seconds=2
```

- 主启动类

```java
@SpringBootApplication
@EnableEurekaClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

#### 服务发现

对注册进Eureka注册中心的微服务，可以通过服务发现获取该微服务的信息

- 主启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 控制器类

```java
@Resource
private DiscoveryClient discoveryClient;

/**
* 通过discoveryClient获取注册到Eureka的微服务信息
*/
@GetMapping("/payment/discovery")
public Object discovery() {
    // 获取注册到Eureka的微服务名称列表
    List<String> services = discoveryClient.getServices();
    for (String service : services) {
        log.info("service: {}", service);
    }

    // 通过微服务名称获取微服务的实例
    List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
    for (ServiceInstance instance : instances) {
        log.info("{}", instance.toString());
    }
    return discoveryClient;
}
```

### OpenFeign

#### 接口项目

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

- 接口

```java
@FeignClient(name = "account-service", contextId = "account-service1")
public interface AccountApi {
    // 不能在接口上写@RequestMapping了，请求路径要写全（本体handler类上@RequestMapping注解的路径要加上来）
    @GetMapping("/account/getAccount")
    ResultVo<AccountPo> getAccount(@RequestParam("userId") Long userId);
}
```

##### @FeignClient

- 用来定义OpenFeign接口

- name，填接口实现方在注册中心的服务名，如果需要网关路由，则填网关在注册中心的服务名

- contextId，当定义了多个name一样的OpenFeign接口时，用contextId为bean起别名

#### 消费项目

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<!-- loadbalancer不加启动会报错 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 配置

```properties
spring.application.name=openFeign-consumer-service
server.port=8080
spring.cloud.nacos.discovery.server-addr=localhost:8848
```

- 主启动类

```java
// 开启OpenFeign功能，并指定openFeign接口所在包
@EnableFeignClients(basePackages = "com.handle.open.feign.api")
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 使用openFeign的接口

```java
@Service
public class AccountServiceImpl implements AccountService{
    @Resource
    private AccountApi accountApi;

    @Override
    public ResultVo<AccountPo> getAccount(Long userId) {
        return accountApi.getAccount(userId);
    }
}
```

#### 超时配置

- 全局配置

```properties

# 连接超时
spring.cloud.openfeign.client.config.default.connectTimeout=5000
# 处理超时
spring.cloud.openfeign.client.config.default.readTimeout=5000
```

- 指定配置（会覆盖全局配置）

```properties

# 连接超时
spring.cloud.openfeign.client.config.服务名.connectTimeout=5000
# 处理超时
spring.cloud.openfeign.client.config.服务名.readTimeout=5000
```

#### 重试配置

```java
@Configuration
public class OpenFeignConfiguration {
    @Bean
    public Retryer retryer() {
        // 默认Retryer.NEVER_RETRY，这里设置为1000ms后，每隔1s，最多请求3次
        return new Retryer.Default(1000, 1, 3);
    }
}
```

#### 使用 Apache HttpClient 5

- 添加依赖

```xml
<!-- 包含了httpclient5 -->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-hc5</artifactId>
</dependency>
```

- 修改配置文件

```properties
spring.cloud.openfeign.httpclient.hc5.enabled=true
```

#### 请求/响应压缩

```properties
# 开启GZIP压缩
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.response.enabled=true

# 触发压缩的数据类型
spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json
# 触发压缩的最小值
spring.cloud.openfeign.compression.request.min-request-size=2048
```

#### 日志打印

- 配置方式（openfeign日志级别和接口日志级别都要设置）

```properties
# openfeign日志
spring.cloud.openfeign.client.config.default.logger-level=full
# 指定openfeign接口日志等级
logging.level.com.handle.base.api.GatewayAccountApi=debug
```

- IOC方式（还没测试过接口日志等级不写会不会打印）

```java
@Configuration
public class OpenFeignConfiguration {
    @Bean
    public Logger.Level openFeignLoggerLevel() {
        return new Logger.Level.BASIC;
    }
}
```

### BUS 消息总线

- maven dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 应用配置

```properties
# rebbitmq配置
rebbitmq.host=host
rebbitmq.port=5672
rebbitmq.username=guest
rebbitmq.password=guest

# 暴露bus刷新配置的端点
management.endpoints.web.exposure.include=bus-refresh
```

### 链路追踪

#### 收集用micrometer

- 依赖

```xml
<dependences>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-tracing</artifactId>
    </dependency>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-tracing-bridge-brave</artifactId>
    </dependency>
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-observation</artifactId>
    </dependency>
    <dependency>
        <groupId>io.github.openfeign</groupId>
        <artifactId>feign-micrometer</artifactId>
    </dependency>
    <dependency>
        <groupId>io.zipkin.reporter2</groupId>
        <artifactId>zipkin-reporter-brave</artifactId>
    </dependency>
    <!-- 要依赖actuator的自动配置（配置文件里面management的配置） -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependences>
```

- 配置

```properties
# 数据发给zipkin展示，指定其地址信息，http://不要漏掉
management.zipkin.tracing.endpoint=http://localhost:9411/api/v2/spans
# 采样频率，默认0.1（10次记录一次），值越大收集越及时
management.tracing.sampling.probability:1.0
```

#### 展示用zipkin

- 官网：<https://zipkin.io/>

- 安装运行后访问页面：<http://localhost:9411/>

- 需要手动点`RUN QUERY`，刷新页面不会更新数据

- 请求链路追踪：一条链路通过trace id唯一标识， span表示发起的请求信息，各span通过parent id 关联起来

### Spring Cloud Gateway

客户端的请求先通过匹配规则找到合适的路由，就能映射到具体的服务

然后请求经过过滤器处理后转发给具体的服务，服务处理后，再次经过过滤器处理，最后返回给客户端

#### 三大核心

- Route，路由，它由id，目标url，一系列断言和过滤器组成，断言为true则匹配该路由，解决找不找得到的问题

- Predicate，断言，匹配条件，用来匹配请求中的所有内容（如请求头、请求参数等），如果请求和断言匹配，则进行路由，解决找到后能不能访问的问题

- Filter，过滤器，GatewayFilter的实例，可在请求被路由前或者之后对请求进行增强

#### Route

##### id

```yaml
spring:
    cloud:
        gateway:
            routes:
                -   # 路由id，自定义key
                    id: gateway-test-service-route
```

##### uri

```yaml
spring:
    cloud:
        gateway:
            routes:
                - 
                    # 匹配后提供服务的路由
                    # 服务地址不要写死http://localhost:8080
                    # 要写成lb://服务在注册中心的名称，这样要是服务端口变化了就不用做改动，也可实现负载均衡
                    uri: lb://gateway-test-service
```

#### Predicate

##### 官方提供的断言

```yaml
spring:
    cloud:
        gateway:
            routes:
                - 
                    predicates:
                        # predicate格式：predicate名称=值
                        # 使用Path，与此路径相匹配的才进行路由
                        - Path=/gateway/getInfo/**
                        # 时间断言，指定一个Java ZonedDateTime，在这个时间点之前/后/之间才能访问，格式可通过ZonedDateTime.now(ZoneId.systemDefault()).toString()得到
                        - After=2024-10-14T16:42:09.008501600+08:00[Asia/Shanghai]
                        - Before=2099-10-14T16:42:09.008501600+08:00[Asia/Shanghai]
                        - Between=2024-10-14T16:50:09.008501600+08:00[Asia/Shanghai],2099-10-14T16:51:09.008501600+08:00[Asia/Shanghai]
                        # 指定请求头包含某个Cookie（key和value都要匹配）才能访问
                        - Cookie=username,handle
                        # 指定请求头要有某个属性，并且属性值符合某个正则表达式才能访问
                        - Header=X-Request-Id,\d+
                        # 指定请求头的Host值符合某个匹配规则才能访问
                        - Host=**.handle.org,**.handle.com
                        # 指定请求方式（例如GET），符合才能访问
                        - Method=GET
                        # 指定请求参数名，以及参数值符合某个正则表达式才能访问
                        - Query=userId,\d+
                        # IP地址表示方法（CIDR），24表示ip地址前缀的位数，最大不能超过32，约束其前缀必须为192.168.56，最后一个字节是0-255任意，其它IP不允许访问
                        - RemoteAddr=192.168.56.1/24
                        # - Weight=group1, 2
                        # - XForwardedRemoteAddr=192.168.1.1/24
```

##### 自定义断言

- 仿照官方的过滤器实现，命名必须为XxxRoutePredicateFactory

#### Filter

##### Global Filters

- 作用于所有路由

```yaml
spring:
    cloud:
        gateway:
            default-filters:
                - PrefixPath=/gateway
```

##### GatewayFilter

- 作用于单一路由，官方提供了大量的GatewayFilter

```yaml
spring:
    cloud:
        gateway:
            routes:
                -
                    id: add_request_header_route
                    uri: https://example.org
                    filters:
                        # 请求头过滤器
                        - AddRequestHeader=X-Request-Red, red
                        # 多个AddRequestHeader另写一行
                        - AddRequestHeader=X-Request-Green, green
                        - SetRequestHeader=X-Request-Green, GREEN
                        - RemoveRequestHeader=X-Request-Yellow

                        # 请求参数过滤器
                        # 如果请求参数里面有red=RED，会覆盖AddRequestParameter设置的red=red
                        - AddRequestParameter=red, red
                        # 前端传red为任何值，后端获取red的值，都为null
                        - RemoveRequestParameter=red

                        # 响应头过滤器
                        - AddResponseHeader=X-Response-Red, red
                        - SetResponseHeader=X-Response-Red, RED
                        - RemoveResponseHeader=X-Response-Yellow

                        # 路径过滤器
                        # 添加路径前缀，加了前缀的请求地址和断言配置的Path路径做比对
                        - PrefixPath=/gateway

                        # 假设断言配置的Path=/abc/{placeholder}，Path除了{placeholder}的内容，其它的内容会被SetPath取代
                        # 比如真实地址是：http:/localhost/gateway/getInfo
                        # 只配置了Path=/abc/{placeholder}和SetPath=/gateway/{placeholder}
                        # 然后请求地址变成：http:/localhost/abc/getInfo依然能够正确访问
                        - SetPath=/gateway/{placeholder}

                        # 符合断言Path配置的请求都跳转到百度
                        - RedirectTo=302, https://www.baidu.com
                        # 符合断言Path配置的请求都跳转到百度，并且带上原来的请求参数
                        - RedirectTo=302, https://acme.org, true

```

##### 自定义Filter

##### 自定义全局Filter

```java
// 加入IOC
@Component
public class ProcessTimeGlobalFilter implements GlobalFilter, Ordered {
    private static final String BEGIN_TIME = "BEGIN_TIME";

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        exchange.getAttributes().put(BEGIN_TIME, Instant.now().toEpochMilli());
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            Long beginTime = exchange.getAttribute(BEGIN_TIME);
            if (Objects.nonNull(beginTime)) {
                String host = exchange.getRequest().getURI().getHost();
                int port = exchange.getRequest().getURI().getPort();
                String path = exchange.getRequest().getURI().getPath();
                String rawQuery = exchange.getRequest().getURI().getRawQuery();
                long processTime = Instant.now().toEpochMilli() - beginTime;
                System.out.println(host + ": " + port + path + "parameter" + rawQuery + " 接口调用时长：" + processTime);
            }
        }));
    }

    // 越小优先级越高
    @Override
    public int getOrder() {
        return 0;
    }
}
```

##### 自定义单一Filter

- 仿照官方的过滤器实现，类名必须是XxxGatewayFilterFactory

#### Gateway微服务

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!-- 需要入驻注册中心 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!-- 填写uri:lb://gateway-test-service的时候需要依赖负载均衡 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<!-- 不需要spring-boot-starter-web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 配置

```yaml
server:
    port: 8000
spring:
    application:
        name: gateway-service
    cloud:
        nacos:
            discovery:
                server-addr: localhost:8848
                namespace: b70655ab-8c47-4dbb-b6b0-589f8eda9441
                username: nacos
                password: nacos
        gateway:
            routes:
                -
                    id: gateway-test-service-route
                    uri: lb://gateway-test-service
                    predicates:
                        - Path=/gateway/getInfo/**
```

- 主启动类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

#### gateway + sentinel

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-simple-http</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-spring-cloud-gateway-adapter</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 配置

```yaml
server:
    port: 8000
spring:
    application:
        name: gateway-sentinel-service
    cloud:
        nacos:
            discovery:
                server-addr: localhost:8848
                namespace: b70655ab-8c47-4dbb-b6b0-589f8eda9441
                username: nacos
                password: nacos
        gateway:
            routes:
                -
                    id: gateway-test-service-route
                    uri: lb://gateway-test-service
                    predicates:
                        - Path=/gateway/getInfo/**
```

- 配置文件

```java
@Configuration
public class GatewayConfiguration {
    private final List<ViewResolver> viewResolvers;

    private final ServerCodecConfigurer serverCodecConfigurer;

    // 构造方法
    public GatewayConfiguration(ObjectProvider<List<ViewResolver>> viewResolversProvider,
            ServerCodecConfigurer serverCodecConfigurer) {
        this.viewResolvers = viewResolversProvider.getIfAvailable(Collections::emptyList);
        this.serverCodecConfigurer = serverCodecConfigurer;
    }

    // 注入SentinelGatewayBlockExceptionHandler
    @Bean
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public SentinelGatewayBlockExceptionHandler sentinelGatewayBlockExceptionHandler() {
        // Register the block exception handler for Spring Cloud Gateway.
        return new SentinelGatewayBlockExceptionHandler(viewResolvers, serverCodecConfigurer);
    }

    // 注入SentinelGatewayFilter
    @Bean
    @Order(-1)
    public GlobalFilter sentinelGatewayFilter() {
        return new SentinelGatewayFilter();
    }


    // 官方代码里面这个注解还是javax.annotation-api的
    // 但是@PostConstruct已经是jakarta.annotation.PostConstruct的了，测试了没问题
    @PostConstruct
    public void init() {
        initGatewayRules();
    }

    // 初始化
    private void initGatewayRules() {
        Set<GatewayFlowRule> rules = new HashSet<>();
        // 定义限流规则，一秒钟QPS超过2个进行限流
        rules.add(new GatewayFlowRule("gateway-test-service-route").setCount(2)
                                                                   .setIntervalSec(1));
        GatewayRuleManager.loadRules(rules);

        // 定义触发限流返回的信息内容
        BlockRequestHandler blockHandler = (serverWebExchange, throwable) -> {
            HashMap<String, Object> map = new HashMap<>();
            map.put("code", HttpStatus.TOO_MANY_REQUESTS.value());
            map.put("message", "触发限流，请不要频繁请求");
            return ServerResponse.status(HttpStatus.TOO_MANY_REQUESTS)
                                 .contentType(MediaType.APPLICATION_JSON)
                                 .body(BodyInserters.fromValue(map));
        };
        GatewayCallbackManager.setBlockHandler(blockHandler);
    }
}
```

## Spring Cloud Vault

vault官网：<https://github.com/hashicorp/vault>

vault支持hcl和json两种格式的配置文件

### 使用docker版本vault

```sh
docker pull hashicorp/vault:1.19.2
```

- 使用json格式配置的compose.yaml

```yaml
services: 
    vault:
        image: hashicorp/vault:1.19.2
        container_name: vault
        command: server
        ports:
            - "8200:8200"
        cap_add:
            - IPC_LOCK
        environment:
            VAULT_LOCAL_CONFIG: >-
                { 
                    "storage": {
                        "file": {
                            "path": "/vault/file"
                        }
                    }, 
                    "listener": [
                        {
                            "tcp": { 
                                "address": "0.0.0.0:8200",
                                "tls_disable": false,
                                "tls_cert_file": "/vault/config.d/vaultPublicKey.pem",
                                "tls_key_file": "/vault/config.d/vaultPrivateKey.pem"

                            }
                        }
                    ], 
                    "default_lease_ttl": "168h", 
                    "max_lease_ttl": "720h", 
                    "ui": true
                }
        volumes:
            - vaultData:/vault/file
            # 这里使用目录映射，记得将vaultPublicKey.pem和vaultPrivateKey.pem文件复制到/path/to/data/vault/config
            - /path/to/data/vault/config:/vault/config.d
volumes: 
    vaultData:
        name: vaultData
```

- 或者是使用json配置文件的compose.yaml

```yaml
services: 
    vault:
        image: hashicorp/vault:1.19.2
        container_name: vault
        command: server -config=/vault/config.d/vault.json
        ports:
            - "8200:8200"
        cap_add:
            - IPC_LOCK
        environment:
        volumes:
            - vaultData:/vault/file
            # 这里使用目录映射，记得将vaultPublicKey.pem、vaultPrivateKey.pem和vault.json文件复制到/path/to/data/vault/config
            - /path/to/data/vault/config:/vault/config.d
volumes: 
    vaultData:
        name: vaultData
```

- vault.json

```json
{                                
    "storage": { 
        "file": {                
            "path": "/vault/file"
        }           
    },                                    
    "listener": [                         
        {                                                        
            "tcp": {                                             
                "address": "0.0.0.0:8200",                      
                "tls_disable": false,                            
                "tls_cert_file": "/vault/config.d/publicKey.pem",
                "tls_key_file": "/vault/config.d/privateKey.pem"
                                
            }                   
        }                   
    ],
    "default_lease_ttl": "168h",
    "max_lease_ttl": "720h",
    "ui": true
}
```

- 访问：<http://10.0.2.15:8200>
    - Key shares设置为1
    - Key threshold设置为1
    - 点击初始化
    - 点击Download keys，下载保存好root token（相当于登录vault的密码）和Key1（相当于密钥，vault管理的密码是经过加密了的，需要用它解密）
    - Unseal Vault的时候用Key1
    - Sign in to Vault默认是token，这时候用root token就行
    - 登录进去后，看到的Secrets Engines就是存储密钥的仓库
    - 导航栏选择Secrets Engines，点击"Enable new engine +"
    - 选择KV，它以键值对方式加密存储信息，如帐号和密码
    - Path输入框固定输入secret就行
    - Maximum number of versions，这是版本控制，会记录这个数据最近N次的变化，如填个10
    - 点击Enable Engine，启用这个KV加密引擎
    - 点击"Create secret +"，"Path for this secret"填个应用名称就行
    - Secret data中填写要添加的键值对，如key：mysql.username，value：具体mysql用户名，最后点击Save保存

- 如果是进入容器终端环境执行命令

```sh
# 这里的容器shell是/bin/sh或sh，不能是/bin/bash和bash
docker exec -it yourVaultName sh

export VAULT_ADDR=https://127.0.0.1:8200

# 如果提示证书验证失败可以这样
export VAULT_SKIP_VERIFY=true

# 然后就可以输入操作命令了
```

### 使用vault-ui

官网：<https://github.com/djenriquez/vault-ui>，代码已经归档好多年了

vault本身的UI界面是限制了创建新Token的功能的，要通过安装vault-ui软件来操作或者通过进入容器使用命令行创建

除了创建创建新Token的功能外，个人感觉其它功能还是官方的UI好用

vault-ui还有appimage版本的包，这里介绍docker版本的使用

```sh
docker pull djenriquez/vault-ui:2.3.0
```

- compose

```yaml
services: 
    vaultUi:
        image: djenriquez/vault-ui:2.3.0
        container_name: vaultUi
        ports:
            - "8000:8000"
        environment:
            # CUSTOM_CA_CERT: /path/to/vaultPublicKey.pem
            # 如果vault配置了https，需要设置这个环境变量来禁用TLS server side校验，上面的方法也可以，但是没试过，二选一就行了
            NODE_TLS_REJECT_UNAUTHORIZED: 0
```

- 访问：<http://yourip:8000/>

### Spring Cloud 集成Vault

- 依赖

```xml
<!-- 在应用启动的时候自动连接到vault服务器，把需要的数据拉到本地从而注入到环境变量中 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-vault-config</artifactId>
</dependency>

<!-- 在应用启动前完成配置信息的拉取 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

- bootstrap.yaml配置文件

```yaml
spring.application.name: spring-cloud-vault-demo
spring.cloud.vault:
    # vualt服务器地址，如果用的是https，那么生成证书的keystore用的是域名这里也必须是域名，否则会报错
    host: www.example.com
    port: 8200
    # 设置https的话后面必须设置ssl
    scheme: https
    # 鉴权方式
    authentication: TOKEN
    # vualt的root token权限太大了，应该生成一个只允许访问特定密钥仓库的令牌填到这里
    token: 你的vault token
    kv:
        # vault定义的Secrets Engines的具体Path
        backend: secret
    # vault定义的具体backend下的特定Path for this secret
    application-name: dubhe
    # 设置ssl
    ssl:
        # 记得将vaultPublicKey.pem复制到resources目录下
        trust-store: classpath:vaultPublicKey.pem
        trust-store-type: PEM
        # 设置启用的SSL/TLS协议列表
        enabled-protocols: TLSv1.2,TLSv1.3
        # 设置启用的SSL/TLS密码套件列表
        enabled-cipher-suites: TLS_AES_128_GCM_SHA256
```

- application.properties配置文件

```properties
# 获取vault中的配置的key的value值，mysql.username是vault中的key
username=${mysql.username}
password=${mysql.password}
```

- controller

```java
@RestController
public class ApplicationController {
    // 使用application.properties中的key
    @Value("${username}")
    private String username;

    // 也可以直接使用vault中的key
    @Value("${mysql.password}")
    private String password;

    @GetMapping("/")
    public String get() {
        return "get from vault: \t" + "username: " + username + "\t password: " + password + "\t timestamp: " + Instant.now().toEpochMilli();
    }
}
```

### 设置访问策略

- 点击导航栏Policies
- 点击Create ACL Policies
- 填写策略

```conf
# 假设只让这个Token增删改secret/myapp下的键值对
# 创建secret engine的时候点击Methods Options可以看到KV版本默认是2
# KV版本是2的引擎读取数据时，路径中需要加上/data/，这个可以在secret engine如secret/app的Overview的API
# 这样写Spring Boot仍然会去默认路径secret/application下找键值对，因为没有权限而报错，但是不影响运行
# 如果觉得别扭可以设置为"secret/*"或"secret/data/*"就不会报错了，但是这样设置使用这个策略的token的权限就更大了，具体情况具体分析吧
path "secret/data/myapp" {
    capabilities = [ "create", "read", "update", "list" ]
}
```

- 点击Create policy

- 创建新Token，将创建的访问策略添加进去就可以了

### Vault集群

教程官网：<https://developer.hashicorp.com/vault/tutorials/raft/raft-storage>

以下教程需要先系统上安装vault，去官网下载压缩包，解压后将vault文件放到/usr/local/bin目录下就可以了

vault集群的配置需要用到cluster.sh脚本，在<https://github.com/hashicorp-education/learn-vault-raft>可以下载，后面会有具体的教程

cluster.sh脚本会配置和启动4个vault服务，如结构下图

![vaultCluster](/images/vaultCluster.png)

脚本初始化并解封vault_1 (<http://127.0.0.1:8200>)，它不加入集群，其root token创建一个自动解封其它vault服务的传输key

脚本初始化并解封vault_2 (<http://127.0.0.2:8200>)，启动后作为集群leader，脚本启用K/V-V2加密引擎作为一个例子

脚本启动vault_3 (<http://127.0.0.3:8200>)，但是需要手动加入集群

脚本启动vault_4 (<http://127.0.0.4:8200>)，但是需要手动加入集群

```sh
# 1.创建并进入目录$HOME/vault-tutorial
mkdir $HOME/vault-tutorial && cd $HOME/vault-tutorial

# 2.复制git项目
git clone https://github.com/hashicorp-education/learn-vault-raft

# 3.进入项目根目录
cd learn-vault-raft/raft-storage/local

# 4.给脚本添加执行权限，笔者下载下来检查发现已经是有执行权限了
chmod +x cluster.sh

# 5.为每个vault创建本地回环地址
# loop back：在计算机里，它指的是数据包发出去后，不会经过物理网卡，而是直接在系统内部“绕一圈”又回到自己这里
./cluster.sh create network

# 6.为每个vault创建配置文件
./cluster.sh create config

# 7.检查当前shell是否存在环境变量，如果已存在则必须执行unset VAULT_TOKEN，才能继续后续步骤
printenv | grep VAULT_TOKEN

# 安装vault_1
# 如果包jq找不到则安装：sudo apt-get install jq
# 然后sudo ./cluster.sh clean，回到第5步重新开始
./cluster.sh setup vault_1

# 安装vault_2
./cluster.sh setup vault_2

# 安装vault_3
./cluster.sh setup vault_3

# 可以看到集群中只有一个leader节点vault_2
export VAULT_ADDR="http://127.0.0.2:8200"
vault operator raft list-peers

# 打开一个新终端并进入指定目录
cd $HOME/vault-tutorial/learn-vault-raft/raft-storage/local

# vault_3加入集群
export VAULT_ADDR="http://127.0.0.3:8200"
vault operator raft join http://127.0.0.2:8200

# 配置vault CLI的请求使用vault_2的root token
export VAULT_TOKEN=$(cat root_token-vault_2)

# 可以看到vault_3作为follower出现在集群节点里面
vault operator raft list-peers

# 查看vault_3日志
cat vault_3.log

# 最后确认可以读取kv/apikey路径的kv
vault kv get kv/apikey
```

接下来，可以像vault_3的步骤一样将vault_4加入集群

但是，如果所有节点的连接明细是事先已经确认的话，可以在配置文件中配置retry_join块来自动加入集群

修改config-vault_4.hcl配置文件，在storage块里面加入retry_join

```conf
storage "raft" {
    path    = "<path_to_local>/raft-vault_4/"
    node_id = "vault_4"
    # 因为vault_2和vault_3的地址是已知的
    # 可以在retry_join块里面预定义可能为集群leader的节点地址
    retry_join {
        leader_api_addr = "http://127.0.0.2:8200"
    }
    retry_join {
        leader_api_addr = "http://127.0.0.3:8200"
    }
}
```

- 启动vault_4

```sh
# 启动vault_4
./cluster.sh start vault_4

# 打开一个新终端并进入指定目录
cd $HOME/vault-tutorial/learn-vault-raft/raft-storage/local

# 配置当前shell的环境变量VAULT_ADDR
export VAULT_ADDR="http://127.0.0.4:8200"

# 可以看到vault_4作为follower出现在集群节点里面
vault operator raft list-peers

# 配置vault CLI的请求使用vault_2的root token
export VAULT_TOKEN=$(cat root_token-vault_2)

# patch路径kv/apikey的键，设置有效期365天
vault kv patch kv/apikey expiration="365 days"

# 回到vault_3的终端，执行如下命令，可以看到多了expiration的信息
vault kv get kv/apikey
```

- vault配置文件的部分说明

```conf
# raft表示使用Integrated Storage
storage "raft" {
    # Vault数据存储的地方
    path    = "/path/to/raft-vault_2/"
    node_id = "vault_2"
}
```

### 数据快照

Integrated Storage提供了一个创建数据快照的接口

这些快照如果后期有需要可以用于数据恢复

社区版是没有自动快照功能的，可以通过定时任务来定时创建快照

回到VAULT_ADDR设置为vault_2的地址(<http://127.0.0.2:8200)的终端>

```sh
# 对当前数据创建快照
# 在当前目录下会创建一个demo.snapshot文件
vault operator raft snapshot save demo.snapshot
```

- 模拟数据丢失

```sh
# 确认kv/apikey有数据
vault kv get kv/apikey

# 删除kv/apikey的数据
vault kv metadata delete kv/apikey

# 确认kv/apikey的数据已删除
vault kv get kv/apikey
```

- 从快照恢复数据

```sh
# 通过恢复demo.snapshot中找到的数据来恢复数据
vault operator raft snapshot restore demo.snapshot

# 查看当前集群节点（vault_2）的末尾的几行日志
grep -B3 'snapshot installed' vault_2.log

# 确认数据已恢复
vault kv get kv/apikey
```

### leader退役（变成follower，其它节点变成leader）

```sh
# 在VAULT_ADDR为http://127.0.0.2:8200（vault_2)的终端执行
vault operator step-down

# 然后在VAULT_ADDR为http://127.0.0.3:8200（vault_3)的终端执行
# 可以看到vault_2变成follower，vault_3晋升变成leader了
vault operator raft list-peers
```

### 移除集群成员

为了维护、升级或保存计算资源，从集群中删除节点可能变得很重要

```sh
# 从集群中移除vault_4节点
vault operator raft remove-peer vault_4

# 在其它节点执行，确认vault_4已经从集群中移除
vault operator raft list-peers
```

### 将vault_4重新添加回集群

```sh
# 在VAULT_ADDR为http://127.0.0.4:8200（vault_4)的终端执行
./cluster.sh stop vault_4

# 删除vault_4的数据目录
rm -rf raft-vault_4

# 重新创建vault_4的数据目录raft-vault_4
# 因为raft的存储目录在启动vault服务前必须已经存在
mkdir raft-vault_4

# 启动vault_4
./cluster.sh start vault_4

# 确认节点已经添加到集群
vault operator raft list-peers
```

### 恢复模式

如果由于存储中的条目损坏而导致Vault服务宕机，操作员可能需要在恢复模式下启动Vault

在恢复模式下，Vault以最小的功能运行，并暴露其子集API

```sh
# 停止集群中的所有存活的节点
./cluster.sh stop vault_2
./cluster.sh stop vault_4
./cluster.sh stop vault_3

# 以恢复模式启动vault_3
VAULT_TOKEN=$(cat root_token-vault_1) VAULT_ADDR=http://127.0.0.3:8200 \
            vault server -recovery -config=config-vault_3.hcl

# 打开一个新终端并进入指定目录
cd $HOME/vault-tutorial/learn-vault-raft/raft-storage/local

# 配置当前shell的环境变量VAULT_ADDR
export VAULT_ADDR="http://127.0.0.3:8200"

# 生成一个一次性密码(OTP)
vault operator generate-root -generate-otp -recovery-token

# 使用上一步得到的一次性密码生成恢复令牌(recovery token)
vault operator generate-root -init \
    -otp=UPeg0ZMO6F16wawH7XFX9jX2jTud -recovery-token

# 查看在安装vault_3期间生成的recovery key（恢复密钥）
# 使用恢复密钥而不是解封密钥，因为此集群配置了传输自动解封（集群默认Transit auto-unseal）
cat recovery_key-vault_2

# 创建一个编码令牌（encoded token），根据交互填入上一步得到的恢复密钥
vault operator generate-root -recovery-token

# 最后使用编码令牌和一次性密码完成恢复令牌的创建
# 比如得到恢复令牌：hvr.oK3gSD81MPQs8o3mzbQgpDEy
vault operator generate-root \
  -decode=PSYXSV8RfihlAgYPOjEmOw53dTVDCAlVGhAwHQ \
  -otp=UPeg0ZMO6F16wawH7XFX7jX2jTud \
  -recovery-token

# 在恢复模式，只能跟原始系统存储进行交互
# 使用恢复令牌解决问题吧
# 例子：列出sys/raw/sys的内容
VAULT_TOKEN=hvr.oK3gSD81MPQs8o3mzbQgpDEy vault list sys/raw/sys

# 解决问题后，继续常规操作
# 在运行恢复模式的终端按Ctrl+C停止恢复模式
# 重新启动所有vault节点
# 因为在恢复模式下启动节点时，它会重置集群成员列表
# 这意味着在恢复常规操作时，每个节点都需要重新加入集群
# 笔者在操作的时候发现vault_2死活加入不了vault_3，不知道出了什么问题
./cluster.sh start vault_3
./cluster.sh start vault_2
```

### 清理cluster.sh生成的数据

```sh
./cluster.sh clean
```

### 不用脚本，手动搭建vault集群

启动vault_node_1后，这里为了简便，用浏览器访问https:vault_node_1_ip:8200，选择"Create a new Raft cluster"

Vault 在初始化时会生成一把能解开所有数据的“总钥匙”（根密钥），但它不会直接把这把钥匙交给你，而是把它拆分成若干份碎片。

Key shares（密钥份额/总份数）：代表 Vault 会把这把“总钥匙”拆分成多少份碎片（Unseal Keys）。

Key threshold（密钥阈值/所需份数）：代表在 Vault 重启或解封时，至少需要凑齐多少份碎片，才能把“总钥匙”重新拼凑出来，从而解开 Vault。

这里都填1就行了，生产环境可以设置根据情况设置，如（Key shares：5，Key threshold：3）

下一步把钥匙下载下来，然后点击继续解封，解封后就可以进入ui了

```sh
vault operator raft list-peers

# 如果报下面的错
# Error reading the raft cluster configuration: Error making API request.
# URL: GET https://127.0.0.1:8200/v1/sys/storage/raft/configuration
# Code: 403. Errors:
# * 2 errors occurred:
#        * permission denied
#        * invalid token
# 先用跟令牌登录
vault login
```

启动vault_node_2后，这里为了简便，用浏览器访问https:vault_node_2_ip:8200，选择"Join an existing Raft cluster"

```sh
export VAULT_ADDR=https://127.0.0.1:8200

# 如果提示证书验证失败可以这样
# tls: failed to verify certificate: x509: cannot validate certificate for 127.0.0.1 because it doesn't contain any IP SANs
export VAULT_SKIP_VERIFY=true
```

## Spring Cloud Alibaba

- maven dependency

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>{project-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### Nacos

- 官网：<https://nacos.io/zh-cn/index.html>

- github：<https://github.com/alibaba/nacos>

#### Nacos部署

##### 数据持久化

- 1.创建nacos数据库

```sql
create database if not exists `nacos` default character set utf8mb4 collate utf8mb4_unicode_ci;
```

- 2.执行[nacos-mysql.sql](/sql/nacos-mysql-schema.sql)脚本

- 3.修改conf/application.properties配置

```properties
spring.sql.init.platform=mysql
db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?useUnicode=true&characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&serverTimezone=GMT%2B8
db.user.0=root
db.password.0=mysql123
```

- 4.运行nacos服务

```sh
# 单机启动
sh startup.sh -m standalone

# 集群启动
sh startup.sh
```

- 访问nacos页面：<http://localhost:8848/nacos>

##### nacos集群

- 1）nacos解压缩后复制成n份

- 2）修改cluster.conf文件配置

```properties
# 填 hostname -i 命令显示的ip，如果是同一ip端口不能连号！
192.168.31.149:3333
192.168.31.149:4444
192.168.31.149:5555
```

- 3）分别修改n个nacos的application.properties文件里面的端口号

- 4）分别启动n个nacos服务

#### nacos-docker

- compose.yaml

```yaml
nacos:
    container_name: nacos01
    image: nacos/nacos-server:v2.3.0
    ports:
        - "8848:8848"
        - "9848:9848"
    environment:
        - MODE=standalone
        - SPRING_DATASOURCE_PLATFORM=mysql
        - MYSQL_DATABASE_NUM=1
        - MYSQL_SERVICE_HOST=mysql01
        - MYSQL_SERVICE_PORT=3306
        - MYSQL_SERVICE_DB_NAME=nacos
        - MYSQL_SERVICE_USER=root
        - MYSQL_SERVICE_PASSWORD=mysql123
        # 在默认的连接参数后加上allowPublicKeyRetrieval=true，这样就不用手动连接一次mysql才能成功启动nacos
        - MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=GMT%2B8
        # 登录配置
        - NACOS_AUTH_SYSTEM_TYPE=nacos
        - NACOS_AUTH_ENABLE=true
        - NACOS_AUTH_TOKEN=VGhpc0lzTXlDdXN0b21TZWNyZXRLZXkwMTIzNDU2Nzg=
        - NACOS_AUTH_IDENTITY_KEY=nacos
        - NACOS_AUTH_IDENTITY_VALUE=nacos
    volumes:
        - /handle/data/nacos/logs/:/home/nacos/logs
    networks: 
        - my-docker-net
    depends_on:
        - mysql
    restart: always
```

#### 注册中心

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 配置

```properties
spring.application.name=nacos-discovery-service
server.port=8080
spring.cloud.nacos.discovery.server-addr=localhost:8848
# nacos注册中心命名空间id
spring.cloud.nacos.discovery.namespace=6643e6b9-6ca9-4d8f-86bd-34fbb893a976
# nacos注册中心分组名称
spring.cloud.nacos.discovery.group=DEV_GROUP
# spring.cloud.nacos.discovery.username=nacos
# spring.cloud.nacos.discovery.password=nacos
```

- 主启动类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 配置负载均衡

注册到Nacos的微服务消费者新建配置类

```java
@Configuration
public class ApplicationConfiguration {
    @LoadBalanced
    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

- （对于虚拟机的NAT和docker版本的nacos）做好9848、9849端口的映射

#### 配置中心

- Data ID命名规则：${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}

- 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 新建文件bootstrap.properties作为全局配置

```properties
server.port=3377

spring.application.name=nacos-config-client

# nacos注册中心地址
spring.cloud.nacos.discovery.server-addr=www.laodeli.top:8848

# nacos注册中心命名空间id
spring.cloud.nacos.discovery.namespace=6643e6b9-6ca9-4d8f-86bd-34fbb893a976

# nacos注册中心分组名称
spring.cloud.nacos.discovery.group=DEV_GROUP

# nacos服务配置中心地址
spring.cloud.nacos.config.server-addr=www.laodeli.top:8848

# 设置配置中心命名空间id
spring.cloud.nacos.config.namespace=6643e6b9-6ca9-4d8f-86bd-34fbb893a976

# 设置配置中心分组名称
spring.cloud.nacos.config.group=DEV_GROUP

# 配置文件后缀
spring.cloud.nacos.config.file-extension=properties
```

- 新建文件application.properties作为应用配置

```properties
# 读配置中心${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}文件
# dev、test、pro
spring.profiles.active=dev
```

- 主启动类

```java
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- 控制器类

```java
@RestController
// 使当前类下的配置支持动态刷新
@RefreshScope
@RestController
@RequestMapping("/nacos/config")
public class ApplicationController {
    @Value("${application.info}")
    private String info;

    @GetMapping("/getInfo")
    public String getInfo() {
        return info;
    }
}
```

### Sentinel

github:<https://github.com/alibaba/Sentinel/>
<br/>
官网：<https://sentinelguard.io/zh-cn/>

#### 控制台

- 下载sentinel-dashboard-1.8.1.jar

- 启动sentinel-dashboard-1.8.1.jar

- 页面：<http://localhost:8080>

- 用户名/密码：sentinel

- 采用的时懒加载，要手动请求一次接口才会在控制台刷新接口信息

#### 流控规则

##### 阈值类型

- QPS，每秒请求数

- 并发线程数，涉及线程上下文切换和线程回收，精确度不如QPS

##### 流控模式

- 直接，当资源（接口）的访问达到阈值后，限流自己

- 关联，当与资源A关联的资源B访问达到阈值后，限流资源A

- 链路，来自不同链路的请求对某一个资源进行访问，实施不同的限流措施

##### 流控效果

- 快速失败，直接抛出异常

- Warm Up，预热，使得系统从空闲状态，经过预期时间后到达最大请求阈值，而不是流量突然增大

    - 访问量阈值从阈值除以冷却因子（默认3）的结果值开始，经过预热时长逐渐提升到设定的阈值

    - 主要用于启动需要额外开销的场景，如建立数据库连接等

- 排队等待，访问达到阈值时，过了间隔时间才允许通过下一个请求

    - 主要用于处理间隔性突发的流量：系统某一时刻突然要处理大量请求，接下来的几秒却处于空闲状态的场景

    - 匀速排队模式暂不支持QPS大于1000的场景

#### 熔断规则

- 熔断策略
    - 慢调用比例
        - 请求的响应时间>最大RT(最大响应时间)，则统计为慢调用
        - 当单位统计时长内请求数目大于设置的最小请求数，且慢调用比例大于阈值，则接下来的熔断时长内请求会被熔断
        - 经过熔断时长后熔断器会进入半开状态，若接下来的一个请求响应时间小于设置的最大RT则结束熔断，否则再次被熔断
    - 异常比例
        - 当单位统计时长内请求数目大于设置的最小请求数，且异常比例大于阈值，则接下来的熔断时长内请求会被熔断
        - 经过
    - 异常数
        - 当单位统计时长内请求数目大于设置的最小请求数，且异常数大于阈值，则接下来的熔断时长内请求会被熔断
        - 经过熔断时长后熔断器会进入半开状态，若接下来的一个请求正常完成则结束熔断，否则再次被熔断

#### 热点规则

- 对请求接口的入参进行监控限流
- 参数例外项，当参数是某一个值时，它的阈值可以达到设置的限流阈值才限流
    - 填写完后记得点击添加按钮

#### 授权规则

- 需要先在客户端代码里面实现RequestOriginParser接口

```java
@Component
public class SentinelRequestOriginParser implements RequestOriginParser {
    @Override
    public String parseOrigin(HttpServletRequest request) {
        return request.getParameter("serverName");
    }
}
```

- handler

```java
@GetMapping("/blackList")
public ResultVo<String> blackList() {
    return ResultVo.success("test blackList");
}
```

- 控制台设置授权规则

![授权规则](/images/授权规则.png)

- 测试
    - url：<http://localhost:8080/sentinel/blackList?serverName=test>
    - 当请求参数serverName=test时，结果：Blocked by Sentinel (flow limiting)

#### 规则持久化

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

- 配置

```properties
# 配置规则持久化到nacos的json配置文件，rule1为自定义key，可以当作规则名，可以定义多个，分别配置不同的限流类型
spring.cloud.sentinel.datasource.rule1.nacos.server-addr=localhost:8848
spring.cloud.sentinel.datasource.rule1.nacos.namespace=b70655ab-8c47-4dbb-b6b0-589f8eda9441
spring.cloud.sentinel.datasource.rule1.nacos.data-id=${spring.application.name}
spring.cloud.sentinel.datasource.rule1.nacos.group-id=DEFAULT_GROUP
# 定义规则配置文件为json格式
spring.cloud.sentinel.datasource.rule1.nacos.data-type=json
# 流控规则，参考com.alibaba.cloud.sentinel.datasource.RuleType设置
spring.cloud.sentinel.datasource.rule1.nacos.rule-type=flow

#spring.cloud.sentinel.datasource.rule2.nacos.server-addr=localhost:8848
#spring.cloud.sentinel.datasource.rule1.nacos.namespace=b70655ab-8c47-4dbb-b6b0-589f8eda9441
#spring.cloud.sentinel.datasource.rule2.nacos.data-id=${spring.application.name}
#spring.cloud.sentinel.datasource.rule2.nacos.group-id=DEFAULT_GROUP
#spring.cloud.sentinel.datasource.rule2.nacos.data-type=json
## 熔断规则，参考com.alibaba.cloud.sentinel.datasource.RuleType设置
#spring.cloud.sentinel.datasource.rule2.nacos.rule-type=degrade
```

- 在nacos中相应的位置新增规则配置文件并发布

```json
[
    {
        // 资源名称
        "resource": "getInfo",
        // 来源应用
        "limitApp": "default",
        // 阈值类型：0-线程数，1-QPS
        "grade": 1,
        // 单机阈值
        "count": 1,
        // 流控模式：0-直接，1-关联，2-链路
        "strategy": 0,
        // 流控效果：0-快速失败，1-Warm UP，2-排队等待
        "controlBehavior": 0,
        // 是否集群
        "clusterMode": false
    }
]
```

#### 客户端

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

- 应用配置

```properties
# sentinel控制台地址
spring.cloud.sentinel.transport.dashboard=localhost:6060
# 默认8179，如果端口被占用会依次自增直到找到未被占有的端口
spring.cloud.sentinel.transport.port=8719
```

- 控制器类

```java
@RestController
@RequestMapping("/sentinel")
public class SentinelController {
    @SentinelResource(
    // 资源名称，对应sentinel控制台定义规则时的资源名，可以自定义或用请求路径
    value = "getInfo", 
    blockHandlerClass = SentinelBlockHandler.class, 
    // blockHandlerClass指定的类中的方法名称
    blockHandler = "getInfoBlockHandler",
    fallbackClass = SentinelFallback.class,
    // fallbackClass指定的类中的方法名称
    fallback = "getInfoFallback")
    @GetMapping("/getInfo")
    public ResultVo<String> getInfo() {
        return ResultVo.success("hello sentinel");
    }
}
```

- 自定义全局BlockHandler，负责sentinel配置的规则违规场景，自定义信息返回

```java
public class SentinelBlockHandler {
    // 必须为static，@SentinelResource注解的方法的形参怎么定义，此方法也怎么定义，最后再加上异常形参
    public static ResultVo<Object> getInfoBlockHandler(BlockException blockException) {
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), "自定义sentinel返回提示信息"); 
    }
}
```

- 自定义全局Fallback，负责业务异常，自定义异常信息返回

```java
public class SentinelFallback {
    // 必须为static，@SentinelResource注解的方法的形参怎么定义，此方法也怎么定义，最后再加上异常形参
    public static ResultVo<Object> getInfoFallback(Throwable throwable) {
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), "自定义异常返回提示信息"); 
    }
}
```

- fallback 和 blockHandler 都配置，则blockHandler处理

#### OpenFeign和Sentinel整合

目的：当接口对外提供服务时，可以将服务提供方的@SentinelResource的fallback放到FeignApi里面的fallback

##### 服务提供方

参考前面写的Sentinel客户端写法

##### FeignApi

- 接口，@FeignClient注解增加fallback属性

```java
@FeignClient(name = "account-service", fallback = AccountApiFallback.class)
public interface AccountApi {
    // 不能在接口上写@RequestMapping了，请求路径要写全（本体handler类上@RequestMapping注解的路径要加上来）
    @GetMapping("/account/getAccount")
    ResultVo<AccountOutputVo> getAccount(@RequestParam("userId") Long userId);
}
```

- fallback，实现接口并覆写方法，调用服务提供方异常时将会走此逻辑

```java
@Component
public class AccountApiFallback implements AccountApi {
    @Override
    public ResultVo<AccountOutputVo> getAccount(Long userId) {
        return ResultVo.failure(ResultCodeEnum.FAILURE.getCode(), "调用account-service失败");
    }
}
```

##### 服务调用方

- 依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

- 配置

```properties
# 如果有通过openFeign调用外部接口，需激活sentinel对feign的支持
feign.sentinel.enabled=true
```

- 主启动类

```java
// 指定openFeign接口所在包
@EnableFeignClients(basePackages = "com.handle.open.feign.api")
// 扫描fallback类所在包
@ComponentScan("com.handle.open.feign.api")
@EnableDiscoveryClient
@SpringBootApplication

public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Seata

#### seata-server

- 创建seata数据库

- 建表：[postgresql.sql](/sql/seata-server-postgresql.sql)

- 解压seata，进入conf文件夹下，参考application.example.yaml，修改application.yaml

[application.yaml](/yaml/application.yaml)

- 启动seata-server

```sh
bash seata-server.sh
```

- compose.yaml

```yaml
seata:
    container_name: seata01
    image: seataio/seata-server:2.0.0
    ports:
        - "7091:7091"
        - "8091:8091"
    environment:
        # 设置公网ip，seata客户端通过这个ip连接seata服务器
        - SEATA_IP=192.168.56.1
        - SEATA_PORT=8091
    volumes:
        - /handle/data/seata/application.yaml:/seata-server/resources/application.yaml
    networks: 
        - my-docker-net
    restart: always
    depends_on:
        - nacos
```

- 查看nacos服务列表中是否有seata，查看<http://localhost:7091/>是否能够正常访问

#### seata-client

- 与seata事务相关的数据库添加表（AT专用）：[postgresql.sql](/sql/seata-client-postgresql.sql)

- 创建测试用账户表[account.sql](/sql/account-postgresql.sql)

- 创建测试用订单表[order.sql](/sql/order-postgresql.sql)

- 创建测试用库存表[inventory.sql](/sql/inventory-postgresql.sql)

- maven dependency

```xml
<!-- seata 版本需要跟seata服务器版本一致 -->
<dependency>
    <groupId>io.seata</groupId>
    <artifactId>seata-spring-boot-starter</artifactId>
    <version>1.3.0</version>
</dependency>
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    <exclusions>
        <exclusion>
            <groupId>io.seata</groupId>
            <artifactId>seata-spring-boot-starter</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

- 应用配置

```properties
# seata 配置中心
seata.config.type=nacos
seata.config.nacos.server-addr=localhost:8848
seata.config.nacos.namespace=59ec6563-6292-4f6c-9e68-e2fb3b653823
seata.config.nacos.group=SEATA_GROUP
seata.config.nacos.username=nacos
seata.config.nacos.password=nacos
# seata注册中心
seata.registry.type=nacos
seata.registry.nacos.application=seata-server
seata.registry.nacos.server-addr=localhost:8848
seata.registry.nacos.namespace=59ec6563-6292-4f6c-9e68-e2fb3b653823
seata.registry.nacos.group=SEATA_GROUP
seata.registry.nacos.username=nacos
seata.registry.nacos.password=nacos

# 事务组名称，由它获得TC服务的集群名称
seata.tx-service-group=default_tx_group
# 事务组与TC服务集群的映射关系：事务组名称-TC服务集群名称
# seata.service.vgroup-mapping后面的事务组名称与seata.tx-service-group定义的事务组名称一致
seata.service.vgroup-mapping.default_tx_group=default
seata.data-source-proxy-mode=AT
```

- 在需要全局事务处理的控制器类、业务类实现方法上加@GlobalTransactional注解

## Spring Data

## Jackson

- @JsonIgnoreProperties 作用在类上用于过滤掉特定字段不返回或者不解析

```java
@Getter
@Setter
@ToString
@JsonIgnoreProperties({"userRoles"})
public class UserVO {
    private String userNo;

    private String userName;

    private List<UserRole> userRoles = new ArrayList<>();
}
```

- @JsonIgnore 作用于字段或getter/setter 方法级别，用于指定在序列化或反序列化时忽略该特定属性

```java
@Getter
@Setter
@ToString
public class UserVO {
    private String userNo;

    private String userName;

    @JsonIgnore 
    private List<UserRole> userRoles = new ArrayList<>();
}
```

- @JsonFormat 用于指定属性在序列化和反序列化时的格式。常用于日期时间类型的格式化。

```java
@Getter
@Setter
@ToString
public class UserVO {
    private String userNo;

    private String userName;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd 'T' HH:mm:ss", timezone = "GMT+8") 
    private LocalDateTime createTime;
}
```

- @JsonUnwrapped 注解作用于字段上，用于在序列化时将其嵌套对象的属性“提升”到当前对象的层级，反序列化时执行相反操作。这可以使 JSON 结构更扁平。

```java
@Getter
@Setter
@ToString
public class UserVO {
    private String userNo;

    private String userName;

    @JsonUnwrapped
    private PageInfo pageInfo;

    @Getter
    @Setter
    @ToString
    public static class PageInfo {
        private Integer currentPage;

        private Integer pageSize;
    }
}
```

```json
// 扁平化后的JSON结构为
{
  "userNo": "0101",
  "userName": "handle",
  "currentPage": "1",
  "pageSize": "15"
}
```

### 常规使用

- 依赖

```kotlin
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.18.0</version>
</dependency>
```

- 使用

```java
@Getter
@Setter
@ToString
public class User {
    private Long id;

    private String name;

    // 也可以不用@JsonSerialize和@JsonDeserialize注解，在创建ObjectMapper时，注册一个新的JavaTimeModule，
    // ObjectMapper mapper = new ObjectMapper().registerModule(new
    // JavaTimeModule());
    @JsonFormat(pattern = "yyyy-MM-dd", timezone = "GMT+8")
    // @JsonSerialize(using = LocalDateSerializer.class)
    // @JsonDeserialize(using = LocalDateDeserializer.class)
    private LocalDate birthday;
}

/**
 * 每个测试方法执行前执行一次
 */
@BeforeEach
public void init() {
    user = new User();
    user.setId(1L);
    user.setName("张三");
}

@Test
public void testJavaObjectToJson() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper().registerModule(new JavaTimeModule());
    // 反序列化时忽略不存在的JavaBean属性:
    objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

    // javabean转json
    String jsoString = objectMapper.writeValueAsString(user);
    System.out.println("user:" + jsoString);

    // json转javabean
    User user2 = objectMapper.readValue(jsoString, User.class);
    System.out.println("user2:" + user2.toString());

    List<User> list = new ArrayList<>();
    list.add(user);
    // java集合转json
    String jsonListString = objectMapper.writeValueAsString(list);
    System.out.println("list:" + jsonListString);

    // json转java集合
    List<User> list2 = objectMapper.readValue(jsonListString, new TypeReference<List<User>>() {});
    System.out.println("list2:" + list2.toString());
}
```

- 自定义JsonSerializer和JsonDeserializer来定制序列化和反序列化

```java
@Getter
@Setter
@ToString
public class User {
    // ...

    // 指定使用自定义的反序列化类
    @JsonDeserialize(using = UuidDeserializer.class)
    private String uuid;
}

public class UuidDeserializer extends JsonDeserializer<String> {
    @Override
    public String deserialize(JsonParser p, DeserializationContext ctxt) throws IOException, JacksonException {
        String property = p.getValueAsString();
        if (Objects.isNull(property)) {
            return null;
        }
        return property.replace("-", "");
    }
}
```

### Spring Boot中使用

- 只要导入了web启动器，不用导入Jackson依赖

- 配置文件

```properties
spring.jackson.time-zone=GMT+8
# 设置Date类型的日期格式
spring.jackson.date-format=yyyy-MM-dd HH:mm:ss
# 返回时间戳给前端，只对Date类型生效
spring.jackson.serialization.write-dates-as-timestamps=true
```

- 使用jackson

```java
@Autowired
private ObjectMapper objectMapper;
```

- 自定义Jackson配置类：[JacksonConfiguration](/java/JacksonConfiguration.java)

- ObjectMapper是线程安全的，因此可以定义一个Jackson工具类，避免每次注入Jackson

```java
// 方法1
// 或者使用SpringBeanHolder，它对所有工具类都适用，然后在工具类里面获取Spring Bean并调用其实例方法
@Component
public class SpringBeanHolder implements ApplicationContextAware {
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        SpringBeanHolder.applicationContext = applicationContext;
    }

    public static <T> T getBean(Class<T> clazz) {
        return applicationContext.getBean(clazz);
    }

    public static <T> T getBean(String name, Class<T> clazz) {
        return applicationContext.getBean(name, clazz);
    }
}

// 这里就不用声明为Spring Bean了
public final class JacksonUtil {
    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return SpringBeanHolder.getCacheBean(ObjectMapper.class).writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return SpringBeanHolder.getCacheBean(ObjectMapper.class).readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return SpringBeanHolder.getCacheBean(ObjectMapper.class).readValue(content, valueTypeRef);
    }
}

// 或者将SpringBeanHolder.getBean提取出来放到一个方法里
public final class JacksonUtil {
    private static volatile ObjectMapper objectMapper;

    private static ObjectMapper getObjectMapper() {
        if (Objects.isNull(objectMapper)) {
            synchronized (JacksonUtil.class) {
                if (Objects.isNull(objectMapper)) {
                    objectMapper = SpringBeanHolder.getBean(ObjectMapper.class);
                }
            }
        }
        return objectMapper;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return getObjectMapper().writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return getObjectMapper().readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return getObjectMapper().readValue(content, valueTypeRef);
    }
}

// 方法2，推荐
@Component
public final class JacksonUtil {
    private static JacksonUtil instance;

    private final ObjectMapper OBJECT_MAPPER;

    // 作为Spring管理的工具类，定义为包级构造器更合适
    JacksonUtil(ObjectMapper objectMapper) {
        this.OBJECT_MAPPER = objectMapper;
    }

    @PostConstruct
    public void init() {
        JacksonUtil.instance = this;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return instance.OBJECT_MAPPER.readValue(content, valueTypeRef);
    }
}

// 方法3，objectMapper不是final的，不推荐
@Component
public final class JacksonUtil {
    private static ObjectMapper objectMapper;

    private final ObjectMapper INJECTED_OBJECT_MAPPER;

    // 作为Spring管理的工具类，定义为包级构造器更合适
    JacksonUtil(ObjectMapper objectMapper) {
        this.INJECTED_OBJECT_MAPPER = objectMapper;
    }

    @PostConstruct
    public void init() {
        JacksonUtil.objectMapper = this.INJECTED_OBJECT_MAPPER;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return objectMapper.writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType) throws JsonProcessingException {
        return objectMapper.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef) throws JsonProcessingException {
        return objectMapper.readValue(content, valueTypeRef);
    }
}
```

### 超过16位的Long类型的字段序列化

```java
@Getter
@Setter
@ToString
public class UserVo {
    // 对于Long类型的数值超过16位时，直接序列化传给前端js将会丢失精度，需要先转为String类型来让js接收
    // 反序列化不用特殊处理，Jackson会自动将前端传来的String类型数值转换为Long类型
    @JsonSerialize(using = ToStringSerializer.class)
    private Long id;

    // 基本类型也可以，但是强烈不建议这样写的
    @JsonSerialize(using = ToStringSerializer.class)
    private long id;
}
```

## javacv

javacv是一个opencv、ffmpeg的java api

官网：<https://github.com/bytedeco/javacv>

- 依赖

```xml
<dependency>
    <groupId>org.bytedeco</groupId>
    <artifactId>javacv-platform</artifactId>
    <version>1.5.13</version>
</dependency>
```

- ffmpeg例子

```java
try (FFmpegFrameGrabber grabber = new FFmpegFrameGrabber("/path/to/input.mp4")) {
    grabber.start();

    int videoBitrate = grabber.getVideoBitrate() / 1000;
    String videoFrameRate = BigDecimal.valueOf(grabber.getVideoFrameRate()).setScale(2, RoundingMode.HALF_UP).toString();
    String videoInformation = String.format("视频编解码器：%s，比特率：%d kb/s，分辨率：%dx%d，帧数：%s fps",
                                            grabber.getVideoCodecName(), videoBitrate, grabber.getImageWidth(), grabber.getImageHeight(), videoFrameRate);
    System.out.println(videoInformation);

    int audioBitrate = grabber.getAudioBitrate() / 1000;
    String audioFrameRate = BigDecimal.valueOf(grabber.getAudioFrameRate() * 1000).setScale(0, RoundingMode.HALF_UP).toString();
    String audioInformation = String.format("音频编解码器：%s，比特率：%d kb/s，采样率：%s Hz，声道数：%d",
                                            grabber.getAudioCodecName(), audioBitrate, audioFrameRate, grabber.getAudioChannels());
    System.out.println(audioInformation);

    grabber.stop();
} catch (FrameGrabber.Exception e) {
    throw new RuntimeException(e);
}
```

## swagger

- 依赖

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.6.0</version>
</dependency>
```

- 配置文件

```java
@Configuration
public class SwaggerConfiguration {
    // 分组配置，请求路径是/pay/**都归为支付模块
    @Bean
    public GroupedOpenApi payApi() {
        return GroupedOpenApi.builder().group("支付模块").pathsToMatch("/pay/**").build();
    }

    @Bean
    public GroupedOpenApi otherApi() {
        return GroupedOpenApi.builder().group("其它模块").pathsToMatch("/other/**").build();
    }

    // 其它描述信息
    @Bean
    public OpenAPI openApi() {
        return new OpenAPI()
        .info(new Info().title("projectName")
            .description("projectName api")
            .version("1.0.0"))
        .externalDocs(new ExternalDocumentation()
            .description("handle studio")
            .url("https://www.handle.com"));
    }
}
```

- 实体类

```java
@Schema(title = "支付模块入参")
public class PayInputVo {
    @Schema(title = "支付流水号")
    private String payNumber;
}
```

- 控制器

```java
@Tag(name = "支付模块", description = "支付增删改查")
public class PayController {
    @Operation(summary = "新增", description = "新增支付记录") 
    @GetMapping("/add")
    public String add(@RequestBody PayInputVo payInputVo) {
    return "add success";
    }
}
```

- 访问页面：<http://ip:port/swagger-ui/index.html>

## Knife4j

集成了swagger功能，更加强大

- 依赖

```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-openapi3-jakarta-spring-boot-starter</artifactId>
    <version>4.5.0</version>
</dependency>
```

- 文档地址：`http://ip:port/doc.html`

## Jmeter

### 线程组

- 10个线程，执行5秒，每次执行2个请求

![jmeter-threadGroup](/images/jmeter-threadGroup.png)

### get请求

- 注意点：如果直接在Path输入框中输入：<http://localhost:8080/jackson/get2>，然后填写请求参数，那么请求参数是不会拼接到请求路径后面的

![jmeter-get](/images/jmeter-get.png)

## 分布式id

### tinyid

- 官网：<https://github.com/didi/tinyid>

- 下载源码后用IDE打开

- 将tinyid/tinyid-server/db.sql改成postgresql数据库支持的sql，然后创建表

```sql
create table tiny_id_info (
    id bigserial not null,
    biz_type varchar(63) not null default '',
    begin_id bigint not null default '0',
    max_id bigint not null default '0',
    step integer default '0',
    delta integer not null default '1',
    remainder integer not null default '0',
    create_time timestamptz not null default '2024-01-01 00:00:00',
    update_time timestamptz not null default '2024-01-01 00:00:00',
    version bigint not null default '0',
    primary key (id),
    constraint uniq_biz_type unique (biz_type)
);

comment on table  tiny_id_info is '账号表'; 
comment on column tiny_id_info.id is '自增主键';
comment on column tiny_id_info.biz_type is '业务类型，唯一';
comment on column tiny_id_info.begin_id is '开始id，仅记录初始值，无其他含义。初始化时begin_id和max_id应相同';
comment on column tiny_id_info.max_id is '当前最大id';
comment on column tiny_id_info.step is '步长';
comment on column tiny_id_info.delta is '每次id增量';
comment on column tiny_id_info.remainder is '余数';
comment on column tiny_id_info.create_time is '创建时间';
comment on column tiny_id_info.update_time is '更新时间';
comment on column tiny_id_info.version is '版本号';

create table tiny_id_token (
    id bigserial not null,
    token varchar(255) not null default '',
    biz_type varchar(63) not null default '',
    remark varchar(255) not null default '',
    create_time timestamptz not null default '2024-01-01 00:00:00',
    update_time timestamptz not null default '2024-01-01 00:00:00',
    primary key (id)
);

comment on table  tiny_id_token is 'token信息表'; 
comment on column tiny_id_token.id is '自增主键';
comment on column tiny_id_token.token is 'token';
comment on column tiny_id_token.biz_type is '此token可访问的业务类型标识';
comment on column tiny_id_token.remark is '备注';
comment on column tiny_id_token.create_time is '创建时间';
comment on column tiny_id_token.update_time is '更新时间';

insert into tiny_id_info (id, biz_type, begin_id, max_id, step, delta, remainder, create_time, update_time, version)
values
 (1, 'test', 1, 1, 100000, 1, 0, '2024-09-30 14:30:06', '2024-09-30 14:30:06', 1);

insert into tiny_id_info (id, biz_type, begin_id, max_id, step, delta, remainder, create_time, update_time, version)
values
 (2, 'test_odd', 1, 1, 100000, 2, 1, '2024-09-30 14:30:06', '2024-09-30 14:30:06', 3);

insert into tiny_id_token (id, token, biz_type, remark, create_time, update_time)
values
 (1, '0f673adf80504e2eaa552f5d791b644c', 'test', '1', '2024-09-30 16:36:46', '2024-09-30 16:36:48');

insert into tiny_id_token (id, token, biz_type, remark, create_time, update_time)
values
 (2, '0f673adf80504e2eaa552f5d791b644c', 'test_odd', '1', '2024-09-30 16:36:46', '2024-09-30 16:36:48');
```

- 修改pom，添加postgresql驱动依赖（mysql的可以删了）

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.6.0</version>
</dependency>
```

- 修改配置文件，tinyid-server/src/main/resources/offline下的application.properties

```properties
datasource.tinyid.primary.driver-class-name=org.postgresql.Driver
datasource.tinyid.primary.url=jdbc:postgresql://localhost:5432/tinyid
datasource.tinyid.primary.username=postgres
datasource.tinyid.primary.password=postgres123
```

- 其它地方根据运行的Java环境和依赖版本做调整

### idgenerator

- 官网：<https://github.com/yitter/IdGenerator>

- 依赖

```xml
<dependency>
    <groupId>com.github.yitter</groupId>
    <artifactId>yitter-idgenerator</artifactId>
    <version>1.0.6</version>
</dependency>
```

- 使用

```java
public class ApplicationTest {
    @BeforeEach
    public void init() {
        // 创建 IdGeneratorOptions 对象，可在构造函数中输入 WorkerId：
        IdGeneratorOptions options = new IdGeneratorOptions((short)1);
        // options.WorkerIdBitLength = 10; // 默认值6，限定 WorkerId 最大值为2^6-1，即默认最多支持64个节点。
        // options.SeqBitLength = 6; // 默认值6，限制每毫秒生成的ID个数。若生成速度超过5万个/秒，建议加大 SeqBitLength 到 10。
        // options.BaseTime = Your_Base_Time; // 如果要兼容老系统的雪花算法，此处应设置为老系统的BaseTime。
        // ...... 其它参数参考 IdGeneratorOptions 定义。

        // 保存参数（务必调用，否则参数设置不生效）：
        YitIdHelper.setIdGenerator(options);

        // 以上过程只需全局一次，且应在生成ID之前完成。
    }
    @Test
    public void test() {
        // 初始化后，在任何需要生成ID的地方，调用以下方法：
        long newId = YitIdHelper.nextId();
        System.out.println(newId);
    }
}
```

## Zookeeper

### 安装Zookeeper单机版

- 修改zoo_sample.cfg为zoo.cfg

```sh
mv zoo_sample.cfg zoo.cfg
```

- 修改zoo.cfg配置

```conf
dataDir=/handle/data/zookeeper/data
```

- 启动zookeeper

```sh
# 启动zookeeper服务器
bin/zkServer.sh start

# 查看zookeeper状态
bin/zkServer.sh status

# 停止zookeeper
bin/zkServer.sh stop

# 启动zookeeper客户端
bin/zkCli.sh

# 退出客户端
quit
```

### 安装Zookeeper集群

- 在dataDir定义的目录下创建一个myid文件
    - 在文件中填写server编号（上下不要有空行，左右不要有空格）
- 修改配置

```conf
# server.id=serverip:port1:port2
# id是myid文件里面对应的编号
# serverip是服务器地址
# port1是fllower和leader交换信息的端口，如2888
# port2是选举端口，如3888
server.1=serverip1:port1:port2
server.2=serverip2:port1:port2
server.3=serverip3:port1:port2
```

## Docker

docker的基本组成：镜像、容器、仓库

### 安装docker

#### Red Hat系列Linux安装Docker

```sh
# 如果安装过docker，先卸载旧版本的docker
sudo yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine


# 安装yum-utils，其提供了yum-config-manager
sudo yum install -y yum-utils

# 配置yum-config-manager，添加仓库地址，这里需要配置成国内仓库，比如阿里云，不要用官网默认的
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum软件包索引（可选操作）
sudo yum makecache fast 

# 安装docker引擎，如果提示接受GPG密钥，请验证指纹是否匹配060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35，如果是，则接受它
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 卸载docker引擎，但不会删除docker镜像、容器和配置文件，docker镜像、容器和配置文件需要手动删除
sudo yum remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras

# 删除docker镜像
sudo rm -rf /var/lib/docker

# 删除docker容器
sudo rm -rf /var/lib/containerd
```

#### Ubuntu安装Docker

教程：<https://docs.docker.com/engine/install/ubuntu/>

- 卸载旧版本

```sh
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)
```

- 配置docker的apt仓库

```sh
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

- 安装

```sh
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 安装后一般docker会自己启动，查看命令
sudo systemctl status docker
```

#### 将当前用户加入docker用户组

```sh
# 将当前用户加入docker用户组并立即生效
sudo usermod -aG docker $USER && newgrp docker
```

#### 配置国内镜像源

- 1.配置文件`/etc/docker/daemon.json`中加入

```json
{
    "registry-mirrors": [
        "https://hub.docker.com"
    ]
}
```

- 2.重新启动docker

```sh
sudo systemctl daemon-reload

sudo systemctl restart docker
```

- 3.在命令行执行`docker info` ，如果从结果中看到了如下内容，说明配置成功:

```sh
Registry Mirrors:
    <https://docker.mirrors.ustc.edu.cn/>
```

#### 启动docker

```sh
# 启动docker
sudo systemctl start docker

# 重启docker
sudo systemctl restart docker

# 关闭docker
sudo systemctl stop docker

# 查看docker状态信息
sudo systemctl status docker

# 将docker服务设置为开机启动
sudo systemctl enable docker
```

### docker常用命令

#### 帮助命令

```sh
# docker版本
docker version

# docker概要信息
docker info

# docker总体帮助文档
docker help

# docker命令帮助文档
docker 具体命令 --help
```

#### 镜像命令

- 列出本地主机所有镜像

```sh
# -a 列出本地所有镜像（含历史映像层），-q 只显示镜像id
docker images [-aq]
```

- 查询镜像

```sh
# 最好是去docker hub查询指定版本的详细信息，如镜像标签，只列出25个镜像（默认）
docker search [--limit 25] 镜像关键字 

docker search --limit 25 tomcat
```

- 拉取镜像

```sh
# 不指定标签会拉取最新镜像，相当于docker pull 镜像名:latest
docker pull 镜像名[:标签]

docker pull mysql:8.0.29
```

- 查看镜像/容器/数据卷所占的空间

```sh
docker system df
```

- 删除镜像

```sh
# -f 强行删除
docker rmi [-f] 镜像id或镜像名:标签

# 删除单个镜像
docker rmi -f mysql:8.0.29

# 删除多个镜像
docker rmi -f 镜像名1:标签 镜像名2:标签

# 删除全部镜像
docker rmi -f $(docker images -aq)
```

#### 容器命令

- 新建容器并运行

```sh
# 需要考虑端口映射、配置映射、数据映射、环境变量、网络名等
# --name 自定义容器名
# -d 后台运行容器并返回容器id，也即启动守护式容器
# -i 以交互模式运行容器，通常与-t同时使用
# -t 为容器重新分配一个伪输入终端，通常与-i同时使用，-it即启动交互式容器（前台有伪终端，等待交互）
# -p 主机端口:容器端口
# -P 随机分配主机端口，很少用
docker run [选项] 镜像名:标签 [命令] [ARG...]

# 使用镜像centos:latest以交互模式新建一个容器并运行，在容器内执行/bin/bash命令
# 放在镜像名后的是命令，这里我们希望有个交互式shell，这里用的是/bin/bash
# 要退出终端，输入exit
docker run -it centos /bin/bash
```

- 设置容器开机/关闭自启动

```sh
docker update 容器id --restart always

docker update 容器id --restart no
```

- 启动容器(已停止运行的容器)

```sh
docker start 容器id/容器名
```

- 重启容器

```sh
docker restart 容器id/容器名
```

- 停止容器(正在运行的容器)

```sh
docker stop 容器id或容器名
```

- 强制停止容器

```sh
docker kill 容器id或容器名
```

- 展示容器列表（默认正在运行的容器）

```sh
# -a 列出所有的容器
# -l 显示最近创建的容器
# -n 任意正整数 显示任意个最近创建的容器
# -q 静默模式，只显示容器编号
docker ps [选项]
```

- 退出容器

```sh
# 在交互式伪终端，用exit退出，容器停止
exit

# 在交互式伪终端，按ctrl + p + q，容器不停止
ctrl + p + q
```

- 重新进入容器

```sh
# 推荐，在容器中打开新的命令终端，并且可以启动新的进程，用exit退出，不会导致容器停止，
docker exec -it 容器id /bin/bash

# 直接进入容器并启动命令终端，不会启动新的进程，用exit退出，会导致容器停止
docker attach 容器id
```

- 删除容器

```sh
# 单个删除已停止的容器，如果容器正在运行要加-f强制删除
docker rm [-f] 容器id

# 删除全部容器
docker rm -f $(docker ps -a -q)

# 删除全部容器
docker ps -a -q | xargs docker rm
```

- 查看容器日志

```sh
# -t显示时间
# -f 跟随最新的日志显示
# --tail 限制显示的日志行数
docker logs -t -f --tail 5 容器id
```

- 查看容器内运行的进程

```sh
docker top 容器id
```

- 查看容器内部细节

```sh
docker inspect 容器id
```

- 在容器内执行命令

```sh
# 在容器内执行命令 ls -l
docker exec -t 容器id [ls -l]
```

### docker的复制/加载

```sh
# 复制容器内的文件到linux主机
docker cp 容器id:容器内路径 主机路径

# 复制linux主机的文件到容器内
docker cp 主机路径 容器id:容器内路径

# 提交容器副本使之成为一个新的镜像
docker commit -m="描述信息" -a="作者" 容器id 要创建的目标镜像名:标签

# 容器备份为一个tar归档文件
docker export 容器id > 文件名.tar

# 将镜像备份出来
docker save my_tomcat:1.0 -o my-tomcat-1.0.tar

# 从容器备份文件创建一个新的文件系统再导入为镜像
cat 文件名.tar | docker import - 镜像用户/镜像名:标签

# 将.tar格式的镜像加载到Docker中
docker load -i my-tomcat-1.0.tar
```

### 搭建私服

- 拉取仓库镜像

```sh
docker pull registry:标签
```

- 运行仓库

```sh
# -v 主机目录:容器目录，默认仓库在容器/var/lib/registry目录下
docker run -d --name registry01 -p 5000:5000 --privileged=true -v /data/docker/registry:/tmp/registry  registry:标签
```

- 查看私服上的镜像

```sh
curl -XGET http://私服地址:端口/v2/_catalog
```

- 将镜像修改为符合私服规范的名称

```sh
docker tag 本地要推送的镜像名:标签 私服地址:端口/推送到私服后的镜像名:标签
```

- docker默认不允许通过http方式推送镜像，修改docker配置文件使之支持http，在文件原来的内容后面添加`,"insecure-registries": ["私服地址:端口"]`

```sh
vi /etc/docker/daemon.json
```

```json
{
    // daemon.json原来的内容后面加上这里表示为...，insecure-registries里面配置非https的hub的地址
    ...,
    "insecure-registries": ["私服地址:端口"]
}
```

- 然后重新启动docker

```sh
sudo systemctl daemon-reload

sudo systemctl restart docker
```

- 将镜像推送到私服

```sh
docker push 私服规范镜像名（私服地址:端口/镜像名:标签）
```

- 再次查看私服上的镜像

```sh
curl -XGET http://私服地址:端口/v2/_catalog
```

- 从私服上拉取镜像到本地

```sh
docker pull 私服规范镜像名（私服地址:端口/镜像名:标签）
```

### 容器数据卷

卷就是目录或文件，其设计目的就是数据的持久化，它独立于容器的生存周期，不会在容器删除时删除其挂载的数据卷。

docker挂载主机目录后加 `--privileged=true`，可以解决挂载目录没有权限的问题。该参数使得容器内的root拥有真正的root权限，否则，容器内的root只有普通用户权限

在用 docker run 命令的时候，使用 --mount 标记来将一个或多个数据卷挂载到容器里

还可以通过 --mount 标记将宿主机上的文件或目录挂载到容器中，这使得容器可以直接访问宿主机的文件系统

Docker 挂载主机目录的默认权限是读写，用户也可以通过增加 readonly 指定为只读

```sh
# 手动创建
docker volume create 卷名

# 自动创建，放在容器启动命令中
-v ngconf:/etc/nginx

# 删除卷
docker volume rm 卷名1 卷名2

# 查看所有的数据卷
docker volume ls

# 查看卷信息
docker volume inspect 卷名
```

- 对于容器卷，docker统一放在主机的`/var/lib/docker/volumes/卷名`目录下，需要有root权限才可以操作该目录

- 使用经验：对于容器数据用命名卷，对于经常改动的比如配置文件，用目录映射更好

```sh
# 目录映射（绑定挂载），可以自定义位置，但是必须先在主机上创建目录/文件
# 举个例子：映射的主机上目录是空的，容器里的目录非空，这种情况下，在主机上的该目录是看不到容器里面的内容的（被隐藏了）
# 因此，对于配置文件要提前复制一份到主机目录
# 一般情况下，比如说容器目录有config，里面有默认的配置文件了，如果想自定义配置，那么应该映射到容器的一个空目录中，比如config.d，d表示Dynamic
# 这样就不会顶掉容器中原有目录下的文件
-v /data/docker/registry:/tmp/registry

# 卷映射（命名卷），卷名前面不带/，卷映射不需要先在主机上创建目录/文件，比如对于配置文件不用提前复制一份到主机目录，但是配置文件一般不用卷映射
-v ngconf:/etc/nginx
```

- 运行带有容器卷存储功能的容器实例

```sh
# ro read only，默认容器权限为读写，只限制容器的权限，宿主机不限制
docker run -it --privileged=true -v 主机目录:容器内目录[:rw/ro] --name 自定义容器名 镜像名:标签
```

- 查看数据卷是否挂载成功，查看返回的Mounts信息可以知道是否挂载成功

```sh
docker inspect 容器id
```

```json
{
    "Mounts": [
        {
            // ...
            "Source": "/data/docker/registry",
            "Destination": "/tmp/registry",
            // ...
        }
    ],
}
```

- 继承其它容器的卷规则

```sh
docker run -it --privileged=true --volumes-from 要继承的容器的id --name 自定义容器名 镜像名:标签
```

### docker网络

- 网络模式

|网络模式|简介|命令|
|:-|:-|:-|
|bridge|虚拟网桥，默认模式，为每一个容器分配、设置ip等，并将容器连接到一个docker0|`--network bridge`，默认使用docker0|
|host|容器将不会虚拟出自己的网卡，配置自己的ip等，而是使用宿主机的ip和端口|`-–network host`|
|none|容器有独立的network namespace，但并没有对其进行任何网络设置，如分配veth pair和网桥连接，ip等|`–-network none`|
|container|新创建的容器不会创建自己的网卡和配置自己的ip，而是和一个指定的容器共享ip、端口范围等|`-–network container:name或容器id`|

- docker0网络，docker为每个容器分配唯一ip，容器之间可以使用`容器ip+容器端口`相互访问，但是这个ip在容器删了重新启动或者迁移的时候会改变，一般都会通过域名进行访问而不用ip

- docker0默认不支持主机域名，需要创建自定义网络，容器名就是主机域名

```sh
# 创建自定义网络
docker network create mynet

# 指定网络名
docker -run -d -p 5050:80 --name app1 --network mynet dpage/pgadmin4

# 进入容器内
docker exec -it app1 bash

# 容器内访问app2，直接用主机域名
curl http://app2:80
```

- 常用命令

```sh
# docker网络命令帮助
docker network --help

# 查看网络
docker network ls

# 查看网络源数据
docker network inspect 网络名

# 添加网络
docker network create 网络名

# 删除网络
docker network rm 网络名

# 查看docker容器内网络信息
docker inspect 容器id/容器名称 | tail -n 20
```

### docker权限

- 在 Docker 中，权限控制通常有三个层级
    - 默认模式：权限受限，最安全，但无法满足像 Vault 这类特殊应用的需求
    - cap_add：缺啥补啥，只给容器最必要的特权，是生产环境推荐的最佳实践
        - 比如设置为IPC_LOCK（全称是“进程间通信锁定”），它的核心作用是允许进程将内存锁定在物理内存（RAM）中，防止这部分内存被操作系统交换（Swap）到硬盘上
    - privileged: 如果设置为true，赋予容器几乎等同于宿主机 root 的全部权限。虽然能解决问题，但安全风险极高，通常不建议使用。

### Docker Compose

#### 编写compose.yaml

```yaml
# 项目名
name: myproject
# services：定义一组要运行的容器服务
services: 
    # 服务名，名称自行定义
    mysql:
        # 容器名，不指定则为：项目名-服务名-数字-（1-n)
        container_name: mysql
        # 镜像名称
        image: mysql:8.0
        # 端口 
        ports:
            - "3306:3306"
        # 环境变量
        environment:
            # 必须
            MYSQL_ROOT_PASSWORD: mysql123
            # 下面的数据库名称、用户名和密码是可选的
            # 启动后，MySQL会自动创建dbname数据库和user_dbname用户并指定密码user_password，并授予该用户对dbname的全部权限
            MYSQL_DATABASE: dbname
            MYSQL_USER: user_dbname
            MYSQL_PASSWORD: user_password
        volumes:
            # 命名卷，不可以自定义位置，卷名要在顶级元素volumes里面声明
            - mysql-data:/var/lib/mysql
            # 绑定挂载，可以自定义位置，但是必须先在主机上创建目录 
            - /app/myconf:/etc/mysql/conf.d
        # 无论容器因为什么原因（容器内部程序崩溃、手动stop、Docker守护进程重启、系统重启）退出，都要自动重启
        restart: always
        # 网络，要在顶级元素networks里面声明，一个应用可以加入多个网络
        networks: 
            # 使用compose网络标识符
            - compose-net-name
        # 依赖：比如要先启动redis才能启动mysql
        depends_on:
            - myredis
    myredis:
# networks表示自定义网络
# 除非指定了external: true，否则无需先手动创建网络，compose能自动创建
networks: 
    # docker-compose文件内部使用的网络标识符，不是docker实际创建的网络名
    compose-net-name: 
        # docker实际创建的网络名称，不指定则为：项目名-compose网络标识符
        name: mynetname
        # 默认
        external: false
# volumes自定义卷
volumes: 
    # compose内部使用的卷名，不是docker实际创建的卷名
    mysql-data: 
        # docker实际创建的卷名，不指定则为：项目名-volume名
        name: myvolumename
configs: 
# 密钥
secrets: 
```

#### 使用compose.yaml启动/下线

```sh
# 自动在当前目录查找compose.yaml、docker-compose.yaml
docker compose up -d [container1 container2 container3]

# 指定compose文件名批量新建容器并以后台方式启动
docker compose -f mycompose.yaml up -d

# 批量移除容器并删除相关资源（如果不在参数中指定不会删除自定义挂载的目录/卷）
docker compose -f mycompose.yaml down

# 批量启动
docker compose start container1 container2 container3

# 批量停止
docker compose stop container1 container2 container3

# container1启动三个实例
docker compose scale container1=3
```

### Dockerfile

Dockerfile时用来构建Docker镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

#### Dockerfile执行流程

- docker从基础镜像运行一个容器

- 执行一条指令并对容器作出修改

- 执行类似`docker commit`的操作提交一个新的镜像层

- docker再基于刚提交的镜像运行一个新容器

- 执行Dockerfile中的下一条指令直到所有指令都执行完成

#### Dockerfile基础

- `#`表示注释

- 每条保留字指令都必须为大写字母且后面要跟随至少一个参数

- 指令按从上到下的顺序执行

- 每条指令都会创建一个新的镜像层饼对镜像进行提交

#### 保留字指令

- FROM

```Dockerfile
# 当前镜像是基于哪个镜像的，指定一个一级存在的镜像作为模板，第一条必须是FROM
FROM 基础镜像（镜像名:标签）
```

- LABEL

LABEL key=value key2=value2 ...

- RUN

容器构建(docker build)时需要运行的命令

```Dockerfile
# shell格式，<命令行命令>等同于在终端操作的shell命令
RUN 命令行命令

# exec格式
RUN ["可执行文件", "参数1", "参数2"]

# 等价于 RUN ./test.php dev offline
RUN ["./test.php", "dev", "offline"]
```

- EXPOSE

当前容器对外暴露出的端口

```Dockerfile
EXPOSE 端口号
```

- WORKDIR

指定在创建容器后，终端默认登录进来的工作目录

```Dockerfile
WORKDIR /
```

- USER

指定该镜像以什么样的用户执行，如果不指定，默认是root

- ENV

用来在构建镜像过程中设置环境变量，这个环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样；也可以在其它指令中直接使用这些环境变量

```Dockerfile
# 定义环境变量
ENV APP_HOME /usr/local/bin

# 使用环境变量
WORKDIR $APP_HOME
```

- VOLUME

容器数据卷，用于数据保存和持久化工作

- ADD

将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包，功能上相当于`COPY+解压`

- COPY

拷贝文件/目录到镜像中，将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置

```Dockerfile
# src为源文件或目录，dest为容器内的指定路径，该路径不用事先建好，不存在会自动创建
COPY src dest
COPY ["src", "dest"]
```

- CMD

指定容器启动（docker run）后要干的事情，Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换

```Dockerfile
# shell格式
CMD <命令>

# exec格式
CMD ["可执行文件", "参数1", "参数2"...]

# 参数列表格式，在指定了ENTRYPOINT指令后，用CMD指定具体的参数
CMD ["参数1", "参数2"...]
```

- ENTRYPOINT

也是用来指定容器启动（docker run）时要运行的命令，类似于CMD，但是不会被docker run后面的命令覆盖，而且这些命令行参数会被当做参数送给ENTRYPOINT指令指定的程序

```Dockerfile
ENTRYPOINT ["命令", "参数1", "参数2"...]

# 容器启动时执行.sh文件，并且不退出容器的写法
ENTRYPOINT ["bash", "-c", "/../*.sh && tail -f /dev/null"]
```

ENTRYPOINT可以和CMD一起使用，一般时变参才会使用CMD，这里的CMD等于是在给ENTRYPOINT传参。当指定了ENTRYPOINT后，CMD的含义旧发生了变化，不再是直接运行其命令而是将CMD的内容作为参数传递给ENTRYPOING指令，二者组合变成`<ENTRYPOINT> "<CMD>"`

假设Dockerfile：

```Dockerfile
ENTRYPOINT ["nginx", "-c"]
CMD ["/etc/nginx/nginx.conf"]
```

则docker命令和Dockerfile结合的实际命令如下表：

|docker命令|实际命令|
|:-|:-|
|docker run nginx:latest|nginx -c /etc/nginx/nginx.conf|
|docker run nginx:latest -c /etc/nginx/new.conf|nginx -c /etc/nginx/new.conf（docker run 后面带了参数变不会再根据Dockerfile改变）|

#### 构建镜像

```sh
# 标签后有个空格，有个点
docker build -t 新镜像名:标签 .
```

#### 构建带Java环境的Ubuntu镜像

```Dockerfile
FROM ubuntu:24.04
LABEL author=handle
# 设置环境变量，指定系统编码
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
# 登进容器后进入/usr/local
WORKDIR /usr/local
RUN mkdir /usr/local/jdk
# 把jdk添加到容器中，并且指定解压到容器中的具体位置
ADD bellsoft-jre21.0.4+9-linux-amd64.tar.gz /usr/local/jdk/
# 配置JAVA环境变量
ENV JAVA_HOME /usr/local/jdk/jre-21.0.4
ENV PATH $JAVA_HOME/bin:$PATH
# 容器启动后打开bash
CMD /bin/bash
```

#### 构建微服务镜像

Dockerfile和jar包要在同一目录下

- 创建Dockerfile

```Dockerfile
FROM bellsoft/liberica-jre-ubuntu:21.0.4-9
# 作者信息
LABEL author=handle
# 在容器中创建目录
RUN mkdir -p /handle/book
# 把jar包添加到容器中的根目录下
COPY file-server-1.0.0-SNAPSHOT.jar /file-server.jar
# 容器启动时运行jar包
ENTRYPOINT ["java", "-jar", "-Dbook.absolutePath=/handle/book", "/file-server.jar"]
# 暴露端口
EXPOSE 2121
```

- 构建镜像

```sh
# 标签后有个空格，有个点（./），表示构建的整个上下文目录就是当前目录
docker build -f Dockerfile -t application:1.0 .
```

- 运行容器

```sh
# 后台运行
docker run -d -p 8080:8080 application:1.0
```

### 虚悬镜像

仓库名、标签都是`<none>`的镜像，虚悬镜像一级失去存在价值，可以删除

```sh
# 查看所有的虚悬镜像
docker image ls -f dangling=true

# 删除虚悬镜像
docker image prune
```

## Kubernetes

Kubernetes是谷歌开源的容器编排引擎，用来管理容器化的应用程序和服务，如部署、扩展、管理等

Kubernetes提供了容器编排的功能，可以通过配置文件来定义应用程序的部署方式

让容器的创建、维护和管理变得更加简单和高效

Kubernetes也提供了很多高可用的特性，比如自动重启、自动重建、自我修复等

还有可扩展性，让系统可以根据负载的变化来动态地扩展或缩减系统的资源，从而提高系统的性能和资源的利用率

还有其它如灾难恢复、弹性伸缩等，这些特性都可以帮助提高应用程序的性能、可用性和稳定性

### Kubernetes组件

#### node（节点）

一个节点就是一个物理机或虚拟机，在一个节点上，可以运行一个或多个pod

#### pod

pod是Kubernetes的最小调度单元

一个pod就是一个或多个应用容器的组合

pod创建了一个容器的运行环境，在这个环境中，容器间可以共享一些资源

比如网络、存储以及一些运行时配置等

一般情况下，一个pod中只运行一个容器

#### srvice（服务）

将一组pod封装成一个服务，这个服务可以通过一个统一的入口来访问

- service的类型有：
    - ClusterIP：默认类型，集群内部的服务
    - NodePort：节点端口类型，将服务公开到集群节点上，然后就可以通过节点的ip地址和端口访问服务
    - LoadBalancer：负载均衡类型，将服务公开到外部负载均衡器上
    - ExternalName: 外部名称类型，通过返回一个CNAME记录，将服务映射到一个外部域名上
    - Headless：无头类型，可以创建一个没有ClusterIP的服务，主要用于DNS解析和服务发现

#### ingress

用来管理从集群外部访问集群内部服务的入口和方式

可以通过ingress配置不同的转发规则

从而根据不同的规则，来访问集群内部不同的service以及service所对应的后端pod

还可以通过ingress来配置域名，这样就可以将原本使用ip和端口的方式转换成使用域名的方式来访问service了

另外ingress还可以配置其它的功能，如负载均衡、SSL证书等

#### ConfigMap

将一些配置信息封装起来，然后就可以在应用程序中读取和使用了

有了ConfigMap，就可以将配置信息和应用程序的镜像内容分离开来

这样就可以保持容器化应用程序的可移植性

#### secret

ConfigMap的配置信息都是明文的，如果配置信息包含一些敏感信息如账号密码，就不建议将其存储在ConfigMap中，secret就是为了解决这个问题的

secret可以将一些敏感信息封装起来，然后就可以在应用程序中读取和使用了

但是secret也只是做了一层Base64的编码而已，还需要配合Kubernetes的其它手段来提高安全性

如user、c.role、sa

#### volume

将持久化存储的资源挂载到集群中的本地磁盘上或者集群外部的远程存储上

#### Deplyment

定义和管理应用程序的副本数量，以及应用程序的更新策略

可以简化应用程序的部署和更新操作

#### StatefulSet

和Deplyment类似，也提供了定义和管理应用程序副本数量、动态扩容缩容等功能

还保证了每个副本都有自己稳定的网络标识符和持久化存储

因此，像数据库、缓存、消息队列等这些有状态的应用，以及一些保留了会话状态的应用程序，一般都使用StatefulSet

对于数据库，更好的方案是从Kubernetes分离出来，单独部署

### Kubernetes架构

Kubernetes是典型的master-worker架构

master节点负责管理整个集群，worker节点负责运行应用程序和服务

worker节点为了能够对外提供服务，每个node都会包含3个组件：kubelet、kube-proxy和container-runtime（容器运行时环境）

- kubelet
    - 负责管理和维护每个节点上的pod，并确保它们按照预期运行
    - 也会定期从api-server组件接收新的或者修改后的pod规范，
    - 同时它也会监控工作节点的运行情况，然后将这些信息汇报给api-server

- kube-proxy
    - 负责为pod对象提供网络代理和负载均衡服务
    - 它会在每个node上启动一个网络代理，使发往service的流量以一种高效的方式路由到正确的pod中

- container-runtime可以是Docker-Engine、Containerd、CRI-O、Mirantis Container Runtime

master节点包含4个组件：kube-apiserver、etcd、ControllerManager和scheduler

- kube-apiserver
    - 负责提供kubernetes集群的API接口服务，所有的组件都会通过这个接口来进行通信
    - 还负责对所有资源对象的增删改查等操作进行认证、授权和访问控制
- scheduler
    - 负责监控集群中所有节点的资源使用情况，然后根据调度策略，将pod调度到合适的节点上运行
- ControllerManager
    - 负责管理集群中各种资源对象的状态，如pod、node、service等，根据它们的状态做出相应的响应，确保集群中的各种资源对象都处于预期的状态
- etcd
    - 一个高可用的键-值存储系统，存储集群中所有资源对象的状态信息

### Helm

kubernetes的包管理工具，帮助我们更方便地管理kubernetes的应用，有点类似于yum

使用它可以方便地安装、升级和管理应用

### 下载kubernetes

minikube是一个轻量级的kubernetes发行版，可在本地计算机上创建虚拟机，并部署仅包含一个节点的简单集群

此外k3s、k3d、kind也可以用来搭建本地的轻量级kubernetes环境

交互工具：kubectl是一个命令行工具，Dashboard是一个webui界面，此外还有API接口，它们都可以用来跟创建的kubernetes集群进行交互，管理集群中的各种资源

#### 使用minikube

下载minikube：<https://github.com/kubernetes/minikube>

```sh
# 解压得到可执行文件，将其改名为minikube
tar -zxvf minikube-linux-amd64.tar.gz 

# 将当前用户加入docker用户组并立即生效
sudo usermod -aG docker $USER && newgrp docker

# 查看minikube版本
./minikube version

# 进入目录下启动minikube
# --driver=docker：指定驱动为docker
# --image-mirror-country=cn：指定使用国内镜像
./minikube start --driver=docker --image-mirror-country=cn

# 查看minikube状态
./minikube status

# 停止minikube
./minikube stop

# 因为没有独立安装kubectl，可以使用minikube内置的，但是命令比较长
# 查看集群中的节点
./minikube kubectl get nodes
```

#### 使用k3s

k3s官网：<https://k3s.io/>

k3s是一个CNCF认证的轻量级kubernetes发行版，更加轻量快速，而且集成了kubernetes的大部分架构和功能，相比较minikube这样的单节点kubernetes集群，k3s可以方便地搭建一个多节点集群

下载稳定版：<https://update.k3s.io/v1-release/channels/stable>

选择文件名是k3s的下载，它是一个可执行文件，别的带image的是容器镜像文件，不要下载它们

```sh
# 下载完后赋予可执行权限
sudo chmod +x k3s

# 启动，会占用当前终端
sudo ./k3s server

# 查看节点
sudo ./k3s kubectl get nodes

# 查看pod
sudo ./k3s kubectl get pods -A
```

#### 配置k3s镜像源

```sh
# 新建文件并添加如下内容，然后重新启动k3s
sudo vim /etc/rancher/k3s/registries.yaml

mirrors:
    docker.io:
        endpoint:
            - "https://45hrqeao.mirror.aliyuncs.com"
            - "https://hub-mirror.c.163.com"
```

##### 创建和配置worker节点

```sh
# 从master节点获取一个token，作为创建worker节点的一个认证凭证
sudo cat /var/lib/rancher/k3s/server/node-token

# 在worker节点上用master的地址和token启动来加入集群
sudo ./k3s agent --server https://master_ip:6443 --token master_token

# 然后在master上可以看到worker节点了
sudo ./k3s kubectl get nodes
```

### kubectl

需要在master节点上执行

```sh
# 查看资源对象状态
# all：列出集群中所有的资源对象
kubectl get nodes/pod/svc/其它资源对象/all

# 查看pod所在节点和pod的ip
kubectl get pod -o wide

# 访问pod所提供的服务
# 一般最好不要直接使用pod来提供服务
# 首先，pod使用的是一个集群内部的ip，这个ip是不能直接从集群外部访问到的
# 其次，pod并不是一个非常稳定的实体，经常会被创建或销毁，这时候它的ip也可能发生变化
# 作为解决方案，可以使用service来对外提供服务
curl pod的ip

# 创建pod，会下载nginx镜像，就算用的是docker驱动，docker已经有nginx镜像了还是会下载，它会用自己的镜像
kubectl run nginx --image=nginx

# 可以看到创建的nginx的创建状态
kubectl get pod

# 查看create命令帮助
kubectl create -h

# 创建deployment，会根据配置创建pod
# 在deployment和pod之间还有一个中间层replicaSet，用来管理pod副本数量，也被创建了
# deployment管理replicaSet，replicaSet管理pod
# 我们只管创建deployment，让k8s自己管理replicaSet和pod
kubectl create deployment nginx-deployment --image=nginx

# 可以看到创建的nginx-deployment的创建状态
kubectl get deployment

# 查看replicaset状态
kubectl get replicaset

# 查看pod状态
# 列出的pod名字构成：kubectl create deployment 指定的名字-replicaset随机串-pod随机串
kubectl get pod

# 编辑deployment，这种方式不常用，但是很快
kubectl edit deployment deploymentName

# 查看日志
kubectl log podName

# 进入容器
# --：告诉kubectl，后面的内容不再是kubectl的参数，而是要传给容器的命令
# 退出容器用exit就可以了
kubectl exec -it podName -- /bin/bash

# 删除资源对象
kubectl delete deployment deploymentName
```

### 创建service

```sh
# 创建/删除服务
kubectl create/delete service serviceName

# 将一个已经存在的deployment对外公开为一个服务
kubectl expose deployment deploymentName

# 查看服务的详细信息
# 如果想要查看其它资源类型的详细信息，只要把service改成其它资源类型就可以了
kubectl describe service serviceName
```

### kubernetes命名空间

命名空间用来对集群中的资源进行隔离和分组的一种机制

这样在不同的命名空间中的资源就不会因为名字相同而产生冲突了

一般可以用来隔离不同的项目或者不同的环境

如果不指定命名空间，创建的资源对象就会放到default这个命名空间中

平时输入的各种命令，如果不指定命名空间，默认也都是在default命名空间中执行的

```sh
# 查看命名空间
kubectl get namespace
```

### kubernetes配置文件(yaml)

```yaml
# 指定使用哪个版本的api，用来定义怎样和apiserver交互，格式：group/version
# group有apps（应用）、batch（批处理）、autoscaling（自动扩缩容）等
apiVersion: apps/v1
# 指定资源对象类型：有Deployment、Service、ConfigMap等
# 不同的资源对象的配置会有所不同，需要根据具体情况来写配置文件，而不是用一个固定的模板
kind: Deployment
# 定义资源对象的元数据，比如资源对象的名称、标签、命名空间等
metadata:
    # 如定义创建的deployment名称
    name: containerName-deployment
# spectification的缩写，定义各种资源对象的配置信息,里面还可以嵌套spec
# 如这里是定义deployment的配置信息
spec:
    # 用来选择特定资源
    selector:
        matchLabels:
            app: nginx
    # 副本数
    replicas: 2
    template:
        metadata:
            labels:
                app: nginx
        # 定义pod的配置信息
        spec:
            containers:
                -   name: nginx
                    # 使用的镜像和版本
                    image: nginx:1.25
                    ports:
                        # 对外暴露的端口
                        - conntainerPort:  80
```

```sh
# 通过配置文件创建/删除资源对象
kubectl create/delete -f /path/to/config.yaml

# 应用配置文件中的内容到集群中（可能是创建或更新资源对象）
kubectl apply -f /path/to/config.yaml
```

#### service配置文件

```yaml
# 指定使用哪个版本的api，用来定义怎样和apiserver交互，格式：group/version
# group有apps（应用）、batch（批处理）、autoscaling（自动扩缩容）等
apiVersion: v1
# 指定资源对象类型：有Deployment、Service、ConfigMap等
# 不同的资源对象的配置会有所不同，需要根据具体情况来写配置文件，而不是用一个固定的模板
kind: Service
# 定义资源对象的元数据，比如资源对象的名称、标签、命名空间等
metadata:
    # 如定义创建的Service名称
    name: serviceName
# spectification的缩写，定义各种资源对象的配置信息,里面还可以嵌套spec
# 如这里是定义deployment的配置信息
spec:
    # 服务类型，默认是ClusterIP类型的服务，它只能在集群内部访问到
    # 指定为NodePort，节点端口类型的服务
    type: NodePort
    # 用来选择特定资源
    selector:
        # 指定所有app是nginx的资源
        app: nginx
    ports:
        -   protocol: TCP
            # 对外暴露的端口
            port:  80
            # serviec背后的pod对应的端口
            targetPort: 80
            # 节点对外提供服务的端口，在外部通过浏览器访问集群服务的就用这个端口访问，要求必须在30000~32767之间
            nodePort: 30080
```

### Portainer

kubernetes的可视化管理工具

下载配置文件：<https://downloads.portainer.io/ce-lts/portainer.yaml>

然后去掉文件里面portainer/templates/rbac.yaml和portainer/templates/pvc.yaml这两个部分的内容，以及和volume有关的内容，不然pod会一直pending启动不了

如果是k3s的话至少要有一个master节点和一个worker节点

笔者现成的配置文件：[poratiner.yaml](/file/kubernetes/portainer.yaml)

```sh
# -n：指定命名空间
kubectl apply -n portainer -f /path/to/portainer.yaml

# 查看portainer状态
kubectl get all -n portainer

# 重启portainer（pod）
kubectl rollout restart deployment portainer -n portainer
```

- 访问：<http://masterIP:30777>，第一次访问会要求设置一个密码

## 消息队列

### Pulsar

- github：<https://github.com/apache/pulsar>

#### 安装pulsar

- 下载压缩包运行

```sh
cd your-pulsar-home/bin

# 保留小黑窗不要动
./pulsar standalone
```

- 测试

```sh
# 1.打开两个虚拟机终端（如果是docker版，则是进入容器交互式终端），都进入your-pulsar-home/bin目录
# 2.在一个终端中执行消费者命令，消费者会监听，所以先执行
./pulsar-client consume topic-test -s 'sub-test'
# 3.在另一个终端中执行生产者命令
./pulsar-client produce topic-test --messages 'hello pulsar'
# 4.查看日志生产者是否成功产生消息，消费者是否成功接收消息
```

#### 安装pulsar-manager

##### 数据库配置（可选，数据量大时使用）

- 创建数据库和表：[postgresql-schema.sql](/file/pulsar-manager/postgresql-schema.sql)

- 修改配置文件:[application.properties](/file/pulsar-manager/application.properties)

```properties
# 默认内置数据库的连接串要注释掉，url直接写postgresql数据库容器名（不写端口也可以）
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://postgres01:5432/pulsar_manager
spring.datasource.username=postgres
spring.datasource.password=postgres123
```

##### compose.yaml

```yaml
pulsar-manager:
    container_name: pulsar-manager
    image: apachepulsar/pulsar-manager:v0.4.0
    ports:
        # 前端端口
        - "9527:9527"
        # 后端端口
        - "7750:7750"
    volumes:
        - /handle/data/pulsar-manager/application.properties:/pulsar-manager/pulsar-manager/application.properties
    environment:
        # 配置文件必须要指定
        - SPRING_CONFIGURATION_FILE=/pulsar-manager/pulsar-manager/application.properties
    # 通过容器服务名和pulsar通信，不写也可以
    # links:
    #     - pulsar
    networks: 
        - my-docker-net
    restart: always
```

##### 初始化登录的超级用户密码

```sh
# 第一步
CSRF_TOKEN=$(curl http://localhost:7750/pulsar-manager/csrf-token)
# 第二步
curl \
   -H 'X-XSRF-TOKEN: $CSRF_TOKEN' \
   -H 'Cookie: XSRF-TOKEN=$CSRF_TOKEN;' \
   -H "Content-Type: application/json" \
   -X PUT http://localhost:7750/pulsar-manager/users/superuser \
   -d '{"name": "pulsar", "password": "pulsar123", "description": "test", "email": "username@test.org"}'
```

#### Function

- 修改pulsar-home/conf/broker.conf

```conf
functionsWorkerEnabled=true
```

#### 进入管理页面

- 访问：<http://localhost:9527/>

- 新增环境：![pulsar-manager-environment](/images/pulsar-manager-environment.png)

#### java client

- 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-pulsar</artifactId>
</dependency>
```

- 发送和接收消息

```java
PulsarClient client = PulsarClient.builder()
                                    .serviceUrl("pulsar://localhost:6650")
                                    .build();
// 创建生产者并指定消息类型（默认字节数组）、主题
Producer<String> producer = client.newProducer(Schema.STRING)
                                    .topic("topic-test")
                                    .create();
producer.send("hello pulsar");
producer.close();

TimeUnit.SECONDS.sleep(2);

// 创建消息监听器
MessageListener messageListener = (consumer, message) -> {
    try {
        System.out.println("receive message: " + new String(message.getData()));

        // 消费确认
        consumer.acknowledge(message);
    } catch (Exception e) {
        // 消息处理失败，稍后重新发送
        consumer.negativeAcknowledge(message);
        throw new RuntimeException(e);
    }
};

// 创建消费者并指定主题、订阅
Consumer consumer = client.newConsumer()
                            .topic("topic-test")
                            .subscriptionName("subscription-test")
                            .messageListener(messageListener)
                            .subscribe();
TimeUnit.SECONDS.sleep(2);
consumer.close();
client.close();
```

- spring boot方式发送和接收消息

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public ApplicationRunner runner(PulsarTemplate<String> pulsarTemplate) {
        return (args) -> pulsarTemplate.send("hello-pulsar-topic", "Hello Pulsar World!");
    }

    @PulsarListener(subscriptionName = "hello-pulsar-sub", topics = "hello-pulsar-topic")
    public void listen(String message) {
        System.out.println("Message Received: " + message);
    }
}
```

- jdk17以上版本，需要添加jvm参数

```sh
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/sun.net.util=ALL-UNNAMED
--add-opens java.base/sun.net=ALL-UNNAMED
```

#### 事务支持

- 修改broker.conf

```conf
# 开启事务支持
transactionCoordinatorEnabled=true
#开启批量确认
acknowledgmentAtBatchIndexLevelEnable=true
```

- 初始化事务协调器元数据，然后重启bookie和broker

```sh
bin/pulsar initialize-transaction-coordinator-metadata -cs addr1:port1,addr2:port2 -c pulsar-cluster
bin/pulsar-daemon stop broker
bin/pulsar-daemon start broker
bin/pulsar-daemon stop bookie
bin/pulsar-daemon start bookie
```

- 构建支持事务的客户端

```java
PulsarClient client = PulsarClient.builder()
                                    .serviceUrl("pulsar://localhost:6650")
                                    .enableTransaction(true)
                                    .build();

```

### RocketMQ

中文官网：<https://rocketmq.apache.org/zh/>

#### 启动

- 启动NameServer

```sh
### 启动namesrv
nohup sh bin/mqnamesrv &
 
### 验证namesrv是否启动成功
tail -f ~/logs/rocketmqlogs/namesrv.log
```

- 启动Broker+Proxy

NameServer成功启动后，我们启动Broker和Proxy，5.x 版本下我们建议使用 Local 模式部署，即 Broker 和 Proxy 同进程部署

```sh
### 先启动broker
nohup sh bin/mqbroker -n localhost:9876 --enable-proxy &

### 验证broker是否启动成功, 比如, broker的ip是192.168.1.2 然后名字是broker-a
tail -f ~/logs/rocketmqlogs/proxy.log 
```

#### 关闭

```sh
sh bin/mqshutdown broker

sh bin/mqshutdown namesrv
```

- 通过mqadmin创建 Topic

```sh
sh bin/mqadmin updatetopic -n localhost:9876 -t TestTopic -c DefaultCluster
```

#### 消息类型

Normal/FIFO/Delay/Transaction

系统默认的消息最大限制如下：

普通和顺序消息：4 MB

事务和定时或延时消息：64 KB

### RabbitMQ

#### 安装RabbitMQ

```sh
rabbitmq:
    container_name: rabbitmq01
    image: rabbitmq:4.0.2-management
    ports:
        # 前端端口
        - "15672:15672"
        # 后端端口
        - "5672:5672"
    environment:
        - RABBITMQ_DEFAULT_USER=rabbitmq
        - RABBITMQ_DEFAULT_PASS=rabbitmq123
    networks: 
        - my-docker-net
    restart: always
```

#### 登录rabbitmq管理页面

- 管理页面：主机地址:15672

#### spring boot发送/接收测试

- 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

- 配置

```yaml
spring:
    rabbitmq:
        host: localhost
        port: 5672
        username: rabbitmq
        password: rabbitmq123
        # 交换机确认
        publisher-confirm-type: CORRELATED
        # 队列确认
        publisher-returns: true
        listener:
            simple:
                # 消息确认模式设置为手动确认
                acknowledge-mode: manual
                # 每次从队列中取回消息的数量，用来做消费端限流
                prefetch: 1
```

- 监听并消费消息

```java
import com.rabbitmq.client.Channel;
import org.springframework.amqp.core.Message;

@Slf4j
@Component
public class RabbitmqMessageListener {
    private static final String EXCHANGE_DIRECT = "exchange.direct.order";

    private static final String ROUTING_KEY = "order";

    private static final String QUEUE_NAME = "queue.order";

    // 监听+创建交换机、队列和绑定路由键
    @RabbitListener(bindings = @QueueBinding(
            // 持久化设置为true
            value = @Queue(value = QUEUE_NAME, durable = "true"),
            exchange = @Exchange(value = EXCHANGE_DIRECT),
            key = {ROUTING_KEY}
    ))
    // 只监听（如果rabbitmq服务器已经有对应交换机和队列了）
    // @RabbitListener(queues = {QUEUE_NAME})
    public void processMessage(String content, Message message, Channel channel) {
        log.info("receive message: {}", content);
    }
}
```

- 发送消息

```java
@SpringBootTest
public class ApplicationTest {
    private static final String EXCHANGE_DIRECT = "exchange.direct.order";

    private static final String ROUTING_KEY = "order";

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void test() {
        rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq");
    }
}
```

#### rabbitmq四种模式

- 1.direct，点对点，只发送到路由键完全匹配的队列
- 2.fanout，广播，不管路由键是什么都全部发送到绑定的队列
- 3.topic，根据路由键匹配规则匹配，*匹配一个，#匹配0个或多个，只发送到匹配的队列
- 4.headers，很少用

#### 定义默认消息转换器

```java
package com.handle.amqp.configuration;

import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置amqp默认的消息转换器，将对象转成json发送
 *
 * @author handle
 * @date 2022-05-06 22:24:30
 * @since jdk-1.8
 */
@Configuration
public class AmqpConfiguration {
    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}

```

#### 创建、绑定或删除交换器、队列

```java
@SpringBootTest
public class AmqpApplicationTests {
    @Autowired
    private AmqpAdmin amqpAdmin;

    @Test
    public void testAmqpAdmin() {
        // 创建交换器
        amqpAdmin.declareExchange(new DirectExchange("amqpAdmin.direct"));

        // 创建队列
        amqpAdmin.declareQueue(new Queue("amqpAdmin.queue"));

        // 绑定队列到交换器
        amqpAdmin.declareBinding(
            new Binding("amqpAdmin.queue", Binding.DestinationType.QUEUE, "amqpAdmin.direct", "amqpAdmin", null));
        // 删除队列
        amqpAdmin.deleteQueue("amqpAdmin.queue");
        // 删除交换器
        amqpAdmin.deleteExchange("amqpAdmin.direct");
    }
}
```

#### 消息可靠

##### 生产端

###### 消息确认方式1

- 最常用方式

```java
@Slf4j
@Configuration
public class RabbitmqConfiguration implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnsCallback {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @PostConstruct
    public void initRabbitTemplate() {
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnsCallback(this);
    }
    /**
     * 确认消息是否发送到交换机（成功/失败）
     * ack true，成功发送到交换机
     * cause 发送失败原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        if (ack) {
            return;
        }
        log.error("消息发送到交换机失败，原因：{}", cause);
        // 补偿措施
    }

    /**
     * 确认消息是否发送到队列，失败时才调用
     */
    @Override
    public void returnedMessage(ReturnedMessage returnedMessage) {
        log.error("消息发送到队列失败");
        log.info("消息主体：{}", new String(returnedMessage.getMessage().getBody()));
        log.info("应答码：{}", returnedMessage.getReplyCode());
        log.info("应答描述：{}", returnedMessage.getReplyText());
        log.info("交换器：{}", returnedMessage.getExchange());
        log.info("路由键：{}", returnedMessage.getRoutingKey());
    }
}
```

###### 消息确认方式2

- 备份交换机，针对目标交换机故障的情况
    - 创建交换机，类型必须是fanout
    - 创建队列，和备份交换机绑定
    - 给目标交换机（Arguments）指定备份交换机

##### 服务器

- 消息持久化
    - 交换机持久化（默认）：durable true，autoDelete false
    - 队列持久化（默认）：durable true，autoDelete false

##### 消费端

- 消费消息成功，给服务器返回ack信息，然后消息队列删除该消息
- 消费消息失败，给服务器返回nack信息，然后消息队列把消息恢复为待消费状态，这样消费者可以再次取回消息重试（需要消费端支持幂等性）

#### 消息超时

- 可在队列创建的时候设置消息的过期时间（x-message-ttl），队列中的所有消息使用这个超时
时间
- 给具体的某个消息设置过期时间
- 如果这两个都做了设置，哪个时间短，哪个生效

```java
// 给具体的某个消息设置过期时间
MessagePostProcessor messagePostProcessor = message -> {
    message.getMessageProperties().setExpiration("5000");
    return message;
};
rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq", messagePostProcessor);
```

#### 死信和死信队列

- 当一个消息无法被消费，它就变成了死信
- 死信产生的原因有如下三种
    - 消费者拒接消息（basicNack/basicReject），并且requeue=false
    - 队列中消息数量达到限制（x-max-length），如果再来一条消息，根据先进先出原则，队列中最早的消息会变成死信
    - 消息超时未被消费
- 死信的处理方式
    - 丢弃：不重要的消息直接丢弃，不做处理
    - 入库：把死信写入数据库，日后处理
    - 监听：将消息放到死信队列，专门设置消费端监听死信队列，做后续处理（通常采用）
        - 创建队列时设置x-dead-letter-exchange、x-dead-letter-routing-key

#### 延迟队列

- 实现方案
    - 设置消息超时时间+死信队列
    - 安装插件，延迟极限最多两天

##### 安装延迟队列插件

官网：<https://github.com/rabbitmq/rabbitmq-delayed-message-exchange>

- 官网下载插件文件放到rabbitmq的指定目录（/plugins）
- 启用插件

```sh
rabbitmq-plugins enable 插件名
```

- 然后重启rabbitmq

- 创建交换机，指定type为x-delayed-message，再通过参数x-delayed-type指定交换机类型（direct/topic等)

- 创建延时消息

```java
MessagePostProcessor messagePostProcessor = message -> {
    // x-delay只有装了上面的延时插件才会生效
    message.getMessageProperties().setHeader("x-delay", "5000");
    return message;
};
rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq", messagePostProcessor);
```

- 需要注意的是，通过延时队列插件发送的消息无论成功/失败都会走returnedMessage方法

#### 事务消息

- 只对生产者端生效，控制缓存里面的消息要么全部发送到broker（业务逻辑都OK），要么全部都不发送（业务逻辑异常了）
- 不能解决生产端消息可靠性传递的问题

- 修改配置

```java
@Bean
public RabbitTransactionManager rabbitTransactionManager(CachingConnectionFactory factory) {
    return new RabbitTransactionManager(factory);
}

@Bean
public RabbitTemplate rabbitTemplate2(CachingConnectionFactory factory) {
    RabbitTemplate rabbitTemplate1 = new RabbitTemplate(factory);
    rabbitTemplate1.setChannelTransacted(true);
    return rabbitTemplate1;
}
```

- 创建事务，消息1和消息2要么全部发送要么都不发送

```java
@Transactional
@Test
// junit默认会回滚事务，这里为了测试效果所以要设置为false
@Rollback(value = false)
public void test2() {
    rabbitTemplate1.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "message1");
    int i = 1 /0 ;
    rabbitTemplate1.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "message2");
}
```

#### 惰性队列

- 前提是队列是持久化的
- 未设置惰性模式时队列只有在队列满了或者borker关闭的时候才做持久化操作
- 设置了惰性队列时（默认），服务器空闲就会持久化

#### 优先级队列

- 创建队列时指定参数x-max-priority，（1-5）就可以了，消息的优先级就不能高于这个设置的值

```java
MessagePostProcessor messagePostProcessor = message -> {
    // 不要超过x-max-priority，数值越高，优先级越高
    message.getMessageProperties().setPriority(3);
    return message;
};
rabbitTemplate.convertAndSend(EXCHANGE_DIRECT, ROUTING_KEY, "hello rabbitmq", messagePostProcessor);
```

#### rabbitmq集群

##### 搭建rabbitmq集群

- 思想：锚定某一个rabbitmq服务器作为基础节点，其它的rabbitmq服务器都加入到这个节点

- 所有节点的Cookie值要设置为一样的

```sh
# 查看cookie
cat /var/lib/rabbitmq/.erlang.cookie
# 设置cookie
vi /var/lib/rabbitmq/.erlang.cookie
```

-修改/etc/hosts，追加如下内容

```sh
具体ip node01
具体ip node02
具体ip node03
```

- 重置节点应用并加入集群

```sh
rabbitmqctl stop_app
rabbitmqctl reset
# 所有rabbitmq服务器节点都加入到node01
rabbitmqctl join_cluster rabbti@node01
rabbitmqctl start_app
```

- 最后可以在管理页面的nodes查看集群

##### rabbitmq负载均衡

- 安装haproxy

```sh
# Red Hat系列Linux
yum install -y haproxy
# 查看版本
haproxy -v
```

- 修改配置文件：/etc/haproxy/haproxy.cfg

```conf
# 前端功能
# 前端配置（自定义前端名称）
frontend rabbitmq_ui_frontend
# 以后浏览器通过访问这个地址进入管理页面
bind ip:port
mode http
# 后端配置
# 默认后端，引用后端名称
default_backend rabbitmq_ui_backend
# 后端名称（自定义后端名称）
backend rabbitmq_ui_backend
mode http
balance roundrobin
option httpchk GET /
# 服务器节点的地址
server rabbitmq_ui1 ip1:15672 check
server rabbitmq_ui2 ip2:15672 check
server rabbitmq_ui3 ip3:15672 check

# 核心功能（自定义前端名称）
frontend rabbitmq_frontend
# 以后后端通过访问这个地址进入和rabbitmq通信
bind ip:port
mode tcp
# 后端配置
# 默认后端，引用后端名称
default_backend rabbitmq_backend
# 后端名称（自定义后端名称）
backend rabbitmq_backend
mode tcp
balance roundrobin
# 服务器节点的地址
server rabbitmq_1 ip1:5672 check
server rabbitmq_2 ip2:5672 check
server rabbitmq_3 ip3:5672 check
```

- 设置SELinux策略，允许haproxy拥有权限连接任意接口

```sh
setsebool -P haproxy_connect_any=1
# 启动haproxy
systemctl start haproxy
# 开机启动
systemctl enable haproxy
```

##### 仲裁队列

- 在某一个节点上创建队列，会自动分散到各个节点上
- 创建队列时，type选择Quorum，node选择一个节点作为主节点

## Elasticsearch

- 官网：<https://www.elastic.co/cn/elasticsearch>

Elastic Stack（ELK Stack）：包括Elasticsearch、Kibana、Beats和Logstash，它们能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化

其中Elasticsearch是一个开源的高扩展的分布式全文搜索引擎，是整个ELK Stack的核心

### 核心概念

#### 索引（Index）

一个索引就是一个拥有相似特征的文档的集合

#### 文档（Document）

一个文档是一个可被索引的基础信息单元，也就是一条数据

文档以json格式来表示

#### 字段（Field）

对文档数据根据不同属性进行的分类标识，相当于数据表的字段

#### 映射（Mapping）

在处理数据的方式和规则方面做一些限制

如：某个字段的数据类型、默认值、分析器、是否被索引等，都可以在映射里面设置

至于其它处理ES数据的一些使用规则设置也叫做映射

#### 分片（Shards）

一个索引可以存储超出单个节点硬件限制的大量数据，这可能导致任一节点都没有这样大的磁盘空间，或者导致单个节点处理搜索请求时，响应太慢

为了解决这个问题，ES提供了将索引划分成多份的能力，每一份就称之为分片（相当于关系型数据库的分表）

- 保存数据时，通过路由计算保存到哪个分片：hash(id) % 主分片数量
    - 客户端请求任意集群节点（协调节点）
    - 协调节点将请求转发到指定节点
    - 主分片将数据保存
    - 主分片将数据发送给副本
    - 副本保存后进行反馈
    - 主分片进行反馈
    - 客户端获取反馈

- 读取数据时，通过分片控制决定读取那个节点上的数据：可以访问任何一个节点获取数据，这个被访问的节点称为协调节点
    - 客户端请求任意集群节点（协调节点）
    - 协调节点计算数据所在分片及全部的副本位置
    - 协调节点将请求转发给具体的节点（负载均衡，轮询所有节点）
    - 节点返回查询结果，将结果反馈回客户端

#### 副本（Replicas）

为了应对某个分片或节点发生故障的情况，需要有一个故障转移机制

为此ES允许创建分片的一份或多份拷贝，，这些拷贝就叫作复制分片或副本

#### 分配（Allocation）

将分片分配给某个节点的过程，包括分配主分片或副本

如果是副本，还包含从主分片复制数据的过程，这个过程是由master节点完成的

一般分片数不超过节点数的3倍，可参考：节点数<=主分片数*(副本数+1)

#### 评分机制

`得分 = boost * idf * tf`

boost：权重系数，默认2.2

##### TF（词频）

Term Frequency：搜索文本中的各个词条（term）在查询文本中出现了多少次，出现次数越多，就越相关，得分越高

`tf = freq / (freq + k1 * (1 - b + b * dl / avgdl))`

freq：关键词在当前文档中出现的次数

k1：关键词参数，默认1.2

b：关键词长度系数参数，默认0.75

dl：分词个数

avgdl：fields / documents，所有分词除以所有的文档数

##### IDF（逆文档频率）

Inverse Document Frequency：搜索文本中的各个词条（term）在整个索引的所有文档中出现了多少次，出现的次数越多，说明越不重要，也就越不相关，得分越低

`idf = log(1 + (N -n + 0.5) / (n + 0.5))`

log：e的对数

N：文档的总字段数

n：文档包含的词条数

### 安装elasticsearch

以elasticsearch 9.x为例

- 下载tar.gz文件并解压到指定目录

#### 修改config/elasticsearch.yaml文件

##### 单点部署

```yaml
# 9.x版本指定ip和端口就可以进行访问了
network.host: 10.0.2.15
http.port: 9200

# 这一行是启动后动态生成的，开发阶段可以设置为false，就可以不用密码登录了
xpack.security.enabled: false
```

- 下面是网上的单点部署教程，笔者没试过，保留着

```yaml
# 集群名称
cluster.name: elasticsearch
# 节点名称
node.name: node-1
network.host: 10.0.2.15
http.port: 9200
# master节点名称，跟上面的节点名称一致
cluster.initial_master_nodes:["node-1"]

# 编辑/etc/security/limits.conf
# 在末尾添加如下内容，设置每个进程可以打开的文件数的限制
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536

# 编辑/etc/security/limits.d/20-nproc.conf
# 在末尾添加如下内容，设置每个进程可以打开的文件数的限制
elasticsearch soft nofile 65536
elasticsearch hard nofile 65536
# 操作系统级别对每个用户创建的进程数的限制，"*"表示所有用户名
* hard nproc 4096

# 编辑/etc/sysctl.conf
# 在末尾添加如下内容，设置一个进程可以拥有的VMA（虚拟内存区域）的数量，默认65536
vm.max_map_count=655360

# 然后重新加载
sysctl -p
```

##### 集群部署

```yaml
# 集群名称
cluster.name: elasticsearch-cluster
# 节点名称，每个节点的名称不能重复
node.name: node-1
node.roles: [ master, data ]
# 节点主机名/ip，每个节点的主机名/ip不能重复
network.host: 10.0.2.15
# 节点端口
http.port: 9200
# 全新集群第一次启动时用来选举初始master的引导名单
# 初始化一个新的集群时需要此配置来选举master
# 第一次启动集群成功后立刻删掉，不然如果该机器宕机重启后将出现问题
# 貌似8.x后面部署集群不需要了
#cluster.initial_master_nodes:["node-1", "node-2", "node-3"]
# 节点发现，默认用9300端口通信
discovery.seed_hosts: ["10.0.2.15:9300", "10.0.2.16:9300", "10.0.2.17:9300"]
```

#### 启动elasticsearch

- elasticsearch不允许使用root启动，先创建elasticsearch用户

```sh
# 创建elasticsearch用户，然后根据提示指定密码
useradd elasticsearch

# 设置密码
passwd elasticsearch

# 将elasticsearch目录和它的所有子目录/文件的权限赋给刚创建的用户
chown -R elasticsearch:elasticsearch /path/to/elasticsearch-xxx

# 切到elasticsearch用户
su elasticsearch
```

- 启动elasticsearch

```sh
# 先进入elasticsearch的bin目录
cd /path/to/elasticsearch-xxx/bin

# 启动elasticsearch
# 启动时，会动态生成文件，如果文件所属用户不匹配，会发生错误，因此前面需要先创建用户和设置目录权限
# -d：后台启动
# 第一次启动会打印elastic的密码和fingerprint，记得复制下来
./elasticsearch [-d]
```

- 测试，访问：<https://localhost:9200/>，输入用户名elastic，密码根据控制台打印的输入

- 修改密码

```sh
# -u：指定用户名
# -i：交互模式，如果不加此选项会进入自动生成密码的模式，无法自定义密码
sudo /path/to/elasticsearch/bin/elasticsearch-reset-password -u elastic -i
```

- 找回fingerprint

```sh
# 证书在/path/to/elasticsearch/config/certs
openssl x509 -noout -fingerprint -sha256 -in /path/to/elasticsearch/config/certs/http_ca.crt | tr -d ':'
```

### Java Client

- 依赖

```xml
<dependency>
    <groupId>co.elastic.clients</groupId>
    <artifactId>elasticsearch-java</artifactId>
    <version>9.2.0</version>
</dependency>
```

- 配置

```java
@Configuration
public class ElasticsearchConfiguration {
    private String url = "https://ip:9200";

    private String username = "elastic";

    private String password = "...";

    private String fingerprint = "...";

    @Bean
    public ElasticsearchClient elasticsearchClient() {
        SSLContext sslContext = TransportUtils
                .sslContextFromCaFingerprint(fingerprint);

        return ElasticsearchClient.of(b -> b
                .host(url)
                .usernameAndPassword(username, password)
                .sslContext(sslContext)
        );
    }
}
```

- 使用

```java
// 创建index
CreateIndexResponse createIndexResponse = elasticsearchClient.indices().create(c -> c.index("test-index"));
System.out.println(createIndexResponse.acknowledged());

// 查询index
GetIndexResponse getIndexResponse = elasticsearchClient.indices().get(c -> c.index("test-index"));
System.out.println(getIndexResponse.toString());
```

### SpringData集成

官网：<https://spring.io/projects/spring-data-elasticsearch>

### Kibana

Kibana 是一个用户界面，让你对es数据进行可视化的各种操作

下载：<https://www.elastic.co/cn/downloads/kibana>，然后解压，以9.x版本为例

```sh
# 将kibana目录和它的所有子目录/文件的权限赋给elasticsearch
chown -R elasticsearch:elasticsearch /path/to/kibana-xxx
```

- 自动生成es的kibana_system用户密码

```sh
elasticsearch/bin/elasticsearch-reset-password -u kibana_system
```

- 编辑配置文件/path/to/kibana/config/kibana.yaml

```yaml
server.port: 5601
server.host: "ip"
# es节点的ip和端口
elasticsearch.hosts: ["https://ip:port"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "..."
elasticsearch.ssl.verificationMode: none
```

- 启动

```sh
su elasticsearch

/path/to/kibana/bin/kibana
```

- 访问<http://10.0.2.15:5601>，然后输入elastic和密码就可以了

### EQL

Event Query Language，事件查询语言，是一种基于事件的时间序列数据（如日志、指标和跟踪）的查询语言

要运行EQL搜索，搜索到的数据流或索引必须包含时间戳和事件类别字段

### ES的SQL

```json
// 查询所有索引
GET _sql?format=txt
{
    "query": """
        show tables
    """
}

// 查询指定索引
GET _sql?format=txt
{
    "query": """
        show tables like 'test%'
    """
}

// 查询索引结构，索引有特殊符号要用引号括住
GET _sql?format=txt
{
    "query": """
        describe "indexName"
    """
}

// 查询索引的文档数据
GET _sql?format=txt
{
    "query": """
        select * from "indexName" limit 5
    """
}

// rlike，r表示正则
GET _sql?format=txt
{
    "query": """
        select * from "indexName" where name rlike 'ha*le'
    """
}
```

#### 游标（cursor）

游标是系统为用户开设的一个数据缓冲区，存储sql语句的执行结果

每个游标区都有一个名字，用户可以用sql语句逐一从游标中获取记录，并赋给主变量，交由主语言进一步处理

```json
// 第一次查询写法，查询结果中有超过1条数据时只显示一条并且末尾包含游标
GET _sql?format=json
{
    "query": """
        select * from "indexName"
    """,
    "fetch_size": 1
}
// 然后可以复制第一次查询结果里面的游标，执行后就可以继续查看剩余的查询结果
// 如果执行后无结果，说明数据已经读取完毕，再次执行会返回错误结果
GET _sql?format=json
{
    "cursor": ...
}

// 最后关闭缓冲区
POST _sql/close
{
    "cursor": ...
}
```

## git

### 安装

#### Arch Linux

```sh
sudo pacman -S git

# 需要安装openssh才能使用ssh-keygen命令生成ssh密钥
sudo pacman -S openssh
```

### 配置git

```sh
# 安装完成后，还需要最后一步设置，因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址
# global参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

# 然后查看git配置，看看刚刚的设置正确没有
# Windows
git config --global --list

# Arch Linux
cat ~/.gitconfig
```

### 生成SSH公/密钥

```sh
# 生成ssh key
# -t：ed25519表示指定密钥类型为Ed25519，rsa不再是首选了
# -C：注释
ssh-keygen -t ed25519 -C "这里填你的邮箱"

# 复制公钥
cat /path/to/id_ed25519.pub
```

### 创建版本库

- 创建一个空目录并进入这个空目录

```sh
mkdir -p 目录名
cd 目录名
```

- 通过git init命令把这个目录变成Git可以管理的仓库

```sh
git init
```

### 把文件添加到版本库

- 新建文件放到本地仓目录下
- 新建文件添加到仓库

```sh
# Git命令必须在Git仓库目录内执行。添加某个文件时，该文件必须在当前目录下存在
git add 新建文件名1 新建文件名2
```

- 把文件提交到仓库

```sh
git commit -m "自定义提交说明"
```

- 查看仓库当前状态

```sh
git status
```

- 查看文件修改了什么内容

```sh
git diff 文件名
```

- 知道文件修改了什么内容后，可以放心提交到仓库

```sh
git add 文件名
# commit之前可以再看看当前仓库状态，更放心提交
git commit -m "自定义提交说明"
```

- 从暂存区域移除，然后提交

```sh
git rm filename
```

- 文件重命名

```sh
# 这个命令相当于mv 旧文件名 新文件名、git rm 旧文件名、git add 新文件名 
git mv 旧文件名 新文件名
```

- 从一个服务器克隆一个现有的 Git 仓库并自定义本地仓库的名字

```sh
git clone url directoryname
```

```sh
# 查看提交历史
git log

# 按行展示log
git log --pretty=oneline

# 查看某个人的提交记录
git log --author=somebody

# 查看git历史执行过的命令
git reflog
```

- 撤销操作

```sh
# 尝试重新提交
git commit --amend

# 取消暂存的文件
git reset filename

# 撤消对文件的修改
git checkout --filename

# 丢弃本地的所有改动与提交，获取服务器上最新的版本历史，并将本地主分支指向它
git fetch origin

# 回退到上一个版本
git reset --hard HEAD^

# 回退到指定分支
git reset --hard origin/dev

# 回退到指定提交id
git reset --hard commitId
```

### 远程仓库

```sh
# 添加远程仓库
git remote add origin remoteUrl

# 远程仓库重命名
git remote rename origin person

# 移除远程仓库
git remote rm origin

# 将本地改动提交到远程仓库
git push origin branchName
```

### 分支操作

```sh
# 创建一个名字叫做 dev 的分支
git branch dev

# 切换当前分支为dev
git checkout dev

# 直接创建分支并切换过去
git checkout -b dev

# 合并分支
git merge dev

# 把新建的分支删掉
git branch -d dev

# 将分支推送到远端仓库
git push origin
```

### git常见问题

- Please commit your changes or stash them before you merge.

解决方法：通过git stash将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常git pull了，git pull完成后，执行git stash pop将之前本地做的修改应用到当前工作区。

```cmd
git stash
git pull
git stash pop
```

git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。

git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。

## 数据库

- sql语句和注释应该用通用写法，这样在不同数据库之间都兼容

```sql
-- 注释内容，注意双减号后有空格
```

- 如果考虑到后期会进行数据库迁移，可以用Long类型来定义时间字段
    - 如：create_at_utc_milli，直接点明了时区基准（零时区），避免了夏令时和跨地域部署的坑，数据单位和精度（毫秒级）

### PostgreSQL

#### 数据库连接串

```properties
jdbc.driverClassName=org.postgresql.Driver
jdbc.jdbcUrl=jdbc:postgresql://localhost:5432/handle
jdbc.username=postgres
jdbc.password=postgres123
```

#### 安装docker版本PostgreSQL

```sh
docker pull postgres：16.4
```

##### 启动PostgreSQL

- docker版本的pgsql启动成功后无需配置监听地址、端口（postgresql.conf）和ip（pg_hba.conf）

- 默认已经是监听所有地址，端口5432，ip：host all all all scram-sha-256

- 默认已经创建数据库postgres，用户postgres，密码登录时已经指定

```sh
# 启动数据库
docker run -p 5432:5432 --name postgres01 -e POSTGRES_PASSWORD=postgres123 -v /data/postgresql:/var/lib/postgresql/data -d postgres:16.4
```

- compose.yaml

```yaml
postgresql:
    container_name: postgres01
    image: postgres:16.4
    ports:
        - "5432:5432"
    environment:
        - POSTGRES_PASSWORD=postgres123
    volumes:
        # PostgreSQL数据库版本17及以下的版本
        - /handle/data/postgresql:/var/lib/postgresql/data
        # PostgreSQL数据库版本18及以上的版本
        - /handle/data/postgresql:/var/lib/postgresql
    networks: 
        - my-docker-net
    restart: always
```

- 1.控制台登录Postgresql

```sh
# 进入数据库终端
docker exec -it postgres01 /bin/bash

# 切换用户为postgres
su - postgres

# 进入数据库
psql [postgres]
```

- 2.vscode安装数据库插件后连接数据库

nat网络，需要配置virtualbox端口映射，如5432:5432，则直接用127.0.0.1:5432连接

- 3.pgadmin4连接数据库

假设pgadmin4也是docker版本的并且和数据库在同一个虚拟系统上，则连接时直接用虚拟系统的ip+数据库端口连接

#### pg控制台命令

|快捷键|功能|
|:-|:-|
|\password|设置密码|
|\h [sql命令]|查看sql命令的解释|
|\?|查看pgsql命令列表|
|\l|列出所有数据库|
|\c [database_name]|连接其它数据库|
|\d|列出当前数据库的所有表格|
|\d [tablename]|列出指定表的结构|
|\du|列出所有用户|
|q|退出|

#### 备份

可以选择的备份格式：*.bak、*.sql、*.tar

- 单个数据库

```sh
# 备份数据库到bak文件
pg_dump dbname > dbname.bak

# 从bak文件恢复数据到指定数据库，数据库不存在时需要先创建数据库
psql dbname < dbname.bak
```

- 全部数据库

```sh
# 备份所有数据库到bak文件
pg_dumpall > pg.bak

# 从bak文件恢复所有数据库数据
psql -f pg.bak [-U] postgres
```

#### 用户操作

```sql
-- 创建用户并设置密码
create user 'username' with password 'password';

-- 修改用户密码
alter user 'username' with password 'password';

-- 指定数据库的所有权限赋予指定用户
grant all privileges on database 'dbname' to 'username';

-- 赋予数据库的所有权限后，还要指定表的所有权限赋予指定用户，才可以读写表
grant all privileges on all tables in schema 'schema' to 'username';

-- 移除指定用户对于指定数据库的所有权限
revoke all privileges on database 'dbname' from 'username';

-- 删除用户
drop user 'username;
```

#### 角色管理

pg没有区分用户和角色的概念，唯一区别就是创建用户和创建角色

```sql
-- 默认不具有登录属性
create role 'rolename';

alter role 'rolename' with login;

-- 默认具有登录属性
create user 'username';

-- 查询角色信息
select * from pg_roles;

-- 查询用户信息
select * from pg_user;
```

#### 创建数据库

```sql
-- 当自定义的名字和关键字冲突时需加上英文双引号
create database "order";
comment on database "order" is '订单数据库'; 
```

#### 创建表

```sql
create table account (
    id bigint,
    name varchar(32),
    password varchar(32),
    salt varchar(128),
    gender boolean,
    enabled boolean not null,
    creator bigint,
    modifier bigint,
    -- timestamptz在Java8中用OffsetDateTime对应
    creation_time timestamptz,
    modification_time timestamptz,
    version integer not null default 1,
    deleted boolean not null default false,
    primary key(id)
);

comment on table account is '账号表'; 
comment on column account.id is '主键';
comment on column account.name is '姓名';
comment on column account.gender is '性别';
comment on column account.enabled is '是否启用';
comment on column account.creator is '创建人';
comment on column account.modifier is '修改人';
comment on column account.creation_time is '创建时间';
comment on column account.modification_time is '修改时间';
comment on column account.version is '修改版本';
comment on column account.deleted is '是否已逻辑删除';
```

#### pgadmin4

- 获取镜像

```sh
docker pull dpage/pgadmin4:<tag name>
```

- 启动容器

```sh
docker run -p 5050:80 \
-e 'PGADMIN_DEFAULT_EMAIL=user@domain.com' \
-e 'PGADMIN_DEFAULT_PASSWORD=pgadmin123' \
--name pgadmin4 \
-d dpage/pgadmin4:<tag name>
```

### MySQL

#### windows安装（免安装版）

以下安装步骤均在管理员身份的dos窗口中执行。

1. 初始化数据库生成空的登录密码：

   ```cmd
   mysqld --initialize -insecure –user=mysql
   ```

2. 安装 mysql（服务）：

   ```cmd
       mysqld --install mysql
   ```

3. 启动 mysql 服务：

   ```cmd
   net start mysql
   ```

4. 登录 mysql 服务器，初始化没有生成密码，提示输入密码直接按回车登录：

   ```cmd
   mysql -u root -P 端口 -p
   ```

5. 修改 root 账户密码：

   ```cmd
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'mysql123';
   ```

6. 关闭服务：

   ```cmd
   net stop mysql
   ```

7. 移除 mysql（服务）：

   ```cmd
   mysqld --remove mysql
   ```

#### RedHat系列Linux安装MySQL

```sh
# 查看是否安装了mysql相关的组件
rpm -qa | grep mariadb

# 卸载mysql相关组件
# --nodeps：就算被其它包依赖了也强行卸载
rpm -e --nodeps mariadb-libs

# 下载mysql-xxx.rpm-bundle.tar,然后解压
# 依次安装以下的rpm包
rpm -ivh mysql-community-common-xxx.rpm
rpm -ivh mysql-community-libs-xxx.rpm
rpm -ivh mysql-community-client-xxx.rpm
rpm -ivh mysql-community-server-xxx.rpm

# 启动mysql
sudo systemctl start mysqld

# 查看mysql初始账号密码
grep "password" /var/log/mysqld.log
```

#### Docker安装MySQL

```sh
# 安装
docker pull mysql:8.0.29

# 启动
docker run -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=mysql123 -d mysql:8.0.29 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

- compose.yaml

```yaml
name: projectname
services: 
    mysql:
        container_name: mysql_container_name
        image: mysql:8.0.29
        ports:
            - "3306:3306"
        environment:
            - MYSQL_ROOT_PASSWORD: root_passwdord
            - MYSQL_DATABASE: dbname
            - MYSQL_USER: user_dbname
            - MYSQL_PASSWORD: user_password
        volumes:
            - mysql-data:/var/lib/mysql
            - mysql-conf:/etc/mysql/conf.d
        networks: 
            - projectname-net
        restart: always
networks: 
    projectname-net: 
        name: projectname-net
volumes: 
    mysql-data: 
        name: mysql-data
    mysql-conf: 
        name: mysql-conf
```

#### 登录

标识密码的p要小写，指定端口号的P要大写

```sql
mysql -u root -p

-- 指定ip和端口号登录
mysql -u root -p -h 127.0.0.1 -P 3306

-- 修改密码
set password = Password('你的密码');

-- 远程访问数据库
-- 修改host字段的值为需要远程连接数据库的主机ip地址或者直接修改成%
-- host的值为'%'表示所有主机可以通过该用户访问数据库
update user set host='%' where user='dbadmin';
flush privileges;
```

#### phpMyAdmin

phpMyAdmin是mysql的数据库管理工具，可以通过浏览器进行操作

官网：<https://www.phpmyadmin.net/>

- 安装docker版phpmyadmin

```sh
docker pull phpmyadmin:5.2.3 
```

- compose.yaml

```yaml
phpmyadmin:
    image: phpmyadmin:5.2.3
    container_name: phpmyadmin
    ports:
        - 8080:80
    environment:
        PMA_ARBITRARY: 1
```

- 访问<http://你的ip:8080/>

#### MySQL字符集

- 层级：server（MySQL 实例级别）、database（库级别）、table（表级别）、column（字段级别），优先级从左往右依次增大

- 查看数据库字符集

```sql
select default_character_set_name, default_collation_name
from information_schema.schemata 
where schema_name = 'db_name';

-- 显示数据库支持的字符集
show charset;
```

##### 连接字符集

- character_set_client ：描述了客户端发送给服务器的 SQL 语句使用的是什么字符集。
- character_set_connection ：描述了服务器接收到 SQL 语句时使用什么字符集进行翻译。
- character_set_results ：描述了服务器返回给客户端的结果使用的是什么字符集

- 查看连接字符集

```sql
select * from performance_schema.session_variables
where variable_name in (
'character_set_client', 'character_set_connection',
'character_set_results', 'collation_connection'
) 
order by variable_name;
```

##### 排序字符集

- utf8mb4_unicode_ci，是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序
- utf8mb4_general_ci，没有实现Unicode排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致，在比较和排序的时候更快
- 使用的时候统一只用某一种就行

##### JDBC驱动字符集

- characterEncoding
- characterSetResults

- MySQL连接串

```properties
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# useUnicode=true&characterEncoding=UTF-8会自动映射为MySQL的utf8mb4
# 一般设置characterEncoding就可以了，新版MySQL驱动会自动处理好整个通信链路的字符集
# 它会向 MySQL 服务器发送一条关键的命令："SET NAMES utf8mb4;"
# 这条命令的效果等同于同时设置了以下三个会话变量，确保数据从客户端到服务端再返回客户端的全链路字符集一致：character_set_client、character_set_connection和character_set_results
spring.datasource.url=jdbc:mysql://localhost:3306/handle?useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=mysql123
```

#### 数据类型

![mysql数据类型](/images/mysql数据类型.png)

char: 定长，非常适合存储密码的哈希值

varchar: 字符串列的最大长度比平均长度大很多，列的更新很少时使用

- 同财务相关的金额类数据必须使用 decimal 类型（精准浮点数，在计算时不会丢失精度）

- 存储时间两种方式
    - DATETIME + UTC：最推荐的“折中方案”
        - 存入时，应用层把用户时间转为 UTC，然后以标准格式（如 '2024-01-01 00:00:00'）写入 DATETIME 字段
        - 读取时：数据库原样返回 '2024-01-01 00:00:00'，应用层知道“这是 UTC 时间”，再转回用户本地时间
        - 可读性强：如果你直接去数据库查表（SELECT *），你看到的 2024-01-01 00:00:00 是人能看懂的格式，方便排查问题
    - BIGINT + UTC：极致的“性能方案”
        - 存入时：应用层计算时间戳数值（如 1704067200000），直接存入 BIGINT
        - 读取时：拿到数字，前端 JS 直接 new Date(1704067200000) 就能自动根据浏览器时区显示
        - 人眼不可读：如果你直接去数据库查表（SELECT *），看到 1704067200 是一脸懵的，必须写函数转换才能看懂

```sql
select 
  -- 1. 先除以1000转为秒（MySQL只认秒级）
  -- 2. from_unixtime 转为日期格式
  -- 3. convert_tz 从 UTC 转到 北京时间
  -- convert_tz 的第二个参数（源时区）必须明确
  -- 如果你的 from_unixtime 默认输出的是 UTC 时间，这里就填 'UTC'；如果数据库本身配置成了东八区，这里就要填 '+08:00'
  convert_tz(from_unixtime(create_time_ms / 1000), 'UTC', 'Asia/Shanghai') AS beijing_time
from tableName;
```

#### MySQL用户操作

```sql
-- 创建用户账号并指定密码 
-- identified by指定的口令为纯文本，MySQL将在保存到user表之前对其进行加密
create user dbadmin identified by 'dbadmin123!!';

-- 更改口令，新口令必须传递到Password()函数进行加密
set password for dbadmin = Password('dbadmin123');

-- 在不指定用户名时，set password更新当前登录用户的口令
set password = Password('dbadmin123');

-- 修改账户名1
rename user old_username to new_username;

-- 修改账户名2
update user set user='new_username' where user='old_username';
flush privileges;

-- 删除用户账号和所有相关的账号权限
drop user dbadmin;

-- 显示当前用户
select user();

-- mysql数据库有一个名为user的表，它包含所有用户账号
use mysql;

-- user表的user列存储用户登录名
select user from user;
```

#### 权限操作

```sql
-- 查看赋予用户账号的权限
-- USAGE表示根本没有权限
-- 用户定义为user@host MySQL的权限用用户名和主机名结合定义。
-- 如果不指定主机名，则使用默认的主机名%（授予用户访问权限而不管主机名）。
show grants for dbadmin;

-- 设置访问权限
-- GRANT和REVOKE可在几个层次上控制访问权限：
-- 整个服务器，使用GRANT ALL和REVOKE ALL；
-- 整个数据库，使用ON database.*；
-- 特定的表，使用ON database.table；
-- 特定的列
-- 特定的存储过程
-- GRANT要求你至少给出以下信息：要授予的权限；被授予访问权限的数据库或表；用户名。
grant select,create,alter,insert,update,delete,drop on database_name.* to dbadmin;

-- GRANT的反操作为REVOKE，用它来撤销特定的权限
-- 被撤销的访问权限必须存在，否则会出错
revoke select on database_name.* from dbadmin;
```

#### 数据库操作

```sql
-- 创建数据库
create database [if not exists] `database_name` character set utf8mb4 collate utf8mb4_unicode_ci;

-- 修改数据库
alter database `database_name` [character set charset_name] [collate collation_name]

-- 删除数据库，同时删除该数据库相关的目录及其目录内容
drop database [if exists] 数据库名

-- 显示可用的数据库列表
show databases [like 'han%'];

-- 查看当前（选择的）数据库
select database();

-- 显示创建数据库的sql语句
show create database 数据库名称;

-- 选择（打开）数据库
use 数据库名称;
```

#### 表操作

##### 建表

建表：字符集utf8mb4 ，排序规则utf8mb4_unicode_ci, InnoDB引擎，增加事务处理，MyISAM引擎，高效处理插入和查询

```sql
create table account (
    id int unsigned not null auto_increment,
    name nvarchar(16) not null unique,
    password char(32) not null,
    salt char(128) not null,
    latestLoginCity nvarchar(32) not null default '',
    latestLoginIp char(16) not null default '',
    latestLoginTime datetime not null default now(),
    latestLogOutTime datetime not null default now(),
    primary key (id)
    # auto_increment=1 设置自增初始值
)auto_increment=1,character set = utf8mb4, collate = utf8mb4_unicode_ci, engine=InnoDB;
```

##### 改表

```sql
-- 添加字段
alter table account add age int(3);

-- 修改字段，id自增到 int unsigned 最大值后不再允许插入
alter table account change column id id int unsigned auto_increment;

-- 删除字段
alter table account drop column age;

-- 添加主键
alter table account add primary key (id);

-- 删除主键
alter table account drop primary key;
```

##### 删表

```sql
-- 如果表存在则删除
drop table if exists payment;
```

##### 清空表数据

```sql
-- 删除指定数据
delete from account where name='Tom';

-- 清空表数据，产生binlog日志，可以回滚
delete from account;
-- 清空表数据，不产生日志，不能回滚，会把表的自增值重置和索引恢复到初始大小等
truncate table account;
```

#### CRUD

##### 插入

```sql
-- 插入一行
insert into account (name, password)  values ('Jack', 'Jack123');
-- 插入多行
insert into account (name, password)  values ('Jack', 'Jack123'), ('Tom', 'Tom123');

-- 插入或替换，插入一条新记录，但如果记录已经存在，就先删除原记录，再插入新记录
replace into students (id, class_id, name, gender, score) values (1, 1, '小明', 'f', 99);

-- 插入或更新，插入一条新记录，但如果记录已经存在，就更新该记录，更新的字段由update指定
insert into students (id, class_id, name, gender, score) values (1, 1, '小明', 'f', 99) on duplicate key update name='小明', gender='f', score=99;

-- 插入或忽略，插入一条新记录，但如果记录已经存在，就啥事也不干直接忽略
insert ignore into students (id, class_id, name, gender, score) values (1, 1, '小明', 'f', 99);
```

##### 查询

```sql
-- 强制使用指定索引，很多时候，数据库系统的查询优化器并不一定总是能使用最优索引。
-- 如果我们知道如何选择索引，可以使用FORCE INDEX强制查询使用指定的索引
-- 指定索引的前提是索引idx_class_id必须存在
SELECT * FROM students FORCE INDEX (idx_class_id) WHERE class_id = 1 ORDER BY id DESC;

-- 分组过滤排序查询
select name, count(*) as repeat_name
from account
where name is not null
group by name
having count(*) > 1;

-- 子查询：将一个select的结果作为另一个sql语句的数据来源或判断条件
-- 子查询要放在()内
-- 子查询常用在from或where后边
-- from后边的子查询相当于一张临时表，需要使用as起一个表名
select t1.name 
from (select name from account where age > 18) as t1
where name in (
     select name from account where gender = 1
)

-- join连接查询
-- sql先根据on生成一张临时表，然后再根据where对临时表进行筛选
-- inner join：内连接（默认连接方式），只有当两个表都存在满足条件的记录时才会返回行
-- left [outer] join： 左(外)连接，返回左表中的所有行，即使右表中没有满足条件的行也是如此
-- right [outer] join：右(外)连接，返回右表中的所有行，即使左表中没有满足条件的行也是如此
-- full [outer] join ：全(外)连接，只要其中有一个表存在满足条件的记录，就返回行
select account.*,user.* 
from account
inner join user
on account.name=user.name 

-- 如果两张表的关联字段名相同，也可以使用using来代替on
select account.*,user.* 
from account
inner join user
using(name)

-- union组合查询
-- 所有查询的列数和列顺序必须相同
-- 所有查询中涉及表的列的数据类型必须相同或兼容
-- union结果集中的列名总是等于union中第一个select语句中的列名
-- union去重，union all不去重
select name from account
union all
select name from user
```

#### 其他操作

```sql
-- 查看数据库端口
show global variables like 'port';

--显示可用数据库列表
show databases;

--选择数据库
use 数据库名;

--显示数据库的表
show tables;

--显示表的字段1
show columns from account;

--显示表的字段1
describe account;

-- 拼接字段
select concat(id,'(',name,')') from account order by id;

-- 显示数据库版本
select version();

-- 显示当前时间
select now();

-- 显示数据库支持的存储引擎
show engines;

-- 查看MySQL当前默认的存储引擎
show variables like '%storage_engine%';

-- 查看数据库中某个表使用的存储引擎
show table status from database_name where name='account';

-- 查看mysql默认隔离级别
select @@transaction_isolation;

-- 显示当前选择的数据库内可用表的列表
show tables;

-- 显示创建表的sql语句
show create table payment;

-- 显示表的字段信息
show columns from account;
describe account;

-- 显示广泛的服务器状态信息
show status;

-- 显示创建表的sql语句
show create table account;

-- 显示服务器错误消息
show errors;

-- 显示服务器警告消息
show warnings;

-- 模糊查询
select * from account where name like "%" 'ku' "%";

-- 限制结果
-- 分页时一定要配合 order by 使用
-- limit 偏移量，返回行数 （limit只有一个参数时表示的是返回行数，此时偏移量默认为零）
select * from employee order by id asc limit 0, 20;

-- MySQL 5支持 limit 的另一种替代语法：limit size offset rowindex
select * from employee order by id asc limit 20 offset 0;

-- 正则表达式
-- MySQL中的正则表达式匹配（自版本3.23.4后）大写和小写都匹配
-- 可添加 binary 关键字区分大小写

-- 检索列name包含文本'R'的所有行
select * from account where name regexp binary 'R';

-- '.' 表示匹配任意一个字符
select * from account where password regexp '.13';

-- '|' 表示or匹配
select * from account where id regexp '[1|2]';

-- '[]' 表示匹配几个字符之一，'-' 定义一个范围
select * from account where password regexp '[0-9]';

-- 为了匹配特殊字符，必须用'\\特殊字符'

-- 拼接字段,列值为id(name)
select concat(id,'(',name,')') as aaa from account order by id;

-- 连接表
-- 内部联结（等值联结），inner join， on 为连接条件（同where）
select account.*, employee.sex, employee.age from account inner join employee on account.id = employee.id;

-- 自联结
-- 自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。
-- 应该试一下自联结和子查询两种方法，以确定哪一种的性能更好。
-- 子查询 select * from employee where sex = (select sex from employee where sex='1' limit 0, 1);
select e1.* from employee as e1, employee as e2 where e1.id = e2.id and e2.sex='1';

-- 自然联结，自然联结排除多次出现的列，使每个列只返回一次。
-- 一般是通过对某张表使用通配符（SELECT *），对所有其他表的列使用明确的子集来完成的。
-- 内部联结基本都是自然联结
select account.*, employee.sex, employee.age from account,employee where account.id = employee.id;

-- 外部联结,包含了那些在相关表中没有关联行的行。
-- 在使用 outer join 时，必须使用 right 或 left 关键字指定包括其所有行的表
-- right 指出的是包括 outer join 右边的表所有行，left 指出的是包括 outer join 左边的表所有行。
select account.*, employee.sex, employee.age from account left outer join employee on account.id = employee.id;

-- group by 分组数据
-- 分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。
-- group by 子句必须出现在 where 子句之后，order by 子句之前
select sex, count(*) as number from employee group by sex;

-- coalesce(a, b, ...)：如果a!=null则返回a，如果a==null则返回b，...，如果都为null则返回null
select coalesce(2, 'b') as someColumnName; 

-- with rollup 可以得到每个分组以及每个分组汇总后的值
select coalesce(sex, 'sum') as category, sex, count(*) from employee group by sex with rollup;

-- having 过滤分组，（用法与where类似，区别是 having 过滤分组，where 过滤行，where 在数据分组前进行过滤，having 在数据分组后进行过滤。）
select sex, count(*) as number from employee group by sex having number >= 2;

-- coalesce，where，group by，with rollup，having，order by 一起使用
select coalesce(sex, 'sum') as category, count(*) as number from employee where age>=18 group by sex  with rollup having number >= 3 order by sex asc;

-- 测试
select 3*2;
select rtrim(' abc ') as rtrim;
select ltrim(' abc ');
select trim(' abc ');
-- 返回当前日期时间
select now();
select upper('abc');
select lower('ABC');
select length('abc');
select curdate();
select curtime();
select year(curdate());
select month(curdate());
select abs(-3);
select pi();

-- 聚集函数(汇总数据)
-- COUNT(*)对表中行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值
select count(*) from account;
select sum(age) from employee;
select distinct id from account;

-- 普通查询
select * from account;

-- DELETE语句从表中删除行，甚至是删除表中所有行
delete from account;

-- TRUNCATE实际是删除原来的表并重新创建一个表，而不是逐行删除表中的数据
truncate account;

-- 重命名表
rename table acc to account;

create table if not exists `user` (
    id bigint not null auto_increment comment 'id',
    name varchar(32) not null comment 'name',
    password nvarchar(16) not null,
    primary key(id)
)
engine = innodb
default character set = utf8mb4
collate = utf8mb4_unicode_ci
comment ='用户表';

-- 返回最后一个AUTO_INCREMENT值
select last_insert_id();


select * from account;

-- 刷新表，清除缓存，同时防止备份时候有新数据写入
flush tables with read lock;
unlock tables;

-- 查看数据库端口
show global variables like 'port';


-- 远程访问数据库
-- 修改host字段的值为需要远程连接数据库的主机ip地址或者直接修改成%
-- host的值为'%'表示所有主机可以通过该用户访问数据库
update user set host='%' where user='dbadmin';
flush privileges;

create table user(
 id int unsigned auto_increment primary key,

    -- gmt 表示格林威治时间，北京是GMT+8
    -- gmt_create表示主动式创建
    gmt_create datetime not null,
    
    -- gmt_modified 过去分词表示被动式更新
    gmt_modified datetime not null default now(),
    
    name varchar(16) not null,
    
    -- tinyint unsigned范围：0-255
    -- 括号中的数字，不表示存储长度（范围），表示的是显示宽度
    -- tinyint(1)  和 tinyint(3) 没什么区别，占用字节都是一位，
    -- 对存储的值123来说，tinyint(1) 只显示一位数字，tinyint(3) 显示三位数字
    -- tinyint(3) zerofill ，当插入的数据少于3位的时候，如存储值为1，则显示001
    -- tinyint() 显示长度设置后期版本会舍弃
    is_male tinyint unsigned  not null,
    age tinyint unsigned  not null,
    
    -- 强制小数类型为 decimal
    -- decimal(n, m)表示数值中共有n位数，其中整数n-m位，小数m位
    -- decimal(n, m) unsigned 无符号设置后期版本会舍弃
    height decimal(5, 2)  not null,
    weight decimal(5, 2)  not null,

    -- 余额
    balance decimal(12, 2)  not null
    
    -- auto_increment=1 设置自增初始值
)auto_increment=1,CHARACTER SET = utf8mb4, COLLATE = utf8mb4_unicode_ci, ENGINE=MyISAM;

select * from user;
```

#### 索引

##### 创建索引

```sql
-- 创建普通索引
create index index_name on `table_name` (`column`);

-- 创建唯一索引
create unique index index_name on `table_name` (`column`);

-- 创建组合索引
create index idx_columnName1_columnName2 on `table_name` (columnName1, columnName2)
```

##### 添加索引

```sql
-- 添加主键索引
alter table `table_name` add primary key (`column`)

-- 添加唯一索引
alter table `table_name` add unique (`column`)

-- 添加普通索引
alter table `table_name` add index index_name (`column`)

-- 添加多列索引
alter table `table_name` add index index_name ( `column1`, `column2`, `column3` )

-- 添加全文索引
alter table `table_name` add fulltext (`column`)
```

##### 删除索引

```sql
alter table `table_name` drop index index_name;
```

- 查询时关联列类型不一致会自动进行数据类型隐式转换，会造成列上的索引失效
- 不同的字符集进行比较前需要进行转换会造成索引失效
- 更新十分频繁、区分度不高（如性别）的字段不适合建索引
- 建立组合索引，区分度高（重复度低）的放左边，能更加有效地过滤数据
- 字段长度小的列放在联合索引的最左侧（因为字段长度越小，一页能存储的数据量越大，IO 性能也就越好）
- 使用最频繁的列放到联合索引的左侧（这样可以比较少的建立一些索引）
- 在定义联合索引时，如果 a 列要用到范围查找的话，就要把 a 列放到联合索引的右侧，使用 left join 或 not exists 来优化 not in 操作，因为 not in 也通常会使用索引失效

- 查询字段的区分度

```sql
-- 越接近1，区分度越高
select count(distinct columnName) / count(*) as columnName_rate from tableName
```

- 回表：通过辅助索引拿到主键后，再回到主键索引查询的过程，需要尽量减少回表次数，提高查询效率
- 覆盖索引（包含了所有查询字段：where,select,order by,group by 包含的字段的索引）避免回表
- 给有大量数据的表新建索引：新建一张表+建索引+导入旧表数据+废弃旧表

#### 事务

##### 事务隔离级别

读未提交、读已提交、可重复读、可串行化

- 脏读：一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读
- 不可重复读：在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致
- 幻读：在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了

##### 事务处理

- 事务处理用来管理insert、update和delete语句，不能回退select、create或drop操作
- MySQL默认是隐式提交
- 当commit或rollback语句执行后，事务会自动关闭（MySQL变回隐式提交）

```sql
select * from account;

-- 开始事务
start transaction;
delete from account;
select * from account;

-- rollback 命令用来回退（撤销）MySQL语句
rollback;
select * from account;

-- 在事务处理块中，提交不会隐含地进行。为进行明确的提交，使用 COMMIT 语句
select * from account;
start transaction;
delete from account where id=4294967295;
select * from account;
commit;
select * from account;

-- 使用保留点
-- 每个保留点都取标识它的唯一名字，以便在回退时，MySQL知道要回退到何处
-- rollback to 用于回滚到指定的保留点；如果没有设置保留点，则回退到start transaction语句处
-- 保留点在事务处理完成（执行一条rollback或commit）后自动释放
-- 也可以用RELEASESAVEPOINT明确地释放保留点
savepoint point1;

-- 回退到保留点
rollback to point1;

-- 释放保留点
release savepoint point1;

-- 更改默认的提交行为
-- autocommit标志是针对每个连接而不是服务器的
-- 设置autocommit为0（假）指示MySQL不自动提交更改
set autocommit = 0;
start transaction;
select * from account;
insert into account (name,password) values('raidon','kien');
insert into account (name,password) values('nintendo','nn134');
rollback;
```

#### 并发事务的控制方式

MySQL 中并发事务的控制方式无非就两种：锁 和 MVCC

锁可以看作是悲观控制的模式

多版本并发控制（MVCC，Multiversion concurrency control）可以看作是乐观控制的模式

##### 锁

锁控制方式下会通过锁来显式控制共享资源而不是通过调度手段，MySQL 中主要是通过`读写锁`来实现并发控制

- InnoDB的行锁是通过锁住索引来实现的

- 索引命中，加的是行锁

```sql
-- 假设id是主键
update user set user_name = #{userName} where id=#{id}
```

- 索引没命中，加的是表锁

```sql
-- 假设user_age没有加索引
update user set user_name = #{userName} where user_age=#{userAge}
```

###### 共享锁和排它锁

共享锁（S 锁）：又称读锁，事务在读取记录的时候获取共享锁，允许多个事务同时获取（锁兼容）

排他锁（X 锁）：又称写锁/独占锁，事务在修改记录的时候获取排他锁，不允许多个事务同时获取。如果一个记录已经被加了排他锁，那其他事务不能再对这条记录加任何类型的锁

```sql
-- 共享锁
select ... lock in share mode;

-- 排他锁
select ... for update;
```

##### 意向锁（Intention Lock）

意向锁用来快速判断是否可以对某个表使用表锁

意向锁是由数据引擎自己维护的，用户无法手动操作意向锁，在为数据行加共享/排他锁之前，InnoDB 会先获取该数据行所在在数据表的对应意向锁

意向共享锁（Intention Shared Lock，IS 锁）：事务有意向对表中的某些记录加共享锁（S 锁），加共享锁前必须先取得该表的 IS 锁

意向排他锁（Intention Exclusive Lock，IX 锁）：事务有意向对表中的某些记录加排他锁（X 锁），加排他锁之前必须先取得该表的 IX 锁

##### InnoDB的行锁

记录锁（Record Lock）：属于单个行记录上的锁

间隙锁（Gap Lock）：锁定一个范围，不包括记录本身

临键锁（Next-Key Lock）：Record Lock+Gap Lock，锁定一个范围，包含记录本身，主要目的是为了解决幻读问题。记录锁只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁

在 InnoDB 默认的隔离级别 REPEATABLE-READ 下，行锁默认使用的是 Next-Key Lock

但是，如果操作的索引是唯一索引或主键，InnoDB 会对 Next-Key Lock 进行优化，将其降级为 Record Lock，即仅锁住索引本身，而不是范围

##### MVCC

MVCC 是多版本并发控制方法，即对一份数据会存储多个版本，通过事务的可见性来保证事务能看到自己应该看到的版本

通常会有一个全局的版本分配器来为每一行数据设置版本号，版本号是唯一的

MVCC 在 MySQL 中实现所依赖的手段主要是: 隐藏字段、read view、undo log

undo log : 用于记录某行数据的多个版本的数据

read view 和 隐藏字段 : 用来判断当前版本数据的可见性

##### 当前读和快照读

快照读（一致性非锁定读）就是单纯的 SELECT 语句

快照读的情况下，如果读取的记录正在执行 UPDATE/DELETE 操作，读取操作不会因此去等待记录上 X 锁的释放，而是会去读取行的一个快照

只有在事务隔离级别读已提交和可重读下，InnoDB 才会使用一致性非锁定读：

在 读已提交 级别下，对于快照数据，一致性非锁定读总是读取被锁定行的最新一份快照数据

在 可重读 级别下，对于快照数据，一致性非锁定读总是读取本事务开始时的行数据版本

快照读比较适合对于数据一致性要求不是特别高且追求极致性能的业务场景

当前读 （一致性锁定读）就是给行记录加 X 锁或 S 锁，常见sql如下

```sql
-- 对读的记录加一个X锁
select ... for update

-- 对读的记录加一个S锁
select...lock in share mode

-- 对读的记录加一个S锁
select...for share

-- 对修改的记录加一个X锁
insert ...
update ...
delete ...
```

#### 删除重复数据

```sql
-- 查看重复数量1
select columnName,count(1) from tableName group by columnName having count(1) > 1

-- 查看重复数量2
select columnName,count(columnName) from tableName group by columnName having count(columnName) > 1

-- 查看重复数据
select * from tableName where columnName in (
    select columnName from tableName group by columnName having count(1) > 1
)

-- 重复数据全部删除
delete from tableName where columnName in (
    select t.columnName from (
        select columnName from tableName group by columnName having count(1) > 1
    ) t
)

-- 重复数据保留一条，其它删了
delete from tableName where id not in (
    select t.id from (
        select min(id) as id from tableName group by columnName
    ) t
)

```

#### sql优化

- where从句中禁止对列进行函数转换和计算，会导致索引失效
- 没有重复值时用union all

##### SQL的执行计划

执行计划是指一条 SQL 语句在经过 MySQL 查询优化器的优化会后，具体的执行方式

可以使用 EXPLAIN 命令来分析 SQL 的 执行计划

EXPLAIN 并不会真的去执行相关的语句，而是通过 查询优化器 对语句进行分析，找出最优的查询方案，并显示对应的信息。

EXPLAIN 适用于 SELECT, DELETE, INSERT, REPLACE, 和 UPDATE语句，我们一般分析 SELECT 查询较多

```sql
explain select语句
```

- explain输出的各个列的含义

|列名|描述|
|:-|:-|
|id|select 查询的序列标识符|
|select_type|select 关键字对应的查询类型|
|table|用到的表名|
|partitions|匹配的分区，对于未分区的表，值为 NULL|
|type|表的访问方法|
|possible_keys|可能用到的索引|
|key|实际用到的索引|
|key_len|所选索引的长度|
|ref|当使用索引等值查询时，与索引作比较的列或常量|
|rows|预计要读取的行数|
|filtered|按表条件过滤后，留存的记录数的百分比|
|Extra|附加信息|

#### 存储过程

```sql
-- 如果使用的是mysql命令行实用进程，需要临时更改命令行实用进程的语句分隔符
-- 除\符号外，任何字符都可以用作语句分隔符
delimiter //

-- 创建存储过程
create procedure find()
begin
    select * from  employee;
end //

-- 恢复默认的语句分隔符
delimiter ;

-- 使用存储过程
call find();

-- 删除存储过程
drop procedure if exists find;

delimiter //
-- 存储过程将保存结果的3个变量名，所有MySQL变量都必须以@开始
-- IN（传递给存储过程）、OUT（从存储过程传出）
create procedure finduser(in userId int unsigned, out userName  varchar(8), out password varchar(16))
begin
    select name into userName from account where id= userId; 
    select account.password into password from account where id= userId; 
end //
delimiter ;

drop procedure if exists finduser;

call finduser(2, @name, @password);

select @name,@password;
```

#### MySQL数据库备份

##### 逻辑备份

备份的是SQL语句，效率较低，用于中小型企业

```sh
# 备份到sql文档，表名为空则复制整个数据库
mysqldump -u root -p -P 3306 handle account employee spidata --single-transaction > d:/handle_backup.sql

# 从sql文档恢复
mysql -u root -p -P 3306 handle < d:/handle_backup.sql
```

#### 找回mysql的root密码

```sh
# 适用mysql5.7
# 追加一行：skip-grant-tables
vim /etc/my.cnf

# 重启
systemctl restart mysqld

# 登录，输入空密码就行
mysql -u root -p

# 选择mysql数据库
use mysql;

# 设置新密码
update user set authentication_string=password("具体密码") where user = 'root';

# 刷新权限
flush privileges;

# 退出
exit

# 删除或注释：skip-grant-tables
vim /etc/my.cnf

# 重启
systemctl restart mysqld
```

#### 读写分离

部署多台数据库，选择其中的一台作为主数据库，其他的一台或者多台作为从数据库。

保证主数据库和从数据库之间的数据是实时同步的，这个过程也就是我们常说的主从复制。

系统将写请求交给主数据库处理，读请求交给从数据库处理。

- 实现方式有两种
    - 代理方式：如使用MySQL Router
    - 组件方式（推荐，用得最多）：如使用ShardingSphere-JDBC

#### 主从复制原理

MySQL binlog(binary log 即二进制日志文件) 主要记录了 MySQL 数据库中数据的所有变化(数据库执行的所有 DDL 和 DML 语句)

因此，根据主库的 MySQL binlog 日志就能够将主库的数据同步到从库中

当然，除了主从复制之外，binlog 还能帮助我们实现数据恢复

- 主从复制过程
    - 主库将数据库中数据的变化写入到 binlog

    - 从库连接主库

    - 从库会创建一个 I/O 线程向主库请求更新的 binlog

    - 主库会创建一个 binlog dump 线程来发送 binlog ，从库中的 I/O 线程负责接收

    - 从库的 I/O 线程将接收的 binlog 写入到 relay log 中

    - 从库的 SQL 线程读取 relay log 同步数据到本地（也就是再执行一遍 SQL ）

MySQL 主从复制是依赖于 binlog 。另外，常见的一些同步 MySQL 数据到其他数据源的工具（比如 canal）的底层一般也是依赖 binlog

Redis 也是通过主从复制实现的读写分离

#### 如何避免主从延迟

- 对于极少数必须强一致的业务，强制将读请求路由到主库处理

```java
// ShardingSphere-JDBC 强制读主库
HintManager hintManager = HintManager.getInstance();
hintManager.setMasterRouteOnly();
// 继续JDBC操作
```

- 延迟读取，在完成写请求之后，避免立即进行请求操作。比如你支付成功之后，跳转到一个支付成功的页面，当你点击返回之后才返回自己的账户。

#### 分库分表

读写分离主要应对的是数据库读并发，而分库分表应对的是数据库存储压力

- 分库：将数据库中的数据分散到不同的数据库上，可以垂直分库，也可以水平分库
    - 垂直分库：把单一数据库按照业务进行划分，不同的业务使用不同的数据库，进而将一个数据库的压力分摊到多个数据库
    - 水平分库：按“行”将数据拆分到不同的数据库实例中（如订单库1，订单库2，...）
- 分表：对单表的数据进行拆分，可以是垂直拆分，也可以是水平拆分
    - 垂直分表：对数据表列的拆分，把一张列比较多的表拆分为多张表
    - 水平分表：对数据表行的拆分，把一张行比较多的表拆分为多张表（如订单表1，订单表2，...)，可以解决单一表数据量过大的问题

水平拆分只能解决单表数据量大的问题，为了提升性能，我们通常会选择将拆分后的多张表放在不同的数据库中。也就是说，水平分表通常和水平分库同时出现。

### SQL Server

```sql
-- 指定数据库
use HR;

-- 查询表所有信息
select * from person;

-- 创建新表
create table person(
    id nvarchar(20) primary key,
    name nvarchar(4),
)

-- 加载指定数据库
exec sp_attach_db 'db1','D:\backup\db2.mdf','D:\backup\db2_log.ldf'

-- 备份数据库后缀.bak
backup database handle to disk='D:\handle.bak'

-- 恢复数据库
use master

restore database handle from disk ='D:\handle.bak' with replace,norecovery

restore log handle from disk ='D:\handle_log.bak' with recovery,stopat='2020-03-30 07:00:43'

restore database handle with recovery

-- 删除表
drop  table employee

-- 修改表，添加字段，带默认值
alter table person add  birthday datetime not null default '2019-8-8' 

-- 修改表，修改字段
alter table person alter column name nvarchar(4) not null 

-- 修改表，删除字段
alter table person drop column birthday 

-- 修改表，设置主键
alter table person add constraint pk_主键 primary key (id) 

-- 修改表，添加约束
alter table person add constraint ck_性别 check (性别='男' or 性别='女') 

-- 修改表，禁用约束
alter table person nocheck constraint ck_性别 

-- 修改表，删除约束
alter table person drop ck_性别 

-- 查询表的所有字段名
select name from syscolumns Where ID=OBJECT_ID('person') 

-- distinct 去重查询
select distinct 性别 from person 

-- union 合并结果集，上下
select * from person where 姓名='李白' union select * from person where 姓名='杜甫' 

-- in 关键字，in 中数据量不能超过1k条
select * from person where 姓名 in('杜甫','杜牧')  

-- like 关键字
select * from person where 姓名 like '%李%' 

-- 结果添加到新表temp
select * into person from person where 姓名='李白' union select * from person where 姓名='杜福' 

-- 创建空表，结构跟person一样，除了约束和标识等
select * into person from person where 0=1 

-- 多字段排序
select * from person order by age desc,name desc 

--查询重复记录
select * from person where id in (
    select id from person group by id having count(id)>1
)

-- 插入记录
insert into person select * from table2 

-- 删除记录
delete from person where age=60

-- 删除 person 中，id 在 other_tb.id中的记录
delete from person where exists(
    select id from other_tb where other_tb.id=person.id
)

-- 删除other_tb中，id不在person.id中的记录
delete from other_tb where id not in (
    select id from person
)

-- 表格记录全部删除
delete person 

-- update语句
update person set sex='男' where name='李白'
update person set person.phone_number= boss.phone_number from person,boss where person.sex='' and person.id=boss.id

-- 本地临时表：命名以#开头的表，创建它的用户可以使用，用户断开连接后SQL Server自动删除，C# connection打开然后关闭后就会自动删除
-- 全局临时表：命名以##开头的表，任何连接用户使用，所有使用该表的用户都断开连接后SQL Server自动删除

select * into #person from person

-- 使用函数
select min(出生日期),max(出生日期) from person  

select * from person where 出生日期 between '1994/1/1' and '1995/12/12' --日期类查询

select * from person where 产品处 is null --查询空值


select b.name,c.name from sysobjects a,syscolumns b,systypes c where
a.id=b.id and a.name='person' and a.xtype='u' and b.xtype=c.xtype  --字段及类型

-- 数字加单引号
select 工号 as '1' from person 

--批量数据（如id)从数据库中捞匹配数据：
--批量数据放入一个临时表，然后join：
select person.* from person join person on person. id=person.id

-- 创建新表
create table person(
    id nvarchar(20) primary key
)

-- 查询字段内容最大值长度
select max(len(name)) from person
select name from person where len(name)=4
```

varchar(4) ：可以存放2个汉字或四个英文字符
nvarchar(4) ：可以存4个汉字或4个其它字符

ldf 文档太大处理方法（先备份数据库）：
1.分离数据库文档为.mdf和.ldf
2.数据库名右键-tasks-detach-drop connection
3.删除数据库的.ldf文档或将其改名
4.databases右键-attached-添加.mdf文档，然后删除not found 的ldf，确定
5.右键数据库，属性，文档，重新设置ldf大小限制

数据库导数据OLEDB.12.0未注册解决方法：安装AccessDatabaseEngine2007

### Oracle

```sql
-- 修改字段名
alter table student rename column password to pwd;

-- 修改字段数据类型
alter table student modify password varchar(16) not null;
```

#### 函数

- upper('value')

将参数值转成大写后返回

- lower('VALUE')

将参数值转成小写后返回

- to_char(create_time,'yyyy-MM-dd HH24:mi:ss')

将字段值转为指定格式字符串

- to_date('2022-02-02 13:14:20','yyyy-MM-dd HH24:mi:ss')

将参数值转为指定格式的日期

- nvl(表达式1，表达式2)

把一个空值（null）转换成一个实际的值。如果表达式1为空值，返回表达式2的值，否则返回表达式1的值。表达式1和表达式2的数据类型必须为同一个类型。

- nvl2(表达式1，表达式2，表达式3)

如果表达式1为空，返回值为表达式3的值。如果表达式1不为空，返回值为表达式2的值

- round(number, 小数位数)

四舍五入

- replace(source, 被替换的字符, 替换成什么字符)

#### 分页查询

```sql
select * from (
    select rownum rowNumber, total.* from (
        select ... order by ...
    ) total
    where rownum < pageNumber * pageSize
)
where rowNumber > (pageNumber - 1) * pageSize
```

#### sql

```sql
--查询当前用户下是否有某个表，表名要大写
select table_name from user_tables where table_name=upper('temp_tb')

--复制表结构及数据，不创建索引
create table temp_tb as select * from person

--替换字段中的字符值
update TEMP_G6011119_20191205 set id=replace(id,'G6',' ')
update temp_G6011119_20191224 set BIRTH_DATE = to_date(replace(to_char(BIRTH_DATE,'yyyy/MM/dd'),'20','11'),'yyyy/MM/dd') where to_char(BIRTH_DATE,'yyyy/MM/dd') like '%1994%'

--修改字段
alter table temp_G6011119_20191204 modify(BIRTH_DATE NVARCHAR2(16))  

--日期类型查询与修改

-- dual是存在于oracle实实在在的表,常用于select中没有目标表的查询
-- trunc(sysdate) 当前日期0时0分0秒
select trunc(sysdate) from dual;
select sysdate from dual;
select to_date(sysdate,'yyyy-MM-dd HH24:mi:ss') from dual;
select to_char(sysdate,'yyyy-MM-dd HH24:mi:ss') from dual;

--当前时间N秒前的数据
select * from cpesmt.jt_reflow_data where work_time between sysdate+3/(24*60*60) and sysdate order by work_time asc

select * from  temp_G6011119_20191204 where to_char(BIRTH_DATE,'yyyy/MM/dd') like '%1994%'

update  temp_G6011119_20191204 set BIRTH_DATE=to_date('2000/10/10','yyyy/MM/dd') where to_char(BIRTH_DATE,'yyyy/MM/dd') like '%1994%'


--创建表注释或者为表重新注释
comment on table temptb is '临时表，会自动删除，请管理员不要手动删除'

--查询表注释
select table_name,comments from user_tab_comments where table_name=upper('temptb')

--删除表
drop table temp_tb

--查询字段及字段类型
select column_name,data_type  from all_tab_cols  where table_name=upper('temp_tb')
--查询表的所有字段名，表名要大写
select column_name from user_tab_columns where table_name=upper('temp_tb')

--查询字段值长度最长的记录的长度
select max(length(CONTROL_TYPE_NAME)) from  EMP_INFO

--创建会话级临时表,当用户退出会话结束时（connection打开后关闭算会话结束），Oracle自动清除临时表中数据，但保留表结构
create global temporary table temp_tb on commit preserve rows as select * from person

-- 声明方法
declare
    i number(2) := 10;
    s varchar2(8) := '张三';
    uname Account.userName%type;
    urow Account%rowtype;
begin
    dbms_output.put_line(i);
    dbms_output.put_line(s);
    select userName into uname from Account where uid = 1;
    dbms_output.put_line(uname);
    select * into urow from Account where uid = 1;
    dbms_output.put_line(urow.uid || urow.userName);
end;

-- if判断
declare
    i number(2) := &i;
begin
    if i > 0 then
        dbms_output.put_line('正数');
    elsif i = 0 then
        dbms_output.put_line('0')
    else
        dbms_output.put_line('负数')
    end if;
end;
```

### Redis

ACID：（Atomicity）原子性、（Consistency）一致性、（Isolation）独立性、（Durability）持久性
CAP：（Consistency）强一致性、（Availability）可用性、（Partition tolerance）分区容错性
CA：传统Oracle数据库
AP：大多数网站架构的选择
CP：Redis、Mongodb
BASE：基本可用（Basically Available）、软状态（Soft state）、最终一致（Eventually consistent）

#### Redis安装

key命名：`表名:列名:主键名:主键值`

##### 修改配置

- redis7.4配置文件：[redis.conf](/file/redis/redis.conf)，其它版本可以去源码仓库那里下载

```conf
# "bind 127.0.0.1 -::1"表示只能本机访问
# 监听所有网卡这样写：bind 0.0.0.0，它会监听包括127.0.0.1的所有网卡
# 远程连接需要指定本机（某个网卡）的ip地址
bind 127.0.0.1 10.0.2.15

# 开启保护模式，默认
protected-mode yes

# 指定redis监听接收连接请求的端口，默认
port 6379

# 客户端空闲了没有给redis发送消息命令也不断开连接，默认
timeout 0

# 60秒检查一次客户端是否存活，默认300
tcp-keepalive 60

# 守护进程（daemon）方式后台运行，不占用当前终端
# redis采用的是单进程多线程的模式
# 当daemonize设置成yes时，代表开启守护进程模式
# 在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程
# 当daemonize选项设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出
daemonize yes

# daemonize设置为yes时，redis将进程id写入到pidfile指定的文件中，默认
pidfile /var/run/redis_6379.pid

# 日志级别，默认
loglevel notice

# 日志文件
# 默认logfile ""，表示打印日志到标准输出，同时如果开启了daemonize, 日志将发送到/dev/null
# 如果是主机部署redis，这样指定：logfile "/var/log/redis.log"
# 如果是docker部署redis，这样指定：logfile ""，然后docker会自动收集日志
logfile ""

# 是否把日志输出到系统日志，默认注释
#syslog-enabled no

# 指定redis在系统日志里面的日志标志，默认注释
#syslog-ident redis

# 指定系统日志设备，值必须为 USER 或 LOCAL0-LOCAL7之一，默认注释
# syslog-facility local0

# redis默认有16个数据库
databases 16

# 3600 秒内至少1次写操作 → 触发 RDB
save 3600 1

# 300 秒内至少 100 次写操作 → 触发 RDB
save 300 100

# 60 秒内至少 10000 次写操作 → 触发 RDB
save 60 10000

# 定义快照文件名，默认
dbfilename dump.rdb

# 指定登录密码
requirepass 具体密码

# 开启AOF持久化
appendonly yes

# 定义只追加文件名，默认
appendfilename "appendonly.aof"

# 每秒写入只追加文件，默认
appendfsync everysec
```

##### 离线安装

- 解压tar.gz文件
- 进入redis根目录
- make命令安装redis
- make install

- 进入redis安装目录

```sh
cd /usr/local/bin
```

- 指定配置文件启动redis服务器

```sh
redis-server /handle/data/redis/conf/redis.conf
```

- 查看redis有没有启动

```sh
ps -ef|grep redis
```

- 指定端口启动redis客户端

```sh
redis-cli -p 6379
```

- 测试redis数据库是否启动成功

```sh
ping
```

- 关闭redis服务器

```sh
# 在redis客户端中端中关闭服务器
shutdown nosave
```

- 退出redis客户端

```sh
exit
```

##### docker-redis

- 下载redis镜像

```sh
docker pull redis:8.10.0
```

docker redis默认不主动加载配置文件的，redis服务会直接以硬编码的内置默认参数运行

如果需要自定义配置，去源码仓库下载默认配置文件redis.conf进行修改

配置文件的配置项不是一成不变的，如果更新版本的话要以新的默认配置文件为参考

```conf
# 1. 引入官方默认配置文件，必须为容器内的绝对路径，不能写相对路径
# 补充：docker redis不用引入默认配置文件，它自己自带默认参数运行的，只要将改动的内容写到redis.conf就行了
# include /usr/local/etc/redis/redis.conf

# 2.在下方写入需要覆盖的配置（后面的配置会顶掉上面引入的配置文件的相同配置）

# 监听所有网卡的地址
bind 0.0.0.0

# 由于Docker 容器的生命周期依赖于前台的主进程
# 如果设置为daemonize yes，redis 启动后会把自己变成后台进程
# 此时，前台的父进程会立即退出，Docker 会认为“主程序结束了，容器该销毁了”，于是容器瞬间退出
# 因此如果是docker版的redis，要设置为daemonize no
daemonize no

# 登录密码
requirepass yourpassword

# 快照（RDB）默认是开启，还要设置触发保存RDB的条件
# save 指令是“追加型”的，先清空默认配置文件的save配置
# save ""
# 3600 秒内至少 1 次写操作 → 触发 RDB
save 3600 1
# 300 秒内至少 100 次写操作 → 触发 RDB
save 300 100
# 60 秒内至少 10000 次写操作 → 触发 RDB
save 60 10000

# 只追加文件（AOF）需要手动开启
appendonly yes

# 设置只追加文件每秒同步一次
appendfsync everysec

# 只追加文件的目录名称
appenddirname "aof"
```

- compose.yaml

```yaml
redis:
    image: redis:8.10.0
    container_name: redis
    ports:
        - "6379:6379"
    volumes:
        - redisData:/data
        - /home/handle/data/redis:/usr/local/etc/redis
    # 使用自定义配置文件
    command: redis-server /usr/local/etc/redis/redis.conf
    networks:
        - dubhe-net
    restart: always
networks: 
    dubhe-net:
        name: dubhe-net
volumes: 
    redisData:
        name: redisData
```

- 启动

```sh
# 首次启动
docker compose -f path/to/compose.yaml up -d

# 然后将修改好的配置文件复制到/var/lib/docker/volumes/redis-conf/_data
cp /path/to/redis.conf /var/lib/docker/volumes/redis-conf/_data

# 然后删除容器，不然直接执行启动配置文件不生效的
docker compose -f mycompose.yaml down

# 最后再启动
docker compose -f mycompose.yaml up -d
```

- 连接redis客户端

```sh
# 方法1，执行后将进入容器环境，然后输入redis-cli并回车，进入redis客户端 
docker exec -it redis01 /bin/bash

# 方式2
docker exec -it redis01 redis-cli
```

- 5.测试redis

```sh
ping
```

#### Redis Insight

Redis Insight是Redis官方推出的redis数据库管理工具

官网：<https://redis.io/insight/>

github：<https://github.com/redis/RedisInsight>

官网下载要填各种信息，建议直接去github下载

- 安装docker版本的redisinsight

```sh
docker pull redis/redisinsight:2.42
```

- compose.yaml

```yaml
redisinsight:
    image: redis/redisinsight:2.42
    container_name: redisinsight
    ports:
        - "5540:5540"
    volumes:
        - redisinsight-data:/data

volumes: 
    redisinsight-data:
        name: redisinsight-data
```

- 访问：<http://你的ip:5540/>

#### 数据库操作命令

```sh
# 选择数据库，默认16个数据库，数据库id为0-15
select 0

# 查看当前数据库的key数量
dbsize

# 列出当前数据库的所有key
keys *

# 把k1从当前库移到指定数据库1
move k1 1

# 清空当前数据库
flushdb

# 清空所有数据库
flushall
```

#### 通用操作命令

```sh
# k1是否存在，1存在，0不存在
exists k1

# 删除k1
del k1

# 设置k1的过期时间为5秒
expire k1 5

# 查看k1剩余多少秒过期，-1永不过期，-2已过期
ttl k1

# 获取k1的类型
type k1
```

#### `String`操作命令

```sh
# 设置k1的值为v1
set k1 v1

# 当且仅当k1不存在时设置k1的值为v1
setnx k1 v1

# 当且仅当k1不存在时设置k1的过期时间和值
setnx k1 30 v1

set key value nx px timeout

# 当且仅当k1存在时设置k1的过期时间和值
setex k1 30 v1

# 获取k1的值
get k1

# 设置k1为新值，返回老值
set k1 v1 get

# 设置k1为新值，返回老值
getset k1 v1

# 设置一个或多个key的值
mset k1 v1 k2 v2

# 获取一个或多个key的值
mget k1 k2

# 从k1下标为1开始，用ab，覆盖原来对应下标的值
setrange k1 1 ab

# 获取k1的子串（闭区间）
getrange k1 0 -1

# k1的（数字）值增1
incr k1

# k1的值自增步长值
incrby k1 2

# k1的（数字）值减1
decr k1

# k1的值自减步长值
decrby k1 2

# k1的值末尾追加字符s
append k1 s

# 获取k1的值的（字符串）长度
strlen k1
```

#### `List`操作命令

- 是一个双端链表的结构
- List的头部为左边，List的尾部在右边
- 可以通过`rpush/lpop`或者`lpush/rpop`实现队列
- 可以通过`rpush/rpop`或者`lpush/lpop`实现栈

```sh
# 在k1的尾部（右边）添加一个或多个元素
rpush k1 1 2 3

# 在k1的头部（左边）添加一个或多个元素
lpush k1 1 2 3

# 将k1索引0的元素值设置为dong，索引越界会报错
lset k1 0 dong

# 获取k1的元素个数
llen k1

# 获取k1 0-2之间的元素，闭区间，如果是取出所有值可以用lrange k1 0 -1
lrange k1 0 2

# 移除并返回k1最左边的元素
lpop k1

# 移除并返回k1最右边的元素
rpop k1

# 通过索引获取列表中的元素
lindex list1 1

# 删除3个值为value1的元素
lrem list1 3 value1

# 截取范围内的值再赋给key
ltrim list 0 2

# list1取出一个元素加到list2
rpoplpush list1 list2

# 在列表中的指定元素前/后插入一个新元素
linsert list1 before/after 元素 新元素
linsert list1 after zhang san
```

#### `Hash`操作命令

```sh
# 设置一个或多个键值对到k1，相当于k1.put("f1", "v1");k1.put("f2", "v2");
hset k1 f1 v1 f2 v2

# 当且仅当k1中不存在字段f1时设置f1的值为v1
hsetnx k1 f1 v1

# 同时设置多个键值对到k1，相当于k1.put("f1", "v1");k1.put("f2", "v2");
hmset k1 f1 v1 f2 v2

# 获取k1中字段f1的值，相当于k1.get("f1")
hget k1 f1

# 从k1中获取多个字段的值
hmget k1 f1 f2

# 获取k1的所有键值对
hgetall k1

# 判断k1中是否存在字段f1，1存在，0不存在
hexists k1 f1

# 删除k1中一个或多个字段
hdel k1 f1 f2

# 获取k1的字段个数
hlen k1

# 获取k1所有字段
hkeys k1

# 获取k1所有字段的值
hvals k1

# k1的字段f1的值加-1
hincrby k1 f1 -1

# k1的字段f1的值加0.1
hincrbyfloat k1 f1 0.1

# k1不存在f1时设置值
hsetnx k1 f1 v1
```

#### `Set`操作命令

```sh
# 向k1添加一个或多个元素
sadd k1 v1 v2

# 删除k1的v1元素
srem k1 v1

# 获取k1的所有元素
smembers k1

# 获取k1的元素个数
scard k1

# 判断v1是否在k1中，存在1，不存在0
sismember k1 v1

# 获取k1和k2的交集
sinter k1 k2

# k1和k2的交集保存在k3中
sinterstore k3 k1 k2

# 获取k1和k2的并集
sunion k1 k2

# k1和k2的并集保存在k3中
sunionstore k3 k1 k2

# 获取k1和k2的差集（k1中去掉k1和k2的交集）
sdiff k1 k2

# k1和k2的差集保存在k3中
sdiffstore k3 k1 k2

# 随机获取k1中的两个元素，可以用于允许重复中奖的抽奖场景
srandmember k1 2

# 随机获取并移除k1中的两个元素，可以用于不允许重复中奖的抽奖场景
spop k1 2

# 将set1中的v1元素移动到set2中
smove set1 set2 v1
```

#### `Sorted Set`操作命令

```sh
# 向k1中添加元素并指定排序
zadd k1 1 v1 3 v3 2 v2

# 获取k1的元素个数
zcard k

# 获取k1中v2的排序值
zscore k1 v2

# 获取k1索引的0到2的元素（按score从低到高排序），zrange k1 0 -1获取所有
zrange k1 0 2

# 获取k1索引的0到2的元素（按score从高到低排序），zrange k1 0 -1获取所有
zrevrange k1

# 获取k1中v1的排名（按score从低到高排序）
zrank k1 v1

# 获取k1中v1的排名（按score从高到低排序）
zrevrank k1 v1
```

- 使用aof持久化

```conf
appendonly yes
```

- 修复aof文件

```sh
redis-check-aof --fix appendonly.aof
```

- 修复rdb文件

```sh
redis-check-rdb --fix dump.rdb
```

- redis事务

```sh
# 监测某个key的改动，一般用在开启事务（multi）之前
watch key

# 取消对所有key的监测，一般用在事务执行exec失败（因为事务期间某个key被其他模块加塞更改了）之后
# 然后再 watch key 重新开始事务，直到执行成功
unwatch

# 标记一个事务块的开始
multi

# 执行事务块内的命令
exec

# 取消执行事务块内的命令
discard
```

#### 主从复制

- 查看数据库信息

```sh
info replication
```

- 给当前数据库设置主数据库

```sh
# 假设主数据库为127.0.0.1 6379
slaveof 127.0.0.1 6379
```

- 使当前数据库停止与其他数据库的同步，自己成为主数据库

```sh
slaveof no one
```

- 启动哨兵

```sh
redis-sentinel sentinel.conf 
```

#### Jedis

官网：<https://github.com/redis/jedis>

Jedis是以性能和简便为目标进行设计的Redis的java客户端

命令行中操作redis的指令在jedis中都有对应的方法实现

- maven依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <!-- 8.0.0开始支持java25 -->
    <version>8.0.0</jedis.version>
</dependency>
```

- jedis连接与基本使用

```java
@Test
public void testJedis() {
    // Jedis jedis = new Jedis("localhost", 6379);
    // 指定用户名和密码，如果没有设置用户名，则默认用户名为default
    Jedis jedis = new Jedis("redis://用户名:密码@ip:port");

    Assertions.assertEquals("PONG", jedis.ping());

    jedis.set("test", "success");
    Assertions.assertEquals("success", jedis.get("test"));

    jedis.close();
}
```

- 事务提交/取消

```java
 @Test
public void testTransaction() {
    Jedis jedis = new Jedis("www.laodeli.top", 6379);

    // 开始事务
    Transaction transaction = jedis.multi();
    transaction.set("k1", "v1");
    transaction.set("k2", "v2");
    // 取消事务
    transaction.discard();

    Assertions.assertEquals(null, jedis.get("k1"));
    Assertions.assertEquals(null, jedis.get("k2"));

    // 开始事务
    transaction = jedis.multi();
    transaction.set("k3", "v3");
    transaction.set("k4", "v4");
    // 提交事务
    transaction.exec();

    Assertions.assertEquals("v3", jedis.get("k3"));
    Assertions.assertEquals("v4", jedis.get("k4"));

    jedis.close();
}
```

#### lettuce

Lettuce是一个可扩展的线程安全的Redis客户端，用于同步、异步和响应式使用

但是Lettuce的版本更新没有Jedis跟进那么快

```xml
<dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <!-- 7.x版本兼容到8.4、java24 -->
    <version>7.6.0.RELEASE</version>
</dependency>
```

#### Spring Data Redis

Jedis不支持master/replica

- maven依赖

```xml
<!-- 默认使用 Lettuce -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- 用于测试的依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-test</artifactId>
    <scope>test</scope>
</dependency>
```

- 配置

```properties
# 写法一：指定url就行了，当设置了url时，host, port, username 和 password属性会被忽略
spring.data.redis.url=redis://default:redis123@10.0.2.15:6379

# 写法二：分开设置host, port, username 和 password
#spring.data.redis.host=10.0.2.15
#spring.data.redis.port=6379
#spring.data.redis.username=default
#spring.data.redis.password=redis123

# 指定使用的数据库
spring.data.redis.database=0

# 配置自动装配的 RedisConnectionFactory 使用 SSL 和 redis 服务器通信
#spring.data.redis.ssl.enabled=true

# 自定义SSL信任材料可以在SSL bundle中配置，自动应用到RedisConnectionFactory中
#spring.data.redis.ssl.bundle=example
```

- 然后就可以使用自动配置的：RedisConnectionFactory, StringRedisTemplate 或 RedisTemplate 了

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Test
public void test() {
    stringRedisTemplate.opsForValue().set("greet", "hello world");
    System.out.println(stringRedisTemplate.opsForValue().get("greet"));
}
```

#### 分布式锁

缓存击穿解决方案：加锁限制访问

- 1.青铜方案：setnx加锁，获取到锁的微服务才能查数据库

```sh
# 当且仅当k1不存在时，将k的值设置为v1
setnx k1 v1
```

缺陷：setnx 占锁成功，业务代码出现异常或者服务器宕机，没有执行删除锁的逻辑，就造成了死锁。

- 2.白银方案：setnx加锁同时设置过期时间
缺陷：因为占锁和设置过期时间是分两步执行的，所以如果在这两步之间发生了异常，则锁的过期时间根本就没有设置成功。

- 3.黄金方案：setnx加锁同时设置过期时间作为原子操作执行

```sh
# 设置某个 key 的值并设置多少毫秒或秒 过期

set <key> <value> PX <多少毫秒> NX
或
set <key> <value> EX <多少秒> NX
```

缺陷：用户A处理业务时间长导致时间到自动开锁了，用户B获取到锁后和用户A操作冲突，用户B操作完把A的锁解开了

- 4.铂金方案：给锁设置编号，只能开编号对应的锁

缺陷：获取锁的值和删除锁不是原子操作

- 5.钻石方案：查询锁和删除锁这两步作为原子指令操作

缺陷：Lua 脚本，非专业

- 6.王者方案：使用Redisson

```java
// 1.设置分布式锁
RLock lock = redisson.getLock("lock");
// 2.占用锁
lock.lock();
// 3.执行业务
...
// 4.释放锁
lock.unlock();
```

## 分布式定时任务

### PowerJob

#### 部署PowerJob

正式环境server和worker一定要部署在同一个网段！

如果server和worker不是部署在同一个局域网（如worker在宿主机ide运行，server在虚拟机（nat）的docker运行）
则server需要添加jvm参数-e JVMOPTIONS="-Dpowerjob.network.external.address=localhost -Dpowerjob.network.external.port.http=10010"；
且worker需要添加jvm参数-Dpowerjob.network.external.address=192.168.56.1 -Dpowerjob.network.external.port=27777，其中192.168.56.1为nat模式下virtualbox虚拟网卡分配的宿主机ip，千万不要用因特网网卡的ip

- 创建数据库

```sql
-- 根据部署的环境（日常（daily）、预发（pre）和线上（product））
-- 分别创建对应的数据库powerjob-daily、powerjob-pre 和 powerjob-product
create database powerjob-daily;
create database powerjob-pre;
create database powerjob_product;
```

- 拉取powerjob的docker镜像(由于server和worker需要在同一局域网部署，镜像的方式有很多问题，笔者这里也是部署到最后worker卡在等待worker接收这一步，推荐自己打包server然后作为服务器和worker放在同一个局域网)

```sh
docker pull powerjob/powerjob-server:4.3.9
```

- 启动调度服务器powerjob-server

```sh
# 这里用的是product环境启动，所以前面的步骤要创建好powerjob_product数据库
# 如果powerjob-server和数据库在同一个虚拟系统上，则连接时直接用虚拟系统的ip+数据库端口连接
docker run -d \
    --restart=always \
    --name powerjob-server01 \
    -p 7700:7700 -p 10086:10086 -p 10010:10010 \
    -e TZ="Asia/Shanghai" \
    -e JVMOPTIONS="-Dpowerjob.network.external.address=localhost -Dpowerjob.network.external.port.http=10010" \
    -e PARAMS="--spring.profiles.active=product --spring.datasource.core.driver-class-name=org.postgresql.Driver --spring.datasource.core.jdbc-url=jdbc:postgresql://10.0.2.15:5432/powerjob_product --spring.datasource.core.username=postgres --spring.datasource.core.password=postgres123" \
    -v /data/powerjob/powerjob-server:/root/powerjob/server -v /data/powerjob/.m2:/root/.m2 \
    powerjob/powerjob-server:4.3.9
```

- 检查是否启动成功

```sh
# 打开链接http://ip:${server.port:7700} 检验是否部署成功
# 不成功则查看日志
tail -f /data/powerjob/powerjob-server/logs/powerjob-server-application.log
```

- 查看账号密码(5.x)

```sh
# 搜索关键字命令1
cat /data/powerjob/powerjob-server/logs/powerjob-server-application.log | grep "username"

# 搜索关键字命令2
grep 'administrator' /data/powerjob/powerjob-server/logs/powerjob-server-application.log
```

## Nginx

Nginx是一个HTTP web服务器，反向代理，内容缓存，负载均衡器，TCP/UDP代理服务器和邮件代理服务器

- 官网：<http://nginx.org/>

|模块|功能|
|:-|:-|
|rewrite|实现重写功能|
|access|来源控制|
|ssl|安全加密|
|ngx_http_gzip_module|网络传输压缩|
|ngx_http_proxy_module|实现代理|
|ngx_http_upstream_module|实现后端服务器列表定义|
|ngx_cache_purge|实现缓存清除|

### 正向代理

举个例子：国内通过浏览器直接访问谷歌（www.google.com）是访问不了的。需要在浏览器配置代理服务器（www.xxx.com），之后每当访问谷歌的时候，代理服务器就会代理你访问谷歌，将请求结果返回浏览器。这个浏览器配置代理服务器，通过代理服务器访问互联网的过程就是正向代理。

也就是说，正向代理是代理客户端（访问目标地址）

### 反向代理

客户端（如浏览器）将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器，获取数据后返回给客户端。此时反向代理服务器和目标服务器对于客户端来说就是一个服务器（暴露的是代理服务器的地址，隐藏了真实服务器地址），客户端对代理是无感知的，因为客户端无需任何配置就可以访问。

也就是说，反向代理是代理服务器（接收客户端请求），它是所有真实服务器的门户

### 负载均衡

通过反向代理服务器，将客户端请求分发到各个真实服务器上

### 动静分离

通过反向代理服务器，把动态资源请求和静态资源请求分给不同的服务器处理，加快解析速度，降低单个服务器的压力

- 实现
    - 1.把静态文件（html、css、js、image）放在独立的服务器上，独立成一个域名，这是目前主流推崇的方案
    - 2.动态跟静态文件混合一起发布，通过nginx分开

### 安装nginx

- Red Hat系列Linux

```sh
# 解压
tar zxvf nginx-1.21.6.tar.gz

# 进入解压后的目录
cd nginx-1.21.6

# 设置安装路径
./configure --prefix=/usr/local/bin/nginx
# 如果提示C编译器没有安装，则安装gcc
yum install -y gcc

# 如果提示pcre模块没安装，则安装pcre
yum install -y pcre pcre-devel

# 如果提示安装zlib则安装zlib
yum install -y zlib zlib-devel

# 如果提示安装openssl则安装
yum install -y openssl openssl-devel

# make
make

# make install
make install
```

### 启动nginx

```sh
# 要先进入nginx的sbin目录
cd /usr/local/bin/nginx/sbin

# 启动nginx
./nginx

# 查看nginx是否启动成功
# 也可以查看nginx的配置文件（/usr/local/nginx/conf/nginx.conf）
# 开放端口后通过浏览器访问nginx的home页面确认
ps -ef | grep nginx

# 快速停止
./nginx -s stop

# 优雅关闭，nginx在退出前完成已经接收的连接请求
./nginx -s quit

# 重新加载配置
./nginx -s reload

# 查看nginx版本号
./nginx -v
```

### nginx的配置文件

- 位置：`/usr/local/nginx/conf/nginx.conf`

- 由三部分构成全局块、events块、http块

#### 全局块

从配置文件开始到events块直接的内容，主要涉及一些影响nginx服务器整体运行的配置指令

包括配置运行nginx服务器的用户（组）、允许生成的worker process数、进程id存放路径、日志存放路径和类型、配置文件的引入等

```conf
# nginx服务器并发处理服务的关键配置
# worker_processes值越大，可以支持的并发处理量也越多，但受硬件、软件等设备的制约
worker_processes 1;
```

#### events块

涉及的指令主要影响nginx服务器与用户的网络连接

包括是否开启对多worker process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个worker process可以同时支持的最大连接数等

这部分的设置堆nginx的性能影响较大，在实际中应该灵活配置

```conf
# 每个worker process可以同时支持的最大连接数
events {
    worker_connections 1024;
}
```

#### http块

nginx服务器配置中最频繁的部分，代理、缓存和日志自定义和第三方模块的配置等都在这里设置

http块包括http全局块、server块

##### http全局块

包括文件引入、MIME-TYPE定义、日志自定义、连接超时时间、但链接请求数上限等

```conf
http {
    include mime.types;
    default_type application/octet-stream;

    sendfile on;

    keepalive_timeout 65;
}
```

##### server块

和虚拟主机有密切关系，从用户角度看，虚拟主机和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器的硬件成本

每个http块可以包含多个server块，而每个server块就相当于一个虚拟主机

每个server块分为全局server块、location块

###### 全局server块

最常见的配置是本虚拟主机的监听配置和本虚拟主机的名称和IP配置

```conf
server {
    # nginx监听的端口号
    listen 80;
    # 主机名称
    server_name localhost;
}
```

###### location块

主要作用是基于nginx服务器接收到的请求字符串（如server_name/uri-string），对虚拟主机名称/IP之外的字符串（如/uri-string）进行匹配，对特定的请求进行处理

地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里设置

一个server块可以包含多个location块

```conf
server {
    # 当请求路径有/，做跳转
    location / {
        root html;
        index index.html index.htm;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root html;
    }
}
```

- location指令

```conf
# =：用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求
# ~：表示uri包含正则表达式，并且区分大小写
# ~*：表示uri包含正则表达式，并且不区分大小写
# ^~：用于不含正则表达式的uri前，要求nginx服务器找到标识uri和请求字符串匹配度最高的location后，立即使用此location处理请求，而不再使用location块中的正则uri和请求字符串做匹配
location [=|~|~*|^~] uri {}
```

### 配置反向代理

- 目的1：当访问`http://10.0.2.15:80`的时候，nginx将会转发请求给<http://127.0.0.1:8080>处理

```conf
server {
    # nginx监听的端口号，记得开放80端口
    listen 80;
    # 主机名称：可以是主机名或IP地址（这里的例子，可以将这个主机名称理解为启动nginx服务的主机的IP地址）
    server_name 10.0.2.15;

    # 当请求路径有/，做跳转
    location / {
        root html;
        # 记得开放8080端口
        proxy_pass http://127.0.0.1:8080;
        index index.html index.htm;
    }
}
```

- 目的2：
    - 当访问`http://10.0.2.15:8001/handle/`的时候，nginx将会转发请求给<http://127.0.0.1:8081>处理
    - 当访问`http://10.0.2.15:8001/jack/`的时候，nginx将会转发请求给<http://127.0.0.1:8082>处理

```conf
server {
    # nginx监听的端口号，记得开放端口
    listen 8001;
    # 主机名称：可以是主机名或IP地址（这里的例子，可以将这个主机名称理解为启动nginx服务的主机的IP地址）
    server_name 10.0.2.15;

    # 当请求路径有handle，做跳转
    location ~ /handle/ {
        # 记得开放端口
        proxy_pass http://127.0.0.1:8081;
    }
    
    # 当请求路径有jack，做跳转
    location ~ /jack/ {
        # 记得开放端口
        proxy_pass http://127.0.0.1:8082;
    }
}
```

### 配置负载均衡

- nginx提供了几种负载均衡策略
    - 1.轮询（默认）：每个请求按时间顺序逐一分配到不同的后端服务器，如果某个后端服务器宕机，能自动剔除
    - 2.weight：默认权重为1,权重越高被分配的客户端越多，用于后端服务器性能不均的情况
    - 3.ip_hash：每个请求按访客ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题
    - 4.fair（第三方）：按后端服务器的响应时间来分配请求，响应时间短的优先分配

```conf
# 轮询
upstream myserver {
    server 10.0.2.15:8080;
    server 10.0.2.15:8081;
}

# weight
upstream myserver {
    server 10.0.2.15:8080 weight=1;
    server 10.0.2.15:8081 weight=3;
}

# ip_hash
upstream myserver {
    ip_hash;
    server 10.0.2.15:8080;
    server 10.0.2.15:8081;
}

# fair
upstream myserver {
    server 10.0.2.15:8080;
    server 10.0.2.15:8081;
    fair;
}
```

```conf
http {
    # 负载均衡服务器命名为myserver，配置负载均衡服务器列表
    upstream myserver {
        server 10.0.2.15:8080;
        server 10.0.2.15:8081;
    }
    server {
        # nginx监听的端口号，记得开放端口
        listen 8001;
        # 主机名称：可以是主机名或IP地址（这里的例子，可以将这个主机名称理解为启动nginx服务的主机的IP地址）
        server_name 10.0.2.15;

        location / {
            # myserver对应前面负载均衡服务器的名字
            proxy_pass http://myserver;
            proxy_connect_timeout 10;
            # ...
        }
    }
}
```

### 配置动静分离

- 目的
    - 1.访问<http://10.0.2.15:8001/html/hello.html>的时候，返回/data/html/hello.html
    - 2.访问<http://10.0.2.15:8001/image/hello.png>的时候，返回/data/image/hello.png
    - 3.访问<http://10.0.2.15:8001/image>的时候，返回/data/image的子目录/文件列表

```conf
server {
        # nginx监听的端口号，记得开放端口
        listen 8001;
        # 主机名称：可以是主机名或IP地址（这里的例子，可以将这个主机名称理解为启动nginx服务的主机的IP地址）
        server_name 10.0.2.15;

        # 请求路径
        location /html/ {
            # 系统目录
            root /data/;
            # ...
        }
        
        # 请求路径
        location /image/ {
            # 系统目录
            root /data/;

            # 列出目录的内容，如访问http://10.0.2.15:8001/image的时候会列出/data/image目录里面的子目录/文件
            autoindex on;
        }
    }
```

### 配置Nginx高可用集群

![Nginx高可用](/images/nginx.png)

keepalived官网：<https://www.keepalived.org/index.html>

```sh
# 启动keepalived
sudo systemctl start keepalived.service

# 设置keepalived开机启动
sudo systemctl enable keepalived.service
```

- 两台服务器分别安装nginx和keepalived（可以通过包管理器安装）

- 配置keepalived，/etc/keepalived/keepalived.conf，未完善...

```conf
# 全局配置
global_defs {
    notification_email {
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
    }
    notification_email_from Alexandre.Cassen@firewall.loc
    smtp_server 192.168.200.1
    smtp_connect_timeout 30
    router_id LVS_DEVEL
    vrrp_skip_check_adv_addr
    vrrp_strict
    vrrp_garp_interval 0
    vrrp_gna_interval 0

    # 视频教程里面有这个配置，但是新版本没看到，先保留着
    # /etc/hosts文件中设置的：127.0.0.1 MY_HOST_NAME
    # router_id MY_HOST_NAME
}

# 视频教程里面有检测脚本这个配置，但是新版本没看到，先保留着
vrrp_script chk_http_port {
    script "/usr/local/src/nginx_check.sh"
    # 检测脚本执行的间隔
    interval 2
    # 当脚本成立的时候，设置当前服务器的权重
    weight 2
}

vrrp_instance VI_1 {
    # 主服务器为MASTER，从服务器为BACKUP
    state MASTER
    # 使用哪个网卡就填哪个网卡名
    interface eth0
    # 主、从必须相同
    virtual_router_id 51
    # 主、从设置不同的优先级，主服务器设置较大值，从服务器设置较小值
    priority 100
    # 每隔多少秒发送心跳
    advert_int 1
    authentication {
        # 鉴权方式
        auth_type PASS
        # 密码
        auth_pass 1111
    }
    virtual_ipaddress {
        # 虚拟ip，如果按照图中的布置应该填192.168.17.50
        192.168.200.16
        192.168.200.17
        192.168.200.18
    }

    # Allow packets addressed to the VIPs above to be received
    accept
}

virtual_server 192.168.200.100 443 {
    delay_loop 6
    lb_algo rr
    lb_kind NAT
    persistence_timeout 50
    protocol TCP

    real_server 192.168.201.100 443 {
        weight 1
        SSL_GET {
            url {
              path /
              digest ff20ad2481f97b1754ef3e12ecd3a9cc
            }
            url {
              path /mrtg/
              digest 9b3a0c85a887a256d6939da88aabd8cd
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }
}

virtual_server 10.10.10.2 1358 {
    delay_loop 6
    lb_algo rr
    lb_kind NAT
    persistence_timeout 50
    protocol TCP

    sorry_server 192.168.200.200 1358

    real_server 192.168.200.2 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }

    real_server 192.168.200.3 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334c
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334c
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }
}

virtual_server 10.10.10.3 1358 {
    delay_loop 3
    lb_algo rr
    lb_kind NAT
    persistence_timeout 50
    protocol TCP

    real_server 192.168.200.4 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }

    real_server 192.168.200.5 1358 {
        weight 1
        HTTP_GET {
            url {
              path /testurl/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl2/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            url {
              path /testurl3/test.jsp
              digest 640205b7b0fc66c1ea91c463fac6334d
            }
            connect_timeout 3
            retry 3
            delay_before_retry 3
        }
    }
}

```

- 脚本

```sh
#!/bin/bash
A=`ps -C nginx --no-header | wc -l`
if [$A -eq 0];then
    /usr/local/nginx/sbin/nginx
    sleep 2
    if [`ps -C nginx --no-header | wc -l` -eq 0];then
        killall keepalived
    fi
fi
```

### Nginx原理

- Nginx有两个进程：master和（多个）worker，master管理worker，多个worker通过争抢的方式处理请求

- 设置worker的数量和cpu的核数相等最为适宜

- 连接数（worker_connection）
    - 客户端发送一个请求，占用2个（一来一回，client<=>worker）或4（client<=>worker<=>tomcat）个worker的连接数
    - nginx的静态资源访问最大并发数：连接数*worker数量/2
    - nginx的动态资源访问（如反向代理）最大并发数：连接数*worker数量/4

### 创建脚本启动nginx服务

```sh
# 创建脚本，脚本内容
vim /usr/lib/systemd/system/nginx.service

# 重新加载系统服务
systemctl daemon-reload

# 启动nginx服务
systemctl start nginx.service

# 查看nginx服务启动状态
systemctl status nginx.service

# 设置nginx服务为开机启动
systemctl enable nginx.service

# 取消nginx服务开机启动
systemctl disable nginx.service
```

- 脚本内容

```service
[Unit]
Description=nginx - web server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/bin/nginx/logs/nginx.pid
ExecStartPre=/usr/local/bin/nginx/sbin/nginx -t -c
/usr/local/bin/nginx/conf/nginx.conf
ExecStart=/usr/local/bin/nginx/sbin/nginx -c /usr/local/bin/nginx/conf/nginx.conf
ExecReload=/usr/local/bin/nginx/sbin/nginx -s reload
ExecStop=/usr/local/bin/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/bin/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

### 开放端口

```sh
# 查看开放的端口，观察ports关键字列出的端口，默认是空的
firewall-cmd --list-all

# 放行端口
firewall-cmd --zone=public --add-port=80/tcp --permanent

# 重启防火墙
firewall-cmd --reload
```

## Deep Java Library

- 模型训练流程
    - 准备数据集
    - 构建神经网络
    - 构建模型（让这个模型应用上一步的神经网络）
    - 训练配置（如何训练，训练数据集、验证数据集、测试数据集）
    - 训练模型
    - 保存模型
- 模型使用流程
    - 加载模型（上面保存的模型）
    - 预测（给模型一个新输入，让其判断）

- 未训练的模型是参数值不确定的公式，训练好的模型是参数值确定的公式

![机器学习算法](/images/机器学习算法.png)

目前Deep Java Library没有办法使用amdgpu进行加速，看了底层代码都没有amd显卡的判断逻辑，因此打算直接用python训练玩玩了

## VirtualBox

### 网络

#### 桥接

需要根据电脑当前实际使用的网络进行选择，当前是用网线的就选网线网络对应的网卡；当前是用WIFI的就选WIFI网络对应的网卡

![网卡选择](/images/网卡选择.png)

#### nat

功能：固定IP + 内外连通 + 外网访问

- 配置虚拟机系统网络适配器

![选择NAT模式](/images/%E9%80%89%E6%8B%A9NAT%E6%A8%A1%E5%BC%8F.png)

- 配置宿主机和虚拟机的映射（不用重启虚拟机系统）

![配置宿主机和虚拟机的映射](/images/%E9%85%8D%E7%BD%AE%E5%AE%BF%E4%B8%BB%E6%9C%BA%E5%92%8C%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%9A%84%E6%98%A0%E5%B0%84.png)

- 配置虚拟机系统的网络

```sh
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

```txt
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=5208bd48-a7df-43f5-8824-090a1d5ba7f5
DEVICE=enp0s3
ONBOOT=yes
IPADDR=10.0.2.15
PREFIX=24
GATEWAY=10.0.2.2
IPV6_PRIVACY=no
DNS1=223.5.5.5
```

- 用127.0.0.1:2222连接虚拟机操作系统

#### nat network

功能：固定IP + 内外连通 + 外网访问 + 虚拟机互通

- 新建nat network，用宿主机的2222端口映射虚拟机操作系统的22端口

![新建nat network](/images/%E6%96%B0%E5%BB%BAnat%20network.png)

- 配置虚拟机操作系统的网络，`Promiscuous mode选Allow all`

![配置虚拟机操作系统的网络](/images/%E9%85%8D%E7%BD%AE%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%9A%84%E7%BD%91%E7%BB%9C.png)

- 配置虚拟机操作系统静态ip

```sh
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3

# 配置完成重启网络
service network restart
```

```txt
TYPE="Ethernet"
PROXY_·ETHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="enp0s3"
UUID="829eb1b0-1a34-45af-9fd5-36a3d3b12e76"
DEVICE="enp0s3"
ONBOOT="yes"
# 自定义虚拟机静态ip
IPADDR=10.0.2.3
# 宿主机子网掩码
NETMASK=255.255.255.0
# 宿主机网关
GATEWAY=192.168.1.1
DNS1=114.114.114.114
```

- 用127.0.0.1:2222连接虚拟机操作系统

#### host-only宿主机静态IP

当docker容器要映射主机的IP时，由于主机的公网IP是动态的，每次启动都要更新这个IP，很不方便
<br/>
因此可以通过创建一个虚拟网卡，设置静态IP，当虚拟机要用到主机IP时，就用这个IP来进行通讯

- 创建虚拟网卡

![创建虚拟网卡](/images/tools-network.png)

- 配置静态IP

![配置静态IP](/images/host-only-network.png)

## IDE

### Eclipse

#### 基本设置

- 打开`Window->Preferences`
    - General
        - `Startup and Shutdown` -> `Plug-ins activated on startup`里面的选项都取消勾选
        - `Apperance` -> `Colors and Fonts`
            - `Basic` -> `Text Font`，点击`Edit`进行代码编辑区的字体和字号设置
            - `View and Editor Folders` -> `Tree and Table font for views` -> 点击`Edit`进行侧边栏的字体和字号设置
            - 至于标题栏、菜单栏和工具栏的字体和字号是继承操作系统的字体和字号的，可以通过后者进行设置
        - `Content Types`
            - Text，在`Default encoding` 输入`UTF-8`，然后点击`Update`
                - `Java Properties File`，在`Default encoding` 输入`UTF-8`，然后点击`Update`
                - `Spring Properties File`，在`Default encoding` 输入`UTF-8`，然后点击`Update`
        - Editors
            - Autosave，勾选`Enable autosave for editors with unsaved changes`
            - `File Associations`,点击`pom.xml`，然后点击`XML Editor`,然后点击`default`，然后应用，这一项设置保留，后面有必要再设置
            - Text Editors
                - 勾上`Insert spaces for tabs`，设置Tab键插入空格
                - 勾上`Show line numbers`，显示行号
        - Keys
            - 搜索`Toggle Comment`，将`TM4E Language`那行的快捷键改成`Ctrl+/`，这样就可以在pom.xml里面使用该快捷键了
        - Workspace，勾上`Refresh using native hooks or polling`，自动刷新文件夹改动
            - `Text file encoding`，设置源文件编码为UTF-8
            - `New text file line delimiter`，设置为Unix换行符
            - Build
                - 勾上`Save automatically before manual build`，手动构建前自动保存

##### Java设置

- Java -> Compiler，`Compiler compliance level`设置为自己的Java版本作为编译器语法版本
    - `Errors/Warnings` -> `Generic types` -> `Redundant type arguments(1.7 or higher)`设置为Warnings
        - 这样泛型补全`List<String> list = new ArrayList<String>();`后，就会显示警告信息，然后就可以通过提示进行处理，目前只想到了这个解决办法

- 设置代码补全，Java->Editor->Content Assist，
    - 取消只有按回车才触发代码补全提示，取消勾选`Disable insertion trigger except 'Enter'`
    - 不使用静态导入，取消勾选`Use static imports`
    - 设置代码补全触发器，`Auto activation triggers for Java`覆盖填入`.qwertyuiopasdfghjklzxcvbnm`
    - 启动Java后缀模板提示，Advance，勾上`Java Postfix Template Proposals`

- 自定义模板，Java->Editor->Templates
    - `.sout` 自动补全

    ![code-template1](image/code-template1.png)

    - `fori` 自动补全

    ![code-template2](image/code-template2.png)

- 设置JRE，Java->Installed JREs，点`Add`添加自己的Java版本

##### Maven设置

- 设置Maven，Maven->Installations，点`Add`添加自己的Maven版本

- 设置Maven配置文件，Maven->User Settings，设置为自己的..\settings.xml

##### XML配置

- 设置缩进，XML->XML Files->Editor，选择`Indent using spaces`，`Indentation size`设置为4
    - 如果是Eclipse IDE for Java Developers版本，需要下载插件`Eclipse XML Editors and Tools`

- 设置xml文件头部约束资源下载，XML(Wild Web Developer)，勾选`Download external resources like referenced DTD,XSD`

##### Mybatis代码提示设置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.handle.application.pojo.AccountDo">

</mapper>
```

- 1. 复制mapper.xml中的`https://mybatis.org/dtd/mybatis-3-mapper.dtd`，粘贴到浏览器地址栏下载，下载完成放到某一个目录中

- 2.XML->XML Catalog，点`Add`

- 3.点`File System`，选中刚刚下载的dtd文件

- `Key type`填`URI`

- `Key`填mapper.xml中的`-//mybatis.org//DTD Mapper 3.0//EN`

#### 安装Lombok插件

- 1.下载Lombok，运行如下命令

```sh
java -jar lombok.jar
```

- 2.在弹出的界面选择eclipse.exe的所在位置，然后重启Eclipse

#### 安装其它插件

- 可以在eclipse插件市场在线安装插件
    - 建议先配置插件的国内镜像源（如清华镜像源）
        - Preference -> `Install/Update` -> `Available Software Sites`，然后更改为国内源

- 建议安装以下插件
    - Kantan Properties Editor
    - Mybatipse
    - Spring Tools

#### 快捷键

|快捷键|功能|
|:-|:-|
|Tab|向右缩进|
|Shift + Tab|向左缩进|
|Ctrl + /|注释/取消注释|
|Ctrl + Shift + C|全局注释/取消注释|
|Alt + Shift + A|进入/退出多行编辑|
|Alt + Shift + R|统一修改变量名|
|Ctrl + Shift + X|转大写|
|Ctrl + Shift + Y|转小写|
|Ctrl + Shift + O|优化导入语句|

#### eclipse使用心得

下载插件特别慢，需要翻墙，下多几次还会被服务器的反爬虫机制阻挡了，下都下不了，很恶心

还有就是配置导出乱七八糟的，没法像idea那样简单，玩不好估计有些配置会漏掉导致没法恢复所有配置

properties文件的中文显示问题，虽然有插件可以正常显示了，但是那个颜色特别刺眼，看久了对视力不好，建议eclipse还是用yaml吧

还有就是代码提示补全，感觉没有idea那么完善

spring-tools-for-eclipse = eclipse-jee + Spring Tools Suite 插件，

虽然eclipse-java也可以通过安装插件变成eclipse-jee或spring-tools-for-eclipse

由于eclipse的插件下载很慢，强烈建议直接使用spring-tools-for-eclipse

### IDEA

官网：<https://www.jetbrains.com/idea/>

社区版：<https://github.com/JetBrains/intellij-community>

- 配置

- File -> Settings
    - Editor
        - General
            - `Auto Import`
                - Java
                    - 勾选`Add unambiguous imports on the fly`
                    - 不要勾选`Optimize imports on the fly`，否则执行会触发导入语句优化的撤销操作时会抽风跟你二人转，设置保存时优化导入就行了
    - Tools
        - `Actions on Save`
            - Action
                - 勾选`Reformat code`，如果使用了版本控制，建议在提交代码的时候再执行代码格式化
                - 勾选`Optimize imports`

|快捷键|功能|
|:-|:-|
|Tab|向右缩进|
|Shift + Tab|向左缩进|
|Ctrl + /|注释/取消注释|
|Shift + F6|重命名，要注意可能跟输入法按键冲突|
|按两下Shift|类搜索|
|Ctrl + Shift + F|全局搜索|
|Ctrl + Shift + R|全局替换|
|Ctrl + Shift + U|大小写转换|
|Ctrl + Shift + O|优化导入语句|
|Ctrl + Shift + L|代码格式化|
|Ctrl + Alt + M|提取代码为作为方法|
|Ctrl + Alt + C|修改变量作用域|

#### idea使用心得

idea目前是社区版和旗舰版合并为一个软件了，如果从官网下载的话，只有一个idea可以下载

官方版idea本会比社区版大一倍，里面塞了很多插件，并且提供了一些社区版没有的一些基础功能，比如CSS、JavaScript、TypeScript、Node.js、npm

看取舍了，如果不打算使用订阅功能并且不介意官方版的更多的基础功能的话建议下载社区版就可以了

更新jdk时，ide无法识别新jdk，显示还是旧的jdk，这时需要在project struct里面删除原来的jdk，然后再添加一次，也可以不用这么麻烦重启电脑就行了

另外不得不提一下maven和mavend了，在日常开发过程中，运行和调试都是idea内置的执行器在工作，跟maven和mavend没有半毛钱关系

除非你在idea设置中委托给maven，但是这会导致调试无法进入断点，一般不会勾选委托给maven

只有执行maven生命周期的任务，如：clean、compile、package、install等时，maven和mavend才有出场的机会

而Sync Project、Sync All Maven Projects和Reload All Maven Projects也是idea在干活的哟

### zed

```sh
# zed，压缩包解压的版本运行后任务栏不显示正确的zed图标，archlinux先用包管理器的版本吧
sudo pacman -S zed
```

安装完后到扩展页面搜索java，下载扩展，只要操作系统层面已经配置好java和maven，在zed完全开箱即用

#### 调试设置

在zed右下角状态栏点击调试图标，然后点击编辑debug.json，编辑内容如下，其中主类要根据具体项目更改

```json
[
  {
    "adapter": "Java",
    "request": "launch",
    "label": "Launch Debugger",
    // if your project has multiple entry points, specify the one to use:
    "mainClass": "具体全限定主类名",
    //
    // this effectively sets a breakpoint at your program entry:
    "stopOnEntry": true,
    // the working directory for the debug process
    "cwd": "$ZED_WORKTREE_ROOT"
  }
]
```

在zed右下角状态栏点击调试图标，添加新会话，选择刚刚编辑的调试名称，然后就能开始调试了

#### zed使用心得

- markdown还不支持转pdf，没有相关的插件
- 内存占用多，显存占用也多，还有点卡
- 特别是markdown预览的时候，很明显的卡顿，并且预览同步延迟也很大
- 更新到12.1后，调试没反应了，作为一个新兴的编辑器，还是先别用吧

### theia-ide

协议友好，但是启动很慢，将来有可能替代vscode，拒绝微软强行喂的无用功能

## Yaml

- `key:空格value`，标识一对键值对

- 键值对的属性和值都是大小写敏感的

- 以缩进的空格数来控制层级关系

- 只要是左对齐的一列数据，都是同一层级

- 字符串默认不用添加双/单引号

    - 英文双引号不会转义字符串里面的特殊字符，

    - 英文单引号会转义字符串里面的特殊字符，会把特殊字符转义成普通的字符

```yaml
# first 换行 second
line: "first \n second"

# first \n second，把换行符变成普通的\n了
line: 'first \n second'
```

## 算法篇

### 全局唯一id生成器

- snowflake算法
- 百度开源的分布式唯一id生成器UidGenerator
- Leaf--美团点评分布式id生成系统

## 踩坑记录

- 服务器要避开使用用浏览器的非安全端口
    - Edge/谷歌
        - 6000
        - 6665-6669
        - 修改成可以使用：桌面快捷图标右键->属性->目标->最后后加上空格，然后加上`--explicitly-allowed-ports=6000,6665`

## 常见问题及处理方法

## 面试篇

### 异常问题

#### catch捕获了异常，try中的return还会返回吗？

答：不返回！

例：

```java
@Slf4j
public class ExceptionTest {
    @Test
    public void test() {
        // 返回2
        log.info("result of fun: {}", fun());
    }

    private int fun() {
        try {
            int a = 1 / 0;
            return 1;
        } catch (Exception e) {
            log.error("", e);
        }
        return 2;
    }
}
```

### 序列化问题

#### 为什么pojo类布尔类型字段不要用is开头？

答：因为不同框架、序列化工具对JavaBean规范的遵守不一样，造成对isFieldName这样的布尔类型字段的解析不一致

导致属性名、getter/setter名、序列化/反序列化字段名不一致，从而引发难以排查的兼容性问题

因为不同的序列化工具（如jackjson、gson）对于布尔类型字段isFieldName的处理不同，如

jackjson遵循JavaBeans规范，isFieldName被认为是一个`方法`，解析完得到的是fieldName

gson不遵循JavaBeans规范，isFieldName被认为是一个`字段`，解析完得到的是isFieldName

如果用jackjson序列化，而用gson反序列化，将导致布尔类型的字段值丢失

团队开发的时候，如微服务之间API调用，你根本不知道对方是用什么序列化/反序列化工具，因此pojo类布尔类型字段不要用is开头

- 定义一个遵循JavaBeans规范的VO

```java
// 如果字段是boolean类型的isFieldName，lombok生成的getter/setter是isFieldName()/setFieldName()
// 如果字段是Boolean类型的isFieldName，lombok生成的getter/setter是getIsFieldName()/setIsFieldName()
// 虽然Boolean没有这个不统一的问题，但是谁知道会使用到哪些框架，它们对isFieldName的解析统一与否，因此还是不要使用isFieldName来定义布尔字段
@ToString
public class UserVO {
    private Boolean isMale;

    public Boolean isMale() {
        return this.isMale;
    }

    public void setMale(Boolean male) {
        this.isMale = male;
    }
}
```

```java
UserVO userVO = new UserVO();
userVO.setMale(true);

String json = new ObjectMapper().writeValueAsString(userVO);
// {"male":true}
System.out.println(json);

Gson gson = new Gson();
// {"isMale":true}
System.out.println(gson.toJson(userVO));

// 用jackson序列化，用gson反序列化
UserVO userVOFromJson = gson.fromJson(json, UserVO.class);
// UserVO(isMale=null)，结果丢失了isMale的字段值
System.out.println(userVOFromJson);
```

### Spring问题

#### Spring是如何解决循环依赖的？

答：使用三级缓存机制（只适用于单例且非构造器注入的场景）

如果A依赖B，B又依赖A，如果都在构造器里注入，直接就报错了

- 一级缓存（singletonObjects）：存成品Bean对象（完全初始化）
- 二级缓存（earlySingletonObjects）：存半成品Bean对象（实例化了但还没注入属性）
- 三级缓存（singletonFactories）：存Bean工厂（用于创建代理或提前暴露引用）

- 创建A时，将其工厂放进三级缓存

- 然后发现A依赖B，于是创建B

- 然后发现B需要A，
    - 先查一级缓存，没有
    - 再查二级缓存，没有
    - 再查三级缓存，找到了A的工厂
        - 调用工厂创建一个提前暴露的A的引用
        - 放入二级缓存
        - 从三级缓存移除A的工厂
        - 这样B就拿到了A的引用了（虽然A还没初始化）
- 然后B完成初始化，放入一级缓存
- 继续完成A的初始化，正常注入B
- 然后B完成初始化，放入一级缓存
- 最终成功解决循环依赖

```yaml
# Spring Boot 2.6+默认禁止循环依赖，需要手动开启才可以
spring:
    main:
        allow-circular-references: true
```
