#### Spring Boot 热部署

在目前的 Spring Boot 项目中，当发生了任何修改之后我们都需要重新启动才能够正确的得到效果，这样会略显麻烦，Spring Boot 提供了热部署的方式，当发现任何类发生了改变，就会通过 JVM 类加载的方式，加载最新的类到虚拟机中，这样就不需要重新启动也能看到修改后的效果了。

> - 做法也很简单，修改 pom.xml 即可！

我们往 pom.xml 中添加一个依赖就可以了：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional> <!-- 这个需要为 true 热部署才有效 -->
</dependency>
```

