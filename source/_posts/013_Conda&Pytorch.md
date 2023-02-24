---
title: Conda & Pytorch安装
description: 
date: 2022-11-21
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/21_19_20_29_asynccode.
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/21_19_20_29_asynccode.
categories: 
- Conda
tag: 
- 深度学习
- Conda
- Pytorch

---

# 下载

**官网** **https://www.anaconda.com/products/distribution**

**清华镜像** **https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/**

下载安装包进行默认安装

# 换源

各系统都可以通过修改**用户目录**下的 `.condarc` 文件来使用 TUNA 镜像源。Windows 用户无法直接创建名为 `.condarc` 的文件，可先执行 `conda config --set show_channel_urls yes` 生成该文件之后再修改。

![img](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/21_19_20_26_asynccode.)

修改源为清华源或阿里源都可以，注意修改源之后尽量避免更改源，以防带来问题

**清华源**

```YAML
channels:
    - defaults
show_channel_urls: true
default_channels:
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
    conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

# 创建新环境

```Shell
//环境名 python版本
conda create -n [env_name] python=[3.8]
//安装成功后
conda activate [env_name]
//退出环境
conda deactivate
```

# 安装 Pytorch

Pytorch 官网 https://pytorch.org/get-started/locally/

![img](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/21_19_20_29_asynccode.)

根据自己的 `cuda` 版本选择合适下载，在cmd里输入 `nvidia-smi` 查看自己版本，下载使用 `pip` 下载，因为conda源下载实在是太慢了。

![img](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/21_19_20_31_asynccode.)

将pytorch下载好之后，如果需要使用 `jupyter notebook` 可以使用 `pip`  下载

```Shell
pip install jupyter notebook
```

# 代码执行

安装虚拟环境，无非就是希望使用干净的环境执行代码，如果使用编译工具 IDE 选择好了已经创建的编译环境，那么执行可以正常在虚拟环境里执行。

如果想要通过 `cmd` 命令行对代码文件进行执行，则执行一下步骤

```Shell
//进入虚拟环境
conda activate [env_name]
//打开执行的文件根目录
cd [filePath]
//python 执行文件
python [test.py]
//如果不加python，直接执行[.py]文件，就会使用电脑本身的环境（环境变量里的环境）
```

到此就结束了！