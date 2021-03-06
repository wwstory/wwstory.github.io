---
title: '[spring] @PropertySource'
date: 2019-10-05 22:23:00
tags:
    - 'web'
    - 'spring'
---

# 配置文件

`@PropertySources`注解用于加载配置文件到Spring的环境中。

配置文件如下。

```properties
demo.msg=this is a message.
```

## 如何引用到配置文件

在app项目中，我们通过`@PropertySource`注解到JavaConfig类上，设置`.properties`配置文件的路径。

> 在gradle项目中，配置文件放在`src/main/resources/`路径下，还可以放在这个目录下的文件夹。如：`src/main/resources/demo/app.properties`的设置`@PropertySource("demo/app.properties")`。

在web项目中，spring web已经将配置文件设置好了，不需要`@PropertySource`配置。

## 如何使用配置的值

spring里的许多配置可以在`.properties`文件中直接配置到。

我们在xml配置，注解等地方需要使用到配置文件的值时，可以使用`spring EL`语言设置，格式如`${x.y.z}`。

## @PropertySource + @Value

通过在类上设置`@PropertySource`设置配置文件。

通过在成员变量上设置`@Value`指定所设置在配置文件中的值。

```java
package com.yww;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

@Component
@PropertySource(value = "application.properties")
public class Message {

    @Value("${demo.msg}")
    private String msg;

}
```

## @PropertySource + @ConfigurationProperties

通过在类上设置`@PropertySource`设置配置文件。

在类上设置`@ConfigurationProperties`自动将配置文件中名称满足的配置值设置。

```java
package com.yww;

import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;
import org.springframework.boot.context.properties.ConfigurationProperties;

@Component
@PropertySource(value = "application.properties")
@ConfigurationProperties(prefix = "demo")
public class Message {

    private String msg;

}
```

> `@ConfigurationProperties`是`spring boot`中的类，需要导入相应的库。

# 参考

https://www.cnblogs.com/517cn/p/10946213.html