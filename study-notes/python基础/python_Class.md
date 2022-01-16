> 描述同一类事物的不同属性的集合,用函数表现这些属性的具体特征，类就是对象，构建类就可以称之为建模

## [规范](https://blog.csdn.net/u014636245/article/details/98856454)

1. 类名要尽量使用驼峰式英文命名，要见名知意，

2. 类的注释，使用三个双引号，描述类的功能，作用，复杂的类还要写明类的细节，属性的声明

   类的每一个方法（函数)也需要注释，标明返回类型

## 类的基本应用

> 创建一个类实例对象

```python 
class Test():
    """这是是一个测试类"""
    class_variable = 100
    def __init__(self,value):
        self.case_veriable = value
```



### 类和实例的区别

> 在异步情况下更加明显，实例时独立的，而类是共享的，在异步时，类的改变可能会影响到其他正在执行的任务，而实例不会



### 类的继承

> 类的继承体现了python的复用性
>
> 在不影响原有业务的情况下，扩展业务功能，或者复用第三方库相关模块，继承后重新改写最为便捷

**继承时常见的问题：**

- 私有方法，私有属性

  > 直接将父类的私有部分拉出来，在子类中实现

- 抽象类的继承问题 - [抽象类](###抽象类)

  > 抽象类无法实例化，只能被继承，且子类必须实现抽象类的方法

```python
class Parent():
    """这是父类"""
    class_variable_parent = 0
    def __init__(self):
        self.value = 1
    def head(self):
        self.value = 2
    def __hide__(self):
        self.value = 3

class Child(Parent):
    """这是一个这是子类"""
    class_variable_child = 100
    def __init__(self,value):
        super().__init__() # 类的继承
        self.case_veriable = value
	def head(self):
        self.value = 101
```

多态：子类继承父类，子类可以调用父类所有的普通方法，属性，当父类和子类拥有一个同名的方法时，子类的方法将覆盖父类的方法

### 关于类中的单下划线和双下划线

- `_privete` 单下划线开头被认为是内部私有,import 时不会导入这样的方法

- `__privete` 双下划綫的属性，方法，变量，在类的继承时，会出现名称重整，即这个类作为父类被继承时，这个类的属性，不会被覆盖,这个就是私有变量

  ```python
  class Test():
      def __init__(self):
      self.__private = 1
      def __private():
          pass
      def common():
          pass
  class New(Test):
      def __init__(self):
          super().__init__()
      def __private():
          pass
      def common():
          pass
  # 根据多态，common 方法会被 New 类的 common 方法覆盖，但是由于双下划綫的名称重整，在 New 类中依然保有以双下划綫开头的属性，方法，只是名称默认改为，self._new__private 和   _New__private()
  
  # 可以看到原本的双下划綫父类私有变量，再被继承后变为了单下划线变量，这意味着
  ```

- `private_ ` 单下划线结尾，可以用来避免与保留字名称冲突		

### 关于python的私有变量

```python
class Test():
    def __init__(self):
        self._com = 3
        self.__priv = 4

    def _common(self):
        print(222222222)

    def __private(self):
        print(111111)

t = Test()

t._com = 13
t.__priv = 14
print(t._com)  	# 13
print(t.__priv) # 14 这里可以看到 即使是私有属性，依然可以修改，虽然在编译器上会标红

# 这里私有方法完全无法调用，直接报错
t._common()  	# 222222222
t.__private() 	# AttributeError: 'Test' object has no attribute '__private'
```

### 类方法与静态方法

- 类方法绑定在类上，而不是绑定在实例上，需要传入一个类参数,他可以通过类直接调用，而不用实例
- 静态方法与类方法相似，它相当于一个普通函数

```python 
class Test:
    name = 1
    def __init__(self):
        self.__private = 1
    # 普通方法
    def add(self):
        return 1
    # 静态方法
    @staticmethod
    def static_add():
        return  2
    # 类方法
    @classmethod
    def class_add(cls):
        return cls.static_add() + 1

print(Test.static_add()) # 2
print(Test.class_add()) # 3
print(Test.add()) # 会报错，因为没有初始化，没有实例化这个类
# 静态方法和类方法就是类本身的属性，不用实例化这个类，就还可以调用修改，就像一个西红柿，不用烹饪，接受品尝这个动作机会响应甜味，但只有加盐烹饪成一道菜（初始化为一个实例），才能响应咸味。
```

2. **类变量与实例变量**

   > 类变量：类变量有所有实例共享

   > 实例变量：由每个实例私有，不同实例之间不影响
   >
   > 当我们需要一个类的不同实例之间共享变量的时候使用类变量

   1. 类与实例 [参考](https://blog.csdn.net/lrs1353281004/article/details/81612390)

      ```python
      class Node():
          def __init__(self, val):
              self.val = val
              self.next = None
      case = Node() # case 是 Node 类的一个实例
      node = Node	# node 直接就是 Node 本身
      print(case) # <__main__.Node object at 0x000001C6F8A212E8>
      print(node) # <class '__main__.Node'>
      ```

   2. 类变量与实例变量 [参考](https://blog.csdn.net/chb4715/article/details/79104376)

      ```python
      class Node():
          class_variable = 100
          def __init__(self, val):
              self.val = val
              self.next = None
      a = Node()
      # 类变量的访问， 	a.class_variable 或者 Person.class_variable
      # 实例变量的访问  	a.val
      # 可以在类定义之后创建类变量 Node.name = Tree，这个增加的属性，以后创建的所有实例都能访问
      # 而实例变量只能只能修改已创建的类的实例变量
      ```

3. 实例方法，类方法，静态方法

   **类方法：**

   > 表现形式，由 classmethod 装饰，第一个参数为类本身，使得它可以调用类变量

   **静态方法：**

   > 表现形式，由 staticmethod 装饰，与普通方法没有区别，与类方法一样无法使用实例变量，可以使用 class.mehtod 调用

   **实例方法：**

   > 表现形式，必须实例化才能调用，第一个参数为 实例对象本身，他可以调用 实例变量和类变量

### 抽象类

> 抽象类实例   作用统一规范接口，降低使用复杂度。
>
> 使用 issubclass(Parent,child) 可以判断是否子类

#### 使用

> 抽象类无法被被实例化，只能作为基类被继承，且子类必须实现被抽象类定义的方法

1. 抽象类无法被被实例化，只能作为基类被继承
2. 继承抽象类的子类**在实例化时**，必须定义抽象类的<u>抽象方法</u>[^被abc.abstractmethod装饰的方法 ]

注意：

	1. 只使用抽象方法装饰器没用
	2. 继承抽象类的子类，可以正常调用子类的类方法

```python
import abc

class Base(metaclass=abc.ABCMeta):
    def get(cls):
        return 1

    @abc.abstractmethod
    def post(self):
        return 3

class Demo(Base):
    @classmethod
    def post(cls):
        return 2

    @classmethod
    def put(cls):
        return 4
d = Demo()
return d.post()    # TypeError
return Demo.post() # 2
```

#### 调用抽象类的方法

**调用普通方法**

抽象类无法实例化，但是可以类方法可以被正常调用

**调用抽象方法 ** ？？

> 在子类中调用抽象类的抽象方法，需要使用 super() 



### 元类

> 用来拦截和修改类的创建，元类常用于继承时

```python
class Meta:
    app = 'weike_slave'
    table = 'liveroom_channelaccess'
```





## 类的高阶

1. 自定义方法 - 魔法函数

   ```python
   import datetime
   import db
   
   class BaseModel():
       """一个简单地数据库基类函数"""
       __tablename__ = 'abstract' 
       id = db.Colume(db.Integer,primary_key=True,nullable=False)
       
   class OtherModel(BaseModel):
       __tablename__ = 'other'
       datetime = db.Colume(db.Datetime,default=datetime.now)
       def __str__(self):
           return self.__tablename__ # 通过 __str__ 自定义输出
   base = BaseModel()
   other = OtherModel()
   print(base) # <__main__.BaseModel object at 0x000001C97F29A400>
   print(other) # other
   ```

2. 创建可管理的属性

   > 通过访问的不同方式，自动触发不同的操作，有点像是前后端分离中Restful风格写法，整合了形式，避免了为了一个很小的功能就需要编写一个相关函数

   ```python
   class A():
       """
       一个测试类，没有实际意义
       :param
       所谓的属性，其实就是 getter,setter, deleter方法的集合
       当调用  A().firstname 时会返回 self.a 这底层其实是一个 getter 方法
       对变量赋值，其实就是调用了变量对象的 setter 方法
       """
       def __init__(self):
           self. a = 0
       @property # 默认是 getter方法
       def first_name(self):
           return self.a
       @first_name.setter # 
       def first_name(self,value):
           self.a = value+self.a
       @first_name.deleter
       def first_name(self):
           raise TypeError
   a =A()
   print(a.first_name) # 0 
   a.first_name = 100
   print(a.first_name) # 100
   del a.first_name
   print(a.first_name)
   ```

---

> 上述总结是我的个人理解，若有不准确的地方，欢迎指正！

:dancer: 现在是2021.6.11,最近的骇客可有点上头了啊，先是勒索了美国最大的石油公司，有勒索了全球最大肉食品加工企业，还都成功了！离谱，

[###抽象类]: 
