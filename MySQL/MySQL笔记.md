# MySQL6天笔记——day01

# MySQL数据库

> 这是JavaSE30天笔记的后续笔记，MySQL笔记，总共分为6天。、
> 全部SQL源文件链接：https://pan.baidu.com/s/1wc51qkVetSRybFzcIYGBIg
> 提取码：3wpt

## 整体大纲

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629175718971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

## 与MySQL的第一次亲密接触

### 为什么学习数据库？

- 实现数据持久化
- 使用完整的管理系统统一管理，易于查询

### 数据库的相关概念

- DB：数据库（database）：存储数据的“仓库”。它保存了一系列有组织的数据。
- DBMS
  - 数据库管理系统（Database Management System）。数据库是通过DBMS创建和操作的容器
  - 常见的数据库管理系统：MySQL、Oracle、DB2、SqlServer等。
- SQL
  - 结构化查询语言（Structure Query Language）：专门用来与数据库通信的语言。
  - SQL的优点：①简单易学；②不是某个特定数据库供应商专有的语言，几乎所有DBMS都支持SQL；③虽然简单，但实际上是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作。

> 以上三者之间的关系：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703091454466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

------

### 数据库存储数据的特点

- 1、将数据放到表中，表再放到库中
- 2、一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己。表名具有唯一性。
- 3、表具有一些特性，这些特性定义了数据在表中如何存储，类似java中 “类”的设计。
- 4、表由列组成，我们也称为字段。所有表都是由一个或多个列组成的，每一列类似java 中的”属性”。
- 5、表中的数据是按行存储的，每一行类似于java中的“对象”。

### MySQL软件

- 介绍
  - MySQL数据库隶属于MySQLAB公司，总部位于瑞典，后被oracle收购。
  - 优点：①成本低：开放源代码，一般可以免费试用；②简单：很容易安装和使用；③性能高：执行很快。
- 安装
  - DBMS分为两类：①基于共享文件系统的DBMS （Access）；②基于客户机——服务器的DBMS（MySQL、Oracle、SqlServer）。
  - 版本可分为：社区版（免费），企业版（收费）。
  - Windows平台下下载：http://dev.mysql.com/downloads/mysql
  - `完整安装卸载步骤`：https://blog.csdn.net/Y_BlueBlack/article/details/81433401
- 启动和停止MySQL服务
  - 方式一：右击此电脑—管理—服务—启动或停止MySQL服务
  - 方式二：

```sql
启动：net start mysql服务名
停止：net stop mysql服务名
```

- MySQL服务端的登录和退出

```sql
登录
mysql–h 主机名–u用户名–p密码
例：mysql -h localhost -u root -proot

退出
exit或ctrl+C
```

- MySQL常见命令介绍

```sql
进入mysql, 在命令行中输入
mysql–uroot–p####  (其中：####表示密码）
例：mysql -uroot -proot

查看mysql中有哪些个数据库
show databases;

新建一个数据库
create database 数据库名;
例：create database book;

选择一个数据库
use 数据库名称;
例：use test;

查询数据表
show tables;

查看指定的数据库中有哪些数据表
show tables from 数据库名;
例：show tables from mysql;

查询当前所在数据库
select database();

新建一个数据表
create table math(
id int,
name varchar(20));

查看表的结构
desc 表名;
例：desc math;

查看表中的所有记录
select * from 表名;
例： select * from math;

向表中插入记录
insert into 表名(列名列表) values(列对应的值的列表);
注意：插入varchar或date 型的数据要用单引号引起来
例：insert into math (id,name) values(1,"ton");

修改记录
update 表名set 列1 = 列1的值, 列2 = 列2的值where ..
例：update math set name="wugang" where id=1;

删除记录
delete from 表名 where ...
例：delete from math where id=1;

删除数据表
drop table 表名;
例：drop table math;
```

- 查看MySQL服务端版本

```sql
登录到mysql服务端
select version();

没有登录到mysql服务端
在cmd命令界面输入：mysql --version
12345
```

- MySQL的语法规范
  - 不区分大小写,但建议关键字大写，表名、列名小写
  - 每句话用;或\g结尾，最好用分号结尾
  - 每条命令根据需要，可以进行缩进或换行
  - 注释：①单行注释：#注释文字；②单行注释：-- 注释文字；③多行注释：/* 注释文字 */
- 图形化用户界面客户端
  - 介绍：主要分为两种——SQLyog 12、Navicat 12 for mysql
  - 安装：https://blog.csdn.net/lihua5419/article/details/73881837/
  - 卸载：方法一：控制面板选中SQLyog直接卸载；方法二：卸载时担心注册表清除不干净,百度geek下载工具，下载免费版即可。下载后，放到桌面，双击后可直接选中要卸载的软件进行卸载,最后可根据选择是否清除注册表。

## SQL语言的分类

> 对于这个网上有三种、四种、五种、六种很多，但综合网上的来说，是5种！！因为大部分用到的是DQL和DML，偶尔会用到DDL，一般的开发人员很少会用到DCL，而且本教程也不会过多赘述，毕竟只有6天。

- **DQL**（Data Query Language）：数据查询语言，用于检索数据库中的数据，主要是SELECT语句；
- **DML**（Data Manipulation Language)：数据操纵语言，用于改变数据库中的数据，主要包括INSERT、UPDATE和DELETE语句；
- **DDL**（Data Definition Language)：数据定义语言，用于库和表的创建、修改、删除。主要包括CREATE、DROP、ALTER语句；
- DCL（Data Control Language)：数据控制语言，用于定义用户的访问权限和安全级别。主要包括GRANT和REVOKE语句；
- **TCL**（Transaction Control Language)：事务控制语言，用于维护数据的一致性，包括COMMIT、ROLLBACK和SAVEPOINT语句。

## 后文需要的数据库数据表文件

- myemployees文件

```sql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.5.15 : Database - myemployees
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`myemployees` /*!40100 DEFAULT CHARACTER SET gb2312 */;

USE `myemployees`;

/*Table structure for table `departments` */

DROP TABLE IF EXISTS `departments`;

CREATE TABLE `departments` (
  `department_id` int(4) NOT NULL AUTO_INCREMENT,
  `department_name` varchar(3) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `location_id` int(4) DEFAULT NULL,
  PRIMARY KEY (`department_id`),
  KEY `loc_id_fk` (`location_id`),
  CONSTRAINT `loc_id_fk` FOREIGN KEY (`location_id`) REFERENCES `locations` (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=271 DEFAULT CHARSET=gb2312;

/*Data for the table `departments` */

insert  into `departments`(`department_id`,`department_name`,`manager_id`,`location_id`) values (10,'Adm',200,1700),(20,'Mar',201,1800),(30,'Pur',114,1700),(40,'Hum',203,2400),(50,'Shi',121,1500),(60,'IT',103,1400),(70,'Pub',204,2700),(80,'Sal',145,2500),(90,'Exe',100,1700),(100,'Fin',108,1700),(110,'Acc',205,1700),(120,'Tre',NULL,1700),(130,'Cor',NULL,1700),(140,'Con',NULL,1700),(150,'Sha',NULL,1700),(160,'Ben',NULL,1700),(170,'Man',NULL,1700),(180,'Con',NULL,1700),(190,'Con',NULL,1700),(200,'Ope',NULL,1700),(210,'IT ',NULL,1700),(220,'NOC',NULL,1700),(230,'IT ',NULL,1700),(240,'Gov',NULL,1700),(250,'Ret',NULL,1700),(260,'Rec',NULL,1700),(270,'Pay',NULL,1700);

/*Table structure for table `employees` */

DROP TABLE IF EXISTS `employees`;

CREATE TABLE `employees` (
  `employee_id` int(6) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(20) DEFAULT NULL,
  `last_name` varchar(25) DEFAULT NULL,
  `email` varchar(25) DEFAULT NULL,
  `phone_number` varchar(20) DEFAULT NULL,
  `job_id` varchar(10) DEFAULT NULL,
  `salary` double(10,2) DEFAULT NULL,
  `commission_pct` double(4,2) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `department_id` int(4) DEFAULT NULL,
  `hiredate` datetime DEFAULT NULL,
  PRIMARY KEY (`employee_id`),
  KEY `dept_id_fk` (`department_id`),
  KEY `job_id_fk` (`job_id`),
  CONSTRAINT `dept_id_fk` FOREIGN KEY (`department_id`) REFERENCES `departments` (`department_id`),
  CONSTRAINT `job_id_fk` FOREIGN KEY (`job_id`) REFERENCES `jobs` (`job_id`)
) ENGINE=InnoDB AUTO_INCREMENT=207 DEFAULT CHARSET=gb2312;

/*Data for the table `employees` */

insert  into `employees`(`employee_id`,`first_name`,`last_name`,`email`,`phone_number`,`job_id`,`salary`,`commission_pct`,`manager_id`,`department_id`,`hiredate`) values (100,'Steven','K_ing','SKING','515.123.4567','AD_PRES',24000.00,NULL,NULL,90,'1992-04-03 00:00:00'),(101,'Neena','Kochhar','NKOCHHAR','515.123.4568','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(102,'Lex','De Haan','LDEHAAN','515.123.4569','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(103,'Alexander','Hunold','AHUNOLD','590.423.4567','IT_PROG',9000.00,NULL,102,60,'1992-04-03 00:00:00'),(104,'Bruce','Ernst','BERNST','590.423.4568','IT_PROG',6000.00,NULL,103,60,'1992-04-03 00:00:00'),(105,'David','Austin','DAUSTIN','590.423.4569','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(106,'Valli','Pataballa','VPATABAL','590.423.4560','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(107,'Diana','Lorentz','DLORENTZ','590.423.5567','IT_PROG',4200.00,NULL,103,60,'1998-03-03 00:00:00'),(108,'Nancy','Greenberg','NGREENBE','515.124.4569','FI_MGR',12000.00,NULL,101,100,'1998-03-03 00:00:00'),(109,'Daniel','Faviet','DFAVIET','515.124.4169','FI_ACCOUNT',9000.00,NULL,108,100,'1998-03-03 00:00:00'),(110,'John','Chen','JCHEN','515.124.4269','FI_ACCOUNT',8200.00,NULL,108,100,'2000-09-09 00:00:00'),(111,'Ismael','Sciarra','ISCIARRA','515.124.4369','FI_ACCOUNT',7700.00,NULL,108,100,'2000-09-09 00:00:00'),(112,'Jose Manuel','Urman','JMURMAN','515.124.4469','FI_ACCOUNT',7800.00,NULL,108,100,'2000-09-09 00:00:00'),(113,'Luis','Popp','LPOPP','515.124.4567','FI_ACCOUNT',6900.00,NULL,108,100,'2000-09-09 00:00:00'),(114,'Den','Raphaely','DRAPHEAL','515.127.4561','PU_MAN',11000.00,NULL,100,30,'2000-09-09 00:00:00'),(115,'Alexander','Khoo','AKHOO','515.127.4562','PU_CLERK',3100.00,NULL,114,30,'2000-09-09 00:00:00'),(116,'Shelli','Baida','SBAIDA','515.127.4563','PU_CLERK',2900.00,NULL,114,30,'2000-09-09 00:00:00'),(117,'Sigal','Tobias','STOBIAS','515.127.4564','PU_CLERK',2800.00,NULL,114,30,'2000-09-09 00:00:00'),(118,'Guy','Himuro','GHIMURO','515.127.4565','PU_CLERK',2600.00,NULL,114,30,'2000-09-09 00:00:00'),(119,'Karen','Colmenares','KCOLMENA','515.127.4566','PU_CLERK',2500.00,NULL,114,30,'2000-09-09 00:00:00'),(120,'Matthew','Weiss','MWEISS','650.123.1234','ST_MAN',8000.00,NULL,100,50,'2004-02-06 00:00:00'),(121,'Adam','Fripp','AFRIPP','650.123.2234','ST_MAN',8200.00,NULL,100,50,'2004-02-06 00:00:00'),(122,'Payam','Kaufling','PKAUFLIN','650.123.3234','ST_MAN',7900.00,NULL,100,50,'2004-02-06 00:00:00'),(123,'Shanta','Vollman','SVOLLMAN','650.123.4234','ST_MAN',6500.00,NULL,100,50,'2004-02-06 00:00:00'),(124,'Kevin','Mourgos','KMOURGOS','650.123.5234','ST_MAN',5800.00,NULL,100,50,'2004-02-06 00:00:00'),(125,'Julia','Nayer','JNAYER','650.124.1214','ST_CLERK',3200.00,NULL,120,50,'2004-02-06 00:00:00'),(126,'Irene','Mikkilineni','IMIKKILI','650.124.1224','ST_CLERK',2700.00,NULL,120,50,'2004-02-06 00:00:00'),(127,'James','Landry','JLANDRY','650.124.1334','ST_CLERK',2400.00,NULL,120,50,'2004-02-06 00:00:00'),(128,'Steven','Markle','SMARKLE','650.124.1434','ST_CLERK',2200.00,NULL,120,50,'2004-02-06 00:00:00'),(129,'Laura','Bissot','LBISSOT','650.124.5234','ST_CLERK',3300.00,NULL,121,50,'2004-02-06 00:00:00'),(130,'Mozhe','Atkinson','MATKINSO','650.124.6234','ST_CLERK',2800.00,NULL,121,50,'2004-02-06 00:00:00'),(131,'James','Marlow','JAMRLOW','650.124.7234','ST_CLERK',2500.00,NULL,121,50,'2004-02-06 00:00:00'),(132,'TJ','Olson','TJOLSON','650.124.8234','ST_CLERK',2100.00,NULL,121,50,'2004-02-06 00:00:00'),(133,'Jason','Mallin','JMALLIN','650.127.1934','ST_CLERK',3300.00,NULL,122,50,'2004-02-06 00:00:00'),(134,'Michael','Rogers','MROGERS','650.127.1834','ST_CLERK',2900.00,NULL,122,50,'2002-12-23 00:00:00'),(135,'Ki','Gee','KGEE','650.127.1734','ST_CLERK',2400.00,NULL,122,50,'2002-12-23 00:00:00'),(136,'Hazel','Philtanker','HPHILTAN','650.127.1634','ST_CLERK',2200.00,NULL,122,50,'2002-12-23 00:00:00'),(137,'Renske','Ladwig','RLADWIG','650.121.1234','ST_CLERK',3600.00,NULL,123,50,'2002-12-23 00:00:00'),(138,'Stephen','Stiles','SSTILES','650.121.2034','ST_CLERK',3200.00,NULL,123,50,'2002-12-23 00:00:00'),(139,'John','Seo','JSEO','650.121.2019','ST_CLERK',2700.00,NULL,123,50,'2002-12-23 00:00:00'),(140,'Joshua','Patel','JPATEL','650.121.1834','ST_CLERK',2500.00,NULL,123,50,'2002-12-23 00:00:00'),(141,'Trenna','Rajs','TRAJS','650.121.8009','ST_CLERK',3500.00,NULL,124,50,'2002-12-23 00:00:00'),(142,'Curtis','Davies','CDAVIES','650.121.2994','ST_CLERK',3100.00,NULL,124,50,'2002-12-23 00:00:00'),(143,'Randall','Matos','RMATOS','650.121.2874','ST_CLERK',2600.00,NULL,124,50,'2002-12-23 00:00:00'),(144,'Peter','Vargas','PVARGAS','650.121.2004','ST_CLERK',2500.00,NULL,124,50,'2002-12-23 00:00:00'),(145,'John','Russell','JRUSSEL','011.44.1344.429268','SA_MAN',14000.00,0.40,100,80,'2002-12-23 00:00:00'),(146,'Karen','Partners','KPARTNER','011.44.1344.467268','SA_MAN',13500.00,0.30,100,80,'2002-12-23 00:00:00'),(147,'Alberto','Errazuriz','AERRAZUR','011.44.1344.429278','SA_MAN',12000.00,0.30,100,80,'2002-12-23 00:00:00'),(148,'Gerald','Cambrault','GCAMBRAU','011.44.1344.619268','SA_MAN',11000.00,0.30,100,80,'2002-12-23 00:00:00'),(149,'Eleni','Zlotkey','EZLOTKEY','011.44.1344.429018','SA_MAN',10500.00,0.20,100,80,'2002-12-23 00:00:00'),(150,'Peter','Tucker','PTUCKER','011.44.1344.129268','SA_REP',10000.00,0.30,145,80,'2014-03-05 00:00:00'),(151,'David','Bernstein','DBERNSTE','011.44.1344.345268','SA_REP',9500.00,0.25,145,80,'2014-03-05 00:00:00'),(152,'Peter','Hall','PHALL','011.44.1344.478968','SA_REP',9000.00,0.25,145,80,'2014-03-05 00:00:00'),(153,'Christopher','Olsen','COLSEN','011.44.1344.498718','SA_REP',8000.00,0.20,145,80,'2014-03-05 00:00:00'),(154,'Nanette','Cambrault','NCAMBRAU','011.44.1344.987668','SA_REP',7500.00,0.20,145,80,'2014-03-05 00:00:00'),(155,'Oliver','Tuvault','OTUVAULT','011.44.1344.486508','SA_REP',7000.00,0.15,145,80,'2014-03-05 00:00:00'),(156,'Janette','K_ing','JKING','011.44.1345.429268','SA_REP',10000.00,0.35,146,80,'2014-03-05 00:00:00'),(157,'Patrick','Sully','PSULLY','011.44.1345.929268','SA_REP',9500.00,0.35,146,80,'2014-03-05 00:00:00'),(158,'Allan','McEwen','AMCEWEN','011.44.1345.829268','SA_REP',9000.00,0.35,146,80,'2014-03-05 00:00:00'),(159,'Lindsey','Smith','LSMITH','011.44.1345.729268','SA_REP',8000.00,0.30,146,80,'2014-03-05 00:00:00'),(160,'Louise','Doran','LDORAN','011.44.1345.629268','SA_REP',7500.00,0.30,146,80,'2014-03-05 00:00:00'),(161,'Sarath','Sewall','SSEWALL','011.44.1345.529268','SA_REP',7000.00,0.25,146,80,'2014-03-05 00:00:00'),(162,'Clara','Vishney','CVISHNEY','011.44.1346.129268','SA_REP',10500.00,0.25,147,80,'2014-03-05 00:00:00'),(163,'Danielle','Greene','DGREENE','011.44.1346.229268','SA_REP',9500.00,0.15,147,80,'2014-03-05 00:00:00'),(164,'Mattea','Marvins','MMARVINS','011.44.1346.329268','SA_REP',7200.00,0.10,147,80,'2014-03-05 00:00:00'),(165,'David','Lee','DLEE','011.44.1346.529268','SA_REP',6800.00,0.10,147,80,'2014-03-05 00:00:00'),(166,'Sundar','Ande','SANDE','011.44.1346.629268','SA_REP',6400.00,0.10,147,80,'2014-03-05 00:00:00'),(167,'Amit','Banda','ABANDA','011.44.1346.729268','SA_REP',6200.00,0.10,147,80,'2014-03-05 00:00:00'),(168,'Lisa','Ozer','LOZER','011.44.1343.929268','SA_REP',11500.00,0.25,148,80,'2014-03-05 00:00:00'),(169,'Harrison','Bloom','HBLOOM','011.44.1343.829268','SA_REP',10000.00,0.20,148,80,'2014-03-05 00:00:00'),(170,'Tayler','Fox','TFOX','011.44.1343.729268','SA_REP',9600.00,0.20,148,80,'2014-03-05 00:00:00'),(171,'William','Smith','WSMITH','011.44.1343.629268','SA_REP',7400.00,0.15,148,80,'2014-03-05 00:00:00'),(172,'Elizabeth','Bates','EBATES','011.44.1343.529268','SA_REP',7300.00,0.15,148,80,'2014-03-05 00:00:00'),(173,'Sundita','Kumar','SKUMAR','011.44.1343.329268','SA_REP',6100.00,0.10,148,80,'2014-03-05 00:00:00'),(174,'Ellen','Abel','EABEL','011.44.1644.429267','SA_REP',11000.00,0.30,149,80,'2014-03-05 00:00:00'),(175,'Alyssa','Hutton','AHUTTON','011.44.1644.429266','SA_REP',8800.00,0.25,149,80,'2014-03-05 00:00:00'),(176,'Jonathon','Taylor','JTAYLOR','011.44.1644.429265','SA_REP',8600.00,0.20,149,80,'2014-03-05 00:00:00'),(177,'Jack','Livingston','JLIVINGS','011.44.1644.429264','SA_REP',8400.00,0.20,149,80,'2014-03-05 00:00:00'),(178,'Kimberely','Grant','KGRANT','011.44.1644.429263','SA_REP',7000.00,0.15,149,NULL,'2014-03-05 00:00:00'),(179,'Charles','Johnson','CJOHNSON','011.44.1644.429262','SA_REP',6200.00,0.10,149,80,'2014-03-05 00:00:00'),(180,'Winston','Taylor','WTAYLOR','650.507.9876','SH_CLERK',3200.00,NULL,120,50,'2014-03-05 00:00:00'),(181,'Jean','Fleaur','JFLEAUR','650.507.9877','SH_CLERK',3100.00,NULL,120,50,'2014-03-05 00:00:00'),(182,'Martha','Sullivan','MSULLIVA','650.507.9878','SH_CLERK',2500.00,NULL,120,50,'2014-03-05 00:00:00'),(183,'Girard','Geoni','GGEONI','650.507.9879','SH_CLERK',2800.00,NULL,120,50,'2014-03-05 00:00:00'),(184,'Nandita','Sarchand','NSARCHAN','650.509.1876','SH_CLERK',4200.00,NULL,121,50,'2014-03-05 00:00:00'),(185,'Alexis','Bull','ABULL','650.509.2876','SH_CLERK',4100.00,NULL,121,50,'2014-03-05 00:00:00'),(186,'Julia','Dellinger','JDELLING','650.509.3876','SH_CLERK',3400.00,NULL,121,50,'2014-03-05 00:00:00'),(187,'Anthony','Cabrio','ACABRIO','650.509.4876','SH_CLERK',3000.00,NULL,121,50,'2014-03-05 00:00:00'),(188,'Kelly','Chung','KCHUNG','650.505.1876','SH_CLERK',3800.00,NULL,122,50,'2014-03-05 00:00:00'),(189,'Jennifer','Dilly','JDILLY','650.505.2876','SH_CLERK',3600.00,NULL,122,50,'2014-03-05 00:00:00'),(190,'Timothy','Gates','TGATES','650.505.3876','SH_CLERK',2900.00,NULL,122,50,'2014-03-05 00:00:00'),(191,'Randall','Perkins','RPERKINS','650.505.4876','SH_CLERK',2500.00,NULL,122,50,'2014-03-05 00:00:00'),(192,'Sarah','Bell','SBELL','650.501.1876','SH_CLERK',4000.00,NULL,123,50,'2014-03-05 00:00:00'),(193,'Britney','Everett','BEVERETT','650.501.2876','SH_CLERK',3900.00,NULL,123,50,'2014-03-05 00:00:00'),(194,'Samuel','McCain','SMCCAIN','650.501.3876','SH_CLERK',3200.00,NULL,123,50,'2014-03-05 00:00:00'),(195,'Vance','Jones','VJONES','650.501.4876','SH_CLERK',2800.00,NULL,123,50,'2014-03-05 00:00:00'),(196,'Alana','Walsh','AWALSH','650.507.9811','SH_CLERK',3100.00,NULL,124,50,'2014-03-05 00:00:00'),(197,'Kevin','Feeney','KFEENEY','650.507.9822','SH_CLERK',3000.00,NULL,124,50,'2014-03-05 00:00:00'),(198,'Donald','OConnell','DOCONNEL','650.507.9833','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(199,'Douglas','Grant','DGRANT','650.507.9844','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(200,'Jennifer','Whalen','JWHALEN','515.123.4444','AD_ASST',4400.00,NULL,101,10,'2016-03-03 00:00:00'),(201,'Michael','Hartstein','MHARTSTE','515.123.5555','MK_MAN',13000.00,NULL,100,20,'2016-03-03 00:00:00'),(202,'Pat','Fay','PFAY','603.123.6666','MK_REP',6000.00,NULL,201,20,'2016-03-03 00:00:00'),(203,'Susan','Mavris','SMAVRIS','515.123.7777','HR_REP',6500.00,NULL,101,40,'2016-03-03 00:00:00'),(204,'Hermann','Baer','HBAER','515.123.8888','PR_REP',10000.00,NULL,101,70,'2016-03-03 00:00:00'),(205,'Shelley','Higgins','SHIGGINS','515.123.8080','AC_MGR',12000.00,NULL,101,110,'2016-03-03 00:00:00'),(206,'William','Gietz','WGIETZ','515.123.8181','AC_ACCOUNT',8300.00,NULL,205,110,'2016-03-03 00:00:00');

/*Table structure for table `jobs` */

DROP TABLE IF EXISTS `jobs`;

CREATE TABLE `jobs` (
  `job_id` varchar(10) NOT NULL,
  `job_title` varchar(35) DEFAULT NULL,
  `min_salary` int(6) DEFAULT NULL,
  `max_salary` int(6) DEFAULT NULL,
  PRIMARY KEY (`job_id`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Data for the table `jobs` */

insert  into `jobs`(`job_id`,`job_title`,`min_salary`,`max_salary`) values ('AC_ACCOUNT','Public Accountant',4200,9000),('AC_MGR','Accounting Manager',8200,16000),('AD_ASST','Administration Assistant',3000,6000),('AD_PRES','President',20000,40000),('AD_VP','Administration Vice President',15000,30000),('FI_ACCOUNT','Accountant',4200,9000),('FI_MGR','Finance Manager',8200,16000),('HR_REP','Human Resources Representative',4000,9000),('IT_PROG','Programmer',4000,10000),('MK_MAN','Marketing Manager',9000,15000),('MK_REP','Marketing Representative',4000,9000),('PR_REP','Public Relations Representative',4500,10500),('PU_CLERK','Purchasing Clerk',2500,5500),('PU_MAN','Purchasing Manager',8000,15000),('SA_MAN','Sales Manager',10000,20000),('SA_REP','Sales Representative',6000,12000),('SH_CLERK','Shipping Clerk',2500,5500),('ST_CLERK','Stock Clerk',2000,5000),('ST_MAN','Stock Manager',5500,8500);

/*Table structure for table `locations` */

DROP TABLE IF EXISTS `locations`;

CREATE TABLE `locations` (
  `location_id` int(11) NOT NULL AUTO_INCREMENT,
  `street_address` varchar(40) DEFAULT NULL,
  `postal_code` varchar(12) DEFAULT NULL,
  `city` varchar(30) DEFAULT NULL,
  `state_province` varchar(25) DEFAULT NULL,
  `country_id` varchar(2) DEFAULT NULL,
  PRIMARY KEY (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=3201 DEFAULT CHARSET=gb2312;

/*Data for the table `locations` */

insert  into `locations`(`location_id`,`street_address`,`postal_code`,`city`,`state_province`,`country_id`) values (1000,'1297 Via Cola di Rie','00989','Roma',NULL,'IT'),(1100,'93091 Calle della Testa','10934','Venice',NULL,'IT'),(1200,'2017 Shinjuku-ku','1689','Tokyo','Tokyo Prefecture','JP'),(1300,'9450 Kamiya-cho','6823','Hiroshima',NULL,'JP'),(1400,'2014 Jabberwocky Rd','26192','Southlake','Texas','US'),(1500,'2011 Interiors Blvd','99236','South San Francisco','California','US'),(1600,'2007 Zagora St','50090','South Brunswick','New Jersey','US'),(1700,'2004 Charade Rd','98199','Seattle','Washington','US'),(1800,'147 Spadina Ave','M5V 2L7','Toronto','Ontario','CA'),(1900,'6092 Boxwood St','YSW 9T2','Whitehorse','Yukon','CA'),(2000,'40-5-12 Laogianggen','190518','Beijing',NULL,'CN'),(2100,'1298 Vileparle (E)','490231','Bombay','Maharashtra','IN'),(2200,'12-98 Victoria Street','2901','Sydney','New South Wales','AU'),(2300,'198 Clementi North','540198','Singapore',NULL,'SG'),(2400,'8204 Arthur St',NULL,'London',NULL,'UK'),(2500,'Magdalen Centre, The Oxford Science Park','OX9 9ZB','Oxford','Oxford','UK'),(2600,'9702 Chester Road','09629850293','Stretford','Manchester','UK'),(2700,'Schwanthalerstr. 7031','80925','Munich','Bavaria','DE'),(2800,'Rua Frei Caneca 1360 ','01307-002','Sao Paulo','Sao Paulo','BR'),(2900,'20 Rue des Corps-Saints','1730','Geneva','Geneve','CH'),(3000,'Murtenstrasse 921','3095','Bern','BE','CH'),(3100,'Pieter Breughelstraat 837','3029SK','Utrecht','Utrecht','NL'),(3200,'Mariano Escobedo 9991','11932','Mexico City','Distrito Federal,','MX');

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103
```

- girls文件

```sql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.7.18-log : Database - girls
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`girls` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `girls`;

/*Table structure for table `admin` */

DROP TABLE IF EXISTS `admin`;

CREATE TABLE `admin` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(10) NOT NULL,
  `password` VARCHAR(10) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

/*Data for the table `admin` */

INSERT  INTO `admin`(`id`,`username`,`password`) VALUES (1,'john','8888'),(2,'lyt','6666');

/*Table structure for table `beauty` */

DROP TABLE IF EXISTS `beauty`;

CREATE TABLE `beauty` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(50) NOT NULL,
  `sex` CHAR(1) DEFAULT '女',
  `borndate` DATETIME DEFAULT '1987-01-01 00:00:00',
  `phone` VARCHAR(11) NOT NULL,
  `photo` BLOB,
  `boyfriend_id` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8;

/*Data for the table `beauty` */

INSERT  INTO `beauty`(`id`,`name`,`sex`,`borndate`,`phone`,`photo`,`boyfriend_id`) VALUES (1,'柳岩','女','1988-02-03 00:00:00','18209876577',NULL,8),(2,'苍老师','女','1987-12-30 00:00:00','18219876577',NULL,9),(3,'Angelababy','女','1989-02-03 00:00:00','18209876567',NULL,3),(4,'热巴','女','1993-02-03 00:00:00','18209876579',NULL,2),(5,'周冬雨','女','1992-02-03 00:00:00','18209179577',NULL,9),(6,'周芷若','女','1988-02-03 00:00:00','18209876577',NULL,1),(7,'岳灵珊','女','1987-12-30 00:00:00','18219876577',NULL,9),(8,'小昭','女','1989-02-03 00:00:00','18209876567',NULL,1),(9,'双儿','女','1993-02-03 00:00:00','18209876579',NULL,9),(10,'王语嫣','女','1992-02-03 00:00:00','18209179577',NULL,4),(11,'夏雪','女','1993-02-03 00:00:00','18209876579',NULL,9),(12,'赵敏','女','1992-02-03 00:00:00','18209179577',NULL,1);

/*Table structure for table `boys` */

DROP TABLE IF EXISTS `boys`;

CREATE TABLE `boys` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `boyName` VARCHAR(20) DEFAULT NULL,
  `userCP` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

/*Data for the table `boys` */

INSERT  INTO `boys`(`id`,`boyName`,`userCP`) VALUES (1,'张无忌',100),(2,'鹿晗',800),(3,'黄晓明',50),(4,'段誉',300);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172
```

- 工资等级

```sql
CREATE TABLE job_grades
(grade_level VARCHAR(3),
 lowest_sal  INT,
 highest_sal INT);

INSERT INTO job_grades
VALUES ('A', 1000, 2999);

INSERT INTO job_grades
VALUES ('B', 3000, 5999);

INSERT INTO job_grades
VALUES('C', 6000, 9999);

INSERT INTO job_grades
VALUES('D', 10000, 14999);

INSERT INTO job_grades
VALUES('E', 15000, 24999);

INSERT INTO job_grades
VALUES('F', 25000, 40000);
12345678910111213141516171819202122
```

- 学生数据库文件

```sql
DROP DATABASE IF EXISTS student;
CREATE DATABASE student;
USE student;
CREATE TABLE student(
	studentno VARCHAR(10) NOT NULL PRIMARY KEY,
	studentname VARCHAR(20) NOT NULL,
	loginpwd VARCHAR(8) NOT NULL,
	sex CHAR(1) ,
	majorid INT NOT NULL REFERENCES grade(majorid),
	phone VARCHAR(11),
	email VARCHAR(20) ,
	borndate DATETIME
)DEFAULT CHARSET=utf8;

CREATE TABLE major(
	majorid INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	majorname VARCHAR(20) NOT  NULL

)DEFAULT CHARSET=utf8;
CREATE TABLE result(
	id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	studentno VARCHAR(10) NOT NULL REFERENCES student(studentno),
	score DOUBLE
)DEFAULT CHARSET=utf8;


INSERT INTO major VALUES(NULL,'javaee');
INSERT INTO major VALUES(NULL,'html5');
INSERT INTO major VALUES(NULL,'android');


INSERT INTO student VALUES('S001','张三封','8888','男',1,'13288886666','zhangsanfeng@126.com','1966-9-1');
INSERT INTO student VALUES('S002','殷天正','8888','男',1,'13888881234','yintianzheng@qq.com','1976-9-2');
INSERT INTO student VALUES('S003','周伯通','8888','男',2,'13288886666','zhoubotong@126.com','1986-9-3');
INSERT INTO student VALUES('S004','张翠山','8888','男',1,'13288886666',NULL,'1995-9-4');
INSERT INTO student VALUES('S005','小小张','8888','女',3,'13288885678','xiaozhang@126.com','1990-9-5');

INSERT INTO student VALUES('S006','张无忌','8888','男',2,'13288886666','zhangwuji@126.com','1998-8-9');
INSERT INTO student VALUES('S007','赵敏','0000','女',1,'13288880987','zhaomin@126.com','1998-6-9');
INSERT INTO student VALUES('S008','周芷若','6666','女',1,'13288883456','zhouzhiruo@126.com','1996-7-9');
INSERT INTO student VALUES('S009','殷素素','8888','女',1,'13288886666','yinsusu@163.com','1986-1-9');
INSERT INTO student VALUES('S010','宋远桥','6666','男',3,'1328888890','songyuanqiao@qq.com','1996-2-9');


INSERT INTO student VALUES('S011','杨不悔','6666','女',2,'13288882345',NULL,'1995-9-9');
INSERT INTO student VALUES('S012','杨逍','9999','男',1,'13288885432',NULL,'1976-9-9');
INSERT INTO student VALUES('S013','纪晓芙','9999','女',3,'13288888765',NULL,'1976-9-9');
INSERT INTO student VALUES('S014','谢逊','9999','男',1,'13288882211',NULL,'1946-9-9');
INSERT INTO student VALUES('S015','宋青书','9999','男',3,'13288889900',NULL,'1976-6-8');



INSERT INTO result VALUES(NULL,'s001',100);
INSERT INTO result VALUES(NULL,'s002',90);
INSERT INTO result VALUES(NULL,'s003',80);

INSERT INTO result VALUES(NULL,'s004',70);
INSERT INTO result VALUES(NULL,'s005',60);
INSERT INTO result VALUES(NULL,'s006',50);
INSERT INTO result VALUES(NULL,'s006',40);
INSERT INTO result VALUES(NULL,'s005',95);
INSERT INTO result VALUES(NULL,'s006',88);
```

# MySQL6天笔记——day02

# MySQL数据库

## DQL语言

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703100542907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

### 基础查询

- 语法：select 查询列表 from 表名;
- 类似于System.out.println(打印东西);
- 特点：
  - 1.查询列表可以是：表中的字段、常量值、表达式、函数
  - 2.查询的结果可以是一个虚拟表格;
- 案例

```sql
USE myemployees;

#1.查询表中的单个字段
SELECT last_name FROM employees;

#2.查询表中多个字段
SELECT last_name,salary,email FROM employees;

#3.查询表中的所有字段
SELECT * FROM employees;

#4.查询常量
# select 常量值;
# 注意：字符型和日期型的常量值必须用单引号引起来，数值型不需要
SELECT 100;
SELECT 'join';

#5.查询函数
#select 函数名(实参列表);
SELECT VERSION();

#6.查询表达式 
SELECT 100%98;

#7.起别名
/*
1.便于理解
2.如果要查询的字段有重名的情况,使用别名区分
*/
#方式一:使用AS
SELECT 100%98 AS 结果;
SELECT last_name AS 姓,first_name AS 名 FROM employees;

#方式二:使用空格
SELECT last_name 姓,first_name 名 FROM employees;

#案例:查询salary,结果显示 out put
SELECT salary AS "out put" FROM employees;

#8.去重
# select distinct 字段名 from 表名;
#案例:查询员工表中涉及的所有部门编号
SELECT DISTINCT department_id FROM employees;

#9.+号的作用
#案例:查询员工的名和姓,并显示为姓名
/*
java中的+号:
1.运算符:两个操作数都为数据型
2.连接符:只要有一个操作数为字符串


mysql中的+号:
只能作为运算符

select 100+90; 两个操作数都为数值型,做加法运算
select '123+90';其中一方为字符型,试图将字符型数值转换为数值型
		如果转换成功,则继续做加法运算
select 'john'+90; 如果转换失败,则将字符型数值转换成0

select null+0; 只要其中一方为null,则结果肯定为null.
*/
SELECT last_name+first_name AS 姓名 FROM employees; 

#10.【补充】concat函数 
/*
功能：拼接字符
select concat(字符1，字符2，字符3,...);
*/
SELECT CONCAT('a','b','c') AS 结果 FROM employees;

SELECT CONCAT(last_name,first_name) AS 姓名 FROM employees;

#11.【补充】ifnull函数
#功能：判断某字段或表达式是否为null，如果为null 返回指定的值，否则返回原本的值

SELECT IFNULL(commission_pct,0) FROM employees;

#12.【补充】isnull函数
#功能：判断某字段或表达式是否为null，如果是，则返回1，否则返回0
```

![image-20210814120547068](https://i.loli.net/2021/08/14/B5TtVRLkg1Hd9po.png)

![image-20210814120613061](https://i.loli.net/2021/08/14/2UgxJN1KqsM68tW.png)

### 条件查询

- 语法：select 查询列表 from 表名 where 筛选条件;

- 分类

  ```sql
  一、按条件表达式筛选
  	条件运算符:> < = != <> >= <= <=>安全等于
  二、按逻辑表达式筛选
  	逻辑运算符:&& || |
  	and or not
  	
  	&& 和 and:两个条件都为true，结果为true，反之为false
  	|| 和 or:只要有一个条件为true，结果为true，反之为false
  	! 或 not:如果连接的条件本身为false，结果为true，反之为false	
  	
  三、模糊查询
  	like:一般搭配通配符使用，可以判断字符型或数值型
  	通配符：%任意多个字符，_任意单个字符
  	like、between and、in、is null
  ```
  
- 案例

```sql
#一.按条件表达式筛选

#案例1:查询工资>12000的员工信息
SELECT * FROM employees WHERE salary>12000;

#案例2:查询部门编号不等于90号的员工名和部门编号
SELECT last_name,department_id FROM employees WHERE department_id <> 90;

#二、按逻辑表达式筛选

#案例1:查询工资z在10000到20000之间的员工名、工资及奖金
SELECT last_name,salary,commission_pct FROM employees WHERE salary>=10000 AND salary<=20000;

#案例2:查询部门编号不是在90-110之间,或者工资高于15000的员工信息
SELECT * FROM employees WHERE department_id <90 OR department_id>110 OR salary>15000;


#三、模糊查询

#1.like

#案例1:查询员工名中包含字符a的员工信息
SELECT * FROM employees WHERE last_name LIKE '%a%';

#案例2:查询员工名中第三个字符为b，第五个字符为a的员工名和工资
SELECT last_name,salary FROM employees WHERE last_name LIKE '__b_a%';

#案例3:查询员工名种第二个字符为_的员工名
SELECT last_name FROM employees WHERE last_name LIKE '_\_%';

#案例4：查询员工last_name第三个字符为a
SELECT * FROM employees WHERE last_name LIKE '__a%';

#2.between and

#案例1:查询员工编号在100到120之间的员工信息
SELECT * FROM employees WHERE employee_id>=100 AND employee_id<=120;

SELECT * FROM employees WHERE employee_id BETWEEN 100 AND 120;

/*注意事项：
1.提高语句简洁度
2.包含临界值
3.两个临界值不能调换顺序
*/

#3.in
/*
含义:判断某字段的值是否属于in列表中的某一项
特点:
 1.使用in提高语句简洁度
 2.in列表的值类型必须一致或兼容
*/
#案例1:查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号

SELECT last_name,job_id FROM employees WHERE job_id='IT_PROG' OR job_id='AD_PRES' OR job_id='AD_VP';

SELECT last_name,job_id FROM employees WHERE job_id IN('IT_PROG','AD_PRES','AD_VP');

#4.is null
/*
=或<>不能用于判断null值
is null 或 is not null 可以判断null值
*/
#案例1:查询没有奖金的员工名和奖金率

SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NULL;

SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NOT NULL;

#安全等于<=>

#案例1:查询没有奖金的员工名和奖金率

SELECT last_name,commission_pct FROM employees WHERE commission_pct <=> NULL;

#案例2:查询工资为12000的员工信息
SELECT last_name,commission_pct FROM employees WHERE salary <=> 12000;

#is null PK <=>
#	      普通类型的数值	null值		可读性
# is null	×		  √		  √
# <=>		√		  √		  ×
```

### 排序查询

- 引入：select * from employees;
- 语法：select 查询列表 from 表 【where 筛选条件】 order by
- 特点：
  - 1.asc代表的是升序，desc代表降序，不写默认为升序
  - 2.order by子句中可以支持单个字段、多个字段、表达式、函数、别名
  - 3.order by子句一般是放在查询语句的最后面,limit子句除外
- 案例

```sql
#案例1:查询员工信息,要求工资从高到低排序
SELECT * FROM employees ORDER BY salary DESC;
SELECT * FROM employees ORDER BY salary;

#案例2:查询部门编号是>=90，按入职时间的先后进行排序
SELECT * FROM employees WHERE department_id>=90 ORDER BY hiredate ASC;

#案例3:按年薪的高低显示员工的信息和年薪【按表达式排序】
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees 
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC; 

#案例4:按年薪的高低显示员工的信息和年薪【按别名排序】
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees 
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) 年薪 DESC;

#案例5:按姓名的长度显示员工的姓名和工资【按函数排序】
SELECT LENGTH(last_name) 字节长度,last_name,salary
FROM employees
ORDER BY LENGTH(last_name) DESC;

#案例6:查询员工共信息,要求按工资排序，再按员工编号排序【按多个字段排序】
SELECT * FROM employees
ORDER BY salary ASC,employee_id DESC;
1234567891011121314151617181920212223
```

### 常见函数

- 概念：类似于Java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名
- 优点：1.隐藏了实现细节 2.提高了代码的重用性
- 语法：select 函数名(实参列表) 【from 表】;
- 特点：
  - 1.叫什么(函数名)
  - 2.干什么(函数功能)
- 分类:
  - 1.单行函数，如concat、length、ifnull等
  - 2.分组函数，做统计使用

#### 单行函数

- 单行函数分类：字符函数、数学函数、日期函数、其他函数、流程控制函数

> 字符函数具体案例：

```sql
#一.字符函数
#1.length 获取参数值的字节值
SELECT LENGTH('subei');
SELECT LENGTH('鬼谷子qwe');

SHOW VARIABLES LIKE '%char%';

#2.concat 拼接字符串
SELECT CONCAT(last_name,'_',first_name) 姓名 FROM employees;

#3.upper:变大写、lower：变小写

SELECT UPPER('ton');
SELECT LOWER('ton');

#示例：将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),LOWER(first_name)) 姓名 FROM employees;


#4.substr、substring
#注意:索引从1开始

#截取从指定所有处后面的所以字符
SELECT SUBSTR('吴刚伐桂在天上',4) out_put;

#截取从指定索引处指定字符长度的字符
SELECT SUBSTR('吴刚伐桂在天上',1,2) out_put;

#案例:姓名中首字符大写,其他字符小写，然后用_拼接,显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) out_put FROM employees;

#5.instr:获取子串第一次出现的索引,找不到返回0
SELECT INSTR('MySQL技术进阶','技术') AS out_put;

#6.trim:去前后空格

SELECT LENGTH(TRIM('	霍山	')) AS out_put;

SELECT TRIM('+' FROM '++++李刚+++刘邦+++') AS out_put;

#7.lpad:用指定的字符实现左填充指定长度
SELECT LPAD('梅林',8,'+') AS out_put;

#8.rpad:用指定的字符实现右填充指定长度
SELECT RPAD('梅林',5,'&') AS out_put;

#9.replace:替换
SELECT REPLACE('莉莉伊万斯的青梅竹马是詹姆','詹姆','斯内普') AS out_put;
```

> 数学函数具体案例：

```sql
#1.round:四舍五入
SELECT ROUND(1.45);
SELECT ROUND(1.567,2);

#2.ceil:向上取整,返回>=该参数的最小整数
SELECT CEIL(1.005);
SELECT CEIL(-1.002);

#3.floor:向下取整,返回<=该参数的最大整数
SELECT FLOOR(-9.99);

#4.truncate:截断
SELECT TRUNCATE(1.65,1);

#5.mod:取余
SELECT MOD(10,3);

#6.rand:获取随机数，返回0-1之间的小数
SELECT RAND();
12345678910111213141516171819
```

> 日期函数具体案例：

```sql
#1.now:返回当前系统时间+日期
SELECT NOW();

#2.year:返回年
SELECT YEAR(NOW());
SELECT YEAR(hiredate) 年 FROM employees;

#3.month:返回月
#MONTHNAME:以英文形式返回月
SELECT MONTH(NOW());
SELECT MONTHNAME(NOW());

#4.day:返回日
#DATEDIFF:返回两个日期相差的天数
SELECT DAY(NOW());
SELECT DATEDIFF('2020/06/30','2020/06/21');

#5.str_to_date:将字符通过指定格式转换成日期
SELECT STR_TO_DATE('2020-5-13','%Y-%c-%d') AS out_put;

#6.date_format:将日期转换成字符
SELECT DATE_FORMAT('2020/6/6','%Y年%m月%d日') AS out_put;
SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日') AS out_put;

#7.curdate:返回当前日期
SELECT CURDATE();

#8.curtime:返回当前时间
SELECT CURTIME();
1234567891011121314151617181920212223242526272829
```

> 其他函数具体案例：

```sql
#version 当前数据库服务器的版本
SELECT VERSION();

#database 当前打开的数据库
SELECT DATABASE();

#user当前用户
SELECT USER();

#password('字符')：返回该字符的密码形式
SELECT PASSWORD('a');

#md5('字符'):返回该字符的md5加密形式
SELECT MD5('a');
```

> 流程控制函数具体案例：

```sql
#1.if函数: if else效果

SELECT IF(10<5,'大','小');

SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金！！！','有奖金!!!') 备注 FROM employees;

#2.case函数
#使用一:switch case 的效果
/*
java中
switch(变量或表达式){
	case 常量1:语句1;break;
	...
	default:语句n;break;
}

mysql中

case 要判断的变量或表达式
when 常量1 then 要显示的值1或语句1
when 常量2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end

#案例:查询员工的工资,要求:

部门号=30,显示的工资为1.1倍
部门号=40,显示的工资为1.2倍
部门号=50,显示的工资为1.3倍
其他部门,显示的工资为原工资

*/

SELECT salary 原始工资,department_id,
CASE department_id
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END AS 新工资
FROM employees;

#3.case函数的使用二:类似于多重if
/*
java中:
if(条件1){
	语句1;
}else if(条件2){
	语句2;
}
...
else{
	语句n;
}	

mysql中:
case 
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end

*/

#案例:查询员工的工资的情况
/*
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000，显示c级别
否则，显示D级别
*/

SELECT salary,
CASE
WHEN salary>20000 THEN 'A'
WHEN salary>15000 THEN 'B'
WHEN salary>10000 THEN 'C'
ELSE 'D'
END AS 工资等级
FROM employees;
```

#### 分组函数

- 功能：用作统计使用，又称为聚合函数或统计函数或组函数
- 分类：sum 求和、avg 平均值、max 最大值、min最小值count 计算个数
- 特点:
  - 1.sum和avg一般用于处理数值型
    max、min、count可以处理任何数据类型
  - 2.以上分组函数都忽略null
  - 3.都可以搭配distinct使用，实现去重的统计
    select sum(distinct 字段) from 表;
  - 4.count函数
    count(字段)：统计该字段非空值的个数
    count(*):统计结果集的行数
  - 5.和分组函数一同查询的字段，要求是group by后出现的字段

```sql
#1.简单使用
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT COUNT(salary) FROM employees;

SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;

#2.参数支持哪些数据类型

SELECT SUM(last_name),AVG(last_name) FROM employees;
SELECT SUM(hiredate),AVG(hiredate) FROM employees;

SELECT MAX(last_name),MIN(last_name) FROM employees;
SELECT MAX(hiredate),MIN(hiredate) FROM employees;

SELECT COUNT(commission_pct) FROM employees;
SELECT COUNT(last_name) FROM employees;

#3.是否忽略null

SELECT SUM(commission_pct),AVG(commission_pct) FROM employees;

SELECT commission_pct FROM employees;

SELECT SUM(commission_pct),AVG(commission_pct),SUM(commission_pct)/35,AVG(commission_pct)/107 FROM employees;

SELECT MAX(commission_pct),MIN(commission_pct) FROM employees;

SELECT COUNT(commission_pct) FROM employees;

#4.和distinct搭配

SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;

SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;

#5.count函数详解

SELECT COUNT(salary) FROM employees;
SELECT COUNT(*) FROM employees;
SELECT COUNT(1) FROM employees;
/*
效率上：
MyISAM存储引擎，count(*)最高
InnoDB存储引擎，count(*)和count(1)效率>count(字段)
*/

#6.和分组函数一同查询的字段有限制

SELECT AVG(salary),employee_id FROM employees;
```

### 分组查询

- 语法:

  ```sql
  select 分组函数,分组后的字段
  from 表
  【where 筛选条件】
  group by 分组的字段
  【having 分组后的筛选】
  【order by 排序列表】
  ```
  
  - 注意:查询列表必须特殊,要求是分组函数和group by后出现的字段
  
- 特点:

  - 1.分组查询中的筛选条件分为两类

    ```sql
    			使用关键字	筛选的表	位置
    分组前筛选	where		原始表		group by的前面
    分组后筛选	having		分组后的结果	group by的后面
    1.分组函数做条件肯定是放在having子句中
    2.能用分组前筛选的，就优先考虑使用分组前筛选
    ```
    
  - 2.group by子句支持单个字段分组，多个字段分组(多个字段之间用逗号隔开没有顺序要求),表达式或函数(使用较少)
  
  - 3.也可以添加排序(排序放在整个分组查询的最后)
  
- 案例

```sql
#引入:查询每个部门的平均工资
SELECT AVG(salary) FROM employees;

#案例1:查询每个工种的最高工资
SELECT MAX(salary),job_id FROM employees 
GROUP BY job_id;

#案例2:查询每个位置上的部门个数
SELECT COUNT(*),location_id
FROM departments
GROUP BY location_id;

#添加筛选条件
#案例1:查询邮箱中包含a字符的，每个部门的平均工资
SELECT AVG(salary),department_id FROM employees
WHERE email LIKE '%a%' GROUP BY department_id;

#案例2:查询有奖金的每个领导手下员工的最高工资
SELECT MAX(salary),manager_id FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;

#添加复杂的筛选条件
#案例1:查询哪个部门的员工个数>2
#1.查询每个部门的员工个数
SELECT COUNT(*),department_id FROM employees
GROUP BY department_id;

#2.根据1的结果进行筛选，查询哪个部门的员工个数大于2
SELECT COUNT(*),department_id FROM employees
GROUP BY department_id HAVING COUNT(*)>2;


#案例2:查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资 
#1.查询每个工种有奖金的员工的最高工资 
SELECT MAX(salary),job_id FROM employees 
WHERE commission_pct IS NOT NULL GROUP BY job_id; 

#2.根据结果继续筛选，最高工资>12000 

SELECT MAX(salary), job_id FROM employees 
WHERE commission_pct IS NOT NULL GROUP BY job_id 
HAVING MAX(salary)>12000; 

#按表达式或函数分组

#案例:按员工姓名的长度分组,查询每一组的员工个数,筛选员工个数>5

#1.查询每个长度的员工个数 
SELECT COUNT(*),LENGTH(last_name) len_name 
FROM employees GROUP BY LENGTH(last_name); 

#2.添加筛选条件
SELECT COUNT(*) c,LENGTH(last_name) len_name 
FROM employees GROUP BY len_name HAVING c>5;

#按多个字段查询
#案例:查询每个部门每个工种的员工的平均工资

SELECT AVG(salary),department_id,job_id
FROM employees GROUP BY department_id,job_id;

#添加排序
#案例:查询每个部门每个工种的员工的平均工资,按平均工资的高低查询

SELECT AVG(salary),department_id,job_id
FROM employees GROUP BY department_id,job_id
ORDER BY AVG(salary) DESC;
```

# MySQL6天笔记——day03

# MySQL数据库

> 全部SQL源文件链接：https://pan.baidu.com/s/1wc51qkVetSRybFzcIYGBIg
> 提取码：3wpt

## DQL语言

数据查询yuyan

### 连接查询

- 含义:又称多表查询,当查询的数据来自多个表时,就会用到连接查询
- 笛卡尔乘积现象：表1 有m行，表2有n行，结果=m*n行
- 发生原因：没有有效的连接条件
- 如何避免：添加有效的连接条件
- 分类

```sql
按年代分类：
	sql92标准:仅仅支持内连接
	sql99标准【推荐】：支持内连接+外连接（左外和右外）+交叉连接
	
按功能分类：
	内连接：
	等值连接
	非等值连接
	自连接
外连接：
	左外连接
	右外连接
	全外连接
		
交叉连接
```

### 不同的 SQL JOIN 类型：

- **INNER JOIN**：如果表中有至少一个匹配，则返回行

- **LEFT JOIN**：即使右表中没有匹配，也从左表返回所有的行

- **RIGHT JOIN**：即使左表中没有匹配，也从右表返回所有的行

- **FULL JOIN**：只要其中一个表中存在匹配，则返回行

  **MySQL中不支持 FULL OUTER JOIN**

#### sql92标准案例

```sql
#1、等值连接

/*
1.多表等值连接的结果为多表的交集部分
2.n表连接，至少需要n-1个连接条件
3.多表的顺序没有要求
4.一般需要为表起别名
5.可以搭配前面介绍的所有子句使用，比如排序、分组、筛选
*/

#案例1：查询女神名和对应的男神名
SELECT NAME,boyName FROM boys,beauty
WHERE beauty.boyfriend_id= boys.id;

#案例2：查询员工名和对应的部门名

SELECT last_name,department_name 
FROM employees,departments
WHERE employees.`department_id`=departments.`department_id`;

#2、为表起别名
/*
1.提高语句的简洁度
2.区分多个重名的字段

注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定
*/
#查询员工名、工种号、工种名
SELECT e.last_name,e.job_id,j.job_title
FROM employees  e,jobs j
WHERE e.`job_id`=j.`job_id`;

#3、两个表的顺序是否可以调换
#查询员工名、工种号、工种名

SELECT e.last_name,e.job_id,j.job_title
FROM jobs j,employees e
WHERE e.`job_id`=j.`job_id`;

#4、可以加筛选
#案例：查询有奖金的员工名、部门名

SELECT last_name,department_name,commission_pct
FROM employees e,departments d
WHERE e.`department_id`=d.`department_id`
AND e.`commission_pct` IS NOT NULL;

#案例2：查询城市名中第二个字符为o的部门名和城市名

SELECT department_name,city
FROM departments d,locations l
WHERE d.`location_id` = l.`location_id`
AND city LIKE '_o%';

#5、可以加分组
#案例1：查询每个城市的部门个数

SELECT COUNT(*) 个数,city
FROM departments d,locations l
WHERE d.`location_id`=l.`location_id`
GROUP BY city;

#案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name,d.`manager_id`,MIN(salary)
FROM departments d,employees e
WHERE d.`department_id`=e.`department_id`
AND commission_pct IS NOT NULL
GROUP BY department_name,d.`manager_id`;

#6、可以加排序
#案例：查询每个工种的工种名和员工的个数，并且按员工个数降序

SELECT job_title,COUNT(*)
FROM employees e,jobs j
WHERE e.`job_id`=j.`job_id`
GROUP BY job_title
ORDER BY COUNT(*) DESC;

#7、可以实现三表连接？
#案例：查询员工名、部门名和所在的城市

SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.`department_id`=d.`department_id`
AND d.`location_id`=l.`location_id`
AND city LIKE 's%'
ORDER BY department_name DESC;

#2、非等值连接
#案例1：查询员工的工资和工资级别

SELECT salary,grade_level
FROM employees e,job_grades g
WHERE salary BETWEEN g.`lowest_sal` AND g.`highest_sal`
AND g.`grade_level`='A';

/*
select salary,employee_id from employees;
select * from job_grades;
CREATE TABLE job_grades
(grade_level VARCHAR(3),
 lowest_sal  int,
 highest_sal int);

INSERT INTO job_grades
VALUES ('A', 1000, 2999);

INSERT INTO job_grades
VALUES ('B', 3000, 5999);

INSERT INTO job_grades
VALUES('C', 6000, 9999);

INSERT INTO job_grades
VALUES('D', 10000, 14999);

INSERT INTO job_grades
VALUES('E', 15000, 24999);

INSERT INTO job_grades
VALUES('F', 25000, 40000);

*/

#3、自连接
#案例：查询 员工名和上级的名称

SELECT e.employee_id,e.last_name,m.employee_id,m.last_name
FROM employees e,employees m
WHERE e.`manager_id`=m.`employee_id`;
```

#### sql99标准案例

- 语法

```sql
select 查询列表
from 表1 别名 【连接类型】
join 表2 别名 
on 连接条件
【where 筛选条件】
【group by 分组】
【having 筛选条件】
【order by 排序列表】
```

- 分类

```sql
内连接（★）：inner
外连接
	左外(★):left 【outer】
	右外(★)：right 【outer】
	全外：full【outer】
交叉连接：cross 
```

- 案例

  ON后面跟的是连接条件，where 后面加的是再次筛选条件。

```sql
#一、内连接
/*
语法：

select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件;

分类：
等值
非等值
自连接

特点：
①添加排序、分组、筛选
②inner可以省略
③ 筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
④inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集

*/

#1、等值连接
#案例1.查询员工名、部门名

SELECT last_name,department_name FROM departments d
INNER JOIN  employees e
ON e.`department_id` = d.`department_id`;

#案例2.查询名字中包含e的员工名和工种名（添加筛选）
SELECT last_name,job_title FROM employees e
INNER JOIN jobs j ON e.`job_id`=  j.`job_id`
WHERE e.`last_name` LIKE '%e%';

#案例3.查询部门个数>3的城市名和部门个数，（添加分组+筛选）

#1.查询每个城市的部门个数
#2.在1结果上筛选满足条件的
SELECT city,COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.`location_id`=l.`location_id`
GROUP BY city
HAVING COUNT(*)>3;

#案例4.查询哪个部门的员工个数>3的部门名和员工个数，并按个数降序（添加排序）

#1.查询每个部门的员工个数
SELECT COUNT(*),department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name;

#2.在1结果上筛选员工个数>3的记录，并排序

SELECT COUNT(*) 个数,department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name
HAVING COUNT(*)>3
ORDER BY COUNT(*) DESC;

#案例5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）

SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d ON e.`department_id`=d.`department_id`
INNER JOIN jobs j ON e.`job_id` = j.`job_id`
ORDER BY department_name DESC;

#二、非等值连接

#查询员工的工资级别

SELECT salary,grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
 
#查询工资级别的个数>20的个数，并且按工资级别降序
SELECT COUNT(*),grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`
GROUP BY grade_level
HAVING COUNT(*)>20
ORDER BY grade_level DESC;

#三、自连接
 
#查询员工的名字、上级的名字
SELECT e.last_name,m.last_name
FROM employees e
JOIN employees m
ON e.`manager_id`= m.`employee_id`;
 
#查询姓名中包含字符k的员工的名字、上级的名字
SELECT e.last_name,m.last_name
FROM employees e
JOIN employees m
ON e.`manager_id`= m.`employee_id`
WHERE e.`last_name` LIKE '%k%';

#二、外连接
 
/*
应用场景：用于查询一个表中有，另一个表没有的记录
 
特点：
1、外连接的查询结果为主表中的所有记录
	如果从表中有和它匹配的，则显示匹配的值
	如果从表中没有和它匹配的，则显示null
	外连接查询结果=内连接结果+主表中有而从表没有的记录
2、左外连接，left join左边的是主表
   右外连接，right join右边的是主表
3、左外和右外交换两个表的顺序，可以实现同样的效果 
4、全外连接=内连接的结果+表1中有但表2没有的+表2中有但表1没有的
*/
#引入：查询男朋友 不在男神表的的女神名

SELECT * FROM beauty;
SELECT * FROM boys;
 
#左外连接
SELECT b.*,bo.* FROM boys bo
LEFT OUTER JOIN beauty b
ON b.`boyfriend_id` = bo.`id`
WHERE b.`id` IS NULL;

#查询男朋友，即使不在boys表中也查出来  beauty作为左表
SELECT `NAME`,boyName FROM beauty LEFT JOIN boys ON beauty.boyfriend_id= boys.id;
 
#案例1：查询哪个部门没有员工
#左外
SELECT d.*,e.employee_id
FROM departments d
LEFT OUTER JOIN employees e
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;

#右外
 
SELECT d.*,e.employee_id
FROM employees e
RIGHT OUTER JOIN departments d
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;

#全外

USE girls;
SELECT b.*,bo.* FROM beauty b
FULL OUTER JOIN boys bo
ON b.`boyfriend_id` = bo.id;

#交叉连接

SELECT b.*,bo.* FROM beauty b
CROSS JOIN boys bo;
```

#### sql92和sql99对比

- 功能：sql99支持的较多
- 可读性：sql99实现连接条件和筛选条件的分离，可读性较高

### 子查询

- 含义:出现在其他语句中的select语句,称为子查询或内查询外部的查询语句，称为主查询或外查询。
- 分类

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703113430918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ2MTUzOTQ5,size_16,color_FFFFFF,t_70#pic_center)

- 案例

```sql
#一、where或having后面
/*
1、标量子查询（单行子查询）
2、列子查询（多行子查询）
3、行子查询（多列多行）

特点：
①子查询放在小括号内
②子查询一般放在条件的右侧
③标量子查询，一般搭配着单行操作符使用
> < >= <= = <>

列子查询，一般搭配着多行操作符使用
in、any/some、all

④子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果
*/

#1.标量子查询★

#案例1：谁的工资比 Abel 高?

#①查询Abel的工资
SELECT salary
FROM employees
WHERE last_name = 'Abel';

#②查询员工的信息，满足 salary>①结果
SELECT *
FROM employees
WHERE salary>(

	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'
	
);

#案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资

#①查询141号员工的job_id
SELECT job_id FROM employees
WHERE employee_id = 141;

#②查询143号员工的salary
SELECT salary FROM employees
WHERE employee_id = 143;

#③查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②

SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143

);

#案例3：返回公司工资最少的员工的last_name,job_id和salary

#①查询公司的最低工资
SELECT MIN(salary) FROM employees;

#②查询last_name,job_id和salary，要求salary=①
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);

#案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资

#①查询50号部门的最低工资
SELECT  MIN(salary)
FROM employees
WHERE department_id = 50;

#②查询每个部门的最低工资

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id;

#③ 在②基础上筛选，满足min(salary)>①
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50
);

#非法使用标量子查询

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250
);

#2.列子查询（多行子查询）★
#案例1：返回location_id是1400或1700的部门中的所有员工姓名

#①查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700);

#②查询员工姓名，要求部门号是①列表中的某一个

SELECT last_name
FROM employees
WHERE department_id  <>ALL(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);


#案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary

#①查询job_id为‘IT_PROG’部门任一工资

SELECT DISTINCT salary FROM employees
WHERE job_id = 'IT_PROG';

#②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';


#案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#3、行子查询（结果集一行多列或多行多列）

#案例：查询员工编号最小并且工资最高的员工信息

SELECT * FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);

#①查询最小的员工编号
SELECT MIN(employee_id) FROM employees;

#②查询最高工资
SELECT MAX(salary) FROM employees;

#③查询员工信息
SELECT * FROM employees
WHERE employee_id=(
	SELECT MIN(employee_id)
	FROM employees
)AND salary=(
	SELECT MAX(salary)
	FROM employees
);


#二、select后面
/*
仅仅支持标量子查询
*/

#案例：查询每个部门的员工个数

SELECT d.*,(
	SELECT COUNT(*)
	FROM employees e
	WHERE e.department_id = d.`department_id`
 ) 个数
 FROM departments d;
 
 
#案例2：查询员工号=102的部门名
 
SELECT (
	SELECT department_name,e.department_id
	FROM departments d
	INNER JOIN employees e
	ON d.department_id=e.department_id
	WHERE e.employee_id=102
	
) 部门名;

#三、from后面
/*
将子查询结果充当一张表，要求必须起别名
*/

#案例：查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees GROUP BY department_id;

SELECT * FROM job_grades;

#②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal

SELECT  ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;

#四、exists后面（相关子查询）
/*
语法：
exists(完整的查询语句)
结果：
1或0
*/
SELECT EXISTS(SELECT employee_id FROM employees WHERE salary=300000);

#案例1：查询有员工的部门名

#in
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees
);

#exists
SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`
);

#案例2：查询没有女朋友的男神信息

#in
SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
);

#exists
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id`=b.`boyfriend_id`
);
```

### 分页查询

- 应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求。
- 语法

```sql
	select 查询列表
	from 表
	【join type join 表2
	on 连接条件
	where 筛选条件
	group by 分组字段
	having 分组后的筛选
	order by 排序的字段】
	limit 【offset,】size;
注意
	offset要显示条目的起始索引（起始索引从0开始）
	size 要显示的条目个数
```

- 特点

```sql
①limit语句放在查询语句的最后
②公式
	要显示的页数 page，每页的条目数size
	
select 查询列表 from 表
limit (page-1)*size,size;
	
size=10
page  
1	0
2  	10
3	20
```

- 案例

```sql
#案例1：查询前五条员工信息
SELECT * FROM  employees LIMIT 0,5;
SELECT * FROM  employees LIMIT 5;

#案例2：查询第11条——第25条
SELECT * FROM employees LIMIT 10,15;

#案例3：有奖金的员工信息，并且工资较高的前10名显示出来
SELECT * FROM employees 
WHERE commission_pct IS NOT NULL 
ORDER BY salary DESC LIMIT 10 ;
```

### 联合查询

- 含义：union (联合、合并)：将多条查询语句的结果合并成一个结果。
- 语法

```sql
查询语句1
union 【all】
查询语句2
union 【all】
...
```

- 意义
  - 1、将一条比较复杂的查询语句拆分成多条语句
  - 2、适用于查询多个表的时候，查询的列基本是一致。
- 特点
  - 1、要求多条查询语句的查询列数是一致的！
  - 2、要求多条查询语句的查询的每一列的类型和顺序最好一致
  - 3、**union关键字默认去重**，如果使用union all 可以包含重复项
- 案例

```sql
#引入的案例：查询部门编号>90或邮箱包含a的员工信息

SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;

SELECT * FROM employees  WHERE email LIKE '%a%'
UNION
SELECT * FROM employees  WHERE department_id>90;


#案例：查询中国用户中男性的信息以及外国用户中年男性的用户信息

SELECT id,cname,csex FROM t_ca WHERE csex='男'
UNION
SELECT t_id,tName,tGender FROM t_ua WHERE tGender='male';
```

- 此处需要的数据库源文件

```sql
/*
 Navicat MySQL Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50145
 Source Host           : localhost:3306
 Source Schema         : test

 Target Server Type    : MySQL
 Target Server Version : 50145
 File Encoding         : 65001

 Date: 03/07/2020 11:08:32
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_ca
-- ----------------------------
DROP TABLE IF EXISTS `t_ca`;
CREATE TABLE `t_ca`  (
  `id` int(20) NOT NULL,
  `cname` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `csex` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of t_ca
-- ----------------------------
INSERT INTO `t_ca` VALUES (1, '韩梅梅', '女');
INSERT INTO `t_ca` VALUES (2, '李雷', '男');
INSERT INTO `t_ca` VALUES (3, '李明', '男');

SET FOREIGN_KEY_CHECKS = 1;


/*
 Navicat MySQL Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50145
 Source Host           : localhost:3306
 Source Schema         : test

 Target Server Type    : MySQL
 Target Server Version : 50145
 File Encoding         : 65001

 Date: 03/07/2020 11:09:05
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_ua
-- ----------------------------
DROP TABLE IF EXISTS `t_ua`;
CREATE TABLE `t_ua`  (
  `t_id` int(11) NOT NULL,
  `tName` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `tGender` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`t_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of t_ua
-- ----------------------------
INSERT INTO `t_ua` VALUES (1, 'john', 'male');
INSERT INTO `t_ua` VALUES (2, 'lucy', 'female');
INSERT INTO `t_ua` VALUES (3, 'lily', 'female');
INSERT INTO `t_ua` VALUES (4, 'jack', 'male');
INSERT INTO `t_ua` VALUES (5, 'rose', 'female');

SET FOREIGN_KEY_CHECKS = 1;
```

# MySQL6天笔记——day04

# MySQL数据库

> 全部SQL源文件链接：https://pan.baidu.com/s/1wc51qkVetSRybFzcIYGBIg
> 提取码：3wpt

## DML语言

DDM：数据操纵语言（Data Manipulation Language）

- 数据操作语言
  - 插入：insert
  - 修改：update
  - 删除：delete

### 插入语句

- 方式一：经典的插入
- 语法：insert into 表名(字段名,…) values(值,…);
- 特点
  - 1、要求值的类型和字段的类型要一致或兼容；
  - 2、字段的个数和顺序不一定与原始表中的字段个数和顺序一致但必须保证值和字段一一对应；
  - 3、假如表中有可以为null的字段，注意可以通过以下两种方式插入null值
    - ①字段和值都省略
    - ②字段写上，值使用null
- 4、字段和值的个数必须一致
- 5、字段名可以省略，默认所有列
- 具体案例：

```sql
SELECT * FROM beauty;
#1.插入的值的类型要与列的类型一致或兼容
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

#2.不可以为null的列必须插入值。可以为null的列如何插入值？
    #方法一：
    INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
    VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

    #方法二：
    INSERT INTO beauty(id,NAME,sex,phone)
    VALUES(15,'娜扎','女','1388888888');

#3.列的顺序是否可以调换
INSERT INTO beauty(NAME,sex,id,phone)
VALUES('蒋欣','女',16,'110');

#4.列数和值的个数必须一致

INSERT INTO beauty(NAME,sex,id,phone)
VALUES('关晓彤','女',17,'110');

#5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致

INSERT INTO beauty
VALUES(18,'张飞','男',NULL,'119',NULL,NULL);
```

- 方式二语法：insert into 表名 set 列名=值,列名=值,…
- INSERT INTO beauty SET id=19,NAME=‘刘涛’,phone=‘999’;
- 两种方式的区别

```sql
1.方式一支持一次插入多行，语法如下：
insert into 表名【(字段名,..)】 values(值，..),(值，...),...;
2.方式一支持子查询，语法如下：
    insert into 表名 查询语句;
    
#1、方式一支持插入多行,方式二不支持
INSERT INTO beauty
VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);

#2、方式一支持子查询，方式二不支持

INSERT INTO beauty(id,NAME,phone) SELECT 26,'宋茜','11809866';

INSERT INTO beauty(id,NAME,phone) 
SELECT id,boyname,'1234567' FROM boys WHERE id<3;       
```

### 修改语句

- 修改单表的记录★
- 语法：update 表名 set 列=新值,列=新值,… where 筛选条件;
- 案例

```sql
#1.修改单表的记录
#案例1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty SET phone = '13899888899'
WHERE NAME LIKE '唐%';

#案例2：修改boys表中id好为2的名称为张飞，魅力值 10
UPDATE boys SET boyname='张飞',usercp=10
WHERE id=2;
```

- 修改多表的记录【补充】
- sql92语法：update 表1 别名,表2 别名 set 列=值,… where 连接条件 and 筛选条件;
- sql99语法：update 表1 别名 inner|left|right join 表2 别名 on 连接条件 set 列=值,… where 筛选条件;
- 案例

```sql
#2.修改多表的记录
#案例 1：修改张无忌的女朋友的手机号为114
UPDATE boys bo
INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`phone`='119',bo.`userCP`=1000
WHERE bo.`boyName`='张无忌';

#案例2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2 WHERE bo.`id` IS NULL;
1234567891011
```

### 删除语句

#### 方式一：delete

- 语法

```sql
1、单表的删除【★】
delete from 表名 where 筛选条件

2、多表的删除【补充】

sql92语法：
delete 表1的别名,表2的别名
from 表1 别名,表2 别名
where 连接条件
and 筛选条件;

sql99语法：
delete 表1的别名,表2的别名
from 表1 别名
inner|left|right join 表2 别名 on 连接条件
where 筛选条件;
12345678910111213141516
```

- 案例

```sql
#1.单表的删除
#案例：删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
SELECT * FROM beauty;

#2.多表的删除
#案例：删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName`='张无忌';

#案例：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='黄晓明';
```

#### 方式二：truncate

```sql
语法：truncate table 表名;
#案例：将魅力值>100的男神信息删除
TRUNCATE TABLE boys ;
```

- 两种方式的区别【面试题】★

```java
1.delete 可以加where 条件，truncate不能加
2.truncate删除，效率高一点
3.假如要删除的表中有自增长列，
  如果用delete删除后，再插入数据，自增长列的值从断点开始，而truncate删除后，再插入数据，自增长列的值从1开始。
4.truncate删除没有返回值，delete删除有返回值
5.truncate删除不能回滚，delete删除可以回滚.
```

## DDL语言

### 库的管理

- 创建库

  ```sql
  create database 【if not exists】 库名【 character set 字符集名】;
  #案例：创建库Books
    CREATE DATABASE IF NOT EXISTS books ;
  ```
  
- 修改库

  ```sql
  alter database 库名 character set 字符集名;
  #案例：更改库的字符集
  ALTER DATABASE books CHARACTER SET gbk;
  ```
  
- 删除库

  ```sql
  drop database 【if exists】 库名;
  #案例：库的删除
  DROP DATABASE IF EXISTS books;
  ```

### 表的管理

- 表的创建 ★

  ```sql
  /*
  语法：
  create table 表名(
  	列名 列的类型【(长度) 约束】,
  	列名 列的类型【(长度) 约束】,
  	列名 列的类型【(长度) 约束】,
  	...
  	列名 列的类型【(长度) 约束】
  )
  */
  #案例：创建表Book
  CREATE TABLE book (
    id INT,
    #编号
    bName VARCHAR (20),
    #图书名
    price DOUBLE,
    #价格
    authorId INT,
    #作者编号
    publishDate DATETIME#出版日期
  ) ;
  
  DESC book;
  
  #案例：创建表author
  CREATE TABLE IF NOT EXISTS author (
    id INT,
    au_n`author`ame VARCHAR (20),
    nation VARCHAR (10)
  );
  DESC author ;
  ```
  
- 表的修改

  ```sql
  1.添加列
  alter table 表名 add column 列名 类型 【first|after 字段名】;
  ALTER TABLE MONITOR_ITEM_RESULT MODIFY COLUMN USER_ID VARCHAR(32) COMMENT'用户id';
  2.修改列的类型或约束
  alter table 表名 modify column 列名 新类型 【新约束】;
  3.修改列名
  alter table 表名 change column 旧列名 新列名 类型;
  4 .删除列
  alter table 表名 drop column 列名;
  5.修改表名
  alter table 表名 rename 【to】 新表名;
  
  案例：
  #①修改列名
  ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME ;
  
  #②修改列的类型或约束
  ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;
  
  #③添加新列
  ALTER TABLE author ADD COLUMN annual DOUBLE; 
  
  #④删除列
  ALTER TABLE book_author DROP COLUMN  annual;
  
  #⑤修改表名
  ALTER TABLE book_author RENAME TO author;
  
  DESC book;
  ```
  
- 表的删除

  ```sql
  drop table【if exists】 表名;
  
  案例：
  DROP TABLE IF EXISTS book_author;
  
  SHOW TABLES;
  
  #通用的写法：
  DROP DATABASE IF EXISTS 旧库名;
  CREATE DATABASE 新库名 ;
  
  DROP TABLE IF EXISTS 旧表名;
  CREATE TABLE  表名();
  ```
  
- 复制表

  ```sql
  1、复制表的结构
  create table 表名 like 旧表;
  2、复制表的结构+数据
  create table 表名 
  select 查询列表 from 旧表【where 筛选】;
  
  案例
  INSERT INTO author VALUES
  (1,'村上春树','日本'),
  (2,'莫言','中国'),
  (3,'冯唐','中国'),
  (4,'金庸','中国');
  
  SELECT * FROM Author;
  SELECT * FROM copy2;
  
  #1.仅仅复制表的结构
  CREATE TABLE copy LIKE author;
  
  #2.复制表的结构+数据
  CREATE TABLE copy2 SELECT * FROM author;
  
  #只复制部分数据
  CREATE TABLE copy3 SELECT id,au_name
  FROM author WHERE nation='中国';
  
  #仅仅复制某些字段
  CREATE TABLE copy4 SELECT id,au_name
  FROM author WHERE 0;
  ```

### 数据类型

#### 数值型

> 整型

- 分类：

  ```sql
  tinyint、smallint、mediumint、int/integer、bigint
  1	       2		   3	       4		 8
  ```
  
- 特点：

  - ① 如果不设置无符号还是有符号，默认是有符号，如果想设置无符号，需要添加unsigned关键字
  - ② 如果插入的数值超出了整型的范围,会报out of range异常，并且插入临界值
  - ③ 如果不设置长度，会有默认的长度
    长度代表了显示的最大宽度，如果不够会用0在左边填充，但必须搭配zerofill使用！

- 案例

```sql
#1.如何设置无符号和有符号
DROP TABLE IF EXISTS tab_int ;

CREATE TABLE tab_int (t1 INT (7) ZEROFILL, t2 INT (7) ZEROFILL) ;

DESC tab_int ;

INSERT INTO tab_int VALUES (- 123456) ;

INSERT INTO tab_int VALUES (- 123456, - 123456) ;

INSERT INTO tab_int VALUES (2147483648, 4294967296) ;

INSERT INTO tab_int VALUES (123, 123) ;

SELECT * FROM tab_int ;
```

> 浮点型

- 定点数：dec(M,D)；decimal(M,D)
- 浮点数：float(M,D) 4；double(M,D) 8
- 特点：
  - ①M代表整数部位+小数部位的个数，D代表小数部位
  - ②如果超出范围，则报out or range异常，并且插入临界值
  - ③M和D都可以省略，但对于定点数，M默认为10，D默认为0
  - ④如果精度要求较高，则优先考虑使用定点数
- 案例

```sql
#测试M和D
DROP TABLE tab_float ;

CREATE TABLE tab_float (f1 FLOAT, f2 DOUBLE, f3 DECIMAL) ;

SELECT * FROM tab_float ;

DESC tab_float ;

INSERT INTO tab_float VALUES(123.4523,123.4523,123.4523);
INSERT INTO tab_float VALUES(123.456,123.456,123.456);
INSERT INTO tab_float VALUES(123.4,123.4,123.4);
INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4);

#原则：
#所选择的类型越简单越好，能保存数值的类型越小越好
12345678910111213141516
```

#### 字符型

- 较短的文本：char、varchar

- 其他：

  - binary和varbinary用于保存较短的二进制
  - enum用于保存枚举
  - set用于保存集合

- 较长的文本：text、blob(较大的二进制)

- 特点

  ```sql
    		写法			M的意思								特点		空间的耗费	效率
  	char	char(M)		最大的字符数，可以省略，默认为1	 固定长度的字符	比较耗费	 高
  
   varchar	varchar(M)	最大的字符数，不可以省略			可变长度的字符	   比较节省		低
  1234
  ```

- 案例

```sql
CREATE TABLE tab_char(
	c1 ENUM('a','b','c')
);

INSERT INTO tab_char VALUES('a');
INSERT INTO tab_char VALUES('b');
INSERT INTO tab_char VALUES('c');
INSERT INTO tab_char VALUES('m');
INSERT INTO tab_char VALUES('A');

SELECT * FROM tab_set;

CREATE TABLE tab_set(
	s1 SET('a','b','c','d')
);
INSERT INTO tab_set VALUES('a');
INSERT INTO tab_set VALUES('A,B');
INSERT INTO tab_set VALUES('a,c,d');
123456789101112131415161718
```

#### 日期型

- 分类：

  - date只保存日期；
  - time 只保存时间；
  - year只保存年；
  - datetime保存日期+时间；
  - timestamp保存日期+时间；

- 特点

  ```sql
  			字节		范围			时区等的影响
  datetime	 8		1000——9999	       不受
  timestamp	 4	    1970-2038	        受
  123
  ```

- 案例

```sql
CREATE TABLE tab_date(
	t1 DATETIME,
	t2 TIMESTAMP
);

INSERT INTO tab_date VALUES(NOW(),NOW());

SELECT * FROM tab_date;

SHOW VARIABLES LIKE 'time_zone';

SET time_zone='+9:00';
```

# MySQL6天笔记——day05

# MySQL

> 全部SQL源文件链接：https://pan.baidu.com/s/1wc51qkVetSRybFzcIYGBIg
> 提取码：3wpt

## DDL语言 

DDL：数据定义语言 （Data Definition Language）

### 常见的约束

- 含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性。

- 分类

  ```sql
  六大约束
  	NOT NULL：非空，用于保证该字段的值不能为空
  	比如姓名、学号等
  	DEFAULT:默认，用于保证该字段有默认值
  	比如性别
  	PRIMARY KEY:主键，用于保证该字段的值具有唯一性，并且非空
  	比如学号、员工编号等
  	UNIQUE:唯一，用于保证该字段的值具有唯一性，可以为空
  	比如座位号
  	CHECK:检查约束【mysql中不支持】
  	比如年龄、性别
  	FOREIGN KEY:外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值
  		在从表添加外键约束，用于引用主表中某列的值
  	比如学生表的专业编号，员工表的部门编号，员工表的工种编号
  ```
  
- 添加约束的时机：1.创建表时；2.修改表时

- 约束的添加分类：

  - 列级约束：六大约束语法上都支持，但外键约束没有效果
  - 表级约束：除了非空、默认，其他的都支持

- 语法

```sql
CREATE TABLE 表名{
	字段名 字段类型 列级约束,
	字段名 字段类型,
	表级约束
};
```

------

> 一、创建表时添加约束

```sql
#先新建一个库
CREATE DATABASE students;
```

- 1.添加列级约束
- 语法：直接在字段名和类型后面追加 约束类型即可。只支持：默认、非空、主键、唯一
- 案例

```sql
USE students;

DROP TABLE stuinfo;

CREATE TABLE stuinfo(
	id INT PRIMARY KEY,#主键
	stuName VARCHAR(20) NOT NULL UNIQUE,#非空
	gender CHAR(1) CHECK(gender='男' OR gender ='女'),#检查
	seat INT UNIQUE,#唯一
	age INT DEFAULT  18,#默认约束
	majorId INT REFERENCES major(id)#外键
);

CREATE TABLE major(
	id INT PRIMARY KEY,
	majorName VARCHAR(20)
);

#查看stuinfo中的所有索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;
```

- 2.添加表级约束
- 语法：语法：在各个字段的最下面 【constraint 约束名】 约束类型(字段名)

```sql
TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT,
	
	CONSTRAINT pk PRIMARY KEY(id),#主键
	CONSTRAINT uq UNIQUE(seat),#唯一键
	CONSTRAINT ck CHECK(gender ='男' OR gender  = '女'),#检查
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键	
);

SHOW INDEX FROM stuinfo;
```

- 通用的写法：★

```sql
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT PRIMARY KEY,
	stuname VARCHAR(20),
	sex CHAR(1),
	age INT DEFAULT 18,
	seat INT UNIQUE,
	majorid INT,
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
);

create table 表名(
  	字段名 字段类型 not null,#非空
  	字段名 字段类型 primary key,#主键
  	字段名 字段类型 unique,#唯一
  	字段名 字段类型 default 值,#默认
  	constraint 约束名 foreign key(字段名) references 主表（被引用列）
);

注意：
  			   支持类型		      可以起约束名			
列级约束		除了外键		     不可以
表级约束		除了非空和默认	  可以，但对主键无效
  
列级约束可以在一个字段上追加多个，中间用空格隔开，没有顺序要求
```

- 主键和唯一的区别

```sql
		保证唯一性  是否允许为空    一个表中可以有多少个   是否允许组合
主键		√				×			至多有1个           √，但不推荐
唯一		√				√			可以有多个          √，但不推荐
```

------

> 二、修改表时添加约束

- 语法

```sql
1、添加列级约束
alter table 表名 modify column 字段名 字段类型 新约束;

2、添加表级约束
alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
```

- 案例

```sql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT
);

DESC stuinfo;
#1.添加非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20)  NOT NULL;

#2.添加默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;

#3.添加主键
    #①列级约束
    ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
    #②表级约束
    ALTER TABLE stuinfo ADD PRIMARY KEY(id);

#4.添加唯一
    #①列级约束
    ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
    #②表级约束
    ALTER TABLE stuinfo ADD UNIQUE(seat);

#5.添加外键
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id); 
```

------

> 三、修改表时删除约束

```sql
#1.删除非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;

#2.删除默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT ;

#3.删除主键
ALTER TABLE stuinfo DROP PRIMARY KEY;

#4.删除唯一
ALTER TABLE stuinfo DROP INDEX seat;

#5.删除外键
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;

SHOW INDEX FROM stuinfo;
```

> 修改表时添加或删除约束的具体语法总结：

```sql
1、非空
添加非空
alter table 表名 modify column 字段名 字段类型 not null;
删除非空
alter table 表名 modify column 字段名 字段类型 ;

2、默认
添加默认
alter table 表名 modify column 字段名 字段类型 default 值;
删除默认
alter table 表名 modify column 字段名 字段类型 ;

3、主键
添加主键
alter table 表名 add【 constraint 约束名】 primary key(字段名);
删除主键
alter table 表名 drop primary key;

4、唯一
添加唯一
alter table 表名 add【 constraint 约束名】 unique(字段名);
删除唯一
alter table 表名 drop index 索引名;

5、外键
添加外键
alter table 表名 add【 constraint 约束名】 foreign key(字段名) references 主表（被引用列）;
删除外键
alter table 表名 drop foreign key 约束名;
```

> 四、自增长列(标识列)

- 含义：可以不用手动的插入值，系统提供默认的序列值

- 特点

  ```sql
  1.不用手动插入值，可以自动提供序列值，默认从1开始，步长为1
    auto_increment_increment
    如果要更改起始值：手动插入值
    如果要更改步长：更改系统变量
    set auto_increment_increment=值;
  2.一个表至多有一个自增长列
  3.自增长列只能支持数值型
  4.自增长列必须为一个key
  ```
  
- 案例

```sql
#一、创建表时设置标识列
DROP TABLE IF EXISTS tab_identity;

CREATE TABLE tab_identity(
	id INT  ,
	NAME FLOAT UNIQUE AUTO_INCREMENT,
	seat INT 
) TRUNCATE TABLE tab_identity;

INSERT INTO tab_identity(id,NAME) VALUES(NULL,'john');

INSERT INTO tab_identity(NAME) VALUES('lucy');

SELECT * FROM tab_identity;

SHOW VARIABLES LIKE '%auto_increment%';

SET auto_increment_increment=3;
123456789101112131415161718
```

- 语法总结

```sql
一、创建表时设置自增长列
create table 表(
	字段名 字段类型 约束 auto_increment
);

二、修改表时设置自增长列
alter table 表 modify column 字段名 字段类型 约束 auto_increment;

三、删除自增长列
alter table 表 modify column 字段名 字段类型 约束;
12345678910
```

## TCL语言（事务控制语言）

> Transaction Control Language 事务控制语言

- 事务：一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。

  ```sql
  案例：转账
  
  张三丰  1000
  郭襄	1000
  
  update 表 set 张三丰的余额=500 where name='张三丰'
  意外
  update 表 set 郭襄的余额=1500 where name='郭襄'
  ```
  
- **事务的特性(ACID)**

  - 原子性：一个事务不可再分割，要么都执行要么都不执行
  - 一致性：一个事务执行会使数据从一个一致状态切换到另外一个一致状态
  - 隔离性：一个事务的执行不受其他事务的干扰
  - 持久性：一个事务一旦提交，则会永久的改变数据库的数据.

> 事务的使用步骤 ★

- 了解

  - 隐式（自动）事务：没有明显的开启和结束，本身就是一条事务可以自动提交，比如insert、update、delete

  - 显式事务：事务具有明显的开启和结束的标记；前提：必须先设置自动提交功能为禁用

    ```sql
    开启事务的语句;
    update 表 set 张三丰的余额=500 where name='张三丰'
    
    update 表 set 郭襄的余额=1500 where name='郭襄' 
    结束事务的语句;
    ```
  
- 具体步骤：

  ```sql
  步骤1：开启事务
  set autocommit=0;
  start transaction;可选的
  
  步骤2：编写事务中的sql语句(select insert update delete)
  语句1;
  语句2;
  ...
  
  步骤3：结束事务
  commit;提交事务
  rollback;回滚事务
  ```
  
- 案例

```sql
SHOW VARIABLES LIKE 'autocommit';
SHOW ENGINES;

#1.演示事务的使用步骤
DROP TABLE IF EXISTS account;
CREATE TABLE account(
	id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(20),
	balance DOUBLE
);
INSERT INTO account(username,balance)
VALUES('张无忌',1000),('赵敏',1000);

#开启事务
SET autocommit=0;
START TRANSACTION;
#编写一组事务的语句
UPDATE account SET balance = 1000 WHERE username='张无忌';
UPDATE account SET balance = 1000 WHERE username='赵敏';

#结束事务
ROLLBACK;
#commit;
SELECT * FROM account;
```

> 并发事务

- 1、事务的并发问题是如何发生的？
  - 多个事务同时操作 同一个数据库的相同数据时
- 2、并发问题都有哪些？
  - 对于同时运行的多个事务, 当这些事务访问数据库中相同的数据时, 如果没有采取必要的隔离机制, 就会导致各种并发问题：
  - 脏读：一个事务读取了其他事务还没有提交的数据，读到的是其他事务“更新”的数据；
  - 不可重复读：一个事务多次读取，结果不一样；
  - 幻读：一个事务读取了其他事务还没有提交的数据，只是读到的是 其他事务“插入”的数据。
- 数据库事务的隔离性: 数据库系统必须具有隔离并发运行各个事务的能力, 使它们不会相互影响, 避免各种并发问题.
- 一个事务与其他事务隔离的程度称为隔离级别. 数据库规定了多种事务隔离级别, 不同隔离级别对应不同的干扰程度, 隔离级别越高, 数据一致性就越好, 但并发性越弱
- 3、如何解决并发问题？
  - 通过设置隔离级别来解决并发问题
- 4、隔离级别

```sql
							脏读			不可重复读		  幻读
read uncommitted:读未提交     ×                ×              ×        
read committed：读已提交      √                ×              ×
repeatable read：可重复读     √                √              ×
serializable：串行化          √                √              √
```

- Oracle 支持的2 种事务隔离级别：READ COMMITED, SERIALIZABLE。Oracle 默认的事务隔离级别为: READ COMMITED
- Mysql 支持4 种事务隔离级别. Mysql 默认的事务隔离级别为: REPEATABLE READ

```sql
查看隔离级别
select @@tx_isolation;
设置隔离级别
set session|global transaction isolation level 隔离级别;

#2.演示事务对于delete和truncate的处理的区别
SET autocommit=0;
START TRANSACTION;

DELETE FROM account;
ROLLBACK;
1234567891011
```

> 回滚点的演示

- savepoint 节点名;设置保存点

```sql
#3.演示savepoint 的使用
SET autocommit=0;

START TRANSACTION;
DELETE FROM account WHERE id=25;
SAVEPOINT a;#设置保存点
DELETE FROM account WHERE id=28;
ROLLBACK TO a;#回滚到保存点

SELECT * FROM account;
```

## 其他

### 视图

> 什么是视图？

- 视图：虚拟表,和普通表一样使用。mysql5.1版本出现的新特性，是通过表动态生成的数据。
- 应用场景
  - 多个地方用到同样的查询结果
  - 该查询结果使用的sql语句较复杂
- 案例

```sql
SELECT stuname,majorname FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`
WHERE s.`stuname` LIKE '张%';

CREATE VIEW v1 AS
SELECT stuname,majorname
FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`;

SELECT * FROM v1 WHERE stuname LIKE '张%';
```

> 视图的创建

- 语法：create view 视图名 as 查询语句;
- 案例

```sql
USE myemployees;

#1.查询姓名中包含a字符的员工名、部门名和工种信息
    #①创建
    CREATE VIEW myv1 AS
    SELECT last_name,department_name,job_title
    FROM employees e
    JOIN departments d ON e.department_id  = d.department_id
    JOIN jobs j ON j.job_id  = e.job_id;

    #②使用
    SELECT * FROM myv1 WHERE last_name LIKE '%a%';

#2.查询各部门的平均工资级别
    #①创建视图查看每个部门的平均工资
    CREATE VIEW myv2 AS
    SELECT AVG(salary) ag,department_id
    FROM employees GROUP BY department_id;

    #②使用
    SELECT myv2.`ag`,g.grade_level FROM myv2
    JOIN job_grades g
    ON myv2.`ag` BETWEEN g.`lowest_sal` AND g.`highest_sal`;

#3.查询平均工资最低的部门信息
SELECT * FROM myv2 ORDER BY ag LIMIT 1;

#4.查询平均工资最低的部门名和工资
    CREATE VIEW myv3 AS
    SELECT * FROM myv2 ORDER BY ag LIMIT 1;

    SELECT d.*,m.ag FROM myv3 m
    JOIN departments d
    ON m.`department_id`=d.`department_id`;
```

- 视图的好处
  - 重用sql语句
  - 简化复杂的sql操作，不必知道它的查询细节
  - 保护数据，提高安全性

> 视图的修改

- 语法

  ```sql
  方式一：
  create or replace view 视图名 as 查询语句;
  方式二：
  alter view 视图名 as 查询语句;
  ```
  
- 案例

```sql
#方式一：
/*
create or replace view  视图名 as 查询语句;
*/
SELECT * FROM myv3 

CREATE OR REPLACE VIEW myv3 AS
SELECT AVG(salary),job_id
FROM employees GROUP BY job_id;

#方式二：
/*
语法：
alter view 视图名 as 查询语句;
*/
ALTER VIEW myv3 AS SELECT * FROM employees;
```

> 视图的删除

- 用户可以一次删除一个或者多个视图，前提是必须有该视图的drop权限。
- 语法：drop view 视图1，视图2,…;
- 案例

```sql
/*
语法：drop view 视图名,视图名,...;
*/
DROP VIEW emp_v1,emp_v2,myv3;
```

> 视图的查看

- 语法

  ```sql
  desc 视图名;
  show create view 视图名;
  ```
  
- 案例

```sql
DESC myv3;
SHOW CREATE VIEW myv3;
```

> 视图的更新

- 1.插入：insert
- 2.修改：update
- 3.删除：delete
- 4.查看：select

```sql
CREATE OR REPLACE VIEW myv1
AS
SELECT last_name,email,salary*12*(1+IFNULL(commission_pct,0)) "annual salary"
FROM employees;

CREATE OR REPLACE VIEW myv1
AS
SELECT last_name,email
FROM employees;

SELECT * FROM myv1;
SELECT * FROM employees;
#1.插入

INSERT INTO myv1 VALUES('张飞','zf@qq.com');

#2.修改
UPDATE myv1 SET last_name = '张无忌' WHERE last_name='张飞';

#3.删除
DELETE FROM myv1 WHERE last_name = '张无忌';
```

- 注意：视图一般用于查询的，而不是更新的，所以具备以下特点的视图都不允许更新
  - 包含以下关键字的sql语句：分组函数、distinct、group by、having、union或者union all
  - 常量视图
  - Select中包含子查询
  - join
  - from一个不能更新的视图
  - where子句的子查询引用了from子句中的表

```sql
#具备以下特点的视图不允许更新
#①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all

CREATE OR REPLACE VIEW myv1
AS
SELECT MAX(salary) m,department_id
FROM employees
GROUP BY department_id;

SELECT * FROM myv1;

#更新
UPDATE myv1 SET m=9000 WHERE department_id=10;

#②常量视图
CREATE OR REPLACE VIEW myv2
AS
SELECT 'john' NAME;

SELECT * FROM myv2;

#更新
UPDATE myv2 SET NAME='lucy';

#③Select中包含子查询

CREATE OR REPLACE VIEW myv3
AS
SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
FROM departments;

#更新
SELECT * FROM myv3;
UPDATE myv3 SET 最高工资=100000;

#④join
CREATE OR REPLACE VIEW myv4
AS
SELECT last_name,department_name
FROM employees e
JOIN departments d
ON e.department_id  = d.department_id;

#更新
SELECT * FROM myv4;
UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
INSERT INTO myv4 VALUES('陈真','xxxx');

#⑤from一个不能更新的视图
CREATE OR REPLACE VIEW myv5
AS SELECT * FROM myv3;

#更新
SELECT * FROM myv5;
UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;

#⑥where子句的子查询引用了from子句中的表

CREATE OR REPLACE VIEW myv6
AS
SELECT last_name,email,salary
FROM employees
WHERE employee_id IN(
	SELECT  manager_id
	FROM employees
	WHERE manager_id IS NOT NULL
);

#更新
SELECT * FROM myv6;
UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
```

> 视图和表的对比

```sql
		关键字		  是否占用物理空间			使用
视图	view		占用较小，只保存sql逻辑	一般用于查询
表	    table		保存实际的数据			  增删改查
```

# MySQL6天笔记——day06

# MySQL

> 全部SQL源文件链接：https://pan.baidu.com/s/1wc51qkVetSRybFzcIYGBIg
> 提取码：3wpt

## 其他

### 变量

> 变量的介绍

- 系统变量：
  - 全局变量
  - 会话变量
- 自定义变量：
  - 用户变量
  - 局部变量

> 系统变量的介绍和语法

- 说明：变量由系统定义，不是用户定义，属于服务器层面

- 注意：全局变量需要添加global关键字，会话变量需要添加session关键字，如果不写，默认会话级别

- 使用步骤：

  - 1、查看所有系统变量

    ```sql
    show global|【session】variables;
    ```
    
  - 2、查看满足条件的部分系统变量
  
    ```sql
    show global|【session】 variables like '%char%';
    ```
    
  - 3、查看指定的系统变量的值

    ```sql
    select @@global|【session】系统变量名;
    ```
    
  - 4、为某个系统变量赋值
  
    ```sql
    方式一：
    set global|【session】系统变量名=值;
    方式二：
    set @@global|【session】系统变量名=值;
    ```

> 全局变量的演示

```sql
#1》全局变量
/*
作用域：针对于所有会话（连接）有效，但不能跨重启
*/
#①查看所有全局变量
SHOW GLOBAL VARIABLES;
#②查看满足条件的部分系统变量
SHOW GLOBAL VARIABLES LIKE '%char%';
#③查看指定的系统变量的值
SELECT @@global.autocommit;
#④为某个系统变量赋值
SET @@global.autocommit=0;
SET GLOBAL autocommit=0;
```

> 会话变量的演示

```sql
#2》会话变量
/*
作用域：针对于当前会话（连接）有效
*/
#①查看所有会话变量
SHOW SESSION VARIABLES;
#②查看满足条件的部分会话变量
SHOW SESSION VARIABLES LIKE '%char%';
#③查看指定的会话变量的值
SELECT @@autocommit;
SELECT @@session.tx_isolation;
#④为某个会话变量赋值
SET @@session.tx_isolation='read-uncommitted';
SET SESSION tx_isolation='read-committed';
```

> 自定义变量—用户变量

```sql
#二、自定义变量
/*
说明：变量由用户自定义，而不是系统提供的
使用步骤：
1、声明
2、赋值
3、使用（查看、比较、运算等）
*/

#1》用户变量
/*
作用域：针对于当前会话（连接）有效，作用域同于会话变量
*/

#赋值操作符：=或:=
#①声明并初始化
SET @变量名=值;
SET @变量名:=值;
SELECT @变量名:=值;

#②赋值（更新变量的值）
#方式一：
	SET @变量名=值;
	SET @变量名:=值;
	SELECT @变量名:=值;
#方式二：
	SELECT 字段 INTO @变量名
	FROM 表;
#③使用（查看变量的值）
SELECT @变量名;
```

> 自定义变量—局部变量

```sql
#2》局部变量
/*
作用域：仅仅在定义它的begin end块中有效
应用在 begin end中的第一句话
*/

#①声明
DECLARE 变量名 类型;
DECLARE 变量名 类型 【DEFAULT 值】;


#②赋值（更新变量的值）

#方式一：
	SET 局部变量名=值;
	SET 局部变量名:=值;
	SELECT 局部变量名:=值;
#方式二：
	SELECT 字段 INTO 具备变量名
	FROM 表;
#③使用（查看变量的值）
SELECT 局部变量名;


#案例：声明两个变量，求和并打印
#用户变量
SET @m=1;
SET @n=1;
SET @sum=@m+@n;
SELECT @sum;

#局部变量
DECLARE m INT DEFAULT 1;
DECLARE n INT DEFAULT 1;
DECLARE SUM INT;
SET SUM=m+n;
SELECT SUM;
```

> 用户变量和局部变量的对比

```sql
		作用域			定义位置		语法
用户变量	当前会话		会话的任何地方		加@符号，不用指定类型
局部变量	定义它的BEGIN END中 	BEGIN END的第一句话	一般不用加@,需要指定类型
```

### 存储过程和函数

- 说明：都类似于java中的方法，将一组完成特定功能的逻辑语句包装起来，对外暴露名字
- 好处
  - 1、提高代码的重用性
  - 2、简化操作
  - 3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

> 存储过程

- 含义：一组预先编译好的SQL语句的集合，理解成批处理语句

- 一、创建语法

  ```sql
  CREATE PROCEDURE 存储过程名(参数列表)
  BEGIN
  	存储过程体（一组合法的SQL语句）
  END
  
  注意：
  1、参数列表包含三部分
  参数模式  参数名  参数类型
  举例：
  in stuname varchar(20)
  
  参数模式：
  in：该参数可以作为输入，也就是该参数需要调用方传入值
  out：该参数可以作为输出，也就是该参数可以作为返回值
  inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值
  
  2、如果存储过程体仅仅只有一句话，begin end可以省略
  存储过程体中的每条sql语句的结尾要求必须加分号。
  存储过程的结尾可以使用 delimiter 重新设置
  语法：
  delimiter 结束标记
  案例：
  delimiter $
  ```
  
- 二、调用语法

- 语法

  ```sql
  CALL 存储过程名(实参列表);
  
  举例：
  调用in模式的参数：call sp1（‘值’）;
  调用out模式的参数：set @name; call sp1(@name);select @name;
  调用inout模式的参数：set @name=值; call sp1(@name); select @name;
  ```
  
- 案例

```sql
#1.空参列表
#案例：插入到admin表中五条记录

SELECT * FROM admin;

DELIMITER $
CREATE PROCEDURE myp1()
BEGIN
	INSERT INTO admin(username,`password`) 
	VALUES('john1','0000'),('lily','0000'),('rose','0000'),('jack','0000'),('tom','0000');
END $

#调用
CALL myp1()$

#2.创建带in模式参数的存储过程

#案例1：创建存储过程实现 根据女神名，查询对应的男神信息

CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
	SELECT bo.*
	FROM boys bo
	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name=beautyName;
END $

#调用
CALL myp2('柳岩')$

#案例2 ：创建存储过程实现，用户是否登录成功

CREATE PROCEDURE myp3(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE result VARBINARY(20) DEFAULT '';#声明并初始化
	
	SELECT COUNT(*) INTO result#赋值
	FROM admin
	WHERE admin.username = username
	AND admin.password = PASSWORD;
	
	SELECT result;#使用
END $

CALL myp3('张飞','8888')$

CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE result INT DEFAULT 0;#声明并初始化
	
	SELECT COUNT(*) INTO result#赋值
	FROM admin
	WHERE admin.username = username
	AND admin.password = PASSWORD;
	
	SELECT IF(result>0,'成功','失败');#使用
END $

#调用
CALL myp4('张飞','8888')$


#3.创建out 模式参数的存储过程
#案例1：根据输入的女神名，返回对应的男神名

CREATE PROCEDURE myp6(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
BEGIN
	SELECT bo.boyname INTO boyname
	FROM boys bo
	RIGHT JOIN
	beauty b ON b.boyfriend_id = bo.id
	WHERE b.name=beautyName ;
	
END $

#案例2：根据输入的女神名，返回对应的男神名和魅力值

CREATE PROCEDURE myp7(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT usercp INT) 
BEGIN
	SELECT boys.boyname ,boys.usercp INTO boyname,usercp
	FROM boys 
	RIGHT JOIN
	beauty b ON b.boyfriend_id = boys.id
	WHERE b.name=beautyName ;
	
END $

#调用
CALL myp7('小昭',@name,@cp)$
SELECT @name,@cp$

#4.创建带inout模式参数的存储过程
#案例1：传入a和b两个值，最终a和b都翻倍并返回

CREATE PROCEDURE myp8(INOUT a INT ,INOUT b INT)
BEGIN
	SET a=a*2;
	SET b=b*2;
END $

#调用
SET @m=10$
SET @n=20$
CALL myp8(@m,@n)$
SELECT @m,@n$
```

- 三、查看语法

  ```sql
  show create procedure 存储过程名;
  
  案例
  DESC myp2;×
  SHOW CREATE PROCEDURE  myp2;
  ```
  
- 四、删除语法

  ```sql
  drop procedure 存储过程名;
  
  案例:
  DROP PROCEDURE p1;
  DROP PROCEDURE p2,p3;#×
  ```

------

> 函数

- 含义：一组预先编译好的SQL语句的集合，理解成批处理语句

- 区别：

  - 存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新；
  - 函数：有且仅有1 个返回，适合做处理数据后返回一个结果。

- 一、创建语法

  ```sql
  CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
  BEGIN
  	函数体
  END
  
  /*
  注意：
  1.参数列表 包含两部分：
  参数名 参数类型
  2.函数体：肯定会有return语句，如果没有会报错
  如果return语句没有放在函数体的最后也不报错，但不建议
  
  return 值;
  3.函数体中仅有一句话，则可以省略begin end
  4.使用 delimiter语句设置结束标记
  */
  
  DELIMITER $
  ```
  
- 二、调用语法

  - SELECT 函数名(参数列表);
  - 案例

  ```sql
  use employees $
  #1.无参有返回
  #案例：返回公司的员工个数
  CREATE FUNCTION myf1() RETURNS INT
  BEGIN	
  	DECLARE c INT DEFAULT 0;
  	SELECT COUNT(*) INTO c
  	FROM employees;
  	RETURN c;
  END $
  
  SELECT myf1()$
  
  
  #2.有参有返回
  #案例1：根据员工名，返回它的工资
  
  CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
  BEGIN
  	SET @sal=0;#定义用户变量 
  	SELECT salary INTO @sal   #赋值
  	FROM employees
  	WHERE last_name = empName;
  	
  	RETURN @sal;
  END $
  
  SELECT myf2('kochhor') $
  
  #案例2：根据部门名，返回该部门的平均工资
  
  CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
  BEGIN
  	DECLARE sal DOUBLE ;
  	SELECT AVG(salary) INTO sal
  	FROM employees e
  	JOIN departments d ON e.department_id = d.department_id
  	WHERE d.department_name=deptName;
  	RETURN sal;
  END $
  
  SELECT myf3('IT')$
  ```
  
- 三、查看函数

```sql
show create function 函数名;

SHOW CREATE FUNCTION myf3 $
```

- 四、删除函数

```sql
drop function 函数名;

DROP FUNCTION myf3 $
```

### 流程控制结构

- 顺序结构：程序从上往下依次执行
- 分支结构：程序按条件进行选择执行，从两条或多条路径中选择一条执行
- 循环结构：程序满足一定条件下，重复执行一组语句

> 一、分支结构

- 1.if函数

  - 语法：if(条件,值1，值2)；
  - 功能：实现双分支；
  - 应用：可以作为表达式放在任何位置

- 2.case结构

  - 语法：

    ```sql
    情况1：类似于switch，一般用于实现等值判断。
    语法：
    case 变量或表达式
    when 值1 then 语句1;
    when 值2 then 语句2;
    ...
    else 语句n;
    end 
    
    情况2：类似于多重if语句，一般用于实现区间判断。
    语法：
    case 
    when 条件1 then 语句1;
    when 条件2 then 语句2;
    ...
    else 语句n;
    end 
    ```
    
  - 特点
  
    - ①可以作为表达式，嵌套在其他语句中使用。
    - ②可以放在任何地方，BEGIN END 中或BEGIN END 的外面可以作为独立的语句去使用，只能放在BEGIN END中如果wHEN中的值满足或条件成立，则执行对应的THzN后面的语句，并且结束CASE如果都不满足，则执行E1SE中的语句或值。
    - ③ELSE可以省略，如果ELSE省略了，并且所有WHEN条件都不满足，则返回NULL。
  
  - 位置
  
    - 可以放在任何位置，
    - 如果放在begin end 外面，作为表达式结合着其他语句使用
    - 如果放在begin end 里面，一般作为独立的语句使用
  
  - 案例
  
    ```sql
    #案例 
    #创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100, 显示A，80-90，显示B，60-80，显示c，否则，显示D
    
    CREATE PROCEDURE test_case (IN score INT) 
    BEGIN 
    	CASE 
    	WHEN score>=90 AND score<=100 THEN SELECT 'A'; 
    	WHEN score>=80 THEN SELECT 'B';
    	WHEN score>=60 THEN SELECT 'C'; 
    	ELSE SELECT 'D';
    	END CASE; 
    END $
    CALL test_case(95)$
    ```
  
- 3.if结构

- 语法：

  ```sql
  if 条件1 then 语句1;
  elseif 条件2 then 语句2;
  ....
  else 语句n;
  end if;
  ```
  
- 功能：类似于多重if；只能应用在begin end 中

```sql
#案例1：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D

CREATE FUNCTION test_case(score FLOAT) RETURNS CHAR
BEGIN 
	DECLARE ch CHAR DEFAULT 'A';
	
	CASE 
	WHEN score>90 THEN SET ch='A';
	WHEN score>80 THEN SET ch='B';
	WHEN score>60 THEN SET ch='C';
	ELSE SET ch='D';
	END CASE;
	
	RETURN ch;
END $

SELECT test_case(56)$
```

> 二、循环结构

- 位置：只能放在begin end中
- 特点：都能实现循环结构
- 语法

```sql
1、while
语法：
【名称:】while 循环条件 do
		循环体
end while 【名称】;
2、loop
语法：
【名称：】loop
		循环体
end loop 【名称】;

3、repeat
语法：
【名称:】repeat
		循环体
until 结束条件 
end repeat 【名称】;
```

- 对比

```sql
①这三种循环都可以省略名称，但如果循环中添加了循环控制语句（leave或iterate）则必须添加名称
②
loop 一般用于实现简单的死循环
while 先判断后执行
repeat 先执行后判断，无条件至少执行一次
```

- 案例

```sql
#1.没有添加循环控制语句
#案例：批量插入，根据次数插入到admin表中多条记录
USE girls$
DROP PROCEDURE pro_while1$
CREATE PROCEDURE pro_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('Rose',i),'666');
		SET i=i+1;
	END WHILE;
	
END $

CALL pro_while1(158)$

select * from admin $
/*
int i=1;
while(i<=insertcount){
	//插入
	i++;

}
*/

#2.添加leave语句
#案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	a:WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		IF i>=20 THEN LEAVE a;
		END IF;
		SET i=i+1;
	END WHILE a;
END $

CALL test_while1(100)$

select * from admin $

#3.添加iterate语句
#案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	a:WHILE i<=insertCount DO
		SET i=i+1;
		IF MOD(i,2)!=0 THEN ITERATE a;
		END IF;
		
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		
	END WHILE a;
END $

CALL test_while1(100)$

/*
int i=0;
while(i<=insertCount){
	i++;
	if(i%2==0){
		continue;
	}
	插入
}
*/

select * from admin $
```

> 二、循环控制语句

- leave：类似于break，用于跳出所在的循环
- iterate：类似于continue，用于结束本次循环，继续下一次