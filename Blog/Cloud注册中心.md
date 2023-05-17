## 服务注册中心

### 一、RestTemplate实现服务调用（并非服务注册中心）

* RestTemplate配置类

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

}
```

* 方法：

```java
 public  static  final  String PAYMENT_URL="http://localhost:8001";
 restTemplate.postForObject(PAYMENT_URL+"/springcloud/payment/save",payment,R.class);
 restTemplate.getForObject(PAYMENT_URL+"/springcloud/payment/"+id, R.class);
```

### 二、Eureka

#### 1、基础知识

* 什么是服务治理.
  * Spring Cloud封装了Netflix 公司开发的Eureka模块来实现服务治理在传统的rpc远程调用框架中，管理每 个服务与服务之间依赖关系比较复杂,管理比较复杂,所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等,实现服务发现与注册。
* 什么是服务注册与发现
  * Eureka采用了CS的设计架构，Eureka Server作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用Eureka的客户端连接Eureka Server并维持心跳连接。这样系统的维护人员就可以通过Eureka Server来监控系统中各个微服务是否正常运行。
  * 在服务注册与发现中，有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息比如服务地址通讯地址等以别名方式注册到注册中心上。另一方(消费者|服务提供者) ,以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地RPC调用RPC远程调用框架核心设计思想:在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)。在任何rpc远程框架中,都会有一个注册中心(存放服务 地址相关信息(接口地址))
* Eureka包含两个组件: Eureka Server和Eureka Client
  * **Eureka Server**提供服务注册服务：各个微服务节点通过配置启动后,会在EurekaServer中进行注册， 这样EurekaServer中的服务注册表中将会存储所有 可用服务节点的信息，服务节点的信息可以在界面中直观看到。
  * **EurekaClient**通过注册中心进行访问：是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、 使用轮询(round-robin)负载算法的负载均衡器。在应用启动后,将会向Eureka Server发送心跳(默认周期为30秒)。 如果Eureka Server在多个心跳周期内没有接收到某个节点的心
    跳，EurekaServer将 会从服务注册表中把这个服务节点移除(默认90秒)。
* 调用流程
  * 1、先启动eureka注册中心
  * 2、启动服务提供者paymen支付服务
  * 3、支付服务启动后会把自身信息(比如服务地址以别名方式注册进eureka)
  * 4、消费者order服务在需要调用接口时，使用服务别名去注册中心获取实际的RPC远程调用地址
  * 5、消费者获得调用地址后，底层实际是利用H ttpClien技术,实现远程调用
  * 6、消费者获得服务地址后会缓存在本地jvm内存中，默认每间隔30秒更新一次服务调用地址

#### 2、单机Eureka构建步骤

* 导入pom文件

```xml
    <dependencies>
        <!--服务端-->
        <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!--注册端-->
         <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-eureka-client -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--依赖的pom-->
         <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
    </dependencies>
```

* 配置yml文件

```properties
server:
  port: 7001

eureka:
  instance:
   hostname: localhost  #eureka服务端的实例名字
  client:
    register-with-eureka: false    #表识不向注册中心注册自己
    fetch-registry: false   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/    #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址

```

* 启动类

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer //表示eureka自身服务  @EnableEurekaClient表示注册用户
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class,args);
    }
}
```

* **<b style="color:red">坑：记得一定药把yml的层次分明还有连接前后不要有空格</b>**

#### 3、 Eureka集群构建

* 问题：微服务RPC远程服务调用最核心的是什么？

  * 高可用，试想如果你的注册中心只有一个only one，他出故障了那就呵呵(￣▽￣)"了，会导致整个服务环境不可用，所以核心是高可用
  * 解决办法：搭建Eureka注册中心集群，实现负载均衡+故障容错

* 原理：互相注册，相互守望

* 集群环境搭建

  * 单机玩法：修改映射配置文件

    * 找到C:\Windows\System32\drivers\etc路径下的hosts文件

    * 添加映射配置：127.0.0.1 eureka7001.com

      						   127.0.0.1 eureka7002.com

  * 集群server的yml文件配置

  ```properties
  server:
    port: 7001
  
  eureka:
    instance:
     hostname: eureka7001.com  #eureka服务端的实例名字
    client:
      register-with-eureka: false    #表识不向注册中心注册自己
      fetch-registry: false   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
      service-url:
        defaultZone: http://eureka7002.com:7002/eureka/    #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
  	#7001和7002互相注册
  ```

  * 集群client的yml文件配置


  ```properties
  server:
    port: 80
  
  spring:
    application:
      name: cloud-order-service
  
  eureka:
    client:
      #表示是否将自己注册斤EurekaServer默认为true
      register-with-eureka: true
      # 是否从EurekaServer抓取以有的注册信息，默认为true。系欸但无所谓，集群必须设置为true才能配合ribbon使用负载均衡
      fetchRegistry: true
      service-url:
        #defaultZone: http://localhost:7001/eureka
        defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
       #分别把80服务注册进两个eureka服务中
  
  
  ```

  * 配置好之后把原先方法里边写死的端口改成Eureka服务名称

  ```java
  public  static  final  String PAYMENT_URL="http://CLOUD-PAYMENT-SERVICE";
  ```

  * 配置类也要修改加上：@LoadBalanced注解意味这开启负载均衡功能
    * 负载均衡：主要采用的轮询算法

* 检测client是否启动：http://localhost:8001/actuator/health

* 完善信息

```properties
  instance:
    instance-id: payment8002 #status的对应名字
    prefer-ip-address: true  #显示服务对应ip地址
```

#### 4、服务发现DIscovery

* 对于注册进eureka里边的微服务，可以通过服务发现获得该服务的信息

* 修改Controller层

* 启动类上加上：@EnableDiscoveryClient注解

  ```java
     @GetMapping(value = "/payment/discovery")
      public Object discovery(){
          //获取服务清单列表
          List<String> services = discoveryClient.getServices();
          for (String service : services) {
              log.info("********element"+service);
          }
          List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");//获取服务名下的具体服务
          for (ServiceInstance instance : instances) {
              log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
          }
          return this.discoveryClient;
      }
  ```

* 输出结果：

```java
2020-07-12 09:07:21.320  INFO 956 --- [nio-8001-exec-2] c.r.s.controller.PaymentController       : CLOUD-PAYMENT-SERVICE	192.168.159.1	8002	http://192.168.159.1:8002
2020-07-12 09:07:21.320  INFO 956 --- [nio-8001-exec-2] c.r.s.controller.PaymentController       : CLOUD-PAYMENT-SERVICE	192.168.159.1	8001	http://192.168.159.1:8001
```

#### 5、Eureka的自我保护

* 概述：保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。一旦进入保护模式,Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。

* 导致原因：

  * 某时刻某一个微服务不可用了，Eureka不会立刻清理，依旧会对该微服务的信息进行保存
  * 在自我保护模式中，Eureka Server会保护服务注册表中的信息，不再注销任何服务实例。（好死不如赖活着）
  * 属于CAP里边的AP分支

* 怎么禁止自我保护

  * enable-self- preservation:ture 默认是开启的 

  * evi ction- interval-timer- in-ms: 2000 缓存时间是2s

  * 客户端：

   * Eureka客户端向服务端发送心跳的时间间隔，单位为秒( 默认是30秒):lease-renewal- interval- in-seconds: 1
   * Eureka服:务端在收到最后一次心跳后等待时间上限，单位为秒( 默认是90秒)超时将剔除服务:lease-expiration-duration-in-seconds: 2

      如果注册服务挂掉在2s内没有再次向服务端发送心跳服务端将会立马清理掉该服务

### 三、Zookeeper

#### 1、注册中心Zookeeper

以zookeeper-3.4.9为例

* zookeeper是一个分布式协调工具，可以实现注册中心
  * 准备：关闭Linux服务器防火墙（systemctl stop firewalld），启动zookeeper
  * zookeeper服务器取代Eureka服务器，zk作为服务注册中心

#### 2、构建服务

* 引入依赖

```xml
 <dependencies>

        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


    </dependencies>

```

* 如果jar版本冲突替换

```xml
 <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-zookeeper-discovery -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
            <!--排除zk3.5.3-->
            <exclusions>
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
            <!--添加zk 3.4,9版本-->
        <!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.9</version>
        </dependency>

```



* 配置yml

```properties
server:
  port: 8004 # 服务端口

spring:
  application:
    name: cloud-provider-payment  #服务名
  cloud:
    zookeeper:
      connect-string: 192.168.136.140:2181 #连接zookeeper的ip+端口

```

* 启动类 加注解：@EnableDiscoveryClient
  * 该注解用于向使用consul或者zookeeper作为注册中心时注册服务
* controller测试

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

@RestController
@Slf4j
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/payment/zk")
    public String paymentzk(){
        return "springcloud with zookeeper:"+serverPort+"\t"+ UUID.randomUUID().toString();
    }

}
 
 

```

#### 3、思考对比

* zookeeper的注册进来的节点是持久的还是临时的？
  * 临时的，比eureka冷酷一点

### 四、Consul

### 1、简介

* Consul是一开源的分布式服务发现和配置管理系统，由HashiCorp公司用Go语言开发。
* 提供了微服务系统中的服务治理、I配置中心、控制总线等功能。这些功能中的每一个 都可以根据需要单独使用,也可以一-起使用以构建全方位的服务网格,总之Consul提供了- -种完整的服务网格解决方案。
* 它具有很多优点。包括:紆raft 协议，比较简洁;支持健康检查,同时支持HTTP和DNS协议支持跨数据中心的WAN集群提供图形界面跨平台,支持Linux、Mac、 Windows

| 功能       | 介绍                                             |
| ---------- | ------------------------------------------------ |
| 服务发现   | 提供HTTP和DNS两种发现方式                        |
| 健康检测   | 支持多种协议，HTTP、TCP、Docker、Shell脚本定制化 |
| KV存储     | Key、Value的存储方式                             |
| 多数据中心 | Consul支持多数据中心                             |

* 可视化web界面

#### 2、安装

* 官网安装说明 https://www.consul.io/downloads.html

### 3、启动

* consul agent -dev

#### 4、构建服务

* pom

```xml
        <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-consul-discovery -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
```

* yml

```xml
server:
  port: 80


spring:
  application:
    name: consul-consumer-order
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
```

* 启动类注解

  * ```java
    @EnableEurekaClient
    ```

### 总结:

#### 对比Eureka、Zookeeper、Consul

| 组件名    | 语言 | 服务健康检测 | 对外暴露接口 | SpringCloud集成 |
| --------- | ---- | ------------ | ------------ | --------------- |
| Eureka    | Java | AP           | HTTP         | 已集成          |
| Consul    | Go   | CP           | HTTP/DNS     | 已集成          |
| Zookeeper | Java | CP           | 客户端       | 已集成          |

* C:Consistency（强一致性）一致性
* A：Availability（可用性） 高可用
* P:Partition tolerance(分区容错性) 容错性