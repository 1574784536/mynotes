### springcloud

##### 服务注册中心

###### 什么是服务治理？

SpringCloud封装了Netflix分公司开发的Eureka模块来实现服务治理

在传统的rpc远程调用框架中，管理每个服务于服务之间依赖关系比较复杂，管理麻烦，所以需要使用服务治理，管理服务于服务之间的依赖关系，可以实现服务调用，负载均衡，容错等，实现服务发现与注册。

###### Eureka

​	Eureka是NetFlix开发的服务框架，本身是基于Rest的服务，主要用于定位在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在子项目spring-cloud-netflix中，以实现SpringCloud服务发现功能

什么是服务注册于发现？

Eureka采用了CS的设计架构，Eureka Server作为服务的注册功能的服务器，它是服务注册中心，而系统的其他微服务，使用Eureka的客户端连接到Eureka Serverb并维持心跳检测，这样系统维护人员就可以通过Eureka Server来监控系统各个服务是否正常运行。

![image-20200629214942324](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200629214942324.png)

服务注册：将服务信息注册到注册中心，

服务发现：从注册中心上获取服务信息，实质：存key服务名，获取value调用地址

1、先启动eureka注册中心，

2、启动服务提供者，

3、启动后会把自身的信息（比如服务地址以及别名注册中心获取实际的RPC远程调用）

5、消费者获取调用地址后，底层实际是利用HttpClient技术实现远程调用

6、消费者获取服务地址后会缓存本地jvm中，默认每间隔30秒更新服务调用地址。

微服务RPC远程服务调用的核心是高可用，所以要搭建Eureka注册中心集群，实现负载均衡+故障容错

Server端

```xml
<!-- eureka-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--监控-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

```yml
server:
  port: 7001
eureka:
  instance:
    # eureka服务端的实例名称
    hostname: http://localhost:7001
  client:
    # false表示不向注册中心注册自己
    register-with-eureka: false
    #false表示自己就是注册中心，我的职责就是维护服务器实例，并不需要检索服务
    fetch-registry: false
    service-url:
      #设置eureka server交互地址查询服务和注册中心服务都需要依赖这个地址
      #单机defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      #相互注册
      defaultZone: http://localhost:7002/eureka/
#      defaultZone: http://localhost:7001
  server:
    #关闭自我保护模式，保证不可用服务被及时删除
    enable-self-preservation: false
    eviction-interval-timer-in-ms: 2000

```

```java
package edu.nf.cloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

/**
 * @author YXD
 * @date 2020/7/1
 */
@SpringBootApplication
@EnableEurekaServer
public class EurekaServer7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServer7001.class,args);
    }
}
```

![image-20200701220807574](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200701220807574.png)

Client端

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--监控-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
<!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
<dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <!--如果没写版本,从父层面找,找到了就直接用,全局统一-->
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

```yml
server:
  port: 8001
spring:
  application:
    #服务名称
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mytest?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
    username: root
    password: 123456
mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: edu.nf.cloud.entity

# eureka配置
eureka:
  client:
    # 表示将自己注册进eureka server，默认为true
    register-with-eureka: true
    # 是否从eureka server抓去已有的注册信息，默认为true，单节点无所谓，集群必须为true才能配合Ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # 单机版
#      defaultZone: http://localhost:7001/eureka
      # 集群版
      defaultZone: http://localhost:7001/eureka,http://localhost:7002/eureka
  instance:
    instance-id: payment8001
    # 访问路径可以显示ip
    prefer-ip-address: true
    # eureka向服务端发送心跳检测，单位为秒(默认30秒)
    lease-renewal-interval-in-seconds: 1
    # eureka服务端收到最后一次心跳后等待时间上线，单位为秒（默认为90秒） 超时将剔除服务
    lease-expiration-duration-in-seconds: 2
```

```java
package edu.nf.cloud;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @author YXD
 * @date 2020/6/29
 */
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
@MapperScan("edu.nf.cloud.dao")
public class Payment8001 {
    public static void main(String[] args) {
        SpringApplication.run(Payment8001.class,args);
    }
}
```

![image-20200701220746165](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200701220746165.png)

Consumer

```xml
<!-- 包含了sleuth zipkin 数据链路追踪-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>edu.nf.cloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--监控-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
```

```yml
server:
  port: 80
spring:
  application:
    name: cloud-order-service
eureka:
  instance:
    instance-id: order-server80
  client:
    #true表示将自己注册进eureka server，默认为true
    register-with-eureka: true
    #是否将eureka server抓取已有的注册信息，默认为true，单节点无所谓，集群必须为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      #单机
      defaultZone: http://localhost:7001/eureka
      #集群
      #defaultZone: http://localhost:7001/eureka,http://localhost:7002/eureka
```

```java
package edu.nf.cloud.config;

//import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

/**
 * @author YXD
 * @date 2020/6/29
 */
@Configuration
public class ApplicationConfig {
    @Bean
//    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

```java
package edu.nf.cloud.controller;


import edu.nf.cloud.entity.Payment;
//import edu.nf.cloud.lb.LoadBalancer;
import edu.nf.cloud.vo.BaseResult;
import edu.nf.cloud.vo.ResponseVO;
import org.springframework.beans.factory.annotation.Autowired;
//import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.client.RestTemplate;

/**
 * @author YXD
 * @date 2020/6/29
 */
@RestController
public class OrderController extends BaseResult {
    private static final String PAYMENT_URL="http://localhost:8001";

    @Autowired
    private RestTemplate restTemplate;

//    @Autowired
//    private LoadBalancer loadBalancer;
//
//    @Autowired
//    private DiscoveryClient discoveryClient;

    @GetMapping("/consumer/payment/get/{id}")
    public ResponseVO getPaymentById(@PathVariable("id") long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,ResponseVO.class);
    }

    @PostMapping("/consumer/payment/create")
    public ResponseVO create(@RequestBody Payment payment){
        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,ResponseVO.class);
    }
}
```

```java
package edu.nf.cloud.lb;

import org.springframework.cloud.client.ServiceInstance;

import java.util.List;

/**
 * @author YXD
 * @date 2020/6/29
 */
public interface LoadBalancer {
    ServiceInstance instance(List<ServiceInstance> serviceInstances);
}
```

```java
package edu.nf.cloud.lb;

import org.springframework.cloud.client.ServiceInstance;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author YXD
 * @date 2020/6/29
 */
@Component
public class LoadBalancerImpl  implements LoadBalancer{
    private AtomicInteger atomicInteger=new AtomicInteger(0);

    public final int getAndIncrement(){
        int current;
        int next;
        do{
            current=this.atomicInteger.get();
            next=current>=2147483647 ? 0 :current+1;
        }while (!this.atomicInteger.compareAndSet(current,next));
        System.out.println("第"+next+"此访问");
        return next;
    }
    @Override
    public ServiceInstance instance(List<ServiceInstance> serviceInstances) {
        int index=getAndIncrement() % serviceInstances.size();
        return serviceInstances.get(index);
    }
}
```

```java
package edu.nf.cloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

/**
 * @author YXD
 * @date 2020/6/29
 */
@SpringBootApplication
@EnableEurekaClient
public class Order80 {
    public static void main(String[] args) {
        SpringApplication.run(Order80.class,args);
    }
}
```

![image-20200701221112731](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200701221112731.png)

###### Zookeeper

###### Cosul

###### Nacos

##### 服务调用

###### Bibbon

###### LoadBalancer

###### OpenFeign

##### 服务降级

###### Hystrix

###### resilience4j

###### sentinel

##### 服务网关

###### Zuul

###### gateway

##### 服务配置

###### Config

###### Nacos

##### 服务总线

###### Bus

###### Nacos

