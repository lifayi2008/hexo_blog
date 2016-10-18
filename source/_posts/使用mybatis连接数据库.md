---
title: 使用mybatis连接数据库
date: 2016-07-28 18:11:21
tags: [Mybatis, Java]
categories: Java
---

本文展示一个简单使用mybatis连接数据的实例
<!-- more -->

##### 主配置文件

resources/mybatis-config.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}" />
                <property name="url" value="${url}" />
                <property name="username" value="${username}" />
                <property name="password" value="${password}" />
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="mapper/BlogMapper.xml" />
    </mappers>
</configuration>
```

##### 映射文件

resources/mapper/BlogMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yoopig.test.BlogMapper">
    <select id="selectBlog" resultType="com.yoopig.test.Blog" parameterType="int">      //注意这里的resulType一定是全类名
        select * from blog where id = #{id}
    </select>
</mapper>
```

##### properties配置文件

resource/config/database.properties
```java
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.1.202:3306/blog
username=test
password=test
```

##### 映射接口、javabean和测试类

java/com/yoopig/test/BlogMapper.java
```java
package com.yoopig.test;

public interface BlogMapper {
    Blog selectBlog(int id);
}
```

java/com/yoopig/test/Blog.java
```java
package com.yoopig.test;

import java.util.Date;

public class Blog {
    int id;
    String username;
    String password;
    String email;
    Date date;

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

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Date getDate() {
        return date;
    }

    public void setDate(Date date) {
        this.date = date;
    }

    @Override
    public String toString() {
        return "Blog{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", email='" + email + '\'' +
                ", date=" + date +
                '}';
    }
}
```

java/com/yoopig/test/Test.java
```java
package com.yoopig.test;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class Test {

    public static void main(String[] args) {
        String resource = "mybatis-config.xml";
        Properties properties = new Properties();

        try {
            properties.load(Test.class.getClassLoader().getResourceAsStream("config/database.properties"));
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream, properties);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            BlogMapper blogMapper = sqlSession.getMapper(BlogMapper.class);
            Blog blog = blogMapper.selectBlog(12);
            System.out.println(blog);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
