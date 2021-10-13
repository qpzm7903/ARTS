1、mybatis是什么？

是一个持久层框架

2、mybatis提供什么功能

它支持自定义 SQL、存储过程以及高级映射

3、组成部分

POJO和数据库表映射

4、底层技术

JDBC

5、同类实现

JPA、Hibernate等





# JDBC

https://en.wikipedia.org/wiki/Java_Database_Connectivity

> JDBC 4.1, is specified by a maintenance release 1 of JSR 221[[3\]](https://en.wikipedia.org/wiki/Java_Database_Connectivity#cite_note-3) and is included in Java SE 7.[[4\]](https://en.wikipedia.org/wiki/Java_Database_Connectivity#cite_note-4)
>
> JDBC 4.2, is specified by a maintenance release 2 of JSR 221[[5\]](https://en.wikipedia.org/wiki/Java_Database_Connectivity#cite_note-5) and is included in Java SE 8.[[6\]](https://en.wikipedia.org/wiki/Java_Database_Connectivity#cite_note-6)
>
> The latest version, JDBC 4.3, is specified by a maintenance release 3 of JSR 221[[7\]](https://en.wikipedia.org/wiki/Java_Database_Connectivity#cite_note-7) and is included in Java SE 9.[[8\]](https://en.wikipedia.org/wiki/Java_Database_Connectivity#cite_note-8)



[4.2规范文档](https://download.oracle.com/otndocs/jcp/jdbc-4_2-mrel2-spec/index.html)





JDBC基础

- statement
  - 发送给数据库服务器
- preparedStatement
  - 预处理的、缓存的statement
- CallableStatement
  - 用于执行存储过程的statement



JDBC驱动

驱动是客户端的适配器，将java代码转为数据库服务器能理解的协议。

# 用法



首先看Mybatis入门用法





