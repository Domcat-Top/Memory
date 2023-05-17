## 一、Spring Cloud Stream消息驱动

### 1、为什么引入cloud Stream

* MQ（消息中间件）
  * ActiveMQ
  * RabbitMQ
  * RocketMQ
  * Kafka

* 问题：可能中间java开发用的RabbitMQ而数据分析用的Kafka
  * 存在切换、维护、开发等问题
* 针对上述问题有没有一种新技术诞生，让我们不再关注具体MQ的细节，我们只需要一种适配绑定的方式，自动给我们在各种MQ内切换
* Cloud Stream应运而生，只需要操作Stream**屏蔽底层消息中间件的差异，降低切换成本，统一消息的编程模型**

### 2、什么是SpringCloud Stream

* 官方定义Spring Cloud Stream是一个构建消息驱动微服务的框架。
* 应用程序通过inputs或者outputs与Spring Cloud Stream中binder对象交互。通过我们配置来binding(绑定)，而Spring Cloud Stream的binder对象负责与消息中间件交互。所以，我们只需要搞清楚如何与Spring Cloud Stream交互就可以防便使用消息驱动的方式。
* 通过使用Spring Integration来连接消息代理中间件以实现消息事件驱动。Spring Cloud Stream为- -些供应商的消息中间件产品提供了个性化的自动化配置实现，引用了发布-订阅、消费组、分区的三个核心概念。
* 目前仅支持RabbitMQ、Kafka.

### 3、Spring Cloud Stream标准流程套路

* Binder：很方便的链接中间件，屏蔽差异
* Channel：通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过Channel对队列进行配置
* Source和Sink：简单的可理解为参照对象是SpringCloud Stream自身，从Stream发布消息就是输出，接收消息就是输入。



| 组成            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Middleware      | 中间件，目前只支持RabbitMQ和Kafka                            |
| Binder          | Binder是应用与消息中间件之间的封装，目前实行了Kafka和RabbitMQ的Binder,通过<br/>Binder可以很方便的连接中间件，可以动态的改变消息类型(对应于Kafka的topic,<br/>RabbitMQ的exchange),这些都可以通过配置文件来实现 |
| @Input          | 注解标识输入通道，通过该输入通道接收到的消息进入应用程序     |
| @Output         | 注解标识输出通道，发布的消息将通过该通道离开应用程序         |
| @StreamListener | 监听队列，用于消费者的队列的消息接收                         |
| @EnableBinding  | 指信道channel和exchange绑定在-起                             |

### 4、案例

#### （1）消息驱动之生产者

* pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
```

* yml

```properties
server:
  port: 8801

spring:
  application:
    name: cloud-stream-provider
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: guest
                password: guest
      bindings: # 服务的整合处理
        output: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
          binder: defaultRabbit  # 设置要绑定的消息服务的具体设置

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: send-8801.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

* service

```java
package com.rw.springcloud.service.impl;

import com.rw.springcloud.service.IMessageProvider;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.messaging.Source;
import org.springframework.integration.support.MessageBuilder;
import org.springframework.messaging.MessageChannel;

import javax.annotation.Resource;
import java.util.UUID;

@EnableBinding(Source.class) //定义消息的同送管道
public class MessageProviderImpl implements IMessageProvider {
    @Resource
    private MessageChannel output; //消息发送管道
    @Override
    public String send() {
        String serial= UUID.randomUUID().toString();
        output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("*******serial:"+serial);
        return serial;
    }
}
```

* controller

```java
package com.rw.springcloud.controller;

import com.rw.springcloud.service.IMessageProvider;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class SendMessageController {
    @Resource
    private IMessageProvider messageProvider;
    @GetMapping("/sendMessage")
    public String sendMessage(){
        return  messageProvider.send();
    }
}
```

#### （2）消息驱动之消费者

* pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
```

* yml

```properties
server:
  port: 8802

spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
          environment: # 设置rabbitmq的相关的环境配置
            spring:
              rabbitmq:
                host: localhost
                port: 5672
                username: guest
                password: guest
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
          binder: defaultRabbit  # 设置要绑定的消息服务的具体设置

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: receive-8802.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

* controller

```java
package com.rw.springcloud.controller;


import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.annotation.StreamListener;
import org.springframework.cloud.stream.messaging.Sink;
import org.springframework.messaging.Message;
import org.springframework.stereotype.Component;


@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController {
    @Value("${server.port}")
    private String serverPort;
    @StreamListener(Sink.INPUT)
    private void  input(Message<String> message){
        System.out.println("消费者1号----->接收消息"+message.getPayload()+"\t port:"+serverPort);
    }
}
```

### 5、分组消费与持久化

#### （1）消费

* 问题：比如在如下场景中，订单系统我们做集群部署,都会从RabbitMQ中获取订单信息,那如果一个订单同时被两个服务获取到，那么就会造成数据错误,我们得避免这种情况。这时我们就可以使用Stream中的消息分组来解决
* 注意在Stream中处于同一个group中的多个消费者是竞争关系,就能够保证消息只会被其中一个应用消费- -次。
* **不同组是可以全面消费的(重复消费),**
* **同一组内会发生竞争关系，只有其中一个可以消费。**
* 故障原因：重复消费
* 导致原因：默认分组group是不同的，组流水号不一样，被认为不同组，可以消费
* 解决：自定义分组
  * 原理：微服务应用放置于同- -个group中，就能够保证消息只会被其中一一个应用消费一次。不同的组是可以消费的，同一一个组内会发生竞争关系，只有其中一个可以消费。
  * yml binder下边对齐添加：group: rwA
  * 把相同的集群服务都设置到一个组，就会产生竞争关系，轮询获取

#### （2）持久化

* 添加group 的如果服务宕机之后 重启不会丢失消息 会重新读取并消费 nice

------



## 二、SpringCloud Sleuth 分布式请求链路跟踪

### 1、概述

#### （1）为什么出现？

* 在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的的服务节点调用来协同产生最后的请求结果,每个前段请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会引|起整个请求最后的失败。

#### （2）是什么？

* Spring Cloud Sleuth提供了一套完整的跟踪的解决方案
* 在分布式系统中提供追踪解决方案并且兼容支持了zipkin

### 2、搭建链路监控步骤

#### （1）zipkin

* 下载
  * SpringCloud从F版起已不需要自己构建ZipkinServer了，只需调用jar包即可
  * https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/
* 运行jar
  * java -jar 
  * http://localhost:9411/zipkin/
* 运行控制台
* 名词解释
  * Trace:类似于树结构的Span集合，表示一条调用链路，存在唯一标识
  * span:表示调用链路来源，通俗的理解span就是一次请求信息

#### （2）服务提供者

* pom

```xml
<!--包含了sleuth+zipkin-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

* yml

```properties
server:
  port: 80

spring:
  application:
    name: cloud-order-service
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      probability: 1
eureka:
  client:
    #表示是否将自己注册斤EurekaServer默认为true
    register-with-eureka: true
    # 是否从EurekaServer抓取以有的注册信息，默认为true。系欸但无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://localhost:7001/eureka
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
  instance:
    instance-id: order80
    prefer-ip-address: true
```

#### （3）服务消费者（调用方）

#### （4）一次启动eureka7001/8001/80

#### （5）打开浏览器访问：http://localhost:9411

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/stream.png?Expires=1595326760&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=F88VGG3dI%2Fk6SxOI8i5dfoo%2BZZk%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/stream.png?Expires=1595326760&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=F88VGG3dI%2Fk6SxOI8i5dfoo%2BZZk%3D)





