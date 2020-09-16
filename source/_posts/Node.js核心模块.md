---
title: Node核心模块
---

### fs文件系统模块

`const fs = require("fs")`引入模块，用于操作文件。fs中几乎所有方法都有同步和异步两种模式，同步方法会直接返回结果而异步方法需要回调函数来进行结果返回。

#### Buffer缓冲区

​	Buffer结构和操作和数组相似，但Buffer能够存储二进制文件。即Buffer可理解为存储二进制文件的数组。

- Buffer.from(str)

  将字符串转换为Buffer结构

- length属性

  占用内存的大小

- Buffer.alloc(n)

  创建大小为n的Buffer结构

#### 文件写入

1. 打开文件
2. 向文件写入内容
3. 保存并关闭文件

##### 同步写入

1. fs.openSync(path, flags, mode)：

   path文件路径，flags打开文件操作类型（r只读w可写），mode设置操作权限（可选）

   返回一个文件描述符fd（数字）

2. fs.writeSync(fd, string, position, encoding)

   fd文件描述符，string写入内容，position写入位置（可选），encoding编码方式（可选）

3. fs.closeSync(fd)

   fd文件描述符

##### 异步写入

1. fs.open(path, flags, mode, callback)

   callback回调函数两参数：err错误对象，fd文件描述符

2. fs.write(fd, string, position, encoding, callback)

   callback参数：err

3. fs.close(fd, callback)

   callback参数：err

##### 简单文件写入

异步写入fs.writeFile(file, data, options,  callback)

​	file文件路径，data写入数据，options写入选项（可选），callback回调函数

​	callback参数err

​	options对象添加属性flag属性值为a，则会自动追加内容

##### 流式文件写入

​	适合大文件持续写入，不易内存溢出。

- 创建一个可写流fs.createWriteStream(path, options)

  path可写流要绑定的文件路径

  options写入选项，可选

  返回值为

- ws.write(data)方法

  只要流不关闭就可以一直写入

- ws.end()

  关闭可写流

#### 文件读取

##### 简单文件读取

异步读取

fs.readFile(path, options, callback)

​	callback回调函数参数：err错误对象，data读取到数据(Buffer结构)

##### 流式读取

​	读取大文件，可以分多次将文件读入内存。

- 创建可读流fs.createReadStream(path, options)
- 读取可读流数据
  - 绑定data事件`rs.on("data", function(data) {})`其中回调函数参数为读取到数据
- 关闭可读流rs.end()

#### 管道

​	将可读流数据传输到可写流的快捷方法，是可读流的一个方法。

- rs.pipe(ws)自动完成流的启动和关闭



### HTTP模块

```javascript
// 搭建HTTP服务器
var http = require('http'); // 引入http模块

http.createServer(function (request, response){
  response.writeHead(200, {'Content-Type': 'text/plain'});
  response.write("Hello World");
  response.end();
}).listen(8080, '127.0.0.1');

console.log('Server running on port 8080.');
```

#### createServer方法

- 参数为一个函数，该函数接收两个参数request和response。两个参数都是对象，其中request表示客户端的请求，response表示服务器端http响应

#### response.writeHead方法

- 写入HTTP响应报文的头信息

#### response.write

- 写入HTTP响应报文主体信息

#### response.end

- 关闭对话，也可以直接传入data写入HTTP响应的具体内容

#### listen

- 启动服务器实例，监听指定端口

```javascript
// 根据不同请求显示不同内容
var http = require('http');

http.createServer(function(req, res) {

  // 主页
  if (req.url == "/") {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Welcome to the homepage!");
  }

  // About页面
  else if (req.url == "/about") {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Welcome to the about page!");
  }

  // 404错误
  else {
    res.writeHead(404, { "Content-Type": "text/plain" });
    res.end("404 error! File not found.");
  }

}).listen(8080, "localhost");
```

