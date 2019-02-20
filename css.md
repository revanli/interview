#### CSS基础知识点  

* 常用布局
  - 盒模型包括怪异盒模型
  - 定位布局
    - position: absolute 会将元素转换为块元素
    - 子元素相对于父元素定位：子元素position: absolute或position: fixed，父元素position: relative
  - 瀑布流布局
    - 纯JS
      - 思路：每一列固定宽度，根据页面大小显示不同的列，页面宽度，重排，预加载一屏，滚动加载
    - multi-columns
      - column-count: number; 把div分为多少列
      - column-gap: number; 列与列的间隙
      - column-rule: column-rule-width column-rule-style column-rule-color; 类似于border
      - column-span: 指定元素跨越多少列
      - column-width: 指定宽高度
    - flex-box
      - flex-direction: row | row-reverse | column | column-row-reverse
      - flex-wrap: wrap | nowrap
      - flex-flow: \<flex-direction\> || \<flex-wrap\>
      - justify-content: flex-start | flex-end | center | space-between | space-around
      - align-items: flex-start | flex-end | center | baseline | stretch
      - align-content(多根轴线的对齐方式): flex-start | flex-end | center | space-between | space-around | streth
    - grid（[完全指南](https://css-tricks.com/snippets/css/complete-guide-grid/)）
      - grid-template-columns: 定义网格的行数、列数、网格大小
      - grid-template-areas: 定义网格区域
      - grid-row-gap、grid-column-gap: 定义网格之间的间距
      - justify-items: start | end | center: 定义网格子项的内容水平方向上的对齐方式
      - align-items: start | end | center | strech: 定义网格子项的内容垂直方向上的对齐方式
  - 圣杯和双飞翼
    - 圣杯布局要求
      - header和footer 宽度100% 高度固定
      - 中间的container是一个三栏布局
      - 三栏布局两侧布局固定不变，中间部分自动填充整个区域
      - 中间部分的高度是三栏中最高的区域的高度


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
  解法二：
  ```css
  <style>
    body {
      min-width: 600px;
    }
    #header, #footer {
      background: rgba(29, 27, 27, 0.726);
      text-align: center;
      height: 60px;
      line-height: 60px;
    }
    #footer {
      clear: both;
    }
    #container {
      padding-left: 200px;  /* leftContent width */
      padding-right: 150px; /* rightContent width */
      overflow: hidden;
    }
    #container .column {
      position: relative;
      float: left;
      text-align: center;
      height: 300px;
      line-height: 300px;
    }
    #center {
      width: 100%;
      background: rgb(206, 201, 201);
    }
    #left {
      width: 200px;
      right: 200px;
      margin-left: -100%;
      background: rgba(95, 179, 235, .9);
    }
    #right {
      width: 150px;           /* rightContent width */
      margin-right: -150px;   /* rightContent width */
      background: rgb(231, 105, 2);
    }
  </style>
  <body>
    <div id="header">#header</div>
    <div id="container">
      <div id="center" class="column">#center</div>
      <div id="left" class="column">#left</div>
      <div id="right" class="column">#right</div>
    </div>
    <div id="footer">#footer</div>
  </body>
  ```