# Alembic 数据库迁移工具

## 什么是 Alembic？

**Alembic** 是 SQLAlchemy 的官方数据库迁移工具，用于管理数据库模式变更。它提供了一种版本控制的方式来跟踪和应用数据库结构的变更。

## 核心概念

### 1. 迁移（Migration）
- **Revision**：数据库模式的一个特定版本
- **Upgrade**：从旧版本升级到新版本
- **Downgrade**：从新版本回滚到旧版本

### 2. 自动生成（Autogenerate）
- Alembic 可以比较 SQLAlchemy 模型和当前数据库结构
- 自动生成迁移脚本，减少手动编写的工作量

## 完整工作流程

### 第一步：安装和初始化
```bash
# 安装
pip install alembic

# 初始化项目
alembic init alembic
```

### 第二步：配置 alembic.ini
关键配置项：
```ini
[alembic]
# 迁移脚本存放路径
script_location = alembic

# 数据库连接 URL
sqlalchemy.url = postgresql://user:password@localhost/dbname

# 迁移文件模板
file_template = %%(year)d_%%(month).2d_%%(day).2d_%%(hour).2d%%(minute).2d-%%(rev)s_%%(slug)s
```

### 第三步：配置 env.py
```python
# 在 env.py 中设置 target_metadata
from your_models import Base
target_metadata = Base.metadata
```

### 第四步：常用命令

#### 生成迁移文件：

```bash
# 自动生成迁移（推荐）
alembic revision --autogenerate -m "描述变更内容"

# 手动创建空迁移文件
alembic revision -m "手动迁移"
```

#### 应用迁移：

```bash
# 升级到最新版本
alembic upgrade head

# 升级到指定版本
alembic upgrade +1
alembic upgrade 2023_01_01_1200-abc123_add_user_table

# 降级到指定版本
alembic downgrade -1
alembic downgrade base
```

#### 查看状态：
```bash
# 查看当前版本
alembic current

# 查看所有版本历史
alembic history

# 查看待应用的迁移
alembic heads
```

## 高级用法

### 1. 批量操作
```bash
# 生成迁移但不执行
alembic revision --autogenerate -m "Add new features" --sql

# 输出 SQL 到文件
alembic upgrade head --sql > migration.sql
```

### 2. 分支和合并
```bash
# 创建新分支
alembic revision -m "Branch point" --head=base --branch-label=feature_branch

# 合并分支
alembic merge -m "Merge feature" branch1 branch2
```

### 3. 环境特定配置
```python
# 在 env.py 中根据环境设置不同连接
import os
from dotenv import load_dotenv

load_dotenv()

def get_database_url():
    if os.getenv('ENVIRONMENT') == 'production':
        return os.getenv('PROD_DATABASE_URL')
    else:
        return os.getenv('DEV_DATABASE_URL')
```

## 最佳实践

### 1. 迁移文件命名规范
```ini
# 在 alembic.ini 中设置有意义的文件名格式
file_template = %%(year)d%%(month).2d%%(day).2d_%%(hour).2d%%(minute).2d_%%(rev)s_%%(slug)s
```

### 2. 数据迁移与结构迁移分离
```python
# 示例：添加列并填充默认值
def upgrade():
    # 1. 先添加列（可为空）
    op.add_column('users', sa.Column('status', sa.String(), nullable=True))

    # 2. 填充数据
    connection = op.get_bind()
    connection.execute(
        "UPDATE users SET status = 'active' WHERE status IS NULL"
    )

    # 3. 修改列为非空
    op.alter_column('users', 'status', nullable=False)

def downgrade():
    op.drop_column('users', 'status')
```

### 3. 处理大数据量迁移
```python
def upgrade():
    # 分批处理大量数据
    batch_size = 1000
    offset = 0

    while True:
        # 处理一批数据
        connection = op.get_bind()
        result = connection.execute(
            f"SELECT id FROM large_table LIMIT {batch_size} OFFSET {offset}"
        )

        if not result.rowcount:
            break

        # 处理这批数据...
        offset += batch_size
```

## 常见问题和解决方案

### 1. 自动生成不工作
```python
# 确保 env.py 中的 target_metadata 正确设置
from models import Base  # 确保导入所有模型
target_metadata = Base.metadata
```

### 2. 迁移失败后的恢复
```bash
# 查看当前状态
alembic current

# 标记迁移为已完成（谨慎使用）
alembic stamp head

# 回滚到之前版本
alembic downgrade <previous_revision>
```

### 3. 多人协作时的冲突处理
```bash
# 拉取最新代码后
git pull origin main

# 重新生成迁移
alembic revision --autogenerate -m "Merge and update"

# 解决可能的冲突
```

## 与其他工具集成

### 1. FastAPI 集成
```python
# 在 FastAPI 启动时自动应用迁移
from alembic.config import Config
from alembic import command

def run_migrations():
    alembic_cfg = Config("alembic.ini")
    command.upgrade(alembic_cfg, "head")

# 在应用启动时调用
@app.on_event("startup")
async def startup_event():
    run_migrations()
```

### 2. Docker 集成
```dockerfile
# Dockerfile
RUN pip install alembic

# 启动脚本中
alembic upgrade head
uvicorn main:app --host 0.0.0.0 --port 8000
```

## 详细配置文件示例

### alembic.ini 完整配置
```ini
# A generic, single database configuration.
# 通用的单一数据库配置。

[alembic]
# path to migration scripts
# 迁移脚本的路径
script_location = alembic

# template used to generate migration files
# 用于生成迁移文件的模板
# file_template = %%(rev)s_%%(slug)s

# sys.path path, will be prepended to sys.path if present.
# defaults to the current working directory.
# (new in 1.5.5)

# sys.path 路径，如果存在，将被添加到 sys.path 之前。
# 默认为当前工作目录。
# 版本1.5.5中新增
prepend_sys_path = .

# timezone to use when rendering the date within the migration file
# as well as the filename.
# If specified, requires the python-dateutil library that can be
# installed by adding `alembic[tz]` to the pip requirements
# string value is passed to dateutil.tz.gettz()
# leave blank for localtime

# 在迁移文件中呈现日期以及文件名时要使用的时区。
# 如果指定，则需要可以通过将 `alembic[tz]` 添加到 pip 要求来安装的 python-dateutil 库
# 字符串值将传递给 dateutil.tz.gettz()
# 本地时间留空
# timezone =

# max length of characters to apply to the
# "slug" field

# 应用于"slug"字段的最大字符长度
# truncate_slug_length = 40

# set to 'true' to run the environment during
# the 'revision' command, regardless of autogenerate
# revision_environment = false

# set to 'true' to allow .pyc and .pyo files without
# a source .py file to be detected as revisions in the
# versions/ directory
# sourceless = false

# version location specification; This defaults
# to ${script_location}/versions.  When using multiple version
# directories, initial revisions must be specified with --version-path.
# The path separator used here should be the separator specified by "version_path_separator" below.

# 版本位置规范； 这默认为`${script_location}/versions`。 使用多个版本目录时，必须使用 --version-path 指定初始版本。
# 这里使用的路径分隔符应该是下面"version_path_separator"指定的分隔符。
# version_locations = %(here)s/bar:%(here)s/bat:${script_location}/versions

# version path separator; As mentioned above, this is the character used to split
# version_locations. The default within new alembic.ini files is "os", which uses os.pathsep.
# If this key is omitted entirely, it falls back to the legacy behavior of splitting on spaces and/or commas.
# Valid values for version_path_separator are:
#
# 版本路径分隔符； 如上所述，这是用于拆分 version_locations 的字符。 新 alembic.ini 文件中的默认值是"os"，它使用 os.pathsep。
# 如果这个键被完全省略，它会退回到在空格和/或逗号上分割的传统行为。
# version_path_separator 的有效值为：
#
# version_path_separator = :
# version_path_separator = ;
# version_path_separator = space
version_path_separator = os  # Use os.pathsep. Default configuration used for new projects. (使用 os.pathsep。 用于新项目的默认配置。)

# the output encoding used when revision files
# are written from script.py.mako
# 从 script.py.mako 写入修订文件时使用的输出编码
# output_encoding = utf-8

sqlalchemy.url = postgresql://aaa:@localhost/test


[post_write_hooks]
# This section defines scripts or Python functions that are run
# on newly generated revision scripts.  See the documentation for further
# detail and examples
# 本节定义在新生成的修订脚本上运行的脚本或 Python 函数。 有关更多详细信息和示例，请参阅文档

# format using "black" - use the console_scripts runner,
# against the "black" entrypoint
# 使用"black"格式 - 使用 console_scripts runner，针对"black"入口点
# hooks = black
# black.type = console_scripts
# black.entrypoint = black
# black.options = -l 79 REVISION_SCRIPT_FILENAME

# Logging configuration
# 日志记录配置
[loggers]
keys = root,sqlalchemy,alembic

[handlers]
keys = console

[formatters]
keys = generic

[logger_root]
level = WARN
handlers = console
qualname =

[logger_sqlalchemy]
level = WARN
handlers =
qualname = sqlalchemy.engine

[logger_alembic]
level = INFO
handlers =
qualname = alembic

[handler_console]
class = StreamHandler
args = (sys.stderr,)
level = NOTSET
formatter = generic

[formatter_generic]
format = %(levelname)-5.5s [%(name)s] %(message)s
datefmt = %H:%M:%S
```

## 学习资源

- [Alembic 官方文档](https://alembic.sqlalchemy.org/)
- [SQLAlchemy 官方文档](https://docs.sqlalchemy.org/)
- [FastAPI + Alembic 教程](https://fastapi.tiangolo.com/tutorial/sql-databases/#alembic-migrations)

---

*创建时间：2025-10-16*
*最后更新：2025-10-16*