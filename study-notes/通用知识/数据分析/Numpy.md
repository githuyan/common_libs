## numpy

> 在python中做科学计算的基础库，重在数值计算，也是大部分python科学计算库的基础库，多用在大型多维数组中执行数值运算

### 数组属性

size 数组的大小

ndim 数组维度

shape 返回一个元组，表示array的维度

dtype 数组中的数据类型

### 创建数组

1. **基础创建**

   arrange()  相当于range 

   zeros(shape=(2,3,3)) 这是一个有着两个数组，每一个数组都是三行三列的二维数组 ，组成的三维数组

   zeros() 创建数组元素全为零的数组

   ones() 创建数组元素全为1的数组

   empty() # 创建数组元素全为随机值的数组，注意**这个随机值不能用，单独查询这个位置的元素其实是为空的**

   new.empty_like(b) # 创建一个b形状的新数组，数组中的内容用随机值填充

   full() 创建数组元素为指定值的数组

2. **创建一个数组形状为shape的，指定填充全为3的，每个元素类型为'int8' 多维数组**

   np.full(shape=(2,3,3),fill_value=3,dtype='int8')

   np.full(3)

3. **创建一个三行三列的 由0-1随机数填充的数组**

   np.random.randn(3,3)

   np.random.randint(2,9,10) 等同于 python的random.randint(start,end,num)

4. **创建一个8个元素的一维数组**

   a = np.arrange(8) 

   b = a.reshape(2,4) 将这个一位数组转化为一个2行4列的二维数组

### 访问数组

#### 切片

> 切片 中numpy的切片是完全的浅复制，切片的访问会修改原来的数组

一维数组的访问和python并没有区别

二维数组的访问

```python
import numpy as np
a = np.arange(12).reshape(3,4)
print(a)
# [[ 0  1  2  3]
#  [ 4  5  6  7]
#  [ 8  9 10 11]]
```

**筛选**

a[2,3] 选择第2行第3列的元素

a[:2,:3] 选择前2行，前3列的元素

a[2,:3] 选择第2行，前3列的元素

a[:,3] 选择所有行，前3列的元素



#### **神奇索引**

**一维数组的访问**和python并没有区别

**二维数组的访问**

```python
import numpy as np

a = np.arange(12).reshape(3,4)
a[2,3] == a[2][3]
a[:2,:3]  == [ a[0][:2],a[1][:3] ]
```

**直接对数组操作**

a =np.arange(5)

a+1  即对所有的元素+1

a[a>4] += 10  所有>4的元素自增加10

print(a>5)  [False False False  True  True]

```python
import numpy as np

nums = np.random.randint(0,9,12).reshape(3,4)
print(nums)
print(nums[nums>4]) # 相当于遍历数组，返回符合条件的列表
# [[6 6 7 8]
#  [0 3 4 3]
#  [4 5 2 8]]

# [6 6 7 8 5 8]
```

**整数数组索引**

```python
import numpy as np

a = np.array([[1,2], 
              [3, 4], 
              [5, 6]])
# 相当于使用 zip 方法定义了这个二维数组的索引
a[[0, 1, 2], [0, 1, 0]] == [a[0][0],a[1][1],a[1][0]]
```

**bool值索引**

```python
import numpy as np

a = np.array([[1,2], [3, 4], [5, 6]])

b_index = a>3
print(b_index)
# 返回的是每个值对于条件的bool
# [[False False]
#  [False  True]
#  [ True  True]]

#使用这个bool索引
new = a[b_index]
print(new)
# 返回的是满足这个bool值索引的所有值
# [4 5 6]
```

**数据类型**

```python
import numpy as np

x = np.array([1, 2])   # 创建一个数组，默认类型为int64
print(x.dtype)         # Prints "int64"

x = np.array([1.0, 2.0])   # 创建一个数组，类型为 float64
print(x.dtype)             # Prints "float64"

x = np.array([1, 2], dtype=np.int64)   # 显式的指定数组中元素的数据类型
print(x.dtype)                         # Prints "int64"
```

### 数组的计算

```python
import numpy as np

x = np.array([[1,2],[3,4]], dtype=np.float64)
y = np.array([[5,6],[7,8]], dtype=np.float64)

# 这两种方式的作用是一样的
# 加
print(x + y) 		# [[ 6.0  8.0]
print(np.add(x, y)) #  [10.0 12.0]]

# 减
print(x - y)
print(np.subtract(x, y))

# 除
print(x/y)
print(np.divide(x,y))

# 开方
# print(np.sqrt(x))

# 乘
# 元素对应相乘
print(x.dot(v))
print(np.dot(x, v))

# 由于两个数组的元素数量不一致，无法对应相乘，
# 会是[sum([x[0][0]*y[0],x[0][1]*y[0]]),sum([x[0][0]*y[1],x[0][1]*y[1]])] 这样的一个一维数组
print(x.dot(y))
print(np.dot(x, y))
```

**计算方法**

```python
import numpy as np

# 创建一个二维数组
x = np.array([[1,2],[3,4]])

print(np.sum(x))  # 求所有元素的值
print(np.sum(x, axis=0))  # 在二维数组的情况下，== 行对应求和，
print(np.sum(x, axis=1))  # 列对一个求和
```

**数组的变换方法**

```python
import numpy as np

x = np.array([[1,2], [3,4]])
print(x)    # Prints "[[1 2]
            #          [3 4]]"
# 转置数组
print(x.T)  # Prints "[[1 3]
            #          [2 4]]"


v = np.array([1,2,3])
print(v)    # Prints "[1 2 3]"
print(v.T)  # Prints "[1 2 3]"
```

### 一些方法

np.arange(4,9,2) 就像是 range(4,9,2) 

#### 例子

```python
import numpy as np

# 指定类型，默认为int32"
a = np.array(range(19),dtype="int8")
print(a) # [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18]
print(a.dtype) # <class 'numpy.int8'>
b = np.array([1,0,2,0],dtype="bool")
print(b) # [ True False  True False]

# 修改类型 这里又会被转回来
c = b.astype('int32')
print(c) # [1 0 1 0]
print(c.dtype) # int32
```



## 常用方法

##### 嵌套数组扁平化

**参考：**

- [[Numpy中扁平化函数ravel()和flatten()的区别](https://www.cnblogs.com/mzct123/p/8659193.html)](https://www.cnblogs.com/mzct123/p/8659193.html) 

```python
from numpy import *

a = arange(12).reshape(3, 4)

print(a)
# [[ 0  1  2  3]
# [ 4  5  6  7]
# [ 8  9 10 11]]

print(a.ravel())
# [ 0  1  2  3  4  5  6  7  8  9 10 11]

print(a.flatten())
# [ 0  1  2  3  4  5  6  7  8  9 10 11]
```
