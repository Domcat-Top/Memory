## 服务降级

### 一、Hystrix

#### 1、分布式系统面临的问题

* 复杂分布式体系结构中的应用程序又数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。
* eg:一个请求需要调用A,P,H,I四个服务，如果一切顺利则没有什么问题，关键如果中间某个服务超时会出现什么情况？**-->服务雪崩**
* 服务雪崩
  * 多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C,微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”。
    如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃,所
    谓的“雪崩效应”
  * 对于高流量的应用来说，单一的后端依赖可能会 导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还
    可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延
    迟进行隔离和管理，以便单个依赖关系的失败,不能取消整个应用程序或系统。
    所以,
  * 通常当你发现-个模块下的某个实例失败后,这时候这个模块依然还会接收流量,然后这个有问题的模块还调用了其他的模块,这样就
    会钹生级联故障，或者叫雪崩。

#### 2、Hystrix是什么？

* Hystrix是一 个用于处理分布式系统的延迟和容错的开源库,在分布式系统里,许多依赖不可避免的会调用失败,比如超时、异常等,
  Hystrix能够保证在一 个依赖出问题的情况下，不会导致整体服务失败,避免级联故障,以提高分布式系统的弹性。
* “断路器”本身是一种开关装置,当某个服务单元发生故障之后, 通过断路器的故障监控(类似熔断保险丝)，向调用方返回一个符合
  预期的、可处理的备选响应(FallBack) ，而不是长时间的等待或者抛出调用方无法处理的异常,这样就保证了服务调用方的线程不会
  被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

#### 3、重要概念

##### （1）服务降级 fallback

* 服务器忙，请稍后再试，不让客户端等待，并立刻返回一个友好提示，fallback
* 哪些情况会触发降级
  * 程序运行异常
  * 超时
  * 服务熔断触发服务降级
  * 线程池/信号量打满也会导致服务降级

##### （2）服务熔断 break

* 类似保险丝达到最大服务访问量后，直接拒绝访问，拉闸限电，然后调用服务降级的方法并返回友好提示
* 就是保险丝

##### （1）服务限流 flowlimit

* 秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排对，一秒钟N个，有序进行

##### 

#### 3、Hystrix案例

##### （1）pom

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-netflix-hystrix -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    <version>2.2.1.RELEASE</version>
</dependency>
```



##### （2）yml

```properties
server:
  port: 8001


eureka:
  client:
    register-with-eureka: true    #表识不向注册中心注册自己
    fetch-registry: true   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      # defaultZone: http://eureka7002.com:7002/eureka/    #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7001.com:7001/eureka/
#  server:
#    enable-self-preservation: false
spring:
  application:
    name: cloud-provider-hystrix-payment
  #    eviction-interval-timer-in-ms: 2000


```



##### （3）Jmeter并发测试

* 请求一个接口另一个也会被拖累，最终倒是服务端8001直接被拖死

##### （4）故障导致

* 用Jmeter高并发去压超时接口 正常的接口会受影响出现超时或者请求变慢
* 因为tomcat线程池里面的工作线程已经被挤占完毕
* 正因为又上述故障或不佳表现才有我们的降级/容错/限流技术诞生

##### （5）如何解决？解决的要求

* 超时导致服务器变慢（转圈）
  * 超时不再等待
* 出错（宕机或程序运行出错）
  * 出错要有兜底
* 解决
  * 对方服务(8001)超时了，调用者(80)不能- -直卡死等待，必须有服务降级
  * 对方服务(8001)down机了，调用者(80)不能一-直 卡死等待，必须有服务降级
  * 对方服务（8001）OK，调用者（80）自己故障或有自我要求（自己的等待时间小于服务提供者），自己处理降级



##### （6）服务降级 fallback

* 降级配置 @HystrixCommand
* 先从自身找问题
  * 设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处理，作为服务降级的fallback
* 8001fallback（服务端降级）
  * 业务类启用 @HystrixCommand报异常后如何处理
    * 一旦调用服务失败并跑哦出了错误信息后，会自动调用@HystrixCommand标注好的fallbackMethod调用类中的指定方法
  * 主启动类激活
    * @EnableCircuitBreaker

```java
    @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds" , value="3000")
    })
    public R paymentInfo_TimeOut(Integer id){
        //暂停几秒线程
        int timeNumber=5;
//        try { TimeUnit.SECONDS.sleep(timeNumber); }catch (InterruptedException e){
//           e.printStackTrace();
//        }
        int age=10/0;
        return R.ok().data("data","线程池"+Thread.currentThread().getName()+
                "paymentInfo_TimeOut,id"+id+"\t"+"O(∩_∩)O嘿嘿~" +"耗时(s)"+timeNumber);
    }

    public R paymentInfo_TimeOutHandler (Integer id){
        return R.ok().data("data","线程池"+Thread.currentThread().getName()+
                "paymentInfo_TimeOutHandler,id"+id+"\t"+"O(！^！)" );
    }
```



* 80 fallback（客户端降级）

  * yml

  ```properties
  #feign开启对hystrix的支持
  feign:
    hystrix:
      enabled: true
  ```

  * 启动类：@EnableHystrix
  * controller

  ```java
      @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",commandProperties = {
              @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "1500")
      })
      @GetMapping("/payment/hystrix/timeout/{id}")
      public R paymentInfo_TimeOut(@PathVariable("id") Integer id){
          return hystrixService.paymentInfo_TimeOut(id);
      }
      public R paymentInfo_TimeOutHandler (Integer id){
          return R.ok().data("data","线程池"+Thread.currentThread().getName()+
                  "我是消费者，对方支付系统繁忙请10s后再试或者自己运行出错请检查自己O(！^！)" );
      }
  ```

  * 这样配置当客户端调用服务端自身出错时或者服务端响应时间超过自己定义等待时间时就会到paymentInfo_TimeOutHandler方法中

* 目前问题：

  * 每个业务方法对应一个兜底的方法，代码膨胀
  * 统一和自定义的分开

* 解决问题

  * 每个方法配置一个？？？膨胀

    * feign接口系列
    * @ DefaultProperties(defaultFallback = "")
    * controller配置

    ```java
    package com.rw.springcloud.controller;
    
    @RestController
    @Slf4j
    @DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")
    //配置了全局默认的 方法上只需要写@HystrixCommand 就默认执行出错的方法，如果不写的话就和服务降级没关系，也可以针对的实现私人定制
    public class OrderHystrixController {
        @Autowired
        private PaymentHystrixService hystrixService;
    
        @GetMapping("/payment/hystrix/ok/{id}")
        public R paymentInfo_OK(@PathVariable("id") Integer id){
            return hystrixService.paymentInfo_OK(id);
        }
    
    //    @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler",commandProperties = {
    //            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "1500")
    //    })
        @HystrixCommand
        @GetMapping("/payment/hystrix/timeout/{id}")
        public R paymentInfo_TimeOut(@PathVariable("id") Integer id){
            return hystrixService.paymentInfo_TimeOut(id);
        }
        public R paymentInfo_TimeOutHandler (Integer id){
            return R.ok().data("data","线程池"+Thread.currentThread().getName()+
                    "我是消费者，对方支付系统繁忙请10s后再试或者自己运行出错请检查自己O(！^！)" );
        }
        //全局fallback
        public R payment_Global_FallbackMethod(){
            return R.error().message("Global异常处理信息，请稍后再试！");
        }
    }
    
    ```

    

  * 和业务逻辑混一起？？？混乱

    * 对feign接口添加解耦类
    * 要面对的异常
      * 运行异常
      * 超时异常
      * 突然宕机
    * 处理，配置统一的异常处理类PaymentFallbackService

    ```java
    package com.rw.springcloud.service;
    
    import com.rw.springcloud.utils.R;
    import org.springframework.stereotype.Component;
    
    @Component
    public class PaymentFallbackService  implements PaymentHystrixService {
        @Override
        public R paymentInfo_OK(Integer id) {
            return R.ok().message("PaymentFallbackService--paymentInfo_OK--fallback");
        }
    
        @Override
        public R paymentInfo_TimeOut(Integer id) {
            return R.ok().message("PaymentFallbackService--paymentInfo_TimeOut--fallback");
        }
    }
    
    ```

    * feign接口做统一处理

    ```java
    package com.rw.springcloud.service;
    
    import com.rw.springcloud.utils.R;
    import org.springframework.cloud.openfeign.FeignClient;
    import org.springframework.stereotype.Component;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    
    @Component
    @FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT",fallback = PaymentFallbackService.class)
    public interface PaymentHystrixService {
        @GetMapping("/payment/hystrix/ok/{id}")
        public R paymentInfo_OK(@PathVariable("id") Integer id);
        @GetMapping("/payment/hystrix/timeout/{id}")
        public R paymentInfo_TimeOut(@PathVariable("id") Integer id);
    }
    
    ```

    * 这样处理后就统一配置了服务降级，客户端宕机、超时、报错都会走fallback方法，如果实现controller私人定制的话还是走私人定制的fallback

##### （7）服务熔断 break

* 断路器：一句话就是家里的保险丝

* 熔断是什么

  * 熔断机制概述
  * 熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时,会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。
    **当检测到该节点微服务调用响应正常后,恢复调用链路。**
  * 在Spring Cloud框架里,熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值, 缺省是5秒内20次调用失败,就会启动熔断机制。熔断机制的注解是@HystrixCommand.

* 实操

  * 在8001provider测试
  * service

  ```java
   /**
       * =====服务熔断
       */
      @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
              @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),  //是否开启断路器
              @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),   //请求次数
              @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),  //时间范围
              @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"), //失败率达到多少后跳闸
      })
      public String paymentCircuitBreaker(@PathVariable("id") Integer id){
          if (id < 0){
              throw new RuntimeException("*****id 不能负数");
          }
          String serialNumber = IdUtil.simpleUUID();
  
          return Thread.currentThread().getName()+"\t"+"调用成功,流水号："+serialNumber;
      }
      public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id){
          return "id 不能负数，请稍候再试,(┬＿┬)/~~     id: " +id;
      }
  ```

  * controller

  ```java
    //服务熔断
      @GetMapping("/payment/circuit/{id}")
      public String paymentCircuitBreaker(@PathVariable("id") Integer id){
          return paymentService.paymentCircuitBreaker(id);
      }
  ```

  效果：错误率达到配置的错误率后服务开始断开就算请求正确的也会走fallback方法，直到错误率逐渐下降，服务恢复

* 原理

  * 熔断打开：请求不再进行调用当前服务，内部设置时钟- -般为MTTR (平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态
  * 熔断关闭：熔断关闭不会对服务进行熔断
  * 熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常,关闭熔断

* 断路器流程

  * 断路器在什么情况下开始其作用
    * 涉及到断路器的三个重要参数:快照时间窗、请求总数阀值、错误百分比阀值。
      * 1:快照时间窗:断路器确定是否打开需要统计- 些请求和错误数据,而统计的时间范围就是快照时间窗,默认为最近的10秒。	
      * 2:请求总数阀值:在快照时间窗内，必须满足请求总数阀值才有资格熔断。默认为20, 意味着在10秒内,如果该hystrix命令的调用次数不足20次,即使所有的请求都超时或其他原因失败，断路器都不会打开。
      * 3:错误百分比阀值:当请求总数在快照时间窗内超过了阀值,比如发生了30次调用，如果在这30次调用中,有15次发生了超时异常,也就是超过50%的错误百分比，在默认设定50%阀值情况下,这时候就会将断路器打开。
  * 断路器开启或关闭的条件
    * 满足一定的阈值（默认10s超过20请求次数）
    * 当失败率达到一定的时候（默认10s内超过50%的失败请求）
    * 到达以上阈值，断路器将会开启
    * 当开启的时候，所有请求都不会进行转发
    * 一段时间之后（默认是5s），这个时候断路器是半开状态，会让其中一个请求进行转发。如果成功，断路器会关闭，若失败，继续开启，重复4和5
  * 断路器打开之后
    * 1:再有请求调用的时候，将不会调用主逻辑，而是直接调用降级allback。通过断路器,实现了自动地发现错误并将降级逻辑切换为主逻辑，减少响应延迟的效果。
    * 2:原来的主逻辑要如何恢复呢?
      对于这一-问题，hystrix也为我们实现了自动恢复功能。
      当断路器打开,对主逻辑进行熔断之后，hystrix会启动一 个休眠时间窗,在这个时间窗内，降级逻辑是临时的成为主逻辑,
      当休眠时间窗到期，断路器将进入半开状态,释放-次请求到原来的主逻辑上,如果此次请求正常返回，那么断路器将继续闭合,
      主逻辑恢复，如果这次请求依然有问题，断路器继续进入打开状态,休眠时间窗重新计时。
  * All配置

##### （8）服务限流 flowlimit

* 后续的alibab的Sentinel说明

##### （9）Hystrix Dashboard 实时监控

* 除了隔离依赖服务的调用以外，Hystrix还提供了准实时的调用监控(Hystrix Dasghboard) , Hystrix会持续地记录所有通过Hystrix发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。Netflix通过hystrix-metrics-event-stream项目实现了对以上指标的监控。Spring Cloud也提供了Hystrix Dashboard的整合，对监控内容转化成可视化界面。
* 使用：
* pom

```xml
<!--新增hystrix dashboard-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
```

* yml:只需要标明端口（9001）
* 启动类：@EnableHystrixDashboard
* 拿9001去监控微服务提供类（8001/8002/8003）
* 监控