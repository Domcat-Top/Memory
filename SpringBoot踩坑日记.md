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

  





