# WSL 使用宿主机网络代理

> 参考：[wsl使用宿主机网络代理 - CSDN博客](https://blog.csdn.net/qq_47564006/article/details/135058896)

## 问题说明
在 WSL (Windows Subsystem for Linux) 中，有时需要使用 Windows 宿主机上的网络代理（如科学上网、公司代理等），但 WSL 默认无法直接使用宿主机的 localhost 代理端口。本文记录如何让 WSL 通过宿主机的代理访问外网。

## 操作步骤

### 1. 获取宿主机 IP
在 WSL 终端中运行以下命令，获取 Windows 宿主机的 IP 地址：

```bash
ip route | grep default | awk '{print $3}'
```

例如输出：
```
172.18.160.1
```

### 2. 设置代理环境变量
假设宿主机代理端口为 7890（如 Clash、V2rayN 等），在 WSL 终端中执行：

```bash
export https_proxy="http://172.18.160.1:7890"
export http_proxy="http://172.18.160.1:7890"
export all_proxy="socks5://172.18.160.1:7890"
export ALL_PROXY="socks5://172.18.160.1:7890"
```

> **说明**：
> - 端口号需根据实际代理软件设置调整。
> - 若想每次启动自动生效，可将上述内容写入 `~/.zshrc` 或 `~/.bashrc`，然后执行 `source ~/.zshrc`。

### 3. 快捷开启/关闭代理
可以在 `~/.zshrc` 中添加如下 alias，方便随时开启或关闭代理：

```bash
alias setp='export https_proxy="http://172.18.160.1:7890"; export http_proxy="http://172.18.160.1:7890"; export all_proxy="socks5://172.18.160.1:7890"; export ALL_PROXY="socks5://172.18.160.1:7890";'
alias unsetp='unset https_proxy; unset http_proxy; unset all_proxy; unset ALL_PROXY;'
```

- 输入 `setp` 即可开启代理
- 输入 `unsetp` 即可关闭代理

### 4. 注意事项
- 若宿主机代理端口或 IP 发生变化，需同步修改上述命令。
- 某些情况下，可能需要在 Windows 代理软件中开启"允许局域网连接"选项。
- 若代理为 socks5，注意协议前缀为 `socks5://`。

---

## 总结
通过上述方法，可以让 WSL 方便地使用 Windows 宿主机上的网络代理，解决 WSL 无法直接访问 localhost 代理端口的问题。

> 参考链接：[https://blog.csdn.net/qq_47564006/article/details/135058896](https://blog.csdn.net/qq_47564006/article/details/135058896) 