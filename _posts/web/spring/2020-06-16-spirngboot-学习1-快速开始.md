---
title: '[springboot 学习1] 快速开始'
date: 2020-06-16 07:33:00
tags:
    - 'web'
    - 'springboot'
---

# 创建项目

> [Spring Quickstart Guide][1]

- 前往[start.spring.io][2]，

- 添加Spring Web依赖，

- 下载。

# 代码

修改`/src/main/java/com/example/demo/DemoApplication.java`，

```java

package com.example.demo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @GetMapping("/hello")
    public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
        return String.format("Hello %s!", name);
    }
}
```

# 运行

maven构建使用：

```sh
./mvnw spring-boot:run
```

gradle构建使用：

```sh
./gradlew bootRun
```

打开浏览器查看：`http://localhost:8080/hello`

> 可传入参数：`?name=xyz`

> `@SpringBootApplication`将3个注解组合在一起`@Configuration`，`@ComponentScan`，`@EnableAutoConfiguration`。

---

[1]: https://spring.io/quickstart
[2]: https://start.spring.io/
