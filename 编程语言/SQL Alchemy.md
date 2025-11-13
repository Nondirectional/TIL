# SQLAlchemy Core vs ORM

SQLAlchemy 提供了两个不同层次的 API：

## SQLAlchemy Core

SQLAlchemy Core 是 SQLAlchemy 作为"数据库工具包"的基础架构。该库提供了管理数据库连接、与数据库查询和结果交互以及程序化构建 SQL 语句的工具。

SQLAlchemy Code 从 `sqlalchemy` 命名空间导入

## SQLAlchemy ORM

SQLAlchemy ORM 在 Core 之上构建，提供可选的对象关系映射能力。ORM 提供了一个额外的配置层，允许用户定义的 Python 类映射到数据库表和其他构造，以及被称为 Session 的对象持久化机制。它还扩展了 Core 级别的 SQL 表达式语言，允许用用户定义的对象来组合和调用 SQL 查询。

SQLAlchemy 构造从 `sqlalchemy.orm` 命名空间导入
