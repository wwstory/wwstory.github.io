---
title: '[spring] ApplicationContext'
date: 2019-10-04 18:37:00
tags:
    - 'web'
    - 'spring'
---

# ApplicationContext

在构建非web应用时，发现了些问题，比如理所当然的使用`@Autowired`在主函数想要获取bean，却失败了，得到了`null`。

这是非web应用的细节问题，了解这些可以帮助构建一个非web应用，也可以帮助处理掉《spring实战》源码的运行。

## app如何获取bean

在`非web`应用中，会发现一个问题，无法通过`@Autowired`获取到bean，这是由于非web应用无法知道bean，也没有提供相应的注解去处理，只能通过`ApplicationContext`应用上下文获取bean。而bean之间是可以通过`@Autowired`获取到的。

```java
ApplicationContext context = new AnnotationConfigApplicationContext(com.example.DemoConfig.class);
A a = (A) context.getBean(A.class);
```


## web中为何能使用

相应的`web`应用中，我们通常使用`spring mvc`的`@Controller`去操作，而这已经是被封装过的了，对每个控制器实则是在写`控制器bean`。（源码`HandlerMethod.class`的`createWithResolvedBean`方法中`getBean`操作展示了出来）
```java
public HandlerMethod createWithResolvedBean() {
    Object handler = this.bean;
    if (this.bean instanceof String) {
        Assert.state(this.beanFactory != null, "Cannot resolve bean name without BeanFactory");
        String beanName = (String) this.bean;
        handler = this.beanFactory.getBean(beanName);
    }
    return new HandlerMethod(this, handler);
}
```

组件扫描也是，需要通过设配置文件（xml配置或java配置）到`ApplicationContext`中，从上下文获取其它组件扫描到的bean。

> 组件扫描的测试却可用`@ContextConfiguration`设置配置文件，从而处于可以直接使用`@Autowired`的上下文环境。


# 参考

HandlerMethod 331行 : https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/method/HandlerMethod.java