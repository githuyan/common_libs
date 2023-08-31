# shell

> shell是一个命令解释器，采用C语言编写，是用户和linux内核沟通的桥梁。它既是一种命令语言，又是一种解释性的编程语言

## 1.基础语法

```shell
# 字符串
a=aaaaaa		# 字符串的赋值
b='bbbbbb'		# 使用单引号赋值，内容就是原生内容，无法容纳变量
c="ccccc ${a} cc'			# 使用双引号赋值 可以引用变量
${ b:1:4 } 		# 字符串切片

# 使用变量的方法
a=111;
echo $a;

# 数组
a=(1 2 3 4) 	# 使用空格分隔元素
# 也可以一次加入多个元素	a=( ` ls -lh ` ) 反括号
${a[0]}			# 可以索引赋值，可以索引取出 都要使用大括号
${ #a[0] }		# 获取a[0]元素的长度
${ #a[@] } 		# 获取数组的长度 	通过 # 获取长度，通过 @ 或 * 获取全部值，
${ a[@]:2 } 	# --> num[2:]
${ a[@]:2:2 } 	# --> num[2,4] 从数组a索引2开始的两个元素 
a=(a[0]=1 a[1]=34)


# 注释
# 单行注释
:<<EOF
	多行注释  EOF 也可以换成 ? 或 '
EOF

echo 	# 输出字符串
\		# 转义 在单引号内不起作用
read name 	# 相当于 input 读取一个输入，并赋值给 name
\c	 	# 不换行
		echo ‘aaaa \c'
		echo   'bbbbb'   return aaaa bbbbb

echo aaa > a.txt		#重定向至文件，若文件不存在就创建一个
```

### 参数变量

#### 位置参数变量

```shell
$n: $0代表命令本身，$1-9代表第1到第9个参数，10个以上参数使用大括号，如${10}
$*: 代表命令行中的所有参数，把所有参数看成一个整体参数
$@: 代表命令行中的所有参数，但是把所有参数看成一个列表，区分对待
$#: 代表所有参数的个数

./demo.sh 111 222
echo $0 $1; # ./demo.sh 111
for i in "$*"; do echo "aa$i"; done # aa111 222
for i in "$@"; do echo "aa$i"; done # aa111; aa222
```



### 特殊字符

#### 括号 [], [[]], (), (()), {}

1. 单小括号 **()**

   常用来命令替换，或用于初始化数组

   ```shell
   $(touch a.txt) 
   
   a=(1 2 3 4)
   ```

2. 双小括号 **(())**

   (( 两个小括号中可以植入数学表达式，不用 -gt))

   ```shell
   sum=0; 
   for((i=1;i<=10;i++)); do sum=$[$sum+$i]; done
   echo sum
   ```

3. 单中括号 **[]**

   作为比较远算符

   ```shell
   if [ -d 'docker-compose.yaml' ] ; then echo aaa ; fi
   # 类似于
   if test -d 'docker-compose.yaml';
   ```

4. 双中括号 **[[]]**

   双中括号比但中括号更加通用，建议比较运算都是用双中括号

   ```shell
   if [[$a != 1 && $a != 2]]; then echo aaa ; fi
   # 等同于
   if [$a != 1] $$ [$a != 1]; then echo aaa ; fi
   ```

5. 大括号 **{}**

   大括号扩展，大括号中不允许空白

   ```shell
   touch {a,b}.txt
   touch {a..c}.txt | touch {a-c}.txt
   # 等同于
   touch a.txt; touch b.txt
   touch a.txt; touch b.txt; touch c.txt
   ```

#### 替换结构

**${var:-string}**

若变量var为空则用string来替换${var:-string}，不为空时，则用 var 来替换

**${var:+string}**

> 跟${var:-string}相反

若变量var不为空时，用string来替换${var:-string}，为空时，则用var 来替换

**${var:=string}**

> 常用来判断赋值并给定默认值

若变量var为空则用string来替换${var:-string}，同时把 string的值赋给变量var，

```shell
echo ${var:=1};
```

**${var?string}** | **${var:?string}**

> **脚本中一条语句报错，只要本条语句正确关闭，就不会影响下面的语句执行，**常用来检查是否设置变量的值

若变量var不为空，则用变量var的值替换${var?string}，若var变量为空，则把string输出到标准错误中，并从脚本中退出

```shell
echo ${var?} #./demo.sh: line 2: var: parameter null or not set
echo ${var?aaa} #./demo.sh: line 2: var: aaa
```



#### 模式匹配

`# 去掉最左边`

`% 去掉最右边`

```shell
${var%pattern} # 使用pattern从左最小匹配var变量的值替换${var%pattern}
${var%pattern} # 使用pattern从左贪婪匹配var变量的值替换${var%pattern}

${var#pattern} # 使用pattern从右最小匹配var变量的值替换${var%pattern}
${var##pattern} # 使用pattern从右贪婪匹配var变量的值替换${var%pattern}
```



**运算**

> test 命令用于检查条件是否成立
>
> if test 1 -gt 3; then echo 1111; fi

| 符号                 | 意义                   | 备注 |
| -------------------- | ---------------------- | ---- |
| -eq                  | ==                     |      |
| -ne                  | !=                     |      |
| -gt                  | >                      |      |
| -ge                  | >=                     |      |
| -lt                  | <                      |      |
| -le                  | <=                     |      |
| -a                   | 与                     |      |
| **-o**               | **或**                 |      |
| !                    | 非                     |      |
|                      |                        |      |
| 以下是文件检查运算   |                        |      |
| -e 文件名            | 文件存在为真           |      |
| -r  文件名           | 文件存在且可读为真     |      |
| -w                   | 文件可写为真           |      |
| -x                   | 文件可执行为真         |      |
| -s	文件名         | 文件存在且不为空为真   |      |
| -d 文件名            | 文件为文件夹为真       |      |
|                      |                        |      |
| 以下是字符串检查运算 |                        |      |
| ==                   |                        |      |
| !=                   |                        |      |
| -z                   | 字符串长度为零则为真   |      |
| -n                   | 字符串长度不为零则为真 |      |

**语句**

> (( 两个小括号中可以植入数学表达式，不用 -gt))
>
> [[双方括号中可以植入匹配表达式，做字符串匹配]]



### **sed 行编辑器**

> 对大文件的操作，尽量避免使用vim直接将文件读取到内存中，应使用行编辑器，减少内存的占用

sed [options] {command} [filename]

- -r 使用扩展的正则表达式
- -！ 取反

常用命令

> 采用/匹配/命令/内容/的格式

- a 尾追加

  ```shell
  # 在data1的每行后追加一行新数据内容: append data "haha"
  [root@www ~]# sed 'a\append data "haha"' data1
  
  # 在第二行后新开一行追加数据: append data "haha"
  sed '2a\append data "haha"' data1
  
  # 在第二到四行每行后新开一行追加数据: append data "haha"
  [root@www ~]# sed '2,4a\append data "haha"' data1
  
  
  # 匹配字符串追加: 找到包含"3 the"的行，在其后新开一行追加内容: append data "haha"
  [root@www ~]# sed '/3 the/a\append data "haha"' data1
  # 这里有些区别，要使用 /匹配/命令\内容 的格式，原格式会多出一个 /
  ```

- 首插入

  ```shell
  # 在data1的每行前插入一行新数据内容: insert data "haha"
  [root@www ~]# sed 'i\insert data "haha"' data1
  
  # 在第二行前新开一行插入数据: insert data "haha"
  [root@www ~]# sed '2i\insert data "haha"' data1
  
  # 匹配字符串插入: 找到包含"3 the"的行，在其前新开一行插入内容: insert data "haha"
  [root@www ~]# sed '/3 the/i\insert data "haha"' data1
  ```

- 替换

- > sed 's/..$//' test.txt	删除每行最后的两个字符

  ```shell
  # 将data1中每行的dog替换为cat
  [root@www ~]# sed 's/dog/cat/' data1
  
  # 将data1中第二行的dog替换为cat
  [root@www ~]# sed '2s/dog/cat/' data1
  
  
  # 匹配字符串替换:将包含字符串"3 the"的行中的dog替换为cat
  [root@www ~]# sed '/3 the/s/dog/cat/' data1
  ```

- 修改

  ```shell
  # 将data1文件中的所有行的内容更改为: change data "data"
  [root@www ~]# sed 'c\change data "haha"' data1
  
  # 将data1文件第二行的内容更改为: change data "haha"
  [root@www ~]# sed '2c\change data "haha"' data1
  
  # 将data1文件中包含"3 the"的行内容更改为: change data "haha"
  [root@www ~]# sed '/3 the/c\change data "data"' data1
  ```

- 字符转换

  > y	转换		sed 'y/abc/ABC/ 源文件  将源文件中的a 装换为A ,b转换为B,c转换为C，数量要相等，

  ```shell
  # 将data1中的a b c字符转换为对应的 A  B  C字符
  [root@www ~]# sed 'y/abc/ABC/' data1
  ```

- 删除

  ```shell
  # 删除文件data1中的所有数据
  [root@www ~]# sed 'd' data1
  
  # 删除文件data1中的第三行数据
  [root@www ~]# sed '3d' data1
  
  # 删除文件data1中包含字符串"3 the"的行
  [root@www ~]# sed '/3 the/d' data1
  ```

- 打印

  ```shell
  # 打印data1文件内容
  [root@www ~]# sed 'p' data1
  
  # 打印data1文件第三行的内容
  [root@www ~]# sed '3p' data1
  
  
  # 打印data1文件包含字符串"3 the"的行
  [root@www ~]# sed '/3 the/p' data1
  ```

命令选项[option]	前面只是在内存中修改，并不会对源文件生效 

-e script 同时使用多条指令

​		sed -e 's/aa/bb/g';'a/fffff' 源文件  两个命令至今用 ; 分割

-f script 将文件中指定的命令添加到处理输入时执行的命令中

​		sed -f filename.sh 源文件 对源文件执行filename文件

-n        抑制自动输出	-n选项经常和 p 配合使用，其含义就是，输出那些匹配的行。

​			sed -n '3s/dog/cat/p' a 只打印到终端第三行的内容，不指定的话会将修改后的文件全部输出到终端

-i        编辑文件内容	对源文件生效

-i.bak    修改时文件并备份旧文件  .bak文件是本次修改之前的文件

-r        使用扩展的正则表达式

!         取反 （跟在模式条件后与shell有所区别）

### **awk**

> 是一个行编辑器，可以截取数据，同时可以完成数据的处理 对输出流的处理
>
> 每一行就是一个记录，每一列是一个字段

**这里的 BEGIN 和 END 都必须大写**

```shell
awk [options] [BEGIN]{program} [END][file]

# 关于awk程序的执行优先级，BEGIN是优先级最高的代码块，是在执行PROGRAM之前执行的，不需要提供数据源，因为不涉及到任何数据的处理，也不依赖与PROGRAM代码块；PROGRAM是对数据流干什么，是必选代码块，也是默认代码块。所以在执行时必须提供数据源；END是处理完数据流后的操作，如果需要执行END代码块，就必须需要PROGRAM的支持，单个无法执行。
```

- -F fs 指定描绘一行中数据字段的文件分隔符  默认为空格
- -f file 指定读取程序的文件名

**awk对字段(列)的提取**

字段提取:提取一个文本中的一列数据并打印输出

字段相关内置变量

$0 表示整行文本

$1 表示文本行中的第一个数据字段

$2 表示文本行中的第二个数据字段

$N 表示文本行中的第N个数据字段

$NF 表示文本行中的最后一个数据字段

```shell
# 读入test每行数据并把每行数据打印出来
[root@www ~]# awk '{print $0}' test 


# 打印test第六个字段 和最后一个字段
[root@www ~]# awk '{print $6，$NF}' test
```

**awk对记录(行)的提取**

记录提取：提取一个文本中的一行并打印输出

记录的提取方法有两种：a、通过行号 b、通过正则匹配

记录相关内置变量

NR: 指定行号

```shell
# 提取test第三行数据,指定行号为3
# 这里的NR也必须是大写
[root@www ~]# awk 'NR==3{print $0}' test 

# 指定第2个字段==3 的行
[root@www ~]# awk '$2=="3"{print $0}' test 
```

**定义变量与数组**

```shell
awk -v name='tom' 'BEGIN{print name}'
awk 'BEGIN{array[0]=100;array[1]=200;print array[0],array[1]}'
# 100 200

v1="Hello"
v2="world"
v3=${v1}${v2} # 这个结果就是简单的叠加
```

**运算**

1. 赋值运算 =

2. 比较运算 > >= == < <= !=

3. 数学运算 + - */ % ** ++ —

4. 逻辑运算 && ||

5. 匹配运算 ~ !~

   ```shell
   awk -v 'count=0' 'BEGIN{count++;print count}'
   ```

**环境变量**

| 变量        | 描述                                                   | 备注                                              |
| ----------- | ------------------------------------------------------ | ------------------------------------------------- |
| FIELDWIDTHS | 以空格分隔的数字列表，用空格定义每个数据字段的精确宽度 | fieldwidths，未知实用                             |
| FS          | 输入字段分隔符号 ，类似 命令选项 -F                    | 但是如果指定 \ / 会出现转义，这类最好使用 -F '/'  |
| OFS         | 输出字段分隔符号                                       |                                                   |
| RS          | 输入记录分隔符                                         |                                                   |
| ORS         | 输出到屏幕上的记录分隔符号                             | awk -F '/' 'BEGIN{ORS="-"}{print $1,$2,$3}' a.txt |

**控制流程**

```shell
# for循环和 C差不多 通过大括号划定范围
awk '{sum=0;for (i=1;i<4;i++){sum+=$i}print sum}' num2.txt

# while循环
awk '{sum=0;i=1;while(sum<150){sum+=$i}print sum}' num2.txt

# do while循环
awk '{sum=0;i=1;do{sum+=$i}while(sum<150);i++;print sum}' num2

# break continue
```

#### 一些实例

1. **打印test文本的行数** 

   ```shell
    awk 'END{print NR}' test
   ```

2. **打印test文本最后一行内容**

   ```shell
   awk 'END{print $0}' test 
   ```

3. **打印test文本列数**

   ```shell
   awk 'END{print NF}' test
   ```



### 命令行工具

#### xargs



