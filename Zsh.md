# zsh

安装zsh

```bash
$ apt install zsh 
```

设置默认shell为zsh

```bash
$ chsh -s $(which zsh)
```

安装 oh my zsh

```bash
sh -c "$(wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

插件安装与配置

### 自动补全插件 (zsh-autosuggestions)

```bash
$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### 语法高亮插件 (zsh-syntax-highlighting)

```bash
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### z 插件 (目录快速跳转)

"z" 插件通常随 Oh My Zsh 自带，一般无需额外安装。如果未启用，可以尝试直接添加到插件列表。

### 编辑 `~/.zshrc` 文件

打开 `~/.zshrc` 文件，找到 `plugins=` 开头的那一行，修改为如下内容，将 `zsh-autosuggestions`、`zsh-syntax-highlighting` 和 `z` 添加到插件列表中。如果已有其他插件，请保留它们。

```zsh
plugins=(
    # other existing plugins...
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
    z
)
```

### 设置 `ll` 命令别名

在 `~/.zshrc` 文件末尾添加以下内容：

```zsh
alias ll='ls -lA'
```

### 使配置生效

修改 `~/.zshrc` 文件后，你需要重新加载配置文件或重启终端才能使更改生效。

重新加载配置文件：
```bash
$ source ~/.zshrc
```

或者，直接关闭当前终端窗口并重新打开一个新的终端窗口。

## 插件功能说明

### zsh-autosuggestions（自动补全插件）
- **功能**: 根据您的历史命令自动建议补全
- **使用**: 开始输入命令时，会显示灰色的建议文本
- **快捷键**: 按 `→` 键或 `End` 键接受建议

### zsh-syntax-highlighting（语法高亮插件）  
- **功能**: 在您输入命令时提供语法高亮
- **效果**: 
  - 有效命令显示为绿色
  - 无效命令显示为红色
  - 字符串、路径等有不同颜色显示
