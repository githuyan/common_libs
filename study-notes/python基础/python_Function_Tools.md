# python常用模块

## collections

### from collections import *

#### Counter

> 这是一个计数器

```python
from collections import Counter

c = Counter('abcdeabcdabcaba') 
print(c)
# 创建一个Counter实例，内部维护了一个字典，可以进行普通字典常用的操作
# {'a': 5, 'b': 4, 'c': 3, 'd': 2, 'e': 1} 默认是降序排列

# 获取前三个最频繁出现的元素
c.most_common(3)

d = 'aaabbb' or {'a':3,'b':3}
# 增量更新
c.update(d) 
# 覆盖更新
c.subtract(d)

# 键的删除，数量为零时并不会主动删除,需要用 del counter[a]
```

#### ChainMap

> 将多个映射合并为单个映射

```python
from collections import ChainMap

a = { 'x':2,'y':4}
b = { 'z':6,'y':5}
ch  = ChainMap(a,b) # 以b 为基准,将a覆盖到b里面
ch.items = {'x':2,'z':6,'y':4} # 若相同项，只取第一个
# 删除  del ch['x']
# 若要重复取值 则：
ch = ChainMap()
ch['x'] = 1
ch = ch.new_child()
ch['x'] = 2
ch = ch.new_child()
ch['x'] = 3
return    ch = ['x':1,'x':2,'x':3]    return ch['x']  = 3
# 删除
ch = ch.parents
ch['x'] = 2
```

#### defaultdict

> 一键多值字典，这个解决的是一个分组问题

这个传入的参数其实是一个**容器**，defaultdict会在初始化的时候，将每一个键都自动映射到这个容器，所以是使用 d[ 'name1' ].append( 'value1' ) 这种方式添加数据的

```python
from collections import defaultdict

# 这个传入的参数其实是一个容器，defaultdict会在初始化的时候，将每一个键都自动映射到这个容器，所以是使用 d[ 'name1' ].append( 'value1' ) 这种方式添加数据的
d = defaultdict(list)  # 这个 list 一定要加，默认了字典的键，也可以是 set ，当使用set时，下面的append操作应改为 add
d[ 'name1' ].append( 'value1' )
d[ 'name1' ].append( 'value2' )
d[ 'name2' ].append( 'value3' )
print(d.items())
# dict_items([('name1', ['value1', 'value2']), ('name2', ['value3'])])
```

#### deque

> 这是一个双向列表

```python
from collections import deque

d = deque(maxlen=None)
# 具有一般列表的所有操作方法

d.count('param') # 一个计数器，返回d中param元素的个数
d.extendleft(iterable) 	# 首扩展
d.appendleft(ele) 		# 首插入
```

#### Nametuple 命名元组

> 通过真正的访问对象的方式，访问元组中的元素

```python
from collections import namedtuple

info = namedtuple('description',['name','age']) # 第一个元素是描述性的

print(info.__doc__) # description(name, age)

a = info('tom',22)
print(a.name,a.age) # tom 22
```

#### 有序字典 OrderedDict

> 可以通过链表的形式构建有序，链表本身就是有序的，然后根据值进行查找，进行插入删除操作,官方使用的是双相链表节点，

## intertools

### from itertools import *

#### chain

> 将不同的可迭代对象联立起来，返回一个生成器

```python
from itertools import chain

# 扁平化可迭代对象
b=[[1,2],[5,6],[8,9]]

# 这里的 *b 注意，前面带 * 说明这是一个元组，传进去的是元组中的元素，而不是一个元组对象
print(list(chain(*b))) 
# 或者这样
print(list(chain([1,2],[5,6],[8,9])))

print([i for i in chain.from_iterable(b)])
# [1, 2, 5, 6, 8, 9]
# [1, 2, 5, 6, 8, 9]

# 连接两个可迭代对象
a = [7,4,5,7,8]
print([i for i in chain(a,b)])
```

#### islice

> 对可迭代对象的切片进行命名，可以对生成器生效

```python
from itertools import islice

a = [i for i in range(10)]

# islice对象可以和 inistance(key,islice) 一起使用
one = islice(a,4,8) # 这个返回值依然是一个生成器
print(list(one))
# <itertools.islice object at 0x000002CD7D98CB38>
# [4, 5, 6, 7]
```

## from functools import *

1. #### 偏函数 partial

   ```python
   from functools import partial
   
   def add(x, y, z):
       print(x + y + z)
       
   new_add = partial(add,3) # 这个新函数的第一个参数永远都被固定为 3
   
   # 这是自己通过闭包实现的类似的精简版本，
   def func(x,y):
       print(x+y)
       
   def partial(func,param):
       def inner(x):
           return func(param,x)
       return inner
   
   new(3) # 4
   new(2) # 3
   ```
   
   
   
2. #### 叠加函数 reduce

   ```python
   from functools import reduce
   
   a = [1,2,3,4,5]
   print(lambda x,y:x+y,a) # 只接受两个参数
   ```

3. #### 装饰器元数据保存 wraps

   ```python
   from functools import wraps
   
   # 当将函数出入装饰器后，函数已经不是原先的函数了，它的一些元信息将会丢失，显示函数名(func.__name__)，函数描述文档(func.doc__)，函数的参数信息(func.__annotations__)
   
   def outer(*params):
       def middle(func):
           @wraps
           def inner(*args,**kwargs):
               result = func(*args,**kwargs)
               return result
           return inner
       return middle
   ```
   
   

## heapq

> 堆结构，堆的第一个元素总是最小的

```python 
import heapq

# 创建一个空堆
a = []
heapq.heappush(a,23)
heapq.heappush(a,3)
heapq.heappush(a,13)
print([heapq.heappop(a) for _ in range(len(a))]) # 正序排序
# [3, 13, 23]

# 对可迭代对象应用堆
b = [3,4,6,3,5,8,3,6,8]
heapq.heapify(b)
print([heapq.heappop(b) for _ in range(len(b))])
# [3, 3, 3, 4, 5, 6, 6, 8, 8]

# 获取堆中的最大值或最小值
c = [3,4,5,6,81,23]
items_l = heapq.nlargest(2,c) # 获取堆中最大的两个值
print(items_l)
# [81, 23]
items_s = heapq.nsmallest(2,c) # 获取堆中最小的两个值
# [3, 4]

# 更灵活的用法 key
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09}
]
cheap = heapq.nsmallest(2, portfolio, key=lambda s: s['price'])
print(cheap)
# [
#     {'name': 'FB', 'shares': 200, 'price': 21.09},
#     {'name': 'IBM', 'shares': 100, 'price': 91.1}
# ]
```

## datetime

### from datetime import *

#### datetime,date,time

> 混合对象，日期对象，时间对象
>
> ```
> %Y-%m-%d %H:%M:%S
> ```

```python
# datetime,time,date 等对象
now = datetime.now()

now.replace(day=20)  # 时间的修改，返回一个新的时间对象
now.weekday() # 计算出是周几
```

![时间的所有格式化](D:\桌面\学习笔记\resource\时间的所有格式化.png)

#### timedelta

> 一个时间间隔

```python
import datetime

# 两周+两天+两小时+两分钟+两秒 一共是多长时间（几天）
interval = datetime.timedelta(weeks=2,days=2,hours=3,minutes=2,seconds=3)

print(type(interval))
print(interval)

# <class 'datetime.timedelta'>
# 16 days, 3:02:03
```

应用

用于时间的加减，并保持时间是合法的

```python
datetime + datetime == datetime

datetime + timedelta == datetime # 迭代时间的加减
```



## 枚举类-enum

> 枚举类，及以枚举类为父类的子类都无法实例化
>
> Python 枚举类中各个成员必须保证 name 互不相同
>
> 枚举类的每个成员都由 2 部分组成，分别为 name 和 value
>
> 枚举类成员之间不能比较大小，但可以用 == 或者 is 进行比较是否相等(只有成员的值可以比较大小)

```python
from enum import Enum

class Color(Enum):
    # 为序列值指定value值
    red = 1
    green = 2
    blue = 3
    
#调用枚举成员的 3 种方式
print(Color.red)
print(Color['red'])
print(Color(1))
#调取枚举成员中的 value 和 name
print(Color.red.value)
print(Color.red.name)
#遍历枚举类中所有成员的 2 种方式
for color in Color:
    print(color)
    print(color.name)
for name,value in Color.__member__.items():
    print(name,value)
    
Python 枚举类中各个成员必须保证 name 互不相同, value 可以相同，可以使用 unique 装饰器使得 value 也是唯一的

from enum import import Enum,unique

@unique
class Color(Enum):
    # 为序列值指定value值
    red = 1
    green = 2
    blue = 3
    
Color = Enum("Color",('red','green','blue'))
print(Color.red.value)  # 默认为序号 从 1 开始
```



## 反射类-inspect

> 可以用来定位
>
> 相当于是 dir，hasattr，getattr，setattr等方法的升级版

### 参考

- [Python自省（反射）指南 - AstralWind - python 2.X ](https://www.cnblogs.com/huxi/archive/2011/01/02/1924317.html)

### 对象的类型

**模块 moudule**

- \__doc__: 文档字符串。如果模块没有文档，这个值是None。
- \__name__: 始终是定义时的模块名；即使你使用import .. as 为它取了别名，或是赋值给了另一个变量名。
- \__dict__: 包含了模块里可用的属性名-属性的字典；也就是可以使用模块名.属性名访问的对象。
- \__file__: 包含了该模块的文件路径。需要注意的是内建的模块没有这个属性，访问它会抛出异常！

**类 class**

- \__doc__: 文档字符串。如果类没有文档，这个值是None。
- \__name__: 始终是定义时的类名。
- \__dict__: 包含了类里可用的属性名-属性的字典；也就是可以使用类名.属性名访问的对象。
- \__module__: 包含该类的定义的模块名；需要注意，是字符串形式的模块名而不是模块对象。
- \__bases__: 直接父类对象的元组；但不包含继承树更上层的其他类，比如父类的父类。

**实例 instance**

- \__dict__: 包含了可用的属性名-属性字典。
- \__class__: 该实例的类对象。对于类Cat，cat.__class__ == Cat 为 True。

**函数 function**

- \__doc__: 函数的文档；另外也可以用属性名func_doc。
- \__name__: 函数定义时的函数名；另外也可以用属性名func_name。
- \__module__: 包含该函数定义的模块名；同样注意，是模块名而不是模块对象。
- \__dict__: 函数的可用属性；另外也可以用属性名func_dict。
  不要忘了函数也是对象，可以使用函数.属性名访问属性（赋值时如果属性不存在将新增一个），或使用内置函数has/get/setattr()访问。不过，在函数中保存属性的意义并不大。
- func_defaults: 这个属性保存了函数的参数默认值元组；因为默认值总是靠后的参数才有，所以不使用字典的形式也是可以与参数对应上的。
- func_code: 这个属性指向一个该函数对应的code对象，code对象中定义了其他的一些特殊属性，将在下文中另外介绍。
- func_globals: 这个属性指向定义函数时的全局命名空间。
- func_closure: 这个属性仅当函数是一个闭包时有效，指向一个保存了所引用到的外部函数的变量cell的元组，如果该函数不是一个内部函数，则始终为None。这个属性也是只读的。

**方法 method**

- \__doc__: 与函数相同。
- \__name__: 与函数相同。
- \__module__: 与函数相同。
- im_func: 使用这个属性可以拿到方法里实际的函数对象的引用。另外如果是2.6以上的版本，还可以使用属性名\__func__。
- im_self: 如果是绑定的(bound)，则指向调用该方法的类（如果是类方法）或实例（如果是实例方法），否则为None。如果是2.6以上的版本，还可以使用属性名\__self__。
- im_class: 实际调用该方法的类，或实际调用该方法的实例的类。注意不是方法的定义所在的类，如果有继承关系的话。

**生成器 generator**

- \__iter__: 仅仅是一个可迭代的标记。
- gi_code: 生成器对应的code对象。
- gi_frame: 生成器对应的frame对象。
- gi_running: 生成器函数是否在执行。生成器函数在yield以后、执行yield的下一行代码前处于frozen状态，此时这个属性的值为0。
- next|close|send|throw: 这是几个可调用的方法，并不包含元数据信息，如何使用可以查看生成器的相关文档。

**代码块 code**

> 就是源代码

- co_argcount: 普通参数的总数，不包括*参数和**参数。
- co_names: 所有的参数名（包括*参数和**参数）和局部变量名的元组。
- co_varnames: 所有的局部变量名的元组。
- co_filename: 源代码所在的文件名。
- co_flags: 这是一个数值，每一个二进制位都包含了特定信息。较关注的是0b100(0x4)和0b1000(0x8)，如果co_flags & 0b100 != 0，说明使用了*args参数；如果co_flags & 0b1000 != 0，说明使用了**kwargs参数。另外，如果co_flags & 0b100000(0x20) != 0，则说明这是一个生成器函数(generator function)。

**栈帧 frame**

> 栈帧表示程序运行时函数调用栈中的某一帧。函数没有属性可以获取它，因为它在函数调用时才会产生，而生成器则是由函数调用返回的，所以有属性指向栈帧。想要获得某个函数相关的栈帧，则必须在调用这个函数且这个函数尚未返回时获取。你可以使用sys模块的_getframe()函数、或inspect模块的currentframe()函数获取当前栈帧。这里列出来的属性全部是只读的。

- f_back: 调用栈的前一帧。
- f_code: 栈帧对应的code对象。
- f_locals: 用在当前栈帧时与内建函数locals()相同，但你可以先获取其他帧然后使用这个属性获取那个帧的locals()。
- f_globals: 用在当前栈帧时与内建函数globals()相同，但你可以先获取其他帧……。

**追踪 traceback**

> 追踪是在出现异常时用于回溯的对象，与栈帧相反。由于异常时才会构建，而异常未捕获时会一直向外层栈帧抛出，所以需要使用try才能见到这个对象。你可以使用sys模块的exc_info()函数获得它，这个函数返回一个元组，元素分别是异常类型、异常对象、追踪。traceback的属性全部是只读的

- tb_next: 追踪的下一个追踪对象。
- tb_frame: 当前追踪对应的栈帧。
- tb_lineno: 当前追踪的行号。

### 反射 inspect 的使用

**检查对象类型**

- inspect.is**(module|class|function|method|builtin)

  > 检查是否是某个特定的类型

  ```python
  import inspect
  
  def test(name:str):
      return anme
  
  print(inspect.isclass(test))
  print(inspect.isfunction(tet))
  ```

- isroutine(obj) 

  > 检查是否是可调用类型（function，class，builtin，等）

  ```python
  import inspect 
  
  def test(name:str):
      return name
  
  print(inspect.isroutine(test))
  ```

**获取对象信息**

- inspect.getmembers(obj)

- inspect.getmodule(obj)

  > 返回 对象的

  ```python
  <module '__main__' from 'F:/python_pycharm/pritice/exam/B.py'>
  ```

- inspect.get**(file|sourcefile)

  > 获取object的定义所在的模块的文件名|源代码文件名（如果没有则返回None）。用于内建的对象（内建模块、类、函数、方法）上时会抛出TypeError异常

- get**(source|sourcelines)

  > 获取object的定义的源代码，以字符串|字符串列表返回。代码无法访问时会抛出IOError异常。

- getfullargspec(obj)

  ```python
  FullArgSpec(args=['name'], varargs=None, varkw=None, defaults=None, kwonlyargs=[], kwonlydefaults=None, annotations={'name': <class 'str'>})
  ```

  
  

### 使用场景

1. 使用 反射 机制能很好的解决 if 判断太多的问题



## typing

**list 和 List 的区别**

> 两种都不报错

```python
from typing import List

def a(a:list):  # 不用规定内容
    return a

def b(b:List[int])  # typing 中的 List 必须要规定内容

aa = [1,2,3,4]
print(a(aa))
```



## Pydantic 

> pydantic主要是一个解析库，而**不是验证库**。验证是达到目的的一种手段：构建符合所提供的类型和约束的模型
>
> pydantic保证输出模型的类型和约束，而不是输入数据。
>
> 一般需要配合 typing 使用

```python
from typing import Union,List,Dict
from pydantic import BaseModel

class Base(BaseModel):
    ids: List[int]
    ages: List[int]

    names: Union[str,int]

a = Base(ids=[1,2,3],ages=[1,2,3,4],names=1)
```

## Re

| 通配符          | 含义                                                         | 实例                                                         |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| .               | 匹配除换行符 \n 之外的任何单字符                             |                                                              |
| []              | 表示字符集，对单个字符给出取值位置                           | [abc] 表示 a 或 b 或 c 任意一个字符                          |
| [^ ]            | 表示非字符集，对单个字符给出排除位置                         | \[^abc] 表示，出现一个字符，这个字符不是 a,抑或 b 抑或 c,抑或 A,只要不是小写a,b,c |
| *               | 表示前一个字符出现 0 次或无限次扩展                          | abc* 表示 可以是，ab,abcc,abccc 等                           |
| +               | 表示前一个字符出现 1 次或无限次扩展                          |                                                              |
| ？              | 表示前一个字符出现 0 或 1 次                                 |                                                              |
| \|              | 或符。表示左右表达式任意一个                                 |                                                              |
| {m}             | 表示扩展前一个字符 m 次                                      |                                                              |
| {m,n}           | 表示扩展前一个字符 m 至 n 次（含n)                           | ab{1,2}c 表示abc ,或abbc                                     |
| ^               | 如果其没有出现在 [] 中，则表示匹配字符串的开头               |                                                              |
| $               | 表示匹配字符串的结尾                                         |                                                              |
| ()              | 表示分组标记，在内部只能使用 \| (或) 操作符    分组标记，可以作用于字符组 r'(\xa0){3}' | 分组标记[^分组标记]                                          |
| \d              | 表示数字，等价于[0-9]                                        |                                                              |
| \w              | 表示单词字符 等价于[A-Z,a-z,0-9,_]                           |                                                              |
|                 |                                                              |                                                              |
| *?/+?/??/{m,n}? | 这四个在保持原意的同时，皆返回非贪婪匹配                     |                                                              |

**主要功能函数**

**参数：**

```python
pattern:  正则表达式的字符串后原生字符串表示
string：   待匹配字符串
flags:        正则表达式使用时的控制标记
maxsplit ：  最大分割数
count :       匹配的最大替换次数
repl :        替换匹配字符串的字符串

​         re.I  (re.IGNORECASE)    使忽略正则表达式的大小写，【A-Z】能够匹配小写字符
​         re.M  (re.MULTILINE)  使正则表达式中的 ^ 操作符能够将给定字符串的每行当做匹配的开始
​         re.S  (re.DOTALL)    使正则表达式中的  .  操作符，能够匹配所有字符，默认匹配除换行符外的所有字符
```



**用法**

```python
re.search(pattern,string,flags=0) # 从一个字符串中搜索匹配正则表达式的第一个位置，返回 match 对象

re.match(pattern,string,flags=0) # 将以正则表达式作为头部的字符串进行匹配，返回 match 对象

re.findall(pattern,string,flags=0)#搜索字符串，以列表类型返回全部能匹配的字串

re.split(pattern,string,maxsplit = 0,flags=0) # 将一个字符串按照正则表达式匹配结果进行分割,把匹配的部分去掉，返回列表类型,maxsplit 为最大分割数，

re.finditer(pattern,string,flags=0) # 搜索字符擦混，返回一个匹配结果的迭代类型，每个迭代元素是 match 对象

re.sub(pattern,relpl,string,count=0,flags=0) # 在一个字符串中替换所有匹配正则表达式的字串，返回替换后的字符串，count用于替换几个，count=1，则只替换第一个，默认替换全部

面对对象的用法 # pattern 并不是正则表达式，这仅仅是一种正则表达式的表示方法，

regex = re.compile(pattern,flags) # 将正则表达式的字符串形式编译成原生字符串表示，经过了 compile 的编译
```





[^分组标记]: 分组标记解释

abc表示 abc,(abc|def) 表示 abc 或 def 

​         regex = re.compile(r'(0\d{2})-(\d{8}')

​         text = regex.search('My python number is 032-12345678')

​         text.group(0)   : 032-12345678

​         text.group(1)   : 032

​         text.group(2)   : 12345678











