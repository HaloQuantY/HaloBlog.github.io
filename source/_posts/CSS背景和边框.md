## CSS背景和边框

### 背景颜色

​	background-color属性，值可以是所有color值



### 背景图片

​	background-img指定背景显示图像，值为一个url

```css
.b {
  background-image: url(star.png);
  background-color: #eee;
}
```

​	背景图片和背景颜色可以同时指定，这时图片将显示在背景颜色顶部

#### 控制背景平铺

​	background-repeat属性，其值有

- no-repeat：不平铺
- repeat-x：水平平铺
- repeat-y：垂直平铺
- repeat：水平垂直平铺

#### 控制背景图像大小

​	background-size属性

- cover：保持高宽比将图片覆盖盒子，一些图片会溢出盒子
- contain：图片包含于盒子，长宽比和盒子大小不同时会出现间隙
- 数值：设置px或em等，可以设置width和height方向

#### 控制背景图片定位

​	background-position属性

- 数值：一个水平值和垂直值
- top：顶部对齐
- left：左部对齐



### 渐变背景

​	渐变用于背景时和图片一样，使用background-image进行设置

- linear-gradient（deg， color %， color %）
- radical-gradient



### 多个背景图像

​	background-image属性值中，利用逗号分隔不同图像，渐变和常规背景图片可以混合使用。

​	对应background-repeat，background-position等，也可以使用逗号分隔不同值，分别应用在多个背景图片。

​	当背景图片数量多于background-repeat，background-position的属性值数量时，多余的图片会循环应用其他属性的属性值。



### 背景附加

​	background-attachment属性

- fixed：相当于position：fixed，滚动时不会改变位置
- scroll：默认值，会跟随滚动



### 边框圆角

​	使用border-radius属性将边框设置圆角

