# WebPack

> 是一个现代的javascript应用的静态模块化打包工具
>
> 依赖各种包，使用npm（node package manager）包管理工具
>
> 在本地执行 安装的包命令如 vue ，会默认在全局找

### 

## 基础使用

### 一个项目的流程

```javascript
// 结构
app/
	src/
    	js/
    	css/
    	main.js
    dist/
    index.html
// webpack 配置，npm init 初始化包的名字，"build": "webpack-one" ，脚本用户名映射
webpack.config.js

const path = require('path')
moudle.exports = {
    entry: './src/main/js', // 入口
    output: {
        path: path.resolve(__dirname__,'dist'),
        filename: 'bundle.js'
    }
}

// npm install webpack@3.6.0 --save-dev, 本地化引入，出新 node_module 文件

```



### 安装

```javascript
// 全局安装
npm install webpack@3.6.0 -g

// 局部安装
npm install webpack@3.6.0 

// 安装开发时依赖
npm install webpack@3.6.0 --save-dev

// 安装运行时依赖
npm install vue@3.6.0 --save
```

### 配置

> 开发时依赖( devDependencies ) 和 运行时依赖，如webpack本身，只在打包时用到，属于开发时依赖

**注意**

只要再项目中引用了 node 中的模块，都要进行 node 的初始化

1. `npm init `填写相关信息，（文件名是必填项）

2. `npm install` 会自动下载所有依赖的所有 node 模块

3. 会自动生成 package.json 文件

4. 修改 scripts（脚本），使得 `webpack` 命令映射到 `npm run` 命令

   ```javascript
   // 注意，直接使用 webpack 打包命令会优先使用全局包，
   // 而修改映射后,使用脚本命令打包（npm run build），会优先使用本地包
   
   // 如果想独立项目，需要将包下载到本地，便于发布
   npm install webpack@3.6.0 --save-dev 
   "devDependencies": {
       "webpack": "^3.6.0"
   }
   ```

   ```javascript
   {
     "name": "meetwebpack",
     "version": "1.0.0",
     "description": "",
     "main": "index.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack"  // 修改打包映射条件到 webpack -> build 
         // 注意，直接使用 webpack 打包命令会优先使用全局包，
         //而修改映射后会优先使用本地包（npm run build）
     },
     "author": "",
     "license": "ISC",
     "devDependencies": {
       "webpack": "^3.6.0"
     }
   }
   ```

```javascript
// webpack.config.js
const path = require('path')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,),  // 这里引用了 node 的 path 模块
        filename: 'boundle.js'
    },
}
```

## loader

> 扩展
>
> 高级用法，转化 css ,less,
>
> webpack.js.org

### css 文件的打包加载

**流程**

1. 在入口 js 文件中引入 css 文件，使得 css 文件与主文件产生联系
2. 下载相关的 loader 和 依赖
3. 下载对应的 loader 并在 webpack.config.js 文件中进行 module 配置（注意多个 loader 的顺序）

**注意：**

需要`npm install css-loader@xxx --save-dev`，同时注意 `style-loader`的版本一致，如果出现权限问题，应该使用 管理员身份 下载

```javascript
// main.js
import {other} from "./js/testi"
import style from './css/normal.css' // 在入口文件中引入，建立联系

other();

// wepack.config.js
const path = require('path')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,) + "\\dist",
        filename: 'boundle.js'
    },
    module: {  // 新增模块，规则
        rules: [
            {
                test: /\.css$/, // 匹配 .css 结尾的文件
                // css-loader 只负责将 css 文件进行加载
                // style-loader 负责将样式添加到 DOM 中
                // 使用流程应该是，先加载 css 文件，在渲染样式（style-loader），但是，由于use，加载顺序是从左到右，所以 style-loader 放在 css-loader 前
                use: ['style-loader', 'css-loader']
            }
        ]
    },
}
```

### CSS预处理器的打包加载

1. 在入口 js 文件中引入 css 文件，使得 css 文件与主文件产生联系
2. 下载相关的 loader 和 依赖 `npm install --save-dev less-loader@4.1.0 less@3.9.0`
3. 下载对应的 loader 并在 webpack.config.js 文件中进行 module 配置（注意多个 loader 的顺序）

```javascript
// main.js
import {other} from "./js/testi"
import style from './css/normal.css'
other();
require('./css/special.less')

// webpack.config.js
const path = require('path')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,) + "\\dist",
        filename: 'boundle.js'
    },
    module: {
        rules: [
            
            
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.less$/,
                // 意义为 ，先使用 less-loader 将 css 文件预处理，然后使用 css-loader 加载 css 文件到项目，最后使用 style-loader 将样式渲染到页面
                // 也可以使用数组，而不是对象
                use: [{
                    loader: "style-loader" // creates style nodes from JS strings
                }, {
                    loader: "css-loader" // translates CSS into CommonJS
                }, {
                    loader: "less-loader" // compiles Less to CSS
                }]
            },
        ]
    },
}
```

各种资源的处理

> P81-webpack,图片文件的处理

### ES6语法处理

> npm install --save-dev  babel-loader@7 babel-core  babel-preset-es2015

## webpack.confg.js 配置

1. entry 打包文件的入口
2. output 打包文件的出口
3. module 各种 loader，用各种规则加载各种文件
4. resolve 各种小问题的解决办法
5. plugin 插件

```javascript
const path = require('path')
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,"dist"),
        filename: 'bundle.js',
        // publicPath: "dist/" // 生成的 bundle.js 的前缀路径
    },
    resolve: {
        alias: {
            'vue$': 'vue/dist/vue.esm.js' // 解决 runtime-only 和 runtime-compiler 问题
        },
        extensions:['.vue','.js','.css'] // 用于在导入时，可以省略扩展名
    },
    module: {
        rules: [
            {
                test: /\.css$/,  // 正则表达式匹配文件
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.vue$/,
                use:['vue-loader']
            }
        ]
    },
    plugins: [
        new webpack.BannerPlugin('最终版权归官方所有'),
        new HtmlWebpackPlugin({
            template:"index.html" // 以配置文件为根路径，以此文件为模板打包 index.html
        }),
        new UglifyjsWebpackPlugin() // js 丑化压缩
    ]

}
```



## 在webpack中配置 Vue

```javascript
// index.html
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</Title>
    </head>
    <body>
        <p> 嵌套，loaders</p>
        <div id="app">
            {{message}}
        </div>

        <script src="./dist/boundle.js"></script>  // 文件的引入放在最后
    </body>
</html>

// main.js
import Vue from "vue";  // 导入 Vue 与下文配置文件中的 vue$ 

new Vue({
    el:"#app",
    data: {
        message: '消息'
    }
})

// webpack.config.js
const path = require('path')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,) + "\\dist",
        filename: 'boundle.js',
        publicPath: "dist/"
    },
    module: {
        rules: [
            resolve: { // 解决 runtime-only 和 runtime-compiler 问题
                alias: {
                    'vue$': 'vue/dist/vue.esm.js'
                }
            }
	]
}
```

## plugin

> 插件，loader像是一个转化器，plugin是一个扩展器，在 webpack.config.js 中

#### **版权声明** -

```javascript
const path = require('path')
const webpack = require('webpack')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,"dist"),
        filename: 'bundle.js',
        publicPath: "dist/"
    },
    resolve: {
        alias: {
            'vue$': 'vue/dist/vue.esm.js'
        },
        extensions:['.vue','.js','.css'] // 用于在导入时，可以省略扩展名
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.vue$/,
                use:['vue-loader']
            }
        ]
    },
    plugins: [
        new webpack.BannerPlugin('最终版权归官方所有')
    ]

}
```

#### 打包 html 

#### 搭建本地服务器

> 在本地搭建web服务器，会将本地代码现在内存中渲染实现，实时交互，便于调试，之后再使用 npm run build 打包到磁盘

1. 安装 webpack-dev-server@2.9.3

2. 在 webpack.config.js 中配置 devServer

   ```javascript
   devServer: {
       contentBase: './dist',
           inline: true
   }
   ```

3. 设置快捷方式

   ```javascript
   {
     "name": "meetwebpack",
     "version": "1.0.0",
     "description": "",
     "main": "index.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack",
       // "dev": "webpack-dev-server" // 默认会在 8080 端口开启一个服务器
       "deb":"webpack-dev-server --open" // 加 --open 自动打开服务
     },
     "author": "",
     "license": "ISC",
     "dependencies": {
       "vue": "^2.5.21"
     },
     "devDependencies": {
       "html-webpack-plugin": "^3.2.0",
       "uglifyjs-webpack-plugin": "^1.1.1",
       "vue-loader": "^13.0.0",
       "vue-template-compiler": "^2.5.21",
       "webpack": "^3.6.0",
       "webpack-dev-server": "^2.9.3"
     }
   }
   
   ```

   

### 分离配置文件

> 有的配置是在开发时使用的，有的配置是在生产时使用的，就想 webpack，vue ，结构化配置文件

**注意：**

1. 相关文件的同步问题
2. 相对路径问题

使用 webapck-merge@4.1.5 来将分离的配置文件合并使用

```javascript
// buidl/base.config.js 
const path = require('path')
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname,"dist"),
        filename: 'bundle.js',
        // publicPath: "dist/" // 生成的 bundle.js 的前缀路径
    },
    resolve: {
        alias: {
            'vue$': 'vue/dist/vue.esm.js' // 解决 runtime-only 和 runtime-compiler 问题
        },
        extensions:['.vue','.js','.css'] // 用于在导入时，可以省略扩展名
    },
    module: {
        rules: [
            {
                test: /\.css$/,  // 正则表达式匹配文件
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.vue$/,
                use:['vue-loader']
            }
        ]
    },
    plugins: [
        new webpack.BannerPlugin('最终版权归官方所有'),
        new HtmlWebpackPlugin({
            template:"index.html" // 以配置文件为根路径，以此文件为模板打包 index.html
        }),
    ],

}

// build/dev.config.js
const webpackMerge = require('webpack-merge')
const baseConfig = require('./base.config') 

module.exports = webpackMerge(baseConfig, {  // 继承基础配置
    devServer: {
        contentBase: './dist',
        inline: true
    }

})

// build/prod.config.js
const webpackMerge = require('webpack-merge')
const UglifyjsWebpackPlugin = require("uglifyjs-webpack-plugin")
const baseConfig = require('./base.config')

module.exports = webpackMerge(baseConfig, {
    plugins: [
        new UglifyjsWebpackPlugin(), // js 丑化压缩
    ],
})
```

使用到配置文件的地方都需要更改 --config 

```javascript
{
  "name": "meetwebpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack", // 指定脚本，（快捷方式）
    "build": "webpack --config ./build/prod.config.js", // 自定义脚本路径
    "dev": "webpack-dev-server --open --config ./build/prod.config.js" // 服务器也需要配置
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "vue": "^2.5.21"
  },
  "devDependencies": {
    "html-webpack-plugin": "^3.2.0",
    "uglifyjs-webpack-plugin": "^1.1.1",
    "vue-loader": "^13.0.0",
    "vue-template-compiler": "^2.5.21",
    "webpack": "^3.6.0",
    "webpack-dev-server": "^2.9.3",
    "webpack-merge": "^4.1.5"
  }
}

```

