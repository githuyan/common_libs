# **python的字符串，变量，**

## String

### 字符串的格式控制

f-string 

```python
f"这是{a}变量，这是{a:0^10}总共十个字符，中间挖坑填a，其余补0"
string.center(50,'*') 	#50个字符，中间挖坑填a，其余补*
f"{m:03d}" # m是一个整数，三个坑，填入m后其余在前面补0
```

| >    | 右对齐                                  |          |
| ---- | --------------------------------------- | -------- |
| <    | 左对齐                                  | {a:0<10} |
| ^    | 居中                                    | {a:0^10} |
| %    | 百分比                                  | {a:0.3%} |
| f    | 保留精度（前面补空格，后面保留2位小数） | {a:8.2f} |
| e    | 科学计数法                              | {a:8.2e} |



### 技巧

- **print**

  ```python
  print(1,2,3,4,sep='\n') # 每个值之间用 换行相连
  print(1,2,3,4,end='\n') # 此条打印结束时输出 换行
  ```

  



### 进制与字符

eval(str )        用来计算在字符串中的有效Python表达式,并返回一个对象   

ast.literval_eval(str) [^安全点的方式],

> eval 和 此函数都不能转化 'null'，包括  'true'

literval_eval  直观来说，相当于把一段字符串两边的引号去掉 如："{'a':10}" 会被执行为一个字典

 

chr(x )         将一个整数转换为一个字符    

ord(x )         将一个字符转换为它的整数值  **ord('1') - ord('0') == 1**

   

hex(x )         		将一个十进制整数转换为一个十六进制字符串   

oct(x)         			将一个十进制整数转换为一个八进制字符串  

bin(x)   	         	将一个十进制整数转换为二进制    bin( 10 )  return   '0b111'

int( 'x' , 2 )  	      将==二进制字符==转换为十进制

int( 'x' , 8 ) 	       将八进制字符转换为十进制           bin( '456' , 8 )    return 302

int( 'x' , 16 )          将十六进制字符转换为十进制

### 字符串前缀

| 前缀      | 作用                                                         | 备注                             |
| --------- | ------------------------------------------------------------ | -------------------------------- |
| u"string" | 后面的string以Unicode格式进行编码，一般用在中文字符串前面，放置因为源码存储格式问题，导致再次使用时出现乱码 | python3将字符串处理为unicode类型 |
| r"string" | 表示一个非转义原生字符串，常用在表示路径时，和正则表达式中   | r'input\n' == input\n            |
| b"string" | 表示这是一个bytes对象，二进制                                | python2将字符串处理为bytes类型   |
| f"string" | 字符串格式化                                                 |                                  |

### 字符串的常用方法

1. **字符串形式变换**

   `.title()`	驼峰

   `.capitalize()`	首字母大写

   `.upper()`	全部大写

   `.lower()`	全部小写

   `.center()`	居中

   `.format()`	格式化

   `.partition（sep)`	从sep第一次出现的位置，将字符串拆分为三部分，0，sep，2

   `.strip()`	删除首尾空格，lstrip()，rstrip()

   `.replace(oldvalue, newvalue, count)` 正则替换的简化，count 为替换旧值的次数，默认全部替换，**没有则不替换**

   `.split(separator，max)`	分割字符串中所有出现str的片段，默认以空格分割,max代表最大分割为几段

   `.splitlines()`	和`.split('\n')`,相似，常用于读取文档([f.readlines()和f.read().splitlines()的区别](https://blog.csdn.net/qq_44846758/article/details/103885629)

   `"str=None".join([string1,string2])`	拼接字符串

   `\.translate(remap)` 字符的重新 映射，类似于sub()

   ```python
   remap = {
       ord('\n'): 'aa'
   }
   strs = 'ni \n 未发送'
   strs.translate(remap) 
   
   # ‘ni aa 未发送'
   ```

2. **查询**

   `.index('sub'，start=None,end=None) `	查询第一个匹配对象的索引

   `.count(str)`	查询总字符str的数量

   `.endswith(suffix,start=None,end=None)`	查询后缀以什么结尾，返回bool 
   
   - 检查多个条件需要传入一个条件的元组
   
     ```python
     a.endswith(('a','b'))
     ```
   
     
   
   `.startswith(preffix,start=None,end=None)`
   
3. **判断**

   `.isspace()`

   `.isuper()`	`.islower()`	`.istitle()`	

   `.isnumeric()`	`.isdigit()`	`.isascii()`	`.isdecimal()`

   `.isidentifier()` 判断是否是有效的python标识符，可用来判断变量名是否合法

   之前加 is  便能实现判断，返回一个布尔值

4. **从右边开始**

   `.lstrip()`删除右边的空格

   很多操作默认从左至右，可以在方法前加 l 使得从右边开始

### **正则表达式**

> 正则表达式是一种通用的匹配方法，在python中体现在 re 模块
>
> 正则表达式很杂，需要经常使用才能做到熟练

```python
import re
```

- 常用操作符

  ```python
  .   --- 表示任何单个字符，，字符表上出现的任意一个字符
  
  []  --- 表示字符集，对单个字符给出取值位置，
            实例：[abc] 表示 a 或 b 或 c 任意一个字符
  
  [^ ]  --- 抑或符 ，表示非字符集，对单个字符给出排除位置
             实例：[^abc] 表示，出现一个字符，这个字符不是 a,抑或 b 抑或 c,抑或 A,只要不是小写a,b,c
  
  *   ---表示前一个字符出现 0 次或无限次扩展
            实例： abc* 表示 可以是，ab,abcc,abccc 等
  
  + 	---表示前一个字符出现 1 次，或无限次扩展
           实例：abc+ 表示 可以是abc,abcc ,abccc 等
  
  ？ 	---表示前一个字符出现 0 次或 1 次扩展，即属于可选选项，
           实例：abc 可以是ab,abc
  
  | 	-----  或符。表示左右表达式任意一个
           实例：abc|def 可以是abc 或def
  
  {m} --表示扩展前一个字符 m 次
          实例：ab{2}c 表示为 abbc
      
  {m,n}--表示扩展前一个字符 m 至 n 次（含n)
         实例：ab{1,2}c 表示abc ,或abbc
  
  ^ 	---如果其没有出现在 [] 中，则表示匹配字符串的开头
         实例：^abc  匹配以abc 为开头的字符串
  $ 	--- 表示匹配字符串的结尾
         实例：abc$ 表示匹配以abc 为结尾的字符串
  
  () 	---表示分组标记，在内部只能使用 | (或) 操作符    分组标记，可以作用于字符组 r'(\xa0){3}'
        实例：(abc) 表示 abc,(abc|def) 表示 abc 或 def 
           regex = re.compile(r'(0\d{2})-(\d{8}')
           text = regex.search('My python number is 032-12345678')
           text.group(0)   : 032-12345678
           text.group(1)   : 032
           text.group(2)   : 12345678
  
  \d 	---表示数字，等价于[0-9]
  
  \w 	---表示单词字符 等价于[A-Z,a-z,0-9,_]
  
  
  \s 	---匹配任何空白字符，包括空格、制表符、换页符等等。等价于[ \f\n\r\t\v]。
  
  
  *?/+?/??/{m,n}?  --这四个在保持原意的同时，皆返回最小匹配
  
  ```

- 常用方法

  ```python 
  pattern:  正则表达式的字符串后原生字符串表示
  string：   待匹配字符串
  flags:        正则表达式使用时的控制标记
  maxsplit ：  最大分割数
  count :       匹配的最大替换次数
  repl :        替换匹配字符串的字符串
  
           re.I  (re.IGNORECASE)    使忽略正则表达式的大小写，【A-Z】能够匹配小写字符
           re.M  (re.MULTILINE)  使正则表达式中的 ^ 操作符能够将给定字符串的每行当做匹配的开始
           re.S  (re.DOTALL)    使正则表达式中的  .  操作符，能够匹配所有字符，默认匹配除换行符外的所有字符
  函数式一次性用法：
  # re.search(pattern,string,flags=0) -- 从一个字符串中搜索匹配正则表达式的第一个位置，返回 match 对象
  
  # re.match(pattern,string,flags=0) -- 将以正则表达式作为头部的字符串进行匹配，返回 match 对象
  	
  
  # re.findall(pattern,string,flags=0)--搜索字符串，以列表类型返回全部能匹配的字串
  
  # re.split(pattern,string,maxsplit = 0,flags=0) -- 将一个字符串按照正则表达式匹配结果进行分割,把匹配的部分去掉，返回列表类型,maxsplit 为最大分割数，
  
  # re.finditer(pattern,string,flags=0) -- 搜索字符擦混，返回一个匹配结果的迭代类型，每个迭代元素是 match 对象
  
  # re.sub(pattern,relpl,string,count=0,flags=0) -- 在一个字符串中替换所有匹配正则表达式的字串，返回替换后的字符串
  
  面对对象的用法----pattern 并不是正则表达式，这仅仅是一种正则表达式的表示方法，
  # regex = re.compile(pattern,flags) -- 将正则表达式的字符串形式编译成原生字符串表示，经过了 compile 的编译
  ```

- match 对象

  > 常用方法大都返回match对象

  ```python 
  # match 对象
  <re.Match object; span=(0, 2), match='aa'>
  # 比如说做爬虫提取数据时
  origin_image_data= '''src="data:image/png;base64,iVBORw0KGgoAAAANSUhE";code=utf-8'''
  origin_data = re.compile('"(?P<image>data.*?)";code=(?P<code>.*)')
  data = origin_data.search(origin_image_data)
  
  # 一次匹配分为能匹配到的所有内容和分组的内容
  print(data.group(1)) 		# data:image/png;base64,iVBORw0KGgoAAAANSUhE
  # 分组捕获
  print(data.group('image')) 	# data:image/png;base64,iVBORw0KGgoAAAANSUhE
  print(data.group('code'))	# utf-8
  ```

## 注意

1. 列表中的字符串之间没有 , 也不会报错

   ```python
   # 直接合并了？
   ["d", "d" "dd", "fds"]
   ['d', 'ddd', 'fds']
   ['a', 'c' 'dd', 'fsdf']
   ['a', 'cdd', 'fsdf']
   ```

   

---

   ## 变量

在最外层定义的变量为全局变量，函数中可以使用此全局变量，但不能修改

1. **global**

   在最外层定义的变量为全局变量，函数中可以使用此全局变量，但不能修改,使用global声明后，可以使用和修改，还可以声明新的全局变量

2. **nonlocal** 

   用于外部内嵌函数的变量，只能声明外部函数中的变量，用在闭包中

   ```python
   demo_1():
   	n = 0
   	demo_2():
   		nonlocal n #此时可以在内嵌函数中修改外部函数中n的初值
   		n += 1
   		return n 
   ```

3. **与或非**

   and  两者都为真，则取其后，有一者为假则取0

   'a' and 'b'   return 'b'

   'b' and 'a'   return 'a'

   

   or  两者都为真，则取其前， 有一者为真则取真值

   'a' or 'b'     return 'a'

   'b' or 'a'     return 'b'
   
   
   
4. ^抑或，两者相同时为假，不同时为真，并且满足交换律

   一个数和 0 做 XOR 运算等于本身：a⊕0 = a

   一个数和其本身做 XOR 运算等于 0：a⊕a = 0

   XOR 运算满足**交换律和结合律**：a⊕b⊕a = (a⊕a)⊕b = 0⊕b = b



4. **判断类型**

   `isinstance(object,classinfo)` 与 `type(classinfo)` 类似

     object -- 实例对象。

     classinfo[^classinfo 一定要是确定的类型，不能使用 type(object) 来作比较] -- 可以是直接或间接类名、基本类型或者由它们组成的元组，**<u>classinfo可以是一个类型的元组</u>**

   判断 object 的类型是否与 classinfo 的类型相同，若相同返回 True,否则 False

   ```python
   if isinstance(1,int):
       print('ok')
   # ok
   ```

### 数据类型

Python3 中有六个标准的数据类型：

Number（数字）包括 整型，浮点型，布尔型，复数类型
String（字符串）
List（列表）
Tuple（元组）
Set（集合）
Dictionary（字典）

**不可变对象**：该对象所指向的内存中的值不能被改变

**可变对象**：该对象所指向的内存中的值可以被改变

传引用：在函数中修改参数，原值改变就是传引用

传值：传递副本，

### 问题

**值传递还是引用传递**

- python采用的特殊的传引用的方式，即当传递的参数是可变对象时，那么传递的就是参数的引用，在函数中对列表的修改都会直接影响到原列表，当传递的参数是不可变对象时，实际上传递的也是引用，但是由于不可变对象不可更改，所以只能复制一份，所以参数的重新复制不会影响原对象

1. python不允许程序员选择采用传值还是传引用。Python参数传递采用的肯定是“**传对象引用”**的方式。这种方式相当于传值和传引用的一种综合。如果函数收到的是一个可变对象（比如字典或者列表）的引用，就能修改对象的原始值－－相当于通过“传引用”来传递对象。如果函数收到的是一个不可变对象（比如数字、字符或者元组）的引用，就不能直接修改原始对象－－相当于通过“传值'来传递对象。

2. Python参数传递统一使用的是**引用传递方式**。因为Python对象分为可变对象(list,dict,set等)和不可变对象(number,string,tuple等)，当传递的参数是可变对象的引用时，因为可变对象的值可以修改，因此可以通过修改参数值而修改原对象，这类似于C语言中的引用传递；当传递的参数是不可变对象的引用时，虽然传递的是引用，参数变量和原变量都指向同一内存地址，但是不可变对象无法修改，只能复制一份，所以参数的重新赋值不会影响原对象，这类似于C语言中的值传递。

- 不可变对象（3 个）：Number（数字）、String（字符串）、Tuple（元组）；
- 可变对象（3 个）：List（列表）、Dictionary（字典）、Set（集合）。

不可变对象也是可哈希对象，

```python
a = 3
#{a:3} 哈希就是键值映射
```

***

> 上述总结是我的个人理解，若有不准确的地方，欢迎指正！

:dancer: 现在是2021.6.8，听说马斯克遭到匿名者组织威胁了

[^classinfo 一定要是确定的类型，不能使用 type(object) 来作比较]: 
