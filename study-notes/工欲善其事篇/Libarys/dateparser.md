#### 时间解析 dateparser



```python
from dateparser import parse

print(parse('9 - 18 - 2022'))
print(parse('2022-9-18'))
print(parse('18-9-2022'))
print(parse('Sun Sep 18 13:42:28 2022'))
print(parse('Sunday/ 18 September/ 2022 13:42PM'))
print(parse('9 / 18 / 2022 13:42:28'))
print(parse('9 / 18 / 2022'))
print(parse('2022-09-25T13:42:28Z'))

# ----
2022-09-18 00:00:00
2022-09-18 00:00:00
2022-09-18 00:00:00
2022-09-18 13:42:28
2022-09-18 13:42:00
2022-09-18 13:42:28
2022-09-18 00:00:00
2022-09-25 13:42:28+00:00
```



