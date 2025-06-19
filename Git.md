# Git 配置管理

## Git 身份验证配置

### 为什么每次push/pull都要输入账号密码？

当Git每次进行push/pull操作时都要求输入账号密码，通常是由以下原因造成的：

1. **使用HTTPS协议克隆仓库**：HTTPS协议默认不会保存身份验证信息
2. **没有配置凭据存储**：Git没有被配置为记住用户凭据
3. **凭据缓存过期**：之前配置的凭据已过期
4. **没有使用SSH密钥**：SSH密钥可以实现免密码访问
5. **Personal Access Token未配置**：某些Git服务要求使用Token而非密码

### 解决方案

#### 方案1：配置Git凭据存储（推荐）

```bash
# 永久存储凭据（明文存储，注意安全）
git config --global credential.helper store

# 使用缓存存储凭据（临时存储，默认15分钟）
git config --global credential.helper cache

# 设置缓存时间（例如1小时 = 3600秒）
git config --global credential.helper 'cache --timeout=3600'

# 在Windows上使用Windows凭据管理器
git config --global credential.helper manager-core

# 在macOS上使用钥匙串
git config --global credential.helper osxkeychain
```

#### 方案2：使用SSH密钥（最安全）

```bash
# 1. 生成SSH密钥对
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# 2. 启动ssh-agent
eval "$(ssh-agent -s)"

# 3. 添加私钥到ssh-agent
ssh-add ~/.ssh/id_rsa

# 4. 复制公钥内容
cat ~/.ssh/id_rsa.pub

# 5. 将公钥添加到Git服务（GitHub/GitLab等）的SSH密钥设置中

# 6. 测试SSH连接
ssh -T git@github.com  # GitHub
ssh -T git@gitlab.com  # GitLab

# 7. 将现有仓库的远程URL改为SSH格式
git remote set-url origin git@github.com:username/repository.git
```

#### 方案3：使用Personal Access Token

对于GitHub等服务，现在推荐使用Personal Access Token：

```bash
# 1. 在GitHub上生成Personal Access Token
# Settings -> Developer settings -> Personal access tokens -> Generate new token

# 2. 配置Git使用Token（用户名为你的GitHub用户名，密码为Token）
git config --global credential.helper store

# 3. 下次push时输入用户名和Token，Git会自动保存
```

#### 方案4：在仓库URL中嵌入凭据（不推荐）

```bash
# 注意：这种方式会暴露密码，仅用于临时测试
git remote set-url origin https://username:password@github.com/username/repository.git

# 使用Token的方式
git remote set-url origin https://username:token@github.com/username/repository.git
```

### 检查和管理已保存的凭据

```bash
# 查看当前凭据配置
git config --global --get credential.helper

# 查看已保存的凭据文件位置（store方式）
ls -la ~/.git-credentials

# 清除已保存的凭据
rm ~/.git-credentials

# 清除缓存的凭据
git credential-cache exit

# 手动添加凭据到存储
echo "https://username:password@github.com" >> ~/.git-credentials
```

### 不同平台的最佳实践

#### Linux (WSL2/Ubuntu)
```bash
# 推荐使用SSH密钥 + 凭据存储组合
git config --global credential.helper store
# 然后配置SSH密钥
```

#### Windows
```bash
# 使用Windows凭据管理器
git config --global credential.helper manager-core
```

#### macOS
```bash
# 使用钥匙串存储
git config --global credential.helper osxkeychain
```

### 常见问题排查

#### 问题1：配置凭据存储后仍然要求输入密码
```bash
# 检查凭据配置
git config --list | grep credential

# 检查远程URL格式
git remote -v

# 确保使用HTTPS格式的URL
git remote set-url origin https://github.com/username/repository.git
```

#### 问题2：SSH密钥配置后仍然要求密码
```bash
# 检查SSH密钥是否正确加载
ssh-add -l

# 检查远程URL是否为SSH格式
git remote -v

# 应该显示类似：git@github.com:username/repository.git
```

#### 问题3：Token或密码已更改
```bash
# 清除旧的凭据
git credential-manager delete https://github.com
# 或
rm ~/.git-credentials

# 重新配置
git config --global credential.helper store
```

### 安全建议

1. **优先使用SSH密钥**：最安全的身份验证方式
2. **使用Personal Access Token**：避免使用真实密码
3. **定期轮换Token**：设置Token过期时间并定期更新
4. **谨慎使用store方式**：明文存储密码存在安全风险
5. **不要在URL中嵌入密码**：容易泄露敏感信息

## Git 代理配置重置

在开发过程中，我们可能需要配置Git代理来访问远程仓库，但有时也需要重置这些代理配置。以下是完整的重置方法：

### 1. 查看当前代理配置

```bash
# 查看全局代理配置
git config --global --get http.proxy
git config --global --get https.proxy

# 查看当前仓库的代理配置
git config --get http.proxy
git config --get https.proxy

# 查看所有包含proxy的配置
git config --list | grep proxy
```

### 2. 重置全局代理配置

```bash
# 删除全局HTTP代理配置
git config --global --unset http.proxy

# 删除全局HTTPS代理配置  
git config --global --unset https.proxy

# 删除所有HTTP相关配置（谨慎使用）
git config --global --remove-section http 2>/dev/null || true
```

### 3. 重置当前仓库的代理配置

```bash
# 删除当前仓库的HTTP代理配置
git config --unset http.proxy

# 删除当前仓库的HTTPS代理配置
git config --unset https.proxy
```

### 4. 重置特定URL的代理配置

有时我们可能为特定的Git服务（如GitHub、GitLab）设置了特定的代理：

```bash
# 删除GitHub的代理配置
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy

# 删除GitLab的代理配置
git config --global --unset http.https://gitlab.com.proxy
git config --global --unset https.https://gitlab.com.proxy
```

### 5. 一键重置所有代理配置

```bash
# 创建一个脚本来重置所有可能的代理配置
#!/bin/bash

echo "正在重置Git代理配置..."

# 重置全局配置
git config --global --unset http.proxy 2>/dev/null || true
git config --global --unset https.proxy 2>/dev/null || true

# 重置当前仓库配置
git config --unset http.proxy 2>/dev/null || true
git config --unset https.proxy 2>/dev/null || true

# 重置常见服务的特定代理配置
git config --global --unset http.https://github.com.proxy 2>/dev/null || true
git config --global --unset https.https://github.com.proxy 2>/dev/null || true
git config --global --unset http.https://gitlab.com.proxy 2>/dev/null || true
git config --global --unset https.https://gitlab.com.proxy 2>/dev/null || true

echo "Git代理配置重置完成！"
echo "当前代理相关配置："
git config --list | grep proxy || echo "没有找到代理配置"
```

### 6. 验证重置结果

```bash
# 查看是否还有代理配置残留
git config --list | grep proxy

# 测试Git连接（以GitHub为例）
git ls-remote https://github.com/git/git.git HEAD

# 如果命令执行成功且没有代理相关输出，说明重置成功
```

### 7. 常见问题排查

#### 问题1：重置后仍然无法访问
可能原因：系统级代理或环境变量设置的代理仍在生效

```bash
# 检查环境变量
echo $http_proxy
echo $https_proxy
echo $HTTP_PROXY
echo $HTTPS_PROXY

# 临时清除环境变量代理
unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY
```

#### 问题2：重置后出现权限错误
```bash
# 检查Git配置文件权限
ls -la ~/.gitconfig
ls -la .git/config

# 修复权限（如果需要）
chmod 644 ~/.gitconfig
chmod 644 .git/config
```

### 8. 重新配置代理（如果需要）

如果重置后需要重新配置代理：

```bash
# 设置全局代理
git config --global http.proxy http://proxy-server:port
git config --global https.proxy http://proxy-server:port

# 设置特定URL的代理
git config --global http.https://github.com.proxy http://proxy-server:port
```

## 其他Git配置管理

### 查看所有Git配置
```bash
# 查看全局配置
git config --global --list

# 查看当前仓库配置
git config --local --list

# 查看系统配置
git config --system --list

# 查看所有配置（包含来源）
git config --list --show-origin
```

### 备份和恢复Git配置
```bash
# 备份全局配置
cp ~/.gitconfig ~/.gitconfig.backup

# 恢复配置
cp ~/.gitconfig.backup ~/.gitconfig
``` 