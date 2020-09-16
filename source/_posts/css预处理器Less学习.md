---
title: css预处理器less
---

### 变量

- 使用@+变量名的形式进行声明和调用

```less
@bgColor: rgb(131, 92, 92);
.div1 {
   margin: 50px auto;
   width: 300px;
   height: 300px;
   text-align: center;
   background-color: @bgColor;
}
```

- 如果变量名是属性名/选择器/url，调用时要用大括号包住（不常用）

```less
@bgColor: rgb(131, 92, 92);
@bg: background-color;
.div1 {
   margin: 50px auto;
   width: 300px;
   height: 300px;
   text-align: center;
   @{bg}: @bgColor;
}
```

- 变量延迟加载

  less中有块级作用域，但会在整个块解析完之后才为声明的变量赋值

```less
@var: 0;
.class1 {
	@var:1;
	.class2 {
		@var: 2;
		property1: @var; // @var == 3
		@var: 3;
	}
	property2: @var;
}
```



### 嵌套规则

- &符号

  表示嵌套中前面的所有选择器，通常用于写状态选择器



### 混合

​	混合是将一系列属性从一个规则引入到另一个规则集的方式（可以重复使用的代码块）。

#### 基本用法

​	使用"."+混合名定义混合（和类选择器相同），并在其他规则内使用".混合名"直接调用。

```less
.positionCenter {
   position: absolute;
   top: 0;
   right: 0;
   left: 0;
   bottom: 0;
   margin: auto;
   height: 100px;
   width: 100px;
   background-color: #ddd;
}
.wrapper1 {
   position: relative;
   margin: 0 auto;
   height: 300px;
   width: 300px;
   border: 1px solid #000;
   .div2 {
      .positionCenter;
   }
   .div3 {
      .positionCenter;
   }
}
```

#### 混合的参数和默认值

​	可以在混合当中添加参数，`.mixin(@variable1, @variable2){}`来定义混合（混合就是less的函数）。

```less
.positionCenter(@w:100px, @h:100px, @color:#ddd) {
   position: absolute;
   top: 0;
   right: 0;
   left: 0;
   bottom: 0;
   margin: auto;
   height: @h;
   width: @w;
   background-color: @color;
}
.wrapper1 {
   position: relative;
   margin: 0 auto;
   height: 300px;
   width: 300px;
   border: 1px solid #000;
   .div2 {
      .positionCenter(200px, 200px, @bgColor);
   }
   .div3 {
      .positionCenter(@color:#ccc); // 可以单独指定某个参数，前提是和形参名对应
   }
}
```



### 继承

​	less中的继承使用语法是`&:extend()`伪类，可以传入一个选择器并继承匹配声明中的全部样式。

```less
.container-fixed(@gutter: @grid-gutter-width) {
  margin-right: auto;
  margin-left: auto;
  padding-left:  floor((@gutter / 2));
  padding-right: ceil((@gutter / 2));
  &:extend(.clearfix all);
}
```

