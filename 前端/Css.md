## Css

### 1. 简介

- 一种标记语言，用于给Html结构设置样式

### 2. 编写位置

- 行内样式：style属性来添加样式（特别不推荐这样的方式编写样式）

  ```html
      <h1 style="color: red; font-size: 40px;">HelloWorld</h1>
  ```

- 内部样式：

  ```html
      <!-- 内部样式 -->
      <style>
          h1 {
              color: rebeccapurple;
              font-size: 50px;
          }
          h2 {
              color: red;
          }
          p {
              color: aliceblue;
          }
      </style>
  ```

  - 优缺点懒得赘述

- 外部样式：

  - 外部新建 .css文件，编写style标签内部的样式

  - 在html页面中引用

    ```html
    	<link rel="stylesheet" href="./position.css">
    ```

  - 这种方式可以触发浏览器的缓存机制，比较方便

- `Css`样表的优先级：
  - 行内样式
  - 内部样式 和 外部样式 的优先级相等。**后写的，就覆盖掉先前的**

### 3. 语法规范

- 选择器 + 声明块

### 4. 代码风格

- 展开风格：平时使用--字面意思
- 紧凑风格：上线使用--字面意思

### 5. 基本选择器

#### 5.1 通配选择器

- 作用：可以选择所有的`Html`元素

- 语法：

  ```html
  * {
  	属性名： 属性值；
  }
  ```

#### 5.2 元素选择器

- 当前文件的标签式指定：

  ```html
  <style>
          /* 通配选择器，选择所有的 */
          h1 {
              color: blue;
          }
      </style>
  ```

#### 5.3 类选择器

- 特指某些标签啥的：

  ```html
  	<style>
          .top {
              color: red;
          }
          .bottom {
              color: blue;
          }
          .big {
              font-size: large;
          }
      </style>
  
  	<p class="bottom big" class=“top”>helloWorld</p>
  ```

- **注意点：如果一行指定了两个class，那么后面的那个class会被忽略掉**

#### 5.4 ID选择器

- 可以存在相同的ID，但是不推荐

- 语法：

  ```html
      <style>
          #test {
              color: red;
              font-size: 99px;
          }
      </style>
  ```

### 6. 复合选择器

#### 6.1 交集选择器

- 基本只用上面两种：

  ```html
      <style>
          .hello {
              color: red;
          }
          .test {
              color: aqua;
          }
          /* 设置P标签下类名为hello的颜色是蓝色 */
          p.hello {
              color: blue;
          }
          /* 同理再追加一个ID，但是实际开发不用 */
          h1.hello#myID {
              color: chartreuse;
          }
      </style>
  ```

#### 6.2 并集选择器

- 代码示例：

  ```html
      <style>
          .helloWorld {
              color: red;
          }
          .testWorld {
              color: blue;
          }
          .cat {
              color: orange;
          }
          /* 并集选择器 */
          /* 给上面的一些样式搞了一个并集 */
          .helloWorld, 
          .testWorld, 
          .cat,
          #myID {
              background-color: green;
          }
      </style>
  ```


#### 6.3 后代选择器

- 代码示例：

  ```html
      <style>
          ul li {
              color: red;
          }
          ol li {
              color: orange;
          }
      </style>
  ```

#### 6.4 子代选择器

- 代码示例：

  ```html
      <style>
          div > a {
              color: red;
          }
          div > p > a {
              color: orange;
          }
      </style>
  ```

#### 6.5 属性选择器

- 代码示例：

  ```html
      <style>
          /* 第一种写法 */
          [title] {
              color: red;
          }
          /* 第二种写法，更加详细 */
          [title = "tomTest"] {
              color: red;
          }
          /* 第三种写法，选出具有title属性的值，并且是以a开头的 */
          [title^= "t"] {
              color: red;
          }
          /* 第四种写法，选出具有title属性的值，并且是以m结尾的 */
          [title$= "m"] {
              color: red;
          }
          /* 第五种写法，选出具有title属性的值，并且属性值中具有o */
          [title*= "o"] {
              color: red;
          }
      </style>
  ```

#### 6.6 伪类选择器

- 代码示例：

  ```html
      <style>
          /* 未访问过的a标签 */
          a:link {
              color: red;
          }
          /* 访问后的 */
          a:visited {
              color: blue;
          }
      </style>
  ```

##### 6.6.1 伪类选择器-动态伪类

- 四种状态：
  - `:link`：超链接未被访问的状态
  - `：visited`：超链接访问过的状态
  - `:hover`：鼠标悬停在元素上的状态
  - `:active`：元素激活的状态
  - `:focus`：元素激活的状态

- 代码示例：

  ```html
      <style>
          /* 这四个顺序不能乱写，否则可能不会生效 */
          a:link {
              color: red;
          }
          a:visited {
              color: aqua;
          }
          /* 鼠标悬浮效果 */
          a:hover {
              color: brown;
          }
          /* 激活状态的 */
          a:active {
              color: green;
          }
      </style>
  ```

##### 6.6.2 结构伪类

- 代码示例：

  ```html
      <style>
          p {
              font-size: 25px;
          }
          /* 第一个设置为什么颜色 */
          div > p:first-child {
              color: red;
          }
          /* 最后一个设置为什么颜色 */
          div > p:last-child {
              color: green;
          }
          /* 第几个设置为什么颜色 */
          /* 括号参数从1开始，不写的话，选不中，写n的话全选中，还可以写一些公式，实现变色处理 */
          div > p:nth-child(3) {
              color: aqua;
          }
          /* 前五个设置为黑色 */
          div > p:nth-child(-n + 5) {
              color: black;
          }
          /* 奇数橙色 2n +1 */
          div > p:nth-child(odd) {
              color: orange;
          }
          /* 偶数白色 2n */
          div > p:nth-child(even) {
              color: white;
          }
      </style>
  
      <style>
          p {
              font-size: 25px;
          }
          /* 选择该类型下第一个 */
          div > p:first-of-type {
              color: red;
          }
          div > p:last-of-type {
              color: greenyellow;
          }
          div > p:nth-last-of-type(3) {
               color: bisque;
          }
      </style>
  ```

##### 6.6.3 否定伪类

- 代码示例：

  ```html
      <style>
          p {
              font-size: 28px;
          }
          /* 排除class为fail的 */
          div > p:not(.fail) {
              color: red;
          }
          /* 排除title开头为  你要  的 */
          div > p:not([title^="你要"]) {
              color: aqua;
          }
          /* 排除第一个元素 */
          div > p:not(:first-child) {
              color: chartreuse;
          }
      </style>
  ```

##### 6.6.4 UI伪类

- 代码示例：

  ```html
      <style>
          /* 勾选了就变大 */
          input:checked {
              width: 100px;
              height: 100px;
          }
          /* 禁用的输入框颜色改变 */
          input:disabled {
              background-color: red;
          }
          /* 选中可用的input */
          input:enabled {
              background-color: aqua;
          }
      </style>
  ```

##### 6.6.5 目标伪类

- 选中锚点所指向的元素

- 代码示例：

  ```html
      <style>
          div {
              background-color: aqua;
              height: 300px;
          }
          div:target {
              background-color: red;
          }
      </style>
  ```

##### 6.6.6 语言伪类

- 代码示例：

  ```html
      <style> 
          div:lang(en) {
              color: red;
          }
          :lang(zh-CN) {
              color: aqua;
          }
      </style>
  ```

#### 6.7 伪元素选择器

- 选中元素中的一些特殊位置

- 代码示例：

  ```html
      <style>
          /* 选中div中的第一个文字 */
          div::first-letter{
              font-size: 40px;
              color: red;
          }
          /* 改变选中文字的背景和文字的颜色 */
          div::selection {
              background-color: orange;
              color: aquamarine;
          }
          /* 在P标签内容之前添加东西 */
          p::before {
              content: "￥";
          }
      </style>
  ```

#### 6.8 选择器优先级

- 行内 > ID选择器 > 类选择器 > 元素选择器 > 通配选择器

- 复合选择器需要计算权重：
  - 格式：（a, b, c）
    - a：ID选择器的个数
    - b：类、伪类、属性 选择器的个数
    - c：元素、伪元素选择器的个数

### 7. CSS三大特性

- 层叠性：
  - 如果发生了样式重叠，那么就会根据一定的规则，进行样式的覆盖
- 继承性：
  - 元素会自动拥有其父元素、或祖先元素上设置的某些样式
- 优先继承离得近的

### 8. 像素

- px常识，不多赘述

### 9. 颜色

- rgba，第四位是透明度

### 10. 常用字体大小

- 字体的复合属性有要求：
  - 最后一位是 字体的名称，倒数第二位是 字体的大小
- 非重要的不写了，看代码吧

### 11. CSS3

#### 11.1 新增单位

- vw：视口宽度的百分比

  ```html
  width: 50vw;
  ```

- vh：视口高度的百分比

- vmax：宽或高的最大值，哪个大，它就是哪个
- vmin：宽或高的最小值

#### 11.2 新增选择器

- 讲过了

#### 11.3 新增盒子模型

- 扩张主义：content-box

- 压缩主义：border-box

- resize：可以给盒子调整大小了，但是需要和overflow配合使用

- box-shadow：最少写两个参数，一个水平，一个垂直，表示出现盒子的阴影

  ```html
  - 第一个参数：水平距离
  - 第二个参数：垂直距离
  - 第三个参数：可以设置颜色，也可以设置阴影的模糊程度（px设置）
  - 第四个参数：设置阴影颜色
  - 第五个参数：阴影的外延
  - 第六个参数：内阴影，阴影在里面
              水平阴影的位置 垂直	    可选（模糊的距离） 可选（模糊的外延值）	可选（外阴改为内阴）
  box-shadow: h-shadow    v-shadow blur 			 spread 			color   inset;
  ```

- opacticy：调整整个盒子以及盒子中内容的透明度

#### 11.4 新增背景属性

- background-origin：content-box，从内容区开始铺图
- background-origin：padding-box，从padding区开始铺图

- background-clip：对背景图修剪，border-box、padding-box、content-box
- background-size：contain、cover

#### 11.5 新增边框属性

- border-radius：设置圆角，可以把盒子变为圆形
- border-top-left-radius：左上角变为圆形，其他三个角同理，还可以设置两个参数，变为椭圆啥的

#### 11.6 新增文本属性

- 文本阴影：text-shadow

- 文本换行：

  ```html
  white-space：pre按原文显示，超出也不换行
              pre-wrap超出换行
              pre-line左右空格删除，会换行
              nowrap不换行
  ```

- 文本溢出：overflow的值必须设置为visible

  ```html
  text-overflow:clip  文本超出暴力截掉
  ellipsis，文本超出的话，用三个点占位
  ```

- 文本修饰：text-decoration：overline dashed blue

- 文本描边：webkit浏览器支持，需要的话百度

#### 11.7 渐变

##### 11.7.1 线性渐变

- ```html
  background-image: linear-gradient(从什么颜色，变到什么颜色，再到什么颜色);
  
  // 指定方向
  background-image: linear-gradient(to right, red, yellow, green)
  background-image: linear-gradient(to right top, red, yellow, green);
  
  // 角度---偏离中心轴
  background-image: linear-gradient(20deg, red, yellow, green)
  
  // 渐变的宽度
  background-image: linear-gradient(20deg, red 50px, yellow 50px, green 50px)
  ```

##### 11.7.2 径向渐变

- ```html
  // 由中心点四散
  background-image: radial-gradient(从什么颜色，变到什么颜色，再到什么颜色);
  
  // 指定起始位置
  background-image: radial-gradient(at right top, 从什么颜色，变到什么颜色，再到什么颜色);
  // 指定起始位置
  background-image: radial-gradient(at 100px 50px, 从什么颜色，变到什么颜色，再到什么颜色);
  background-image: radial-gradient(circle, 从什么颜色，变到什么颜色，再到什么颜色);
  // 渐变区域
  background-image: radial-gradient(red 50px, yellow 50px, green 50px)
  ```

##### 11.7.3 重复渐变

- 在没有发生渐变的区域，发生渐变

- ```html
  background-image: repeating-linear-gradient(从什么颜色，变到什么颜色，再到什么颜色);
  ```

#### 11.8 web字体

- ```html
  @font-face {
  	font-family: "fontName";
  	src: url(引入路径，本地或网络);
  }
  h1 {
  	font-family: "fontName";
  }
  ```

- 在线定制特定字的网站：

  - www.iconfont.cn/webfont
  - 对于你需要的字，生成相应的字的文件
  - 全部引入当前页面
  - 粘贴demo.html中的font-face中的配置

#### 11.9 变换（旋转放大啥的）

- 2D变换

  - 位移：transform: translate(50px, 50px);

  - 缩放：transform: scale(1.5);
  - 旋转：transform: rotateZ(30edg);
  - 扭曲：transform: skewX(30deg, 30deg);
  - 多重变换：transform: translate(100px, 100px) scale(0.5);
  - 设置旋转的原点：transform-origin: 50px 50px;

- 3D：想实现3D效果，首先要找到父元素，开启3D空间

  - ```html
                /* 开启3D空间 */
                transform-style: preserve-3d;
                /* 设置景深，才能看到3D效果 */
                perspective: 500px;
    ```

  - 景深：设置透视点的位置

    ```html
    			/* 设置透视点的位置 */
                perspective-origin: 100px 100px;
    ```

  - 位移：

    ```html
                /* 根据景深定的，超过景深的话，当前平面就跑到脑后去了 */
                transform: translateZ(400px);
    ```

  - 旋转：

    ```html
                transform: rotateX(45deg);
    ```

#### 11.10 过渡

- 基本使用：

  - ```html
    // 设置哪个属性需要过渡效果
    transition-preperty: height, width;
    // 设置过渡的时间
    transition-duration: 1s;
    ```

  - 可以过渡的属性：属性可以用数字、百分比之类的表示，则可以使用过渡

- 高级使用：

  - ```html
    // 过渡的延迟
    transition-delay: 2s;
    // 平滑过渡
    transition-timing-function: ease;
    // 匀速过度
    transition-timing-function: linear;
    // 先慢后快
    transition-timing-function: ease-in;
    // 先快后慢
    transition-timing-function: ease-out;
    // 慢快慢
    transition-timing-function: ease-in-out;
    // 直接到达终点，不考虑过渡的时间
    transition-timing-function: step-start;
    // 时间结束后直接到终点
    transition-timing-function: step-end;
    // 一步一步到终点：分步过渡
    transition-timing-function: steps(20 ,start);
    // 贝塞尔曲线---可以做一个橡皮经的效果
    transition-timing-function: 贝塞尔曲线公式
    ```

  - 复合属性：没啥记的

#### 11.11 动画

- 基本使用：

  - ```html
    // 应用动画
    animation-name: cartoonName;
    // 动画持续时间
    animation-duration: 3s;
    
    // 定义一组关键帧（定义一个动画）
    @keyframes cartoonName {
    	// 第一帧
    	from {
    		
    	}
    	// 最后一帧
    	to {
    		// 移动900px
    		transform: translate(900px);
    	}
    }
    
    // 第二种写法
    @ketframes cartoonName {
    	// 第一帧
    	0% {
    		
    	}
    	50% {
    
    	}
    	// 最后一帧
    	100% {
    		// 移动900px
    		transform: translate(900px);
    	}
    }
    ```

- 其他属性：

  - ```html
    // 设置动画的方式
    animation-timing-function: linear;
    // 动画播放的次数
    animation-iteration-count: infinite;
    // 动画的方向---参数很多，都只写了一个，可以百度
    animation-direction: alternate;
    // 动画以外的状态(不发生动画的时候在哪里)---可以设置为初始值、末尾值
    animation-fill-mode: forwards;
    // 动画的播放状态
    animation-play-state: pause;
    ```

- 动画和过渡的区别：

  - 过渡需要触发条件，动画不需要触发条件
  - 动画有关键帧，还可以操作中间状态，过渡不可以，只有始末状态

#### 11.12 多列布局

- ```html
  // 指定列数
  column-count: 5
  // 指定宽度，自动计算列数
  column-width: 250px;
  // 调整列间距
  column-gap: 20px;
  // 边框样式
  column-rule-style: dashed;
  column-rule-color: red;
  column-rule-width: 2px;
  ```

#### **==11.13 伸缩盒模型==**

- ```html
  // 把该元素变为伸缩容器---开启了flex布局
  display: flex;
  // 实现多个容器并排了，但是中间有一个缝，尽量不用这个
  display: inline-flex;
  ```

- 主轴方向：

  - ```html
    // 调整主轴的方向，水平从右到左
    flex-direction: row-reverse;
    ```

  - **主轴默认是从左到右的**

- 主轴上的换行方式：

  - ```html
    flex-wrap: wrap;
    ```

- 主轴的对齐方式：

  - ```html
    // 主轴的对齐方式，默认为主轴的起始位置
    justify-content: flex-start;
    // 从右排
    justify-content: flex-end;
    // 居中
    justify-content: center;
    // 项目均匀的分布在一行中
    justify-content: space-around;
    // 项目与项目之间的距离是相等的，并且在两边没距离
    justify-content: space-between;
    // 均匀的分布在一行中
    justify-content: space-evenly;
    ```

- 侧轴对齐方式：

  - 一行：

    - ```html
      // 侧轴的起始位置对齐
      align-items: flex-start;
      align-items: center;
      align-items: flex-end;
      // 基线对齐
      align-items: basline;
      // 拉伸对齐-所有的伸缩项目都没给高度，才会生效
      align-items: stretch;
      ```

  - 多行：

    - ```html
      // 侧轴的起始位置对齐
      align-content: flex-start;
      // 结束位置对齐
      align-content: flex-end;
      // 中间位置对齐
      align-content: center;
      // 伸缩项目之间的距离是相等，并且是中间距离的二倍
      align-content: space-around;
      // 伸缩项目之间的距离是相等的，且边缘没有距离
      align-content: space-between;
      // 伸缩项目之间的距离是相等的
      align-content: space-evenly;
      // 拉伸--项目没给高度才有效
      align-content: stretch;
      ```

##### 11.13.1 元素的水平垂直居中

- ```html
  display: flex;
  align-items: center;
  justify-content: center;
  ```

##### 11.13.2 基准长度

- ```html
  // 根据主轴的方向，设置伸缩项目的基准长度
  flex-basis: 300px;
  ```

#### 11.14 伸缩性

- 伸：数字控制的是比例，权重
  - flex-grow: 1;

- 缩：它的权重是按照整体计算的
  - flex-shrink: 1;

#### 11.15 排序问题和单独对齐（了解）

- ```html
  order:1;
  ```

- 自身单独对齐：

  - ```html
    align-self: center;
    ```

#### 11.16 响应式布局

- 媒体查询：
  - 根据当前的窗口，来做出相应的操作，比如说在手机上和电脑上做出不同的显示





BFC





### 12. 动画库

- Hover.css
- Animate.css
- Magic官网
- csshake
- hint.css
- kite





























































































