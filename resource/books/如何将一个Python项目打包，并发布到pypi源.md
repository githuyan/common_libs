\### 如何将一个Python项目打包，并发布到pypi源 

\#### 一、准备.pypirc 

\- 在$HOME(~)目录创建[.pypirc](https://packaging.python.org/specifications/pypirc/)内容如下： 

\``` 

[distutils] 

index-servers = 

weike 

[weike] 

repository: https://pypi:9LmOBdpxKQ3B@pypi.weike.fm 

username: 

password: 

\``` 

distutils部分定义了一个index-servers字段，该字段列出了描述远程仓库的所有字段名称。 

\- repository --远程仓库地址 

\- username 远程仓库账号 

\- password 远程仓库密码 

\#### 二、setup.py 

\```python 

\#!/usr/bin/env python3 

from __future__ import print_function 

from setuptools import setup, find_packages 

import os 

here = os.path.abspath(os.path.dirname(__file__)) 

with open(os.path.join(here, "README.md"), "r", encoding="utf-8") as fobj: 

long_description = fobj.read() 

setup( 

name="", 

version="", 

author="", 

author_email="", 

description="", 

long_description=long_description, 

long_description_content_type="text/markdown", 

license="MIT", 

url="" 

install_requires=[ 

"requests>=1.0", 

"fastapi>=1.0" 

], 

classifiers=[ 

"Development Status :: 5 - Production/Stable", 

"Intended Audience :: Developers", 

"Topic :: Software Development :: Libraries :: Python Modules", 

"License :: OSI Approved :: MIT License", 

"Programming Language :: Python :: 3", 

"Programming Language :: Python :: 3 :: Only", 

], 

platforms=["all"], 

packages=find_packages(), 

entry_points={ 

"console_scripts": [ 

"notify = src.notify:notify" 

] 

} 

) 

\``` 

setup.py是打包时的重要文件,以下是要特别注意的字段: 

\- version --版本，要注意每次上传到远程仓库时的版本号要唯一 

\- install_requires --被打包的项目所以依赖的第三方库 

\- classsifiers --声明包的分类信息，详情请见[classifiers](https://pypi.org/pypi?%3Aaction=list_classifiers) 

\- packages --指定哪些文件夹需要被打包，推荐使用find_packages() 

\``` 

find_packages(where='.', exclude=(), include=('*',)) 

\``` 

find_packages默认在与 setup.py 文件同一目录下搜索各个含有 __init__.py 的目录做为要添加的包。 

对于简单的项目结构，也可以使用 

\``` 

package_dir={'': 'src'} 

\``` 

这表示项目根目录中模块都在src中 

\- entry_points --生成脚本，一般如果想要在命令行中执行模块里的代码就需要配置entry_points。entry_points是一个字典,console_scripts是一个列表 

\``` 

"notify = src.notify:notify" 

"命令=文件夹.文件A:文件A里面的函数 

会被翻译成from src.notify import notify 

\``` 

\#### 三、打包 

\- 打包工具的安装 

pip3 install setuptools wheel twine 

\- 检查setup.p 

\``` 

python3 setup.py check 

\``` 

如果setup.py有问题这里会报一些错误或者警告 

\- 打包 

python3 setup.py sdist bdist_wheel 

生成： 

\``` 

dist/ 

example_pkg-0.0.1-py3-none-any.whl 

example_pkg-0.0.1.tar.gz 

\``` 

\- 检查打包是否成功 

\``` 

python3 setup.py install 

\``` 

执行完就可以在本地的site-packages中看到你的包 

\- 上传到pypi 

\``` 

python3 -m twine upload -r weike dist/* 

\``` 

这里的weike 就是你在~/.pypi里配的远程仓库 

\#### 四、reference 

[Python编程：twine模块打包python项目上传pypi](https://blog.csdn.net/mouday/article/details/80736312) 

[Python 库打包分发(setup.py 编写)简易指南](https://blog.konghy.cn/2018/04/29/setup-dot-py/) 