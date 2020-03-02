---
title: '[mybatis] mybatis快速开始'
date: 2019-10-14 14:05:00
categories: 
    - 'mybatis'
tags:
    - 'web'
    - 'mybatis'
---

# JDBC

传统的jdbc程序。

```java
import java.sql.*;

public class Main {

    public static void main(String[] args){
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;

        try {
            // 加载数据库驱动
            Class.forName("com.mysql.jdbc.Driver");
            // 通过驱动管理获取数据库连接
            connection = DriverManager.getConnection(
                    "jdbc:mysql://localhost:3306/demo_db",
                    "root",
                    "123456");
            // 获取预处理
            preparedStatement = connection.prepareStatement("select * from user_t where id = ?");
            // 设置参数
            preparedStatement.setString(1, "1001");
            // 执行查询语句
            resultSet = preparedStatement.executeQuery();
            // 遍历查询结果集
            while (resultSet.next()){
                System.out.println(resultSet.getString("name"));
            }

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //释放资源
            if(resultSet != null){
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if(preparedStatement != null){
                try {
                    preparedStatement.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if(connection != null){
                try {
                    connection.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

# Mybatis

使用mybatis做ORM映射的程序。

## 目录结构

```
.
├── build.gradle
├── settings.gradle
└── src/
    ├── main/
    │   ├── java/
    │   │   └── com/
    │   │       └── yww/
    │   │           ├── dao/
    │   │           │   └── UserDao.java
    │   │           ├── Main.java
    │   │           └── po/
    │   │               └── User.java
    │   └── resources/
    │       ├── com/
    │       │   └── yww/
    │       │       └── dao/
    │       │           └── user-mapper.xml
    │       └── mybatis-config.xml
    └── test/
        ├── java/
        └── resources/
```

## 准备数据库

- 设置账户：root
- 设置密码：123456

```sql
DROP DATABASE demo_db;

CREATE DATABASE if NOT exists demo_db;
use demo_db;
create table if NOT exists user_t(
    id int auto_increment,
    name VARCHAR(10),
    passwd VARCHAR(20),
    PRIMARY KEY (id)
) engine=InnoDB;

INSERT INTO user_t(id, name, passwd)
VALUES
(1001, "admin", "123456"),
(1002, "yww", "123456");

SELECT * from user_t;
```

## 代码

- build.gradle


```gradle
plugins {
    id 'java'
}

group 'com.yww'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile "mysql:mysql-connector-java:5.1.48"
    compile "org.mybatis:mybatis:3.5.2"

    testCompile "junit:junit:4.12"
}

jar {
    from {
        configurations.runtime.collect{zipTree(it)}
    }
    manifest {
        attributes 'Main-Class': 'com.yww.Main'
    }
}
```

- User.java

映射类

```java
package com.yww.po;

public class User {
    private int id;
    private String name;
    private String passwd;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPasswd() {
        return passwd;
    }

    public void setPasswd(String passwd) {
        this.passwd = passwd;
    }
}
```

- UserDao.java

映射方法

```java
package com.yww.dao;

import com.yww.po.User;

import java.util.List;

public interface UserDao {

    public void insert(User user);

    public User findUserById(int userId);

    public List<User> findALLUsers();
}
```

- mybatis-config.xml

mybatis配置文件（文件名任意），这里主要配置了mysql的连接。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <environments default="development">
        <environment id="development">
<!--            配置事务管理器的类型-->
            <transactionManager type="JDBC"/>
<!--            配置数据源的类型，以及数据库连接的相关信息-->
            <dataSource type="POOLED">

              <property name="driver" value="com.mysql.jdbc.Driver"/>
              <property name="url" value="jdbc:mysql://localhost:3306/demo_db"/>
              <property name="username" value="root"/>
              <property name="password" value="123456"/>

            </dataSource>
        </environment>
    </environments>

    <!-- 映射文件 -->
    <mappers>
        <mapper resource="com/yww/dao/user-mapper.xml"/>
    </mappers>

</configuration>
```

- user-mapper.xml

映射配置文件。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//ibatis.apache.org//DTD Mapper 3.0//EN"
        "http://ibatis.apache.org/dtd/ibatis-3-mapper.dtd">

<mapper namespace="com.yww.dao.UserDao">

    <select id="findUserById" resultType="com.yww.po.User">
      select * from user_t where id = #{id}
    </select>

</mapper>
```

- Main.java

主函数展示如何使用mybatis。

```java
package com.yww;

import com.yww.dao.UserDao;
import com.yww.po.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

public class Main {

    public static void main(String[] args) throws Exception {

        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sessionFactory.openSession();
        UserDao userMapper = sqlSession.getMapper(UserDao.class);
        User user = userMapper.findUserById(1001);
        System.out.println(user);
    }
}
```
