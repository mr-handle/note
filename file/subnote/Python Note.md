# Python Note

- python源文件默认是UTF-8编码的

```py
# 注释内容
```

## 数据类型

### 数值

```py
# 数据类型
int
float
Decimal 
Fraction

# 算术运算符：+ - * / %
# 除法运算总是返回浮点数结果
# 如果想要获取整型结果，用//，如下面的例子返回1
5 // 3

# 幂运算：**，如下面的例子返回8
2 ** 3

# 混合类型的操作数的运算，会将整型操作数转为浮点型，如下面的例子返回9.0
4*2.5-1

# 等号用于给变量赋值
width = 3
height = 4

# 还可以使用j或J后缀表示虚数
1+2j

# 对于交互式终端，最后一个表达式的值会赋给变量_，但是这个变量是只读的
# 当你给这个变量赋值时，会创建一个同名的局部变量
```

### 字符串

```py
# 字符串属于str类型，使用单引号或双引号包围都可以
# 唯一区别就是'...'中无需转义"，"..."中无需转义'
s = "hello world"

# 字符串里面要表示单引号或双引号字符，用\转义
'doesn\'t'

# 或者最外面用另外的引号
"doesn't"

# 在交互式终端，Python shell和print的输出可读性方面有所区别
# Python shell输出：'First line.\nSecond line.'
# print(s)输出：
# First line.
# Second line.
s = 'First line.\nSecond line.' 

# 如果不想print的字符串中，\后面的字符被转义，可以这样写，r表示raw strings
print(r'C:\some\name') 

# 多行字符串用一对三个单/双引号包围，这种写法会在字符串的开始、末尾添加换行符，如果不想要换行符，可以在行末添加\
print("""\
first line
second line\
""")

# 可以用+号连接字符串，用*号指定字符串重复次数
# 'ununion'
2 * "un" + "ion"

# 当且仅当两个或多个相互紧挨着的是字符串字面量时，会自动连接
# 当你想要分开写一个长字符串的时候特别有用
# 但是两个或多个相互紧挨着的是字符串变量/字符串表达式（如2 * "un"）和字符串字面量时，必须用+连接
# 'union'
"un" "ion"

# 'union'
s = ("un"
    "ion")

# 字符串可以被索引，第一个字符的索引为0，python没有单独的字符类型，每个字符都是长度为1的字符串
s = "hello world"

# e
s[1]

# 索引也可以是负数，从-1开始，表示从字符串右边开始数
# 索引-0和0是一样的
# r
s[-3]

# 字符串除了可以索引得到单个字符外，还可以切片获得子字符串，半开区间[startIndex, endIndex)
# 字符串切片是半开区间是为了确保s[:i] + s[i:]的结果总是完整的s
# hello
s[0:5]

# 省略的startIndex，表示0
# hello
s[:5]

# 省略的endIndex，表示从startIndex到字符串结束
# world
s[6:]

# 不管索引是正数还是负数，字符串切片都是从左往右切片的
# world
s[-5:]

# 非负索引的切片如果超出了字符串的范围，也不会报错
# world
s[6:20]

# 空串
s[20:]

# 字符串是不可变的，如果给字符串某个索引位置赋值，将报错
s[0] = "H"
s[0:5] = "Hello"

# 获取字符串长度
len(s)
```

### 列表

```py
l = [1, 2, 3]
letters = ["a", "b", "c"]

# 与字符串类似，列表也可以被索引和切片
# a
letters[0]

# c
letters[-1]

# ['b', 'c']
letters[-2:]

# 连接
# ['a', 'b', 'c', 'd', 'e']
# 注意letters还是["a", "b", "c"]
letters + ["d", "e"]

# 修改列表元素值
letters[2] = "C"

# 在列表末尾插入新元素
letters.append("d")

# 将一个列表a赋给另一个变量b时，使用的是浅复制
# 当通过其中一个列表变量修改列表时，其它指向这个列表的变量都能看到改变
newLetters = letters
# newLetters和letters指向同一个对象，true
id(newLetters) == id(letters)

# 列表切片返回一个新列表，但是里面的元素是浅复制的（元素共享）
newLetters = letters[:]

# 但是如果你用赋值操作，元素共享将被破坏，letters[-1]还是原来的值，没有跟着newLetters变
newLetters[-1] = "D"

# 用切片的方式批量修改列表的值
letters = ["a", "b", "c"]

# letters = ['a', 'B', 'C']
letters[1:3] = ["B", "C"]

# letters = ['a']
letters[1:3] = []

# 移除列表所有元素
# letters = []
letters[:] = []

# 获取列表长度
len(letters)

# 嵌套列表
innerList1 = ["a", "b"]
innerList2 = ["c", "d"]
# outerList = [['a', 'b'], ['c', 'd']]
outerList = [innerList1, innerList2]

# ['a', 'b']
outerList[0]

# 'b'
outerList[0][1]
```

## 语法

### 流程控制

流程控制语句靠相同的代码缩进来区分语句块

#### if语句

```py
inputNumber = int(input("请输入一个整数: "))
if inputNumber < 0:
    print("Negative number")
# 可以有0个或多个elif
elif inputNumber == 0:
    print("Zero")
# else也是可选的
else:
    print("Postive number")

inputNumber = int(input("请输入一个整数: "))
if inputNumber not in (-1, 0, 1):
    print("请输入-1, 0, 1这三个数字中的一个")
else:
    print("你输入的数字是: ", inputNumber)
```

#### for语句

```py
letters = ["a", "b", "c"]
for item in letters:
    print(item)

# 如果要在迭代集合的时候修改集合
users = {"Jack": "male", "Handle": "male", "Anna": "female"}

# 方法1: 创建一个新集合，并添加符合条件的元素
femaleUsers = {}
for userName, gender in users.items():
    if gender == "female":
        femaleUsers[userName] = gender

print(femaleUsers)

# 方法2: 迭代该集合的副本
for userName, gender in users.copy().items():
    if gender == "male":
        del users[userName]

print(users)
```

#### while语句

```py
# 输出小于10的斐波那契序列

# 赋值语句，分别给变量a和b赋值0和1
a, b = 0, 1
while a < 10 :
    # 循环体要用相同的缩进
    # 在交互式终端，循环体要以空行结束
    # print(a)
    # print方法的end参数，可以定义一个结束字符来替代默认输出后的换行
    print(a, end=',')
    # 在赋值前，会按顺序从左到右顺序计算表达式的值
    a, b = b, a + b

```

#### break语法、continue语法和else从句

这种语法在for循环和while循环都支持，下面以for循环为例子

```py
# break，退出该层循环
for i in range(5):
    for j in range(5):
        if i == j:
            print(i, "*", j, "=", i * j)
            break

# continue，退出当次迭代，开始下一次迭代
for i in range(5):
    if i % 2 == 0:
        print(i, " is even number")
        continue
    print(i, " is odd number")


# else从句：只有在for循环“正常结束”（即没有执行过break）时，就执行else的代码
for i in range(5):
    if i % 2 == 0:
        print(i)
        break
else:
    print("no break occurs")
```

#### pass语句

pass语句表示一个空操作，它什么都不做

python是强缩进语言，语法上不允许代码块（比如if的后面）是空的，如果还没想好逻辑，可以用pass先占位

```py
a = 2
if a > 1:
    pass

class MyClass:
    pass

def total(*args):
    pass
```

#### match语句

match看起来像Java的switch，实际上更类似于模式匹配

```py
def weekDictionary(code):
    match code:
        case 6:
            return "Saturday"
        case 7:
            return "Sunday"
        # 也可以用|（表示或）将相同处理逻辑的字面量组合在一起
        case 1 | 2 | 3 | 4 | 5:
            return "Weekday"
        # 变量名_表示匹配任何值
        case _:
            return "Unsupported code"

# 此外还有一些其它例子，目前笔者看官方文档还不是很了解，先记下来，回头再消化
```

### 定义方法

```py
def functionName(arguments):
    """function documentation"""
    # function body
    # return statement

def greet():
    print("Hello, World!")
    # 没有return语句，默认返回None

def power(base, exponent):
    """幂运算"""
    return base ** exponent

# 可以给方法定义别名
alias = power

# 可以用别名调用方法
print(alias(2, 3))

# 给参数设置默认值，在调用的时候，有默认值的参数可以根据情况传参或者不传参
def greet(name= "World"):
    print("Hello ", name)

greet()

# 定义方法官网还有很多其它的特性，可以回头再详细研究
```

### 内置方法

#### print

```py
s = "hello world"
print(s)
```

#### range

```py
# 生成0，1，2
for i in range(3):
    print(i)

# 指定开始生成的数字
l = list(range(0, 3))

# 指定增长步数
l = list(range(0, 3, 2))

# 使用索引遍历
letters = ["a", "b", "c"]
for i in range(len(letters)):
    print(i, letters[i])

# 求和
# 0 + 1 + 2
result = sum(range(3))
```
