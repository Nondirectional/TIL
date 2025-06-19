# Git身份验证与代理配置完整指南

> 参考：[GitHub不再支持密码验证解决方案 - 腾讯云开发者社区](https://cloud.tencent.com/developer/article/1861466)

## 📋 目录

- [🔍 问题背景](#-问题背景)
- [🚀 快速解决方案](#-快速解决方案)
- [🔐 身份验证配置详解](#-身份验证配置详解)
  - [SSH密钥配置（推荐）](#ssh密钥配置推荐)
  - [Personal Access Token配置](#personal-access-token配置)
  - [Git凭据存储配置](#git凭据存储配置)
- [⚙️ IDE配置](#️-ide配置)
- [🌐 代理配置管理](#-代理配置管理)
- [🔧 常见问题解决](#-常见问题解决)
- [📱 多平台配置](#-多平台配置)
- [🛡️ 安全最佳实践](#️-安全最佳实践)

## 🔍 问题背景

### GitHub密码验证政策变更

从2021年8月13日开始，GitHub不再支持密码验证进行Git操作，需要使用Token-based认证方式。当尝试使用密码推送代码时，会出现以下错误：

```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: unable to access 'https://github.com/username/repo.git/': The requested URL returned error: 403
```

### 常见问题原因

当Git每次进行push/pull操作时都要求输入账号密码，通常是由以下原因造成的：

1. **使用HTTPS协议克隆仓库**：HTTPS协议默认不会保存身份验证信息
2. **没有配置凭据存储**：Git没有被配置为记住用户凭据
3. **凭据缓存过期**：之前配置的凭据已过期
4. **没有使用SSH密钥**：SSH密钥可以实现免密码访问
5. **Personal Access Token未配置**：某些Git服务要求使用Token而非密码

## 🚀 快速解决方案

### 方案选择指南

| 方案 | 安全性 | 便利性 | 推荐度 | 适用场景 |
|------|--------|--------|--------|----------|
| SSH密钥 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 长期开发，多设备使用 |
| Personal Access Token | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 临时使用，CI/CD |
| 凭据存储 | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | 简单项目，个人使用 |

### 一键配置脚本

```bash
#!/bin/bash
# Git身份验证一键配置脚本
# 使用方法: chmod +x git_auth_setup.sh && ./git_auth_setup.sh

set -e  # 遇到错误立即退出

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# 日志函数
log_info() {
    echo -e "${BLUE}[INFO]${NC} $1"
}

log_success() {
    echo -e "${GREEN}[SUCCESS]${NC} $1"
}

log_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}

log_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

# 检查依赖
check_dependencies() {
    log_info "检查系统依赖..."
    
    if ! command -v git &> /dev/null; then
        log_error "Git未安装，请先安装Git"
        exit 1
    fi
    
    if ! command -v ssh-keygen &> /dev/null; then
        log_error "SSH工具未安装，请先安装OpenSSH"
        exit 1
    fi
    
    log_success "依赖检查完成"
}

# SSH密钥配置函数
setup_ssh_key() {
    log_info "开始SSH密钥配置..."
    
    # 检查现有SSH密钥
    if [ -f ~/.ssh/id_rsa ] && [ -f ~/.ssh/id_rsa.pub ]; then
        log_warning "发现现有SSH密钥"
        read -p "是否重新生成SSH密钥？(y/N): " regenerate
        if [[ $regenerate =~ ^[Yy]$ ]]; then
            log_info "备份现有密钥..."
            cp ~/.ssh/id_rsa ~/.ssh/id_rsa.backup.$(date +%Y%m%d_%H%M%S)
            cp ~/.ssh/id_rsa.pub ~/.ssh/id_rsa.pub.backup.$(date +%Y%m%d_%H%M%S)
        else
            log_info "使用现有SSH密钥"
            return 0
        fi
    fi
    
    # 获取用户邮箱
    read -p "请输入您的邮箱地址: " email
    if [ -z "$email" ]; then
        log_error "邮箱地址不能为空"
        return 1
    fi
    
    # 生成SSH密钥
    log_info "生成SSH密钥..."
    ssh-keygen -t rsa -b 4096 -C "$email" -f ~/.ssh/id_rsa -N ""
    
    # 启动ssh-agent并添加密钥
    log_info "配置SSH代理..."
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_rsa
    
    # 显示公钥
    log_success "SSH密钥生成完成！"
    echo ""
    log_info "请将以下公钥添加到您的Git服务（GitHub/GitLab等）："
    echo ""
    cat ~/.ssh/id_rsa.pub
    echo ""
    
    # 测试SSH连接
    read -p "是否测试SSH连接？(Y/n): " test_ssh
    if [[ ! $test_ssh =~ ^[Nn]$ ]]; then
        log_info "测试SSH连接..."
        if ssh -T git@github.com 2>&1 | grep -q "successfully authenticated"; then
            log_success "SSH连接测试成功！"
        else
            log_warning "SSH连接测试失败，请检查公钥是否已添加到Git服务"
        fi
    fi
    
    # 配置Git使用SSH
    read -p "是否配置Git使用SSH？(Y/n): " config_ssh
    if [[ ! $config_ssh =~ ^[Nn]$ ]]; then
        log_info "配置Git使用SSH..."
        git config --global url."git@github.com:".insteadOf "https://github.com/"
        git config --global url."git@gitlab.com:".insteadOf "https://gitlab.com/"
        log_success "Git SSH配置完成"
    fi
}

# Personal Access Token配置函数
setup_token() {
    log_info "开始Personal Access Token配置..."
    
    # 获取Token信息
    read -p "请输入您的GitHub用户名: " username
    if [ -z "$username" ]; then
        log_error "用户名不能为空"
        return 1
    fi
    
    read -s -p "请输入您的Personal Access Token: " token
    echo ""
    if [ -z "$token" ]; then
        log_error "Token不能为空"
        return 1
    fi
    
    # 配置Git凭据存储
    log_info "配置Git凭据存储..."
    git config --global credential.helper store
    
    # 创建凭据文件
    echo "https://$username:$token@github.com" > ~/.git-credentials
    chmod 600 ~/.git-credentials
    
    log_success "Token配置完成！"
    log_info "Token已保存到 ~/.git-credentials"
    
    # 测试Token
    read -p "是否测试Token？(Y/n): " test_token
    if [[ ! $test_token =~ ^[Nn]$ ]]; then
        log_info "测试Token..."
        if curl -s -H "Authorization: token $token" https://api.github.com/user | grep -q "login"; then
            log_success "Token验证成功！"
        else
            log_error "Token验证失败，请检查Token是否正确"
        fi
    fi
}

# 凭据存储配置函数
setup_credential_helper() {
    log_info "开始凭据存储配置..."
    
    # 检测操作系统
    if [[ "$OSTYPE" == "linux-gnu"* ]]; then
        log_info "检测到Linux系统"
        git config --global credential.helper store
        log_success "已配置为永久存储凭据"
    elif [[ "$OSTYPE" == "darwin"* ]]; then
        log_info "检测到macOS系统"
        git config --global credential.helper osxkeychain
        log_success "已配置为使用钥匙串存储"
    elif [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "cygwin" ]]; then
        log_info "检测到Windows系统"
        git config --global credential.helper manager-core
        log_success "已配置为使用Windows凭据管理器"
    else
        log_warning "未知操作系统，使用默认存储方式"
        git config --global credential.helper store
    fi
    
    # 配置缓存时间
    read -p "是否配置凭据缓存时间？(Y/n): " config_cache
    if [[ ! $config_cache =~ ^[Nn]$ ]]; then
        read -p "请输入缓存时间（秒，默认3600）: " cache_time
        cache_time=${cache_time:-3600}
        git config --global credential.helper "cache --timeout=$cache_time"
        log_success "凭据缓存时间设置为 ${cache_time} 秒"
    fi
}

# 代理配置函数
setup_proxy() {
    log_info "开始代理配置..."
    
    read -p "是否需要配置代理？(y/N): " need_proxy
    if [[ $need_proxy =~ ^[Yy]$ ]]; then
        read -p "请输入代理服务器地址（如：http://proxy.example.com:8080）: " proxy_url
        if [ -n "$proxy_url" ]; then
            git config --global http.proxy "$proxy_url"
            git config --global https.proxy "$proxy_url"
            log_success "代理配置完成"
        fi
    else
        # 清除代理配置
        git config --global --unset http.proxy 2>/dev/null || true
        git config --global --unset https.proxy 2>/dev/null || true
        log_info "已清除代理配置"
    fi
}

# 基础Git配置
setup_basic_git() {
    log_info "配置基础Git设置..."
    
    # 获取用户信息
    read -p "请输入您的姓名: " name
    if [ -n "$name" ]; then
        git config --global user.name "$name"
    fi
    
    read -p "请输入您的邮箱: " email
    if [ -n "$email" ]; then
        git config --global user.email "$email"
    fi
    
    # 配置默认编辑器
    read -p "请选择默认编辑器 (vim/nano/vscode，默认vim): " editor
    editor=${editor:-vim}
    git config --global core.editor "$editor"
    
    # 配置默认分支名
    git config --global init.defaultBranch main
    
    log_success "基础Git配置完成"
}

# 显示配置信息
show_config() {
    log_info "当前Git配置："
    echo ""
    echo "用户信息："
    git config --global user.name
    git config --global user.email
    echo ""
    echo "凭据配置："
    git config --global credential.helper
    echo ""
    echo "代理配置："
    git config --global --get http.proxy || echo "未配置"
    echo ""
    if [ -f ~/.ssh/id_rsa.pub ]; then
        echo "SSH公钥："
        cat ~/.ssh/id_rsa.pub
        echo ""
    fi
}

# 主函数
main() {
    echo "🔧 Git身份验证配置工具"
    echo "================================"
    
    # 检查依赖
    check_dependencies
    
    # 基础Git配置
    setup_basic_git
    
    # 选择配置方式
    echo ""
    echo "请选择配置方式："
    echo "1. SSH密钥配置（推荐）"
    echo "2. Personal Access Token配置"
    echo "3. 凭据存储配置"
    echo "4. 完整配置（SSH + 凭据存储）"
    echo "5. 仅配置代理"
    echo "6. 显示当前配置"
    echo "7. 退出"
    
    read -p "请输入选择 (1-7): " choice
    
    case $choice in
        1)
            setup_ssh_key
            ;;
        2)
            setup_token
            ;;
        3)
            setup_credential_helper
            ;;
        4)
            setup_ssh_key
            setup_credential_helper
            ;;
        5)
            setup_proxy
            ;;
        6)
            show_config
            ;;
        7)
            log_info "退出配置工具"
            exit 0
            ;;
        *)
            log_error "无效选择"
            exit 1
            ;;
    esac
    
    # 配置代理
    setup_proxy
    
    # 显示最终配置
    echo ""
    log_success "配置完成！"
    show_config
    
    echo ""
    log_info "配置说明："
    echo "- SSH密钥：已生成并添加到SSH代理"
    echo "- 凭据存储：已配置为自动保存凭据"
    echo "- 代理设置：已根据您的选择配置"
    echo ""
    log_info "如果遇到问题，请检查："
    echo "1. SSH公钥是否已添加到Git服务"
    echo "2. Token是否具有足够权限"
    echo "3. 网络连接是否正常"
}

# 运行主函数
main "$@"
```


## 🔐 身份验证配置详解

### SSH密钥配置（推荐）

#### 步骤1：检查现有SSH密钥

```bash
ls -al ~/.ssh
```

**预期输出**：
- 如果已配置：`id_rsa` (私钥) 和 `id_rsa.pub` (公钥)
- 如果未配置：目录为空或不存在

#### 步骤2：生成新的SSH密钥

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

> **参数说明**：
> - `-t rsa`：指定密钥类型为RSA
> - `-b 4096`：指定密钥长度为4096位
> - `-C "email"`：添加注释，通常是邮箱地址

**交互提示处理**：
1. 保存路径：直接回车使用默认路径 `~/.ssh/id_rsa`
2. 私钥密码：直接回车（无密码）或设置密码保护

#### 步骤3：配置SSH代理

```bash
# 启动ssh-agent
eval "$(ssh-agent -s)"

# 添加私钥到ssh-agent
ssh-add ~/.ssh/id_rsa

# 验证密钥已添加
ssh-add -l
```

#### 步骤4：添加SSH密钥到Git服务

##### 复制公钥内容
```bash
cat ~/.ssh/id_rsa.pub
```

##### GitHub配置步骤
1. 登录GitHub → 点击头像 → Settings
2. 左侧菜单 → "SSH and GPG keys"
3. 点击 "New SSH key"
4. 填写标题（如：My MacBook）
5. 粘贴公钥内容到Key字段
6. 点击 "Add SSH key"

##### GitLab配置步骤
1. 登录GitLab → 点击头像 → Preferences
2. 左侧菜单 → "SSH Keys"
3. 粘贴公钥内容
4. 点击 "Add key"

#### 步骤5：测试SSH连接

```bash
ssh -T git@github.com  # GitHub
ssh -T git@gitlab.com  # GitLab
```

**预期输出**：
```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

#### 步骤6：修改Git仓库地址

将HTTPS地址改为SSH地址：

```bash
# 查看当前远程地址
git remote -v

# 修改为SSH地址
git remote set-url origin git@github.com:username/repository.git

# 验证修改
git remote -v
```

### Personal Access Token配置

#### 步骤1：生成Personal Access Token

1. **GitHub操作路径**：
   - 登录GitHub → 头像 → Settings
   - Developer settings → Personal access tokens → Tokens (classic)
   - Generate new token → Generate new token (classic)

2. **Token配置选项**：
   - **Note**：Token描述（如：Git CLI Access）
   - **Expiration**：过期时间（建议90天）
   - **Scopes**：权限范围
     - `repo`：完整的仓库访问权限
     - `workflow`：GitHub Actions权限
     - `write:packages`：包管理权限

3. **重要提醒**：复制生成的Token（只显示一次）

#### 步骤2：使用Token进行Git操作

```bash
# 方法1：交互式输入
git clone https://github.com/username/repository.git
# 用户名：GitHub用户名
# 密码：刚才生成的Token

# 方法2：URL中包含Token
git clone https://username:token@github.com/username/repository.git

# 方法3：配置凭据存储
git config --global credential.helper store
# 然后正常操作，Git会自动保存Token
```

### Git凭据存储配置

#### 不同平台的凭据存储方式

```bash
# Linux (推荐)
git config --global credential.helper store

# Windows
git config --global credential.helper manager-core

# macOS
git config --global credential.helper osxkeychain

# 临时缓存（默认15分钟）
git config --global credential.helper cache

# 自定义缓存时间（1小时）
git config --global credential.helper 'cache --timeout=3600'
```

#### 凭据管理命令

```bash
# 查看当前凭据配置
git config --global --get credential.helper

# 查看已保存的凭据文件
ls -la ~/.git-credentials

# 清除已保存的凭据
rm ~/.git-credentials

# 清除缓存的凭据
git credential-cache exit
```

## ⚙️ IDE配置

### IntelliJ IDEA配置

#### Git路径配置
1. File → Settings → Version Control → Git
2. Path to Git executable：填入Git路径
3. 点击 "Test" 验证

#### GitHub账户配置

##### Token方式
1. File → Settings → Version Control → GitHub
2. 点击 "+" 添加账户
3. 选择 "Log in with Token"
4. 填入Token

##### SSH方式
1. 确保已配置SSH密钥
2. 在GitHub设置中选择 "Use SSH"
3. 使用SSH格式的仓库地址

### VS Code配置

```json
// settings.json
{
    "git.path": "/usr/bin/git",
    "git.autofetch": true,
    "git.confirmSync": false
}
```

## 🌐 代理配置管理

### 查看当前代理配置

```bash
# 查看全局代理配置
git config --global --get http.proxy
git config --global --get https.proxy

# 查看当前仓库的代理配置
git config --get http.proxy
git config --get https.proxy

# 查看所有代理相关配置
git config --list | grep proxy
```

### 重置代理配置

#### 一键重置脚本

```bash
#!/bin/bash
echo "🔄 正在重置Git代理配置..."

# 重置全局配置
git config --global --unset http.proxy 2>/dev/null || true
git config --global --unset https.proxy 2>/dev/null || true

# 重置当前仓库配置
git config --unset http.proxy 2>/dev/null || true
git config --unset https.proxy 2>/dev/null || true

# 重置特定服务配置
git config --global --unset http.https://github.com.proxy 2>/dev/null || true
git config --global --unset https.https://github.com.proxy 2>/dev/null || true
git config --global --unset http.https://gitlab.com.proxy 2>/dev/null || true
git config --global --unset https.https://gitlab.com.proxy 2>/dev/null || true

echo "✅ Git代理配置重置完成！"
echo "📋 当前代理相关配置："
git config --list | grep proxy || echo "没有找到代理配置"
```

#### 手动重置命令

```bash
# 重置全局代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 重置当前仓库代理
git config --unset http.proxy
git config --unset https.proxy

# 重置特定URL代理
git config --global --unset http.https://github.com.proxy
git config --global --unset https.https://github.com.proxy
```

### 重新配置代理

```bash
# 设置全局代理
git config --global http.proxy http://proxy-server:port
git config --global https.proxy http://proxy-server:port

# 设置特定URL代理
git config --global http.https://github.com.proxy http://proxy-server:port
```

## 🔧 常见问题解决

### 问题诊断流程

```bash
# 1. 检查Git配置
git config --list | grep -E "(credential|proxy|remote)"

# 2. 检查SSH连接
ssh -T git@github.com

# 3. 检查远程URL格式
git remote -v

# 4. 检查SSH密钥
ssh-add -l
```

### 常见问题及解决方案

#### 问题1：SSH连接失败

**症状**：`ssh -T git@github.com` 失败

**解决方案**：
```bash
# 检查SSH代理
ssh-add -l

# 添加SSH密钥到代理
ssh-add ~/.ssh/id_rsa

# 测试连接
ssh -T git@github.com
```

#### 问题2：Token权限不足

**症状**：推送时提示权限错误

**解决方案**：
确保Token具有足够权限：
- `repo`：完整的仓库访问权限
- `workflow`：GitHub Actions权限
- `write:packages`：包管理权限

#### 问题3：凭证缓存问题

**症状**：配置凭据存储后仍要求输入密码

**解决方案**：
```bash
# 清除已保存的凭证
git config --global --unset credential.helper
rm ~/.git-credentials

# 重新配置
git config --global credential.helper store
```

#### 问题4：URL格式问题

**症状**：SSH密钥配置后仍要求密码

**解决方案**：
```bash
# 检查远程URL格式
git remote -v

# 修改为SSH格式
git remote set-url origin git@github.com:username/repository.git
```

## 📱 多平台配置

### Linux (WSL2/Ubuntu)

```bash
# 推荐配置
git config --global credential.helper store
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"

# SSH密钥配置
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

### Windows

```bash
# 使用Windows凭据管理器
git config --global credential.helper manager-core

# 或者使用Git Credential Manager
git config --global credential.helper manager
```

### macOS

```bash
# 使用钥匙串存储
git config --global credential.helper osxkeychain

# SSH密钥配置
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-add -K ~/.ssh/id_rsa  # macOS 10.12.2之前
ssh-add ~/.ssh/id_rsa     # macOS 10.12.2之后
```

## 🛡️ 安全最佳实践

### 安全配置检查清单

- [ ] 使用SSH密钥而非密码
- [ ] 设置SSH密钥密码保护
- [ ] 定期轮换Personal Access Token
- [ ] 为Token设置合适的过期时间
- [ ] 只授予Token必要的权限
- [ ] 不在代码中硬编码Token
- [ ] 定期清理不再使用的密钥
- [ ] 使用HTTPS而非HTTP

### 安全建议

#### 1. SSH密钥安全
- 使用4096位RSA密钥
- 设置密钥密码保护
- 定期轮换密钥
- 每个设备使用独立密钥

#### 2. Token安全
- 设置合适的过期时间（建议90天）
- 只授予必要的权限
- 定期轮换Token
- 不要在代码中硬编码

#### 3. 凭据存储安全
- 避免使用明文存储
- 使用系统级凭据管理器
- 定期清理过期凭据

### 配置验证

```bash
# 验证SSH配置
ssh -T git@github.com

# 验证Git配置
git config --list --show-origin

# 验证凭据存储
git config --global --get credential.helper

# 验证代理配置
git config --list | grep proxy
```

## 📋 配置管理

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

### 备份和恢复配置

```bash
# 备份全局配置
cp ~/.gitconfig ~/.gitconfig.backup

# 恢复配置
cp ~/.gitconfig.backup ~/.gitconfig

# 备份SSH密钥（可选）
cp -r ~/.ssh ~/.ssh.backup
```

## 📚 总结

### 推荐配置流程

1. **首选SSH密钥配置**：最安全，配置一次长期使用
2. **备选Personal Access Token**：适用于CI/CD或临时使用
3. **配置凭据存储**：提高使用便利性
4. **定期安全维护**：轮换密钥和Token

### 关键要点

- GitHub从2021年8月13日起不再支持密码验证
- SSH密钥是最安全可靠的身份验证方式
- Personal Access Token是HTTPS方式的推荐选择
- 合理配置代理可以提高访问效率
- 定期维护和更新配置确保安全性

通过合理配置Git的身份验证和代理设置，可以大大提高开发效率，同时确保安全性。根据不同的使用场景和平台，选择最适合的配置方案。

> 参考链接：[https://cloud.tencent.com/developer/article/1861466](https://cloud.tencent.com/developer/article/1861466) 