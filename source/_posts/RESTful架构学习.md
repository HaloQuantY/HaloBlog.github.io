---
title: RESTful架构学习
---

​	REST是`(Resources)Representational State Transfer`的缩写，有译为“表现层状态转化”。

##### 资源Resources

​	资源是网络中的信息实体，获得资源可以访问资源的URL，用URL来标识资源。

##### 表现层Representationa

​	资源的表现方式，各种资源有不同的文件格式，这些文件格式可以在HTTP的头部“Content-Type”中体现。

##### 状态转化State Transfer

​	由于HTTP是无状态协议，资源文件的状态都在服务器中保存。如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。

​	客户端利用HTTP的动词实现状态转换，即GET获取资源，POST新建或更新资源，PUT更新资源，DELETE删除资源。

##### 综述

​	RESTful是利用URL对资源进行唯一识别，利用HTTP动词进行操作识别的API架构。服务器利用HTTP特性，使得客户端可以利用HTTP协议对服务器暴露的API进行请求，完成数据增删改查操作。



### RESTful API设计

#### 搭建服务器

##### 监听端口

​	使用Express框架可以快捷搭建服务器，在初始化包后，引入Express模块创建Express实例即可使用listen方法监听端口。

```javascript
const express = require("express");
const app = express();
// process.env.PORT是系统的环境变量，尽量取用环境变量中端口
const port = process.env.PORT || 8000;




app.listen(port, function() {
   console.log(`Server running on port ${port}`);
});
```

##### 设计路由

​	使用不同的中间件，对客户端请求进行响应。

```javascript
const express = require("express");
const app = express();
const port = process.env.PORT || 8000;

// 利用express实例的METHOD方法可以实现对某路径响应，并访问res对象写入响应
app.get("/", function(req, res) {
   res.send("Hello World!");
});

app.get("/api/users", function(req, res) {
   res.send([1, 2, 3]);
});

app.get("/api/users/:id", function(req, res) {
   res.send(req.params.id); // url中的变量保存在req对象params属性
});

app.get("/api/posts", function(req, res) {
   res.send(req.query); // url中的查询变量存储在req的query属性
});
// http://127.0.0.1:8000/api/posts/?sortBy=name将会返回{"sortBy": "name"}

app.listen(port, function() {
   console.log(`Server running on port ${port}.`);
});
```

​	当请求资源不存在时，使用res.status()写入状态

```javascript
app.get("/api/users/:id", function(req, res) {
   const user = users.find(user => user.id === parseInt(req.params.id));
   if (!user) {
      res.status(404).send("No matching user.");
   }
   res.send(user);
});
```

​	post方法写入资源时，需要加载中间件json来解析req对象的报文体部分。并且需要对请求内容进行检测。

```javascript
app.use(express.json());

app.post("/api/users", function(req, res) {
   const user = {
      id: users.length + 1,
      name: req.body.name,
      age: req.body.age
   };
   users.push(user);
   res.send(user);
});
```

​	请求内容检测：Joi库

```javascript
const Joi = require("joi");

const schema = Joi.object({
      name: Joi.string().min(3).required(),
      age: Joi.number().integer().min(0).max(200)
   });

const {error, value} = schema.validate(req.body);

if (error) {
    res.status(400).send(error.details[0].message);
    return;
}
```

​	使用put方法更新资源(更新后的资源位于服务器内存中)

```javascript
app.put("/api/users/:id", function(req, res) {
   const user = users.find(user => user.id === parseInt(req.params.id));
   if (!user) {
      res.status(404).send("No matching user.");
   }

   const {error, value} = validateUsers(req.body);
   if (error) {
      res.status(400).send(error.details[0].message);
      return;
   }

   user.name = req.body.name;
   user.age = req.body.age;
   res.send(user);
});
```

