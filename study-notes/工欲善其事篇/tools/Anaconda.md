# Anaconda



```shell
conda create -n env_name python==3.7.0 // 创建虚拟环境

activate  // 激活虚拟环境

conda activate env_name  // 切换虚拟环境


# 可能会出现环境没有彻底删除，建议直接到 ~/anaconda3/env 目录手动删除
conda env remove --name env-name  // 删除虚拟环境

# 清理anaconda缓存：
conda clean -p      # 删除没有用的包 --packages
conda clean -t      # 删除tar打包 --tarballs
conda clean -y -a # 删除所有的安装包及cache(索引缓存、锁定文件、未使用过的包和tar包)

# 重命名环境（conda默认不支持环境重命名，用另一种方式实现）
conda create --name conda-new --clone conda-old  # 从旧环境创建新环境
conda remove --name conda-old --all


conda info -e  // 查看虚拟环境中安装哪些环境

conda list：查看安装了哪些包。
conda install package_name(包名)：安装包
conda env list 或 conda info -e：查看当前存在哪些虚拟环境
conda update conda：检查更新当前conda
```



### anaconda安装与配置

**参考：**

- [Conda 换源（国内清华源）](https://www.quanxiaoha.com/conda/conda-update-channel.html) 

**镜像源**

北京外国语: [Index of /anaconda/archive/ | 北京外国语大学开源软件镜像站 | BFSU Open Source Mirror](https://mirrors.bfsu.edu.cn/anaconda/archive/?C=M&O=D)

阿里: [anaconda-archive安装包下载_开源镜像站-阿里云 (aliyun.com)](https://mirrors.aliyun.com/anaconda/archive/?spm=a2c6h.25603864.0.0.1f024eb9sBVnQc)

```shell
# wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/
wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh


bash Anaconda3-2022.10-Linux-x86_64.sh

# 在.bashrc文件中添加
export PATH="/home/huyan/anaconda3/bin:$PATH"

# 激活conda环境
source activate base
```

**换源**

```shell
# 生成.condarc文件，或者直接创建一个
conda config 

# 换源 写入.condarc文件
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


# 切换会默认源
conda config --remove-key channels
```

