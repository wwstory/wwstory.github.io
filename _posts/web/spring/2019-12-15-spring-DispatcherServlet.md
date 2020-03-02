---
title: '[spring] DispatcherServlet'
date: 2019-12-15 19:13:00
tags:
    - 'web'
    - 'spring'
---

# 请求过程

```
                            ┌─────────┐
                           ↗│ 处理器映射│
                         2/ └─────────┘
                         /         3
请求 1 ┌─────────────────┐ --------------------> ┌─────┐
----->│DispatcherServlet│ <--┌─────────────┐  4 │控制器│
      └─────────────────┘    │模型及逻辑视图名│ ---│     │
                      \  \   └─────────────┘    └─────┘
                       \  \5   ┌────────┐
                       6\  --->│视图解析器│
                         ↘     └────────┘
            响应 7      ┌────┐
<----------------------│视图 │
                       └────┘
```

过程：

- 请求：请求离开浏览器时，会带有用户所请求内容的信息，至少会包含请求的URL。（还可能带有如表单信息）
- DispatcherServlet：将请求发送给Spring MVC控制器（controller）

> - `DispatcherServlet`：与大多数基于Java的Web框架一样，Spring MVC所有的请求都会通过一个前端控制器（front controller）Servlet。在这里一个单例的Servlet将请求委托给应用程序的其他组件来执行实际的处理。在Spring MVC中，DispatcherServlet就是前端控制器。
> - `DispatcherServlet`的任务是将请求发送给Spring MVC控制器（`controller`）。（控制器是一个用于处理请求的Spring组件。）
> - `DispatcherServlet`需要知道应该将请求发送给哪个控制器。所以`DispatcherServlet`以会查询一个或多个`处理器映射（handler mapping`）来确定请求的下一站在哪里。处理器映射会根据请求所携带的URL信息来进行决策。
> - 到了控制器，请求会卸下负载（用户提交的信息）并耐心等待控制器处理这些信息。（实际上，设计良好的控制器本身只处理很少甚至不处理工作，而是将业务逻辑委托给一个或多个服务对象进行处理）
> - 控制器在完成逻辑处理后，通常会产生一些信息，这些信息需要返回给用户并在浏览器上显示。这些信息被称为模型（`model`）。
> - 这些信息通常需要以用户友好的方式进行格式化，一般会是HTML。所以信息需要发送给一个视图（`view`），通常会是JSP。

> - 控制器所做的最后一件事就是将模型数据打包，并且标识出用于渲染输出的视图名。它接下来会将请求连同`模型`和`视图名`发送回`DispatcherServlet`。
> - 传递给`DispatcherServlet`的视图名并不直接表示某个特定的JSP。实际上它甚至不能确定视图就是JSP。相反，它仅仅传递了一个`逻辑名称`，这个名字将会用来查找产生结果的真正视图。
> - `DispatcherServlet`将会使用`视图解析器（view resolver）`来将逻辑视图名匹配为一个特定的视图实现，它可能是也可能不是JSP。
> - 视图将会使用模型数据渲染输出，这个输出会通过响应对象传递给客户端。


# 配置DispatcherServlet

`DispatcherServlet`可以配置到`web.xml`文件中。

## AbstractAnnotationConfigDispatcherServletInitializer

在Servlet 3规范和Spring 3.1中，可以使用java将`DispatcherServlet`配置在`Servlet容器`中，需要继承`AbstractAnnotationConfigDispatcherServletInitializer`，重写3个方法。

```java
package com.yww.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{ RootConifg.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {    // 指定配置类
        return new Class[]{ WebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {   // 将DispatcherServlet映射到"/"
        return new String[]{ "/" };
    }
}
```

- `getServletMappings()`：会将一个或多个路径映射到`DispatcherServlet`上。本例中，它映射的是"`/`"，这表示它会是应用的默认Servlet。它会处理进入应用的所有请求。
- 要理解另2个方法，首先要理解`DispatcherServlet`和`Servlet监听器（ContextLoaderListener）`的关系。（如下）

```
    AbstractAnnotationConfigDispatcherServletInitializer
              |                            |
             创建                          创建
              |                            |
              v                            v
      DispatcherServlet               ContextLoaderListener
              |                            |
             加载                          加载
              |                            |
              v                            V
          应用上下文                     应用上下文
（查找@Configuration注解的类，      （查找@Configuration注解的类，
  加载包含Web组件的bean。             加载应用中的其它bean。
  如控制器,视图解析器,处理器映射等）     如自定义的业务所需的bean）

```


> **`AbstractAnnotationConfigDispatcherServletInitializer`剖析**：
> - 在Servlet 3.0环境中，容器会在类路径中查找实现`javax.servlet.ServletContainerInitializer`接口的类，如果能发现的话，就会用它来配置Servlet容器。
> - Spring提供了这个接口的实现，名为`SpringServletContainerInitializer`，这个类反过来又会查找实现`WebApplicationInitializer`的类并将配置的任务交给它们来完成。
> - Spring 3.2引入了一个便利的`WebApplicationInitializer`基础实现，也就是`AbstractAnnotationConfigDispatcherServletInitializer`。
> - 当我们自定义的类继承了`AbstractAnnotationConfigDispatcherServletInitializer`，当部署到`Servlet 3.0容器`中的时候，容器会自动发现它，并用来配置`Servlet上下文`。

```
                        容器(Servlet 3.0)
                                |类路径查找
                                v
            实现javax.servlet.ServletContainerInitializer接口的类
                                |
                                v
              SpringServletContainerInitializer（Spring提供）
                                |查找
                                v
        实现`WebApplicationInitializer`的类，将配置的任务交给它们完成
                                |
                AbstractDispatcherServletInitializer
                  AbstractContextLoaderInitializer
                                |
                                v
    AbstractAnnotationConfigDispatcherServletInitializer（Spring提供）
                                |
                                v
                  自定义类继承，即可配置Servlet上下文
```

## Servlet配置类

作用：启用Web MVC，启用组件扫描，配置视图解析器，配置静态资源的处理。

`WebConfig.java`在上面`getServletConfigClasses()`方法中设置。

**最简单的配置**：
```java
package com.yww.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@EnableWebMvc
public class WebConfig {
}
```

空配置的`WebConfig`尽管能启用Spring MVC，但有不少问题：

- 没有配置视图解析器。Spring默认会使用`BeanNameVire-Resolver`，这个视图解析器会查找ID与视图名称匹配的bean，并且查找bean要实现View接口，它以这样的方式解析视图。
- 没有启用组件扫描。Spring只能找到显式声明在配置类中的控制器。
- `DispatcherServlet`会映射为应用的默认Servlet，所以它会处理所有请求，包括对静态资源的请求。

**最小但可用的Spring MVC配置**：
```java
package com.yww.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration
@EnableWebMvc       // 启用Spring MVC
@ComponentScan      // 启用组件扫描
public class WebConfig extends WebMvcConfigurationSupport {

    // 配置JSP视图解析器
    @Bean
    public ViewResolver viewResolver(){
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/jsp/");
        resolver.setSuffix(".jsp");
        resolver.setExposeContextBeansAsAttributes(true);
        return resolver;
    }

    // 配置静态资源的处理
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer){
        configurer.enable();
    }
}
```

`@ComponentScan`会找到带有`@Controller`注解的类，因此，我们不需要在配置类中显式声明控制器。

`ViewResolver`会按照设定的前后缀查找JSP文件。

`configureDefaultServletHandling()`方法通过调用`DefaultServletHandlerConfigurer`的`enable()`方法，要求`DispatcherServlet`将对静态资源的请求转发到`Servlet容器`中默认的Servlet上，而不是`DispatcherServlet`本身来处理此类要求。

## Root配置类

作用：加载自定义的业务组件。

```java
package com.yww.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@ComponentScan(
        basePackages = {"com.yww"},
        excludeFilters = {@Filter(type= FilterType.ANNOTATION, value = EnableWebMvc.class)}
)
public class RootConfig {
}
```

# web.xml中声明DispatcherServlet

> 使用java配置的方式，`AbstractAnnotationConfigDispatcherServletInitializer`会自动注册`DispatcherServlet`和`ContextLoaderListener`。而使用web.xml配置，需要手动配置。

```xml
<!-- web.xml -->
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

`ContextLoaderListener`和`DispatcherServlet`前面提到过，它们各自会加载一个Spring应用上下文。

- **`ContextLoaderListener`**：上下文参数`contextConfigLocation`指定了一个XML文件的地址，这个文件定义了根应用上下文，它会被`ContextLoaderListener`加载。根上下文会从`/WEB-INF/applicationContext.xml`中加载bean定义。

- **`DispatcherServlet`**：`DispatcherServlet`会根据Servlet的名字找到一个文件，并基于该文件加载应用上下文。这里Servlet的名字为`dispatcher`，因此`DispatcherServlet`会从`/WEB-INF/dispatcher-servlet.xml`文件中加载其应用上下文。（也可以使用`<init-param>`指定文件位置。如下）

```xml
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/spring/servlet-context.xml</param-value>
        </init-param>
    </servlet>
```

# 配置DispatcherServlet 更多
在`AbstractAnnotationConfigDispatcherServletInitializer`将`DispatcherServlet`注册到`Servlet容器`中之后，就会调用`customizeRegistration()`，并将Servlet注册后得到的`Registration.Dynamic`传递进来。通过重载`customizeRegistration()`方法，我们可以对`DispatcherServlet`进行额外的配置。

> 可用于开启对`multipart请求`和`文件上传`等。

## 添加其它的Servlet和Filter
`AbstractAnnotationConfigDispatcherServletInitializer`会创建`DispatcherServlet`和`ContextLoaderListener`。

如果自己想创建`Servlet`,`Filter`,`Listener`，（就是往`Web容器`中注册其它组件），只需要创建一个新的初始化器就可以了。最简单的方式就是实现Spring的`WebApplicationInitializer`接口。

```java
// MyServletInitializer.java
package com.yww.config;

import org.springframework.web.WebApplicationInitializer;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;


public class MyServletInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        // 注册Servlet
        javax.servlet.ServletRegistration.Dynamic myServlet = servletContext.addServlet("myServlet", MyServlet.class);
        myServlet.addMapping("/custom/**");

        // 注册Filter
        javax.servlet.FilterRegistration.Dynamic myFilter = servletContext.addFilter("myFilter", MyFilter.class);
//        myFilter.addMappingForServletNames(null, false, "myServlet");
        myFilter.addMappingForUrlPatterns(null, false, "/custom/*");
    }
}
```

如果只是注册`Filter`，并且该`Filter`只会映射到`DispatcherServlet`上的话。可以重载`AbstractAnnotationConfigDispatcherServletInitializer`的`getServletFilters()`方法。

```java
// MyWebAppInitializer.java
package com.yww.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

import javax.servlet.Filter;

public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Filter[] getServletFilters() {
        return new Filter[]{ new MyFilter()};
    }
}
```
