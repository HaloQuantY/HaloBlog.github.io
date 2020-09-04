### Bootstrap笔记(freeCodeCamp)

- 响应式图片: img-responsive

- 文本居中: text-center

- 文本背景颜色：

  text-primary（蓝色）

  text-danger(橘红色)

- 创建默认button: btn btn-default 

- 块状button: btn-block

- 为button改变背景颜色（替换button-default类）：

  btn-primary（蓝色）

  btn-info（天蓝色）

  btn-danger（橘红色）

- 栅格系统：12纵列法，为行容器指定row类，而子元素即使是块元素也不能够直接指定col-xx-*类，需要包在一个div中

- 指定列和列的宽度：

  col-md-*

  col-xs-*

  在类中，md和xs指定的是列的绝对宽度（和屏幕宽度有关），*则是具体相对于行的宽度

- font-awesome使用

- 响应式radio单选框：

  对于表单元素也可以使用栅格系统，直接对单选框使用栅格系统达成效果是响应式横向均匀分布

- 响应式checkbox：

  应用栅格系统实现响应式横向均匀分布

- 使用form-control类美化<input><textarea><select>文本类元素

  form-control所控制的类宽度为100%

- 利用栅格系统将form元素放置在一排，可结合form-control类使用

- 保证页面移动端响应式：container-fluid容器

- 为栅格中的列创建阴影区（视觉上有一定深度）