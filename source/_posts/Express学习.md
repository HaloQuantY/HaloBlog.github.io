---
title: Express框架学习
---

### Hello World

```javascript
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`))
```

​	其中，app.get函数设置了响应访问该服务器端口时的响应。当url为根目录时将返回一个“Hello World”字符串；



### Express-generator

​	应用生成工具，用于快速搭建app骨架。

#### 基本使用

```javascript
// 安装express-generator包
$ npm install -g express-generator
$ express

// 在当前目录下myapp文件夹创建app
$ express --view=pug myapp

// 下载依赖
$ cd myapp
$ npm install
```



### 基础路由

​	路由决定app如何根据URL和HTTP请求方法（GET，POST等），响应客户端请求。每个路由都有一个或多个处理函数，用于在路由匹配时的响应。也可以使用app.all()处理所有HTTP请求方法，并用app.use()来指定中间件作为回调函数。

​	通常路由定义遵循以下结构

```javascript
app.METHOD(PATH, HANDLER);
```

​	其中，METHOD是HTTP请求方法类型（GET，POST），以小写表示

```javascript
app.get('/', function (req, res) {
  res.send('Hello World!')
})

app.post('/', function (req, res) {
  res.send('Got a POST request')
})
```

​	这些路由方法定义了一个回调函数，当app接收指向特定路径的请求时进行调用。也就是说app监视匹配路由的请求并调用定义回调函数。

​	当路由方法拥有多个回调函数作为参数时，需要在回调函数中定义一个`next`函数并在回调函数体中调用`next()`控制下一个回调。

#### 路由路径

​	路由路径和请求方法结合，定义了请求能够达到的端点。路由路径可以是字符串，正则表达式。如果路径中需要使用`$`，需要将它包裹成`[\$]`形式，如`/data/([\$])book`。

#### 路由处理函数

​	可以定义多个回调函数，类似中间件一样处理请求。

```javascript
var cb0 = function (req, res, next) {
  console.log('CB0')
  next()
}

var cb1 = function (req, res, next) {
  console.log('CB1')
  next()
}

var cb2 = function (req, res) {
  res.send('Hello from C!')
}

app.get('/example/c', [cb0, cb1, cb2])
```

##### app.route()

​	为一个路径创建链式处理函数

```javascript
app.route('/book')
  .get(function (req, res) {
    res.send('Get a random book')
  })
  .post(function (req, res) {
    res.send('Add a book')
  })
  .put(function (req, res) {
    res.send('Update the book')
  })
```





### 静态文件

​	静态文件是如CSS，图片和JS脚本之类文件，需要使用`express.static`内置中间件函数。

```javascript
express.static(root, [options])
```

​	利用app.use()方法将静态文件夹下文件对外访问

```javascript
app.use(express.static('public'))
```

​	访问的URL中只会出现文件名而没有路径

```
http://localhost:3000/images/kitten.jpg
http://localhost:3000/css/style.css
http://localhost:3000/js/app.js
http://localhost:3000/images/bg.png
http://localhost:3000/hello.html
```

​	创建虚拟路径

```javascript
app.use('/static', express.static('public'))
```

​	访问虚拟路径下的文件

```
http://localhost:3000/static/images/kitten.jpg
http://localhost:3000/static/css/style.css
http://localhost:3000/static/js/app.js
http://localhost:3000/static/images/bg.png
http://localhost:3000/static/hello.html
```

​	创建绝对路径

```javascript
app.use('/static', express.static(path.join(__dirname, 'public')))
```



### 中间件

#### 开发中间件

​	中间件函数是可以访问app请求-响应循环中request对象（req），response对象（res）和next函数的函数。在路由中，当前中间件函数执行完毕后会调用next函数来执行下一个中间件函数。

##### 中间件函数作用

- 执行任意代码

- 改变request和response对象

- 结束请求-响应循环

- 调用栈中下一个中间件函数

  如果当前中间件函数没有结束请求-响应循环，那么它必然调用next函数传递下一个中间件，否则请求将会被挂起。

  ```javascript
  // 中间件就是接收请求后的回调函数
  function middleware(req, res, next) {
  	res.send("middleware");
  	next();
  }
  
  app.get("/", middleware);
  
  app.listen(8000);
  ```

##### 直接使用app.use()方法加载中间件

​	app.use()可以加载和定义中间件，并且每次有请求时都会调用中间件。

```javascript
var express = require('express')
var app = express()

var myLogger = function (req, res, next) {
  console.log('LOGGED')
  next()
}

app.use(myLogger)

app.get('/', function (req, res) {
  res.send('Hello World!')
})

app.listen(3000)
```

​	但是如果把下面get路由和上放myLogger中间件调换顺序，则myLogger不会执行，因为get路由的中间件终止了请求-响应循环。调换顺序后只有在get中间件内调用next函数才会执行myLogger中间件。

#### 使用中间件

​	Express是基于路由和中间件的web开发框架，而最基本的单元就是中间件，Express开发应用是由一系列中间件函数调用组成的。

​	Express应用可以使用以下种类中间件：

- 应用级中间件

- 路由级中间件

- 错误处理中间件

- 内置中间件

- 第三方中间件

  应用级和路由级中间件可以被加载到某一个路径，当对某路径加载多个中间件函数时会创建一个该路径下的中间件系统子栈。

##### 应用级中间件

​	应用级中间件使用`app.use()`和`app.METHOD()`进行绑定，当中间件函数没有绑定到路径时，每次端口接收到请求都会执行中间件。

```javascript
app.get('/user/:id', function (req, res, next) {
  // if the user ID is 0, skip to the next route
  if (req.params.id === '0') next('route')
  // otherwise pass the control to the next middleware function in this stack
  else next()
}, function (req, res, next) {
  // send a regular response
  res.send('regular')
})

// handler for the /user/:id path, which sends a special response
app.get('/user/:id', function (req, res, next) {
  res.send('special')
})
```

​	可以在next中传入“route”跳过当前路径中间件子栈，将控制权交给下一个路由（仅对METHOD绑定中间件有效）

##### 路由级中间件

​	路由级中间件和应用级中间件原理类似，只是需要绑定给一个express.Router()实例。

```javascript
var app = express()
var router = express.Router()

// a middleware function with no mount path. This code is executed for every request to the router
router.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
})

// a middleware sub-stack shows request info for any type of HTTP request to the /user/:id path
router.use('/user/:id', function (req, res, next) {
  console.log('Request URL:', req.originalUrl)
  next()
}, function (req, res, next) {
  console.log('Request Type:', req.method)
  next()
})

// a middleware sub-stack that handles GET requests to the /user/:id path
router.get('/user/:id', function (req, res, next) {
  // if the user ID is 0, skip to the next router
  if (req.params.id === '0') next('route')
  // otherwise pass control to the next middleware function in this stack
  else next()
}, function (req, res, next) {
  // render a regular page
  res.render('regular')
})

// handler for the /user/:id path, which renders a special page
router.get('/user/:id', function (req, res, next) {
  console.log(req.params.id)
  res.render('special')
})

// mount the router on the app
app.use('/', router)
```

