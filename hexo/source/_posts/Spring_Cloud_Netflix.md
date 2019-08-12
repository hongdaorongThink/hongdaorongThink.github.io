# Spring_Cloud_Netflix

[1]: https://zhuanlan.zhihu.com/p/26472547

[2]: http://blog.didispace.com/spring-cloud-starter-dalston-1/

## 一、Spring Cloud Netflix

该项目是Spring Cloud的核心子项目，是对Netflix公司一系列开源产品的封装。它为Spring Boot应用提供了自配置的整合，只需要通过一些简单的注解，就可以快速地在Spring Cloud的应用中使用起来。


它主要提供的模块包括：

    服务发现注册（Eureka）
    客户端负载均衡（Ribbon）
    断路器（Hystrix）
    智能路由（Zuul）

## 二、服务注册和服务发现

调用关系说明：

    1.服务提供者在启动时，向注册中心注册自己提供的服务。
    2.服务消费者在启动时，向注册中心订阅自己所需的服务。
    3.注册中心返回服务提供者地址给消费者。
    4.服务消费者从提供者地址中调用消费者。

注意！ 下面的服务端指：注册中心，客户端指：提供者和消费者

## 三、如何使用Eureka进行服务注册和发现

### 创建“服务注册中心”

创建一个基础的Spring Boot工程，命名为eureka-server，并在pom.xml中引入需要的依赖内容：

```xml 
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.4.RELEASE</version>
    <relativePath/>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-dependencies</artifactId>
           <version>Dalston.SR1</version>
           <type>pom</type>
           <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

服务端添加配置, 在默认设置下，该服务注册中心也会将自己作为客户端来尝试注册它自己，所以我们需要禁用它的客户端注册行为，只需要在application.properties配置文件中增加如下信息：

```properties
spring.application.name=eureka-server
server.port=1001

eureka.instance.hostname=localhost
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

3、服务端添加注解

通过@EnableEurekaServer注解启动一个服务注册中心提供给其他应用进行对话。这一步非常的简单，只需要在一个普通的Spring Boot应用中添加这个注解就能开启此功能，比如下面的例子：

```java 
@EnableEurekaServer
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        new SpringApplicationBuilder(Application.class)
                    .web(true).run(args);
    }
}
```

为了与后续要进行注册的服务区分，这里将服务注册中心的端口通过server.port属性设置为1001。启动工程后，访问：http://localhost:1001/，可以看到下面的页面，其中还没有发现任何服务。



### 创建“服务提供方”

下面我们创建提供服务的客户端，并向服务注册中心注册自己。本文我们主要介绍服务的注册与发现，所以我们不妨在服务提供方中尝试着提供一个接口来获取当前所有的服务信息。

首先，创建一个基本的Spring Boot应用。命名为eureka-client，在pom.xml中，加入如下配置：

```xml 
<parent> 
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.4.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-dependencies</artifactId>
           <version>Dalston.SR1</version>
           <type>pom</type>
           <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

其次，实现/dc请求处理接口，通过DiscoveryClient对象，在日志中打印出服务实例的相关内容。

```java 
@RestController
public class DcController {

    @Autowired
    DiscoveryClient discoveryClient;

    @GetMapping("/dc")
    public String dc() {
        String services = "Services: " + discoveryClient.getServices();
        System.out.println(services);
        return services;
    }

}
```

最后在应用主类中通过加上@EnableDiscoveryClient注解，该注解能激活Eureka中的DiscoveryClient实现，这样才能实现Controller中对服务信息的输出。

```java 
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        new SpringApplicationBuilder(
            ComputeServiceApplication.class)
            .web(true).run(args);
    }
}
```

我们在完成了服务内容的实现之后，再继续对application.properties做一些配置工作，具体如下：

提供者

```properties
spring.application.name=eureka-client
server.port=2001
eureka.client.serviceUrl.defaultZone=http://localhost:1001/eureka/
```

通过spring.application.name属性，我们可以指定微服务的名称后续在调用的时候只需要使用该名称就可以进行服务的访问。eureka.client.serviceUrl.defaultZone属性对应服务注册中心的配置内容，指定服务注册中心的位置。为了在本机上测试区分服务提供方和服务注册中心，使用server.port属性设置不同的端口。

启动该工程后，再次访问：http://localhost:1001/。可以如下图内容，我们定义的服务被成功注册了。

其中，方括号中的eureka-client就是通过Spring Cloud定义的DiscoveryClient接口在eureka的实现中获取到的所有服务清单。由于Spring Cloud在服务发现这一层做了非常好的抽象，所以，对于上面的程序，我们可以无缝的从eureka的服务治理体系切换到consul的服务治理体系中区。


