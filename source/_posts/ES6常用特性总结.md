---
title: ES6常用特性总结
---
### let和const命令

#### 基本用法

​	let声明变量，const声明常量。



#### 特性

- 块级作用域。声明变量/常量仅在声明语句所在代码块内生效
- 没有变量提升。
- 暂时性死区。只要一代码块中有let或const声明的变量，虽然没有变量提升的特性，但从一开始let和const声明的变量就被所处代码块锁死，在声明前使用这些变量会报错。

```javascript
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}

// 暂时性死区，含有let代码块锁死声明的同名变量
```



#### let命令和for语句

- 循环体内部和设置循环变量部分为两个独立作用域

```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc

// 循环体为子作用域
```

- let声明变量仅在for循环体内有效

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10

var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```







### 函数扩展

#### 箭头函数

##### 基本使用

```javascript
var f = function(a) {
	return a;
}
// 等同于
var f = (a) => { return a };

// 如果函数体部分为return语句可以省略大括号,只有一个参数时也可以省略圆括号
var f = a => a;
```

##### 特性

- 绑定this对象。箭头函数中this对象强制绑定为函数定义时所在对象而非使用时所在对象（实际上箭头函数没有this对象，会沿作用域链寻找上级this对象）

```javascript
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 });
// id: 42

// 箭头函数绑定this对象的特性可以用于绑定setTimeout中this为定义时所在作用域，同样也方便对回调函数中this进行绑定
```

- 不能用作构造函数。使用new命令将报错
- 箭头函数没有arguments对象。
- 不能使用yield命令。箭头函数无法作为Generrator函数



#### 函数参数默认值

##### 基本用法

​	直接为函数参数定义一个值即可，如果在写入参数时省略的设定默认值的参数，则会使用默认值。

```javascript
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

##### 参数默认值位置

​	设定了默认值的参数应该位于参数列表的尾部，否则无法省略此参数以及之后参数

```javascript
function f(x = 1, y) {
  return [x, y];
}

f() // [1, undefined]
f(2) // [2, undefined]
f(, 1) // 报错
f(undefined, 1) // [1, 1]
```



#### rest参数

##### 基本用法

​	`...values`，用于获取函数多余参数。此时values为一个数组，接收函数多余的参数。

```javascript
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```

##### 特性

- rest参数是真正数组，可以使用数组方法。这一点主要是规避以往需要将arguments对象先转换为数组的操作`Array.prototype.slice.call`







### 数组扩展

#### 扩展运算符

##### 基本用法

​	扩展运算符为`...`，可看做rest参数逆运算，将一个数组转为逗号分隔开的参数序列。

```javascript
// ES5中要使用如下方法对数组应用Math.max()方法，因为Math.max()方法接收的是一个参数序列而非数组
var arr = [6, 89, 3, 45];
var maximus = Math.max.apply(null, arr); // returns 89

// ES6中使用扩展运算符则可以方便将数组转换为参数序列
const arr = [6, 89, 3, 45];
const maximus = Math.max(...arr); // returns 89
```

##### 特性

- 主要是替换ES5中利用apply方法将数组转为函数参数的操作
- 扩展运算符使用相当灵活，通常情况放置在中括号内将其他数据类型转换为数组，以及在函数参数列表中和普通参数连用
- 任何具有Iterator接口的对象都可以使用扩展运算符转换为一参数序列，然后将参数序列置于中括号内，转换为真正的数组







### 解构赋值

​	解构赋值是ES6中引入，用于从数组和对象中提取值，对变量进行赋值的特殊语法。解构赋值实际上是按照某种模式进行赋值。

#### 基本用法

​	解构赋值主要是从对象和数组中按照模式提取值，同时也可以应用到字符串，数值和布尔值上（字符串转换为类似数组对象，数值和布尔值转换为对象）

```javascript
// 对数组进行解构赋值
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

// 不完全解构，模式中变量没有对应值时变量的值为undefined
let [foo] = [];
let [bar, foo] = [1];
// foo undefined
```

```javascript
// 对对象进行解构赋值
let { foo, bar } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"

// 没有对应属性名的单独变量无法进行解构赋值
let {foo} = {bar: 'baz'};
foo // undefined

// 对象的解构赋值实际上是将相同属性名的变量进行赋值，只不过单独变量的情况是利用了对象简写属性的特点
let { foo: foo, bar: bar } = { foo: 'aaa', bar: 'bbb' };
```



#### 默认值

​	对象和数组的解构赋值中都可以在等号左边的模式中，为变量指定默认值。默认值生效的条件是等号右边的数组或对象中，对应值为undefined。

```javascript
// 为数组解构赋值指定默认值
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'

// 为对象解构赋值指定默认值
var {x: y = 3} = {x: 5};
y // 5

var { message: msg = 'Something went wrong' } = {};
msg // "Something went wrong"
```

 

#### 函数参数解构赋值

​	对于函数参数的解构赋值，函数的形参是模式，而实参则是目标对象或数组。

##### 基本用法

```javascript
function add([x, y]){
  return x + y;
}
add([1, 2]); // 3

function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]

[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```







### 字符串扩展

#### 模板字符串

​	模板字符串用于输出模板，在两个反引号``内部填写的内容可以按照格式输出，可用于定义多行字符串或在字符串中嵌入变量。



##### 基本用法

```javascript
// 普通字符串
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

console.log(`string text line 1
string text line 2`);

// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```







### 对象扩展

#### 属性的简洁表示法

​	ES6允许在对象大括号中直接写入变量和函数，作为对象的属性和方法。



##### 基本用法

```javascript
// 变量直接写在大括号中，属性名就是变量名，属性值就是变量值
const foo = 'bar';
const baz = {foo};
baz // {foo: "bar"}

// 等同于
const baz = {foo: foo};

// 方法同样可以简写，函数名就是方法名
const o = {
  method() {
    return "Hello!";
  }
};

// 等同于
const o = {
  method: function() {
    return "Hello!";
  }
};
```







### Class

​	class关键字在ES6中可以用于定义类，但class关键字本身只是语法糖。

#### 基础用法

```javascript
// 利用class关键字对类进行定义
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

// 等同于ES5中如下写法
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
```



#### 特性

- class语法仅仅是将构造函数的创建进行了替换，并没有完整实现类这一特性
- class声明的类，在调用时同样使用new关键字，但此时会执行constructor函数
- class语法声明类后不跟括号，对于创建实例传入的参数由constructor进行接收

- class中的constructor函数对应于ES5中的构造函数
- class中的方法对应于ES5中构造函数原型方法



#### getter和setter

​	getter函数的seter函数用于控制对象的数据存取行为。

​	getter函数将会返回对象私有变量的值而非直接返回对象私有变量。

​	setter函数将会根据setter函数的参数，对对象的私有变量进行值的设置。可能包含计算操作或覆盖操作。

```javascript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```

​	getter和setter函数都是拦截某属性的存取操作，该属性名对应于getter和setter的方法名。







### Module

​	Module是用于在各JavaScript文件之中共享代码，这包含了在文件中输出部分代码和在文件中引入需要代码两种操作。

​	模块的功能主要是两个命令：export和import

#### 创建Module Script

```html
<script type="module" src="filename.js"></script>
```

​	type为module的脚本中才可以使用import和export功能



#### export命令

​	export命令用于规定模块的对外接口，共享代码块。使用时有两种形式：

```javascript
// 第一种直接在变量声明前使用export命令，输出紧跟着的变量
export const add = (x, y) => {
  return x + y;
}
```

```javascript
// 第二种则是用一个对象输出多个变量
const add = (x, y) => {
  return x + y;
}

export { add };
```

​	export可以输出变量，函数和类。



#### import命令

​	使用了export命令规定了模块的对外接口之后，其他JS模块就可以通过import命令加载这个模块。

```javascript
import { add } from './math_functions.js';
```

​	import语句允许选择要加载的接口，以及加载模块的路径。



#### 模块整体加载（import as语句）

​	在import命令中使用星号`*`指定一个对象，将所有的输出值都加载在对象上。

```javascript
import * as myMathModule from "./math_functions.js";
```



#### export default语句

​	通常export default语句用于只需要从模块中输出一个值的情况，以及为模块创建备用输出的情况。

```javascript
// named function
export default function add(x, y) {
  return x + y;
}

// anonymous function
export default function(x, y) {
  return x + y;
}
```

​	export default只能够输出一个值，可以是变量或函数，还可以是常量（因为实际上是用export关键字输出了一个名为default的变量）



#### 引入default export

​	对于普通的import语句和针对export default的import语句，仅有大括号的区别：

```javascript
import add from "./math_functions.js";
// 此处add并非源模块中的输出，而是由引入者自己命名的，并且不被大括号包围
```







### Promise

#### Promise含义

​	是异步编程的一种解决方案，相比于传统的回调函数和事件两种解决方案而言，Promise更加灵活也更加合理。Promise可以在异步操作完成后指定回调函数，且书写方式更加易于维护，解决了回调函数的回调地狱问题。

​	Promise有以下特点

- Promise是一个封装了异步操作的容器，在定义Promise实例时其内部的异步操作就已经运行
- Promise有三种状态，分别是pending，fulfilled（resolved）和rejected。代表了异步操作进行中，成功完成和失败完成。
- Promise对象的状态不受外界影响，仅取决于内部异步操作的完成情况。
- Promise对象的状态仅改变一次，不是从pending到fulfilled就是从pending到rejected



#### Promise基本使用

	##### Promise实例创建

​	Promise是一个构造函数，用于生成Promise实例。

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

​	在创建Promise实例时，接收一个函数作为参数，该函数体为异步操作，该函数参数为resolve参数和reject参数。resolve和reject函数用于决定Promise的结果，完成promise，如果不调用这两个函数Promise实例将会永远停留在pending状态。

​	在函数体内调用resolve返回value值则Promise实例状态由pending改为fulfilled，调用reject返回error值则Promise实例由pending状态改为rejected状态。

##### then方法

​	then方法是Promise实例可调用方法，接收两个参数分别为onresolved和onrejected函数，分别用于接收Promise实例中对应resolve和reject函数传递出的数据value和error。

```javascript
function timeout(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms, 'done');
  });
}

timeout(100).then((value) => {
  console.log(value);
});
```

​	then方法返回一个新Promise实例，因此可以采取链式写法（返回的新Promise实例会传入then方法的返回值）







