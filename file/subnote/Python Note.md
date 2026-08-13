# Python Note

## 安装python包管理器

### uv

包管理器：uv pip

```sh
# archlinux安装uv
sudo pacman -S uv

# 创建一个新项目（python环境）
uv init

# 创建一个环境，比如在项目中删除.venv文件夹后可以执行，这样就重新创建一个环境了
# --python 3.11：指定python版本
# --system-site-packages：继承系统的包，但是`uv pip list` or `uv pip install`不会将系统的包算在其中，但是笔者试了不知道为什么带不进来
uv venv [--python 3.11] [--system-site-packages]

# 根据 pyproject.toml 重新生成 uv.lock 锁定文件
# 这一步只更新锁文件，不安装任何东西
uv lock    

# 更新项目环境：根据 uv.lock 将依赖安装到虚拟环境中
# --default-index "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"：指定国内源
uv sync [--default-index "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"]

# 安装依赖包
# 也可以在安装依赖包的时候指定源，--index-url也可以简写为-i
uv pip install matplotlib --index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple

# 激活环境：打开终端，进入项目根目录，执行如下命令
# 一些ide如vscode在你打开终端的时候会自动执行这个命令
source .venv/bin/activate

# 清空不可访问的缓存（相当于没有项目用到的缓存）
uv cache prune

# 清空所有缓存（包括项目中会用到的缓存）
uv cache clean

# 在新项目中添加依赖
uv add

# 在新项目中删除依赖
uv remove   
```

### miniforge

miniforge可以看成是增强版的miniconda，跟uv一样把它当成包管理器就行了

它用的是mamba命令，conda命令执行的语句都可以将conda替换为mamba

```sh
# archlinux安装miniforge
yay -S miniforge

# 安装完成为当前用户执行
echo "[ -f /opt/miniforge/etc/profile.d/conda.sh ] && source /opt/miniforge/etc/profile.d/conda.sh" >> ~/.bashrc

# 执行完成后会在~/.bashrc中追加下面的命令
# 意思是：如果 /opt/miniforge/etc/profile.d/conda.sh 这个文件存在，那么就把它加载到当前 Shell 中（初始化 conda 环境）
[ -f /opt/miniforge/etc/profile.d/conda.sh ] && source /opt/miniforge/etc/profile.d/conda.sh

# 测试
mamba --version

mamba create -p /path/to/miniforge/envs/cosyvoice -y python=3.10

# 报'mamba' is running as a subprocess and can't modify the parent shell.
# Thus you must initialize your shell before using activate and deactivate.
# 就根据提示如执行下面的命令
eval "$(mamba shell hook --shell bash)"

mamba activate /path/to/miniforge/envs/cosyvoice

# 删除环境
mamba env remove -p /path/to/miniforge/envs/GPTSoVits

# 清理所有未使用的包和缓存
mamba clean -y --all
```

## python知识点

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

# 对于交互式终端，最后一个表达式的值会赋给变量"_"，但是这个变量是只读的
# 当你给这个变量赋值时，会创建一个同名的局部变量
```

### 字符串

```py
# 字符串属于str类型，使用单引号或双引号包围都可以
# 唯一区别就是单引号包围的字符串中的双引号无需转义：'..."...'
# 双引号包围的字符串中的单引号无需转义："...'..."
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

### 模块

一个包含python定义和语句的文件就是一个模块

模块名就是后缀为`.py`的文件名，可以通过`模块名.__name__`得到模块名称

```py
# 创建文件mathUtil.py，定义power方法
def power(base, exponent):
    """幂运算"""
    return base ** exponent

# 在另一个文件main.py中导入mathUtil
import mathUtil

# 调用模块的power方法
print(mathUtil.power(5, 3))

# 打印模块名称
print(mathUtil.__name__)

# 可以给模块方法定义本地别名
power = mathUtil.power

print(power(2, 4))

# 只导入模块的某个方法，多个方法用英文逗号隔开，这种方式mathUtil是未定义的
from mathUtil import power

print(power(5, 3))

# 导入模块的所有方法，除了以下划线为前缀的，不推荐使用
from mathUtil import *

# 设置别名
import mathUtil as alia_math_util 
from mathUtil import power as alia_power

# 在每个解释器会话期间，每一个模块只会导入一次，如果修改了模块，必须重启解释器会话
# 或者，如果你只在交互式会话使用一个模块，则可以
 import importlib; importlib.reload(modulename)
```

#### 把模块当作脚本执行

```py
# 在模块文件的末尾加上
if __name__ == "__main__":
    import sys
    print(power(int(sys.argv[1]), int(sys.argv[2])))

# 当执行下面的命令时，模块名称__name__将被设置为 "__main__"
# 就会执行定义好的代码，这时候这个模块就成了一个脚本
# 但是如果是import mathUtil，就不会执行这些代码
python mathUtil.py 参数1，参数2

# 可以定义通用的入口文件和入口方法如下：
# 定义入口方法
def main():
    pass

# 如果这个文件被直接运行，就执行main()方法
if __name__ == "__main__":
    main()
```

#### 模块搜索路径

当一个模块如mathUtil被导入时，python解释器首先查找内置模块有没有跟导入模块同名的，这些模块名被列在sys.builtin_module_names

如果找不到，再查找所有文件（在sys.path包含的路径中查找）有没有文件名是mathUtil.py的

- sys.path从下面的路径中初始化得来
    - 包含了输入脚本的路径（当没有指定输入脚本文件时，为当前路径）
    - PYTHONPATH（和shell的环境变量PATH一样）
    - 安装依赖默认的路径（包括sit模块管理的site-packages路径）

```py
import sys

print(sys.builtin_module_names)
print(sys.path)

# 使用标准list操作修改sys.path，如添加一个模块搜索路径
sys.path.append('/path/to/python/module')
```

#### “编译”Python文件

为了提升加载模块的速度（不会提升执行速度），python解释器会在__pycache__路径缓存每个模块的编译版本

编译后的模块名称为module.version.pyc，版本一般是python版本

python解释器会检查源文件的修改日期和编译版本，以确认是否需要重新编译

- 有两种情况python解释器不会检查缓存文件
    - 第一，当模块是从命令行加载时，总是会重新编译模块，但是不会保存到缓存
    - 第二，当模块源文件不存在时

#### 标准模块

python自带一个标准模块库，如sys

```py
import sys

# dir方法可以获取一个模块定义的所有名称：变量名、模块名、方法名等
print(dir(sys))

# 列出当前文件定义的名称
print(dir())

# dir()不会列出内置方法的方法名和变量名，如果你想要打印它们，可以用标准模块builtins
import builtins

dir(builtins) 
```

#### 包

包是一种通过使用".模块名称"，来组织模块命名空间的方式

- 比如有目录结构
    - src
        - module
            - __init__.py
            - mathUtil.py
        - main.py

- `__init__.py`文件使得python编译器将module目录视为包
    - 它可以是一个空文件
    - 它也可以执行初始化代码或者设置__all__变量的值

```py
# 可以从包中导入指定的模块，如果不写别名需要通过全限定名称调用：module.mathUtil.power(5, 2)
import module.mathUtil as mathUtil

# 或者这么导入
from module import mathUtil

print(mathUtil.power(5, 2))

# 或者这么导入
from module.mathUtil import power

print(power(5, 2))

# 即使用from package import item，这里的item可以是package的子模块（或者子包）
# 也可以是package里面定义的其它名称，比如方法名，类名或变量名
# 而使用import item.subitem.subsubitem，除了最后的item，必须是包；最后的item可以是模块名或包名，但不能是前一item定义的类名或方法名或变量名


# module/__init__.py文件中可以定义如下变量
# 以便于当使用from package import * 时，导入该变量指定的模块，但是不建议全部导入这种写法
# 如果确定没人使用from package import *这个语句进行模块导入，则不用写
__all__ = ["mathUtil"]

# 如果在module/__init__.py中也定义了mathUtil名称（比如同名方法）
# from module import mathUtil将不会导入mathUtil模块
```

### 类

```py
# 最简单的类定义
class ClassName:
    pass


# 定义一个家庭类
class Family:
    """家庭类"""
    # 类字段
    father = "Handle"

    # 无参构造方法
    # def __init__(self):
    #     pass

    # 有参构造方法
    def __init__(self, mother):
        # 实例字段
        self.mother = mother

    def work(self):
        print(f"{self.father} is working")
        print(f"{self.mother} is cooking")

# 实例化类对象
family = Family("Anna")

# 调用类属性
print(f"class description: {family.__doc__}")
print(f"family's father: {family.father}")
print(f"family's mother: {family.mother}")

# 等价于Family.work(family) ，但不推荐使用这种方式调用实例方法
family.work()

# 类继承写法
class SonClassName(FatherClassName):
    pass
```
