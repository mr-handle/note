# Note

## Java基础

- 文件夹名称一律用小驼峰，并且单个单词的文件夹名称一律用单数形式
- 变量命名：变量名+变量类型，这样可以快速知道变量属于什么类型，并且不用输入那么多字母就弹出来代码补全了，省的都是时间啊
- 用来存储对象的变量称为引用变量。
- 方法名有final修饰，表示此方法是终结方法，不能被子类重写。
- 可变长参数在一个方法中最多只能有一个，并且必须放在最后。
- Java编译器自动引入java.lang。
- `方法的签名`（signature）: 要完整地描述一个方法，需要指出`方法名`以及`参数类型`
    - 返回类型不是方法签名的一部分。也就是说， 不能有两个名字相同、参数顺序和类型也相同，但是返回不同类型值的方法。
- VO（View Object）：视图对象，前端和控制层之间的数据传输对象。
- DTO（Data Transfer Object）：数据传输对象，控制层与服务层之间的数据传输对象。
- DO（Domain Object）：领域对象，就是从现实世界中抽象出来的有形或无形的业务实体。
- PO（Persistent Object）：持久化对象，它跟持久层（通常是关系型数据库）的数据结构形成一一对应的映射关系
    - 如果持久层是关系型数据库，那么，数据表中的每个字段（或若干个）就对应PO的一个（或若干个）属性。
- 不用public、protected、private修饰的字段和方法就是包作用域。位于同一个包的类，可以访问包作用域的字段和方法。

### JDK

- `JRE` 是 Java 运行时环境
    - 它是运行已编译 Java 程序所需的所有内容的集合，包括 Java 虚拟机（JVM），Java 类库，java 命令和其他的一些基础构件。
    - 但是，它不能用于创建新程序。
- `JDK` 是 Java Development Kit 缩写，它是功能齐全的 Java SDK。
    - 它拥有 JRE 所拥有的一切
    - 还有编译器（javac）和工具（如 javadoc 和 jdb）。
    - 它能够创建和编译程序。

#### 安装JDK

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

- 2.设置环境变量（解压后bin文件夹所在目录为jdk根目录，假设为`/usr/local/jdk/xxx`）

```sh
# /etc/profile 是系统级的shell启动配置文件，它作用于所有用户的登录shell
# 启动一个非登录shell，这时会读取 ~/.bashrc
# 当通过SSH登录远程服务器，或在图形界面登录后启动一个shell，会读取~/.bash_profile，目前笔者用的是这个
# 下面以/etc/profile为例，其它两个文件类似

# 1.先备份/etc/profile（或~/.bashrc或~/.bash_profile）
cp /etc/profile /etc/profile.bk

# 2.编辑profile文件
vi /etc/profile

# 3.在profile文件末尾添加
export JAVA_HOME=/usr/local/jdk/xxx
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

- 若将多个类的声明放在一个文档中，只能有一个类声明为公有类。

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

### 类路径

- 类路径是所有包含类文件的路径的集合

- 类路径包括
    - 根目录（某个自定义目录，里面包含了各种类文件）
    - 当前目录（.）
    - jar文件/目录（/home/user/archives/app.jar,/home/user/archives/'*'）

#### 通过指定类路径运行Java程序

如果我们要执行一个jar包的类文件，就可以把jar包放到类路径中，这样JVM会自动在xxx.jar文件里去搜索这个类文件

- 通过指定类路径和主类运行某个路径下的类文件

```sh
# .表示当前路径，假设类文件路径为：./com/handle/HelloWorld.class
java -classpath . com.handle.HelloWorld
```

- 通过指定类路径和主类运行jar包中的类文件

```sh
# ./HelloWorld.jar 表示当前目录下的HelloWorld.jar文件，里面包含com.handle.HelloWorld.class（主类）文件
java -classpath ./HelloWorld.jar com.handle.HelloWorld
```

![getResource输出](/images/类路径.png)

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

- 8位二进制为一个字节，范围值从0B00000000～0B11111111，程序中用十六进制表示的时候就是从0X00到0XFF

- 二进制4位一组，遵循8,4,2,1的规律比如 1111，那么从最高位开始算，数字大小是`8 * 1 + 4 * 1 + 2 * 1 + 1 * 1` = 15，那么十进制就是15，十六进制就是0XF。

- 二进制转十六进制的时候，十六进制一位刚好是和二进制4位相互对应的。8位二进制为一个字节，对应十六进制2位。

- Java中，byte、short、int、long分别占1、2、4、8个字节，char占2个字节

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

- 左移`<<`，最低位补0，十进制数m左移n位相当于m乘以2的n次方

```java
int m = 1;       // 00000000 00000000 00000000 00000001 = 1
int r = m << 1;  // 00000000 00000000 00000000 00000010 = 2
```

- 右移`>>`，最高位补0，十进制数m右移n位相当于m除以2的n次方

```java
int m = 2;       // 00000000 00000000 00000000 00000010 = 2
int r = m >> 1;  // 00000000 00000000 00000000 00000001 = 1
```

- 无符号右移`>>>`，高位总是补0，注意，`没有无符号左移`！

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

- 继承写法：子类名 extends 父类名；子类名 implements 父接口名；子接口名 extends 父接口名。

- 子类自动获得父类的所有字段，严禁定义与父类重名的字段

- 子类不能继承父类的静态属性，但可以对父类静态属性操作

- 子类能不能访问父类字段要看父类字段的访问修饰符

- 任何class的构造方法，第一行语句必须是调用父类的构造方法。如果没有明确地调用父类的构造方法，编译器会帮我们自动加一句super();

- 从Java 15开始，允许使用sealed修饰class，并通过permits明确写出能够从该class继承的子类名称

- 如果一个父类的方法本身不需要实现任何功能，仅仅是为了定义方法签名，目的是让子类去覆写它，那么，可以把父类的方法声明为抽象方法；
    - 因为这个抽象方法本身是无法执行的，所以，父类也无法被实例化，因而这个父类也必须申明为抽象类

- 如果一个抽象类没有字段，所有方法全部都是抽象方法，就可以把该抽象类改写为接口

#### 继承关系判断

```java
// 判断实例是否为某个类型：（子）实例 instanceof （父）类型
Assertions.assertTrue("handle" instanceof Object);

  // 判断能子类型能否向上转型：父类型类实例.isAssignableFrom(子类型类实例);
Assertions.assertTrue(Object.class.isAssignableFrom(Integer.class));
```

- 从Java 14开始，判断instanceof后，可以直接转型为指定变量，避免再次强制转型。

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

### 操作文件

#### 读取文件

- 读取resources目录下的文件

```java
// 通过class.getResourceAsStream，要加前缀/
InputStream input = Xxx.class.getResourceAsStream("/fileName");
// 通过classLoader.getResourceAsStream，不需要加前缀
InputStream input = Xxx.class.getClassLoader().getResourceAsStream("fileName");
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

#### String

##### 字符串常量池

- 保证字符串变量s指向的是字符串常量池中的数据的两种方式
    - 1.字面量定义的方式：String s = "hello";
    - 2.调用intern方法：String s = xxxString.intern();

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

- 防止精度丢失，更推荐使用`new BigDecimal(String val)`构造方法来创建对象；
- `BigDecimal.valueOf(double val)`静态方法创建对象，当数值有效位数很多的时候，会有科学计数的精度问题。
- 等值比较应该用compareTo()方法，而不是equals()方法；因为equals()方法会比较值和精度，而compareTo()方法比较的时候会忽略精度

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

##### 定义和使用泛型

```java
public class ClassName<T> {
    private T field;

    public T getField() {
        return field;
    }
}
```

- 定义泛型方法
  
```java
public class ClassName {
    public <T> T function(T t) {
        return t;
    }
}
```

- 定义泛型类的对象
  
```java
泛型类名<具体类型> object = new 泛型类名<具体类型>();
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

#### 队列

- 队列的方法

|操作|throw Exception|返回false或null|
|:-|:-|:-|
|添加元素到队尾| add(E e)| boolean offer(E e)|
|取队首元素并删除| E remove()| E poll()|
|取队首元素但不删除| E element()| E peek()|

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
|添加元素到队尾| add(E e) / offer(E e) |addLast(E e) / offerLast(E e)|
|取队首元素并删除| E remove() / E poll() |E removeFirst() / E pollFirst()|
|取队首元素但不删除| E element() / E peek() |E getFirst() / E peekFirst()|
|添加元素到队首| 无 |addFirst(E e) / offerFirst(E e)|
|取队尾元素并删除| 无 |E removeLast() / E pollLast()|
|取队尾元素但不删除| 无 |E getLast() / E peekLast()|

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

### 多线程

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

一个计数信号量。

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

| 方法名                                                    | 描述                                            |
|:------------------------------------------------------ |:--------------------------------------------- |
| runAsync(Runnable runnable)                            | 使用ForkJoinPool.commonPool()作为它的线程池执行异步代码，无返回值 |
| runAsync(Runnable runnable, Executor executor)         | 使用指定的thread pool执行异步代码，无返回值                   |
| supplyAsync(`Supplier<U>` supplier)                    | 使用ForkJoinPool.commonPool()作为它的线程池执行异步代码，有返回值 |
| supplyAsync(`Supplier<U>` supplier, Executor executor) | 使用指定的thread pool执行异步代码，有返回值                   |

- CompletableFuture的实例方法

| 方法名                                 | 描述                                                                        |
|:----------------------------------- |:------------------------------------------------------------------------- |
| exceptionally                       | 执行异步代码发生异常时执行，如果有返回值，可以指定一个异常发生时的返回值，另外需要注意exceptionally方法会返回一个新的CompletableFuture，更推荐链式调用的方式定义exceptionally方法                                                            |
| whenComplete                        | future完成或异常时执行                                                            |
| handle                              | 如果在链式调用的时候抛出异常，则可以在最后使用handle来接收                                          |
| thenRun                             | 无返回值的future执行成功后执行                                                        |
| thenAccept                          | 有返回值的future执行成功后执行                                                        |
| runAfterBoth                        | 等待两个无返回值的future完成，然后执行                                                    |
| thenAcceptBoth                      | 等待两个有返回值的future完成，然后执行                                                    |
| thenCompose                         | step1.thenCompose() 将 step1 的结果作为输入，对两个异步操作进行流水线，返回新的CompletableFuture，阻塞 |
| thenApply                           | step1.thenApply() 将 step1 的结果作为输入，同一个CompletableFuture，不阻塞                |
| thenCombine                         | 两个有返回值的future完成，然后合并其结果                                                   |
| join                                | 获取future返回结果，阻塞                                                           |
| acceptEither                        | 两个future任意一个执行完毕，无返回值                                                     |
| applyToEither                       | 两个future任意一个执行完毕，有返回值                                                     |
| runAfterEither                      | 两个future任意一个执行完毕，无返回值                                                     |
| allOf                               | 等待所有future都执行完毕，无返回值                                                      |
| anyOf                               | 任意个future只要一个成功，返回值类型为Object                                              |
| complete(T t)                       | 完成异步执行，并返回future的结果                                                       |
| completeExceptionally(Throwable ex) | 抛出future执行异常                                                              |

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
- CPU利用率：cpuUsage = (0, 1]
- 等待时间：waitTime
- 计算时间：computeTime
- 建议线程池大小：`threadSize = cpuNumbers * cpuUsage * (1 + waitTime / computeTime)`
- 建议设置线程大小上限：maxThreadSize = 100

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

##### Spring的线程池

- 自带优雅关闭线程池的骚操作

###### 配置文件

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

###### SimpleAsyncTaskExecutor  

- 创建虚拟线程池

```java
@Slf4j
@Setter
@ConfigurationProperties("thread.pool.task.executor")
@Configuration
public class ThreadPoolConfiguration {
    private int maxPoolSize;

    @Bean
    public SimpleAsyncTaskExecutor simpleAsyncTaskExecutor() {
        // 不能像ThreadPoolTaskExecutor那样自定义异常处理，只可以设置最大线程数
        SimpleAsyncTaskExecutor simpleAsyncTaskExecutor = new SimpleAsyncTaskExecutor();
        simpleAsyncTaskExecutor.setVirtualThreads(true);
        simpleAsyncTaskExecutor.setConcurrencyLimit(maxPoolSize);
        return simpleAsyncTaskExecutor;
    }
}

@Resource
private SimpleAsyncTaskExecutor simpleAsyncTaskExecutor;

@Test
public void test() {
    simpleAsyncTaskExecutor.execute(() -> System.out.println("hello virtual thread"));
    simpleAsyncTaskExecutor.execute(() -> System.out.println(Thread.currentThread().isVirtual()));
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

### 动态代理

从JVM角度来说，动态代理是在运行时动态生成类字节码，并加载到JVM中。

#### JDK原生动态代理

缺点：只能代理实现了接口的类。
<br/>
核心：InvocationHandler接口和Proxy类。

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

#### CGLIB动态代理

Maven依赖

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>${cglib.version}</version>
</dependency>
```

CGLIB是一个基于ASM的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。<br/>
缺点：CGLIB通过继承方式实现代理，因此不能代理终结类和终结方法
<br/>
核心：MethodInterceptor接口和Enhancer类

- 1.定义一个类

```java
public class UserServiceImpl {
    public void save(String name) {
        System.out.println("save user: " + name);
    }
}
```

- 2.实现MethodInterceptor接口并重写intercept方法，intercept用于拦截增强被代理类的方法

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

// 数组元素不支持增删
List<Integer> list1 = Arrays.asList(array);

// 数组元素支持增删，数据量不大时适用
List<Integer> list2 = new ArrayList<>(Arrays.asList(array));

// 数组元素支持增删，数据量大时推荐使用
List<Integer> list3 = new ArrayList<>(array.length);
Collections.addAll(list3, array);
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

| 函数式接口 | 函数描述符 | 原始类型特化 |
|:--------- | --------- | ----------- |
| `Predicate<T>` | T -> boolean | IntPredicate, LongPredicate, DoublePredicate |
| `Consumer<T>`       | T -> void        | IntConsumer, LongConsumer, DoubleConsumer |
| `Function<T,R>`     | T -> R           | `IntFunction<R>`, `LongFunction<R>`, `DoubleFunction<R>`,`ToIntFunction<T>`, `ToLongFunction<T>` `ToDoubleFunction<T>`, IntToLongFunction, IntToDoubleFunction, LongToIntFunction, LongToDoubleFunction |
| `Supplier<T>`       | () -> T          | BooleanSupplier, IntSupplier, LongSupplier, DoubleSupplier |
| `UnaryOperator<T>`  | T -> T           | IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator|
| `BinaryOperator<T>` | (T,T) -> T       | IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator |
| `BiPredicate<L,R>`  | (L,R) -> boolean ||
| `BiConsumer<T,U>`   | (T,U) -> void    | `ObjIntConsumer<T>`, `ObjLongConsumer<T>`, `ObjDoubleConsumer<T>` |
| `BiFunction<T,U,R>` | (T, U) -> R      | `ToIntBiFunction<T, U>`, `ToLongBiFunction<T, U>`, `ToDoubleBiFunction<T, U>` |

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

| 正则表达式    | 规则           | 可以匹配                          |
|:-------- |:------------ |:----------------------------- |
| `A`      | 指定字符         | `A`                           |
| `\u548c` | 指定Unicode字符  | `和`                           |
| `.`      | 任意字符         | `a`，`b`，`&`，`0`               |
| `\d`     | 数字0~9        | `0`~`9`                       |
| `\D`     | 非数字          | `a`，`A`，`&`，`_`，……            |
| `\w`     | 大小写字母，数字和下划线 | `a`~ `z`，`A`~ `Z`，`0`~`9`，`_` |
| `\W`     | 非`\w`        | `&`，`@`，`中`，……                |
| `\s`     | 空格、Tab键      | 空格，Tab                        |
| `\S`     | 非`\s`        | `a`，`A`，`&`，`_`，……            |

#### 多个字符的匹配规则

| 正则表达式     | 规则      | 可以匹配                 |
|:--------- |:------- |:-------------------- |
| `A*`      | 任意个字符   | 空，`A`，`AA`，`AAA`，……  |
| `A+`      | 至少1个字符  | `A`，`AA`，`AAA`，……    |
| `A?`      | 0个或1个字符 | 空，`A`                |
| `A{n}`    | n个字符    | `AAA`                |
| `A{m, n}` | m-n个字符  | `AA`，`AAA`           |
| `A{n, }`  | 至少n个字符  | `AA`，`AAA`，`AAAA`，…… |
| `A{0, n}` | 最多n个字符  | 空，`A`，`AA`，`AAA`     |

#### 复杂匹配规则

| 正则表达式      | 规则         | 可以匹配                          |
|:---------- |:---------- |:----------------------------- |
| ^          | 开头         | 字符串开头                         |
| $          | 结尾         | 字符串结束                         |
| [ABC]      | […]内任意字符   | A，B，C                         |
| [A-F0-9xy] | 指定范围的字符    | `A`，……，`F`，`0`，……，`9`，`x`，`y` |
| [^A-F]     | 指定范围外的任意字符 | 非`A`~`F`                      |
| AB\|CD\|EF | AB或CD或EF   | `AB`，`CD`，`EF`                |

### 异常

- 当某个方法抛出了异常时，如果当前方法没有捕获异常，异常就会被抛到上层调用方法，直到遇到某个try {...} catch {...} finally {}被捕获为止

#### 捕获异常

- 捕获异常使用try {...} catch {...} finally {} 语句，把可能发生异常的代码放到try中，然后使用catch捕获对应的Exception及其子类

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

- 创建一个keystore

```sh
# storepass 指定口令
# genkeypair 生成公钥/私钥对
# keyalg 指定加密算法
# keysize 指定密钥长度/位数
# sigalg 指定签名算法
# validity 指定有效天数
# alias 指定证书别名
# keystore 指定keystore名称
# dname 最重要的CN=www.sample.com指定了Common Name，如果证书用在HTTPS中，这个名称必须与域名完全一致
# 此命令在当前目录创建一个my.keystore文件，并存储创建成功的一个私钥和一个证书到文件中
# 有了keystore存储的数字证书，我们就可以进行加解密和签名
keytool -storepass handle123 -genkeypair -keyalg RSA -keysize 2048 -sigalg SHA256withRSA -validity 180 -alias mycert -keystore my.keystore -dname "CN=www.handle.com.cn, OU=handle, O=handle, L=BJ, ST=BJ, C=CN"
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

| 字符  | 表示       |
|:--- |:-------- |
| <   | `&lt;`   |
| >   | `&gt;`   |
| &   | `&amp;`  |
| "   | `&quot;` |
| '   | `&apos;` |

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

### 部署

#### 手动生成jre

```sh
# 1.打开cmd（权限不够时用管理员身份打开cmd）
# 2.进入某个目录，生成的jre会在这个目录下
cd 指定目录

# 3.生成jre
jlink.exe --module-path jmods --add-modules java.base --output jre
```

#### 普通springboot发布

- 1.复制jre、可执行jar文档、run.bat（springboot项目看需求创建config文档夹，里面放application.properties配置文档），到同一目录下即可
- 2.运行run.bat快速启动可执行jar文档

#### 生成可执行文件graalvm

`关闭360`

```sh
mvn -Pnative native:compile

# 如果报Execution of ..\jdk-xxx\bin\native-image.cmd @target\tmp\native-image-xxxxxxxxxx.args returned non-zero result
native-image @target\tmp\native-image-xxxxxxx.args
```

## Java高级

### JVM

- 类加载器子系统
    - 加载阶段
        - 引导类加载器
        - 扩展类加载器
        - 系统类加载器
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

加载扩展类加载器和系统类加载器，并指定为它们的父加载器

##### 扩展类加载器

Java语言编写，是sun.misc.Launcher的内部类ExtClassLoader，间接继承于ClassLoader

父类加载器为引导类加载器

加载`java.ext.dirs`系统属性指定的目录中的类库，或者`JDK家目录/jre/lib/ext`中的类库。如果用户创建的jar放在此目录下，也会由扩展类加载器加载

##### 系统类加载器

Java语言编写，是sun.misc.Launcher的内部类AppClassLoader，间接继承于ClassLoader

父类加载器为扩展类加载器

加载环境变量classpath或系统属性`java.class.path`指定路径下的类库

我们的应用程序的类加载器默认是系统类加载器

是用户自定义的类加载器的默认父类加载器

通过ClassLoader的getSystemClassLoader()方法可以获取到该类加载器

##### 自定义类加载器

在日常开发中，类的加载几乎是由上述三种类加载器想回配合执行的。必要时，还可以自定义类加载器

自定义类加载器要继承ClassLoader

自定义类加载器可以实现类库的动态加载，加载源可以是本地jar包，也可以是网络上的远程资源

自定义类加载器还可以实现绝妙的插件机制，如OSGI组件框架，Eclipse的插件机制。其为应用程序提供了一种动态增加新功能的机制，这种机制无需重新打包发布应用程序就能实现

同时，自定义类加载器能够实现应用隔离，如Tomcat、Spring等中间件和组件框架都在内部实现了自定义类加载器，并通过它们隔离不同的组件模块

##### 获取类加载器的途径

```java
// 通过某个类的Class实例获取类加载器
clazz.getClassLoader()

// 获取当前线程上下文的类加载器，默认情况下它就是系统类加载器
Thread.currentThread().getContextClassLoader()

// 获取系统类加载器
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
    - 被扩展类加载器和系统类加载器加载的类型在运行期间不太可能被卸载，因为扩展类加载器的实例或系统类加载器的实例基本上在整个运行期间总能直接或间接访问得到，它们不可达的可能性极小
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
|L`ClassName;`|reference|  an instance of class ClassName|
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

每当为方法分配栈帧时，JVM需要开辟一块额外空间作为操作数栈，存放用于计算的操作数及其返回结果

执行每一条指令之前，JVM要求该指令的操作数已经入栈

在执行指令时，JVM会将该指令的操作数弹出，并将执行指令的结果重新入栈

操作数栈也是一个槽4个字节，做为一个单元

#### JVM参数

- `-X`表示JVM的运行参数（非标准参数）

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

# 表示eden/两个survivor的比例，默认8:1:1（但是如果不显式指定比例，就算加上-XX:-UseAdaptiveSizePolicy，默认的比例也是不生效的）
-XX:SurvivorRatio=8

# 关闭（关闭用减号，开启用加号）自适应的内存分配策略
-XX:-UseAdaptiveSizePolicy
```

##### 指定对象晋升到老年代的年龄阈值

```properties
# 默认是15
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
# 元空间的初始大小
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

```properties
-XX:+UseGCLogFileRotation 
-XX:NumberOfGCLogFiles=50 
-XX:GCLogFileSize=100m
-Xloggc:/.../gc.log
```

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

##### 在OOM时，打印输出

```properties
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=你要输出的日志路径
```

##### 其它jvm设置

```sh
# 字符串常量池是一个哈希表
# 设置字符串常量池大小（jdk21默认65536，最小128）
-XX:StringTableSize=65536

# 打印字符串常量池统计信息
-XX:+PrintStringTableStatistics

# 设置栈内存大小
-Xss1024K

# 打印GC简要信息1
-XX:+PrintGC

# 打印GC简要信息2
-verbose:gc

# 打印GC简要信息3
-Xlog:gc

# 打印GC详情1
-XX:+PrintGCDetails

# 打印GC详情2
-Xlog:gc*

# 输出GC信息到指定文件
-Xlog:gc:文件名

# 输出GC详情到控制台，并且包含时间信息
-Xlog:gc*:stdout:time

# 开启TLAB空间（TLAB在Eden空间中，每个线程有自己的TLAB空间）
-XX:+UseTLAB

# 设置TLAB空间占Eden空间的百分比，默认TLAB仅占整个Eden空间的1%
# 一旦对象在TLAB空间分配内存失败，JVM就会使用加锁机制，在Eden空间分配内存
-XX:TLABWasteTargePercent

# 查看所有参数的默认初始值
-XX:+PrintFlagsInitial

# 查看所有参数的最终值（可能存在修改，不再是初始值）
-XX:+PrintFlagsFinal

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

- 内存泄露（Memory Leak）：狭义上，对象不会再被程序用到，但是GC又不能将其回收的情况；广义上，不规范的对象声明和定义，导致其生命周期变得很长最终导致OOM的情况

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
# G1垃圾收集器
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
```

###### ZGC

- ZGC采用标记-复制算法，不过 ZGC 对该算法做了重大改进

```sh
# 使用ZGC垃圾收集器，并启用分代ZGC功能
-XX:+UseZGC -XX:+ZGenerational
```

###### 查看默认的垃圾收集器

```sh
# 查看命令行相关参数
-XX:+PrintCommandLineFlags

# 查看具体垃圾收集器参数的值
jinfo -flag 垃圾收集器参数（如：UseG1GC） 进程id
```

#### 性能调优工具

- JDK命令行

```sh
# 查看当前运行的java进程和进程id
jps

# 查看进程内存使用和垃圾收集信息
jstat -gc 进程id

# 查看java进程某个参数的值（最终值）
jinfo -flag 参数名（比如NewRatio） 进程id
```

##### 获取dump文件

- 方式1: 使用jmap

```sh
# 通过jps查看进程id
jps

# 生成dump文件
jmap -dump:format=b,live,file=dumpfile.bin 进程id
```

- 方式2: 使用visualvm导出

##### visualvm

- 可导出dump文件

##### MemoryAnalyzer(MAT)

- 可打开dump文件查看GC Roots

##### jconsole

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
        <mirrorOf>central</mirrorOf>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/repositories/central</url>
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

| 依赖范围 | 编译有效 | 测试有效 | 运行有效 | 打包有效 | 例子 |
|:- |:- |:- |:- |:- |:- |
| compile | true | true | true | true | spring-core |
| test | false | true | false | false | junit |
| provided | true | true | false | false | lombok |
| runtime | false | true | true | true | 数据库驱动 |
| system | true | true | false | false | 本地maven仓库之外的类库，基本用不到 |

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

Maven配置文件允许我们配置不同环境的构建设置，例如开发、测试和生产

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
<br/>
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

- 5.修改`MVND_HOME\mvn\conf\settings.xml` 配置文件

- 6.测试`mvnd -v`

#### Idea配置Maven Daemon

- IntelliJ IDEA 2024.3 (Community Edition)还没支持Maven Daemon，通过Maven Helper插件来使用

- 点击`File->Settings->Other Settings->Maven Helper`
    - 勾上`Use a Terminal to run goals, with a custom mvn command`
    - 并指定命令为`mvnd`，然后关闭再打开Idea，如果不行就直接指定命令的全路径为`MVND_HOME\bin\mvnd.cmd`

- 使用：右键项目名->Run Maven选择对应指令执行即可

### Gradle

- 官网：<https://gradle.org/>

#### 安装Gradle

- 参考Spring Boot支持的Gradle版本

- 参考Idea支持的Gradle版本：`idea_home\plugins\gradle\lib`

- 参考Gradle支持的jdk版本，需要先安装jdk并配置好

- 下载完整版（包含了文档和源码）

- 修改maven下载源
    - 在`GRADLE_HOME/init.d`目录下创建init.gradle文件，内容如下

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

| 符号 | 原符号| 替换符号 |
| ---  | ---- | ------- |
| 小于 | < | `&lt;` |
| 小于等于 | <= | `&lt;=` |
| 大于 | > | `&gt;` |
| 大于等于 | >= | `&gt;=` |
| 不等于 | <> | `&lt;&gt;` |
| 与 | & | `&amp;` |
| 单引号 | ' | `&apos;` |
| 双引号 | " | `&quot;` |

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

## PageHelper

官网<https://github.com/pagehelper/Mybatis-PageHelper>

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

- person.yml

```yml
user01:
    name: 张三
```

- 将配置文件中person.name的值封装到Person对象

```java
@Component
@PropertySource(value = "classpath:person.yml")
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

3.定义.定义配置类

```java
@Configuration
@ImportResource("classpath:spring.xml")
public class MainConfiguration {}
```

#### DI注解

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

- 假设配置文件application.yml存在配置

```yml
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
@PropertySource(value = "classpath:person.yml")
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

- JSR-303是Java提供，hibernate实现的，springmvc支持这套实现

#### 定义实体类

```java
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

#### 定义控制器

```java
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
 * pid存放到指定目录：在application.properties(yml)配置文档中添加spring.pid.file=/var/log/app.pid
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

- 作用：配置绑定，将类中所有属性和配置文件中的相关配置进行绑定，默认从application.properties(.yml)文件中获取值，不用写@Value
  
- 属性：prefix：取前缀为xxx的属性

- 此注解所在的类必须是一个组件，在类上注解

- 可以给集合类型赋值

- 如果是properties文件，并且内容有中文时需要用@PropertySource指定编码格式，并且文件名不能是application.properties

假设配置文件application.yml存在配置

```yml
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

// @ConstructorBinding 构造函数注入只能提取application.properties(.yml)文件中的值
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

### 命令启动

```sh
java -jar [选项] [参数] <jar文件名>
```

- 指定系统属性-D<属性名>=<属性值>，可以通过System.getProperty()在应用程序获取该属性值
    - 指定服务器端口：`-Dserver.port=80`
    - 指定要激活的配置文件：`-Dspring.profiles.active=<dev/test/prod>`

- 指定JVM参数`-X`
    - 设置最大堆内存：`-Xmx1024m`
    - 设置最小堆内存：`-Xms512m`

## Spring Security

认证 (Authentication)： 是验证用户的身份的凭据（例如用户名/用户ID和密码），通过这个凭据，系统得以知道用户是谁

授权 (Authorization)： 发生在认证之后，授予了什么权限，有权限干什么

### 身份验证方案

#### Session-Cookie方案

1. 用户成功登录系统后，服务器为用户创建一个Session并存储起来（Redis），然后返回给客户端具有SessionID的Cookie

2. 当用户向后端发起请求的时候会把SessionID带上，后端对其和数据库（Redis）中的SessionID进行比对

要确保客户端开启了Cookie，还要注意Session的过期时间

如果客户端禁用了Cookie，可以对SessionID进行一次加密，然后通过放到请求参数或者请求体的方式传给后端

- Cookie无法防止CSRF（Cross Site Request Forgery，跨站请求伪造）攻击，而Token可以
    - 如果点击了非法链接，会拿到Cookie干坏事
    - Token存放在浏览器的localStorage，非法链接拿不到这个Token

- 无论Cookie还是Token都无法避免XSS（Cross Site Scripting，跨站脚本攻击，为了跟层叠样式表（Cascading Style Sheets，CSS）的缩写区别）攻击

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

### Gateway

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

- 解压seata，进入conf文件夹下，参考application.example.yml，修改application.yml

[application.yml](/yml/application.yml)

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
        - /handle/data/seata/application.yml:/seata-server/resources/application.yml
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

## Jackson

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

- 自定义Jackson配置类：[JacksonConfiguration](/java/JacksonConfiguration.java)

- ObjectMapper是线程安全的，因此可以定义一个Jackson工具类，避免每次注入Jackson

```java
// 1.定义工具类
public final class JacksonUtil {
    private static ObjectMapper objectMapper;

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return objectMapper.writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType)
    throws JsonProcessingException, JsonMappingException {
        return objectMapper.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef)
            throws JsonProcessingException, JsonMappingException {
        return objectMapper.readValue(content, valueTypeRef);
    }
}

// 2.初始化工具类的objectMapper
@Component
public class JacksonUtilInitializer {
    @Autowired
    private ObjectMapper objectMapper;

    @PostConstruct
    public void init() throws NoSuchFieldException, SecurityException, IllegalArgumentException, IllegalAccessException {
        Field field = JacksonUtil.class.getDeclaredField("objectMapper");
        field.setAccessible(true);
        field.set(null, objectMapper);
    }
}

// 或者直接通过构造方法注入
@Component
public final class JacksonUtil {
    private static ObjectMapper objectMapper;

    private JacksonUtil(ObjectMapper objectMapper) {
        JacksonUtil.objectMapper = objectMapper;
    }

    public static String writeValueAsString(Object value) throws JsonProcessingException {
        return objectMapper.writeValueAsString(value);
    }

    public static <T> T readValue(String content, Class<T> valueType)
    throws JsonProcessingException, JsonMappingException {
        return objectMapper.readValue(content, valueType);
    }

    public <T> T readValue(String content, TypeReference<T> valueTypeRef)
            throws JsonProcessingException, JsonMappingException {
        return objectMapper.readValue(content, valueTypeRef);
    }
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

- Red Hat系列Linux

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

- 对于容器卷，docker统一放在主机的`/var/lib/docker/volumes/卷名`目录下

```sh
# 目录映射目录映射带/
-v /data/docker/registry:/tmp/registry
# 卷映射，卷名前面不带/，卷映射对于配置文件不用提前复制一份到主机目录，目录映射要
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

### Docker Compose

#### 编写compose.yml

```yml
# 应用名
name: myserver
services: 
    # 服务名，要启动多少个就写多少个
    mysql:
        # 容器名，不指定则为：应用名-服务名-数字-（1-n)
        container_name: mysql
        # 镜像
        image: mysql:8.0
        # 端口 
        ports:
            - "3306:3306"
        # 环境变量
        environment:
            MYSQL_ROOT_PASSWORD: mysql123
        volumes:
            # 如果是卷名要在顶级元素volumes里面声明
            - mysql-data:/var/lib/mysql
            - /app/myconf:/etc/mysql/conf.d
        # 随系统启动
        restart: always
        # 网络，要在顶级元素networks里面声明，一个应用可以加入多个网络，
        networks: 
            - mynet
        # 依赖：比如要先启动redis才能启动mysql
        depends_on:
            - myredis
    myredis:
networks: 
    mynet: 
        # 不指定则为：应用名-newwork名
        name: mynetname
volumes: 
    mysql-data: 
        # 不指定则为：应用名-volume名
        name: myvolumename
configs: 
# 密钥
secrets: 
```

#### 使用compose.yml启动/下线

```sh
# 指定compose文件名批量新建容器并以后台方式启动
docker compose -f mycompose.yml up -d

# 批量移除容器并删除相关资源（如果不在参数中指定不会删除自定义挂载的目录/卷）
docker compose -f mycompose.yml down

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

## elasticsearch

- 官网：<https://www.elastic.co/cn/elasticsearch>

### 安装elasticsearch

- 下载tar.gz文件并解压到指定目录
- 修改conf/elasticsearch.yml文件

```yaml
xpack.security.enabled: false
```

- elasticsearch不允许使用root启动，先创建elasticsearch用户

```sh
# 创建elasticsearch用户，然后根据提示指定密码
adduser elasticsearch
# 将elasticsearch的根目录权限赋给用户
chown -R elasticsearch yourpath/elasticsearch-8.15.3

# 切到elasticsearch用户
su elasticsearch
```

- 启动elasticsearch

```sh
# 先进入elasticsearch的bin目录
cd yourpath/elasticsearch-8.15.3/bin
# 启动elasticsearch
./elasticsearch
```

- 测试，访问：<http://localhost:9200/>

## git

### 安装

```sh
# 安装完成后，还需要最后一步设置，因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址
# global参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

# 然后查看git配置，看看刚刚的设置正确没有
git config --global --list

# 生成ssh key，-t ed25519表示指定密钥类型为Ed25519，rsa不再是首选了
 ssh-keygen -t ed25519 -C "这里填你的邮箱"
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
        - /handle/data/postgresql:/var/lib/postgresql/data
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

#### Linux安装MySQL

```sh
# 查看是否安装了mysql相关的组件
rpm -qa | grep mariadbMySQL

# 卸载mysql相关组件
rpm -e --nodeps mariadb-libs
```

#### Docker安装MySQL

```sh
# 安装
docker pull mysql:8.0.29

# 启动
docker run -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=mysql123 -d mysql:8.0.29 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

#### 登录

标识密码的p要小写，指定端口号的P要大写

```sql
mysql -u root -p

-- 指定ip和端口号登录
mysql -u root -p -h 127.0.0.1 -P 3306

-- 远程访问数据库
-- 修改host字段的值为需要远程连接数据库的主机ip地址或者直接修改成%
-- host的值为'%'表示所有主机可以通过该用户访问数据库
update user set host='%' where user='dbadmin';
flush privileges;
```

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
# UTF-8会自动映射为MySQL的utf8mb4
spring.datasource.url=jdbc:mysql://localhost:3306/handle?useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=mysql123
```

#### 数据类型

char: 定长，非常适合存储密码的MD5值

varchar: 字符串列的最大长度比平均长度大很多，列的更新很少时使用

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

读未提交、读已提交、可重复读、可序列化

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

#### 锁

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

##### 共享锁和排它锁

```sql
-- 共享锁
select ... lock in share mode;

-- 排他锁
select ... for update;
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

##### 修改配置

- 配置文件：[redis.conf](/file/redis/redis.conf)

- daemonize no 改成 daemonize yes
- protected-mode yes 改成 protected-mode no，不然Java程序连接不上
- bind 127.0.0.1，表示只能本机访问，需要注释掉或者改成本机的ip地址，不然会影响远程ip连接
- 去掉对requirepass的注释，并且指定登录密码

- 配置部分注释如下

```conf
# 绑定可以连redis数据库的ip，多个ip用空格隔开
bind 127.0.0.1 192.168.31.8


# daemonize:yes:redis采用的是单进程多线程的模式。当redis.conf中选项daemonize设置成yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。
# daemonize:no: 当daemonize选项设置成no时，当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(putty,xshell等)都会导致redis进程退出。
daemonize yes

# daemonize设置为yes时，redis将进程id写入到pidfile指定的文件中
pidfile /var/run/redis_6379.pid

# 指定redis监听接收连接请求的端口
port 6379

# 客户端空闲了没有给redis发送消息命令也不断开连接
timeout 0

# 60秒检查一次客户端是否存活
tcp-keepalive 60

# 日志级别
loglevel notice

# 日志文件
logfile "/var/redis.log"

# 是否把日志输出到系统日志
#syslog-enabled no

# 指定redis在系统日志里面的日志标志
#syslog-ident redis

# 指定系统日志设备，值必须为 USER 或 LOCAL0-LOCAL7之一
# syslog-facility local0

# redis默认有16个数据库
databases 16

save 3600 1
save 300 100
save 60 10000

dbfilename dump.rdb

appendonly yes
appendfilename "appendonly.aof"
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
docker pull redis:7.4
```

- compose.yaml

```yaml
redis:
    image: redis:7.4
    container_name: redis01
    ports:
        - "6379:6379"
    volumes:
        - /handle/data/redis/data:/data
        - /handle/data/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf
    networks: 
        - my-docker-net
    restart: always
```

- 4.连接redis客户端

```sh
# 方法1
docker exec -it redis01 /bin/bash
# 方式2
docker exec -it redis01 redis-cli
```

- 5.测试redis

```sh
ping
```

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

#### 在 `java` 中使用 `jedis`

命令行中操作redis的指令在jedis中都有对应的方法实现

- maven依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>4.2.2</jedis.version>
</dependency>
```

- jedis连接与基本使用

```java
@Test
public void testJedis() {
    Jedis jedis = new Jedis("www.laodeli.top", 6379);

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

- 官网：<http://nginx.org/>

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

# make
make

# make install
make install
```

### 启动nginx

```sh
# 进入nginx的sbin目录
cd /usr/local/bin/nginx/sbin
# 启动nginx
./nginx

# 快速停止
./nginx -s stop

# 优雅关闭，nginx在退出前完成已经接收的连接请求
./nginx -s quit

# 重新加载配置
./nginx -s reload
```

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

打开`Window->Preferences`

##### 通用设置

- 设置字体和字号，General->Apperance->Colors and Fonts->Basic->Text Font，点击`Edit`进行设置

- 设置自动保存，General->Content Types->Text，在`Default encoding` 输入`UTF-8`，然后点击`Update`

- 设置properties文件编码，General->Editors->Autosave
- General->Editors->Text Editors
    - 设置Tab键插入空格，勾上`Insert spaces for tabs`
    - 显示行号，勾上`Show line numbers`

- 自动刷新文件夹改动，General->Workspace,勾上`Refresh using native hooks or polling`
    - 设置源文件编码，`Text file encoding`设置为UTF-8
    - 设置换行符，`New text file line delimiter`设置为Unix
    - 手动构建自动保存，Build，勾上`Save automatically before manual build`

##### Java设置

- 设置编译器语法版本，Java->Compiler，`Compiler compliance level`设置为自己的Java版本

- 设置代码补全，Java->Editor->Content Assist，
    - 取消只有按回车才触发代码补全提示，取消勾选`Disable insertion trigger except 'Enter'`
    - 不使用静态导入，取消勾选`Use static imports`
    - 设置代码补全触发器，`Auto activation triggers for Java`覆盖填入`.qwertyuiopasdfghjklzxcvbnm`
    - 启动Java后缀模板提示，Advance，勾上`Java Postfix Template Proposals`

- 自定义模板，Java->Editor->Templates
    - `.sout` 自动补全
![](images/code-template1.png)
    - `fori` 自动补全
![](images/code-template2.png)

- 设置JRE，Java->Installed JREs，点`Add`添加自己的Java版本

##### Maven设置

- 设置Maven，Maven->Installations，点`Add`添加自己的Maven版本

- 设置Maven配置文件，Maven->User Settings，设置为自己的..\settings.xml

##### XML配置

- 设置缩进，XML->XML Files->Editor，选择`Indent using spaces`，`Indentation size`设置为4

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

### IDEA

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

## 格式文件

### Yaml

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

### Markdown

```md
# 图片
![可选的图片描述，当图片不能被显示时而出现的替代文字](图片相对路径 "鼠标悬置于图片上会出现的文字，可以不写")

# 链接
[超链接显示名](超链接地址 "超链接title，当鼠标悬停在链接上时会出现的文字")
```

## 前端篇

[前端笔记](/file/subfile/front%20end.md "点击链接查看前端笔记")

## Linux篇

### Linux目录结构

- / 根目录，在此目录下创建其它目录

- /bin (usr/bin usr/local/bin) binary的缩写，存放着最经常使用的命令

- /sbin (usr/sbin usr/local/sbin) 存放着系统管理员使用的系统管理程序

- /home 存放着普通用户的主目录，在Linux中每个用户都有一个自己的目录，一般该目录名是用户的账号

- /root 系统管理员的用户主目录

- /lib 系统开机所需要最基本的动态链接共享库，类似Windows的dll文件，几乎所有的应用程序都需要用到这些共享库

- /lost+found 这个目录一般情况下是空的，系统非法关机后，这里就存放了一些文件

- /etc 所有的系统管理所需要的配置文件和目录，比如mysql的my.conf

- /usr 用户的很多应用程序和文件都放在这个目录下，类似Windows的program files目录

- /boot 存放启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件

- /proc 不能动，这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息

- /srv 不能动，service的缩写，该目录存放一些服务启动之后需要提取的数据

- /sys 不能动，这是linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs

- /tmp 用来存放一些临时文件

- /dev device的缩写，类似Windows的设备管理器，把所有的硬件用文件的形式存储

- /media linux系统会自动识别一些设备，例如U盘、光驱等，当识别后，Linux会把识别的设备挂载到这个目录下

- /mnt 系统提供该目录是为了让用户临时挂载别的文件系统，我们可以将外部的存储挂载在/mnt上，然后进入该目录就可以查看里面的内容了

- /opt 给主机额外安装软件存放的目录，如mysql、jdk等的安装文件

- /usr/local 也是安装软件所安装的目录，一般是通过编译源码方式安装的程序，存放的是可执行程序

- /var 存放着在不断扩充的东西，习惯将经常被修改的目录放在这里，包括各种日志文件

- /selinux security-enhanced linux，是一种安全子系统，它能控制程序只能访问特定文件，有三种工作模式，可以自行设置

### 安装Linux系统

- 选Basic Web Server

手动设置三个分区

#### `boot`分区

- 分区大小：`1g`
- 设备类型：`标准分区`
- 文件系统：`ext4`

#### `swap`分区

- 分区大小：`内存多大就设置多大`
- 设备类型：`标准分区`
- 文件系统：`swap`

#### `/`分区

- 分区大小：`总空间 - boot分区大小 - swap分区大小`
- 设备类型：`标准分区`
- 文件系统：`ext4`

- 生产环境还需设置开启kdump

### Linux软件安装

#### 安装`*.src.rpm`形式的源代码软件包

安装

```sh
# 将源代码编译并在/usr/src/dist/RPMS下生成二进制的rpm包
rpm -rebuild *.src.rpm
```

cd /usr/src/dist/RPMS
rpm -ivh*.rpm

卸载：rpm -e packgename
说明：rpm --rebuild*.src.rpm命令将源代码编译并在/usr/src/dist/RPMS下生成二进制的rpm包，然后再安装该二进制包即可。packgename如前所述，两种方法如下：

法1：
rpm -i your-package.src.rpm
cd /usr/src/redhat/SPECS
rpmbuild -bp your-package.specs           #一个和你的软件包同名的specs文件
cd /usr/src/redhat/BUILD/your-package/    #一个和你的软件包同名的目录
./configure                    #这一步和编译普通的源码软件一样，可以加上参数
make
make instal
法2：
rpm -i you-package.src.rpm cd /usr/src/redhat/SPECS  #前两步和方法一相同
rpmbuild -bb your-package.specs   #一个和你的软件包同名的specs文件
这时在/usr/src/redhat/RPM/i386/（根据具体包的不同，也可能是i686,noarch等等）在这个目录下，有一个新的rpm包，这个是编译好的二进制文件。
rpm -i new-package.rpm即可安装完成。

#### 安装`*.tar.gz/*.tgz`或`*.bz2`形式的源代码软件包

- 解压文件

```sh
# 解压文件
tar -zxvf *.tar.gz
# 先解压然后进入解压后的目录
tar -jxvf *.tar.gz
```

- 配置安装路径

```sh
./configure --prefix=安装目标路径
```

- 编译

```sh
make
```

- 安装

```sh
make install
```

- 卸载

```sh
make uninstall
```

- 彻底删除安装路径

```sh
rm -rf 安装目标路径
```

### Linux常用命令

#### Linux帮助命令

```sh
# 获得命令/配置文件的帮助信息
man 命令/配置文件

# 获得shell内置命令的帮助信息
help 命令
```

#### 重启和关机

- 立即重启，`shutdown -r now` 或 `reboot` 或 `systemctl reboot`

- 立即关机，`shutdown -h now` 或 `halt` 或 `systemctl poweroff`

- 一分钟后关机，`shutdown` 或 `shutdown -h 1`

- 把内存数据同步到磁盘，`sync`

注意：虽然shutdown/reboot/halt等命令已经在关机前进行了`sync`，我们在重启或关机前最好还是执行一次`sync`

#### 登录和注销

登录时尽量少用root账号登录，避免操作失误；当权限不够时，可以通过切换用户命令切换为管理员身份

- 切换用户

```sh
# 从权限高的用户切换到权限低的用户，不需要输入密码，反之需要输入密码
# 切换用户后返回到原来用户，使用 exit/logout
su - 用户名
```

- 注销，`logout`

注意：`logout`在运行级别3以下有效，在图形运行级别无效

#### 用户管理

- 添加用户

```sh
# 创建用户成功后，会自动创建和用户名相同的家目录: /home/用户名
useradd 用户名

# 创建新用户
# 创建该用户的家目录，将该家目录的所有者设置为该用户
useradd -m 用户名

# 创建新用户并指定家目录
useradd -d 指定目录 用户名
```

- 指定/修改用户密码

```sh
passwd 用户名
```

- 删除用户

```sh
# 删除用户，但是保留用户的家目录
userdel 用户名

# 删除用户及用户的家目录，谨慎使用
userdel -r 用户名
```

- 查询用户信息

```sh
id 用户名
```

- 查看当前用户信息（是登录用户，非切换后的用户）

```sh
whoami/who am i
```

#### 用户组

用户组类似于角色，系统通过用户组对有共性（权限）的用户进行统一管理

- 新增用户组

```sh
groupadd 用户组名称
```

- 删除用户组

```sh
groupdel 用户组名称
```

- 添加用户时指定用户组，如果不指定则系统默认创建一个和用户同名的组，并将用户添加到组中

```sh
useradd -g 用户组名称 用户名
```

- 修改用户所属的用户组

```sh
usermod -g 用户组名称 用户名
```

#### 用户和组相关文件

- /etc/passwd文件 用户的配置文件，记录用户的各种信息

每行的含义：用户名:口令(x):用户标识号(uid):组标识号(gid):注释性描述:主目录(家目录):登录Shell(一般是Bash shell)

- /etc/shadow文件 口令的配置文件

每行的含义：登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志

- /etc/group文件 用户组的配置文件，记录Linux包含的组的信息

每行的含义：组名:口令(x):组标识号(uid):组内用户列表

#### 运行级别

运行级别说明：

- 0：关机

- 1：单用户【找回丢失密码】

- 2：多用户状态无网络服务

- 3：多用户状态有网络服务

- 4：系统未使用，保留给用户的运行级别

- 5：图形界面

- 6：系统重启

- 常用运行级别是3和5，可以指定默认运行级别

- 切换运行级别

```sh
init 运行级别
```

- CentOS 7 在/etc/inittab文件中进行了简化

multi-user.target:analogou to runlevel 3
graphical.target.target:analogou to runlevel 5

```sh
# 获取当前机器默认运行级别
systemctl get-default

# 设置当前机器默认运行级别
systemctl set-default multi-user.target
```

#### 找回root密码

- 重启系统，在开机界面按“e”进入编辑界面

![开机界面](/images/开机界面.png)

- 在编辑界面光标往下移动，定位到“linux16”开头最在行的末尾，输入`init=/bin/sh`，完成后按“Ctrl + X”进入单用户模式

![编辑界面](/images/编辑界面.png)

- 在光标闪烁的位置输入`mount -o remount,rw /`，完成后按`回车`

- 在新的一行输入`passwd`，完成后按`回车`

- 然后输入新密码，完成后按`回车`；然后再次输入新密码，完成后按`回车`；显示passwd...的样式，说明密码修改成功

- 在光标闪烁的位置输入`touch /.autorelabel`，完成后按`回车`

- 在光标闪烁的位置输入`exec /sbin/init`，完成后按`回车`，等待系统自动修改密码，过程可能有点长，完成后系统会自动重启，就可以输入密码登录了

![单用户模式界面](/images/单用户模式界面.png)

#### 文件目录命令

- 常用目录

```sh
# 当前目录
./

# 当前目录的上一级目录
..

# 家目录
~
```

- 显示当前所在目录的绝对路径

```sh
pwd
```

- 展示当前目录下的文件和目录

```sh
# -a，展示当前目录下所有的文件和目录，包括隐藏的
# -l，以列表的方式显示信息
ls [-a|-l] 文件/目录
```

- 切换到指定目录

```sh
cd 目录

# 切换到当前目录上一级目录
cd ..
# 切换到上上级目录
cd ../..
# 切换到家目录
cd ~
```

- 创建空文件

```sh
touch 文件名称
```

- 新建目录

```sh
# mkdir默认创建一级目录，通过指定选项 -p 创建多级目录
mkdir [-p] 目录名称
```

- 删除空目录

```sh
rmdir [可选项] 目录名称
```

- 删除非空目录（谨慎操作）

```sh
# -r：递归，-f：强制
rm [可选项] 目录/文件

# 删除文件hello.txt
rm /code/hello.txt

# 强制删除整个hello目录不提示
rm -rf /hello
```

- 复制目录/文件到指定目录

```sh
# \ 强制覆盖不提示 -r 递归整个目录
[\]cp [-r] 源目录/文件 目的目录

# 复制文件hello.txt到file目录下
cp /code/hello.txt /file

# 复制整个code目录到file目录下
cp -r /code /file
```

- 移动/重命名目录/文件

```sh
# 当旧目录与新目录一样时，就是重命名
mv 旧文件名 新文件名

# 当源目录/文件与目的目录/文件名称不一样时，就是移动并重命名
mv 源目录/文件 目的目录/文件
```

- cat查看文件内容

```sh
# -n 显示行号，cat只能浏览不能修改，一般带上管道命令 "| more"，将cat命令的处理结果交给more命令处理
cat [可选项] 文件名称

cat -n /etc/profile | more
```

#### more命令

more命令是基于vi编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容

```sh
more 文件名称
```

|快捷键|功能|
|:-|:-|
|空格|下一页|
|回车|下一行|
|ctrl + f|下一屏|
|ctrl + b|上一屏|
|=|当前行号|
|:f|文件名和当前行号|
|q|退出more|

#### less命令

```sh
less  文件名称
```

less命令用来分屏查看文件内容，功能比more强大。less根据显示需要加载内容，对于大型文件具有更高的效率。

|快捷键|功能|
|:-|:-|
|空格|下一页|
|pagedown|下一页|
|pageup|上一页|
|/字符串|向下查找字符串，n向下查找；N向上查找|
|?字符串|向上查找字符串，n向下查找；N向上查找|
|q|退出less|

#### head命令

head用于显示文件的开头部分内容，默认前10行

```sh
# 查看文件前[任意行]内容
head [-n 任意正整数]  文件名称
```

#### tail命令

用于显示文件的末尾部分内容，默认末尾10行

```sh
# 查看末尾[任意行]内容
tail [-n 任意正整数]  文件名称

# 实时追踪该文档的所有更新
tail -f 文件名称
```

#### eccho命令

用于输出内容到控制台

```sh
echo [可选项] 输出内容

# 输出环境变量（要大写）
echo $PATH

# 输出字符串
echo "hello world"
```

#### >命令

输出重定向（覆盖）

```sh
# 将输出到控制台的字符串重定向（覆盖写）到hello.txt（文件不存在会自动创建）
echo "hello world" > hello.txt
```

#### >>命令

追加

```sh
# 将输出到控制台的字符串追加写到hello.txt
echo "hello world" >> hello.txt
```

#### ln命令

软链接（符号链接），类似于windows里的快捷方式，存放了链接到其它文件的路径

```sh
# 给目录/文件创建一个软链接
ln -s 目录/文件 软链接名称

# 删除链接
rm 链接名称
```

#### history命令

查看执行过的历史命令

```sh
# 显示最近执行过的任意条命令
history [任意正整数]

# 执行对应历史编号的命令
!历史命令编号
```

#### 网络命令

- 网卡配置

```sh
# 查看ip地址信息1
ip addr

# 查看ip地址信息2
ifconfig

# 进入对应网卡文件进行修改
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3

# 重启网络
service network restart
```

#### 系统命令

```sh
# 显示操作系统的发行版号
uname -r

# 显示系统名、节点名称、操作系统的发行版号、内核版本等信息
uname -a

# 查看linux系统版本，适用于Redhat的Linux
cat /etc/redhat-release

# 总体内存占用，-m 用Mb单位来显示
free -m
```

#### 日志命令

```sh
# 查看关于nginx的系统日志
cat /var/log/messages | grep nginx
```

#### 压缩/解压文件

##### tar.gz文件

```sh
# 将目标文件夹压缩
tar -zcvf tar.gz文件名 目标文件夹

# tar.gz文件解压到当前目录
tar -zxvf tar.gz文件名

# tar.gz文件解压到目标路径
tar -zxvf tar.gz文件名 -C 目标路径
```

##### zip文件

```sh
# 解压到当前目录
unzip file.zip

# 解压到指定目录
unzip file.zip -d /path/to/directory
```

- 查看端口有没有被某个进程占用

```sh
lsof -i:6379
```

#### 防火墙命令

```sh
# 关闭防火墙1
service firewalld stop

# 关闭防火墙2
systemctl stop firewalld

# 禁止防火墙开机启动
systemctl disable firewalld

# 重启防火墙
firewall -cmd --reload

# 查看防火墙状态1
service firewalld status

# 查看防火墙状态2
systemctl status firewalld 

# 放行端口
firewall -cmd --zone=public --add -port=80/tcp --permanent

```

#### 应用/端口查询命令

```sh
# 查看应用有没有启动
ps -ef|grep 应用名

# 查看应用占用的端口
netstat -tunlp|grep 应用名
```

#### 日期/时间类命令

```sh
# 显示当前时间（年月日时分秒和星期）
date

# 显示当前年
date +%Y

# 显示为：年-月-日 时:分:秒
date "+%Y-%m-%d %H:%M:%S"

# 设置时间
date -s "2025-10-28 15:20:00"

# 显示当前日历信息（只显示本月日历）
cal

# 显示当前日历信息（显示2025年的日历）
cal 2025
```

#### 查找命令

##### find

从指定目录向下递归遍历其各个子目录，将满足条件的文件或者目录显示在终端

|选项|功能|
|:-|:-|
|-name `文件名`|按照指定文件名查找文件|
|-user `用户名`|查找属于指定用户的所有文件|
|-size `文件大小`|按照指定大小查找文件|

```sh

find 指定目录 可选选项

# 前缀：+、-，表示大于、小于，不加前缀表示等于
# 单位：k、M、G
fine /home -size +10M
```

##### locate

快速定位文件路径

locate指令利用事先建立的系统中所有文件名称及路径的locate数据库实现快速定位

```sh
# 在第一次执行locate前，必须使用updatedb命令创建locate数据库
# 之后每次有新增、删除或者移动文件的操作，需要再次执行这个命令
updatedb

locate 文件名
```

##### which

查看指定命令在哪个目录下

```sh
which ls
```

##### grep命令和管道符号`|`

grep：过滤查找

`|`：将`|`左边命令的处理结果传递给右边的命令处理

|选项|功能|
|:-|:-|
|-n|显示匹配行及行号|
|-i|忽略字母大小写|

```sh
grep 可选选项 查找内容 源文件

# 如下两个命令的功能一样
grep "handle" /home/handle/hello.txt
cat /home/handle/hello.txt | grep "handle"
```

### vi/vim编辑器

vim就是vi的增强版

- 打开文件`vi 文件名`或`vim 文件名`

- 从一般模式进入编辑模式，`i`

- 从一般模式进入命令模式，`:`或`/`

- 从编辑/命令模式进入一般模式`esc`

- 保存退出，`:wq`

- 退出，`:q`

- 强制退出，不保存，`:q!`

- 复制当前行`yy`，复制当前光标所在行及其向下的共n行，`nyy`

- 粘贴，`p`

- 删除当前行`dd`，复制当前光标所在行及其向下的共n行，`ndd`

- 撤销，`u`

- 跳到文档开始行，`gg`

- 跳到文档末尾行，`G`

- 跳转到第n行，`n shift g`

- 显示行号，`:set nu`

- 隐藏行号，`:set nonu`

- 查找，`/关键字`，按回车开始查找，按`n`查找下一个

### Red Hat系列Linux

#### yum工具

```sh
# 搜索软件
yum search 软件名

# 安装软件
yum install 软件名

# 卸载软件
yum remove 软件名

# 升级软件
yum update 软件名

# 升级操作系统所有软件及内核，更新所有已安装包到最新版本，但会移除已废弃或替代的依赖包，可能影响系统稳定性
yum upgrade 

# 升级操作系统所有软件及内核，更新所有已安装包到最新版本，但保留旧的依赖包，避免潜在破坏系统
sudo yum update

# 查询可用jdk
yum search java|grep jdk

# 从查询结果中选择jdk安装
yum install java-1.8.0-openjdk-devel.x86_64
```

### Ubuntu

- 查看系统版本信息

```sh
lsb_release -a
```

### Arch

#### pacman

```sh
# -S：同步
# y： 从服务器下载最新的软件包数据库（相当于更新索引）
# u： 升级所有已安装的软件包到最新版本
# 安装软件前先执行这一命令
sudo pacman -Syu

# 从远程仓库拉取软件安装
sudo pacman -S 软件名

# 安装本地.pkg.tar.zst安装包
sudo pacman -U path/to/pkg.tar.zst安装包

# 卸载软件
# R：删除指定包
# s：删除不需要的依赖（仅限于没有其他包需要的依赖）
# n：删除配置文件
sudo pacman -Rns package_name

# 搜索远程仓库的某个软件
pacman -Ss 软件关键字

# 搜索已安装的某个软件
pacman -Qs 软件关键字

# 查看已安装的所有软件
pacman -Q

# 列出包组的所有应用
sudo pacman -Sg kde-applications

# 选择安装包组里面的应用，默认全部安装，可以根据交互选择应用对应的数字，如果安装多个用空格隔开，来自定义安装
sudo pacman -S kde-applications
```

#### 安装教程

- 下载Arch Linux系统镜像文件并校验：<https://archlinux.org/download/>

- 下载Rufus（Portable版本，免安装）：<https://rufus.ie>，制作U盘启动

- 关闭BIOS的安全启动模式，不然可能会提示安全引导违规

- 终端键盘布局和字体

```sh
# 列出可选的键盘布局
localectl list-keymaps

# 设置键盘布局为美式键盘（默认）
loadkeys us

# 设置当前终端的字体，立马见效，觉得当前终端字体不合适可以自行修改
# 分辨率为1080p的屏幕，建议选择 ter-122b
# 分辨率更高的屏幕，可以在 ter-124b，ter-128b，ter-132b 中选择一个, 默认lat9w-16
setfont lat9w-16
```

- 检查是否为UEFI启动，ArchLinux只支持UEFI模式启动，输出为64就行了

```sh
cat /sys/firmware/efi/fw_platform_size
```

- 连接网络，这里连接wifi为例

```sh
# 确认是否启用了网络接口
ip link

# 执行iwctl命令，进入wifi连接交互式命令行
iwctl

# 查看帮助
help

# 列出网卡设备，无限网卡一般为wlan0，后面以它为例
device list

# 让网卡扫描wifi设备
station wlan0 scan

# 列出可连接的wifi
station wlan0 get-networks

# 连接wifi，然后根据提示输入wifi密码
station wlan0 connect 上一步获取的某个wifi名称

# 退出wifi连接交互式命令iwctl
exit

# 测试网络是否连接成功
ping www.baidu.com 
```

- 更新系统时间

```sh
# 更新系统时间是有必要的，因为下载软件是服务器会验证系统时间，如果时间不正确，可能出现下载失败的情况
# 将系统时间与网络时间进行同步
timedatectl set-ntp true 
# 查看系统时间状态，检查是否成功 看到（System clock synchronized :yes）这一句就是成功了
timedatectl status   
```

- 创建分区

```sh
# 列出设备和分区信息，记住要安装arc linux系统的设备，一般通过容量判断比较直观
fdisk -l

# 选择要分区的设备
fdisk /dev/the_disk_to_be_partitioned

# 首先输入g创建 GPT 分区表
g 

# 随后输入n创建新的分区
n

# 然后根据终端提示，输入分区编号，然后enter
# 然后根据终端提示，输入第一扇区，这一步保持默认，直接enter
# 然后根据终端提示，指定分区大小，+512M表示创建512M的分区
# 然后输入t改变分区类型，根据提示输入类型名称或者代码
t

# 依次创建efi分区（512M），类型为EFI System；swap分区（和内存同样的大小），类型为linux swap；/分区（设备容量剩余多少就设置多少），类型为linux filesystem

# 查看分区
p

# 保存并退出分区
w
```

- 格式化分区

```sh
# 格式化efi系统分区为fat32
mkfs.fat -F 32 /dev/efi_system_partition

# 格式化swap分区
mkswap /dev/swap_partition

# 格式化/分区为ext4
mkfs.ext4 /dev/root_partition
```

- 挂载分区

```sh
# 要先挂载跟分区
mount /dev/root_partition /mnt

# 挂载efi系统分区，使用--mkdir创建/mnt/boot目录，并将efi分区挂载在/mnt/boot目录下
mount --mkdir /dev/efi_system_partition /mnt/boot

# 挂载swap分区
swapon /dev/swap_partition
```

- 配置镜像源，不然安装会很慢

```sh
# 用vim或nano编辑文件/etc/pacman.d/mirrorlist
vim /etc/pacman.d/mirrorlist
nano /etc/pacman.d/mirrorlist

# 在文件中加上如下国内镜像源，一般加一个就行了，加在文件中的Server行前面
Server = https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.sjtug.sjtu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

- 安装基础包

```sh
# base，linux，linux-firmware分别是基础包组，linux内核和驱动程序
# Arch linux 官方提供了 linux，linux-lts，linux-zen，linux-hardened内核，但是对于初学者，只推荐使用linux内核
# 如果使用的是2019年及以后生产的笔记本电脑，可能需要安装sof-firmware，否则可能没有声音
pacstrap -K /mnt base linux linux-firmware
```

- 生成fstab系统文件

```sh
# 生成fstab系统文件，它决定了系统启动时如何自动挂载分区。没有fstab，系统将找不到根分区，从而无法启动
#  -U：是以UUID的描述方式生成 fstab，>>：是将输出结果追加在后面的文件末尾
genfstab -U /mnt >> /mnt/etc/fstab
```

```sh
# 使用arch-chroot工具切换到新安装的系统，以后的操作就可以在新安装的系统中完成了，执行完成终端用户目录的~会变成/
arch-chroot /mnt

# 时区 /usr/share/zoneinfo/Region/City，可输入/usr/share/zoneinfo后按tab键查看可选时区
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 同步硬件时钟
hwclock --systohc

# 本地化

# 需要先安装vim和terminus-font，terminus-font是终端字体，后面设置FONT=ter-122b需要依赖这个字体，-S：sync
pacman -S vim terminus-font

# 编辑/etc/locale.gen，删除#en_US.UTF-8 UTF-8行和#zh_CN.UTF-8 UTF-8行的注释#并保存
vim /etc/locale.gen

# 生成locale
locale-gen

# 创建并编辑/etc/locale.conf，输入LANG=en_US.UTF-8并保存
vim /etc/locale.conf

# 创建并编辑/etc/vconsole.conf，设置KEYMAP=us,FONT=ter-122b，这样新系统启动时终端字体（大小）就变成设置的了
vim /etc/vconsole.conf

# 设置主机名
vim /etc/hostname

# 安装网络管理器（不安装则需要手动配置网络）
pacman -S networkmanager

# 设置网络管理器开机启动
systemctl enable NetworkManager.service

# 设置root用户的密码，根据提示输入密码
passwd
```

- 安装引导程序

```sh
# 查看cpu型号
cat /proc/cpuinfo | grep "model name"

# 如果是intel的cpu，安装 intel-ucode
pacman -S intel-ucode

# 如果是amd的cpu，安装 amd-ucode
pacman -S amd-ucode

# 安装grub
pacman -S grub
# 安装efibootmgr
pacman -S efibootmgr

# 安装grub到计算机
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB

# 生成grub配置
grub-mkconfig -o /boot/grub/grub.cfg

# 退出chroot环境，执行完成终端用户目录的/会变成~
exit

# 取消挂载/mnt
umount -R /mnt

# 重启
reboot

# 如果安装了NetworkManager，先连接网络，这里以wifi为例
# 查看可用 Wi-Fi 网络
nmcli device wifi list

# 连接到指定 Wi-Fi 网络
nmcli device wifi connect "上面列出的Wifi的SSID" password "wifi连接密码"

# 查看连接状态
nmcli connection show --active

# 设置开机自动连接
nmcli connection modify "上面列出的Wifi的SSID" connection.autoconnect yes
```

##### 安装KDE桌面环境

KDE由桌面环境Plasma、KDE Frameworks、KDE Applications三部分组成

但是安装时只用安装Plasma和KDE Applications，会自动安装依赖库KDE Frameworks

###### 安装xorg

```sh
# 应该是不用单独安装的，笔者想要卸载的时候提示sddm依赖到它,安装sddm的时候应该会将其自动安装
# 安装xorg图形支持
# xorg-server， X11 图形服务器，负责窗口显示和图形渲染
sudo pacman -S xorg-server
```

###### 安装plasma和kde-applications

只安装了plasma-desktop plasma-workspace plasma-nm plasma-pa kwin kscreen konsole dolphin ，笔者的火狐浏览器最大最小化按钮不显示，motrix下载器无法接管火狐浏览器下载，后面完整安装了就没问题了

后面安装了完整gnome，切到了gnome桌面，然后又安装了完整的plasma，然后切回kde桌面，然后设置了window decorations，缩放设置回100%，这两个问题就好了，莫名其妙

- 一次性安装plasma和kde-applications

```sh
# 安装完整的KDE软件，先用着，找到更好的就卸载
# 其实可以安装完整的plasma，kde-applications可以指定安装某一些就行了
sudo pacman -S plasma kde-applications
```

- 只安装plasma的部分应用

```sh
# plasma-desktop，桌面壳，包含面板、菜单、任务栏等基本界面
# plasma-workspace，Plasma 会话管理器、启动器、设置中心
# plasma-nm，网络的托盘图标和系统设置
# plasma-pa，图形音量控制器（托盘小喇叭）
# kwin，窗口管理器，负责窗口动画、特效、布局
# kscreen,显示设置，支持设置分辨率、缩放等，不安装的话系统设置里面Display & Monitor选项置灰
sudo pacman -S plasma-desktop plasma-workspace plasma-nm plasma-pa kwin kscreen
```

- 只安装kde-applications的部分应用

```sh
# konsole，终端模拟器，支持标签、透明、快捷键
# dolphin，文件管理器，支持标签、网络挂载、批处理等
sudo pacman -S konsole dolphin 
```

###### 安装SDDM（图形登录管理器）

完整的plasma包含了sddm-kcm（sddm的kde桌面配置工具），还需要自行安装SDDM

```sh
sudo pacman -S sddm

# 启用SDDM开机启动
sudo systemctl enable sddm
```

###### 创建非root用户

sddm登录界面只默认显示一个非root用户给你输入密码，然后登录进入桌面，因此这里要先创建一个非root用户

```sh
# 创建非root用户，这里以handle为例
useradd handle

# 指定handle的登录密码
passwd handle

# 创建handle的家目录
mkdir -p /home/handle

# 将handle的家目录的权限赋给handle
chown -R handle:handle /home/handle

# 或者一步创建用户和家目录，然后指定密码就行了
useradd -m 用户名
```

###### （root用户）安装sudo

sudo在整个系统的使用过程中，使用频率是非常高的，不装只能通过`su - root`切换超级用户进行操作，非常麻烦

```sh
# 安装 sudo
pacman -S sudo
```

- 配置 sudo 权限，让wheel组的用户（普通用户）都能用sudo

```sh
# 如果直接用 vim /etc/sudoers，一旦写错，可能导致系统无法使用 sudo，必须进 root 修复
# 因此这里临时指定使用 vim 编辑器来打开并编辑 sudoers 文件
# visudo 会在保存前检查语法，防止你把 sudo 配坏
# 找到这行： # %wheel ALL=(ALL:ALL) ALL然后取消注释，保存退出
EDITOR=vim visudo

# 用户加入 wheel 组，然后重启，新的组权限才会生效，这里以上面创建的handle为例
# usermod 修改用户账户的命令
# -a append，追加组（不移除已有组）
# -G wheel 指定要加入的组是 wheel
usermod -aG wheel handle
```

###### GRUB设置(也可以在任意时间设置)

```sh
# 打开文件
sudo vim /etc/default/grub

# 找到并设置如下两个属性，就可以默认快速开机不用等待了，需要的时候按ESC弹出菜单选项
GRUB_TIMEOUT=0
GRUB_TIMEOUT_STYLE=hidden

# 然后更新GRUB配置
grub-mkconfig -o /boot/grub/grub.cfg
```

###### 安装音频支持（推荐 PipeWire），安装完后重启

```sh
# pipewire 核心服务 音频/视频服务器，替代 PulseAudio 和 JACK，支持低延迟和多设备
# pipewire-pulse 兼容层 提供 PulseAudio API 兼容性，让旧程序（如 Firefox、Steam）能正常输出声音
# wireplumber 会话管理器 管理设备热插拔、权限、策略，替代旧的 pipewire-media-session
# pipewire-audio 模块扩展 提供额外音频模块支持（如 PCM、蓝牙、JACK 桥接），增强兼容性
# alsa-utils 调试工具 提供 aplay、alsamixer、speaker-test 等命令行工具，用于声卡识别与音量调试，声音正常后无需调试了可卸载
# sof-firmware 驱动固件 Intel 声卡 DSP 所需固件，适用于 Tiger Lake、Ice Lake、Alder Lake 等平台（非 Intel 可跳过）
sudo pacman -S pipewire pipewire-pulse wireplumber pipewire-audio alsa-utils sof-firmware

# 其中3个设置为开机自动启动，并立即启动服务
# 其他组件无需启动，它们是工具或驱动，安装即可
systemctl --user enable pipewire pipewire-pulse wireplumber --now
```

###### 安装显卡驱动

- 先启用 multilib 仓库，它是Arch官方提供的32位兼容库仓库，Steam、Wine、某些游戏需要它

```sh
# 编辑/etc/pacman.conf配置文件
sudo vim /etc/pacman.conf

# 找到这两行，取消注释，保存退出
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

- 根据显卡类型安装显卡驱动

```sh
# 这一步可能还要摸索，笔者装了英特尔和英伟达的后，重启黑屏了，然后切换tty又装了optimus-manager，然后重启又正常了

# 安装 GPU 驱动（根据显卡选择，虚拟机可以不用执行）
# NVIDIA（闭源）
# nvidia NVIDIA 官方驱动模块
# nvidia-utils 包含 nvidia-smi、OpenGL/Vulkan 支持
# nvidia-settings 图形化控制面板（可选）
sudo pacman -S nvidia nvidia-utils nvidia-settings

# AMD（开源）
sudo pacman -S xf86-video-amdgpu mesa

# Intel（开源）
# mesa OpenGL 支持
# libva-intel-driver 视频加速（VA-API）
# vulkan-intel Vulkan 支持（如游戏、图形加速）
sudo pacman -S mesa libva-intel-driver vulkan-intel

# 最后我好像还安装了
sudo pacman -S lib32-nvidia-utils
```

- 安装 AUR（yay）

```sh
sudo pacman -Syu

# 先安装base-devel 和 git
sudo pacman -S --needed base-devel git

# 检查有没有安装go包（编译器）
go version

# 如果没有安装则安装
sudo pacman -S go

# 以下用指令非root用户执行
cd ~
git clone https://aur.archlinux.org/yay.git
cd yay

# 有可能因为网络原因导致安装失败，因此先修改PKGBUILD文件
vim PKGBUILD
# 找到 build() 函数，在多条 export 语句后追加保存退出
export GO111MODULE=on
export GOPROXY=https://goproxy.cn

# makepkg 使用当前目录下的 PKGBUILD 构建一个 Arch 包
# -s 自动安装构建所需的依赖（resolve dependencies）
# -i 构建完成后自动安装生成的 .pkg.tar.zst 包
makepkg -si

# 测试 yay 是否安装成功
yay --version
```

- 安装切换显卡工具

```sh
# 安装完重启
yay -S optimus-manager

# 命令行切换显卡，nvidia, integrated, hybrid，分别为独显、集显和混合模式
# 切换到 Nvidia GPU
optimus-manager --switch nvidia
```

###### 安装火狐浏览器

```sh
sudo pacman -S firefox

# 启动火狐浏览器
firefox
```

- 安装中文字体支持，避免浏览器中文乱码，下面四种字体基本够用了

```sh
# noto-fonts-cjk：Google 出品，覆盖简体、繁体、日文、韩文，现代网页首选
# wqy-zenhei：文泉驿正黑体，开源中文字体，兼容性好，适合旧网页和轻量界面
# wqy-microhei：更紧凑的文泉驿字体，适合嵌入式或小屏幕
# adobe-source-han-sans-otc-fonts：思源黑体，质量高但体积大，适合美化界面
sudo pacman -S noto-fonts-cjk wqy-zenhei
sudo pacman -S wqy-microhei adobe-source-han-sans-otc-fonts
```

###### 安装Watt Tookit

```sh
# 去官网的github地址下载.tgz版本的软件包
# 解压软件包（同tar.gz)
# 打开软件，找到Settings-General，找到AppData data并跳转到该目录
# 找到该目录的子目录下的文件Plugins/Accelerator/SteamTools.Certificate.cer，一般该位置在~/.local/share/Steam++/Plugins/Accelerator/SteamTools.Certificate.cer
# 将这个cer文件复制到一个另一个目录，不然文件选择弹窗可能找不到上面的默认的位置

# 1.将证书导入到系统
sudo trust anchor --store 上面复制的SteamTools.Certificate.cer的具体路径

# 2.将证书导入到火狐浏览器
# 打开火狐浏览器，进入设置 - 隐私与安全 - 安全 - 证书 - 查看证书 ，选择 证书颁发机构( Authorities )，然后导入上面位置的证书
# 只勾选 信任由此证书颁发机构来标识网站，然后确定导入


# 3.将证书导入到Steam，这个暂时用不到，先不做笔记了

# 最后导入完成后这个复制的cer文件可以删除掉
```

###### 安装v2rayN

```sh
# 下载.appimage格式的v2rayN
# 通过终端启动，可以看到报错缺少fuse
# 安装fuse2，本人是安装fuse3了然后还是报错，就继续装fuse2, 然后发现fuse3卸载不了了
sudo pacman -S fuse2
```

###### 安装输入法fcitx5

```sh
# fcitx5,主程序
# fcitx5-gtk fcitx5-qt，UI开发工具包的输入法模块，如果装有vscode，则必须安装mr，否则输入法会抽风
# fcitx5-configtool，GUI配置程序
# fcitx5-rime,一种可自定义的输入法引擎，但默认情况下其默认配置为拼音。
sudo pacman -S fcitx5 fcitx5-gtk fcitx5-qt fcitx5-configtool，fcitx5-rime

# 配置环境变量（官方建议的，其实一开始笔者没配置也没啥问题发生），在~/.bash_profile中添加如下内容，SDDM+KDE+Wayland的桌面环境只用添加这一行就可以了
# v2rayN和微信输入法需要设置GTK_IM_MODULE、QT_IM_MODULE和SDL_IM_MODULE，虽然官方和电脑提示不需要设置，不要听
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export SDL_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx

# 然后配置输入法
fcitx5-configtool

# 然后在input method添加：Rime
# 然后在input method，下方的configure addons，选择Classic User Interface,然后配置字体大小，然后应用直接见效，不然候选字真的太小了
# 然后在系统设置KeyBoard，找到virtual keyboard，选择Fcitx 5，然后应用就可以了
```

###### 安装输入法ibus-rime,(在vscode下会抽风，所以建议换成fcitx5)

```sh
# 安装
pacman -S ibus-rime

# 然后配置输入法
ibus-setup

# 然后在input method添加：Chinese-Rime
# 然后在系统设置KeyBoard，找到virtual keyboard，选择IBus Wayland，然后应用就可以了
```

###### 安装kwallet

```sh
# kwalletmanager：管理工具
# kwallet-pam：让 KWallet 在登录时自动解锁( 只针对Blowfish模式，gpg不可以)
# 可以管理vscode里面的的账号密码，vscode启动的时候就会提示了
# 安装完成后打开系统设置启动钱包服务，设置Blowfish密码和开机密码一样
# gpg模式还没有官方的登录解锁工具，开机每次都要输入一次密码，麻烦就先不用了
sudo pacman -S kwalletmanager kwallet-pam

# 然后查看文件
cat /etc/pam.d/sddm

# 看看有没有登录解锁，一般kde+sddm都自动配置了
# 这样下次登录时，Blowfish 钱包会随登录密码自动解锁，不再弹窗
-auth       optional    pam_kwallet5.so
-session    optional    pam_kwallet5.so         auto_start
```

#### 安装其它常用软件

```sh
# 压缩/解压软件ark
sudo pacman -S ark

# 截图工具，安装好后到系统设置里面设置截图的快捷方式
sudo pacman -S flameshot

# 草莓音乐播放器
sudo pacman -S strawberry

# vlc音乐/视频播放器
sudo pacman -S vlc
```

##### 安装wine

```sh
# 需要启用multilib仓库来兼容32位的软件运行
sudo pacman -S wine

# 安装/运行exe
wine path/to/exe文件

# 卸载windows程序
wine uninstaller

# 配置
winecfg
```

##### 安装VirtualBox

十分不推荐自己安装.run的软件包

```sh
# 然后根据提示选择virtualbox-host-modules-arch
# 这个包是专门为Arch官方的默认内核linux编译好的VirtualBox内核模块
# 安装它后无需自己编译，也不需要额外安装linux-headers
sudo pacman -S virtualbox

# 如果还需要虚拟机增强功能（共享剪贴板、共享文件夹等），可以安装
sudo pacman -S virtualbox-guest-utils
```

#### 创建菜单/桌面快捷方式

- 如果只想创建桌面快捷方式，则在`~/Desktop`目录下创建`应用名.desktop`文件

- 如果想要在菜单也显示该应用，则在`~/.local/share/applications`目录下创建`应用名.desktop`文件，然后在菜单找到对应的应用名称，鼠标右键-添加到桌面

- 以微信为例，内容如下:

```content
[Desktop Entry]
Type=Application
Name=WeChat
Exec=/home/handle/Applications/WeChat.AppImage
Icon=/home/handle/Applications/WeChat.png
Terminal=false
```

- 如果是wine，则Exec键值对需要做变动

```sh
Exec=wine /path/to/App.exe
```

#### 获取下载文件的哈希码

```sh
sha256sum path/to/下载文件
```

#### .run软件包

.run软件包的卸载不好说，根据具体软件具体分析，下面的例子使用的是virtualbox

十分不推荐自己安装.run的软件包

```sh
# 给.run软件包执行权限
chmod +x path/to/软件包

# 执行安装，可能需要root权限
sudo path/to/软件包

# 卸载
sudo path/to/软件包 uninstall
```

## Windows篇

### cmd命令

```cmd
# 查看端口占用情况
netstat -ano|findstr 端口号

# 根据进程id查看进程信息
tasklist|findstr 进程id

# 关闭进程
taskkill /F /PID 进程id
```

### host文件

hosts文件里可建立许多常用域名与其对应IP的映射。当用户在浏览器中输入一个想要浏览的网址时，系统会首先在hosts文件里面查找有没有对应的IP，若有的话，则会立即打开对应的网页；若是没有，则会请求DNS服务器进行解析

- hosts文件目录为在`C:\Windows\System32\drivers\etc\`

- hosts语法格式 1个IP对应1个主机名或域名，构成一组对应关系。一组对应关系占一行。IP在前，主机名或网址在后；IP与主机名间至少有1个空格。

```hosts
# 这行是注释
127.0.0.1 www.baidu.com
```

- 当我们在文件中写入“127.0.0.1+空格+你想屏蔽的网址”，或者是“0.0.0.0+空格+你想屏蔽的网址”就可以实现该网站的屏蔽
  
- cmd 输入 `ipconfig /flushdns` 让host文件生效

### 校验下载的文件的Hash值

- 在文件所在位置鼠标右键->`Open Git Bash Here`

```sh
# SHA512可以换成别的hash方式，具体看校验的是什么码就用什么方式
certutil -hashfile filename SHA512
```

## 中州韵输入法

### 扩展现有词汇

- 1.新建文件`luna_pinyin_simp.custom.yaml`，加入如下内容

```yaml
patch:
    translator/dictionary: luna_pinyin.extension
```

- 2.新建文件`luna_pinyin.extension.dict.yaml`，在文件末尾追加词条

```yaml
# Rime dictionary
# encoding: utf-8

# 表示yaml文件开始
---
name: luna_pinyin.extension
version: "2025.09.29"
sort: by_weight
use_preset_vocabulary: true
# 從 luna_pinyin.dict.yaml 導入包含單字的碼表
import_tables:
  - luna_pinyin
# 表示yaml文件结束
...

# table begins
# 在下面添加自定義的詞條
垌 dong
㘃 re
```

- 3.将这两个文件放到用户文件夹下，然后重新部署即可

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

## 参考文献

- java 8实战

- [寥雪峰老师的java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)

- [JavaGuide（Java学习&&面试指南）](https://javaguide.cn/home.html)

- [尚硅谷Docker实战教程](https://www.bilibili.com/video/BV1gr4y1U7CY?p=1&vd_source=30f7a9f2f8c5b510e9723e6777dec113)
