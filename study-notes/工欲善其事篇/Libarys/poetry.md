### poetry python环境管理工具

**参考：**

- [Python - poetry（2）命令介绍](https://www.cnblogs.com/poloyy/p/15267595.html) 
- [保障Python项目质量的工具](https://so1n.me/2021/08/10/%E4%BF%9D%E9%9A%9CPython%E9%A1%B9%E7%9B%AE%E8%B4%A8%E9%87%8F%E7%9A%84%E5%B7%A5%E5%85%B7/) 

#### 创建项目脚手架

1. 创建一个新的脚手架

   `poetry new [demo-my|顶级目录] --name [demo|项目名]` 

2. 在一个指定目录创建项目

   `poetry new --[demo-my|顶级目录] [demo|项目名]` 

3. 在一个指定的项目目录中初始化一个旧项目

   `poetry init` 

#### 安装

```shell
# 开启代理 安装
curl -sSL https://install.python-poetry.org | python3 -

# 卸载
curl -sSL https://install.python-poetry.org | python3 - --uninstall
```

#### 基本使用

##### 依赖

**读取`project.toml`并安装:**  `poetry install`

**不安装开发依赖项：** `poetry install --no-dev` 

```shell
# 添加依赖
poetry add requests
  --dev (-D)：将包添加为开发依赖项
  --path：指定依赖项的路径
  --optional：作为可选依赖项添加
  --dry-run：输出操作，不执行任何操作
  --lock：不执行安装，仅更新 poetry.lock 文件
  
# 添加到主环境 默认不会添加到开发依赖中需要使用 `--dev` 参数
poetry add requests

# 添加到开发环境
poerty add requests --dev

# 更新指定的依赖
poetry update flask

# 移除依赖
poetry remove requesets
  --dev（-D)：从开发依赖项中删除包
  --dry-run：输出操作，不执行任何操作
```

##### **查看依赖信息**

 `poetry show fastapi`

- --no-dev：不要列出开发依赖项
- --tree：树的形式列出依赖项
- --latest (-l)：显示最新版本
- --outdated (-o)：显示最新版本，但仅适用于过时的软件包
```shell
poetry show  # 查看当前环境（没有则创建一个）的所有三方库信息
```

**检查toml文件格式** `poetry check`

**输出requestments.txt文件**

```shell
# 测试环境
poetry export -o requirements-dev.txt --without-hashes --with-credentials --dev
```



#### 执行脚本

> 指定目录下的所有脚本或单个地哦啊本

```shell
poetry run my_script
poetry run signle_script.py

# pyproject.toml
[tool.poetry.scripts]
my_script = "my_module:main"
```

#### 虚拟环境管理

```shell
# 查询
poetry env list

# 显式开启虚拟环境
poetry shell
# 退出虚拟环境
deactivate
# 查看安装包信息
poetry show
poetry show --tree #添加--tree 参数选项可以查看依赖关系：

# 使用环境
poetry env use 3.9
```

#### 环境配置

```shell
poetry config --list

# 使用 Poetry 时，在 pyproject.toml 末尾添加下面的内容来设置自定义镜像源：

# 添加镜像源
[[tool.poetry.source]]
name = "douban"
url = "https://pypi.doubanio.com/simple/"
```

