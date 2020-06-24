---
title: '[springboot] mybatis快速开始'
date: 2020-06-22 12:00:00
tags:
    - 'web'
    - 'springboot'
---


# 示例 - 使用mybatis

[Quick-Start][1]

## 目录结构

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
        │               ├── DemoApplication.java
        │               ├── HelloController.java
        │               ├── User.java
        │               └── UserMapper.java
        └── resources/
            ├── application.properties
            └── schema.sql
```

## 代码

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
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.3'
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

`[schema.sql]`

```sql
CREATE TABLE user_t
(
  id        INT PRIMARY KEY auto_increment,
  username  VARCHAR,
  password  VARCHAR
);

```

`[User.java]`

```java
package com.yww.demo;

public class User {
    
    private Long id;
    private String username;
    private String password;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
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

`[UserMapper.java]`

```java
package com.yww.demo;

import java.util.List;

import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Options;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserMapper {
    
    @Insert("INSERT INTO user_t (username, password) VALUES (#{username}, #{password})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    void insert(User user);

    @Select("SELECT id, username, password FROM user_t WHERE id = #{id}")
    User findById(long id);

    @Select("SELECT id, username, password FROM user_t")
    List<User> findAll();
}
```

`[HelloController.java]`

```java
package com.yww.demo;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

	@Autowired
	UserMapper userMapper;

	@PostMapping("/add")
	public String add(@RequestParam(value = "username", defaultValue = "yww") String username, @RequestParam(value = "password", defaultValue = "123456") String password) {
		User user = new User();
		user.setUsername(username);
		user.setPassword(password);

		userMapper.insert(user);

		return String.format("add ok!");
	}
	
	@GetMapping(value="/find")
	public User find(@RequestParam(value = "id", defaultValue = "1") long id) {
		User user = userMapper.findById(id);
		return user;
	}

	@GetMapping(value="/all")
	public List<User> all(@RequestParam(value = "id", defaultValue = "1") long id) {
		return userMapper.findAll();
		
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

## 运行

```sh
./gradlew bootRun
```

访问h2管理界面：http://localhost:8080/h2-console

添加数据：

```sh
curl http://localhost:8080/add -d username=yww -d password=123456
```

访问：http://localhost:8080/find?id=1

访问：http://localhost:8080/all

---

[1]: https://github.com/mybatis/spring-boot-starter/wiki/Quick-Start
