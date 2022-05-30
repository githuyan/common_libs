# Vue

## 理解

1. **什么是方法，什么是函数**

   定义在类中的称之为方法，因为他通常与整个类挂钩

   定义在外围的称之为函数

2. **双向绑定**

   view model （虚拟dom) 数据的双向绑定，包括视图的行为和状态，前端通过操作对象取值，而不是直接通过后端接口取值，

3. **生命周期函数**

   组件是组件，主题是主题，路由跳转时组件的挂载是一个大过程，刚创建（created），刚挂载（mounted），界面发生刷新，界面上的内容改变时	（updated）

## ES6语法规范

#### var 没有块级作用域，尽量使用let

const 

- 修饰的为常量，不允许在此赋值，建议优先使用const来保证数据的安全性

- 使用const标识符的时候必须赋值

- 常量的含义是指向的对象不能修改，但是可以修改对象内部的属性

  ```javascript
  const obj = {
      name:22
  }
  obj.name = 100 // 这样可以修改
  ```

#### 对象字面量增强写法

const obj = {}  {} J就是obj的字面量

```javascript
// 这是 ES5 的写法
const name = 'why';
const age = 12;
const height = 12222;
const obj = {
    name=name,
    age=age,
    height=height
};

// ES6 的对象增强写法
const obj = {
    name,
    age,
    height
}

// ES5 的函数写法
const obj = {
    run: function () {
        pass
    },
    eat: function () {
        pass
    }
}

// ES6 的函数增强写法
const obj = {
    run() {
        pass
    },
    eat() {
        pass
    }
}
```

```vue
// let 变量 、 // const 常量 (es6)
// 声明式编程范式
const vm = new Vue({
el: "#app", // 用于管理要挂载的元素
})

    <!-- 解决闪烁问题，渲染流程为渲染模板，然后在渲染数据，但是如果网速太慢，会导致，模板会展现在页面上一小段时间，以下作用为，在渲染数据之前不显示模板，展示空白 -->
    <style>
        [v-clock]{
            display: none;
        }
    </style>
```

#### 字符串定义

```vue
const str = " ---- "  // 双引号，不能换行
const str = ' ---- '  // 单引号，不能换行
const str = ` ---- `  // 反引号，能够换行
```



## 基础操作

### 虚拟DOM

>         // 虚拟DOM
>         // 调用方法时，每次都需要重新计算，即使是一些基本不变的值，而虚拟DOM将结果放到缓存中，减少了系统开销
>         // 当系统变化时，缓存会刷新，所有结果再重新计算

```vue
<body>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>

    <!-- 解决闪烁问题，渲染流程为渲染模板，然后在渲染数据，但是如果网速太慢，会导致，模板会展现在页面上一小段时间，以下作用为，在渲染数据之前不显示模板，展示空白 -->
    <style>
        [v-clock]{
            display: none;
        }
    </style>

    <div id='app'>
        {{ currentTime() }} 
        {{ currentTime1 }}
    </div>
    <script>77
        // vm 是主函数
        var vm = new Vue({
            el: "#app",
            methods: {
                currentTime:function() {
                    return Date.now()
                }
            },
            // 虚拟DOM
            // 调用方法时，每次都需要重新计算，即使是一些基本不变的值，而虚拟DOM将结果放到缓存中，减少了系统开销
            // 当系统变化时，缓存会刷新，所有结果再重新计算
            computed:{  // 计算属性，和上面的普通方法调用的区别类似于 property ，这是一个属性，而不是一个方法
                        // 当两种定义模式所定义的方法重名时，methods的优先级更高
                        // 计算属性保存在内存中，
                currentTime1:function(){
                    return Date.now()
                }
            }
        })
    </script>
</body>
```

Mustache语法

> {{ 可以进行一些简单的计算 }}

### 语法

#### v-once 

去除响应式

> 即 数据绑定，实时更新，此属性停止实时更新

```vue
<h2 v-once>
    {{ message }}
</h2>
```

#### v-html

```vue
// url: '<a href="www.baidu.com">百度一下</a>'
<h2 v-html='url'></h2>
```

#### v-text

```vue
<h2 v-text='message'></h2> == {{ message }}
```

#### v-pre

```vue
<h2 v-pre>
    {{ message }} // 不对这条语句做任何解析，类似于反斜杠
</h2>
```

#### v-cloak

```vue
// 在 vue 解析之前，div中会有一个属性c-cloak
在 vue 解析之后，这个属性会被自动删除
<div id='app' v-cloak> 
    {{ message }}
</div>
```

### 过滤器 filters

```vue
<div id="app">
    {{ price | getPrice }}
</div>
<script>
   const vm = new Vue({
        el:"#app",
        data: {
            imgurl: 'www.baidu.com'
        } ,
       filters:{ 
           getPrice(price) {  // 圆整两位小数
               return '￥'+Number(price).toFixed(2)
           }
    })
</script>
```

### v-on

> 用于事件的监听，绑定事件监听器
>
> 这是一个表单指向数据的单向绑定，由交互产生的数据将影响原本的数据

语法糖： **@**

```vue
// 参数的传递
// 如果我们定义的函数需要参数，但是没有传，不会报错，会默认参数为 None,相当于都是具有默认值的关键字参数、
// 在定义的事件函数需要参数，但是并没有传入时，浏览器会传入自动生成的 event 对象
<button @click='btnclick'>
    按钮1
</button>
<script>
    methods: {
    btnclick (event) {
        console.log(event)
        }
}
</script>
```

#### 参数传递

```vue
// 1. 不需要参数
<button @click='btnClick'></button>

// 2. 默认参数 event,这里虽然省略了（） 但是，触发操作后，会自动生成一个 event 对象
<button @click='btnClick'></button>
<script>
    methods: {
        btnClick(event) {
            pass
        }
    }
</script>

// 3. 除了 event 对象，同时又需要其他参数
<button @click='btnClick(param,$event)'></button> // 使用 $ 符，获取event对象
<script>
    methods: {
        btnClick(param,event) {
            pass
        }
    }
</script>

// 4. 参数的形式
<button @click="btnClick(message,$event)">按钮</button> 此时 message 是变量
<button @click="btnClick('message',$event)">按钮</button> // 此时 message 是字符串
```

#### 修饰符

##### **stop**

> 当 内层事件被触发时，冒泡停止，不触发外层事件

```vue
<div id='app' v-on:click='divclick'>
    aaa<br>
    <button v-on:click.stop='btnclick'>按钮1</button>  // stop 修饰符，
</div>
<script>
    const vm = new Vue({
        el: "#app",
        methods:  {
            btnclick () {
                console.log('btnclick')
            },
            divclick () {
                console.log('divclick')
            },
            submitClick () {
                
            }
        }
    })
</script>
```

##### **prevent**

> 阻止默认事件，
>
> 下文表单，默认情况下，在点击了 提交2 按钮时，就会默认进行跳转，prevent 修饰符使得不只执行函数，不跳转

```vue
<div id='app' v-on:click='divclick'>
    <form action="www.baidu.com">
        <input type="submit" value="提交1"> // 点击此按钮后，会自动默认跳转到 action<br>
        <input type="submit" value="提交2" v-on:click.prevent="submitClick"> // prevent 修饰符
    </form><br>
</div><br>

<script>
    const vm = new Vue({
        el: "#app",
        methods: {
            divclick() {
                console.log('divclick')
            },
            submitClick() {
                console.log('提交')
            }
        }
    })
</script>
```

##### 监听键盘

> 监听键盘事件，enter：回车，a:a，忽略大小写

```vue
<div id='app' v-on:click='divclick'>
    <input type="text" @keyup.enter='checkKey'> // 监听到 回车 操作会触发函数
</div><br>

<script>
    const vm = new Vue({
        el: "#app",
        methods: {
            divclick() {
                console.log('divclick')
            },
            checkKey(event) {
                console.log('监听到回车操作')
            }
        },
    })
</script>
```

##### once

> 限制只触发一次回调

##### native



#### key值绑定

> 使用 v-for 时，尽量绑定一个 key , 提高复用性，增加性能
>
> key值不能冲突，可以使用 id 

```vue
// 16-（理解）v-for绑定Key与非绑定Key的区别 13:00
// 注意key值绑定的值一定要一一对应，否则将起不到提升性能的作用
<li v-for='item in items' :key='item'>{{item}}</li>
```



### v-bind   属性绑定

> 同样也可以绑定对象（函数）
>
> 这是一个数据指向表单的单向绑定

#### 注意

b-bind 绑定的属性，单引号  \' \'  包围的是常量，其余是变量

> <router-link :to="/home">会报错</router-link>
>
> <router-link :to=" '/home' + homeMessage ">成功</router-link>

语法糖： **:**

```vue
<div id='app'>
    <img v-bind:src='imgurl'>
</div>
<script>
    const vm = new Vue({
        el:"#app",
        data: {
            imgurl: 'www.baidu.com'
        } 
    })
</script>
// 动态绑定 class
<2 v-bind:class="{类型1:true,类型2:false}">{{ message }}</2> // 当为true时，这个class会被绑定到这个标签，当为false时,就不会被绑定
```

####  动态绑定基本属性

```vue
<script>
const app=new Vue({
    el:"#app",
    data: {
        message: 'aaa',
        isActive: true,
        isLine: true
    },
    methods: {
        btnClick: function () {
            this.isActive = !this.isActive
        },
        getClasses: function() {
            return {active:isActive,line:isLine}
        }
    }
</script>
```

#### 动态绑定 style

```vue
<h2 :style="{fontsize: fontsize,color:finalColor}" v-for="item for movies">
    {{ items }}
</h2>
<script>
    const vm = new Vue({
        el:"#app",
        data: {
            movies: [1,2,3,4,5],
            fontsize: '100px'，
            finalColor: 'red'
        }
</script>
```

#### 动态绑定 class

```vue
// 例子
<div id="app">
    <h2 v-bind:class="{active:isActive,line:isLine}"> // 当有很多个 class 时，不会覆盖，会叠加
    {{ message }}
</h2>
    <h2 v-bind:class="getClasses()"> // 也可绑定对象
    {{ message }}
</h2>
<button v-on:click="btnClick">
    按钮
</button>
</div>
```

###  v-model  表单绑定

> 双向绑定
>
> 数据指向表单，表单指向数据

#### 原理

```vue
<div id="app">
    <input type="text" v-model="message">{{message}}
    <!-- v-model 本质是一个语法糖，相当于单向绑定+事件绑定 -->
    <input type="text" v-bind:value="message" v-on:input="message=$event.target.value">
</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: 'aaaa'
        }
    })
</script>
```

#### 修饰符

> 可以叠加使用

#####  lazy --懒加载

> 默认情况下，由于双向绑定的作用，用户交互的数据会实时的影响原本数据，懒加载的作用在于，只有当操作失去焦点或按下回车键时，才触发更新

```vue
<div id="app">
    <label>
        <input type="text" v-model.lazy="message">{{message}}
    </label>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: "abc"
        }
    })
</script>
```

##### number --类型转换

> v-model 之类的绑定的变量，默认都会转为 String 类型
>
> string
>
> number

```vue
<div id="app">
    <label>
        <input v-model.string.lazy="message1">{{message1}}--{{typeof message1}}<br>
        <input v-model.number="message2">{{message2}}--{{typeof message2}}<br>
        <input v-model.string.lazy="message3">{{message3}}--{{typeof message3}}
    </label>
</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message1: 0,
            message2: 0,
            message3: 0
        }
    })
</script>
```

##### trim --去除空格

```vue
        <div id="app">
            <label>
                <input v-model.trim="message">{{message}}<br>

            </label>

        </div>

        <script>
            const vm = new Vue({
                el: "#app",
                data: {
                    message: ''
                }
            })
        </script>
```



#### 单选框

> 单选框 默认为男，双向绑定之后，变量gender的值也随着交互改变

```vue
<div id="app">
    / value 值是必须的，v-model绑定的是变量，当选中时，将此控件的值传递给变量
    <input type="radio" id="man" v-model="gender" value="男">
    <input type="radio" id="woman" v-model="gender" value="女">
    <h1>{{gender}}</h1>
</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            gender:'男'
        }kaolong
    })
</script>
```

#### 复选框

注意：数组的绑定，自动填充值，而且是按照交互选择的顺序填充

```vue
<div id="app">
    <label for="love1"> // value 值是必须的，v-model绑定的是变量，当选中时，将此控件的值传递给变量
        <input type="checkbox" id="love1" v-model="loves" value="篮球"> 篮球
    </label>
    <label for="love2">
        <input type="checkbox" id="love2" v-model="loves" value="足球"> 足球
    </label>
    <label for="love3">
        <input type="checkbox" id="love3" v-model="loves" value="乒乓球"> 乒乓球
    </label>

    <h1>爱好：{{loves}}</h1>
</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            loves: null // 绑定一个数组，使用复选框，自动填充数组
        }
    })
</script>
```

#### 下拉框

```vue
<select name='adb' v-model='fruits'> // 单选
    <option value='苹果'></option>
    <option value='葡萄'>葡萄</option>
</select>

<select name='adb' v-model='fruits' multiple> // 多选
    <option value='苹果'></option>
    <option value='葡萄'>葡萄</option>
</select>
```

### 生命周期函数

#### created

> 当这个组件被创建出来时，对这个函数进行一个回调

mounted

> 

### computed 计算属性

> 注意这是一个属性，是一个名词而不是动词
>
> 调用方法时，每次都需要重新计算，即使是一些基本不变的值，而虚拟DOM将结果放到缓存中，减少了系统开销
>
> 计算属性，与 methods 类似，和上面的普通方法调用的区别类似于 property ，这是一个属性，而不是一个方法
> 当两种定义模式所定义的方法重名时，methods的优先级更高，计算属性保存在内存中，

```vue
<div id='app'>
    <h2>
        {{ fullName }}
        {{ name }}
    </h2>
</div>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            name: null
        },
        computed: {
            fullName: { // 其实这才是vue计算属性的本质，是一个对象，只是计算属性常为只读属性，不需要set方法
                get: function () {
                    return '其实计算属性大部分只需要一个制度属性，而不需要用户给属性设置值'
                },
                set: function (val) {
                    this.name = val;
                    console.log(val);
                }
            },
            otherName: function () {
                console.log('计算属性的简写方式')
            }
        }
    })
</script>
```

#### 属性 computed 与 方法 methods 的对比

1. 形式的不同

   ```vue
   // mehtods
   {{ func() }}
   
   // computed
   {{ func }}
   ```

2. 性能的不同

   ```vue
   // computed // methods
   虚拟DOM
   调用方法时，每次都需要重新计算，即使是一些基本不变的值，而虚拟DOM将结果放到缓存中，减少了系统开销,当系统变化时，缓存会刷新，所有结果再重新计算
   ```

### el 与 template 的区别

```javascript
// 在根组件创建了一个模板，编译后这个模板将会替换掉 el 绑定的那个 div
new Vue({
    el: '#app',
    components: { App },
    template: '<App/>'
})
```

### 条件判断

```vue
v-if   
// v-if 与 v-show 
v-if 指的是此元素是否显示（相当于被删掉)
v-show 指的是此元素是否被渲染 (加了一个 display=None) 用于频率高时

v-else-if
v-else
<h1 v-if='core>=90'> // 不建议这样写，这些比较复杂的逻辑应该使用其他方法，如计算属性 computed,后面直接显示
    {{ message}}
</h1>

v-for
<li v-for="item for items"></li>  // 遍历对象时，若只指定一个接收参数，则默认接受 值
// <li v-for='(val,key,index) in items'>{{index}}-{{key}}-{{val}}</li>//注意第一个是值，第二个才是键，只要是迭代的都能获取它的索引
<script>
    data: {
        items: {
            name:'tom',
            age: 11,
            sex: 'man'
        }
    }
</script>
```

#### 实例

```vue
// 登录切换
<div id="app">
    <button @click="switchLogin">切换</button>

    <span v-if="isUser">
        <label for="username">密码登录</label>
        <input v-bind:placeholder="ways[isUser]">
    </span>

    <span v-else>
        <label for="email">邮箱登录</label>
        <input v-bind:placeholder="ways[isUser]">
    </span>
</div>
// 小问题 ，当输入之后进行切换，文本框内依然保留之前输入的内容，（虚拟DOM，因为放在内存中）
可以指定 关键字 Key 进行区分，效果为切换后，文本框同步清空
<input v-bind:placeholder="ways[isUser]" key='password'>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            isUser: true,
            ways: {
                true: "密码登录",
                false: "邮箱登录"
            }
        },
        methods: {
            switchLogin() {
                this.isUser = !this.isUser
            }
        }
    })
</script>
```

### 响应式与非响应式

> 在 vm 中定义方法，通过函数改变数组或对象的内容，在响应页面能够实时的渲染

```vue
// 能做到响应式
push()
pop()
shift()
unshift()
splice()

// 不能做到响应式
colors[0] = 'new_ele'  // 尽量使用 splice() 方法来替换这种实现
Vue.set(this.colors,0,'new_ele') // 三个参数为，要修改的对象，索引，新值
```

###  案例

#### 选择变换

> 一个图书列表，默认第一个显示红色，随后，点击哪一个哪一个才显示红色

```vue
<head>
    <meta charset="UTF-8">
    <style>
        .active {
            color: red;
        }
    </style>
</head>

<body>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>


    <div id="app">
        <li v-for="(book,index) in books" :key=index :class="{active:currentIndex===index}"
            @click="cutColor(index)">{{book}}</li>
    </div>

    <script>
        const vm = new Vue({
            el: "#app",
            data: {
                books: ['python', 'java', 'mysql', 'Django'],
                currentIndex: 0
            },
            methods: {
                cutColor(index) {
                    this.currentIndex = index;
                }
            }
        })
    </script>
</body>
```

#### 图书购物车

##### 要点

- 通过绑定 disabled 属性来控制交互
- 使用计算属性
- 使用过滤器
- 数字的圆整
- 表格的形式

```vue
<div id="app">
    <table>
        <thead>
            <tr>
                <th></th>
                <th>书籍名称</th>
                <th>出版日期</th>
                <th>价格</th>
                <th>购买数量</th>
                <th>操作</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(book,index) in books">
                <td>{{index}}</th>
            <td>{{book.name}}</td>
            <th>{{book.ptime}}</th>
            <th>{{book.price | getPrice}}</th>
            <th>
                <button @click="increment(book)">+</button>
                {{book.paynum}}
                <button @click="decrement(book)" :disabled="book.paynum<=0">-</button> 
                <!-- // 这里通过绑定disabled属性来控制交互 -->
            </th>
            <th>
                <button @click="removeBook(index)">删除</button>
            </th>
        </tr>
    </tbody>
</table>

<h2 v-if="books.length===0">购物车为空</h2>

<h2 v-else>总价是：{{ totalPrice | getPrice }}</h2>

</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            books: [
                {
                    name: '算法导论',
                    ptime: '2008-5',
                    price: 1,
                    paynum: 1
                },
                {
                    name: 'UNIX编程艺术',
                    ptime: '2000-8',
                    price: 3,
                    paynum: 1
                },
                {
                    name: '编程珠玑',
                    ptime: '2003-8',
                    price: 2,
                    paynum: 1
                },
                {
                    name: '代码大全',
                    ptime: '2001-9',
                    price: 6,
                    paynum: 1
                }
            ]
        },
        methods: {
            increment(book) {
                book.paynum++
            },
            decrement(book) {
                // if (book.paynum > 1) {
                //     book.paynum--
                // } 
                book.paynum--
            },
            removeBook(index) {
                console.log(index);
                this.books.splice(index, 1);
            }
        },
        computed: {
            totalPrice() {
               totalPrice() {
                    let totalprice = 0
                    // for (let i = 0; i < this.books.length; i++) {
                    //     totalprice += this.books[i].price * this.books[i].paynum
                    // }
                    
                    for (let book of this.books){
                        totalprice += book.price*book.paynum
                    }
                    return totalprice
                }
        },
        filters: {
            getPrice(price) {
                return '￥' + Number(price).toFixed(2)
            }
        }
    })
</script>
```

## 组件化

> 组件是一个单独功能模块的封装，这个模块有自己的HTML模板，也应该由自己的属性，自己的数据（data），尽量不要将数据都放在顶层的 组件树（root） 中
>
> 组件也有自己的 data 属性，methods，template，components
>
> 根项目 vm 也是一个组件，是一个根组件，而每一个子组件都由根组件相似的功能

### 注册组件 

> 使用 反引号 定义字符串
>
> const cpnC = Vue.extend({}) --继承组件，组件构造器
>
> Vue.component( 组件名，cpnC) --初始化组件
>
> <组件名></组件名> -- 使用组件

#### 语法糖

```VUE
<div id="app">
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>

    <cpn></cpn>
</div>

<script>
    // const cpnC = Vue.extend({   // 组件构造器
    //     template:`<h2> 啊啊啊啊啊 </h2>`
    // })

    // Vue.component('my-cpn',cpnC)  // 全局组件，意味着可以在多个 Vue 实例下使用

    Vue.component('my-cpn', {   // 语法糖，底层依然调用上述 extend 方式
        template: `<h2> 啊啊啊啊啊 </h2>`
    })

    const vm = new Vue({
        el: "#app",
        components: {  // 只能在本Vue实例中使用，局部组件，（推荐）
            // cpn:cpnC  
            cpn: {   // 语法糖，底层依然调用上述 extend 方式
                template: `<h2> 啊啊啊啊啊 </h2>`
            }
        }
    })
</script>
```

#### 模板分离写法

```vue
<!-- <script type="text/x-template" id="cpn"></script> // 下方更简洁--> 

<template id="cpn1">
	<h1>这是一个组件模板</h1>
</template>

<div id="app">
    <cpn></cpn>
</div>

<script>
    const vm = new Vue({
        el: "#app",
        components: {
            cpn: {   
                template: "#cpn1"
            }
        }
    })
</script>
```

#### **全局组件-局部组件**

```vue
<div id="app">
    <my-cpn></my-cpn>
    <my-cpn></my-cpn>

    <cpn></cpn>
</div>

<script>
    const cpnC = Vue.extend({   // 组件构造器
        template:`<h2> 啊啊啊啊啊 </h2>`
    })

    Vue.component('my-cpn',cpnC)  // 全局组件，意味着可以在多个 Vue 实例下使用

    const vm = new Vue({
        el: "#app",
        data: {
            message: ''
        },
        components: {  // 只能在本Vue实例中使用，局部组件，（推荐）
            cpn:cpnC  
        }
    })
</script>
```

#### 组件嵌套

> 子组件要写在父组件前，否则无法工作
>
> 子组件注册在父组件中，则子组件只能在父组件的作用域中使用，只有在作用域中注册过了，才能使用组件
>
> 这父子关系的定义与 python 正好相反，父组件拥有组件所有功能

```vue
<div id="app">
    <cpn2>
        <cpn1></cpn1>
    </cpn2>
</div>

<script>
    // 子组件
    const cpnC1 = Vue.extend({
        template: `<div><h2> 这是 cpnC1 组件</h2></div>`
    })

    // 父组件
    const cpnC2 = Vue.extend({
        template:`<div><h2> 这是 cpnC2 组件</h2><cpn1></cpn1></div>`,
        components: {
            cpn1: cpnC1
        }
    })

    // 这也相当于一个组件，是 根组件（root）
    const vm = new Vue({
        el: "#app",
        components: {
            cpn2: cpnC2
        }
    })
</script>
```

#### 跨组件数据传递

> 注意 模板 必须要有一个 总 的标签去承载
>
> ```vue
> // 1  -- 错误
> <template id='cpn'>
>  <h1>aaaa</h1>
>  <h1>{{message}}</h1>
> </template>
> // 2 -- 正确
> <template id='cpn'>
> 	<div>
>      <h1>aaaa</h1>
>  	<h1>{{message}}</h1>
>  </div>
> </template>
> 
> // 3 -- 错误
> <template id='cpn'>
> 	{{message}}
> </template>
> // 4 -- 正确
> <template id='cpn'>
> 	<h1>{{message}}</h1>
> </template>
> ```

使用组件中的 data 传递数据，数据和变量的区别是，数据是确定的，而变量不确定使用 props 传递

```vue
<div id="app">
    <cpn></cpn>
</div>

<template id="cpn">
    <div>   // 注意这里必须要有一个 <div> 标签
        <h1>这是一个组件模板</h1>
        <h1> {{ title }} </h1>
    </div>
</template>

<script>
    // Vue.component('cpn', {
    //     template: "#cpn",
    //     data() {
    //         return {
    //             title: '标题'
    //         }
    //     }
    // })

    const vm = new Vue({
        el: "#app",
        components: {
            cpn: {
                template: "#cpn",
                data() {
                    return {
                        title: "标题"
                    }
                }
            }
        }
    })
</script>
```

**为什么组件中的 data 是一个函数（必须返回一个对象），而根组件中的 data 是一个对象？**

> 每一个组件都要有自己的状态，同一个模板（类）创建的不同模板实例，他们之间的状态时互不影响的，
>
> 组件是可复用的，如下，将一个计数器封装成一个组件，但是Mustache语法绑定的‘同一个’变量，将组件中的data设计成函数的形式，就是为了利用闭包的特性，每次调用模板，都是一个创建新的实例对象（闭包）的过程，用于保证变量互不污染

```vue
<div id="app">
    <cpn></cpn>
    <cpn></cpn>
    <cpn></cpn>
</div>

<template id="cpn">
    <div>
        <h1>计数器：{{counter}}</h1>
        <input type="button" @click="increment" value="+">
        <input type="button" @click="decrement" value="-">
    </div>
</template>

<script>
    const vm = new Vue({
        el: "#app",
        components: {
            cpn: {
                template: "#cpn",
                data() {  // 此处通过函数调用返回一个对象，而不直接是一个对象，利用了闭包
                    return {
                        counter: 0
                    }
                },
                methods: {
                    increment() {
                        this.counter++
                    },
                    decrement() {
                        this.counter--
                    }
                }
            }
        }
    })
</script>
```

### 父子组件之间的通信

> props

**注意**：

props 中使用驼峰形式的变量名（myMessageInfo），在传递到子组件中需要使用时，必须转化为 （my-Message-Info)的形式，子组件中不支持驼峰命名方式

**参数类型限制**

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

#### props-- 父传子 

> props 中的数据是参数名，是形参而不是实参，模板就像是一个函数，后面使用 v-bind 将实参（根组件中的data参数）赋值给 子组件，子组件再传递给模板

```vue
<div id="app">
    <cpn :message="message" :items="items"></cpn>
    <cpn></cpn>  // 不传入值，使用默认值
</div>

<template id="cpn">
<div>
    <h1>{{message}}</h1>
    <p v-for="item in items">{{item}}</p>
    </div>
</template>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: 'tom',
            items: [1, 2, 3]
        },
        components: {
            cpn: {
                template: "#cpn",
                // 1
                // props: ['message','items']
                
                // props: ['param1','param2'] // 这是模板函数的形参名

                // 2  类型限制
                // props: {
                //     message: [String,Number]
                //     items: Array
                // }

                // 3  类型限制 + 默认值 + 必传值 + 自定义验证 + 自定义类型（未写）
                props:{
                    message: {
                        type: String,
                        default: 'jerry',
                        required: true,
                        validator: function(value) { // 自定义验证
                            return ['success','warning'].indexOf(value) !== -1
                        }
                    },
                    items: {
                        type: Array,
                        // default: [1,2,3,4,5,6,7,8,0]
                        default() {
                            return [1,2,3,4,5,6,7,8,0]
                        }
                    }
                }
            }
        }
    })
</script>
```

#### $emit-- 子传父 

> 通过自定义事件传递

**流程：**

> 在子组件中，通过@emit() 触发事件
>
> 在父组件中，通过v-on 来监听子组件的事件

1. 子组件创建自定义事件 btnclick，监听到 click 事件后，发射（事件-itemclick，参数-value） 
2. 父组件监听事件-itemclick，并收到参数 value
3. 下一步操作

```vue
<div id="app">
    <cpn :message="message" :items="items" @itemClick="cpnclick"></cpn>
</div>

<template id="cpn">
<div>
    <ul>
        <button v-for="item in items" @click="btnclick(item)">{{item}}</button>
    </ul>
    </div>
</template>

<script>
    const cpn = {
        template: "#cpn",
        props: {
            message: String,
            items: Array
        },
        methods: {
            btnclick(value) {
                this.$emit('itemclick', value)
            }
        }
    }

    const vm = new Vue({
        el: "#app",
        data: {
            message: 'tom',
            items: ['乒乓球','篮球']
        },
        components: {
            cpn
        },
        methods: {
            cpnclick(value) {
                console.log(value)
            }
        }
    })
</script>
```

#### watch 监听属性的改变

```vue
<div id="app">
    <input v-model:value="message" v-model:param="param">{{message}}--{{param}}
</div>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: 'tom',
            param: "aaaaaaaaaa"
        },
        watch: {
            param(newValue,oldValue) { // 这里的函数名只能是待监听的属性名
                this.param = newValue
            }
        }
    })
</script>
```

### 父子组件之间的访问，调用

####  父访问子

> 父访问子中的 属性，方法，对象

##### $refs

> $refs 默认是一个空对象，通过在父组件调用子组件时，在子组件的标签中使用 ref 属性为一个具体的子组件对象绑定一个别名，通过 this.$refs.别名 来精确调用

```vue
<div id="app">
    <cpn ref="child1"></cpn> // 通过 ref 为子组件模板实例 绑定别名
    <button @click="btnClick">父组件中的按钮</button>
</div>

<template id="cpn">
<div>
    <h2>这是子组件</h2>
    </div>
</template>
<script>
    const vm = new Vue({
        el: "#app",
        components: {
            cpn: {
                template: "#cpn",
                methods: {
                    showMessage() {
                        console.log('现在在调用子组件');
                    }
                },
                data() {
                    return {
                        message: "这是子组件中的数据"
                    }
                }
            }
        },
        methods: {
            btnClick() {
                console.log('点击按钮');
                // console.log(this.$children[0].message);
                // console.log(this.$children)
                // this.$children[0].showMessage()

                console.log(this.$refs);
                console.log(this.$refs.child1.message);
                this.$refs.child1.showMessage();

            }
        }
    })
</script>
```



##### $children   （不推荐用，使用不灵活）

> 常用于 需要遍历全部子组件 的情况

注意： 这个 $children（VueComponent对象） 是相对于父组件来说的，对于父组件可以有多个子组件，因此 $children 是一个数组

```vue
<div id="app">
    <cpn></cpn>
    <button @click="btnClick">父组件中的按钮</button>
</div>

<template id="cpn">
<div>
    <h2>这是子组件</h2>
    </div>
</template>
<script>
    const vm = new Vue({
        el: "#app",
        components: {
            cpn: {
                template: "#cpn",
                methods: {
                    showMessage() {
                        console.log('现在在调用子组件')
                    }
                },
                data() {
                    return {
                        message: "这是子组件中的数据"
                    }
                }
            }
        },
        methods: {
            btnClick() {
                console.log('点击按钮');
                console.log(this.$children.message);
                console.log(this.$children) 
                this.$children[0].showMessage()
            }
        }
    })
</script>
```

#### 子访问父 （不建议使用，提升了耦合度）

> 设置子组件的目的在于，提高复用性，降低耦合度，模块化开发，

##### $parent  -- 访问父组件

> this.$parent（VueComponent对象），每一个子只有一个父

```vue
<div id="app">
    <cpn></cpn>
</div>

<template id="cpn">
<div>
    <h2>这是子组件</h2>
    <button @click="btnClick">子组件中的按钮</button>
    </div>
</template>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: '这是父组件中的内容'
        },
        methods: {
            showMessage() {
                console.log('正在调用父组件（现在是根组件）')
            }
        },
        components: {
            cpn: {
                template: "#cpn",
                methods: {
                    btnClick() {
                        console.log('点击按钮')
                        console.log(this.$parent.message)
                        this.$parent.showMessage()
                    }
                }
            }
        },
    })
</script>
```

##### $root -- 访问根组件

> $this.root (Vue 对象)，和VueComponent 对象类似

```vue
<div id="app">
    <cpn></cpn>
</div>

<template id="cpn">
<div>
    <h2>这是子组件</h2>
    <button @click="btnClick">子组件中的按钮</button>
    </div>
</template>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: '这是父组件中的内容'
        },
        methods: {
            showMessage() {
                console.log('正在调用父组件（现在是根组件）')
            }
        },
        components: {
            cpn: {
                template: "#cpn",
                methods: {
                    btnClick() {
                        console.log('点击按钮')
                        // console.log(this.$parent.message)
                        // this.$parent.showMessage()

                        console.log(this.$root.message)
                    }
                }
            }
        },
    })
</script>
```

## 插槽 - slot

> 更具扩展性，像USB插槽，像jinjia2的预留挖坑一样，赋予组件更大的灵活性，通常是一个按钮或者一段文字，不值得再重写一个新的组件
>
> 用于同样的结构，不同的细节

#### 基本使用

**注意**： 本例中，填充插槽时，插槽预留的位置是子组件中，在父组件中填充一个按钮到子组件的插槽，此时的点击触发，仍然是在父组件中，而不需要使用 props 之类的进行组件间通信

```vue
<div id="app">
    <button @click="showMessage">在父组件中</button>
    <cpn><button @click="showMessage">填充到子组件中的按钮</button></cpn>
    <cpn><button>标签</button></cpn>  // 填充插槽
</div>

<template id="cpn">
    <div>
        <h2>这是子组件</h2>
        <slot></slot>  // 挖坑，预留了一个插槽
        // <slot>这是默认值</slot>  // 插槽的默认值
    </div>
</template>
<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: '这是父组件中的内容'
        },
        methods: {
            showMessage() {
                console.log('现在是根组件')
            }
        },
        components: {
            cpn: {
                template: "#cpn"
            }
        }
    })
</script>
```

#### 具名插槽

> 用于多个插槽

```vue
<slot name="left"></slot>  // 给插槽绑定名称
<slot name="right"></slot>

<cpn>
    <button slot="left">左边按钮</button>  // 使用指定插槽
    <button slot="right">右边按钮</button>
</cpn>
```

#### 作用域插槽

> 为了从模板中

##### 编译作用域

> 每个模板是一个作用域

父组件获取子组件中的数据（data）

```vue
<div id="app">
    <cpn>
        <template slot-scope='abc'>   // 这里必须要有一个标签承载，一般是 template,获取这个插槽对象
			<span>{{abc.langue}}</span>
        </template>
    </cpn>
</div>

<template id="cpn">
    <div>
        <h2>这是子组件</h2>
        <slot :langue="languages"></slot> // 这里的langue可以改变，就是一个别名，方便上面调用
    </div>
</template>

<script>
    const vm = new Vue({
        el: "#app",
        data: {
            message: '这是父组件中的内容'
        },
        components: {
            cpn: {
                template: "#cpn",
                data() {
                    return {
                        languages: ['java','python']
                    }
                }
            }
        }
    })
</script>
```

## 模块化开发

> 模块化，匿名函数，闭包

#### 原理-- 匿名函数（ES5）

> 像类一样
>
> 使用模块作为出口，每个开发者只需关注自己的模块，（这是ES5,中的解决方案，现有更好的替代方案）
>
> 常见的模块化规范，CommonJS，AMD，CMD，Modules(ES6)

```javascript
// moudledevelop1.js

// 将整个文件写成一个函数，返回一个包含所有数据，方法，对象的总对象，
;moduleA = (function() {
    let obj = {};  // 初始化定义返回的总对象

    let flag = true;

    sum = function(param1,param2) {
        console.log('模块化开发'+param1+param2)
    };
	
    // 将这个模块中的所有对象都绑定到 总对象 上，然后返回
    obj.flag = flag;
    obj.sum = sum;
    return obj
})()

// moudledevelop2.js

// 使用
;(function() {
    if (moduleA.flag) {
        console.log(moduleA.sum(1,2))
    }
})()
```

##### CommonJS 标准的模块化

> 这种方法只能在 CommonJS 标准中使用才能正常解析

```javascript
// aaa.js 
// 导出
let flag = true;
function sum(param1,param2) {
    console.log(param1+param2)
}

module.exports = {
    flag,
    sum
}

// bbb.js
// 导入
let {flag,sum} = require('../aaa.js')
```

##### ES6标准的模块化

```javascript
// 使用 export ，import 
// index.html 展示文件，入口文件
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</Title>
    </head>
    <body>  // 这里 导入文件的顺序 并无影响
        <script src="es6i.js" type="module"></script>
        <script src="es6o.js" type="module"></script>
    </body>
</html>

// es6o.js  导出模块
let message = "es6 导入文件";
function showMessage(m) {
    console.log(m)
}
// 各自导出
export let name = '在定义时直接导出'
// 一次性导出
export {
    message,showMessage
}

 // es6i.js 导入模块
import {message,showMessage} from "./es6o.js"
// import * as moduleA from "./es6o.js"  // 导入文件很多时，一次性导入，起别名
console.log(message);
showMessage('这是在导出文件中引用')
```

## 一些问题

1. vue程序运行过程

   ![脚手架项目运行流程](D:\桌面\学习笔记\resource\脚手架项目运行流程.png)

   

   - runtime-compiler

     template -> ast抽象语法树 -> render函数 -> 虚拟DOM（virtual DOM ）-> 真是 DOM -> UI

     ```javascript
     
     new Vue({
         el: '#app',
         components: { App },
         template: '<App/>'
     })
     ```

   - runtime-only 

     > 相较于 runtime-compiler 它执行项目要走的流程更少，通过 render 函数直接将项目编译成二进制执行，而不需要先抽象再转 render 函数
     >
     > - 性能更高
     > - 源代码更少

     render函数 -> 虚拟DOM -> UI 

     ```javascript
     new Vue({
       el: '#app',
       render: function (createElement) {
         return createElement('h2',{'class':'titl'},['这是一段信息'])
       }
     })
     ```

