---
title: '[spring] proxyMode'
date: 2019-10-06 18:28:00
tags:
    - 'web'
    - 'spring'
---

# 应用场景

当我们对设置bean的作用域`@Scope`时，有个购物车场景，通常我们将商城的bean设为`Singleton`单例，购物车对于每个用户毫无疑问是单独所有的，设购物车的bean为`Session`。

```java
@Component
@Scope(
        value = WebApplicationContext.SCOPE_SEESION,
        proxyMode = ScopedProxyMode.TARGET_CLASS
)
public class ShoppingCart {
}
```

```java
@Component
public class StoreService {
    private ShoppingCart shoppingCart;

    @Autowired
    public void setShoppingCart(ShoppingCart shoppingCart){
        this.shoppingCart = shoppingCart;
    }
}
```

# 问题

因为`StoreService`是一个单例的bean，会在Spring应用上下文加载的时候创建。当它创建的时候，Spring会试图将`ShoppingCart`的bean注入到setShoppingCart()方法中。

但是`ShoppingCart`的bean是会话作用域的，此时并不存在。直到某个用户进入系统，创建了会话之后，才会出现`ShoppingCart`实例。

另外，系统中将会有多个`ShoppingCart`实例：每个用户一个。我们并不想让Spring注入某个固定的`ShoppingCart`实例到`StoreService`中。因为每个用户应该持有自己哪个购物车。

# 解决

通过给`@Scope`设置`proxyMode`参数。Spring将不会将实际的`ShoppingCart`的bean注入到`StoreService`中。

Spring会注入到一个`ShoppingCart`的bean的代理。这个代理会暴露与`ShoppingCart`相同的方法，所以`StoreService`会认为它就是一个购物车。但是，当`StoreService`调用`ShoppingCart`的方法时，代理会对其进行懒解析并将调用委托给会话作用域内真正的`ShoppingCart`的bean。

# 注意

`ScopedProxyMode.INTERFACES`是用于接口的。

具体的类，需要使用`ScopedProxyMode.TARGET_CLASS`。
