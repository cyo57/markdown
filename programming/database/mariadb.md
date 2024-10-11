---
tags:
  - mariadb
  - database
  - mysql
date: "241011"
---
# SQL

## 关系数据库概述

#### 数据模型

数据库按照结构来组织, 存储和管理数据. 实际上, 数据库一共有三种模型

- 层次模型
  以上下级的层次关系来组织数据, 为树状结构
- 网状模型
  每个数据节点其他很多节点连接, 数据结构看起来像城市路网
- 关系模型
  二维表格, 类似于Excel表

随着时间的推移, 关系模型相比层次和网状更广泛, 理解和使用最简单

关系数据的关系模型给予数学理论建立, 域 (Domain) 定义为一组具有相同数据类型的值得集合, 给定一组域 D1, D2, ..., Dn, 他们的笛卡尔集定义为D1\*D2……\*Dn={(d1,d2,...,dn)|di∈Di,i=1,2,...,n}, ........

基于数学理论的关系模型虽然讲起来复杂, 但是基于日常的关系模型十分容易理解, 例如

| ID   | 姓名 | 班级 | 性别 | 年龄 |
| ---- | ---- | ---- | ---- | ---- |
| 1    | 小明 | 201  | 男   | 9    |
| 2    | 小红 | 202  | 女   | 8    |
| 3    | 小军 | 202  | 男   | 10   |

其中班级ID对应着另一个班级表

| ID   | 名称       | 班主任 |
| ---- | ---------- | ------ |
| 201  | 二年级一班 | 王老师 |
| 202  | 二年级二班 | 李老师 |

这样, 二维表格之间通过ID映射建立了"一对多"关系

#### 数据类型

对于一个关系表, 除了定义每一列的名称, 还需要定义类型.
关系数据库支持的标准类型包括数值, 字符串, 时间 等.

| 名称         | 类型           | 说明                                                         |
| ------------ | -------------- | ------------------------------------------------------------ |
| INT          | 整型           | 4字节整数类型，范围约+/-21亿                                 |
| BIGINT       | 长整型         | 8字节整数类型，范围约+/-922亿亿                              |
| REAL         | 浮点型         | 4字节浮点数，范围约+/-1038                                   |
| DOUBLE       | 浮点型         | 8字节浮点数，范围约+/-10308                                  |
| DECIMAL(M,N) | 高精度小数     | 由用户指定精度的小数，例如，DECIMAL(20,10)表示一共20位，其中小数10位，通常用于财务计算 |
| CHAR(N)      | 定长字符串     | 存储指定长度的字符串，例如，CHAR(100)总是存储100个字符的字符串 |
| VARCHAR(N)   | 变长字符串     | 存储可变长度的字符串，例如，VARCHAR(100)可以存储0~100个字符的字符串 |
| BOOLEAN      | 布尔类型       | 存储True或者False                                            |
| DATE         | 日期类型       | 存储日期，例如，2018-06-22                                   |
| TIME         | 时间类型       | 存储时间，例如，12:20:59                                     |
| DATETIME     | 日期和时间类型 | 存储日期+时间，例如，2018-06-22 12:20:59                     |

同时很多数据类型还有别名, 例如`real`可以写`FLOAT(24)`. 还有一些不常见类型, 比如`TINYINT`. 各数据库厂商还支持特定数据类型, 例如`JSON`

选择数据类型时, 根据业务规则选择合适的类型. 通常来说 `BIGINT` 能满足整数,  `VARCHAR(N)` 能满足字符串, 这两种类型使用最广

#### 主流关系数据库

- 商用
  Oracle, SQL Server, DB2
- 开源
  MySQL, PostgreSQL
- 桌面
  Access
- 嵌入式
  Sqlite

#### SQL

SQL是结构化查询语言的缩写, 既可以查询, 也可以添加, 更新, 删除数据库中的数据, 还可以进行管理和维护. 不同的数据库都支持SQL.

虽然SQL被ANSI组织定义为标准, 但各个数据库对标准SQL支持不太一致. 并且大部分数据库在SQL上做了拓展. 也就是说如果只是用标准SQL, 理论上所有数据库都可以支持, 但特定拓展换一个数据库就不一定能执行了.

总的来说, SQL语言定义了几种能力

- **DDL: Data Definition Language**
  允许用户定义数据,也就是创建表, 删除表, 修改表结构. 通常DDL由数据库管理员执行
- **DML: Data Manipulation Language**
  为用户提供添加, 删除, 更新数据的能力, 这些是应用程序对数据库的日常操作
- **DQL: Data Query Language**
  用户查询数据, 通常是最频繁的日常操作

#### 语法特点

SQL语言关键字不区分大小写, 但针对不同的数据库, 对于表名和列名, 有的数据库区分大小写, 有的数据库不区分大小写. 同一个数据库, 有的在Linux上区分, 有的在Windows上不区分.

## 安装MySQL

MySQL只是一个SQL接口, 它的内部包含了多种数据引擎, 常用的包括

- InnoDB
  支持事务的数据库引擎, 2006年被Oracle收购
- MyISAM
  MySQL早期集成的默认引擎

使用MySQL时, 不同的表可以使用不同的引擎, 如果知道用哪个就用`InnoDB`

因为MySQL是开源, 所以基于此项目衍生出了各种版本

- MariaDB
  MySQL创建的开源分支, 使用XtraDB引擎
- Aurora
  Amazon改进的版本, 提供给AWS用户
- PolarDB
  Alibaba改进的版本, 提供给阿里云用户

MySQL官方版本也有很多版本

- Community Edition
- Standard Edition
- Enterprise Edition
- Cluster Carrier Grade Edition

以上版本的功能依次递增，价格也依次递增。不过，功能增加的主要是监控、集群等管理功能，对于基本的SQL功能是完全一样的。

#### 安装
Windows和Mac用户可以通过 https://dev.mysql.com/downloads/mysql/ 下载Community版本

Debian和Ubuntu用户可以简单地通过命令`apt-get install mysql-server`

> [!tip]
> Debian12 已经从官方仓库移除 mysql，可以使用 mariadb 代替

#### 运行 MariaDB

apt 安装完成后会自动为 MariaDB 设置 service 并立即运行

验证安装成功
```shell
root@aliyun-sp:~# mysqladmin --version
mysqladmin  Ver 9.1 Distrib 10.5.26-MariaDB, for debian-linux-gnu on x86_64
```

设置数据库密码
```shell
root@aliyun-sp:~# mysqladmin -u root password passwd000000;

root@aliyun-sp:~# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
```

### 基础管理

创建新用户帐户
```sql
MariaDB [(none)]> CREATE USER 'testuser'@'localhost' IDENTIFIED BY 'passwd000000';
Query OK, 0 rows affected (0.002 sec)
```

设置权限
```sql
CREATE DATABASE mydatabase;
USE mydatabase;

CREATE TABLE exampletable (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

GRANT SELECT, INSERT, UPDATE, DELETE ON database TO 'testuser'@'localhost';
```


配置文件 /etc/mysql/my.cnf
选择是否开放远程访问
```shell
[mysqld]

```