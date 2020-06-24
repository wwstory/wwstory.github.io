---
title: '[springboot 学习2] 自动配置'
date: 2020-06-17 08:08:00
tags:
    - 'web'
    - 'springboot'
---

# 起步依赖

> Spirng Boot的依赖能互相兼容，可以通过`gradle dependencies`或`mvn dependency:tree`查看依赖树。

## @SpringBootApplication

`@SpringBootApplication`包含：

- `@Configuration`
- `@EnableAutoConfiguration`: 告诉SpringBoot基于`类路径`、`其它Bean`、`各种属性设置`添加Bean。
- `@ComponentScan`: 告诉Spring寻找其它的组件、配置、服务。

## 覆盖起步依赖引入的传递依赖

可以使用`exclude`排除指定的传递依赖：

```gradle
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
```

如果要指定新版的库，可以增加这个依赖，由于Gradle会使用其中最新的版本，所以会覆盖传递依赖引入的依赖：

```gradle
dependencies {
	compile group: 'org.springframework', name: 'spring-jdbc', version: '5.0.0.RELEASE'
}
```

> 如果使用旧版的库，就需要先`exclude`，再添加。

# 条件化配置

Spring Boot的自动配置是一个运行时的过程，考虑了多个因素才决定Spring配置应该用那个。

> 例如：
> - Spring的JdbcTemplate是不是在Classpath里面？如果是，并且有DataSource的Bean，则自动配置一个JdbcTemplate的Bean。
> - Thymeleaf是不是在Classpath里？如果是，则配置Thymeleaf的模板解析器、视图解析器、模板引擎。
> - Spring Security是不是在Classpath里？如果是，则进行一个非常基本的Web安全设置。

> 每当应用程序启动的时候，Spring Boot的自动配置都要做将近200个这样的决定，涵盖安全、集成、持久化、Web开发等诸多方面。所有这些自动配置就是为了尽量不让你自己写配置。

Spring Boot的自动配置是利用spring4新特性的`条件化配置`实现的。条件化配置允许配置存在于应用程序中，但在满足某些特定的条件前都忽略这个配置。

想编写自己的条件，可以通过实现`Condition接口`，覆盖其`matches()`方法。

使用时，在需要条件的类上注解这个条件即可。

**自动配置中使用的条件化注解：**

条件化注解                           | 配置生效条件
- | -
@ConditionalOnBean					| 配置了某个特定Bean
@ConditionalOnMissingBean			| 没有配置特定的Bean
@ConditionalOnClass					| Classpath里有指定的类
@ConditionalOnMissingClass			| Classpath里缺少指定的类
@ConditionalOnExpression			| 给定的Spring Expression Language(SpEL)表达式计算结果为true
@ConditionalOnJava					| Java的版本匹配特定值或者一个范围值
@ConditionalOnJndi					| 参数中给定的JNDI位置必须存在一个，如果没有给参数，则要有JNDI InitialContext
@ConditionalOnProperty				| 指定的配置属性要有一个明确的值
@ConditionalOnResource				| Classpath里有指定的资源
@ConditionalOnWebApplication		| 这是一个Web应用程序
@ConditionalOnNotWebApplication		| 这不是一个Web应用程序

## 示例 - 自定义条件化配置

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
        └── java/
            └── com/
                └── yww/
                    └── demo/
                        ├── controller/
                        │   └── HelloController.java
                        ├── DemoApplication.java
                        ├── MyBean.java
                        ├── MyClass.java
                        └── MyCondition.java
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
	implementation 'org.springframework.boot:spring-boot-starter-web'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}

	compile group: 'org.springframework', name: 'spring-jdbc', version: '5.0.0.RELEASE'
}

test {
	useJUnitPlatform()
}

```

`[MyClass.java]`

```java
package com.yww.demo;

import org.springframework.stereotype.Component;

@Component
public class MyClass {
    public String name;

    public void print(){
        System.out.println("hi~");
    }
}
```

`[MyCondition.java]`

```java
package com.yww.demo;

import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class MyCondition implements Condition{

	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        try{
            context.getClassLoader().loadClass("com.yww.demo.MyClass");
            System.out.println("------------ load ok");
        } catch(Exception e){
            System.out.println("------------ load fault");
            return false;
        }

		return true;
	}
}
```

`[MyBean.java]`

```java
package com.yww.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;

@Configuration
@Conditional(MyCondition.class)
public class MyBean {
    
    @Autowired
    private MyClass mc;

    public MyBean(){
        System.out.println("==== build bean ok!");
        // mc.print(); // error, 只声明了各个Bean，还没有注入，mc还不能用。
    }

    public void print(){
        System.out.println("====> ok");
        mc.print();
    }
}
```

执行命令运行：

```sh
./gradlew bootRun
```

在终端可看到打印信息。
