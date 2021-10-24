https://dev.mysql.com/doc/refman/8.0/en/

https://dev.mysql.com/doc/refman/5.7/en/



本文阅读的是8.0的文档

[概览](https://dev.mysql.com/doc/refman/8.0/en/introduction.html)

# mysql特性





- 用c和c++写的
- 使用多种不同的编译器测试
- 在不同的平台工作
- 使用cmake，提高可移植性
- 采用多层服务器设计，独立模块。
- 设计为使用内核线程实现完全多线程，以便在多个 CPU 可用时轻松使用
- 提供事务性和非事务性存储引擎。
- 使用带有索引压缩的非常快的 B 树磁盘表 (MyISAM)。
- 引擎可扩展
- 使用非常快速的基于线程的内存分配系统。
- 实现内存中的哈希表，用作临时表。
- 使用高度优化的类库实现 SQL 函数，该类库应该尽可能快。通常在查询初始化之后根本没有内存分配。



数据类型

语句和函数

- select、where
- group by ， order by， count avg std max min group concat
- left outer join，right outer join
- table alias
- delete，insert，replace，update
- show，information_schema
- explain

安全



扩展性和限制

- 支持大库，支持5000万条记录，20万张表
- 每张表支持64索引，每个索引包含1-16列



连接性

- 客户端连接服务端，可以支持不同的协议
  - TCP/IP socket
  - windows：name pipe
  - unix：domain socket
- 客户端可以用任何语言写
- ODBC
- JDBC



本地化



客户端和工具

- mysqldump, mysqladmin, mysql workbench
- mysqlcheck, 



# 历史

名称

> MySQL is named after co-founder Monty Widenius's daughter, My.



# 8.0新特性

https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html



- 数据字典
- **Atomic DDL**
- **Upgrade procedure**



# 数据类型



数值类型

日期和时间

字符串

空间数据类型

JSON数据类型

默认值







# 连接



`INNER JOIN` permits rows from either table to appear in the result if and only if both tables meet the conditions specified in the `ON` clause.



# 外键

ON UPDATE CASCADE       

ON DELETE CASCADE



# 用户定义变量

https://dev.mysql.com/doc/refman/8.0/en/user-variables.html





# 自增长

AUTO_INCREMENT





# SQL语句

https://dev.mysql.com/doc/refman/8.0/en/sql-statements.html



# 问题列表

Mysql8.0 mysql 5.7的区别在哪里



