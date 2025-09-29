# Linux 系统安装 NVIDIA GPU 驱动程序文档

本文档以配备 NVIDIA® Tesla V100 GPU 的 VGV 系列虚拟运算个体（VCS）为例子，详细介绍 Linux 系统下 NVIDIA 驱动的安装步骤，以启用 GPU 资源并加速运算。

## 一、前置准备

1. **确认硬件与系统**：确保使用的虚拟运算个体为 TWCC VGV 系列，配备 NVIDIA Tesla V100 GPU，且操作系统为 Linux（本文以 Linux 为例，Windows 系统操作可参考官方其他指南）。

1. **连接虚拟运算个体**：按照 TWCC 官方指引[连接 Linux 虚拟运算个体](https://man.twcc.ai/@twccdocs/vcs-guide-connect-to-linux-from-windows-zh)，确保能正常执行命令行操作。

## 二、详细安装步骤

### Step 1：搜索并获取适用的驱动程序

1. 访问 [NVIDIA 官方网站（台湾地区）](https://www.nvidia.com.tw/Download/index.aspx?lang=tw)，按以下参数选择驱动：

- **Product Type**：Data Center / Tesla

- **Product Series**：V-Series

- **Product**：Tesla V100

- **Operating System**：选择与当前 Linux 虚拟运算个体一致的操作系统版本

- **CUDA Toolkit**：根据自身运算需求选择（如无特殊需求，可默认推荐版本）

- **Language**：根据偏好选择（如中文、英文）

1. 点击 “**Search**” 按钮，找到匹配的驱动程序后点击 “**Download**”。

1. 右键点击 “**Agree & Download**” 按钮，复制驱动程序文件的下载链接（后续用于在虚拟个体中下载）。


### Step 2：将驱动程序下载至虚拟运算个体

在已连接的 Linux 虚拟个体命令行中，使用 wget 命令下载驱动程序（将示例中的 “驱动程序网址” 替换为 Step 1 中复制的链接）：

```
wget https://tw.download.nvidia.com/tesla/550.90.07/NVIDIA-Linux-x86_64-550.90.07.run  # 示例链接，需替换为实际复制的网址
```

### Step 3：安装依赖库 libc-dev gcc gcc-12 make

执行以下命令更新软件源并安装 libc-dev gcc gcc-12（驱动编译依赖）：

```
sudo apt-get update
sudo apt-get install libc-dev gcc make gcc-12 -y
```

### Step 4：安装对应版本的 Kernel Header

Kernel Header 是驱动与系统内核适配的关键文件，执行以下命令自动安装当前系统内核对应的 Header：

```
sudo apt-get install linux-headers-$(uname -r)
```

- 说明：$(uname -r) 会自动获取当前系统内核版本（如 5.4.0-100-generic），确保安装的 Header 版本与内核完全一致。

### Step 5：关闭 Nouveau 驱动（关键步骤）

Nouveau 是 Linux 系统默认的开源 NVIDIA 驱动，会与官方驱动冲突，需强制关闭：

1. 编辑 Nouveau 黑名单配置文件：

```
sudo vi /etc/modprobe.d/blacklist.conf
```

1. 进入编辑模式（按 i 键），在文件末尾添加以下内容：

```
blacklist nouveau
options nouveau modeset=0
```

1. 保存并退出（按 Esc 键，输入 :wq! 后按 Enter 键）。

1. 重新生成系统内核初始化镜像并重启虚拟个体：

```
sudo update-initramfs -u
sudo reboot
```

1. 重启后重新连接虚拟个体，验证 Nouveau 是否已关闭：

```
lsmod | grep nouveau
```

- 若命令无任何输出，说明 Nouveau 已成功关闭；若有输出，需重新检查 Step 5 的配置步骤。

### Step 6：赋予驱动程序执行权限并运行

1. 为下载的驱动程序文件添加可执行权限（将 “驱动程序文件名” 替换为实际下载的文件名，如 NVIDIA-Linux-x86_64-550.90.07.run）：

```
sudo chmod +x NVIDIA-Linux-x86_64-550.90.07.run  # 替换为实际文件名
```

1. 执行驱动安装程序：

```
sudo ./NVIDIA-Linux-x86_64-550.90.07.run  # 替换为实际文件名
```

### Step 7：按照安装向导选择配置

驱动程序启动后会进入图形化向导（命令行界面），无特殊需求时，全部选择 “**yes**” 或 “**ok**” 即可：

- 操作提示：使用键盘方向键（←/→）切换选项，按 Enter 键确认选择。

- 常见选项说明：

- “Install NVIDIA's 32-bit compatibility libraries?”：建议选 “yes”，确保兼容 32 位应用。

- “Would you like to run the nvidia-xconfig utility?”：建议选 “yes”，自动配置 X 服务器。

### Step 8：验证驱动安装成功

安装完成后，执行以下命令验证驱动是否生效：

```
nvidia-smi
```

- 若输出包含 NVIDIA Tesla V100 GPU 的型号、驱动版本、显存容量等信息（示例如下图），说明驱动安装成功；若提示 “command not found”，需重新检查安装步骤。

**

![nvidia-smi 输出示例](https://i.imgur.com/Vgn1ZQX.png)

## 三、常见问题排查

1. **驱动安装时提示 “Kernel Header 版本不匹配”**：

- 原因：linux-headers-$(uname -r) 未正确安装或内核版本已更新。

- 解决：执行 uname -r 确认当前内核版本，手动安装对应 Header（如 sudo apt-get install linux-headers-5.4.0-100-generic）。

1. **lsmod | grep nouveau 仍有输出**：

- 原因：Nouveau 配置未生效或重启不彻底。

- 解决：重新执行 Step 5 的 sudo update-initramfs -u 和 sudo reboot，确保重启后再验证。

1. **nvidia-smi 无输出或报错**：

- 原因：驱动未正确编译或与系统内核不兼容。

- 解决：检查驱动文件是否与 GPU 型号匹配，重新执行 Step 6-7，安装过程中留意报错信息（如缺少依赖）并补充安装。