### Mysql主键策略
* 自动增长  AUTO INCREMENT ：分表时不方便
* UUID：每次操作生成随机唯一的值 ：排序不方便
* Redis实现：incr 实现
* Mybatis-plus自动生成（长19位）
* ID_WORKER:mp自带侧策略，生成19位值，数字类型使用这种策略 比如long
* ID_WORKER_STR:mp自带侧策略，生成19位值，子字符串类型使用这种策略 比如String

### mp实现自动填充
* 不需要set到对象里边的值，使用mp的方式实现数据添加（比如不用手动添加时间）
```java
    @TableField(fill = FieldFill.INSERT)
    private Date creatTime;
    @TableField(fill =FieldFill.INSERT_UPDATE)
    private  Date updateTime;
```
* 创建类，实现MetaObjectHandler，实现接口方法

```java
package com.example.handler;
import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;
import java.text.DateFormat;
import java.util.Date;

@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    DateFormat mediumDateFormat = DateFormat.getDateTimeInstance
            (DateFormat.MEDIUM,DateFormat.MEDIUM);
    // 添加填充
    @Override
    public void insertFill(MetaObject metaObject) { this.setFieldValByName("creatTime",mediumDateFormat.format(new Date()),metaObject);
        this.setFieldValByName("updateTime",mediumDateFormat.format(new Date()),metaObject);
    }
    //修改填充
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime",mediumDateFormat.format(new Date()),metaObject);
    }
}
```

### 乐观锁
* 主要解决丢失更新（通过version版本号来控制并发）
* 表中添加version字段
* 添加注解@Version配置插件
```java
@Configuration
public class MpConfig {
    /**
     * 乐观锁插件
     * @return
     */
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor(){
        return new OptimisticLockerInterceptor();
    }
}
```
### 多个ID批量查询
```java
userMapper.selectBatchIds(Arrays.asList("id1", "id2"));
```
### 分页查询（PageHelper类似）
* 配置分页插件

```java
/**
    * 分页插件
    * @return
    */
   @Bean
   public PaginationInterceptor paginationInterceptor(){
       return new PaginationInterceptor();
   }
```
* 编写分页代码 直接new page对象传入两个参数（当前页，每页显示记录数）

```java
Page<User>userPage=new Page<>(2,1);
      userMapper.selectPage(userPage, null);
      System.out.println(userPage.getCurrent());//获取当前页
      System.out.println(userPage.getRecords());//每页数据list集合
      System.out.println(userPage.getSize());//每页显示记录数
      System.out.println(userPage.getTotal());//总记录数
      System.out.println(userPage.getPages());//总页数
      System.out.println(userPage.hasNext());//是否有下一页
      System.out.println(userPage.hasPrevious());//是否有上一页
```
### 删除
#### 物理删除
* 根据id删除

* 批量删除 mapper.deleteBatchIds(集合)

#### 逻辑删除

* 先加deleted字段
* 加上@TableLogic注解
* 加上配置

```java
    @Bean
    public ISqlInjector sqlInjector(){
        return new DefaultSqlInjector();
    }
```
* 可设置默认值

```xml
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```
### 条件构造器
* ge、gt、le、lt 大于等于，大于，小于等于，小于
* eq、ne、等于，不等于
* between 之间
* like 模糊查询
* last 拼接
* select（列名字...）查询指定的列
* orderByDES降序 orderESC升序
* mp还提供了性能分析

### mp代码生成器
* 需要再引入依赖

```xml
<dependency>
   <groupId>org.apache.velocity</groupId>
   <artifactId>velocity-engine-core</artifactId>
   <version>2.0</version>
</dependency>
```

```java
package com.rw.utils;
import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import org.junit.Test;

/**
 * @author
 * @since 2018/12/13
 */
public class CodeGenerator {

    @Test
    public void run() {

        // 1、创建代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 2、全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir("F:\\e\\SpringBoot-Vue-OnlineExam-master\\education_parent\\service\\service_edu" + "/src/main/java");

        gc.setAuthor("Ruiwen");
        gc.setOpen(false); //生成后是否打开资源管理器
        gc.setFileOverride(false); //重新生成时文件是否覆盖

        gc.setServiceName("%sService");	//去掉Service接口的首字母I
        gc.setIdType(IdType.ID_WORKER_STR); //主键策略
        gc.setDateType(DateType.ONLY_DATE);//定义生成的实体类中日期类型
        gc.setSwagger2(true);//开启Swagger2模式

        mpg.setGlobalConfig(gc);

        // 3、数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/education?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("123456rw");
        dsc.setDbType(DbType.MYSQL);
        mpg.setDataSource(dsc);

        // 4、包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("eduservice"); //模块名
        pc.setParent("com.rw");
        pc.setController("controller");
        pc.setEntity("entity");
        pc.setService("service");
        pc.setMapper("mapper");
        mpg.setPackageInfo(pc);

        // 5、策略配置
        StrategyConfig strategy = new StrategyConfig();
        // 根据表名生成实体类
        strategy.setInclude("edu_course","edu_course_description","edu_chapter","edu_video");
        strategy.setNaming(NamingStrategy.underline_to_camel);//数据库表映射到实体的命名策略
        strategy.setTablePrefix(pc.getModuleName() + "_"); //生成实体时去掉表前缀

        strategy.setColumnNaming(NamingStrategy.underline_to_camel);//数据库表字段映射到实体的命名策略
        strategy.setEntityLombokModel(true); // lombok 模型 @Accessors(chain = true) setter链式操作

        strategy.setRestControllerStyle(true); //restful api风格控制器
        strategy.setControllerMappingHyphenStyle(true); //url中驼峰转连字符
        mpg.setStrategy(strategy);

        // 6、执行
        mpg.execute();
    }
}

```