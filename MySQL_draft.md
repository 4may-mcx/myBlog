## MySQL

### 1. 起步

#### 1.1 MySQL默认的数据库： 

- **infomation_schema**：信息数据库，其中包括MySQL在维护的其他数据库、表、 列、访问权限等信息

- **performance_schema**：性能数据库，记录着MySQL Server数据库引擎在运行 过程中的一些资源消耗相关的信息；

- **mysql**：用于存储数据库管理者的用户信息、权限信息以及一些日志信息等；

- **sys**：相当于是一个简易版的performance_schema，将性能数据库中的数据汇 总成更容易理解的形式；



### 2. 终端操作

- `show databases;` ：显示所有数据库
- `create database <databaseName>` ：创建新的数据库
- `use <databaseName>` ：选择使用的数据库
- `create table <tableName>()` ：在选中的数据库中建表