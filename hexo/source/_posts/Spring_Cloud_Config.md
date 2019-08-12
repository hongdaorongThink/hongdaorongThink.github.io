# Spring_Cloud_Config

[1]: https://www.cnblogs.com/shamo89/p/8016908.html
[2]: http://download.csdn.net/download/u013081610/10152869

## 一、简介

在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。市面上开源的配置中心有很多，BAT每家都出过，360的QConf、淘宝的diamond、百度的disconf都是解决这类问题。国外也有很多开源的配置中心Apache的Apache Commons Configuration、owner、cfg4j等等。

在Spring Cloud中，有分布式配置中心组件spring cloud config ，它支持配置服务放在配置服务的内存中（即本地），也支持放在远程Git仓库中。在spring cloud config 组件中，分两个角色，一是config server，二是config client。


一个配置中心提供的核心功能

    提供服务端和客户端支持
    集中管理各环境的配置文件
    配置文件修改之后，可以快速的生效
    可以进行版本管理
    支持大的并发查询
    支持各种语言

Spring Cloud Config可以完美的支持以上所有的需求。

Spring Cloud Config项目是一个解决分布式系统的配置管理方案。它包含了Client和Server两个部分，server提供配置文件的存储、以接口的形式将配置文件的内容提供出去，client通过接口获取数据、并依据此数据初始化自己的应用。Spring cloud使用git或svn存放配置文件，默认情况下使用git，我们先以git为例做一套示例。


## 二、构建Config Server

创建一个spring-boot项目，取名为config-server,pom.xml中引入依赖:

```xml 
<dependencies>
      <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>

        <!--表示为web工程-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--暴露各种指标  貌似是必须的  -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

      
    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
  </dependencies>
```

新建入口类BootApplication：

```java 
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;


@SpringBootApplication
@EnableConfigServer
public class BootApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootApplication.class, args);
    }
}
```


application.properties：

```properties
server.port=7010
spring.cloud.config.server.default-application-name=config-server

# 配置git仓库地址
spring.cloud.config.server.git.uri=https://github.com/s***w*/myspringcloudconfig
# 配置仓库路径
spring.cloud.config.server.git.search-paths=myconfigpath
# 配置仓库的分支
spring.cloud.config.label=master
# 访问git仓库的用户名
spring.cloud.config.server.git.username=xxxxoooo
# 访问git仓库的用户密码 如果Git仓库为公开仓库，可以不填写用户名和密码，如果是私有仓库需要填写
spring.cloud.config.server.git.password=xxxxoooo
```


远程仓库https://github.com/shaweiwei/myspringcloudconfig/ 中有个文件config-client-dev.properties文件中有一个属性：

```properties
myww=myww version 2
```


启动程序：访问http://localhost:7010/myww/dev


证明配置服务中心可以从远程程序获取配置信息。

http请求地址和资源文件映射如下:

    /{application}/{profile}[/{label}]
    /{application}-{profile}.yml
    /{label}/{application}-{profile}.yml
    /{application}-{profile}.properties
    /{label}/{application}-{profile}.properties


## 三、构建一个config client

重新创建一个springboot项目，取名为config-client,其pom文件引入依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```




其配置文件bootstrap.properties：

```properties
# 和git里的文件名对应
spring.application.name=config-client
# 远程仓库的分支
spring.cloud.config.label=master
# dev 开发环境配置文件 |  test 测试环境  |  pro 正式环境
# 和git里的文件名对应
spring.cloud.config.profile=dev
# 指明配置服务中心的网址
spring.cloud.config.uri= http://localhost:7010/
server.port=7020
```



程序的入口类，写一个API接口“／hi”，返回从配置中心读取的foo变量的值，代码如下：


```java 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;


@SpringBootApplication
@RestController
public class BootApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootApplication.class, args);
    }
    
    @Value("${myww}") // git配置文件里的key
    String myww;
    
    @RequestMapping(value = "/hi")
    public String hi(){
        return myww;
    }
    
}
```


打开网址访问：http://localhost:7020/hi，网页显示：

myww version 2

这就说明，config-client从config-server获取了foo的属性，而config-server是从git仓库读取的,如图：

## 四、扩展

我们在进行一些小实验，手动修改service-config-dev.properties中配置信息提交到github,再次请求会看到还是用的旧的配置，这是为什么？因为spirngboot项目只有在启动的时候才会获取配置文件的值，修改github信息后，client端并没有再次去获取，所以导致这个问题。如何去解决这个问题呢？

