# Android 应用打包指南

本文档详细介绍了如何使用系统安装的Java和Gradle进行Android应用打包，覆盖项目配置。

## 目录
- [环境要求](#环境要求)
- [环境检查](#环境检查)
- [配置修复](#配置修复)
- [许可证接受](#许可证接受)
- [打包步骤](#打包步骤)
- [常见问题](#常见问题)

## 环境要求

### 必需软件
- **Java JDK**: 版本 11 或更高 (推荐 JDK 21)
- **Gradle**: 版本 8.11.1 或兼容版本
- **Android SDK**: 包含 Build-Tools 和 Platform Tools

### 环境变量
确保以下环境变量正确设置：
```bash
export JAVA_HOME=/path/to/your/java
export ANDROID_HOME=/path/to/your/android/sdk
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin
```

## 环境检查

### 1. 检查Java环境
```bash
# 检查Java版本
java -version

# 检查JAVA_HOME
echo $JAVA_HOME

# 检查Java安装路径
ls -la $JAVA_HOME
```

### 2. 检查Gradle环境
```bash
# 检查Gradle版本
gradle --version

# 检查Gradle安装路径
which gradle
```

### 3. 检查Android SDK
```bash
# 检查ANDROID_HOME
echo $ANDROID_HOME

# 检查SDK工具
ls -la $ANDROID_HOME/cmdline-tools/latest/bin/

# 检查SDK组件
ls -la $ANDROID_HOME/
```

## 配置修复

### 设置正确的环境变量
```bash
# 设置Java环境
export JAVA_HOME=/home/nondirectional/.sdkman/candidates/java/current

# 设置Android SDK环境
export ANDROID_HOME=/home/nondirectional/Android

# 验证环境变量
echo "JAVA_HOME: $JAVA_HOME"
echo "ANDROID_HOME: $ANDROID_HOME"
```

## 许可证接受

### 1. 接受Android SDK许可证
在构建之前，必须接受所有Android SDK许可证：

```bash
# 使用 sdkmanager 接受所有 SDK 许可证
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --licenses
```

系统会依次显示多份许可证协议内容，每份协议后会出现如下提示：

```
Accept? (y/N):
```

请每次都输入 `y` 并回车，直到所有许可证都被接受，终端显示 `All SDK package licenses accepted` 即可。

> **注意：**
> - 如果命令卡住或网络慢，请耐心等待。
> - 如遇到权限问题，可尝试加上 `sudo`（不推荐，建议SDK目录归属当前用户）。
> - 许可证未全部接受会导致 Gradle 构建失败，务必全部同意。

### 2. 许可证类型
- Android SDK License
- Android SDK Preview License
- Google GDK License
- MIPS Android System Image License
- 其他相关许可证

## 打包步骤

### 1. 进入项目目录
```bash
cd android_project_name
```

### 2. 清理项目
```bash
gradle clean
```

### 3. 构建Debug版本
```bash
gradle assembleDebug
```

### 4. 构建Release版本
```bash
gradle assembleRelease
```

### 5. 构建所有版本
```bash
gradle assemble
```

## 输出文件位置

### Debug APK
```
app/build/outputs/apk/debug/app-debug.apk
```

### Release APK
```
app/build/outputs/apk/release/app-release.apk
```

### 其他构建产物
- **AAB文件**: `app/build/outputs/bundle/release/app-release.aab`
- **映射文件**: `app/build/outputs/mapping/release/mapping.txt`
- **资源文件**: `app/build/outputs/apk/release/app-release-unsigned.apk`

## 签名配置

### 1. 生成签名密钥
```bash
keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

### 2. 配置签名信息
在`app/build.gradle.kts`中添加签名配置：

```kotlin
android {
    signingConfigs {
        create("release") {
            storeFile = file("my-release-key.keystore")
            storePassword = "your-store-password"
            keyAlias = "my-key-alias"
            keyPassword = "your-key-password"
        }
    }
    
    buildTypes {
        release {
            isMinifyEnabled = true
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
            signingConfig = signingConfigs.getByName("release")
        }
    }
}
```

## 常见问题

### 1. Java路径错误
**错误信息**: `Value 'C:\Program Files\Java\jdk-21' given for org.gradle.java.home Gradle property is invalid`

**解决方案**: 
- 检查并修复`gradle.properties`中的Java路径配置
- 确保使用正确的Linux路径格式

### 2. SDK许可证未接受
**错误信息**: `Failed to install the following Android SDK packages as some licences have not been accepted`

**解决方案**:
```bash
$ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager --licenses
```

### 3. 网络连接问题
**错误信息**: `Downloading from https://services.gradle.org/distributions/gradle-8.11.1-bin.zip failed: timeout`

**解决方案**:
- 使用系统安装的Gradle而不是Gradle Wrapper
- 检查网络连接
- 配置代理设置

### 4. 内存不足
**错误信息**: `Java heap space`

**解决方案**:
在`gradle.properties`中增加内存配置：
```properties
org.gradle.jvmargs=-Xmx4096m -Dfile.encoding=UTF-8
```

### 5. 依赖下载失败
**错误信息**: `Could not resolve dependencies`

**解决方案**:
- 检查网络连接
- 配置Maven镜像源
- 清理Gradle缓存：`gradle clean build --refresh-dependencies`

## 构建优化

### 1. 启用并行构建
在`gradle.properties`中添加：
```properties
org.gradle.parallel=true
```

### 2. 启用构建缓存
```properties
org.gradle.caching=true
```

### 3. 配置守护进程
```properties
org.gradle.daemon=true
```

## 自动化脚本

### 创建构建脚本
创建`build.sh`脚本：

```bash
#!/bin/bash

# 设置环境变量
export JAVA_HOME=/home/nondirectional/.sdkman/candidates/java/current
export ANDROID_HOME=/home/nondirectional/Android

# 进入项目目录
cd k4r-android

# 清理项目
echo "清理项目..."
gradle clean

# 构建Debug版本
echo "构建Debug版本..."
gradle assembleDebug

# 构建Release版本
echo "构建Release版本..."
gradle assembleRelease

echo "构建完成！"
echo "Debug APK: app/build/outputs/apk/debug/app-debug.apk"
echo "Release APK: app/build/outputs/apk/release/app-release.apk"
```

### 使用脚本
```bash
chmod +x build.sh
./build.sh
```

## 总结

通过以上步骤，您可以成功使用系统安装的Java和Gradle进行Android应用打包。关键要点：

1. **环境配置**: 确保Java和Android SDK环境正确配置
2. **路径修复**: 修复项目中的Windows路径配置
3. **许可证接受**: 接受所有必要的Android SDK许可证
4. **构建命令**: 使用`gradle assembleDebug`或`gradle assembleRelease`
5. **问题排查**: 根据常见问题解决方案进行故障排除

如果遇到其他问题，请检查构建日志并参考Android官方文档。 

## 附录

### 脚本`build.sh`

```bash
#!/bin/bash

# Android 应用快速构建脚本
# 使用系统安装的Java和Gradle进行打包

set -e  # 遇到错误时退出

echo "=== Android 应用构建脚本 ==="
echo "时间: $(date)"
echo ""

# 设置环境变量
export JAVA_HOME=/home/nondirectional/.sdkman/candidates/java/current
export ANDROID_HOME=/home/nondirectional/Android

echo "环境变量设置:"
echo "JAVA_HOME: $JAVA_HOME"
echo "ANDROID_HOME: $ANDROID_HOME"
echo ""

# 检查环境
echo "检查环境..."
if ! command -v gradle &> /dev/null; then
    echo "错误: Gradle 未安装或不在 PATH 中"
    exit 1
fi

if ! command -v java &> /dev/null; then
    echo "错误: Java 未安装或不在 PATH 中"
    exit 1
fi

echo "✓ Gradle 版本: $(gradle --version | head -n 1)"
echo "✓ Java 版本: $(java -version 2>&1 | head -n 1)"
echo ""

# 进入项目目录
cd "$(dirname "$0")"
echo "项目目录: $(pwd)"
echo ""

# 清理项目
echo "步骤 1: 清理项目..."
gradle clean
echo "✓ 清理完成"
echo ""

# 构建类型选择
BUILD_TYPE=${1:-debug}

case $BUILD_TYPE in
    debug|Debug|DEBUG)
        echo "步骤 2: 构建 Debug 版本..."
        gradle assembleDebug
        APK_PATH="app/build/outputs/apk/debug/app-debug.apk"
        ;;
    release|Release|RELEASE)
        echo "步骤 2: 构建 Release 版本..."
        gradle assembleRelease
        APK_PATH="app/build/outputs/apk/release/app-release.apk"
        ;;
    *)
        echo "错误: 无效的构建类型 '$BUILD_TYPE'"
        echo "用法: $0 [debug|release]"
        exit 1
        ;;
esac

echo "✓ 构建完成"
echo ""

# 检查APK文件
if [ -f "$APK_PATH" ]; then
    APK_SIZE=$(du -h "$APK_PATH" | cut -f1)
    echo "✓ APK 文件生成成功:"
    echo "  路径: $APK_PATH"
    echo "  大小: $APK_SIZE"
    echo ""
    
    # 显示文件信息
    echo "APK 文件详细信息:"
    ls -lh "$APK_PATH"
    echo ""
    
    echo "=== 构建成功完成 ==="
    echo "APK 文件已生成: $APK_PATH"
else
    echo "错误: APK 文件未生成"
    echo "请检查构建日志以获取更多信息"
    exit 1
fi 
```