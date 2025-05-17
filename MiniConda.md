# MiniConda

## 安装

```Bash
# 下载 miniconda3 安装脚本
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh

# 执行安装脚本
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh
```

## 使用指南

### 配置清华软件源

```Bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
# 以上两条是Anaconda官方库的镜像

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
# 以上是Anaconda第三方库 Conda Forge的镜像

# for linux
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
# 以上两条是Pytorch的Anaconda第三方镜像

conda config --set show_channel_urls yes
```

### 创建虚拟环境

```Bash
# --name 执行虚拟环境名称
# python=3.X 执行使用的python版本
conda create --name my-env python=3.12
```

### 激活虚拟环境

```Bash
conda activate my-env
```

### 退出虚拟环境

```Bash
conda deactivate my-env
```

### 删除虚拟环境

```Bash
conda env remove --name env-name --all --yes
```