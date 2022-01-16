#### 使用python类的继承来打补丁

**注意：** 类的继承中私有方法会被命名重整，解决方法一般为在子类中重写私有方法



#### super 的使用

> **super()实际返回的是一个代理的super对象!**
>
> 调用super()这个构造方法时, 只是返回一个super()对象, 并不做其他的操作.
>
> 然后对这个super对象进行方法调用时, 发生的事情如下:
>
> 找到第一个参数的__mro__列表中的下一个直接定义了该方法的类, 并实例化出一个对象
> 然后将这个对象的self变量绑定到第二个参数上, 返回这个对象

```python
class Root:
   def __init__( self ):
     print ( 'Root' )

class A(Root):
   def __init__( self ):
     super ().__init__() # 等同于super(A, self).__init__()
```

**解析：** - [ Python中super()函数简介及用法分享](https://blog.csdn.net/zzsfqiuyigui/article/details/61672631)

1. 在A的构造方法中, 先调用super()得到一个super对象, 然后向这个对象调用init方法
2. 于是就找到了Root, 然后调用Root.\_\_init\__(self), 这里的self是super()的第二个参数, 是编译器自动填充的, 也就是A的\_\_init__的第一个参数
3. 这样就完成对\__init__方法调用的分配



**作用：**

1. [解决菱形继承问题](https://blog.csdn.net/weixin_43866211/article/details/103599843)

   ```python
   # 菱形继承问题
   class A():
       def __init__(self):
           self.a = 1
       def geta(self):
           return self.a
   
   class B(A):
       def __init__(self):
           super(B, self).__init__()
           self.b = 2
       def getb(self):
           return self.b
   
   class C(A):
       def __init__(self):
           super().__init__()
           self.c = 3
       def getc(self):
           return self.c
   
   class D(B,C):
       def __init__(self):
           super().__init__()
           self.d = 4
       def getd(self):
           return self.d
   d = D()
   print(d.getb())
   ```

2. 直接调用父类的方法，属性

   > super() 

   参考：

   

   ```python
   ```

   

