# Flex和Grid布局总结

## flex布局

阮一峰网络日志：http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html

1. flex布局是将元素变为flex容器，为盒模型提供了最大的灵活性。
2. 可以指定任意元素（包括行内元素）为flex容器。
3. 指定为flex容器后，其内部元素的position，clear和vertical-align属性将会失效。
4. flex容器内部有主轴和交叉轴，默认主轴方向是横向左至右，交叉轴方向纵向上至下。



#### 容器属性

- flex-direction
- flex-flow
- flex-wrap
- justify-content
- align-items
- align-content



##### flex-direction

决定主轴方向，默认值为row，其他值有row-reverse，column，column-reverse



##### flex-wrap

决定元素换行行为，值为

no-wrap（默认值，不换行，根据比例决定元素宽度），

wrap（换行，第一行在上方），

wrap-reverse（换行，第一行在下方）



##### flex-flow

是direction和wrap的简写属性，默认值为row no-wrap



##### justify-content

项目在主轴上的对齐方式，值为

flex-start（默认值，和主轴前端对齐），

flex-end（和主轴后端对齐），

flex-center（主轴方向居中对齐），

space-between（两端对齐，剩余空间位于项目中间），

space-around（项目两侧空白相等，剩余空间位于项目周围）



##### align-items

项目在交叉轴上对齐方式（每一行元素都有一条交叉轴），值为

strech（默认值，如果高度为auto或者不指定高度，将自动填充容器高度），

flex-start，

flex-end，

center，

baseline（对齐元素内第一行文字）



##### align-content

对齐多条交叉轴，如果只有一条交叉轴则此属性无效，值为

strech（默认值，如果不指定高度或者高度为auto则会填充容器高度），

flex-start

flex-end

center

space-around

space-between



#### 项目属性

项目属性主要是定义空间不足时元素的缩放行为

- order
- flex-grow
- flex-shrink
- flex-basis
- flex
- align-self

##### order

定义元素在主轴方向排列顺序，属性值为数字，数字越小越靠前

##### flex-grow

元素放大比例，默认值为0，即使有剩余空间也不会放大

##### flex-shrink

元素缩小比例，默认值为1，空间不足时将会缩小元素

##### flex-basis

分配多余空间之前，项目所占的主轴空间，默认值为auto，即元素本来大小

##### flex

是flex-grow，flex-shrink和flex-basis的简写属性，默认值为0 1 auto，后面两个值可选

两个快捷属性值：auto（1 1 auto），none（0 0 auto）

##### align-self

单独指定交叉轴对齐方式，可以覆盖父元素的align-items属性值，默认值为auto继承父元素align-items属性值



## Grid布局

阮一峰网络日志：http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html

1. Grid布局将容器划分为行和列并产生单元格，然后指定项目所在的单元格完成布局，属于二维布局。而flex布局相比是针对轴线位置进行布局，是一维布局
2. 容器中水平区域为行，垂直区域为列，行和列交叉区域为单元格
3. 行和列由网格线划分，n行有n+1条水平网格线，m列有m+1条垂直网格线

#### 容器属性

- display
- grid-template-columns
- grid-template-rows
- grid-row-gap
- grid-column-gap
- grid-gap
- grid-template-areas
- grid-auto-flow
- justify-items
- align-items
- place-items
- justify-content
- align-content
- place-content
- grid-auto-columns
- grid-auto-rows
- grid-template
- grid



##### display

设为网格布局后，容器子元素的float，display：inline-block，display：tabel-cell，vertical-align等属性失效

display：grid指定容器为网格布局

display：inline-grid指定容器为采用网格布局行内元素



##### grid-template-columns，grid-template-rows

用于划分容器的行和列，使用这两个属性可以指定行高和列宽

- repeat（）函数：用于指定重复列宽/行高时简化重复操作，第一个参数为重复次数，第二个参数是重复的数值

- auto-fill关键字，用在repeat（）函数中，用于代替第一个参数，表示每一行/列尽可能容纳多的单元格（多用于单元格大小确定而容器大小不确定）

- 在指定行高/列宽时，一些对细节行为进行描述的关键字或单位：

  单位 px，百分比，fr（表示比例关系，可以和固定值混用）

  auto 表示浏览器自己决定长度，一般是单元格最大宽度/高度

  minmax（）用于指定单元格尺寸长度范围，第一个参数是最小长度，第二个参数是最大长度

  利用中括号[]，在指定单元格尺寸时为每一根网格线命名



##### row-gap，column-gap，gap

指定单元格间距

gap为行间距和列间距简写



##### grid-template-areas

用于定义区域，一个区域由单个或多个单元格构成，值的形式为

‘a a c’

'd e f'

'g h i'

用标识符为每个单元格定义，标识符相等的单元格组成一个区域



##### grid-auto-flow

决定元素流入单元格的顺序，默认值为row，即先填满行后填满列

dense关键字：紧密流入，优先保证单元格不发生空格



##### justify-items，align-items，place-items

决定每个单元格内部的内容，即内部元素在单元格中的位置

justify-items排列水平位置，align-items排列垂直位置，place-items则是前面两个属性的简写（align-items在前justify-items在后）

justify-items和align-items两个属性的值相同，如下

strech： 默认值，拉伸占满单元格

start： 对齐单元格起始边缘

end： 对齐单元格结束边缘

center： 单元格内部居中



##### justify-content，align-content，place-content

用于决定整个内容区在容器内部的布局

justify-content和align-content属性值相同

strech（默认）

start

end

center

space-between

space-around

space-evenly

而place-content则是align-content和justify-content属性的缩写



#### 项目属性

- grid-column-start
- grid-column-end
- grid-row-start
- grid-row-end
- grid-column
- grid-row
- grid-area
- justify-self
- align-self
- place-self



##### grid-column-start，grid-column-end，grid-row-start，grid-row-end，grid-column，grid-row

指定项目位置，和grid-auto-flow不同，是指定单个元素的位置

grid-column-start：元素左边框所在垂直网格线

grid-column-end：元素右边框所在垂直网格线

grid-row-start：元素上边框所在水平网格线

grid-row-end：元素下边框所在水平网格线

grid-column：grid-column-start，grid-column-end属性简写，两条垂直网格线之间用“/”分隔

grid-row：grid-row-start，grid-row-end属性简写，两条垂直网格线之间用“/”分隔



##### grid-area

指定项目放置在哪个区域



##### justify-self，align-self，place-self

用于设置单元格内元素的位置

justify-self和align-self属性值一样

strech：（默认值）

start

end

center

place-self则是两个属性的简写（align-self在先，如果省略只写一个值则认为两个值相同）





