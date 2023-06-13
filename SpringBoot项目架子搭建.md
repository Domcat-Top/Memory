## SpringBoot项目架子搭建

> 写在前面：一切以代码为准，这里是梗概

### 1. 项目依赖

- 打包方式：pom
- 父子间的parent关系
  - model

### 2. 接口相关工具类

#### 2.1 Mybatis-plus配置

- 配置mapperScan，看自己的注释吧，别人也看不懂

- 配置固定代码：

  ```java
  /**
       * 新的分页插件，一级缓存和二级缓存遵循了Mybatis的规则
       * 需要设置mYBATISConfiguration#useDeprecatedExecutor = false
       * 避免缓存出现问题
       */
      @Bean
      public MybatisPlusInterceptor optimisticLockerInnerInterceptor(){
          MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
          //向Mybatis过滤器链中添加分页拦截器
          interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
          return interceptor;
      }
  
      @Bean
      public ConfigurationCustomizer configurationCustomizer() {
          return configuration -> configuration.setUseDeprecatedExecutor(false);
      }
  ```

#### 2.2 编写统一返回接口类

- 看代码去，没含量

#### 2.3 统一异常返回类

- 接口出现异常时候，返回相应的结果，自己期望的结果，而不是堆栈

- 步骤：
  1. 自定义全局异常--切面和responseBody返回
  2. 自定义运行时异常：暂时没有抛，等具体业务时再进行抛出（要我的话就一劳永逸了，当场抛出）

#### 2.4 Swagger配置

- 自己看去

### 2.5 后面就是写业务了



















