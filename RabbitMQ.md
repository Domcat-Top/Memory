## RabbitMQ快速入门

> 无法启动问题：
>
> - sibn目录下进入cmd
> - 输入`rabbitmq-plugins.bat enable rabbitmq_management`
> - 重启rabbitMQ
> - 浏览器输入`http://localhost:15672`

### 1. 生产者和消费者

- 生产者代码示例：

  ```java
  	public static void main(String[] args) throws IOException, TimeoutException {
  
          // 创建连接工厂
          ConnectionFactory factory = new ConnectionFactory();
  
          // 设置参数
          factory.setHost("localhost");
          factory.setPort(5672);
          // 这里注意，虚拟机一定不要加 /  不然会找不到虚拟机报错
          factory.setVirtualHost("tomTest"); // 设置虚拟机的默认值
          factory.setUsername("tom");
          factory.setPassword("tom");
  
          // 创建连接
          Connection connection = factory.newConnection();
          // 创建channel
          Channel channel = connection.createChannel();
          // 创建队列Queue
          /**
           * 构造函数解释
           * queueDeclare(String queue.....)
           * queue：队列名称
           * durable：是否持久化，当MQ重启，数据还在
           * exclusive：
           *  是否独占。只能有一个消费者监听这队列
           *  是否删除队列。当Connection关闭时，是否删除队列
           * autoDelete：是否自动删除。当没有Customer时，自动删除掉
           * arguments：参数
           *
           */
          // 如果没有一个叫做 tomQueue 的队列，则会自动创建该队列，如果有的话，则不会创建
          channel.queueDeclare("tomQueue",
                  true,
                  false,
                  false,
                  null);
          // 发送消息
          /**
           * basicPublish()方法参数说明：
           *      exchange：交换机的名称，简单模式下会使用默认的“”
           *      routingKey：路由名称，如果是用默认的交换机，这个参数需要和队列的名称保持一致
           *      props：配置信息
           *      body：发送消息数据
           */
          String body = "helloWorld";
          channel.basicPublish("",
                  "tomQueue",
                  null,
                  body.getBytes());
          // 释放资源
          channel.close();
          connection.close();
      }
  ```

- 消费者代码示例：

  ```java
  public static void main(String[] args) throws IOException, TimeoutException {
  
          // 创建连接工厂
          ConnectionFactory factory = new ConnectionFactory();
          // 设置参数
          factory.setHost("localhost");
          factory.setPort(5672);
          // 这里注意，虚拟机一定不要加 /  不然会找不到虚拟机报错
          factory.setVirtualHost("tomTest"); // 设置虚拟机的默认值
          factory.setUsername("tom");
          factory.setPassword("tom");
          // 创建连接
          Connection connection = factory.newConnection();
          // 创建channel
          Channel channel = connection.createChannel();
          // 创建队列Queue
          /**
           * 构造函数解释
           * queueDeclare(String queue.....)
           * queue：队列名称
           * durable：是否持久化，当MQ重启，数据还在
           * exclusive：
           *  是否独占。只能有一个消费者监听这队列
           *  是否删除队列。当Connection关闭时，是否删除队列
           * autoDelete：是否自动删除。当没有Customer时，自动删除掉
           * arguments：参数
           *
           */
          // 如果没有一个叫做 tomQueue 的队列，则会自动创建该队列，如果有的话，则不会创建
          channel.queueDeclare("tomQueue",
                  true,
                  false,
                  false,
                  null);
          // 接收消息
          // 匿名内部类的方式实现接口方法
          Consumer consumer = new DefaultConsumer(channel){
              // 回调方法，当收到消息后，会自动执行该方法
              /**
               *
               * @param consumerTag 标识
               * @param envelope 获取一些信息，如交换机的信息、路由信息
               * @param properties 配置信息
               * @param body 消息体，真实的数据
               * @throws IOException
               */
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  System.out.println(consumerTag);
                  System.out.println(envelope);
                  System.out.println(properties);
                  System.out.println(new String(body));
              }
          };
          /**
           * basicConSume(String queue, boolean autoAck, Consumer callback);
           *      queue：队列名称
           *      autoAck：自动确认
           *      callBack：回调对象
           */
          channel.basicConsume("tomQueue",
                  true,
                  consumer);
          // 消费者这边不需要关闭资源，需要时刻保持接收消息的状态
      }
  ```

### 2. RabbitMQ的工作模式

#### 2.1 WorkQueues工作队列模式

1. 一个生产者，多个消费者的模式：
   - 应用场景：对于任务过重或者任务较多情况下使用工作队列，可以提高任务处理的速度

2. Pub/Sub 订阅模式：
   - 消费者把消息发送给交换机
   - 交换机一方面接收生产者发送的消息。另一方面知道如何处理消息，如递交给某个特别队列、递交给所有队列或是将消息丢弃。常见的三种类型：
     - Fanout：广播，将消息交给所有绑定到交换机的队列
     - Direct：定向，把消息交给符合指定routing key的队列
     - Topic：通配符，把消息交给符合routing pattern（路由模式）的队列

3. 路由模式：

   - 模式说明：
     - 队里和交换机的绑定，不能是任意绑定了，而是要指定一个`RoutingKey`（路由Key）
     - 消息发送方在向Exchange发送消息时，也必须指定消息的`RoutingKey`
     - Exchange不再把消息交给每一个绑定的队列，而是根据消息的`RoutingKey`进行判断，只有队列的`RoutingKey`和消息的`RouteingKey`完全一致，才会接受到消息
   - 需要设置队列和交换机的`RoutingKey`保持一致

4. Topic通配符模式

   - Topic在配置RoutingKey时可以使用通配符，更加灵活区分那些数据需要进行哪些操作

   - 解释：

     ```java
     # 可以代表 0-多 个单词
     * 可以代表一个单词
     ```

### 3. SpringBoot整合RabbitMQ

- 引入依赖：

  ```xml
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-amqp</artifactId>
          </dependency>
  ```

- 配置文件：

  ```yaml
  server:
    port: 9999
  
  # rabbitMQ配置
  spring:
    rabbitmq:
      host: localhost
      port: 5672
      username: tom
      password: tom
      virtual-host: tomTest
  
  ```

- 生产者代码示例：

  ```java
  package com.tom.springbootrabbitmqproducer.rabbitmq.config;
  
  import org.springframework.amqp.core.*;
  import org.springframework.beans.factory.annotation.Qualifier;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  
  @Configuration
  public class RabbitMQConfig {
      
      public static final String EXCHANGE_NAME = "boot_topic_exchange";
      public static final String QUEUE_NAME = "boot_queue";
      
      // 交换机
      @Bean("bootExchange")
      public Exchange bootExchange() {
          return ExchangeBuilder.topicExchange(EXCHANGE_NAME)
                  .durable(true)
                  .autoDelete().build();
      }
      // 队列
      @Bean("bootQueue")
      public Queue bootQueue() {
          return QueueBuilder.durable(QUEUE_NAME)
                  .build();
      }
      // 队列 和 交换机 的绑定关系
      // 需要知道是 哪个队列 哪个交换机 routingKey
      @Bean
      public Binding bindQueueExchange(@Qualifier("bootQueue") Queue queue, @Qualifier("bootExchange") Exchange exchange) {
          return BindingBuilder.bind(queue).to(exchange).with("boot.#").noargs();
      }
  }
  
  	// 测试方法
      @Test
      void test(){
          rabbitTemplate.convertAndSend(RabbitMQConfig.EXCHANGE_NAME,
                  "boot.routingKey",
                  "Second test");
      }
  ```

- 消费者代码示例：

  ```java
  @Component
  public class RabbitMQListener {
  
      @RabbitListener(queues = "boot_queue")
      public void ListenerQueue(Message message) {
          // message可以获取到消息
          System.out.println(message.getBody());
      }
  }
  ```

### 4. 高级特性

#### 4.1 消息的可靠投递

- 消息的传递路径：

  ```xml
  producer --> rabbitMQbroker --> exchange --> queue --> consumer
  ```

- 提供了两种方式来控制消息的投递可靠性模式：

  - confirm 确认模式：

    - 消息从 producer 到 exchange 会返回一个 confirmCallback

    - 代码示例：

      ```java
              rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
                  @Override
                  public void confirm(CorrelationData correlationData, boolean b, String s) {
                      // 第二个参数就表示是否成功返回了
                      // 第三个参数是原因，返回失败才有原因，成功为null
                      if(b) {
                          System.out.println("成功");
                      } else {
                          System.out.println("发送失败了");
                          System.out.println("原因是：" + s);
                      }
                  }
              });
      ```

- return 退回模式：
  - 消息从 exchange 到 queue 投递失败会返回一个 returnCallback









