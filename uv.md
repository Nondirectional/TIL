# uv 使用指南

---

## 1. 简介

uv 是一个用于加速 Python 包管理和虚拟环境的现代工具，目标是替代 pip、virtualenv、pip-tools 等传统工具。它以极快的速度和简洁的命令行体验著称。

---

## 2. 安装 uv

**uv 支持多种安装方式，适用于不同操作系统和用户习惯：**

### 2.1 独立安装器（推荐，适用于所有平台）
- **Windows**：
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```
- **macOS/Linux**：
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
- 指定版本号（示例）：
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/0.7.21/install.ps1 | iex"
  ```

### 2.2 PyPI 安装
- 推荐使用 pipx（隔离环境）：
  ```bash
  pipx install uv
  ```
- 也可以直接用 pip：
  ```bash
  pip install uv
  ```
> 注意：部分平台如无预编译 wheel，pip 安装时会自动从源码构建，需提前安装 Rust 工具链。

### 2.3 WinGet（Windows 包管理器）
```powershell
winget install --id=astral-sh.uv -e
```

### 2.4 Scoop（Windows 包管理器）
```powershell
scoop install main/uv
```

### 2.5 Homebrew（macOS/Linux 包管理器）
```bash
brew install uv
```

### 2.6 Cargo（Rust 用户）
```bash
cargo install --git https://github.com/astral-sh/uv uv
```

### 2.7 Docker
官方提供 Docker 镜像，适合容器化场景。

---

## 3. 安装和加速 Python 版本

**uv 支持直接下载和管理多版本 Python 解释器。**

- 安装指定版本 Python：
  ```bash
  uv python install 3.14
  ```
  其中 `3.14` 是你想安装的 Python 版本号。

### 3.1 镜像加速下载（推荐国内用户）

- 使用加速镜像源：
  ```bash
  uv python install 3.14 -v --mirror https://gh.chjina.com/https://github.com/astral-sh/python-build-standalone/releases/download/
  ```
  - `-v`：显示详细日志（可选）。
  - `--mirror`：指定下载镜像地址。

---

## 4. 依赖管理与加速

### 4.1 安装依赖
- 安装单个包：
  ```bash
  uv pip install requests
  ```
- 安装 requirements.txt：
  ```bash
  uv pip install -r requirements.txt
  ```

### 4.2 依赖下载镜像加速（单次加速）
- 指定 PyPI 镜像源：
  ```bash
  uv pip install -i https://pypi.tuna.tsinghua.edu.cn/simple 包名
  ```
  - `-i` 或 `--index-url`：指定 PyPI 镜像源地址。

  常用国内 PyPI 镜像源：
  - 清华大学：https://pypi.tuna.tsinghua.edu.cn/simple
  - 阿里云：https://mirrors.aliyun.com/pypi/simple
  - 中国科技大学：https://pypi.mirrors.ustc.edu.cn/simple

### 4.3 全局依赖下载加速（配置 uv.toml）
- 编辑 `~/.config/uv/uv.toml`，添加：
  ```toml
  [[index]]
  url = "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/"
  default = true
  ```
  这样所有 uv pip install 命令都会自动使用该镜像，无需每次手动加参数。

---

## 5. 常用命令速查

| 功能           | 命令                                 |
|----------------|--------------------------------------|
| 创建虚拟环境   | uv venv                              |
| 进入虚拟环境   | uv venv shell                        |
| 安装包         | uv pip install 包名                  |
| 升级包         | uv pip install --upgrade 包名        |
| 卸载包         | uv pip uninstall 包名                |
| 列出已安装包   | uv pip list                          |
| 生成依赖文件   | uv pip freeze > requirements.txt     |
| 安装 Python    | uv python install 3.14               |

---

## 6. 其他说明

- uv 兼容 pip 的大部分命令和参数。
- uv 的安装和包管理速度非常快，适合对效率有要求的开发者。
- 你可以把 uv 当作 pip 的超高速替代品来用。

如需更详细的用法或遇到具体问题，可以告诉我你的需求或报错信息，我会进一步帮你解答！

---

## 7. 项目管理与 pyproject.toml

uv 支持现代化的 Python 项目管理，推荐使用 `pyproject.toml` 作为依赖和元数据的声明文件。

### 7.1 创建新项目

你可以通过 uv 快速初始化一个新项目：

```bash
uv init hello-world
cd hello-world
```

或者在当前目录初始化：

```bash
mkdir hello-world
cd hello-world
uv init
```

初始化后，uv 会自动生成如下项目结构：

```
├── .gitignore
├── .python-version
├── README.md
├── main.py
└── pyproject.toml
```

- `main.py`：包含 Hello world 示例代码，可用 `uv run main.py` 直接运行。

### 7.2 项目结构说明

首次运行 `uv run`、`uv sync` 或 `uv lock` 时，uv 会自动创建虚拟环境和锁定文件，完整结构如下：

```
.
├── .venv/                # 虚拟环境目录
├── .python-version       # 项目默认 Python 版本
├── README.md             # 项目说明
├── main.py               # 主程序
├── pyproject.toml        # 项目元数据与依赖声明
└── uv.lock               # 依赖锁定文件
```

- `.venv/`：项目隔离的 Python 环境，依赖都安装在这里。
- `.python-version`：指定项目默认 Python 版本。
- `pyproject.toml`：项目元数据、依赖、配置等。
- `uv.lock`：锁定所有依赖的精确版本，保证环境一致性。

### 7.3 pyproject.toml 说明

`pyproject.toml` 是 Python 官方推荐的项目声明文件，内容示例：

```toml
[project]
name = "hello-world"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
dependencies = []
```

- 你可以手动编辑，也可以用 uv 命令自动管理依赖。

### 7.4 依赖管理

- 添加依赖：
  ```bash
  uv add requests
  ```
- 指定版本：
  ```bash
  uv add 'requests==2.31.0'
  ```
- 添加 git 依赖：
  ```bash
  uv add git+https://github.com/psf/requests
  ```
- 从 requirements.txt 批量导入：
  ```bash
  uv add -r requirements.txt
  ```
- 移除依赖：
  ```bash
  uv remove requests
  ```
- 升级指定依赖：
  ```bash
  uv lock --upgrade-package requests
  ```
- 升级全部依赖：
  ```bash
  uv lock --upgrade
  ```
  
  将所有依赖升级到 pyproject.toml 允许的最新版本，并自动更新 uv.lock 和虚拟环境。

### 7.5 运行与同步

- 运行脚本（自动保证环境和锁文件一致）：
  ```bash
  uv run main.py
  ```
- 运行任意命令：
  ```bash
  uv run -- flask run -p 3000
  ```
- 手动同步环境：
  ```bash
  uv sync
  # 然后激活虚拟环境
  source .venv/bin/activate  # macOS/Linux
  .venv\Scripts\activate    # Windows
  ```

### 7.6 构建分发包

- 构建 wheel 和源码包：
  ```bash
  uv build
  # 生成的包在 dist/ 目录下
  ```
---