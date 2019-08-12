# Spring_Cloud_Bus

[1]: https://segmentfault.com/a/1190000011718099


Spring Cloud Bus可以将分布式系统的节点与轻量级消息代理链接，然后可以实现广播状态更改（例如配置更改）或广播其他管理指令。Spring Cloud Bus就像一个分布式执行器，用于扩展的Spring Boot应用程序，但也可以用作应用程序之间的通信通道。那么这里就涉及到了消息代理，目前流行的消息代理中间件有不少，Spring Cloud Bus支持RabbitMQ和Kafka，本文我们主要来看看RabbitMQ的基本使用。


工程创建

首先我们来创建一个普通的Spring Boot工程，然后添加如下依赖：

```xml 
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```


属性配置

接下来在application.properties中配置RabbitMQ的连接信息，如下：

```properties 
spring.application.name=rabbitmq-hello
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=sang
spring.rabbitmq.password=123456
server.port=2009
```

这里我们分别配置了RabbitMQ的地址为localhost，端口为5672（注意这里没写错，web管理端端口是15672），用户名和密码则是我们刚刚创建出来的（也可以使用默认的guest）。


创建消息生产者

发送消息我们有一个现成的封装好的对象AmqpTemplate，通过AmqpTemplate我们可以直接向某一个消息队列发送消息，消息生产者的定义方式如下：

```java 
@Component
public class Sender {
    @Autowired
    private AmqpTemplate rabbitTemplate;
    public void send() {
        String msg = "hello rabbitmq:"+new Date();
        System.out.println("Sender:"+msg);
        this.rabbitTemplate.convertAndSend("hello", msg);
    }
}
```


注入AmqpTemplate，然后利用AmqpTemplate向一个名为hello的消息队列中发送消息。

创建消息消费者

```java
@Component
@RabbitListener(queues = "hello")
public class Receiver {
    @RabbitHandler
    public void process(String msg) {
        System.out.println("Receiver:"+msg);
    }
}
```


@RabbitListener(queues = "hello")注解表示该消息消费者监听hello这个消息队列，@RabbitHandler注解则表示process方法是用来处理接收到的消息的，我们这里收到消息后直接打印即可。




配置消息队列Bean

创建一个名为hello的消息队列。

```java
@Configuration
public class RabbitConfig {
    @Bean
    public Queue helloQueue() {
        return new Queue("hello");
    }
}
```




测试

创建单元测试类，用来发送消息，如下：

```java 
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = RabbitmqHelloApplication.class)
public class RabbitmqHelloApplicationTests {

    @Autowired
    private Sender sender;

    @Test
    public void contextLoads() {
        sender.send();
    }
}
```




这个表示程序已经创建了一个访问RabbitMQ的连接，此时在RabbitMQ的web管理面板中，我们也可以看到连接信息，如下： 


