## JavaScript模块化

​	es6之前js的模块化主要是两种社区规范CommonJS和AMD，前者用于服务器后者用于浏览器。

- es6模块化：发展趋势，可以替代现有模块化标准
- CommonJS：在nodejs中使用
- AMD：用于浏览器端，可以被es6取代



### es6模块化（Module）

#### 静态加载

​	CommonJS模块是对象，引入时实际上是加载整个模块的输出对象，然后查找对应对象属性。

```javascript
// CommonJS模块
let { stat, exists, readfile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

​	但es6模块化并不是整体加载模块对象，而是export指定输出代码，然后通过import输入。

```javascript
// ES6模块
import { stat, exists, readFile } from 'fs';
```

​	es6模块是在语言层面实现，因此可以在编译时就加载模块。

#### export语句

​	直接使用export语句有两种写法

- 变量，函数声明前

  ```javascript
  // profile.js
  export var firstName = 'Michael';
  export var lastName = 'Jackson';
  export var year = 1958;
  ```

- 大括号前，大括号内是要输出的变量/函数名

  ```javascript
  // profile.js
  var firstName = 'Michael';
  var lastName = 'Jackson';
  var year = 1958;
  
  export { firstName, lastName, year };
  ```

#### import语句

​	引用有export的js模块使用import语句

```javascript
// main.js
import { firstName, lastName, year } from './profile.js';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

- import语句后需要跟大括号，大括号内部是和export对应的变量名，如果名称不符则不能正常引入

- from后是引入模块的相对地址

- import语句有提升

- 多次重复相同import语句仅执行一次

  ```javascript
  import { foo } from 'my_module';
  import { bar } from 'my_module';
  
  // 等同于
  import { foo, bar } from 'my_module';
  ```

#### 模块整体加载import *

​	使用*指定一个对象，将所有模块输出值都加载到这个对象上

```javascript
// circle.js

export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}

// main.js

import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```

#### export default语句

​	指定默认输出，在输入时，import语句可以不加大括号，并且直接指定自定义名称。

```javascript
// export-default.js
export default function () {
  console.log('foo');
}

// import-default.js
import customName from './export-default';
customName(); // 'foo'
```

​	实际上export default语句是输出一个叫做default的变量，所以后面不能跟变量声明语句。

```javascript
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;
```

#### import()动态加载

​	语法`import('path').then(({ var }) => { var(); });`

```javascript
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });
```

动态加载应用场景

- 按需加载

- 条件加载

- 动态模块路径生成

  ```javascript
  import(f())
  .then(...);
  // 根据函数f的返回结果，加载不同的模块
  ```



### es6模块和CommonJS差异

- CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口
- CommonJS 模块的`require()`是同步加载模块，ES6 模块的`import`命令是异步加载，有一个独立的模块依赖的解析阶段

