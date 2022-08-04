## Lua

> lua脚本小巧，性能优异，常用 redis 中，redis会将 Lua 的整个脚本当成一个原子性操作。

### 基本使用

**注释**

```lua
-- 单行注释

--[[
 多行注释
]]--
```

**数据类型**

> 感觉跟python类似，比如字符串的拼接，以及语法

```lua
-- 字典
a = {bb=23, cc='ddd'}
a['dd'] = 'qwer'


-- nil
表示空值， 也可为删除，变量被赋值nil，表示被删除

-- 字符串
字符串由一对双引号或单引号来表示, 或一对方括号（[[]])
字符串的链接 ..
print('aaa'..1234)  -- aaa1234

a = '12345'
#a  -- 5   使用 # 等价于 len(a)


-- 布尔值
lua 中的 false 和 nil 为假， 其他都为真

-- number
lua 默认真有一种 number 类型（双精度），默认类型可以再luaconf.h文件修改
```

**流程控制**

```lua
a = 10
while(a<20) do
    print(a)
    a = a-1
end

repeat
    print(a)
    a = a-1
until(a<20)

-- 普通for循环  （类似C）
for start, end, step do
    ...
end

for i=10, 8, -1 do print(i) end -- 从10--8步长为-1



-- 泛型for循环，（类似python）
a = {1,2,3}  -- 注意： 这里是一个集合
for k,v in ipairs(a) do
    print(k,v)  -- 这里的  a 可以是【列表|字典|集合】，若为【列表|集合】 k：索引， v: 值， 若为字典则是正常的键值
end

-- ipairs 的用法
for k,v in ipairs{1,2,3} do print(v) end  -- ok
for k,v in ipairs(a) do print(v) end  -- ok
for k,v in ipairs{a} do print(v) end  -- error

-- 字典的遍历 -- pairs
dict = {aa=1,bb='baba'}
for k,v in pairs(dict) do print(k,v) end  -- ok
for k,v in pairs{aa=1,bb='baba'} do print(k,v) end   --ok

```

**条件判断**

```lua
a = 10
if(a<10) then
    print(a)
elseif(a<5) then
    print(a)
else
    print(a)
end
```

**函数**

```lua
function max(test, ...)  -- 固定参数加变长参数
    for i,v in ipairs{...} do
        print(v)
    end
end
```



### 函数

**字符转换**

- tonumber(param)

  > 尝试转换为数字

  ```lua
  a = {b=2,c='4'}
  print(tonumber(a.b), tonumber(a.c))
  
  d = '3.4'
  tonumber(d)  -- 3.4
  ```

- tostring(param)

  > 尝试转换为字符
  
- 获取数组长度

  ```lua
  a = {1,2,3}
  
  #a  -- 3
  select("#", a)  -- 3  返回可变参数的长度
  ```


### Redis执行lua脚本

**执行缓存脚本**

> lua脚本预加载

```shell
# 1. 先通过redis讲一段lua脚本缓存在服务器,获得一个 sha1 校验码
redis-cli script load "$(cat test.lua)"
"123456789b24c879d926f3a38cb21a3fd9062e55"

# 2. 根据给定的 sha1 校验码，执行缓存在服务器中的脚本。
raw = redis.evalsha(sha1, params)
```

```lua
# lua脚本，默认获取两个参数， KEYS ARGV-
local key = KEYS[1]
local limit = tonumber(ARGV[1])
local expire_time = ARGV[2]
 
local current = tonumber(redis.call('get', key) or "0")
if current > 0 then
    if current + 1 > limit then
        return 0
    else
        redis.call("INCR", key)
        return 1
    end
else
    redis.call("SET", key, 1)
    redis.call("EXPIRE", key, expire_time)
    return 1
end
```



**直接运行脚本**

EVAL 命令

> 接收两个参数大概是使用的类的用法
>
> EVAL script numkeys key [key ...] arg [arg ...]

```shell
script：
numkeys： # 用于指定键名参数的个数。
key [key ...]： # 全局变量 KEYS 数组，用 1 为基址的形式访问 KEYS[1]
arg [arg ...]： # 附加参数，全局变量 ARGV 数组访问，访问的形式 ARGV[1]

# 例子
eval "redis.call('set', 'name', 'tom')" 1 name tom
eval "redis.call('set', KEYS[1], ARGV[1])" 1 name tom  # KEYS 和 ARGV 必须是大写
```







