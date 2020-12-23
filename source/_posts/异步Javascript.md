## 异步Javascript

- 阻塞
  - JavaScript是单线程语言，正常情况下同一时间只会执行一个任务，在执行负荷重的代码时会产生阻塞，即web应用产生密集运算无法将控制权返回浏览器，浏览器无任何响应。
  - web workers可以开启js多线程，但是在一些逻辑中无法保证任务的先后顺序
  - 异步Javascript可以解决阻塞和任务顺序问题
- 两种异步代码
  - 传统异步回调函数：XHR（但不是所有回调函数都是异步执行）
  - promise：fetchAPI为代表
- promise优点
  - 使用链式then函数进行连接，避免回调地狱
  - promise中回调函数严格遵守事件队列顺序
  - 使用catch函数错误处理更加容易
  - 回调函数传入第三方库时严格受控

#### Timeout和Interval

- setTimeout： 
  - 指定延迟后执行代码
- setInterval： 
  - 按指定周期重复执行代码
- requestAnimationFrame： 
  - 现代版setInterval，在浏览器下次重渲染之前执行代码，使动画在不同环境下都已合适帧率运行

##### 递归setTimeout和setInterval区别

- 递归setTimeout
  - 保证执行完毕到下次执行开始之间延迟相同，函数执行完毕之后等待指定事件然后再执行
- setInterval
  - 执行完毕到下次执行开始之间延迟不同，如果interval值为100ms，而执行需要40ms，则执行完毕到下次执行仅等待60ms
- 递归setTimeout可以在递归内部改变延迟数值

#### Promise

​	Promise本质上是一个对象，代表操作的中间状态，保证在未来可能返回某种结果。

​	promise和事件监听器区别：

- 一个promsie状态只能够改变一次
- promise的回调函数可以在执行函数执行后指定

##### Promise术语

- 创建promise时，初始既非成功也非失败状态，这个状态叫做pending（待定）
- promise返回时，成为resolved（已解决）
  - 成功resolved的promise称为fullfilled（实现）。它返回一个值可以通过.then()进行访问
  - 不成功resolved的promise称为rejected（拒绝）。它返回一个原因（reason），可以通过.catch()访问原因

##### 自定义Promise

​	以下代码实现了Promise基本功能（处理异步和同步函数，链式调用以及异常穿透）：

```javascript
(function(window) {
   const PENDING = 'PENDING';
   const FULLFILLED = 'FULLFILLED';
   const REJECTED = 'REJECTED';

   class myPromise{
      constructor(excutor) {
         this.status = PENDING;
         this.data = undefined;
         this.onResolvedCallbacks = [];
         this.onRejectedCallbacks = [];

         const resolve = (value) => {
            this.status = FULLFILLED;
            this.data = value;
            if (this.onResolvedCallbacks.length > 0) {
               setTimeout(() => {
                  this.onResolvedCallbacks.forEach((item) => {
                     item();
                  });
               }, 0);
            }
         };

         const reject = (reason) => {
            this.status = REJECTED;
            this.data = reason;
            if (this.onRejectedCallbacks.length > 0) {
               setTimeout(() => {
                  this.onRejectedCallbacks.forEach((item) => {
                     item();
                  });
               }, 0);
            }
         };

         try {
            excutor(resolve, reject);
         } catch (e) {
            reject(e);
         }
      }

      then(onResolved, onRejected) {
         onResolved = typeof onResolved === 'function' ? onResolved : (value) => value;
         onRejected = typeof onRejected === 'function' ? onRejected : (reason) => {throw reason};

         return new myPromise((resolve, reject) => {
             // handle函数用于决定返回promise的状态，由于需要上个promise的结果，因此借用then的回调函数对之前promise的data进行处理，然后根据处理的结果result判断（resolve，reject）本promise的状态
            const handle = (callback) => {
               try {
                  let result = callback(this.data);

                  if (typeof result === myPromise) {
                     // 如果链式调用上级处理结果类型为promise，则用then函数取得该promise结果
                     result.then(resolve, reject);
                  } else {
                     resolve(result);
                  }
               } catch (e) {
                  reject(e);
               }
            };

            if (this.status === FULLFILLED) {
               setTimeout(() => {
                  handle(onResolved);
               }, 0);
            }
   
            if (this.status === REJECTED) {
               setTimeout(() => {
                  handle(onRejected);
               }, 0);
            }
   
            if (this.status === PENDING) {
               this.onResolvedCallbacks.push(() => {
                  handle(onResolved);
               });
   
               this.onRejectedCallbacks.push(() => {
                  handle(onRejected);
               });
            }
         });
      }

      catch(onRejected) {
         return this.then(undefined, onRejected);
      }
   }

   window.myPromise = myPromise;
})(window);
```

### Generator函数

​	Generator函数本质上是一个封装了多个内部状态的状态机，返回值为一个遍历器对象，因此也是一个遍历器对象生成函数。

##### 遍历器Iterator

​	遍历器用来为各种不同数据结构提供统一访问机制，本质上是一个指针对象。遍历器让成员有序排列，并且可以使用for of循环进行遍历。

- next方法

  - 返回一个对象，包含成员信息和遍历信息（value， done）

  ```javascript
  var it = makeIterator(['a', 'b']);
  
  it.next() // { value: "a", done: false }
  it.next() // { value: "b", done: false }
  it.next() // { value: undefined, done: true }
  
  function makeIterator(array) {
    var nextIndex = 0;
    return {
      next: function() {
        return nextIndex < array.length ?
          {value: array[nextIndex++], done: false} :
          {value: undefined, done: true};
      }
    };
  }
  ```

- for of循环

  - 对数据结构使用for of进行遍历时会自动寻找遍历器接口

##### Generator函数特征

- function和函数名间有一星号*

- 函数体内部使用yield表达式

  ```javascript
  function* helloWorldGenerator() {
    yield 'hello';
    yield 'world';
    return 'ending';
  }
  
  var hw = helloWorldGenerator();
  ```

##### Generator函数基本使用

​	函数内部使用yield语句定义不同状态，调用函数后返回值为一个遍历器对象，可以使用next方法获取yield所定义的状态。

```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();

hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```

​	这种遍历器对象的性质让Generator函数实际上分段执行，当返回的遍历器对象使用next方法时，Generator函数向下一状态执行。

##### yield表达式

​	yield表达式在Generator函数中像是暂停标志，当生成的遍历器对象调用next方法时，会从当前位置执行直到下一个yield表达式，并获得下一个yield表达式定义的状态值。

- next方法执行逻辑
  - 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
  - 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式
  - 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
  - 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

- yield表达式后面的表达式，调用next方法时才会执行，因此提供了手动的惰性求值功能

- yield表达式没有返回值，next方法可以带一个参数作为上一个yield表达式返回值

  ```javascript
  function* f() {
    for(var i = 0; true; i++) {
      var reset = yield i;
      if(reset) { i = -1; }
    }
  }
  
  var g = f();
  
  g.next() // { value: 0, done: false }
  g.next() // { value: 1, done: false }
  g.next(true) // { value: 0, done: false }
  
  // 这种性质可以让函数开始运行之后再通过next方法的参数向其中注入值
  // 由于next参数是上次yield返回值，第一次使用next方法传入的参数会被忽略
  ```

##### yield*表达式

​	Generator函数内部调用另一个Generator函数，需要在前者函数内部手动完成遍历

```javascript
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  // 手动遍历 foo()
  for (let i of foo()) {
    console.log(i);
  }
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// x
// a
// b
// y
```

​	而使用yield*表达式就是自动执行另一个Generator函数

```javascript
function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  for (let v of foo()) {
    yield v;
  }
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "a"
// "b"

function* concat(iter1, iter2) {
  yield* iter1;
  yield* iter2;
}

// 等同于

function* concat(iter1, iter2) {
  for (var value of iter1) {
    yield value;
  }
  for (var value of iter2) {
    yield value;
  }
}
```

### async函数

​	async函数是Generator函数的语法糖。

Generator函数：

```javascript
const fs = require('fs');

const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

async函数：

```javascript
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

​	async函数将*替换成async，yield替换成await。

##### 对Generator函数改进

- 内置执行器
  - 不需要调用next方法，可以和普通函数一样直接运行
- 更好语义
- 更广适用性
- 返回值是promise
  - 返回值是promise对象，可以使用then方法指定回调函数
  - async函数可以看作有多个异步操作所包装成的promise对象，await命令就是内部then命令语法糖

##### async函数语法

- 返回promise对象
  - async函数内部return语句返回值会称为then方法回调函数参数
- promise对象状态变化
  - async函数返回promise对象，需要内部所有await命令后的promise对象执行完才会改变状态，除非有return语句或抛出错误
- await命令返回值
  - await命令后是一个promise对象，返回该promise结果
  - 不是promise对象则返回对应值
  - 内部定义有then方法的对象也可以使用await语句取得结果
  - await命令后promise变为reject状态，则reject参数会被catch方法回调函数接收
- 错误处理
  - 如果await后异步操作出错，那么等同于async函数返回promise对象被reject

##### async函数缺点

​	async函数让异步操作变得更像同步操作，但同时某些情况也确实和同步操作一样产生阻塞。

```javascript
async function makeResult(items) {
 let newArr;
 for(let i=0; i < items.length; i++) {
  newArr[i].push('word_'+i);
 }
 return newArr;
}

async function getResult() {
 let result = await makeResult(items); // Blocked on this line
 useThatResult(result); // Will not be executed before makeResult() is done
}
```

​	当有大量await语句处理promise时，每个await语句都需要等待之前的await语句处理完成，降低程序效率。

```javascript
function timoutPromise(interval) {
         return new Promise((resolve, reject) => {
            setTimeout(() => {
               resolve('done');
            }, interval);
         });
      }

      async function timeTest() {
          // 第一种执行方式await造成了阻塞，后两个promise需要等待上一个promise返回结果才会执行       
         // await timoutPromise(3000);
         // await timoutPromise(3000);
         // await timoutPromise(3000);
          // 9003
          
          
          
          // 第二种执行方式三个promise几乎同时执行，只是利用await来等待结果
         const timeoutPromise1 = timoutPromise(3000);
         const timeoutPromise2 = timoutPromise(3000);
         const timeoutPromise3 = timoutPromise(3000);

         await timeoutPromise1;
         await timeoutPromise2;
         await timeoutPromise3;
      }
	     // 3002

      let startTime = Date.now();
      timeTest().then(() => {
         let finishTime = Date.now();
         let timeTaken = finishTime - startTime;
         console.log('Time taken in milleseconds: ' + timeTaken);
      });
```



​	