## SpringCloud Alibaba

### 1、能干嘛？

* 服务限流降级:默认支持Servlet、Feign、 RestTemplate、 Dubbo 和RocketMQ限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级Metrics监控。
* 服务注册与发现:适配Spring Cloud服务注册与发现标准,默认集成了Ribbon的支持。
* 分布式配置管理:支持分布式系统中的外部化配置,配置更改时自动刷新。
* 消息驱动能力:基于Spring Cloud Stream为微服务应用构建消息驱动能力。
* 阿里云对象存储:阿里云提供的海量、安全、低成本、舸靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
* 分布式任务调度:提供秒级、精准、舸靠、可用的定时(基于Cron表达式)任务调度服务。同时提供分布式的任务执行模型,如网格任务。网
* 格任务支持海量子任务均匀分配到所有Worker (schedulerx-client). 上执行。

### 2、 去哪下？

* https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md

### 3、怎么玩？

* Sentinel
  * 阿里巴巴开源产品，把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。
* Nacos
  * 阿里巴巴开源产品，一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。
* RocketMQ
  * Apache RocketMQM基于Java的高性能、高吞吐量的分布式消息和流计算平台。
* Dubbo
  * Apache DubboM是-款高性能Java RPC框架。
* Seata
  * 阿里巴巴开源产品，-个易于使用的高性能微服务分布式事务解决方案.
* Alibaba Cloud OSS
  * 阿里云对象存储服务(Object Storage Service,简称OSS)， 是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。
* Alibaba Cloud SchedulerX
  * 阿里中间件团队开发的一款分布式任务调度产品，支持周期性的任务与固定时间点触发任务.

### 4、Nacos

#### （1）是什么？

* Nacos=Eureka+Config+Bus

#### （2）能干嘛？

* 替代Eureka做服务注册中心
* 替代Config做服务配置中心

#### （3）Nacos安装运行

* 下载地址：https://github.com/alibaba/nacos/releases/tag/1.1.4
* 下载解压后直接进入bin目录运行start.cmd

#### （3）Nacos作为服务注册中心演示

##### 微服务提供者

* pom

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

* yml

```properties
server:
  port: 9002

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

* 主启动类

```java
package com.rw.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain9002 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain9002.class,args);
    }
}
```

##### 微服务消费者

* pom

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

* yml

```properties
server:
  port: 83

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


service-url:
  nacos-user-service: http://nacos-payment-provider
```

* config

```java
package com.rw.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;


@Configuration
public class ApplicationContextConfig
{
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
```

* controller

```java
package com.rw.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderNacosController {
    @Resource
    private RestTemplate restTemplate;

    @Value("${service-url.nacos-user-service}")
    private  String serverURL;
    @GetMapping("/consumer/payment/nacos/{id}")
    public String PaymentInfo(@PathVariable("id") Long id){
        return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
    }
}
```



##### Nacos与其他注册中心相比较

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba.png?Expires=1595326956&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=VfNl%2BHTeXTQs25SFvlhIvq%2BDdP8%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba.png?Expires=1595326956&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=VfNl%2BHTeXTQs25SFvlhIvq%2BDdP8%3D)

* C是所有节点在同一时间看到的数据是一致的; 而A的定义是所有的请求都会收到响应。
* 何时选择使用何种模式?
  * 一般来说,
  * 如果不需要存储服务级别的信息且服务实例是通过nacos- client注册，并能够保持心跳上报,那么就可以选择AP模式。当前主流的服务如Spring cloud和Dubbo服务,都适用于AP模式，AP模式为了服务的可能性而减弱了一致性， 因此AP模式下只支持注册临时实例。
  * 如果需要在服务级别编辑或者存储配置信息，那么CP是必须，K8S服务和DNS服务则适用于CP模式。CP模式下则支持注册持久化实例，此时则是以Raft 协议为集群运行模式，该模式下注册实例之前必须先注册服务,如果服务不存在,则会返回错误。
  * curl -X PUT '$NACOS_ SERVER:8848/nacos/v1/ns/operator/switches?entry= serverMode&value=CP'

#### （3）Nacos作为服务配置中心演示

* pom

```xml
  <!--nacos-config-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

* bootstrop.yml

```properties
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #服务注册中心地址
      config:
        server-addr: localhost:8848 #配置中心地址
        file-extension: yaml #指定yaml格式的配置
```

* application.yml

```properties
spring:
  profiles:
    active: dev
```

* 主启动类

```java
package com.rw.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
@EnableDiscoveryClient
@SpringBootApplication
public class NacosConfigClientMain3377
{
    public static void main(String[] args) {
        SpringApplication.run(NacosConfigClientMain3377.class, args);
    }
}
```

* controller

```java
package com.rw.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
@RefreshScope //支持Nacos的动态刷新功能
public class ControllerClientController {
    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/config/info")
    public String getConfigInfo() {
        return configInfo;
    }
}
```

* nacos自带动态刷新功能
* Nacos作为配置中心-分类配置
  * yml中加group 实现分组
  * yml中加namespace实现命名空间切换

#### （3）Nacos集群和持久化配置（重要）

* 默认Nacos使用嵌入式数据库实现数据的存储。所以,如果启动多个默认配置下的Nacos节点,数据存储是存在- 致性问题的。为了解决这个问题，Nacos采用了集中式存储的方式来支持集群化部署,目前只支持MySQL的存储。
* Nacos默认自带的是嵌入式数据库derby，但是如果搭建集群的话每个nacos都带有一个自己的数据库保证不了数据的一致性，所以需要统一的配置mysql数据库来做数据统一

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba1.png?Expires=1595326993&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=ih8gPwhIdDIb0tUaI%2F8tTQ1tzZI%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba1.png?Expires=1595326993&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=ih8gPwhIdDIb0tUaI%2F8tTQ1tzZI%3D)
### 5、Sentinel

* 单独一个组件，可以独立出来
* 支持节目安华的细粒度统一配置

#### （1）Sentinel安装

* 下载地址：https://github.com/alibaba/Sentinel/releases
* java -jar 运行就可

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba2.png?Expires=1595327066&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=0AkI9eWy%2BSErsyVpYp5OIeDn6CA%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba2.png?Expires=1595327066&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=0AkI9eWy%2BSErsyVpYp5OIeDn6CA%3D)

#### （2）Sentinel核心

* Sentinel分为两个部分:
  * 核心库(Java客户端)不依赖任何框架/库，能够运行于所有Java运行时环境，同时对Dubbo /Spring Cloud等框架也有较好的支持。
  * 控制台(Dashboard) 基于Spring Boot开发,打包后可以直接运行，不需要额外的Tomcat等应用容器。

#### （3）Sentinel安装

#### （4）Sentinel使用

* pom

```xml
<dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-datasource-nacos</artifactId>
    </dependency>

    <dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>

    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
```

* yml

```properties
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
        dashboard: localhost:8080
        port: 8719  #默认8719，假如被占用了会自动从8719开始依次+1扫描。直至找到未被占用的端口

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

#### （5）Sentinel流控规则

* 资源名:唯一名称，默认请求路径
* 针对来源: Sentinel可以针对调用者进行限流，填写微服务名，默认default (不区分来源)
* 阈值类型/单机阈值:
* QPS (每秒钟的请求数量) :当调用该api的QPS达到阈值的时候，进行限流
  * 线程数:当调用该api的线程数达到阈值的时候，进行限流
* 是否集群:不需要集群
* 流控模式:
  * 直接: api达到限流条件时，直接限流
  * 关联:当关联的资源达到阈值时,就限流自己
  * 链路:只记录指定链路上的流量(指定资源从入口资源进来的流量, 如果达到阈值，就进行限流) [api级别的针对来源]
* 流控效果:
  * 快速失败:直接失败,抛异常
  * Warm Up:根据codeFactor (冷加载因子,默认3)的值,从阈值/codeFactor, 经过预热时长,才达到设置的QPS阈值
  * 排队等待:匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS,否则无效

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba3.png?Expires=1595327100&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=LAdQe11F5nZ98OhJFlBfXBfEIMc%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba3.png?Expires=1595327100&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=LAdQe11F5nZ98OhJFlBfXBfEIMc%3D)

* 直接失败
* 关联：eg：订单处理不过来 挂掉下订单服务
* WarmUp预热
* 刚开始是阈值/3 经过预热时间后达到阈值
  * eg：秒杀系统开启瞬间，回有很多流量上来，很有可能把系统打死，预热方式就是为了保护系统，可慢慢的把流量放进来，慢慢的把阈值增长到设置的阈值
* 排对等待：高并发进来之后排对，挨个等待，只能是QPS

* 自己写兜底方法

#### （6）Sentinel降级规则

* RT (平均响应时间,秒级)
  * 平均响应时间超出阈值且在时间窗口内通过的请求>=5, 两个条件同时满足后触发降级
  * 窗口期过后关闭断路器
  * RT最大4900 (更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才 能生效)
* Sentinel没有半开状态 跳闸之后默认行为抛出：DegradeException
* **默认1s中请求数>5 且请求时间都>设定阈值的话在未来设置的 关闭窗口时间内会跳闸无法访问**
* 异常比例
  * 1s中请求数>5 且 异常比例大于设定的异常比例也会开启
* 异常数
  * 当资源近1分钟的异常数目超过阈值之后会进行熔断

#### （7）Sentinel热点key限流（重要）

* 何为热点?热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的Top K数据，并对其访问进行限制。比如:
  * 商品ID为参数,统计一段时间内最常购买的商品ID并进行限制
  * 用户ID为参数，针对一段时间内频繁访问的用户ID进行限制
* 配置

```java
@GetMapping("/testHotKey")
@SentinelResource(value = "testHotKey",blockHandler = "deal_testHotKey")
public String testHotKey(@RequestParam(value = "p1", required = false) String p1,
                         @RequestParam(value = "p2", required = false) String p2) {
    return "testHotKey";
}
public String deal_testHotKey(String p1, String p2, BlockException exception){
    return "------deal_testHotKey,哭哭";
}
```

* 参数例外项：
  * 我们期望p1参数当它是某个特殊值时，它的限流值和平时不一样（开小灶）
* 如果出现异常的话不会给你兜底，配置类出错会兜底
* 总结
  @SentinelResource主管配置出错，运行出错该走异常走异常

#### （8）Sentinel系统规则

* Sentinel系统自适应限流从整体维度对应用入口流量进行控制，结合应用的Load、CPU 使用率、总体平均RT、入口QPS和并发线程数等几个维度的监控指标,通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

  

#### （9）SentinelResource

* 按 URL地址限流
* 按资源名限流
* 以上会出现问题：
  * 1、系统默认的, 没有体现我们自己的业务要求。
  * 2、依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观。
  * 3、每个业务方法都添加一个兜底的，那代码膨胀加剧。
  * 4、全局统一的处理方法没有体现。

* 客户自定义的限流处理逻辑

  * 自定义限流处理类

  ```java
  package com.rw.springcloud.alibaba.myhandler;
  
  import com.alibaba.csp.sentinel.slots.block.BlockException;
  import com.atguigu.springcloud.entities.*;
  
  public class CustomerBlockHandler {
  
      public static CommonResult handleException(BlockException exception) {
          return new CommonResult(2020, "自定义限流处理信息....CustomerBlockHandler");
  
      }
      public static CommonResult handleException2(BlockException exception) {
          return new CommonResult(2020, "自定义限流处理信息....CustomerBlockHandler2");
  
      }
  }
  ```

  * 修改controller

  ```java
  @GetMapping("/rateLimit/customerBlockHandler")
  @SentinelResource(value = "customerBlockHandler",
          blockHandlerClass = CustomerBlockHandler.class,  // 哪个类
          blockHandler = "handlerException2")  // 类中的哪个方法
  public CommonResult customerBlockHandler()
  {
      return new CommonResult(200,"按客戶自定义",new Payment(2020L,"serial003"));
  }
  ```

#### （10）服务熔断功能

* fallback 异常处理兜底方法：管java异常
* blockHandler：只管sentinel降级
* 若blockHandler和fallback都进行了配置,则被限流降级而抛出BlockException时只会进入blockHandler处理逻辑。
* 可结合rest ribbon
* 可结合 openFeign

#### （11）规则持久化

* 问题：之前是重启后限流规则就没了
* pom

```xml
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

* yml

```properties
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
    sentinel:
      transport:
        dashboard: localhost:8080 #配置Sentinel dashboard地址
        port: 8719
      datasource:
        ds1:
          nacos:
            server-addr: localhost:8848
            dataId: cloudalibaba-sentinel-service
            groupId: DEFAULT_GROUP
            data-type: json
            rule-type: flow

management:
  endpoints:
    web:
      exposure:
        include: '*'

feign:
  sentinel:
    enabled: true # 激活Sentinel对Feign的支持
```

* 添加nacos内容解析
[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba4.png?Expires=1595327144&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=zwS5G%2FNEtQChaoLbNJfnP2WyAHM%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/alibaba4.png?Expires=1595327144&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=zwS5G%2FNEtQChaoLbNJfnP2WyAHM%3D)

### 6、SpringCloud Alibaba Seata处理分布式事务

* 一次业务 操作需要跨多个数据源或需要跨多个系统进行远程调用，就会产生分布式事务问题

#### （1）Seata简介

* 1+3：Transaction ID XID 全局唯一的事务ID
* 三组件概念：
  * Transaction Coordinator (TC-事务协调者)：事务协调器，维护全局事务的运行状态，负责协调并驱动全局事务的提交或回滚
  * Transaction Manager (TM-事务管理器)：控制全局事务的边界，负责开启一一个全局事务，并最终发起全局提交或全局回滚的决议;
  * Resource Manager (RM-资源管理器)：控制分支事务，负责分支注册、状态汇报，并接收事务协调器的指令，驱动分支(本地)事务的提交和回滚

* 处理过程
  * TM向TC申请开启一个全局事務，全局事务创建成功并生成一 个全局唯一 的XID;
  * XID 在微服务调用链路的上下文中传播;
  * RM向TC注册分支事务,将其纳入XID对应全局事务的管辖;
  * TM向TC发起针对XID的全局提交或回滚决议;
  * TC调度XID下管辖的全部分支事务完成提交或回滚请求。

#### （2）Seata-Server安装下载

* https://github.com/seata/seata/releases
* 需要配置信息注册到nacos还有本地数据库链接

#### （3）怎么玩？

* 非常简单
* 本地@Transactional
* 全局@GlobalTransactional