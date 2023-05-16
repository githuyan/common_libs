## Pytest

**参考：**

1. [Pytest - 使用介绍](https://www.jianshu.com/p/a754e3d47671) 

```python
class TestClutter():
    @classmethod
    def setup_class(cls):
        print("测试类前置方法---setup_class---")

    @classmethod
    def teardown_class(cls):
        print("测试类后置方法---teardown_clas---")

    def test_string_format(self):
        assert 1 == 1

def setup_module(module):
    print("测试模块的前置方法---setup_module---")


def teardown_module(module):
    print("测试模块的前置方法---teardown_module---")


if __name__ == '__main__':
    import pytest
    pytest.main()
```

### 参数化单测

```python
# @pytest.mark.parametrize(["data","param"], [({"a":23,}, "444dd"), (33, 44)]) 或者
@pytest.mark.parametrize("data,param", [({"a":23,}, "444dd"), (33, 44)])
def test_demo(self, data, param):
    print(data, param)
    print('----')

# 每一个元祖都是一次完整的参数传递
.{'a': 23} 444dd
----
.33 44
----
```



### fixture的scope参数

scope参数有四种，分别是'function','module','class','session'，默认为function。

- function：每个test都运行，默认是function的scope
- class：每个class的所有test只运行一次
- module：每个module的所有test只运行一次
- session：每个session只运行一次



### setup和teardown操作



### 通过pytest.mark对test方法分类执行

```python
@pytest.mark.website
def test_1(setup_function):
    print('Test_1 called.')
 
# 执行所有带有websit标记的参数
pytest  -v -m "website" pytest1.py
# 执行所有不带带websit标记的参数
pytest  -v -m "not website" pytest1.py
```



### 命令行操作



#### 指定测试结果漏斗

```python
以下是可用字符的完整列表：
f -失败
E -误差
s 跳过
x -失败
X -XPASS
p 通过
P -通过输出

用于（取消）选择组的特殊字符：
a - all except pP
A -所有
N -无，这不能用来显示任何内容（因为 fE 是默认设置）


pytest -rfs  # 只查看失败和跳过的测试

```

#### 分析测试执行持续时间

```python
要获取长度超过1.0秒的最慢10个测试持续时间的列表：
默认情况下，pytest不会显示太小的测试持续时间（<0.005s），除非 -vv 在命令行上传递。
pytest --durations=10 --durations-min=1.0 -vv

########
(debug) D:\Work\debug>pytest demo.py  --durations=10 --durations-min=1.0 -vv
============================================= test session starts =============================================
platform win32 -- Python 3.8.13, pytest-7.1.2, pluggy-1.0.0 -- D:\Tools\Anaconda\anaconda\envs\debug\python.exe 
cachedir: .pytest_cache
rootdir: D:\Work\debug, configfile: tox.ini
collected 3 items

demo.py::test_aaa PASSED                                                                                 [ 33%]
demo.py::TestAAA::test_one PASSED                                                                        [ 66%] 
demo.py::TestAAA::test_two PASSED                                                                        [100%] 

============================================== warnings summary =============================================== 
..\..\Tools\Anaconda\anaconda\envs\debug\lib\site-packages\_pytest\config\__init__.py:1198
  D:\Tools\Anaconda\anaconda\envs\debug\lib\site-packages\_pytest\config\__init__.py:1198: PytestRemovedIn8Warni
ng: The --strict option is deprecated, use --strict-markers instead.
    self.issue_config_time_warning(

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
============================================ slowest 10 durations ============================================= 
1.01s call     demo.py::test_aaa
0.00s setup    demo.py::TestAAA::test_one
0.00s setup    demo.py::test_aaa
0.00s teardown demo.py::test_aaa
0.00s call     demo.py::TestAAA::test_one
0.00s teardown demo.py::TestAAA::test_one
0.00s setup    demo.py::TestAAA::test_two
0.00s teardown demo.py::TestAAA::test_two
0.00s call     demo.py::TestAAA::test_two
======================================== 3 passed, 1 warning in 1.06s ========================================= 
```



### 插件 pytest-mock

**例子：**

- https://github.com/changhsinlee/pytest-mock-examples

```python
#用法 mocker将slow_funciton操作中位于mock_examples.main路径下的api_call函数的值做了修改

def slow_function():
    api_result = api_call()
    # do some more stuff here
    return api_result

def test_slow_function_mocked_api_call(mocker):
    mocker.patch(
        'mock_examples.main.api_call',
        return_value=5
    )

    expected = 5
    actual = slow_function()
    assert expected == actual

```

