1.新建springboot项目

2.添加pom依赖

```xml
<!-- https://mvnrepository.com/artifact/com.baomidou/mybatisplus-spring-boot-starter -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatisplus-spring-boot-starter</artifactId>
    <version>1.0.5</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
</dependency>
```

3.设置application.properties

```properties
#springboot2.1之后
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/changgou_user?serverTimezone=GMT%2B*
spring.datasource.username=root
spring.datasource.password=Pan199668!

mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```



