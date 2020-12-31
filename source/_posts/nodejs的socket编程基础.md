### nodejs的socket编程基础

​	nodejs中要使用socketAPI进行TCP服务器编程需要使用net模块

```javascript
const net = require('net');
```

​	引入net模块之后就可以创建TCP服务器并进行相应操作

#### net模块主要组成

- net.Server
  - 用于创建服务器和绑定服务器事件回调函数
  - 在回调函数中会返回net.Socket对象
- net.Socket
  - 用于绑定socket连接事件回调函数

##### net.Server

- net.createServer()
  - 创建一个Server对象
- server.listen(port, host)
  - port为端口号，整数型
  - host为ip地址，字符串型
  - 根据传递的参数会创建TCP或IPC服务器
- server.close()
  - 关闭服务器
- server绑定事件回调函数
  - listening事件：指定监听端口host后触发
  - connection事件：有连接建立时触发
    - 回调函数会自动传入一个socket对象
  - close事件：关闭服务器时触发

```javascript
// app.js
const net = require('net');
const PORT = 3000;

const server = net.createServer();

server.on('listening', () => {
   console.log(`Server running on ${PORT}`);
});

server.on('close', () => {
   console.log('Server closed'); 
});

server.on('connection', (socket) => [
   console.log('New connection built');
   server.close();
]);

server.listen(PORT, '127.0.0.1');
```

##### net.Socket

​	用于创建和管理一个连接，nodejs中可以使用server的connection事件回调函数获得socket对象，而要直接创建socket对象使用net.connect()方法，返回一个socket对象。

- net.connect(port, host)
  - 创建一个socket连接，返回socket对象
  - 传入port为端口号，整型
  - host为ip地址，字符串
- socket.write(data)
  - 发送数据，传入的data可以是整型，字符串等类型
- socket.end()
  - 关闭socket连接
- socket事件
  - connect
    - 创建连接时使用，一般在回调函数中指定data事件回调函数
  - end
  - data
    - 获得数据时触发，回调函数中会自动传入data数据（二进制）
  - error

```javascript
// client.js
const net = require('net');
const PORT = 3000;

const socket = net.connect(PORT, '127.0.0.1');

socket.on('error', () => {
   console.log('Connecting failed');
});

socket.on('connect', () => {
   console.log('New connection built');

   socket.on('data', (buffer) => {
      console.log(buffer.toString());
      socket.end();
   });
});

socket.on('end', () => {
   console.log('Connection ended');
});
```



#### demo：在线计算器

​	通过TCP服务器连接，在client.js中输入计算表达式，app.js中获取表达式并返回计算表达式结果。

##### nodejs输入输出

​	用于在nodejs中输入表达式

```javascript
const readline = require('readline');

const rl = readline.createInterface({
   input: process.stdin,
   output: process.stdout
});

rl.on('line', (str) => {
   if (str === 'close') {
      rl.close();
   }
   console.log(str);
});
```

##### demo代码

```javascript
// app.js
const net = require('net');
const PORT = 3000;

// 创建TCP服务器
const server = net.createServer();

// 绑定事件回调函数
server.on('listening', () => {
   console.log(`Server running on port ${PORT}`);
});

server.on('close', () => {
   console.log('Server closed');
});

server.on('connection', (socket) => {
   console.log('New connection built');
   socket.write('Calculator(Input your line plz):');
	
    // 接收表达式，使用eval函数直接取得运行结果
   socket.on('data', (buffer) => {
      const line = buffer.toString();
      const result = eval(line);
      socket.write(`result: ${result}`);
   });
});

// 监听端口
server.listen(PORT, '127.0.0.1');
```

```javascript
// client.js
const readline = require('readline');
const net = require('net');
const PORT = 3000;

// 创建socket连接
const socket = net.connect(PORT, '127.0.0.1');

// 绑定事件回调函数
socket.on('end', () => {
   console.log('Connection ended');
});

socket.on('error', () => {
   console.log('Connecting failed');
});

socket.on('connect', () => {
   console.log('New connection built');
});

socket.on('data', (buffer) => {
    // 接收结果，进行显示
   console.log(buffer.toString());
});

// 输入处理
const rl = readline.createInterface({
   input: process.stdin,
   output: process.stdout
});

rl.on('line', (str) => {
   if (str === 'close') {
      rl.close();
   }
    // 发送表达式
   socket.write(str);
});
```

​	运行结果：

```powershell
PS D:\vsCode_local\tcp_server> node app.js
Server running on port 3000
New connection built

PS D:\vsCode_local\tcp_server> node client.js
New connection built
Calculator(Input your line plz):
1+1
result: 2
2+2
result: 4
2*100000
result: 200000
173*229
result: 39617
```



