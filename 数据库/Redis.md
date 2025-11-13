## Maven 依赖

Spring Boot 提供 **spring-boot-starter-data-redis** 解决 Redis 相关依赖。它为 Lettuce 和 Jedis 提供了基本的自动配置。Spring Boot 2.x 默认使用 Lettuce 作为连接 Redis 的客户端。如果需要配置连接池，需要添加 **commons-pool2** 的依赖。 **pom.xml**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>       
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

