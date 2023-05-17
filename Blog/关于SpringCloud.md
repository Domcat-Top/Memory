## 1、 从面试题开始

### 1、微服务的由来

微服务最早由Martin Fowler与James Lewis于2014年共同提出，微服务架构风格是一种使用一套小服务来开发单个应用的方式途径，每个服务运行在自己的进程中，并使用轻量级机制通信，通常是HTTP API，这些服务基于业务能力构建，并能够通过自动化部署机制来独立部署，这些服务使用不同的编程语言实现，以及不同数据存储技术，并保持最低限度的集中式管理。 

### 2、为什么需要微服务

在传统的IT行业软件大多都是各种独立系统的堆砌，这些系统的问题总结来说就是扩展性差，可靠性不高，维护成本高。到后面引入了SOA服务化，但是，由于 SOA 早期均使用了总线模式，这种总线模式是与某种技术栈强绑定的，比如：J2EE。这导致很多企业的遗留系统很难对接，切换时间太长，成本太高，新系统稳定性的收敛也需要一些时间。 

### 3、微服务与单体架构区别

（1）单体架构所有的模块全都耦合在一块，代码量大，维护困难。

     微服务每个模块就相当于一个单独的项目，代码量明显减少，遇到问题也相对来说比较好解决。

（2）单体架构所有的模块都共用一个数据库，存储方式比较单一。

     微服务每个模块都可以使用不同的存储方式（比如有的用redis，有的用mysql等），数据库也是单个模块对应自己的数据库。

（3）单体架构所有的模块开发所使用的技术一样。

     微服务每个模块都可以使用不同的开发技术，开发模式更灵活。 



### 4、微服务本质

（1）微服务，关键其实不仅仅是微服务本身，而是系统要提供一套基础的架构，这种架构使得微服务可以独立的部署、运行、升级，不仅如此，这个系统架构还让微服务与微服务之间在结构上“松耦合”，而在功能上则表现为一个统一的整体。这种所谓的“统一的整体”表现出来的是统一风格的界面，统一的权限管理，统一的安全策略，统一的上线过程，统一的日志和审计方法，统一的调度方式，统一的访问入口等等。
（2）微服务的目的是有效的拆分应用，实现敏捷开发和部署 。
（3）微服务提倡的理念团队间应该是 inter-operate, not integrate 。inter-operate是定义好系统的边界和接口，在一个团队内全栈，让团队自治，原因就是因为如果团队按照这样的方式组建，将沟通的成本维持在系统内部，每个子系统就会更加内聚，彼此的依赖耦合能变弱，跨系统的沟通成本也就能降低。


### 5、什么样的项目适合微服务

微服务可以按照业务功能本身的独立性来划分，如果系统提供的业务是非常底层的，如：操作系统内核、存储系统、网络系统、数据库系统等等，这类系统都偏底层，功能和功能之间有着紧密的配合关系，如果强制拆分为较小的服务单元，会让集成工作量急剧上升，并且这种人为的切割无法带来业务上的真正的隔离，所以无法做到独立部署和运行，也就不适合做成微服务了。

### 6、微服务开发框架

目前微服务的开发框架，最常用的有以下四个：

Spring Cloud：http://projects.spring.io/spring-cloud（现在非常流行的微服务架构）

Dubbo：http://dubbo.io

Dropwizard：http://www.dropwizard.io （关注单个微服务的开发）

Consul、etcd&etc.（微服务的模块）



### 7、什么是Spring Cloud

Spring Cloud是一系列框架的集合。它利用Spring Boot的开发便利性简化了分布式系统基础设施的开发，如服务发现、服务注册、配置中心、消息总线、负载均衡、 熔断器、数据监控等，都可以用Spring Boot的开发风格做到一键启动和部署。Spring并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过SpringBoot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包

### 8、Spring Cloud和Spring Boot是什么关系

Spring Boot 是 Spring 的一套快速配置脚手架，可以基于Spring Boot 快速开发单个微服务，Spring Cloud是一个基于Spring Boot实现的开发工具；Spring Boot专注于快速、方便集成的单个微服务个体，Spring Cloud关注全局的服务治理框架； Spring Boot使用了默认大于配置的理念，很多集成方案已经帮你选择好了，能不配置就不配置，Spring Cloud很大的一部分是基于Spring Boot来实现，必须基于Spring Boot开发。可以单独使用Spring Boot开发项目，但是Spring Cloud离不开 Spring Boot。

### 9、springCloud和Dubbo有哪些区别

* Dubbo基于tcp的rRPC、 cloud是http协议的RESTful API

### 10、Springboot和SpringClout，请你谈谈对他们的理解

* Springboot专注于快速方便的开发单个个体微服务
* SpringCloud是关注全局的微信服务协调整理治理框架，他将Springboot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配偶制管理、服务发现、断路器、路由、微代理、事件总线、全局所、决策竞选、分布式会话等等集成服务
* Springboot可以离开SpringCloud独立使用开发项目，但SpringCloud离不开Springboot，属于依赖关系
* Springboot专注于快速、方便的开发但各微服务个体，SpringCloud关注全局的服务治理框架

### 11、什么是服务熔断？什么是服务降级

### 12、微服务的优缺点是什么？说一下你在项目开发中碰到的坑

### 13、你所知道的威武福技术栈有哪些？请举例一二

### 14、eureka和zookeeper都可以提供服务注册与发现的功能，请说说两个的区别

## 2、微服务概述

### 1、微服务与微服务架构

#### 1.1微服务

* idea工具里面用maven开发的一个个独立的小module，具体是使用
* springboot开发的一个小模块，专业的事情交给专业的人做，一个模块
* 就做这一件事、强调的是一个个的个体，每个完成特定的任务

* 强调的是服务的大小，**它关注的是某一个点**, 是具体解决某一个问题/ 提供落地对应服务的一个服务应用,狭意的看,可以看作Eclipse里面的一个个微服务工程/或者Module

#### 1.2微服务架构

* 微服务架构是一种架构模式， 它提倡将单一应用程序划分成- 组小的服务,服务之间互相协调、互相配合,为用户提供最终价值。
* 每个服务运行在其独立的进程中，服务与服务间采用轻量级的通信机制互相协作(通常是基于HTTP协议的RESTful API)。
* 每个服务都围绕着具体业务进行构建,并且能够被独立的部署到生产环境、类生产环境等。另外,应当尽量避免统-的、集中式的服务管理机制，对具体的一个服务而言,应根据业务.上下文,选择合适的语言、干具对其进行构建。

### 2微服务优缺点

* 优点
  * 每个服务足够内聚，足够小，代码容易理解这样能聚焦一个制定业务功能或需求
  * 开发简单、开发效率提高，一个服务可能就是专一的干一件事
  * 微服务能被小团队单独开发，这个 小团队是2-5个开发成员组成
  * 微服务是松耦合的，是功能意义的服务，无论是在开发阶段或不熟阶段都是独立的
  * 能使用不同的语言开发
  * 易于集成第三方，微服务允许且灵活的方式集成自动部署，通过持续集成工具如：Jenkins，Hudson，bamboo。
  * 易于修护
  * **微服务只是业务逻辑的代码，不会和HTML，CSS或其他界面组件混合**
  * 每个微服务都可以有自己的数据库，连接公共库、也可以有统一数据库
* 缺点
  * 开发人员要处理分布式系统的复杂性
  * 多为服务运维度，随着服务增加，运维的压力也在增大
  * 系统部署以来
  * 服务器间通信成本
  * 数据一致性
  * 系统集成测试
  * 性能监控

### 3、微服务 技术栈有哪些

* 微服务技术栈：多种技术的集合体
* 我们在讨论一个分布式的微服务架构的话，他需要有哪些维度？
  * 服务治理、服务注册、服务调运、服务负载均衡、服务监控、服务开发，配置与管理......+对应微服务的落地技术

### 4、为什么选择springCloud作为微服务架构

* 选型依据
  * 整体解决方案和框架成熟度
  * 社区热度
  * 可维护性
  * 学习曲线
* 当前各大IT公司用的微服务加购有哪些
  * 阿里Dubbo/HSF
  * 京东JSF
  * 新浪微博Motan
  * 当当网DubboX
* 各微服务框架对比
  * 功能齐全

## 3、SpringCloud入门概述

### 1、是什么

* 官网说明 :SpringCloud,基于SpringBoot提供了一套微服务解决方案, 包括服务注册与发现，配置中心，全链路监控，服务网关，负载均
  衡，熔断器等组件，除了基于NetFlix的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。
  [![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/springcloud.png?Expires=1594182718&OSSAccessKeyId=TMP.3KkB1rwQ6vEF7zqJYZJ7KKBsAaoSHyaVRt1SY4rc6eVi565YJXbVr5Ct6dfUzjf7qDsEfWvBamrbxWrY49qt8WGPdDuj9w&Signature=6m3spMx3nXVBCCe1Uv21DF%2FnPdg%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/springcloud.png?Expires=1594182718&OSSAccessKeyId=TMP.3KkB1rwQ6vEF7zqJYZJ7KKBsAaoSHyaVRt1SY4rc6eVi565YJXbVr5Ct6dfUzjf7qDsEfWvBamrbxWrY49qt8WGPdDuj9w&Signature=6m3spMx3nXVBCCe1Uv21DF%2FnPdg%3D)


* SpringCloud=分布式微服务架构下的一站式解决方案，是个各微服务架构落地技术的几何体，俗称微服务全家桶

* SpringCloud和SpringBoot是什么关系

  * Springboot专注于快速方便的开发单个个体微服务
  * SpringCloud是关注全局的微信服务协调整理治理框架，他将Springboot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配偶制管理、服务发现、断路器、路由、微代理、事件总线、全局所、决策竞选、分布式会话等等集成服务
  * Springboot可以离开SpringCloud独立使用开发项目，但SpringCloud离不开Springboot，属于依赖关系
  * Springboot专注于快速、方便的开发但各微服务个体，SpringCloud关注全局的服务治理框架

* Dubbo是怎么到SpringCloud的？哪些优缺点让你去技术选型

  * 目前成熟的互联网架构（分布式+服务治理Dubbo）

[![](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/springcloud1.png?Expires=1594182915&OSSAccessKeyId=TMP.3KkB1rwQ6vEF7zqJYZJ7KKBsAaoSHyaVRt1SY4rc6eVi565YJXbVr5Ct6dfUzjf7qDsEfWvBamrbxWrY49qt8WGPdDuj9w&Signature=nQUwUTb6Tj13hCs8sMtXX944Jng%3D)](http://rw-blog.oss-cn-beijing.aliyuncs.com/springcloud/springcloud1.png?Expires=1594182915&OSSAccessKeyId=TMP.3KkB1rwQ6vEF7zqJYZJ7KKBsAaoSHyaVRt1SY4rc6eVi565YJXbVr5Ct6dfUzjf7qDsEfWvBamrbxWrY49qt8WGPdDuj9w&Signature=nQUwUTb6Tj13hCs8sMtXX944Jng%3D)

  * 我们把SpringCloud VS Dubbo 进行对比

    * 活跃度

      * https://github.com/dubbo
      * https://github.com/spring-cloud

|              | Dubbo         | SpringCloud                 |
| ------------ | ------------- | --------------------------- |
| 服务注册中心 | Zookeeper     | SpringCloud Netflix Eureka  |
| 服务调用方式 | RPC           | REST API                    |
| 服务监控     | Dubbo-monitor | SpringBoot Admin            |
| 断路器       | 不完善        | SpringCloud Netflix Hystrix |
| 服务网管     | 无            | SpringCloud Netflix Zuul    |
| 分布式配置   | 无            | SpringCloud Config          |
| 服务跟踪     | 无            | SpringCloud Sleuth          |
| 消息总线     | 无            | SpringCloud Bus             |
| 数据流       | 无            | SpringCloud Stream          |
| 批量任务     | 无            | SpringCloud Task            |



   * 最大区别：SpringCloud抛弃了Dubbo的RPC通信，才用的是基于HTTP的REST方式
   * 严格来说，这两种方式各有优劣。虽然从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原生RPC带来的问题。而且REST相比RPC更为灵活，服务提供方和调用方的依赖只依靠一纸契约， 不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适。

* 总结Cloud与Dubbo
    * Dubbo重启维护开发的刘军，主要负责人之一
    * Dubbo的定位始终是一款RPC框架，而SpringCloud的目标是微服务框架的下一站式解决方案
    * 面临微服务基础框架选型时Dubbo与SpringCloud只能是二选一

### 2、能干嘛

* Distributed/versioned configuration(分布式/版本控制配置)
* Service registration and discovery(服务注册与发现)
* Routing(路由)
* Service-to-service calls(服务到服务的调用)
* Load balancing(负载均衡配置)
* Circuit Breakers(断路器)
* Distributed messaging(分布式消息管理) 
* 。。。。。

### 3、去哪下

* 官网：http://progects.spring.io/spring-cloud/
* 参考
  * https://springcloud.cc/spring-cloud-netflix.html 中文API文档
  * API说明
    * https://cloud.spring.io/spring-cloud-static/Dalston.SR1/
    * https://cloud.springcloud.cc/spring-cloud-dalston.html
  * springcloud中文社区 https://springcloud.cn/
  * springcloud中文网 https://springcloud.cc/

### 4、怎么玩

* 服务的注册与发现(Eureka)
* 服务消费者(rest+ Ribbon)
* 服务消费者(Feign)
* 断路器(Hystrix)
* 断路器监控(Hystrix Dashboard)
* 路由网关(Zuul)
* 分布式配置中心(Spring Cloud Config)
* 消息总线(Spring Cloud Bus)
* 服务链路追踪(Spring Cloud Sleuth)
  。。。。。
* All:截止到18年4月cloud集成了21种技术

### 5、SpringCloud国内使用情况

* 国内公司 
  * 中国联通子公司
  * 华为
  * 视觉中国
  * 拍拍贷
  * 阿里云