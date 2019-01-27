* 盒模型？
  组成：页面上的元素都是由width, height, padding, border, margin几个属性组成盒模型，存在两种盒子模型：
    i. box-sizing: content-box; 标准盒模型，盒子元素的尺寸为内容的尺寸，padding、border、margin都不会计入盒子元素尺寸中
    ii. box-sizing: border-box; 盒子元素的尺寸为内容的尺寸加入padding, border的尺寸，而margin不会被计入元素宽高中


* 未知宽高实现水平垂直居中？
  i. flexbox: align-items: center(元素垂直居中) 、align-content: center(多根轴线的对齐方式，垂直方向)、justify-content: center(元素水平居中)、justify-content: center(内容水平居中)
  ii. 可以使用伪类，设置伪类的高度占满整个空间，然后使用inline-block和vertical-align: middle实现垂直居中

  * rem布局
    i.设置文档节点根元素的字体大小为标准单位, 以好计算为准
    ```javascript
    document.documentElement.style.fontSize = document.documentElement.clientWidth / 7.5 + 'px'
    ```
    ii.都用rem做单位
    iii.监听屏幕resize事件，动态计算根元素rem的值


* 圣杯或者双飞翼（两边固定宽，中间自适应）
```css
  <style>
    .left1{
      float: left;
      width: 200px;
      background-color: red;
      height: 200px;
    }
    .right1{
      float: right;
      width: 220px;
      height: 200px;
      background-color: green;
    }
    .middle1{
      margin-left: 200px;
      margin-right: 220px;
      height: 200px;
      background-color: blue;
    }
  </style>
  <body>
    <div class="left1"></div>
    <div class="right1"></div>
    <div class="middle1"></div>
  </body>
```

* 移动端的点击事件为什么存在300ms的延迟现象：
  这是因为在移动端的前提下，直接点击屏幕，手机浏览器需要知道你是否是想执行双击放大网页内容的操作，此时我们可以使用 touch 相关事件去解决这个延迟问题，如 touchStart 即可，有些移动端 UI 库将这个操作称为 fastclick。

