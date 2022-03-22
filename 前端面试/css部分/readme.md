https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing
1. 谈一谈盒模型
IE 盒模型 border-box 属性width height 包含 content border padding

W3C 标准盒模型 属性width height 只包含 content

IE 盒模型 转化 为标准盒模型
box-sizing：border-box

2. 多种方式实现上面 100px 下面自适应布局

flex
margin-top + calc
定位 + calc

3. 块元素 行内元素 行内块元素的区别是什么

块元素
常见块元素 <h1>~</h6> <p> <div> <ul> <ol> <li> 

块级元素特点
  独占一行
  可以设置宽，高 内外边距均可设置
  宽度不设置默认继承父容器宽度

只有文字才能组成段落，因此 p 标签内部不能放块元素 特别 p 标签不能放 div 同理 还有h1 ~ h6 dt 他们都是文字类块级标签 里面不能放置其他块元素

行内元素
常见行内元素 a b em i span 

行内元素特点
  相邻行内元素 一行可显示多个
  设置宽高无效， 可以设置内边距 可以设置部分外边距 top bottom 除外
  宽度是自身内容宽度

常见行内块元素
<img> <input> <td>

4. 

https://juejin.cn/post/6844903749345886216

obj上 并没有hasOwnProperty 这个方法 为什么我们能调用呢？ 这就引出 原型

每一个对象从被创建开始就和另一个对象关联 从另一个对象上继承其属性 这个 另一个对象 就是原型

当访问一个对象的属性时 
先在对象的本身找 
找不到就去对象的原型上找 
还是找不到 就去对象的原型的原型上找
如此继续 直到找到为止
或者找到最顶层的原型对象也没有找到 就结束查找 返回undefined

这条由对象及其原型组成的链就叫做原型链

每一个对象都有原型
怎么获取对象的原型呢？ _proto_ 属性

对象有 __proto__ 属性 函数有 __proto__ 属性 数组有 __proto__ 属性 只要是引用类型 就有 __proto__ 属性 指向其原型

只有函数有 prototype 属性 只有函数有 prototype 属性 只有函数有 prototype 属性


