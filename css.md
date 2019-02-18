#### CSS基础知识点  

* 盒模型  
  页面渲染时，dom元素所采用的布局模型，通过**box-sizing**设置，默认为content-box，根据计算宽高的区域分为
  - content-box(标准盒模型)：宽度=内容的宽度(content) + border + padding + margin
  - border-box(IE盒模型)：宽度=内容宽度(content + border + padding) + margin
  - padding-box
  - margin-box

* BFC  
  块级格式化上下文(block formatting context)，是一个独立的渲染区域，让处于BFC内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。  
  - 触发条件：
    - 根元素html
    - position: absolute/fixed
    - display: inline-block/table
    - float元素
    - overflow !== visible
  - 规则：
    - 属于同一个BFC的两个相邻Box垂直排列
    - 属于同一个BFC的两个相邻Box的margin会发生重叠
    - BFC中子元素不会超出他的包含块
    - BFC的区域不会与float的元素区域重叠
    - 计算BFC的高度时，浮动子元素也参与计算
    - 文字层不会被浮动层覆盖，环绕于周围
  - 应用：
    - 阻止margin重叠
    - 可以包含浮动元素-清除内部浮动
    - 自适应两栏布局
    - 可以阻止元素被浮动元素覆盖

* 居中布局
  - 水平居中
    - 行内元素：text-align: center
    - 块级元素：margin: 0 auto
    - absolute + transform
    - flex + justify-content: center
  - 垂直居中
    - line-height: height
    - absolute + transform
    - flex + align-items: center
    - table
  - 水平垂直居中
    - absolute + transform
    - flex + justify-content + align-items
    - 块级元素和伪元素(display: inline-block + vertical-align: middle) + 伪元素(content: '' + widht: 1px + height: 100%)

* 选择器优先级
  - !important > 行内样式 > #id > .class > tag > * > 继承 > 默认
  - 选择器从右往左解析

* 清除浮动
  - 增加尾元素清除浮动
    - after / <br> : clear: both
  - 创建父级BFC
  - 父级设置高度


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