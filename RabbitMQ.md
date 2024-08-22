# RabbitMQ

## 基本介绍

- RabbitMQ是基于Erlang语言开发的开源消息通信中间件，官网地址：http://www.rabbitmq.com/

- 由四个部分组成：virtual-host，publisher，consumer，queue，exchange

## RabbitMQ的安装

1. RabbitMQ的运行在docker中

```dockerfile
docker run -d --name myrabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

2. 浏览器访问http://localhost:15672，账号和密码都是guest

## JavaAPI

### Java中的使用

1. 引入依赖

```xml
<dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-amqp</artifactId>
         <version>2.7.17</version>
 </dependency>
```

2. 配置RabbitMQ客户端信息

```yaml
spring:
  rabbitmq:
    host: 192.168.0.1
    port: 5672
    virtual-host: /huang
    username: huangkaiwen
    password: 744669
```

3. 发送消息

```java
@ExtendWith(SpringExtension.class) //
@SpringBootTest
public class SendMessage {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Test
    void Send() {
        String queueName = "q1";
        String message = "hello";
        rabbitTemplate.convertAndSend(queueName, message);
    }
}
```

4. 接收消息

```java
@Slf4j
@Component
public class Listener {
    @RabbitListener(queues = "q1")
    public void listen(String msg){
        log.info("Spring 消费者收到消息【"+msg+"】");
    }
}
```

### Work模型

Work模型是指多个消费者绑定到一个队列，默认情况下，RabbitMQ会将消息依次轮询投递给绑定在队列上的每个消费者，但并没有考虑消费者是否处理完消息，因此我们需要修改application.yml

```java
rabbitTemplate.convertAndSend(queueName,message);//发送消息给队列
```



```yaml
spring:
  rabbitmq:
    listener:
      direct:
        prefetch: 1 #每次只能获取一条消息，处理完才能获取下一条
```

### Fanout交换机

Fanout交换机会将接收到的消息广播到每一个跟其绑定的queue

```java
rabbitTemplate.convertAndSend(FanoutExchangeName,"",message); // 发送消息给交换机
```

### Direct交换机

Direct Exchange会将收到的信消息根据规则路由到指定Queue，因此称为定向路由

```java
rabbitTemplate.convertAndSend(DirectExchangeName,"yellow",message); // 发送消息给交换机
```

### Topic交换机

与Direct交换机类似，区别在于routingKey可以是多个单词列表并且以 . 分割，Topic交换机与队列绑定时，可以制定分配符号， # 表示0个或多个单词，* 表示一个单词

### 声明队列和交换机

1. 基于Configuration的方式声明队列和交换机

```java
@Configuration
public class RabbitMQConfiguration{
  //创建exchange
    @Bean
    public DirectExchange directExchange(){
        return new DirectExchange("direct.exchange1");
    }
    
    //创建 queue
    @Bean
    public Queue fanoutQ1(){
        return new Queue("fanout.q1");
    }
  
    //绑定
    @Bean
    public Binding binding2(){
        return BindingBuilder.bind(fanoutQ1()).to(directExchange()).with("white");
    }
  
}
```

2. 使用@RabbitListener注解来声明队列和交换机

```java
@RabbitListener(bindings = @QueueBinding(
            value = @Queue(value = "q1", durable = "true"),
            exchange =  @Exchange(value = "direct.exchange", type = ExchangeTypes.DIRECT),
            key = {"red", "yellow"}
    ))
    public void listen3(String msg){
        log.info("Spring 消费者2收到消息【"+msg+"】");
    }
```

### 消息转换器

由于Spring的消息对象是由JDK序列化的，存在风险大，消息长， 可读性差等缺点，所以建议用json序列化代替默认的JDK序列化

1.引入json序列化坐标

```xml
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
```

2. 在publisher和consumer中配置MessageConverter

```java
//配置消息转换器
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
```

### 生产者可靠性

#### 生产者重连

```yaml
spring:
    connection-timeout: 1s # 连接超时时间
    template:
      retry:
        enabled: true # 开启
        max-attempts: 3 # 最大尝试次数
        multiplier: 1 # 下一次等待时长倍数
        max-interval: 10000ms #失败后初试等待时间
```

#### 生产者确认

RabbitMQ在开启确认机制后， 在MQ成功收到消息后会返回确认消息给生产者，返回结果有以下几种情况，

- 消息到了MQ，但是路由失败，会返回路由失败原因，ACK
- 临时消息到了MQ，并成功入队，ACK
- 持久消息到了MQ，并且入队完成持久化，ACK
- 其他均返回NACK

```yaml
spring:
  rabbitmq:
    publisher-returns: true   # 开启return机制
    publisher-confirm-type: correlated # 设置confirm类型 还有simple，none类型 
```

为RabbitTemplate配置ReturnCallback，当MQ消息路由到队列失败时的处理

```java
@Configuration
public class publisherConfiguration implements ApplicationContextAware {
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        RabbitTemplate rabbitTemplate = applicationContext.getBean(RabbitTemplate.class);
        //设置returnCallback
        rabbitTemplate.setReturnCallback(((message, i, s, s1, s2) -> {
            log.info("消息发送失败，应答码{}, 原因{}，交换机{}，路由键{}，消息{}", message, i, s, s1, s2);
        }));
    }
}

```

为发送的信息配置ConfirmCallback

```java
@Test
    void testPublicsherConfirm() {
        // 1.创建CorrelationData
        CorrelationData cd = new CorrelationData();
        // 2.给Future添加ConfirmCallback
        cd.getFuture().addCallback(new ListenableFutureCallback<CorrelationData.Confirm>() {
            @Override
            public void onFailure(Throwable ex) {
                //2.1future发生异常时的处理逻辑，基本不会触发
                log.error("fail");
            }

            @Override
            public void onSuccess(CorrelationData.Confirm result) {
                //2.2 future 接收到回执的处理逻辑
                if(result.isAck()){
                    log.debug("消息发送成功");
                }else {
                    log.error("消息发送失败：{}", result.getReason());
                }
            }
        });
        //发送到交换机
        rabbitTemplate.convertAndSend("direct.exchange","Math.mul","hello", cd);
    }
```

### MQ可靠性

在默认情况下，RabbitMQ会将接收到的信息保存在内存中以降低消息收发的延迟，这样会导致两个问题：

1. 一旦MQ宕机，内存中存在的消息会丢失
2. 内存空间有限，当消费者故障的或处理过慢，会导致消息积压，引发MQ阻塞

#### 消息持久化

```java
@Test
void testPageOut(){
    Message message = MessageBuilder
                .withBody("hello".getBytes())
                .setDeliveryMode(MessageDeliveryMode.PERSISTENT).build();
    rabbitTemplate.convertAndSend("q1", message);

}
```

#### Lazy Queue

惰性队列接收到消息后直接存入磁盘而非内存，消费者需要消息时才会从磁盘中读取并加载到内存，MQ3.6之后所有queue都是默认惰性队列，开启持久化和生产者确认时，RabbitMQ只有在消息持久化完成后才会给生产者返回ACK回执

```java
@Bean
    public Queue lazyQueue(){
        return QueueBuilder.durable("q1").lazy() //开启lazy模式
                .build();
    }
```

### 消费者可靠性

为了确认消费者是否成功处理消息，RabbitMQ提供了消费者确认机制，当消费者处理消息结束后，应该向RabbitMQ发送一个回执

- ACK ： 成功处理消息，RabbitMQ从队列中删除该消息
- NACK：消息处理失败，RabbitMQ需要再次投递消息
- REJECT：消息处理失败并拒接该消息，从MQ队列中删除

```yaml
spring:
  rabbitmq:
    password: 744669
    listener:
      simple:
        acknowledge-mode: auto # 开启消费者确认机制
        prefetch: 1 #每次只能获取一条消息，处理完才能获取下一条
```

#### 失败重试机制

当消费者异常后，消息入队然后重新发送，导致mq的消息处理飙升，带来不必要的压力，所以我们可以使用Spring的retry机制，当消费者异常后利用本地重试

```yaml
spring:
  rabbitmq:
    listener:
        retry:
          enabled: true
          initial-interval: 1000ms
          multiplier: 1 
          max-attempts: 3 
          stateless: true # true无状态，false有状态，如果业务中包含事务则改为false
```

### 延迟消息

延迟消息：生产者发送消息时，指定一个时间，消费者不会立刻收到消息，而是在指定时间后才收到消息

延迟任务：设置在一定时间之后才执行的任务

#### 死信交换机

当一个队列中的消息满足下列情况之一时，就会成为死信：

- 消费者使用basic.reject或basic.nack声明消费失败，并且消息的requeue参数设置为false
- 消息是一个过期消息，超时无人消费
- 要投递的队列消息堆积满了，最早的消息可能成为死信

如果一个队列通过dead-letter-exchange属性指定了一个交换机，那么该队列中死信就会投递到这个交换机中，称为死信交换机（Dead letter exchange）![](https://gulinall-hkw.oss-cn-shenzhen.aliyuncs.com/c1578076-9294-45c1-b148-0f381d24b9bc.png)

#### 延迟消息插件

