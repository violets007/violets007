---
# date是手动设置此篇文章编写的时间
date: "2022/04/25 16:08"

# 手动设置此篇文章封面
coverUrl: "springcloud/spring-cloud-nacos.png"

# 是否置顶
sticky: false

# 设置keyword 多个以,分开
keyword: SpringCloudAlibaba

# 设置description
description: SpringCloudAlibaba-Nacos学习

# 手动设置标题，否则使用h1标签作为标题
title: SpringCloudAlibaba-Nacos学习

# 这是设置标签，数组形式
tag: [SpringCloudAlibaba,SpringCloud]

# 这里设置类别，数组形式
categories: [SpringCloudAlibaba,SpringCloud]
---



## **Nacos是什么**？

Nacos是什么？好像没听过，不要紧。那Eureka听说过吧，在SpringCloud中做服务注册中心组件，类似的还有Zookeeper、Consul。

所以Nacos也是一个注册中心组件咯，当然是，不过**它不仅仅是注册中心**。

**Nacos也是一个配置中心**，比如SpringCloud中的Config，将配置文件版本化管理。

那么Nacos到底是什么呢, 总结为官网一句话就是:

> Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。



## 如何使用Nacos?

### 1.预备环境准备

Nacos 依赖 [Java](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 环境来运行。如果您是从代码开始构建并运行Nacos，还需要为此配置 [Maven](https://maven.apache.org/index.html)环境，请确保是在以下版本环境中安装使用:

1. 64 bit OS，支持 Linux/Unix/Mac/Windows，推荐选用 Linux/Unix/Mac。
2. 64 bit JDK 1.8+；[下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) & [配置](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)。
3. Maven 3.2.x+；[下载](https://maven.apache.org/download.cgi) & [配置](https://maven.apache.org/settings.html)。

### 2.下载源码或者安装包

你可以通过源码和发行包两种方式来获取 Nacos。

#### 从 Github 上下载源码方式

```bash
git clone https://github.com/alibaba/nacos.git
cd nacos/
mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U  
ls -al distribution/target/

// change the $version to your actual path
cd distribution/target/nacos-server-$version/nacos/bin
```

#### 下载编译后压缩包方式

您可以从 [最新稳定版本](https://github.com/alibaba/nacos/releases) 下载 `nacos-server-$version.zip` 包。

```bash
  unzip nacos-server-$version.zip 或者 tar -xvf nacos-server-$version.tar.gz
  cd nacos/bin
```

### 3.启动服务器

#### Linux/Unix/Mac

启动命令(standalone代表着单机模式运行，非集群模式):

```
sh startup.sh -m standalone
```

如果您使用的是ubuntu系统，或者运行脚本报错提示[[符号找不到，可尝试如下运行：

```
bash startup.sh -m standalone
```

#### Windows

启动命令(standalone代表着单机模式运行，非集群模式):

```
startup.cmd -m standalone
```

#### 4.启动效果如图所示

![icon](springcloud/start.png)

![icon](springcloud/start1.png)



## 创建服务提供者模块

如何创建分布式项目可参考：[SpringCloud入门及创建分布式项目](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fstronglxp%2FlearnNote%2Fblob%2Fmain%2FJava%2FSpringCloud%E7%9B%B8%E5%85%B3%2FSpringCloud%E5%85%A5%E9%97%A8%E5%8F%8A%E5%88%9B%E5%BB%BA%E5%88%86%E5%B8%83%E5%BC%8F%E9%A1%B9%E7%9B%AE.md)一文。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringCloudAlibaba-Demo</artifactId>
        <groupId>top.zixuan007.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>order-service</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            <version>2.2.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>

```

**application.yml文件如下**

```yaml
server:
  port: 8082

spring:
  application:
    name: order-service

  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


```

**主启动类**

```java
package top.violets007.cloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author zixuan007
 * @description:
 */
@SpringBootApplication
public class OrderSpringApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderSpringApplication.class, args);
    }
}

```

**编写一个控制器**

```java
package top.violets007.cloud.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author zixuan007
 * @description:
 */
@RestController
public class OrderController {

    @RequestMapping("/pay")
    public String order() {

        return "下单成功";
    }
}

```

**然后启动该模块，在nacos控制台就能看到该实例已经注册到nacos。**

![icon](springcloud/server-list.png)



## 创建消费者模块

**pom.xml文件如下**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SpringCloudAlibaba-Demo</artifactId>
        <groupId>top.zixuan007.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>service-nacos</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>

```

**application.yml文件如下**

```yaml
server:
  port: 8081

spring:
  application:
    name: nacos-service

  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


```

主启动类

```java
package top.violets007.cloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;


/**
 * @author violets007
 */
@EnableDiscoveryClient
@SpringBootApplication
public class NacosServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosServiceApplication.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(RestTemplateBuilder restTemplateBuilder) {
        return restTemplateBuilder.build();
    }

}

```

**控制器**

```java
package top.violets007.cloud.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

/**
 * 订单服务
 *
 * @author violets007
 */
@RestController
@RequestMapping("/order")
public class OrderController {

    @Autowired
    RestTemplate restTemplate;


    @RequestMapping("/add")
    public String addOrder() {
        String url = "http://order-service/pay";
        String mes = restTemplate.getForObject(url, String.class);
        return "订单服务" + mes;
    }
}

```

**然后启动消费者模块，在nacos控制台可以看到服务列表**

![icon](springcloud/server-list1.png)

**最后调用服务就可以看见**
![icon](springcloud/call-service.png)