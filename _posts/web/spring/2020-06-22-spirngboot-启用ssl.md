---
title: '[springboot] 启用ssl'
date: 2020-06-22 17:46:00
tags:
    - 'web'
    - 'springboot'
---

要使用https访问，只需要修改配置。

1. 先创建key

```sh

```

2. 修改配置文件`application.yml`

```yml
server:
    port: 8443
    ssl:
        # key-store: file:///tmp/yww.jks
        key-store: classpath:yww.jks
        key-store-password: 123456
        key-password: 123456
```

可以指定系统路径，也可指定jar文件的路径。

3. 访问： https://localhost:8443