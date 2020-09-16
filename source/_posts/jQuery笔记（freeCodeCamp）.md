---
title: jQuery笔记（freeCodeCamp）
---

- $(doucment).ready(function() { })：保证function内部的代码在HTML渲染完成后运行

- 使用$(" ")选取元素：通常用选择器对HTML元素进行选取

  $("tagname")：直接选取对应标签的所有元素

  $(".className")：选取有指定类的所有元素

  $("#id")：选取有对应id的元素

- addClass()方法：对选取到的元素进行添加类名操作

- removeClass()方法：对选取到的元素进行删除类名操作

- css()方法：改变选取到元素的样式，两个字符串字符串分别代表属性名和属性值

- prop()方法：改变选取元素的属性，两个字符串，属性名和属性值

- html()和text()方法：替换元素内容，前者可以放入HTML标签，后者只能够改变元素内部的文本

- remove()方法：删除元素，无参数

- appendTo()方法：移动元素，参数是另一个选择器，效果是将选中的元素添加到后一个选择器对应元素的子元素

- clone()方法：获得一个元素的拷贝，没有参数

- parent()方法：获得一个元素的父元素，没有参数

- children()方法：获得一个元素的所有子元素

- :nth-child(n)修饰符：是选择器一部分，用于精准选取某个子元素

  注意：如果是想选取.target类元素中的第二个元素，直接使用`$(".target:nth-child(2)")`，这个命令的含义是对于有同一个父元素的.target元素中，父元素的第二个子元素被选取

- :odd/even修饰符：对于同一个类选取到的所有元素，从0开始排序按奇偶进行选取



### 总结

#### 增

- appendTo()
- clone()

#### 删

- remove()

#### 改

- addClass()
- removeClass()
- html()
- text()
- css()
- prop()

#### 查

1. 直接查询
   - $("tagName")
   - $(".className")
   - $("#id")
2. 查询元素的相关元素
   - parent()
   - children()
   - :nth-child(n)修饰符
   - :odd/even修饰符