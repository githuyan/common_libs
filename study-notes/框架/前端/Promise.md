# Promise

> 在ES6中的特性，是异步编程的一种解决方案，当有异步操作时，对异步操作进行封装。链式编程

### promise 的调用方式与处理

resolve: 成功是调用的方法

reject: 失败时调用的方法

```html
<script>
    new Promise((resolve, reject) => {
        setTimeout((data) => {
            resolve('这是一些异步代码')
        }, 1000)
    }).then((data) => {
        console.log(data)
    }).catch((err) => {
        console.log('失败')
    })

    // 第二种写法
    new Promise((resolve, reject) => {
        setTimeout(function () {
            reject('dfdsfssdff')
        }, 1000)
    }).then(data => {
        console.log('then 可以传入两个参数，第一个参数是成功后调用')
    }, error => {
        console.log('第二个参数是失败后调用')
    })
</script>
```

### promise 的链式调用

> 流程是，创建 Promise 对象，传入 resolve,reject 这是两个处理函数（成功时，失败时），在异步函数中调用这两个函数进行处理，之后的结果再 then 中，then 中又可以再次调用 Promise 对象，再对上一步的结果进行处理

```html
<script>
    new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('aaa')
        }, 1000)
    }).then(res => {
        console.log(res, '--第一层的处理代码')

        return new Promise((resolve, reject) => {
            console.log('接受参数')
            resolve('传递参数')
        })
    }).then(res => {
        console.log(res + '--第二层的处理代码')

        return Promise.resolve(res+'这是第二种一种方式') // 这里直接调用 Promise.resolve 而不是之后再单独 resolve
    }).then(res => {
        console.log(res,'----这是第三层')

        return '这是第三重方式'  // 这里并没有显式的指定 Promise 但是也可以调用 then
    }).then(res => {
        console.log(res)
    }).catch( err => {
        console.log(err)
    })
</script>
```

