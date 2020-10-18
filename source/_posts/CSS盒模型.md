## CSS盒模型

​	所有元素都被盒子包围

### 块级盒子和内联盒子

#### 块级盒子

- 内联方向占据父容器该方向所有可用空间
- 每个盒子都会换行
- width和height属性可用
- 内边距，外边距和边框会将其他元素从当前盒子周围推开

#### 内联盒子

- 盒子不会换行
- width和height属性不可用
- 垂直方向内外边距，边框被应用，但不推开其他盒子
- 水平方向内外边距，边框同样被应用，且会推开其他盒子

### 内部和外部显示类型

​	CSS的盒模型有一个外部显示类型，决定盒子是块级还是内联

​	同样也有内部显示模式，决定了盒子内部如何布局，默认按照正常流

​	一些display属性值可以改变内部显示类型，如flex，此时元素外部显示为block，内部显示为flex（所有内部直接子元素成为flex元素



### 盒模型

​	CSS中盒子的组成部分

- Content box：内容区，大小由width和height设置
- Padding box：内容区外空白区域，大小由padding设置
- Border box：包裹内容和内边距盒子，通过border设置大小
- Margin box：是盒子和其他元素间空白区域

#### 标准盒模型

​	标准盒模型中，width和height指定的是content box大小，padding和border加上width与height决定整个盒子大小

![standard box](https://mdn.mozillademos.org/files/16559/standard-box-model.png)

#### 怪异（IE）盒模型

​	使用box-sizing：border-box来进行切换

​	怪异盒模型width和height直接设置盒子大小

![IE box](https://mdn.mozillademos.org/files/16557/alternate-box-model.png)

#### margin垂直方向折叠

​	对于块元素，在垂直方向的margin会取最大值而非叠加