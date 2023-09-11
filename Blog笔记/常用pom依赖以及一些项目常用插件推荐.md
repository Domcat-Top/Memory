### Thymeleaf、MyBatis-plus 等常用pom引用

#### Thymeleaf模板引擎
```java
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
#### Thymleaf使用时需要加命名空间
```
xmlns:th="http://www.thymeleaf.org"
```
#### 一些简单的基础用法
```
 语法规则：th：text：改变当前元素里面的文本内容；
th：任意属性都可以用来替换原生属性的值 
• 表达式：
 ${….} 获取变量的值   用的最多
	获取对象属性
	使用过内置的基本对象 eg：${session.foo}
	内置的一些工具对象
*{….}选择表达式 和${…}在功能上一样 
 补充使用 配合th:object="${session.ser}"
 #{…}获取国际化内容
 @{…..}定义URL的 	       
 ~{….}片段文档的表达式
```
#### MySQL、Mybatis-plus驱动包
```java
 <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>8.0.17</scope>
</dependency>
<dependency>
  <groupId>com.baomidou</groupId>
  <artifactId>mybatis-plus-boot-starter</artifactId>
  <version>3.2.0</version>
</dependency>
```
#### Druid数据源
```java
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>
```
#### Druid yml配置写法：
        druid:
          initial-size: 5 # 连接池的初始化大小
          min-idle: 10 #　最小空闲连接数
          max-active: 20 #　最大空闲数
          web-stat-filter:
            exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*" #不统计这些请求数据
          stat-view-servlet: #　访问监控网页的登录用户名和密码
            login-username: druid
            login-password: druid

#### Shiro安全框架
```java
<!--ShiroSubject用户SecurityManager管理所有用户Realm连接数据-->
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-spring</artifactId>
	<version>1.4.1</version>
</dependency>
```
#### logj日志框架

```java
<dependency>
   <groupId>log4j</groupId>
   <artifactId>log4j</artifactId>
   <version>1.2.17</version>
</dependency>
```
#### Shiro整合Thymleaf
```java
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>     		<version>2.0.0</version>
</dependency>
```

###2.项目中用到的一些插件网址

[背景图片网址](https://www.toptal.com/designers/subtlepatterns/)

[前端评论区模板](https://1.semantic-ui.com/views/comment.html )

[编辑器：Markdown]( https://pandao.github.io/editor.md/)

[内容排版：typo.css]( https://github.com/sofish/typo.css/)

[代码高亮：prism]( https://github.com/PrismJS/prism)

[滚动侦测：waypoints]( http://imakewebthings.com/waypoints/)

[平滑滚动：jquery.scrolTo](https://github.com/flesler/jquery.scrollTo)
[目录生成：Tocbot](https://tscanlin.github.io/tocbot/)

[二维码生成：qrcode.js](https://davidshimjs.github.io/qrcodejs/)

### 3.前端使用模板 Semantic UI 
#### 推荐：Layui、bootstrap