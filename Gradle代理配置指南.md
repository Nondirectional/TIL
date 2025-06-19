# Gradle代理配置完整指南

## 📋 目录

- [🔍 概述](#-概述)
- [🌐 全局代理配置](#-全局代理配置)
- [📁 项目代理配置](#-项目代理配置)
- [🔧 不同场景配置](#-不同场景配置)
- [🚀 快速配置脚本](#-快速配置脚本)
- [❓ 常见问题解决](#-常见问题解决)
- [📚 最佳实践](#-最佳实践)

## 🔍 概述

Gradle作为构建工具，在下载依赖、插件或与远程仓库通信时可能需要通过代理服务器。本文详细介绍如何为Gradle配置HTTP/HTTPS代理。

### 代理配置场景

- **企业网络环境**：公司内网需要通过代理访问外网
- **网络限制**：某些地区网络访问限制
- **安全策略**：企业安全策略要求通过代理访问
- **开发环境**：本地开发环境需要代理

## 🌐 全局代理配置

### 方法1：gradle.properties文件配置

在用户主目录下创建或编辑 `~/.gradle/gradle.properties` 文件：

```properties
# HTTP代理配置
systemProp.http.proxyHost=proxy.example.com
systemProp.http.proxyPort=8080
systemProp.http.proxyUser=username
systemProp.http.proxyPassword=password

# HTTPS代理配置
systemProp.https.proxyHost=proxy.example.com
systemProp.https.proxyPort=8080
systemProp.https.proxyUser=username
systemProp.https.proxyPassword=password

# 非代理主机配置（可选）
systemProp.http.nonProxyHosts=localhost|127.0.0.1|*.local|*.example.com
systemProp.https.nonProxyHosts=localhost|127.0.0.1|*.local|*.example.com
```

### 方法2：环境变量配置

#### Linux/macOS
```bash
# 设置环境变量
export GRADLE_OPTS="-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080 -Dhttps.proxyHost=proxy.example.com -Dhttps.proxyPort=8080"

# 添加到 ~/.bashrc 或 ~/.zshrc 使其永久生效
echo 'export GRADLE_OPTS="-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080 -Dhttps.proxyHost=proxy.example.com -Dhttps.proxyPort=8080"' >> ~/.bashrc
source ~/.bashrc
```

#### Windows
```cmd
# CMD
set GRADLE_OPTS=-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080 -Dhttps.proxyHost=proxy.example.com -Dhttps.proxyPort=8080

# PowerShell
$env:GRADLE_OPTS="-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080 -Dhttps.proxyHost=proxy.example.com -Dhttps.proxyPort=8080"

# 永久设置（系统环境变量）
setx GRADLE_OPTS "-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080 -Dhttps.proxyHost=proxy.example.com -Dhttps.proxyPort=8080"
```

### 方法3：JVM系统属性

在gradle.properties中设置JVM系统属性：

```properties
# 使用JVM系统属性
org.gradle.jvmargs=-Dhttp.proxyHost=proxy.example.com -Dhttp.proxyPort=8080 -Dhttps.proxyHost=proxy.example.com -Dhttps.proxyPort=8080
```

## 📁 项目代理配置

### 方法1：项目级gradle.properties

在项目根目录的 `gradle.properties` 文件中添加：

```properties
# 项目专用代理配置
systemProp.http.proxyHost=proxy.example.com
systemProp.http.proxyPort=8080
systemProp.https.proxyHost=proxy.example.com
systemProp.https.proxyPort=8080

# 如果需要认证
systemProp.http.proxyUser=username
systemProp.http.proxyPassword=password
systemProp.https.proxyUser=username
systemProp.https.proxyPassword=password
```

### 方法2：build.gradle脚本配置

在 `build.gradle` 文件中动态配置代理：

```groovy
// 在build.gradle中配置代理
allprojects {
    // 配置系统属性
    System.setProperty('http.proxyHost', 'proxy.example.com')
    System.setProperty('http.proxyPort', '8080')
    System.setProperty('https.proxyHost', 'proxy.example.com')
    System.setProperty('https.proxyPort', '8080')
    
    // 如果需要认证
    System.setProperty('http.proxyUser', 'username')
    System.setProperty('http.proxyPassword', 'password')
    System.setProperty('https.proxyUser', 'username')
    System.setProperty('https.proxyPassword', 'password')
}

// 或者使用repositories配置
repositories {
    mavenCentral {
        url 'https://repo1.maven.org/maven2/'
        // 为特定仓库配置代理
        content {
            includeGroupByRegex '.*'
        }
    }
}
```

### 方法3：settings.gradle配置

在 `settings.gradle` 文件中配置：

```groovy
// 在settings.gradle中配置代理
gradle.projectsLoaded {
    rootProject.allprojects {
        // 设置代理系统属性
        System.setProperty('http.proxyHost', 'proxy.example.com')
        System.setProperty('http.proxyPort', '8080')
        System.setProperty('https.proxyHost', 'proxy.example.com')
        System.setProperty('https.proxyPort', '8080')
    }
}
```

## 🔧 不同场景配置

### 场景1：企业内网代理

```properties
# ~/.gradle/gradle.properties
# 企业内网代理配置
systemProp.http.proxyHost=proxy.company.com
systemProp.http.proxyPort=3128
systemProp.https.proxyHost=proxy.company.com
systemProp.https.proxyPort=3128

# 企业内网认证
systemProp.http.proxyUser=your_username
systemProp.http.proxyPassword=your_password
systemProp.https.proxyUser=your_username
systemProp.https.proxyPassword=your_password

# 内网地址不需要代理
systemProp.http.nonProxyHosts=localhost|127.0.0.1|*.company.com|*.local
systemProp.https.nonProxyHosts=localhost|127.0.0.1|*.company.com|*.local
```

### 场景2：SOCKS代理

```properties
# SOCKS代理配置
systemProp.socks.proxyHost=proxy.example.com
systemProp.socks.proxyPort=1080
systemProp.socks.proxyVersion=5

# 或者使用JVM参数
org.gradle.jvmargs=-Dsocks.proxyHost=proxy.example.com -Dsocks.proxyPort=1080 -Dsocks.proxyVersion=5
```

### 场景3：本地开发代理

```properties
# 本地开发环境代理（如Clash、V2Ray等）
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=7890
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=7890

# 本地地址不需要代理
systemProp.http.nonProxyHosts=localhost|127.0.0.1|*.local
systemProp.https.nonProxyHosts=localhost|127.0.0.1|*.local
```

### 场景4：WSL2环境

```properties
# WSL2环境下的代理配置
systemProp.http.proxyHost=172.18.160.1
systemProp.http.proxyPort=7890
systemProp.https.proxyHost=172.18.160.1
systemProp.https.proxyPort=7890

# 获取WSL2宿主机IP的命令
# ip route | grep default | awk '{print $3}'
```

## 🚀 快速配置脚本

### 一键配置脚本

```bash
#!/bin/bash
# Gradle代理配置脚本

set -e

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m'

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

# 创建gradle.properties文件
create_gradle_properties() {
    local gradle_dir="$HOME/.gradle"
    local properties_file="$gradle_dir/gradle.properties"
    
    # 创建目录
    mkdir -p "$gradle_dir"
    
    log_info "创建Gradle配置文件: $properties_file"
    
    # 获取代理信息
    read -p "请输入代理服务器地址: " proxy_host
    read -p "请输入代理服务器端口: " proxy_port
    read -p "是否需要认证？(y/N): " need_auth
    
    # 构建配置内容
    local config=""
    config+="# Gradle代理配置\n"
    config+="# 生成时间: $(date)\n\n"
    config+="systemProp.http.proxyHost=$proxy_host\n"
    config+="systemProp.http.proxyPort=$proxy_port\n"
    config+="systemProp.https.proxyHost=$proxy_host\n"
    config+="systemProp.https.proxyPort=$proxy_port\n\n"
    
    if [[ $need_auth =~ ^[Yy]$ ]]; then
        read -p "请输入代理用户名: " proxy_user
        read -s -p "请输入代理密码: " proxy_pass
        echo ""
        config+="systemProp.http.proxyUser=$proxy_user\n"
        config+="systemProp.http.proxyPassword=$proxy_pass\n"
        config+="systemProp.https.proxyUser=$proxy_user\n"
        config+="systemProp.https.proxyPassword=$proxy_pass\n\n"
    fi
    
    # 添加非代理主机
    config+="# 非代理主机配置\n"
    config+="systemProp.http.nonProxyHosts=localhost|127.0.0.1|*.local\n"
    config+="systemProp.https.nonProxyHosts=localhost|127.0.0.1|*.local\n"
    
    # 写入文件
    echo -e "$config" > "$properties_file"
    
    log_success "Gradle代理配置已保存到: $properties_file"
}

# 清除代理配置
clear_proxy_config() {
    local gradle_dir="$HOME/.gradle"
    local properties_file="$gradle_dir/gradle.properties"
    
    if [ -f "$properties_file" ]; then
        log_info "备份现有配置..."
        cp "$properties_file" "$properties_file.backup.$(date +%Y%m%d_%H%M%S)"
        
        # 移除代理相关配置
        sed -i '/^systemProp\.\(http\|https\)\.proxy/d' "$properties_file"
        sed -i '/^systemProp\.socks\.proxy/d' "$properties_file"
        sed -i '/^# Gradle代理配置/d' "$properties_file"
        sed -i '/^# 生成时间:/d' "$properties_file"
        sed -i '/^# 非代理主机配置/d' "$properties_file"
        
        log_success "代理配置已清除"
    else
        log_warning "未找到gradle.properties文件"
    fi
}

# 显示当前配置
show_config() {
    local gradle_dir="$HOME/.gradle"
    local properties_file="$gradle_dir/gradle.properties"
    
    if [ -f "$properties_file" ]; then
        log_info "当前Gradle配置:"
        echo ""
        grep -E "(proxy|Proxy)" "$properties_file" || echo "未找到代理配置"
        echo ""
    else
        log_warning "未找到gradle.properties文件"
    fi
}

# 测试代理连接
test_proxy() {
    log_info "测试代理连接..."
    
    # 检查gradle是否可用
    if command -v gradle &> /dev/null; then
        log_info "运行Gradle测试..."
        gradle --version
        log_success "Gradle代理测试完成"
    else
        log_warning "Gradle未安装或不在PATH中"
    fi
}

# 主函数
main() {
    echo "🔧 Gradle代理配置工具"
    echo "========================"
    
    echo ""
    echo "请选择操作："
    echo "1. 配置代理"
    echo "2. 清除代理配置"
    echo "3. 显示当前配置"
    echo "4. 测试代理连接"
    echo "5. 退出"
    
    read -p "请输入选择 (1-5): " choice
    
    case $choice in
        1)
            create_gradle_properties
            ;;
        2)
            clear_proxy_config
            ;;
        3)
            show_config
            ;;
        4)
            test_proxy
            ;;
        5)
            log_info "退出配置工具"
            exit 0
            ;;
        *)
            log_error "无效选择"
            exit 1
            ;;
    esac
}

# 运行主函数
main "$@"
```

### 使用脚本

```bash
# 保存脚本为 gradle_proxy_setup.sh
chmod +x gradle_proxy_setup.sh
./gradle_proxy_setup.sh
```

## ❓ 常见问题解决

### 问题1：代理配置不生效

**症状**：配置了代理但Gradle仍然无法下载依赖

**解决方案**：
```bash
# 1. 检查配置文件位置
ls -la ~/.gradle/gradle.properties

# 2. 检查配置内容
cat ~/.gradle/gradle.properties

# 3. 清除Gradle缓存
rm -rf ~/.gradle/caches/

# 4. 重新运行构建
gradle clean build --refresh-dependencies
```

### 问题2：认证失败

**症状**：代理需要认证但配置后仍然失败

**解决方案**：
```properties
# 确保在gradle.properties中正确配置认证信息
systemProp.http.proxyUser=your_username
systemProp.http.proxyPassword=your_password
systemProp.https.proxyUser=your_username
systemProp.https.proxyPassword=your_password

# 注意：密码中的特殊字符需要转义
systemProp.http.proxyPassword=your\@password
```

### 问题3：特定仓库无法访问

**症状**：某些Maven仓库无法通过代理访问

**解决方案**：
```groovy
// 在build.gradle中为特定仓库配置
repositories {
    maven {
        url 'https://repo.example.com/maven2/'
        // 为特定仓库禁用代理
        content {
            includeGroupByRegex '.*'
        }
    }
}
```

### 问题4：WSL2环境代理问题

**症状**：在WSL2中Gradle无法通过宿主机代理

**解决方案**：
```bash
# 1. 获取宿主机IP
HOST_IP=$(ip route | grep default | awk '{print $3}')

# 2. 配置gradle.properties
cat > ~/.gradle/gradle.properties << EOF
systemProp.http.proxyHost=$HOST_IP
systemProp.http.proxyPort=7890
systemProp.https.proxyHost=$HOST_IP
systemProp.https.proxyPort=7890
EOF
```

### 问题5：SSL证书问题

**症状**：通过代理访问时出现SSL证书错误

**解决方案**：
```properties
# 在gradle.properties中添加SSL配置
systemProp.javax.net.ssl.trustStore=/path/to/cacerts
systemProp.javax.net.ssl.trustStorePassword=changeit

# 或者禁用SSL验证（不推荐用于生产环境）
systemProp.javax.net.ssl.trustStoreType=JKS
systemProp.javax.net.ssl.trustStore=/dev/null
```

## 📚 最佳实践

### 1. 配置文件管理

```bash
# 创建配置模板
mkdir -p ~/.gradle/templates
cp ~/.gradle/gradle.properties ~/.gradle/templates/gradle.properties.template

# 备份重要配置
cp ~/.gradle/gradle.properties ~/.gradle/gradle.properties.backup
```

### 2. 环境隔离

```properties
# 为不同环境创建不同的配置文件
# ~/.gradle/gradle.properties.dev
systemProp.http.proxyHost=dev-proxy.company.com
systemProp.http.proxyPort=8080

# ~/.gradle/gradle.properties.prod
systemProp.http.proxyHost=prod-proxy.company.com
systemProp.http.proxyPort=3128
```

### 3. 安全考虑

```properties
# 不要在配置文件中硬编码密码
# 使用环境变量
systemProp.http.proxyUser=${PROXY_USER}
systemProp.http.proxyPassword=${PROXY_PASSWORD}

# 设置文件权限
chmod 600 ~/.gradle/gradle.properties
```

### 4. 调试技巧

```bash
# 启用Gradle调试模式
gradle build --debug

# 查看详细的网络请求
gradle build --info

# 检查依赖解析
gradle dependencies
```

### 5. 性能优化

```properties
# 配置非代理主机以提高性能
systemProp.http.nonProxyHosts=localhost|127.0.0.1|*.local|*.company.com
systemProp.https.nonProxyHosts=localhost|127.0.0.1|*.local|*.company.com

# 配置Gradle守护进程
org.gradle.daemon=true
org.gradle.parallel=true
```

## 📋 配置验证

### 验证代理配置

```bash
# 1. 检查配置文件
cat ~/.gradle/gradle.properties | grep proxy

# 2. 测试网络连接
curl -x http://proxy.example.com:8080 https://repo1.maven.org/maven2/

# 3. 运行Gradle测试
gradle --version

# 4. 检查依赖下载
gradle dependencies --refresh-dependencies
```

### 配置检查清单

- [ ] 代理服务器地址和端口正确
- [ ] 认证信息（如果需要）正确
- [ ] 非代理主机配置合理
- [ ] 配置文件权限设置正确
- [ ] 环境变量（如果使用）设置正确
- [ ] SSL证书配置（如果需要）正确

通过合理配置Gradle代理，可以解决网络访问问题，提高构建效率，确保项目依赖能够正常下载。 