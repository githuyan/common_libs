### 一个类Django-orm的SQL拼接工具

> SQL拼接工具(sql_concat)是一个用于生成SQL查询条件语句和参数的Python模块，其主要目的是简化与复用构建原生SQL时查询条件的拼接
>
> 源码：https://gitee.com/githuyanhu/common_libs.git



在一个接口中如果出现了大量的筛选条件需要拼接到SQL去筛选、分页，就会写出大量需要`if`的代码，我个人排斥代码中出现大量的`if`，类似于下面这样：

```python
sql = "select id from user left join class on user.id=class.student_id where "

if age:
    sql += f" age>{age}"
...
```

当条件较多时，整体的简洁性和复用性会随之下降。

#### 1 功能描述

> sql_concat提供了一个名为`db_condition_parse()`的函数，接受一个字典作为输入，该字典描述了SQL查询条件的各个方面。该函数解析字典并生成相应的SQL查询条件和参数。

支持全等(=)、不等(!=)、大于(>)、大于等于(>=)、小于(<)、小于等于(<=)、左模糊匹配(LIKE)、右模糊匹配(LIKE)、全模糊匹配(LIKE)、 在列表(IN)、不在列表(NOT IN)、空值判断(IS NULL, IS NOT NULL)、分页限制(LIMIT)、位移(OFFSET)、排序(ORDER BY)、分组(GROUP BY)、 与操作(AND)、或操作(OR)、自定义操作(eg: 支持在任意位置添加任意数量的自定义操作

支持自定义全局钩子，对每一个条件自定义处理函数， 默认开启SQL预处理钩子，支持参数替换(%s)，参数采集

支持字段类型精确匹配的索引操作，（eg: 在SQL预处理中，由于存在参数替换，可能不支持精确索引匹配）

支持空值判断，目前只有 0、False被认为有效空值，其他空值如[], (), {}, None 在AND流程中舍弃当前上下文流程块全部条件，在OR流程中舍弃当前条件

默认支持四个语法糖，>=, <=, ~, %



#### 2 核心代码实现

```python
def general_process_layer(flag: str, value: Any, **kwargs) -> Tuple[str, Any]:
    """
    通用流程处理层
    获取每一个条件对应的处理函数，并执行调用，响应最终结果，本层可以获取每一个条件最终函数执行之前的所有元数据，可以在本层对所有条件进行全局处理，如SQL防注入预处理,参数采集

    :param flag: 所支持的所有操作的前缀或后缀标识字符串 [
            "__end", "__gt", "__lt", "__e", "__gte", "_lte", "__ne", "__ll", "__rl", "__al", "__in", "__not_in",
            "__limit", "__offset", "__group_by", "__order_by", "__and", "__or", "__extra"
            ]
    :param value: 参数值
    :return: str 一个拼接字符串

    >>> params = ("age__gt", 100)
    >>> general_process_layer(*params)
    ("__gt", "age<100")

    >>> params = ("user.id__in", [1, 2, 3])
    >>> general_process_layer(*params)
    ("__in", "user.id IN (1,2,3)")

    >>> params = ("__order_by", {"created_at": True})
    >>> general_process_layer(*params)
    ("__order_by", "ORDER BY created_at ASC")
        ...
    >>> params = ("__offset", 10)
    >>> general_process_layer(params)
    ("__limit", "OFFSET 10")
    """
    # 通用操作处理
    flag_func, flag_sign, field, flag_value = meta_condition_map(flag, value)
    meta_cond = MetaCondition(flag=flag, flag_func=flag_func, flag_sign=flag_sign, field=field, flag_value=flag_value, value=value)

    # 自定义处理钩子
    meta_cond, kwargs = execute_hook(meta_cond, **kwargs)

    return meta_cond.flag_sign, meta_cond.flag_func(field=meta_cond.field, value=meta_cond.flag_value, **kwargs)

def condition_parse(flag: str, value: Dict, **kwargs) -> Tuple[str, Any]:
    """
    条件解析
    会将字典结构视为一个“块”，每一个块中可以包含若干个条件与值的映射{condition: value}，解析每一个条件，拼接为字符串

    :param flag: 所支持的所有操作的前缀或后缀标识字符串
        -> flag in [
            "__end", "__gt", "__lt", "__e", "__gte", "_lte", "__ne", "__ll", "__rl", "__al", "__in", "__not_in",
            "__limit", "__offset", "__group_by", "__order_by", "__and", "__or", "__extra"
            ]
    :param value: 参数值
    :return: Tuple[str, str] (条件标识, 参数拼接结果)

    >>> params = {"flag": "__end", "value": {"name": "ttt", "age__in": [1, 2]}
    >>> condition_concat(params)
    ("__end", "name='ttt' AND age IN (1,2)")

    >>> params = {"flag": "__gt", "value": {"age__lt": "10"}
    >>> condition_concat(params)
    ("__gt", "age<10")

    >>> params = {"flag": "__order_by", "value": {"created_at": True}}
    >>> condition_concat(params)
    ("__order_by", "ORDER BY created_at ASC")

    >>> params = {"flag": "__and", "value": {"age__lt": 10, "name": "ttt"}
    >>> condition_concat(params)
    ("__and", "age<10 AND name='ttt'")

    >>> params = {"flag": "__or", "value": {"age__lt": 10, "name": "ttt"}
    >>> condition_concat(params)
    ("__or", "(age<10 OR name='ttt'")

    >>> params = {"flag": "__extra", "value": "group by name,id"}
    >>> condition_concat(params)
    ("__extra", "group by name,id")
    """
    # 特殊操作处理，用于解析值为字典的操作
    if flag in extra_dict_condition_map:
        return general_process_layer(flag, value, **kwargs)

    conditions_str = []
    for sub_flag, sub_value in value.items():
        # 当字段值为流程块（字典）时，进入流程块，递归处理每一条条件参数
        if isinstance(sub_value, Dict):
            sub_condition = condition_parse(sub_flag, sub_value, **kwargs)
            conditions_str.append(sub_condition)
            continue

        # 普通对应参数处理
        flag_sign, flag_value = general_process_layer(sub_flag, sub_value, **kwargs)
        conditions_str.append((flag_sign, flag_value))

    return general_process_layer(flag, conditions_str, **kwargs)
```

##### 2.1 扩展条件处理

```python
def _order_by(value: Union[str, List[Union[Dict[str, Union[bool, OrderEnum]]]]], **kwargs) -> str:
    """
    SQL排序操作处理
    支持单值（单值时默认值为倒序 DESC）、多值排序，支持使用布尔值(True, False, 0, 1)或者枚举值（DESC, ASC, desc, asc）去定义指定字段的排序方式

    :param value: 一个列表，元素为一个字典或者一个二值元祖
    :return: str 一个拼接字符串

    >>> value = [("created_at", True), ("deleted_at", "desc")]
    >>> _order_by(value)
    "ORDER BY created_at ASC, deleted_at DESC"

    >>> value = [{"created_at": True, "deleted_at": "desc"}]
    >>> _order_by(value)
    "ORDER BY created_at ASC,deleted_at DESC"

    >>> value = "created_at"
    >>> _order_by(value)
    "CREATED_AT ASC"

    todo
        1. 类型定义校验不够完善，缺少二值元组校验
        2. 错误捕获不够优雅
    """
    if isinstance(value, (List, Tuple)):
        values = []
        for k, o in value:
            if isinstance(o, bool):
                values.append((k, OrderEnum.ASC.value if o else OrderEnum.DESC.value))
                continue
            values.append((k, OrderEnum.ASC.value if str(o).upper()==OrderEnum.ASC.value else OrderEnum.DESC.value))
    elif isinstance(value, dict):
        values = []
        for k, o in value.items():
            if isinstance(o, bool):
                values.append((k, OrderEnum.ASC.value if o else OrderEnum.DESC.value))
                continue
            values.append((k, OrderEnum.ASC.value if str(o).upper()==OrderEnum.ASC.value else OrderEnum.DESC.value))

    elif isinstance(value, str):
        values = [(value, OrderEnum.DESC.value)]
    else:
        raise ValueError("order by 解析参数类型错误！")

    clauses = [f"{v[0]} {v[1].upper()}" for v in values]

    return f"ORDER BY {string_concat(clauses, separator=',')}"

def _limit(value: Union[int, List[int], Tuple[int]], **kwargs) ->str:
    """
    SQL数量限制操作处理
    支持使用单值，多值定义

    :param value: int|str单值， 二值元组或者二值列表
    :return: str 一个拼接字符串
    >>> value = 1
    >>> _limit(value)
    "LIMIT 1"

    >>> value = [1, 2]
    >>> _limit(value)
    "LIMIT 1,2"

    todo
        1. 类型定义校验不够完善，缺少二值元组校验
    """
    strict_mode = kwargs.get("strict_mode", True)
    value = value if isinstance(value, (List, Tuple)) else [value]
    return f"LIMIT {string_concat(value, separator=',', strict_mode=strict_mode)}"

def _or(value: List[Tuple[str, str]], **kwargs) -> str:
    """
    OR流程处理函数
    使用SQL中的OR处理一个流程块

    :param value: 流程块列表
    :return: str 一个拼接字符串

    >>> value = [("__end", "age=1"), ("__is_null", "created_at IS NOT NULL")]
    >>> _or(value)
    "(age=1 OR created_at IS NOT NULL)"

    >>> value = [("__end", "age=1"), ("__is_null", "created_at IS NOT NULL"), ("__limit", "LIMIT 10")]
    >>> _or(value)
    "(age=1 OR created_at IS NOT NULL LIMIT 10)"
    """
    value = list(filter(lambda x: x[1], value))
    if not value:
        return ""

    strs = string_concat(reduce(or_operation_concat, value)[1])

    # 优化结构，当有多个元素时，才需要括号包裹，单个元素不需要括号包裹，对整体结构来说更加清晰
    if len(value) > 1:
        strs = string_concat(strs, boundary=("(", ")"))
    return strs
```



##### 2.1 钩子函数处理

```python
class HookFunc(object):
    """
    单条件处理钩子
    支持对每一个条件进行自定义处理，默认启用SQL预处理钩子，并收集参数
    """

    @staticmethod
    def __default_hook(meta_cond: MetaCondition, **kwargs):
        """
        默认空钩子
        可以用于处理找不到指定钩子时的额外操作

        :params meta_cond: MetaCondition类型，当前处理条件的所有可用信息
        :params kwargs: 自定义参数

        :return: 统一响应 meta_cond, kwargs
        """
        # 给定一个钩子报错等级，指定未找到预期钩子时是跳过还是报错, 默认报错。
        if kwargs.get("hook_error", True):
            raise TypeError("预期钩子未找到！")

        return meta_cond, kwargs

    @staticmethod
    def sql_preprocess_hook(meta_cond: MetaCondition, **kwargs):
        """
        SQL预处理钩子
        支持参数替换 %s，并收集参数

        :param meta_cond: MetaCondition类型，当前处理条件的所有可用信息
        :params query_params: 参数收集容器
        :params strict_mode: 关闭拼接操作严格模式，严格模式下字符串"'"也会被转义，而所有替换字符"%s"都是字符串，无需转义

        :return: 统一响应 meta_cond, kwargs
        """

        # SQL预处理时，需要收集参数的条件类型
        collection_params_operations = {
            "__end", "__gt", "__lt", "__e", "__gte", "_lte", "__ne", "__ll", "__rl", "__al", "__in", "__not_in",
            "__limit", "__offset"
        }

        # SQL防注入预处理,参数采集
        if meta_cond.flag_sign in collection_params_operations:
            # todo 多值与单值无法明确判断
            kwargs.setdefault("query_params", [])
            kwargs["query_params"].extend(meta_cond.value if isinstance(meta_cond.value, (list, tuple)) else [meta_cond.value])
            kwargs["strict_mode"] = False
            flag_value = ["%s" for _ in range(len(meta_cond.value))] if isinstance(meta_cond.value, (list, tuple)) else "%s"

            meta_cond.flag_value = flag_value

        return meta_cond, kwargs

    @staticmethod
    def null_handle_hook(meta_cond: MetaCondition, **kwargs):
        """
        空值处理钩子
        支持自定义参数值为空时的处理， 0、False被视为有效空值，拼接时正常处理，
        如 age=0, ORDER BY created_at DESC ,
        如 [], {}, (), None之类的被视为无效空值，此条件失效，不再参与拼接

        :param meta_cond: MetaCondition类型，当前处理条件的所有可用信息
        :return: 统一响应 meta_cond, kwargs

        """
        # fixme break时，参数捕获错误，在and拼接流程中，其中一个条件失效则本流程全部条件失效，or拼接流程中，其中一个条件失效不影响其他条件
        def _default_flag_func(**kwargs):
            return None

        if meta_cond.value or meta_cond.value == 0 or meta_cond.value is False:
            return meta_cond, kwargs
        elif meta_cond.value is None:
            meta_cond.flag_sign = ""
            meta_cond.flag_value = ""
        else:
            meta_cond.flag_sign = None
            meta_cond.flag_value = None
            meta_cond.flag_func = _default_flag_func

        return meta_cond, kwargs

    @classmethod
    @abc.abstractmethod
    def execute(cls, meta_cond: MetaCondition, **kwargs):
        """
        自定义钩子执行入口
        支持对每一个条件进行自定义处理，默认启用SQL预处理钩子，并收集参数

        :params meta_cond: MetaCondition类型，当前处理条件的所有可用信息
        :params user_hook: 是否启用钩子的开关，默认启用钩子
        :params hook_error: 是否捕获钩子报错，指定未找到预期钩子时是跳过还是报错, 默认报错
        :params hooks: List 钩子列表， 默认启用SQL预处理钩子 hooks=["sql_preprocess_hook"]
        :params kwargs: 自定义参数

        :return: 统一响应 meta_cond, kwargs
        """
        # 默认启用钩子
        if not kwargs.get("use_hook", True):
            return meta_cond, kwargs

        # 默认启用SQL预处理钩子
        kwargs.setdefault("hooks", ["null_handle_hook", "sql_preprocess_hook"])
        # 支持使用多个钩子，执行顺序按照默认顺序
        for hook in kwargs.get("hooks", []):
            execute_hook_func = getattr(cls, hook, cls.__default_hook)
            meta_cond, kwargs = execute_hook_func(meta_cond, **kwargs)

        return meta_cond, kwargs

def execute_hook(meta_cond: MetaCondition, **kwargs) -> Tuple[MetaCondition, Dict]:
    """
    执行自定义钩子
    自定义钩子错误抛出异常

    :param meta_cond: MetaCondition类型，当前处理条件的所有可用信息
    :return:

    """
    # 自定义处理钩子
    hook: HookFunc = kwargs.get("hook_func", HookFunc)
    if isinstance(hook, HookFunc) and hasattr(hook, "execute"):
        raise TypeError("钩子类型错误！")
    return hook.execute(meta_cond, **kwargs)
```





#### 3 demo



```python
# 不进行参数预处理
conds = {
    "name": "Alice",
    "age__gt": 18,
    "__and": {"gender": "female"},
    "__or": {"age__lt": 10, "sex": 1},
    "__extra": "some extra conditions",
    "created_at__gte": "2022-01-01",
    "updated_at__lt": "2022-02-01",
    "email__ll": "%example.com",
    "status__in": [1, 2, 3],
    "status__not_in": [4, 5, 6],
    "address__is_null": True,
    "__group_by": ["age"],
    "__limit": 10,
    "__offset": 20,
    "__order_by": [("created_at", True), ("updated_at", False)],
}
sql, params = db_condition_parse(conds, use_hook=False)
print(sql, params)


# (name='Alice' AND age>18 AND gender='female' AND (age<10 OR sex=1) some extra conditions created_at>=2022-01-01 AND updated_at<2022-02-01 AND email LIKE '%%%example.com' AND status IN (1,2,3) AND status NOT IN (4,5,6) AND address IS NULL GROUP BY age ORDER BY created_at ASC,updated_at DESC LIMIT 10 OFFSET 20)
# []
```





#### 4 总结与TODO

这个拼接工具在设想中还有很多功能可以条件，比如

1. 需要给一些操作提供默认值
2. 在最终条件处理函数中增加一个自定义回调函数，便于局部自定义处理, 空值处理，可能每一个操作都不一样 （eg: 局部钩子可以通过全局钩子实现，在全局钩子中返回自定义处理函数）
3. _\_gt之类的操作嵌套复杂操作，比如 WHERE user_id = 123456789 AND fs_id > (上次查询结果中最后一条记录的id值) ORDER BY fs_id LIMIT 300000;
    3.1. 后期可能单字段多级嵌套处理， (eg: 当前可用__extra特殊操作直接拼接)
4. 所有的值都被解析为字符串，无法充分利用索引
    4.1. 当前采用SQL预处理替换参数的防注入方案，无法解决精确利用索引问题
5. 兼容一些常见的语法糖，比如 ~, !=
6. 对于增、删、改等其他操作的支持， 对于更新需求，比如，update user set value=value+1 where id=1
7. 关于双重否定的优化，如 id_\_not_in []，实际想要的效果应该是 “查全部”，因为所有id都不为空
    7.1 当前实现为正常拼接，空值条件会被舍弃，（eg: 实现 “查全部”的效果可以通过钩子函数来实现，或者自定义字段，当前无法局部实现，只能通过__extra实现）
8. 定义一个操作，可以拼接一大堆相同的条件，暂时没想到怎么实现，或许可以使用自定义局部钩子做到。



其实现在的实现中，有一些并不是特别满意的地方，比如`__order_by`的实现实在是太丑了，但是因为要兼容各种用法做了妥协，后续想一下怎么优化。还有一个比较想实现的功能是空值处理，现在的空值处理虽然已经能够覆盖大部分情况了，但是对于日常开发的使用情况来说，还是有几个地方需要优化，比如说，`NOT IN`一个参数，如果这个参数是一个空数组，那么正常思维来说，操作者应该是想要舍弃掉这个条件，想要查询全部，但是现在的处理还是按照整体的通用空值处理，不能够兼容到这种情况。还有关于参数校验，虽然每一个方法都有详细的参数注解，但是python的参数注解并不能限制实际参数的传入，后续的想法是是做强制参数校验，全局捕获异常。

一直想要追求完美，但是改了一点不足又发现另一处不足，导致这个项目一直在拖延，过度的代码设计与优化是万恶之源，还是应该先有一个方向，实现了通用功能就可以是v1.0.0版了，实用才是王道，不能沉浸于自己意淫之中。所以目前虽然有着诸多不足之处，但毕竟是费了点心思的东西，还是写一篇文章用来介绍。