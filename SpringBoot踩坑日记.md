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

#### 5. git初始化项目以及项目绑定（忘记了）

- 创建github仓库

- 对应文件夹打开隐藏文件，删除.git文件

- 终端执行命令：

  ```shell
  - git init 
  - git remote add origin 仓库地址
  - git add .
  - git commit -m "init project"
  - git push origin master  # 执行这一步以后，会弹出绑定框，让你绑定github账户
  - git push origin master
  # 至此，大功告成
  ```





