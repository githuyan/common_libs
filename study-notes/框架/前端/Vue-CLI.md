# `VueCLI`

> CLI (Command-Line Interface)，命令行界面，俗称脚手架

## 文件结构解析

`.editorconfig` 文件 用于对项目的结构做一些约束

```javascript
root = true

[*]
charset = utf-8
indent_style = space  // 使用空格缩进
indent_size = 2  // 限制缩进为2
end_of_line = lf
insert_final_newline = true  // 在项目最后增加一行
trim_trailing_whitespace = true  // 删除多余的空格
```

`.babelrc` 文件，用于将代码转化为浏览器兼容的格式

```vue
{
  "presets": [
    ["env", {
      "modules": false,
      "targets": {
        "browsers": ["> 1%", "last 2 versions", "not ie <= 8"] // 为符合这些条件的浏览器转化代码
      }
    }],
    "stage-2"
  ],
  "plugins": ["transform-vue-jsx", "transform-runtime"]
}

```

`.config/` 

- dev.env.js  开发时依赖

- prod.env.js  生产时依赖

- index.js  基本配置文件 

  ```javascript
  autoOpenBrowser: true, // 开启虚拟服务器后，自动打开浏览器
  ```

package.json

> 指定了依赖的大致版本，每次 npm install 会安装指定限制下的最新版本
>
> ^5.2.1 指的是，大版本 5 不变，后面的 2.1 可能会变

package.lock.json

> 记录了依赖模块的实际版本

### npm run build 运行流程

![脚手架项目运行流程](D:\桌面\学习笔记\resource\脚手架项目运行流程.png)

1. 安装 vue-router 

   建立 router 文件，建立 index.js 入口，安装 vue-router 插件，配置路由关系，导出对象

   ```js
   // 配置路由相关的信息
   
   import VueRouter from "vue-router"
   import Vue from "vue"
   
   import Home from "../components/Home"
   import About from "../components/About"
   
   
   Vue.use(VueRouter)   // 为 vue 安装 vue-router 插件
   
   const routes = [  	// 从组件文件中导入，配置路由关系
     {
       path: '/home',
       component: Home
     },
     {
       path: '/about',
       component: About
     },
     {
       path: '/',
       redirect: '/home'
     }
   ]
   
   const router = new VueRouter({  	// 注册路由
     routes,
     mode: 'history'
   })
   
   export default router;  			// 导出路由
   ```

2. 在主文件中（main.js）导入路由，挂载路由

   ```js
   import Vue from 'vue'
   import App from './App.vue'
   
   import router from './router'
   
   Vue.config.productionTip = false // 提示信息
   
   new Vue({
     el: '#app',
     router,
     render: h => h(App),
   })
   ```

3. 在 Vue.vue 中使用组件

   ```html
   <template>
     <div id="app">
       <h2>标题</h2>
       <button @click="aboutClick">关于</button>
       <button @click="clickBtnToH">跳转首页</button>
       <router-view></router-view>
     </div>
   </template>
   
   <script>
   export default {
     name: "App",
     methods: {
       clickBtnToH() {
         console.log("首页");
         // this.$router.push("/home").catch((err) => err); // 不加错误捕获会在重复点击的时候报错
         this.$router.replace("/home");
       },
       aboutClick() {
         this.$router.replace("/about");
       },
     },
   };
   </script>
   ```

   

## 创建项目

1. vue create 项目名 ( vue3 的创建命令 )

   > vue init webpack my_project
   
2. 


## vue-router

> 网络路由，前端路由，改变 URL 但是页面不刷新，一个页面就是一个大的组件

### 基础

> 什么是前端渲染，什么是后端渲染

#### 发展阶段

1. 早期发展阶段：

   是由后端渲染好所有的页面，然后直接返回页面给前端浏览器，有前端呈现，

2. 前后端分离阶段：

   一般有三个服务器（静态资源服务器，后端开发服务器（提供API接口），数据库服务器，），这就是前端渲染阶段（ajax)，当输入一个 url 时，会先从静态资源服务器获取 js,css,html 

3. 单页面富应用阶段，（SPA页面）

> 整个网页只有一个HTML页面，

​	前端路由：前端交互生成各种路由，由前端路由映射执行不同的 js ，

#### 约定俗成

```js
// 注册组件时，<RouterView> 也可以被默认编译成 <router-view> 驼峰会被默认为以 - 连接
```

### vue-router 的使用

**流程** + 默认路由

```js
// src/router/index.js

// 配置路由相关的信息

import VueRouter from "vue-router"
import Vue from "vue"

import Home from "../components/Home"
import About from "../components/About"

Vue.use(VueRouter)   // 为 vue 安装 vue-router 插件

const routes = [
  {
    path: '/home',
    component: Home
  },
  {
    path: '/about',
    component: About
  },
  {
    path: '/',
    redirect: '/home'  // 默认路由，最开始时重定向到 /home 路径
  }
]

const router = new VueRouter({
  routes
})

export default router;

// Vue.vue

<template>
  <div id="app">
    <router-link to="/home">首页</router-link> 3. 在 Vue 实例中挂载创建的路由实例
    <router-link to="/about">关于</router-link>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: "App",
};
</script>
```

#### history 和 hash 模式

```js
// router/index.js

const router = new VueRouter({
  routes,
  mode: 'history'  // 使用 history 模式，（改变 url 而不刷新页面）
})
```

### 路由跳转

#### 改变 URL 而不刷新页面

1. 改变 hash 值

   ```javascript
   location.hash = 'new_url'
   // http://xxxx:xx/xx#/new_url 		// 会增加一个 锚点，然后拼接 新的 url 
   ```

2. 堆栈 push，堆栈操作，**可以保留历史记录**

   ```javascript
   history.pushState({},'','new_url')
   // http://xxxx:xx/xx#/new_url 		// 会增加一个 锚点，然后拼接 新的 url 
   // 再次 Push 后替换栈顶，换成新的 url
   history.back() // 会返回上一个栈元素
   history.forward() 
   ```

3. 替换，replace，**不能保留历史记录**

   ```javascript
   history.replaceState({},"","new_url")  
   // http://xxxx:xx/xx/new_url    	// 直接替换，而不使用锚点
   ```

4. go

   ```javascript
   history.go(-1) 相似于  history.back()
   ```

#### router-link 

**to 属性**

```html
<router-link to='/home'>to 属性添加路由，</router-link>
```

**tag 属性**

```html
<router-link tag="button">可变的值</router-link> // 默认 router-link 组件会被渲染成 a 标签
```

**replace 属性**

```html
<router-link replace> 不会留下历史记录，无法回退 </router-link>
```

**active-class 属性**

> 当点击按钮时，此按钮标签会被标记为活跃状态（增加一个这样的属性 class="router-link-exact-active router-link-active"），当点击下一个按钮时，这个按钮的活跃状态被解除 

```html
<router-link active-class='activate'>修改这个活跃标签</router-link>
```

#### $router 和 $route 

**$router.push**

> 这个 router 是一个总的路由器

```js
this.$router.replace("/about");  // 路由跳转，这里的 router 是 一个 VueRouter 对象
const router = new VueRouter({
  routes,
  mode: 'history'
})
```

> 这里的 push 和 replace 方法对应 history 的 pushState 和 replaceState 方法

```html
<button @click="clickBtnToH">跳转首页</button>
<script>
export default {
  name: "App",
  methods: {
    clickBtnToH() {
      console.log("首页");
      // this.$router.push("/home").catch((err) => err); // 不加错误捕获会在重复点击的时候报错
      this.$router.replace("/home"); 
      // 传递参数
      this.$router.repace({
          path:"/home",
          query: {
              name: 'tom'
          }
      })
    },
    aboutClick() {
      this.$router.replace("/about");
    },
  },
};
</script>
```

**$route.params**

> 这里获取的 route 是当前活跃的路由

```js
$route.params.userId  // 获取 动态路由的 userId 参数值
```

**所有的组件都继承子 Vue 原型 Vue.property** 

```js
// 当使用 Vue.property.test = function() {
	console.log('所有的组件都可以调用这个 test 方法')
}

this.test()

Vue.property.$test = function() {}
this.$test()
```

#### 动态路由+获取参数

1. **params 传递参数**

   **格式：** /router/:id

   **路径：** /router/12

   ```js
   $router.push
   $route.params.userId  // 获取 动态路由的 userId 参数值
   const routes = [
     {
       path: '/',
       redirect: '/home'
     },
     {
       path: '/user/:abc', // 动态路由
       component: User
     }
   ]
   ```

2. **query 传递参数**

   > https://www.bilibili.com/video/BV15741177Eh  ?p=112&spm_id_from=pageDriver，后面这一部分就是这条 URL  的 query
   >
   > URL ： 协议:// 域名 : 端口 / 路径 ? query
   >
   >  			scheme :// host : port / path / ?query

   **格式：** /router

   **路径：** /router?id=123

   ```html
   <router-link :to="{path:'/profile'}">个人 Profile 这里传递了一个对象</router-link>
   <router-link :to="/profile">个人 Profile  根这里是相同的</router-link>
   
   <router-link :to="{path:'/profile',query:{name:'tom',age:22}}">
       个人Profile
   </router-link>
   
   // Profile.vue
   <div>
       {{ $route.query.name }}
   </div>
   
   // 函数内实现跳转
   <script>
   export default {
     data() {
       return {
         userId: 2,
       };
     },
     methods: {
       aboutClick() {
         this.$router.replace({  // 在其中传入对象，携带参数
           path:"/about",
           query: {
             name:'tom',
             age: 22
           }
         });
       },
       homeClick() {
         this.$router.replace("/home");
       },
     },
   };
   </script>
   ```

#### 路由的懒加载

一个 vue 文件就是一个组件，在打包时通常会将这些文件打包到同一个 js 文件中，这将会导致一个文件过大，降低用户体验，懒加载就是将不同的文件分别打包，只在使用的时候才会请求这个文件

```js
const Home = () => import('../components/Home')
const About = () => import('../components/about')
```

#### 嵌套子路由

```js
const routes = [
  {
    path: '/',
    redirect: '/home'
  },
  {
    path: '/home',
    component: Home,
    children: [  // 嵌套
      {
        path: 'news',
        component: HomeNews
      },
      {
        path: 'message',
        component: HomeMessage
      }
    ]
  },
]
```

#### 导航守卫 - [| Vue Router (vuejs.org)](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)

> 路由跳转是一个大过程，其中包括组件的变换，组件的声明周期，导航守卫用于监听路由跳转，相当于 flask 中的钩子函数

**全局守卫**

> 每个组件都会生效

```js
// index.js,
// 当从一个路由跳转到另一个路由就会执行这个函数
// 前置钩子
router.beforeEach((to:Route,from,next) => {
  document.title = to.matched[0].meta.title;
  console.log('跳转之前调用');
  next()  // 这里的next一定要有，不然就会执行跳转
  next(false) // 跳转中断，或者直接不写这个 next() 
  next({path:'/home'}) // 指定跳转地址，类似于重定向了
})

// 后置钩子
router.afterEach((to,from) => {
  console.log('跳转之后调用')
})
```

**路由独享守卫**

```js
const routes = [
  {
    path: '/',
    redirect: '/home',
    meta: {
      title: '首页'
    },
  },
  {
    path: '/about',
    component: About,
    meta: {
      title: '关于'
    },
    beforeEnter: (to,from,next) => {
      console.log('这是一个 about 页面的前置路由独享守卫')
      next() // 注意 next 是必须的，否则不会发生跳转
    }

]

const router = new VueRouter({
  routes,
  mode: 'history'
})
```

**组件内守卫**

```js
// Home.vue
export default {
  name: "Home",
  beforeRouteEnter (to,from,next) {
    console.log('这是一个组件内守卫')
  }
};
```

#### keep-alive

> 是 Vue 内置的一个组件，

```html
// HomeMessage.vue
<keep-alive>
    <router-view></router-view>
</keep-alive>
<script>
    export default {
        name: "HomeMessage",
        activated() {
            console.log("home-message - activated"); // 只有使用了 keep-alive 才能使用这两个钩子
        },
        deactivated() {
            console.log("home-message - deactivated");
        },
    };
</script>
```

**include**

> 字符串或正则表达式，只有匹配的组件才会被缓存

**exclude**

> 字符串或正则表达式，任何匹配的组件都会被排除缓存

```html
    <keep-alive exclude="HomeMessage,HomeNews"> // 使用 , 分割，中间没有空格
      <router-view></router-view>
    </keep-alive>
```



## 一些错误

1. export default { router } 使用这种格式 需要用变量的形式接收，如果你用的是import xx from '../'那么就错了. 如果你是使用以上格式 那么正确引用格式为. import {xx} from '.

   ```
   beforeCreate hook: "TypeError: this._router.init is not a function"
   
   // 如果导出部分是
   export default router
   // name 导入部分可以是
   import router from './router/index.js'
   ```

2. NavigationDuplicated: Avoided redundant navigation to current location: "/about?name=tom&age=22"

   冗余路由，

