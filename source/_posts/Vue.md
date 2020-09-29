## Vue基础

### Vue实例

#### 创建Vue实例

​	利用Vue函数创建一个Vue实例

```javascript
const vm = new Vue({
	// 选项
});
```

​	其中参数为一个选项对象，可以指定各种实例行为。而Vue应用都由根实例和组件构成

#### 数据和方法

​	实例创建时，其选项对象中的data对象将会绑定数据到响应式系统中。

```javascript
var vm = new Vue({
  data: data
})
```

​	只有在创建实例时就存在于data对象中的属性才是响应式的。

```javascript
const a = {msg: "Hello World!"};

const vm = new Vue({
    el: ".hw",
    data: a
});

a.msg = "Welcome to Vue.";
```



### 模板语法

​	Vue将模板翻译为虚拟DOM渲染函数。

#### 插值

- 文本

  - 双大括号进行文本插值
  - v-once一次性插值，数据改变时插值不变

  ```javascript
  const div1 = new Vue({
      el: "#div1",
      data: {
          msg: "MESSAGE0"
      }
  });
  
  div1.msg = "MESSAGE1"; // Message: MESSAGE1 Once Message: MESSAGE0
  ```

- 原始HTML

  - v-html将会把data中的文本当作原生HTML解析
  - 原生HTML代码将会作为内容填充至绑定v-html指令的元素内部
  - v-html容易遭受XSS攻击，禁止为用户提供内容使用

  ```javascript
  <div id="div2">
      <p>rawHtml: {{ rawHtml }}</p>
  	<p>v-html: <span v-html="rawHtml"></span></p>
  </div>
  
  const div2 = new Vue({
      el: "#div2",
      data: {
          rawHtml: "<p style='color: red'>Red quote</p>"
      }
  });
  ```

  

- 属性

  - v-bind属性可以进行进行属性修改

#### 指令

​	指令是带有v-前缀的特殊属性，值为单个JS表达式（v-for除外）。

- 参数
  - 一些指令可以接收参数，在指令名称后以冒号表示`v-bind:href="url"`
- 动态参数
  - 用方括号括起来的JS表达式作为指令参数`v-bind:[attributeName]`
  - 动态参数值为一个字符串，异常情况为null
- 修饰符
  - 以点知名的特殊后缀，指出一个指令以特殊方式绑定`v-on:submit.prevent="onSubmit"`

#### 缩写

​	v-bind和v-on两个最常用的指令有缩写。

- v-bind
  - v-bind:href  		:href
- v-on
  - v-on:click		@click



### 计算属性和侦听器

#### 计算属性

​	模板用于简单运算，复杂的逻辑运算需要交给计算属性。计算属性每次源数据改变之后便执行函数，和侦听器很类似但可以监控多个数据变化。

```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

​	在计算属性中声明的函数将作为getter函数，reversedMessage作为vm.reversedMessage的getter函数

- 计算属性缓存vs方法

  - 相同的数据也可以通过在表达式中调用方法达到同样效果

  ```javascript
  methods: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
  ```

  ​	但计算属性是依靠依赖进行缓存的，只要依赖数据不改变，就不会重新求值，而methods中方法每一次调用时都会执行。

- 计算属性vs侦听属性

  - 侦听属性用于观察和响应Vue实例上的数据变动watch
  - 侦听的数据发生变化时会执行回调函数
  - 计算属性减少了侦听属性的重复操作，更加直观

- 计算属性的setter

  - 计算属性默认只有getter，但可以自定义setter

  ```javascript
  computed: {
    fullName: {
      // getter
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
  ```

#### 侦听器

​	侦听器可以侦听属性并执行异步操作，一些情况下使用侦听器比计算属性要合适。



### class和style绑定

​	同样使用v-bind指令，这两个属性通常有字符串拼接操作所以参数的结果可以是字符串，对象和数组

#### 绑定HTML Class

- 对象语法

  - 对象中属性名是要绑定类名，属性值的真假决定类是否存在
  - v-bind:class语句可以和普通类名共存，结果将合并类名
  - 通常将class的所有属性都放在data中的classObject对象中或者返回对象的计算属性中

  ```javascript
  computed: {
    classObject: function () {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  }
  ```

- 数组语法

  - 将数组传给v-bind:class，将其中各项对应的数据作为类名
  - 和对象语法不同，对象语法中属性名就是类名，而数组语法中类名是各项的变量值
  - 在数组语法中可以使用对象，根据条件添加类名

#### 绑定内联样式

- 对象语法
  - `v-bind:style="{ color: activeColor}"`参数传入一个对象，属性名为驼峰命名的CSS属性
  - 通常会将样式对象在data中绑定一个单独的styleObject对象
- 数组语法
  - `v-bind:style="[baseStyles, overridingStyles]"`数组中的项可以是多个样式对象，将多个样式对象应用到同一个元素上
- 自动添加前缀
  - v-bind:style会自动添加浏览器引擎前缀



### 条件渲染

​	v-if指令条件性渲染一块内容，当且仅当表达式返回真时，这块内容会被渲染。

#### v-if

​	v-if指令可用于条件性渲染一块内容，会在表达式返回值为真时渲染。也可以使用v-else表达一个二选一渲染内容块。

- 条件渲染分组<template>

  - 条件渲染语句中的分组元素，本身当作不可见的包裹元素

- v-else

  - 使用v-else要紧跟v-if或v-else-if后否则不被识别

- v-else-if

  - 必须跟在v-if或v-else-if后

- 使用key管理可复用元素

  - 在一组v-if内容块中，如果可选内容块元素相同，那么在替换时会尽可能高效地复用已有元素，修改元素的属性而不是直接重新渲染
  - 要表达两个元素完全独立时，需要为不复用的元素添加一个key属性来标识，但v-if内容块中，没有添加key属性的相同元素仍然会复用

  ```javascript
  <template v-if="loginType === 'username'">
    <label>Username</label>
    <input placeholder="Enter your username" key="username-input">
  </template>
  <template v-else>
    <label>Email</label>
    <input placeholder="Enter your email address" key="email-input">
  </template>
  ```

#### v-show

​	用法和v-if指令类似，但是仅简单修改了CSS样式的dipslay属性而不是条件性渲染。

- v-show指令不支持template元素和v-else

#### v-if和v-show

- v-if是真正的渲染，能够保证切换后条件块内事件监听器和子组件销毁
- v-if是惰性的，只有渲染条件变为真才会渲染条件块
- v-show总是会渲染元素，只是通过display属性决定是否显示
- v-if切换开销大，v-show初始开销大，如果经常切换使用v-show性能更好

#### v-if和v-for一起使用

- 这两条语句不一起使用，v-for比起v-if有更高优先级



### 列表渲染

#### 用v-for将一个数组对应为一组元素

​	v-for用于实现将一组数组数据渲染成为一组元素，使用`item in items`特殊语法，items是源数据数组，item则是被迭代的数组元素别名。

​	v-for绑定的元素可以访问父作用域中所有属性

​	v-for的参数中，item可以添加第二个参数`(item, index) in items`标识当前项在数组中的索引

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

#### 在v-for中使用对象

​	v-for同样可以遍历一个对象属性。可以提供第二个参数name作为当前遍历的属性名，第三个参数index作为属性在对象中的出现顺序。

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

​	遍历对象时按照Object.keys()结果进行遍历

#### 维护状态

​	在源数组/对象数据更新时，v-for渲染的元素默认不会更改顺序，而是就地更新每个元素。

​	但为了更好的元素复用和重排序，需要利用`v-bind:key`给每项添加一个key值进行标识。

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- 内容 -->
</div>
```

​	v-for中key值只能是数值或者字符串类型。

#### 数组更新检测

- 变更方法
  - 对于可以改变源数组的方法，都会触发视图更新
- 替换数组
  - 对于返回新数组的方法，可以直接替换源数组

#### 显示过滤/排序后结果

​	要显示过滤后的数组可以创建计算属性来返回过滤或排序后数组，将不同排序要执行的代码都写在computed中，通过标识符决定运行哪种过滤模式。

```javascript
computed: {
    filterdItems: function() {

        const {typein, items, orderType} = this;

        let fItems = items.filter(function(item) {
            return item.name.toLowerCase().indexOf(typein.toLowerCase()) !== -1;
        });

        if (orderType === 1) {
            fItems.sort(function(a, b) {
                return a.age - b.age;
            });
        } else if (orderType === 2) {
            fItems.sort(function(a, b) {
                return b.age - a.age;
            });
        }

        return fItems;
    }
}
```

#### 在v-for中使用范围

​	v-for可以接收整数，将会把模板重复对应次数。

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

#### 在`<template>`上使用v-for

​	可以在带有v-for的template上循环渲染多个元素。



### 事件处理

#### 监听事件

​	使用v-on指令监听DOM事件，触发时运行一段JS代码。

```html
<div id="example-1">
  <button v-on:click="counter += 1;">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```

#### 事件处理方法

​	复杂的代码可以使用事件处理方法进行封装，v-on指令可以接收一个参数作为调用的方法名。

```html
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
```

​	方法中的this将会指向当前Vue实例而不是调用方法的元素。

```javascript
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})

// 也可以用 JavaScript 直接调用方法
example2.greet() // => 'Hello Vue.js!'
```

​	方法可以传入一个event参数，指向当前事件对象。

#### 内联处理器中方法

​	可以在内联JS语句当中调用方法

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```

​	在内联处理器中调用方法时，要在方法内部使用事件对象需要在调用时传入$event

```html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

#### 事件修饰符

- .stop
  - 阻止事件传播
- .prevent
  - 阻止事件默认行为
- .capture
  - 使用事件捕获模式
- .self
  - 仅在当前元素自身触发方法
- .once
  - 事件仅触发一次
- .passive
  - passive会使事件默认行为立即触发，而不会执行回调函数后再判断是否触发默认行为，提升了性能

#### 按键修饰符

​	监听键盘事件时可以指定按键修饰符检查详细按键。

- 按键码
  - .enter
  - .tab
  - .esc
  - .space



### 表单输入绑定

#### 基础用法

​	`v-model`指令可以在表单元素<input><textarea><select>创建双向数据绑定。会根据控件类型自动选取正确方法更新元素。

​	v-model指令本质上是语法糖，监听用户输入事件以更新数据。

​	v-model指令将忽略value，checked，selected等属性值，总将Vue实例数据作为数据来源。

​	初始数据值对应的value值，作为表单元素的默认值。

- 文本

  ```html
  <input v-model="message" placeholder="edit me">
  <p>Message is: {{ message }}</p>
  ```

- 多行文本

  - 在文本区插值{{}}不会生效，要使用v-model代替

- 复选框

  - 复选框将会绑定到同一个数组，v-model只要指定同一个数组，位于所有复选框input元素就可以绑定
  - 只有一个复选框则会绑定到布尔值

- 单选框

  - 绑定到一个变量，在多个单选框input指定v-model指向同一个数据即可

- 选择框

  - v-model不支持对option绑定，而是给select元素绑定一个变量或数组

#### 值绑定

​	使用v-bind:value可以实现动态绑定value值，做到value的值由Vue实例对象中数据决定。

#### 修饰符

- .lazy
  - 默认v-model在input事件触发后进行数据同步，但lazy修饰符将会转为change事件
- .number
  - 自动将输入值转为数值类型
- .trim
  - 自动过滤输入首尾空白字符

### 组件基础

​	组件是可以复用的Vue实例。

​	使用Vue.component可以全局注册Vue组件。在new Vue创建的实例中可以使用已注册的组件。

```javascript
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
new Vue({
    el: "#components-demo"
})
```

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

​	使用自定义元素时，组件的模板会替换自定义元素内容。

#### 组件的复用

​	组件的复用通过在html父组件模板中使用自定义元素的形式进行，自定义元素的元素名是注册的组件名。

​	每个自定义元素所复用的组件都会各自独立维护自己的数据。

- data必须是一个函数
  - 组件中的data为一个函数并且返回一个数据对象，实现组件复用时各自维护自己数据
  - data是组件自身数据，要从父组件传入数据需要使用prop属性

#### 组件的组织

​	组件需要注册后才能在模板中使用，有两种组件注册方式：全局注册和局部注册。

#### 通过Prop向子组件传递数据

​	父组件向子组件传递数据是通过子组件的props属性进行，props属性是一个数组，数组项是接收的变量名字符串。

​	数据的传递通过父组件内的自定义属性完成，直接使用自定义属性可以传递常量，而通过v-bind定义的属性可以传递变量（传递父实例中的数据）。

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

```javascript
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
new Vue({
  el: '#blog-post-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue' },
      { id: 2, title: 'Blogging with Vue' },
      { id: 3, title: 'Why Vue is so fun' }
    ]
  }
})
```

​	子组件使用props中的数组项和使用data中数据一样，并且父实例传递的变量可以是一个对象，组件复用时只需要使用一个自定义属性就可以传递需要的所有数据。

#### 单个根元素

​	每个组件只能够包含一个根元素，如果有多个元素需要在模板中写在同一个根元素之下。

#### 监听子组件事件

​	一些情况下需要在子组件事件触发时，调用父实例的方法和数据。

​	可以在组件模板指定事件，并将监听器设置为`emit("functionName", data)`发送事件，然后在组件复用时，监听functionName事件，触发父实例的对应处理函数。

```html
// 子组件模板
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
// 复用组件指定特定事件
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```

- 使用事件抛出一个值

  - $emit第二个参数是抛出的参数，在组件复用时，监听的特定事件可以通过$event访问到这个参数
  - 如果组件复用时，指定的是一个回调函数，则抛出的值会默认作为回调函数第一个参数

- 在组件上使用v-model

  - v-model="searchText"

  - 在根实例上绑定v-model可以分解成两部分：

  - v-bind:value="searchText"

  - v-on:input="searchText = $event.target.value"

  - 在子组件中这两部分成为：

  - v-bind:value="searchText"

  - v-on:input="searchText = $event"

    ```javascript
    Vue.component('custom-input', {
      props: ['value'],
      template: `
        <input
          v-bind:value="value"
          v-on:input="$emit('input', $event.target.value)"
        >
      `
    })
    ```

    

#### 通过插槽分发内容

​	组件模板中的<slot>元素，在实际渲染时会被组件复用时，传入自定义元素的内容替换。

```html
<alert-box>
  Something bad happened.
</alert-box>
```

```javascript
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

