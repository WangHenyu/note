# RabbitMQ

## MQ简介

<h4>同步与异步</h4>

- 同步 需要等待结果返回才能继续运行就是同步
- 异步 不需要等待结果返回就能继续运行就是异步

同步通讯的优点 时效性较强可以立即得到结果

同步通讯存在的问题

  1.耦合度高

  2.性能和吞吐能力低

  3.有额外的资源消耗

  4.有级联失败的问题

异步通讯可以解决同步通讯存在的问题

异步通讯的优点

  1.吞吐量提升

  2.故障隔离

  3.耦合度极低

  4.流量削峰



<h4>MQ的作用</h4>

MQ(message queue)消息队列,本质是个队列

- 流量削峰
- 应用解耦
- 异步处理



<h4>常见MQ对比</h4>

|            | RabbitMQ | ActiveMQ | RocketMQ | Kafka      |
| ---------- | -------- | -------- | -------- | ---------- |
| 社区       | Rabbit   | Apache   | Alibaba  | Apache     |
| 开发语言   | Erlang   | Java     | Java     | Scala&Java |
| 可用性     | 高       | 一般     | 高       | 高         |
| 单机吞吐量 | 一般     | 差       | 高       | 非常高     |
| 消息延迟   | 微秒级   | 毫秒级   | 毫秒级   | 毫秒以内   |
| 消息可靠性 | 高       | 一般     | 高       | 一般       |



<h4>RabbitMQ概念</h4>

四大核心角色

- `生产者`：生产消息
- `交换机`：负责消息的转发 不具备存储消息的能力
- `队列`：本质上就是消息缓冲区 存放等待被消费的消息
- `消费者`：消费消息

![image-20220831193943105](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220831193943105.png)

其他概念

- `Connection`

  生产者／消费者和RabbitMQ之间的TCP连接

- `Channel`

  Channel是在connection内部建立的逻辑连接

  Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销 

- `Binding`

  交换机和队列之间的虚拟连接

- `Virtualhost`

  虚拟主机 隔离不同租户的exchange、queue、消息的隔离

  

## MQ安装

官方下载地址http://www.rabbitmq.com/download.html

<h4>Linux安装</h4>

1.安装rabbitmq

~~~shell
rpm -ivh erlang-21.3-1.el7.x86_64.rpm
yum install socat -y
rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm
~~~



<h4>Docker安装</h4>

1.拉取镜像 `docker pull rabbitmq:3-management`

2.启动容器

~~~shell
docker run \
		-e RABBITMQ_DEFAULT_USER=why \
		-e RABBITMQ_DEFAULT_PASS=123 \
		--name mq1 \
		--hostname mq1 \
		-p 15672:15672 \
		-p 5672:5672 \
		-d \
 rabbitmq:3-management
~~~

3.访问`http://虚拟机IP:15672`



## 常用指令

1.服务相关

- 开机自启 `chkconfig rabbitmq-server on`

- 启动服务 `/sbin/service rabbitmq-server start`

- 查看服务 `/sbin/service rabbitmq-server status`

- 停止服务 `/sbin/service rabbitmq-server stop`

2.应用相关

- 关闭应用 `rabbitmqctl stop_app`
- 清空队列 `rabbitmqctl reset`
- 重启应用 `rabbitmqctl start_app`
- 查看队列 `rabbitmqctl list_queues`
- 清除队列中的消息 `rabbitmqctl -p <虚拟主机路径> purge_queue blue`

3.虚拟主机

- 创建虚拟主机 `rabbitmqctl add_vhost <虚拟主机路径>`
- 删除虚拟主机 `rabbitmqctl delete_vhost <虚拟主机路径>`
- 显示虚拟主机 `rabbitmqctl list_vhosts`
- 显示虚拟主机权限 `rabbitmqctl list_permissions -p <虚拟主机路径>`

4.用户相关命令

- 创建用户 `rabbitmqctl add_user <用户名> <密码>`
- 修改密码 `rabbitmqctl change_password <用户名> <新密码>`
- 删除用户 `rabbitmqctl delete_user <用户名>`
- 查看所有用户 `rabbitmqctl list_users`
- 设置用户角色 `rabbitmqctl set_user_tags <用户名> <角色>`

- 设置用户权限 `rabbitmqctl set_permissions -p <虚拟主机路径> <用户名> ".*" ".*" ".*"`

- 清除用户权限 `rabbitmqctl clear_permissions -p <虚拟主机路径> <用户名>`
- 列出用户权限 `rabbitmqctl list_user_permissions <用户名>`

5.开启WEB插件 `rabbitmq-plugins enable rabbitmq_management`







## HelloWorld

以官网提供的[HelloWorld](https://www.rabbitmq.com/tutorials/tutorial-one-java.html)为例,这里是详细的[官方教程](https://www.rabbitmq.com/getstarted.html) 

P表示生产者

C表示消费者

![image-20220901170550265](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220901170550265.png)

1.导入依赖

~~~xml
<!--rabbitmq 依赖客户端-->
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.8.0</version>
</dependency>
~~~



2.创建消息生产者

- 建立连接(连接工厂、connection、channel)

- 利用channel声明队列
- 利用channel向队列发送消息
- 关闭连接

~~~java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

public class Producer {
    private static final String QUEUE_NAME = "hello.queue";

    public static void main(String[] args) {
        /**
         * 创建连接工厂
         */
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.140.132");
        factory.setUsername("why");
        factory.setPassword("123");

        try {
            /**
             * 创建连接
             */
            Connection connection = factory.newConnection();
            Channel channel = connection.createChannel();
            /**
             * 声明队列
             * Queue.DeclareOk queueDeclare(String queue, boolean durable,
             * 				  boolean exclusive, boolean autoDelete,Map<String, Object> arguments);
             * 1.要将消息发送给哪个队列
             * 2.是否消息持久化
             * 3.是否支持多消费者
             * 4.是否自动删除队列
             * 5.其他参数
             */
            channel.queueDeclare(QUEUE_NAME,false,false,false,null);
            System.out.println("来任务啦,把订单信息存到数据库...");
            String message = "product='手机',price='5999',name='jack',";
            /**
             * 发送消息
             * void basicPublish(String exchange, String routingKey, BasicProperties props, byte[] body);
             * 1.交换机名称
             * 2.路由KEY
             * 3.其他参数
             * 4.消息体
             */
            channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
            System.out.println("消息发送完成");
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            // 关闭连接...
        }
    }
}
~~~



3.创建消息消费者

- 建立连接(连接工厂、connection、channel)
- 利用channel声明队列
- 利用channel声明队列

~~~java
import com.rabbitmq.client.*;

public class Consumer {
    private static final String QUEUE_NAME = "hello.queue";

    public static void main(String[] args) {
        /**
         * 创建连接工厂
         */
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.140.132");
        factory.setUsername("why");
        factory.setPassword("123");

        Connection connection = null;
        try {
            /**
             * 创建连接
             */
            connection = factory.newConnection();
            Channel channel = connection.createChannel();
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            /**
             * 接收到消息的回调：在这个回调函数中可以定义消息的处理逻辑
             * 1.消费者标识
             * 2.消息内容
             */
            DeliverCallback deliverCallback = (consumerTag, message) -> {
                String messageStr = new String(message.getBody());
                System.out.println("接收到消息了:" + messageStr);
                System.out.println("正在存入信息到数据库...");
            };
            /**
             * 取消消息消费的回调
             */
            CancelCallback cancelCallback = consumerTag -> System.out.println("消息消费被取消了");
            /**
             * 消费消息
             * String basicConsume(String queue, boolean autoAck,
             *					 DeliverCallback deliverCallback, CancelCallback cancelCallback);
             * 1.从哪个队列中消费消息
             * 2.消息消费成功后是否自动应答
             * 3.接收消息的回调
             * 4.取消消息消费的回调
             *
             */
            channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

<h4>Channel常用方法</h4>

~~~java
/**
 * 声明临时队列（无参表示声明的是临时队列）
 * 1.队列名称
 * 2.是否消息持久化
 * 3.是否支持多消费者
 * 4.是否自动删除队列
 * 5.其他参数
 */
queueDeclare(String queue, boolean durable, boolean exclusive, boolean autoDelete,Map<String, Object> arguments)
/**
 * 声明交换机
 * 1.交换机名称
 * 2.交换机类型
 */
exchangeDeclare(String exchange, BuiltinExchangeType type)
/**
 * 绑定交换机与队列
 * 1.队列名称
 * 2.交换机名称
 * 3.路由KEY
 */
queueBind(String queue, String exchange, String routingKey)
/**
 * 发送消息
 * 1.交换机名称
 * 2.路由KEY
 * 3.参数
 * 4.消息
 */
basicPublish(String exchange, String routingKey, BasicProperties props, byte[] body)
/**
 * 消费消息
 * 1.从哪个队列中消费消息
 * 2.消息消费成功后是否自动应答
 * 3.接收消息的回调
 * 4.取消消息消费的回调
 */
basicConsume(String queue, boolean autoAck, DeliverCallback deliverCallback, CancelCallback cancelCallback)
~~~





## SpringAMPQ

SpringAMQP是基于RabbitMQ封装的模板,并且还利用SpringBoot对其实现了自动装配,使用非常方便

SpringAMPQ的官方地址：https://spring.io/projects/spring-amqp

SpringAMPQ提供了三个功能

- <font color='red'>自动声明</font>队列、交换机、绑定关系
- 基于注解的监听器模式,异步接收消息
- 封装了RabbitTempalte用于发送消息

>使用SpringAMPQ实现helloworld案例

1.创建两个Boot项目

2.导入依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
~~~



3.添加配置

~~~yaml
spring:
  rabbitmq:
    host: 192.168.140.132
    virtual-host: /
    username: why
    password: 123
    port: 5672
# 15672: 网页管理端口
# 5672: AMPQ端口
~~~



4.生产者项目

~~~java
@SpringBootTest
class ApplicationTests {
    private static final String QUEUE_NAME = "work.queue";

    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    @Test
    void publishMessage() {
        /**
         * 发送消息
         * 1.队列名称
         * 2.消息体
         */
        rabbitTemplate.convertAndSend(QUEUE_NAME,"{name:andy,age:12}");
    }
}
~~~



5.消费者项目

~~~java
@Component
public class SpringRabbitMqListener {

    /**
     * 监听指定的队列
     * 队列中的消息按照此方法的逻辑执行
     */
    @RabbitListener(queues = "work.queue")
    public void listenQueueMessage(String message){
        
        System.out.println("消费者接收到消息："+ message);
    }
}
~~~





## WorkQueue

WorkQueue 工作队列又称任务队列（Task Queue）的主要思想是避免立即执行资源密集型任务

将多个消费者绑定到同个队列共同消费队列中的消息

当消息处理比较耗时的时候,生产消息的速度可能远远大于消费消息的速度,长此以往就会造成消息堆积

使用Work模型,多个消费者共同处理消息速度就大大提升了

![image-20220901215005072](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220901215005072.png)

1.生产者项目

~~~java
@SpringBootTest
class ApplicationTests {
    private static final String QUEUE_NAME = "work.queue";
    
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void publishMessageMulti() {
        /**
         * 向队列中不停地发送消息模拟消息堆积
         */
        for (int i = 0; i < 10; i++) {
            rabbitTemplate.convertAndSend(QUEUE_NAME,"message" + i);
            System.out.println("消息发送成功");
        }
    }
}
~~~



2.消费者项目

~~~java
@Component
public class SpringRabbitMqListener {

    @RabbitListener(queues = "work.queue")
    public void listenQueueMessageOne(String message) throws InterruptedException {
        System.out.println("消费者1接收到消息："+ message);
        Thread.sleep(20);
    }

    @RabbitListener(queues = "work.queue")
    public void listenQueueMessageTwo(String message) throws InterruptedException {
        System.out.println("消费者2接收到消息："+ message);
        Thread.sleep(1000);
    }
}
~~~



经过测试我们发现虽然两个消费者消费消息的时间不相同但是消费的信息数量是相同的

消息是被`轮询`的分配给消费者,显然这样的分配是有问题的,我们希望的是能者多劳

只需要添加下面配置就能实现`能者多劳`

~~~yaml
spring:
  rabbitmq:
    listener:
      simple:
        # 每次只获取1条消息,等消息处理完成才获取下个消息
        prefetch: 1
~~~



## 发布/订阅

RabbitMQ消息传递模型的核心思想是<font color='red'>生产者生产的消息从不会直接发送到队列</font>

实际上通常生产者甚至都不知道这些消息传递传递到了哪些队列中

发布订阅的模型如图

![](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220904212544081.png)

其中 P表示生产者 X表示交换机 C表是消费者

交换机的三种类型

- `Fanout`: 扇出(广播) 将消息转发给所有与它绑定的队列
- `Direct`: 直接(定向) 将消息转发给指定RoutingKey的队列
- `Topic`:  主题(组播) 把消息交给符合RoutingPattern的队列

<font color='red'>交换机只负责转发消息不具备存储消息的能力</font>,因此没有任何队列与交换机绑定或者没有符合路由规则的队列,那么消息将丢失

前面的案例没有使用交换机仍然能够将消息发送到队列是因为为我们使用的是默认交换机

> 交换机和队列到底由谁来声明?
>
> 1.生产者声明交换机,消费者声明队列和绑定关系（前提是消费者知道交换机）
>
> 2.生产者声明所有（交换机、队列、绑定关系）
>
> 3.消费者声明所有（交换机、队列、绑定关系）
>
> 详细原因可以参考[这篇文章](https://blog.csdn.net/u013174617/article/details/107412641)



### Fanout

> Spring提供了接口Exchang来表示所有不同类型的交换机,其具体实现类有
>
> - FanoutExchange 
> - DirectExchange
> - TopicExchange
> - HeaderExchange



本案例由生产者创建所有（交换机、队列、绑定关系）

`JavaBean方式`

~~~java
@Configuration
public class RabbitConfig {

    /**
     * 声明交换机
     * 1.交换机名称
     * 2.是否持久化默认为true
     * 3.是否自动删除
     * 4.其他参数
     */
    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange("why.fanout",false,false,null);
    }

    /**
     * 声明队列
     * 1.队列名称
     * 2.是否消息持久化
     * 3.是否支持多消费者
     * 4.是否自动删除队列
     * 5.其他参数
     */
    @Bean
    public Queue fanoutQueueOne(){
        return new Queue("fanout.queue1",false,false,false,null);
    }
    @Bean
    public Queue fanoutQueueTwo(){
        return new Queue("fanout.queue2",false,false,false,null);
    }

    /**
     * 绑定交换机与队列
     * 1.队列,从容器中自动注入
     * 2.交换机,从容器中自动注入
     */
    @Bean
    public Binding bindingQueueOne(Queue fanoutQueueOne,FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueueOne).to(fanoutExchange);
    }
    @Bean
    // 使用@Qualifier注解按名字注入
    public Binding bindingQueueTwo(@Qualifier("fanoutQueueTwo")Queue queue,
                                   @Qualifier("fanoutExchange")FanoutExchange exchange){
        return BindingBuilder.bind(queue).to(exchange);
    }
}

~~~



发送消息

~~~java
@SpringBootTest
class ApplicationTests {
    private static final String EXCHANGE_NAME = "why.fanout";

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void fanoutExchange(){
        String message = "{name:Andy,age:20}";
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,"",message);
    }
}
~~~



接收消息

~~~java
@Component
public class SpringRabbitMqListener {

    /**
     * 监听指定的队列
     * 队列中的消息按照此方法的逻辑执行
     */
    @RabbitListener(queues = "fanout.queue1")
    public void listenQueueMessageOne(String message) throws InterruptedException {
        System.out.println("消费者1接收到消息："+ message);
        Thread.sleep(20);
    }

    @RabbitListener(queues = "fanout.queue2")
    public void listenQueueMessageTwo(String message) throws InterruptedException {
        System.out.println("消费者2接收到消息："+ message);
        Thread.sleep(1000);
    }
}
~~~



### Direct

在Direct模式中

- 队列与交换机的绑定不能是任意绑定了,而是要指定一个 RoutingKey
- 消息的发送方在向Exchange发送消息时也必须指定消息的 RoutingKey
- Exchange不再把消息交给每个绑定的队列,而是根据消息的 Routing Key 进行判断



此案例由消费者创建所有（交换机、队列、绑定关系）

如果让消费者创建所有一定要让消费者先启动否则会造成消息丢失

如果交换机和队列已经存在那无所谓

`注解方式`

~~~java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "direct.queue1"),
        exchange = @Exchange(name = "why.direct",type = ExchangeTypes.DIRECT),
        key = {"red","blue"}
))
public void listenQueueMessageThree(String message) throws InterruptedException {
    System.out.println("消费者1接收到消息："+ message);
}

@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "direct.queue2"),
        exchange = @Exchange(name = "why.direct",type = ExchangeTypes.DIRECT),
        key = {"red","green"}
))
public void listenQueueMessageFour(String message) throws InterruptedException {
    System.out.println("消费者2接收到消息："+ message);
}
~~~



发送消息

~~~java
@Test
void directExchange(){
    final String EXCHANGE_NAME = "why.direct";
    String message = "{name:Andy,age:20}";
    rabbitTemplate.convertAndSend(EXCHANGE_NAME,"blue",message);
    rabbitTemplate.convertAndSend(EXCHANGE_NAME,"red",message);
}
~~~





### Topic

Topic模式可以根据RoutingKey把消息路由到不同的队列,RoutingKey使用通配符

- `#`: 匹配1个或多个词
- `*`: 匹配1个词

`china.#` 能够匹配 china.city 或 china.city.news

`china.*` 只能匹配 china.city



消费消息

~~~java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "topic.queue1"),
        exchange = @Exchange(name = "why.topic",type = ExchangeTypes.TOPIC),
        key = {"*.news"}
))
public void listenQueueMessageFive(String message) throws InterruptedException {
    System.out.println("消费者1接收到消息："+ message);
}

@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "topic.queue2"),
        exchange = @Exchange(name = "why.topic",type = ExchangeTypes.TOPIC),
        key = {"china.#"}
))
public void listenQueueMessageSix(String message) throws InterruptedException {
    System.out.println("消费者2接收到消息："+ message);
}
~~~



发送消息

~~~java
@Test
void topicExchange(){
    final String EXCHANGE_NAME = "why.topic";
    String message = "今日头条:xxxx";
    rabbitTemplate.convertAndSend(EXCHANGE_NAME,"china.news",message);
}
~~~



## 消息转换器

Spring会把你发送的消息序列化为字节发送给MQ,接收消息的时候还会把字节反序列化为Java对象

默认情况下Spring采用的序列化方式是JDK序列化,JDK序列化存在的问题

- 数据体积过大
- 有安全漏洞
- 可读性差

<h4>配置JSON转换器</h4>

引入依赖（生产者消费者都需要）

~~~xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.9.10</version>
</dependency>
~~~



配置消息转换器（生产者消费者都需要）

~~~java
@Bean
public MessageConverter jsonMessageConverter(){
    return new Jackson2JsonMessageConverter();
}
~~~



注意：配置了Jackson2JsonMessageConverter发送消息和接收消息所用的类型要相同





## 消息可靠性

在消息队列使用的过程中面临着很多实际问题需要思考

- 消息可靠性问题：如何确保发送的消息至少被消费1次
- 延迟消息问题：如何实现消息的延时投递
- 消息堆积问题：如何解决数百万消息堆积无法及时消费的问题
- 高可用问题：如何避免单点MQ故障导致不可用的问题



分析消息丢失的原因

- 发送时丢失

  1.生产者发送的消息没用送到交换机

  2.消息到达交换机但是没有送到队列

- MQ故障或宕机导致消息丢失

- 消费者接收到消息为完成消费就宕机导致消息丢失



RabbitMQ给出的解决方案

- 对于发送时丢失消息问题,MQ使用了`生产者确认`机制
- 对于MQ宕机导致消息丢失,MQ使用了`持久化`技术
- 对于消费时丢失消息问题,MQ使用了`消费者确认`机制和`失败重试`机制



### 生产者确认机制

RabbitMQ提供了生产者确认机制来避免消息发送到MQ过程中的丢失消息问题

这种机制必须为每个消息指定唯一ID以避免ack冲突,消息发送到MQ以后,MQ会返回结果给生产者,表示消息是否处理成功

- publisher-confirm 发送者确认

  1.消息成功投递到交换机返回ack

  2.消息未成功投递到交换机返回nack

- publisher-return 发送者回执

  1.消息成功投递到交换机但是没有路由到队列返回ack及路由失败原因



<h4>开启生产者确认机制</h4>

~~~yaml
spring:
  rabbitmq:
    # 开启publisher-confirm simple表示同步等待结果 correlated表示异步回调,MQ返回结果时会调用ConfirmCallback
    publisher-confirm-type: correlated
~~~



<h4>定义ConfirmCallback</h4>

ConfirmCallback可以在发送消息时指定,因为每个业务处理confirm成功或失败的逻辑不一定相同

方式1

~~~java
    @Test
    void testProducerConfirm() throws InterruptedException {
        final String EXCHANGE_NAME = "why.confirm";

        CorrelationData correlationData = new CorrelationData();
        // 设置全局唯一ID
        correlationData.setId(UUID.randomUUID().toString());

        // 消息发送成功的回调
        SuccessCallback<CorrelationData.Confirm> successCallback = result -> {
            if (result.isAck())
                System.out.println("消息发送成功"+correlationData.getId());
            else
                System.out.println("消息发送失败"+correlationData.getId()+"失败原因"+result.getReason());
        };
        // 消息发送异常的回调
        FailureCallback failureCallback = ex -> {
            System.out.println("消息发送异常"+correlationData.getId()+"异常原因"+ex.getMessage());
        };
        // 添加callback
        correlationData.getFuture().addCallback(successCallback,failureCallback);
        // 发送消息
        String message = "message:xxx";
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,"confirm",message,correlationData);
        // 模拟业务继续执行,等待ack回执
        Thread.sleep(1000);
    }
~~~

方式2

~~~java
    @Test
    void testProducerConfirm() throws InterruptedException {
        final String EXCHANGE_NAME = "why.confirm";
        
        CorrelationData correlationData = new CorrelationData();
        // 设置全局唯一ID
        correlationData.setId(UUID.randomUUID().toString());
        String message = "message:xxx";
        // 设置ConfirmCallback回调
        rabbitTemplate.setConfirmCallback((correlationData1, ack, cause) -> {
            if (ack)
                System.out.println("消息发送成功");
            else
                System.out.println("消息发送失败"+correlationData1.getId()+"失败原因"+cause);
        });
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,"confirm",message,correlationData);
        // 模拟业务继续执行,等待ack回执
        Thread.sleep(1000);
    }
~~~



我们去控制台删除交换机模拟交换机接收不到消息

~~~java
消息发送失败b68ba673-8aaf-4b53-beba-646381cbb914失败原因channel error; protocol method: #method<channel.close>(reply-code=404, reply-text=NOT_FOUND - no exchange 'why.confirm' in vhost '/', class-id=60, method-id=40)
~~~



### 回退消息

<h4>Mandatory参数</h4>

在仅开启了生产者确认机制的情况下,交换机接收到消息后会直接给消息生产者发送确认消息

如果发现该消息不可路由,那么消息会被直接丢弃,此时生产者是不知道消息被丢弃这个事件的

通过定义`Mandatory`参数可以在当消息传递过程中`不可达目的队列`时将消息`返回`给生产者

~~~yaml
spring:
  rabbitmq:
    # 开启publish-return功能
    publisher-returns: true
    template:
      # 定义消息路由失败时的策略 true调用ReturnCallback false直接丢弃消息
      mandatory: true

~~~



<h4>定义ReutrnCallback</h4>

由于每个RabbitTemplate只能配置1个ReturnCallback,因此需要在项目加载时配置

~~~java
@Configuration
public class CommonConfig implements ApplicationContextAware {
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        RabbitTemplate rabbitTemplate = applicationContext.getBean(RabbitTemplate.class);
        /**
         * 设置ReturnCallback回调
         * 1.消息体
         * 2.应答码
         * 3.消息失败原因
         * 4.路由键
         */
        rabbitTemplate.setReturnCallback((message, replyCode, replyText, exchange, routingKey) -> {
            // 消息投递失败记录日志(这里仅为了演示才输出到控制台)
            System.err.println(message.toString());
            System.err.println(replyCode);
            System.err.println(replyText);
            System.err.println(routingKey);
            // 如果有业务需要可以重新发送消息
        });
    }
}
~~~



### 备份交换机

使用了ReturnCallback我们可以获取到无法路由到队列的消息,然后手动处理这些无法路由的消息,然而这种处理方式并不优雅

1中优雅的方式就是给交换机设置备份交换机.当交换机接收到1条不可路由消息时,将会把这条消息转发到备份交换机中

由备份交换机来进行转发和处理,通常备份交换机的类型为 Fanout 



![image-20220907094939452](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220907094939452.png)



> 思考
>
> 为什么不能用死信交换机来实现？
>
> 因为是这些不可路由消息根本没有机会进入到队列,而死信交换机是由队列来指定的



声明交换机和队列

~~~java
@Configuration
public class RabbitMQConfig {

    private static final String EXCHANGE_NAME = "confirm.exchange";
    private static final String BACKUP_EXCHANGE_NAME = "backup.exchange";
    private static final String CONFIRM_QUEUE_NAME = "confirm.queue";
    private static final String BACKUP_QUEUE_NAME = "backup.queue";
    private static final String WARNING_QUEUE_NAME = "warning.queue";
    private static final String ROUTING_KEY = "key";

    @Bean
    public DirectExchange confirmExchange(){
        return ExchangeBuilder.directExchange(EXCHANGE_NAME)
                            .durable(true)
                            // 设置该交换机的备份交换机
                            .withArgument("alternate-exchange",BACKUP_EXCHANGE_NAME)
                            .build();
    }
    @Bean
    public Queue confirmQueue(){
        // 声明确认队列
        return new Queue(CONFIRM_QUEUE_NAME,true);
    }
    @Bean
    public Binding confirmBindingKey(DirectExchange confirmExchange,Queue confirmQueue){
        // 声明确认交换机个确认队列的绑定关系
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with(ROUTING_KEY);
    }

    @Bean
    public FanoutExchange backupExchange(){
        // 声明备份交换机
        return new FanoutExchange(BACKUP_EXCHANGE_NAME,true,false);
    }
    @Bean
    public Queue backupQueue(){
        // 声明备份队列
        return new Queue(BACKUP_QUEUE_NAME,true);
    }
    @Bean
    public Queue warningQueue(){
        // 声明警告队列
        return new Queue(WARNING_QUEUE_NAME,true);
    }
    @Bean
    public Binding backupBindingKey(FanoutExchange backupExchange,Queue backupQueue){
        return BindingBuilder.bind(backupQueue).to(backupExchange);
    }
    @Bean
    // 使用@Qualifier根据名字获取容器中的Bean 效果等同于上面代码
    public Binding warningBindingKey(@Qualifier("backupExchange") FanoutExchange exchange,
                                     @Qualifier("warningQueue") Queue queue){
        return BindingBuilder.bind(queue).to(exchange);
    }
}
~~~

发送消息

~~~java
@SpringBootTest
class BAckUpProducerApplicationTests {

    private static final String EXCHANGE_NAME = "confirm.exchange";
    private static final String ROUTING_KEY = "key";

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void testHasRoutingKey(){
        Message message = MessageBuilder.withBody("hello".getBytes())
                .build();

        CorrelationData correlationData = new CorrelationData();
        correlationData.setId(UUID.randomUUID().toString());
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,ROUTING_KEY,message,correlationData);
        System.out.println("消息已发送");
    }

    @Test
    void testNoRoutingKey(){
        Message message = MessageBuilder.withBody("hello".getBytes())
                .build();
        CorrelationData correlationData = new CorrelationData();
        correlationData.setId(UUID.randomUUID().toString());
        // 指定不存在的key 模拟消息无法送到队列
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,"abc",message,correlationData);
        System.out.println("消息已发送");
    }
}
~~~

普通消费者

~~~java
    private static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    @RabbitListener(queues = CONFIRM_QUEUE_NAME)
    public void confirmListener(Message message){
        System.out.println("确认消费者接收到来自确认队列的消息："+new String(message.getBody()));
    }
~~~

警告消费者

~~~java
    private static final String WARNING_QUEUE_NAME = "warning.queue";

    @RabbitListener(queues = WARNING_QUEUE_NAME)
    public void warningListener(Message message){
        System.out.println("警告消费者接收到来自警告队列的消息："+new String(message.getBody()));
    }
~~~

mandatory参数与备份交换机可以一起使用的时候 如果两者同时开启`备份交换机优先级高`





### 消息持久化

生产者确认可以确保消息投递到RabbitMQ队列中,但是在RabbitMQ收到消息后,突然宕机也可能导致消息丢失

因此想要确保消息在RabbitMQ中安全保存,必须要开启消息持久化机制

- 交换机持久化
- 队列持久化
- 消息持久化



<h4>交换机持久化</h4>

RabbitMQ中的交换机默认是非持久化的,MQ重启后就丢失

生产者指定交换机持久化

~~~java
    @Bean
    public FanoutExchange fanoutExchange(){
        /**
         * 指定交换机持久化
         */
        boolean durable = true;
        return new FanoutExchange("why.fanout",durable,false,null);
    }
~~~

消费者指定消息持久化

~~~java
    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(name = "durable.queue"),
            /**
             * 指定交换机持久化 durable = "true"
             */
            exchange = @Exchange(name = "why.durable",type = ExchangeTypes.TOPIC,durable = "true"),
            key = {"durable"}
    ))
    public void listenQueueMessageSeven(String message) throws InterruptedException {
        System.out.println("消费者接收到消息："+ message);
    }
~~~

> 事实上 由SpringAMPQ声明的交换机默认都是持久化的
>
> 在RabbitMQ控制台上持久化的交换机都会带上D标识



<h4>队列持久化</h4>

RabbitMQ中的队列默认是非持久化的,MQ重启后就丢失

生产者指定队列持久化

~~~java
    @Bean
    public Queue confirmQueue(){
        /**
         * 指定队列持久化
         */
        boolean durable = true;
        return new Queue("confirm.queue",durable);
    }
~~~



消费者指定队列持久化

~~~java
    @RabbitListener(bindings = @QueueBinding(
            /**
             * 指定队列持久化 durable = "true"
             */
            value = @Queue(name = "durable.queue",durable = "true"),
            exchange = @Exchange(name = "why.durable",type = ExchangeTypes.TOPIC,durable = "true"),
            key = {"durable"}
    ))
    public void listenQueueMessageSeven(String message) throws InterruptedException {
        System.out.println("消费者接收到消息："+ message);
    }
~~~

> 事实上 由SpringAMPQ声明的队列默认都是持久化的
>
> 在RabbitMQ控制台上持久化的队列都会带上D标识



<h4>消息持久化</h4>

使用SpringAMPQ发送消息时可以设置消息的属性（MessageProperties）指定deliver-mode

~~~java
    @Test
    void messageDurable(){
        final String EXCHANGE_NAME = "why.message.durable";
        final String ROUTING_KEY = "message";
        Message message = MessageBuilder
                // 设置消息体
                .withBody("message:xxx".getBytes())
                // 设置消息持久化
                .setDeliveryMode(MessageDeliveryMode.PERSISTENT)
                .build();

        CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
        // 发送消息
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,ROUTING_KEY,message,correlationData);
    }
~~~



### 消费者确认机制

RabbitMQ是阅后即焚机制,RabbitMQ确认消息被消费之后就会立即删除该消息

RabbitMQ是通过消费者回执来确认消费者是否成功处理消息,消费者获取消息后应该先RabbitMQ发送ack回执表明消息已经被处理

SpringAMPQ允许配置三种确认模式

- manual 手动ack 需要在业务代码结束hou调用API发送ack
- auto 自动ack 由Spring监测Listener代码是否抛出异常,没有异常者返回ack,抛出异常者返回nack
- none 关闭ack RabbitMQ假定消费者获取消息后会成功处理,因此消息投递后会立即被删除



<h4>配置none模式</h4>

这种模式的消息投递是不可靠的因此不建议使用

~~~yaml
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: none
~~~



<h4>配置auto模式</h4>

~~~yaml
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: auto
~~~

模拟出现异常

~~~java
    @RabbitListener(queues = "simple.queue")
    public void testAck(String message){
        System.out.println("消费者接收到消息："+ message);
        if(true)
            throw new RuntimeException("出异常了");
        System.out.println("消费完成");
    }
~~~

在异常处打上断点,启动项目

经过测试发现异常抛出后,此时消息的状态是Unacked（未确认状态）,当我们放行断点后消息的状态由Unacked恢复至Ready状态

这种情况下未确认的消息重新入队发给消费者,消费者再次出异常,消息由重新入队,无限循环下去会导致MQ消息处理飙升

如何解决？

可以利用Spring的retry机制,在消费者出现异常时利用本地重试而不是无限制的requeue到mq 队列



<h4>本地重试</h4>

添加配置

~~~yaml
spring:
  rabbitmq:
    listener:
      simple:
        acknowledge-mode: auto
        retry:
          # 开启消费者失败重试
          enabled: true
          # 初始的失败等待时间长1秒
          initial-interval: 1000
          # 失败等待时长的倍数
          # 下次等待时间 = multiplier * last-interval
          multiplier: 1
          # 最大重试次数
          max-attempts: 3
          # true无状态 false有状态,若业务包含事务则应该改为false
          stateless: true

~~~



重启消费者工程测试

在重试3次后SpringAMPQ抛出`AmqpRejectAndDontRequeueException`异常说明本地重试触发了

观察控制台发现消息被删除了



结论

- 开启本地重试时,消息处理过程中抛出异常不会requeue到队列而是在消费者本地重试 
- 重试达到最大次数后,Spring会返回ack 消息会被丢弃



<h4>失败策略</h4>

在之前的测试中开启重试模式后,重试次数耗尽如果消息依然失败则需要有MessageRecoverer接口来处理,它包含三种不同的实现：

- RejectAndDontRequeueRecoverer 重试次数耗尽后,直接reject然后丢弃消息（默认）
- ImmediateRequeueMessageRecoverer 重试次数耗尽后,返回nack,消息重新入队
- RepublishMessageRecoverer 重试次数耗尽后,将失败的消息投递到指定交换机



实现案例：将失败的消息投递到指定交换机

在消费者服务中声明处理失败消息的交换机和队列

~~~java
@Configuration
public class RabbitMQConfig {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Bean
    public DirectExchange errorMessageExchange(){
        return new DirectExchange("error.direct");
    }
    @Bean
    public Queue errorQueue(){
        return new Queue("error.queue", true);
    }
    @Bean
    public Binding errorBinding(Queue errorQueue, DirectExchange errorMessageExchange){
        return BindingBuilder.bind(errorQueue).to(errorMessageExchange).with("error");
    }

    @Bean
    public MessageRecoverer republishMessageRecoverer(){
        return new RepublishMessageRecoverer(rabbitTemplate,"error.direct","error");
    }
}
~~~



## 死信队列

死信 顾名思义就是无法被消费的信息,当生产者将消息投递到队列 消费者从队列中获取消息但是由于某些特殊的原因导致队列的消息无法被消费.

这样的消息如果没有后续的处理,就变成了死信,而存储死信的队列叫死信队列

当队列中的消息满足下列情况之一就会成为死信

- 消息TTL过期
- 队列满了,无法将消息投递到队列中
- 消息被拒绝（basic.reject或basic.nack 并且requeue=false）

如果包含死信的队列配置了`dead-letter-exchange`属性指定死信交换机,那么消息会被投递到这个死信交换机

![image-20220906100131062](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220906100131062.png)

<h4>消息被拒绝</h4>

消费者声明普通交换机和普通队列

消费者声明死信交换机和死信队列

~~~java
    @Bean
    public DirectExchange directExchange(){
        return new DirectExchange("simple.direct",true,false);
    }
    @Bean
    public Queue simpleQueue(){
        return  QueueBuilder.durable("simple.queue")
                // 指定死信交换机
                .deadLetterExchange("dl.direct")
                .deadLetterRoutingKey("dead")
                .build();
    }
    @Bean
    public Binding simpleBinding(DirectExchange directExchange,Queue simpleQueue){
        return BindingBuilder.bind(simpleQueue).to(directExchange).with("simple");
    }

    @Bean
    public DirectExchange deadExchange(){
        // 声明死信交换机
        return new DirectExchange("dl.direct",true,false);
    }
    @Bean
    public Queue deadQueue(){
        // 声明死信队列
        return new Queue("dl.queue",true);
    }
    @Bean
    public Binding deadBinding(DirectExchange deadExchange,Queue deadQueue){
        return BindingBuilder.bind(deadQueue).to(deadExchange).with("dead");
    }
~~~

开启手动确认ack

~~~yaml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1
        acknowledge-mode: manual
~~~

消费者拒绝消费消息

~~~java
    @RabbitListener(queues = "simple.queue")
    public void testAck(Message message, Channel channel) throws IOException {
        /* 获取消息在队列中的索引 */
        long deliveryTag = message.getMessageProperties().getDeliveryTag();
        /**
         * 拒绝消息
         * 1.消息在队列中的索引
         * 2.是否批量处理
         * 3.是否重新入队
         */
        channel.basicNack(deliveryTag,false,false);

        System.out.println("消费者拒绝接收到消息："+ new String(message.getBody()));
    }
~~~

发送消息

~~~java
    @Test
    void publishMessage() {
        String message = "{name:andy,age:12}";
        rabbitTemplate.convertAndSend("simple.direct","simple",message);
        System.out.println("消息发送成功");
    }
~~~



启动消费者,发送消息 由于消费者拒绝消费消息 所以消息被投递到死信队列

如果某个队列指定了死信交换机 会带有`DLX`和`DLK`标识



<h4>消息TTL过期</h4>

队列中的消息如果超时未消费,就会变成死信,而超时可分为两种

- 消息所在的队列设置了超时时间
- 消息本身设置了超时时间

![image-20220906144700047](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220906144700047.png)

<h5>队列设置超时</h5>

消费者声明普通交换机和普通队列并设置超时时间

消费者声明死信交换机和死信队列

~~~java
    @Bean
    public DirectExchange ttlExchange(){
        return new DirectExchange("ttl.direct",true,false);
    }
    @Bean
    public Queue ttlQueue(){
        return QueueBuilder.durable("ttl.queue")
                .deadLetterExchange("dl.ttl.exchange")
                .deadLetterRoutingKey("ttl")
                // 设置队列的超时时间10秒
                .ttl(10000)
                .build();
    }
    @Bean
    public Binding ttlBinding(DirectExchange ttlExchange,Queue ttlQueue){
        return BindingBuilder.bind(ttlQueue).to(ttlExchange).with("direct");
    }

    @Bean DirectExchange deadTtlExchange(){
        return new DirectExchange("dl.ttl.exchange",true,false);
    }
    @Bean
    public Queue deadTtlQueue(){
        return new Queue("dl.ttl.queue",true);
    }
    @Bean
    public Binding deadBinding(DirectExchange deadTtlExchange,Queue deadTtlQueue){
        return BindingBuilder.bind(deadTtlQueue).to(deadTtlExchange).with("ttl");
    }
~~~

给死信队列设置消费者

~~~java
    @RabbitListener(queues = "dl.ttl.queue")
    public void dlTtlMessage(Message message,Channel channel) throws InterruptedException, IOException {
        System.out.println("死信队列中的消息被消费了");
        // 手动确认ack
        channel.basicAck(message.getMessageProperties().getDeliveryTag(),false);
    }
~~~

发送消息

~~~java
    @Test
    void publishMessage() {
        String message = "{name:andy,age:12}";
        rabbitTemplate.convertAndSend("ttl.direct","direct",message);
        System.out.println("消息发送成功");
    }
~~~



我们不给ttl.queue设置消费者来模拟消息超时

经过测试发现10秒后消息被投递到了死信队列由死信队列的消费者消费



<h5>消息设置超时</h5>

~~~java
    @Test
    void publishTTLMessage(){
        // 创建消息
        Message message = MessageBuilder
                .withBody("{name:andy,age:12}".getBytes(StandardCharsets.UTF_8))
                .setExpiration("3000")
                .build();
        // 设置消息ID
        CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
        // 发送消息
        rabbitTemplate.convertAndSend("ttl.direct","direct",message,correlationData);
    }
~~~

经过测试发现虽然队列的超时时间是10秒但是消息只经过3秒就被投递到死信队列中去



## 延迟队列

利用TTL结合死信交换机就可以实现消费者延时接收消息的效果,这种消息模式被称为延时队列

延时队列的使用场景

- 延时发送信息
- 用户下单 用户在15分钟内未支付则自动取消
- 预约工作会议20分钟后自动通知所有参会人员

![image-20220906214032554](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220906214032554.png)

生产者声明所有交换机和队列

~~~java
@Configuration
public class RabbitMQConfig {
    private static final String EXCHANGE_NAME= "X";
    private static final String DEAD_EXCHANGE_NAME = "Y";
    private static final String QUEUE_A_NAME = "QA";
    private static final String QUEUE_B_NAME = "QB";
    private static final String QUEUE_D_NAME = "QD";
    private static final String ROUTING_KEY_A = "XA";
    private static final String ROUTING_KEY_B = "XB";
    private static final String ROUTING_KEY_D = "YD";

    @Bean
    public DirectExchange directExchangeX(){
        // 声明X交换机
        return  new DirectExchange(EXCHANGE_NAME,true,false);
    }
    @Bean
    public Queue queueA(){
        // QueueBuilder方式指定死信队列
        return QueueBuilder.durable(QUEUE_A_NAME)
                .deadLetterExchange(DEAD_EXCHANGE_NAME)
                .deadLetterRoutingKey(ROUTING_KEY_D)
                .ttl(10000)
                .build();
    }
    @Bean
    public Queue queueB(){
        // 使用参数指定死信队列
        Map<String,Object> args = new HashMap<>();
        args.put("x-dead-letter-exchange",DEAD_EXCHANGE_NAME);
        args.put("x-dead-letter-routing-key",ROUTING_KEY_D);
        args.put("x-message-ttl",40000);
        return new Queue(QUEUE_B_NAME,true,false,false,args);
        // QueueBuilder也可以传入参数
        // return QueueBuilder.durable(QUEUE_B_NAME).withArguments(args).build();
    }
    @Bean
    public Binding xToQA(DirectExchange directExchangeX,Queue queueA){
        // 声明交换机X与队列QA绑定关系
        return BindingBuilder.bind(queueA).to(directExchangeX).with(ROUTING_KEY_A);
    }
    @Bean
    public Binding xToQB(DirectExchange directExchangeX,Queue queueB){
        // 声明交换机X与队列QB绑定关系
        return BindingBuilder.bind(queueB).to(directExchangeX).with(ROUTING_KEY_B);
    }

//下面的可以在生产者中声明也能在消费者中声明
//    @Bean
//    public DirectExchange deadExchange(){
//        // 声明死信队列Y
//        return new DirectExchange(DEAD_EXCHANGE_NAME,true,false);
//    }
//    @Bean
//    public Queue queueD(){
//        return new Queue(QUEUE_D_NAME,true);
//    }
//    @Bean
//    public Binding yToQD(DirectExchange deadExchange,Queue queueD){
//        // 声明死信交换机Y和死信队列DQ的绑定关系
//        return BindingBuilder.bind(queueD).to(deadExchange).with(ROUTING_KEY_D);
//    }
}
~~~

演示在消费者中声明

~~~java
    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(name = "QD",durable = "true"),
            exchange = @Exchange(name = "Y",type = ExchangeTypes.TOPIC,durable = "true"),
            key = {"YD"}
    ))
    public void deadQueueConsumer(String message){
        System.out.println("收到死信队列消息" + message);
    }
~~~



发送消息

~~~java
@SpringBootTest
class ConsumerApplicationTests {
    private static final String EXCHANGE_NAME= "X";
    private static final String ROUTING_KEY_A = "XA";
    private static final String ROUTING_KEY_B = "XB";

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void sendMessage(){
        String messageTen = "来自ttl为10S的的队列的消息：message-xxx";
        String messageFour = "来自ttl为40S的的队列的消息：message-xxx";
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,ROUTING_KEY_A,messageTen);
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,ROUTING_KEY_B,messageFour);
        System.out.println("消息发送成功");
    }
}
~~~

测试结果10秒中后消费投递到QA的消息 40秒后消费投递到QB的消息



### 延时队列优化

可以不设置队列的TTL 而是设置消息的TTL 由生产者决定延迟的时间

![image-20220906231917848](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220906231917848.png)

生产者添加代码

~~~java
    @Bean
    public Queue queueC(){
        return QueueBuilder.durable("QC")
                .deadLetterExchange("Y")
                .deadLetterRoutingKey("YD")
                .build();
    }
    @Bean Binding xToQC(DirectExchange directExchangeX,Queue queueC){
        return  BindingBuilder.bind(queueC).to(directExchangeX).with("XC");
    }
~~~

发送消息

~~~java
    @Test
    void sendTTLMessage(){
        String messageStr = "来自队列QC的消息：message-xxx";
        Message message = MessageBuilder
                            .withBody(messageStr.getBytes())
                            // 设置消息过期时间
                            .setExpiration("20000")
                            .build();
        rabbitTemplate.convertAndSend(EXCHANGE_NAME,"XC",message);
        
        System.out.println("消息发送成功");
    }
~~~



### 延时插件

下载插件 https://www.rabbitmq.com/community-plugins.html

用法参考 https://blog.rabbitmq.com/posts/2015/04/scheduling-messages-with-rabbitmq

Docker中安装插件请参考资料

![image-20220907091408748](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220907091408748.png)

<h4>DelayExchange原理</h4>

DelayExchange需要将1个交换机声明为delayed类型，具体流程如下

  1.delayExchange接收到消息

  2.判断消息是否具备x-delay属性

  3.如果有x-delay属性说明是延迟消息,持久化到硬盘,读取x-delay值作为延迟时间

  4.返回routing not found结果给消息发送者

  5.x-delay时间到期后，重新投递消息到指定队列



<h4>声明延时交换机</h4>

注解方式

~~~java
    @RabbitListener(bindings = @QueueBinding(
            value =  @Queue(name = "delay.queue",durable = "true"),
            exchange = @Exchange(name = "delay.dorect",delayed = "true"),
            key = "delay"
    ))
    public void listenDelayQueue(String message){
        System.out.println(message);
    }
~~~

Bean方式

~~~java
    @Bean
    public DirectExchange delayExchange(){
        return ExchangeBuilder
                .directExchange("delay.direct")
                .delayed() //设置delay属性为true
                .durable(true)
                .build();
    }
~~~

发送消息

~~~java
    @Test
    public void sendDelayMessage(){
        Message message = MessageBuilder.withBody("hello".getBytes())
                .setHeader("x-delay", 10000)
                .build();
        CorrelationData correlationData = new CorrelationData(UUID.randomUUID().toString());
        // 发送消息
        rabbitTemplate.convertAndSend("delay.direct","delay",message,correlationData);
    }
~~~



延迟队列插件的使用步骤

- 声明延时交换机（delayed属性为true）

- 发送消息时添加x-delay头值为超时时间



## 其他知识

### 惰性队列

> 默认情况下,当生产者将消息发送到RabbitMQ的时候队列中的消息会尽可能的存储在内存之中,这样可以更加快速的将消息发送给消费者.
>
> 即使是持久化的消息,在被写入磁盘的同时也会在内存中驻留1份备份.
>
> 当RabbitMQ需要释放内存的时候会将内存中的消息换页至磁盘中,这个操作会耗费较长的时间也会阻塞队列的操作进而无法接收新的消息.

从RabbitMQ3.6.0开始引入了惰性队列,惰性队列会尽可能的将消息存入磁盘中而在消费者消费到相应的消息时才会被加载到内存中

这样设置的目的是支持更长的消息队列,惰性队列的特征

- 接收到消息后直接存入磁盘而非内存
- 消费者要消费消息时才会从磁盘中读取并加载到内存
- 支持数百万条的消息存储



<h4>声明惰性队列</h4>

基于Bean声明惰性队列

~~~java
    @Bean
    public Queue backupQueue(){
        return QueueBuilder.durable(BACKUP_QUEUE_NAME)
                            // 将x-queue-mode设置为lazy,声明惰性队列
                            .lazy()
                            .build();
        // 与上面代码效果相同
        // Map<String,Object> arguements = new HashMap<>();
        // arguements.put("x-queue-mode","lazy");
        // return QueueBuilder.durable(BACKUP_QUEUE_NAME)
        //                    .withArguments(arguements)
        //                    .build();
    }
~~~



基于注解声明惰性队列

~~~java
    @RabbitListener(queuesToDeclare = @Queue(
            name = BACKUP_QUEUE_NAME,
            durable = "true",
            // 声明惰性队列
            arguments = @Argument(name = "x-queue-mode",value = "lazy")
    ))
    public void backupExchange(Message message){
        // 消费逻辑...
    }
~~~



惰性队列在控制台上有Args标签,鼠标移到标点上就会显示x-queue-mode:lazy

惰性队列的优点

- 基于磁盘存储,消息上限高 可解决消息堆积问题
- 没有间歇性的页面切换,性能比较稳定

惰性队列的缺点

- 基于磁盘存储,消息时效性会降低
- 性能受限于磁盘的IO

### 幂等性

幂等性就是1个方法短时间内被多次调用但是产生的结果和只调用1次的结果相同

思考这样1种情况：

  消费者在消费MQ中的消息时,MQ已把消息发送给消费者,消费者在给MQ返回ack时网络中断,故MQ未收到确认信息,该条消息会重新发给其他的消费者

或者在网络重连后再次发送给该消费者,但实际上该消费者已成功消费了该条消息,造成消费者消费了重复的消息



<h4>解决消息重复消费</h4>

在海量订单生成的业务高峰期,生产端有可能就会重复发生了消息,这时候消费端就要实现幂等性,这就意味着我们的消息永远不会被消费多

业界主流的幂等性有两种操作:

- a.唯一ID+指纹码机制,利用数据库主键去重
- b.利用 redis 的原子性去实现(setnx)
- 

### 优先级队列

RabbitMQ在3.5.0版本的时候提供了优先级队列的实现.客户端通过配置队列的`x-max-priority`参数的方式设置1个队列支持的最大优先级

优先级最大值为255、最小值为0（默认值）推荐1～10

生产者可以通过设置Basic.Properties的priority属性设置消息的优先级（值越大优先级越高）

优先级越高越先被消费者消费,但是带来的内存、磁盘、CPU开销越高

> 如果消费者的消费速度远低于生产者生产消息的速度、Broker有消息积压的情况下,对消息设置的优先级才有意义

Bean方式声明优先级队列

~~~java
    @Bean
    public Queue backupQueue(){

        return QueueBuilder.durable(BACKUP_QUEUE_NAME)
						  // 设置优先级队列
                            .maxPriority(10)
                            .build();
        // 与上面代码效果相同
        // Map<String,Object> arguements = new HashMap<>();
        // arguements.put("x-max-priority",10);
        // return QueueBuilder.durable(BACKUP_QUEUE_NAME)
        //                    .withArguments(arguements)
        //                    .build();
    }
~~~



注解方式声明优先级队列

~~~java
    @RabbitListener(queuesToDeclare = @Queue(
            name = BACKUP_QUEUE_NAME,
            durable = "true",
            // 声明优先级队列
            arguments = @Argument(name = "x-max-priority",value = 10)
    ))
    public void backupExchange(Message message){
        // 消费逻辑...
    }
~~~



### Queue属性

<h5>Queue常用属性</h5>

- `x-message-ttl` 队列中所有消息的过期时间

- `x-expires` 队列生存期(毫秒)内没有被使用就会自动删除

- `x-overflow` 队列的处于ready状态存储消息的个数或消息占用的容量超过设定值后的处理策略,有3个值

​			  drop-head 删除头部信息

​			  reject-publish 拒绝接收消息

​			  reject-publish-dlx 拒绝消息并将其发送消息到死信交换机

- `x-single-active-consumer` 是否是单消费者消费

- `x-dead-letter-exchange` 指定死信交换机

- `x-dead-letter-routing-key` 指定死信交换机根据哪个路由KEY转发消息

- `x-max-length` 队列的最大条数

- `x-max-length-bytes` 队列中可以存储处于ready状态消息占用内存的大小

- `x-queue-mode` 有两个值lazy为惰性队列default为普通队列

- `x-queue-master-locator` 将队列设置为主位置模式 确定在节点集群上声明队列主节点所在的规

- `x-max-priority` 队列支持优先级(0-255)值越大优先级越高



<h5>Exchange常用属性</h5>

- `alternate-exchange` 指定该交换机的备份交换机



### Message详解

消息本质上就是一段数据,由Properties和Payload(body)组成

![image-20220907175758790](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220907175758790.png)

1.Delivery mode 是否持久化,如果未设置持久化,转发到queue中并未消费则重启服务或者服务宕机则消息丢失

2.Headers 头信息,是由一个或多个健值对组成的,当固定的Properties不满足我们需要的时候,可以自己扩展

3.Properties

![image-20220907175821406](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220907175821406.png)

- `content_type` 传输协议
- `content_encoding` 编码方式
- `priority` 优先级
- `correlation_id` rpc属性，请求的唯一标识。
- `reply_to` rpc属性，
- `xpiration` 消息的过期时间
- `message_id` 消息的id
- `timestamp` 消息的时间戳
   ...

4.Payload 消息体



## MQ集群

待写...

## 疑问

谁来创建交换机、队列、绑定关系?

- 1.生产者声明交换机,消费者声明队列和绑定关系（前提是消费者知道交换机）

- 2.生产者声明所有（交换机、队列、绑定关系）

- 3.消费者声明所有（交换机、队列、绑定关系）

创建交换机、队列、绑定关系有几种方式?

- Bean：new xxx 和 xxxBuilder

- 注解：@RabbitListener

设置死信交换机有几种方式?

- QueueBuilder的deadLetterExchange方法

- 使用x-dead-letter-exchange参数

  1.new Queue的时候传入map参数

  2.QueueBuilder的withArguments
  
  

配置Jackson2JsonMessageConverter无法转化Message?

为什么我的MQ是application/octet-stream?