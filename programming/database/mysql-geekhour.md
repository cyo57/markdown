---
tags:
  - mariadb
  - database
  - mysql
date: "241011"
origin: geekhour
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

## MariaDB
### 安装 MariaDB

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

设置数据库默认ROOT密码
```shell
root@aliyun-sp:~# mariadb-secure-installation
# 设置默认密码

root@aliyun-sp:~# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
```

### 基础管理

开放远程访问，配置文件 `/etc/mysql/my.cnf`
```ini
[mysqld]
bind-address = 0.0.0.0
```


创建新用户帐户
```sql
-- 创建一个允许远程访问的用户
MariaDB [(none)]> CREATE USER 'testuser'@'%' IDENTIFIED BY 'passwd000000';
Query OK, 0 rows affected (0.002 sec)
```

创建数据库
```sql
CREATE DATABASE gamedata;
USE gamedata;
SHOW DATABASES; --查看数据库列表
```

删除数据库
```sql
DROP DATABASE gamedata;
SHOW DATABASES;
```

创建表，在创建之前先选择数据库
```sql
CREATE DATABASE gamedata;
USE gamedata;

CREATE TABLE player(
    id INT,
    name VARCHAR(12),
    level INT,
    exp INT,
    gold DECIMAL(10,2)
)

DESC player; --描述表的结构
```

> MySQL 数据类型大致可以分为五个大的类别，包括数值类型，日期和时间类型，字符串类型，JSON类型和空间类型，每种类型也包含不同的子类型。

修改字段的属性
```sql
ALTER table player MODIFY COLUMN name VARCHAR(20);
```

字段的名称修改
```sql
ALTER TABLE player RENAME COLUMN name to nick_name;
```

增加新的字段
```sql
ALTER TABLE player ADD COLUMN last_login datetime;
```

删除字段
```sql
ALTER TABLE player DROP COLUMN last_login;
```

删除表
```sql
DROP TABLE player;
```

### 操作表 (增删改查)

插入数据
```sql
INSERT INTO user (id, name, age) VALUES (1, '李子龙', 18);

SELECT * FROM user;
```

INSERT 也可以插入多条数据，直接用逗号隔开
```sql
INSERT INTO user (id, name, age) VALUES (1, '李子龙', 18), (2, '李父龙', 18);
```

为字段设置默认值
```sql
ALTER TABLE user MODIFY age INT DEFAULT 1;
INSERT INTO user (id, name) VALUES (1, '子龙'), (2, '李父父龙');
```

> 此时插入数据不指定 age 已经默认为 1 了。
> 也可以在建表的时候指定一个默认值，也可以使用 NULL 或者 NOT NULL 指定是否允许为空值，或者使用 UNIQUE 指定字段是否唯一，除此之外还有
> 主键约束：保证数据的唯一性且不为空
> 外键约束：一个表的外键必须是另一个表的主键

这里有个小问题，之前插入的等级还是 NONE
修改已存在的数据化
```sql
UPDATE user set age=18 WHERE name='子龙';
```

删除表中的数据
```sql
DELETE FROM user WHERE age is NULL;
DELETE FROM user WHERE age=18;
```

### 导入导出

导出数据库
```shell
mysqldump -u root -p dbname tablename > db.sql
```

> [!tip]
> dbname 代表数据库，tablename 代表数据表
> 如果不加 tablename 则导出整个 dbname

导入数据库
```shell
mysql -u root -p dbname < game.sql
```

### 常用语句

> [!tip]
> 这里导入了 GeekHour 课程的 [练习配套数据](https://github.com/geekhournet/mysql-course) 进行学习
> 
> 若你使用 `MariaDB`，可以将 SQL 文件中的 `COLLATE=utf8mb4_0900_ai_ci` 修改为 `COLLATE=utf8mb4_unicode_520_ci`

查找多个条件的数据
```sql
SELECT * FROM player WHERE level > 1 and level < 5;

SELECT * FROM player
WHERE level > 1 AND level < 5 OR exp > 1 AND exp < 5;

SELECT * FROM player
WHERE level > 1 AND (level < 5 OR exp > 1) AND exp < 5;
```

优先级顺序是 NOT > AND > OR，除非被括号`()`改变优先级


使用 IN 指定查找多个值
```sql
-- 查找等级为 1/3/5 的玩家
SELECT * FROM player
WHERE level IN (1,3,5);
```


使用 BETWEEN / END 查找指定范围的值
```sql
SELECT * FROM player
WHERE level BETWEEN 1 AND 10;
-- 等同于 >= 1 and <= 10
```


使用 NOT 取反
```sql
SELECT * FROM player
WHERE level NOT BETWEEN 1 AND 10;
-- NOT 可以加在任何条件语句前
```


使用 LIKE 模糊查找，`%`表示任意n个字符，`_`表示任意一个字符
```sql
SELECT * FROM player
WHERE name LIKE '王%';

SELECT * FROM player
WHERE name LIKE '%王%';

SELECT * FROM player
WHERE name LIKE '王_';
```


使用 REGEXP 正则表达式查找
```sql
SELECT * FROM player
WHERE name REGEXP 'PATTRN';

SELECT * FROM player
WHERE name REGEXP '^王.$';
```


使用 IS NULL 查找空结果
> [!warning]
> 有些字段是空字符串而不是 NULL ，NULL表示没有值
> 查询时推荐 `WHERE email IS NULL or email = ''`

```sql
SELECT * FROM player
WHERE email IS NULL;

SELECT * FROM player
WHERE email IS NOT NULL;
```


ORDER BY 依据某个字段排列
```sql
SELECT * FROM player
ORDER BY level;
-- 默认为升序

SELECT * FROM player
ORDER BY level DESC;
-- 降序排列

SELECT * FROM player
ORDER BY level DESC, exp DESC;
-- 多个条件排列，等级相同则按照经验排列

SELECT * FROM player
ORDER BY 5;
-- 按照列序号排序（第五列）
```


聚合函数，对某列进行一些计算

| 函数名   | 作用  |
| ----- | --- |
| AVG   | 平均值 |
| COUNT | 项目数 |
| MAX   | 最大值 |
| MIN   | 最小值 |
| SUM   | 求和  |
| ...   | ... |
```sql
SELECT COUNT(*) FROM player;
-- 查询总人数

SELECT AVG(level) FROM player;
-- 查询平均值
```


GROUP BY 分组查询
```sql
SELECT sex, COUNT(*) FROM player
GROUP BY sex;
-- 每个性别的玩家有多少名

SELECT level, COUNT(level) FROM player
GROUP BY level;
-- 每个等级的玩家有多少名
```


HAVING 过滤分组后的结果
```sql
SELECT level, COUNT(level) FROM player
GROUP BY level
HAVING count(level) > 4;
-- 只统计玩家数大于四的等级

SELECT level, COUNT(level) FROM player
GROUP BY level
HAVING level > 4;
-- 只统计等级大于四的玩家

SELECT level, COUNT(level) FROM player
GROUP BY level
HAVING level > 4
ORDER BY level DESC;
-- 只统计等级大于四的玩家，并降序排列
```


LIMIT 限制数量
```sql
SELECT level, COUNT(level) FROM player
GROUP BY level
ORDER BY level DESC
LIMIT 5;
-- 只统计等级大于四的玩家，并降序排列，只统计前五个

SELECT level, COUNT(level) FROM player
GROUP BY level
ORDER BY level DESC
LIMIT 3,3;
-- 只统计等级大于四的玩家，并降序排列
-- LIMIT 偏移量,数量 此处相当于从第四名开始，返回三个
```



> [!tip]
> 小练习
> 统计表中每个玩家的数量，倒序排列，只显示数量大于等于5的姓

```sql
-- SUBSTR 参数分别为截取的字符串，开始，结束位置
SELECT SUBSTR(username,1,1) AS 姓, COUNT(SUBSTR(username,1,1)) AS 数量 FROM student
GROUP BY SUBSTR(username,1,1)
HAVING COUNT(SUBSTR(username,1,1)) >= 5
ORDER BY COUNT(SUBSTR(username,1,1)) DESC;
```