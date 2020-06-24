---
title: '[springboot] mysql jpa 快速开始'
date: 2020-06-19 08:46:00
tags:
    - 'web'
    - 'springboot'
---

# spring boot连接mysql

[Accessing data with MySQL][1]

## 示例 - 连接mysql

### 项目结构

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

### 数据库

```sql
create database db_test;
```

> 采用`spring.jpa.hibernate.ddl-auto=update`设置，`update`可以使用jpa自动建表。

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
	runtimeOnly 'mysql:mysql-connector-java'
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
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/db_test
spring.datasource.username=root
spring.datasource.password=123456
```

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
    private int id;
    private String username;
    private String password;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

}
```

> 使用`@Entity`注解，这是一个JPA实体。
> PS: 不能创建构造函数。[jpa获取数据库对象时，由于持久化类带有构造方法，导致hibernate无法使用反射创建对象][2]

`[UserRepository.java]`

```java
package com.yww.demo.dao;

import com.yww.demo.po.User;

import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Integer> {

    User findById(int id);
    
}
```

`[HelloController.java]`

```java
package com.yww.demo.controller;

import java.util.HashMap;
import java.util.Map;

import com.yww.demo.dao.UserRepository;
import com.yww.demo.po.User;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;


@RestController
@RequestMapping(path="/demo")
public class HelloController {

	@Autowired
	UserRepository userRepository;

	@PostMapping("/add")
	// public @ResponseBody Map<String, String> addNewUser(@RequestParam String username, @RequestParam String password){
		// Map<String, String> map = new HashMap<>();
	public @ResponseBody User addNewUser(@RequestParam String username, @RequestParam String password){
		User u = new User();
		u.setUsername(username);
		u.setPassword(password);

		userRepository.save(u);

		
		// map.put("status", "ok");
		// return map;
		return u;
	}
	
	@GetMapping("/all")
	public @ResponseBody Iterable<User> getAllUsers(){
		return userRepository.findAll();
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

```sh
./gradlew bootRun
curl "http://localhost:8080/demo/add" -d username=yww -d password=123456
curl "http://localhost:8080/demo/all"
```



---

[1]: https://spring.io/guides/gs/accessing-data-mysql/
[2]: https://blog.csdn.net/u012930117/article/details/52781851/