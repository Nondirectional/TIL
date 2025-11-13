# Python 数据结构详解

## 目录

1. [字符串 (str) 详解](#字符串-str-详解)
2. [列表 (list) 完整指南](#列表-list-完整指南)
3. [元组 (tuple) 详解](#元组-tuple-详解)
4. [字典 (dict) 完全指南](#字典-dict-完全指南)
5. [集合 (set) 详解](#集合-set-详解)

---

## 字符串 (str) 详解

字符串是 Python 中最常用的数据类型之一，用于表示文本数据。它们是不可变的序列，这意味着一旦创建就不能修改。

### 字符串的创建

```python
# 单引号、双引号、三引号
str1 = 'Hello'
str2 = "World"
str3 = '''Multi-line
string'''

# 转义字符
path = "C:\\Users\\name\\file.txt"
quote = "He said, \"Hello!\""

# 原始字符串
raw_path = r"C:\Users\name\file.txt"
```

### 字符串索引和切片

```python
text = "Python Programming"

# 索引访问
print(text[0])      # P
print(text[-1])     # g
print(text[7:10])   # Pro (切片)
print(text[:6])     # Python
print(text[7:])     # Programming
print(text[::2])    # Pto rgamng (步长为2)
print(text[::-1])   # gnimmargorP nohtyP (反转)
```

### 字符串方法详解

#### 大小写转换

```python
text = "Hello World"

print(text.upper())      # HELLO WORLD
print(text.lower())      # hello world
print(text.capitalize()) # Hello world
print(text.title())      # Hello World
print(text.swapcase())   # hELLO wORLD
```

#### 查找和替换

```python
text = "Python is powerful. Python is popular."

# 查找
print(text.find("Python"))    # 0 (首次出现位置)
print(text.rfind("Python"))   # 20 (最后一次出现位置)
print(text.index("Python"))   # 0 (找不到会报错 ValueError)
print(text.count("Python"))   # 2 (出现次数)

# 判断包含
print("Python" in text)       # True
print(text.startswith("Py"))   # True
print(text.endswith("."))      # True

# 替换
print(text.replace("Python", "Java"))    # Java is powerful. Java is popular.
print(text.replace("is", "was", 1))     # Python was powerful. Python is popular. (只替换1次)
```

#### 分割和连接

```python
text = "apple,banana,cherry,date"

# 分割
print(text.split(","))                    # ['apple', 'banana', 'cherry', 'date']
print(text.split(",", maxsplit=2))        # ['apple', 'banana', 'cherry,date']
print(text.splitlines())                  # 对于多行字符串按行分割

# 连接
fruits = ["apple", "banana", "cherry"]
print("-".join(fruits))                   # apple-banana-cherry
print(" ".join(fruits))                   # apple banana cherry
```

#### 去除空白字符

```python
text = "   Hello World   \n"

print(text.strip())       # Hello World (去除首尾空白)
print(text.lstrip())      # Hello World
# (去除左侧空白)
print(text.rstrip())      #    Hello World (去除右侧空白)
print(text.replace(" ", "")) # HelloWorld (去除所有空格)
```

#### 对齐和填充

```python
text = "Python"

print(f"'{text:<10}'")   # 'Python    ' (左对齐，宽度10)
print(f"'{text:>10}'")   # '    Python' (右对齐，宽度10)
print(f"'{text:^10}'")   # '  Python  ' (居中对齐，宽度10)
print(f"'{text:*^10}'")  # '**Python**' (居中，用*填充)

# 使用方法
print(text.center(10))   # '  Python  '
print(text.ljust(10))    # 'Python    '
print(text.rjust(10))    # '    Python'
```

#### 类型检查

```python
print("123".isdigit())     # True
print("abc".isalpha())     # True
print("abc123".isalnum())  # True
print("hello world".islower())  # True
print("HELLO".isupper())   # True
print("Hello World".istitle()) # True
print("   ".isspace())     # True
```

### 字符串格式化进阶

#### F-strings 高级用法

```python
name = "Alice"
age = 30
pi = 3.14159

# 基本格式化
print(f"Name: {name}, Age: {age}")

# 表达式计算
print(f"Next year: {age + 1}")
print(f"Name length: {len(name)}")

# 格式化说明符
print(f"Pi: {pi:.2f}")              # 3.14
print(f"Pi: {pi:10.2f}")            #       3.14 (总宽度10)
print(f"Name: {name:>10}")          #      Alice (右对齐)
print(f"Name: {name:<10}")          # Alice      (左对齐)
print(f"Name: {name:^10}")          #   Alice   (居中)

# 数字格式化
num = 1234567.89
print(f"Number: {num:,}")           # 1,234,567.89 (千位分隔符)
print(f"Number: {num:,.2f}")        # 1,234,567.89

# 百分比
percentage = 0.75
print(f"Complete: {percentage:.1%}") # 75.0%

# 日期时间
from datetime import datetime
now = datetime.now()
print(f"Time: {now:%Y-%m-%d %H:%M:%S}")  # 2025-01-01 12:30:45
```

#### `.format()` 方法

```python
# 位置参数
print("Hello {}, you are {} years old.".format("Bob", 25))

# 关键字参数
print("Hello {name}, you are {age} years old.".format(name="Bob", age=25))

# 混合使用
print("{0} {1} {0}".format("Hello", "World"))  # Hello World Hello

# 格式化
num = 123.4567
print("Number: {:.2f}".format(num))  # 123.46
print("Number: {:>10}".format(num))  #    123.4567
```

### 正则表达式与字符串处理

```python
import re

text = "My email is user@example.com and phone is 123-456-7890."

# 查找所有邮箱
emails = re.findall(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', text)
print(emails)  # ['user@example.com']

# 查找所有电话号码
phones = re.findall(r'\d{3}-\d{3}-\d{4}', text)
print(phones)  # ['123-456-7890']

# 替换
new_text = re.sub(r'\d{3}-\d{3}-\d{4}', '[PHONE]', text)
print(new_text)  # My email is user@example.com and phone is [PHONE].

# 分割
words = re.split(r'\s+', text)  # 按空白字符分割
print(words)
```

---

## 列表 (list) 完整指南

列表是 Python 中最常用的数据结构之一，它是一个有序的、可变的序列，可以包含任意类型的元素。

### 列表的创建

```python
# 空列表
empty_list = []
empty_list2 = list()

# 包含元素的列表
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True, [1, 2, 3]]

# 使用 list() 构造函数
str_list = list("hello")      # ['h', 'e', 'l', 'l', 'o']
tuple_list = list((1, 2, 3))  # [1, 2, 3]

# 列表推导式
squares = [x**2 for x in range(1, 6)]  # [1, 4, 9, 16, 25]
evens = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]
```

### 访问列表元素

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]

# 索引访问
print(fruits[0])      # apple
print(fruits[-1])     # elderberry
print(fruits[2:4])    # ['cherry', 'date']
print(fruits[:3])     # ['apple', 'banana', 'cherry']
print(fruits[2:])     # ['cherry', 'date', 'elderberry']
print(fruits[::2])    # ['apple', 'cherry', 'elderberry']
print(fruits[::-1])   # ['elderberry', 'date', 'cherry', 'banana', 'apple']
```

### 修改列表元素

```python
fruits = ["apple", "banana", "cherry", "date"]

# 直接修改
fruits[1] = "blueberry"
print(fruits)  # ['apple', 'blueberry', 'cherry', 'date']

# 通过切片修改
fruits[1:3] = ["blackberry", "coconut"]
print(fruits)  # ['apple', 'blackberry', 'coconut', 'date']

# 批量修改
fruits[::2] = ["avocado", "banana"]
print(fruits)  # ['avocado', 'blackberry', 'banana', 'date']
```

### 列表操作方法

#### 添加元素

```python
fruits = ["apple", "banana"]

# append() - 在末尾添加一个元素
fruits.append("cherry")
print(fruits)  # ['apple', 'banana', 'cherry']

# insert() - 在指定位置插入元素
fruits.insert(1, "blueberry")
print(fruits)  # ['apple', 'blueberry', 'banana', 'cherry']

# extend() - 扩展列表（添加多个元素）
fruits.extend(["date", "elderberry"])
print(fruits)  # ['apple', 'blueberry', 'banana', 'cherry', 'date', 'elderberry']

# 使用 + 运算符（创建新列表）
new_fruits = fruits + ["fig", "grape"]
print(new_fruits)
```

#### 删除元素

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]

# remove() - 删除指定值的元素
fruits.remove("cherry")
print(fruits)  # ['apple', 'banana', 'date', 'elderberry']

# pop() - 删除指定位置的元素（默认最后一个）
last_fruit = fruits.pop()
print(f"Removed: {last_fruit}")  # Removed: elderberry
print(fruits)  # ['apple', 'banana', 'date']

second_fruit = fruits.pop(1)
print(f"Removed: {second_fruit}")  # Removed: banana
print(fruits)  # ['apple', 'date']

# del 语句
del fruits[0]  # 删除第一个元素
print(fruits)  # ['date']

# clear() - 清空列表
fruits.clear()
print(fruits)  # []
```

#### 查找和排序

```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# 查找
print(len(numbers))        # 8 (长度)
print(numbers.count(1))    # 2 (1出现的次数)
print(numbers.index(4))    # 2 (4首次出现的位置)

# 排序
numbers_copy = numbers.copy()
numbers_copy.sort()        # 原地排序
print(numbers_copy)        # [1, 1, 2, 3, 4, 5, 6, 9]

numbers_copy.sort(reverse=True)  # 降序排序
print(numbers_copy)        # [9, 6, 5, 4, 3, 2, 1, 1]

# 使用 sorted() 函数（返回新列表）
sorted_numbers = sorted(numbers)
print(sorted_numbers)      # [1, 1, 2, 3, 4, 5, 6, 9]
print(numbers)             # 原列表不变

# 反转
numbers_copy.reverse()
print(numbers_copy)        # [1, 9, 6, 2, 5, 1, 4, 3]
```

### 列表高级操作

#### 列表推导式

```python
# 基本列表推导式
squares = [x**2 for x in range(1, 11)]
print(squares)  # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# 带条件的列表推导式
evens = [x for x in range(1, 21) if x % 2 == 0]
print(evens)  # [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

# 嵌套列表推导式
matrix = [[i*j for j in range(1, 4)] for i in range(1, 4)]
print(matrix)  # [[1, 2, 3], [2, 4, 6], [3, 6, 9]]

# 复杂条件的列表推导式
words = ["hello", "world", "python", "programming"]
long_words = [word.upper() for word in words if len(word) > 5]
print(long_words)  # ['PYTHON', 'PROGRAMMING']
```

#### 列表的函数式操作

```python
numbers = [1, 2, 3, 4, 5]

# map() - 对每个元素应用函数
squared = list(map(lambda x: x**2, numbers))
print(squared)  # [1, 4, 9, 16, 25]

# filter() - 过滤元素
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4]

# reduce() - 累积操作
from functools import reduce
product = reduce(lambda x, y: x * y, numbers)
print(product)  # 120
```

### 多维列表

```python
# 二维列表（矩阵）
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# 访问元素
print(matrix[1][2])  # 6

# 遍历二维列表
for row in matrix:
    for element in row:
        print(element, end=" ")
    print()

# 创建二维列表
rows, cols = 3, 4
matrix2 = [[0 for _ in range(cols)] for _ in range(rows)]
print(matrix2)
```

### 列表的性能考虑

```python
# 列表操作的复杂度
# 索引访问: O(1)
# 末尾添加 (append): O(1)
# 中间插入 (insert): O(n)
# 删除元素 (remove/pop): O(n)
# 查找元素 (in/index): O(n)

# 性能优化示例
# 使用 deque 进行频繁的两端操作
from collections import deque

# 对于大量数据，考虑使用生成器
def large_list():
    for i in range(1000000):
        yield i**2

# 使用集合进行快速成员检查
my_set = set(range(10000))
print(5000 in my_set)  # O(1) 操作
```

---

## 元组 (tuple) 详解

元组是 Python 中另一个重要的序列类型，与列表类似，但元组是不可变的。一旦创建，就不能修改其内容。

### 元组的创建

```python
# 创建元组
empty_tuple = ()
single_tuple = (42,)  # 注意逗号，否则会被认为是表达式
multi_tuple = (1, 2, 3, 4, 5)

# 不使用括号
tuple1 = 1, 2, 3
tuple2 = 1,

# 使用 tuple() 构造函数
list_tuple = tuple([1, 2, 3])      # (1, 2, 3)
str_tuple = tuple("hello")         # ('h', 'e', 'l', 'l', 'o')
range_tuple = tuple(range(5))      # (0, 1, 2, 3, 4)

# 元组推导式（使用生成器表达式）
squares_tuple = tuple(x**2 for x in range(1, 6))  # (1, 4, 9, 16, 25)
```

### 元组的访问

```python
fruits = ("apple", "banana", "cherry", "date")

# 索引访问
print(fruits[0])      # apple
print(fruits[-1])     # date
print(fruits[1:3])    # ('banana', 'cherry')
print(fruits[:2])     # ('apple', 'banana')
print(fruits[::2])    # ('apple', 'cherry')
print(fruits[::-1])   # ('date', 'cherry', 'banana', 'apple')

# 元组解包
a, b, c, d = fruits
print(a, b, c, d)  # apple banana cherry date

# 部分解包
first, *middle, last = fruits
print(first)   # apple
print(middle)  # ['banana', 'cherry']
print(last)    # date
```

### 元组的特性

#### 不可变性

```python
numbers = (1, 2, 3, 4, 5)

# 不能修改元素
# numbers[0] = 10  # TypeError: 'tuple' object does not support item assignment

# 不能添加或删除元素
# numbers.append(6)  # AttributeError: 'tuple' object has no attribute 'append'

# 但可以创建新元组
new_numbers = numbers + (6, 7, 8)
print(new_numbers)  # (1, 2, 3, 4, 5, 6, 7, 8)
```

#### 嵌套结构

```python
# 嵌套元组
nested = ((1, 2), (3, 4), (5, 6))
print(nested[1][0])  # 3

# 包含可变对象
mixed_tuple = (1, [2, 3], "hello")
# mixed_tuple[0] = 10  # 错误
mixed_tuple[1].append(4)  # 可以修改列表内容
print(mixed_tuple)  # (1, [2, 3, 4], 'hello')
```

### 元组的方法

```python
numbers = (1, 2, 3, 2, 4, 2, 5)

# count() - 统计元素出现次数
print(numbers.count(2))  # 3

# index() - 查找元素位置
print(numbers.index(3))  # 2
print(numbers.index(2, 2))  # 从索引2开始查找2，返回3

# len() - 获取长度
print(len(numbers))  # 7

# max(), min() - 最大最小值
print(max(numbers))  # 5
print(min(numbers))  # 1

# sum() - 求和
print(sum(numbers))  # 19
```

### 元组的应用场景

#### 作为字典的键

```python
# 元组可以作为字典的键（不可变）
coordinates = {(0, 0): "origin", (1, 1): "point one"}
print(coordinates[(0, 0)])  # origin

# 列表不能作为字典的键
# invalid_dict = {[1, 2]: "value"}  # TypeError: unhashable type: 'list'
```

#### 函数返回多个值

```python
def calculate_stats(numbers):
    return min(numbers), max(numbers), sum(numbers) / len(numbers)

stats = calculate_stats([1, 2, 3, 4, 5])
print(stats)  # (1, 5, 3.0)

min_val, max_val, avg = calculate_stats([1, 2, 3, 4, 5])
print(f"Min: {min_val}, Max: {max_val}, Avg: {avg}")
```

#### 命名元组

```python
from collections import namedtuple

# 创建命名元组类型
Point = namedtuple('Point', ['x', 'y'])
Person = namedtuple('Person', ['name', 'age', 'city'])

# 创建实例
p1 = Point(10, 20)
person1 = Person("Alice", 30, "New York")

# 访问属性
print(p1.x, p1.y)  # 10 20
print(person1.name)  # Alice

# 像普通元组一样使用
print(p1[0])  # 10

# _make() 和 _asdict()
p2 = Point._make([30, 40])
print(p2._asdict())  # {'x': 30, 'y': 40}
```

### 元组的性能优势

```python
import timeit
import sys

# 元组比列表占用更少内存
list_example = [1, 2, 3, 4, 5]
tuple_example = (1, 2, 3, 4, 5)

print(f"List size: {sys.getsizeof(list_example)} bytes")
print(f"Tuple size: {sys.getsizeof(tuple_example)} bytes")

# 元组创建速度更快
def create_list():
    return [i for i in range(1000)]

def create_tuple():
    return tuple(i for i in range(1000))

# 时间测试
list_time = timeit.timeit(create_list, number=1000)
tuple_time = timeit.timeit(create_tuple, number=1000)

print(f"List creation time: {list_time:.6f}")
print(f"Tuple creation time: {tuple_time:.6f}")
```

---

## 字典 (dict) 完全指南

字典是 Python 中最重要的数据结构之一，它存储键值对（key-value pairs）。字典是可变的、无序的（Python 3.7+ 保持插入顺序），键必须是唯一的且不可变的。

### 字典的创建

```python
# 空字典
empty_dict = {}
empty_dict2 = dict()

# 直接创建
student = {
    "name": "Alice",
    "age": 20,
    "major": "Computer Science",
    "grades": [85, 90, 78]
}

# 使用 dict() 构造函数
dict1 = dict(name="Bob", age=25, city="Boston")
dict2 = dict([("name", "Charlie"), ("age", 30)])

# 从键值对序列
pairs = [("a", 1), ("b", 2), ("c", 3)]
dict3 = dict(pairs)

# 字典推导式
squares = {x: x**2 for x in range(1, 6)}
print(squares)  # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# 条件字典推导式
even_squares = {x: x**2 for x in range(1, 11) if x % 2 == 0}
print(even_squares)  # {2: 4, 4: 16, 6: 36, 8: 64, 10: 100}
```

### 访问字典元素

```python
student = {
    "name": "Alice",
    "age": 20,
    "major": "Computer Science",
    "grades": [85, 90, 78]
}

# 使用键访问
print(student["name"])    # Alice
print(student["age"])     # 20

# 使用 get() 方法（更安全）
print(student.get("name"))    # Alice
print(student.get("city"))    # None
print(student.get("city", "Unknown"))  # Unknown (默认值)

# 检查键是否存在
print("name" in student)     # True
print("city" in student)     # False
print("city" not in student) # True

# 获取所有键、值、键值对
print(student.keys())    # dict_keys(['name', 'age', 'major', 'grades'])
print(student.values())  # dict_values(['Alice', 20, 'Computer Science', [85, 90, 78]])
print(student.items())   # dict_items([('name', 'Alice'), ('age', 20), ...])
```

### 修改字典

```python
student = {
    "name": "Alice",
    "age": 20,
    "major": "Computer Science"
}

# 添加或更新元素
student["age"] = 21  # 更新
student["city"] = "Boston"  # 添加
print(student)

# 使用 update() 方法批量更新
student.update({
    "grades": [85, 90, 78],
    "phone": "123-456-7890"
})
print(student)

# 使用 update() 添加另一个字典
extra_info = {"email": "alice@example.com", "year": "junior"}
student.update(extra_info)
print(student)
```

### 删除字典元素

```python
student = {
    "name": "Alice",
    "age": 20,
    "major": "Computer Science",
    "grades": [85, 90, 78]
}

# 使用 del 语句
del student["grades"]
print(student)  # {'name': 'Alice', 'age': 20, 'major': 'Computer Science'}

# 使用 pop() 方法
age = student.pop("age")
print(f"Removed age: {age}")  # Removed age: 20
print(student)  # {'name': 'Alice', 'major': 'Computer Science'}

# 使用 pop() 带默认值
city = student.pop("city", "Unknown")
print(f"City: {city}")  # City: Unknown

# 使用 popitem() 删除并返回最后一个键值对
last_item = student.popitem()
print(f"Removed: {last_item}")  # Removed: ('major', 'Computer Science')

# 清空字典
student.clear()
print(student)  # {}
```

### 字典的高级操作

#### 遍历字典

```python
student = {
    "name": "Alice",
    "age": 20,
    "major": "Computer Science",
    "grades": [85, 90, 78]
}

# 遍历键
print("Keys:")
for key in student:
    print(f"  {key}")

print("\nValues:")
for value in student.values():
    print(f"  {value}")

print("\nItems:")
for key, value in student.items():
    print(f"  {key}: {value}")

# 使用 enumerate() 获取索引
for index, (key, value) in enumerate(student.items(), 1):
    print(f"{index}. {key}: {value}")
```

#### 字典排序

```python
grades = {"Alice": 85, "Bob": 92, "Charlie": 78, "Diana": 96}

# 按键排序
sorted_by_key = dict(sorted(grades.items()))
print(sorted_by_key)  # {'Alice': 85, 'Bob': 92, 'Charlie': 78, 'Diana': 96}

# 按值排序
sorted_by_value = dict(sorted(grades.items(), key=lambda item: item[1]))
print(sorted_by_value)  # {'Charlie': 78, 'Alice': 85, 'Bob': 92, 'Diana': 96}

# 降序排序
sorted_desc = dict(sorted(grades.items(), key=lambda item: item[1], reverse=True))
print(sorted_desc)  # {'Diana': 96, 'Bob': 92, 'Alice': 85, 'Charlie': 78}
```

#### 字典合并

```python
dict1 = {"a": 1, "b": 2, "c": 3}
dict2 = {"d": 4, "e": 5, "f": 6}

# Python 3.9+ 使用 | 操作符合并
merged = dict1 | dict2
print(merged)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5, 'f': 6}

# 使用 update()
merged_update = dict1.copy()
merged_update.update(dict2)
print(merged_update)

# 使用 {**dict1, **dict2}
merged_unpack = {**dict1, **dict2}
print(merged_unpack)

# 处理重复键（后面的覆盖前面的）
dict3 = {"a": 10, "b": 20}
dict4 = {"b": 30, "c": 40}
merged_conflict = {**dict3, **dict4}
print(merged_conflict)  # {'a': 10, 'b': 30, 'c': 40}
```

#### 嵌套字典

```python
# 嵌套字典结构
university = {
    "name": "Tech University",
    "students": {
        "CS101": {
            "Alice": {"age": 20, "grades": [85, 90, 78]},
            "Bob": {"age": 21, "grades": [92, 88, 95]}
        },
        "MATH201": {
            "Charlie": {"age": 22, "grades": [78, 82, 80]},
            "Diana": {"age": 19, "grades": [95, 98, 97]}
        }
    }
}

# 访问嵌套数据
alice_grades = university["students"]["CS101"]["Alice"]["grades"]
print(f"Alice's grades: {alice_grades}")

# 添加新数据
university["students"]["CS101"]["Eve"] = {"age": 23, "grades": [88, 91, 89]}

# 计算平均分
for course, students in university["students"].items():
    print(f"\nCourse: {course}")
    for name, info in students.items():
        avg_grade = sum(info["grades"]) / len(info["grades"])
        print(f"  {name}: Average grade = {avg_grade:.2f}")
```

### 特殊字典类型

#### defaultdict

```python
from collections import defaultdict

# 自动为不存在的键创建默认值
word_count = defaultdict(int)
text = "hello world hello python world hello"

words = text.split()
for word in words:
    word_count[word] += 1

print(word_count)  # defaultdict(<class 'int'>, {'hello': 3, 'world': 2, 'python': 1})

# 使用 list 作为默认值
groups = defaultdict(list)
data = [('a', 1), ('b', 2), ('a', 3), ('b', 4), ('c', 5)]

for key, value in data:
    groups[key].append(value)

print(groups)  # defaultdict(<class 'list'>, {'a': [1, 3], 'b': [2, 4], 'c': [5]})
```

#### OrderedDict

```python
from collections import OrderedDict

# 保持插入顺序
ordered = OrderedDict()
ordered["first"] = 1
ordered["second"] = 2
ordered["third"] = 3

print(ordered)  # OrderedDict([('first', 1), ('second', 2), ('third', 3)])

# 移动元素到末尾
ordered.move_to_end("first")
print(ordered)  # OrderedDict([('second', 2), ('third', 3), ('first', 1)])

# 弹出最后元素
last_item = ordered.popitem()
print(f"Removed: {last_item}")  # Removed: ('first', 1)
```

#### Counter

```python
from collections import Counter

# 计数器
word_count = Counter("hello world hello python world hello".split())
print(word_count)  # Counter({'hello': 3, 'world': 2, 'python': 1})

# 最常见的元素
print(word_count.most_common(2))  # [('hello', 3), ('world', 2)]

# 计数操作
counter1 = Counter(a=3, b=1, c=5)
counter2 = Counter(a=1, b=2, d=3)

# 加法
print(counter1 + counter2)  # Counter({'c': 5, 'a': 4, 'b': 3, 'd': 3})

# 减法
print(counter1 - counter2)  # Counter({'c': 5, 'a': 2})

# 交集
print(counter1 & counter2)  # Counter({'a': 1, 'b': 1})

# 并集
print(counter1 | counter2)  # Counter({'c': 5, 'a': 3, 'd': 3, 'b': 2})
```

### 字典的性能考虑

```python
# 字典操作的时间复杂度
# 访问/设置/删除: 平均 O(1), 最坏 O(n)
# 包含检查 (in): 平均 O(1), 最坏 O(n)

# 内存使用
import sys

small_dict = {"a": 1, "b": 2}
large_dict = {f"key_{i}": i for i in range(1000)}

print(f"Small dict size: {sys.getsizeof(small_dict)} bytes")
print(f"Large dict size: {sys.getsizeof(large_dict)} bytes")

# 优化建议
# 1. 使用合适的数据结构（如用列表代替小字典）
# 2. 避免过深的嵌套
# 3. 考虑使用 __slots__ 优化内存（对于自定义类）
```

---

## 集合 (set) 详解

集合是 Python 中用于存储唯一元素的无序集合。集合是可变的，元素必须是不可变的（哈希able）。

### 集合的创建

```python
# 创建集合
empty_set = set()  # 空集合
numbers = {1, 2, 3, 4, 5}
fruits = {"apple", "banana", "cherry"}

# 从列表创建（自动去重）
numbers_with_duplicates = [1, 2, 2, 3, 3, 3, 4]
unique_numbers = set(numbers_with_duplicates)
print(unique_numbers)  # {1, 2, 3, 4}

# 从字符串创建
char_set = set("hello")
print(char_set)  # {'h', 'e', 'l', 'o'}

# 集合推导式
squares = {x**2 for x in range(1, 11)}
print(squares)  # {1, 4, 9, 16, 25, 36, 49, 64, 81, 100}

# 条件集合推导式
even_squares = {x**2 for x in range(1, 21) if x % 2 == 0}
print(even_squares)  # {4, 16, 36, 64, 100, 144, 196, 256, 324, 400}
```

### 集合的基本操作

#### 添加和删除元素

```python
fruits = {"apple", "banana", "cherry"}

# 添加元素
fruits.add("date")
print(fruits)  # {'apple', 'banana', 'cherry', 'date'}

# 添加多个元素
fruits.update(["elderberry", "fig", "grape"])
print(fruits)  # {'apple', 'banana', 'cherry', 'date', 'elderberry', 'fig', 'grape'}

# 删除元素
fruits.remove("banana")  # 如果元素不存在会报错
fruits.discard("mango")  # 如果元素不存在不会报错

# 随机删除并返回元素
removed = fruits.pop()
print(f"Removed: {removed}")

# 清空集合
fruits.clear()
print(fruits)  # set()
```

#### 成员检查

```python
fruits = {"apple", "banana", "cherry", "date"}

# 检查元素是否存在
print("apple" in fruits)   # True
print("mango" in fruits)   # False
print("mango" not in fruits)  # True

# 获取集合大小
print(len(fruits))  # 4
```

### 集合运算

#### 数学集合运算

```python
set1 = {1, 2, 3, 4, 5}
set2 = {4, 5, 6, 7, 8}

# 并集 (| 或 union())
print(set1 | set2)  # {1, 2, 3, 4, 5, 6, 7, 8}
print(set1.union(set2))  # {1, 2, 3, 4, 5, 6, 7, 8}

# 交集 (& 或 intersection())
print(set1 & set2)  # {4, 5}
print(set1.intersection(set2))  # {4, 5}

# 差集 (- 或 difference())
print(set1 - set2)  # {1, 2, 3}
print(set1.difference(set2))  # {1, 2, 3}
print(set2 - set1)  # {6, 7, 8}

# 对称差集 (^ 或 symmetric_difference())
print(set1 ^ set2)  # {1, 2, 3, 6, 7, 8}
print(set1.symmetric_difference(set2))  # {1, 2, 3, 6, 7, 8}
```

#### 集合关系操作

```python
set1 = {1, 2, 3, 4, 5}
set2 = {1, 2, 3}
set3 = {4, 5, 6, 7, 8}

# 子集检查
print(set2.issubset(set1))  # True (set2 是 set1 的子集)
print(set2 <= set1)  # True
print(set1.issubset(set2))  # False

# 真子集检查
print(set2 < set1)  # True (set2 是 set1 的真子集)
print(set1 < set1)  # False (集合不是自己的真子集)

# 超集检查
print(set1.issuperset(set2))  # True (set1 是 set2 的超集)
print(set1 >= set2)  # True

# 不相交检查
print(set1.isdisjoint(set3))  # False (有共同元素 4, 5)
print(set2.isdisjoint(set3))  # True (没有共同元素)
```

#### 就地操作

```python
set1 = {1, 2, 3}
set2 = {3, 4, 5}

# 就地并集
set1.update(set2)
print(set1)  # {1, 2, 3, 4, 5}

# 就地交集
set1.intersection_update({1, 2, 3, 4})
print(set1)  # {1, 2, 3, 4}

# 就地差集
set1.difference_update({2, 3})
print(set1)  # {1, 4}

# 就地对称差集
set1.symmetric_difference_update({4, 5, 6})
print(set1)  # {1, 5, 6}
```

### 集合的高级应用

#### 去重操作

```python
# 列表去重
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
unique_numbers = list(set(numbers))
print(unique_numbers)  # [1, 2, 3, 4] (顺序可能不同)

# 保持顺序的去重
def remove_duplicates_preserve_order(seq):
    seen = set()
    result = []
    for item in seq:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

ordered_unique = remove_duplicates_preserve_order([1, 2, 2, 3, 1, 4, 3, 5])
print(ordered_unique)  # [1, 2, 3, 4, 5]
```

#### 集合用于算法

```python
# 查找共同元素
list1 = [1, 2, 3, 4, 5, 6]
list2 = [4, 5, 6, 7, 8, 9]

common_elements = set(list1) & set(list2)
print(common_elements)  # {4, 5, 6}

# 查找只在一个列表中出现的元素
unique_to_list1 = set(list1) - set(list2)
unique_to_list2 = set(list2) - set(list1)
print(f"Only in list1: {unique_to_list1}")  # {1, 2, 3}
print(f"Only in list2: {unique_to_list2}")  # {8, 9, 7}
```

#### frozenset (不可变集合)

```python
# 创建不可变集合
frozen = frozenset([1, 2, 3, 4, 5])
print(frozen)  # frozenset({1, 2, 3, 4, 5})

# frozenset 可以作为字典的键
cache = {
    frozenset([1, 2, 3]): "Group A",
    frozenset([4, 5, 6]): "Group B"
}
print(cache[frozenset([1, 2, 3])])  # Group A

# frozenset 支持集合运算
frozen1 = frozenset([1, 2, 3])
frozen2 = frozenset([3, 4, 5])
print(frozen1 | frozen2)  # frozenset({1, 2, 3, 4, 5})
```

### 集合的性能考虑

```python
import timeit

# 集合 vs 列表的成员检查性能
large_list = list(range(10000))
large_set = set(range(10000))
target = 9999

# 列表成员检查
list_time = timeit.timeit(lambda: target in large_list, number=1000)
print(f"List membership check: {list_time:.6f}")

# 集合成员检查
set_time = timeit.timeit(lambda: target in large_set, number=1000)
print(f"Set membership check: {set_time:.6f}")

# 集合 vs 列表的去重性能
duplicates = list(range(5000)) + list(range(5000))

# 列表去重
list_unique_time = timeit.timeit(
    lambda: list(dict.fromkeys(duplicates)),
    number=100
)
print(f"List deduplication: {list_unique_time:.6f}")

# 集合去重
set_unique_time = timeit.timeit(
    lambda: list(set(duplicates)),
    number=100
)
print(f"Set deduplication: {set_unique_time:.6f}")
```

### 实际应用示例

```python
# 示例1：文本分析 - 找出常用词
def find_common_words(text, n=10):
    words = text.lower().split()
    # 过滤停用词
    stop_words = {"the", "a", "an", "and", "or", "but", "in", "on", "at", "to", "for", "of", "with", "by"}
    filtered_words = [word for word in words if word not in stop_words]

    # 统计词频
    word_count = {}
    for word in filtered_words:
        word_count[word] = word_count.get(word, 0) + 1

    # 找出最常见的词
    common_words = sorted(word_count.items(), key=lambda x: x[1], reverse=True)[:n]
    return common_words

# 示例2：权限检查
def check_permissions(user_permissions, required_permissions):
    user_perm_set = set(user_permissions)
    required_perm_set = set(required_permissions)

    missing = required_perm_set - user_perm_set
    has_all = len(missing) == 0

    return has_all, missing

# 示例3：数据分析 - 分组统计
def group_by_category(data, key_func):
    groups = {}
    for item in data:
        key = key_func(item)
        if key not in groups:
            groups[key] = set()
        groups[key].add(item)
    return groups

# 使用示例
students = [
    ("Alice", "CS"), ("Bob", "Math"), ("Charlie", "CS"),
    ("Diana", "Physics"), ("Eve", "Math"), ("Frank", "CS")
]

grouped = group_by_category(students, lambda x: x[1])
print(grouped)
# {'CS': {('Alice', 'CS'), ('Charlie', 'CS'), ('Frank', 'CS')},
#  'Math': {('Bob', 'Math'), ('Eve', 'Math')},
#  'Physics': {('Diana', 'Physics')}}
```

---

## 总结

Python 提供了丰富的内置数据结构，每种结构都有其特定的用途和优势：

### 选择合适的数据结构

- **字符串 (str)**：处理文本数据，不可变，支持丰富的字符串操作方法。
- **列表 (list)**：有序的可变序列，适合存储和操作同类型或混合类型的数据。
- **元组 (tuple)**：有序的不可变序列，适合存储不应该改变的数据，可以作为字典的键。
- **字典 (dict)**：键值对映射，适合快速查找和关联数据，Python 3.7+ 保持插入顺序。
- **集合 (set)**：无序的唯一元素集合，适合去重、成员检查和集合运算。

### 性能考虑

- **成员检查**：集合和字典是 O(1)，列表和元组是 O(n)
- **插入/删除**：列表末尾是 O(1)，字典/集合平均是 O(1)
- **内存使用**：元组比列表更节省内存，集合和字典需要额外的哈希表开销

### 最佳实践

1. 根据使用场景选择合适的数据结构
2. 利用数据结构的特性来优化代码性能
3. 使用推导式来创建简洁高效的数据结构
4. 注意可变性对程序的影响
5. 合理使用内置方法和特殊数据类型（如 defaultdict, Counter）

掌握这些数据结构及其操作方法，将帮助您编写更高效、更优雅的 Python 代码。