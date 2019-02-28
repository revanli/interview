#### CSS基础知识点  

* 常用布局
  - 浮动布局
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
    - position: absolute / fixed
    - display: inline-block / table / flex
    - 浮动元素: float除none以外的值
    - overflow: overflow除了visible以外的值(hidden, auto, scroll)
  - 规则：
    - 属于同一个BFC的两个相邻Box垂直排列
    - 属于同一个BFC的两个相邻Box的margin会发生重叠
    - BFC中子元素不会超出他的包含块
    - BFC的区域不会与float的元素区域重叠
    - 计算BFC的高度时，浮动子元素也参与计算
    - 文字层不会被浮动层覆盖，环绕于周围
  - 应用：
    - 可以避免margin重叠，放在不同的BFC容器中
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
  - 圣杯布局
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
      /* bfc */
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
      <div id="center" class="column">\#center</div>
      <div id="left" class="column">\#left</div>
      <div id="right" class="column">\#right</div>
    </div>
    <div id="footer">#footer</div>
  </body>
  ```
  - 双飞翼布局
  - 圣杯布局
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
      /* bfc */
      overflow: hidden;
    }
    #container .column {
      position: relative;
      float: left;
      text-align: center;
      height: 300px;
      line-height: 300px;
    }
    /* 差异点 */
    .middle-container {
      float: left
      width: 100%;
      height: 300px;
      background: rgb(206, 201, 201);
    }
    /* 差异点 */
    #center {
      margin-left: 200px;
      margin-right: 150px;
    }
    #left {
      width: 200px;
      right: 200px;
      /* 负的中间的div宽度 */
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
    <div id="header">\#header</div>
    <div id="container">
      <!-- 差异点 -->
      <div class="middle-container">
        <div id="center">\#center</div>
      </div>
      <div id="left" class="column">\#left</div>
      <div id="right" class="column">\#right</div>
    </div>
    <div id="footer">\#footer</div>
  </body>
  ```
  - 相同点：三栏全部float浮动，在左右两栏加上负margin与中间的div并排，形成三栏布局
  - 不同点：
    - 圣杯布局：为了中间div不被遮挡，将中间div设置左右padding-left和padding-right后，将左右两个div用相对布局position: relative并分别配合right和left属性
    - 双飞翼布局：直接在中部div外部包一层div, 然后在该div内用margin-left和margin-right左右两栏预留位置

* 解决移动端Retina屏幕1px边框问题  
  在Retina屏幕，屏幕的像素比不是1，就是css像素和设备的像素不是1：1的关系，此时就会出现css像素1px，但是肉眼看上去是2px的问题。
  - 解决方法：
    - 使用background渐变来解决
    ```css
    @media screen and (-webkit-min-device-pixel-ratio: 2) {
      .1px-boder {
        background-image: -webkit-linear-gradient(180deg, transparent 0%, transparent 50%, gray, 100%)
      }
    }
    ```
    - 使用伪类 + tranform scale: 使用伪类设置宽高模拟边框，然后使用transform: scale(0.5)缩小一倍解决
    - border-image: 使用图片代替像素变更，因为图片不会缩放，缺点是不同的情况需要准备不同的图片