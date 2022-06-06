## Magic Methods 魔术方法

### \__dict__

> **查看属性**

用类名直接调用` __dict__`，返回所有 类方法 与 内置方法；

使用类实例调用 `__dict__`，返回实例对象的所有 属性。

**dir(obj)**

:dancer:  注意，当对象时类对象时，不会输出实例属性，但会输出实例方法

当对象是实例方法时，会输出所有方法，所有属性

### \_\_getattr\_\_ 和 \_\_getattribute\_\_ 

**obj.attr**

1. 首先会在对象的实例属性中寻找，找不到执行第二步 [^ 这里就是调用__getattritute__获取实例属性]

2. 来到对象所在的类中查找类属性，如果还找不到执行第三步

3. 来到对象的继承链上寻找，如果还找不到执行第四步

4. 调用obj.\__getattr__方法，如果用户没有定义或者还是找不到，抛出AttributeError异常，属性查找失败！

```python
class Die(object):
    def __init__(self, sides=6):
        self.sides = sides

    def __getattr__(self, item): # 这里的 item 就是要查找的属性名
        print(f"getattr:{item}")

    def __getattribute__(self, item):
        print(f"getattribute:{item}")


d = Die(sides=3)
print(d.sides) 	# 这里查找的属性 sides 存在
print(d.y) 		# 而这里查找的属性 y 不存在

# getattribute:sides
# None
# getattribute:y
# None
```

#### 应用

1. 访问控制

   ```python 
   class User():
       def __init__(self,*name):
           self.name = name
       def get(self,value):
           return value
           
       # 每次对实例进行赋值操作时，都会调用__setattr__方法，然后将key:value放进__dict__中
       def __str__(self):
           return f'{self.__dict__}'
   
   	# 可以自定义这个对象的 __setattr__方法的行为
       def __setattr__(self, key, value):
           print('*'*38)
         
   ```

   - `__getattr__(self.name)`

     当试图访问不存在的属性时它才会被调用

   - `__setattr__(self,name,value)__` 当企图对这个**对象实例**进行赋值操作时，会触发这个方法

     允许你自定义某个属性的赋值行为，不管这个属性存在与否

   - `__delattr__(self,name)`

     这个魔法方法和 __setattr__ 几乎相同，只不过它是用于处理删除属性时的行为。和 _setattr__ 一样，使用它时也需要多加小心，防止产生无限递归（在 __delattr__ 的实现中调用 del self.name 会导致无限递归）。

### __str__(self)

> 文本,定义对类的实例调用 str() 时的行为。

```python
class User():
    def get(self):
        return 1
    def __str__(self):
        return 'zheshiyige user'
    def __repr__(self):
        return 'asdfsadfaaaaaaaaaaaaaaa'

print(repr(User()))

# 定义对这个对象使用 repr()方法后的行为
# 其他魔法函数也类似
# 像是 __iter__ 方法，可以 iter(object) 调用
```

### `__call__`

> 作用为，使得类的实例对象可以像调用普通函数一样使用 `对象名()` 的形式调用

```python
class A:
    def __call__(self, *args, **kwargs):
        print('调用了call')

A()()

# 调用了call
```

#### **应用**

```python
# 用 __call__() 弥补 hasattr() 函数的短板,hasattr() 方法可以判断对象是否有指定名称的属性或方法，但是无法确认到底是属性还方法
class A:
    def __init__(self):
        self.name = 'tom'
    def age(self):
        return 'jerry'

a = A()
if hasattr(a,'name'):
    print(hasattr(a.name,"__call__"))  # False
if hasattr(a,'age'):
    print(hasattr(a.age,"__call__"))  # True


# 用类实现斐波那契数列
class Fib():
    def __call__(self, *args, **kwargs):
        ret = [1,1]
        num = int(args[0])
        if num == 1:
            return [1,]
        else:
            while len(ret)< num:
                ret.append(ret[-1]+ret[-2])
            return ret

fib = Fib()
print(fib(5))
==>
[1, 1, 2, 3, 5]
```

### \__getitem__

> **Slice对象**和`__getitem__`方法，灵活的使用切片`a[:]`与索引 `a[0]`，使得对象可以应用索引操作

#### **slice对象**

```python
nums = [1,2,3,4]
a = slice(0,3,2)
print(nums[a])

# [1, 3]
```

#### 应用

```python
class CustomerList():
    """一个自定义列表"""
    def __init__(self,nums):
        self.items = nums

    # 这定义了一个可迭代对象
    def __getitem__(self, key):
        # 在对这个对象使用索引时
        if isinstance(key,int):
            return self.items[key]
        # 在对这个对象使用切片时
        if isinstance(key,slice):
            print(key) # 这里可以验证此时的key是一个slice 对象
            return self.items[key]
        else:
            raise TypeError
       
nums = [1,2,3,4]
items = CustomerList(nums)
print(items[1:3:1])

# slice(1, 3, 1)
# [2, 3]

```

### \_\_iter\__ 和 \__next__

> 迭代器 iterator 和 可迭代对象 iterable   [参考](https://www.jianshu.com/p/1b0686bc166d)

```python
# 实现了__iter__方法的对象为可迭代对象

# 实现了__iter__方法和__next__方法的对象为迭代器(同时也是一个可迭代对象)
# 当调用iter函数的时候，生成了一个迭代对象，要求__iter__必须返回一个实现了__next__的对象,如果自身实现了 __next__ 方法，也可以返回自身，这样就可以调用 next()方法
```

### \_\_missing\_\_

> `__missing__(self ,key)`
>
> `__missing__ `在字典的子类中使用，它定义了当试图访问一个字典中不存在的键时的行为（目前为止是指字典的实例，例如我有一个字典 d ， “george” 不是字典中的一个键，当试图访问 d[“george’] 时就会调用 d.__missing__(“george”) ）。



**\__name__**

> if __name__ == "\__main__"

```python
class Student:
    type = "person"
    class_num = 34

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def get_age(self):
        print(self.__class__.__name__)  # 注意这里
        return self.age

    @classmethod
    def get_teacher(self):
        return "Brain"


print(Student.__class__.__name__)  # 注意这里
print("*" * 30)
stu = Student("weihua", 18)
print(stu.__class__.__name__)  # 注意这里
```



**上下文管理器**

> 异步情况下的上下文管理器

**参考：** [(142条消息) asyncio之异步上下文管理器_python学习开发的博客-CSDN博客](https://blog.csdn.net/muzico425/article/details/98920943)

```python
class AContext:
    def __init__(self):
        print("in init")

    async def __aenter__(self):
        print("in aenter")

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("in aexit")


async def main():
    async with AContext() as ac:
        print("in with", ac)


if __name__ == '__main__':
    print("start")
    import asyncio
    asyncio.run(main())
```



### \__slots__

```
# 限制动态添加属性 #init中变量必须在__slots__中
# __slots__ = ('delete')
```



### List 对象及其实现

```python
class FunctionalList:
    '''一个列表的封装类，实现了一些额外的函数式
    方法，例如head, tail, init, last, drop和take。'''

    def __init__(self, values=None):
        if values is None:
            self.values = []
        else:
            self.values = values

    def __len__(self):
        return len(self.values)

    def __getitem__(self, key):
        # 如果键的类型或值不合法，列表会返回异常
        return self.values[key]

    def __setitem__(self, key, value):
        self.values[key] = value

    def __delitem__(self, key):
        del self.values[key]

    def __iter__(self):
        return iter(self.values)

    def __reversed__(self):
        return reversed(self.values)

    def append(self, value):
        self.values.append(value)

    def head(self):
        # 取得第一个元素
        return self.values[0]

    def tail(self):
        # 取得除第一个元素外的所有元素
        return self.valuse[1:]

    def init(self):
        # 取得除最后一个元素外的所有元素
        return self.values[:-1]

    def last(self):
        # 取得最后一个元素
        return self.values[-1]

    def drop(self, n):
        # 取得除前n个元素外的所有元素
        return self.values[n:]

    def take(self, n):
        # 取得前n个元素
        return self.values[:n]
```

展示了如何实现自己的序列。当然啦，自定义序列有更大的用处，而且绝大部分都在标准库中实现了（Python是自带电池的，记得吗？），像 Counter , OrderedDict 和 NamedTuple 。

__slots__[python __slots__ 详解（上篇）_快递小可的博客-CSDN博客](https://blog.csdn.net/sxingming/article/details/52892640)
