# Python 控制流

## 目录

1. [条件语句 (if-elif-else)](#条件语句-if-elif-else)
2. [for 循环详解](#for-循环详解)
3. [while 循环详解](#while-循环详解)
4. [循环控制语句 (break, continue, pass)](#循环控制语句-break-continue-pass)
5. [循环的 else 子句](#循环的-else-子句)
6. [嵌套控制结构](#嵌套控制结构)
7. [控制流最佳实践](#控制流最佳实践)

---

## 条件语句 (if-elif-else)

条件语句允许程序根据不同的条件执行不同的代码块，这是编程中最基本的决策机制。

### 基本的 if 语句

```python
age = 18

if age >= 18:
    print("您已成年")
    print("可以享有完全的民事权利")

# 输出: 您已成年
#       可以享有完全的民事权利
```

### if-else 语句

```python
age = 16

if age >= 18:
    print("您已成年")
else:
    print("您未成年")
    print("需要监护人同意")

# 输出: 您未成年
#       需要监护人同意
```

### if-elif-else 语句

```python
score = 85

if score >= 90:
    grade = 'A'
    print("优秀！")
elif score >= 80:
    grade = 'B'
    print("良好！")
elif score >= 70:
    grade = 'C'
    print("中等")
elif score >= 60:
    grade = 'D'
    print("及格")
else:
    grade = 'F'
    print("不及格")

print(f"您的成绩等级是: {grade}")

# 输出: 良好！
#       您的成绩等级是: B
```

### 条件表达式 (三元运算符)

```python
# 基本语法: value_if_true if condition else value_if_false
age = 20
status = "成年" if age >= 18 else "未成年"
print(f"年龄 {age}: {status}")

# 嵌套三元运算符
score = 75
result = "优秀" if score >= 90 else ("良好" if score >= 80 else "及格")
print(f"成绩 {score}: {result}")
```

### 复杂条件判断

#### 使用逻辑运算符

```python
# 检查用户权限
username = "admin"
is_logged_in = True
has_permission = True

if username == "admin" and is_logged_in and has_permission:
    print("管理员权限已验证")
elif is_logged_in and has_permission:
    print("普通用户权限已验证")
elif is_logged_in:
    print("用户已登录，但无权限")
else:
    print("用户未登录")

# 检查数字属性
num = 24

if num > 0 and num % 2 == 0:
    print(f"{num} 是正偶数")
elif num > 0 and num % 2 != 0:
    print(f"{num} 是正奇数")
elif num < 0:
    print(f"{num} 是负数")
else:
    print(f"{num} 是零")
```

#### 使用成员运算符

```python
# 检查用户角色
user_role = "editor"
allowed_roles = ["admin", "editor", "moderator"]

if user_role in allowed_roles:
    print("用户有编辑权限")
    if user_role == "admin":
        print("管理员权限：可以删除内容")
    elif user_role == "editor":
        print("编辑权限：可以创建和修改内容")
    elif user_role == "moderator":
        print("版主权限：可以审核内容")
else:
    print("用户无权限")
```

#### 使用链式比较

```python
age = 25
temperature = 28

# 链式比较
if 18 <= age <= 65:
    print("年龄在正常工作范围内")

if 20 <= temperature <= 30:
    print("温度适宜")

# 复杂条件组合
if (18 <= age <= 65) and (20 <= temperature <= 30):
    print("适合户外活动")
```

### 条件语句的嵌套

```python
# 用户登录验证系统
username = "alice"
password = "123456"
is_active = True
login_attempts = 2
max_attempts = 3

if username and password:
    if is_active:
        if login_attempts < max_attempts:
            # 验证用户名和密码
            if username == "admin" and password == "admin123":
                print("管理员登录成功")
            elif username == "alice" and password == "123456":
                print("普通用户登录成功")
            else:
                print("用户名或密码错误")
        else:
            print("登录尝试次数过多，账户已锁定")
    else:
        print("账户已被禁用")
else:
    print("请输入用户名和密码")
```

---

## for 循环详解

for 循环用于遍历序列（如列表、元组、字典、集合、字符串）或其他可迭代对象。

### 基本语法

```python
# 遍历列表
fruits = ["apple", "banana", "cherry", "date"]

for fruit in fruits:
    print(f"我喜欢吃 {fruit}")

# 输出:
# 我喜欢吃 apple
# 我喜欢吃 banana
# 我喜欢吃 cherry
# 我喜欢吃 date
```

### 遍历不同类型的序列

```python
# 遍历字符串
for char in "Python":
    print(char)

# 遍历元组
numbers = (1, 2, 3, 4, 5)
for num in numbers:
    print(f"数字: {num}")

# 遍历字典
student = {
    "name": "Alice",
    "age": 20,
    "major": "Computer Science"
}

# 遍历键
print("遍历键:")
for key in student:
    print(f"  {key}")

# 遍历值
print("遍历值:")
for value in student.values():
    print(f"  {value}")

# 遍历键值对
print("遍历键值对:")
for key, value in student.items():
    print(f"  {key}: {value}")

# 遍历集合
unique_numbers = {1, 2, 3, 4, 5, 1, 2, 3}
for num in unique_numbers:
    print(f"唯一数字: {num}")
```

### 使用 range() 函数

```python
# 基本用法
for i in range(5):
    print(i)
# 输出: 0, 1, 2, 3, 4

# 指定起始和结束
for i in range(2, 8):
    print(i)
# 输出: 2, 3, 4, 5, 6, 7

# 指定步长
for i in range(0, 10, 2):
    print(i)
# 输出: 0, 2, 4, 6, 8

# 递减序列
for i in range(10, 0, -1):
    print(i)
# 输出: 10, 9, 8, 7, 6, 5, 4, 3, 2, 1

# 实际应用：计算平方
print("1-10的平方:")
for i in range(1, 11):
    square = i ** 2
    print(f"{i}² = {square}")
```

### 使用 enumerate() 获取索引

```python
fruits = ["apple", "banana", "cherry", "date"]

# 使用 enumerate() 获取索引和值
for index, fruit in enumerate(fruits):
    print(f"索引 {index}: {fruit}")

# 指定起始索引
for index, fruit in enumerate(fruits, start=1):
    print(f"第 {index} 个水果: {fruit}")

# 实际应用：带编号的列表
tasks = ["写代码", "测试", "文档编写", "部署"]
print("任务列表:")
for i, task in enumerate(tasks, 1):
    print(f"{i}. {task}")
```

### 使用 zip() 并行遍历多个序列

```python
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
cities = ["New York", "Boston", "Chicago"]

# 并行遍历多个序列
for name, age, city in zip(names, ages, cities):
    print(f"{name} ({age}岁) 来自 {city}")

# 处理不等长序列（以最短的为准）
short_names = ["Alice", "Bob"]
long_ages = [25, 30, 35, 40]

for name, age in zip(short_names, long_ages):
    print(f"{name}: {age}岁")

# 使用 zip_longest() 处理不等长序列
from itertools import zip_longest

for name, age in zip_longest(names, ages, fillvalue="未知"):
    print(f"{name}: {age}岁")
```

### 列表推导式 vs for 循环

```python
# 传统 for 循环创建列表
squares = []
for i in range(1, 11):
    squares.append(i ** 2)
print(f"平方列表: {squares}")

# 列表推导式（更简洁）
squares_comp = [i ** 2 for i in range(1, 11)]
print(f"推导式平方列表: {squares_comp}")

# 带条件的列表推导式
even_squares = []
for i in range(1, 21):
    if i % 2 == 0:
        even_squares.append(i ** 2)
print(f"偶数平方: {even_squares}")

# 带条件的列表推导式（更简洁）
even_squares_comp = [i ** 2 for i in range(1, 21) if i % 2 == 0]
print(f"推导式偶数平方: {even_squares_comp}")
```

### 字典推导式

```python
# 创建字典的平方映射
squares_dict = {}
for i in range(1, 6):
    squares_dict[i] = i ** 2
print(f"平方字典: {squares_dict}")

# 字典推导式
squares_dict_comp = {i: i ** 2 for i in range(1, 6)}
print(f"推导式平方字典: {squares_dict_comp}")

# 字母计数
text = "hello world"
char_count = {}
for char in text:
    if char.isalpha():
        char_count[char] = char_count.get(char, 0) + 1
print(f"字母计数: {char_count}")

# 字典推导式
char_count_comp = {char: text.count(char) for char in set(text) if char.isalpha()}
print(f"推导式字母计数: {char_count_comp}")
```

### 实际应用示例

```python
# 示例1：计算列表中数字的统计信息
numbers = [12, 45, 78, 23, 56, 89, 34, 67, 90, 12, 45, 78]

total = 0
count = 0
max_num = numbers[0]
min_num = numbers[0]

for num in numbers:
    total += num
    count += 1
    if num > max_num:
        max_num = num
    if num < min_num:
        min_num = num

average = total / count
print(f"统计信息:")
print(f"  总和: {total}")
print(f"  平均值: {average:.2f}")
print(f"  最大值: {max_num}")
print(f"  最小值: {min_num}")
print(f"  数量: {count}")

# 示例2：文件内容处理
lines = [
    "# 这是注释",
    "print('Hello, World!')",
    "x = 10",
    "y = 20",
    "print(x + y)",
    "# 这是另一个注释",
    "for i in range(5):",
    "    print(i)"
]

code_lines = []
comment_lines = []
empty_lines = []

for line in lines:
    stripped_line = line.strip()
    if not stripped_line:
        empty_lines.append(line)
    elif stripped_line.startswith('#'):
        comment_lines.append(line)
    else:
        code_lines.append(line)

print(f"\n代码行: {len(code_lines)}")
print(f"注释行: {len(comment_lines)}")
print(f"空行: {len(empty_lines)}")

# 示例3：嵌套数据处理
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12],
    [13, 14, 15, 16]
]

row_sums = []
col_sums = [0] * len(matrix[0])

for i, row in enumerate(matrix):
    row_sum = 0
    for j, element in enumerate(row):
        row_sum += element
        col_sums[j] += element
    row_sums.append(row_sum)

print(f"\n矩阵行和: {row_sums}")
print(f"矩阵列和: {col_sums}")
```

---

## while 循环详解

while 循环在条件为真的情况下重复执行代码块，适用于不知道循环次数但知道循环条件的情况。

### 基本语法

```python
# 基本计数器
count = 0
while count < 5:
    print(f"计数: {count}")
    count += 1

print("循环结束")

# 输出:
# 计数: 0
# 计数: 1
# 计数: 2
# 计数: 3
# 计数: 4
# 循环结束
```

### 用户输入验证

```python
# 密码验证
correct_password = "secret123"
attempts = 0
max_attempts = 3

while attempts < max_attempts:
    password = input("请输入密码: ")

    if password == correct_password:
        print("密码正确，登录成功！")
        break
    else:
        attempts += 1
        remaining_attempts = max_attempts - attempts
        if remaining_attempts > 0:
            print(f"密码错误，还有 {remaining_attempts} 次机会")
        else:
            print("密码错误次数过多，账户已锁定")
```

### 数值计算

```python
# 计算阶乘
number = 5
factorial = 1
current = 1

while current <= number:
    factorial *= current
    current += 1

print(f"{number}! = {factorial}")

# 计算斐波那契数列
n = 10
fib_sequence = [0, 1]

while len(fib_sequence) < n:
    next_fib = fib_sequence[-1] + fib_sequence[-2]
    fib_sequence.append(next_fib)

print(f"前 {n} 个斐波那契数: {fib_sequence}")
```

### 游戏循环

```python
# 猜数字游戏
import random

target = random.randint(1, 100)
guess = None
attempts = 0

print("我想了一个1到100之间的数字，猜猜看是多少？")

while guess != target:
    try:
        guess = int(input("请输入你的猜测: "))
        attempts += 1

        if guess < target:
            print("太小了！")
        elif guess > target:
            print("太大了！")
        else:
            print(f"恭喜你，猜对了！数字是 {target}")
            print(f"你用了 {attempts} 次猜中")

    except ValueError:
        print("请输入有效的数字！")
```

### 数据处理循环

```python
# 累加直到达到目标
target_sum = 1000
current_sum = 0
number = 1

print(f"累加数字直到总和达到 {target_sum}:")

while current_sum < target_sum:
    current_sum += number
    print(f"加上 {number}, 当前总和: {current_sum}")
    number += 1

print(f"最终达到 {current_sum}, 加了 {number-1} 个数字")

# 查找满足条件的最小值
value = 1
while True:
    if value ** 2 > 1000:
        print(f"最小的平方大于1000的整数是 {value}")
        print(f"{value}² = {value ** 2}")
        break
    value += 1
```

### 无限循环与退出条件

```python
# 菜单系统
while True:
    print("\n=== 主菜单 ===")
    print("1. 查看余额")
    print("2. 存款")
    print("3. 取款")
    print("4. 退出")

    choice = input("请选择操作 (1-4): ")

    if choice == "1":
        print("您的余额是: ¥1000.00")
    elif choice == "2":
        amount = input("请输入存款金额: ")
        print(f"存款 ¥{amount} 成功")
    elif choice == "3":
        amount = input("请输入取款金额: ")
        print(f"取款 ¥{amount} 成功")
    elif choice == "4":
        print("谢谢使用，再见！")
        break
    else:
        print("无效选择，请重新输入")
```

### 嵌套 while 循环

```python
# 打印乘法表
i = 1
while i <= 9:
    j = 1
    while j <= i:
        print(f"{j} × {i} = {i * j}", end="  ")
        j += 1
    print()  # 换行
    i += 1

# 二维数组遍历
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

i = 0
while i < len(matrix):
    j = 0
    while j < len(matrix[i]):
        print(f"matrix[{i}][{j}] = {matrix[i][j]}", end="  ")
        j += 1
    print()
    i += 1
```

### while 与 for 循环的比较

```python
# 使用 for 循环：明确循环次数
print("使用 for 循环打印 1-5:")
for i in range(1, 6):
    print(i)

# 使用 while 循环：条件控制
print("\n使用 while 循环打印 1-5:")
i = 1
while i <= 5:
    print(i)
    i += 1

# for 循环更适合遍历序列
fruits = ["apple", "banana", "cherry"]
print("\n使用 for 循环遍历水果:")
for fruit in fruits:
    print(fruit)

# while 循环更适合条件驱动
print("\n使用 while 循环等待用户输入正确答案:")
answer = "Python"
while True:
    user_answer = input("编程语言的首字母是什么？")
    if user_answer.lower() == answer.lower():
        print("正确！")
        break
    else:
        print("再试一次！")
```

---

## 循环控制语句 (break, continue, pass)

循环控制语句用于改变循环的正常执行流程。

### break 语句

break 语句用于立即终止循环，跳出循环体。

```python
# 查找列表中的特定元素
numbers = [3, 7, 2, 9, 5, 8, 1, 6]
target = 5

print(f"查找数字 {target}:")
for i, num in enumerate(numbers):
    print(f"检查第 {i+1} 个元素: {num}")
    if num == target:
        print(f"找到 {target} 在索引 {i}")
        break
else:
    print("未找到目标数字")

# 在嵌套循环中使用 break
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

target = 5
found = False

for i, row in enumerate(matrix):
    for j, element in enumerate(row):
        print(f"检查位置 ({i},{j}): {element}")
        if element == target:
            print(f"找到 {target} 在位置 ({i},{j})")
            found = True
            break
    if found:
        break

# 用户输入示例
print("\n输入验证示例:")
while True:
    user_input = input("请输入一个正数 (输入 'quit' 退出): ")

    if user_input.lower() == 'quit':
        print("程序结束")
        break

    try:
        number = float(user_input)
        if number <= 0:
            print("请输入正数！")
            continue
        print(f"您输入的正数是: {number}")
        print(f"平方是: {number ** 2}")
    except ValueError:
        print("无效输入，请输入数字或 'quit'")
```

### continue 语句

continue 语句用于跳过当前迭代，继续执行下一次迭代。

```python
# 跳过特定元素
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

print("跳过偶数，只打印奇数:")
for num in numbers:
    if num % 2 == 0:
        continue
    print(num)

# 过滤数据
data = ["apple", "", "banana", None, "cherry", "   ", "date"]
valid_data = []

print("\n过滤无效数据:")
for item in data:
    if not item or not item.strip():
        print(f"跳过无效项: {repr(item)}")
        continue
    valid_data.append(item.strip())
    print(f"添加有效项: {item.strip()}")

print(f"有效数据: {valid_data}")

# 在用户输入中使用
print("\n密码强度检查:")
while True:
    password = input("请输入密码 (输入 'quit' 退出): ")

    if password.lower() == 'quit':
        break

    if len(password) < 8:
        print("密码长度至少8位")
        continue

    has_upper = any(c.isupper() for c in password)
    has_lower = any(c.islower() for c in password)
    has_digit = any(c.isdigit() for c in password)

    if not has_upper:
        print("密码必须包含大写字母")
        continue
    if not has_lower:
        print("密码必须包含小写字母")
        continue
    if not has_digit:
        print("密码必须包含数字")
        continue

    print("密码强度符合要求！")
    break
```

### pass 语句

pass 语句是空操作，用于保持语法完整性。它不做任何事情，通常用作占位符。

```python
# 基本占位符用法
for i in range(5):
    if i == 2:
        pass  # 暂时不处理特定情况
    else:
        print(f"处理数字: {i}")

# 函数占位符
def future_function():
    """将来要实现的功能"""
    pass  # 函数体暂时为空

# 类占位符
class FutureClass:
    """将来要实现的类"""
    pass

# 异常处理占位符
try:
    result = 10 / 0
except ZeroDivisionError:
    pass  # 暂时不处理除零错误

# 条件语句占位符
condition = True
if condition:
    pass  # 将来要添加的逻辑
else:
    print("条件为假")

# 实际应用：框架代码
class DataProcessor:
    def __init__(self):
        self.data = []

    def load_data(self, source):
        """加载数据的方法"""
        # TODO: 实现数据加载逻辑
        pass

    def process_data(self):
        """处理数据的方法"""
        # TODO: 实现数据处理逻辑
        pass

    def save_data(self, destination):
        """保存数据的方法"""
        # TODO: 实现数据保存逻辑
        pass
```

### 组合使用循环控制语句

```python
# 复杂的循环控制示例
numbers = [1, -2, 3, -4, 5, -6, 7, -8, 9, -10]
target_positive = 5
target_negative = -4

print("查找特定数字:")
for i, num in enumerate(numbers):
    print(f"检查第 {i+1} 个元素: {num}")

    # 跳过0（如果存在）
    if num == 0:
        print("  跳过0")
        continue

    # 找到目标正数
    if num == target_positive:
        print(f"  找到目标正数 {target_positive}!")
        break

    # 跳过负数（除非是目标负数）
    if num < 0:
        if num == target_negative:
            print(f"  找到目标负数 {target_negative}!")
            break
        else:
            print(f"  跳过负数 {num}")
            continue

    print(f"  处理正数 {num}")

# 嵌套循环中的复杂控制
grid = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12],
    [13, 14, 15, 16]
]

target_values = [7, 10]
found_values = []

print("\n在网格中查找多个目标值:")
for i, row in enumerate(grid):
    for j, value in enumerate(row):
        print(f"检查位置 ({i},{j}): {value}")

        # 如果已经找到所有目标值，退出循环
        if len(found_values) == len(target_values):
            print("所有目标值都已找到!")
            break

        # 跳过已找到的值
        if value in found_values:
            print(f"  值 {value} 已找到，跳过")
            continue

        # 检查是否是目标值
        if value in target_values:
            print(f"  找到目标值 {value} 在位置 ({i},{j})")
            found_values.append(value)

    # 外层循环也检查是否找到所有目标值
    if len(found_values) == len(target_values):
        break

print(f"找到的目标值: {found_values}")
```

---

## 循环的 else 子句

Python 中的循环可以有一个可选的 else 子句，它在循环正常完成（没有被 break 语句中断）时执行。

### for 循环的 else 子句

```python
# 查找元素 - 找到时中断
numbers = [1, 3, 5, 7, 9, 11]
target = 7

print(f"在 {numbers} 中查找 {target}:")
for num in numbers:
    print(f"检查: {num}")
    if num == target:
        print(f"找到 {target}!")
        break
else:
    print(f"未找到 {target}")

print("---")

# 查找元素 - 未找到
target2 = 8

print(f"在 {numbers} 中查找 {target2}:")
for num in numbers:
    print(f"检查: {num}")
    if num == target2:
        print(f"找到 {target2}!")
        break
else:
    print(f"未找到 {target2}")
```

### while 循环的 else 子句

```python
# 密码验证 - 成功时中断
correct_password = "secret123"
attempts = 0
max_attempts = 3

print("密码验证:")
while attempts < max_attempts:
    attempts += 1
    password = input(f"第 {attempts} 次尝试，请输入密码: ")

    if password == correct_password:
        print("密码正确!")
        break
    else:
        remaining = max_attempts - attempts
        if remaining > 0:
            print(f"密码错误，还有 {remaining} 次机会")
else:
    print(f"密码错误 {max_attempts} 次，账户已锁定")

print("---")

# 计算直到达到条件
current_sum = 0
number = 1
target = 100

print(f"累加数字直到总和达到 {target}:")
while current_sum < target:
    current_sum += number
    print(f"加上 {number}, 总和: {current_sum}")
    number += 1

    if current_sum == target:
        print(f"正好达到目标 {target}!")
        break
else:
    print(f"超过目标 {target}，最终总和: {current_sum}")
```

### 实际应用场景

```python
# 场景1：验证数据完整性
def validate_data(data):
    """验证数据是否包含所有必需字段"""
    required_fields = ["id", "name", "email", "age"]

    for field in required_fields:
        if field not in data:
            print(f"缺少必需字段: {field}")
            return False
        if not data[field]:
            print(f"字段 {field} 不能为空")
            return False
    else:
        print("数据验证通过")
        return True

# 测试数据
valid_data = {"id": 1, "name": "Alice", "email": "alice@example.com", "age": 25}
invalid_data = {"id": 2, "name": "Bob", "age": 30}  # 缺少 email

print("验证有效数据:")
validate_data(valid_data)

print("\n验证无效数据:")
validate_data(invalid_data)

# 场景2：查找素数
def is_prime(n):
    """判断一个数是否为素数"""
    if n < 2:
        return False

    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    else:
        return True

# 查找指定范围内的素数
start = 10
end = 30
primes = []

print(f"\n查找 {start} 到 {end} 之间的素数:")
for num in range(start, end + 1):
    if is_prime(num):
        primes.append(num)
        print(f"{num} 是素数")
else:
    if primes:
        print(f"找到 {len(primes)} 个素数: {primes}")
    else:
        print(f"{start} 到 {end} 之间没有素数")

# 场景3：处理用户输入直到有效
def get_valid_input(prompt, validation_func, error_message="无效输入"):
    """获取有效用户输入"""
    while True:
        user_input = input(prompt)
        if validation_func(user_input):
            return user_input
        else:
            print(error_message)

def validate_email(email):
    """验证邮箱格式"""
    import re
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

print("\n邮箱验证示例:")
try:
    email = get_valid_input(
        "请输入邮箱地址: ",
        validate_email,
        "请输入有效的邮箱地址"
    )
    print(f"有效邮箱: {email}")
except KeyboardInterrupt:
    print("\n用户取消输入")
```

### else 子句的注意事项

```python
# 注意：else 子句只在循环正常完成时执行
numbers = [1, 2, 3, 4, 5]

# 使用 continue 不会影响 else 子句的执行
print("使用 continue 的循环:")
for num in numbers:
    if num % 2 == 0:
        continue  # 跳过偶数
    print(f"处理奇数: {num}")
else:
    print("循环正常完成（即使跳过了某些元素）")

# 使用 break 会阻止 else 子句的执行
print("\n使用 break 的循环:")
for num in numbers:
    if num == 3:
        print("找到3，中断循环")
        break
    print(f"处理数字: {num}")
else:
    print("这条不会执行，因为循环被 break 中断")

# 在嵌套循环中，else 子句属于其对应的循环
print("\n嵌套循环中的 else:")
for i in range(3):
    print(f"外层循环 i = {i}")
    for j in range(3):
        if j == 1:
            break  # 只中断内层循环
        print(f"  内层循环 j = {j}")
    else:
        print("  内层循环正常完成（但被break中断了）")
else:
    print("外层循环正常完成")
```

---

## 嵌套控制结构

嵌套控制结构是指在一个控制结构内部包含另一个控制结构，这是处理复杂逻辑的常用方式。

### 嵌套条件语句

```python
# 多级条件判断
def determine_grade(score):
    """根据分数确定等级和评语"""
    if score >= 0 and score <= 100:
        if score >= 90:
            grade = 'A'
            if score >= 95:
                comment = "卓越表现"
            else:
                comment = "优秀"
        elif score >= 80:
            grade = 'B'
            if score >= 85:
                comment = "良好"
            else:
                comment = "中等偏上"
        elif score >= 70:
            grade = 'C'
            comment = "中等"
        elif score >= 60:
            grade = 'D'
            comment = "及格"
        else:
            grade = 'F'
            comment = "不及格"

        return f"成绩: {grade} ({score}分) - {comment}"
    else:
        return "无效分数"

# 测试
test_scores = [95, 87, 75, 62, 45, 105, -5]
for score in test_scores:
    print(determine_grade(score))

# 复杂的用户权限检查
def check_user_permission(user):
    """检查用户权限"""
    if not user:
        return "用户不存在"

    if not user.get('is_active', False):
        return "用户未激活"

    role = user.get('role', 'guest')
    permissions = user.get('permissions', [])

    if role == 'admin':
        return "管理员：拥有所有权限"
    elif role == 'moderator':
        if 'content_moderation' in permissions:
            return "版主：拥有内容审核权限"
        else:
            return "版主：权限配置不完整"
    elif role == 'user':
        if 'read' in permissions:
            if 'write' in permissions:
                return "用户：拥有读写权限"
            else:
                return "用户：只读权限"
        else:
            return "用户：无权限"
    else:
        return "访客：受限权限"

# 测试用户权限
users = [
    None,
    {'role': 'user', 'is_active': False},
    {'role': 'admin', 'is_active': True},
    {'role': 'moderator', 'is_active': True, 'permissions': ['content_moderation']},
    {'role': 'user', 'is_active': True, 'permissions': ['read']},
    {'role': 'user', 'is_active': True, 'permissions': ['read', 'write']},
    {'role': 'guest', 'is_active': True}
]

for user in users:
    print(check_user_permission(user))
```

### 嵌套循环

```python
# 矩阵操作
def process_matrix(matrix):
    """处理矩阵的每个元素"""
    if not matrix:
        print("矩阵为空")
        return

    rows = len(matrix)
    cols = len(matrix[0]) if rows > 0 else 0

    print(f"处理 {rows}×{cols} 矩阵:")

    # 计算每行和每列的总和
    row_sums = []
    col_sums = [0] * cols

    for i in range(rows):
        row_sum = 0
        for j in range(cols):
            element = matrix[i][j]
            row_sum += element
            col_sums[j] += element
            print(f"  元素 ({i},{j}) = {element}")

        row_sums.append(row_sum)
        print(f"第 {i} 行总和: {row_sum}")

    for j in range(cols):
        print(f"第 {j} 列总和: {col_sums[j]}")

    return row_sums, col_sums

# 测试矩阵
matrix = [
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12]
]

process_matrix(matrix)

# 查找二维数组中的目标值
def find_in_2d_array(arr, target):
    """在二维数组中查找目标值"""
    if not arr:
        return None

    for i, row in enumerate(arr):
        if not row:
            continue

        for j, element in enumerate(row):
            if element == target:
                return (i, j)

    return None

# 测试查找
test_array = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

targets = [5, 10, 7]
for target in targets:
    result = find_in_2d_array(test_array, target)
    if result:
        print(f"找到 {target} 在位置 {result}")
    else:
        print(f"未找到 {target}")
```

### 条件语句与循环的嵌套

```python
# 数据筛选和处理
def filter_and_process_data(data, min_value=None, max_value=None, process_func=None):
    """筛选并处理数据"""
    if not data:
        print("数据为空")
        return []

    processed_data = []

    for i, item in enumerate(data):
        print(f"处理第 {i+1} 个项目: {item}")

        # 应用筛选条件
        if min_value is not None and item < min_value:
            print(f"  跳过：小于最小值 {min_value}")
            continue

        if max_value is not None and item > max_value:
            print(f"  跳过：大于最大值 {max_value}")
            continue

        # 应用处理函数
        if process_func:
            try:
                processed_item = process_func(item)
                print(f"  处理结果: {item} -> {processed_item}")
                processed_data.append(processed_item)
            except Exception as e:
                print(f"  处理失败: {e}")
        else:
            print(f"  保持原样: {item}")
            processed_data.append(item)

    return processed_data

# 测试数据处理
numbers = [15, 8, 25, 3, 18, 30, 12, 7]

# 定义处理函数
def square_if_positive(x):
    if x > 0:
        return x ** 2
    else:
        return x

print("筛选并处理数字 (10-20之间，平方处理):")
result = filter_and_process_data(
    numbers,
    min_value=10,
    max_value=20,
    process_func=square_if_positive
)
print(f"处理结果: {result}")

# 复杂的用户输入处理
def complex_user_input():
    """复杂的用户输入处理"""
    users = []

    while True:
        print("\n=== 用户信息录入 ===")

        # 获取用户名
        while True:
            username = input("请输入用户名 (或 'quit' 退出): ").strip()

            if username.lower() == 'quit':
                if users:
                    print("录入完成，用户列表:")
                    for user in users:
                        print(f"  {user}")
                else:
                    print("没有录入任何用户")
                return users

            if not username:
                print("用户名不能为空")
                continue

            if any(user['username'] == username for user in users):
                print("用户名已存在")
                continue

            if len(username) < 3:
                print("用户名至少3个字符")
                continue

            break

        # 获取年龄
        while True:
            age_input = input("请输入年龄: ").strip()

            if not age_input.isdigit():
                print("请输入有效的数字")
                continue

            age = int(age_input)

            if age < 0 or age > 150:
                print("年龄必须在0-150之间")
                continue

            break

        # 获取邮箱
        while True:
            email = input("请输入邮箱 (可选): ").strip()

            if not email:
                email = None
                break

            import re
            email_pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
            if not re.match(email_pattern, email):
                print("请输入有效的邮箱地址")
                continue

            break

        # 创建用户记录
        user = {
            'username': username,
            'age': age,
            'email': email
        }

        users.append(user)
        print(f"用户 {username} 录入成功！")

        # 询问是否继续
        while True:
            continue_input = input("是否继续录入? (y/n): ").strip().lower()
            if continue_input in ['y', 'yes']:
                break
            elif continue_input in ['n', 'no']:
                if users:
                    print("\n最终用户列表:")
                    for user in users:
                        email_info = f" (邮箱: {user['email']})" if user['email'] else " (无邮箱)"
                        print(f"  {user['username']}, {user['age']}岁{email_info}")
                return users
            else:
                print("请输入 y 或 n")

# 运行用户输入程序
# complex_user_input()
```

### 函数式嵌套

```python
# 嵌套函数和闭包
def create_calculator():
    """创建计算器工厂函数"""

    def add(a, b):
        return a + b

    def subtract(a, b):
        return a - b

    def multiply(a, b):
        return a * b

    def divide(a, b):
        if b == 0:
            return "除数不能为零"
        return a / b

    def calculate(operation, a, b):
        """执行计算操作"""
        operations = {
            '+': add,
            '-': subtract,
            '*': multiply,
            '/': divide
        }

        if operation not in operations:
            return "不支持的操作"

        func = operations[operation]
        return func(a, b)

    return calculate

# 使用计算器
calc = create_calculator()

print("计算器测试:")
operations = ['+', '-', '*', '/', '%']
for op in operations:
    result = calc(op, 10, 5)
    print(f"10 {op} 5 = {result}")

# 高阶函数嵌套
def apply_operations(data, operations):
    """对数据应用一系列操作"""
    result = data

    for i, operation in enumerate(operations):
        print(f"应用第 {i+1} 个操作: {operation.__name__}")

        if callable(operation):
            result = operation(result)
            print(f"  结果: {result}")
        else:
            print(f"  跳过不可调用的操作: {operation}")

    return result

# 定义操作函数
def double(x):
    return x * 2

def square(x):
    return x ** 2

def to_string(x):
    return f"结果: {x}"

# 测试
test_data = 5
operations = [double, square, to_string]
final_result = apply_operations(test_data, operations)
print(f"最终结果: {final_result}")
```

---

## 控制流最佳实践

### 1. 保持控制结构简单

```python
# ❌ 不推荐：过度嵌套
def bad_nested_example(data):
    if data:
        if isinstance(data, list):
            if len(data) > 0:
                if data[0] > 0:
                    for item in data:
                        if item % 2 == 0:
                            print(f"处理偶数: {item}")
                        else:
                            if item > 10:
                                print(f"大于10的奇数: {item}")

# ✅ 推荐：使用早期返回和简化逻辑
def good_nested_example(data):
    """处理数据的良好示例"""
    if not data:
        return

    if not isinstance(data, list) or len(data) == 0:
        return

    if data[0] <= 0:
        return

    for item in data:
        if item % 2 == 0:
            print(f"处理偶数: {item}")
        elif item > 10:
            print(f"大于10的奇数: {item}")
```

### 2. 使用有意义的变量名

```python
# ❌ 不推荐：无意义的变量名
for i in range(len(items)):
    if items[i] > 0:
        for j in range(len(items[i])):
            if items[i][j] % 2 == 0:
                print(items[i][j])

# ✅ 推荐：有意义的变量名
for row_index, row in enumerate(data_matrix):
    if row and row[0] > 0:
        for col_index, value in enumerate(row):
            if value % 2 == 0:
                print(f"位置 ({row_index}, {col_index}) 的偶数: {value}")
```

### 3. 合理使用列表推导式

```python
# 简单情况使用推导式
squares = [x**2 for x in range(1, 11)]
even_squares = [x**2 for x in range(1, 21) if x % 2 == 0]

# 复杂逻辑使用传统循环
def complex_processing(data):
    """复杂的数据处理"""
    result = []

    for item in data:
        # 复杂的处理逻辑
        processed_item = item

        if item > 0:
            processed_item *= 2
        else:
            processed_item = abs(item)

        if processed_item % 3 == 0:
            result.append(processed_item)

    return result
```

### 4. 避免魔法数字

```python
# ❌ 不推荐：使用魔法数字
if age >= 18:
    print("成年")
if score >= 90:
    print("优秀")

# ✅ 推荐：使用常量
ADULT_AGE = 18
EXCELLENT_GRADE = 90

if age >= ADULT_AGE:
    print("成年")
if score >= EXCELLENT_GRADE:
    print("优秀")
```

### 5. 函数职责单一

```python
# ❌ 不推荐：函数做太多事情
def process_user_input_bad():
    """处理用户输入的不良示例"""
    while True:
        name = input("姓名: ")
        if not name:
            continue

        age = input("年龄: ")
        if not age.isdigit():
            continue

        age = int(age)
        if age < 0 or age > 150:
            continue

        email = input("邮箱: ")
        if '@' not in email:
            continue

        # 保存到数据库
        # 发送邮件
        # 更新缓存
        # 记录日志
        print("用户已保存")
        break

# ✅ 推荐：拆分为多个函数
def validate_name(name):
    """验证姓名"""
    return bool(name and name.strip())

def validate_age(age_str):
    """验证年龄"""
    if not age_str.isdigit():
        return None

    age = int(age_str)
    return age if 0 <= age <= 150 else None

def validate_email(email):
    """验证邮箱"""
    import re
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

def save_user_to_database(user_data):
    """保存用户到数据库"""
    # 实际的保存逻辑
    print(f"保存用户: {user_data}")

def process_user_input_good():
    """处理用户输入的良好示例"""
    while True:
        name = input("姓名: ").strip()
        if not validate_name(name):
            print("姓名无效")
            continue

        age_str = input("年龄: ").strip()
        age = validate_age(age_str)
        if age is None:
            print("年龄无效")
            continue

        email = input("邮箱: ").strip()
        if not validate_email(email):
            print("邮箱无效")
            continue

        user_data = {
            'name': name,
            'age': age,
            'email': email
        }

        save_user_to_database(user_data)
        break
```

### 6. 使用异常处理替代复杂的条件判断

```python
# ❌ 不推荐：复杂的条件检查
def process_file_bad(filename):
    """处理文件的不良示例"""
    if os.path.exists(filename):
        if os.path.isfile(filename):
            if os.access(filename, os.R_OK):
                try:
                    with open(filename, 'r') as f:
                        content = f.read()
                        return content
                except:
                    return None
            else:
                return None
        else:
            return None
    else:
        return None

# ✅ 推荐：使用异常处理
import os

def process_file_good(filename):
    """处理文件的良好示例"""
    try:
        with open(filename, 'r') as f:
            return f.read()
    except FileNotFoundError:
        print(f"文件不存在: {filename}")
        return None
    except PermissionError:
        print(f"没有权限读取文件: {filename}")
        return None
    except Exception as e:
        print(f"处理文件时出错: {e}")
        return None
```

---

## 总结

Python 的控制流结构提供了强大的程序控制能力：

### 主要控制结构

1. **条件语句**：
   - `if`: 基本条件判断
   - `if-else`: 二选一判断
   - `if-elif-else`: 多条件判断
   - 三元运算符：简洁的条件表达式

2. **循环语句**：
   - `for`: 遍历序列和可迭代对象
   - `while`: 基于条件的循环
   - `range()`: 生成数字序列
   - `enumerate()`: 获取索引和值
   - `zip()`: 并行遍历多个序列

3. **循环控制**：
   - `break`: 立即终止循环
   - `continue`: 跳过当前迭代
   - `pass`: 空操作，保持语法完整性
   - `else`: 循环正常完成时执行

4. **嵌套结构**：
   - 嵌套条件语句
   - 嵌套循环
   - 条件与循环的嵌套

### 最佳实践

1. 保持控制结构简单，避免过度嵌套
2. 使用有意义的变量名
3. 合理使用列表推导式
4. 避免魔法数字，使用常量
5. 保持函数职责单一
6. 适当使用异常处理

掌握这些控制流结构将帮助您编写逻辑清晰、结构良好的 Python 程序。