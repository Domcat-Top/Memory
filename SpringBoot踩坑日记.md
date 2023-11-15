## SpringBoot踩坑日记

#### 1. Maven编译插件报错

- 解决方式：
  - 添加版本号：`<version>3.9.0</version>`
  - 添加组：`<groupId>org.apache.maven.plugins</groupId>`
  - 版本是否兼容
  - 项目结构和编译器的配置是否更改

#### 2. IDEA不显示找不到Main方法的一个原因

- 引入了一个JDK内部专用的API，导致main方法失效
- 比如引入了`sun.String`，会导致Main方法无法执行

#### 3. 创建项目ServerURL爆红

- 出现原因：网速问题，或者就是因为代理关系，被墙了
- 解决方式：ServerURL改为`http://spring.aliyun.com`
- 差异性：
  - 依赖选择页面会出现Aliyun的默认Cloud依赖，注意选择，同时Spring版本会根据Aliyun仓库的版本来，需要后期更改

#### 4. Bean注解用于方法上

- 当配置类中的方法存在这个注解时，这个注解会将方法的返回值放入到`IOC`容器中
- 当@Bean标注的方法中有参数的时候，一般会和`@Qualifier（"beanId"）`一起使用，会去IOC容器中寻找该类型的Bean作为参数注入进该方法中

#### 5. Navicat的日期格式设置初始化

- ```sql
  alter table t_tag add update_time datetime NOT NULL DEFAULT CURRENT_TIMESTAMP
  ```

- 直接在设计表中设计的话，会出现转义问题

#### 6. SpringBoot整合Mybatis-plus和PageHelper

- 一定注意版本，版本冲突了，死活报错，甚至出现环形依赖，那就彻底寄了

- ```xml
          <!--mybatis-plus 持久层-->
          <dependency>
              <groupId>com.baomidou</groupId>
              <artifactId>mybatis-plus-boot-starter</artifactId>
              <version>3.5.1</version>
          </dependency>
          <!--   分页插件-->
          <dependency>
              <groupId>com.github.pagehelper</groupId>
              <artifactId>pagehelper-spring-boot-starter</artifactId>
              <version>1.4.3</version>
          </dependency>
  ```

- PageHelper写到查询的上一句即可，也没其他要求了

- mapperScan扫描的是包，**不是类**，下次写错了是猪

- 配置文件配置一下xml的路径即可，没别的了

#### 7. Mybatis-plus驼峰转下划线

- 数据库字段用驼峰的话，在使用中，它会默认帮你转为下划线字段，如果实体类没有进行下划线定义，会报错

- 添加如下配置：

  ```yaml
  mybatis-plus:
    configuration:
      # 关闭驼峰和下划线互转
      map-underscore-to-camel-case: false
  ```


#### 8. Window删除服务两种方式

1. Dos删除
   - 管理员打开Dos
   - 执行命令：`sc delete ServiceName`
2. 修改注册表进行删除/卸载服务：
   - 运行执行`regedit`，打开注册表
   - 找到`HKEY_LOCAL_MACHINESYSTEMCurrentControlSetService`，删除对应的键值

#### 9. Token验证登录逻辑流程

1. 客户端使用账号密码请求登录
2. 服务端收到请求，去验证用户名和密码
3. 验证成功后，服务端签发Token，并且发送Token到客户端
4. 客户端收到Token后，把它存到到Cookie或LocalStorage中
5. 客户端每次向服务端请求资源都需要携带服务端签发的Token
6. 服务端收到请求后，去验证客户端请求里携带的Token信息，如果验证成功，就向客户端返回相关的用户信息

#### 10. MybatisPlus代码生成器记录

- 依赖：

  ```xml
  		<!-- mysql驱动 -->
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>8.0.27</version>
          </dependency>
          
  		<!-- springboot整合MybatisPlus -->
          <dependency>
              <groupId>com.baomidou</groupId>
              <artifactId>mybatisplus-spring-boot-starter</artifactId>
              <version>1.0.5</version>
          </dependency>
  
  		<!-- MyBatisPlus依赖 -->
          <dependency>
              <groupId>com.baomidou</groupId>
              <artifactId>mybatis-plus</artifactId>
              <version>3.4.3.4</version>
          </dependency>
  
  		<!-- MyBatis代码生成器依赖 -->
          <dependency>
              <groupId>com.baomidou</groupId>
              <artifactId>mybatis-plus-generator</artifactId>
              <version>3.5.1</version>
          </dependency>
  
  		<!-- MyBatis代码生成器的模板引擎 -->
          <dependency>
              <groupId>org.apache.velocity</groupId>
              <artifactId>velocity-engine-core</artifactId>
              <version>2.3</version>
          </dependency>
  ```

- 具体代码：

  ```java
  @Test
      public void tomTest() {
          //创建一个代码生成器
          FastAutoGenerator.create("slqUrl",
                          "root", "root")
                  //全局配置(GlobalConfig)
                  .globalConfig(builder -> {
                      builder.author("Tom") // 设置作者，可以写自己名字
                              //.enableSwagger() // 开启 swagger 模式，这个是接口文档生成器，如果开启的话，就还需要导入swagger依赖
                              .fileOverride() // 覆盖已生成文件
                              .dateType(DateType.TIME_PACK) //时间策略
                              .commentDate("yyyy-MM-dd") //注释日期
                              .outputDir("E:\\JavaCode\\TestRBAC\\src\\main\\java"); // 指定输出目录，一般指定到java目录
                  })
                  //包配置(PackageConfig)
                  .packageConfig(builder -> {
                      builder.parent("com.tom") // 设置父包名
                              .moduleName("") // 设置父包模块名，这里一般不设置
                              .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "E:\\JavaCode\\TestRBAC\\src\\main\\resources\\mapper")); // 设置mapperXml生成路径，这里是Mapper配置文件的路径，建议使用绝对路径
                  })
                  //策略配置(StrategyConfig)
                  .strategyConfig(builder -> {
                      builder.addInclude("r_Menu") // 设置需要生成的表名
                              .addInclude("t_menu") // 设置需要生成的表名
                              .addInclude("t_role") // 设置需要生成的表名
                              .addInclude("t_user") // 设置需要生成的表名
                              .addTablePrefix("t_") // 设置过滤表前缀
                              .addTablePrefix("r_"); // 设置过滤表前缀
  
                      builder.serviceBuilder()
                              .formatServiceFileName("%sService") //设置service的命名策略,没有这个配置的话，生成的service和serviceImpl类前面会有一个I，比如IUserService和IUserServiceImpl
                              .formatServiceImplFileName("%sServiceImpl"); //设置serviceImpl的命名策略
                      builder.controllerBuilder()
                              .enableRestStyle(); // 开启生成@RestController 控制器，不配置这个默认是Controller注解，RestController是返回Json字符串的，多用于前后端分离项目。
                      builder.mapperBuilder()
                              .enableMapperAnnotation();//开启 @Mapper 注解，也就是在dao接口上添加一个@Mapper注解，这个注解的作用是开启注解模式，就可以在接口的抽象方法上面直接使用@Select和@Insert和@Update和@Delete注解。
                  })
  //                .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker引擎模板，默认的是Velocity引擎模板
                  .templateEngine(new VelocityTemplateEngine())
                  .execute(); //执行以上配置
      }
  ```


#### 11. 多参数规范化泛型

- ```java
  public class Triple<T1, T2, T3> extends Pair<T1, T2> {
  
      protected final T3 t3;
  
      public T3 getThird(){return t3;}
  
      public Triple(T1 iputT1, T2 iputT2, T3 iputT3) {
          super(iputT1, iputT2);
          t3 = iputT3;
      }
  }
  ```

- 对于出现产生连锁反应的组件，需要一次操作的，则使用继承式泛型封装

#### 12. SpringBoot自动任务

1. 重写ApplicationRunner接口的run方法--太过简单不多赘述
2. 重写CommandLineRunner接口的run方法--太过简单不多赘述
3. 定时器：设置成一分钟一次或者一秒一次，程序启动后也就自动执行了

4. 实现InitializingBean接口，重写afterPropertiesSet方法

   - ```java
     @Service
     public class UserService implements InitializingBean {
         @Resource
         private UserMapper userMapper;
      
         public List<User> getByName() {
             List<User> userList = userMapper.selectList(null);
             return userList;
         }
      
         @Override
         public void afterPropertiesSet() {
             System.out.println("这里是你要执行的方法捏！");
         }
     }
     ```

#### 13. SpringBoot整合WebSocket

- 依赖：

  ```xml
  <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-websocket</artifactId>
          </dependency>
  ```

- 配置类：

  ```java
  @Configuration
  public class WebSocket {
  
      /**
       * bean注册：会自动扫描带有@ServerEndpoint注解声明的Websocket Endpoint(端点)，注册成为Websocket bean。
       * 要注意，如果项目使用外置的servlet容器，而不是直接使用springboot内置容器的话，就不要注入ServerEndpointExporter，因为它将由容器自己提供和管理。
       */
      @Bean
      public ServerEndpointExporter serverEndpointExporter() {
          return new ServerEndpointExporter();
      }
  
  }
  ```

- **Server实现类：**

  ```java
  /**
   * @author: Tom
   * @date: 2023/10/24 14:10
   * @description:
   */
  @Component
  // 定义websocket服务器端，它的功能主要是将目前的类定义成一个websocket服务器端。
  // 注解的值将被用于监听用户连接的终端访问URL地址
  @ServerEndpoint("/websocket")
  @Slf4j
  public class WebSocketServer {
  
      //实例一个session，这个session是websocket的session
      private Session session;
  
      //存放websocket的集合（本次demo不会用到，聊天室的demo会用到）
      private static CopyOnWriteArraySet<WebSocketServer> webSocketSet = new CopyOnWriteArraySet<>();
  
      //前端请求时一个websocket时
      @OnOpen
      public void onOpen(Session session) {
          this.session = session;
          webSocketSet.add(this);
          log.info("【websocket消息】有新的连接, 总数:{}", webSocketSet.size());
      }
  
      //前端关闭时一个websocket时
      @OnClose
      public void onClose() {
          webSocketSet.remove(this);
          log.info("【websocket消息】连接断开, 总数:{}", webSocketSet.size());
      }
  
      //前端向后端发送消息
      @OnMessage
      public void onMessage(String message) {
          log.info("【websocket消息】收到客户端发来的消息:{}", message);
      }
  
      //  try-catch 中的那一行是最重要的~
      public void sendMessage(String message) {
          for (WebSocketServer webSocketServer: webSocketSet) {
              log.info("【websocket消息】广播消息, message={}", message);
              try {
                  webSocketServer.session.getBasicRemote().sendText(JSON.toJSONString(new TestPojo("tom", 72)));
              } catch (Exception e) {
                  e.printStackTrace();
              }
          }
      }
  }
  
  ```

- 前端：

  ```html
  <body>
      <div id="message"></div>
      <input type="button" name="" id="btn" value="开始" />
  </body>
  <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
  <script type="text/javascript">
      var websocket = null;
  
      //判断当前浏览器是否支持WebSocket
      if ('WebSocket' in window) {
          websocket = new WebSocket("ws://localhost:8888/websocket");
      } else {
          alert('Not support websocket')
      }
  
      //连接发生错误的回调方法
      websocket.onerror = function () {
          setMessageInnerHTML("发生错误");
      };
  
      //连接成功建立的回调方法
      websocket.onopen = function (event) {
          setMessageInnerHTML("建立连接");
      }
  
      //接收到消息的回调方法
      websocket.onmessage = function (event) {
          console.log(event.data)
      }
  
      //连接关闭的回调方法
      websocket.onclose = function () {
          setMessageInnerHTML("关闭连接");
      }
  
      //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
      window.onbeforeunload = function () {
          alert("已关闭连接");
          websocket.close();
      }
  
      //将消息显示在网页上
      function setMessageInnerHTML(innerHTML) {
          document.getElementById('message').innerHTML += innerHTML + '<br/>';
      }
  
      //关闭连接
      function closeWebSocket() {
          alert("已关闭连接");
          websocket.close();
      }
      // 开始
      // 后端给前端的
      $("#btn").click(function () {
          $.ajax({
              url: "http://localhost:8888/test/send",
              type: 'post',
              success: function (HTML) {//返回页面内容
                  console.log(HTML);
              }
          });
      })
  </script>
  ```

#### 14. SpringBoot定时任务

- 如果执行的定时任务有阻塞行为，需要进行 多线程 定时器任务 的配置

- 代码如下：

  ```java
  /**
   * @author: Tom
   * @date: 2023/11/7 10:28
   * @description:
   */
  @Configuration
  public class ScheduledConfiguration implements SchedulingConfigurer {
      @Override
      public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
          // 根据自己实际业务来设置线程池的大小，我这里测试的话，设置了三个，具体的业务根据自己的情况来设置。
          scheduledTaskRegistrar.setScheduler(Executors.newScheduledThreadPool(3));
      }
  }
  ```

- 需要额外在使用的Component类添加异步注解：

  ```java
  // 启动Spring的异步方法来执行当前的Component
  @EnableAsync
  ```


#### 15. 原生Java获取Http请求JSON数据

- Java：

  ```java
   /**
       * Http请求，调取第三方接口，获取到一句话
       * 获取的是 content 和 name 字段
       * @return 返回获取到的文案
       */
      public static String getWord() {
          String url = "https://api.xygeng.cn/one";
          try {
              URL obj = new URL(url);
              HttpURLConnection con = (HttpURLConnection) obj.openConnection();
              // 设置请求方法和请求头
              con.setRequestMethod("GET");
              con.setRequestProperty("User-Agent", "Mozilla/5.0");
              // 读取响应内容
              BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
              String inputLine;
              StringBuilder content = new StringBuilder();
              while ((inputLine = in.readLine()) != null) {
                  content.append(inputLine);
              }
              in.close();
              JSONObject jsonObject = JSONObject.parseObject(content.toString());
              JSONObject data = jsonObject.getJSONObject("data");
              return data.getString("content") + "-- " + data.getString("name");
          } catch (Exception e) {
              e.printStackTrace();
          }
          return null;
      }
  ```

- 该JSON完整结构：

  ```json
  {
      "code": 200,
      "data": {
          "id": 92,
          "tag": "网络",
          "name": "佚名",
          "origin": "佚名",
          "content": "我在时间的轨迹上徘徊，踏上每一列经过的车。沿途的风景在渐渐远去，我举着那快叫思念的车牌，等待着最后一站——故乡。",
          "created_at": "2018-12-23T06:10:47.000Z",
          "updated_at": "2022-03-09T08:42:10.000Z"
      },
      "updateTime": 1699834653198
  }
  ```


#### 16. jar包和war包的启动区别

- **jar包：**执行 SpringBootApplication 的 run 方法，启动 IOC 容器，然后创建嵌入式 Servlet 容器
- **war包：**先是启动 Servlet 服务器，服务器启动 SpringBoot 应用 （SpringBootServletInitizer），然后启动 IOC 容器

















