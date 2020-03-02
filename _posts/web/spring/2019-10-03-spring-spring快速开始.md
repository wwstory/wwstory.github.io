---
title: '[spring] spring快速开始'
date: 2019-10-03 14:18:00
tags:
    - 'web'
    - 'spring'
---

# 开始spring应用

## 创建app项目

创建java application项目，设目录结构：

```
.
├── lib/
│   ├── commons-logging-1.2.jar
│   ├── spring-aop-5.1.9.RELEASE.jar
│   ├── spring-aspects-5.1.9.RELEASE.jar
│   ├── spring-beans-5.1.9.RELEASE.jar
│   ├── spring-context-5.1.9.RELEASE.jar
│   ├── spring-core-5.1.9.RELEASE.jar
│   └── spring-expression-5.1.9.RELEASE.jar
└── src/
    ├── beans.xml
    ├── Main.java
    └── Message.java
```

> `beans.xml`文件名可以任意，后面是通过传递xml名称给`ApplicationContext`类查找的。

## 添加库

先从官网下载spring库：https://repo.spring.io/release/org/springframework/spring/ 

使用ide添加(add as library)以下几个常用到的spring库（aop、aspects是使用切面时需要的）：
- core
- context
- beans
- expression
- aop
- aspects

由于spring需要common logging库，官网下载：http://commons.apache.org/proper/commons-logging/download_logging.cgi

添加到ide中：
- common logging

## 代码

添加java类，作为bean。

```java
// src/Message.java
public class Message {
    private String msg;

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = "this mssage is : " + msg;
    }

}

```

添加xml文件，配置bean。

```xml
<!--src/beans.xml-->
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="msg1" class="Message">
        <property name="msg" value="hello world"/>
    </bean>

</beans>
```

添加java类，作为主类，从xml配置文件中，获取bean。

```java
// src/Main.java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

    public static void main(String[] args){
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Message msg = (Message) context.getBean("msg1");
        System.out.println(msg.getMsg());
    }
}

```

运行主类。

---

# 开始spring网页

## 创建web项目

> 直接使用idea创建web项目向导，可减少一些idea的配置，这是没必要的学习成本。

New Project -> Java Enterprise -> Web Application

> 也可直接选Spring里的配置，这将会自动下载配置好一些需要的spring库。

目录结构：

```
.
├── lib/
│   ├── aopalliance-1.0.jar
│   ├── commons-logging-1.2.jar
│   ├── spring-aop-4.3.18.RELEASE.jar
│   ├── spring-aspects-4.3.18.RELEASE.jar
│   ├── spring-beans-4.3.18.RELEASE.jar
│   ├── spring-context-4.3.18.RELEASE.jar
│   ├── spring-context-support-4.3.18.RELEASE.jar
│   ├── spring-core-4.3.18.RELEASE.jar
│   ├── spring-expression-4.3.18.RELEASE.jar
│   ├── spring-instrument-4.3.18.RELEASE.jar
│   ├── spring-instrument-tomcat-4.3.18.RELEASE.jar
│   ├── spring-jdbc-4.3.18.RELEASE.jar
│   ├── spring-jms-4.3.18.RELEASE.jar
│   ├── spring-messaging-4.3.18.RELEASE.jar
│   ├── spring-orm-4.3.18.RELEASE.jar
│   ├── spring-oxm-4.3.18.RELEASE.jar
│   ├── spring-test-4.3.18.RELEASE.jar
│   ├── spring-tx-4.3.18.RELEASE.jar
│   ├── spring-web-4.3.18.RELEASE.jar
│   ├── spring-webmvc-4.3.18.RELEASE.jar
│   ├── spring-webmvc-portlet-4.3.18.RELEASE.jar
│   └── spring-websocket-4.3.18.RELEASE.jar
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── yww/
│                   ├── HomeController.java
│                   └── Message.java
└── web/
    ├── index.jsp
    └── WEB-INF/
        ├── applicationContext.xml
        ├── dispatcher-servlet.xml
        ├── jsp/
        │   └── home.jsp
        └── web.xml
```

> `com.yww.HomeController.java`是自己创建的控制器，做项目时，应归到一个controller文件夹便于管理。
> `applicationContext.xml`文件不是必须的，用来配置bean，可以在`web.xml`中除去这部分不想要的`<context-param>`。
> `applicationContext.xml`文件中可以配置`bean`，因其在`web.xml`文件中`<context-param>`标签下配置好了这个文件，故不必像前面app项目中，使用`ClassPathXmlApplicationContext`的一系列上下文获取`bean的xml配置文件`，再去获取`bean`。

## 添加库

> 为了使用java ee，需安装idea企业版。

> 相比应用，网页需要添加web、webmvc库。

添加库到lib文件夹：
- common logging
- core
- context
- beans
- expression
- aop
- aspects
- web
- webmvc

选取所有库，右键`add as library`。

## 配置

- 设置代码路径：File -> Project Structure -> Modules -> 右键选中项目路径src/main/java/，选择Sources。

- 配置输出：File -> Project Structure -> Artifacts -> + -> Web Application:Exploded -> From Modules...

- 修复问题（打包时添加lib）：File -> Project Structure -> Problems -> Fix，就会自动添加修复。

- 为了直接在idea中运行调试，配置idea的tomcat：Run -> Edit Configurations... -> + -> Tomcat Server -> local -> Application server的Configure... -> Tomcat_Home选择自己下载的Tomcat的目录。

- 将`web.xml`中`<url-pattern>*.form</url-pattern>`改为`<url-pattern>/</url-pattern>`，否则只会拦截`*.form`结尾的请求。

如果需要部署到自己的tomcat服务器上，需要配置打包为war的配置。

- 打包war配置：File -> Project Structure -> Artifacts -> + -> Web Application:Archive -> From 'xxx'，从之前的Exploded打包成war。
- 打包war：Build -> Build Artifacts... -> xxx archive -> build。

> 在`Project Structure` -> `Artifacts`的配置时，先创建`Web Application:Exploded`（因为可以从Modules中生成），再创建`Web Application:Archive`（因为可以直接从Exploded中生成）。

## 代码

tomcat会读取web.xml进行配置，其中包含了spring的DispatcherServlet路径处理的servlet。

```xml
<!-- web/WEB-INF/web.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
<!--        <url-pattern>*.form</url-pattern>-->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

配置，开启组件扫描，配置view视图解析器解析的前后缀。

```xml
<!-- web/WEB-INF/dispatcher-servlet.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.yww"/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

配置bean，将`Message.java`类放入`com/yww/`目录下，配置bean的文件`applicationContext.xml`。

```xml
<!-- src/main/webapp/WEB-INF/applicationContext.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="msg1" class="Message">
        <property name="msg" value="hello world"/>
    </bean>

</beans>
```

作为bean的Message类。

```java
// src/main/java/com/yww/Message.java
package com.yww;

public class Message {
    private String msg;

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = "this mssage is : " + msg;
    }

}

```

控制器，设置请求的路径映射，返回渲染的视图名称的字符串，spring会自动使用上面的xml配置指定的后缀.jsp添加后，寻找符合的jsp文件（如此处的home.jsp）。

```java
// src/main/java/com/yww/HomeController.java
package com.yww;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
@RequestMapping("/home")
public class HomeController {
    @RequestMapping(method = RequestMethod.GET)
    public String printHome(ModelMap model){
        model.addAttribute("msg", "this is a page!");

        return "home";
    }
}

```

渲染视图，通过控制器返回的字符串查找匹配的视图。

```html
<!-- web/WEB-INF/jsp/home.jsp -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>${msg}</h1>
</body>
</html>
```

## 运行

配置了idea中的tomcat配置，即可直接点击运行按钮运行，在浏览器中输入：http://localhost:8080/ 或者 http://localhost:8080/home ，即可查看。

或配置了打包war，先将war放在`tomcat目录/webapps/`下，再手动运行`tomcat目录/bin/startup.sh`启动tomcat，tomcat会自动部署该web应用，最后打开浏览器输入：http://localhost:8080/ 。

---

# Maven快速开始

## 创建maven的spring项目

下载Maven，使用命令创建webapp：
```bash
mvn archetype:generate -DarchetypeArtactId="maven-archetype-webapp" -DarchetypeGroupId="org.apache.maven.archetypes" -DarchetypeVersion="1.4"
```

目录结构：
```
.
├── pom.xml
└── src/
    └── main/
        ├── java/
        │   └── com/
        │       └── yww/
        │           ├── HomeController.java
        │           └── Message.java
        ├── resources/
        └── webapp/
            ├── index.jsp
            └── WEB-INF/
                ├── applicationContext.xml
                ├── dispatcher-servlet.xml
                ├── jsp/
                │   └── home.jsp
                └── web.xml
```

## 代码

> 其它的文件都同不使用maven管理项目的一样，只是目录结构有所不同。

pom.xml是maven的配置文件。

```xml
<!-- pom.xml -->
<?xml version="1.0" encoding="UTF-8" ?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.yww</groupId>
  <artifactId>demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>demo Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
      <version>1.2</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

</project>
```

> 此处将`<build>`的所有`<plugin>`删除了，也可以正常打包构建项目。

> 不同与之前的bean.xml配置文件，这里需要使用包名限定。

`web.xml`文件同之前配置。

控制器中获取bean（自动连线需要导Autowired包）。

```java
package com.yww;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.beans.factory.annotation.Autowired;

@Controller
@RequestMapping("/home")
public class HomeController {

    @Autowired
    public Message msg1;

    @RequestMapping(method = RequestMethod.GET)
    public String printHome(ModelMap model){
        String str1 = msg1.getMsg();
        model.addAttribute("msg", "this is a page! gradle " + str1);

        return "home";
    }
}

```


## 打包运行

在与pom.xml同级目录下，使用命令下载依赖的库并打包成war：
```bash
mvn install
```

将`target/`目录下的war文件放入`tomcat/webapps/`中运行。

---

# Gradle快速开始

## 创建gradle目录结构

下载Gradle，执行命令创建web项目：
```bash
gradle init
```

目录结构：
```
.
├── build.gradle
└── src/
    └── main/
        ├── java/
        │   └── com/
        │       └── yww/
        │           ├── HomeController.java
        │           └── Message.java
        ├── resources/
        │   └── application.properties
        └── webapp/
            ├── index.jsp
            └── WEB-INF/
                ├── applicationContext.xml
                ├── dispatcher-servlet.xml
                ├── jsp/
                │   └── home.jsp
                └── web.xml
```

## 代码

> 未列出的代码皆同上。

build.gradle是gradle管理项目的配置文件。

```gradle
// build.gradle
plugins {
	id 'java'
    id 'war'
}

group = 'com.yww'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8
targetCompatibility = 1.8

ext{
    springVersion = '5.2.0.RELEASE'
}

repositories {
	mavenCentral()
}

dependencies {
    compile "org.springframework:spring-core:$springVersion"
    compile "org.springframework:spring-context:$springVersion"
    compile "org.springframework:spring-beans:$springVersion"
    compile "org.springframework:spring-expression:$springVersion"
    compile "org.springframework:spring-aop:$springVersion"
    compile "org.springframework:spring-aspects:$springVersion"
    compile "org.springframework:spring-web:$springVersion"
    compile "org.springframework:spring-webmvc:$springVersion"

	testCompile "junit:junit:4.12"
    compile "org.springframework:spring-jdbc:$springVersion"
    compile group: 'mysql', name: 'mysql-connector-java', version: '8.0.15'
}

```

## 打包运行

在与build.gradle同级目录下，执行命令构建打包war：
```bash
gradle build
```

将`build/lib/`目录下的`war`文件放入tomcat运行。

---