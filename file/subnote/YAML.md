# YAML

- `key:空格value`，标识一对键值对，属性和值都是大小写敏感的

- 以缩进的空格数来控制层级关系

- 只要是左对齐的一列数据，都是同一层级

- 字符串默认不用添加双/单引号，除非有特殊字符
    - 双引号会将特殊字符展开
    - 单引号不会将特殊字符展开，即将所有字符都当成普通字符

```yaml
# first 换行 second
line: "first \n second"

# first \n second，把换行符变成普通的\n了
line: 'first \n second'
```

- 语法

```yaml
# 对象的普通写法
a:
    b:
        c

# 对象的行内写法
a: {b: {c}}

# map普通写法
myMap:
    k1: v1
    k2: v2

# map行内写法
myMap: {k1: v1, k2: v2}

# 数组普通写法
myArrays:
    - red
    - green
    - blue

# 数组行内写法
myArrays: [red, green, blue]

# 数组的元素是对象的普通写法1
myArrays:
    -
        name: handle
        age: 18
    -
        name: Jack
        age: 20

# 数组的元素是对象的普通写法2，行业写法，但是缩进很怪，个人更喜欢写法1
# 可以用写法1，然后格式化为写法2，这样就方便快速写配置文件了
myArrays:
    - name: handle
      age: 18
    - name: Jack
      age: 20
```
