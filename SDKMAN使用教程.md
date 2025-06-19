# SDKMAN! 使用教程

> 参考：[SDKMAN! Usage Documentation](https://sdkman.io/usage#latest-stable)

## 什么是 SDKMAN!

SDKMAN! (Software Development Kit Manager) 是一个用于管理多个软件开发工具版本的工具，特别适用于管理 Java、Kotlin、Scala、Groovy 等 JVM 生态系统的工具。

## 基本操作

### 安装 SDK

#### 安装最新稳定版本
安装指定 SDK 的最新稳定版本：

```bash
sdk install java
```

输出示例：
```
Downloading: java 21.0.4-tem

In progress...

######################################################################## 100.0%

Installing: java 21.0.4-tem
Done installing!

Do you want java 21.0.4-tem to be set as default? (Y/n):
```

回答 `Y` 或直接回车，将设置该版本为默认版本。

#### 安装特定版本
安装指定版本的 SDK：

```bash
sdk install scala 3.4.2
```

#### 安装本地版本
如果已有本地安装或使用快照版本，可以指定本地路径：

```bash
# 安装快照版本
sdk install groovy 3.0.0-SNAPSHOT /path/to/groovy-3.0.0-SNAPSHOT

# 安装本地 JDK
sdk install java 17-zulu /Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home
```

> **注意**：本地版本名称必须唯一，不能与已存在的版本名称冲突。

### 删除版本
删除已安装的版本：

```bash
sdk uninstall scala 3.4.2
```

> **注意**：删除本地版本不会删除本地安装文件。

### 查看可用候选版本
查看所有可用的 SDK 候选版本：

```bash
sdk list
```

这将显示一个可搜索的字母顺序列表，包含名称、当前稳定默认版本、网站 URL、描述和安装命令。

### 查看特定 SDK 的版本
查看特定 SDK 的所有可用版本：

```bash
sdk list groovy
```

输出示例：
```
================================================================================
Available Groovy Versions
================================================================================
> * 2.4.4                2.3.1                2.0.8                1.8.3
2.4.3                2.3.0                2.0.7                1.8.2
2.4.2                2.2.2                2.0.6                1.8.1
2.4.1                2.2.1                2.0.5                1.8.0
2.4.0                2.2.0                2.0.4                1.7.9
...

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```

## 版本管理

### 使用特定版本
在当前终端中使用指定版本：

```bash
sdk use scala 3.4.2
```

> **重要**：这只会为当前 shell 切换版本。要使更改永久生效，请使用 `default` 命令。

### 设置默认版本
将指定版本设置为默认版本：

```bash
sdk default scala 3.4.2
```

这将确保所有后续 shell 都使用该版本。

### 查看当前版本
查看特定 SDK 的当前版本：

```bash
sdk current java
```

输出：
```
Using java version 21.0.4-tem
```

查看所有 SDK 的当前版本：

```bash
sdk current
```

输出：
```
Using:
groovy: 4.0.22
java: 21.0.4-tem
scala: 3.4.2
```

## 项目环境管理

### 初始化项目环境
为项目创建 `.sdkmanrc` 文件：

```bash
sdk env init
```

这将创建包含以下内容的配置文件：
```
# Enable auto-env through the sdkman_auto_env config
# Add key=value pairs of SDKs to use below
java=21.0.4-tem
```

### 应用项目环境
切换到 `.sdkmanrc` 文件中指定的 SDK 版本：

```bash
sdk env
```

输出：
```
Using java version 21.0.4-tem in this shell.
```

### 清除项目环境
恢复到默认版本：

```bash
sdk env clear
```

输出：
```
Restored java version to 21.0.4-tem (default)
```

### 安装项目依赖
安装项目中 `.sdkmanrc` 文件指定的缺失 SDK：

```bash
sdk env install
```

## 高级功能

### 升级版本
查看特定 SDK 的过时版本：

```bash
sdk upgrade springboot
```

查看所有过时的 SDK：

```bash
sdk upgrade
```

### 离线模式
启用离线模式（当网络不可用时）：

```bash
sdk offline enable
```

禁用离线模式：

```bash
sdk offline disable
```

在离线模式下，大多数命令仍可工作，但功能会有所限制。

### 自更新
更新 SDKMAN! 自身：

```bash
sdk selfupdate
```

强制重新安装：

```bash
sdk selfupdate force
```

### 更新候选版本缓存
刷新候选版本元数据：

```bash
sdk update
```

### 清理缓存
清理 SDKMAN! 的本地状态：

```bash
sdk flush
```

> **重要**：不要手动删除 `.sdkman/tmp` 等目录，始终使用 `flush` 命令！

### 获取 SDK 路径
获取 SDK 的绝对路径（类似 macOS 的 `java_home` 命令）：

```bash
sdk home java 21.0.4-tem
```

输出：
```
/home/myuser/.sdkman/candidates/java/21.0.4-tem
```

### 查看版本信息
显示 SDKMAN! 的当前版本：

```bash
sdk version
```

输出：
```
SDKMAN!
script: 5.7.0
native: 0.1.3
```

## 帮助和配置

### 获取帮助
查看基本帮助：

```bash
sdk help
```

查看特定命令的帮助：

```bash
sdk help install
```

### 配置
编辑配置文件：

```bash
sdk config
```

主要配置选项：

```bash
# 使 SDKMAN 非交互式，适用于 CI 环境
sdkman_auto_answer=true|false

# 检查新版本并提示更新
sdkman_selfupdate_feature=true|false

# 禁用 SSL 证书验证（不推荐）
sdkman_insecure_ssl=true|false

# 配置 curl 超时
sdkman_curl_connect_timeout=5
sdkman_curl_continue=true
sdkman_curl_max_time=10

# 订阅测试版频道
sdkman_beta_channel=true|false

# 启用详细调试模式
sdkman_debug_mode=true|false

# 启用彩色模式
sdkman_colour_enable=true|false

# 启用自动环境切换
sdkman_auto_env=true|false

# 启用 bash 或 zsh 自动补全
sdkman_auto_complete=true|false
```

## 常用工作流程

### 1. 新项目设置
```bash
# 进入项目目录
cd my-project

# 初始化项目环境
sdk env init

# 编辑 .sdkmanrc 文件，添加需要的 SDK 版本
# 例如：java=17.0.2-tem, gradle=8.0

# 安装项目依赖
sdk env install

# 应用项目环境
sdk env
```

### 2. 切换 Java 版本
```bash
# 查看可用的 Java 版本
sdk list java

# 安装特定版本
sdk install java 17.0.2-tem

# 设置为默认版本
sdk default java 17.0.2-tem

# 或在当前 shell 中使用
sdk use java 17.0.2-tem
```

### 3. 管理多个项目
```bash
# 项目 A 使用 Java 17
cd project-a
sdk env init
echo "java=17.0.2-tem" > .sdkmanrc
sdk env

# 项目 B 使用 Java 21
cd ../project-b
sdk env init
echo "java=21.0.4-tem" > .sdkmanrc
sdk env
```

## 总结

SDKMAN! 是一个强大的工具，可以帮助开发者轻松管理多个 SDK 版本。通过合理使用项目环境配置和版本管理功能，可以确保不同项目使用正确的工具版本，提高开发效率。

> 参考链接：[https://sdkman.io/usage#latest-stable](https://sdkman.io/usage#latest-stable) 