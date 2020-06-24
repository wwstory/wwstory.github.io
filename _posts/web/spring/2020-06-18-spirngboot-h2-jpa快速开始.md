---
title: '[springboot] h2 jpa快速开始'
date: 2020-06-18 11:29:00
tags:
    - 'web'
    - 'springboot'
---

# spring boot连接h2

[Accessing Data with JPA][1]

## 示例 - jpa连接h2

### 目录结构

```sh
.
├── build.gradle
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew*
└── src/
    └── main/
        ├── java/
        │   └── com/
        │       └── yww/
        │           └── demo/
        │               ├── controller/
        │               │   └── HelloController.java
        │               ├── dao/
        │               │   └── UserRepository.java
        │               ├── DemoApplication.java
        │               └── po/
        │                   └── User.java
        └── resources/
            ├── application.properties
            ├── static/
            └── templates/

```

### 代码

`[build.gradle]`

```gradle
plugins {
	id 'org.springframework.boot' version '2.3.1.RELEASE'
	id 'io.spring.dependency-management' version '1.0.9.RELEASE'
	id 'java'
}

group = 'com.yww'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	runtimeOnly 'com.h2database:h2'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}

test {
	useJUnitPlatform()
}

```

`[application.properties]`

```properties
spring.h2.console.enabled=true
spring.datasource.url=jdbc:h2:mem:testdb
# logging.level.org.hibernate=DEBUG
```

> 开启网页控制界面，并固定其url。

`[User.java]`

```java
package com.yww.demo.po;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}
```

`[UserRepository.java]`

```java
package com.yww.demo.dao;

import com.yww.demo.po.User;

import org.springframework.data.repository.CrudRepository;

public interface UserRepository extends CrudRepository<User, Long> {

    User findById(long id);
    
}
```

`[HelloController.java]`

```java
package com.yww.demo.controller;

import com.yww.demo.dao.UserRepository;
import com.yww.demo.po.User;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

	private static final Logger log = LoggerFactory.getLogger(HelloController.class);

	@GetMapping("/hello")
	public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
		return String.format("Hello %s!", name);
	}

	@Autowired
	UserRepository userRepository;
	
	// @GetMapping("/save")
	// public String save() {
	// 	User user1 = new User();
	// 	user1.setName("yww");
	// 	User user2 = new User();
	// 	user2.setName("ms");
	// 	User user3 = new User();
	// 	user3.setName("ww");

	// 	userRepository.save(user1);
	// 	userRepository.save(user2);
	// 	userRepository.save(user3);
	// 	return String.format("save ok!");
	// }

	@GetMapping("/jpa")
	public String jpa(@RequestParam(value = "id", defaultValue = "1") Long id) {
		User user1 = new User();
		user1.setName("yww");
		User user2 = new User();
		user2.setName("ms");
		User user3 = new User();
		user3.setName("ww");
		userRepository.save(user1);
		userRepository.save(user2);
		userRepository.save(user3);
		
		User user = userRepository.findById(id).orElse(null);

		log.info(user.toString());
		return String.format("Hello %s!", user.getName());
	}
    
}
```

`[DemoApplication.java]`

```java
package com.yww.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}

```

### 运行

运行：

```sh
./gradlew bootRun
```

访问：http://localhost:8080/h2-console， 连接

访问：http://localhost:8080/jpa


---

[1]: https://spring.io/guides/gs/accessing-data-jpa/