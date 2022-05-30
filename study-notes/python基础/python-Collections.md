###### python自带的计数器：Counter

 Hight-perform container datatypes 翻译为一个高性能容量数据类型

```python
from collections import Counter 
charset = ['A', 'A', 'B', 'B', 'C', 'C'] 
counter = Counter(charset) 
print(charset) 
# Counter({'A': 2, 'B': 2, 'C': 2})
```

`Counter(iterable)`传入一个可迭代对象

