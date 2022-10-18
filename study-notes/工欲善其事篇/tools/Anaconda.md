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



