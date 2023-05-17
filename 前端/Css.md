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































































































