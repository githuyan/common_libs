# JavaScript

## 基础操作

### 技巧

- 单独引入的js文件要放在body尾部，而不能放在head中，因为代码从上到下解析，此时htm还没有完全渲染好，但是监听事件就有了（js),会报错，除非写一个预加载的函数( onload )，而css文件放在 head 

- ```javascript 
  npm instll xxx@3.6.0 --save-dev   // 本地安装
  ```


### 字符

**字符操作**

```js
select * from users
```



### 数组

#### 数组操作

```javascript
let lis = ['yellow','red']
// concat(param) 返回一个新数组
let colors = lis.concat('black',lis) 

// 尾增加 返回数组长度
colors.push('black')
colors.push('black','pink') // 可以一次添加多个元素

// 首增加 返回数组长度
colors.unshift('black') 	// 可以一次添加多个元素

// 尾删除 返回删除元素
colors.pop()

// 首删除 返回删除元素
colors.shift()

// 连接
colors.join(' - ')

// 切片
colors.slice(start,end=length) //end 默认为数组长度
colors.slice(-2，-1) // 支持负索引

// 万能方法  返回 [start end] -- 注意这个方法会修改原数组
// splice(start,end,--新元素--)  
let removed = colors.splice(0,1)  // 从第0项开始，删除之后的一项
let replaced = colors.splice(1,0,'new1','new2') // 将[1,1+0] 替换为 'new1','new2'
```

#### 循环遍历

```javascript
// for
for(let index=0;index<10;i++){
    console.log(index)  // 返回索引
}
for(let index in books) // 返回索引
for(let item of books)  // 返回元素
```

#### 迭代方法

```javascript
// 都不会修改原数组

.every(func) // 相当于 all 都为真则返回 true
.some(func) // 相当于 any 任一元素为真，则返回 true

.filter(func) // 返回为 True 的元素组成的数组
.forEach(func)  // 类似于 filter 没有返回值
	nums.forEach(console.log) 
.map(func) // 返回调用函数后每一项结果元素所组成的数组
.reduce(func) // 类似于python中的reduce 常用于数组求和
	nums.reduce(function(prevalue,n){
        return prevalue+n
    }
// 例子 取 <100 的元素*2 再求和
nums.filter(function(n){n<100}).map(function(n){n*2}).reduce(prevalue,n){return prevalue+n}

// 会修改原数组
.sort(func=None) // 注意sort排序会默认现将元素转换为字符串，再根据ascii编码进行排序，所以区分大小写， --会改变原数组

.find(func) // 查找符合的第一个元素
.findIndex(func) // 查找符合的第一个元素的索引
```

### 函数

#### 基础

```javascript
// 普通函数
function func() {
    pass
}

// 匿名函数  // 让函数自执行的方法时 后面加 ()
(function () {
    pass
})

// 箭头函数
const test = (参数1，参数2) => {
    console.log('箭头函数')
}

const test = 参数1 => {
    console.log('单参数箭头函数')
}

const test =(num1,num2) => num1+num2 // 当函数块中只有 一行代码 时

// 箭头函数中 this 的使用
箭头函数中的 this 引用的是最近作用域中的 this，一层一层的往外找，直到找到最近的 this 定义

```

#### 常用函数

- toFixed(num)

  ```js
  // toFixed(num) 四舍五入精确到num位小数
  Number(price).toFixed(2) // 操作数要是一个数字，要显式的转化
  ```

- .sort(func=None)

  ```js
  // .sort(func=None) // 注意sort排序会默认现将元素转换为字符串，再根据ascii编码进行排序，所以区分大小写， --会改变原数组，返回原数组
  ```

- num.indexOf(item,startindex)

  ```js
  // 在可迭代对象中会返回 index ，否则返回 -1
  ```

#### 高阶函数

```javascript
// 多参数传递
let func = function(...params){
    console.log(params)
}
```

