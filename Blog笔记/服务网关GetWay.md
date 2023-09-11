## 服务网关

### Getway新一代网关

#### 1、概述简介

##### （1）简介

* SpringCloud Gateway是Spring Cloud的一个全新项目，纡Spring 5.0+ Spring Boot 2.0和Project Reactor等技术开发的网关,它旨在为微服务架构提供一种简单有效的统- 的API路由管理方式。
* SpringCloud Gateway作为Spring Cloud生态系统中的网关,目标是替代Zuul,在Spring Cloud 2.0以上版本中，没有对新版本的Zuul 2.0以上最新高性能版本进行集成，仍然还是使用的Zuul 1.x非Reactor模式的老版本。而为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty。
* Spring Cloud Gateway的目标提供统- -的路由方式且基于 Filter 链的方式提供了网关基本的功能，例如:安全，监控/指标, 和限流。
* 一句话：springCloud Geteway使用的Webflux中的reactor-netty响应式变成组建，底层使用了Netty通讯框架。

##### （2）能干嘛

* 反向代理
* 鉴权
* 流量控制
* 熔断
* 日志监控等

##### （3）用在哪里?

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/gateway.png?Expires=1595326199&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=B%2FUDdPiObb26VQrNAW%2FB98TlZhw%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/gateway.png?Expires=1595326199&OSSAccessKeyId=TMP.3Kgc87sv3Aj38F3p3rNzt5MaUP6q7gHEe12X9o69ZPu2KDHiiWAZC4QdjkharV5H3J3Hx5mpFhkUwvXyCDj6SX7UmmhKfQ&Signature=B%2FUDdPiObb26VQrNAW%2FB98TlZhw%3D)
#### 2、三大核心概念

##### （1）Route 路由

* 构建网关的基本模块，它由ID,目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

##### （2）Predicate 断言

* 参考的是Java8的java.util.function.Predicate 开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数)，如果请求与断言相匹配则进行路由

##### （3）Filter 过滤

* 指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

##### （4）总体

* web请求，通过一些匹配条件，定位到真正的服务节点。并在这个转发过程的前后，进行一些精细化控制。
* predicate就是我们的匹配条件;
* 而filter,就可以理解为一个无所不能的拦截器。有了这两个元素,再加上目标uri,就可以实现一个具体的路由了

#### 3、Getway工作流程

* 客户端向Spring Cloud Gatqway发出请求。然后在Gateway Handler Mapping中找到与请求相匹配的路由，将其发送到Gateway
* **Web Handler。**
* Handler再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。
* 过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前( "pre" )或之后( "post" )执行业务逻辑。
* Filter在 "pre" 类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等,
* 在"post" 类型的过滤器中可以做响应内容、响应头的修改,日志的输出，流量监控等有着非常重要的作用。
* 核心逻辑：路由转发+执行过滤链

####  4、入门配置

##### （1）pom

```xml
<!--新增gateway-->
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-gateway</artifactId> </dependency>
```

##### （2）路由配置

* yml

```properties
server:
  port: 9527
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001   #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**   #断言,路径相匹配的进行路由

        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/lb/**   #断言,路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```

* bean

```java
package com.rw.springcloud.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GateWayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes=routeLocatorBuilder.routes();
        routes.route("path_rout_rw1",
                r->r.path("/guonei")
                        .uri("http://news.baidu.com/guonei"))
                .build();
        return routes.build();
    }
}

```

##### 

#### 5、通过微服务名实现动态路由

* 默认情况下Gateway会根据注册中心注册的服务列表以注册中心上微服务名为路径创建**动态路由进行转发，从而实现动态路由的功能**
* yml

```properties
server:
  port: 9527
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh #路由的ID，没有固定规则但要求唯一，建议配合服务名
#          uri: http://localhost:8001   #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service
          predicates:
            - Path=/payment/get/**   #断言,路径相匹配的进行路由

        - id: payment_routh2
          #uri: http://localhost:8001
          uri: lb://cloud-payment-service
          predicates:
            - Path=/payment/lb/**   #断言,路径相匹配的进行路由


eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```



#### 6、Predicate的使用

| 类型                      | 示例                                                         | 解释                                                   |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| 1.After Route Predicate   | - After=2020-03-08T10:59:34.102+08:00[Asia/Shanghai]         | 在XXX时间之后访问有效                                  |
| 2.Before Route Predicate  | - Before=2020-03-08T10:59:34.102+08:00[Asia/Shanghai]        | 在XXX时间之前访问有效                                  |
| 3.Between Route Predicate | - Between=2020-03-08T10:59:34.102+08:00[Asia/Shanghai] ,  2020-03-08T10:59:34.102+08:00[Asia/Shanghai] | 在XXX时间之间访问有效                                  |
| 4. Cookie Route Predicate | - Cookie=username,atguigu                                    | 带上Cookie访问并且Cookie 是username=zhangshuai才能访问 |
| 5. Header Route Predicate | -Header=X-Request-Id, \d+                                    | 请求头中要有X-Request-Id属性且值为整数的正则表达式     |
| 6.Host Route Predicate    | - Host=**.atguigu.com                                        | 接收一组匹配的域名列表                                 |
| 7.Method Route Predicate  | - Method=GET                                                 | 规定访问方法为GET                                      |
| 8.Path Route Predicate    |                                                              |                                                        |
| 9. Query Route Predicate  | - Query=username, \d+                                        | 要有参数名称并且是正整数才能路由                       |



#### 7、Filter的使用

##### （1）是什么？

* 路由过滤器可用于修改进入的HTTP请求和返回的HTTP响应,路由过滤器只能指定路由进行使用。
* Spring Cloud Gateway内置Y多种路由过滤器，他们都由GatewayFilter的工厂 类来产生

##### （2）Spring Cloud Gateway的Filter

* 生命周期，Only Two
  * pre  请求之前
  * post 请求之后
* 种类，Only Two
  * GatewayFilter 单一的
  * GlobalFilter  全局的

##### （3）自定义过滤器

* 两个接口介绍
  * GlobalFilter，Ordered
* 功能
  * 全局日志记录
  * 统一网关鉴权
* 案例代码

```java
package com.rw.springcloud.filter;

import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Date;

@Component
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        System.out.println("*********com in MyLogGateWayFilter"+new Date());
        String name=exchange.getRequest().getQueryParams().getFirst("uname");
        if(name==null){
            System.out.println("******用户名为null，非法用户");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}

```

* 效果：请求地址中带由uname才让访问http://localhost:9527/payment/lb?uname=z3