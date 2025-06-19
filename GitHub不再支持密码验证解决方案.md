# GitHub不再支持密码验证解决方案：SSH免密与Token登录配置

> 参考：[GitHub不再支持密码验证解决方案 - 腾讯云开发者社区](https://cloud.tencent.com/developer/article/1861466)

## 问题背景

从2021年8月13日开始，GitHub不再支持密码验证进行Git操作，需要使用Token-based认证方式。当尝试使用密码推送代码时，会出现以下错误：

```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: unable to access 'https://github.com/username/repo.git/': The requested URL returned error: 403
```

## 解决方案

有两种主要的解决方案：
1. **SSH免密登录**（推荐）
2. **Personal Access Token登录**

## 方案一：SSH免密登录配置

### 1. 检查现有SSH密钥

首先检查是否已经配置过SSH密钥：

```bash
ls -al ~/.ssh
```

如果已配置，会看到以下文件：
- `id_rsa` (私钥) - 不能泄露
- `id_rsa.pub` (公钥) - 需要添加到GitHub

如果没有配置，则继续下一步。

### 2. 生成新的SSH密钥

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

> **参数说明**：
> - `-t rsa`：指定密钥类型为RSA
> - `-b 4096`：指定密钥长度为4096位
> - `-C "email"`：添加注释，通常是邮箱地址

**交互提示**：
1. 保存路径：建议直接回车使用默认路径
2. 私钥密码：如果不需要密码保护，直接回车；否则设置密码

### 3. 添加SSH密钥到GitHub

#### 复制公钥内容
```bash
cat ~/.ssh/id_rsa.pub
```

#### 在GitHub中添加SSH密钥
1. 登录GitHub，点击头像 → Settings
2. 左侧菜单选择 "SSH and GPG keys"
3. 点击 "New SSH key"
4. 填写标题（如：My MacBook）
5. 粘贴公钥内容到Key字段
6. 点击 "Add SSH key"

### 4. 测试SSH连接

```bash
ssh -T git@github.com
```

首次连接会提示确认，输入 `yes` 即可。

### 5. 修改Git仓库地址

如果当前使用的是HTTPS地址，需要修改为SSH地址：

#### 方法一：使用命令修改
```bash
git remote set-url origin git@github.com:username/repository.git
```

#### 方法二：删除后重新添加
```bash
git remote remove origin
git remote add origin git@github.com:username/repository.git
```

#### 方法三：直接编辑配置文件
编辑 `.git/config` 文件，将URL替换为SSH格式。

## 方案二：Personal Access Token配置

### 1. 生成Personal Access Token

1. 登录GitHub，点击头像 → Settings
2. 左侧菜单选择 "Developer settings"
3. 选择 "Personal access tokens" → "Tokens (classic)"
4. 点击 "Generate new token" → "Generate new token (classic)"
5. 填写Token描述
6. 选择权限范围（建议根据需要选择，或全选）
7. 点击 "Generate token"
8. **重要**：复制生成的Token（只显示一次）

### 2. 使用Token进行Git操作

#### 命令行使用
```bash
# 克隆仓库时使用Token
git clone https://github.com/username/repository.git
# 当提示输入用户名和密码时：
# 用户名：GitHub用户名
# 密码：刚才生成的Token

# 或者直接在URL中包含Token
git clone https://username:token@github.com/username/repository.git
```

#### 配置Git凭证存储
```bash
# 配置凭证助手，避免每次都输入
git config --global credential.helper store
```

## IDE配置（以IntelliJ IDEA为例）

### 1. 配置Git路径
1. File → Settings → Version Control → Git
2. 在 "Path to Git executable" 中填入Git路径
3. 点击 "Test" 验证

### 2. 配置GitHub账户

#### 使用Token方式
1. File → Settings → Version Control → GitHub
2. 点击 "+" 添加账户
3. 选择 "Log in with Token"
4. 填入Token

#### 使用SSH方式
1. 确保已配置SSH密钥
2. 在GitHub设置中选择 "Use SSH"
3. 使用SSH格式的仓库地址

## 常见问题解决

### 1. SSH连接失败
```bash
# 检查SSH代理
ssh-add -l

# 添加SSH密钥到代理
ssh-add ~/.ssh/id_rsa

# 测试连接
ssh -T git@github.com
```

### 2. Token权限不足
确保Token具有足够的权限：
- `repo`：完整的仓库访问权限
- `workflow`：GitHub Actions权限
- `write:packages`：包管理权限

### 3. 凭证缓存问题
```bash
# 清除已保存的凭证
git config --global --unset credential.helper

# 重新配置凭证助手
git config --global credential.helper store
```

## 最佳实践建议

### 1. 推荐使用SSH方式
- 更安全，无需在本地存储Token
- 配置一次即可长期使用
- 支持密钥密码保护

### 2. Token使用建议
- 设置合适的过期时间
- 只授予必要的权限
- 定期轮换Token
- 不要在代码中硬编码Token

### 3. 多设备管理
- 每个设备生成独立的SSH密钥
- 使用有意义的密钥描述
- 定期清理不再使用的密钥

## 总结

GitHub取消密码验证是为了提高安全性。推荐使用SSH免密登录方式，它更安全且配置一次即可长期使用。如果必须使用HTTPS方式，则使用Personal Access Token替代密码。

> 参考链接：[https://cloud.tencent.com/developer/article/1861466](https://cloud.tencent.com/developer/article/1861466) 