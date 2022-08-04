# python文件和异常

## python 文件

> 文件的读取和写入都属于IO操作，对造成对内存的占用，一般采用异步IO操作来高效利用内存

### 文件的读取与写入

关键字with 将自动控制打开和关闭文件，open（）\\close（）只打开或只关闭文件，

若使用文件时 文件已关闭，会出现 `ValueError:  I/O operation on closed file`

| 标志 | 作用                           | 备注                               |
| ---- | ------------------------------ | ---------------------------------- |
| `r`  | 以二进制读方式打开，只能读文件 |                                    |
| `rb` | 以二进制读方式打开，只能读文件 |                                    |
| `w`  | 以二进制读方式打开，只能读文件 | 覆盖式写入                         |
| `wb` | 以二进制写方式打开，只能写文件 | 像是图片，视频等资源都是二进制形式 |
| w+   | 以二进制读方式打开，只能读文件 | 追加式写入                         |

#### 读取

```python
# 读取全部文件
with open ('file.txt','r',encoding='utf-8') as f:
      files=f.read()

# 逐行读取文件,逐行读取时，若遇到换行符，也将会读到，将会出现空白，去除空白可用strip()
wiith open('file.txt',encoding='utf-8') as f:
    for line  in f:
    print(line)
# 或者
with open('file.txt','r',encoding='utf-8') as f:
    files = f.readline() # 这是一个生成器
for line in files:
    print(line)

# 这是一个读取文件的实例
from collections import Counter
result = Counter()
with open("./data.txt","r",encoding='utf-8') as f:
    while True:
        lines = f.read(1024).splitlines()
        if lines==[]:
            break
        lines = [lines[i].split(" ") for i in range(len(lines))]
        words = []
        for line in lines:
            words.extend(line)
        tmp = Counter(words)
        result+=tmp

print (result.most_common(10))
```

#### 写入

```python
with open('file.txt','w',encoding='utf-8') as f:
    f.write(data)
```

#### JSON

**参考：** 

- https://www.w3cschool.cn/article/30808038.html

```python 
import json

# .loads() 将普通字符转化为python对象(List,Dict,int,float,tuple...)
# .load()  将字节流转化为python对象

with open('file.json','r',encoding='utf-8') as f:
    # data = f.load(f)

    line = f.read()
    data = json.loads(line)
    
# .dumps() 将python对象格式化成json字符
# .dump() 用来将python对象写入json文件

with open('file.json','w',encoding='utf-8') as f:
    json.dump(data,f) # 直接将python对象数据 data 写入文件f中
    
    file = json.dumps(file,indent=2,ensure_ascii =False)
    # ensure_ascii使可以写入中文字符,indent规定了写入文件后字典格式的缩进
    f.write(file)

# 这两个方法主要是用来转化的    
json.dumps() # 将json对象转化为字符串
json.loads() # 将字符串转化为json对象
```

json.dumps

```python
json.dumps(obj, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, encoding="utf-8", default=None, sort_keys=False, **kw)

# separators=(",", ":") 表示每个键值对之间的连接， 和键与值之间的连接
```





### 文件的导入

> 实际上python的任何对象都是可以导入的，包括类，函数，字典，甚至是变量

- **绝对导入**

  优点：绝对的路径保证了绝对的正确性，如果只是在本地工作，把值写死一般可以解决一切导入问题

  缺点：太死板，当文件夹变动，则全部路径都要修改，很是麻烦，效率低下

- **相对导入**

  优点：相对的路径更显智能便利，当文件夹路径变动，只要保证内层文件不变，就无需修改，敏捷高效

  缺点：很容易出问题，尤其是对编辑器不太熟悉的情况下，只有理解了`if __name__ == "__main__"`，才能保证遇到导入问题，能更快的解决

```python
绝对导入	from package_1.package_2 import module_21
相对导入	from . import package_1
			from .package1 import package_2		一个点表示从当前同级目录导入
			from ..package_1 import package_2   两个点表示从上一级目录导入

从模块中导入多个类     from car inport Car
导入整个模块     		import car
从模块中导入所有的类    from car import *
```

- **`if __name__ == "__main__"`**

  > 作用：这一条语句后的内容不被其他文件导入，从而避免了很多问题

  ```python
  # practice/test/parent.py
  def test():
  	print( __name__ )
  if __name__ == '__main__':
  	test()
  # __main__
  
  # practice/test/child.py
  from practice.parent import test
  test()
  # practice.test.parent
  
  # 当__name__在定义它的文件运行时，他被解释为 __main__，而当他作为模块被导入时，他就是被定义时的文件名
  ```

3. ##### 一些文件

   - **`__init__.py`**  

     > 这个文件是表明，当前文件夹是一个模块，而不是一个普通文件夹，在`pycharm`编辑器要注意这点

     ```python
     # 一般来说这个文件的内容应该尽量少
     #作用
     - module
     	-__init__.py
         -test.py
         -new.py
     *************************
     __all__ = ['test','new']
     # 像这样，当使用 from module import * 时，没有添加进__all__的模块，不会被导入进来
     ```

4. 其他操作文件的方法

   1. 创建临时文件 [参考](http://c.biancheng.net/view/2560.html)

      > 临时文件，用完即删，缓解内存压力

      ```python
      import tempfile
      import time
      
      fp = tempfile.TemporaryFile() # 创建临时文件
      time.sleep(10)
      print(fp.name)
      # C:\Users\HP\AppData\Local\Temp\tmph3kxctm1,
      # 可以看到此文件被创建在这个目录，文件名随机，10秒后程序结束，文件被自动删除
      
      #使用with自动管理上下文，with会自动关闭临时文件，当文件被关闭，文件就会被自动删除
      with tempfile.TemporaryFile() as temp:
          temp.write(b'Some data')
          time.sleep(10)
          
          # 将文件指针移到开始处，准备读取文件
          temp.seek(0)
          print(temp.read())
      #写入数据之后，文件描述符的内部指针必须重置到文件开始以便从文件读回数据。
      
      # 也可以使用手动关闭
      fp.close()
      
      #文件名  dir + prefix + random + suffix = dir(路径）/prefixandomsuffix 后缀并不是格式后缀，默认没有文件名
      ```

### 异常

> 不能过分依赖异常机制，毫无条理的异常会导致，程序明明出错了，但是却没有报错信息，导致无法定位，无法排查，程序报错是一种提示，不能畏惧报错，当没有报错，却达不到预期结果时，才是最让人头疼的
>
> 但是适当，详细的异常检测，能更高效的处理问题

- try/except

  ```python
  try:
      f = open("file", "r",encoding='utf-8')
  except IOError as e:
      print(f"open exception: {e.args}")
  except ValueError as e:
      print(f"open exception: {e.args}")
  except SyntaxError as e:
      print(f"格式错误: {e.args}")
  else:
      print('文件打开正常！')
  finally:
      print('结束！')
  # 捕获错误越精确，，越详细，越能尽快的修复
  ```



## 技巧

**避免名称冲突**

我们将直接导入 `items` 子模块，而不是仅导入其 `router` 变量

```python
from .routers import items, users

app.include_router(users.router)
app.include_router(items.router)
```









