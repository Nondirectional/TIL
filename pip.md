# pip

在Python的世界里，**pip**是一个标准的软件包管理系统。它的全称是 "Pip Installs Packages"，或者递归地说是 "Pip Installs Python"。

简单来说，pip就是用来安装和管理Python软件包（也称为库或模块）的工具。这些软件包通常包含其他人编写好的代码，可以帮助你更轻松地完成各种编程任务，而不需要从零开始编写所有功能。

pip的主要功能包括：

- **安装软件包：** 使用 `pip install <package_name>` 命令可以从Python Package Index (PyPI) 或其他索引中下载并安装软件包。
- **卸载软件包：** 使用 `pip uninstall <package_name>` 命令可以移除已安装的软件包。
- **管理软件包：** 可以查看已安装的软件包列表 (`pip list`)，检查软件包的详细信息 (`pip show <package_name>`)，以及管理项目的依赖关系（通过 `requirements.txt` 文件）。
- **升级软件包：** 使用 `pip install --upgrade <package_name>` 命令可以更新已安装的软件包到最新版本。

PyPI (Python Package Index) 是一个存储了大量Python软件包的中央仓库。pip默认就是从PyPI下载软件包。

大多数现代的Python发行版都预装了pip，所以通常你安装好Python后就可以直接使用pip了。



## 设置源加速

## 临时加速

```bash
$ pip install xxx -i https://mirrors.aliyun.com/pypi/simple/
```

如果使用的是 http 的源，会报如下错误（https 源不会有问题）：

```bash
WARNING: The repository located at pypi.douban.com is not a trusted or secure host and is being ignored. If this repository is available via HTTPS we recommend you use HTTPS instead, otherwise you may silence this warning and allow it anyway with '--trusted-host pypi.douban.com'.
```

此时需要信任主机，使用 `--trusted-host`：

```bash
$ pip install selenium -i https://mirrors.aliyun.com/pypi/simple/ --trusted-host pypi.douban.com
```

## 永久加速

```bash
$ pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```

