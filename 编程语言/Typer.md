# Typer 命令行工具教程

## 目录
- [快速开始](#快速开始)
  - [最小化示例](#最小化示例)
- [命令的基本使用](#命令的基本使用)
  - [多命令程序](#多命令程序)
- [参数和选项](#参数和选项)
  - [命令参数](#命令参数)
- [命令进阶用法](#命令进阶用法)
  - [自定义命令名称](#自定义命令名称)
  - [添加帮助信息](#添加帮助信息)
  - [子命令 (Subcommands)](#子命令-subcommands)

## 快速开始

Typer 是一个用于构建命令行应用的 Python 库，它基于 Python 类型提示功能，能够帮助我们快速构建功能丰富的命令行程序。

### 安装

```bash
pip install typer
```

### 最小化示例

下面是一个最简单的 Typer 程序示例：

```python
import typer

app = typer.Typer()

@app.command()
def say_hello():
    print("Hello World!")

if __name__ == "__main__":
    app()
```

运行程序：
```bash
$ python main.py
Hello World!
```

当程序只有一个命令时，Typer 会自动执行该命令。我们也可以通过 `--help` 参数查看程序的帮助信息：

```bash
$ python main.py --help
Usage: main.py [OPTIONS] COMMAND [ARGS]...
                                                                      
╭─ Options ────────────────────────────────────────────────────────────────────╮
│ --install-completion    Install completion for the current shell.            │
│ --show-completion      Show completion for the current shell.                │
│ --help                 Show this message and exit.                           │
╰──────────────────────────────────────────────────────────────────────────────╯
```

## 命令的基本使用

### 多命令程序

Typer 支持在一个程序中定义多个命令：

```python
import typer

app = typer.Typer()

@app.command()
def say_hello():
    print("Hello World!")

@app.command()
def say_goodbye():
    print("Goodbye World!")

if __name__ == "__main__":
    app()
```

当程序包含多个命令时，需要在运行时指定要执行的命令：

```bash
$ python main.py say-hello
Hello World!
$ python main.py say-goodbye
Goodbye World!
```

如果不指定具体命令，程序会报错：

```bash
$ python main.py
Usage: test.py [OPTIONS] COMMAND [ARGS]...
Try 'test.py --help' for help.
╭─ Error ──────────────────────────────────────╮
│ Missing command.                             │
╰──────────────────────────────────────────────╯
```

## 参数和选项

### 命令参数

Typer 支持两种类型的命令行输入：Arguments（参数）和 Options（选项）。

**Arguments（参数）**：
- 位置性参数，顺序很重要
- 直接跟在命令后面，无需前缀
- 通常用于必需的主要输入

**Options（选项）**：
- 带有名称的参数，使用 `--` 或 `-` 前缀
- 顺序不重要
- 通常用于可选配置

示例：

```python
from typing import Annotated
import typer

app = typer.Typer()

@app.command()
def greet(
    name: Annotated[str, typer.Argument(help="要问候的名字")],
    count: Annotated[int, typer.Option(help="重复次数")] = 1
):
    """向某人问好"""
    for _ in range(count):
        print(f"Hello {name}!")

if __name__ == "__main__":
    app()
```

使用方式：
```bash
# 使用位置参数
$ python main.py greet World
Hello World!

# 使用命名参数
$ python main.py greet --name World
Hello World!

# 使用选项
$ python main.py greet World --count 3
Hello World!
Hello World!
Hello World!
```

## 命令进阶用法

### 自定义命令名称

默认情况下，Typer 使用函数名转换为命令名（将下划线替换为连字符）。我们也可以通过 `name` 参数自定义命令名：

```python
import typer
app = typer.Typer()

@app.command(name="hello")
def say_hello(name: str):
    print(f"Hello {name}!")

@app.command(name="bye")
def say_goodbye(name: str):
    print(f"Goodbye {name}!")

if __name__ == "__main__":
    app()
```

### 添加帮助信息

为了让命令行工具更易用，我们可以添加帮助信息：

```python
import typer
from typing import Annotated

app = typer.Typer()

@app.command(
    name="hello",
    help="向某人问好，默认是向世界问好"
)
def say_hello(
    name: Annotated[str, typer.Argument(
        help="要问候的对象名称",
        default="World"
    )]
):
    print(f"Hello {name}!")

if __name__ == "__main__":
    app()
```

查看帮助信息：
```bash
$ python main.py hello --help
Usage: main.py hello [OPTIONS] [NAME]

向某人问好，默认是向世界问好

╭─ Arguments ───────────────────────────────────────────────────────╮
│    name      TEXT  [default: World]  要问候的对象名称            │
╰───────────────────────────────────────────────────────────────────╯
╭─ Options ─────────────────────────────────────────────────────────╮
│ --help      Show this message and exit.                          │
╰───────────────────────────────────────────────────────────────────╯
```

### 子命令 (Subcommands)

当命令行工具功能逐渐复杂时，将相关功能组织成子命令是一种很好的实践。Typer 通过 `add_typer()` 方法轻松实现子命令的创建和管理。

**什么是子命令？**

子命令允许你将一个大的 CLI 程序拆分成多个小的、功能独立的单元。例如，`git` 命令有 `git commit`、`git push`、`git pull` 等子命令。每个子命令可以有自己的参数和选项。

**如何创建子命令**

创建子命令通常涉及以下步骤：

1.  **创建主 `Typer` 应用实例**：这是你 CLI 的入口点。
2.  **创建子 `Typer` 应用实例**：每个子命令组对应一个 `Typer` 实例。
3.  **使用 `app.add_typer()` 添加子应用**：将子应用注册到主应用上，并指定子命令的名称。
4.  **在子应用中定义具体的命令**：使用 `@sub_app.command()` 装饰器。

**示例：**

假设我们要创建一个管理用户 (users) 和物品 (items) 的 CLI 工具。

```python
import typer
from typing_extensions import Annotated

# 主应用
app = typer.Typer()

# 用户管理的子应用
users_app = typer.Typer()
app.add_typer(users_app, name="users", help="管理用户相关操作")

# 物品管理的子应用
items_app = typer.Typer()
app.add_typer(items_app, name="items", help="管理物品相关操作")


@users_app.command("create")
def users_create(username: Annotated[str, typer.Argument(help="要创建的用户名")]):
    """
    创建一个新用户。
    """
    print(f"正在创建用户: {username}")

@users_app.command("delete")
def users_delete(username: Annotated[str, typer.Argument(help="要删除的用户名")]):
    """
    删除一个用户。
    """
    print(f"正在删除用户: {username}")


@items_app.command("add")
def items_add(item_name: Annotated[str, typer.Argument(help="要添加的物品名称")], quantity: Annotated[int, typer.Option(help="物品数量")] = 1):
    """
    添加一个新物品。
    """
    print(f"正在添加物品: {item_name}, 数量: {quantity}")

@items_app.command("remove")
def items_remove(item_name: Annotated[str, typer.Argument(help="要移除的物品名称")]):
    """
    移除一个物品。
    """
    print(f"正在移除物品: {item_name}")


if __name__ == "__main__":
    app()
```

**如何调用子命令**

现在，你可以像下面这样调用你的子命令：

```bash
# 查看主应用帮助信息，会列出 users 和 items 子命令组
$ python main.py --help
Usage: main.py [OPTIONS] COMMAND [ARGS]...

╭─ Options ────────────────────────────────────────────────────────────────────╮
│ --install-completion          Install completion for the current shell.      │
│ --show-completion             Show completion for the current shell.         │
│ --help                        Show this message and exit.                    │
╰──────────────────────────────────────────────────────────────────────────────╯
╭─ Commands ───────────────────────────────────────────────────────────────────╮
│ items                         管理物品相关操作                               │
│ users                         管理用户相关操作                               │
╰──────────────────────────────────────────────────────────────────────────────╯

# 查看 users 子命令组的帮助信息
$ python main.py users --help
Usage: main.py users [OPTIONS] COMMAND [ARGS]...

 管理用户相关操作

╭─ Options ────────────────────────────────────────────────────────────────────╮
│ --help                        Show this message and exit.                    │
╰──────────────────────────────────────────────────────────────────────────────╯
╭─ Commands ───────────────────────────────────────────────────────────────────╮
│ create                        创建一个新用户。                                │
│ delete                        删除一个用户。                                  │
╰──────────────────────────────────────────────────────────────────────────────╯

# 调用 users 子命令组下的 create 命令
$ python main.py users create Alice
正在创建用户: Alice

# 调用 items 子命令组下的 add 命令
$ python main.py items add Sword --quantity 2
正在添加物品: Sword, 数量: 2
```

**为子命令组添加帮助信息**

如上面的示例所示，在 `app.add_typer()` 时，可以通过 `help` 参数为子命令组提供描述信息，这会在主命令的帮助文本中显示。

```python
# ... (其他代码) ...
app.add_typer(users_app, name="users", help="管理用户相关操作")
app.add_typer(items_app, name="items", help="管理物品相关操作")
# ... (其他代码) ...
```

通过这种方式，你可以构建出结构清晰、易于扩展的复杂命令行应用程序。

        