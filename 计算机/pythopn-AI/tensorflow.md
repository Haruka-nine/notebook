
```ad-warning
tensorflow版本在2.0以后不区分cpu和gpu版本
tensorflow在10.0后已经不支持在window系统安装GPU支持
windows系统能使用的最高GPU版本为tensorflow==2.10.0
```

# 安装

## conda一体安装

```ad-note
建议在conda环境下使用，安装有点奇怪，在本机环境下安装后，我们在pycharm中需要使用管理员模式启动才可以使用
```

首先 tensorflow2.6以上的版本对应的cuda=11.2 cudnn=8.1.0

windows建议使用conda虚拟环境

可以在本机安装和虚拟环境分别安装

本机安装后虚拟环境都可以使用，但可能不同环境使用

此处需要强调的是,tensorflow的安装不要用conda install

cudatoolkit和cudnn可以用conda install安装

**如果这里本机安装了cuda和cudnn可以直接安装tensorflow**

`conda install -c conda-forge cudatoolkit=11.2 cudnn=8.1.0`

```ad-attention
title:cuda问题

关于cuda是否需要本机安装

1.其实可以不在本机安装，使用上方的命令，在虚拟环境中也会给我们安装好cuda,我们直接就可以使用

2.但如果需要编译、部署，那我们还是需要本机安装cuda的。

3.**建议本机和虚拟环境的cuda版本一致，防止出现问题**

```

`pip install "tensorflow<2.11"`

没有问题的话建议使用2.6.0

## 本机存在cuda


`pip install tensorflow==2.10.0`

