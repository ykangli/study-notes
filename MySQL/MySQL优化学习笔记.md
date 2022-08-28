# MySQL优化学习笔记

# 一、MySQL架构介绍

## 01_课程简介

[教学视频](https://www.bilibili.com/video/BV1KW411u7vy)

1. MySQL架构介绍
2. 索引优化分析
3. 查询截取分析
4. MySQL锁机制
5. 主从复制

## 02_MySQL简介

**概述**

MySQL是一个关系型数据库管理系统，由瑞典MySQL AB公司开发，目前属于Oracle公司。

MySQL是一种关联数据库管理系统，将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。

MySQL是开源的，所以你不需要支付额外的费用。

MySQL支持大型的数据库。可以处理拥有上千万条记录的大型数据库。MySQL使用标准的SQL数据语言形式。

MySQL可以允许于多个系统上，并且支持多种语言。这些编程语言包括C、C++、Python、Java、Per、PHP、Eifel、Ruby和Tcl等。Mysql对PHP有很好的支持，PHP是目前最流行的Web开发语言。

MySQL支持大型数据库，支持5000万条记录的数据仓库，32位系统表文件最大可支持4GB，64位系统支持最大的表文件为8TB。Mysql是可以定制的，采用了GPL协议，你可以修改源码来开发自己的MySQL系统。

**高级MySQL**

MySQL内核

SQL优化攻城狮

MySQL服务器的优化

各种参数常量设定

查询语句优化

主从复制

软硬件升级

容灾备份

SQL编程

完整的MySQL优化需要很深的功底，大公司甚至有专门的DBA写上述。

## 03_RPM安装

MySQL Linux版（CentOS）

[官网下载地址](https://dev.mysql.com/downloads/mysql/)

**检查当前系统是否安装过MySQL**

- 查询命令：`rpm -qa|grep -i mysql`
- 删除命令：`rpm -e RPM软件包名(该名字是上一个命令查出来的名字)`

没装过：

![img](https://img-blog.csdnimg.cn/img_convert/7e1d45b749a2675a1c442e1c3858d32b.png)

已装过

![img](https://img-blog.csdnimg.cn/img_convert/14f1e3891e0ae2758aa7d89a00ffd405.png)

**安装MySQL服务端（注意提示）**

- 安装命令：`rpm -ivh RPM软件包名`

![img](https://img-blog.csdnimg.cn/img_convert/511fbc00ee4fc1b3763773b1b2dae8db.png)

![img](https://img-blog.csdnimg.cn/img_convert/e2b6968cacb9baae10527599aef1ce12.png)

**安装MySQL客户端**

- 安装命令：`rpm -ivh RPM软件包名`

![img](https://img-blog.csdnimg.cn/img_convert/bfd2148f1fd6e9efb31cce2ba36caeee.png)

**查看MySQL安装时创建的MySQL用户和MySQL组**

- `cat /etc/passwd |grep mysql`
- `cat /etc/group |grep mysql`

![img](https://img-blog.csdnimg.cn/img_convert/4d513892b6b76ab7accaf2b63c24307f.png)

或者可以执行`mysqladmin --version`命令。

![img](https://img-blog.csdnimg.cn/img_convert/74b4faf6cb1835d1d65984bbe677c2ba.png)

或者`ps -ef | grep mysql`

**MySQL服务启动与暂停**

- `service mysql start`
- `service mysql stop`
- `ps -ef | grep mysql`查看

## 04_ROOT密码设置和开机自启动

mysql服务启动后，开始连接。

**首次连接成功**

- 输入命令`mysql`
- 退出命令`exit`

![img](https://img-blog.csdnimg.cn/img_convert/9fbca5b421087d417b8ab970c4dbd17e.png)

注意这里，因为MySQL默认没有密码，所以这里我们没有输入密码就直接连上了。

**按照安装Server中的提示修改登录密码**

- `/usr/bin/mysqladmin -u root password 123456`

![img](https://img-blog.csdnimg.cn/img_convert/46239fd0bb8413a9190971ceda1de314.png)

**自启动mysql服务**

![img](https://img-blog.csdnimg.cn/img_convert/4b75b917ae2290316ec0de9b25fbb273.png)

![img](https://img-blog.csdnimg.cn/img_convert/6c2b15a5409478432113233c79644e5b.png)

## 05_安装位置

在Linux下查看安装目录`ps -ef | grep mysql`。

![img](https://img-blog.csdnimg.cn/img_convert/f29e8b13366992e76d9c52e3e7ca7bbc.png)

| 路径              | 解释                      | 备注                               |
| ----------------- | ------------------------- | ---------------------------------- |
| /var/lib/mysql    | mysql数据库文件的存放路径 | 如/var/lib/mysql/atguigu.cloud.pid |
| /usr/share/mysql  | 配置文件目录              | mysql.server命令及配置文件         |
| /usr/bin          | 相关命令目录              | mysqladmin mysqldump等命令         |
| /etc/init.d/mysql | 启停相关脚本              |                                    |

## 06_修改字符集

**修改配置文件**

- `cp /usr/share/mysql/my-huge.cnf /etc/my.cnf`

![img](https://img-blog.csdnimg.cn/img_convert/012c6793426477a25fdcd0d2ad370ad8.png)

> **为什么mysql会自己读取/etc/my.cnf**？
>
> 打开mysql.server你就会发现conf=/etc/my.cnf居然是写死的。
>
> [link](https://blog.csdn.net/DataIntel_XiAn/article/details/96473095)

**修改字符集和数据存储路径**

插入中文，未能正确显示：

![img](https://img-blog.csdnimg.cn/img_convert/24616cdb7ce749f6688cbbc7e30c4b67.png)

**查看字符集**

- `show variables like 'character%";`
- `show variables like ‘%char%";`

![img](https://img-blog.csdnimg.cn/img_convert/1c1deb87b242c21489800135717409c8.png)

![img](https://img-blog.csdnimg.cn/img_convert/d03b5c6736fa0f73f438a68abd091452.png)

默认的是客户端和服务器都用了latin1，所以会乱码。

**修改**

- `vim /etc/my.cnf`

![img](https://img-blog.csdnimg.cn/img_convert/f266212a51245407521a5aaff925859f.png)

![img](https://img-blog.csdnimg.cn/img_convert/613db422c0c3bb568d4c8d5fb128ea28.png)

红色字为修改项

**重启数据库**

- `service mysql start`
- `service mysql stop`

**重新连接后重新create databse并使用新建库，然后再重新建表试试**

## 07_MySQL配置文件

主要配置文件

二进制日志log-bin

- 用于主从复制

错误日志log-error

- 默认是关闭的，记录严重的警告和错误信息，每次启动和关闭的详细信息等。

查询日志log

- 默认关闭，记录查询的sql语句，如果开启会减低mysql的整体性能，因为记录日志也是需要消耗系统资源

数据文件

- 两系统
  - windows
    - 输入`mysql`后`select @@database;`
  - linux
    - 默认路径：/var/lib/mysql
- frm文件（form）
  - 存放表结构
- myd文件（my data）
  - 存放表数据
- myi文件（my index）
  - 存放表索引

如何配置

- Windows - my.ini文件
- Linux - /etc/my.cnf文件

## 08_MySQL逻辑架构简介

**总体概览**

和其它数据库相比，MySQL有点与众不同，它的架构可以在多种不同场景中应用并发挥良好作用。主要体现在存储引擎的架构上，

插件式的存储引擎架构将查询处理和其它的系统任务以及数据的存储提取相分离。这种架构可以根据业务的需求和实际需要选择合适的存储引擎。

![img](https://img-blog.csdnimg.cn/img_convert/d330e53fd93b8da4e74ac3dfeaf2d288.png)

1.连接层

最上层是一些客户端和连接服务，包含本地sock通信和大多数基于客户端/服务端工具实现的类似于tcplip的通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

2.服务层

第二层架构主要完成大多少的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺序是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存。如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。

3.引擎层

存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同这样我们可以根据自己的实际需要进行选取，后面介绍MyISAM和InnoDB。

4.存储层
数据存储层，主要是将数据存储在运行于裸设备的文件系统之上，并完成与存储引擎的交互。

## 09_存储引擎简介

查看命令：

- 查看mysql以提供什么存储引擎
  - `show engines;`

```cmd
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.01 sec)

mysql>
1234567891011121314151617
```

- 查看mysql当前默认的存储引擎
  - `show variables like '%storage_engine%';`

```cmd
mysql> show variables like '%storage_engine%';
+----------------------------------+--------+
| Variable_name                    | Value  |
+----------------------------------+--------+
| default_storage_engine           | InnoDB |
| default_tmp_storage_engine       | InnoDB |
| disabled_storage_engines         |        |
| internal_tmp_disk_storage_engine | InnoDB |
+----------------------------------+--------+
4 rows in set, 1 warning (0.01 sec)
12345678910
```

**MyISAM和InnoDB**

| 对比项   | MyISAM                                                   | InnoDB                                                       |
| -------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 主外键   | 不支持                                                   | 支持                                                         |
| 事务     | 不支持                                                   | 支持                                                         |
| 行表锁   | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作 | 行锁，操作时只锁某一行，不对其他行有影响，适合高并发的操作   |
| 缓存     | 只缓存索引，不缓存真实数据                               | 不仅缓存索引还要缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 表空间   | 小                                                       | 大                                                           |
| 关注点   | 性能                                                     | 事务                                                         |
| 默认安装 | 是                                                       | 是                                                           |

**阿里巴巴、淘宝用哪个？**

![img](https://img-blog.csdnimg.cn/img_convert/15adb4a83b06c602363685c775c8ac5c.png)

- Percona为MySQL数据库服务器进行了改进，在功能和性能上较MySQL有着很显著的提升。该版本提升了在高负载情况下的InnoDB的性能、为DBA提供一些非常有用的性能诊断工具；另外有更多的参数和命令来控制服务器行为。
- 该公司新建了一款存储引擎叫xtradb完全可以替代innodb,并且在性能和并发上做得更好，
- 阿里巴巴大部分mysql数据库其实使用的percona的原型加以修改。
- AliSql+AliRedis

# 二、索引优化分析

## 10_SQL性能下降原因

1.查询语句写的烂

2.索引失效

- 单值

```sql
select * from user where name='';
create index idx_user_name on user(name);
12
```

- 复合

```sql
select * from user where name='' and email='';
create index idx_user_name on user(name, email);
12
```

3.关联查询太多join（设计缺陷或不得已的需求）

4.服务器调优及各个参数设置（缓冲、线程数等）

## 11_SQL执行加载顺序

手写

```sql
SELECT DISTINCT
    <select_list>
FROM
    <left_table> <join_type>
JOIN 
    <right_table> 
ON
    <join_condition>
WHERE
    <where_condition>
GROUP BY
    <group_by_list>
HAVING
    <having_condition>
ORDER BY
    <order_by_condition>
LIMIT
    <limit_number>
123456789101112131415161718
```

机读

```sql
1 FROM <left_table>
2 ON <join_condition>
3 <join_type> JOIN <right_table>
4 WHERE <where_condition>
5 GROUP BY <group_by_list>
6 HAVING <having_condition>
7 SELECT
8 DISTINCT <select_list>
9 ORDER BY <order_by_condition>
10 LIMIT <limit_number>
12345678910
```

总结

![img](https://img-blog.csdnimg.cn/img_convert/300509958f3201f1192342b06c5a552f.png)

## 12_七种JOIN理论

![img](https://img-blog.csdnimg.cn/img_convert/b83a988a5240818d9beb29bae12d76fb.png)

## 13_七种JOIN的SQL编写

准备工作，创建以下表，插入新数据：

```sql
CREATE TABLE tbl_dept(
	id INT(11) NOT NULL AUTO_INCREMENT,
	deptName VARCHAR(30) DEFAULT NULL,
	locAdd VARCHAR(40) DEFAULT NULL,
	PRIMARY KEY(id)
)ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

CREATE TABLE tbl_emp (
	id INT(11) NOT NULL AUTO_INCREMENT,
	NAME VARCHAR(20) DEFAULT NULL,
	deptId INT(11) DEFAULT NULL,
	PRIMARY KEY (id),
	KEY fk_dept_Id (deptId)
	#CONSTRAINT 'fk_dept_Id' foreign key ('deptId') references 'tbl_dept'('Id')
)ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

INSERT INTO tbl_dept(deptName,locAdd) VALUES('RD',11);
INSERT INTO tbl_dept(deptName,locAdd) VALUES('HR',12);
INSERT INTO tbl_dept(deptName,locAdd) VALUES('MK',13);
INSERT INTO tbl_dept(deptName,locAdd) VALUES('MIS',14);
INSERT INTO tbl_dept(deptName,locAdd) VALUES('FD',15);

INSERT INTO tbl_emp(NAME,deptId) VALUES('z3',1);
INSERT INTO tbl_emp(NAME,deptId) VALUES('z4',1);
INSERT INTO tbl_emp(NAME,deptId) VALUES('z5',1);
INSERT INTO tbl_emp(NAME,deptId) VALUES('w5',2);
INSERT INTO tbl_emp(NAME,deptId) VALUES('w6',2);
INSERT INTO tbl_emp(NAME,deptId) VALUES('s7',3);
INSERT INTO tbl_emp(NAME,deptId) VALUES('s8',4);
INSERT INTO tbl_emp(NAME,deptId) VALUES('s9',51);

12345678910111213141516171819202122232425262728293031
```

执行后的结果：

```sql
mysql> select * from tbl_dept;
+----+----------+--------+
| id | deptName | locAdd |
+----+----------+--------+
|  1 | RD       | 11     |
|  2 | HR       | 12     |
|  3 | MK       | 13     |
|  4 | MIS      | 14     |
|  5 | FD       | 15     |
+----+----------+--------+
5 rows in set (0.00 sec)

mysql> select * from tbl_emp;
+----+------+--------+
| id | NAME | deptId |
+----+------+--------+
|  1 | z3   |      1 |
|  2 | z4   |      1 |
|  3 | z5   |      1 |
|  4 | w5   |      2 |
|  5 | w6   |      2 |
|  6 | s7   |      3 |
|  7 | s8   |      4 |
|  8 | s9   |     51 |
+----+------+--------+
8 rows in set (0.00 sec)

mysql>
12345678910111213141516171819202122232425262728
```

**1.inner join**

```sql
mysql> select * from tbl_emp a inner join tbl_dept b on a.deptId = b.id;
+----+------+--------+----+----------+--------+
| id | NAME | deptId | id | deptName | locAdd |
+----+------+--------+----+----------+--------+
|  1 | z3   |      1 |  1 | RD       | 11     |
|  2 | z4   |      1 |  1 | RD       | 11     |
|  3 | z5   |      1 |  1 | RD       | 11     |
|  4 | w5   |      2 |  2 | HR       | 12     |
|  5 | w6   |      2 |  2 | HR       | 12     |
|  6 | s7   |      3 |  3 | MK       | 13     |
|  7 | s8   |      4 |  4 | MIS      | 14     |
+----+------+--------+----+----------+--------+
7 rows in set (0.00 sec)
12345678910111213
```

**2.left join**

```sql
mysql> select * from tbl_emp a left join tbl_dept b on a.deptId = b.id;
+----+------+--------+------+----------+--------+
| id | NAME | deptId | id   | deptName | locAdd |
+----+------+--------+------+----------+--------+
|  1 | z3   |      1 |    1 | RD       | 11     |
|  2 | z4   |      1 |    1 | RD       | 11     |
|  3 | z5   |      1 |    1 | RD       | 11     |
|  4 | w5   |      2 |    2 | HR       | 12     |
|  5 | w6   |      2 |    2 | HR       | 12     |
|  6 | s7   |      3 |    3 | MK       | 13     |
|  7 | s8   |      4 |    4 | MIS      | 14     |
|  8 | s9   |     51 | NULL | NULL     | NULL   |
+----+------+--------+------+----------+--------+
8 rows in set (0.00 sec)
1234567891011121314
```

**3.right join**

```sql
mysql> select * from tbl_emp a right join tbl_dept b on a.deptId = b.id;
+------+------+--------+----+----------+--------+
| id   | NAME | deptId | id | deptName | locAdd |
+------+------+--------+----+----------+--------+
|    1 | z3   |      1 |  1 | RD       | 11     |
|    2 | z4   |      1 |  1 | RD       | 11     |
|    3 | z5   |      1 |  1 | RD       | 11     |
|    4 | w5   |      2 |  2 | HR       | 12     |
|    5 | w6   |      2 |  2 | HR       | 12     |
|    6 | s7   |      3 |  3 | MK       | 13     |
|    7 | s8   |      4 |  4 | MIS      | 14     |
| NULL | NULL |   NULL |  5 | FD       | 15     |
+------+------+--------+----+----------+--------+
8 rows in set (0.00 sec)
1234567891011121314
```

**4.left join 2**

```sql
mysql> select * from tbl_emp a left join tbl_dept b on a.deptId = b.id where b.id is null;
+----+------+--------+------+----------+--------+
| id | NAME | deptId | id   | deptName | locAdd |
+----+------+--------+------+----------+--------+
|  8 | s9   |     51 | NULL | NULL     | NULL   |
+----+------+--------+------+----------+--------+
1 row in set (0.00 sec)
1234567
```

**5.right join 2**

```sql
mysql> select * from tbl_emp a right join tbl_dept b on a.deptId = b.id where a.deptid is null;
+------+------+--------+----+----------+--------+
| id   | NAME | deptId | id | deptName | locAdd |
+------+------+--------+----+----------+--------+
| NULL | NULL |   NULL |  5 | FD       | 15     |
+------+------+--------+----+----------+--------+
1 row in set (0.00 sec)
1234567
```

**6.full join**

MySQL不支持full join，不过可以换种方法表示

```sql
mysql> select * from tbl_emp a left join tbl_dept b on a.deptId = b.id
    -> union
    -> select * from tbl_emp a right join tbl_dept b on a.deptId = b.id;
+------+------+--------+------+----------+--------+
| id   | NAME | deptId | id   | deptName | locAdd |
+------+------+--------+------+----------+--------+
|    1 | z3   |      1 |    1 | RD       | 11     |
|    2 | z4   |      1 |    1 | RD       | 11     |
|    3 | z5   |      1 |    1 | RD       | 11     |
|    4 | w5   |      2 |    2 | HR       | 12     |
|    5 | w6   |      2 |    2 | HR       | 12     |
|    6 | s7   |      3 |    3 | MK       | 13     |
|    7 | s8   |      4 |    4 | MIS      | 14     |
|    8 | s9   |     51 | NULL | NULL     | NULL   |
| NULL | NULL |   NULL |    5 | FD       | 15     |
+------+------+--------+------+----------+--------+
9 rows in set (0.00 sec)
1234567891011121314151617
```

**7.full join 2**

```sql
mysql> select * from tbl_emp a left join tbl_dept b on a.deptId = b.id where b.id is null union select * from tbl_emp a right join tbl_dept b on a.deptId = b.id where a.deptId is null;
+------+------+--------+------+----------+--------+
| id   | NAME | deptId | id   | deptName | locAdd |
+------+------+--------+------+----------+--------+
|    8 | s9   |     51 | NULL | NULL     | NULL   |
| NULL | NULL |   NULL |    5 | FD       | 15     |
+------+------+--------+------+----------+--------+
2 rows in set (0.00 sec)
12345678
```

## 14_索引是什么

**MySQL官方对索引的定义为：索引（Index）是帮助MySQL高效获取数据的数据结构。可以得到索引的本质：索引是数据结构。**

索引的目的在于提高查询效率，可以类比字典。

如果要查“mysql”这个单词，我们肯定需要定位到m字母，然后从下往下找到y字母，再找到剩下的sql。

如果没有索引，那么你可能需要逐个逐个寻找，如果我想找到Java开头的单词呢？或者Oracle开头的单词呢？

是不是觉得如果没有索引，这个事情根本无法完成？

**你可以简单理解为“排好序的快速查找数据结构”。**

**详解**

在数据之外，**数据库系统还维护着满足特定查找算法的数据结构**，这些数据结构以某种方式引用(指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引。下图就是一种可能的索引方式示例：

![img](https://img-blog.csdnimg.cn/img_convert/5fad4fc5ca7d3e1ddbb83c45fea72525.png)

左边是数据表，一共有两列七条记录，最左边的是数据记录的物理地址。

为了加快Col2的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找在一定的复杂度内获取到相应数据，从而快速的检索出符合条件的记录。

**数据本身之外，数据库还维护着一个满足特定查找算法的数据结构，这些数据结构以某种方式指向数据，这样就可以在这些数据结构的基础上实现高级查找算法，这种数据结构就是索引。**

一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储的磁盘上。

我们平常所说的索引，如果没有特别指明，都是指B树（多路搜索树，并不一定是二叉的）结构组织的索引。其中聚集索引，次要索引，覆盖索引，复合索引，前缀索引，唯一索引默认都是使用B+树索引，统称索引。当然，除了B+树这种类型的索引之外，还有哈稀索引(hash index)等。

## 15_索引优劣势

**优势**

类似大学图书馆建书目索引，提高数据检索的效率，降低数据库的IO成本。

通过索引列对数据进行排序，降低数据排序的成本，降低了CPU的消耗。

**劣势**

实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录，所以索引列也是要占用空间的（占空间）

虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息。（）

索引只是提高效率的一个因素，如果你的MysQL有大数据量的表，就需要花时间研究建立最优秀的索引，或优化查询。

**总结**

索引，空间换取时间。

## 16_索引分类和建索引命令语句

MySQL索引分类：

- 单值索引：即一个索引只包含单个列，一个表可以有多个单列索引。
- 唯一索引：索引列的值必须唯一，但允许有空值。
- 复合索引：即一个索引包含多个列。
- 基本语法：
  - 创建
    - `CREATE [UNIQUE] INDEX indexName ON mytable(columnName(length));`
    - `ALTER mytable ADD [UNIQUE] INDEX [indexName] ON (columnName(length));`
  - 删除
    - `DROP INDEX [indexName] ON mytable;`
  - 查看
    - `SHOW INDEX FROM tableName;`
  - 使用alter命令 - 有四种方式来添加数据表的索引
    - `ALTER TABLE tbl_name ADD PRIMARY KEY (column_list);`：该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
    - `ALTER TABLE tbl name ADD UNIQUE index_name (column_list);`：这条语句创建索引的值必须是唯一的(除了NULL外，NULL可能会出现多次)。
    - `ALTER TABLE tbl_name ADD INDEX index_name (column_list);`：添加普通索引，索引值可出现多次。
    - `ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list);`：该语句指定了索引为FULLTEXT，用于全文索引。

## 17_索引结构与检索原理

MySQL索引结构

- BTree索引
- Hash索引
- full-text全文索引
- R-Tree索引

BTree索引检索原理

![img](https://img-blog.csdnimg.cn/img_convert/941c7edb511712f0b3de9a242917d723.png)

**初始化介绍**

一颗b+树，浅蓝色的块我们称之为一个磁盘块，可以看到每个磁盘块包含几个数据项（深蓝色所示）和指针（黄色所示),如磁盘块1包含数据项17和35，包含指针P1、P2、P3,
P1表示小于17的磁盘块，P2表示在17和35之间的磁盘块，P3表示大于35的磁盘块。

**真实的数据存在于叶子节点**即3、5、9、10、13、15、28、29、36、60、75、79、90、99。

**非叶子节点只不存储真实的数据，只存储指引搜索方向的数据项**，如17、35并不真实存在于数据表中。

**查找过程**

如果要查找数据项29，那么首先会把磁盘块1由磁盘加载到内存，此时发生一次IO。在内存中用二分查找确定29在17和35之间，锁定磁盘块1的P2指针，内存时间因为非常短（相比磁盘的IO）可以忽略不计，通过磁盘块1的P2指针的磁盘地址把磁盘块3由磁盘加载到内存，发生第二次IO，29在26和30之间，锁定磁盘块3的P2指针，通过指针加载磁盘块8到内存，发生第三次IO，同时内存中做二分查找找到29，结束查询，总计三次IO。

真实的情况是，3层的b+树可以表示上百万的数据，如果上百万的数据查找只需要三次IO，性能提高将是巨大的，如果没有索引，每个数据项都要发生一次IO，那么总共需要百万次的IO，显然成本非常非常高。

## 18_哪些情况适合建索引

1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段应该创建索引
3. 查询中与其它表关联的字段，外键关系建立索引
4. 频繁更新的字段不适合创建索引，因为每次更新不单单是更新了记录还会更新索引
5. Where条件里用不到的字段不创建索引
6. 单键/组合索引的选择问题，who?(在高并发下倾向创建组合索引)
7. 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
8. 查询中统计或者分组字段

## 19_哪些情况不适合建索引

1. 表记录太少
2. 经常增删改的表
3. 数据重复且分布平均的表字段，因此应该只为最经常查询和最经常排序的数据列建立索引。注意，如果某个数据列包含许多重复的内容，为它建立索引就没有太大的实际效果。

假如一个表有10万行记录，有一个字段A只有T和F两种值，且每个值的分布概率天约为50%，那么对这种表A字段建索引一般**不会提高数据库的查询速度**。

索引的选择性是指索引列中不同值的数目与表中记录数的比。如果一个表中有2000条记录，表索引列有1980个不同的值，那么这个索引的选择性就是1980/2000=0.99。一个索引的选择性越接近于1，这个索引的效率就越高。

## 20_性能分析前提知识

**MySQL Query Optimizer**

Mysql中有专门负责优化SELECT语句的优化器模块，主要功能:通过计算分析系统中收集到的统计信息，为客户端请求的Query提供他认为最优的执行计划（他认为最优的数据检索方式，但不见得是DBA认为是最优的,这部分最耗费时间）

当客户端向MySQL请求一条Query，命令解析器模块完成请求分类，区别出是SELECT并转发给MySQL Query Optimizer时，MySQL Query Optimizer首先会对整条Query进行优化，处理掉一些常量表达式的预算直接换算成常量值。并对Query中的查询条件进行简化和转换，如去掉一些无用或显而易见的条件、结构调整等。然后分析Query 中的 Hint信息(如果有），看显示Hint信息是否可以完全确定该Query的执行计划。如果没有Hint 或Hint信息还不足以完全确定执行计划，则会读取所涉及对象的统计信息，根据Query进行写相应的计算分析，然后再得出最后的执行计划。

**MySQL常见瓶颈**

- CPU：CPU在饱和的时候一般发生在数据装入内存或从磁盘上读取数据时候
- IO：磁盘I/O瓶颈发生在装入数据远大于内存容量的时候
- 服务器硬件的性能瓶颈：top，free，iostat和vmstat来查看系统的性能状态

## 21_explain使用简介

使用EXPLAIN关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈。

[官网地址](https://dev.mysql.com/doc/refman/8.0/en/execution-plan-information.html)

能干嘛

- 表的读取顺序
- 数据读取操作的操作类型
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询

怎么玩

- explain + sql语句
- 执行计划包含的信息
  - | id | select_type | table | partitions | type | possible_keys | key | key_len | ref | rows | filtered | Extra |

```sql
mysql> select * from tbl_dept;
+----+----------+--------+
| id | deptName | locAdd |
+----+----------+--------+
|  1 | RD       | 11     |
|  2 | HR       | 12     |
|  3 | MK       | 13     |
|  4 | MIS      | 14     |
|  5 | FD       | 15     |
+----+----------+--------+
5 rows in set (0.00 sec)

mysql> explain select * from tbl_dept;
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | tbl_dept | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |   100.00 | NULL  |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
12345678910111213141516171819
```

## 22_explain之id介绍

select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序

三种情况：

- id相同，执行顺序由上至下
- id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行
- id相同不同，同时存在

**id相同，执行顺序由上至下**

![img](https://img-blog.csdnimg.cn/img_convert/489a7f6eff3f1bd0b1b72ce7efd3860f.png)

**id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行**

![img](https://img-blog.csdnimg.cn/img_convert/1fbb87af7a3428f3ded4ce9ef927eabf.png)

**id相同不同，同时存在**

![img](https://img-blog.csdnimg.cn/img_convert/b3447be516e705f3adb4348fa1cc1229.png)

id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行，衍生=DERIVED

**小结**

id越大越先查询

## 23_explain之select_type和table介绍

select_type：查询的类型，主要是用于区别普通查询、联合查询、子查询等的复杂查询。

select_type有哪些？

1. SIMPLE - 简单的select查询,查询中不包含子查询或者UNION。
2. PRIMARY - 查询中若包含任何复杂的子部分，最外层查询则被标记为。
3. SUBQUERY - 在SELECT或WHERE列表中包含了子查询。
4. DERIUED - 在FROM列表中包含的子查询被标记为DERIVED（衍生）MySQL会递归执行这些子查询，把结果放在临时表里。
5. UNION - 若第二个SELECT出现在UNION之后，则被标记为UNION；若UNION包含在FROM子句的子查询中外层SELECT将被标记为：DERIVED。
6. UNION RESULT - 从UNION表获取结果的SELECT。

table：显示这一行的数据是关于哪张表的。

## 24_explain之type介绍

**访问类型排列**

type显示的是访问类型，是较为重要的一个指标，结果值从最好到最坏依次是：

system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index >ALL

system>const>eq_ref>ref>range>index>ALL

一般来说，得保证查询至少达到range级别，最好能达到ref。

**详细说明**

system：表只有一行记录（等于系统表），这是const类型的特列，平时不会出现，这个也可以忽略不计。

const：表示通过索引一次就找到了，const用于比较primary key或者unique索引。因为只匹配一行数据，所以很快如将主键置于where列表中，MySQL就能将该查询转换为一个常量。

![img](https://img-blog.csdnimg.cn/img_convert/13e25a155803062c26ec5669e74c5fa4.png)

eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描。选择本地图片

![img](https://img-blog.csdnimg.cn/img_convert/7cff8e85d84605216d677387460f10d7.png)

ref：非唯一性索引扫描，返回匹配某个单独值的所有行，本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体。

![img](https://img-blog.csdnimg.cn/img_convert/26633a476411b138886bb94b13fbfb6e.png)

range：只检索给定范围的行,使用一个索引来选择行。key列显示使用了哪个索引一般就是在你的where语句中出现了between、<、>、in等的查询。这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。

![img](https://img-blog.csdnimg.cn/img_convert/2f69477aa5f79e3092352644aba9c3b2.png)

index：Full Index Scan，index与ALL区别为index类型只遍历索引树。这通常比ALL快，因为索引文件通常比数据文件小（也就是说虽然all和Index都是读全表，但index是从索引中读取的，而all是从硬盘中读的）。

![img](https://img-blog.csdnimg.cn/img_convert/1f740bdbde95ce4ee3dbc86499b93f98.png)

**all**：Full Table Scan，将遍历全表以找到匹配的行。

![img](https://img-blog.csdnimg.cn/img_convert/e7a941c6ba4b1f674843a31f5e32c849.png)

备注：一般来说，得保证查询至少达到range级别，最好能达到ref。

## 25_explain之possible_keys和key介绍

**possible_keys**

显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段火若存在索引，则该索引将被列出，**但不一定被查询实际使用**。

**key**

实际使用的索引。如果为NULL，则没有使用索引

查询中若使用了覆盖索引，则该索引仅出现在key列表中

![img](https://img-blog.csdnimg.cn/img_convert/e7a941c6ba4b1f674843a31f5e32c849.png)

## 26_explain之key_len介绍

表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精确性的情况下，长度越短越好

key_len显示的值为索引字段的最大可能长度，**并非实际使用长度**，即key_len是根据表定义计算而得，不是通过表内检索出的

![img](https://img-blog.csdnimg.cn/img_convert/e7a941c6ba4b1f674843a31f5e32c849.png)

## 27_explain之ref介绍

显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值。

![img](https://img-blog.csdnimg.cn/img_convert/c3f5c1c1b1db3f4e2014d9b113466365.png)

由key_len可知t1表的idx_col1_col2被充分使用，col1匹配t2表的col1，col2匹配了一个常量，即 ‘ac’。

查询中与其它表关联的字段，外键关系建立索引。

## 28_explain之rows介绍

根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数。

![img](https://img-blog.csdnimg.cn/img_convert/1cc2534c7e7149b22fc67a45a84b5d3c.png)

## 29_explain之Extra介绍

包含不适合在其他列中显示但十分重要的额外信息。

**Using filesort**

说明mysql会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。MySQL中无法利用索引完成的排序操作称为"文件排序"

![img](https://img-blog.csdnimg.cn/img_convert/348a6753472b993e35ab8e016d6dbc8b.png)

**Using temporary**

使了用临时表保存中间结果，MysQL在对查询结果排序时使用临时表。常见于排序order by和分组查询group by。

![img](https://img-blog.csdnimg.cn/img_convert/b2ff96a5474c9ed9dc3866c0077fd50c.png)

**Using index**

表示相应的select操作中使用了覆盖索引（Covering Index），避免访问了表的数据行，效率不错！

如果同时出现using where，表明索引被用来执行索引键值的查找；

如果没有同时出现using where，表明索引用来读取数据而非执行查找动作。

![img](https://img-blog.csdnimg.cn/img_convert/b833dcdeadfd386eeb9347c5f23b1b9a.png)

如果同时出现using where，表明索引被用来执行索引键值的查找；

![img](https://img-blog.csdnimg.cn/img_convert/0d8667b3f990407f9f4ad2c8e4b4f9ea.png)

如果没有同时出现using where，表明索引用来读取数据而非执行查找动作。

**覆盖索引**（Covering Index）,一说为索引覆盖。

理解方式一：就是select的数据列只用从索引中就能够取得，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件,换句话说**查询列要被所建的索引覆盖**。

理解方式二：索引是高效找到行的一个方法，但是一般数据库也能使用索引找到一个列的数据，因此它不必读取整个行。毕竟索引叶子节点存储了它们索引的数据；当能通过读取索引就可以得到想要的数据，那就不需要读取行了。一个索引包含了（或覆盖了）满足查询结果的数据就叫做覆盖索引。

注意：

如果要使用覆盖索引，一定要注意select列表中只取出需要的列，不可select*，因为

如果将所有字段一起做索引会导致索引文件过大，查询性能下降。

> **What is a Covering Index？**
>
> A *covering index* is an index that contains all of, and possibly more, the columns you need for your query.
>
> For instance, this:
>
> ```
> SELECT *
> FROM tablename
> WHERE criteria
> 123
> ```
>
> will typically use indexes to speed up the resolution of which rows to retrieve using *criteria*, but then it will go to the full table to retrieve the rows.
>
> However, if the index contained the columns *column1, column2* and *column3*, then this sql:
>
> ```
> SELECT column1, column2
> FROM tablename
> WHERE criteria
> 123
> ```
>
> and, provided that particular index could be used to speed up the resolution of which rows to retrieve, the index already contains the values of the columns you’re interested in, so it won’t have to go to the table to retrieve the rows, but can produce the results directly from the index.
>
> This can also be used if you see that a typical query uses 1-2 columns to resolve which rows, and then typically adds another 1-2 columns, it could be beneficial to append those extra columns (if they’re the same all over) to the index, so that the query processor can get everything from the index itself.
>
> [link](https://stackoverflow.com/questions/62137/what-is-a-covered-index)

**Using where**

表明使用了where过滤。

**Using join buffer**

使用了连接缓存。

**impossible where**

where子句的值总是false，不能用来获取任何元组。

![img](https://img-blog.csdnimg.cn/img_convert/e879ce43d5bbbed4e712034201dae403.png)

**select tables optimized away**

在没有GROUPBY子句的情况下，基于索引优化MIN/MAX操作，或者对于MyISAM存储引擎优化COUNT(*)操作，不必等到执行阶段再进行计算，查询执行计划生成的阶段即完成优化。

**distinct**

优化distinct操作，在找到第一匹配的元组后即停止找同样值的动作。

## 30_explain之热身Case

![img](https://img-blog.csdnimg.cn/img_convert/bdfcbbf0a47e1bb6cd3943c687d76b1a.png)

第一行（执行顺序4）：id列为1，表示是union里的第一个select，select_type列的primary表示该查询为外层查询，table列被标记为，表示查询结果来自一个衍生表，其中derived3中3代表该查询衍生自第三个select查询，即id为3的select。【select d1.name… 】

第二行（执行顺序2）：id为3，是整个查询中第三个select的一部分。因查询包含在from中，所以为derived。【select id,namefrom t1 where other_column=’’】

第三行（执行顺序3）：select列表中的子查询select_type为subquery，为整个查询中的第二个select。【select id from t3】

第四行（执行顺序1）：select_type为union，说明第四个select是union里的第二个select，最先执行【select name,id from t2】

第五行（执行顺序5）：代表从union的临时表中读取行的阶段，table列的<union1,4>表示用第一个和第四个select的结果进行union操作。【两个结果union操作】

## 31_索引单表优化案例

建表SQL

```sql
CREATE TABLE IF NOT EXISTS article(
	id INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
	author_id INT(10) UNSIGNED NOT NULL,
	category_id INT(10) UNSIGNED NOT NULL,
	views INT(10) UNSIGNED NOT NULL,
	comments INT(10) UNSIGNED NOT NULL,
	title VARCHAR(255) NOT NULL,
	content TEXT NOT NULL
);

INSERT INTO article(author_id,category_id,views,comments,title,content)
VALUES
(1,1,1,1,'1','1'),
(2,2,2,2,'2','2'),
(1,1,3,3,'3','3');

12345678910111213141516
mysql> select * from article;
+----+-----------+-------------+-------+----------+-------+---------+
| id | author_id | category_id | views | comments | title | content |
+----+-----------+-------------+-------+----------+-------+---------+
|  1 |         1 |           1 |     1 |        1 | 1     | 1       |
|  2 |         2 |           2 |     2 |        2 | 2     | 2       |
|  3 |         1 |           1 |     3 |        3 | 3     | 3       |
+----+-----------+-------------+-------+----------+-------+---------+
3 rows in set (0.00 sec)
123456789
```

**案例**

查询category_id为1且comments 大于1的情况下，views最多的article_id。

```sql
mysql> SELECT id, author_id FROM article WHERE category_id = 1 AND comments > 1 ORDER BY views DESC LIMIT 1;
+----+-----------+
| id | author_id |
+----+-----------+
|  3 |         1 |
+----+-----------+
1 row in set (0.00 sec)
1234567
mysql> show index from article;
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table   | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| article |          0 | PRIMARY  |            1 | id          | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
1 row in set (0.00 sec)
1234567
mysql> explain SELECT id, author_id FROM article WHERE category_id = 1 AND comments > 1 ORDER BY views DESC LIMIT 1;
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-----------------------------+
| id | select_type | table   | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                       |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-----------------------------+
|  1 | SIMPLE      | article | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where; Using filesort |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-----------------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

结论：很显然，type是ALL，即最坏的情况。Extra里还出现了Using filesort，也是最坏的情况。优化是必须的。

**开始优化**

新建索引+删除索引

```sql
mysql> create index idx_article_ccv on article(category_id,comments,views);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show index from article;
+---------+------------+-----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table   | Non_unique | Key_name        | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+---------+------------+-----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| article |          0 | PRIMARY         |            1 | id          | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
| article |          1 | idx_article_ccv |            1 | category_id | A         |           2 |     NULL | NULL   |      | BTREE      |         |               |
| article |          1 | idx_article_ccv |            2 | comments    | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
| article |          1 | idx_article_ccv |            3 | views       | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
+---------+------------+-----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
4 rows in set (0.00 sec)
1234567891011121314
```

或者用下面sql创建索引：

```sql
ALTER TABLE 'article' ADD INDEX idx_article_ccv ( 'category_id , 'comments', 'views' );
1
```

创建后的效果

```sql
mysql> explain SELECT id, author_id FROM article WHERE category_id = 1 AND comments > 1 ORDER BY views DESC LIMIT 1;
+----+-------------+---------+------------+-------+-----------------+-----------------+---------+------+------+----------+---------------------------------------+
| id | select_type | table   | partitions | type  | possible_keys   | key             | key_len | ref  | rows | filtered | Extra                                 |
+----+-------------+---------+------------+-------+-----------------+-----------------+---------+------+------+----------+---------------------------------------+
|  1 | SIMPLE      | article | NULL       | range | idx_article_ccv | idx_article_ccv | 8       | NULL |    1 |   100.00 | Using index condition; Using filesort |
+----+-------------+---------+------------+-------+-----------------+-----------------+---------+------+------+----------+---------------------------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

Extra里还是出现了Using filesort，创建这索引作用不大。

如果`comments > 1`换成`comments = 1`，可以让Using filesort消失，但不符题目要求。

```sql
mysql> explain SELECT id, author_id FROM article WHERE category_id = 1 AND comments = 1 ORDER BY views DESC LIMIT 1;
+----+-------------+---------+------------+------+-----------------+-----------------+---------+-------------+------+----------+-------------+
| id | select_type | table   | partitions | type | possible_keys   | key             | key_len | ref         | rows | filtered | Extra       |
+----+-------------+---------+------------+------+-----------------+-----------------+---------+-------------+------+----------+-------------+
|  1 | SIMPLE      | article | NULL       | ref  | idx_article_ccv | idx_article_ccv | 8       | const,const |    1 |   100.00 | Using where |
+----+-------------+---------+------------+------+-----------------+-----------------+---------+-------------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

既然创建这索引作用不大，删了它吧。

```sql
mysql> DROP INDEX idx_article_ccv ON article;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
123
```

**缘由**

type变成了range，这是可以忍受的。但是extra里使用Using filesort仍是无法接受的。

但是我们已经建立了索引，为啥没用呢？

这是因为按照BTree索引的工作原理，先排序category_id，如果遇到相同的category_id则再排序comments,如果遇到相同的comments 则再排序views。

当comments字段在联合索引里处于中间位置时，因comments > 1条件是一个范围值(所谓range)，MySQL无法利用索引再对后面的views部分进行检索，即range类型查询字段后面的索引无效。

**改进**

跟上次创建索引相比，这次不为comments字段创建索引。

```sql
mysql> create index idx_article_cv on article(category_id, views);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
123
```

再次explain

```sql
mysql> explain SELECT id, author_id FROM article WHERE category_id = 1 AND comments > 1 ORDER BY views DESC LIMIT 1;
+----+-------------+---------+------------+------+----------------+----------------+---------+-------+------+----------+-------------+
| id | select_type | table   | partitions | type | possible_keys  | key            | key_len | ref   | rows | filtered | Extra       |
+----+-------------+---------+------------+------+----------------+----------------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | article | NULL       | ref  | idx_article_cv | idx_article_cv | 4       | const |    2 |    33.33 | Using where |
+----+-------------+---------+------------+------+----------------+----------------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

可以看到，type变为了ref，Extra中的Using filesort也消失了，结果非常理想。

## 32_索引两表优化案例

新建SQL

```sql
CREATE TABLE IF NOT EXISTS class(
	id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
	card INT(10) UNSIGNED NOT NULL,
	PRIMARY KEY(id)
);

CREATE TABLE IF NOT EXISTS book(
	bookid INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
	card INT(10) UNSIGNED NOT NULL,
	PRIMARY KEY(bookid)
);

INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO class(card) VALUES(FLOOR(1+(RAND()*20)));

INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO book(card) VALUES(FLOOR(1+(RAND()*20)));

12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455
```

创建后的结果：

```sql
mysql> select * from class;
+----+------+
| id | card |
+----+------+
|  1 |   17 |
|  2 |    2 |
|  3 |   18 |
|  4 |    4 |
|  5 |    4 |
|  6 |    8 |
|  7 |    9 |
|  8 |    1 |
|  9 |   18 |
| 10 |    6 |
| 11 |   15 |
| 12 |   15 |
| 13 |   12 |
| 14 |   15 |
| 15 |   18 |
| 16 |    2 |
| 17 |   18 |
| 18 |    5 |
| 19 |    7 |
| 20 |    1 |
| 21 |    2 |
+----+------+
21 rows in set (0.00 sec)

mysql> select * from book;
+--------+------+
| bookid | card |
+--------+------+
|      1 |    8 |
|      2 |   14 |
|      3 |    3 |
|      4 |   16 |
|      5 |    8 |
|      6 |   12 |
|      7 |   17 |
|      8 |    8 |
|      9 |   10 |
|     10 |    3 |
|     11 |    4 |
|     12 |   12 |
|     13 |    9 |
|     14 |    7 |
|     15 |    6 |
|     16 |    8 |
|     17 |    3 |
|     18 |   11 |
|     19 |    5 |
|     20 |   11 |
+--------+------+
20 rows in set (0.00 sec)

mysql>
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556
```

开始explain分析

```sql
mysql> EXPLAIN SELECT * FROM class LEFT JOIN book ON class.card = book.card;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                              |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
|  1 | SIMPLE      | class | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   21 |   100.00 | NULL                                               |
|  1 | SIMPLE      | book  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   20 |   100.00 | Using where; Using join buffer (Block Nested Loop) |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+----------------------------------------------------+
2 rows in set, 1 warning (0.00 sec)
12345678
```

type都是all，需要优化。

为book.card创建索引

```sql
mysql> ALTER TABLE `book` ADD INDEX Y(`card`);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
123
```

再次explain分析

```sql
mysql> EXPLAIN SELECT * FROM class LEFT JOIN book ON class.card = book.card;
+----+-------------+-------+------------+------+---------------+------+---------+---------------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref           | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+---------------+------+----------+-------------+
|  1 | SIMPLE      | class | NULL       | ALL  | NULL          | NULL | NULL    | NULL          |   21 |   100.00 | NULL        |
|  1 | SIMPLE      | book  | NULL       | ref  | Y             | Y    | 4       | my.class.card |    1 |   100.00 | Using index |
+----+-------------+-------+------------+------+---------------+------+---------+---------------+------+----------+-------------+
2 rows in set, 1 warning (0.00 sec)
12345678
```

可以看到第二行的type变为了ref，rows也变少了，优化比较明显。这是由左连接特性决定的。LEFT JOIN条件用于确定如何从右表搜索行，左边一定都有，所以右边是我们的关键点，一定需要在右表建立索引。

删除为book.card创建索引

```sql
mysql> drop index y on book;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
123
```

为class.card创建索引

```sql
mysql> ALTER TABLE `class` ADD INDEX Y(`card`);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
123
```

再次explain分析

```sql
mysql> EXPLAIN SELECT * FROM class LEFT JOIN book ON class.card = book.card;
+----+-------------+-------+------------+-------+---------------+------+---------+------+------+----------+----------------------------------------------------+
| id | select_type | table | partitions | type  | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                              |
+----+-------------+-------+------------+-------+---------------+------+---------+------+------+----------+----------------------------------------------------+
|  1 | SIMPLE      | class | NULL       | index | NULL          | Y    | 4       | NULL |   21 |   100.00 | Using index                                        |
|  1 | SIMPLE      | book  | NULL       | ALL   | NULL          | NULL | NULL    | NULL |   20 |   100.00 | Using where; Using join buffer (Block Nested Loop) |
+----+-------------+-------+------------+-------+---------------+------+---------+------+------+----------+----------------------------------------------------+
2 rows in set, 1 warning (0.00 sec)
12345678
```

可见右边是我们的关键点，要想优化需要在右表建立索引。

然后我们换用右连接RIGHT JOIN查询

```sql
mysql> EXPLAIN SELECT * FROM class right JOIN book ON class.card = book.card;
+----+-------------+-------+------------+------+---------------+------+---------+--------------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref          | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+--------------+------+----------+-------------+
|  1 | SIMPLE      | book  | NULL       | ALL  | NULL          | NULL | NULL    | NULL         |   20 |   100.00 | NULL        |
|  1 | SIMPLE      | class | NULL       | ref  | Y             | Y    | 4       | my.book.card |    1 |   100.00 | Using index |
+----+-------------+-------+------------+------+---------------+------+---------+--------------+------+----------+-------------+
2 rows in set, 1 warning (0.00 sec)
12345678
```

换成左边是我们的关键点，要想优化需要在左表建立索引。

**小结**

索引两表优化，左连接右表建索引，右连接左表建索引。

## 33_索引三表优化案例

新建SQL

```sql
CREATE TABLE IF NOT EXISTS phone(
	phoneid INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
	card INT(10) UNSIGNED NOT NULL,
	PRIMARY KEY(phoneid)
)ENGINE=INNODB;

INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
INSERT INTO phone(card) VALUES(FLOOR(1+(RAND()*20)));
1234567891011121314151617181920212223242526
```

建后效果

```sql
mysql> select * from phone;
+---------+------+
| phoneid | card |
+---------+------+
|       1 |   10 |
|       2 |   13 |
|       3 |   17 |
|       4 |    5 |
|       5 |   12 |
|       6 |    7 |
|       7 |   15 |
|       8 |   17 |
|       9 |   17 |
|      10 |   14 |
|      11 |   19 |
|      12 |   13 |
|      13 |    5 |
|      14 |    8 |
|      15 |    2 |
|      16 |    8 |
|      17 |   11 |
|      18 |   14 |
|      19 |   13 |
|      20 |    5 |
+---------+------+
20 rows in set (0.00 sec)
1234567891011121314151617181920212223242526
```

复用到上一节book，class两表，移除它们原有的索引。

```sql
mysql> show index from class;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| class |          0 | PRIMARY  |            1 | id          | A         |          21 |     NULL | NULL   |      | BTREE      |         |               |
| class |          1 | Y        |            1 | card        | A         |          12 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.01 sec)

mysql> drop index y on class;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show index from book;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| book  |          0 | PRIMARY  |            1 | bookid      | A         |          20 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
1 row in set (0.01 sec)

123456789101112131415161718192021
```

为phone.card和book.card创建新的索引。

```sql
mysql> alter table `phone` add index z(`card`);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> alter table `book` add index y(`card`);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
1234567
```

explain三表连接

```sql
mysql> explain SELECT * FROM class LEFT JOIN book ON class.card = book.card LEFT JOIN phone ON book.card = phone.card;
+----+-------------+-------+------------+------+---------------+------+---------+---------------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref           | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+---------------+------+----------+-------------+
|  1 | SIMPLE      | class | NULL       | ALL  | NULL          | NULL | NULL    | NULL          |   21 |   100.00 | NULL        |
|  1 | SIMPLE      | book  | NULL       | ref  | y             | y    | 4       | my.class.card |    1 |   100.00 | Using index |
|  1 | SIMPLE      | phone | NULL       | ref  | z             | z    | 4       | my.book.card  |    1 |   100.00 | Using index |
+----+-------------+-------+------------+------+---------------+------+---------+---------------+------+----------+-------------+
3 rows in set, 1 warning (0.00 sec)
123456789
```

后2行的 type 都是ref且总 rows优化很好，效果不错。因此索引最好设置在需要经常查询的字段中。

**结论**

Join语句的优化

尽可能减少Join语句中的NestedLoop的循环总次数：“**永远用小结果集驱动大的结果集**”。

优先优化NestedLoop的内层循环，保证Join语句中被驱动表上Join条件字段已经被索引。

当无法保证被驱动表的Join条件字段被索引且内存资源充足的前提下，不要太吝惜JoinBuffer的设置。

## 34_索引失效1-跳过复合索引中间列

索引失效（应该避免） [参考：谁还没碰过索引失效呢？](https://mp.weixin.qq.com/s/lEx6iRRP3MbwJ82Xwp675w)

1. 最佳左前缀法则 - 如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且**不跳过复合索引中间列**。
2. 不在索引列上做任何操作（计算、函数、（自动or手动）类型转换），会导致索引失效而转向全表扫描。
3. 存储引擎不能使用索引中范围条件右边的列。
4. 尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少select *。
5. mysql在使用不等于（!=或者<>）的时候无法使用索引会导致全表扫描。
6. is null, is not null 也无法使用索引。
7. like以通配符开头（’%abc…’），mysql索引失效会变成全表扫描的操作。
8. 字符串不加单引号索引失效。
9. 少用or，用它来连接时会索引失效。

全值匹配我最爱

新建SQL

```sql
CREATE TABLE staffs(
	id INT PRIMARY KEY AUTO_INCREMENT,
	`name` VARCHAR(24) NOT NULL DEFAULT'' COMMENT'姓名',
	`age` INT NOT NULL DEFAULT 0 COMMENT'年龄',
	`pos` VARCHAR(20) NOT NULL DEFAULT'' COMMENT'职位',
	`add_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT'入职时间'
)CHARSET utf8 COMMENT'员工记录表';

INSERT INTO staffs(`name`,`age`,`pos`,`add_time`) VALUES('z3',22,'manager',NOW());
INSERT INTO staffs(`name`,`age`,`pos`,`add_time`) VALUES('July',23,'dev',NOW());
INSERT INTO staffs(`name`,`age`,`pos`,`add_time`) VALUES('2000',23,'dev',NOW());

ALTER TABLE staffs ADD INDEX index_staffs_nameAgePos(`name`,`age`,`pos`);

1234567891011121314
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND age=25;
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref         | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 78      | const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND age=25 AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref               | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 140     | const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
1234567
```

注意下面的explain

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE age=25 AND pos='dev';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> EXPLAIN SELECT * FROM staffs WHERE pos='dev';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

上面两个索引失效了

**最佳左前缀法则** - 如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且**不跳过索引中的列**。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |    33.33 | Using index condition |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

## 35_索引失效2-索引列上做额外操作

不在索引列上做任何操作（计算、函数、（自动or手动）类型转换），会导致索引失效而转向全表扫描。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE left(NAME,4)='July';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |   100.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

## 36_索引失效3-限定复合索引某列的范围

存储引擎不能使用索引中**范围条件**右边的列（我理解为限定复合索引某字段的范围会时索引失效，也就是>，<，between…and…谨慎用在复合索引某字段）。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND age=25;
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref         | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 78      | const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND age=25 AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref               | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 140     | const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND age>25 AND pos='dev';
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys           | key                     | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | range | index_staffs_nameAgePos | index_staffs_nameAgePos | 78      | NULL |    1 |    33.33 | Using index condition |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

由`age=25`变成`age>25`后，type从ref变成range。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |    33.33 | Using index condition |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND pos='dev' and age > 25;
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys           | key                     | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | range | index_staffs_nameAgePos | index_staffs_nameAgePos | 78      | NULL |    1 |    33.33 | Using index condition |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

## 37_索引失效4-select *

尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少select *

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July' AND age=25 AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref               | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 140     | const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT name,age,pos FROM staffs WHERE NAME='July' AND age=25 AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref               | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 140     | const,const,const |    1 |   100.00 | Using index |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

我们最好按需索取，少用select *

```sql
mysql> EXPLAIN SELECT name,age,pos FROM staffs WHERE NAME='July' AND pos='dev';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+--------------------------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra                    |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+--------------------------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |    33.33 | Using where; Using index |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT name,age,pos FROM staffs WHERE NAME='July' AND age>25 AND pos='dev';
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys           | key                     | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | staffs | NULL       | range | index_staffs_nameAgePos | index_staffs_nameAgePos | 78      | NULL |    1 |    33.33 | Using where; Using index |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT name FROM staffs WHERE NAME='July' AND age=25;
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref         | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 78      | const,const |    1 |   100.00 | Using index |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
```

## 38_索引失效5-!=或者<>

mysql在使用不等于（!=或者<>）的时候无法使用索引会导致全表扫描。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME!='July';
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | index_staffs_nameAgePos | NULL | NULL    | NULL |    3 |    66.67 | Using where |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME<>'July';
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | index_staffs_nameAgePos | NULL | NULL    | NULL |    3 |    66.67 | Using where |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
```

## 39_索引失效6-is null或者is not null

is null, is not null 也无法使用索引

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME is null;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra            |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
|  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | Impossible WHERE |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME is not null;
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | index_staffs_nameAgePos | NULL | NULL    | NULL |    3 |    66.67 | Using where |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

12345678910111213141516
```

Extra打印为Impossible WHERE，是因为我们在创建staffs表，设置name字段的属性为not null。

下面额外演示Extra为Impossible WHERE情况。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE 1=1;
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |   100.00 | NULL  |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE 1!=1;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra            |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
|  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | Impossible WHERE |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

## 40_索引失效7-like以通配符%开头字符串

like以通配符%开头（’%abc…’），mysql索引失效会变成全表扫描的操作。

```sql
mysql> EXPLAIN SELECT * FROM staffs WHERE NAME='July';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME like '%July%';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME like '%July';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM staffs WHERE NAME like 'July%';
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys           | key                     | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | staffs | NULL       | range | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | NULL |    1 |   100.00 | Using index condition |
+----+-------------+--------+------------+-------+-------------------------+-------------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
12345678910111213141516171819202122232425262728293031
```

**问题：解决like '%字符串%'时索引不被使用的方法？**

新建SQL

```sql
CREATE TABLE `tbl_user`(
	`id` INT(11) NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(20) DEFAULT NULL,
	`age`INT(11) DEFAULT NULL,
	`email` VARCHAR(20) DEFAULT NULL,
	PRIMARY KEY(`id`)
)ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

INSERT INTO tbl_user(`name`,`age`,`email`)VALUES('1aa1',21,'a@163.com');
INSERT INTO tbl_user(`name`,`age`,`email`)VALUES('2bb2',23,'b@163.com');
INSERT INTO tbl_user(`name`,`age`,`email`)VALUES('3cc3',24,'c@163.com');
INSERT INTO tbl_user(`name`,`age`,`email`)VALUES('4dd4',26,'d@163.com');
123456789101112
mysql> select * from tbl_user;
+----+------+------+-----------+
| id | name | age  | email     |
+----+------+------+-----------+
|  1 | 1aa1 |   21 | a@163.com |
|  2 | 2bb2 |   23 | b@163.com |
|  3 | 3cc3 |   24 | c@163.com |
|  4 | 4dd4 |   26 | d@163.com |
+----+------+------+-----------+
4 rows in set (0.00 sec)
12345678910
```

------

创建索引前，先看看以下explain：

```sql
EXPLAIN SELECT NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';

EXPLAIN SELECT id FROM tbl_user WHERE NAME LIKE '%aa%';
EXPLAIN SELECT NAME FROM tbl_user WHERE NAME LIKE '%aa%';
EXPLAIN SELECT age FROM tbl_user WHERE NAME LIKE '%aa%';

EXPLAIN SELECT id,NAME FROM tbl_user WHERE NAME LIKE '%aa%';
EXPLAIN SELECT id,NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
EXPLAIN SELECT NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';

EXPLAIN SELECT * FROM tbl_user WHERE NAME LIKE '%aa%';
EXPLAIN SELECT id,NAME,age,email FROM tbl_user WHERE NAME LIKE '%aa%';
123456789101112
mysql> EXPLAIN SELECT NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> EXPLAIN SELECT id FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT NAME FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
mysql> EXPLAIN SELECT id,NAME FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT id,NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
mysql> EXPLAIN SELECT * FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT id,NAME,age,email FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

没有创建索引，都全表查找。

现在创建索引

```sql
mysql> CREATE INDEX idx_user_nameAge ON tbl_user(NAME,age);
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
123
```

再执行上述一系列explain

```sql
mysql> EXPLAIN SELECT NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

看得出，用上索引（覆盖索引）

```sql
mysql> EXPLAIN SELECT id FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT NAME FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
```

看得出，都用上索引（覆盖索引）

```sql
mysql> EXPLAIN SELECT id,NAME FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT id,NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT NAME,age FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type  | possible_keys | key              | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tbl_user | NULL       | index | NULL          | idx_user_nameAge | 68      | NULL |    4 |    25.00 | Using where; Using index |
+----+-------------+----------+------------+-------+---------------+------------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223
```

看得出，都用上索引（覆盖索引）

```sql
mysql> EXPLAIN SELECT * FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT id,NAME,age,email FROM tbl_user WHERE NAME LIKE '%aa%';
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | tbl_user | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |    25.00 | Using where |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

看得出，都没用上索引了，有email字段再，只能全表搜索。

**覆盖索引**（Covering Index）,一说为索引覆盖。

理解方式一：就是select的数据列只用从索引中就能够取得，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，换句话说**查询列要被所建的索引覆盖**。

理解方式二：索引是高效找到行的一个方法，但是一般数据库也能使用索引找到一个列的数据，因此它不必读取整个行。毕竟索引叶子节点存储了它们索引的数据；当能通过读取索引就可以得到想要的数据，那就不需要读取行了。一个索引包含了（或覆盖了）满足查询结果的数据就叫做覆盖索引。

注意：

如果要使用覆盖索引，一定要注意select列表中只取出需要的列，不可select*，因为

如果将所有字段一起做索引会导致索引文件过大，查询性能下降。

**小结**

解决like '%字符串%'时索引不被使用的方法？复合索引，然后覆盖索引。

## 41_索引失效8-数目字符串不加单引号

**数目字符串不加单引号索引失效**。

```sql
mysql> SELECT * FROM staffs WHERE NAME=2000;
+----+------+-----+-----+---------------------+
| id | name | age | pos | add_time            |
+----+------+-----+-----+---------------------+
|  3 | 2000 |  23 | dev | 2021-04-03 14:03:18 |
+----+------+-----+-----+---------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT * FROM staffs WHERE NAME='2000';
+----+------+-----+-----+---------------------+
| id | name | age | pos | add_time            |
+----+------+-----+-----+---------------------+
|  3 | 2000 |  23 | dev | 2021-04-03 14:03:18 |
+----+------+-----+-----+---------------------+
1 row in set (0.00 sec)

mysql> explain SELECT * FROM staffs WHERE NAME=2000;
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | index_staffs_nameAgePos | NULL | NULL    | NULL |    3 |    33.33 | Using where |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
1 row in set, 3 warnings (0.00 sec)

mysql> explain SELECT * FROM staffs WHERE NAME='2000';
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys           | key                     | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | staffs | NULL       | ref  | index_staffs_nameAgePos | index_staffs_nameAgePos | 74      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+-------------------------+-------------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
12345678910111213141516171819202122232425262728293031
```

## 42_索引失效9-用关键字OR

**少用or，用它来连接时会索引失效**。

```java
mysql> explain SELECT * FROM staffs WHERE NAME='July' or name='z3';
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys           | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | staffs | NULL       | ALL  | index_staffs_nameAgePos | NULL | NULL    | NULL |    3 |    66.67 | Using where |
+----+-------------+--------+------------+------+-------------------------+------+---------+------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT * FROM staffs WHERE NAME='July' or name='z3';
+----+------+-----+---------+---------------------+
| id | name | age | pos     | add_time            |
+----+------+-----+---------+---------------------+
|  1 | z3   |  22 | manager | 2021-04-03 14:03:18 |
|  2 | July |  23 | dev     | 2021-04-03 14:03:18 |
+----+------+-----+---------+---------------------+
2 rows in set (0.00 sec)
12345678910111213141516
```

## 43_索引失效10-小总结

**小总结**

假设index(a, b, c)

| where语句                                               | 索引是否被使用                       |
| ------------------------------------------------------- | ------------------------------------ |
| where a = 3                                             | Y，使用到a                           |
| where a = 3 and b = 5                                   | Y，使用到a，b                        |
| where a = 3 and b = 5 and c = 4                         | Y，使用到a，b，c                     |
| where b = 3 或者 where b = 3 and c = 4 或者 where c = 4 | N                                    |
| where a = 3 and c = 5                                   | 使用到a，但是c不可以，b中间断了      |
| where a = 3 and b > 4 and c = 5                         | 使用到a和b，c不能用在范围之后，b断了 |
| where a = 3 and b like ‘kk%’ and c = 4                  | Y，使用到a，b，c                     |

## 44_索引面试题分析

新建SQL

```sql
create table test03(
    id int primary key not null auto_increment,
    c1 char(10),
    c2 char(10),
    c3 char(10),
    c4 char(10),
    c5 char(10)
);

insert into test03(c1,c2,c3,c4,c5) values ('a1','a2','a3','a4','a5');
insert into test03(c1,c2,c3,c4,c5) values ('b1','b2','b3','b4','b5');
insert into test03(c1,c2,c3,c4,c5) values ('c1','c2','c3','c4','c5');
insert into test03(c1,c2,c3,c4,c5) values ('d1','d2','d3','d4','d5');
insert into test03(c1,c2,c3,c4,c5) values ('e1','e2','e3','e4','e5');

create index idx_test03_c1234 on test03(c1,c2,c3,c4);
12345678910111213141516
mysql> select * from test03;
+----+------+------+------+------+------+
| id | c1   | c2   | c3   | c4   | c5   |
+----+------+------+------+------+------+
|  1 | a1   | a2   | a3   | a4   | a5   |
|  2 | b1   | b2   | b3   | b4   | b5   |
|  3 | c1   | c2   | c3   | c4   | c5   |
|  4 | d1   | d2   | d3   | d4   | d5   |
|  5 | e1   | e2   | e3   | e4   | e5   |
+----+------+------+------+------+------+
5 rows in set (0.00 sec)

mysql> show index from test03;
+--------+------------+------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table  | Non_unique | Key_name         | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+--------+------------+------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| test03 |          0 | PRIMARY          |            1 | id          | A         |           2 |     NULL | NULL   |      | BTREE      |         |               |
| test03 |          1 | idx_test03_c1234 |            1 | c1          | A         |           5 |     NULL | NULL   | YES  | BTREE      |         |               |
| test03 |          1 | idx_test03_c1234 |            2 | c2          | A         |           5 |     NULL | NULL   | YES  | BTREE      |         |               |
| test03 |          1 | idx_test03_c1234 |            3 | c3          | A         |           5 |     NULL | NULL   | YES  | BTREE      |         |               |
| test03 |          1 | idx_test03_c1234 |            4 | c4          | A         |           5 |     NULL | NULL   | YES  | BTREE      |         |               |
+--------+------------+------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
5 rows in set (0.00 sec)
1234567891011121314151617181920212223
```

问题：我们创建了复合索引idx_test03_c1234 ，根据以下SQL分析下索引使用情况？

### 1

```sql
explain select * from test03 where c1='a1';
explain select * from test03 where c1='a1' and c2='a2';
explain select * from test03 where c1='a1' and c2='a2' and c3='a3';
explain select * from test03 where c1='a1' and c2='a2' and c3='a3' and c4='a4';
1234
mysql> explain select * from test03 where c1='a1';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c2='a2';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c2='a2' and c3='a3';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref               | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------+------+----------+-------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 123     | const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c2='a2' and c3='a3' and c4='a4';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref                     | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 164     | const,const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
12345678910111213141516171819202122232425262728293031
```

都用上索引了。

### 2

换一下条件顺序

```sql
explain select * from test03 where c1='a1' and c2='a2' and c3='a3' and c4='a4';
explain select * from test03 where c1='a1' and c2='a2' and c4='a4' and c3='a3';
12
mysql> explain select * from test03 where c1='a1' and c2='a2' and c3='a3' and c4='a4';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref                     | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 164     | const,const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c2='a2' and c4='a4' and c3='a3';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref                     | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 164     | const,const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------------------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

都用上索引了。

### 3

限定范围

```sql
explain select * from test03 where c1='a1' and c2='a2' and c3>'a3' and c4='a4';
explain select * from test03 where c1='a1' and c2='a2' and c4>'a4' and c3='a3';
12
mysql> explain select * from test03 where c1='a1' and c2='a2' and c3>'a3' and c4='a4';
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys    | key              | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | range | idx_test03_c1234 | idx_test03_c1234 | 123     | NULL |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c2='a2' and c4>'a4' and c3='a3';
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys    | key              | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | range | idx_test03_c1234 | idx_test03_c1234 | 164     | NULL |    1 |   100.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

### 4

```sql
mysql> explain select * from test03 where c1='a1' and c2='a2' and c4='a4' order by c3;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

**c3作用在排序而不是查找**

```sql
mysql> explain select * from test03 where c1='a1' and c2='a2' order by c3;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |   100.00 | Using index condition |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

上面两个explain的相同。

order by c3换成order by c4

```sql
mysql> explain select * from test03 where c1='a1' and c2='a2' order by c4;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+---------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra                                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+---------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |   100.00 | Using index condition; Using filesort |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+---------------------------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

用到Using filesort。

### 5

```sql
mysql> explain select * from test03 where c1='a1' and c5='a5' order by c2,c3;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                              |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition; Using where |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+------------------------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

只用c1一个字段索引，但是c2、c3用于排序，无filesort。

将order by c2,c3换成order by c3,c2。

```sql
mysql> explain select * from test03 where c1='a1' and c5='a5' order by c3,c2;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+----------------------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                                              |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+----------------------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition; Using where; Using filesort |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+----------------------------------------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

出现了filesort，我们建的索引是1234，它没有按照顺序来，3，2颠倒了。

### 6

```sql
mysql> explain select * from test03 where c1='a1' and c2='a2' order by c2,c3;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |   100.00 | Using index condition |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c2='a2' and c5='a5' order by c2,c3;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra                              |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |    20.00 | Using index condition; Using where |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+------------------------------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

用c1、c2两个字段索引，但是c2、c3用于排序，无filesort

### 7

```sql
mysql> explain select * from test03 where c1='a1' and c2='a2' and c5='a5' order by c3,c2;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref         | rows | filtered | Extra                              |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 82      | const,const |    1 |    20.00 | Using index condition; Using where |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------------+------+----------+------------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c5='a5' order by c3,c2;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+----------------------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                                              |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+----------------------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition; Using where; Using filesort |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+----------------------------------------------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

跳过c2，就用c3，就出翔Using filesort。

### 8

```sql
mysql> explain select * from test03 where c1='a1' and c4='a4' group by c2,c3;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> explain select * from test03 where c1='a1' and c4='a4' group by c3,c2;
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+--------------------------------------------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                                                  |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+--------------------------------------------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition; Using temporary; Using filesort |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+--------------------------------------------------------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415
```

定值、范围还是排序，一般order by是给个范围

group by基本上都需要进行排序，会有临时表产生

### 一般性建议

- 对于单键索引，尽量选择针对当前query过滤性更好的索引。
- 在选择组合索引的时候，当前Query中过滤性最好的字段在索引字段顺序中，位置越靠前越好。
- 在选择组合索引的时候，尽量选择可以能够包含当前query中的where字句中更多字段的索引。
- 尽可能通过分析统计信息和调整query的写法来达到选择合适索引的目的。

## 45_索引优化答疑补充和总结口诀

继续上一章节

```sql
mysql> explain select * from test03 where c1='a1' and c2 like 'kk%' and c3='a3';
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys    | key              | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | range | idx_test03_c1234 | idx_test03_c1234 | 123     | NULL |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> explain select * from test03 where c1='a1' and c2 like '%kk' and c3='a3';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> explain select * from test03 where c1='a1' and c2 like '%kk%' and c3='a3';
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
| id | select_type | table  | partitions | type | possible_keys    | key              | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | ref  | idx_test03_c1234 | idx_test03_c1234 | 41      | const |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+------+------------------+------------------+---------+-------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
mysql> explain select * from test03 where c1='a1' and c2 like 'k%kk%' and c3='a3';
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys    | key              | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | test03 | NULL       | range | idx_test03_c1234 | idx_test03_c1234 | 123     | NULL |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------+------------------+---------+------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

**小总结**

假设index(a, b, c)

| where语句                               | 索引是否被使用   |
| --------------------------------------- | ---------------- |
| where a = 3 and b like ‘kk%’ and c = 4  | Y，使用到a，b，c |
| where a = 3 and b like ‘%kk’ and c = 4  | Y，使用到a       |
| where a = 3 and b like ‘%kk%’ and c = 4 | Y，使用到a       |
| where a= 3 and b like ‘k%kk%’ and c = 4 | Y，使用到a，b，c |

**优化总结口诀**

全值匹配我最爱， 最左前缀要遵守；

带头大哥不能死， 中间兄弟不能断；

索引列上少计算， 范围之后全失效；

LIKE 百分写最右， 覆盖索引不写 *；

不等空值还有 OR， 索引影响要注意；

VAR 引号不可丢， SQL 优化有诀窍。

# 三、查询截取分析

## 46_小表驱动大表

通常SQL调优过程：

1. 观察，至少跑1天，看看生产的慢SQL情况。
2. 开启慢查询日志，设置阙值，比如超过5秒钟的就是慢SQL，并将它抓取出来。
3. explain + 慢SQL分析。
4. show profile。
5. 运维经理 or DBA，进行SQL数据库服务器的参数调优。

总结：

1. 慢查询的开启并捕获
2. explain + 慢SQL分析
3. show profile查询SQL在Mysql服务器里面的执行细节和生命周期情况
4. SQL数据库服务器的参数调优。

优化原则：小表驱动大表，即小的数据集驱动大的数据集。

RBO原理

```sql
select * from A where id in (select id from B)
等价于:
for select id from B
for select * from A where A.id = B.id
1234
```

当B表的数据集必须小于A表的数据集时，用in优于exists。

```sql
select * from A where exists (select 1 from B where B.id = A.id)
等价于：
for select * from A
for select * from B where B.id = A.id
1234
```

当A表的数据集系小于B表的数据集时，用exists优于in。

注意：A表与B表的ID字段应建立索引。

**EXISTS关键字**

```sql
SELECT ...FROM table WHERE EXISTS (subquery)
1
```

该语法可以理解为：将主查询的数据，放到子查询中做条件验证，根据验证结果（TRUE或FALSE）来决定主查询的数据结果是否得以保留。

提示

1. EXSTS(subquey)只返回TRUE或FALSE，因此子查询中的SELECT * 也可以是 SELECT 1 或select ‘X’，官方说法是实际执行时会忽略SELECT清单，因此没有区别。
2. EXISTS子查询的实际执行过程可能经过了优化而不是我们理解上的逐条对比，如果担忧效率问题，可进行实际检验以确定是否有效率问题。
3. EXISTS子查询往往也可以用条件表达式，其他子查询或者JOIN来替代，何种最优需要具体问题具体分析

## 47_in和exists

示例表：

```sql
mysql> select * from tbl_emp;
+----+------+--------+
| id | NAME | deptId |
+----+------+--------+
|  1 | z3   |      1 |
|  2 | z4   |      1 |
|  3 | z5   |      1 |
|  4 | w5   |      2 |
|  5 | w6   |      2 |
|  6 | s7   |      3 |
|  7 | s8   |      4 |
|  8 | s9   |     51 |
+----+------+--------+
8 rows in set (0.02 sec)

mysql> select * from tbl_dept;
+----+----------+--------+
| id | deptName | locAdd |
+----+----------+--------+
|  1 | RD       | 11     |
|  2 | HR       | 12     |
|  3 | MK       | 13     |
|  4 | MIS      | 14     |
|  5 | FD       | 15     |
+----+----------+--------+
5 rows in set (0.01 sec)
1234567891011121314151617181920212223242526
```

in和exists用法

```sql
mysql> select * from tbl_emp e where e.deptId in (select id from tbl_dept d);
+----+------+--------+
| id | NAME | deptId |
+----+------+--------+
|  1 | z3   |      1 |
|  2 | z4   |      1 |
|  3 | z5   |      1 |
|  4 | w5   |      2 |
|  5 | w6   |      2 |
|  6 | s7   |      3 |
|  7 | s8   |      4 |
+----+------+--------+
7 rows in set (0.00 sec)

mysql> select * from tbl_emp e where exists (select 1 from tbl_dept d where d.id = e.deptId);
+----+------+--------+
| id | NAME | deptId |
+----+------+--------+
|  1 | z3   |      1 |
|  2 | z4   |      1 |
|  3 | z5   |      1 |
|  4 | w5   |      2 |
|  5 | w6   |      2 |
|  6 | s7   |      3 |
|  7 | s8   |      4 |
+----+------+--------+
7 rows in set (0.00 sec)

mysql> select * from tbl_emp e where exists (select 'X' from tbl_dept d where d.id = e.deptId);
+----+------+--------+
| id | NAME | deptId |
+----+------+--------+
|  1 | z3   |      1 |
|  2 | z4   |      1 |
|  3 | z5   |      1 |
|  4 | w5   |      2 |
|  5 | w6   |      2 |
|  6 | s7   |      3 |
|  7 | s8   |      4 |
+----+------+--------+
7 rows in set (0.00 sec)
1234567891011121314151617181920212223242526272829303132333435363738394041
```

## 48_为排序使用索引OrderBy优化

ORDER BY子句，尽量使用Index方式排序，避免使用FileSort方式排序。

新建SQL

```sql
create table tblA(
    #id int primary key not null auto_increment,
    age int,
    birth timestamp not null
);

insert into tblA(age, birth) values(22, now());
insert into tblA(age, birth) values(23, now());
insert into tblA(age, birth) values(24, now());

create index idx_A_ageBirth on tblA(age, birth);

123456789101112
mysql> select * from tblA;
+------+---------------------+
| age  | birth               |
+------+---------------------+
|   22 | 2021-04-04 19:31:45 |
|   23 | 2021-04-04 19:31:45 |
|   24 | 2021-04-04 19:31:45 |
+------+---------------------+
3 rows in set (0.00 sec)

mysql> show index from tblA;
+-------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name       | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| tbla  |          1 | idx_A_ageBirth |            1 | age         | A         |           3 |     NULL | NULL   | YES  | BTREE      |         |               |
| tbla  |          1 | idx_A_ageBirth |            2 | birth       | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)
123456789101112131415161718
```

------

```sql
mysql> EXPLAIN SELECT * FROM tblA where age > 20 order by age;
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+--------------------------+
| id | select_type | table | partitions | type  | possible_keys  | key            | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | idx_A_ageBirth | idx_A_ageBirth | 9       | NULL |    3 |   100.00 | Using where; Using index |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM tblA where age>20 order by age,birth;
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+--------------------------+
| id | select_type | table | partitions | type  | possible_keys  | key            | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | idx_A_ageBirth | idx_A_ageBirth | 9       | NULL |    3 |   100.00 | Using where; Using index |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM tblA where age>20 order by birth;
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+------------------------------------------+
| id | select_type | table | partitions | type  | possible_keys  | key            | key_len | ref  | rows | filtered | Extra                                    |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+------------------------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | idx_A_ageBirth | idx_A_ageBirth | 9       | NULL |    3 |   100.00 | Using where; Using index; Using filesort |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+------------------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM tblA where age>20 order by birth,age;
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+------------------------------------------+
| id | select_type | table | partitions | type  | possible_keys  | key            | key_len | ref  | rows | filtered | Extra                                    |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+------------------------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | idx_A_ageBirth | idx_A_ageBirth | 9       | NULL |    3 |   100.00 | Using where; Using index; Using filesort |
+----+-------------+-------+------------+-------+----------------+----------------+---------+------+------+----------+------------------------------------------+
1 row in set, 1 warning (0.00 sec)
12345678910111213141516171819202122232425262728293031
mysql> EXPLAIN SELECT * FROM tblA order by birth;
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+-----------------------------+
| id | select_type | table | partitions | type  | possible_keys | key            | key_len | ref  | rows | filtered | Extra                       |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+-----------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | NULL          | idx_A_ageBirth | 9       | NULL |    3 |   100.00 | Using index; Using filesort |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+-----------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM tblA WHERE birth > '2016-01-28 00:00:00' order by birth;
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+------------------------------------------+
| id | select_type | table | partitions | type  | possible_keys | key            | key_len | ref  | rows | filtered | Extra                                    |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+------------------------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | NULL          | idx_A_ageBirth | 9       | NULL |    3 |    33.33 | Using where; Using index; Using filesort |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+------------------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM tblA WHERE birth > '2016-01-28 00:00:00' order by age;
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+--------------------------+
| id | select_type | table | partitions | type  | possible_keys | key            | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | NULL          | idx_A_ageBirth | 9       | NULL |    3 |    33.33 | Using where; Using index |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> EXPLAIN SELECT * FROM tblA order by age, birth desc;
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+-----------------------------+
| id | select_type | table | partitions | type  | possible_keys | key            | key_len | ref  | rows | filtered | Extra                       |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+-----------------------------+
|  1 | SIMPLE      | tblA  | NULL       | index | NULL          | idx_A_ageBirth | 9       | NULL |    3 |   100.00 | Using index; Using filesort |
+----+-------------+-------+------------+-------+---------------+----------------+---------+------+------+----------+-----------------------------+
1 row in set, 1 warning (0.00 sec)
12345678910111213141516171819202122232425262728293031
```

MySQL支持二种方式的排序，FileSort和lIndex，Index效率高，它指MySQL扫描索引本身完成排序。FileSort方式效率较低。

ORDER BY满足两情况，会使用Index方式排序：

1. ORDER BY语句使用索引最左前列。
2. 使用where子句与Order BY子句条件列组合满足索引最左前列。

如果不在索引列上，mysql的filesort有两种算法：

1. 双路排序
2. 单路排序

**双路排序**

MySQL4.1之前是使用双路排序，字面意思就是两次扫描磁盘，最终得到数据，读取行指针和OrderBy列，对他们进行排序，然后扫描已经排序好的列表，按照列表中的值重新从列表中读对应的数据输出。

从磁盘取排序字段，在buffer进行排序，再从磁盘取其他字段。

取一批数据，要对磁盘进行了两次扫描，众所周知，I\O是很耗时的，所以在mysql4.1之后，出现了第二种改进的算法，就是单路排序。

**单路排序**

从磁盘读取查询需要的所有列，按照order by列在buffer对它们进行排序，然后扫描排序压的列表进行输出，它的效率更快一些，避免了第二次读取数据。并且把随机IO变成了顺序IO,但是它会使用更多的空间，因为它把每一行都保存在内存中了。

**结论及引申出的问题**

由于单路是后出的，总体而言好过双路

但是用单路有问题

在sort_buffer中，方法B比方法A要多占用很多空间，因为方法B是把所有字段都取出,所以有可能取出的数据的总大小超出了sort_buffer的容量，导致每次只能取sort_buffer容量大小的数据，进行排序（创建tmp文件，多路合并)，排完再取取
sort_buffer容量大小，再排……从而多次I/O。

本来想省一次I/O操作，反而导致了大量的I/O操作，反而得不偿失。

**优化策略**

- 增大sort_buffer_size参数的设置
- 增大max_length_for_sort_data参数的设置

**为什么设置sort_buffer_size、max_length_for_sort_data参数能优化排序？**

提高Order By的速度

1. Order by时select * 是一个大忌只Query需要的字段，这点非常重要。在这里的影响是;
   1. 当Query的字段大小总和小于max_length_for_sort_data而且排序字段不是TEXT|BLOB类型时，会用改进后的算法——单路排序，否则用老算法——多路排序。
   2. 两种算法的数据都有可能超出sort_buffer的容量，超出之后，会创建tmp文件进行合并排序，导致多次IO，但是用单路排序算法的风险会更大一些，所以要提高sort_buffer__size。
2. 尝试提高sort_buffer_size，不管用哪种算法，提高这个参数都会提高效率，当然，要根据系统的能力去提高，因为这个参数是针对每个进程的。
3. 尝试提高max_length_for_sort_data，提高这个参数，会增加用改进算法的概率。但是如果设的太高，数据总容量超出sort_buffer_size的概率就增大，明显症状是高的磁盘I/O活动和低的处理器使用率。

### 小结

为排序使用索引

- MySql两种排序方式∶文件排序 或 扫描有序索引排序
- MySql能为 排序 与 查询 使用相同的索引

创建复合索引 a_b_c (a, b, c)

order by能使用索引最左前缀

- ORDER BY a
- ORDER BY a, b
- ORDER BY a, b, c
- ORDER BY a DESC, b DESC, c DESC

如果WHERE使用素引的最左前缀定义为常量，则order by能使用索引

- WHERE a = const ORDER BY b,c
- WHERE a = const AND b = const ORDER BY c
- WHERE a = const ORDER BY b, c
- WHERE a = const AND b > const ORDER BY b, c

不能使用索引进行排序

- ORDER BY a ASC, b DESC, c DESC //排序不—致
- WHERE g = const ORDER BY b, c //产丢失a索引
- WHERE a = const ORDER BY c //产丢失b索引
- WHERE a = const ORDER BY a, d //d不是素引的一部分
- WHERE a in (…) ORDER BY b, c //对于排序来说,多个相等条件也是范围查询

## 49_GroupBy优化与慢查询日志

**GroupBy优化**

- group by实质是先排序后进行分组，遵照索引建的最佳左前缀。
- 当无法使用索引列，增大max_length_for_sort_data参数的设置 + 增大sort_buffer_size参数的设置。
- where高于having，能写在where限定的条件就不要去having限定了。

### 慢查询日志

- MySQL的慢查询日志是MySQL提供的一种日志记录，它用来记录在MySQL中响应时间超过阀值的语句，具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。
- 具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。long_query_time的默认值为10，意思是运行10秒以上的语句。
- 由他来查看哪些SQL超出了我们的最大忍耐时间值，比如一条sql执行超过5秒钟，我们就算慢SQL，希望能收集超过5秒的sql，结合之前explain进行全面分析。

#### 如何操作

默认情况下，MySQL数据库没有开启慢查询日速，需要我们手动来设置这个参数。

当然，如果不是调优需要的话，一般不建议启动该参数，因为开启慢查询日志会或多或少带来一定的性能影响。慢查询日志支持将日志记录写入文件。

**查看是否开启及如何开启**

- 默认 - `SHOW VARIABLES LIKE '%slow_query_log%';`
- 开启 - `set global slow_query_log=1;`，只对当前数据库生效，如果MySQL重启后则会失效。

```sql
mysql> SHOW VARIABLES LIKE '%slow_query_log%';
+---------------------+--------------------------+
| Variable_name       | Value                    |
+---------------------+--------------------------+
| slow_query_log      | OFF                      |
| slow_query_log_file | DESKTOP-LNJQ0VF-slow.log |
+---------------------+--------------------------+
2 rows in set, 1 warning (0.00 sec)

mysql> set global slow_query_log=1;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW VARIABLES LIKE '%slow_query_log%';
+---------------------+--------------------------+
| Variable_name       | Value                    |
+---------------------+--------------------------+
| slow_query_log      | ON                       |
| slow_query_log_file | DESKTOP-LNJQ0VF-slow.log |
+---------------------+--------------------------+
2 rows in set, 1 warning (0.00 sec)
1234567891011121314151617181920
```

如果要永久生效，就必须修改配置文件my.cnf(其它系统变量也是如此)

修改my.cnf文件，[mysqld]下增加或修改参数slow_query_log和slow_query_log_file后，然后重启MySQL服务器。也即将如下两行配置进my.cnf文件

```properties
slow_query_log =1
slow_query_log_file=/var/lib/mysqatguigu-slow.log
12
```

关于慢查询的参数slow_query_log_file，它指定慢查询日志文件的存放路径，系统默认会给一个缺省的文件host_name-slow.log（如果没有指定参数slow_query_log_file的话）

**开启了慢查询日志后，什么样的SQL才会记录到慢查询日志里面呢？**

这个是由参数long_query_time控制，默认情况下long_query_time的值为10秒，命令：`SHOW VARIABLES LIKE 'long_query_time%';`

```sql
mysql> SHOW VARIABLES LIKE 'long_query_time%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set, 1 warning (0.00 sec)
1234567
```

可以使用命令修改，也可以在my.cnf参数里面修改。

假如运行时间正好等于long_query_time的情况，并不会被记录下来。也就是说，在mysql源码里是判断大于long_query_time，而非大于等于。

**设置慢SQL阈值时间**：`set global long_query_time=3;`

```sql
mysql> SHOW VARIABLES LIKE 'long_query_time%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set, 1 warning (0.00 sec)

mysql> set global long_query_time=3;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE 'long_query_time%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set, 1 warning (0.00 sec)
123456789101112131415161718
```

**为什么设置后看不出变化？**

需要重新连接或新开一个会话才能看到修改值。

**记录慢SQL并后续分析**

假设我们成功设置慢SQL阈值时间为3秒（`set global long_query_time=3;`）。

模拟超时SQL：

```sql
mysql> SELECT sleep(4);
+----------+
| sleep(4) |
+----------+
|        0 |
+----------+
1 row in set (4.00 sec)
1234567
```

日志记录：

![img](https://img-blog.csdnimg.cn/img_convert/08f4e9c29e8dd23f858aad56e9c183e2.png)

**查询当前系统中有多少条慢查询记录**

```sql
mysql> show global status like '%Slow_queries%';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Slow_queries  | 1     |
+---------------+-------+
1 row in set (0.00 sec)
1234567
```

**在配置文件中设置慢SQL阈值时间**

```properties
#[mysqld]下配置:
slow_query_log=1;
slow_query_log_file=/var/lib/mysql/atguigu-slow.log
long_query_time=3;
log_output=FILE;
12345
```

#### 日志分析工具mysqldumpslow

在生产环境中，如果要手工分析日志，查找、分析SQL，显然是个体力活，MySQL提供了日志分析工具mysqldumpslow。

查看mysqldumpslow的帮助信息，`mysqldumpslow --help`。

![img](https://img-blog.csdnimg.cn/img_convert/b3b01f9db20a6cce52b9906192dabf96.png)

- s：是表示按照何种方式排序
- c：访问次数
- l：锁定时间
- r：返回记录
- t：查询时间
- al：平均锁定时间
- ar：平均返回记录数
- at：平均查询时间
- t：即为返回前面多少条的数据
- g：后边搭配一个正则匹配模式，大小写不敏感的

**工作常用参考**

- 得到返回记录集最多的10个SQL
  - `mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log`
- 得到访问次数最多的10个SQL
  - `mysqldumpslow -s c -t 10 /var/lib/mysql/atguigu-slow.log`
- 得到按照时间排序的前10条里面含有左连接的查询语句
  - `mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/atguigu-slow.log`
- 另外建议在使用这些命令时结合│和more 使用，否则有可能出现爆屏情况
  - `mysqldumpslow -s r-t 10 /ar/lib/mysql/atguigu-slow.log | more`

## 50_批量插入数据脚本

**创建SQL**

```sql
create database bigData;
use bigData;

CREATE TABLE dept(
	id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
	deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
	dname VARCHAR(20)NOT NULL DEFAULT "",
	loc VARCHAR(13) NOT NULL DEFAULT ""
)ENGINE=INNODB DEFAULT CHARSET=utf8;

CREATE TABLE emp(
    id int unsigned primary key auto_increment,
    empno mediumint unsigned not null default 0,
    ename varchar(20) not null default "",
    job varchar(9) not null default "",
    mgr mediumint unsigned not null default 0,
    hiredate date not null,
    sal decimal(7,2) not null,
    comm decimal(7,2) not null,
    deptno mediumint unsigned not null default 0
)ENGINE=INNODB DEFAULT CHARSET=utf8;
123456789101112131415161718192021
```

**设置参数log_bin_trust_function_creators**

创建函数，假如报错：This function has none of DETERMINISTIC…

由于开启过慢查询日志，因为我们开启了bin-log，我们就必须为我们的function指定一个参数。

```sql
show variables like 'log_bin_trust_function_creators';
set global log_bin_trust_function_creators=1;
12
```

这样添加了参数以后，如果mysqld重启，上述参数又会消失，永久方法：

- windows下my.ini[mysqld]加上`log_bin_trust_function_creators=1`
- linux下/etc/my.cnf 下my.cnf[mysqld]加上`log_bin_trust_function_creators=1`

**创建函数，保证每条数据都不同**

随机产生字符串

```sql
delimiter $$ # 两个 $$ 表示结束
create function rand_string(n int) returns varchar(255)
begin
    declare chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyz';
    declare return_str varchar(255) default '';
    declare i int default 0;
    while i < n do
        set return_str = concat(return_str,substring(chars_str,floor(1+rand()*52),1));
        set i=i+1;
    end while;
    return return_str;
end $$
123456789101112
```

随机产生部门编号

```sql
delimiter $$
create function rand_num() returns int(5)
begin
    declare i int default 0;
    set i=floor(100+rand()*10);
    return i;
end $$
1234567
```

运行结果

```sql
mysql> delimiter $$ # 两个 $$ 表示结束
mysql> create function rand_string(n int) returns varchar(255)
    -> begin
    ->     declare chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyz';
    ->     declare return_str varchar(255) default '';
    ->     declare i int default 0;
    ->     while i < n do
    ->         set return_str = concat(return_str,substring(chars_str,floor(1+rand()*52),1));
    ->         set i=i+1;
    ->     end while;
    ->     return return_str;
    -> end $$
Query OK, 0 rows affected (0.00 sec)

mysql> select rand_string(2);
    -> ;
    -> $$
+----------------+
| rand_string(2) |
+----------------+
| af             |
+----------------+
1 row in set (0.00 sec)

mysql> delimiter $$
mysql> create function rand_num() returns int(5)
    -> begin
    ->     declare i int default 0;
    ->     set i=floor(100+rand()*10);
    ->     return i;
    -> end $$
Query OK, 0 rows affected (0.00 sec)

mysql> select rand_num()$$
+------------+
| rand_num() |
+------------+
|        105 |
+------------+
1 row in set (0.00 sec)
12345678910111213141516171819202122232425262728293031323334353637383940
```

**创建存储过程**

创建往emp表中插入数据的存储过程

```sql
delimiter $$
create procedure insert_emp(in start int(10),in max_num int(10))
begin
    declare i int default 0;
    set autocommit = 0;
    repeat
        set i = i+1;
        insert into emp(empno,ename,job,mgr,hiredate,sal,comm,deptno) values((start+i),rand_string(6),'salesman',0001,curdate(),2000,400,rand_num());
        until i=max_num
        end repeat;
    commit;
end $$
123456789101112
```

创建往dept表中插入数据的存储过程

```sql
delimiter $$
create procedure insert_dept(in start int(10),in max_num int(10))
begin
    declare i int default 0;
    set autocommit = 0;
    repeat
        set i = i+1;
        insert into dept(deptno,dname,loc) values((start+i),rand_string(10),rand_string(8));
        until i=max_num
        end repeat;
    commit;
end $$

12345678910111213
```

**调用存储过程**

往dept表中插入数据

```sql
mysql> DELIMITER ;
mysql> CALL insert_dept(100, 10);
Query OK, 0 rows affected (0.01 sec)
123
```

往emp表中插入50万数据

```sql
mysql> DELIMITER ;
mysql> CALL insert_emp(100001, 500000);
Query OK, 0 rows affected (27.00 sec)
123
```

运行结果

```sql
mysql> select * from dept;
+----+--------+---------+--------+
| id | deptno | dname   | loc    |
+----+--------+---------+--------+
|  1 |    101 | mqgfy   | ck     |
|  2 |    102 | wgighsr | kbq    |
|  3 |    103 | gjgdyj  | brb    |
|  4 |    104 | gzfug   | p      |
|  5 |    105 | keitu   | cib    |
|  6 |    106 | nndvuv  | csue   |
|  7 |    107 | cdudl   | tw     |
|  8 |    108 | aafyea  | aqq    |
|  9 |    109 | zuqezjx | dpqoyo |
| 10 |    110 | pam     | cses   |
+----+--------+---------+--------+
10 rows in set (0.00 sec)

mysql> select * from emp limit 20;
+----+--------+-------+----------+-----+------------+---------+--------+--------+
| id | empno  | ename | job      | mgr | hiredate   | sal     | comm   | deptno |
+----+--------+-------+----------+-----+------------+---------+--------+--------+
|  1 | 100002 | xmbva | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
|  2 | 100003 | aeq   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    109 |
|  3 | 100004 | cnjfz | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    105 |
|  4 | 100005 | wwhd  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    100 |
|  5 | 100006 | e     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
|  6 | 100007 | yjfr  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
|  7 | 100008 | xlp   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
|  8 | 100009 | mp    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
|  9 | 100010 | tcdl  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
| 10 | 100011 | akw   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    106 |
| 11 | 100012 | re    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
| 12 | 100013 | qip   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    105 |
| 13 | 100014 | bvaf  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
| 14 | 100015 | g     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    106 |
| 15 | 100016 | qt    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
| 16 | 100017 | bzy   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    109 |
| 17 | 100018 | gf    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
| 18 | 100019 | r     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
| 19 | 100020 | ydokg | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    100 |
| 20 | 100021 | ee    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
+----+--------+-------+----------+-----+------------+---------+--------+--------+
20 rows in set (0.00 sec)
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

## 51_用Show Profile进行sql分析

Show Profile是mysql提供可以用来分析当前会话中语句执行的资源消耗情况。可以用于SQL的调优的测量。

[官方文档](https://dev.mysql.com/doc/refman/8.0/en/show-profile.html)

默认情况下，参数处于关闭状态，并保存最近15次的运行结果。

**分析步骤**

1.是否支持，看看当前的mysql版本是否支持。

```sql
mysql> show variables like 'profiling';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| profiling     | OFF   |
+---------------+-------+
1 row in set, 1 warning (0.00 sec)
1234567
```

默认是关闭，使用前需要开启。

2.开启功能，默认是关闭，使用前需要开启。

```sql
mysql> set profiling=on;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> show variables like 'profiling';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| profiling     | ON    |
+---------------+-------+
1 row in set, 1 warning (0.00 sec)
12345678910
```

3.运行SQL

```sql
mysql> select * from emp group by id%10 limit 150000;
+----+--------+-------+----------+-----+------------+---------+--------+--------+
| id | empno  | ename | job      | mgr | hiredate   | sal     | comm   | deptno |
+----+--------+-------+----------+-----+------------+---------+--------+--------+
| 10 | 100011 | akw   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    106 |
|  1 | 100002 | xmbva | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
|  2 | 100003 | aeq   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    109 |
|  3 | 100004 | cnjfz | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    105 |
|  4 | 100005 | wwhd  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    100 |
|  5 | 100006 | e     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
|  6 | 100007 | yjfr  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
|  7 | 100008 | xlp   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
|  8 | 100009 | mp    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
|  9 | 100010 | tcdl  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
+----+--------+-------+----------+-----+------------+---------+--------+--------+
10 rows in set (0.55 sec)

mysql> select * from emp group by id%20 order by 5;
+----+--------+-------+----------+-----+------------+---------+--------+--------+
| id | empno  | ename | job      | mgr | hiredate   | sal     | comm   | deptno |
+----+--------+-------+----------+-----+------------+---------+--------+--------+
|  1 | 100002 | xmbva | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
|  3 | 100004 | cnjfz | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    105 |
|  5 | 100006 | e     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
|  7 | 100008 | xlp   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
|  9 | 100010 | tcdl  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
| 11 | 100012 | re    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
| 13 | 100014 | bvaf  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
| 15 | 100016 | qt    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
| 17 | 100018 | gf    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
| 19 | 100020 | ydokg | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    100 |
|  2 | 100003 | aeq   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    109 |
|  4 | 100005 | wwhd  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    100 |
|  6 | 100007 | yjfr  | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
|  8 | 100009 | mp    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    102 |
| 10 | 100011 | akw   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    106 |
| 12 | 100013 | qip   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    105 |
| 14 | 100015 | g     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    106 |
| 16 | 100017 | bzy   | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    109 |
| 18 | 100019 | r     | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    108 |
| 20 | 100021 | ee    | salesman |   1 | 2021-04-05 | 2000.00 | 400.00 |    107 |
+----+--------+-------+----------+-----+------------+---------+--------+--------+
20 rows in set (0.57 sec)
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

4.查看结果，`show profiles;`

```sql
mysql> show profiles;
+----------+------------+-----------------------------------------------+
| Query_ID | Duration   | Query                                         |
+----------+------------+-----------------------------------------------+
|        1 | 0.00204000 | show variables like 'profiling'               |
|        2 | 0.55134250 | select * from emp group by id%10 limit 150000 |
|        3 | 0.56902000 | select * from emp group by id%20 order by 5   |
+----------+------------+-----------------------------------------------+
3 rows in set, 1 warning (0.00 sec)
123456789
```

5.诊断SQL，`show profile cpu,block io for query 上一步前面的问题SQL数字号码;`

```sql
mysql> show profile cpu,block io for query 3;
+----------------------+----------+----------+------------+--------------+---------------+
| Status               | Duration | CPU_user | CPU_system | Block_ops_in | Block_ops_out |
+----------------------+----------+----------+------------+--------------+---------------+
| starting             | 0.000049 | 0.000000 |   0.000000 |         NULL |          NULL |
| checking permissions | 0.000005 | 0.000000 |   0.000000 |         NULL |          NULL |
| Opening tables       | 0.000012 | 0.000000 |   0.000000 |         NULL |          NULL |
| init                 | 0.000021 | 0.000000 |   0.000000 |         NULL |          NULL |
| System lock          | 0.000009 | 0.000000 |   0.000000 |         NULL |          NULL |
| optimizing           | 0.000003 | 0.000000 |   0.000000 |         NULL |          NULL |
| statistics           | 0.000017 | 0.000000 |   0.000000 |         NULL |          NULL |
| preparing            | 0.000008 | 0.000000 |   0.000000 |         NULL |          NULL |
| Creating tmp table   | 0.000045 | 0.000000 |   0.000000 |         NULL |          NULL |
| Sorting result       | 0.000004 | 0.000000 |   0.000000 |         NULL |          NULL |
| executing            | 0.000002 | 0.000000 |   0.000000 |         NULL |          NULL |
| Sending data         | 0.568704 | 0.546875 |   0.046875 |         NULL |          NULL |
| Creating sort index  | 0.000048 | 0.000000 |   0.000000 |         NULL |          NULL |
| end                  | 0.000003 | 0.000000 |   0.000000 |         NULL |          NULL |
| query end            | 0.000005 | 0.000000 |   0.000000 |         NULL |          NULL |
| removing tmp table   | 0.000006 | 0.000000 |   0.000000 |         NULL |          NULL |
| query end            | 0.000003 | 0.000000 |   0.000000 |         NULL |          NULL |
| closing tables       | 0.000004 | 0.000000 |   0.000000 |         NULL |          NULL |
| freeing items        | 0.000061 | 0.000000 |   0.000000 |         NULL |          NULL |
| cleaning up          | 0.000015 | 0.000000 |   0.000000 |         NULL |          NULL |
+----------------------+----------+----------+------------+--------------+---------------+
20 rows in set, 1 warning (0.00 sec)
1234567891011121314151617181920212223242526
```

参数备注

- ALL：显示所有的开销信息。
- BLOCK IO：显示块lO相关开销。
- CONTEXT SWITCHES ：上下文切换相关开销。
- CPU：显示CPU相关开销信息。
- IPC：显示发送和接收相关开销信息。
- MEMORY：显示内存相关开销信息。
- PAGE FAULTS：显示页面错误相关开销信息。
- SOURCE：显示和Source_function，Source_file，Source_line相关的开销信息。
- SWAPS：显示交换次数相关开销的信息。

6.日常开发需要注意的结论

- converting HEAP to MyISAM 查询结果太大，内存都不够用了往磁盘上搬了。
- Creating tmp table 创建临时表，拷贝数据到临时表，用完再删除
- Copying to tmp table on disk 把内存中临时表复制到磁盘，危险!
- locked

## 52_全局查询日志

永远不要在生产环境开启这个功能。

配置文件启用。在mysql的my.cnf中，设置如下：

```properties
#开启
general_log=1
#记录日志文件的路径
general_log_file=/path/logfile
#输出格式
log_output=FILE
123456
```

编码启用。命令如下：

- `set global general_log=1;`
- `set global log_output='TABLE';`

```sql
mysql> set global general_log=1;
Query OK, 0 rows affected (0.01 sec)

mysql> set global log_output='TABLE';
Query OK, 0 rows affected (0.00 sec)
12345
```

此后，你所编写的sql语句，将会记录到mysql库里的geneial_log表，可以用下面的命令查看：

```sql
mysql> select * from mysql.general_log;
+----------------------------+------------------------------+-----------+-----------+--------------+---------------------------------+
| event_time                 | user_host                    | thread_id | server_id | command_type | argument                        |
+----------------------------+------------------------------+-----------+-----------+--------------+---------------------------------+
| 2021-04-05 19:57:28.182473 | root[root] @ localhost [::1] |         5 |         1 | Query        | select * from mysql.general_log |
+----------------------------+------------------------------+-----------+-----------+--------------+---------------------------------+
1 row in set (0.00 sec)
1234567
```

# 四、MySQL锁机制

## 53_数据库锁理论概述

锁是计算机协调多个进程或线程并发访问某一资源的机制。

在数据库中，除传统的计算资源（如CPU、RAM、I/O等）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

**类比：网上购物**

打个比方，我们到淘宝上买一件商品，商品只有一件库存，这个时候如果还有另一个人买，那么如何解决是你买到还是另一个人买到的问题？

这里肯定要用到事务，我们先从库存表中取出物品数量，然后插入订单，付款后插入付款表信息，然后更新商品数量。在这个过程中，使用锁可以对有限的资源进行保护，解决隔离和并发的矛盾。

**锁的分类**

从对数据操作的类型（读\写）分

- 读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会互相影响。
- 写锁（排它锁）：当前写操作没有完成前，它会阻断其他写锁和读锁。

从对数据操作的粒度分

- 表锁
- 行锁

## 54_读锁案例讲解

表锁（偏读）

特点：偏向MyISAM存储引擎，开销小，加锁快；无死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低。

**案例分析**

建表SQL

```sql
create table mylock (
    id int not null primary key auto_increment,
    name varchar(20) default ''
) engine myisam;

insert into mylock(name) values('a');
insert into mylock(name) values('b');
insert into mylock(name) values('c');
insert into mylock(name) values('d');
insert into mylock(name) values('e');

select * from mylock;
123456789101112
```

运行结果

```sql
mysql> create table mylock (
    ->     id int not null primary key auto_increment,
    ->     name varchar(20) default ''
    -> ) engine myisam;
Query OK, 0 rows affected (0.01 sec)

mysql>
mysql> insert into mylock(name) values('a');
Query OK, 1 row affected (0.00 sec)

mysql> insert into mylock(name) values('b');
Query OK, 1 row affected (0.00 sec)

mysql> insert into mylock(name) values('c');
Query OK, 1 row affected (0.00 sec)

mysql> insert into mylock(name) values('d');
Query OK, 1 row affected (0.00 sec)

mysql> insert into mylock(name) values('e');
Query OK, 1 row affected (0.00 sec)

mysql>
mysql> select * from mylock;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  4 | d    |
|  5 | e    |
+----+------+
5 rows in set (0.00 sec)
12345678910111213141516171819202122232425262728293031323334
```

手动增加表锁

```
lock table 表名字 read(write), 表名字2 read(write), 其他;
mysql> lock table mylock read;
Query OK, 0 rows affected (0.00 sec)
12
```

查看表上加过的锁

```sql
mysql> show open tables;
+--------------------+------------------------------------------------------+--------+-------------+
| Database           | Table                                                | In_use | Name_locked |
+--------------------+------------------------------------------------------+--------+-------------+
| performance_schema | events_waits_summary_by_user_by_event_name           |      0 |           0 |
| performance_schema | events_waits_summary_global_by_event_name            |      0 |           0 |
| performance_schema | events_transactions_summary_global_by_event_name     |      0 |           0 |
| performance_schema | replication_connection_status                        |      0 |           0 |
| mysql              | time_zone_leap_second                                |      0 |           0 |
| mysql              | columns_priv                                         |      0 |           0 |
| my                 | test03                                               |      0 |           0 |
| bigdata            | mylock                                               |      1 |           0 |
...
12345678910111213
```

释放锁

```sql
mysql> unlock tables;
Query OK, 0 rows affected (0.00 sec)
12
```

**加读锁——为mylock表加read锁（读阻塞写例子）**

![img](https://img-blog.csdnimg.cn/img_convert/0e71513b7dd3449d8012c56e55630d54.png)

## 55_读锁案例讲解2

为mylock表加write锁（MylSAM存储引擎的写阻塞读例子）

![img](https://img-blog.csdnimg.cn/img_convert/2eb7d50fbb4410b3a246db53f63dc3c9.png)

**案例结论**

MyISAM在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行增删改操作前，会自动给涉及的表加写锁。

MySQL的表级锁有两种模式：

1. 表共享读锁(Table Read Lock)
2. 表独占写锁(Table Write Lock)

| 锁类型 | 可否兼容 | 读锁 | 写锁 |
| ------ | -------- | ---- | ---- |
| 读锁   | 是       | 是   | 否   |
| 写锁   | 是       | 否   | 否   |

结合上表，所以对MyISAM表进行操作，会有以下情况：

1. 对MyISAM表的读操作（加读锁），不会阻塞其他进程对同一表的读请求，但会阻塞对同一表的写请求。只有当读锁释放后，才会执行其它进程的写操作。
2. 对MyISAM表的写操作〈加写锁），会阻塞其他进程对同一表的读和写操作，只有当写锁释放后，才会执行其它进程的读写操作。

**简而言之，就是读锁会阻塞写，但是不会堵塞读。而写锁则会把读和写都堵塞。**

**表锁分析**

*看看哪些表被加锁了*

```sql
mysql> show open tables;
1
```

*如何分析表锁定*

可以通过检查table_locks_waited和table_locks_immediate状态变量来分析系统上的表锁定。

```sql
mysql>  show status like 'table_locks%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| Table_locks_immediate | 170   |
| Table_locks_waited    | 0     |
+-----------------------+-------+
2 rows in set (0.00 sec)
12345678
```

这里有两个状态变量记录MySQL内部表级锁定的情况，两个变量说明如下：

- Table_locks_immediate：产生表级锁定的次数，表示可以立即获取锁的查询次数，每立即获取锁值加1 ;
- Table_locks_waited：出现表级锁定争用而发生等待的次数(不能立即获取锁的次数，每等待一次锁值加1)，此值高则说明存在着较严重的表级锁争用情况；

此外，MyISAM的读写锁调度是写优先，这也是MyISAM不适合做写为主表的引擎。因为写锁后，其他线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞。

## 56_行锁理论

偏向InnoDB存储引擎，开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

InnoDB与MyISAM的最大不同有两点：一是支持事务(TRANSACTION)；二是采用了行级锁。

**由于行锁支持事务，复习老知识**

- 事务(Transaction）及其ACID属性
- 并发事务处理带来的问题
- 事务隔离级别

事务是由一组SQL语句组成的逻辑处理单元，事务具有以下4个属性，通常简称为事务的ACID属性：

- 原子性(Atomicity）：事务是一个原子操作单元，其对数据的修改，要么全都执行，要么全都不执行。
- 一致性（Consistent）：在事务开始和完成时，数据都必须保持一致状态。这意味着所有相关的数据规则都必须应用于事务的修改，以保持数据的完整性;事务结束时，所有的内部数据结构〈如B树索引或双向链表）也都必须是正确的。
- 隔离性（lsolation）：数据库系统提供一定的隔离机制，保证事务在不受外部并发操作影响的“独立”环境执行。这意味着事务处理过程中的中间状态对外部是不可见的，反之亦然。
- 持久性（Durable）：事务完成之后，它对于数据的修改是永久性的，即使出现系统故障也能够保持。

**并发事务处理带来的问题**

- 更新丢失(Lost Update)
- 脏读(Dirty Reads)
- 不可重复读(Non-Repeatable Reads)
- 幻读(Phantom Reads)

**更新丢失(Lost Update)**

当两个或多个事务选择同一行，然后基于最初选定的值更新该行时，由于每个事务都不知道其他事务的存在，就会发生丢失更新问题――最后的更新覆盖了由其他事务所做的更新。

例如，两个程序员修改同一java文件。每程序员独立地更改其副本，然后保存更改后的副本，这样就覆盖了原始文档。最后保存其更改副本的编辑人员覆盖前一个程序员所做的更改。

如果在一个程序员完成并提交事务之前，另一个程序员不能访问同一文件，则可避免此问题。

**脏读(Dirty Reads)**

一个事务正在对一条记录做修改，在这个事务完成并提交前，这条记录的数据就处于不一致状态；这时，另一个事务也来读取同一条记录，如果不加控制，第二个事务读取了这些“脏”数据，并据此做进一步的处理，就会产生未提交的数据依赖关系。这种现象被形象地叫做”脏读”。

一句话：事务A读取到了事务B已修改但尚未提交的的数据，还在这个数据基础上做了操作。此时，如果B事务回滚，A读取的数据无效，不符合一致性要求。

**不可重复读(Non-Repeatable Reads)**

一个事务在读取某些数据后的某个时间，再次读取以前读过的数据，却发现其读出的数据已经发生了改变、或某些记录已经被删除了!这种现象就叫做“不可重复读”。

一句话：事务A读取到了事务B已经提交的修改数据，不符合隔离性。

**幻读(Phantom Reads)**

一个事务接相同的查询条件重新读取以前检索过的数据，却发现其他事务插入了满足其查询条件的新数据，这种现象就称为“幻读“。

一句话:事务A读取到了事务B体提交的新增数据，不符合隔离性。

多说一句：幻读和脏读有点类似，

脏读是事务B里面修改了数据，

幻读是事务B里面新增了数据。

**事务隔离级别**

”脏读”、“不可重复读”和“幻读”，其实都是数据库读一致性问题，必须由数据库提供一定的事务隔离机制来解决。

| 读数据—致性及允许的并发副作用（隔离级别） | 读数据一致性                             | 脏读 | 不可重复读 | 幻读 |
| ----------------------------------------- | ---------------------------------------- | ---- | ---------- | ---- |
| 未提交读（Read Uncommitted）              | 最低级别，只能保证不读取物理上损坏的数据 | 是   | 是         | 是   |
| 已提交读（Read committed）                | 语句级                                   | 否   | 是         | 是   |
| 可重复读（Repeatable read）               | 事务级                                   | 否   | 否         | 是   |
| 可序列化（serializable）                  | 最高级别，事务级                         | 否   | 否         | 否   |

数据库的事务隔离越严格，并发副作用越小，但付出的代价也就越大，因为事务隔离实质上就是使事务在一定程度上“串行化”进行，这显然与“并发”是矛盾的。同时，不同的应用对读一致性和事务隔离程度的要求也是不同的，比如许多应用对“不可重复读”和“幻读”并不敏感，可能更关心数据并发访问的能力。

常看当前数据库的事务隔离级别：`show variables like 'tx_isolation';`

```sql
mysql> show variables like 'tx_isolation';
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| tx_isolation  | REPEATABLE-READ |
+---------------+-----------------+
1 row in set, 1 warning (0.00 sec)
1234567
```

## 57_行锁案例讲解

新建SQL

```sql
CREATE TABLE test_innodb_lock (a INT(11),b VARCHAR(16))ENGINE=INNODB;

INSERT INTO test_innodb_lock VALUES(1,'b2');
INSERT INTO test_innodb_lock VALUES(3,'3');
INSERT INTO test_innodb_lock VALUES(4, '4000');
INSERT INTO test_innodb_lock VALUES(5,'5000');
INSERT INTO test_innodb_lock VALUES(6, '6000');
INSERT INTO test_innodb_lock VALUES(7,'7000');
INSERT INTO test_innodb_lock VALUES(8, '8000');
INSERT INTO test_innodb_lock VALUES(9,'9000');
INSERT INTO test_innodb_lock VALUES(1,'b1');

CREATE INDEX test_innodb_a_ind ON test_innodb_lock(a);
CREATE INDEX test_innodb_lock_b_ind ON test_innodb_lock(b);

123456789101112131415
```

运行结果

```sql
mysql> select * from test_innodb_lock;
+------+------+
| a    | b    |
+------+------+
|    1 | b2   |
|    3 | 3    |
|    4 | 4000 |
|    5 | 5000 |
|    6 | 6000 |
|    7 | 7000 |
|    8 | 8000 |
|    9 | 9000 |
|    1 | b1   |
+------+------+
9 rows in set (0.00 sec)

mysql> show index from test_innodb_lock;
+------------------+------------+------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table            | Non_unique | Key_name               | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+------------------+------------+------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| test_innodb_lock |          1 | test_innodb_a_ind      |            1 | a           | A         |           8 |     NULL | NULL   | YES  | BTREE      |         |               |
| test_innodb_lock |          1 | test_innodb_lock_b_ind |            1 | b           | A         |           9 |     NULL | NULL   | YES  | BTREE      |         |               |
+------------------+------------+------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)
123456789101112131415161718192021222324
```

**行锁定基本演示**（两个客户端更新同一行记录）

![img](https://img-blog.csdnimg.cn/img_convert/237f4f1d2a298eff8584a57ed19ebb80.png)

## 58_行锁演示答疑补充

![img](https://img-blog.csdnimg.cn/img_convert/4d6cc9a4f1f62dfd0e55c08b045708f7.png)

## 59_索引失效行锁变表锁

无索引行锁升级为表锁

![img](https://img-blog.csdnimg.cn/img_convert/8465bd3028f93033eeb3b3015ae99f48.png)

## 60_间隙锁危害

![img](https://img-blog.csdnimg.cn/img_convert/dbac29504d199437dec11a528c36a211.png)

**什么是间隙锁**

当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的索引项加锁，对于键值在条件范围内但**并不存在的**记录，叫做“间隙（GAP）”。

InnoDB也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁（Next-Key锁）。

**危害**

因为Query执行过程中通过过范围查找的话，他会锁定整个范围内所有的索引键值，即使这个键值并不存在。

间隙锁有一个比较致命的弱点，就是当锁定一个范围键值之后，即使某些不存在的键值也会被无辜的锁定，而造成在锁定的时候无法插入锁定键值范围内的任何数据。在某些场景下这可能会对性能造成很大的危害。

## 61_如何锁定一行

面试：如何锁定一行？begin…commit

![img](https://img-blog.csdnimg.cn/img_convert/bd3729f61f5a72a2368fb130210738fc.png)

## 62_行锁总结与页锁

Innodb存储引擎由于实现了行级锁定，虽然在锁定机制的实现方面所带来的性能损耗可能比表级锁定会要更高一些，但是在整体并发处理能力方面要远远优于MyISAM的表级锁定的。当系统并发量较高的时候，Innodb的整体性能和MylISAM相比就会有比较明显的优势了。

但是，Innodb的行级锁定同样也有其脆弱的一面，当我们使用不当的时候，可能会让Innodb的整体性能表现不仅不能比MyISAM高，甚至可能会更差。

**行锁分析**

如何分析行锁定
通过检查lnnoDB_row_lock状态变量来分析系统上的行锁的争夺情况

```sql
mysql> show status like 'innodb_row_lock%';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| Innodb_row_lock_current_waits | 0     |
| Innodb_row_lock_time          | 0     |
| Innodb_row_lock_time_avg      | 0     |
| Innodb_row_lock_time_max      | 0     |
| Innodb_row_lock_waits         | 0     |
+-------------------------------+-------+
5 rows in set (0.00 sec)
1234567891011
```

对各个状态量的说明如下:

- Innodb_row_lock_current_waits：当前正在等待锁定的数量；
- Innodb_row_lock_time：从系统启动到现在锁定总时间长度；
- Innodb_row_lock_time_avg：每次等待所花平均时间；
- Innodb_row_lock_time_max：从系统启动到现在等待最常的一次所花的时间；
- Innodb_row_lock_waits：系统启动后到现在总共等待的次数；

对于这5个状态变量，比较重要的主要是

- Innodb_row_lock_time_avg（等待平均时长）
- lnnodb_row_lock_waits（等待总次数）
- lnnodb_row_lock_time（等待总时长）这三项。

尤其是当等待次数很高，而且每次等待时长也不小的时候，我们就需要分析系统中为什么会有如此多的等待，然后根据分析结果着手指定优化计划。

**优化建议**

- 尽可能让所有数据检索都通过索引来完成，避免无索引行锁升级为表锁。
- 合理设计索引，尽量缩小锁的范围
- 尽可能较少检索条件，避免间隙锁
- 尽量控制事务大小，减少锁定资源量和时间长度
- 尽可能低级别事务隔离

**页锁**

开销和加锁时间界于表锁和行锁之间;会出现死锁;锁定粒度界于表锁和行锁之间，并发度一般。（了解一下即可）

# 五、主从复制

## 63_主从复制

**复制的基本原理**

slave会从master读取binlog来进行数据同步

![img](https://img-blog.csdnimg.cn/img_convert/4b98290223b24a65a415b66ff5bdc310.png)

三步骤+原理图

**MySQL复制过程分成三步**

1. master将改变记录到二进制日志(binary log)。这些记录过程叫做二进制日志事件，binary log events;
2. slave将master的binary log events拷贝到它的中继日志(relay log) ;
3. slave重做中继日志中的事件，将改变应用到自己的数据库中。MySQL复制是异步的且串行化的

**复制的基本原则**

- 每个slave只有一个master
- 每个slave只能有一个唯一的服务器ID
- 每个master可以有多个salve

复制的最大问题是**延迟**。

### 一主一从常见配置

**mysql版本一致且后台以服务运行**

**主从都配置在[mysqld]结点下，都是小写**

**主机修改my.ini配置文件**

1. [必须]主服务器唯一ID
   1. server-id=1
2. [必须]启用二进制日志
   1. log-bin=自己本地的路径/mysqlbin
   2. log-bin=D:/devSoft/MySQLServer5.5/data/mysqlbin
3. [可选]启用错误日志
   1. log-err=自己本地的路径/mysqlerr
   2. log-err=D:/devSoft/MySQLServer5.5/data/mysqlerr
4. [可选]根目录
   1. basedir=“自己本地路径”
   2. basedir=“D:/devSoft/MySQLServer5.5/”
5. [可选]临时目录
   1. tmpdir=“自己本地路径”
   2. tmpdir=“D:/devSoft/MySQLServer5.5/”
6. [可选]数据目录
   1. datadir=“自己本地路径/Data/”
   2. datadir=“D:/devSoft/MySQLServer5.5/Data/”
7. 主机，读写都可以
   1. read-only=O
8. [可选]设置不要复制的数据库
   1. binlog-ignore-db=mysql
9. [可选]设置需要复制的数据库
   1. binlog-do-db=需要复制的主数据库名字

**从机修改my.cnf配置文件**

- [必须]从服务器唯一ID
- [可选]启用二进制日志

**配置文件，请主机+从机都重启后台mysql服务**

- service mysql stop
- service mysql start

**主机从机都关闭防火墙**

- windows手动关闭
- 关闭虚拟机linux防火墙 service iptables stop

**在Windows主机上建立帐户并授权slave**

- GRANT REPLICATION SLAVE ON *.* TO ‘zhangsan’@‘从机器数据库IP’ IDENTIFIED BY ‘123456’;
- flush privileges;//刷新
- 查询master的状态
  - show master status;
  - 记录下File和Position的值

![img](https://img-blog.csdnimg.cn/img_convert/4ce75527d13519074bf7674e205524a3.png)

- 执行完此步骤后不要再操作主服务器MYSQL，防止主服务器状态值变化

**在Linux从机上配置需要复制的主机**

- CHANGE MASTER TO MASTER_HOST=’主机
  IP’, MASTER_USER=‘zhangsan’, MASTER_PASSWORD=’123456’, MASTER_LOG_FILE='File名字’, MASTER_LOG_POS=Position数字;
- 启动从服务器复制功能
  - start slave;
- show slave status\G
  - 下面两个参数都是Yes，则说明主从配置成功!
  - Slave_IO_Running：Yes
  - Slave_SQL_Running：Yes

**主机新建库、新建表、insert记录，从机复制**

**如何停止从服务复制功能**

- stop slave;

[![CSDN首页](https://img-home.csdnimg.cn/images/20201124032511.png)](https://www.csdn.net/)

- [博客](https://blog.csdn.net/)
- [专栏课程](https://edu.csdn.net/)
- [下载](https://download.csdn.net/)
- [问答](https://ask.csdn.net/)
- [社区](https://www.csdn.net/c/)
- [插件 ![img](https://img-home.csdnimg.cn/images/20210806113404.gif)](https://so.csdn.net/marketing/download.html?invitationCode=U23CJF4&from=0802toobar)
- [认证](https://ac.csdn.net/?short_code=13271b93)

 搜索

[登录/注册](https://passport.csdn.net/account/login)

[会员中心](https://mall.csdn.net/vip)

[收藏](https://i.csdn.net/#/user-center/collection-list?type=1)

[动态](https://blink.csdn.net/)

[创作](https://mp.csdn.net/)

# MySQL高级建表语句

## P13

- tbl_emp
- tbl_dept

```sql
CREATE TABLE `tbl_emp` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`name` varchar(20) DEFAULT NULL,
`deptId` int(11) DEFAULT NULL,
PRIMARY KEY (`id`) ,
KEY `fk_dept_id`(`deptId`)
)ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8;

CREATE TABLE `tbl_dept` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`deptName` varchar(30) DEFAULT NULL,
`locAdd` varchar(40) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8;

insert into tbl_dept(deptName,locAdd) values('RD',11);
insert into tbl_dept(deptName,locAdd) values('HR',12);
insert into tbl_dept(deptName,locAdd) values('MK',13);
insert into tbl_dept(deptName,locAdd) values('MIS',14);
insert into tbl_dept(deptName,locAdd) values('FD',15);

insert into tbl_emp(NAME,deptId) values('z3',1);
insert into tbl_emp(NAME,deptId) values('z4',1);
insert into tbl_emp(NAME,deptId) values('z5',1);
insert into tbl_emp(NAME,deptId) values('w5',2);
insert into tbl_emp(NAME,deptId) values('w6',2);
insert into tbl_emp(NAME,deptId) values('s7',3);
insert into tbl_emp(NAME,deptId) values('s8',4);
insert into tbl_emp(NAME,deptId) values('s9',51);
1234567891011121314151617181920212223242526272829
```

## P31

- article

```sql
CREATE TABLE IF NOT EXISTS `article`(
`id` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`author_id` INT (10) UNSIGNED NOT NULL,
`category_id` INT(10) UNSIGNED NOT NULL , 
`views` INT(10) UNSIGNED NOT NULL , 
`comments` INT(10) UNSIGNED NOT NULL,
`title` VARBINARY(255) NOT NULL,
`content` TEXT NOT NULL
);

insert into `article`(author_id,category_id,views,comments,title,content) values
(1,1,1,1,'1','1'),
(2,2,2,2,'2','2'),
(1,1,3,3,'3','3');
1234567891011121314
```

## P32

- class
- book

```sql
CREATE TABLE IF NOT EXISTS `class`(
`id` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`card` INT (10) UNSIGNED NOT NULL
);
CREATE TABLE IF NOT EXISTS `book`(
`bookid` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`card` INT (10) UNSIGNED NOT NULL
);

insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));
insert into class(card) values(floor(1+(rand()*20)));

insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
insert into book(card) values(floor(1+(rand()*20)));
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950
```

## P33

- phone

```sql
CREATE TABLE IF NOT EXISTS `phone`(
`phoneid` INT(10) UNSIGNED NOT NULL PRIMARY KEY AUTO_INCREMENT,
`card` INT (10) UNSIGNED NOT NULL
)ENGINE = INNODB;

insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
insert into phone(card) values(floor(1+(rand()*20)));
12345678910111213141516171819202122232425
```

## P34

- staffs

```sql
CREATE TABLE staffs(
id INT PRIMARY KEY AUTO_INCREMENT,
`name` VARCHAR(24)NOT NULL DEFAULT'' COMMENT'姓名',
`age` INT NOT NULL DEFAULT 0 COMMENT'年龄',
`pos` VARCHAR(20) NOT NULL DEFAULT'' COMMENT'职位',
`add_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT'入职时间'
)CHARSET utf8 COMMENT'员工记录表';

insert into staffs(NAME,age,pos,add_time) values('z3',22,'manager',NOW());
insert into staffs(NAME,age,pos,add_time) values('July',23,'dev',NOW());
insert into staffs(NAME,age,pos,add_time) values('2000',23,'dev',NOW());
1234567891011
```

## P40

- tbl_user

```sql
CREATE TABLE tbl_user(
`id` INT(11) NOT NULL AUTO_INCREMENT,
`name` VARCHAR(20) DEFAULT NULL,
`age`INT(11) DEFAULT NULL,
`email` VARCHAR(20) DEFAULT NULL,
PRIMARY KEY(`id`)
)ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

insert into tbl_user(NAME,age,email) values('1aa1',21,'b@163.com');
insert into tbl_user(NAME,age,email) values('2aa2',222,'a@163.com');
insert into tbl_user(NAME,age,email) values('3aa3',265,'c@163.com');
insert into tbl_user(NAME,age,email) values('4aa4',21,'d@163.com');
123456789101112
```

## P44

- test03

```sql
create table test03(
id int primary key not null auto_increment,
c1 char(10),
c2 char(10),
c3 char(10),
c4 char(10),
c5 char(10)
);

insert into test03(c1,c2,c3,c4,c5) values('a1','a2','a3','a4','a5');
insert into test03(c1,c2,c3,c4,c5) values('b1','b2','b3','b4','b5');
insert into test03(c1,c2,c3,c4,c5) values('c1','c2','c3','c4','c5');
insert into test03(c1,c2,c3,c4,c5) values('d1','d2','d3','d4','d5');
insert into test03(c1,c2,c3,c4,c5) values('e1','e2','e3','e4','e5');
1234567891011121314
```

## P48

- tblA

```sql
create table tblA(
age int,
birth timestamp not null
);

insert into tblA(age,birth) values(22,now());
insert into tblA(age,birth) values(23,now());
insert into tblA(age,birth) values(24,now());
12345678
```

## P50

```sql
create table dept(
id int unsigned primary key auto_increment,
deptno mediumint unsigned not null default 0,
dname varchar(20) not null default "",
loc varchar(13) not null default ""
)engine=innodb default charset=GBK;

CREATE TABLE emp(
id int unsigned primary key auto_increment,
empno mediumint unsigned not null default 0,
ename varchar(20) not null default "",
job varchar(9) not null default "",
mgr mediumint unsigned not null default 0,
hiredate date not null,
sal decimal(7,2) not null,
comm decimal(7,2) not null,
deptno mediumint unsigned not null default 0
)ENGINE=INNODB DEFAULT CHARSET=GBK;
//函数
delimiter $$
create function ran_string(n int) returns varchar(255)
begin
declare chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
declare return_str varchar(255) default '';
declare i int default 0;
while i < n do
set return_str = concat(return_str,substring(chars_str,floor(1+rand()*52),1));
set i=i+1;
end while;
return return_str;
end $$
//函数
delimiter $$
create function rand_num() returns int(5)
begin
declare i int default 0;
set i=floor(100+rand()*10);
return i;
end $$
//存储过程
delimiter $$ 
create procedure insert_emp(in start int(10),in max_num int(10))
begin
declare i int default 0;
set autocommit = 0;
repeat
set i = i+1;
insert into emp(empno,ename,job,mgr,hiredate,sal,comm,deptno) values((start+i),ran_string(6),'salesman',0001,curdate(),2000,400,rand_num());
until i=max_num
end repeat;
commit;
end $$
//存储过程
delimiter $$ 
create procedure insert_dept(in start int(10),in max_num int(10))
begin
declare i int default 0;
set autocommit = 0;
repeat
set i = i+1;
insert into dept(deptno,dname,loc) values((start+i),ran_string(10),ran_string(8));
until i=max_num
end repeat;
commit;
end $$
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465
```

## P54

- mylock

```sql
create table mylock (
id int not null primary key auto_increment,
name varchar(20) default ''
) engine myisam;

insert into mylock(name) values('a');
insert into mylock(name) values('b');
insert into mylock(name) values('c');
insert into mylock(name) values('d');
insert into mylock(name) values('e');
12345678910
```

## P57

- test_innodb_lock

```sql
CREATE TABLE test_innodb_lock (
a INT(11),
b VARCHAR(16)
)ENGINE=INNODB;
1234
```