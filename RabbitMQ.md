# RabbitMQ

## 同步调用

同步的优势：

时效性强，等待到结果才返回

存在的问题：

拓展性差、性能下降、级联失败

## 异步调用

异步调用主要基于消息通知的方式

存在三个角色：

消息发送者：投递消息的人，原来的调用方

消息代理：管理、暂存、转发消息，一个转发消息的服务器

消息接收者：接收和处理消息的人，原来服务的提供方

![](assets/RabbitMQ1.png)

优势：

耦合度低，拓展性强

异步调用，无需等待，性能好

故障隔离，下游服务故障不影响上游业务

缓存消息，流量削峰填谷

问题：

不能立即得到结果，时效性差

不确定下游业务是否成功

业务安全依赖于broker的可靠性

## MQ选型

![](assets/RabbitMQ2.png)

Kafka一般用于日志收集

![](assets/RabbitMQ3.png)

RabbitMQ中可以建造一个VirtualHost，虚拟主机，形成数据隔离

## Java客户端

![](assets/RabbitMQ4.png)

对于每个服务都需要对其进行配置连接文件

![](assets/RabbitMQ5.png)

![](assets/RabbitMQ6.png)

```
@Autowired
private RabbitTemplate rabbitTemplate;
@Test
public void testPublisher() {
  String queueName = "simple.queue";
  String message = "hello, spring amqp!";
  rabbitTemplate.convertAndSend(queueName, message);
}
```

```
/**
 * @author 自然醒
 * @version 1.0
 */
@Component
@Slf4j
public class RabbitListenerConfig {
    @RabbitListener(queues = "simple.queue")
    public void receive(String message) {
        log.info("接收到的消息：{}", message);
    }
}
```

## WorkQueue模型

WorkQueue模型：

- 多个消费者绑定到一个队列，可以加快消息处理速度，此时也可以解决消息积压问题
- 同一个消息只会被一个消费者处理
- 设置prefetch可以控制消费者预取的消息数量，处理完一条之后才可以处理下一条

对于消费者消息推送的限制：

默认情况，RabbitMQ是以轮询的形式将消息推送给每一个消费者，此时并不知道消费者是否处理完上一条消息，这时候就会出现消息积压

解决这种限制需要配置文件

```
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1
```

这时候每个消费者只能消费一条消息，并且要处理完上一条才可以处理下一条，解决了消息积压

## 交换机

作用：接收publisher发送的消息、将消息按照规则路由到与之绑定的队列

![](assets/RabbitMQ7.png)

**Fanout交换机：**

将接收到的消息广播到每一个跟其绑定的queue，广播模式

**Direct交换机：**

将接到的消息根据规则路由到指定的Queue，定向路由

- 每一个Queue都与Exchange设置一个BindingKey
- 发送消息时，指定消息的RoutingKey
- Exchange将消息路由到BindingKey与RoutingKey一致的队列

可以实现一种按需推送消息

**Topic交换机：**

![](assets/RabbitMQ8.png)

![](assets/RabbitMQ9.png)

## 客户端绑定

![](assets/RabbitMQ10.png)

消费者进行声明队列、交换机

![](assets/RabbitMQ11.png)

**消息转换器：**

传输对象时会默认使用原生的JDK序列化

![](assets/RabbitMQ12.png)

解决方案

![](assets/RabbitMQ13.png)

消费者、生产者均需要配置文件

## 生产者重连

![](assets/RabbitMQ14.png)

![](assets/RabbitMQ15.png)

这个是连接时的重试，不是消息发送失败时的重试

## 生产者确认

![](assets/RabbitMQ16.png)

确认配置

![](assets/RabbitMQ17.png)

每一个RabbitTemplate只能配置一个ReturnCallback，所以需要再项目启动过程中配置，以下是Return确认方式

![](assets/RabbitMQ18.png)

publisher配置中的return主要是返回路由失败消息

以下是ConfirmCallback

![](assets/RabbitMQ19.png)

如何保证生产者的可靠性？

RabbitMQ中存在一种重连机制，当网络抖动时，我们可以进行重连，避免网络抖动导致发送消息失败，除此RabbitMQ还有一个生产者确认机制，当消息发送失败会有一个ack回执，发送失败则是nack回执，这样就保证了生产者的可靠性，但此时会有额外的资源开销，一般情况下是不需要确认回执的

## MQ的可靠性

![](assets/RabbitMQ20.png)

**数据持久化：**

1.交换机持久化   2.队列持久化   3.消息持久化

**Lazy Queue：**

![](assets/RabbitMQ21.png)

![](assets/RabbitMQ22.png)

![](assets/RabbitMQ23.png)

配置非持久化消息会直接写入内存，满了再同步到磁盘

消息持久化会直接存入磁盘再同步到内存

配置消息非持久化与持久化再配置LazyQueue会直接存入磁盘

![](assets/RabbitMQ24.png)

## 消费者可靠性

**消费者确认机制：**

![](assets/RabbitMQ25.png)

**SpringAMQP实现的确认机制：**

![](assets/RabbitMQ26.png)

![](assets/RabbitMQ27.png)

**消费者失败重试机制：**

![](assets/RabbitMQ28.png)

![](assets/RabbitMQ29.png)

**RepublishMessageRecoverer处理失败消息：**

![](assets/RabbitMQ30.png)

![](assets/RabbitMQ31.png)

![](assets/RabbitMQ32.png)

**业务幂等性：**

![](assets/RabbitMQ33.png)

1.全局唯一ID

2.数据库事务＋乐观锁

3.分布式锁

## 延迟消息

生产者发送消息时指定一个时间，消费者不会立刻收到消息，而是在指定时间之后才收到消息

**死信交换机：**

![](assets/RabbitMQ34.png)

如果队列通过dead-letter-exchange属性指定了一个交换机，那么该队列中的死信机会投递到这个交换机中。这个交换机就为死信交换机

可以模拟延迟消息

![](assets/RabbitMQ35.png)

**延迟消息插件的使用：**

![](assets/RabbitMQ36.png)

![](assets/RabbitMQ37.png)





