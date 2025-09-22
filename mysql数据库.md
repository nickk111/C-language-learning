后台开发MySQL技术  

- 本课程主要结合后台开发项目用到的MySQL以及校招面试中出现的MySQL重点知识内容进行讲解 
- Win10  + CentOS 7  + MySQL 5.7 
- 侧重实践  + 理论 
- MySQL源码  

[TOC]

# MySQL基础  

## 学习目标

- 说出什么是数据库和常见的数据库
- 能够安装启动和连接MySQL数据库
- 能够说出MySQL的常用的数据类型以及它们的区别和特点
- 能够使用MySQL运算符实现条件查询
- 能够说出MySQL的语法规则
- 能够说出SQL语言的六个组成部分及每部分的功能
- 使用SQL语句操作数据库结构
- 使用SQL语句创建修改以及显示数据库和表的结构
- 能够修改数据库的字符集编码
- 能够对数据库表的字段进行设置约束

## MySQL介绍 

数据库是“按照数据结构来组织、存储和管理数据的仓库”，是一个长期存储在计算机内的、有组织的、可共享的、统一管理的大量数据的集合。 简单来说，就是一个用来存储数据的仓库。当今世界是一个充满着数据的互联网世界，充斥着大量的数据。即这个互联网世界就是数据世界。数据的来源有很多，比如出行记录、消费记录、浏览的网页、发送的消息等等。除了文本类型的数据，图像、音乐、声音都是数据。 

-  关系型数据库  SQLite   
- MySQL目前属于Oracle，大家熟悉的关系型数据库还有SQL Server，Oracle、MySQL，MariaDB，DB2  
- MySQL分为企业版和社区版，其中社区版是完全免费并且开源的  
- MySQL区别于其它关系型数据库很大的一个特点就是支持插件式的存储引擎，支持如InnoDB，MyISAM，Memory等  
- 目前google、腾讯、淘宝、百度、新浪、facebook等公司都在使用MySQL作为数据存储层方案  
- MySQL设计成C/S模型  
- MySQL的服务器模型采用的是I/O复用 + 可伸缩的线程池，是实现网络服务器的经典模型  

## MySQL环境搭建  

https://dev.mysql.com/downloads/  

## 安装mysql-server服务  

### Win10 安装 mysql-server  

**启动服务器**

右键`我的电脑`-->`管理`--> `服务和应用程序`-->`服务`，找到MySQL80这个服务，查看服务的状态。通常情况下，MySQL安装完成以后会自动启动MySQL服务器，如果此时服务器状态不是`正在运行`，可以右键这个服务，选择`启动`，将MySQL服务器开启。

**卸载**

1. 停止MySQL服务：右键`我的电脑`-->`管理`--> `服务和应用程序`-->`服务`，找到MySQL80这个服务，然后右键选择停止。
2. 在控制面板中找到MySQL8.0相关软件，右键进行卸载。
3. 找到MySQL的相关文件夹，默认会有`C:\Program Files\MySQL`和 `C:\ProgramData\MySQL`，删除这两个文件夹。
4. 上面三步完成以后，MySQL就已经从本机上卸载了。但是此时如果 右键`我的电脑`-->`管理`--> `服务和应用程序`-->`服务`，依然能够看到MySQL80这个服务存在。此时需要在DOS界面使用`sc delete MySQL80` 即可将服务删除。
5. 删除环境变量里的MySQL配置。

### CentOS 7 安装 mysql-server  

**1.下载rpm安装包**  

```shell
[admin@localhost Downloads]$ wget http://repo.mysql.com/mysql57-community-release-el7.rpm
--2020-10-03 11:59:55--  http://repo.mysql.com/mysql57-community-release-el7.rpm
 Resolving repo.mysql.com (repo.mysql.com)... 23.45.53.26
 Connecting to repo.mysql.com (repo.mysql.com)|23.45.53.26|:80... connected.
 HTTP request sent, awaiting response... 200 OK
 Length: 25680 (25K) [application/x-redhat-package-manager]
 Saving to: ‘mysql57-community-release-el7.rpm’
100%
[============================================================================>] 
25,680      --.-K/s   in 0.09s 
2020-10-03 11:59:56 (273 KB/s) - ‘mysql57-community-release-el7.rpm’ saved 
[25680/25680]

 [admin@localhost Downloads]$ ls
 mysql57-community-release-el7.rpm
 [admin@localhost Downloads]$ ll /etc/yum.repos.d/mysql*
 ls: cannot access /etc/yum.repos.d/mysql*: No such file or directory
 
 [root@localhost Downloads]# rpm -ivh mysql57-community-release-el7.rpm 
Preparing...                              ################################# [100%]
Updating / installing...
 	1:mysql57-community-release-el7-11    ################################# [100%]
[root@localhost Downloads]# ll /etc/yum.repos.d/mysql*
-rw-r--r--. 1 root root 1838 Apr 27  2017 /etc/yum.repos.d/mysql-community.repo
-rw-r--r--. 1 root root 1885 Apr 27  2017 /etc/yum.repos.d/mysql-community-source.repo
 [root@localhost Downloads]# yum install mysql-server
```

### 启动mysql-server  

超户模式下启动mysqld服务： service mysqld start 

```shell
[root@localhost Downloads]# service mysqld start
 Redirecting to /bin/systemctl start mysqld.service
 
 dys@DanYuesen:~$ netstat -tanp
(No info could be read for "-p": geteuid()=1000 but you should be root.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 10.255.255.254:53       0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      -
tcp        0      0 192.168.83.101:46369    23.53.118.114:80        TIME_WAIT   -
tcp6       0      0 240e:476:2c2:617c:47755 2600:1413:5000:25::1:80 TIME_WAIT   -
```

### 登录mysql-server  

安装mysql默认生成的密码在log日志文件中：  

```shell
[root@localhost Downloads]# grep "password" /var/log/mysqld.log
2020-10-03 01:46:39 58097 [Note] Shutting down plugin 'sha256_password'
2020-10-03 01:46:39 58097 [Note] Shutting down plugin 'mysql_old_password'
2020-10-03 01:46:39 58097 [Note] Shutting down plugin 'mysql_native_password'
2020-10-03T04:10:10.401393Z 1 [Note] A temporary password is generated for 
root@localhost: fCyQpvz2ao*P
2020-10-03T04:10:17.873968Z 2 [Note] Access denied for user 'root'@'localhost' 
(using password: NO)
```

此时通过上面的密码可首次登录mysql server：  

```shell
dys@DanYuesen:~/documents$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 13
Server version: 8.4.5 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.16 sec)
mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

根据提示必须修改初始密码并重新测试登录： 

```shell 
mysql> set global validate_password_policy=0;
 Query OK, 0 rows affected (0.00 sec)
 mysql> set global validate_password_length=1;
 Query OK, 0 rows affected (0.00 sec)
 mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
 Query OK, 0 rows affected (0.00 sec)
 mysql> quit
Bye
 [root@localhost Downloads]# mysql -u root -p
 Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
 Your MySQL connection id is 7
 Server version: 5.7.31 MySQL Community Server (GPL)
 Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
 Oracle is a registered trademark of Oracle Corporation and/or its
 affiliates. Other names may be trademarks of their respective
 owners.
 Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 mysql>
```

### 目录介绍  

/var/lib/mysql/ 数据文件

/etc/my.cnf 配置文件  

## MySQL数据类型  

**MySQL数据类型定义了数据的大小范围，因此使用时选择合适的类型，不仅会降低表占用的磁盘空间，间接减少了磁盘I/O的次数，提高了表的访问效率，而且索引的效率也和数据的类型息息相关。**  

选择合适的字段类型可以降低磁盘IO。

### 数值类型  

![image-20250516103956019](./mysql数据库.assets/image-20250516103956019.png)

浮点类型推荐使用decimal类型（保存为字符串格式）。 

定点数的位数更加长

- float(M,D)
- double(M,D)
- decimal(M,D)
- M 是支持多少个长度, D 是小数点后面的位数

```sql
create table t5 (
    a float(10, 2),
    b double(10, 2),
    c decimal(10, 2)
);
```

### 字符串类型  

![image-20250516104010659](./mysql数据库.assets/image-20250516104010659.png)

面试题1：age INT(9)

整形占用内存的大小是固定的，和具体的类型时强相关的。(M)只是代表整数显示的宽度。

`CHAR` 与 `VARCHAR` 类型的区别：

1. 允许的最大值范围不同。

   注意：无论是char(n)还是varchar(n),n表示的都是字符数，而不是字节数。

   - char(n)类型里，无论数据库使用的是哪种编码方式，n的最大取值都只能取到255.
   - varchar(n)类型里，如果使用的是Latin1编码，最大值允许到65535(但是实际取值只能到65532,还要保留三个字符用来记录长度);如果使用的是UTF8编码，最大值允许到21845(但是实际取值只能到21844,还要保留一个字符用来记录长度)。

2. 数据库内存存储的长度不同。

   - char(n)类型里，无论输入的内容是什么，存储的长度都是 n个字符。例如，数据类型char(10)，写入字符串'张三'，在数据库里存入的数据长度是10个字符。
   - varchar(n)类型里，存储的长度是不固定可变的。

3. 对于空格的处理不同。

   - char类型在存储字符串时，会将右侧末尾的空格去掉。
   - varchar类型在存储字符串时，会将右侧末尾的空格保留。

### 日期和时间类型  

![image-20250516104024244](./mysql数据库.assets/image-20250516104024244.png)

日期类型也是做项目过程中，经常使用的类型信息，尤其是TIMESTAMP和DATETIME两个类型，但是注意TIMESTAMP会自动更新时间，非常适合那些需要记录最新更新时间的场景，而DATETIME需要手动更新。  

```sql
create table datetime_test (
    create_at datetime
);

insert into datetime_test values('2019-4-2 16:54:00');
insert into datetime_test values('2019/4/2 16:54:00');
insert into datetime_test values(now());
-- 年份最大支持4个长度
insert into datetime_test values('10000/4/2 16:54:00');  -- 错误
insert into datetime_test values('9999/4/2 16:54:00');
```

timestamp 时间戳类型

1. 时间戳类型在显示方面和datetime是一样的, 在存储上不一样
2. 范围从 1970-1-1 0:0:0 到 2038-1-19 11:14:07
3. 时间戳使用 4 个字节表示
4. 该值大小与存储的位长有关: 2 ** (4* 8 - 1)

```sql
create table timestamp_test (
    create_time timestamp
);

insert into timestamp_test values(now());
insert into timestamp_test values('2038-1-19 11:14:07');  -- 时间戳最大值
insert into timestamp_test values('2038-1-19 11:14:08');  -- 错误
```

### enum和set  

这两个类型，都是限制该字段只能取固定的值，但是枚举字段只能取一个唯一的值，而集合字段可以取任意个值。  

多选一的时候使用的一种数据类型

在前端使用单选框的时候, 枚举类型可以发挥作用

枚举类型的优点:

1. 限制了可选值
2. 节省空间
3. 运行效率高

```sql
create table t6(
    name varchar(32),
    sex enum('男','女','保密') default '保密'
);

-- 枚举类型的计数默认从1开始
insert into t6 set name='guapi',sex=1;
```

SET最多可以有64个不同的成员。类似于复选框, 有多少可以选多少。

```sql
create table t7 (
    name varchar(32),
    hobby set('吃','睡','玩','喝','抽')
);

insert into t7 values('张三','睡,抽,玩,吃,喝');
insert into t7 values('李四','睡,抽');
```

### 布尔型

mysql中的bool类型其实就是数字类型。使用0表示false,非0就表示true.

## MySQL运算符  

### 算数运算符   


![image-20250516104053164](./mysql数据库.assets/image-20250516104053164.png)

### 逻辑运算符  

![image-20250516104101748](./mysql数据库.assets/image-20250516104101748.png)

```mysql
select * from user where sex = 'M' and score >=90.0
select * from user where sex = 'M' or score >=90.0
```

### 比较运算符  


![image-20250516104109730](./mysql数据库.assets/image-20250516104109730.png)

```sql
#使用示例
#不等于
select * from emp where age ！= 24；
select * from emp where age <> 24;
#并且 逻辑与
select * from emp where age>=15 && age <= 25;
select * from emp where age>=15 and age <= 20;
select * from emp where age between 15 and 20; #between跟的是最小值，and跟的是最大值
#或者 逻辑或 其中一个满足即可
select * from emp where age=18 or age = 20 or age = 24;
select * from emp where age in(18,20,40);#in中数值满足其一即可
```

## MySQL常用函数  

### 字符串函数  

### 数值函数  

### 时间和日期函数  

NOW()：返回当前的日期和时间

UNIX_TIMESTAMP(data)：返回日期date的UNIX时间戳

CURRENT_TIMESTAMP、CURRENT_TIMESTAMP() ：当前时间  

CURDATE()：当前日期  

CURTIME()：当前时间DATE_FORMAT(date, "%y-%m-%d")：格式化日期时间等  

### 聚合函数  

count、sum、avg、max、min  

![image-20250612113811012](./mysql数据库.assets/image-20250612113811012.png)

语法：select 聚合函数（字段列表）from 表名；

null值不参与所有聚合函数运算；

```sql
select count(*) from emp;
select count(id) from emp;
```



## MySQL完整性约束  

### **主键约束**

primary key  

### 自增键约束

auto_increment  

### 唯一键约束  

unique  

### 非空约束  

not null  

### 默认值约束  

default  

### 外键约束

foreign key  

```mysql
CREATE TABLE user(
id INT UNSIGNED PRIMARY KEY AUTO INCREMENT COMMENT '用户的主键id',
nickname VARCHAR(50) UNIQUE NOT NULL COMMENT '用户的昵称',
age TINYINT UNSIGNED NOT NULL DEFAULT 18,
sex ENUM('male', 'female"));
```



## 关系型数据库表设计  

产生表的关联，不产生数据冗余。

### 一对一

在子表中增加一列，关联父表的主键

![image-20250712162317763](./mysql数据库.assets/image-20250712162317763.png)

### 一对多

用户-商品: 没有关系

用户-订单:一对多的关系

商品-订单:多对多的关系

![image-20250706210943295](./mysql数据库.assets/image-20250706210943295.png)

### 多对多  

增加一个中间表

![image-20250712162051420](./mysql数据库.assets/image-20250712162051420.png)

![image-20250706211038935](./mysql数据库.assets/image-20250706211038935.png)

## 关系型数据库范式

面试题目：你对数据库的范式是怎么理解的， 遵循范式的直接好处是减少数据的冗余存储，在进行数据的插入、更新、删除的时候，要对多条记录同样的数据进行更新删除。 

**应用数据库范式可以带来许多好处，但是最重要的好处归结为三点：**  

1）减少数据冗余（这是最主要的好处，其他好处都是由此而附带的）  

2）消除异常（插入异常，更新异常，删除异常）  

3）让数据组织的更加和谐  

但是数据库范式绝对不是越高越好，范式越高，意味着表越多，多表联合查询的机率就越大，SQL的效率就变低。  

### 第一范式（1NF）  

> **每一列保持原子特性**  

列都是基本数据项，不能够再进行分割，否则设计成一对多的实体关系。例如表中的地址字段，可以再细分为省，市，区等不可再分割（即原子特性）的字段，如下：  

![image-20250516104148794](./mysql数据库.assets/image-20250516104148794.png)

上图的表就是把地址字段分成更详细的city，country，street三个字段，注意，不符合第一范式不能称作关系型数据库。  

### 第二范式（2NF）  

> **属性完全依赖于主键-主要针对联合主键**  

非主属性完全依赖于主关键字，如果不是完全依赖主键，应该拆分成新的实体，设计成一对多的实体关系。  

例如：选课关系表为SelectCourse(学号, 姓名, 年龄, 课程名称, 成绩, 学分)，（学号，课程名称）是联合主键，但是学分字段只和课程名称有关，和学号无关，相当于只依赖联合主键的其中一个字段，不符合第二范式。  

![image-20250710204309656](./mysql数据库.assets/image-20250710204309656.png)

![image-20250516104204990](./mysql数据库.assets/image-20250516104204990.png)

### 第三范式（3NF）  

**属性不依赖于其它非主属性**  

要求一个数据库表中不包含已在其它表中已包含的非主关键字信息。  

示例：学生关系表为Student（学号， 姓名， 年龄， 所在学院， 学院地点， 学院电话），学号是主键，但是学院电话只依赖于所在学院，并不依赖于主键学号，因此该设计不符合第三范式，应该把学院专门设计成一张表，学生表和学院表，两个是一对多的关系。  

![image-20250516104216029](./mysql数据库.assets/image-20250516104216029.png)

注意：一般关系型数据库满足第三范式就可以了。  

### BC范式（BCNF）  

> **每个表中只有一个候选键**  

简单的说，BC范式是在第三范式的基础上的一种特殊情况，即每个表中只有一个**候选键（在一个数据库中每行的值都不相同，则可称为候选键）**，在上面第三范式的noNF表（上面图3）中可以看出，每一个员工的email都是唯一的（不可能两个人用同一个email），则此表不符合BC范式，对其进行BC范式化后的关系图为:  

![image-20250516104231490](./mysql数据库.assets/image-20250516104231490.png)
消除表中的多值依赖  

### 第四范式（4NF）  

> **消除表中的多值依赖**

简单来说，第四范式就是要消除表中的多值依赖，也就是说可以减少维护数据一致性的工作。比如图4中的noNF表中的skill技能这个字段，有的人是“java，mysql”，有的人描述的是“Java，MySQL”，这样数据就不一致了，解决办法就是将多值属性放入一个新表，所以满足第四范式的关系图如下：  

![image-20250516104241462](./mysql数据库.assets/image-20250516104241462.png)

从上面对于数据库范式进行分解的过程中不难看出，应用的范式越高，表越多。表多会带来很多问题：  

**1、 查询时需要连接多个表，增加了SQL查询的复杂度**  

**2 、查询时需要连接多个表，降低了数据库查询性能**  

因此，并不是应用的范式越高越好，视实际情况而定。**第三范式已经很大程度上减少了数据冗余，并且基本预防了数据插入异常，更新异常，和删除异常了。**  

## MySQL核心SQL  

### 结构化查询语句SQL  

查询的时候，怎么去写sql语句，购买就涉及了这个后台数据的增加删除以及更新，查询涉及select，所以select是我们最常用的，单表的查询，多表的查询，带in的子查询，多表查询的时候有内连接查询，有外连接的左连接跟右连接查询，甚至我们会有两个表，三个表的查询。那我们查询的时候是怎么去写sql语句的啊？我们要注意些什么这个circle语句写出来的效率怎么样？我们要给一个多表联合查询的sql去加一些索引的话，我们该怎么去加？有没有一些指导规规则？

SQL是结构化查询语言（Structure Query Language），它是关系型数据库的通用语言。  

SQL主要可以划分为以下 3 个类别：  

- DDL（Data Definition Languages）语句

  数据定义语言，这些语句定义了不同的数据库、表、列、索引等数据库对象的定义。常用的语句关键字主要包括 **create、drop、alter**等。 

- DML（Data Manipulation Language）语句

  数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据完整性，常用的语句关键字主要包括 **insert、delete、update 和select** 等。  

- DCL（Data Control Language）语句  

  数据控制语句，用于控制不同的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括 **grant、revoke** 等。  

**结构操作	**

在操作数据之前，必须要先创建数据库，在数据库里添加表格，并指定表格里的字段，然后才能在表格里对数据进行增删改查操作。

数据库表结构操作通常包含一下内容：

- 创建数据库
- 查看和修改数据库的字符集和校对集
- 创建表格，指定字段
- 设置字段约束
- 修改表格的字段，包括新增删除以及修改已有字段的属性

### 库操作  

按住 CTRL + L实现清理屏幕操作。

**查询数据库**  

代码上操作数据库的时候，实际上就是在客户端发送相应的sql语句，到mysql-server来进行执行，跟我们在这里边mysql-client shell上执行sql命令，然后通过TCP网络 ，发送到mysql-server上，实际上最后返回结果到我的这个client界面上呈现，其实道理是一样的。

```mysql 
show databases; 
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)
#MySQL服务器在搭建完成以后，默认就会有information_schema，mysql,performance_schema和sys四个数据库。这四个数据库用来存储MysQL里的基本信息
```

**创建数据库**

```mysql
create database ChatDB;  
```

**删除数据库**  

```mysql
drop database ChatDB;  
```

**选择数据库**  

```mysql
use ChatDB;  
```

**查看数据库创建语句**

```mysql
mysql> show create database chatdb;
+----------+-------------------------------------------------------------------------------------------------------+
| Database | Create Database                                                                                       |
+----------+-------------------------------------------------------------------------------------------------------+
| chatdb   | CREATE DATABASE `chatdb` /*!40100 DEFAULT CHARACTER SET utf8mb3 */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+----------+-------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> show create database chatdb\G
*************************** 1. row ***************************
       Database: chatdb
Create Database: CREATE DATABASE `chatdb` /*!40100 DEFAULT CHARACTER SET utf8mb3 */ /*!80016 DEFAULT ENCRYPTION='N' */
1 row in set (0.00 sec)
```

### 表操作  

**查看表**  

```mysql
show tables;  #前提是前面有use 数据库名;的语句
show tables from 数据库名;
mysql> show tables;
+------------------+
| Tables_in_chatdb |
+------------------+
| student          |
| user             |
+------------------+
2 rows in set (0.00 sec)
```

**创建表**  

```mysql
CREATE TABLE user (
    id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT NOT NULL,
    name VARCHAR(50) UNIQUE NOT NULL,
    age TINYINT UNSIGNED NOT NULL,
    sex ENUM('M','F') NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

**查看表结构**  

```mysql
desc user;
mysql> desc user;
+-------+---------------+------+-----+---------+----------------+
| Field | Type          | Null | Key | Default | Extra          |
+-------+---------------+------+-----+---------+----------------+
| id    | int unsigned  | NO   | PRI | NULL    | auto_increment |
| name  | varchar(50)   | NO   |     | NULL    |                |
| age   | tinyint       | NO   |     | NULL    |                |
| sex   | enum('M','W') | NO   |     | NULL    |                |
+-------+---------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)
#删除表 drop table user;

```

**查看建表sql**  

```mysql
show create table user\G  
show create table user

mysql> show create table user\G
*************************** 1. row ***************************
       Table: user
Create Table: CREATE TABLE `user` (
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `age` tinyint NOT NULL,
  `sex` enum('M','W') NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3
1 row in set (0.00 sec)
```

**新增字段**

```sql
alter table [数据库名.]表名称 add [column] 字段名 数据类型;
alter table [数据库名.]表名称 add [column] 字段名 数据类型 first;
alter table [数据库名.]表名称 add [column] 字段名 数据类型 after 另一个字段;
```

```sql
alter table user add address varchar(128);
alter table user add height double after name;

*************************** 原始 ***************************
mysql> desc user;
+-------+---------------+------+-----+---------+----------------+
| Field | Type          | Null | Key | Default | Extra          |
+-------+---------------+------+-----+---------+----------------+
| id    | int unsigned  | NO   | PRI | NULL    | auto_increment |
| name  | varchar(50)   | NO   |     | NULL    |                |
| age   | tinyint       | NO   |     | NULL    |                |
| sex   | enum('M','W') | NO   |     | NULL    |                |
+-------+---------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)
*************************** 新增后 ***************************
mysql> desc user;
+---------+---------------+------+-----+---------+----------------+
| Field   | Type          | Null | Key | Default | Extra          |
+---------+---------------+------+-----+---------+----------------+
| id      | int unsigned  | NO   | PRI | NULL    | auto_increment |
| name    | varchar(50)   | NO   |     | NULL    |                |
| height  | double        | YES  |     | NULL    |                |
| age     | tinyint       | NO   |     | NULL    |                |
| sex     | enum('M','W') | NO   |     | NULL    |                |
| address | varchar(128)  | YES  |     | NULL    |                |
+---------+---------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)
```

**删除字段**

```sql
alter table [数据库名.]表名称 drop [column] 字段名;

alter table student drop height;
```

**修改字段属性**

```sql
alter table [数据库名.]表名称 modify [column] 字段名 新数据类型;

alter table student modify address varchar(256);
```

**修改字段名**

```sql
alter table [数据库名.]表名称 change[column] 旧字段名 新字段名 新数据类型;

alter table student change address city varchar(16);
```

**修改字段的位置**

```sql
alter table [数据库名.]表名称 modify [column]字段名 数据类型 first;
alter table [数据库名.]表名称 modify [column]字段名 数据类型 after 另一个字段;
alter table student modify name varchar(32) after city;
```

**修改表名称**

```sql
alter table 旧表名 rename 新表名;
rename table 旧表名 to 新表名;
alter table student rename students;
```

**删除表**  

```mysql
drop table user;
```

### CRUD操作 

#### **DML语句** 

#### insert增加  

![image-20250712143208408](./mysql数据库.assets/image-20250712143208408.png)

引入数据库连接池。

![image-20250712145505468](./mysql数据库.assets/image-20250712145505468.png)

```mysql
insert into user(nickname, name, age, sex) values('fixbug', 'zhang san', 22, 'M');
insert into user(nickname, name, age, sex) values('666', 'li si', 21, 'W'),('888', 'gao yang', 20, 'M');

insert into [数据库名.]表名称 values(值列表);
#要求值列表的顺序、个数、类型，要与表格中的字段的顺序、个数、类型一一匹配
insert into [数据库名.]表名称(部分字段列表) values(值列表);
#要求列表的顺序、个数、类型，要与前面的(部分字段列表)的顺序、个数、类型一一匹配

insert into [数据库名.]表名称 values(值列表1),(值列表2)...;
insert into [数据库名.]表名称(部分字段列表) values(值列表1),(值列表2)...;

注意：如果一个字段有自增约束，在添加数据时，不要再手动的插入数值，可以使用0或者null来代替。
```

#### update修改  

```sql
#复制一张表格
create table 新表名 like 源表名;
insert into 新表名 select * from 源表名;
```

```mysql
update [数据库名.]表名称 set 字段名1 = 值1,字段名2 = 值2 ... [where 条件];
update user set age=23 where name='zhang san';
update user set age=age+1 where id=3;
```

> **如果没有加where条件，表示修改所有行，这个字段的值**

#### delete删除  

删除自增主键后，在后面的数据上一直加，不会停。

```mysql
delete from [数据库名.]表名称 [where 条件];
delete from user where age=23;
delete from user where age between 20 and 22;
delete from user;
```

> **如果没有where条件，表示删除整张表的数据;**

```sql
truncate [数据库名.]表名称;
```

> **truncate 也可以删除整张表的数据，它的效率更高，但是它不能回滚**

用delete删除整张表和用truncate删除整张表的数据的区别？

（1）truncate速度快

（2）truncate无法回滚

truncate因为底层是把表drop掉，然后新建了一张空表，而delete底层实现是一行一行的删数据。

## DQL

数据库中用来实现查询的语句，我们称之为Data Query Language(简称DQL).在DQL语句中，最基本最常用的就是`select`语句。基本的`select`语句，配合七大查询字句，MysQL内置函数，以及多表查询等，组成了功能复杂强大的DQL.

### 基本查询语句

我们一般说优化都是sql跟索引的优化，一般sql的优化大部分情况也包含索引，索引的这个优化它也离不开一个sql语句，但是我们现在还没有涉及到索引，只是讲sql。但是呢，sql跟索引本身，它也是息息相关的，所以为了给我们后边大家去学索引。

优化的时候跟我们sql的一些知识能够结合起来，所以在这讲解的时候呢，我也会给大家去强调一些需要大家呢。事先比较注意留意的东西啊，

基本的查询使用`select`语句来完成，再配合`from,as,where`等关键字来丰富。

```sql
select 字段1 [as 别名1],字段2[ [as 别名2] ... from [数据库名.]表名称 [where 条件];
select * from [数据库名.]表名称 [where 条件];
```

### select查询  

```mysql
select * from user;
select id,nickname,name,age,sex from user;
select id,name from user;
select id,nickname,name,age,sex from user where sex='M' and age>=20 and age<=25;
select id,nickname,name,age,sex from user where sex='M' and age between 20 and 25;
select id,nickname,name,age,sex from user where sex='W' or age>=22;
select name,age,sex from user where name like  "zhang%";
selece name,age,sex from user where name like "zhang_";
selece name,age,sex from user where name is null;
selece name,age,sex from user where name is not null;
```

#### 去重distinct  

```mysql
select distinct name from user;
```

#### 空值查询  

is [not] null  

```mysql
select * from user where name is null;
```

#### union合并查询  

SELECT expression1, expression2, ... expression_n 
FROM tables[WHERE conditions] 
UNION [ALL | DISTINCT]    # 注意：union默认去重，不用修饰distinct，all表示显示所有重复值SELECT expression1, expression2, ... expression_n 
FROM tables[WHERE conditions];  

```mysql
SELECT country FROM Websites UNION ALL SELECT country FROM apps ORDER BY country;
```

#### 带in子查询  

[NOT] IN(元素1，元素2，...，元素3)  

```mysql
select * from user where id in(10, 20, 30, 40, 50)
select * from user where id not in(10, 20, 30, 40, 50)
select * from user where id in(select stu_id from grade where average>=60.0)
```

#### DQL-分页查询  

语法：
`SELECT 字段列表 FROM 表名 LIMIT 起始索引,查询记录数`
注意
起始索引从0开始，起始索引=(查询页码-1)*每页显示记录数。
如果查询的是第一页数据，起始索引可以省略，直接简写为limit 10。

```mysql
select id,nickname,name,age,sex from user limit 10; 
select id,nickname,name,age,sex from user limit 2000,10;  
```

delimiter  $

create procedure add_t_user (IN n INT) 

BEGIN 
DECLARE i INT; 
SET  i  = 0 ;

WHILE i<n DO 
INSERT INTO t_user VALUES(NULL,CONCAT(i+1,'@fixbug.com'),i+1); 

SET  i = i +1 ; 
END WHILE; 
END $

delimiter ;  

call add_t_user(2000000);  

![image-20250713142631127](./mysql数据库.assets/image-20250713142631127.png)

#### DQL-排序查询-排序order by  

语法：

- SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1,字段2 排序方式2

排序方式：

- ASC:升序(默认值)
- DESC:降序
- 前一个字段相同，按照第二个字段排序。

```mysql
select id,nickname,name,age,sex from user where sex='M' and age>=20 and age<=25 order by age asc; 
select id,nickname,name,age,sex from user where sex='M' and age>=20 and age<=25 order by age desc;
```

#### 分组 group by  

```mysql
select sex from user group by sex;
select age, count(age) as number from user group by age;
select count(id),sex from user group by sex;
select count(id),age from user group by age having age>20;
```

#### 笔试实践问题  

下表bank_bill是某银行代缴话费的主流水表结构：  

| 字段名 | 描述     |
| ------ | -------- |
| serno  | 流水号   |
| date   | 交易日期 |
| accno  | 账号     |
| name   | 姓名     |
| amount | 金额     |
| brno   | 缴费网点 |

1. 统计表中缴费的总笔数和总金额

   select count(serno),sum(amount) from bank_bill;

2. 给出一个sql，按网点和日期统计每个网点每天的营业额，并按照营业额进行倒序排序  

   select brno,date,sum(amount) as money from bank_bill group by brno,date order by brno, money desc;

表结构：

```mysql
CREATE TABLE bank_bill (   
    serno BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    date DATE NOT NULL,
    accno VARCHAR(100) NOT NULL,
    name VARCHAR(50) NOT NULL,
    amount DECIMAL(10,1) NOT NULL,   
    brno VARCHAR(150) NOT NULL,
    PRIMARY KEY (serno)   
);
```

```mysql
INSERT INTO bank_bill VALUES
('101000','2021-03-01','111','zhang',100,'高新区支行'),
('101001','2021-03-01','222','liu',200,'碑林区支行'),     
('101002','2021-03-01','333','gao',300,'高新区支行'),
('101003','2021-03-01','444','lian',150,'雁塔区支行'),
('101004','2021-03-01','555','lan',360,'雁塔区支行'),
('101005','2021-03-01','666','wang',300,'碑林区支行'),     
('101006','2021-03-02','777','wei',500,'碑林区支行'),
('101007','2021-03-02','888','yao',50,'碑林区支行'),
('101008','2021-03-02','111','zhang',100,'高新区支行'),
('101009','2021-03-02','222','liu',200,'雁塔区支行'),    
('101010','2021-03-03','333','gao',300,'高新区支行'),    
('101011','2021-03-03','444','lian',150,'雁塔区支行'),
('101012','2021-03-03','555','lan',360,'雁塔区支行'),
('101013','2021-03-03','666','wang',300,'碑林区支行'); 
```

### DCL

DCL（Data Control Language）语句  

数据控制语句，用于控制不同的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括 **grant、revoke** 等。  

### 连接查询  

表多了，我们有时候要在两个表甚至多个表之间进行查询，一次select涉及到多张表格。 
**如果你是一张一张表select，一个一个取数据的话，是不好的。** 
mysql client 发送一条SQL语句到mysql server，mysql server要做很多校验操作。你发2条不同的SQL，首先，在mysql server处理的流程就要走2遍，而且，1次通信就代表1次完整的TCP的3次握手，整个的处理流程返回结果，和TCP的4次挥手，效率就低了，所以有联合查询。

![image-20250716144707174](./mysql数据库.assets/image-20250716144707174.png)



<img src="./mysql数据库.assets/image-20250713210017143.png" alt="image-20250713210017143" style="zoom:50%;" />

<img src="./mysql数据库.assets/image-20250713210341590.png" alt="image-20250713210341590" style="zoom:50%;" />

一个学生可以考多门课程，一个课程的考试成绩只属于一个学生，一对多的关系，所以在这里边用了一个UID关联了这个附表的主键。

```mysql
#建表操作
CREATE TABLE student(
uid INT UNSIGNED PRIMARY KEY NOT NULL AUTO_INCREMENT, 
name VARCHAR (50) NOT NULL,
age TINYINT UNSIGNED NOT NULL,
sex ENUM('M', 'W') NOT NULL);

CREATE TABLE course(
cid INT UNSIGNED PRIMARY KEY NOT NULL AUTO_INCREMENT,
cname VARCHAR (50) NOT NULL,
credit TINYINT UNSIGNED NOT NULL);

CREATE TABLE exame(
uid INT UNSIGNED NOT NULL,
cid INT UNSIGNED NOT NULL,
time DATE NOT NULL,
score FLOAT NOT NULL,
PRIMARY KEY(uid,cid));
```

```mysql
INSERT INTO student (name, age, sex) VALUES 
('zhangsan', 18, 'M'),
('gaoyang', 20, 'W'),
('chenwei', 22, 'M'),
('linfeng', 21, 'W'),  
('liuxiang', 19, 'W');

INSERT INTO course(cname, credit) VALUES 
('C++基础课程', 5),
('C++高级课程', 10),
('C++项目开发', 8),
('C++算法课程', 12); 

INSERT INTO exame(uid, cid, time, score) VALUES
(1, 1, '2021-04-09', 99.0),
(1, 2, '2021-04-10', 80.0),
(2, 2, '2021-04-10', 90.0),
(2, 3, '2021-04-12', 85.0),
(3, 1, '2021-04-09', 56.0),
(3, 2, '2021-04-10', 93.0),   
(3, 3, '2021-04-12', 89.0),
(3, 4, '2021-04-11', 100.0),   
(4, 4, '2021-04-11', 99.0),   
(5, 2, '2021-04-10', 59.0),
(5, 3, '2021-04-12', 94.0),
(5, 4, '2021-04-11', 95.0); 
```

#### 内连接查询  

SELECT a.属性名1,a.属性名2,...,b,属性名1,b.属性名2... FROM table_name1 a inner join table_name2 b on a.id = b.id where a.属性名 满足某些条件;  

```mysql
#在student里面查zhangsan的数据，然后在exam里面也要查zhangsan的数据，在course查考试成绩的课程名称。
预置条件：uid:1  cid:2 
select score from exame where uid=1 and cid=2;
select a.uid,a.name,a.age,a.sex from student a where a.uid=1;
select c.score from exame c where c.uid=1 and c.cid=2;

// on a.uid=c.uid 区分大表 和 小表，按照数据量来区分，小表永远是整表扫描，然后去大表搜索
// 从student小表中取出所有的a.uid，然后拿着这些uid去exame大表中搜索
// 对于inner join内连接，过滤条件写在where的后面和on连接条件里面，效果是一样的
select a.uid,a.name,a.age,a.sex,c.score from student a
inner join exame c on a.uid=c.uid where c.uid=1 and c.cid=2; 

select b.cid,b.cname,b.credit from course b where b.cid=2;

select a.uid,a.name,a.age,a.sex,b.cid,b.cname,b.credit,c.score 
from exame c 
inner join student a on c.uid=a.uid
inner join course b on c.cid=b.cid
where c.uid=1 and c.cid=2;

select a.uid,a.name,a.age,a.sex,b.cid,b.cname,b.credit,c.score 
from exame c 
inner join student a on c.uid=a.uid
inner join course b on c.cid=b.cid
where c.cid=2 and c.score>=90.0; 

select b.cid,b.cname,b.credit,count(*)
from exame c 
inner join course b on c.cid=b.cid
where c.score>=90.0
group by c.cid
having c.cid=2;

select b.cid,b.cname,b.credit,count(*) cnt
from exame c 
inner join course b on c.cid=b.cid
where c.score>=90.0
group by c.cid
order by cnt;
```

**有关联字段的表才可以进行连接** 
**在进行多张表连接的话，主表写在前面，然后拿主表连接其他的表。**

**内连接和limit 的联系**

**我们打开之前大数据创建的表** 

select * from t_user limit 1000,10;

如果只选择1个id，这个效率差别就很明显了！

select id from t_user limit 1000,10;

**这2个操作告诉我们** ：在做大量偏移的时候，我们select的多与少也是影响偏移的效率。 
**我们一般通过where加索引的方式把M性能的消耗去掉。** 
**但有的时候我们没有办法加。只能limitM N** 
那怎么优化？

如果说，我就是想选择这么多列的数据，但是要它的时间消耗和下面的一样。

 select * from t_user where id>1000 limit 10;

**这个方法很好，利用索引去做。但是我们有时候不知道这个id的值啊！** 
我们只知道要偏移多少而已，只知道要展现那么多列的数据。

创建临时表，只显示10行，但是id是有索引的，都是常量时间！ 

select a.id,a.email,a.password from t_user a inner join (select id from t_user limit 1000,10) b on a.id= b.id;

小表整张表搜索。然后拿小表的id去大表索引搜索。

### 内连接的SQL执行过程是什么？

首先，它会在内连接的SQL语句中，区分大表和小表（以表里的行数），小表永远都是整张表扫描，然后拿小表连接的字段去大表里搜索，搜索完之后的结果就是内连接的结果。 
**如果给内连接的SQL语句增加where条件** ，当然是where条件先执行，因为能不能用到索引是看where后面的字段，和select的字段没有关系，where执行完， **这个大表和小表的身份可能对调** 。然后再去小表整张表扫描，然后拿小表连接的字段去大表里搜索，搜索完之后的结果就是内连接的结果。

### 外连接查询  

explain select a. *,b. *  from student a inner join exame b on a.uid = b.uid;

![image-20250716161026943](./mysql数据库.assets/image-20250716161026943.png)

**a表是小表，b表是大表。因为a是小表，整表扫描，所以实际上没有用到索引。然后在b表是用主键索引搜索**

**我们现在把b表变成小表：** 
先执行b表的整张表扫描，没有用到索引，因为是cid，涉及到多列的索引，多列索引必须是先用到第1列，才可以用到这个联合的索引。 

explain select a. *,b. * from student a inner join exame b on a.uid = b.uid where b.cid = 3;

![image-20250716160717381](./mysql数据库.assets/image-20250716160717381.png)

下面两种写法结果一样，执行计划也是一样的

explain select a. *,b. * from student a inner join exame b on a.uid = b.uid and b.cid = 3;

// 对于inner join内连接，过滤条件写在where的后面和on连接条件里面，效果是一样的

相当于是优化翻译为where了，因为用where的好处是where后面的过滤条件可以通过索引进行过滤，效率是相当快的。

#### 左连接查询  

如果是左连接的话，相当于查询蓝色部分（表1特有的数据）。

SELECT a.属性名列表, b.属性名列表 FROM table_name1 a  LEFT [OUTER]  JOIN table_name2 b ona.id  =  b.id; 

```mysql
// 把left这边的表所有的数据显示出来，在右表中不存在相应数据，则显示NULL
 select a.* from User a left outer join Orderlist b on a.uid=b.uid where 
a.orderid is null;
```

**把left这边的表所有的数据显示出来，在右表中不存在相应数据，则显示NULL** 
**因为7这个同学没有创建考试，在b表中没有数据，所以就显示空。**

#### 右连接查询  

SELECT a.属性名列表, b.属性名列表 FROM table_name1 a RIGHT [OUTER]  JOIN table_name2 b ona.id  =  b.id;  

```mysql
// 把right这边的表所有的数据显示出来，在左表中不存在相应数据，则显示NULL
 select a.* from User a right outer join Orderlist b on a.uid=b.uid where 
b.orderid is null;
```

 **右连接是对右表做1个整张表的搜索，然后去左表通过uid去找，找见的显示出来，找不见的显示null** 
**左连接是对左表做1个整张表的搜索，然后去右表通过uid去找，找见的显示出来，找不见的显示null**

**我们现在想看看哪些人没有参加过考试**  （内连接只能查看哪些人参加过考试，因为是取两张表的共同部分。）

**方法1：** 

select * from student where uid not in (select distinct uid from exame);

in是可以用到索引的。 not in基本上是用不到索引，理所当然，因为不在这个列表里，肯定要搜索完才知道不在列表里面。 
但是not in能不能用到索引和select的列有关系。 
**子查询先查询。uid会重复，我们去重。** 

select distinct uid from exame => 会产生一张中间表存储结果供外面的sql来查询。

not in 对于索引的命中并不高。

中间表不一定产生，用explain去查看，而且MySQL会做优化的。

**方法2：采用左连接**  

select a.* from student a left join exame b on a.uid = b.uid where b.cid is null;

### 外连接和内连接的区别

**我现在想找没有参加某一门课的同学的信息，我们先看看参加某一门课程的同学**

select a.* from student a inner join exame b on a.uid = b.uid where b.cid=3;

**没有参加的就是1，4，6 如果我们这么写**

select a.* from student a left join exame b on a.uid=b.uid where b.cid=3;

为什么上面的和内连接的结果一样？

select a.* from student a left join exame b on a.uid=b.uid and b.cid=3;

这样看起来好像就是左连接了

 select a.* from student a left join exame b on a.uid=b.uid and b.cid=3 where b.cid is null;

**这才是正确的  在外连接，条件加到on和where后面是有区别的。**

**我们用explain来查看** 

这样的话外连接和内连接的效果一模一样，先是用where后面的b.uid=3，把b表过滤了一下，变成了小表了，然后拿b.uid去大表搜索。 
**如果我们在外连接把这个限制条件加到where，处理的结果和内连接一样。**

**所以在外连接的时候，要把过滤条件放在连接的on后面！** 
因为左连接肯定是先搜索a表，左右连接是不区分大小表的，左连接的话肯定是左表要整表搜索，右连接的话肯定是右表要整表搜索。  

![image-20250716165809326](./mysql数据库.assets/image-20250716165809326.png)

**这是外连接的执行过程** 

在写外连接sql语句的时候，查不存在的场景，带有限制条件，

 把限制条件写在连接的on连接后面，where后面只写null值判断就可以了

## MySQL存储引擎  

![image-20250716171915453](./mysql数据库.assets/image-20250716171915453.png)

**服务层**
第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SOL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如 过程、函数等。
**引擎层**
存储引擎真正的负责了MvSOL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。

**插件式的存储引擎，存储引擎可更换，支持不同类型的存储引擎** 
**我们创建的表有3个东西** ：表的结构（字段，字段的类型，有什么约束条件），数据，表的索引（增加查询速度） 
**怎么存储，存储方式是什么？这就是存储引擎直接影响上面内容的存储方式**

**MyISAM** 不支持事务、也不支持外键，索引采用非聚集索引，其优势是访问的速度快，对事务完整性没有要求，以 SELECT、INSERT 为主的应用基本上都可以使用这个存储引擎来创建表。**MyISAM的表在磁盘上存储成 3 个文件，其文件名都和表名相同，扩展名分别是：**  

**.frm（存储表定义）**

**.MYD（MYData，存储数据）**

**.MYI （MYIndex，存储索引）**  

**InnoDB** 存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全，支持自动增长列，外键等功能，索引采用聚集索引，索引和数据存储在同一个文件，**所以InnoDB的表在磁盘上有两个文件，其文件名都和表名相同，扩展名分别是：**  

**.frm（存储表的定义）**

**.ibd（存储数据和索引）**  

为什么在InnoDB，你创建一个表，没有加主键，它自动给你加主键（整型的一个列）呢？ **因为数据和索引是一起存放的。**

**MEMORY** 存储引擎使用存在内存中的内容来创建表。**每个MEMORY 表实际只对应一个磁盘文件，格式是.frm（表结构定义）。**MEMORY 类型的表访问非常快，因为它的数据是放在内存中的，并且**默认使用** HASH 索引（不适合做范围查询），但是一旦服务关闭，表中的数据就会丢失掉。  

show engines;

### 各存储引擎区别  

在回答”MySQL不同的存储引擎有哪些区别？“这样的问题时，主要从以下几点来回答就可以（问题的核心）：  

| **种类** | **锁机制** | **B-树索引** | **哈希索引** | **外键**   | **事务**   | **索引缓存** | **数据缓存** |
| -------- | ---------- | ------------ | ------------ | ---------- | ---------- | ------------ | ------------ |
| MyISAM   | **表锁**   | **支持**     | **不支持**   | **不支持** | **不支持** | **支持**     | **不支持**   |
| InnoDB   | **行锁**   | **支持**     | **不支持**   | **支持**   | **支持**   | **支持**     | **支持**     |
| Memory   | **表锁**   | **支持**     | **支持**     | **不支持** | **不支持** | **支持**     | **支持**     |

**锁机制：**表示数据库在并发请求访问的时候，多个事务在操作时，并发操作的粒度。

**B-树索引和哈希索引：**主要是加速SQL的查询速度。  

**外键：**子表的字段依赖父表的主键，设置两张表的依赖关系。  

**事务：**多个SQL语句，保证它们共同执行的原子操作，要么成功，要么失败，不能只成功一部分，失败需要回滚事务。  

**索引缓存和数据缓存：**和MySQL Server的查询缓存相关，在没有对数据和索引做修改之前，**重复查询可以不用进行磁盘I/O（数据库的性能提升，目的是为了减少磁盘I/O操作来提升数据库访问效率），读取上一次内存中查询的缓存就可以了。**  

## MySQL索引  

**只要是关系型数据库，索引都是其查询最核心的关键**

当表中的数据量到达几十万甚至上百万的时候，SQL查询所花费的时间会很长，导致业务超时出错，此时就需要用索引来加速SQL查询。  

由于索引也是需要存储成索引文件的，因此对索引的使用也会涉及磁盘I/O操作。如果索引创建过多，使用不当，会造成SQL查询时，进行大量无用的磁盘I/O操作，降低了SQL的查询效率，适得其反，因此掌握良好的索引创建原则非常重要！  

索引也是**数据结构**，要对索引列，存储引擎会对这列的值进行排序，当我们索引太多的话，我们对表增加或者减少或者更新元素的时候，都会涉及到索引文件的改动。索引文件改动也涉及到磁盘I/O操作。

### 索引分类  

索引是创建在表上的，是对数据库表中一列或者多列的值进行排序的一种结果。**索引的核心是提高查询的速度！**  

**不用的时候，存储在磁盘上的。索引是要从磁盘上的文件读出来的。**

物理上(聚集索引&非聚集索引)

逻辑上(就是下面阐述的)  

**索引的优点： 提高查询效率**  

**索引的缺点： 索引并非越多越好，过多的索引会导致CPU**使用率居高不下，由于数据的改变，会造成索引文件的改动，过多的磁盘I/O造成CPU负荷太重  

1、普通索引**（二级索引）**：没有任何限制条件，可以给任何类型的字段创建普通索引(创建新表&已创建表，数量是不限的，一张表的一次sql查询只能用一个索引 where a = 1 and  b  =  'M'）

2、唯一性索引：使用UNIQUE修饰的字段，值不能够重复，主键索引就隶属于唯一性索引  

3、主键索引：使用Primary Key修饰的字段会自动创建索引(MyISAM（不会自动添加主键）, InnoDB（没有加主键，会默认增加1个整型字段的主键，因为数据和索引是存储在同一个文件中，必须建索引树，然后在索引树上存储数据))  

4、单列索引：在一个字段上创建索引  

5、多列索引：在表的多个字段上创建索引 (uid+cid，多列索引必须使用到第一个列，才能用到多列索引，否则索引用不上)  

6、全文索引：（支持专业的内容搜索，留言板，评论）**MyISAM支持**。使用FULLTEXT参数可以设置全文索引，只支持CHAR，VARCHAR和TEXT类型的字段上，常用于数据量较大的字符串类型上，**可以提高查询速度**(线上项目支持专门的搜索功能，给后台服务器增加专门的搜索引擎支持快速高校的搜索  elasticsearch 简称es C  ++ 开源的搜索引擎 搜狗的workflow)  ， 用于字符串的搜索。

### 索引创建和删除  

创建表的时候指定索引字段：

```mysql
CREATE TABLE index1(id INT,
 	name VARCHAR(20),
 	sex ENUM('male', 'female'),
 	INDEX(id,name));
```

现在建立索引，对id进行排序。INDEX(id) 因为id没有被修饰成主键，所以是单列索引，是普通索引，是二级索引。

**还可以这样：INDEX(id,name) 这个就是多列索引了，先对id排序，id相同的对name排序。**

在已经创建的表上添加索引：  

```mysql
CREATE [UNIQUE] INDEX 索引名 ON 表名（属性名（length） [ASC | DESC]);
```

删除索引:  

```mysql
DROP INDEX 索引名 ON 表名;  
```

![image-20250718111359748](./mysql数据库.assets/image-20250718111359748.png)

我们看到，type是const，所以使用到了主键或者唯一键索引精确匹配。然后看到key是primary，所以确定是使用了主键索引。主键索引的长度是4。扫描了1行。是从索引树上直接找到了数据，是常量时间，不管顺序，不管几行数据，都是直接命中主键索引树的相应记录，耗时间是一样的。

我们用name去过滤看看。因为name没有索引，所以不管是什么顺序，哪怕数据在第一行，都是进行整张表扫描，type是all，key是null，rows是6。
![image-20250718111556011](./mysql数据库.assets/image-20250718111556011.png)

**我们现在给name创建索引：** 

create index nameidx on student(name);

到时候以name过滤，就是用name索引加速搜索
但是，不一定是添加索引了，到时候作为过滤条件就一定能使用到索引，因为MySQL server有优化，它会先进行分析，如果发现使用索引最后得到的数据基本上也是所有数据的大概百分之七八十左右，其实是不会使用索引的，因为如果花费差不多，读索引文件花费磁盘I/O，还要扫描索引树，数据取不完，还要到整个数据表上取数据，还不如直接整张表搜索取数据。
![image-20250718112227669](./mysql数据库.assets/image-20250718112227669.png)

**ref是搜索索引树。key的值是nameidx，表示用到了索引，索引长度是152。还有一个优化是：对于字符串列，在增加索引的时候，会给索引定义长度，把前几个字符可以区分出来不同就可以了，没必要整个列建立索引。**
**现在是不管顺序都只扫1行就可以了。**

**我们继续看**

这是因为存储引擎索引和数据是有缓存的，同样的操作，第一次花费时间长，第二次花费时间短，因为是做同样的查询，中间没有更新（增加删除更新），第一次查询后的结果会放在数据缓存或者索引缓存里，第二次就不用花费磁盘I/O从磁盘读取索引了。

**我们看看下面问题，加了索引，感觉时间还是比较久**

![image-20250718112917349](./mysql数据库.assets/image-20250718112917349.png)

**但是当我们去创建索引的时候，没有考虑到字符串有多长，因为越长越不好。**
**我们要指定索引的长度：**

**显示已经创建了索引，但是为什么没有用到索引呢？**
**还有一个问题是**：password是字符串类型，而我们写的时候没有加’ '，是默认整型，MySQL在做过滤的时候涉及到了类型转换，把整型转成字符串类型。**如果SQL涉及到类型转换，就不能用到索引了！！！**

select * from t_user where password='10000';

删除索引：drop index pwdidx on t_user;

#### 定义索引的注意问题

1.经常作为where条件过滤的字段考虑添加索引  

2.字符串列创建索引时，尽量规定索引的长度，而不能让索引值的长度key_len过长  

3.索引字段涉及类型强转、mysql函数调用、表达式计算等，索引就用不上了  

## 索引的执行过程  

### explain查看执行计划  

使用explain查看sql的执行计划，分析索引的执行过程，mysql的user权限表示例如下：  

```shell
mysql> explain select Host,User from user where Host='%'\G
 *************************** 1. row ***************************
 id: 1
 select_type: SIMPLE
 table: user
 partitions: NULL
 type: ref
 possible_keys: PRIMARY
 key: PRIMARY
 key_len: 180
 ref: const
 rows: 1
 filtered: 100.00
 Extra: Using index
 1 row in set, 1 warning (0.00 sec)
```

可以看到使用了主键索引，共扫描1行，Using index表示直接从索引树上查询到结果，不需要回表。  

```shell
mysql> explain select Host,User from user where User='root'\G
 *************************** 1. row ***************************
 id: 1
 select_type: SIMPLE
 table: user
 partitions: NULL
 type: index
 possible_keys: NULL
 key: PRIMARY
 key_len: 276
 ref: NULL
 rows: 4
 filtered: 25.00
 Extra: Using where; Using index
 1 row in set, 1 warning (0.00 sec)
 mysql> explain select * from user where User='root'\G
 *************************** 1. row ***************************
 id: 1
select_type: SIMPLE
 table: user
 partitions: NULL
 type: ALL
 possible_keys: NULL
 key: NULL
 key_len: NULL
 ref: NULL
 rows: 4
 filtered: 25.00
 Extra: Using where
 1 row in set, 1 warning (0.00 sec)
```

### explain结果字段分析  

- **select_type**  

simple：表示不需要union操作或者不包含子查询的简单select语句。有连接查询时，外层的查询为simple且只有一个。  

primary：一个需要union操作或者含有子查询的select，位于最外层的单位查询的select_type即为primary且只有一个。  

union：union连接的两个select查询，除了第一个表外，第二个以后的表的select_type都是union。  

union result：包含union的结果集，在union和union all语句中，因为它不需要参与查询，所以id字段为null。  

- **table**

显示查询的表名；  

如果不涉及对数据库操作，这里显示null； 
如果显示为尖括号就表示这是个临时表，后边的N就是执行计划中的id，表示结果来自于这个查询产生的； 
如果是尖括号括起来<union M,N>也是一个临时表，表示这个结果来自于union查询的id为M，N的结果集；  

- **type**  

const：使用唯一索引或者主键，返回记录一定是1行记录的等值where条件时，通常type就是const。  

ref：常见于辅助索引的等值查找，或者多列主键、唯一索引中，使用第一个列之外的列作为等值查找会出现；返回数据不唯一的等值查找也会出现。  

range：索引范围扫描，常见于使用<、>、is null、between、in、like等运算符的查询中。  

index：索引全表扫描，把索引从头到尾扫一遍；常见于使用索引列就可以处理不需要读取数据文件的查询，可以使用索引排序或者分组的查询。  

all：全表扫描数据文件，然后在server层进行过滤返回符合要求的记录。  

- **ref**  

​	如果使用常数等值查询，这里显示const； 
​	如果是连接查询，被驱动表的执行计划这里会显示驱动表的关联字段；  

- **Extra**  
  - using filesort：排序时无法用到索引，常见于order by和group by语句中。  
  - using index：查询时不需要回表查询，直接通过索引就可以获取查询的数据。  

![image-20250720102336045](./mysql数据库.assets/image-20250720102336045.png)

### 索引的底层实现原理  

> **数据库索引是存储在磁盘上的，当数据量大时，就不能把整个索引全部加载到内存了，只能逐一加载每一个磁盘块（对应索引树的节点），索引树越低，越“矮胖”，磁盘IO次数就少**  

MySQL支持两种索引，一种的B-树索引，一种是哈希索引，大家知道，B-树和哈希表在数据查询时的效率是非常高的。  

这里我们主要讨论一下MySQL InnoDB存储引擎，基于B-树（但实际上MySQL采用的是B +树结构）的索引结构。  

B-树是一种m阶平衡树，叶子节点都在同一层，由于每一个节点存储的数据量比较大，索引整个B-树的层数是非常低的，基本上不超过三层。  

由于磁盘的读取也是按block块操作的（内存是按page页面操作的，一般是16k，是内存页面的整数倍，读1块，刚好放到4个内存页面上），因此B-树的节点大小一般设置为和磁盘块大小一致，这样一个B-树节点，就可以通过一次磁盘I/O把一个磁盘块的数据全部存储下来，所以当使用B-树存储索引的时候，磁盘I/O的操作次数是最少的（MySQL的读写效率，主要集中在磁盘I/O上）。  

数据和索引都是放在磁盘上的，MySQL server不可能直接从磁盘上读取数据，得通过操作系统把磁盘上的数据加载到内存中，也就是说，运行起来的进程要访问索引，需要花费磁盘I/O，先把数据，索引读到内存当中，这个磁盘I/O当然是少的好，磁盘I/O影响效率。

在我们的C/C++语言中，如果我们new/malloc向内存申请4个字节，实际上不可能只拿4个字节，内存管理是按页面4K为大小单位的，操作系统相当于批发站，它批发内存是以页面为单位的，我们申请4个字节，实际上我们向内核kernel申请，内核是按页面给我们分配的。按页面分配以后，但是我们的应用程序只需要4个字节，还剩下的字节就被libc.so或者 libc++.so库的ptmalloc（缓存），tcmalloc来管理，相当于2个函数，管理剩下的空闲的字节，等你下次还malloc申请字节的时候，就不用向内核空间申请，直接在用户空间，从c库分配出来就可以了。等用光了，才向内核申请。
**假设有2千万的数据，要进行读取，如果是平衡的二叉树，一个节点1个数据，2个指针，二叉平衡树构建2千万的数据，树有多少层？log2000w， 25层。 **

![image-20250718133421709](./mysql数据库.assets/image-20250718133421709.png)

**在最坏的情况下，读取一个索引，要花费25次磁盘I/O** 

##### B-树  

涉及到磁盘到内存的一些读取，我们一般都采用B-树结构。
首先，B-树是平衡树，所有叶子节点都在一层，AVL树是平衡二叉树（左右子树高度差不会超过1），搜索的时间复杂度是O(logn)，二分搜索也是。

我们称B-树是m阶平衡树，一般取值300-500。
如果我们用B-树构建数据结构来存储2千万的索引，假如m取500，

**最多3层，最多3次磁盘I/O就可以了**
**最好的情况是一次磁盘I/O读取的磁盘块的内容，刚好存储在B树的第1个节点中。**  

![image-20250718141327968](./mysql数据库.assets/image-20250718141327968-1752819210164-1.png)

**我们把uid设置成了主键，主键会自动创建索引，**
当我们进行一些查询操作的时候，

![image-20250718141355744](./mysql数据库.assets/image-20250718141355744.png)

**一看uid有索引，请求存储引擎，请求kernel，花费磁盘I/O从磁盘上读索引文件到内存上，然后拿读取的索引的数据构建B-树来加速搜索。**

![image-20250516110432614](./mysql数据库.assets/image-20250516110432614.png)

关于操作系统从磁盘读取索引文件到内存中的疑问解答
在这里进行一些解释：
1、索引文件在磁盘上存储，磁盘的索引文件中的索引就是已经按B+树构建好的了？
那肯定不是哦，磁盘上只是存储的二进制文件，读取索引文件的时候，在内存上构建一颗B+树存放磁盘上读来的索引数据
数据结构都是在内存上表示的，没有说磁盘上构建个数据结构。

2、那操作系统把磁盘的索引文件读到内存上构建B+树，如果磁盘的索引文件太大，内存读不下怎么办？那磁盘IO怎么算次数，现在不是都在内存上的B+树搜索读取数据了吗？
肯定不是读整个文件的，平衡树怎么搜索的？
先读索引文件的前几个字节，里面有第一个要读取的根节点数据在索引文件中的偏移量，读一个根节点后，根据你要搜索的数据进行搜索，看是接着加载他的哪个孩子节点。包括根节点的每一个节点，都存储了索引key值和它的孩子节点在磁盘上的位置偏移量信息的。
这样每一次搜索，最多只从根节点沿着某个路径加载到叶子节点上，怎么可能整个索引文件都加载呢？？？

总结：索引文件在磁盘上是二进制的，但是文件中存储了根节点的key值和这个节点的整个的偏移量，还存储的它的左右孩子的key值和整个节点的偏移量。操作系统从磁盘的索引文件中，一次读取一个块的大小，也就是一个节点到内存中构建B+树，然后在节点中二分搜索元素，如果发现值大于根节点的所有数据值，那么就继续从磁盘的索引文件中把该节点的右孩子节点加载到内存上，然后进行二分搜索查找，以此类推下去。

**key是建索引的列**，17, 35 , 8，12等紫色的这些数字是key值，左孩子的值比父节点的值小，父节点的值比右孩子的值小，这是平衡树的概念，
黄色的data表示key索引所在的这一行的数据，data存储的是数据本身内容，还是数据在磁盘上的地址？
这得看我们使用哪种存储引擎。如果我们用的是MyISAM存储引擎，索引和数据是在不同的文件存储，上图这构建的是索引树，存储的自然是在磁盘的地址了，通过索引值在磁盘上找到包含这个索引值的记录，然后通过地址在磁盘上读取数据文件中相应的数据。
如果我们用的是InnoDB存储引擎，数据和索引是在一块放着的，索引树上放的就是数据，没有索引树，数据没地方放。即使没有加主键，也会自动创建主键和索引。

这样一来，在搜索的时候，比如我们要搜索28，但是一个B树节点里面的数据很多，不止在图中画的17和35两个节点，m取值是300-500。如果m是取500的B树，相当于一个节点有500个指针域，指向500个孩子，有499个数据域。在平衡树中，在一个节点里，里面的数据也是从小到大排序的。在一个节点里，有很多数据域，当我们想要去找28时，在一个节点里面怎么搜？肯定是二分搜索。 时间复杂度：O(log~2~n)
选择B树结构的好处是：非常少的磁盘I/O啊

**如果uid没有索引的话，搜索的时候，是整张表一行一行的搜索下去，不在索引树上搜索了。**

![image-20250719134223117](./mysql数据库.assets/image-20250719134223117.png)

如果name没有索引的话，如果在MyISAM中，就是把整张表过1遍，效率非常低。加个limit 1，效率到是提高一些。
如果我们给name建1个索引的话，当我们再去执行这个select语句的时候，MySQL server的分析器就知道name有索引，直接加载name的索引文件，花费磁盘I/O，把磁盘上的数据加载到内存中来，构建成B-树。name就是相当于上图中的紫色数据。然后在加载到内存的节点里进行二分搜索。

从上图可以看到B-树存在的缺点：
每个节点中有key，也有data域，但是每一个节点的存储空间是有限的，如果data数据较大时会导致每个节点能存储的key的数据很小。
当存储的数据量很大时同样会导致B-树的高度较大，磁盘IO次数花费增大，效率降低！！！

从上图可以看到B-树存在的缺点：  

- 每个节点中有key，也有data，但是每一个节点的存储空间是有限的，如果data数据较大时会导致每个节点能存储的key的数据很小
- 当存储的数据量很大时同样会导致B-树的高度较大，磁盘IO次数花费增大，效率降低  

##### B+树  

![image-20250516110459817](./mysql数据库.assets/image-20250516110459817.png)

1、每一个非叶子节点只存放key，不存放data，这样的好处是一个节点存放的key值更多，这样B+树在理论上来说，层数更低一些，磁盘I/O次数少，搜索的效率更好一些。
2、数据都存储在叶子节点上。我们发现，叶子节点上存储了所有的索引值和其对应的data。直接的好处就是搜索每一个索引对应的值data都需要跑到叶子节点上，这样，每一行记录搜索的时间是非常均匀的。
3、叶子节点被串在一个链表当中，形成了一个有序的链表。如果要进行索引树的搜索或者是整表搜索，直接遍历叶子节点的有序链表即可。或者做范围查询的时候，直接遍历叶子节点的有序链表即可。

![image-20250719142000704](./mysql数据库.assets/image-20250719142000704.png)

**那么MySQL最终为什么要采用B+ 树存储索引结构呢，那么看看 B-树 和 B+树 在存储结构上有什么不同？**  

![image-20250719142128708](./mysql数据库.assets/image-20250719142128708.png)

1. B-树的每一个节点，存了关键字和对应的数据地址，而  B + 树的非叶子节点只存关键字，不存数据地址。因此B + 树的每一个非叶子节点存储的关键字是远远多于B-树的，  B + 树的叶子节点存放关键字和数据，因此，从树的高度上来说， B + 树的高度要小于B-树，使用的磁盘I/O次数少，因此查询会更快一些。  
2. B-树由于每个节点都存储关键字和数据，因此离根节点进的数据，查询的就快，离根节点远的数据，查询的就慢；  B + 树所有的数据都存在叶子节点上，因此在B +  树上搜索关键字，找到对应数据的时间是比较平均的，没有快慢之分。  
3. 在B-树上如果做区间查找，遍历的节点是非常多的；  B + 树所有叶子节点被连接成了有序链表结构，因此做整表遍历和区间查找是非常容易的。  

**从一张表进行搜索的话，如果我加了过滤条件，**  ![image-20250719142116796](./mysql数据库.assets/image-20250719142116796.png)

MySQL先检查过滤字段有没有索引，如果没有索引的话，就做整表搜索，效率是非常低的，如果有索引的话，操作系统会给我们从磁盘上的索引文件加载到内存上，用B+树来构建，一个节点刚好对应1个磁盘I/O，非叶子节点存的都是key，所有的key和data都是存储在叶子节点上，花费最少的磁盘I/O次数，以logn的时间复杂度，来找见索引对应的数据。

哈希索引当然是由哈希表实现的，哈希表对数据并不排序，因此不适合做区间查找，效率非常低，需要搜索整个哈希表结构。  

**InnoDB存储引擎**

**数据和索引存储在一起。**
**一张表有2个文件**：student.frm，student.ibd（数据和索引文件）

**场景1（主键索引树）**

**uid是主键，其他字段没有添加任何索引**

![image-20250719143815390](./mysql数据库.assets/image-20250719143815390.png)

```sql
select * from student;
```

**搜索的是整个索引树。**
**主键索引树：如下图**

![image-20250719150833533](./mysql数据库.assets/image-20250719150833533.png)

**整表搜索：从叶子节点的链表上，从左到右遍历，从小到大访问。**

select * from student where uid=5; 等值查询 

select * from student where uid<5; 范围查询

**MySQL看到有过滤条件，uid有索引！！！
如果是做等值查询，相当于从根节点开始，按照二分搜索的方式找到5，就找到5对应的数据data了。
如果是做范围查询，就直接在有序链表中遍历搜索就可以了，直到遍历到5结束遍历。比在B+树上搜索简单的多了。**

![image-20250719145709938](./mysql数据库.assets/image-20250719145709938.png)

**ref的const**一般出现在等值查询比较中，常量时间，直接就找到了。

 select * from student where name='linfeng';

**我们知道，name没有索引，所以就是整张表搜索咯，直接搜有序链表，遍历搜完整个链表！**

**场景2（回表，二级索引树）**

**uid是主键，以name创建了普通索引（二级索引）**
**以name为索引构建的索引树，叫辅助索引树，也叫做二级索引树**
**key是辅助索引字段name的值，然后还有外加uid主键的值。**
二级索引树如下：

![image-20250719150815614](./mysql数据库.assets/image-20250719150815614.png)

如果是：select name from student where name='linfeng';

**因为过滤字段是name，然后name有索引，所以从name的二级索引树上去等值匹配‘linfeng’**

如果是：select uid,name from student where name='linfeng';

**这种情况也是搜索二级索引树就完事了。**

但是如果是：select * from student where name='linfeng';

此句涉及到回表！！！
过滤字段是name，有索引，搜索name的二级索引树，找到linfeng了，但是这里的select是加了*，不仅仅需要uid,name（这2个在二级索引树上就找到了），还需要age,sex，但是这棵二级索引树并没有存age，sex。
所以MySQL的执行过程是：
1、搜索name的二级索引树，找到linfeng对应的主键uid：4
2、然后再拿uid=4回表在主键索引树上搜索uid=4的那一行的记录
回表意味着更多的搜索，也就是有更多的磁盘I/O，所以效率就低了。

如果业务只需要uid,name，就不要写出select *了，这样可以避免回表！

刚才所讲述的过程的实践如下：

![image-20250719151713174](./mysql数据库.assets/image-20250719151713174.png)

Extra出现using index意味着在二级索引树中搜索一下就都搜到了，就完成了。

![image-20250719152330032](./mysql数据库.assets/image-20250719152330032.png)

删除name的索引：

![image-20250719152604548](./mysql数据库.assets/image-20250719152604548.png)

没有用到索引，使用外部排序了。我们看到using filesort，就一定要优化了。

**现在执行：**select * from student where age=20 order by name;

**如果只给age添加索引，行不行？还有什么没有考虑到？**

![image-20250719153038422](./mysql数据库.assets/image-20250719153038422.png)

**我们看到，age命中索引了**，查询age所在的索引树，**但是有回表**！因为select *
但是还有using filesort，因为name没有索引。
**但是一次SQL执行只能用到1个索引哦，不能通过给name加载索引来解决问题。（因为加载索引是要耗费磁盘I/O的）**

**解决方法：**
**我们可以在二级索引树上的key:age+name**，形成多列索引，联合索引，先按age排序，age相同了，然后再按name排序。

create index name_age_idx on student(age,name);

![image-20250719153425553](./mysql数据库.assets/image-20250719153425553.png)

**如果只有age建索引，会有using filesort，所以我们需要创建name+age的多列索引！**
此时相同的age的name已经排好序了！！！
**注意：多列索引一定要使用到第1个列哦，这样才能用到索引！**

![image-20250719153909129](./mysql数据库.assets/image-20250719153909129.png)

因为构建B+树的时候，key是先出现age，name匹配不匹配无所谓。如果没有age，就不知道，没有办法排除，只能一个一个看，整表搜索了。

### 聚集和非聚集索引  

**MyISAM  数据和索引没有放在一块。叫做 非聚集索引**

**InnoDB：数据和索引存放在一块，叫聚集索引**

##### MyISAM  

###### 主键索引  

MyISAM引擎使用B  + 树作为索引结构，叶节点的data域存放的是数据记录的地址。下图是MyISAM主键索引的原理图：  

![image-20250516110643010](./mysql数据库.assets/image-20250516110643010.png)

###### 辅助索引  

在MyISAM中，主索引和辅助索引（Secondary key）在结构上没有任何区别，只是主索引要求key是唯一的，而辅助索引的key可以重复，如果给其它字段创建辅助索引，结构图如下：  

![image-20250516110707487](./mysql数据库.assets/image-20250516110707487.png)

根据上面两张图，首先按照  B + Tree搜索算法搜索索引，如果指定的Key存在，则取出其data域的值，然后以data域的值为地址，读取相应数据记录。  

可以看到，MyISAM存储引擎，索引结构叶子节点存储关键字和数据地址，也就是说索引关键字和数据没有在一起存放，体现在磁盘上，就是索引在一个文件存储，数据在另一个文件存储，例如一个user表，会在磁盘上存储三个文件 user.frm（表结构文件） user.MYD（表的数据文件） user.MYI（表的索引文件）。  

MyISAM的索引方式也叫做**非聚集索引。** 

**MyISAM的主键索引树和二级索引树是没有区别的：**
唯一的区别是主键索引树的主键值uid是不能重复的
二级索引树的普通name是可以重复的
**他们的存储内容都是数据的地址。不涉及回表操作**。数据和索引没在一块存放。
这叫做“**非聚集索引**”的存储结构，索引和数据分开存放

**uid是主键，有主键索引树。name创建二级索引树**

**主键索引树如下**
**每一个key底下的data放的都是数据的地址**

 ![image-20250719155048648](./mysql数据库.assets/image-20250719155048648.png)

##### InnoDB  

###### 主键索引  

InnoDB存储引擎的主键索引，叶子节点中，索引关键字和数据是在一起存放的，如图：  

![image-20250516110803424](./mysql数据库.assets/image-20250516110803424.png)

###### 辅助索引  

InnoDB的辅助索引，叶子节点上存放的是索引关键字和对应的主键，如图：  

![image-20250516110822465](./mysql数据库.assets/image-20250516110822465.png)

辅助索引的 B + 树，先根据关键字找到对应的主键，再去主键索引树上找到对应的行记录数据。从索引树上可以看到，InnoDB的索引关键字和数据都是在一起存放的，体现在磁盘存储上，例如创建一个user表，在磁盘上只存储两种文件，user.frm（存储表的结构），user.ibd（存储索引和数据）。  

InnoDB的索引树叶节点包含了完整的数据记录，这种索引叫做**聚集索引**。因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（区别于MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。    

### 哈希索引（memory支持）

memory：基于内存的存储引擎，支持哈希索引
哈希表在可控的冲突范围，我们经常写的链式哈希表，它的增删改查的时间复杂度都是O(1)
哈希索引，是基于哈希表这个数据结构实现的 （链式哈希表）

平衡树的增删改查的时间复杂度是O(logn)
B+树索引就是把磁盘上的存储的索引加载到内存上构建的数据结构，索引就是数据结构。

看起来哈希表比B+树好，
但是为什么MyISAM和InnoDB存储引擎用的是B+树索引？
我们主要看
1、搜索的效率：
2、磁盘I/O的花费：

我们改用创建哈希索引来看看： create index nameidx on student(name) using hash;

**实际上，show create 只是显示你创建索引的时候加的标志，实际上底层到底是不是哈希索引是不准的！！！**

**这个是准的：**

show index from student;

**我们看到，都是B+树！！！**

**假设现在默认存储引擎是memory**

**memory：基于内存的存储引擎，支持哈希索引，数据和索引都是在内存上放的，一断电就没了，因为内存搜索，哈希表是最快的**

**现在默认选择的是哈希索引：**create index nameidx on student(name)；

**构建链式哈希表：**
根据选定的哈希函数，把每一行记录的name字段作为参数来求一个哈希值，哈希值相当于桶的序号。（会产生哈希冲突）
**解决哈希冲突的方式**：在桶里面用链表串起来。（链地址法）

![image-20250719163253230](./mysql数据库.assets/image-20250719163253230-1752913976896-1.png)

**哈希表的增删查效率高，时间复杂度是O(1)，但是哈希表中的元素没有任何顺序可言。
所以意味着如果用哈希表来构建索引的话，我们只能进行等值比较。**

![image-20250719163519110](./mysql数据库.assets/image-20250719163519110.png)

**像上面这个，就用不着索引了。**
**因为不能确保这个值具体是多少，不能确保在同一个桶中：**

在一个桶中
对于哈希表来说，不能做范围搜索和前缀搜索和order by了
在哈希表中，不同元素，哪怕是15和16，很近，通过求哈希值，模上一个桶的个数，可能就不在同一个桶中了。

如果用链式哈希表构建索引，一个桶里面的节点代表1次磁盘I/O，除非是等值搜索，不然就太慢太慢了。
哈希索引只适用于数据都在内存上，增删查快，而且查询仅仅适合等值查询。
并不能为我们减少磁盘I/O的次数！！！

### 自适应哈希索引

**自适应**：自动，优化的功能，加速搜索

如果是 InnoDB存储引擎
如果它监测到同样的二级索引不断被使用（比如说：select *，此语句操作要回表，要搜主键索引树），那么它会根据这个二级索引在内存上根据二级索引树(B+树）上的二级索引的值，在内存上构建1个哈希索引来加速搜索（只有等值比较）
现在O(1)的时间复杂度就访问到name了，然后直接访问它的数据data指针，就可以速度更快了
**自动的功能，优化的功能，加快搜索**

![image-20250516110846044](./mysql数据库.assets/image-20250516110846044.png)

**蓝色的箭头先搜二级索引树**，再通过二级索引树找到主键，再通过主键索引树找到相应的数据页。
**现在是用二级索引直接构建哈希索引了**，name=‘zhangsan’，通过这个哈希表找到‘zhangsan’直接得到data的指针了，**就是图中的黄色箭头，直接访问到数据页了。**

省了二级索引树和主键索引树的搜索过程
对B+树上频繁使用二级索引搜索并且进行回表操作的优化！
hash索引的生成和维护也是耗费性能的，我们有参数指标，通过参数指标查看，如果自适应哈希索引可以提供效率，那我们使用它，如果它成为性能的损坏，我们就关闭自适应哈希索引。
自适应哈希索引并不能绝对的在任何场景下提高对二级索引搜索的效率
分区：要是在一个链表或者数组进行并行操作，只能加锁。要是在不同的桶操作，就不用加锁。
每一个索引被分到1个分区。
![image-20250719170548855](./mysql数据库.assets/image-20250719170548855.png)

### 索引常见问题，慢查询日志

![image-20250719171830114](./mysql数据库.assets/image-20250719171830114.png)

默认是在InnoDB存储引擎。
首先，最基本的，userid要加索引（因为一般用过滤条件加索引），此时创建的是二级索引树，select *还要涉及回表。而且还有order by addtime，如果addtime没有建索引的话，explain分析时会出现using filesort（涉及addtime的文件排序）！
所以，我们要加联合索引（多列索引）：userid+addtime
这样的话，选出来的addtime在二级索引树上就已经是有序的了
![image-20250719172357739](./mysql数据库.assets/image-20250719172357739.png)

**有using filesort的差别**，数据的[外排序]。用userid=123选择出来的数据越多，耗费的排序性能差的越大。

![image-20250719172649913](./mysql数据库.assets/image-20250719172649913.png)

**例如，看下图**，sex创建索引了，但是没有用到，这是MySQL server的一个优化，过滤出来的数据量已经占了整表的大部分，就没有必要用索引了。

![image-20250719172828244](./mysql数据库.assets/image-20250719172828244.png)

![image-20250719173010920](./mysql数据库.assets/image-20250719173010920.png)

**一张表的一次查询只能用到1个索引**，它看用a=1过滤的数据少还是b=2过滤的少，**用拿个索引搜出来的数据少的话，就用哪个索引。**

**那如果确实有很多索引的话有什么办法吗**

![image-20250719173249787](./mysql数据库.assets/image-20250719173249787.png)

小表决定循环的次数，大表决定每次循环的查询时间

小表是整表扫描，每一个都要用到，大表相关联的字段没有索引的话，小表的每一个uid在大表中搜索都是整表搜索。如果大表相关联的字段有索引，小表的每一个uid在大表中就是走索引，搜的非常快。

SQL和索引的优化问题
我们使用explain去分析SQL语句
但是真正的企业级项目有上千条万条SQL
我们不可能从头开始一条一条explain去分析。也不是所有的SQL都慢。
从什么地方可以获取那些运行时间长，耗性能的SQL，然后再用explain去分析它？？？
我们可以打开慢查询日志：
根据具体的业务和并发量来预估1个时间，比如说100毫秒，20毫秒，设置好后开启业务，压测过程中，如果打开慢查询日志，就会看到超过执行时间的SQL，然后就用explain
步骤如下：
1、设置合理的、业务可以接受的慢查询时间
2、压测执行各种业务
3、查看慢查询日志，找出所有执行耗时的SQL语句
4、用explain分析这些耗时的SQL语句
5、举例子
比如说：在where加order by，explain时发现有using filesort 外部排序，数据都是在磁盘上放，没有建立合适的索引的话，只能进行外部排序了。用where过滤条件和order by排序的字段建立联合索引。
没有加索引，索引没用到，类型强转，过滤条件用了MySQL的函数，都用不到索引，要优化。

### MySQL事务  

**MyISAM不支持事务（每一个SQL自动就提交了，不能做转账这种多条SQL组成的业务啊）！ InnoDB支持事务，支持行锁！**

#### 事务概念  

一个事务是由一条或者多条对数据库操作的SQL语句所组成的一个不可分割的单元，只有当事务中的所有操作都正常执行完了，整个事务才会被提交给数据库；如果有部分事务处理失败，那么事务就要回退到最初的状态，因此，事务要么全部执行成功，要么全部失败。  

所以记住事务的几个基本概念，如下：  

1. 事务是一组SQL语句的执行，要么全部成功，要么全部失败，不能出现部分成功，部分失败的结果。保证事务执行的原子操作。
2. 事务的所有SQL语句全部执行成功，才能提交（commit）事务，把结果写回磁盘上。
3. 事务执行过程中，有的SQL出现错误，那么事务必须要回滚（rollback）到最初的状态。  

**有的业务需要1条以上的SQL语句共同完成，只有这些SQL都成功了才算业务成功了。**
类似于银行的转账，商品的入库出库的场景：

![image-20250719193940601](./mysql数据库.assets/image-20250719193940601.png)

**转账业务由2条SQL组成。** 没有一部分成功一部分失败这一说。
**begin开启事务，如果这2句SQL都成功了，那么commit提交一个事务。**

![image-20250719194155231](./mysql数据库.assets/image-20250719194155231.png)

…是捕捉任何类型的异常
**如果其中任意一句SQL由于突然停电，或者系统出错，执行出错了，那么事务就没有提交，事务就回滚，回滚就是数据恢复到事务开始前的状态。**
这是存储引擎来保证的（redo log 和undo log保证的）。

select @@autocommit;

**默认是自动提交。改成 0 ，手动提交事务，在代码上进行控制，做业务，业务都成功，提交1个事务，如果业务中间出现失败，就回滚1个事务**

#### ACID特性  

每一个事务必须满足下面的4个特性：  

事务的原子性（Atomic）： 
事务是一个不可分割的整体，事务必须具有原子特性，及当数据修改时，要么全执行，要么全不执行，即不允许事务部分的完成。  

事务的一致性（Consistency）：  

一个事务执行之前和执行之后，数据库数据必须保持一致性状态。数据库的一致性状态必须由用户来负责，由并发控制机制实现。就拿网上购物来说，你只有让商品出库，又让商品进入顾客的购物车才能构成一个完整的事务。  

事务的隔离性（Isolation）：  

当两个或者多个事务并发执行时，为了保证数据的安全性，将一个事物内部的操作与其它事务的操作隔离起来，不被其它正在执行的事务所看到，使得并发执行的各个事务之间不能互相影响。  

事务的持久性（Durability）： 
事务完成(commit)以后，DBMS保证它对数据库中的数据的修改是永久性的，即使数据库因为故障出错，也应该能够恢复数据！

（DB写数据都是先在cache缓存上写的，因为速度快，然后操作系统通过磁盘I/O往磁盘上写，当事务提交后，cache往磁盘上提交数据是要花时间的，这个花时间的过程中如果停电了，或者宕机了，或者自动重启了，那么此时数据就丢了，commit通知应用事务提交成功了，用户理所应当认为该修改的数据都修改成功了，但是由于不可控因素-硬件问题，导致缓存上的数据向磁盘上写的时候没写完。因为commit不是同步操作，commit只要提交事务成功就返回了，并不会等着缓存上的数据向磁盘全部写完才返回。因为我们用户会写很多很多的数据，commit是不会等着这些数据从缓存全部写到磁盘，毕竟要经过磁盘I/O，业务上不可能让commit去等那么长时间。）

**但是MySQL的redo log重做日志机制，进行数据恢复，可以保证数据库的永久性，还有undo log回滚日志）**

**所以说，MySQL最重要的是日志，不是数据！**

事务的ACID特性
ACD:是由mysql的redo log和undo log机制来保证的，

I：隔离性，是由mysql事务的锁机制来实现保证的

#### 事务并发存在的问题  

事务处理不经隔离，并发执行事务时通常会发生以下的问题：  

**脏读**（Dirty Read）：一个事务读取了另一个事务未提交的数据。例如当事务A和事务B并发执行时，当事务A更新后，事务B查询读取到A尚未提交的数据，此时事务A回滚，则事务B读到的数据就是无效的脏数据。（事务B读取了事务A尚未提交的数据）  

(**脏读必须杜绝**，**其他下面的情况是事务已经提交了，不能算有问题**，**解不解决是看业务的需求！！！通过设置不同的隔离级别！！！**）

**不可重复读**（NonRepeatable Read）：一个事务的操作导致另一个事务前后两次读取到不同的数据。例如当事务A和事务B并发执行时，当事务B查询读取数据后，事务A更新操作更改事务B查询到的数据，此时事务B再次去读该数据，发现前后两次读的数据不一样。（事务B读取了事务A已提交的数据）  

**虚读**（Phantom Read）幻读：一个事务的操作导致另一个事务前后两次查询的结果数据量不同。例如当事务A和事务B并发执行时，当事务B查询读取数据后，事务A新增或者删除了一条满足事务B查询条件的记录，此时事务B再去查询，发现查询到前一次不存在的记录，或者前一次查询的一些记录不见了。（事务B读取了事务A新增加的数据或者读不到事务A删除的数据）  

![image-20250720100444869](./mysql数据库.assets/image-20250720100444869.png)

#### 事务的隔离级别  

MySQL支持的四种隔离级别是：  

1. **TRANSACTION_READ_UNCOMMITTED。**未提交读。说明在提交前一个事务可以看到另一个事务的变化。这样读脏数据，不可重复读和虚读都是被允许的。  
2. **TRANSACTION_READ_COMMITTED。**已提交读。说明读取未提交的数据是不允许的。这个级别仍然允许不可重复读和虚读产生。  
3. **TRANSACTION_REPEATABLE_READ。**可重复读。说明事务保证能够再次读取相同的数据而不会失败，但虚读仍然会出现。  
4. **TRANSACTION_SERIALIZABLE**。串行化。是最高的事务级别，它防止读脏数据，不可重复读和虚读。  

| 隔离级别 | 脏读   | 不可重复读 | 幻读   |
| -------- | ------ | ---------- | ------ |
| 未提交读 | 可以   | 可以       | 可以   |
| 已提交读 | 不可以 | 可以       | 可以   |
| 可重复读 | 不可以 | 不可以     | 可以   |
| 串行化   | 不可以 | 不可以     | 不可以 |

备注：  

事务隔离级别越高，为避免冲突所花费的性能也就越多。 
在“可重复读”级别，实际上可以解决部分的虚读问题，但是不能防止update更新产生的虚读问题，要禁止虚读产生，还是需要设置串行化隔离级别。  

#### MySQL的事务处理命令  

打开MySQL的Command命令行窗口，测试以下命令：  

1、SELECT @@AUTOCOMMIT;  查看MySQL是否自动提交事务  

```mysql
mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+
1 row in set (0.04 sec)
```

0表示手动提交事务，1表示自动提交事务，设置事务提交方式为手动提交方式：  **一般我们业务上如果要考虑到事务处理，我们就要设置事务提交方式为手动提交方式，否则的话一句SQL就表示自动提交成功了。**

```mysql
mysql> set autocommit=0;
 Query OK, 0 rows affected (0.00 sec)
```

这个set操作是和session有关，只是改变当前的session会话。

BEGIN;   开启一个事务  

COMMIT;   提交一个事务  

ROLLBACK; 回滚一个事务到初始的位置  

SAVEPOINT  point1;  设置一个名字为point1的保存点  

ROLLBACK TO point1;  事务回滚到保存点point1，而不是回滚到初始状态  

SET  transaction_isolation = 'REPEATABLE-READ'; 设置事务的隔离级别  

SELECT  @@ transaction_isolation; 查询事务的隔离级别  

```mysql
mysql> select @@transaction_isolation;
 +-----------------+
 | @@tx_isolation  |
 +-----------------+
 | REPEATABLE-READ |    可以看到MySQL默认工作在可重复读级别
+-----------------+ 
1 row in set, 1 warning (0.00 sec)
```

事务隔离级别的实现原理
锁+MVCC
事务-》事务的ACID特性-》事务的隔离性-》事务要能够允许并发执行-》数据的安全性，一致性和并发的效率问题-》事务的隔离级别

串行化-》靠锁实现，通过锁给所有的事务都排个序，并发的效率就太低了，但是数据的安全性高。
未提交读-》没有做任何的并发控制，明知是错误的，造成脏读，数据的安全性最低。优点是并发效率非常高。（多线程的竞态条件)

所以我们一般用的是下面这2个隔离级别：
已提交读
可重复读
这2个隔离级别就结合了数据的安全性，一致性，并发的效率 ，是由MVCC多版本并发控制实现的。（MVCC是已提交读和可重复读的原理，锁是串行化的原理）


#### MySQL的锁机制  

#### 表级锁&行级锁  

表级锁：对整张表加锁。开销小，（因为不用去找表的某一行的记录进行加锁，要修改这张表，直接申请加这张表的锁），加锁快，不会出现死锁；锁粒度大，发生锁冲突的概率高，并发度低。  

行级锁：对某行记录加锁。开销大（需要找到表中相应的记录，有搜表搜索引的过程），加锁慢，会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度高。  

#### 排它锁和共享锁  

排它锁（Exclusive），又称为X 锁，写锁。  

共享锁（Shared），又称为S 锁，读锁。  

X和S锁之间有以下的关系： SS可以兼容的，XS、SX、XX之间是互斥的  

- 一个事务对数据对象 O 加了 S 锁，可以对 O 进行读取操作但不能进行更新操作。加锁期间其它事务能对O 加 S 锁但不能加 X 锁。  
- 一个事务对数据对象 O 加了 X 锁，就可以对 O 进行读取和更新。加锁期间其它事务不能对 O 加任何锁。  
- 显示加锁：select ... lock in share mode强制获取共享锁，select ... for update获取排它锁  

InnoDB支持行级锁，刚才以主键id为过滤条件的时候，事务1和事务2获取不同行的排它锁，是各自可以获取成功的。但是现在用普通字段作为过滤条件，没有加索引，事务1和事务2获取不同行记录的排它锁，就获取失败了！
InnoDB的行锁是通过给索引上的索引项加锁来实现的，而不是给表的行记录加锁实现的，这就意味着只有通过索引条件检索数据，InnoDB才使用行级锁，否则InnoDB都将使用表锁!!!   

**现在name创建了辅助索引**。事务1获取了zhangsan的排它锁。现在让事务2获取一下chenwei的排它锁

#### InnoDB行级锁  

#### 行级锁  

InnoDB存储引擎支持事务处理，表支持行级锁定，并发能力更好。  

1、InnoDB行锁是通过给索引上的索引项加锁来实现的，而不是给表的行记录加锁实现的，这就意味着只有通过索引条件检索数据，InnoDB才使用行级锁，否则InnoDB将使用表锁。  

2、由于InnoDB的行锁实现是针对索引字段添加的锁，不是针对行记录加的锁，因此虽然访问的是InnoDB引擎下表的不同行，但是如果使用相同的索引字段作为过滤条件，依然会发生锁冲突，只能串行进行，不能并发进行。  

3、即使SQL中使用了索引，但是经过MySQL的优化器后，如果认为全表扫描比使用索引效率更高，此时会放弃使用索引，因此也不会使用行锁，而是使用表锁，比如对一些很小的表，MySQL就不会去使用索引。  



 InnoDB串行化隔离级别(间隙锁)

**怎么解决幻读问题的？？？**

 ![image-20250720154113997](./mysql数据库.assets/image-20250720154113997.png)

**此时在事务1 insert into插入数据，而且这些数据是满足事务2 select的指定的条件的。然后事务1 commit。**

![image-20250720154146851](./mysql数据库.assets/image-20250720154146851.png)

**然后现在事务2同样的根据同样的条件再次查询，查询出了比第一次查询的结果还多出来的数据。**

![image-20250720154230337](./mysql数据库.assets/image-20250720154230337.png)

在串行化里面是怎么解决幻读的？是通过间隙锁（gap lock)解决的。

我们把事务2 select的指定的条件分为2类：
第一类条件：范围查询
第二类条件：等值查询

record lock（记录锁，就是行锁）
gap lock（间隙锁）
next-key lock（间隙锁）：包含record lock+gap lock

#### 间隙锁  

当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB 会给符合条件的已有数据记录的索引项加锁；对于键值在条件范围内但并不存在的记录，叫做“间隙（GAP)” ，InnoDB 也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁。举例来说， 假如 user 表中只有 101 条记录， 其userid 的值分别是 1,2,...,100,101， 下面的 SQL：  

select \* from user where userid > 100 for update;  

是一个范围条件的检索，InnoDB 不仅会对符合条件的 userid 值为 101 的记录加锁，也会对userid 大于 101（但是这些记录并不存在）的"间隙"加锁，防止其它事务在表的末尾增加数据。  

InnoDB使用间隙锁的目的，为了防止幻读，以满足串行化隔离级别的要求，对于上面的例子，要是不使用间隙锁，如果其他事务插入了 userid 大于 100 的任何记录，那么本事务如果再次执行上述语句，就会发生幻读。  

#### 意向共享锁和意向排他锁  

意向共享锁（IS锁）：事务计划给记录加行共享锁，事务在给一行记录加共享锁前，必须先取得该表的IS 锁。  

意向排他锁（IX锁）：事务计划给记录加行排他锁，事务在给一行记录加排他锁前，必须先取得该表的IX 锁。  

![image-20250516111246878](./mysql数据库.assets/image-20250516111246878.png)

1、意向锁是由InnoDB存储引擎获取行锁之前自己获取的  

2、意向锁之间都是兼容的，不会产生冲突  

3、意向锁存在的意义是为了更高效的获取表锁（表格中的X和S指的是表锁，不是行锁！！！）  

4、意向锁是表级锁，协调表锁和行锁的共存关系。主要目的是显示事务正在锁定某行或者试图锁定某行。  

#### InnoDB表级锁  

**我们在之前知道，InnoDB是行锁，但是不是每次都获取行锁，如果没有索引项的话，还是一个表锁。而且有的时候，我们希望直接去使用表锁。**

在绝大部分情况下都应该使用行锁，因为事务和行锁往往是选择InnoDB的理由，但个别情况下也使用表级锁；  

1）事务需要更新大部分或全部数据，表又比较大，如果使用默认的行锁，不仅这个事务执行效率低，而且可能造成其他事务长时间等待和锁冲突；  

2）事务涉及多个表，比较复杂，很可能引起死锁，造成大量事务回滚。  

如：  

LOCK TABLE user READ；读锁锁表（获取这张表的读锁）

LOCK TABLE user WRITE;  写锁锁表  （获取这张表的写锁）

事务执行...  

COMMIT/ROLLBACK; 事务提交或者回滚

UNLOCK TABLES; 本身自带提交事务，释放线程占用的所有表锁  

**在使用表锁的时候，涉及到效率的问题：**
**如果我们要获取一张表的共享锁S或者排它锁X**，最起码得确定，这张表没有被其他事务获取过X锁，这张表里的数据没有被其他事务获取过行锁X锁。

假如这张表有1000万个数据，怎么判断这1000万行哪些有X锁哪些没有X锁？？？
**不扫描 不知道。**
**得全部扫描过去才知道。**

意向共享锁和意向排他锁

**目的是解决效率问题：**这张表里的数据没有被其他事务获取过行锁X锁。

**都是和表锁的概念相关的。为了可以更快速的获取表锁。**

#### 死锁  

MyISAM 表锁是 deadlock free 的， 这是因为 MyISAM 总是一次获得所需的全部锁，要么全部满足，要么等待，因此不会出现死锁。但在 InnoDB 中，除单个 SQL 组成的事务外，锁是逐步获得的，即锁的粒度比较小，这就决定了在 InnoDB 中发生死锁是可能的。 

```mysql
mysql> select * from test_dead_lock where id=1 for update;
ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting 
transaction
```

死锁问题一般都是我们自己的应用造成的，和多线程编程的死锁情况相似，大部分都是由于我们多个线程在获取多个锁资源的时候，获取的顺序不同而导致的死锁问题。因此我们应用在对数据库的多个表做更新的时候，不同的代码段，应对这些表按相同的顺序进行更新操作，以防止锁冲突导致死锁问题。

 **死锁出现的典型场景：**

事务1成功获取行锁1
事务2成功获取行锁2
…
然后事务1现在要获取行锁2，就要等待了。
而事务2现在要获取行锁1，也要等待了。

![image-20250721092902909](./mysql数据库.assets/image-20250721092902909.png)

当前进行的2个事务都阻塞住了。
所有的事务都阻塞住了，相当于进程内的所有线程都阻塞住了，造成了死锁问题。

多个事务/线程 在获取多个相同资源锁的时候应该按照同样的顺序获取资源的锁！
事务被阻塞了，或者死锁了，事务是不会一直阻塞在那里的，mysqld（MySQL服务器进程）是有事务阻塞的超时时间，超时后，事务处理失败。

#### 锁的优化建议  

1.尽量使用较低的隔离级别  （已提交读，可重复读）

2.设计合理的索引并尽量使用索引访问数据，使加锁更加准确，减少锁冲突的机会提高并发能力 
3.选择合理的事务大小，小事务发生锁冲突的概率小 
4.不同的程序访问一组表时，应尽量约定以相同的顺序访问各表，对一个表而言，尽可能以固定的顺序存取表中的行。这样可以大大减少死锁的机会 
5.尽量用相等条件访问数据，这样可以避免间隙锁对并发插入的影响 
6.不要申请超过实际需要的锁级别 
7.除非必须，查询时不要显示加锁  

#### MVCC多版本并发控制  

InnoDB的事务隔离级别已提交读和可重复读的底层实现原理：MVCC（多版本并发控制）–》并发的读取方式：快照读
InnoDB提供了2种读取操作：锁定读和非锁定读
锁定读就是读取的时候加锁了（S锁或者X锁）
非锁定读就是读取的时候没有加锁，指的就是MVCC提供的快照读–》依赖底层的undo log回滚日志。

**事务日志**：undo log回滚日志 和 redo log重做日志
**ACD（依靠 事务日志 保证的） I（依靠 锁+MVCC 保证的）**  

undo log回滚日志的主要作用：
1、事务发生错误时回滚rollback ，回滚日志（数据在更新的时候，把改之前的数据存下来在回滚日志中，目的是为了事务万一出错回滚了或者我们手动回滚的时候，能够把最初的数据在回滚日志中找到）
2、提供了MVCC的非锁定读（快照读）—》undo log的原理

示例讲解

**这是原始的user表：**

![image-20250720191050817](./mysql数据库.assets/image-20250720191050817.png)

**我们现在对id=7这条记录进行更改。**
在MVCC机制下，对这张表增加2列。修改当前数据的事务ID和指向undo log的指针。
**我们把id=7的age改为16。如下图所示：**

 ![image-20250720191141922](./mysql数据库.assets/image-20250720191141922.png)

**假如我们增加了1行新数据。**
因为新的数据就没有老数据，如下图所示：

![image-20250720191439157](./mysql数据库.assets/image-20250720191439157.png)

**回滚的时候发现是空，就知道是insert增加的数据。回滚的时候执行delete就把这条数据删除了。**

**如果我们对这条新数据进行修改了，那么这些数据就都跑到undo log中了！如下图所示：**

![image-20250720191345326](./mysql数据库.assets/image-20250720191345326.png)

   **在已提交读和可重复读这2种隔离级别下**，当我们去读数据的时候，是通过MVCC的快照读实现的。

**已提交读 这个隔离机制的测试**

**已提交读**：解决了脏读问题。但是不可重复读和幻读的问题还是存在。

我们先修改隔离级别：

这就是已经解决了脏读了。

不管是已提交读还是可重复读，当我们只要去select的时候，就会产生一个数据快照，相当于给当前的数据拍个照片，以后去查询，都是查询快照上的数据。已提交读是采用非锁定读。通过MVCC提供的快照读解决的。
非锁定读是在快照上的操作。

已提交读：每一次select都会产生一次新的数据快照。
当事务1进行更改的时候，事务2又去select，重新产生数据快照了，但是产生新的数据快照的前提是新的数据已经被事务正确commit过的。
数据有2种状态：prepare和commit
事务2第二次select的时候，由于事务1修改数据并没有commit，当又一次产生数据快照时，产生的数据快照还是undo log回滚日志的链表指向的旧数据。
![image-20250720201554755](./mysql数据库.assets/image-20250720201554755.png)

**接下来测试不可重复读**
事务1把修改的数据进行commit了，事务2现在select出来的数据就是age=16了，**这就是不可重复读的现象。**

![image-20250720202104118](./mysql数据库.assets/image-20250720202104118.png)

**为什么无法解决不可重复读？**
因为每一次select都会重新产生1次数据快照。其他事务更新后而且已commit的数据可以实时反馈到当前事务的select结果当中。

**同样的为什么会出现幻读？**

事务2 select，事务1insert，然后commit提交了，然后现在事务2再查

![image-20250720202634046](./mysql数据库.assets/image-20250720202634046.png)

**出现了幻读。因为每一次select都重新产生1次快照。**
**每一次select就是相当于给下图的绿色框拍照。然后找age=16的数据，自然就产生幻读了。**

![image-20250720203118318](./mysql数据库.assets/image-20250720203118318.png)

![image-20250720203541707](./mysql数据库.assets/image-20250720203541707.png)

#### 可重复读测试 这个隔离机制的测试

解决了脏读问题（因为生成了数据快照，在数据快照上查询），而且支持可重复读！！！

为什么解决了不可重复读问题？
因为 一个事务的第一次select 产生数据快照，而且只产生1次数据快照。

我们修改好隔离级别：

SET  transaction_isolation = 'REPEATABLE-READ';

事务2 第一次 select ，产生了数据快照

![image-20250720204219353](./mysql数据库.assets/image-20250720204219353.png)

**这张照就已经写死了。事务2后面的select就不生成数据快照了。**
事务1 update，然后commit

![image-20250720204359128](./mysql数据库.assets/image-20250720204359128.png)

照片第一次已经拍了，事务1现在去修改，

![image-20250720204714603](./mysql数据库.assets/image-20250720204714603.png)

事务2 现在再去select。查的还是第一次select生成的快照。

**再举一个例子：**

刚才是事务2先第一次select了，所以产生了一次数据快照，然后事务1更改数据，事务2再查就是原来的数据快照。
**现在我们让事务1先修改数据，commit**

![image-20250720205140142](./mysql数据库.assets/image-20250720205140142.png)

**现在事务2才第一次select，之前没有数据快照。**所以可以访问到事务1更改后的数据

![image-20250720205251117](./mysql数据库.assets/image-20250720205251117.png)

**当前事务第一次select产生数据快照，后面其他事务虽然修改了最新的数据，但是当前事务再select的时候，依然查看的是最初的快照数据。**

**为什么部分解决了幻读？？？** 

已经解决了幻读。
但是我们说，它只是部分解决了幻读
那是因为
我们现在在事务2 update

但是update，insert，delete，做的是当前读，读的是最新的数据！
更新的是最新的数据

假设事务1的ID是1000，事务2的ID是2000
事务1第一次insert的数据的事务ID就是1000
现在事务2执行update，修改事务1insert的数据，当前最新数据的事务ID是2000
如下图所示 ：

![image-20250720210336342](./mysql数据库.assets/image-20250720210336342.png)

**当前事务是可以看见自己事务修改、更新的数据的！！！**

  **事务2update修改，是当前读。**![image-20250720210457555](./mysql数据库.assets/image-20250720210457555.png)

**如果事务2再去select的时候，它不仅可以看到数据快照，还可以看到自己修改的数据**

![image-20250720210613858](./mysql数据库.assets/image-20250720210613858.png)

**当前事务是可以看见自己事务修改、更新的数据的！！！**
**所以，并没有完全解决幻读问题**

---

MVCC是多版本并发控制（Multi-Version Concurrency Control，简称MVCC），是MySQL中基于乐观锁理论实现隔离级别的方式，用于实现已提交读和可重复读隔离级别的实现，也经常称为多版本数据库。MVCC机制会生成一个数据请求时间点的一致性数据快照 （Snapshot)， 并用这个快照来提供一定级别 （语句级或事务级） 的一致性读取。从用户的角度来看，好象是数据库可以提供同一数据的多个版本（系统版本号和事务版本号）。  

MVCC多版本并发控制中，读操作可以分为两类：

1、快照读（snapshot read）读的是记录的可见版本，不用加锁。如select  

2、当前读（current read） 读取的是记录的最新版本，并且当前读返回的记录。如insert，delete，update，select...lock in share mode/for update 
MVCC：每一行记录实际上有多个版本，每个版本的记录除了数据本身之外，增加了其它字段 
DB_TRX_ID：记录当前事务ID 
DB_ROLL_PTR：指向undo log日志上数据的指针  

**已提交读：**每次执行语句的时候都重新生成一次快照（Read View），每次select查询时。

**可重复读：**同一个事务开始的时候生成一个当前事务全局性的快照（Read View），第一次select查询时。  

#### 快照内容读取原则：  

1、版本未提交无法读取生成快照 
2、版本已提交，但是在快照创建后提交的，无法读取 
3、版本已提交，但是在快照创建前提交的，可以读取 
4、当前事务内自己的更新，可以读到  **（通过匹配当前事务的id号就可以看到当前数据的最新更新是不是当前事务做的，如果是当前事务做的，当前事务就可以看到最近的更新）**

#### MyISAM表级锁  

MyISAM存储引擎不支持事务处理，因此它的并发比较简单，只支持到表锁的粒度，粒度比较大，并发能力一般，但不会引起死锁的问题，它支持表级共享的读锁和互斥的写锁。  

#### 表级锁  

对 MyISAM 表的读操作（共享锁），不会阻塞其他用户对同一表的读请求，但会阻塞对同一表的写请求；对 MyISAM 表的写操作（排它锁），则会阻塞其他用户对同一表的读和写操作。 
MyISAM 表的读操作与写操作之间，以及写操作之间是串行的。 
MyISAM 在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT 等）前，会自动给涉及的表加写锁，这个过程并不需要用户控制，是MySQL Server端自动完成的。  

#### 并发插入优化  

concurrent_insert优化。  

```mysql
mysql> show variables like 'concurrent_insert';
 +-------------------+-------+
 | Variable_name     
| Value |
 +-------------------+-------+
 | concurrent_insert | AUTO  |
 +-------------------+-------+
 1 row in set (0.00 sec)
```

普通情况下，MyISAM的读操作和写操作都是串行的，但是其实MyISAM也是支持读和写的并发操作的，上面的concurrent_insert变量就是开关，允许一个线程在读的时候，另外一个线程在尾部进行插入（但是不能并发进行删除delete和更新update）。  

#### 锁调度优化  

low_priority_updates优化。  

在MyISAM存储引擎下，多个线程并发操作时，线程1试图获取读锁，线程2获取写锁，一般MyISAM认为写操作要比读操作重要，因此线程2几乎都会优先获取写锁，写操作完成后，线程1才会获取读锁。  

即使线程1的读锁请求先到达，线程2的写锁请求后到达，那么线程2写锁的获取也会排在线程1读锁的前面。  

因此，MyISAM存储引擎不适合大量的更新操作和查询操作，因为查询操作获取读锁的优先级比较低，会导致客户端查询获取结果的过程很慢。当然MySQL提供了很多参数设置，可以调整读锁的获取优先级，可以自行网上查阅深入了解。  

### MySQL优化 

MySQL的优化问题
MySQL需要优化的地方有哪些？？？
分为3个方面：

1、SQL和索引的优化
（数据量比较大，执行SQL效率低，要优化）

开启慢查询日志-》设置相应的项目表数据量的合理的慢查询时间–》找到记录慢查询SQL–》用explain分析SQL的执行计划–》给出相应的优化措施–》如果数据量太大的话，用分页。没有用索引。用多列索引没没有用到第一列。联合查询的大小表设置。多表查询不用in，用外连接替代带in子查询的过程，合理使用索引，不会产生中间表。

2、应用优化
1、连接数据库上，可以引入连接池（中间件）
2、引入缓存（存储热点数据，redis，如果客户端的请求来了，先在redis上查一下，如果redis上直接查到就不经过数据库，如果没有查到就去访问MySQL数据库，访问回来，先把访问的数据往redis上缓存一下，再把结果返回给用户。redis是基于内存的数据库）
业务上增加redis、memcache，一般用缓存把经常访问的数据缓存起来。
引入redis缓存的话，就要防止这些问题 ：缓存数据一致性问题。缓存穿透和缓存雪崩的问题。

除了优化SQL和索引，很多时候，在实际生产环境中，由于数据库服务器本身的性能局限，就必须要对上层的应用来进行一些优化，使得上层应用访问数据库的压力能够减到最小。

3、MySQL Server优化
各种参数的配置

关闭自适应哈希索引， 维护自适应哈希索引要花费很多性能，如果很少用到，通过参数配置关掉它。

提高redo log性能，可以重置InnoDB log buffer大小（redo log缓存的大小），Innodb_buffer_pool_size（缓存的大小），来减少磁盘I/O次数，因为缓存区大了，在缓冲区工作的时间就长了，效率就高了。

对于MySQL Server端的优化，主要指的就是MySQL Server启动时加载的配置文件的配置项内容的优化
（就是那个my.ini或者Linux etc下的my.cnf），下面我们看看它的配置文件中有哪些是我们需要重点关注的优化参数。

#### SQL和索引优化  

#### 应用优化  

除了优化SQL和索引，很多时候，在实际生产环境中，由于数据库服务器本身的性能局限，就必须要对上层的应用来进行一些优化，使得上层应用访问数据库的压力能够减到最小。  

#### 连接池  

应用上一般访问数据库，都是先和MySQL Server创建连接，然后发送SQL语句，Server处理完成后，再把结果通过网络返回给应用，然后关闭和MySQL Server的连接，因此短时间大量的数据库访问，消耗的TCP三次握手和四次挥手所花费的时间就很大了，稍微大一点的项目，我们都会在应用访问数据库的那一层上，添加连接池模块，相当于应用和MySQL Server事先创建一组连接，当应用需要请求MySQL Server时，不需要再进行TCP连接和释放连接了，一般连接池都会维护以下资源：  

1、连接池里面保持固定数量的活跃TCP连接，供应用使用。  

2、如果应用瞬间访问MySQL的量比较大，那么连接池会实时创建更多的连接给应用使用。  

3、当连接池里面的TCP连接一段时间内没有被用到，连接池会释放多余的连接资源，保留它设置的最大空闲连接量就可以了。  

连接池可以自己实现，也可以用第三方写好的库。  

#### 增加cache缓存层  

业务上增加redis、memcache，一般用缓存把经常访问的数据缓存起来。  

#### MySQL Server优化  

对于MySQL Server端的优化，主要指的就是MySQL Server启动时加载的配置文件的配置项内容的优化（就是那个my.ini或者my.cnf），下面我们看看它的配置文件中有哪些是我们需要重点关注的优化参数。  

#### MySQL查询缓存  

MySQL的查询缓存是把select查询语句上一次的查询结果记录下来放在缓存当中，下一次再查询相同内容的时候，直接从缓存中取出来就可以了，不用再进行一遍真正的SQL查询。但是当两个select查询中间出现insert，update，delete语句的时候，查询缓存就会被清空。查询缓存适用更新不频繁的表，因为当表更新频繁的话，查询缓存也总是被清空，过多的查询缓存的数据添加和删除，就会影响MySQL的执行效率，还不如每次都从磁盘上查来得快（缓存指的就是一块内存，内存I/O比磁盘I/O快很多）。  

可以在MySQL上通过以下命令，来查看查询缓存的设置：  

![image-20250721140636838](./mysql数据库.assets/image-20250721140636838.png)

通过show status命令，可以查看MySQL查询缓存的使用状况，如下：  

![image-20250721140711214](./mysql数据库.assets/image-20250721140711214.png)

可以通过set命令设置上面的缓存参数开启MySQL查询缓存功能，也可以找到MySQL的配置文件（windows是my.ini，linux是my.cnf），修改query_cache_type参数为1就可以了，然后重启MySQLServer就可以使用了，如下：  

```shell
query_cache_type=1
query_cache_size=10
```

修改完成，重启MySQL Server，查询缓存生效。  

没有直接使用redis好用的多，而且这是MySQL的查询缓存，我们作为MySQL服务的使用者来说，对这个控制不了，如果我们引入redis的话，可以通过redis提供的API进行精确的控制，要缓存哪些数据，不缓存哪些数据。

#### 索引和数据缓存  

主要指的就是innodb_buffer_pool_size配置项，从名字上就能看到，该配置项是针对InnoDB存储引擎起作用的，这个参数定义了InnoDB 存储引擎的表数据和索引数据的最大内存缓冲区大小。innodb_buffer_pool_size是同时为数据块和索引块做缓存，这个值设得越高，访问表中数据需要的磁盘 I/O 就越少。  

```shell
innodb_buffer_pool_size=512M  
```

#### MySQL线程缓存  

主要指配置文件中thread_cache_size配置项。给大家讲过MySQL Server网络模块采用经典的I/O复用+线程池模型，之所以引入线程池，主要就是为了在业务执行的过程中，不会因为临时创建和销毁线程，造成系统性能降低，因为线程的创建和销毁是很耗费性能的，所以线程池就是在业务使用之前，先创建一组固定数量的线程，等待事件发生，当有SQL请求到达MySQL Server的时候，在线程池中取一个线程来执行该SQL请求就可以了，执行完成后，不销毁线程，而是把线程再归还到线程池中，等待下一次任务的处理（MySQL会根据连接量，自动加大线程池的数量）。（线程池的线程数量随着请求越来越多，是可以动态增加的）。
可伸缩的线程池（初始化是1个线程，处理所有业务，如果业务请求处理不来，向线程池请求线程总失败，此时线程池会创建更多的线程来处理更多的请求，处理完之后一段时间，可能请求没有那么多了，空闲的线程会回到线程池，线程池还会根据相应的配置减少线程池里的线程，把资源归还给系统）

```shell
thread_cache_size=10
```

配置完thread_cache_size，重启MySQL Server服务生效。  

#### 并发连接数量和超时时间  

MySQL Server作为一个服务器，可以设置客户端的最大连接量和连接超时时间，如果数据库连接统计数量比较大，这两个参数的值需要设置大一些。  

```mysql
mysql> show variables like '%connect%';
```

在配置文件（my.cnf或my.ini）最下面，添加配置：max_connection  = 2000 ，然后重启MySQLServer，设置生效。  

```mysql
show global variables like '%timeout%';
```

MySQL Server对于超时未通信的连接，进行主动关闭操作。设置超时时间，超过设置时间没有请求就主动断开，单位是秒，在配置文件中添加配置：wait_timeout = 600 。  

#### MySQL日志  

**在之前，我们已经讲了MySQL事务日志：redo log 和 undo log**（是InnoDB engine存储引擎生成的）

**当我们作为一个mysql client 来发起一个连接请求：经过了如下图示过程：**

![image-20250721143635487](./mysql数据库.assets/image-20250721143635487.png)

MySQL支持 plugin engine 插件式的存储引擎，可以更换
**这个MySQL日志主要分为4类：**
**错误日志
查询日志
二进制日志
慢查询日志**



 ```shell
 mysql> show variables like 'log_%';# 全局变量  设置&状态   show variables & show status
 #log_bin是二进制日志，只要是修改操作，在二进制日志都会出现。
 ```

打开my.ini,在后面加上上面的参数，保存后重启mysql服务就行了。  

```shell
#Enter a name for the error log file.  Otherwise a default name will be used.
 log-error=err.log
 #Enter a name for the query log file. Otherwise a default name will be used.
 #log=
 #Enter a name for the slow query log file. Otherwise a default name will be used.
 #log-slow-queries=
 #Enter a name for the update log file. Otherwise a default name will be used.
 #log-update=
 #Enter a name for the binary log. Otherwise a default name will be used.
 #log-bin=
```

**在开启log-bin=mysql-bin的时候还要同时加上 server-id=1(表示当前MySQL的身份）**

expire_logs_days=7,设置过期的时间，否则总有一天磁盘被这个日志占满，导致服务器不可运行，设置7天后失效，超过7天后日志文件被删除掉

如果想要设置是持久的起作用：需要在配置文件上定义它，然后重启MySQL服务，就可以持久性的生效，而不会出现在每个session中都要自定义设置，然后session结束都恢复到原始状态的情况。**如果不自定义指定路径：默认在数据的路径：/var/lib/mysql**

在linux root下重启mysqld服务：service mysqld restart  

#### 错误日志  

错误日志是 MySQL 中最重要的日志之一，它记录了当 mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息（cordump，error，异常exception）。当数据库出现任何故障导致无法正常使用时，可以首先查看此日志。  

mysqld 使用错误日志名 host_name.err(host_name 为主机名) 并默认在参数 DATADIR(数据目录)指定的目录中写入日志文件。  

#### 查询日志  

查询日志记录了客户端的所有语句。由于上线项目sql特别多，开启查询日志IO太多导致MySQL效率低，只有在调试时才开启，比如通过查看sql发现热点数据进行缓存。  

> **mysql> show global variables like "%genera%";**  

#### 二进制日志  

不能直接查看，不是明文。
**（二进制日志：不记录select操作，记录的是数据库的更改操作。）**

二进制日志(BINLOG)记录了所有的 DDL(数据定义语言)语句和 DML(数据操纵语言) 语句，但是不包括数据查询语句。语句以“事件”的形式保存，它描述了数据的更改过程。 此日志对于灾难时的数据恢复起着极其重要的作用。  

#### 两个重要的应用场景：主从复制、数据恢复  

**主库所有的更新操作（update,delete,insert）都记录在binlog中，从库读主库的binlog，把binlog的所有操作像拍电影一样在从库上演一下。**

查看binlog：mysql> show binary logs;  

通过mysqlbinlog工具（mysql原生自带的工具）可以快速解析大量的binlog日志文件，如： 

```shell 
hell> mysqlbinlog --no-defaults --database=school  --base64-output=decode-rows -v --start-datetime='2021-05-01 00:00:00' --stop-datetime='2021-05-10 00:00:00'  
mysql-bin.000001 | more
```

#### 慢查询日志  

MySQL可以设置慢查询日志，当SQL执行的时间超过我们设定的时间，那么这些SQL就会被记录在慢查询日志当中，然后我们通过查看日志，用explain分析这些SQL的执行计划，来判定为什么效率低下，是没有使用到索引？还是索引本身创建的有问题？或者是索引使用到了，但是由于表的数据量太大，花费的时间就是很长，那么此时我们可以把表分成n个小表，比如订单表按年份分成多个小表等。  

慢查询日志相关的参数如下所示：  

（MySQL定义的很多的全局的开关，都是在全局变量中存储，可以用show/set variables查看或者设置全局变量的值）

```mysql
mysql> show variables like '%slow_query%';
+---------------------+-----------------------------------+
| Variable_name       | Value                             |
+---------------------+-----------------------------------+
| slow_query_log      | OFF                               |
| slow_query_log_file | /var/lib/mysql/DanYuesen-slow.log |
+---------------------+-----------------------------------+
2 rows in set (0.00 sec)
```

**慢查询日志开关**：默认是关闭的。
**慢查询日志的路径**：默认在数据路径。 

慢查询日志记录了包含所有执行时间超过参数 long_query_time（单位：秒）所设置值的 SQL语句的日志，在MySQL上用命令可以查看，如下：  

```mysql
mysql> show variables like 'long%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)
```

这个值是可以修改的，如下：  

```mysql
mysql> set long_query_time = 1;  #注意，单位是秒
Query OK, 0 rows affected (0.00 sec)
```

现在修改成超过1秒的SQL都会被记录在慢查询日志当中！可以设置为0.01秒，表示10毫秒。  

慢查询日志，默认名称是host_name-slow.log，存放在MySQL的数据路径下，内容格式显示大致如下： 

```shell
Query_time: 0.012000  Lock_time: 0.000000 Rows_sent: 0  Rows_examined: 139
 use tuluneducation;
 SET timestamp=1534527397;
 select id,author from subject where content like '%linux%' and title like '%c++ linux%'; 
```

通过查询慢查询日志，发现项目运行过程中，上面这条SQL语句的执行时间超过了设定的慢查询时间，那么接下来就需要用explain分析一下该SQL的执行计划了，根据具体情况找出SQL和索引该怎么去优化。  

> show profiles命令可有查看sql具体的运行时间，全局变量的名字是：profiling  

举个例子：select * from student where uid=3;

像这种查询的时间在小数点后2位也体现不出来运行时间，如果想再优化它，就要使用**profiling** 

show variables like 'profiling';没有要求是global，说明这个变量的设置只和当前的session有关而已。
**可以看到：会把之前所做的所有的SQL语句操作的耗时时间显示出来。精确度还是很高的。我们可以根据它来进行更加的优化措施。**
Query_ID是查询的标号。duration是耗时的时间。query是具体的SQL语句。

#### redo log和undo log  

**持久性：当事务提交之后，不管MySQL server会出现什么不可预期的错误，哪怕是进程挂了，断电了，只要事务提交成功，由MySQL-server处理完异常后把数据恢复到正常状态**

![image-20250721105216207](./mysql数据库.assets/image-20250721105216207.png)

**redo log**  

redo log：重做日志，用于记录事务操作的变化，确保事务的持久性。redo log是在事务开始后就开始记录，**（并不是事务commit时才记录，因为整个事务改的可能有很多，如果在commit的时候才写redo log的话，可能花的时间特别长了。而是事务begin开始后就记录了，随着事务执行过程中对数据的修改，实时写redo log）**，不管事务是否提交都会记录下来，在异常发生时（如数据持久化过程中掉电），InnoDB会使用redo log恢复到掉电前的时刻，保证数据的完整性。  

innodb_log_buffer_size默认是16M，就是redo log缓冲区的大小，它随着事务开始，就开始写redolog，如果事务比较大，为了避免事务执行过程中花费过多磁盘IO，可以设置比较大的redo log缓存，节省磁盘IO。  **可以设置比较大的redo log缓存，节省磁盘IO。往磁盘上刷是有刷新的时机。达到时机就花费磁盘IO，如果buffer比较大，会更慢的达到刷新的时机，效率更快。**

InnoDB修改操作数据，不是直接修改磁盘上的数据，实际只是修改Buffer Pool中的数据。InnoDB总是先把Buffer Pool中的数据改变记录到redo log中，用来进行崩溃后的数据恢复。 优先记录redo log，然后再慢慢的将Buffer Pool中的脏数据刷新到磁盘上。  

innodb_log_group_home_dir指定的目录下的两个文件：ib_logfile0，ib_logfile1，该文件被称作重做日志。  

buffer pool缓存池：  （B+树索引，自适应哈希）

作用：加速读和加速写，直接操作data page，写redo log修改就算完成，有专门的线程去做把bufferpool中的dirty page写入磁盘。  

**MySQL server**：检查一些连接，SQL的优化操作，**然后就是存储引擎（绿色）**，**黄色的柱状就是磁盘**，

![image-20250516112452556](./mysql数据库.assets/image-20250516112452556.png)

**弧形**：是奔溃的恢复。通过redo log恢复上次事务提交成功后的数据。
**MySQL server**：检查一些连接，SQL的优化操作
**然后就是存储引擎（绿色）**

**左边绿色的是日志缓存，专门的重做日志的缓存，写日志写索引是不可能直接往磁盘上写的，但是最终记录在磁盘上。
右边绿色的就是缓存池，就是数据缓存，索引缓存，最终发送到磁盘上。**

当我们在数据更新，数据操作的时候，事务开始以后，随着事务执行一些相应的SQL，这些对数据的最终更改都是先记录在redo log buff（专门做redo log的缓冲区）里面，对于数据或者索引的修改都是先记录在buff poll（在buffpool建B+索引树，B+树索引优化的自适应哈希）里面。
当我们提交事务commit的时候，在关系图上的操作就是把InnoDB Log Buffer的内容写入磁盘，写成功的话，在磁盘上的redo log会记录状态：commit，如果没有写成功或者写完，就是记录状态：prepare

在写入磁盘的过程中也有可能发生异常，掉电，服务崩溃，导致在写redo log的时候没有写完，出错了，这个相当于事务没有提交成功，此时MySQL下次在恢复的时候就没有必要考虑这个事务的完整性，因为状态并不是commit，都写入磁盘上才表示redo log写成功，状态才变成commit。  

是不是commit的时候，buff poll里面的脏数据（数据有被修改）写入磁盘？
并不需要和commit操作同步。
因为进入数据修改的时候，当前事务可能修改的数据量还是比较大的，对于buff poll缓存的数据，我们有专门的线程在合适的时间，往磁盘上去刷新，如果出现掉电，下一次mysql启动后，会根据redo log里面记录的数据，对数据进行恢复。undo log也要记录在redo log中

undo log支持事务回滚，也不是一瞬间就发生了，最终要修改的也是磁盘上的数据，要防止在回滚中出现异常，所以要记录在redo log里面。
事务commit成功或者rollback成功，对于底层，都是成功的把操作写到redo log里面。

当我们去事务commit成功的时候，最重要的是redo log内容成功刷新到磁盘上，会把数据的状态改为commit，这才算一个真真正正的事务提交成功，数据还是buff pool，此时其他的事务查询还是从buff pool查询，没有直接从磁盘查，buff pool有专门的刷新到磁盘的时间和机制。最重要的不是写数据到磁盘。

![image-20250721113317394](./mysql数据库.assets/image-20250721113317394.png)

undo log也要记录在redo log中 因为undo log也要支持事务回滚 ，事务回滚也不是一瞬间发生，最终要修改的是磁盘上的数据，如果在回滚中出现异常，mysql重启后也能从redo log中找到undo log的内容，重启事务的回滚，把事务恢复到最初的状态。体现事务的原子性！

**redo log**里面记录的就是最终修改后的按页面存储的数据页（物理日志）。直接存数据最终的状态。

**undo log**是逻辑日志，存储的是具体的相应的SQL语句。如果现在执行的是insert，回滚的时候就执行delete；如果现在执行的update，就把原来的旧值再update回来。

事务commit成功或者rollback成功，从表面来说，函数执行成功，对于业务来说是成功的，但是对于底层来说，都是要成功的把相应的操作写入到redo log中。写入到redo log成功，才算真真正正的成功，才能保证原子性，一致性，尤其是数据的持久性。

![image-20250721120929072](./mysql数据库.assets/image-20250721120929072.png)

**读出来的，修改的都是优先修改缓存池中的东西**
在实际项目中，mysqld会单独的跑在一个机器上，可以分配大量的内存专门做InnoDB的buffer pool，性能还是非常不俗的。

 show engine innodb status;

![image-20250721121201945](./mysql数据库.assets/image-20250721121201945.png)

show variables like 'innodb_buffer_poll%';

当我们在进行事务更改的时候，永远先写的是redo log，然后才是写buff pool，当事务提交成功，就是要保证redo log就记录完整到磁盘上。
对于表数据的更改，buff pool的脏数据是不是刷新到磁盘上，我们根本不用担心，因为我们可以随时通过redo log重做日志来恢复事务提交成功的状态。

#### undo log  

undo log：回滚日志，保存了事务发生之前的数据的一个版本，用于事务执行时的回滚操作，同时也是实现多版本并发控制（MVCC）下读操作的关键技术。  

DB_TRX_ID:事务ID   DB_ROLL_PTR:回滚指针  

![image-20250516112506637](./mysql数据库.assets/image-20250516112506637.png)

在MVCC下，针对表的所有记录，除了我们能看到的book_id,book_name等存放书籍信息的一张表， 它还会给这张表添加额外的字段如下：![image-20250720185234338](./mysql数据库.assets/image-20250720185234338.png)

我们看到额外添加了3个字段，DB_ROW_ID和MVCC的关系不大，这个是你在创建表的时候，如果没有加主键列，那么InnoDB就给你增加的主键列id，为了区分每一行记录，通过这个id作为主键创建索引树，在B+树的叶子节点上存放一行一行的数据，因为InnoDB的数据和索引是存放在一起的，如果你没有设置主键，它会帮你设置主键。
DB_TRX_ID(事务ID） （1002, 1001, 1000），我们在之前经常登录两个窗口session，然后各输入begin；这个意思就是在两个窗口上各开启一个事务，事务开启的请求最终会发到MySQL server上，MySQL server为每1个事务都会分配一个全局的，不冲突的事务ID。（InnoDB存储引擎分配的，因为它才支持事务）
那也就是说，当我这个事务1修改数据的时候，DB_TRX_ID放的就是事务1的ID，同一个事务对这个数据不断改来改去，这个事务ID是不会改变的。

DB_ROLL_PTR（回滚指针），我们看到，存放的是地址，这个地址表示的是一个数据的内存的位置，看起来是一个链表，上图中的这个橙色表是最后的成品，那么这个表是怎么变来的呢？
**最初的时候表是这样的：**

![image-20250720185545054](./mysql数据库.assets/image-20250720185545054.png)

**假设现在有一个事务来更改这条数据了。**
这个事务把book_name给更改了。
改成了这个：![image-20250720185631819](./mysql数据库.assets/image-20250720185631819.png)**此时数据涉及到修改了，修改的数据存放在当前这个位置，那修改之前的数据存放在哪里？存放在undo log中！**

**假设最初是** 最初给这条数据赋值的事务的ID是1000，回滚指针是空 

![image-20250720185819859](./mysql数据库.assets/image-20250720185819859.png)

**现在另一个事务要对数据进行修改：**

![image-20250720185831912](./mysql数据库.assets/image-20250720185831912.png)

**对这条数据修改的事务的ID是1020**
它的DB_ROLL_PTR指针就指向修改之前的数据（undo log，是写在缓存中的，提高效率）

![image-20250720190045863](./mysql数据库.assets/image-20250720190045863.png)

**假设现在1000这个事务又把book_name给更改了**
此时之前的这2条数据就是老数据了，都到undo log里面了

![image-20250720190223828](./mysql数据库.assets/image-20250720190223828.png)

MVCC会给我们创建的表增加2个列，一个是事务ID，一个是指向修改前的数据的指针，修改之前的数据都是放在undo log回滚日志当中。当前数据中是哪个事务ID改的就写哪个事务ID号，DB_ROLL_PTR指针把当前数据和旧数据串成1个链表。从当前行的DB_ROLL_PTR可以访问到旧数据，进行回滚是理所当然的。

![image-20250720190547829](./mysql数据库.assets/image-20250720190547829.png)

**绿色数据表示当前行的最新修改的数据**，是事务1002修改的，从指针DB_ROLL_PTR可以找到undo log找到上次修改数据的是事务1001，从事务1001的数据的DB_ROLL_PTR还可以找到上上次的数据是事务1000修改的。以此类推。



#### 数据备份和GUI工具  

#### 数据备份恢复常用命令  

### mysqldump数据备份

部署在后台服务器或者云端的MySQL大部分做了一些限制，在本地无法直连后台服务的数据库 3306端口上，一般有防火墙之类的网络中间件。
没有条件用GUI图形化界面工具，鼠标进行数据备份，数据恢复。
**我们通过命令。命令也最快速。**

mysqldump -u root -p123456 --all-databases > ~/all.sql 
mysqldump -u root -p123456 --databases school > ~/school.sql 
mysqldump -u root -p123456 school user > ~/user.sql 
mysql -u root -p123456 -D school -e 'select \* from user where age>18' > ~/user_data.txt  

source \~/school.sql  

#### SQLyog  

#### Navicat  

## MySQL集群  

在实际生产环境中，如果对mysql数据库的读和写都在一台数据库服务器中操作，无论是在安全性、高可用性，还是高并发等各个方面都是不能满足实际需求的，一般要通过主从复制的方式来同步数据，再通过读写分离来提升数据库的并发负载能力。  

1、数据备份 - 热备份&容灾&高可用 
2、读写分离，支持更大的并发  **（有了主从复制，才可以实现读写分离）**

读写分离：我们读操作多，写操作少。主库专门写操作，数据的更新，通过binlog同步到从库，客户端的查询请求最终映射到从库上，从库读操作。1主2从。提高后端[并发]能力和高可用容灾。

**服务器中间件：消息队列，缓存，zk。
MySQL的中间件：Mycat进行读写分离配置**

### 主从复制  

#### 原理介绍  

**读写分离就是基于主从复制来实现的。**

主从就是角色关系。
主库用来进行增删改查，从库用来数据的同步和备份。

主库（master）从库(slave）
主库对外提供数据的增删改查服务。主库中涉及到数据的修改都会写binary log。
从库通过专门的线程从主库里面的binlog（二进制日志），把主库里跟数据，权限，表结构相关的修改同步到从库里面。相当于就是后台在主库的所有修改通过主从复制机制体现在从库。
好处是可以做一个数据的备份，通过mysql中间件mycat，直接配置热备份&容灾，如果主库挂了，由中间件代理mycat自动把服务的请求映射到从库，又从库继续对外提供服务，这就是容灾，高可用（后端的服务允许一定的异常发生，但是后端的架构服务要可以容错，把这些异常的错误处理掉，并对外重新提供正常的服务）。 

![image-20250516112534347](./mysql数据库.assets/image-20250516112534347.png)

上图中的binlog，我们没有主从复制，也是会写binlog，只不过主从复制就是通过binlog来复制的，从库专门有一个I/O线程：专门读取接收主库发过来的binlog的内容，并写到中继日志relay log，相当于做了一个缓冲。并不是把主库的binlog读过来直接执行，这样的不好之处是：主库的binlog的内容可能很多，而从库接收到binlog的内容执行起来比较慢，导致从库的更新速度和主库相差越来越大。
**从库还会写一个线程**：SQL线程，专门从中继日志读取相应的操作，跟放电影一样，全部的SQL都执行一下，这样，从库的内容和主库的内容同步更新了。

主从复制的流程：两个日志（binlog二进制日志&relay log日志）和三个线程（master的一个线程和slave的二个线程）  

1、主库的更新操作写入binlog二进制日志中。 

 所以，我们的主库要打开二进制日志。因为要把主的数据同步给从。主要写binlog。

2、master服务器创建一个binlog转储线程，将二进制日志内容发送到从服务器。  

3、slave机器执行START SLAVE命令会在从服务器创建一个IO线程，接收master的binary log复制到其中继日志（是在内存上放着，日志多，操作多，所以内存可能放不了，会在磁盘上二级存储）。 

**从线程相当于客户端，是主动来连主库的。** 

首先slave开始一个工作线程（I/O线程），I/O线程在master上打开一个普通的连接，然后开始binlog dump process，binlog dump process从master的二进制日志中读取事件，如果已经跟上master（主库上的dump线程已经把binlog的内容发完了，而且主库上binlog没有产生更多的内容），它会睡眠并等待master产生新的事件，I/O线程将这些事件写入中继日志。  

4、sql slave thread（sql从线程）处理该过程的最后一步，sql线程从中继日志中读取事件，并重放其中的事件而更新slave机器的数据，使其与master的数据一致。只要该线程与I/O线程保持一致，中继日志通常会位于os缓存中，所以中继日志的开销很小。  

#### 配置命令  

条件：master和slave机器的信息  

master（centos7）：192.168.230.101
slave（win10）：192.168.230.101

保证master和slave之间的网络互通，并且保证3306端口是开放的。  

master配置：  

1、开启二进制日志

配置log_bin和全局唯一的server-id。  

2、创建一个用于主从库通信用的账号  

```shell
mysql> CREATE USER 'mslave'@'192.168.131.1' IDENTIFIED BY '1qaz@WSX';
mysql> GRANT REPLICATION SLAVE ON *.* to 'mslave'@'192.168.131.1' IDENTIFIED BY '1qaz@WSX';
mysql> FLUSH PRIVILEGES;
```

3、获取binlog的日志文件名和position

mysql> show master status;  

slave配置：  

1、配置全局唯一的server-id（涉及修改配置文件，需要重启mysql57服务）  

2、使用master创建的账户读取binlog同步数据（stop slave；start slave） 

```shell 
mysql> CHANGE MASTER TO MASTER_HOST='192.168.131.129',
 MASTER_PORT=3306,
 MASTER_USER='mslave',
 MASTER_PASSWORD='1qaz@WSX',
 MASTER_LOG_FILE='mysql-bin.000006',
 MASTER_LOG_POS=1106;
```

3、START SLAVE  

通过show slave status命令查看主从复制状态。show processlist查看master和salve相关线程的运行状态。  

#### 读写分离  

**基于主从复制的读写分离**，是我们在**单机环境**下，数据库的性能到瓶颈了，我们进行读写分离，提高后台服务，**存储这一块的增删改查**的并发的处理能力。**有一个库专门写操作，从库专门读取操作，主库的数据更改提供主从复制同步到从库。**

读写分离就是在主服务器上修改，数据会同步到从服务器，从服务器只能提供读取数据，不能写入，实现备份的同时也实现了数据库性能的优化，以及提升了服务器安全。  

![image-20250516112652800](./mysql数据库.assets/image-20250516112652800.png)

我们把图中的客户端看作：代码 ，作为mysql client的角色，通过mysql 提供的API，用mysql自定义的基于TCP的数据协议，简称mysql协议。我们的一些数据就存在mysql数据库，服务通过操作mysql的API和mysql数据库 进行 通信。双方的数据交互遵守mysql协议。
最初，我们只有一台MySQL服务器，所有数据的增删改查都是在一台机器上进行，随着服务越来越多的人使用，流量越来越大，需要并发能力的不断提升，如果数据库的性能到瓶颈了，我们就要进行读写分离的操作。读操作如果特别多，耗时特别大。

**图中的MySQL主服务端专门做写操作。**
**下面连着2个mysql服务器专门做读操作。**
1主2从。
**在A机器上写，在B, C机器上读**

![image-20250721192153472](./mysql数据库.assets/image-20250721192153472.png)



如果我们在客户端上 直接用代码写死，insert update之类的操作，在A上做，show，select操作在B，C上做，相当于代码和主从环境就是强绑定的状态，代码的稳定性不太好，因为和环境强相关了，必须得知道哪个机器是写操作的主库，哪个机器是读操作的从库，由代码来选择， 比如说哪个机器挂掉了也不知道，没人帮忙动态监测。
所以，用代码 肯定是不合适。

所以实际上，读写分离，分库分表都是需要依赖数据库中间件—》mycat
mycat就是代理服务器的角色。
也是遵循mysql通信协议的。

**配置读写分离，我们在客户端上的代码不用做任何变更，代码上不需要区分哪个是读，哪个是写，代码直接访问的是mycat**

**作为客户端来说，实际上区分不出来连的是mycat还是MySQL，因为通信都是遵守的是mysql通信协议。所以不用进行区分。****客户端通过mysql的API发送的所有的增删改查请求都是到mycat，在mycat配置读写分离。**

mycat对客户端的请求进行解析，如果是写操作，就把这个操作发到主服务器上，如果发现是读操作，就把这个操作发到从服务器上。（读写分离）
**主库上的数据通过主从复制同步到从库。**
**主从复制是在mysql数据库上配置的。**

**在mycat上就是要配置主服务器和从服务器的信息。**
有3种情况：一主一从、一主多从、多主多从。

**我们配置的是1主多从**
当写库挂了，配置上还可以立马把一个从库直接变身成一个写库。然后从库和从库还要配置一下主从复制。

**多主多从，就是mycat后面挂了2套环境（1主2从 为1套）。**

目前较为常见的MySQL读写分离方式有：  

程序代码内部实现引入中间代理层。 MySQL_proxy、Mycat  

#### Mycat读写分离配置  

条件：  

master（centos7）：192.168.131.129 
slave（win10）：192.168.31.27 
1、JDK1.7版本以上（java -version检查jdk环境） 
2、MySQL的root账户有远程访问权限  

安装与配置：  

1、安装Mycat  

- Mycat-server-1.6-RELEASE-20161028204710-linux.tar.gz（见课程附件资料 rz工具上传到linux，tar -zxvf解压） 
- 解压放到/usr/local/mycat目录下 
- ./mycat start启动服务，默认在8066和9066端口  

2、配置文件  

server.xml：配置登录Mycat的账号信息  

```xml
<user name="root">
        <property name="password">123456</property>
        <property name="schemas">USERDB</property>
 </user>
```

schema.xml：配置逻辑库和数据源、续写分离、分库分表信息等  

```xml 
 <?xml version="1.0"?>
 <!DOCTYPE mycat:schema SYSTEM "schema.dtd">
 <mycat:schema xmlns:mycat="http://io.mycat/">
        <!-- 逻辑数据库  -->
        <schema name="USERDB" checkSQLschema="false" sqlMaxLimit="100" 
dataNode="dn1"></schema>
        <!-- 存储节点 -->
        <dataNode name="dn1" dataHost="node1" database="mytest" />
        <!-- 数据库主机 -->
        <dataHost name="node1" maxCon="1000" minCon="10" balance="3"
                          writeType="0" dbType="mysql" dbDriver="native" 
switchType="1"  slaveThreshold="100">
                <heartbeat>select user()</heartbeat>
                <!-- can have multi write hosts -->
                <writeHost host="192.168.131.129" url="192.168.131.129:3306" 
user="root"
                                   password="123456">
                        <!-- can have multi read hosts -->
                        <readHost host="192.168.31.27" url="192.168.31.27:3306" 
user="root" password="123456" />
                </writeHost>
user="root"
 <!-- 写数据库宕机后的备份数据库 -->
 <writeHost host="192.168.31.27" url="192.168.31.27:3306" 
password="123456" />
 </dataHost>
 </mycat:schema>
```

balance：  

"0"：不开启读写分离 

"1"：全部的readHost和stand by writeHost参与select语句的负载 

"2"：所有读操作随机在readHost和writeHost上分发 

"3"：所有读请求随机分发到writeHost对应的readHost上执行  

writeType  = "  0  "：所有写操作发送到配置的第一个writeHost，第一个挂掉切换到还生存的第二个writeHost  

switchType：  

"-1"：不自动切换 
"1"：自动切换，根据心跳select user() 
"2"：基于MySQL的主从同步状态决定是否进行切换 show slave status  

3、端口  

8066：数据端口 
9066：管理端口  

#### 【扩展】 MySQL分库分表  

#### 数据库架构演变  

刚开始多数项目用单机数据库就够了，随着服务器流量越来越大，面对的请求也越来越多，我们做了数据库读写分离， 使用多个从库副本（Slave）负责读，使用主库（Master）负责写，master和slave通过主从复制实现数据同步更新，保持数据一致。slave 从库可以水平扩展，所以更多的读请求不成问题。  

但是当用户量级上升，写请求越来越多，怎么保证数据库的负载足够？增加一个Master是不能解决问题的， 因为数据要保存一致性，写操作需要2个master之间同步，相当于是重复了，而且架构设计更加复杂。  

这时需要用到分库分表（sharding），对写操作进行切分。  

#### 库表问题  

#### 单库太大  

单库处理能力有限、所在服务器上的磁盘空间不足、遇到IO瓶颈，需要把单库切分成更多更小的库  

#### 单表太大  

CURD效率都很低、数据量太大导致索引膨胀、查询超时，需要把单表切分成多个数据集更小的表  

#### 拆分策略  

单个库太大，先考虑是表多还是数据多：  

- 如果因为表多而造成数据过多，则使用垂直拆分，即根据业务拆分成不同的库
- 如果因为单张表的数据量太大，则使用水平拆分，即把表的数据按照某种规则拆分成多张表  

分库分表的原则应该是先考虑垂直拆分，再考虑水平拆分。  

#### 垂直拆分  

server.xml  

```xml 
<user name="root">
        <property name="password">123456</property>
        <property name="schemas">USERDB1,USERDB2</property>
 </user>
```

schema.xml  

```xml
 <?xml version="1.0"?>
 <!DOCTYPE mycat:schema SYSTEM "schema.dtd">
 <mycat:schema xmlns:mycat="http://io.mycat/">
        <!-- 逻辑数据库  -->
        <schema name="USERDB1" checkSQLschema="false" sqlMaxLimit="100" 
dataNode="dn1" />
        <schema name="USERDB2" checkSQLschema="false" sqlMaxLimit="100" 
dataNode="dn2" />
        <!-- 存储节点 -->
        <dataNode name="dn1" dataHost="node1" database="mytest1" />
        <dataNode name="dn2" dataHost="node2" database="mytest2" />
        <!-- 数据库主机 -->
        <dataHost name="node1" maxCon="1000" minCon="10" balance="0"
                          writeType="0" dbType="mysql" dbDriver="native">
                <heartbeat>select user()</heartbeat>
                <writeHost host="192.168.131.129" url="192.168.131.129:3306" 
user="root"
                                   password="123456">
                </writeHost>
        </dataHost>
        <dataHost name="node2" maxCon="1000" minCon="10" balance="0"
                          writeType="0" dbType="mysql" dbDriver="native">
                <heartbeat>select user()</heartbeat>
                <writeHost host="192.168.0.6" url="192.168.0.6:3306" user="root"
                                   password="123456" />
        </dataHost>
 </mycat:schema>
```


#### 垂直分表  

也就是“大表拆小表”，基于列字段进行。 
一般是表中的字段较多，将不常用的， 数据较大，长度较长（比如text类型字段）的拆分到“扩展表“。 
一般是针对几百列的这种大表，也避免查询时，数据量太大造成的“跨页”问题。  

#### 垂直分库  

垂直分库针对的是一个系统中的不同业务进行拆分。  

比如用户User一个库，商品Product一个库，订单Order一个库， 切分后，要放在多个服务器上，而不是一个服务器上。想象一下，一个购物网站对外提供服务，会有用户，商品，订单等的CRUD。没拆分之前， 全部都是落到单一的库上的，这会让数据库的单库处理能力成为瓶颈。按垂直分库后，如果还是放在一个数据库服务器上， 随着用户量增大，这会让单个数据库的处理能力成为瓶颈，还有单个服务器的磁盘空间，内存，tps等非常吃紧。 所以我们要拆分到多个服务器上，这样上面的问题都解决了，以后也不会面对单机资源问题。  

数据库业务层面的拆分，和服务的“治理”，“降级”机制类似，也能对不同业务的数据分别的进行管理维护，监控，扩展等。 数据库往往最容易成为应用系统的瓶颈，而数据库本身属于“有状态”的，相对于Web和应用服务器来讲，是比较难实现“横向扩展”的。 数据库的连接资源比较宝贵且单机处理能力也有限，在高并发场景下，垂直分库一定程度上能够突破IO、连接数及单机硬件资源的瓶颈。  

#### 水平拆分  

server.xml  

```xml
<user name="root">
        <property name="password">123456</property>
        <property name="schemas">USERDB</property>
 </user>
```

schema.xml  

```xml
 <?xml version="1.0"?>
 <!DOCTYPE mycat:schema SYSTEM "schema.dtd">
 <mycat:schema xmlns:mycat="http://io.mycat/">
        <!-- 逻辑数据库  -->
        <schema name="USERDB" checkSQLschema="false" sqlMaxLimit="100">
            <table name="user" dataNode="dn1" />
            <table name="student" primaryKey="id" autoIncrement="true" 
dataNode="dn1,dn2" rule="mod-long"/>
        </schema>
        <!-- 存储节点 -->
        <dataNode name="dn1" dataHost="node1" database="mytest1" />
        <dataNode name="dn2" dataHost="node2" database="mytest2" />
        <!-- 数据库主机 -->
        <dataHost name="node1" maxCon="1000" minCon="10" balance="0"
                          writeType="0" dbType="mysql" dbDriver="native">
                <heartbeat>select user()</heartbeat>
                <writeHost host="192.168.131.129" url="192.168.131.129:3306" 
user="root"
                                   password="123456">
                </writeHost>
        </dataHost>
        <dataHost name="node2" maxCon="1000" minCon="10" balance="0"
                          writeType="0" dbType="mysql" dbDriver="native">
                <heartbeat>select user()</heartbeat>
                <writeHost host="192.168.0.6" url="192.168.0.6:3306" user="root"
                                   password="123456" />
        </dataHost>
 </mycat:schema>
```


#### 水平分表  

针对数据量巨大的单张表（比如订单表），按照某种规则（RANGE,HASH取模等），切分到多张表里面去。 但是这些表还是在同一个库中，所以库级别的数据库操作还是有IO瓶颈，不建议采用。  

#### 水平分库分表  

将单张表的数据切分到多个服务器上去，每个服务器具有相应的库与表，只是表中数据集合不同。 水平分库分表能够有效的缓解单机和单库的性能瓶颈和压力，突破IO、连接数、硬件资源等的瓶颈。  

**【扩展】SQL详细处理流程** 

当我们要进行一个请求的时候，作为mysql client，首先和服务器进行通信，里面有一个查询缓存，属于连接器的部分，MySQL server是网络服务器，控制连接的，走的是TCP 加密的ssl通信协议，客户端在登录的时候，要进行合法性的检查（验证mysql账号密码之类的是否正确，后续如果有SQL过来，如果是select的话优先访问查询缓存，连接器到后边，就是所谓的解析器，解析SQL，生成解析树，验证SQL是否合法。解析器完成以后就到优化器，根据解析完成的SQL生成相应的SQL执行计划以及当前SQL的索引的选择，第五步就是在执行器，调用存储引擎对外提供的API接口，进行数据读写操作。

![image-20250516113049244](./mysql数据库.assets/image-20250516113049244.png)


**连接器：**管理连接，权限验证 
**解析器：**词法以及语法分析 
**优化器：**生成执行计划，选择合适索引 
**执行器：**操作引擎获取结果 
**存储引擎：**存储数据，提供读写接口  

![image-20250516113134634](./mysql数据库.assets/image-20250516113134634.png)

#### 【扩展】 MySQL常用操作  

#### 设置字符编码  

有时候MySQL中的表需要存储中文，需要设置表的字符编码为utf8，否则默认的字符编码有可能不能正确处理中文，在MySQL中设置表的字符编码如下：  

通过命令查看MySQL表的字符编码，如下：  

```mysql
mysql> show variables like 'charac%';
+--------------------------+--------------------------------+
| Variable_name            | Value                          |
+--------------------------+--------------------------------+
| character_set_client     | utf8mb4                        |
| character_set_connection | utf8mb4                        |
| character_set_database   | utf8mb4                        |
| character_set_filesystem | binary                         |
| character_set_results    | utf8mb4                        |
| character_set_server     | utf8mb4                        |
| character_set_system     | utf8mb3                        |
| character_sets_dir       | /usr/share/mysql-8.4/charsets/ |
+--------------------------+--------------------------------+
8 rows in set (0.04 sec)
```

如下设置： 

```shell
mysql> set character_set_server=utf8;
Query OK, 0 rows affected (0.00 sec)
```

字符编码设置utf8成功，MySQL的status**运行状态和运行参数都是通过全局变量来控制的**，**用showstatus和show variables**两个命令可以查看这些信息，用set可以设置这些信息，改变配置。  

#### 设置存储引擎  

如何查看当前MySQL版本都支持哪些存储引擎呢？如下命令：

```mysql 
mysql> show engines\G;
 *************************** 1. row ***************************
 Engine: InnoDB
 Support: DEFAULT
 Comment: Supports transactions, row-level locking, and foreign keys
 Transactions: YES
 XA: YES
 Savepoints: YES
 *************************** 2. row ***************************
 Engine: MRG_MYISAM
 Support: YES
 Comment: Collection of identical MyISAM tables
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 3. row ***************************
 Engine: PERFORMANCE_SCHEMA
 Support: YES
 Comment: Performance Schema
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 4. row ***************************
 Engine: BLACKHOLE
 Support: YES
 Comment: /dev/null storage engine (anything you write to it disappears)
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 5. row ***************************
Engine: CSV
 Support: YES
 Comment: CSV storage engine
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 6. row ***************************
 Engine: MyISAM
 Support: YES
 Comment: MyISAM storage engine
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 7. row ***************************
 Engine: ARCHIVE
 Support: YES
 Comment: Archive storage engine
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 8. row ***************************
 Engine: MEMORY
 Support: YES
 Comment: Hash based, stored in memory, useful for temporary tables
 Transactions: NO
 XA: NO
 Savepoints: NO
 *************************** 9. row ***************************
 Engine: FEDERATED
 Support: NO
 Comment: Federated MySQL storage engine
 Transactions: NULL
 XA: NULL
 Savepoints: NULL
 9 rows in set (0.01 sec)
```

Support后面是DEFAULT，表示当前使用的存储引擎，可以看到上面是InnoDB存储引擎。  

可以在创建表的时候指定存储引擎，如下：  

```mysql
CREATE TABLE ai (
 	i BIGINT(20) NOT NULL AUTO_INCREMENT,
 	PRIMARY KEY (i)
 	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

也可以修改已存在表的存储引擎，如下：  

```mysql
ALTER TABLE user ENGINE = InnoDB;
```

还可以修改配置文件，MySQL Server启动的时候都会加载一个配置文件，windows下是my.ini文件，linux下是my.cnf文件，打开配置文件，在[mysqld]下面添加如下内容，保存，重启MySQL Server服务器，默认就采用配置项指定的存储引擎了。  

```mysql
default-storage-engine=INNODB
```

#### 数据备份与恢复  

通过mysqldump命令把指定db里面的表进行备份，写入sql脚本中：  

mysqldump -h 9.84.205.186 -P 3306 -u root -p123456 db_name table_name $>$ data_log.sql  

登录mysql后，通过source命令可以把sql脚本的数据恢复到db中：  

source /home/localuser/data_log.sql  

还可以直接备份sql查询的数据：  

mysql -h 9.84.205.186 -P 3306 -u root -p123456 -D db_name -e "select \* from db_table" $>$ data_log.txt  

#### 设置支持ip远程连接  

mysql默认只能通过localhost访问，不能通过ip远程访问，主要考虑安全的问题。如果在集群分布式环境中单独部署mysql，就需要设置通过ip远程访问了  

```mysql
mysql> select Host,User,authentication_string from user;
```

上面显示可以看到，mysql默认只支持localhost访问。 

```mysql 
mysql> grant all privileges on *.* to 'root'@'%' identified by '123456' with 
grant option;
 Query OK, 0 rows affected, 1 warning (0.00 sec)
 mysql> flush privileges;
 Query OK, 0 rows affected (0.00 sec)
 mysql> quit
 Bye
 [root@localhost Downloads]# service mysqld restart
 Redirecting to /bin/systemctl restart mysqld.service
```

通过ip地址远程连接mysql如下：  

```mysql
mysql -h 172.20.10.3 -u root -p123456  
```

#### 创建账户  

创建新账户和密码，只能在本地访问：

```mysql  
mysql> create user 'ms'@'localhost' identified by '123456';  
```

创建新账户和密码，可以通过任意或者指定IP访问：  

```mysql
mysql> create user 'ms'@'%' identified by '123456';  
```

新用户创建完成后，刷新库表赋予权限：  

```mysql
mysql> grant all privileges on \*.\* to 'tony'@'%' identified by '123456' with grant option;   
mysql> flush privileges;  
```

### 开启centos防火墙3306端口  

centos7防火墙配置不再是iptables了，配置如下：  

```shell
# 开放3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent
 # 重启防火墙
firewall-cmd --reload
 # 查看当前开放的端口列表
firewall-cmd --list-ports
```

可以在win下测试是否通过mysql命令能够成功连接centos的mysqld服务。  

systemctl start firewalld.service 

systemctl stop firewalld.service 

systemctl enable firewalld.service 

systemctl disable firewalld.service 

systemctl status firewalld.service  

### MySQL面试问题  

范式设计 
SQL 
索引 
存储引擎 
事务 
锁+MVCC 
日志 
主从复制 +  读写分离 
数据库中间件 
分库分表  

可以重点关注面经上的数据库问题！  