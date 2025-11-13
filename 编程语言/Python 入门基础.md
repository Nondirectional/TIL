# Python 入门基础

## 目录

1. [变量的概念与命名规则](#变量的概念与命名规则)
2. [整数 (int) 与浮点数 (float)](#整数-int-与浮点数-float)
3. [字符串 (str) 基础](#字符串-str-基础)
4. [布尔类型 (bool)](#布尔类型-bool)
5. [数据类型转换](#数据类型转换)
6. [算术运算符](#算术运算符)
7. [比较运算符](#比较运算符)
8. [逻辑运算符](#逻辑运算符)
9. [赋值运算符](#赋值运算符)
10. [成员运算符](#成员运算符)
11. [身份运算符](#身份运算符)
12. [input() 和 print() 函数](#input-和-print-函数)
13. [格式化输出](#格式化输出)

---

## 变量的概念与命名规则

### 变量的概念

想象一下，你在做一个数学题，需要用到一个数字，比如圆周率 π ≈ 3.14159。你不会每次用到它的时候都把这一长串数字写一遍，而是可能会用一个符号，比如 "pi"，来代表这个数字。在程序中，**变量 (Variable)** 就扮演着类似的角色。

简单来说，**变量就是一个用来存储数据的"标签"或"容器"**。你可以把数据（比如数字、文字、列表等）放进这个容器里，并且给这个容器贴上一个名字（就是变量名）。以后当你想使用这个数据时，只需要通过这个名字就可以找到并使用它。

**核心思想：**

1. **存储数据：** 变量的主要目的是在内存中存储信息。
2. **引用数据：** 通过变量名，我们可以方便地引用（访问或修改）存储在内存中的数据。
3. **可变性：** "变量"之所以叫"变"量，是因为它存储的数据通常是可以改变的。

**在 Python 中，当你创建一个变量时，实际上发生了两件事：**

1. Python 会在计算机的内存中找一块空间来存放你要存储的数据。
2. Python 会将你指定的变量名与这块内存空间关联起来（就像给这块内存贴上了标签）。

**示例：**

```python
# 创建一个名为 pi 的变量，并把浮点数 3.14159 存进去
pi = 3.14159

# 创建一个名为 message 的变量，并把字符串 "你好，世界！" 存进去
message = "你好，世界！"

# 创建一个名为 count 的变量，并把整数 10 存进去
count = 10

# 之后可以修改变量中存储的数据
count = count + 5  # 现在 count 存储的是 15
```

**Python 变量的特点：**

- **动态类型 (Dynamically Typed)：** 你不需要在使用变量前显式声明它的类型。Python 解释器会在你给变量赋值时自动判断其类型。
- **变量只是一个引用：** 变量名本身不包含数据，它只是指向数据在内存中的位置。

### 变量的命名规则

给变量起一个好名字非常重要，它能让你的代码更容易阅读和理解。

**强制性规则 (必须遵守，否则程序会报错)：**

1. **字符组成：** 变量名只能包含 **字母 (a-z, A-Z)**、**数字 (0-9)** 和 **下划线 (`_`)**。
2. **开头字符：** 变量名 **不能以数字开头**。
3. **区分大小写：** Python 是大小写敏感的。这意味着 `myVariable` 和 `myvariable` 是两个不同的变量。
4. **不能是关键字 (Keywords)：** Python 有一些保留字，它们有特殊的含义，不能用作变量名。

常见的关键字有：`if`, `else`, `for`, `while`, `def`, `class`, `return`, `True`, `False`, `None` 等。

**命名规范和建议 (非强制，但强烈推荐遵守)：**

1. **见名知意 (Descriptive Names)：** 变量名应该清晰地描述它所存储的数据的含义。
2. **使用下划线分隔单词 (Snake Case)：** 这是 Python 社区最推荐的变量命名风格，即所有字母小写，单词之间用下划线连接。
3. **避免使用容易混淆的字符：** 避免使用小写字母 `l` (elle)、大写字母 `O` (oh) 和数字 `0` (zero)，因为它们在某些字体下看起来很相似。
4. **简洁明了：** 在保证见名知意的前提下，尽量让变量名简洁。
5. **一致性：** 在一个项目中，保持统一的命名风格。
6. **避免使用 Python 内置函数或类型的名称作为变量名。**

**有效和无效的变量名示例：**

- **有效的变量名：** `name`, `user_age`, `_count`, `MAX_CONNECTIONS`, `studentName`, `item1`
- **无效的变量名：** `2items` (不能以数字开头), `user name` (不能包含空格), `my-variable` (不能包含连字符), `class` (是关键字)

---

## 整数 (int) 与浮点数 (float)

### 什么是整数 (int) 和浮点数 (float)

在 Python 中，数字类型用于表示数值。最常用的两种数字类型是：

- **整数 (Integer, `int`)**: 表示不带小数点的正数、负数或零。例如：-2, 0, 10, 1000000。在 Python 3 中，整数的大小是 **没有限制** 的，只受限于你的计算机内存。
- **浮点数 (Floating-Point Number, `float`)**: 表示带小数点的数字。例如：3.14, -0.001, 2.0, 1.23e5 (科学计数法)。浮点数通常用于表示实数，但它们在计算机内部的表示方式决定了可能存在 **精度问题**。

### 如何定义和使用它们

直接赋值即可创建整数和浮点数变量：

```python
# 定义整数
age = 30
year = -2023
big_number = 12345678901234567890 # Python 3 整数没有大小限制

print(f"age 的类型是: {type(age)}")
print(f"year 的类型是: {type(year)}")
print(f"big_number 的类型是: {type(big_number)}")

# 定义浮点数
pi = 3.14159
temperature = -4.5
speed = 3.0 # 即使是整数值，加上小数点就变成了浮点数
scientific = 1.5e-3 # 表示 1.5 * 10^(-3) = 0.0015

print(f"\npi 的类型是: {type(pi)}")
print(f"temperature 的类型是: {type(temperature)}")
print(f"speed 的类型是: {type(speed)}")
print(f"scientific 的类型是: {type(scientific)}")
```

**注意**: `type()` 函数可以帮助你查看任何变量的数据类型。

### 基本算术运算

整数和浮点数都支持标准的算术运算符：

| 运算符 | 名称    | 示例 (int) | 结果 (int) | 示例 (float) | 结果 (float) |
| ------ | ------- | ---------- | ---------- | ------------ | ------------ |
| `+`    | 加法    | `5 + 3`    | `8`        | `5.0 + 3.0`  | `8.0`        |
| `-`    | 减法    | `5 - 3`    | `2`        | `5.0 - 3.0`  | `2.0`        |
| `*`    | 乘法    | `5 * 3`    | `15`       | `5.0 * 3.0`  | `15.0`       |
| `/`    | 除法    | `10 / 3`   | `3.333...` | `10.0 / 3.0` | `3.333...`   |
| `%`    | 取模 (余数) | `10 % 3` | `1`        | `10.0 % 3.0` | `1.0`        |
| `//`   | 整除    | `10 // 3`  | `3`        | `10.0 // 3.0`| `3.0`        |
| `**`   | 幂运算  | `2 ** 3`   | `8`        | `2.0 ** 3.0` | `8.0`        |

**重要:**

- 在 Python 3 中，无论操作数是整数还是浮点数，`/` 除法的结果 **总是** 浮点数。
- `//` 整除运算符会丢弃小数部分，结果的类型取决于操作数的类型。

### 类型转换 (Casting)

你可以使用内置函数在整数和浮点数之间进行转换：

- `int(x)`: 将 `x` 转换为整数。如果 `x` 是浮点数，它会 **截断** 小数部分（直接去掉，而不是四舍五入）。
- `float(x)`: 将 `x` 转换为浮点数。如果 `x` 是整数，它会加上 `.0`。

```python
# int 转 float
num_int = 100
num_float = float(num_int)
print(f"\nint 转 float: {num_int} -> {num_float}, type: {type(num_float)}")

# float 转 int (截断)
num_float2 = 123.789
num_int2 = int(num_float2)
print(f"float 转 int (截断): {num_float2} -> {num_int2}, type: {type(num_int2)}")

# 字符串转数字
str_int = "123"
str_float = "45.67"

try:
    int_from_str = int(str_int)
    float_from_str = float(str_float)
    print(f"字符串转 int: \"{str_int}\" -> {int_from_str}, type: {type(int_from_str)}")
    print(f"字符串转 float: \"{str_float}\" -> {float_from_str}, type: {type(float_from_str)}")
except ValueError as e:
    print(f"转换错误: {e}")
```

### int 和 float 混合运算

当一个整数和一个浮点数进行运算时，Python 会自动将整数 **提升** 为浮点数，然后再进行计算。结果将是浮点数。

```python
result_mixed = 5 + 3.14
print(f"\nint 和 float 混合运算: 5 + 3.14 = {result_mixed}, type: {type(result_mixed)}")

result_mixed2 = 10 * 2.5
print(f"int 和 float 混合运算: 10 * 2.5 = {result_mixed2}, type: {type(result_mixed2)}")
```

### 浮点数精度问题

这是掌握 `float` 时非常重要的一个概念。由于计算机使用二进制来表示浮点数，而许多十进制小数在二进制下是无限循环的，无法精确表示。这会导致微小的舍入误差。

```python
print(f"\n浮点数精度问题:")
print(0.1 + 0.2) # 结果不是精确的 0.3
print(0.1 + 0.2 == 0.3) # 结果是 False
```

**为什么会这样？**

就像十进制无法精确表示 1/3 (0.333...) 一样，许多十进制小数在二进制下也是无限循环的。

**什么时候需要注意？**

- 进行 **精确** 的金融计算时。
- 进行比较相等性 (`==`) 时，浮点数的相等性比较通常需要小心。

### 常用内置函数

除了上面提到的 `type()`, `int()`, `float()`，还有一些常用的内置函数用于数字操作：

- `abs(x)`: 返回 `x` 的绝对值。
- `round(x, n=None)`: 对 `x` 进行四舍五入。

```python
print(f"\n常用内置函数:")
print(f"abs(-10) = {abs(-10)}")
print(f"abs(-5.5) = {abs(-5.5)}")

print(f"round(3.14159) = {round(3.14159)}")
print(f"round(3.14159, 2) = {round(3.14159, 2)}")
print(f"round(3.5) = {round(3.5)}")   # 银行家舍入
print(f"round(2.5) = {round(2.5)}")   # 银行家舍入
print(f"round(4.789, 1) = {round(4.789, 1)}")
```

---

## 字符串 (str) 基础

### 什么是字符串 (str)

字符串是一个由零个或多个字符组成的序列。字符可以是字母、数字、符号、空格等等。在 Python 中，字符串是 **不可变 (immutable)** 的序列。这意味着一旦创建了一个字符串对象，就不能更改它里面的单个字符。

### 如何创建字符串

可以使用单引号 (`'`)、双引号 (`"`)、三单引号 (`'''`) 或三双引号 (`"""`) 来创建字符串。

- **单引号或双引号**: 用于创建单行字符串。
- **三单引号或三双引号**: 用于创建多行字符串。它们可以跨越多行，并且会保留原始字符串中的换行符和缩进。

```python
# 单引号或双引号
string1 = 'Hello, World!'
string2 = "Python Programming"

# 如果字符串本身包含单引号或双引号，可以使用另一种引号来包围它
quote1 = "He said, 'Hello!'"
quote2 = 'It\'s a beautiful day.' # 使用反斜杠转义单引号

# 三引号用于多行字符串
multiline_string = """This is a
multiline string.
It preserves
line breaks and
indentation."""
```

### 字符串的不可变性

字符串的一个重要特性。你不能修改一个已存在的字符串对象中的某个字符。

```python
my_string = "Python"
# my_string[0] = "J" # 这行代码会引发 TypeError 错误，因为字符串不可变

# 如果想改变，必须创建一个新的字符串
new_string = "Jython" # 创建了一个新的字符串对象
```

### 访问字符串中的字符 (索引)

字符串中的每个字符都有一个对应的位置编号，称为 **索引 (index)**。索引从 0 开始。你也可以使用负数索引，它从字符串的末尾开始计数。

```python
word = "Example"

print(f"第一个字符: {word[0]}")
print(f"第三个字符: {word[2]}")
print(f"最后一个字符: {word[-1]}")
print(f"倒数第三个字符: {word[-3]}")
```

### 字符串切片 (Slicing)

切片允许你从字符串中提取一部分作为子字符串。语法是 `[start:stop:step]`。

```python
phrase = "Python Slicing"

print(f"从索引 0 到 6 (不包含 6): {phrase[0:6]}") # Python
print(f"从索引 7 到末尾: {phrase[7:]}")      # Slicing
print(f"从开头到索引 6 (不包含 6): {phrase[:6]}")      # Python
print(f"复制整个字符串: {phrase[:]}")       # Python Slicing
print(f"从索引 0 到末尾，步长为 2: {phrase[::2]}")     # Pto lc n
print(f"反转字符串: {phrase[::-1]}")      # gnicilS nohtyP
```

### 字符串长度

使用内置函数 `len()` 可以获取字符串中字符的数量。

```python
string_length = "Hello"
print(f"字符串 '{string_length}' 的长度是: {len(string_length)}")
```

### 字符串拼接和重复

- **拼接 (`+`)**: 使用 `+` 运算符可以将两个或多个字符串连接起来。
- **重复 (`*`)**: 使用 `*` 运算符和一个整数可以重复字符串多次。

```python
# 拼接
greeting = "Hello"
name = "Alice"
message = greeting + ", " + name + "!"
print(f"拼接字符串: {message}")

# 重复
repeat_string = "abc" * 3
print(f"重复字符串: {repeat_string}")
```

### 转义序列 (Escape Sequences)

转义序列以反斜杠 (`\`) 开头，用于在字符串中表示特殊字符。

| 转义序列 | 表示字符     |
| -------- | ------------ |
| `\'`     | 单引号       |
| `\"`     | 双引号       |
| `\\`     | 反斜杠       |
| `\n`     | 换行符       |
| `\t`     | 制表符 (Tab) |
| `\r`     | 回车符       |

```python
print("This is a line.\nThis is another line.")
print("Item 1:\tValue 1")
print("This contains a backslash: \\")
print("She said, \"Hello!\"")
```

### 原始字符串 (Raw Strings)

在某些情况下，你可能不想让反斜杠作为转义符，而是希望它被当作普通字符处理。这时可以在字符串前加上 `r` 或 `R`，创建原始字符串。

```python
# 普通字符串，\n 会被解释为换行
print("C:\\Users\\name\\documents\\newfolder")

# 原始字符串，\n 不会被解释
print(r"C:\Users\name\documents\newfolder")
```

**注意**: 原始字符串不能以单个反斜杠结束。

---

## 布尔类型 (bool)

Python 的布尔类型（bool）是一种基本数据类型，只有两个可能的值：

- `True`（真）
- `False`（假）

```python
# 基本布尔值
is_student = True
has_graduated = False

print(type(True))   # <class 'bool'>
print(type(False))  # <class 'bool'>
```

**重要注意事项：**

- `True` 和 `False` 必须首字母大写
- 它们是 Python 的关键字，不需要引号
- `true` 和 `false`（小写）会导致 NameError

### 类型特性

#### bool 是 int 的子类

```python
print(issubclass(bool, int))  # True
print(isinstance(True, int))  # True

# 布尔值的数值特性
print(True == 1)    # True
print(False == 0)   # True
print(True + True)  # 2
print(False * 10)   # 0
```

### bool()函数

`bool()` 函数可以将任何 Python 对象转换为布尔值。

**"假"值规则：**
在 Python 中，以下值会被认为是 `False`：

- `None`
- `False`
- 数字 `0` (整数、浮点数、复数)
- 空序列 (空字符串 `""`, 空列表 `[]`, 空元组 `()`)
- 空映射 (空字典 `{}` )
- 空集合 (`set()`)

所有其他值都被认为是 `True`。

```python
# 整数转换为布尔值
print(f"bool(0): {bool(0)}")     # False
print(f"bool(1): {bool(1)}")     # True
print(f"bool(-5): {bool(-5)}")   # True

# 字符串转换为布尔值
print(f"bool(''): {bool('')}")   # False
print(f"bool('hello'): {bool('hello')}") # True
print(f"bool('False'): {bool('False')}") # 注意：非空字符串 'False' 也是 True

# 列表、元组、字典、集合转换为布尔值
print(f"bool([]): {bool([])}")   # False
print(f"bool([1, 2]): {bool([1, 2])}") # True
print(f"bool(()): {bool(())}")   # False
print(f"bool({{}}): {bool({{}})}") # False

# None 转换为布尔值
print(f"bool(None): {bool(None)}") # False
```

### 布尔运算符

#### 逻辑运算符

- `and`：逻辑与
- `or`：逻辑或
- `not`：逻辑非

```python
# and 运算符
print(True and True)   # True
print(True and False)  # False
print(False and True)  # False
print(False and False) # False

# or 运算符
print(True or True)    # True
print(True or False)   # True
print(False or True)   # True
print(False or False)  # False

# not 运算符
print(not True)        # False
print(not False)       # True
```

#### 短路求值

Python 的逻辑运算符使用短路求值：

```python
def expensive_function():
    print("函数被调用了")
    return True

# and 短路：如果第一个操作数为 False，不会计算第二个
result1 = False and expensive_function()  # 函数不会被调用

# or 短路：如果第一个操作数为 True，不会计算第二个
result2 = True or expensive_function()   # 函数不会被调用
```

---

## 数据类型转换

Python 是一种动态类型语言，但理解和掌握不同数据类型之间的转换对于编写健壮和灵活的代码至关重要。

### 为什么需要数据类型转换？

- **数据兼容性：** 不同操作可能需要特定类型的数据。
- **数据清洗：** 从外部源获取的数据通常是字符串形式，需要转换为其他类型。
- **用户输入：** `input()` 函数总是返回字符串，如果需要数字或其他类型的数据，则必须进行转换。
- **格式化输出：** 有时需要将数字转换为字符串以便于打印或与其他字符串拼接。

### 常用数据类型转换函数

#### 转换为整数 (`int()`)

`int()` 函数可以将其他类型（主要是数字型字符串或浮点数）转换为整数。

**语法:** `int(x, base=10)`

```python
# 浮点数转换为整数（截断小数部分）
float_num = 3.14
int_from_float = int(float_num)
print(f"浮点数 {float_num} 转换为整数: {int_from_float}") # 输出: 3

# 数字字符串转换为整数
str_num = "123"
int_from_str = int(str_num)
print(f"字符串 '{str_num}' 转换为整数: {int_from_str}") # 输出: 123

# 带进制的字符串转换为整数
binary_str = "1011"
int_from_binary = int(binary_str, 2)
print(f"二进制字符串 '{binary_str}' 转换为整数: {int_from_binary}") # 输出: 11
```

#### 转换为浮点数 (`float()`)

`float()` 函数可以将整数或数字型字符串转换为浮点数。

```python
# 整数转换为浮点数
int_num = 10
float_from_int = float(int_num)
print(f"整数 {int_num} 转换为浮点数: {float_from_int}") # 输出: 10.0

# 数字字符串转换为浮点数
str_float = "3.14159"
float_from_str = float(str_float)
print(f"字符串 '{str_float}' 转换为浮点数: {float_from_str}") # 输出: 3.14159
```

#### 转换为字符串 (`str()`)

`str()` 函数可以将几乎所有类型的数据转换为字符串。

```python
# 整数转换为字符串
num = 123
str_from_int = str(num)
print(f"整数 {num} 转换为字符串: '{str_from_int}' (类型: {type(str_from_int)})")

# 浮点数转换为字符串
pi = 3.14159
str_from_float = str(pi)
print(f"浮点数 {pi} 转换为字符串: '{str_from_float}'")

# 列表转换为字符串
my_list = [1, 2, 3]
str_from_list = str(my_list)
print(f"列表 {my_list} 转换为字符串: '{str_from_list}'")

# 布尔值转换为字符串
is_true = True
str_from_bool = str(is_true)
print(f"布尔值 {is_true} 转换为字符串: '{str_from_bool}'")
```

#### 转换为布尔值 (`bool()`)

`bool()` 函数可以将任何类型的数据转换为布尔值。详细的转换规则请参考上面的"布尔类型"章节。

### 类型转换的注意事项

- **数据丢失：** `int(float_num)` 会截断小数部分，而不是四舍五入。
- **`ValueError`：** 当你尝试将一个不能转换为目标类型的字符串时，会引发 `ValueError`。
- **`TypeError`：** 当你尝试将一个不支持转换操作的类型作为参数传递给转换函数时，会引发 `TypeError`。

### 实践案例

```python
# 获取用户输入
user_input_str_1 = input("请输入第一个数字: ")
user_input_str_2 = input("请输入第二个数字: ")

try:
    # 尝试将字符串转换为浮点数
    num1 = float(user_input_str_1)
    num2 = float(user_input_str_2)

    # 进行计算
    sum_nums = num1 + num2
    product_nums = num1 * num2

    # 将结果转换为字符串进行输出
    print(f"这两个数字的和是: {str(sum_nums)}")
    print(f"这两个数字的积是: {str(product_nums)}")

except ValueError:
    print("错误：请输入有效的数字。")
except Exception as e:
    print(f"发生未知错误: {e}")
```

---

## 算术运算符

在编程中，算术运算符用于执行数学运算，例如加法、减法、乘法等。Python 提供了一套直观且强大的算术运算符。

### Python 中的算术运算符

#### 1. 加法运算符 (`+`)

加法运算符用于计算两个或多个数字的和。

```python
a = 10
b = 5

result = a + b
print(f"{a} + {b} = {result}") # 输出: 10 + 5 = 15

# 也可以用于连接字符串（这是一种特殊的用法，不是严格意义上的算术运算）
str1 = "Hello"
str2 = " World"
combined_str = str1 + str2
print(f"'{str1}' + '{str2}' = '{combined_str}'") # 输出: 'Hello' + ' World' = 'Hello World'
```

#### 2. 减法运算符 (`-`)

减法运算符用于计算两个数字的差。

```python
a = 15
b = 7

result = a - b
print(f"{a} - {b} = {result}") # 输出: 15 - 7 = 8

# 也可以表示负数
negative_num = -10
print(f"负数: {negative_num}") # 输出: 负数: -10
```

#### 3. 乘法运算符 (`*`)

乘法运算符用于计算两个数字的积。

```python
a = 6
b = 4

result = a * b
print(f"{a} * {b} = {result}") # 输出: 6 * 4 = 24

# 也可以用于字符串的重复
text = "Python"
repeated_text = text * 3
print(f"'{text}' * 3 = '{repeated_text}'") # 输出: 'Python' * 3 = 'PythonPythonPython'
```

#### 4. 除法运算符 (`/`)

除法运算符用于计算两个数字的商。**需要注意的是，Python 3 中的 `/` 运算符执行的是浮点除法，即使操作数是整数，结果也会是浮点数。**

```python
a = 10
b = 3

result = a / b
print(f"{a} / {b} = {result}") # 输出: 10 / 3 = 3.3333333333333335
print(f"结果的类型: {type(result)}") # 输出: 结果的类型: <class 'float'>

c = 8
d = 2
result_exact = c / d
print(f"{c} / {d} = {result_exact}") # 输出: 8 / 2 = 4.0
print(f"结果的类型: {type(result_exact)}") # 输出: 结果的类型: <class 'float'>
```

#### 5. 整除运算符 (`//`)

整除运算符（也称为"地板除"）用于计算两个数字的商，并向下取整到最接近的整数。

```python
a = 10
b = 3

result = a // b
print(f"{a} // {b} = {result}") # 输出: 10 // 3 = 3

# 对于负数，结果会向下取整到负无穷大
e = -10
f = 3
result_neg = e // f
print(f"{e} // {f} = {result_neg}") # 输出: -10 // 3 = -4 (而不是 -3)
```

**注意：** 当操作数中包含负数时，`//` 运算符的行为是"向下取整到负无穷大"。

#### 6. 取模/取余运算符 (`%`)

取模运算符用于计算两个数字相除后的余数。

```python
a = 10
b = 3

result = a % b
print(f"{a} % {b} = {result}") # 输出: 10 % 3 = 1 (10 除以 3 等于 3 余 1)

# 对于负数操作数，取模运算的结果符号与除数相同
e = -10
f = 3
result_neg_dividend = e % f
print(f"{e} % {f} = {result_neg_dividend}") # 输出: -10 % 3 = 2 (因为 -10 = 3 * (-4) + 2)
```

#### 7. 幂运算符 (`**`)

幂运算符用于计算一个数字的指定次幂（次方）。

```python
base = 2
exponent = 3

result = base ** exponent
print(f"{base} ** {exponent} = {result}") # 输出: 2 ** 3 = 8 (即 2 * 2 * 2)

# 浮点数幂
float_base = 2.5
float_exponent = 2
result_float_power = float_base ** float_exponent
print(f"{float_base} ** {float_exponent} = {result_float_power}") # 输出: 2.5 ** 2 = 6.25

# 小数幂（开方）
square_root = 16 ** 0.5
print(f"16 ** 0.5 = {square_root}") # 输出: 16 ** 0.5 = 4.0
```

### 运算符优先级

当一个表达式中包含多个算术运算符时，Python 会遵循特定的运算顺序：

1. **括号 `()`**：总是最高优先级，括号内的表达式最先计算。
2. **幂运算 `**`**：次高优先级。
3. **乘法 `*`，除法 `/`，整除 `//`，取模 `%`**：这些运算符优先级相同，从左到右依次计算。
4. **加法 `+`，减法 `-`**：最低优先级，从左到右依次计算。

**助记符：** PEMDAS / BODMAS (括号，指数，乘除，加减)

```python
result1 = 5 + 3 * 2  # 3 * 2 先计算，然后加 5
print(f"5 + 3 * 2 = {result1}") # 输出: 11

result2 = (5 + 3) * 2 # 括号内的 5 + 3 先计算，然后乘以 2
print(f"(5 + 3) * 2 = {result2}") # 输出: 16

result3 = 2 ** 3 + 4 / 2 # 2 ** 3 先计算 (8)，然后 4 / 2 (2.0)，最后 8 + 2.0
print(f"2 ** 3 + 4 / 2 = {result3}") # 输出: 10.0
```

---

## 比较运算符

在编程中，除了进行数学运算外，我们经常需要比较两个值的大小、相等性或不相等性。比较运算符在 Python 中用于评估两个值之间的关系，并总是返回一个布尔值（`True` 或 `False`）。

### Python 中的比较运算符

#### 1. 等于运算符 (`==`)

等于运算符用于检查两个值是否相等。

```python
a = 10
b = 10
c = 5

print(f"{a} == {b} 结果: {a == b}") # 输出: 10 == 10 结果: True
print(f"{a} == {c} 结果: {a == c}") # 输出: 10 == 5 结果: False

# 比较字符串
str1 = "Hello"
str2 = "Hello"
str3 = "hello"

print(f"'{str1}' == '{str2}' 结果: {str1 == str2}") # 输出: 'Hello' == 'Hello' 结果: True
print(f"'{str1}' == '{str3}' 结果: {str1 == str3}") # 输出: 'Hello' == 'hello' 结果: False (大小写敏感)

# 比较不同类型（通常返回 False，除非有明确的转换规则）
print(f"10 == 10.0 结果: {10 == 10.0}") # 输出: 10 == 10.0 结果: True (Python 会进行隐式类型转换来比较数值)
print(f"10 == '10' 结果: {10 == '10'}") # 输出: 10 == '10' 结果: False (不同类型通常不相等)
```

#### 2. 不等于运算符 (`!=`)

不等于运算符用于检查两个值是否不相等。

```python
a = 10
b = 10
c = 5

print(f"{a} != {b} 结果: {a != b}") # 输出: 10 != 10 结果: False
print(f"{a} != {c} 结果: {a != c}") # 输出: 10 != 5 结果: True

# 比较字符串
str1 = "Apple"
str2 = "Orange"

print(f"'{str1}' != '{str2}' 结果: {str1 != str2}") # 输出: 'Apple' != 'Orange' 结果: True
```

#### 3. 大于运算符 (`>`)

大于运算符用于检查左侧的值是否大于右侧的值。

```python
a = 20
b = 15
c = 20

print(f"{a} > {b} 结果: {a > b}") # 输出: 20 > 15 结果: True
print(f"{a} > {c} 结果: {a > c}") # 输出: 20 > 20 结果: False
print(f"{b} > {a} 结果: {b > a}") # 输出: 15 > 20 结果: False
```

#### 4. 小于运算符 (`<`)

小于运算符用于检查左侧的值是否小于右侧的值。

```python
a = 10
b = 15
c = 10

print(f"{a} < {b} 结果: {a < b}") # 输出: 10 < 15 结果: True
print(f"{a} < {c} 结果: {a < c}") # 输出: 10 < 10 结果: False
print(f"{b} < {a} 结果: {b < a}") # 输出: 15 < 10 结果: False
```

#### 5. 大于等于运算符 (`>=`)

大于等于运算符用于检查左侧的值是否大于或等于右侧的值。

```python
a = 20
b = 15
c = 20

print(f"{a} >= {b} 结果: {a >= b}") # 输出: 20 >= 15 结果: True
print(f"{a} >= {c} 结果: {a >= c}") # 输出: 20 >= 20 结果: True
print(f"{b} >= {a} 结果: {b >= a}") # 输出: 15 >= 20 结果: False
```

#### 6. 小于等于运算符 (`<=`)

小于等于运算符用于检查左侧的值是否小于或等于右侧的值。

```python
a = 10
b = 15
c = 10

print(f"{a} <= {b} 结果: {a <= b}") # 输出: 10 <= 15 结果: True
print(f"{a} <= {c} 结果: {a <= c}") # 输出: 10 <= 10 结果: True
print(f"{b} <= {a} 结果: {b <= a}") # 输出: 15 <= 10 结果: False
```

### 比较字符串和序列

比较运算符不仅适用于数字，也适用于字符串和其他序列类型（如列表、元组）。

- **字符串比较：** 字符串是根据它们的字典顺序（字母顺序）进行比较的，比较是逐个字符进行的，基于每个字符的 Unicode 值。大小写敏感。
- **序列比较：** 列表、元组等序列也是按元素从左到右依次比较。

```python
# 字符串比较
print(f"'apple' < 'banana' 结果: {'apple' < 'banana'}") # 输出: True (a 在 b 之前)
print(f"'Apple' < 'apple' 结果: {'Apple' < 'apple'}") # 输出: True (A 的 Unicode 值小于 a)
print(f"'cat' == 'Cat' 结果: {'cat' == 'Cat'}") # 输出: False

# 列表比较
list1 = [1, 2, 3]
list2 = [1, 2, 4]
list3 = [1, 2, 3]
list4 = [1, 2]

print(f"{list1} < {list2} 结果: {list1 < list2}")   # 输出: True (3 < 4)
print(f"{list1} == {list3} 结果: {list1 == list3}") # 输出: True
print(f"{list1} > {list4} 结果: {list1 > list4}")   # 输出: True (list1 较长，且是 list4 的超序列)
```

### 链式比较

Python 支持链式比较，这使得编写复合条件变得非常简洁和易读。

**语法:** `a < b < c` 等同于 `a < b and b < c`

```python
age = 25

# 检查 age 是否在 18 到 30 之间（包含 18 和 30）
is_adult = 18 <= age <= 30
print(f"age {age} 是否在 18 到 30 之间: {is_adult}") # 输出: True

grade = 85
# 检查 grade 是否在 60 到 90 之间（不包含 90）
is_passing_grade = 60 <= grade < 90
print(f"grade {grade} 是否是及格分数: {is_passing_grade}") # 输出: True
```

---

## 逻辑运算符

在编程中，我们经常需要组合多个条件来做出更复杂的决策。这时，逻辑运算符就派上用场了。逻辑运算符用于连接和评估布尔表达式（即返回 `True` 或 `False` 的表达式），并返回一个最终的布尔结果。

### Python 中的逻辑运算符

#### 1. 逻辑与运算符 (`and`)

`and` 运算符用于连接两个或多个布尔表达式。只有当所有连接的表达式都为 `True` 时，整个表达式的结果才为 `True`；否则，结果为 `False`。

**真值表：**

| 表达式 1 | 表达式 2 | 结果 (表达式 1 `and` 表达式 2) |
| -------- | -------- | ------------------------------ |
| `True`   | `True`   | `True`                         |
| `True`   | `False`  | `False`                        |
| `False`  | `True`   | `False`                        |
| `False`  | `False`  | `False`                        |

```python
age = 25
has_license = True

# 条件：年龄大于 18 岁 且 有驾照
can_drive = (age > 18) and has_license
print(f"({age} > 18) and {has_license} = {can_drive}") # 输出: (25 > 18) and True = True

age = 16
can_drive_again = (age > 18) and has_license
print(f"({age} > 18) and {has_license} = {can_drive_again}") # 输出: (16 > 18) and True = False
```

**短路评估 (Short-circuiting) for `and`:**
如果 `and` 运算符的左侧表达式为 `False`，那么 Python 会立即判断整个 `and` 表达式的结果为 `False`，而不会去评估右侧的表达式。

```python
x = 0
y = 10

# print(y / x) # 这会引起 ZeroDivisionError

# 使用 and 进行短路评估
if x != 0 and (y / x > 5): # 如果 x 是 0，则 y / x 不会被执行
    print("条件为真")
else:
    print("条件为假或 x 为 0") # 输出: 条件为假或 x 为 0
```

#### 2. 逻辑或运算符 (`or`)

`or` 运算符也用于连接两个或多个布尔表达式。只要有一个连接的表达式为 `True`，整个表达式的结果就为 `True`；只有当所有连接的表达式都为 `False` 时，结果才为 `False`。

**真值表：**

| 表达式 1 | 表达式 2 | 结果 (表达式 1 `or` 表达式 2) |
| -------- | -------- | ----------------------------- |
| `True`   | `True`   | `True`                        |
| `True`   | `False`  | `True`                        |
| `False`  | `True`   | `True`                        |
| `False`  | `False`  | `False`                       |

```python
is_weekend = False
has_day_off = True

# 条件：是周末 或 有休假
can_relax = is_weekend or has_day_off
print(f"{is_weekend} or {has_day_off} = {can_relax}") # 输出: False or True = True

is_weekend = False
has_day_off = False
can_relax_again = is_weekend or has_day_off
print(f"{is_weekend} or {has_day_off} = {can_relax_again}") # 输出: False or False = False
```

**短路评估 (Short-circuiting) for `or`:**
如果 `or` 运算符的左侧表达式为 `True`，那么 Python 会立即判断整个 `or` 表达式的结果为 `True`，而不会去评估右侧的表达式。

```python
default_value = ""
user_input = "Hello"

# 如果 user_input 为空，则使用 default_value
final_value = user_input or default_value
print(f"final_value: '{final_value}'") # 输出: final_value: 'Hello'

user_input_empty = ""
final_value_empty = user_input_empty or default_value
print(f"final_value_empty: '{final_value_empty}'") # 输出: final_value_empty: ''
```

#### 3. 逻辑非运算符 (`not`)

`not` 运算符是一个一元运算符（只作用于一个操作数），它会反转布尔表达式的真假值。如果表达式为 `True`，`not` 会使其变为 `False`；如果表达式为 `False`，`not` 会使其变为 `True`。

**真值表：**

| 表达式 | 结果 (`not` 表达式) |
| ------ | ------------------- |
| `True` | `False`             |
| `False`| `True`              |

```python
is_raining = True
print(f"not {is_raining} = {not is_raining}") # 输出: not True = False

is_hungry = False
print(f"not {is_hungry} = {not is_hungry}") # 输出: not False = True

# 应用于表达式
age = 17
is_adult = age >= 18
print(f"age {age} is_adult: {is_adult}") # 输出: age 17 is_adult: False
print(f"not (age >= 18) = {not (age >= 18)}") # 输出: not (age >= 18) = True (即 not False = True)
```

### 运算符优先级

当一个表达式中包含多个逻辑运算符以及其他运算符时，Python 会遵循特定的运算顺序。优先级从高到低如下：

1. **算术运算符** (如 `+`, `-`, `*`, `/`, `**` 等)
2. **比较运算符** (如 `==`, `!=`, `>`, `<`, `>=`, `<=` 等)
3. **`not`** 运算符
4. **`and`** 运算符
5. **`or`** 运算符

**括号 `()`** 总是具有最高优先级，可以用来强制改变运算顺序。

```python
# 示例 1: 比较运算符优先于逻辑运算符
x = 10
y = 5
z = 12

result1 = x > y and y < z # (x > y) (True) and (y < z) (True) -> True and True -> True
print(f"{x} > {y} and {y} < {z} = {result1}") # 输出: 10 > 5 and 5 < 12 = True

# 示例 2: not 优先于 and 和 or
is_active = True
is_admin = False

result2 = not is_active and is_admin # (not is_active) (False) and is_admin (False) -> False and False -> False
print(f"not {is_active} and {is_admin} = {result2}") # 输出: not True and False = False

# 示例 3: 使用括号改变优先级
result3 = not (is_active and is_admin) # (is_active and is_admin) (False)，然后 not False -> True
print(f"not ({is_active} and {is_admin}) = {result3}") # 输出: not (True and False) = True
```

---

## 赋值运算符

在 Python 中，赋值运算符用于将值存储到变量中。除了最基本的 `=` 运算符，Python 还提供了一系列复合赋值运算符，它们结合了算术（或其他）运算和赋值操作，使得代码更加简洁高效。

### 基本赋值运算符 (`=`)

`=` 是最常用的赋值运算符。它将右侧的值或表达式的结果赋给左侧的变量。

```python
# 将整数值 10 赋给变量 x
x = 10
print(f"x 的值: {x}") # 输出: x 的值: 10

# 将字符串 "Hello" 赋给变量 greeting
greeting = "Hello"
print(f"greeting 的值: {greeting}") # 输出: greeting 的值: Hello

# 将一个表达式的结果赋给变量
y = 5
z = x + y # x + y 的结果 (15) 赋给 z
print(f"z 的值: {z}") # 输出: z 的值: 15

# 可以同时为多个变量赋值（链式赋值）
a = b = c = 100
print(f"a: {a}, b: {b}, c: {c}") # 输出: a: 100, b: 100, c: 100

# 也可以进行解包赋值（sequence unpacking）
data = [10, 20, 30]
val1, val2, val3 = data
print(f"val1: {val1}, val2: {val2}, val3: {val3}") # 输出: val1: 10, val2: 20, val3: 30
```

### 复合赋值运算符

复合赋值运算符结合了算术（或其他）运算和赋值操作。它们是以下形式的简写：`variable = variable operator expression`。

#### 1. 加法赋值 (`+=`)

`a += b` 等同于 `a = a + b`。它将变量 `a` 的值与 `b` 相加，然后将结果重新赋给 `a`。

```python
x = 10
x += 5 # 等同于 x = x + 5
print(f"x += 5 后的值: {x}") # 输出: x += 5 后的值: 15

# 也可以用于字符串拼接
message = "Hello"
message += " World" # 等同于 message = message + " World"
print(f"message += ' World' 后的值: {message}") # 输出: message += ' World' 后的值: Hello World

# 也可以用于列表拼接（扩展列表）
my_list = [1, 2]
my_list += [3, 4] # 等同于 my_list = my_list + [3, 4]
print(f"my_list += [3, 4] 后的值: {my_list}") # 输出: my_list += [3, 4] 后的值: [1, 2, 3, 4]
```

#### 2. 减法赋值 (`-=`)

`a -= b` 等同于 `a = a - b`。它将变量 `a` 的值减去 `b`，然后将结果重新赋给 `a`。

```python
y = 20
y -= 7 # 等同于 y = y - 7
print(f"y -= 7 后的值: {y}") # 输出: y -= 7 后的值: 13
```

#### 3. 乘法赋值 (`*=`)

`a *= b` 等同于 `a = a * b`。它将变量 `a` 的值与 `b` 相乘，然后将结果重新赋给 `a`。

```python
z = 5
z *= 4 # 等同于 z = z * 4
print(f"z *= 4 后的值: {z}") # 输出: z *= 4 后的值: 20

# 也可以用于字符串重复
text = "ABC"
text *= 3 # 等同于 text = text * 3
print(f"text *= 3 后的值: {text}") # 输出: text *= 3 后的值: ABCABCABC
```

#### 4. 除法赋值 (`/=`)

`a /= b` 等同于 `a = a / b`。它将变量 `a` 的值除以 `b`，然后将结果重新赋给 `a`。
**注意：** 结果总是浮点数。

```python
m = 25
m /= 5 # 等同于 m = m / 5
print(f"m /= 5 后的值: {m}") # 输出: m /= 5 后的值: 5.0

n = 10
n /= 3 # 等同于 n = n / 3
print(f"n /= 3 后的值: {n}") # 输出: n /= 3 后的值: 3.3333333333333335
```

#### 5. 整除赋值 (`//=`)

`a //= b` 等同于 `a = a // b`。它将变量 `a` 的值整除 `b`，然后将结果重新赋给 `a`。

```python
p = 17
p //= 3 # 等同于 p = p // 3
print(f"p //= 3 后的值: {p}") # 输出: p //= 3 后的值: 5

q = -10
q //= 3 # 等同于 q = q // 3
print(f"q //= 3 后的值: {q}") # 输出: q //= 3 后的值: -4
```

#### 6. 取模赋值 (`%=`)

`a %= b` 等同于 `a = a % b`。它将变量 `a` 的值对 `b` 取模，然后将结果重新赋给 `a`。

```python
r = 27
r %= 5 # 等同于 r = r % 5
print(f"r %= 5 后的值: {r}") # 输出: r %= 5 后的值: 2
```

#### 7. 幂赋值 (`**=`)

`a **= b` 等同于 `a = a ** b`。它将变量 `a` 的值进行 `b` 次幂运算，然后将结果重新赋给 `a`。

```python
s = 2
s **= 3 # 等同于 s = s ** 3 (2 的 3 次方)
print(f"s **= 3 后的值: {s}") # 输出: s **= 3 后的值: 8
```

### 赋值表达式 (Python 3.8+ `:=` 海象运算符)

自 Python 3.8 起，引入了赋值表达式 (`:=`)，也常被称为"海象运算符"。它允许你在表达式内部进行变量赋值。

```python
# 传统方式
data = [1, 2, 3]
n = len(data)
if n > 0:
    print(f"列表长度为: {n}")

# 使用海象运算符
data = [1, 2, 3]
if (n := len(data)) > 0: # 在 if 条件中同时赋值和判断
    print(f"列表长度为: {n}") # 输出: 列表长度为: 3

# 在列表推导式中使用
scores = [80, 75, 90, 60]
if any((score := s) >= 90 for s in scores): # 赋值 score 为符合条件的 s
    print(f"有至少一个分数达到 90 或更高，其中一个分数是: {score}")

# 简化 while 循环
while (command := input("请输入命令 (输入 'exit' 退出): ")) != 'exit':
    print(f"执行命令: {command}")
```

---

## 成员运算符

在 Python 中，成员运算符用于测试序列（如字符串、列表、元组）或集合中是否包含某个特定值。它们在检查元素是否存在于集合或序列中时非常有用。

### Python 中的成员运算符

Python 提供了两个成员运算符：`in` 和 `not in`。

#### 1. 成员运算符 (`in`)

`in` 运算符用于检查左侧的操作数（一个值）是否存在于右侧的操作数（一个序列、集合或字典的键）中。如果存在，则返回 `True`；否则，返回 `False`。

**适用于：**
- **字符串：** 检查子字符串是否存在于字符串中。
- **列表：** 检查元素是否存在于列表中。
- **元组：** 检查元素是否存在于元组中。
- **集合：** 检查元素是否存在于集合中。
- **字典：** 检查键是否存在于字典中（不检查值）。

```python
# 检查字符串中的子字符串
text = "Hello, Python!"
print(f"'Python' in '{text}' 结果: {'Python' in text}") # 输出: True
print(f"'Java' in '{text}' 结果: {'Java' in text}")     # 输出: False
print(f"'hello' in '{text}' 结果: {'hello' in text}")   # 输出: False (大小写敏感)

# 检查列表中是否存在元素
my_list = [10, 20, 30, 40]
print(f"20 in {my_list} 结果: {20 in my_list}")   # 输出: True
print(f"50 in {my_list} 结果: {50 in my_list}")   # 输出: False

# 检查字典中是否存在键
my_dict = {'name': 'Alice', 'age': 30, 'city': 'New York'}
print(f"'age' in {my_dict} 结果: {'age' in my_dict}") # 输出: True (检查键)
print(f"'country' in {my_dict} 结果: {'country' in my_dict}") # 输出: False (检查键)
print(f"30 in {my_dict} 结果: {30 in my_dict}") # 输出: False (不直接检查值)

# 如果想检查字典的值，你需要遍历字典的值：
print(f"30 in {my_dict.values()} 结果: {30 in my_dict.values()}") # 输出: True
```

#### 2. 非成员运算符 (`not in`)

`not in` 运算符是 `in` 运算符的反义。它用于检查左侧的操作数（一个值）是否 **不** 存在于右侧的操作数（一个序列、集合或字典的键）中。如果不存在，则返回 `True`；否则，返回 `False`。

```python
# 检查字符串中不存在的子字符串
text = "Programming is fun!"
print(f"'Java' not in '{text}' 结果: {'Java' not in text}") # 输出: True
print(f"'fun' not in '{text}' 结果: {'fun' not in text}")   # 输出: False

# 检查列表中不存在的元素
numbers = [100, 200, 300]
print(f"50 not in {numbers} 结果: {50 not in numbers}")     # 输出: True
print(f"200 not in {numbers} 结果: {200 not in numbers}")   # 输出: False

# 检查字典中不存在的键
student_info = {'id': 101, 'major': 'CS'}
print(f"'grade' not in {student_info} 结果: {'grade' not in student_info}") # 输出: True
print(f"'id' not in {student_info} 结果: {'id' not in student_info}")     # 输出: False
```

### 应用场景

成员运算符在日常编程中非常常见，尤其是在条件判断、数据验证和过滤时。

- **条件判断：**
    ```python
    user_role = "admin"
    if user_role in ["admin", "moderator"]:
        print("用户有管理权限。")
    ```

- **数据验证：**
    ```python
    valid_colors = ['red', 'green', 'blue']
    user_color = input("请输入您喜欢的颜色: ")
    if user_color.lower() not in valid_colors:
        print("输入的颜色无效。")
    else:
        print(f"您选择了 {user_color}。")
    ```

- **循环和搜索：**
    ```python
    sentence = "The quick brown fox jumps over the lazy dog."
    if "fox" in sentence:
        print("句子中包含 'fox'。")
    
    # 查找特定字符
    vowels = "aeiouAEIOU"
    char_to_check = 'P'
    if char_to_check in vowels:
        print(f"'{char_to_check}' 是一个元音字母。")
    else:
        print(f"'{char_to_check}' 不是一个元音字母。")
    ```

---

## 身份运算符

在 Python 中，除了比较值是否相等之外，有时还需要判断两个变量是否引用了内存中的同一个对象。这时就需要用到身份运算符。身份运算符关注的是对象的内存地址，而不是对象的值。

### Python 中的身份运算符

Python 提供了两个身份运算符：`is` 和 `is not`。

#### 1. 身份运算符 (`is`)

`is` 运算符用于检查左侧的操作数和右侧的操作数是否指向内存中的同一个对象。如果它们是同一个对象，则返回 `True`；否则，返回 `False`。

**重要说明：**

- **`is` 与 `==` 的区别：**
    - `is` 比较的是两个变量的 **身份（identity）**，即它们在内存中的存储地址是否相同。
    - `==` 比较的是两个变量的 **值（value）**，即它们所表示的数据内容是否相等。

- **小整数缓存（Integer Caching）/字符串驻留（String Interning）：**
    Python 解释器为了优化性能，会对一些常用的、不可变的对象（如小整数：通常是 -5 到 256 之间的整数；短字符串）进行缓存或驻留。

```python
# 比较整数
a = 10
b = 10
c = 20

print(f"{a} is {b} 结果: {a is b}") # 输出: True (因为 10 在小整数缓存范围内)
print(f"{a} == {b} 结果: {a == b}") # 输出: True

print(f"{a} is {c} 结果: {a is c}") # 输出: False
print(f"{a} == {c} 结果: {a == c}") # 输出: False

# 比较超出缓存范围的整数
x = 300
y = 300
print(f"{x} is {y} 结果: {x is y}") # 输出: False (通常情况下，因为 300 超出缓存范围)
print(f"{x} == {y} 结果: {x == y}") # 输出: True

# 比较字符串
str1 = "hello"
str2 = "hello"
str3 = "world"

print(f"'{str1}' is '{str2}' 结果: {str1 is str2}") # 输出: True (短字符串通常会被驻留)
print(f"'{str1}' == '{str2}' 结果: {str1 == str2}") # 输出: True

# 比较列表 (可变对象，通常总是创建新对象)
list1 = [1, 2, 3]
list2 = [1, 2, 3]
list3 = list1 # list3 引用了 list1 指向的同一个对象

print(f"{list1} is {list2} 结果: {list1 is list2}") # 输出: False (两个不同的列表对象，即使值相同)
print(f"{list1} == {list2} 结果: {list1 == list2}") # 输出: True (值相等)

print(f"{list1} is {list3} 结果: {list1 is list3}") # 输出: True (它们引用了同一个对象)
print(f"{list1} == {list3} 结果: {list1 == list3}") # 输出: True

# 比较 None
val_none = None
another_none = None
print(f"{val_none} is {another_none} 结果: {val_none is another_none}") # 输出: True
print(f"{val_none} == {another_none} 结果: {val_none == another_none}") # 输出: True
```

#### 2. 身份非运算符 (`is not`)

`is not` 运算符是 `is` 运算符的反义。它用于检查左侧的操作数和右侧的操作数是否 **不** 指向内存中的同一个对象。如果它们不是同一个对象，则返回 `True`；否则，返回 `False`。

```python
list_a = [1, 2]
list_b = [1, 2]
list_c = list_a

print(f"{list_a} is not {list_b} 结果: {list_a is not list_b}") # 输出: True (是两个不同的对象)
print(f"{list_a} is not {list_c} 结果: {list_a is not list_c}") # 输出: False (是同一个对象)

# 结合 None
user_input = None
if user_input is not None:
    print("用户输入了内容。")
else:
    print("用户输入为空。")
```

### 应用场景

`is` 和 `is not` 运算符在以下场景中特别有用：

- **检查是否为 `None`：** 这是 `is` 运算符最常见的用法。由于 `None` 是一个单例对象，始终推荐使用 `is None` 和 `is not None` 来检查变量是否为空。
    ```python
    my_variable = None
    if my_variable is None:
        print("变量为空。")
    ```

- **判断两个变量是否引用同一个可变对象：**
    ```python
    data1 = [10, 20]
    data2 = data1 # data2 和 data1 引用同一个列表对象
    data3 = [10, 20] # data3 是一个新的列表对象
    
    print(data1 is data2) # True
    print(data1 is data3) # False
    
    data1.append(30)
    print(data2) # 输出: [10, 20, 30] (data2 也变了，因为它们是同一个对象)
    print(data3) # 输出: [10, 20] (data3 没变)
    ```

### `id()` 函数

Python 提供了一个内置函数 `id()`，它可以返回对象的"身份"值，这个值通常是对象在内存中的地址。

```python
list_a = [1, 2, 3]
list_b = [1, 2, 3]
list_c = list_a

print(f"id(list_a): {id(list_a)}")
print(f"id(list_b): {id(list_b)}")
print(f"id(list_c): {id(list_c)}")

print(f"list_a is list_b: {list_a is list_b}") # False，id 不同
print(f"list_a is list_c: {list_a is list_c}") # True，id 相同
```

---

## input() 和 print() 函数

在交互式程序中，从用户那里获取数据是非常常见的需求。Python 的内置函数 `input()` 专门用于实现这一功能，而 `print()` 函数则用于显示信息给用户。

### `input()` 函数：获取用户输入

`input()` 函数会从标准输入（通常是键盘）读取一行文本，直到遇到换行符（用户按下回车）。它总是将读取到的内容作为 **字符串** 返回。

**语法：** `input([prompt])`

- `prompt` (可选参数)：一个字符串，作为提示信息显示给用户。

**基本用法：**

```python
# 程序会暂停，等待用户输入
user_data = input()
print(f"您输入了: {user_data}")

# 带提示信息的用法
name = input("请输入您的名字: ")
print(f"您好, {name}!")
```

**获取数字输入并进行转换：**

正如之前数据类型转换教程中提到的，`input()` 总是返回字符串。如果需要获取数字进行计算，就必须进行类型转换。

```python
# 获取用户的年龄
age_str = input("请输入您的年龄: ")

# 将字符串转换为整数
try:
    age_int = int(age_str)
    print(f"您的年龄是: {age_int} 岁。")
    print(f"再过一年您将是: {age_int + 1} 岁。")
except ValueError:
    print("输入的年龄无效，请确保输入的是一个整数。")

# 获取用户的身高（可能是浮点数）
height_str = input("请输入您的身高（米）: ")
try:
    height_float = float(height_str)
    print(f"您的身高是: {height_float} 米。")
except ValueError:
    print("输入的身高无效，请确保输入的是一个数字。")
```

### `print()` 函数：输出结果

`print()` 函数最简单的用法是直接在括号内放入你想要输出的内容。它会将内容显示到标准输出（通常是控制台或终端），并在输出末尾自动添加一个换行符。

**语法：** `print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)`

#### 基本用法

```python
print("Hello, Python!") # 输出字符串
print(123)            # 输出整数
print(3.14159)        # 输出浮点数

name = "Alice"
age = 30
print(name)           # 输出变量的值
print(age)            # 输出变量的值
```

#### 输出多个值

`print()` 函数可以接受多个值作为参数，它们之间用逗号 `,` 分隔。默认情况下，这些值之间会用一个空格作为分隔符。

```python
print("My name is", name, "and I am", age, "years old.")
# 输出: My name is Alice and I am 30 years old.

city = "New York"
temp = 25.5
print("Current city:", city, "Temperature:", temp, "degrees Celsius.")
# 输出: Current city: New York Temperature: 25.5 degrees Celsius.
```

#### `sep` 参数 (Separator)

`sep` 参数用于指定在打印多个值时，它们之间使用的分隔符。默认值是单个空格 `' '`。

```python
print("apple", "banana", "cherry")         # 默认分隔符是空格
# 输出: apple banana cherry

print("apple", "banana", "cherry", sep="-") # 使用连字符作为分隔符
# 输出: apple-banana-cherry

print("192", "168", "1", "1", sep=".")     # 打印 IP 地址
# 输出: 192.168.1.1

date_day = 10
date_month = 6
date_year = 2025
print(date_year, date_month, date_day, sep="/") # 打印日期
# 输出: 2025/6/10
```

#### `end` 参数 (End)

`end` 参数用于指定 `print()` 函数在输出所有内容后，在末尾添加的字符。默认值是换行符 `'\n'`。

```python
print("This is the first line.")
print("This is the second line.")
# 默认情况下，每次 print 都会换行

print("Hello", end=" ") # 不换行，以空格结尾
print("World!")         # 接着上面的 "Hello " 后面打印
# 输出: Hello World!

print("Counting: ", end="")
for i in range(1, 6):
    print(i, end="...") # 每次打印一个数字，后面跟着 "..."
print("Done!")         # 最后打印 Done! 并换行
# 输出: Counting: 1...2...3...4...5...Done!
```

### 结合 `input()` 和 `print()` 的实际应用

```python
# 简单的计算器程序
print("=== 简单计算器 ===")
print("支持加法 (+)、减法 (-)、乘法 (*)、除法 (/)")

# 获取第一个数字
num1_str = input("请输入第一个数字: ")
try:
    num1 = float(num1_str)
except ValueError:
    print("错误：第一个数字无效！")
    exit()

# 获取运算符
operator = input("请输入运算符 (+, -, *, /): ")
if operator not in ['+', '-', '*', '/']:
    print("错误：无效的运算符！")
    exit()

# 获取第二个数字
num2_str = input("请输入第二个数字: ")
try:
    num2 = float(num2_str)
except ValueError:
    print("错误：第二个数字无效！")
    exit()

# 执行计算
result = None
if operator == '+':
    result = num1 + num2
elif operator == '-':
    result = num1 - num2
elif operator == '*':
    result = num1 * num2
elif operator == '/':
    if num2 == 0:
        print("错误：除数不能为零！")
        exit()
    result = num1 / num2

# 显示结果
print(f"计算结果: {num1} {operator} {num2} = {result}")
```

---

## 格式化输出

在 Python 中，仅仅打印变量的值通常是不够的。我们经常需要将变量值嵌入到预定义的文本中，并以特定的格式（如小数位数、对齐方式、千位分隔符等）显示它们。

### F-strings (格式化字符串字面量) - Python 3.6+ 推荐

F-strings 是从 Python 3.6 版本引入的一种字符串格式化方式，它以其简洁、强大和高性能而迅速成为最受欢迎的方法。

**语法：** 在字符串前加上 `f` 或 `F`，然后用花括号 `{}` 包裹你想要嵌入的变量或表达式。

#### 基本用法：嵌入变量和表达式

```python
name = "Alice"
age = 30
salary = 5000.75

# 嵌入变量
print(f"My name is {name} and I am {age} years old.")
# 输出: My name is Alice and I am 30 years old.

# 嵌入表达式
print(f"Next year, {name} will be {age + 1} years old.")
# 输出: Next year, Alice will be 31 years old.

# 可以直接进行简单的运算
print(f"Monthly salary: ${salary}, Annual salary: ${salary * 12}.")
# 输出: Monthly salary: $5000.75, Annual salary: $60009.0.

# 嵌入函数调用
print(f"Name in uppercase: {name.upper()}")
# 输出: Name in uppercase: ALICE
```

#### 格式化说明符 (Format Specifiers)

F-strings 的强大之处在于可以在花括号内使用"格式化迷你语言"来控制输出的格式。格式化说明符位于冒号 `:` 之后。

**常用格式说明符：**

- **精度和浮点数：**
    ```python
    pi = 3.1415926535
    print(f"Pi (2 decimal places): {pi:.2f}")  # 输出: Pi (2 decimal places): 3.14
    print(f"Pi (4 decimal places): {pi:.4f}")  # 输出: Pi (4 decimal places): 3.1416 (四舍五入)
    
    progress = 0.857
    print(f"Progress: {progress:.2%}")         # 输出: Progress: 85.70%
    print(f"Progress (no decimals): {progress:.0%}") # 输出: Progress (no decimals): 86%
    ```

- **对齐和填充：**
    ```python
    item = "Book"
    price = 25.99
    
    print(f"|{item:<10}|{price:>10.2f}|") # 左对齐 item (10宽), 右对齐 price (10宽, 2小数)
    # 输出: |Book      |     25.99|
    
    print(f"|{item:^10}|{price:^10.2f}|") # 居中对齐
    # 输出: |   Book   |   25.99  |
    
    # 使用填充字符
    print(f"|{item:*^10}|") # 以 '*' 填充，居中对齐，总宽 10
    # 输出: |***Book***|
    ```

- **数字格式：**
    ```python
    large_number = 123456789
    print(f"Large number: {large_number:,}") # 输出: Large number: 123,456,789
    
    balance = 1234.56
    print(f"Balance: ${balance:,.2f}") # 输出: Balance: $1,234.56
    ```

- **不同进制：**
    ```python
    num = 255
    print(f"Decimal: {num}")         # 输出: Decimal: 255
    print(f"Binary: {num:b}")        # 输出: Binary: 11111111
    print(f"Octal: {num:o}")         # 输出: Octal: 377
    print(f"Hexadecimal: {num:x}")   # 输出: Hexadecimal: ff
    print(f"Hexadecimal (upper): {num:X}") # 输出: Hexadecimal (upper): FF
    ```

#### F-string 的调试功能 (Python 3.8+)

从 Python 3.8 开始，f-strings 提供了方便的调试功能，通过在变量名后添加 `=` 来同时打印变量名和其值。

```python
user = "Bob"
age = 42

print(f"{user=}")   # 输出: user='Bob'
print(f"{age=}")    # 输出: age=42

# 结合其他格式化
price = 99.99
print(f"{price=:.2f}") # 输出: price=99.99
```

### `.format()` 方法 (Python 2.6+ 备选方案)

`.format()` 方法是 f-strings 之前最推荐的格式化字符串的方式。它也使用花括号 `{}` 作为占位符，但变量或值是在 `.format()` 方法中作为参数传入的。

#### 位置参数格式化

```python
item = "Monitor"
price = 300
print("The {} costs ${}.".format(item, price))
# 输出: The Monitor costs $300.

# 可以通过索引指定位置
print("The {1} costs ${0}.".format(price, item)) # {1} 对应 item, {0} 对应 price
# 输出: The Monitor costs $300.
```

#### 关键字参数格式化

通过在占位符中指定关键字名称，并在 `.format()` 方法中以关键字参数的形式传入。这提高了可读性。

```python
print("My name is {name} and I am {age} years old.".format(name="David", age=25))
# 输出: My name is David and I am 25 years old.

# 结合位置参数和关键字参数 (关键字参数必须在位置参数之后)
print("Product: {0}, Quantity: {qty}, Price: {price}.".format("Keyboard", qty=5, price=75.50))
# 输出: Product: Keyboard, Quantity: 5, Price: 75.5.
```

#### 格式化说明符 (`.format()` 版)

`.format()` 方法也支持与 f-strings 相同的格式化迷你语言。

```python
pi = 3.1415926535
print("Pi (2 decimal places): {:.2f}".format(pi))
# 输出: Pi (2 decimal places): 3.14

large_num = 987654321
print("Large number with comma: {:,}".format(large_num))
# 输出: Large number with comma: 987,654,321

width = 15
value = "Center"
print("|{:^15}|".format(value)) # 居中对齐，总宽 15
# 输出: |   Center      |

percentage = 0.45
print("Completion: {:.0%}".format(percentage))
# 输出: Completion: 45%
```

### 选择哪种格式化方法？

- **推荐：F-strings (Python 3.6+)**
    - **优点：** 最简洁、最直观、性能最好。直接在字符串中嵌入变量和表达式，所见即所得。
    - **缺点：** 仅适用于 Python 3.6 及更高版本。
    - **场景：** 大多数新项目和支持 Python 3.6+ 的环境。

- **良好选择：`.format()` 方法**
    - **优点：** 兼容性好（Python 2.6+），功能强大，可读性也不错。
    - **缺点：** 相比 f-strings 稍微啰嗦一些，需要分别定义字符串和参数。
    - **场景：** 需要兼容旧版 Python 的项目，或者个人偏好。

**总之，对于现代 Python 开发，强烈推荐使用 **f-strings** 进行字符串格式化。**

---

## 总结

本教程涵盖了 Python 编程的基础知识，包括：

1. **变量的概念与命名**：理解变量作为数据容器的作用，掌握命名规则和最佳实践
2. **基本数据类型**：整数、浮点数、字符串和布尔类型的特性和用法
3. **数据类型转换**：使用 `int()`, `float()`, `str()`, `bool()` 等函数进行类型转换
4. **运算符**：算术、比较、逻辑、赋值、成员和身份运算符的使用
5. **输入输出**：使用 `input()` 获取用户输入，使用 `print()` 进行输出
6. **格式化输出**：掌握 f-strings 和 `.format()` 方法进行字符串格式化

这些基础知识是学习 Python 的基石，掌握它们将为后续学习更复杂的编程概念打下坚实的基础。建议通过编写小程序来练习这些概念，加深理解和记忆。