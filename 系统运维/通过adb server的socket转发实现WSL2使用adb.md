# 通过adb server的socket转发实现WSL2使用adb

本教程介绍如何在WSL2中通过adb server的socket转发来实现adb功能。这种方法的特点是：
- 上手难度：较高
- 稳定性：中等
- 兼容性：非常好

## 实现步骤

### 1. 下载匹配版本的adb

首先需要从Android官网下载与Windows侧版本完全一致的adb。版本一致性非常重要，否则无法实现转发。

检查Windows侧adb版本：
```bash
adb version
```

输出示例：
```
Android Debug Bridge version 1.0.41
Version 30.0.5-6877874
Installed as D:\dev_tools\android-sdk\platform-tools\adb.exe
```

请注意记录版本号（如上例中的1.0.41），确保下载的Linux版本adb与之完全匹配。

### 2. 确认WSL2中的adb版本

如果之前通过alias方式配置过adb，需要先注释掉相关配置并重新打开终端。然后执行：

```bash
which adb
```

这个命令会显示当前使用的adb路径，确保使用的是正确版本的adb。

### 3. 启动Windows侧的adb server

在Windows的命令提示符或PowerShell中执行：

```bash
adb kill-server
adb -a -P 5037 nodaemon server
```

参数说明：
- `-a`：让adb server监听所有网络设备（WSL2使用虚拟网卡，不是localhost）
- `-P 5037`：指定监听端口为5037（默认端口）
- `nodaemon`：以非守护进程模式运行
- `server`：启动adb server

### 4. 配置WSL2环境变量

在WSL2中，需要设置环境变量来指定adb server的地址。首先获取Windows主机IP：

```bash
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
```

然后设置环境变量：

```bash
export ADB_SERVER_SOCKET=tcp:$windows_host:5037
```

其中`$windows_host`替换为上一步获取的IP地址。

### 5. 自动化配置（可选）

为了方便日常使用，可以添加以下shell函数到`~/.bashrc`或`~/.zshrc`：

```bash
# 获取Windows主机IP
function get_win_host() {
    cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
}

# 启动adb server
function start_adb_server() {
    windows_host=$(get_win_host)
    export ADB_SERVER_SOCKET=tcp:$windows_host:5037
    echo "ADB_SERVER_SOCKET设置为：$ADB_SERVER_SOCKET"
}

# 停止adb server
function stop_adb_server() {
    adb kill-server
    unset ADB_SERVER_SOCKET
    echo "ADB server已停止，环境变量已清除"
}

# 重启adb server
function restart_adb_server() {
    stop_adb_server
    start_adb_server
}
```

## 验证配置

完成上述配置后，在WSL2中执行：

```bash
adb devices
```

如果能看到设备列表，说明配置成功。

## 注意事项

1. 每次重启WSL2或Windows后，需要重新启动adb server
2. 如果遇到连接问题，可以尝试重启adb server
3. 确保Windows防火墙没有阻止WSL2与Windows之间的通信
4. 保持Windows侧的adb server窗口开启状态

## 优缺点分析

优点：
- 兼容性最好，支持所有adb命令
- 与原生Ubuntu下的adb使用体验一致
- 不会出现命令未找到的问题

缺点：
- 配置相对复杂
- 需要维护Windows侧的adb server进程
- 重启后需要重新配置

## 参考来源

本教程内容参考自[CSDN博客](https://blog.csdn.net/u014175785/article/details/113438143)。 