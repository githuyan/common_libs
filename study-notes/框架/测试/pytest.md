## Pytest

### 指定测试结果漏斗

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

### 分析测试执行持续时间

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

