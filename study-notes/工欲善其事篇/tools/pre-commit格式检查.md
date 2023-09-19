#### git格式化

1. 建立per-commit.yaml文件

   ```yaml
   repos:
     - repo: https://github.com/psf/black # 第一次会下载，后面会reuse
       rev: 22.3.0 # 当前最新版本
       hooks:
         - id: black
           args: ['-l 127'] # 默认最大长度是88
           language_version: python3.8 # 建议明确版本，不然会使用默认版本3.7(https://pre-commit.com/#top_level-default_language_version)
   
     - repo: https://github.com/pycqa/flake8 # 同上
       rev: 4.0.1  # 同上
       hooks:
         - id: flake8
           args: ['--show-source', '--count', '--statistics', '--max-line-length=127', '--max-complexity=10', '--extend-ignore=E203,E402,C901,W291,E501'] # 默认最大长度79，复杂度和可选忽略错误可以看情况更新，其默认值可通过flake8 -h查看
           exclude: ^venv/ # (^foo/|^bar/)
   
   ```

2. 下载`pre-commit`插件

   ```shell
   pip install pre-commit -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
   ```

3. git命令增加hook

   ```shell
   pre-commit install
   ```

   

