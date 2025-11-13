# Ubuntu 安装 Fcitx5 + Rime (中州韵) + Rime-ice

## 1. 删除原有输入法框架
```bash
sudo apt remove ibus ibus-pinyin ibus-libpinyin -y
```

## 2. 安装 Fcitx5 和 Rime
```bash
sudo apt update
sudo apt install fcitx5 fcitx5-rime fcitx5-config-qt \
  fcitx5-frontend-gtk2 fcitx5-frontend-gtk3 \
  fcitx5-frontend-gtk4 fcitx5-frontend-qt5 -y
```

## 3. 设置 Fcitx5 为默认输入法框架
```bash
im-config -n fcitx5
sudo apt install fcitx5-configtool
```

## 4. 重启系统
```bash
sudo reboot
```

## 5. 配置 Rime 输入法
```bash
fcitx5-configtool
```
在配置界面中：
1. 点击"添加输入法"
2. 选择并添加"Rime"
3. 重启 Fcitx5 使配置生效：
```bash
pkill -9 fcitx5 && fcitx5 &
```

## 6. 安装 Rime-ice 配置方案
> 待补充具体安装步骤