---
title: DOM总结
---

- DOM1级
- DOM扩展

### DOM1级

#### node类型

​	所有DOM节点都是node类型，可以使用以下属性和方法

属性：

- nodeType：节点类型，返回数值

- nodeName：标签名
- nodeValue：根据不同节点类型，属性值不同
- childNodes：子元素，返回一个nodeList对象（类数组）
- parentNode：父元素
- previousSibling：前兄弟元素
- nextSibling：后兄弟元素
- firstChild
- lastChild

方法：

- parentNode.appendChild( newNode )
- parentNode.insetBefore( newNode, childNode )
- parentNode.replaceChild( newNode, childNode )
- parentNode.removeChild( childNode )

#### document类型

nodeValue为null

方法：

- document.getElementById()
- document.getElementsByTagName()
- document.createElement( tagName ) 
- document.createTextNode( text )

#### element类型

nodeValue为null

属性：

- tagName/nodeName：注意属性值是大写
- id
- className：返回类名字符串，类和类用空格隔开

方法：

- elem.getAttribute( attrName )
- elem.setAttribute( attrName, attrValue )
- elem.removeAttribute( attrName )

#### text类型

nodeValue为包含文本

一般用firstChild取得text类型节点，然后用nodeValue获得文本



### DOM扩展

​	最主要的扩展是selectorsAPI和HTML5

### selectorsAPI

- document.querySelector( selector )
- document.querySelectorAll( selector )：返回nodeList列表

##### DOM元素属性扩展

- childElementCount
- firstElementChild
- lastElementChild
- previousElementSibling
- nextElementSibling

#### HTML5

##### 类相关扩展

- document.getElementsByClassName( className )

- classList属性

  方法

  - add（）
  - contains（）返回布尔值
  - remove（）
  - toggle（value）：已有value则删除，没有value则添加

##### 专有扩展

- children属性：等同于childNodes
- parentNode.contains( childNode )：检测传入节点是不是后代

##### 插入文本

- innerText属性：获取元素包含的所有文本（包括子元素文本，会分层显示）
  - 如果直接设置innerText属性值，会把节点子节点删除