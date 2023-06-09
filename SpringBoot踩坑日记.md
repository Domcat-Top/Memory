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

- 一定注意版本，版本冲突了，死活报错，甚至出现环形依赖，那就彻底寄咯

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

- mapperScan扫描的是包，不是类。。。

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

  

B站图床搭建，原理貌似是利用了B站动态，但是不予说明，个人感觉还是用Tomcat的cdn开放



