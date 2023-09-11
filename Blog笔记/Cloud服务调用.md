## 服务调用

###  一、Ribbon

#### 1、概述

* Spring Cloud Ribbon是基于Netflix Ribboh实现的一套客户端负载均衡的工具。
* 简单的说，Ribbon是Netflix发布的开源项目， 主要功能是提供客户端的软件负载均衡算法和服务调用。Ribbon客户端组件提供- 系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer (简称LB)后面所有的机器，Ribbon会自动的帮助你基于某种规则(如简单轮询,随机连接等)去连接这些机器。我们很容易使用Ribbon实现自定义的负载均衡算法。
* 目前已经进入部署，但是目前主流还是ribbon
* LB负载均衡(Load Balance)是什么
  * 简单的说就是将用户的请求平摊的分配到多个服务上,从而达到系统的HA (高可用)。
  * 常见的负载均衡有软件Nginx, LVS, 硬件F5等。
* Ribbon本地负载均衡客户端VS Nginx服务端负载均衡区别
  * Nginx是服务器负载均衡，客F端所有请求都会交给nginx,然后由nginx实现转发请求。即负载均衡是由服务端实现的。
  * Ribbon本地负载均衡，在调用微服务接口时候,会在注册中心上获取注册信息服务列表之后缓存到JVM本地,从而在本地实现RPC远程服务调用技术。
* **LB（负载均衡）**
  * 集中式LB
    * 即在服务的消费方和提供方之间使用独立的LB设施可以是硬件,如F5, 也可以是软件,如nginx), 由该设施负责把访问请求通过某种策略转发至服务的提供方;
  * 进程内LB
    * 将LB逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用,然后自己再从这些地址中选择出一个合适的服务器。Ribbon就属于进程内LB,它只是-个库，集成于消费方进程，消费方通过它来获取到服务提供方的地址。
* Ribbon在工作时分成两步
  * 第一步先选择EurekaServer ,它优先选择在同-个区域内负载较少的server.
  * 第二步再根据用户指定的策略，在从server取到的服务注册列表中选择一个地址。
  * 其中Ribbon提供了多种策略:比如轮询、随机和根据响应时间加权。

#### 2、负载均衡演示

* pom
```xml
<dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
 </dependency>
<!--spring-cloud-starter-netflix-eureka-client就包含了ribbon-->
```
* getForObject方法/getForEntity方法

[![](https://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/%E6%9C%8D%E5%8A%A1%E8%B0%83%E7%94%A8.png?Expires=1595058846&OSSAccessKeyId=TMP.3KikgrMgKshXzBFc6kWth5vPd2WQR7YwsvisQxsoqn7HcwwLWH4tPVBaBRAKeL2tj7R8LEhu1Cg9BBthsJnfUuvKG3j1jM&Signature=KDPuIm%2BlygcOVSmL3a1zdKMj4Ik%3D)](https://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/%E6%9C%8D%E5%8A%A1%E8%B0%83%E7%94%A8.png?Expires=1595058846&OSSAccessKeyId=TMP.3KikgrMgKshXzBFc6kWth5vPd2WQR7YwsvisQxsoqn7HcwwLWH4tPVBaBRAKeL2tj7R8LEhu1Cg9BBthsJnfUuvKG3j1jM&Signature=KDPuIm%2BlygcOVSmL3a1zdKMj4Ik%3D)

* postForObject/postForEntity方法

[![](https://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/%E6%9C%8D%E5%8A%A1%E8%B0%83%E7%94%A82.png?Expires=1595058863&OSSAccessKeyId=TMP.3KikgrMgKshXzBFc6kWth5vPd2WQR7YwsvisQxsoqn7HcwwLWH4tPVBaBRAKeL2tj7R8LEhu1Cg9BBthsJnfUuvKG3j1jM&Signature=KoQ8NCJsjK7NxoaG7AO2zm8GSuk%3D)](https://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/%E6%9C%8D%E5%8A%A1%E8%B0%83%E7%94%A82.png?Expires=1595058863&OSSAccessKeyId=TMP.3KikgrMgKshXzBFc6kWth5vPd2WQR7YwsvisQxsoqn7HcwwLWH4tPVBaBRAKeL2tj7R8LEhu1Cg9BBthsJnfUuvKG3j1jM&Signature=KoQ8NCJsjK7NxoaG7AO2zm8GSuk%3D)


#### 3、核心组件IRule

* 根据特定算法从服务列表中选取一个要访问的服务



| 实现                                    | 描述                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| com.netflix.loadbalancer.RoundRobinRule | 轮询                                                         |
| com.netflix.loadbalancer.RandomRule     | 随机                                                         |
| com.netflix.loadbalancer.RetryRule      | 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试 |
| WeightedResponse TimeRule               | 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择 |
| BestAvailableRule                       | 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择-个并发量最小的服务 |
| AvailabilityFilteringRule               | 先过滤掉故障实例，再选择并发较小的实例                       |
| ZoneAvoidanceRule                       | 默认规则，复合判断server所在区域的性能和server的可用性选择服务器 |

* 替换轮询算法：编写配置类
  * 方文档明确给出了警告:
  * 这个自定，义配置类不能放在@ComponentScan所扫描的当前包下以及子包下，I
  * 否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享,达不到特殊化定制的目的了。

* 配置类写法：

```java
package com.rw.myruler;

import com.netflix.loadbalancer.IRule;
import com.netflix.loadbalancer.RandomRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MySelfRuler {
    @Bean
    public IRule myRule() {
        return new RandomRule(); //定义为随机
    }
}

```

* 启动类读取配置文件

```java
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration = MySelfRuler.class)
```

#### 4、负载均衡算法

* rest接口第几次请求数%服务器集群总数量=实际调用服务器位置下标，没此服务重启后rest接口计数从1开始

[![](https://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png?Expires=1595058925&OSSAccessKeyId=TMP.3KikgrMgKshXzBFc6kWth5vPd2WQR7YwsvisQxsoqn7HcwwLWH4tPVBaBRAKeL2tj7R8LEhu1Cg9BBthsJnfUuvKG3j1jM&Signature=PdYzje%2B2LpXACf2f2am6rFv2BO4%3D)](https://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png?Expires=1595058925&OSSAccessKeyId=TMP.3KikgrMgKshXzBFc6kWth5vPd2WQR7YwsvisQxsoqn7HcwwLWH4tPVBaBRAKeL2tj7R8LEhu1Cg9BBthsJnfUuvKG3j1jM&Signature=PdYzje%2B2LpXACf2f2am6rFv2BO4%3D)

* 手写轮询算法：JUC（CAS+自旋锁）

### 二、OpenFegin

### 1、fegin

#### (1)是什么？

* Feign是一个声明式WebService客户端。 使用Feign能让编写Web Service客户端更加简单。
* 它的使用方法是定义一个服务接口然后在上面添注解。Feign也支 持可拔插式的编码器和解码器。Spring Cloud对Feign进行了封装使其支持了Spring MVC标准注解和HttpMessageConverters。Feign可以与Eureka和Ribbon组合使用以支 负载均衡

#### （2）能干嘛？

* Feign能干什么
  * **Feign旨在使编写Java Http客户端变得更容易。**
  * 前面在使用Ribbon+ RestTemplate时,利用RestTemplate对http请求的封装处理，形成了-套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一-处，往往-一个接会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装， 由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下， 我们只需创建一 个接口并使用注解的方式来配置它(以前是Dao接口上面标注Mapper注解，现在是一 个微服务接口上面标注一 个Feign注解即可)，即阿完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时，自动封装服务调用客户端的开发量。
  * **Feign集成了Ribbon**
    利用Ribbon维护了Payment的服务列表信息，且通过轮询实现了客户端的负载均衡。而与Ribbon不同的是,通过feign只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用
* fegin和openfegin的区别

| Fegin                                                        | OpenFegin                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Feign是Spring Cloud组件中的一个轻量级RESTful的HTTP服务客户端Feign内置了Ribbon,用来做客户端负载均衡，去调用服务注册中心的服务。Feign的使用方式是:使用Feign的注解定义接口，调用这个接口就可以调用服务注册中心的服务 | OpenFeign是Spring Cloud在Feign的基础上支持了SpringMVC的注解，如@RequesMapping等等。OpenFeign的@FeignClient可以解析SpringMVC的@RequestMapping注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。 |
| <dependency><br/><groupId>org . springframework. cloud</ groupId><br/><artifactId>spring-cloud-starter-feign</artifactId><br/></dependency> | <dependency><br/><groupId>org. springframework. cloud</ groupId><br/><artifactId>spring-C loud-starter-openfeign</artifactId><br/></ dependency> |

### 2、OpenFegin使用步骤

使用openFegin实现面向接口编程

#### （1）接口+注解

* 微服务调用接口+@FeignClient

#### （2）POM

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-openfeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
    <version>2.2.1.RELEASE</version>
</dependency>
```



#### （3）YML

```properties
server:
  port: 80
eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka, http://eureka7002.com:7002/eureka
 

```



#### （4）主启动类

* ```java
  package com.rw.springcloud;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
  import org.springframework.cloud.openfeign.EnableFeignClients;
  import springfox.documentation.swagger2.annotations.EnableSwagger2;
  
  @SpringBootApplication
  @EnableFeignClients(basePackages = "com.rw.springcloud.service")
  @EnableEurekaClient
  @EnableSwagger2
  public class OrderFeignMain80 {
      public static void main(String[] args) {
          SpringApplication.run(OrderFeignMain80.class,args);
      }
  }
  
  ```

#### （5）业务类

- 业务逻辑接口+@FeginClient配置调用provider服务
- 新建PaymentFeginService接口并新增注解@FeginClient

```java
package com.rw.springcloud.service;

import com.rw.springcloud.utils.R;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
// 大坑！！ 
@FeignClient(name = "CLOUD-PAYMENT-SERVICE")
public interface PaymentFeignService {
    //必须使用@RequestMapping,不能使用@GetMapping
    //@PathVariable 必须指定参数名称
    @RequestMapping(method = RequestMethod.GET,value = "/springcloud/payment/{id}")
    public R save(@PathVariable("id") long id);
}

```

* 控制层Controller

```java
package com.rw.springcloud.controller;
import com.rw.springcloud.service.PaymentFeignService;
import com.rw.springcloud.utils.R;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
/**
 * <p>
 *  前端控制器
 * </p>
 *
 * @author Ruiwen
 * @since 2020-07-10
 */
@RestController
public class PaymentController {
    @Autowired
    private PaymentFeignService paymentFeignService;
    @GetMapping("/consumber/payment/get/{id}")
    public R save(@PathVariable("id") long id){
        return paymentFeignService.save(id);
    }

}

```



#### （6）Feign自带负载均衡配置项

###  3、OpenFegin超时控制

#### （1）OpenFegin默认等待一秒，超过就报超时错误

* 超时配置

```properties
#设置feign客户端超时时间（opengeign默认支持ribbon）
ribbon:
  #指的是建立连接所用的时间，是用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout:  5000  # 5s
  # 指的是建立连接后从服务启读取到可用资源所用的时间
  ConnectTimeout: 5000
```

### 4、OpenFegin日志打印功能

#### (1)日志级别

* NONE:默认的，不显示任何日志;
* BASIC:仅记录请求方法、URL、 响应状态码及执行时间;
* HEAD[ERS:除了BASIC中定义的信息之外,还有请求和响应的头信息;
* FULL:除了IHEADERS中定义的信息之外，还有请求和响应的正文及元数据。

#### （2）配置类

```java
package com.rw.springcloud.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
 

```

####  (3)yml中开启日志的fegin客户端



```properties
logging:
  level:
    com.atguigu.springcloud.service.PaymentFeignService: debug
```