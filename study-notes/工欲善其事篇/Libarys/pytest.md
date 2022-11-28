### 单测

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

#### 参数化单测

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

