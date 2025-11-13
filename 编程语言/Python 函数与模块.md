# Python 函数与模块

## 目录

1. [函数的定义与调用](#函数的定义与调用)
2. [函数参数详解](#函数参数详解)
3. [返回值与作用域](#返回值与作用域)
4. [lambda 表达式 (匿名函数)](#lambda-表达式-匿名函数)
5. [递归函数](#递归函数)
6. [高阶函数 (map, filter, reduce)](#高阶函数-map-filter-reduce)
7. [模块的导入与使用](#模块的导入与使用)
8. [常用内置模块](#常用内置模块)
9. [自定义模块创建](#自定义模块创建)
10. [包 (package) 的使用](#包-package-的使用)
11. [函数式编程概念](#函数式编程概念)
12. [函数与模块最佳实践](#函数与模块最佳实践)

---

## 函数的定义与调用

函数是组织好的、可重复使用的代码块，用于执行特定任务。函数是 Python 编程的核心概念之一。

### 基本函数定义

```python
# 基本语法
def function_name(parameters):
    """函数文档字符串"""
    # 函数体
    return value  # 可选

# 简单的问候函数
def greet():
    """简单的问候函数"""
    print("Hello, World!")

# 调用函数
greet()  # 输出: Hello, World!

# 带参数的函数
def greet_person(name):
    """向特定的人问好"""
    print(f"Hello, {name}!")

greet_person("Alice")  # 输出: Hello, Alice!
greet_person("Bob")    # 输出: Hello, Bob!
```

### 带返回值的函数

```python
# 计算平方的函数
def square(x):
    """返回数字的平方"""
    return x * x

result = square(5)
print(f"5的平方是: {result}")  # 输出: 5的平方是: 25

# 计算矩形面积的函数
def calculate_rectangle_area(width, height):
    """计算矩形面积"""
    area = width * height
    return area

area = calculate_rectangle_area(5, 3)
print(f"矩形面积: {area}")  # 输出: 矩形面积: 15

# 多个返回值
def get_name_and_age():
    """返回姓名和年龄"""
    name = "Alice"
    age = 25
    return name, age  # 实际上返回一个元组

person_name, person_age = get_name_and_age()
print(f"姓名: {person_name}, 年龄: {person_age}")  # 输出: 姓名: Alice, 年龄: 25
```

### 函数文档字符串

```python
def calculate_bmi(weight, height):
    """
    计算身体质量指数 (BMI)

    Args:
        weight (float): 体重，单位公斤
        height (float): 身高，单位米

    Returns:
        float: BMI 值

    Raises:
        ValueError: 当体重或身高不是正数时

    Example:
        >>> calculate_bmi(70, 1.75)
        22.86
    """
    if weight <= 0 or height <= 0:
        raise ValueError("体重和身高必须是正数")

    bmi = weight / (height ** 2)
    return round(bmi, 2)

# 查看函数文档
help(calculate_bmi)
print(calculate_bmi.__doc__)
```

### 函数的类型提示 (Type Hints)

```python
# 基本类型提示
def add_numbers(a: int, b: int) -> int:
    """两个整数相加"""
    return a + b

# 复杂类型提示
from typing import List, Dict, Optional, Union

def process_numbers(numbers: List[int]) -> List[int]:
    """处理数字列表"""
    return [x * 2 for x in numbers]

def get_user_info(user_id: int) -> Optional[Dict[str, Union[str, int]]]:
    """获取用户信息"""
    users = {
        1: {"name": "Alice", "age": 25},
        2: {"name": "Bob", "age": 30}
    }
    return users.get(user_id)

# 使用类型提示
result = add_numbers(5, 3)
processed = process_numbers([1, 2, 3, 4, 5])
user_info = get_user_info(1)

print(f"相加结果: {result}")
print(f"处理后的数字: {processed}")
print(f"用户信息: {user_info}")
```

---

## 函数参数详解

Python 函数支持多种参数类型，提供了很大的灵活性。

### 位置参数 (Positional Arguments)

```python
def describe_person(name, age, city):
    """描述一个人的基本信息"""
    print(f"{name} 今年 {age} 岁，来自 {city}")

# 按位置传递参数
describe_person("Alice", 25, "New York")
# 输出: Alice 今年 25 岁，来自 New York

describe_person("Bob", 30, "Boston")
# 输出: Bob 今年 30 岁，来自 Boston
```

### 关键字参数 (Keyword Arguments)

```python
def describe_person(name, age, city):
    """描述一个人的基本信息"""
    print(f"{name} 今年 {age} 岁，来自 {city}")

# 使用关键字参数（顺序不重要）
describe_person(name="Charlie", city="Chicago", age=35)
# 输出: Charlie 今年 35 岁，来自 Chicago

describe_person(age=28, city="Seattle", name="Diana")
# 输出: Diana 今年 28 岁，来自 Seattle

# 混合使用（位置参数必须在前面）
describe_person("Eve", city="Portland", age=22)
# 输出: Eve 今年 22 岁，来自 Portland
```

### 默认参数 (Default Arguments)

```python
def create_user(username, email, role="user", active=True):
    """创建用户"""
    user = {
        "username": username,
        "email": email,
        "role": role,
        "active": active
    }
    return user

# 使用默认值
user1 = create_user("alice", "alice@example.com")
print(user1)
# 输出: {'username': 'alice', 'email': 'alice@example.com', 'role': 'user', 'active': True}

# 覆盖默认值
user2 = create_user("admin", "admin@example.com", role="admin")
print(user2)
# 输出: {'username': 'admin', 'email': 'admin@example.com', 'role': 'admin', 'active': True}

# 覆盖所有默认值
user3 = create_user("inactive_user", "user@example.com", role="user", active=False)
print(user3)
# 输出: {'username': 'inactive_user', 'email': 'user@example.com', 'role': 'user', 'active': False}
```

### 可变参数 (Variable Arguments)

#### *args - 任意数量的位置参数

```python
def sum_all(*numbers):
    """计算所有数字的总和"""
    total = 0
    for num in numbers:
        total += num
    return total

# 调用方式
print(sum_all(1, 2, 3))        # 输出: 6
print(sum_all(1, 2, 3, 4, 5))  # 输出: 15
print(sum_all())               # 输出: 0

# 与其他参数结合使用
def create_profile(name, *hobbies):
    """创建用户档案"""
    profile = {"name": name}
    if hobbies:
        profile["hobbies"] = list(hobbies)
    return profile

profile1 = create_profile("Alice", "reading", "swimming", "coding")
print(profile1)
# 输出: {'name': 'Alice', 'hobbies': ['reading', 'swimming', 'coding']}

profile2 = create_profile("Bob")
print(profile2)
# 输出: {'name': 'Bob'}
```

#### **kwargs - 任意数量的关键字参数

```python
def create_config(**settings):
    """创建配置字典"""
    return settings

# 调用方式
config1 = create_config(host="localhost", port=8080, debug=True)
print(config1)
# 输出: {'host': 'localhost', 'port': 8080, 'debug': True}

config2 = create_config()
print(config2)
# 输出: {}

# 与其他参数结合使用
def send_email(to, subject, **options):
    """发送邮件"""
    email = {
        "to": to,
        "subject": subject,
        "from": options.get("from", "noreply@example.com"),
        "cc": options.get("cc", []),
        "bcc": options.get("bcc", []),
        "priority": options.get("priority", "normal")
    }
    return email

email1 = send_email(
    "user@example.com",
    "Welcome!",
    from="admin@example.com",
    cc=["manager@example.com"],
    priority="high"
)
print(email1)
```

#### 组合使用 *args 和 **kwargs

```python
def complex_function(required_arg, default_arg="default", *args, **kwargs):
    """复杂的参数组合示例"""
    print(f"必需参数: {required_arg}")
    print(f"默认参数: {default_arg}")
    print(f"额外位置参数: {args}")
    print(f"额外关键字参数: {kwargs}")

# 调用示例
complex_function(
    "必需值",                    # required_arg
    "自定义默认值",              # default_arg
    1, 2, 3,                    # *args
    key1="value1",              # **kwargs
    key2="value2"
)
```

### 参数传递的注意事项

```python
# 默认参数的陷阱
def bad_append(value, lst=[]):
    """不好的示例：使用可变对象作为默认参数"""
    lst.append(value)
    return lst

# 问题演示
result1 = bad_append(1)  # [1]
result2 = bad_append(2)  # [1, 2] - 使用了同一个列表！

print(f"结果1: {result1}")
print(f"结果2: {result2}")

# 正确的做法
def good_append(value, lst=None):
    """正确的示例：使用 None 作为默认参数"""
    if lst is None:
        lst = []
    lst.append(value)
    return lst

result3 = good_append(1)  # [1]
result4 = good_append(2)  # [2] - 每次都创建新列表

print(f"结果3: {result3}")
print(f"结果4: {result4}")

# 强制关键字参数
def divide(a, b, *, precision=2):
    """除法函数，precision 必须作为关键字参数"""
    result = a / b
    return round(result, precision)

# 正确调用
print(divide(10, 3, precision=4))  # 输出: 3.3333
print(divide(10, 3))              # 输出: 3.33

# 错误调用
# divide(10, 3, 4)  # TypeError: divide() takes 2 positional arguments but 3 were given
```

---

## 返回值与作用域

### 函数返回值

```python
# 多个返回值
def get_statistics(numbers):
    """计算统计数据"""
    if not numbers:
        return None, None, None, None

    total = sum(numbers)
    count = len(numbers)
    average = total / count
    maximum = max(numbers)
    minimum = min(numbers)

    return total, average, maximum, minimum

# 解包返回值
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
total, avg, max_val, min_val = get_statistics(numbers)

print(f"总和: {total}")
print(f"平均值: {avg}")
print(f"最大值: {max_val}")
print(f"最小值: {min_val}")

# 有选择地接收返回值
stats = get_statistics(numbers)
if stats[0] is not None:
    print(f"统计成功，总和: {stats[0]}")

# 使用 _ 忽略不需要的返回值
_, avg, _, _ = get_statistics(numbers)
print(f"只关心平均值: {avg}")
```

### 作用域 (Scope)

```python
# 局部作用域
def local_scope_example():
    """局部作用域示例"""
    local_var = "我在函数内部"
    print(f"函数内部: {local_var}")

local_scope_example()
# print(local_var)  # NameError: name 'local_var' is not defined

# 全局作用域
global_var = "我在全局作用域"

def access_global():
    """访问全局变量"""
    print(f"函数内访问全局变量: {global_var}")

access_global()
print(f"函数外访问全局变量: {global_var}")

# 修改全局变量
def modify_global():
    """尝试修改全局变量"""
    global_var = "我在函数内被修改了"
    print(f"函数内修改后: {global_var}")

modify_global()
print(f"函数外查看修改结果: {global_var}")  # 还是原来的值！

# 正确修改全局变量
def properly_modify_global():
    """正确修改全局变量"""
    global global_var
    global_var = "我被正确修改了"
    print(f"函数内正确修改: {global_var}")

properly_modify_global()
print(f"函数外查看正确修改结果: {global_var}")

# 嵌套作用域
def outer_function():
    """外层函数"""
    outer_var = "我在外层函数"

    def inner_function():
        """内层函数"""
        # nonlocal 关键字用于修改外层（非全局）变量
        nonlocal outer_var
        outer_var = "我在内层函数被修改了"
        print(f"内层函数中: {outer_var}")

    print(f"修改前 - 外层函数中: {outer_var}")
    inner_function()
    print(f"修改后 - 外层函数中: {outer_var}")

outer_function()
```

### 变量查找规则 (LEGB 规则)

```python
# L (Local): 局部作用域
# E (Enclosing): 嵌套作用域
# G (Global): 全局作用域
# B (Built-in): 内置作用域

x = "全局变量"

def outer():
    x = "外层变量"

    def inner():
        x = "内层变量"
        print(f"内层: {x}")

    inner()
    print(f"外层: {x}")

outer()
print(f"全局: {x}")

# 演示 LEGB 查找规则
def demonstrate_legb():
    """演示 LEGB 规则"""
    # L: 局部变量
    local_var = "局部变量"

    # 访问内置函数
    print(len("hello"))  # B: 内置作用域

    # 访问全局变量
    print(f"全局 x: {x}")  # G: 全局作用域

    # 创建嵌套变量
    enclosing_var = "嵌套变量"

    def nested():
        # nonlocal enclosing_var  # 如果要修改嵌套变量
        print(f"嵌套变量: {enclosing_var}")  # E: 嵌套作用域
        print(f"局部变量: {local_var}")  # L: 局部作用域（通过闭包访问）

    nested()

demonstrate_legb()
```

---

## lambda 表达式 (匿名函数)

lambda 表达式是一种创建匿名函数的简洁方式，通常用于简单的函数操作。

### 基本 lambda 语法

```python
# 基本语法: lambda arguments: expression
square = lambda x: x ** 2
print(f"5的平方: {square(5)}")  # 输出: 5的平方: 25

add = lambda x, y: x + y
print(f"3 + 7 = {add(3, 7)}")  # 输出: 3 + 7 = 10

# 无参数的 lambda
get_greeting = lambda: "Hello, World!"
print(get_greeting())  # 输出: Hello, World!
```

### lambda 与内置函数结合

```python
# 与 map() 结合
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x ** 2, numbers))
print(f"平方列表: {squared}")  # [1, 4, 9, 16, 25]

# 与 filter() 结合
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(f"偶数列表: {even_numbers}")  # [2, 4]

# 与 sorted() 结合
students = [
    {"name": "Alice", "score": 85},
    {"name": "Bob", "score": 92},
    {"name": "Charlie", "score": 78}
]

# 按分数排序
sorted_by_score = sorted(students, key=lambda x: x["score"])
print("按分数排序:")
for student in sorted_by_score:
    print(f"  {student['name']}: {student['score']}")

# 按姓名排序
sorted_by_name = sorted(students, key=lambda x: x["name"])
print("按姓名排序:")
for student in sorted_by_name:
    print(f"  {student['name']}: {student['score']}")
```

### lambda 在数据结构中的应用

```python
# 字典排序
scores = {"Alice": 85, "Bob": 92, "Charlie": 78, "Diana": 96}

# 按值排序
sorted_by_value = dict(sorted(scores.items(), key=lambda item: item[1]))
print(f"按值排序: {sorted_by_value}")

# 按键长度排序
words = ["apple", "banana", "kiwi", "strawberry", "orange"]
sorted_by_length = sorted(words, key=lambda word: len(word))
print(f"按长度排序: {sorted_by_length}")

# 列表复杂排序
people = [
    ("Alice", 25, "Engineer"),
    ("Bob", 30, "Designer"),
    ("Charlie", 22, "Developer"),
    ("Diana", 28, "Manager")
]

# 先按年龄排序，再按姓名排序
sorted_people = sorted(people, key=lambda x: (x[1], x[0]))
print("复合排序:")
for person in sorted_people:
    print(f"  {person}")
```

### lambda 的局限性

```python
# lambda 只能包含单个表达式
# 正确示例
simple_lambda = lambda x: x * 2

# 错误示例 - lambda 不能包含语句
# complex_lambda = lambda x:
#     if x > 0:
#         return x * 2
#     else:
#         return x

# 对于复杂逻辑，使用普通函数
def complex_function(x):
    """复杂的条件逻辑"""
    if x > 0:
        return x * 2
    elif x < 0:
        return x * 3
    else:
        return 0

print(f"复杂函数结果: {complex_function(5)}")  # 10
print(f"复杂函数结果: {complex_function(-3)}")  # -9
print(f"复杂函数结果: {complex_function(0)}")  # 0
```

---

## 递归函数

递归函数是指函数调用自身的函数，通常用于解决可以分解为相同子问题的问题。

### 基本递归示例

```python
# 计算阶乘
def factorial(n):
    """递归计算阶乘"""
    if n <= 1:
        return 1
    else:
        return n * factorial(n - 1)

print(f"5! = {factorial(5)}")  # 输出: 5! = 120
print(f"3! = {factorial(3)}")  # 输出: 3! = 6
print(f"0! = {factorial(0)}")  # 输出: 0! = 1

# 斐波那契数列
def fibonacci(n):
    """递归计算斐波那契数列"""
    if n <= 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fibonacci(n - 1) + fibonacci(n - 2)

print(f"斐波那契数列第10项: {fibonacci(10)}")  # 输出: 55
print(f"斐波那契数列第7项: {fibonacci(7)}")    # 输出: 13

# 最大公约数
def gcd(a, b):
    """递归计算最大公约数"""
    if b == 0:
        return a
    else:
        return gcd(b, a % b)

print(f"gcd(48, 18) = {gcd(48, 18)}")  # 输出: gcd(48, 18) = 6
print(f"gcd(56, 98) = {gcd(56, 98)}")  # 输出: gcd(56, 98) = 14
```

### 递归的实际应用

```python
# 目录遍历（模拟）
def print_directory_structure(items, indent=0):
    """递归打印目录结构"""
    for item in items:
        if isinstance(item, dict):
            # 目录
            name = item.get("name", "Unknown")
            children = item.get("children", [])
            print("  " * indent + f"📁 {name}")
            print_directory_structure(children, indent + 1)
        else:
            # 文件
            print("  " * indent + f"📄 {item}")

# 模拟文件系统结构
file_system = [
    "README.md",
    {
        "name": "src",
        "children": [
            "main.py",
            "utils.py",
            {
                "name": "models",
                "children": [
                    "user.py",
                    "product.py"
                ]
            }
        ]
    },
    {
        "name": "tests",
        "children": [
            "test_main.py",
            "test_utils.py"
        ]
    },
    "requirements.txt"
]

print("目录结构:")
print_directory_structure(file_system)

# 快速排序
def quick_sort(arr):
    """快速排序算法"""
    if len(arr) <= 1:
        return arr
    else:
        pivot = arr[0]
        less = [x for x in arr[1:] if x <= pivot]
        greater = [x for x in arr[1:] if x > pivot]
        return quick_sort(less) + [pivot] + quick_sort(greater)

unsorted = [3, 6, 8, 10, 1, 2, 1]
sorted_arr = quick_sort(unsorted)
print(f"快速排序: {sorted_arr}")  # [1, 1, 2, 3, 6, 8, 10]

# 二分查找
def binary_search(arr, target, left=0, right=None):
    """递归二分查找"""
    if right is None:
        right = len(arr) - 1

    if left > right:
        return -1  # 未找到

    mid = (left + right) // 2

    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search(arr, target, mid + 1, right)
    else:
        return binary_search(arr, target, left, mid - 1)

sorted_list = [1, 3, 5, 7, 9, 11, 13, 15]
target = 7
index = binary_search(sorted_list, target)
print(f"目标 {target} 在索引 {index}")  # 目标 7 在索引 3
```

### 递归的注意事项

```python
# 递归深度限制
import sys

print(f"默认递归深度限制: {sys.getrecursionlimit()}")  # 通常是 1000

# 可能导致栈溢出的递归
def infinite_recursion(n):
    """无限递归示例（会导致栈溢出）"""
    return infinite_recursion(n + 1)

# try:
#     infinite_recursion(1)
# except RecursionError as e:
#     print(f"递归错误: {e}")

# 尾递归优化（Python 不支持）
def tail_recursive_factorial(n, accumulator=1):
    """尾递归计算阶乘"""
    if n <= 1:
        return accumulator
    else:
        return tail_recursive_factorial(n - 1, n * accumulator)

print(f"尾递归阶乘: {tail_recursive_factorial(5)}")  # 120

# 记忆化递归（避免重复计算）
def memoized_fibonacci(n, memo={}):
    """记忆化斐波那契数列"""
    if n in memo:
        return memo[n]

    if n <= 0:
        memo[0] = 0
        return 0
    elif n == 1:
        memo[1] = 1
        return 1
    else:
        result = memoized_fibonacci(n - 1, memo) + memoized_fibonacci(n - 2, memo)
        memo[n] = result
        return result

print(f"记忆化斐波那契: {memoized_fibonacci(30)}")  # 832040
```

---

## 高阶函数 (map, filter, reduce)

高阶函数是指接受其他函数作为参数或将函数作为返回值的函数。

### map() 函数

```python
# map() 对序列中的每个元素应用函数
numbers = [1, 2, 3, 4, 5]

# 使用普通函数
def square(x):
    return x ** 2

squared = list(map(square, numbers))
print(f"平方: {squared}")  # [1, 4, 9, 16, 25]

# 使用 lambda
squared_lambda = list(map(lambda x: x ** 2, numbers))
print(f"平方 (lambda): {squared_lambda}")

# 多个序列
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 22]
cities = ["New York", "Boston", "Chicago"]

combined = list(map(lambda name, age, city: f"{name} ({age}) from {city}",
                      names, ages, cities))
print(f"组合信息: {combined}")
# ['Alice (25) from New York', 'Bob (30) from Boston', 'Charlie (22) from Chicago']

# 字符串操作
words = ["hello", "world", "python"]
capitalized = list(map(str.upper, words))
print(f"大写: {capitalized}")  # ['HELLO', 'WORLD', 'PYTHON']
```

### filter() 函数

```python
# filter() 过滤序列中的元素
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# 过滤偶数
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(f"偶数: {evens}")  # [2, 4, 6, 8, 10]

# 过滤大于5的数
greater_than_five = list(filter(lambda x: x > 5, numbers))
print(f"大于5: {greater_than_five}")  # [6, 7, 8, 9, 10]

# 过滤字符串
words = ["apple", "banana", "kiwi", "strawberry", "orange"]
short_words = list(filter(lambda word: len(word) <= 5, words))
print(f"短单词: {short_words}")  # ['apple', 'kiwi']

# 使用普通函数
def is_positive(x):
    return x > 0

mixed_numbers = [-1, 2, -3, 4, -5, 6]
positives = list(filter(is_positive, mixed_numbers))
print(f"正数: {positives}")  # [2, 4, 6]
```

### reduce() 函数

```python
from functools import reduce

# reduce() 对序列元素进行累积操作
numbers = [1, 2, 3, 4, 5]

# 求和
sum_result = reduce(lambda x, y: x + y, numbers)
print(f"求和: {sum_result}")  # 15

# 求积
product_result = reduce(lambda x, y: x * y, numbers)
print(f"求积: {product_result}")  # 120

# 找最大值
max_result = reduce(lambda x, y: x if x > y else y, numbers)
print(f"最大值: {max_result}")  # 5

# 带初始值的 reduce
sum_with_initial = reduce(lambda x, y: x + y, numbers, 10)
print(f"带初始值的求和: {sum_with_initial}")  # 25

# 复杂的累积操作
def cumulative_operation(sequence):
    """演示各种累积操作"""
    # 求和
    total = reduce(lambda x, y: x + y, sequence, 0)

    # 求积
    product = reduce(lambda x, y: x * y, sequence, 1)

    # 拼接字符串
    joined = reduce(lambda x, y: f"{x}-{y}", sequence)

    return {
        "sum": total,
        "product": product,
        "joined": joined
    }

result = cumulative_operation([1, 2, 3, 4])
print(f"累积操作结果: {result}")
# {'sum': 10, 'product': 24, 'joined': '1-2-3-4'}
```

### 组合使用高阶函数

```python
# 链式操作
numbers = list(range(1, 11))

# 处理流程: 过滤 -> 映射 -> 求和
result = reduce(
    lambda x, y: x + y,
    map(lambda x: x ** 2,
         filter(lambda x: x % 2 == 0, numbers))
)
print(f"偶数平方和: {result}")  # 220 (2² + 4² + 6² + 8² + 10²)

# 数据处理管道
def process_data(data):
    """数据处理管道"""
    # 1. 过滤掉无效数据
    valid_data = filter(lambda x: x is not None and x > 0, data)

    # 2. 转换数据
    transformed = map(lambda x: x * 2, valid_data)

    # 3. 排序
    sorted_data = sorted(transformed)

    # 4. 累积操作
    result = reduce(lambda acc, x: acc + x, sorted_data, 0)

    return result

test_data = [1, None, -2, 3, 0, 4, None, 5]
processed_result = process_data(test_data)
print(f"处理结果: {processed_result}")  # 26 ((2*1 + 2*3 + 2*4 + 2*5) = 2 + 6 + 8 + 10 = 26)
```

### 自定义高阶函数

```python
# 接受函数作为参数的高阶函数
def apply_operation(data, operation):
    """对数据应用操作"""
    return [operation(x) for x in data]

numbers = [1, 2, 3, 4, 5]

# 应用不同的操作
squared = apply_operation(numbers, lambda x: x ** 2)
cubed = apply_operation(numbers, lambda x: x ** 3)
absolute = apply_operation([-1, 2, -3, 4], abs)

print(f"平方: {squared}")
print(f"立方: {cubed}")
print(f"绝对值: {absolute}")

# 返回函数的高阶函数
def create_multiplier(factor):
    """创建乘法器函数"""
    def multiplier(x):
        return x * factor
    return multiplier

# 创建特定的乘法器
double = create_multiplier(2)
triple = create_multiplier(3)

print(f"double(5) = {double(5)}")    # 10
print(f"triple(5) = {triple(5)}")    # 15

# 创建条件函数
def create_conditional_function(condition_func, true_func, false_func):
    """创建条件函数"""
    def conditional(x):
        if condition_func(x):
            return true_func(x)
        else:
            return false_func(x)
    return conditional

# 创建平方偶数，对奇数加1的函数
square_even_add_one_odd = create_conditional_function(
    lambda x: x % 2 == 0,
    lambda x: x ** 2,
    lambda x: x + 1
)

test_numbers = [1, 2, 3, 4, 5]
result = [square_even_add_one_odd(x) for x in test_numbers]
print(f"条件处理结果: {result}")  # [2, 4, 4, 16, 6]
```

---

## 模块的导入与使用

模块是 Python 组织代码的基本单位，允许你将相关的函数、类和变量组织在一起。

### 基本导入语法

```python
# 导入整个模块
import math
print(f"圆周率: {math.pi}")
print(f"平方根: {math.sqrt(16)}")

# 导入模块并重命名
import numpy as np
# 注意：这个示例需要安装 numpy
# array = np.array([1, 2, 3, 4, 5])
# print(f"NumPy 数组: {array}")

# 从模块导入特定函数
from random import randint, choice
random_number = randint(1, 100)
print(f"随机数: {random_number}")

# 从模块导入所有内容（不推荐）
# from math import *
# print(f"sin(π/2): {sin(pi/2)}")
```

### 常用内置模块

```python
# math 模块
import math
print(f"向上取整: {math.ceil(3.2)}")      # 4
print(f"向下取整: {math.floor(3.8)}")     # 3
print(f"幂运算: {math.pow(2, 3)}")       # 8.0
print(f"对数: {math.log(100, 10)}")      # 2.0

# random 模块
import random
numbers = [1, 2, 3, 4, 5]
print(f"随机选择: {random.choice(numbers)}")
print(f"随机打乱: {random.shuffle(numbers)}")
print(f"随机范围: {random.randint(1, 10)}")

# datetime 模块
from datetime import datetime, timedelta
now = datetime.now()
print(f"当前时间: {now}")
future = now + timedelta(days=7)
print(f"一周后: {future}")

# json 模块
import json
data = {"name": "Alice", "age": 25, "city": "New York"}
json_string = json.dumps(data, indent=2)
print(f"JSON 字符串:\n{json_string}")

parsed_data = json.loads(json_string)
print(f"解析后的数据: {parsed_data}")

# os 模块
import os
print(f"当前工作目录: {os.getcwd()}")
print(f"目录内容: {os.listdir('.')}")

# sys 模块
import sys
print(f"Python 版本: {sys.version}")
print(f"模块搜索路径: {sys.path[:3]}...")  # 只显示前3个路径
```

### 条件导入

```python
# 尝试导入可选模块
try:
    import requests
    HAS_REQUESTS = True
except ImportError:
    HAS_REQUESTS = False
    print("requests 模块未安装")

# 根据导入情况执行不同逻辑
def fetch_data(url):
    """根据可用的模块获取数据"""
    if HAS_REQUESTS:
        import requests
        response = requests.get(url)
        return response.text
    else:
        import urllib.request
        with urllib.request.urlopen(url) as response:
            return response.read().decode()

# 版本检查
import sys
if sys.version_info >= (3, 8):
    print("支持海象运算符 := ")
    # 可以使用海象运算符
else:
    print("不支持海象运算符")

# 平台检查
import platform
if platform.system() == "Windows":
    print("运行在 Windows 系统上")
elif platform.system() == "Darwin":
    print("运行在 macOS 系统上")
elif platform.system() == "Linux":
    print("运行在 Linux 系统上")
else:
    print(f"运行在 {platform.system()} 系统上")
```

---

## 常用内置模块

### collections 模块

```python
from collections import Counter, defaultdict, deque, namedtuple

# Counter - 计数器
words = "hello world hello python world hello".split()
word_count = Counter(words)
print(f"词频统计: {word_count}")
# Counter({'hello': 3, 'world': 2, 'python': 1})

print(f"最常见的词: {word_count.most_common(2)}")
# [('hello', 3), ('world', 2)]

# defaultdict - 默认字典
groups = defaultdict(list)
data = [('a', 1), ('b', 2), ('a', 3), ('b', 4), ('c', 5)]
for key, value in data:
    groups[key].append(value)
print(f"分组数据: {dict(groups)}")
# {'a': [1, 3], 'b': [2, 4], 'c': [5]}

# deque - 双端队列
dq = deque([1, 2, 3])
dq.append(4)      # 右端添加
dq.appendleft(0)  # 左端添加
dq.pop()          # 右端删除
dq.popleft()      # 左端删除
print(f"双端队列: {dq}")

# namedtuple - 命名元组
Point = namedtuple('Point', ['x', 'y'])
p1 = Point(10, 20)
print(f"点坐标: ({p1.x}, {p1.y})")
print(f"点的表示: {p1}")
```

### itertools 模块

```python
import itertools

# count() - 无限计数器
# 注意：这会无限循环，所以要限制
counter = itertools.count(10, 2)  # 从10开始，步长为2
print("前5个计数:", [next(counter) for _ in range(5)])

# cycle() - 无限循环
colors = ['red', 'green', 'blue']
color_cycle = itertools.cycle(colors)
print("前6个颜色:", [next(color_cycle) for _ in range(6)])

# chain() - 连接多个迭代器
list1 = [1, 2, 3]
list2 = [4, 5, 6]
chained = list(itertools.chain(list1, list2))
print(f"连接结果: {chained}")

# combinations() - 组合
items = ['A', 'B', 'C', 'D']
combos = list(itertools.combinations(items, 2))
print(f"所有2元素组合: {combos}")

# permutations() - 排列
perms = list(itertools.permutations(items, 2))
print(f"所有2元素排列: {perms[:5]}...")  # 只显示前5个

# product() - 笛卡尔积
colors = ['red', 'blue']
sizes = ['S', 'M', 'L']
products = list(itertools.product(colors, sizes))
print(f"所有组合: {products}")
```

### re 模块 (正则表达式)

```python
import re

# 基本匹配
text = "我的邮箱是 alice@example.com，电话是 123-456-7890"

# 查找邮箱
email_pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'
emails = re.findall(email_pattern, text)
print(f"找到的邮箱: {emails}")

# 查找电话号码
phone_pattern = r'\d{3}-\d{3}-\d{4}'
phones = re.findall(phone_pattern, text)
print(f"找到的电话: {phones}")

# 替换
new_text = re.sub(r'\d{3}-\d{3}-\d{4}', '[PHONE]', text)
print(f"替换后的文本: {new_text}")

# 分割
sentence = "Hello, world! How are you today?"
words = re.split(r'[,\s!?]+', sentence)
print(f"分割后的单词: {[w for w in words if w]}")

# 编译正则表达式（提高性能）
email_regex = re.compile(email_pattern)
long_text = "alice@example.com bob@test.com charlie@demo.com"
all_emails = email_regex.findall(long_text)
print(f"多个邮箱: {all_emails}")
```

### functools 模块

```python
from functools import wraps, lru_cache, partial

# wraps - 装饰器工具
def debug_function(func):
    """调试装饰器"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"调用函数 {func.__name__}")
        print(f"参数: args={args}, kwargs={kwargs}")
        result = func(*args, **kwargs)
        print(f"返回值: {result}")
        return result
    return wrapper

@debug_function
def add_numbers(a, b):
    return a + b

result = add_numbers(3, 5)

# lru_cache - 缓存装饰器
@lru_cache(maxsize=128)
def fibonacci(n):
    """缓存斐波那契数列"""
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(f"斐波那契(30): {fibonacci(30)}")
print(f"缓存信息: {fibonacci.cache_info()}")

# partial - 偏函数
def multiply(x, y):
    return x * y

double = partial(multiply, 2)
triple = partial(multiply, 3)

print(f"double(5) = {double(5)}")    # 10
print(f"triple(5) = {triple(5)}")    # 15

# 固定底数的幂运算
power_of_2 = partial(pow, 2)
print(f"2^3 = {power_of_2(3)}")      # 8.0
print(f"2^5 = {power_of_2(5)}")      # 32.0
```

---

## 自定义模块创建

### 创建简单的模块

```python
# 文件: my_math.py
"""
我的数学工具模块
提供基本的数学运算函数
"""

def add(a, b):
    """两数相加"""
    return a + b

def subtract(a, b):
    """两数相减"""
    return a - b

def multiply(a, b):
    """两数相乘"""
    return a * b

def divide(a, b):
    """两数相除"""
    if b == 0:
        raise ValueError("除数不能为零")
    return a / b

# 模块级别的变量
PI = 3.14159265359
E = 2.71828182846

def circle_area(radius):
    """计算圆面积"""
    return PI * radius ** 2

def circle_circumference(radius):
    """计算圆周长"""
    return 2 * PI * radius

if __name__ == "__main__":
    # 模块测试代码
    print("模块测试:")
    print(f"5 + 3 = {add(5, 3)}")
    print(f"10 - 4 = {subtract(10, 4)}")
    print(f"6 * 7 = {multiply(6, 7)}")
    print(f"15 / 3 = {divide(15, 3)}")
    print(f"半径为5的圆面积: {circle_area(5)}")
```

### 使用自定义模块

```python
# 导入自定义模块
# 注意：假设 my_math.py 在同一目录下
# import my_math

# 使用模块中的函数
# result = my_math.add(10, 20)
# print(f"10 + 20 = {result}")

# 访问模块变量
# print(f"PI = {my_math.PI}")

# 从模块导入特定内容
# from my_math import add, PI
# print(f"使用导入的函数: {add(5, 3)}")
# print(f"使用导入的变量: {PI}")

# 使用别名导入
# import my_math as math_utils
# print(f"使用别名: {math_utils.multiply(4, 6)}")
```

### 创建模块包

```
project/
├── __init__.py
├── math/
│   ├── __init__.py
│   ├── basic.py
│   ├── advanced.py
│   └── geometry.py
└── utils/
    ├── __init__.py
    ├── string_utils.py
    └── file_utils.py
```

```python
# 文件: math/__init__.py
"""
数学工具包
"""

# 导入基本功能让用户可以直接使用
from .basic import add, subtract, multiply, divide
from .geometry import circle_area, circle_circumference

# 包级别的变量
__version__ = "1.0.0"
__author__ = "Your Name"

# 文件: math/basic.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        raise ValueError("除数不能为零")
    return a / b

# 文件: math/geometry.py
PI = 3.14159265359

def circle_area(radius):
    return PI * radius ** 2

def circle_circumference(radius):
    return 2 * PI * radius
```

### 模块的搜索路径

```python
import sys
import os

# 查看模块搜索路径
print("模块搜索路径:")
for i, path in enumerate(sys.path):
    print(f"  {i+1}. {path}")

# 添加自定义路径
custom_path = "/path/to/my/modules"
if custom_path not in sys.path:
    sys.path.append(custom_path)

# 动态导入模块
def dynamic_import(module_name):
    """动态导入模块"""
    try:
        module = __import__(module_name)
        return module
    except ImportError as e:
        print(f"无法导入模块 {module_name}: {e}")
        return None

# 使用示例
# math_module = dynamic_import("math")
# if math_module:
#     print(f"成功导入模块: {math_module.__name__}")
#     print(f"平方根函数: {math_module.sqrt(16)}")
```

---

## 包 (package) 的使用

包是一种组织 Python 模块命名空间的方式，通过目录结构来实现。

### 包的结构

```
my_package/
├── __init__.py          # 包初始化文件
├── module1.py           # 模块1
├── module2.py           # 模块2
├── subpackage/          # 子包
│   ├── __init__.py
│   ├── submodule1.py
│   └── submodule2.py
└── data/                # 数据文件
    ├── config.json
    └── data.txt
```

### 创建包

```python
# 文件: __init__.py
"""
我的工具包
提供各种实用工具函数和类
"""

# 定义包级别的变量
__version__ = "1.0.0"
__author__ = "Your Name"
__email__ = "your.email@example.com"

# 导入核心功能供直接使用
from .utils import format_string, validate_email
from .calculators import basic_calculator

# 包级别的函数
def get_package_info():
    """获取包信息"""
    return {
        "name": "my_package",
        "version": __version__,
        "author": __author__,
        "email": __email__
    }

# 文件: utils.py
def format_string(text, style="upper"):
    """格式化字符串"""
    if style == "upper":
        return text.upper()
    elif style == "lower":
        return text.lower()
    elif style == "title":
        return text.title()
    else:
        return text

def validate_email(email):
    """验证邮箱格式"""
    import re
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

# 文件: calculators.py
class basic_calculator:
    """基础计算器类"""

    @staticmethod
    def add(a, b):
        return a + b

    @staticmethod
    def subtract(a, b):
        return a - b

    @staticmethod
    def multiply(a, b):
        return a * b

    @staticmethod
    def divide(a, b):
        if b == 0:
            raise ValueError("除数不能为零")
        return a / b
```

### 使用包

```python
# 导入整个包
import my_package

# 使用包中的函数
info = my_package.get_package_info()
print(f"包信息: {info}")

# 使用包中的工具
formatted_text = my_package.format_string("hello world", "title")
print(f"格式化文本: {formatted_text}")

# 导入包中的特定模块
from my_package import calculators

calc = calculators.basic_calculator()
result = calc.add(10, 20)
print(f"计算结果: {result}")

# 从包中导入特定函数
from my_package.utils import validate_email

email = "test@example.com"
is_valid = validate_email(email)
print(f"邮箱 {email} 是否有效: {is_valid}")

# 使用别名导入
from my_package.calculators import basic_calculator as calc

result2 = calc.multiply(5, 6)
print(f"乘法结果: {result2}")
```

### 子包的使用

```python
# 文件: subpackage/__init__.py
"""
子包示例
"""

from .database import DatabaseConnection
from .network import HttpClient

# 文件: subpackage/database.py
class DatabaseConnection:
    """数据库连接类"""

    def __init__(self, host="localhost", port=5432):
        self.host = host
        self.port = port
        self.connected = False

    def connect(self):
        """连接数据库"""
        print(f"连接到数据库 {self.host}:{self.port}")
        self.connected = True

    def disconnect(self):
        """断开数据库连接"""
        print("断开数据库连接")
        self.connected = False

    def execute_query(self, query):
        """执行查询"""
        if not self.connected:
            raise RuntimeError("未连接到数据库")
        print(f"执行查询: {query}")

# 文件: subpackage/network.py
class HttpClient:
    """HTTP客户端类"""

    def __init__(self, base_url="https://api.example.com"):
        self.base_url = base_url
        self.headers = {}

    def get(self, endpoint):
        """发送GET请求"""
        url = f"{self.base_url}/{endpoint}"
        print(f"发送GET请求到: {url}")
        return {"status": "success", "data": "sample data"}

    def post(self, endpoint, data):
        """发送POST请求"""
        url = f"{self.base_url}/{endpoint}"
        print(f"发送POST请求到: {url}")
        print(f"数据: {data}")
        return {"status": "success", "id": 123}
```

### 相对导入

```python
# 在子包中使用相对导入
# 文件: subpackage/__init__.py
from .database import DatabaseConnection
from .network import HttpClient

# 文件: subpackage/database.py
from ..utils import validate_email  # 相对导入父包的模块

class DatabaseConnection:
    def __init__(self):
        self.users = []

    def add_user(self, email):
        if validate_email(email):
            self.users.append(email)
            print(f"添加用户: {email}")
        else:
            print(f"无效邮箱: {email}")

    def get_users(self):
        return self.users
```

---

## 函数式编程概念

Python 支持多种函数式编程特性，让代码更加简洁和优雅。

### 纯函数 (Pure Functions)

```python
# 纯函数：相同输入总是产生相同输出，没有副作用
def pure_add(a, b):
    """纯函数示例"""
    return a + b

# 非纯函数：有副作用
total = 0
def impure_add(value):
    """非纯函数示例"""
    global total
    total += value
    return total

# 使用纯函数
def process_numbers_pure(numbers):
    """纯函数处理数字"""
    return [x * 2 for x in numbers if x > 0]

original = [1, -2, 3, -4, 5]
result = process_numbers_pure(original)
print(f"原始列表: {original}")  # 不变
print(f"处理结果: {result}")    # [2, 6, 10]

# 使用非纯函数
def process_numbers_impure(numbers):
    """非纯函数处理数字"""
    for i in range(len(numbers)):
        if numbers[i] > 0:
            numbers[i] *= 2
        else:
            numbers[i] = 0
    return numbers

modified = original.copy()
result2 = process_numbers_impure(modified)
print(f"修改后列表: {modified}")  # 被修改了
print(f"处理结果: {result2}")     # [2, 0, 6, 0, 10]
```

### 高阶函数与函数组合

```python
# 函数组合
def compose(*functions):
    """组合多个函数"""
    def composed(x):
        for func in reversed(functions):
            x = func(x)
        return x
    return composed

# 创建函数管道
def add_five(x):
    return x + 5

def multiply_by_two(x):
    return x * 2

def to_string(x):
    return f"结果: {x}"

# 组合函数
pipeline = compose(to_string, multiply_by_two, add_five)
result = pipeline(10)
print(f"管道处理结果: {result}")  # "结果: 30"

# 函数式编程工具
def curry(func):
    """柯里化函数"""
    def curried(*args, **kwargs):
        if len(args) + len(kwargs) >= func.__code__.co_argcount:
            return func(*args, **kwargs)
        return lambda *more_args, **more_kwargs: curried(*(args + more_args), **{**kwargs, **more_kwargs})
    return curried

# 柯里化示例
def add(a, b):
    return a + b

curried_add = curry(add)
add_5 = curried_add(5)
result = add_5(3)
print(f"柯里化加法: {result}")  # 8
```

### 函数式数据处理

```python
# 函数式的数据处理管道
def functional_data_pipeline(data):
    """函数式数据处理管道"""
    # 定义处理步骤
    steps = [
        lambda x: [item for item in x if isinstance(item, (int, float))],  # 过滤数字
        lambda x: [item for item in x if item > 0],                      # 过滤正数
        lambda x: [item * 2 for item in x],                               # 乘以2
        lambda x: sorted(x),                                             # 排序
        lambda x: sum(x) / len(x) if x else 0                           # 计算平均值
    ]

    # 组合所有步骤
    pipeline = compose(*steps)
    return pipeline(data)

# 测试数据
test_data = [1, "hello", -2, 3.5, None, 4, "world", -5]
result = functional_data_pipeline(test_data)
print(f"函数式处理结果: {result}")  # (2 + 7 + 8) / 3 = 5.666...

# 函数式的映射-规约模式
def map_reduce(data, map_func, reduce_func, initial):
    """映射-规约模式"""
    mapped = map(map_func, data)
    return reduce(reduce_func, mapped, initial)

# 计算所有数字的平方和
numbers = [1, 2, 3, 4, 5]
square_sum = map_reduce(
    numbers,
    lambda x: x ** 2,           # 映射：平方
    lambda acc, x: acc + x,    # 规约：求和
    0                          # 初始值
)
print(f"平方和: {square_sum}")  # 55
```

### 装饰器模式

```python
# 函数式装饰器
def memoize(func):
    """记忆化装饰器"""
    cache = {}

    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]

    return wrapper

@memoize
def fibonacci(n):
    """记忆化斐波那契"""
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(f"斐波那契(20): {fibonacci(20)}")

# 日志装饰器
def logger(func):
    """日志装饰器"""
    def wrapper(*args, **kwargs):
        print(f"调用函数 {func.__name__}({args}, {kwargs})")
        result = func(*args, **kwargs)
        print(f"函数 {func.__name__} 返回: {result}")
        return result
    return wrapper

@logger
def calculate_area(width, height):
    """计算面积"""
    return width * height

area = calculate_area(5, 3)
```

---

## 函数与模块最佳实践

### 1. 函数设计原则

```python
# ✅ 好的函数设计
def calculate_loan_payment(principal, annual_rate, years):
    """
    计算贷款月供

    Args:
        principal (float): 贷款本金
        annual_rate (float): 年利率（小数形式，如0.05表示5%）
        years (int): 贷款年限

    Returns:
        float: 月供金额

    Raises:
        ValueError: 当参数无效时
    """
    if principal <= 0:
        raise ValueError("本金必须大于0")

    if annual_rate <= 0:
        raise ValueError("利率必须大于0")

    if years <= 0:
        raise ValueError("年限必须大于0")

    monthly_rate = annual_rate / 12
    total_months = years * 12

    if monthly_rate == 0:
        return principal / total_months

    payment = principal * (monthly_rate * (1 + monthly_rate) ** total_months) / \
               ((1 + monthly_rate) ** total_months - 1)

    return round(payment, 2)

# ❌ 避免的函数设计
def bad_function(data):
    """函数做了太多事情"""
    # 验证数据
    if not data:
        return None

    # 处理数据
    processed = []
    for item in data:
        if item > 0:
            processed.append(item * 2)

    # 计算统计
    total = sum(processed)
    average = total / len(processed) if processed else 0

    # 格式化输出
    result = f"总和: {total}, 平均值: {average}"
    return result
```

### 2. 模块组织最佳实践

```python
# ✅ 好的模块结构
"""
财务计算模块
提供各种财务相关的计算功能
"""

# 模块级别的常量
TAX_RATE = 0.08
DISCOUNT_RATE = 0.10

# 私有函数（内部使用）
def _calculate_tax(amount):
    """计算税费（内部函数）"""
    return amount * TAX_RATE

def _apply_discount(amount):
    """应用折扣（内部函数）"""
    return amount * (1 - DISCOUNT_RATE)

# 公共API函数
def calculate_final_price(original_price, apply_tax=True, apply_discount=False):
    """
    计算最终价格

    Args:
        original_price (float): 原价
        apply_tax (bool): 是否应用税费
        apply_discount (bool): 是否应用折扣

    Returns:
        float: 最终价格
    """
    if original_price <= 0:
        raise ValueError("价格必须大于0")

    final_price = original_price

    if apply_discount:
        final_price = _apply_discount(final_price)

    if apply_tax:
        final_price += _calculate_tax(final_price)

    return round(final_price, 2)

# 模块级别的测试代码
if __name__ == "__main__":
    # 简单的模块测试
    test_price = 100
    final = calculate_final_price(test_price, apply_tax=True, apply_discount=True)
    print(f"测试结果: 原价 {test_price} -> 最终价 {final}")
```

### 3. 错误处理最佳实践

```python
# ✅ 好的错误处理
def safe_divide(a, b):
    """安全的除法运算"""
    try:
        return a / b
    except ZeroDivisionError:
        return float('inf')
    except TypeError:
        raise TypeError("参数必须是数字")

def read_file_safe(filepath):
    """安全读取文件"""
    try:
        with open(filepath, 'r', encoding='utf-8') as f:
            return f.read()
    except FileNotFoundError:
        return f"文件不存在: {filepath}"
    except PermissionError:
        return f"没有权限读取文件: {filepath}"
    except UnicodeDecodeError:
        return f"文件编码错误: {filepath}"
    except Exception as e:
        return f"读取文件时发生未知错误: {e}"
```

### 4. 性能优化建议

```python
# ✅ 使用生成器处理大数据
def process_large_file(filepath):
    """处理大文件的最佳实践"""
    try:
        with open(filepath, 'r') as f:
            for line in f:  # 逐行处理，避免内存溢出
                processed_line = line.strip().upper()
                if processed_line:
                    yield processed_line
    except Exception as e:
        print(f"处理文件时出错: {e}")
        return

# ✅ 使用缓存优化重复计算
from functools import lru_cache
import time

@lru_cache(maxsize=128)
def expensive_calculation(n):
    """耗时的计算函数"""
    print(f"执行耗时计算: {n}")
    time.sleep(0.1)  # 模拟耗时操作
    return n ** 2

# 测试缓存效果
print("第一次调用:")
result1 = expensive_calculation(5)

print("第二次调用（应该使用缓存）:")
result2 = expensive_calculation(5)
```

### 5. 文档和注释

```python
def calculate_compound_interest(principal, annual_rate, times_compounded, years):
    """
    计算复利

    复利公式：A = P(1 + r/n)^(nt)
    其中：
    - A: 最终金额
    - P: 本金
    - r: 年利率
    - n: 每年复利次数
    - t: 年数

    Args:
        principal (float): 本金金额
        annual_rate (float): 年利率（小数形式，0.05 表示 5%）
        times_compounded (int): 每年复利次数
        years (int): 投资年限

    Returns:
        float: 最终金额

    Raises:
        ValueError: 当任何参数为负数或零时

    Example:
        >>> calculate_compound_interest(1000, 0.05, 12, 10)
        1647.01
    """
    if principal <= 0:
        raise ValueError("本金必须大于0")
    if annual_rate <= 0:
        raise ValueError("利率必须大于0")
    if times_compounded <= 0:
        raise ValueError("复利次数必须大于0")
    if years <= 0:
        raise ValueError("年限必须大于0")

    amount = principal * (1 + annual_rate / times_compounded) ** (times_compounded * years)
    return round(amount, 2)
```

---

## 总结

Python 的函数与模块系统提供了强大的代码组织能力：

### 核心概念

1. **函数基础**：
   - 函数定义与调用
   - 参数类型：位置、关键字、默认、可变参数
   - 返回值与作用域

2. **高级函数**：
   - lambda 表达式：匿名函数
   - 递归函数：自我调用
   - 高阶函数：接受或返回函数

3. **模块系统**：
   - 模块导入与使用
   - 包的组织结构
   - 内置模块的应用

4. **函数式编程**：
   - 纯函数概念
   - 函数组合
   - 装饰器模式

### 最佳实践

1. 函数应该单一职责，易于理解和测试
2. 合理使用参数类型，提供清晰的文档
3. 避免副作用，优先使用纯函数
4. 模块应该有明确的边界和职责
5. 使用适当的错误处理和缓存机制

掌握这些概念将帮助您编写更加模块化、可维护和高效的 Python 代码。