---
title: '[springboot 学习3] 自定义配置'
date: 2020-06-21 20:25:00
tags:
    - 'web'
    - 'springboot'
---

# 如何覆盖自动配置，使用自定义配置

要覆盖自动配置，可以就当自动配置不存在，直接显式地写一段配置。（可以是java, xml, groovy形式）

Spring Boot会发现你的配置，随后降低自动配置的优先级，以你的配置为准。


# 自定义配置的原理

1. 大部分情况下，`@ConditionalOnMissingBean`注解是覆盖自动配置的关键。

> 例如：Spring Boot的`DataSourceAutoConfiguration`中定义的`JdbcTemplate Bean`。
> 
> ```java
> @Bean
> @ConditionalOnMissingBean(JdbcOperations.class)
> public JdbcTemplate jdbcTemplate(){
>   return new JdbcTemplate(this.dataSource);
> }
> ```
> 
> 当不存在`JdbcOperations`类型（`JdbcTemplate`实现了该接口）的Bean时才生效。

Spring Boot的设计是加载`应用级配置`，随后再考虑`自动配置类`。

2. 还有通过一些`@EnableXXX`的注解实现的自定义配置。

> 例如：
> 
> ```java
> @Configuration
> @EnbaleConfigurationProperties
> @ConditionalOnClass({EnableWebSecurity.class})
> @ConditionalOnMissingBean(WebSecurityConfiguration.class)
> @ConditionalOnWebApplication
> public class SpringBootWebSecurityConfiguration{
> }
> ```
>
> 这个`security`自动配置有多个条件，这里比较关注的是`ConditionalOnClass`和`ConditionalOnMissingBean`。
> `ConditionalOnClass`要求`classpath`要有`@EnableWebSecurity`注解。
> `ConditionalOnMissingBean`要求在没有`WebSecurityConfiguration`类型的bean时创建。表面上，我们自定义配置时没有这个bean，但通过在`SecurityConfig`（自定义的配置类）上添加`@EnableWebSecurity`注解，实际上间接创建了一个`WebSecurityConfiguration`的Bean。所以这个自动配置`SpringBootWebSecurityConfiguration`提供的配置被跳过了。

# 通过属性文件外置配置

只做了一点微调，为了不放弃自动配置，可以在`环境变量`、`JVM系统属性`、`JNDI`、`命令行参数`、`属性文件`中设置。

> 以关闭ascii-art banner（运行时，Spring的文字图标）为例：
> 1. 执行jar时，命令行参数指定：
> ```sh
> java -jar demo-0.0.1-SNAPSHOT.jar --spring.main.banner-mode=off
> ```
> 2. 在`application.properties`中指定配置：
> ```properties
> spring.main.banner-mode=off
> ```
> 3. 设置环境变量
> ```sh
> export spring_main_banner_mode=off
> ```

各配置方式之间有优先级排序，高优先级会覆盖低优先级的相同属性。

# Profile

## 配置文件

通过遵循`application-{xxx}.properties`/`application-{xxx}.yml`这种命名格式，就可以被spring boot自动读取。

`[build.gradle]`

```gradle
dependencies {
	runtimeOnly 'com.h2database:h2'
	runtimeOnly 'mysql:mysql-connector-java'
}
```

### 多个配置文件

```sh
.
├── application-h2.properties
├── application-mysql.yml
└── application.properties
```

`[application.properties]`

```properties
spring.profiles.active=mysql
spring.jpa.show-sql=true
```

> 通过`spring.profiles.active`设置`h2`/`mysql`选择`application-h2.properties`/`application-mysql.yml`文件做配置。

`[application-h2.properties]`

```properties
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:testdb
```

`[application-mysql.yml]`

```yml
spring:
    datasource:
        url: jdbc:mysql://${MYSQL_HOST:localhost}:3306/db_test
        username: root
        password: 123456
    jpa.hibernate.ddl-auto: update
```

### 单配置文件

`[application.yml]`

```yml
spring.profiles.active: h2
spring.jpa.show-sql: true

---

spring:
    profiles: h2
    h2.console.enabled: true
    datasource.url: jdbc:h2:mem:testdb

---

spring:
    profiles: mysql
    datasource:
        url: jdbc:mysql://${MYSQL_HOST:localhost}:3306/db_test
        username: root
        password: 123456
    jpa.hibernate.ddl-auto: update
```


## 配置类

```java
package com.yww.demo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

@Profile("production")
@Configuration
public class Config {
    
}
```
