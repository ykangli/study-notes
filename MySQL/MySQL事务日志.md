---
title: MySQL事务日志
date: 2022-01-26 16:14:47
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/s8958042.jpg
---

# MySQL事务日志  

事务有4种特性：原子性、一致性、隔离性和持久性。那么事务的四种特性到底是基于什么机制实现呢？

事务的**隔离性**由 **锁机制** 实现。

而事务的**原子性、一致性和持久性**由事务的 **redo 日志和undo 日志**来保证。

- REDO LOG 称为 **重做日志** ，提供再写入操作，恢复提交事务修改的页操作，用来保证事务的**持久性**。
- UNDO LOG 称为 **回滚日志** ，回滚行记录到某个特定版本，用来保证事务的**原子性、一致性**。

有人或许会认为UNDO是REDO的逆过程，其实不然。REDO 和 UNDO 都可以视为是一种**恢复操作**，但是:

-  **redo log**:	是**存储引擎层(innodb)生成的日志**，记录的是"**物理级别**"上的页修改操作，比如页号xx、偏移量y写入了'zzz'数据。主要为了保证数据的可靠性;
- **undo log**:    是存储引擎层(innodb)生成的日志，记录的是**逻辑操作**日志，比如对某一行数据进行了INSERT语句操作，那么undo log就记录一条与之相反的DELETE操作。主要用于**事务的回滚**(undo log记录的是每个修改操作的**逆操作**)和**一致性非锁定读**(undo log回滚行记录到某种特定的版本---MVCC，即多版本并发控制)

## 1. redo日志  

InnoDB存储引擎是**以页为单位**来管理存储空间的。在真正访问页面之前，需要把在**磁盘上**的页缓存到内存中的**Buffer Pool**之后才可以访问。所有的变更都必须**先更新缓冲池中的数据**，然后缓冲池中的**脏页**会以一定的频率被刷入磁盘（**checkPoint机制**），通过缓冲池来优化CPu和磁盘之间的鸿沟，这样就可以保证整体的性能不会下降太快。

### 1.1 为什么需要REDO日志

一方面，缓冲池可以帮助我们消除CPU和磁盘之间的鸿沟，checkpoint机制可以保证数据的最终落盘，然而由于checkpoint 并不是每次变更的时候就触发 的，而是**master线程隔一段时间去处理的**。

**所以最坏的情况**就是事务提交后，刚写完缓冲池，还没将数据刷新到磁盘（内存中完成了，但磁盘中未完成），数据库宕机了，那么这段数据就是丢失的，无法恢复。

另一方面，事务包含 **持久性** 的特性，就是说对于一个已经提交的事务，在事务提交后即使系统发生了崩溃，这个事务对数据库中所做的更改也不能丢失。

那么如何保证这个持久性呢？ **一个简单的做法** ：在事务提交完成之前把该事务所修改的所有页面都刷新到磁盘，但是这个简单粗暴的做法有些问题：

- **修改量与刷新磁盘工作量严重不成比例**

有时候我们仅仅修改了某个页面中的一个字节，但是我们知道在InnoDB中是**以页为单位来进行磁盘IO的**，也就是说我们在该事务提交时**不得不将一个完整的页面从内存中刷新到磁盘**，我们又知道一个页面默认是16KB大小，只修改一个字节就要刷新16KB的数据到磁盘上显然是太小题大做了。

- **随机lO刷新较慢**

一个事务可能包含很多语句，即使是一条语句也可能修改许多页面，假如该事务修改的这些页面可能并不相邻，这就意味着在将某个事务修改的Buffer Pool中的页面刷新到磁盘时，需要进行很多的随机IO，随机IO比顺序IO要慢，尤其对于传统的机械硬盘来说。

**另一个解决的思路** ：我们只是想让已经提交了的事务对数据库中数据所做的修改永久生效，即使后来系统崩溃，在重启后也能把这种修改恢复出来。所以我们其实没有必要在每次事务提交时就把该事务在内存中修改过的全部页面刷新到磁盘，只需要把**修改了哪些东西记录一下就好**。比如，某个事务将系统表空间中 第10号 页面中偏移量为 100 处的那个字节的值 1 改成 2 。我们只需要记录一下：将第0号表空间的10号页面的偏移量为100处的值更新为 2 。  

InnoDB引擎的事务采用了WAL技术（`Write-Ahead Logging`)，这种技术的思想就是**先写日志，再写磁盘**，**只有日志写入成功，才算事务提交成功**，这里的日志就是**redo log**。当发生宕机且数据未刷到磁盘的时候，可以通过redo log来恢复，保证ACID中的D，这就是redo log的作用。

![image-20220126162713847](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126162713847.png)

### 1.2 REDO日志的好处、特点

**好处：**

- redo日志降低了刷盘频率
- redo日志占用的空间非常小

存储表空间ID、页号、偏移量以及需要更新的值，所需要的存储空间是很小的，刷盘快。

**特点：**

- redo日志是顺序写入磁盘的

在执行事务的过程中，每执行一条语句，就可能产生若干条redo日志，这些日志是按照**产生的顺序写入磁盘的**，也就是使用顺序lo，效率比随机IO快。

- 事务执行过程中，redo log不断记录  

redo log跟bin log的区别，redo log是**存储引擎层**产生的，而**bin log是数据库层**产生的。假设一个事务，对表做10万行的记录插入，在这个过程中，一直不断的往redo log顺序记录，而bin log不会记录，直到这个事务提交，才会一次写入到bin log文件中。

### 1.3 redo的组成

Redo log可以简单分为以下两个部分：

- 重做日志的缓冲 (redo log buffer) ，保存在内存中，是易失的。

在服务器启动时就向操作系统申请了一大片称之为redo log buffer的**连续内存**空间，翻译成中文就是redo日志缓冲区。这片内存空间被划分成若干个连续的redo log block。一个redo log block占用512字节大小。

![image-20220126164244593](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126164247620.png)

**参数设置：innodb_log_buffer_size：**  

redo log buffer 大小，默认 16M ，最大值是4096M，最小值为1M。 

```sql
mysql> show variables like '%innodb_log_buffer_size%';
+------------------------+----------+
| Variable_name | Value |
+------------------------+----------+
| innodb_log_buffer_size | 16777216 |
+------------------------+----------+   
```

- 重做日志文件 (redo log file) ，保存在硬盘中，是持久的。  

REDO日志文件如图所示，其中的ib_logfile0和ib_logfile1即为REDO日志。

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126164528296.png" alt="image-20220126164528296" style="zoom:67%;" />

### 1.4 redo的整体流程

以一个更新事务为例，redo log 流转过程，如下图所示：  

![image-20220126164659436](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126164659436.png)

- 第1步：先将原始数据从磁盘中读入内存中来，修改数据的内存拷贝
- 第2步：生成一条重做日志并写入redo log buffer，记录的是数据被修改后的值
- 第3步：当事务commit时，将redo log buffer中的内容刷新到 redo log file，对 redo log file采用追加写的方式
- 第4步：定期将内存中修改的数据刷新到磁盘中  

### 1.5 redo log的刷盘策略

redo log的写入并不是直接写入磁盘的，InnoDB引擎会在写redo log的时候先写redo log buffer，之后以 **一定的频率** 刷入到真正的redo log file 中。这里的一定频率怎么看待呢？这就是我们要说的刷盘策略。  

![image-20220126164801521](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126164801521.png)

注意，redo log buffer刷盘到redo log file的过程并不是真正的刷到磁盘中去，只是刷入到 **文件系统缓存（page cache）**中去（这是现代**操作系统**为了提高文件写入效率做的一个优化），真正的写入会交给系统自己来决定（比如page cache足够大了）。那么对于InnoDB来说就存在一个问题，如果交给系统来同步，同样如果**操作系统系统宕机**，那么数据也丢失了（虽然整个系统宕机的概率还是比较小的）。
针对这种情况，InnoDB给出 innodb_flush_log_at_trx_commit 参数，该参数控制 commit提交事务时，如何将 redo log buffer 中的日志刷新到 redo log file 中。它支持三种策略：  

- 设置为0 ：表示每次事务提交时不进行刷盘操作。（系统默认master thread每隔1s进行一次重做日志的同步）
- 设置为1 ：表示**每次事务提交时都将进行同步，刷盘操作**（ **默认值** ）
- 设置为2 ：表示每次事务提交时都只把 redo log buffer 内容写入 page cache，不进行同步。由os自己决定什么时候同步到磁盘文件。  

![image-20220126165353406](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126165353406.png)

另外，InnoDB存储引擎有一个后台线程，每隔1秒，就会把 redo log buffer 中的内容写到文件系统缓存( page cache ) ，然后调用刷盘操作。

### 1.6 不同刷盘策略演示

####  流程图  

![image-20220126165559699](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126165559699.png)

**小结**: `innodb_flush_log_at_trx_commit=1`

为1时，只要事务提交成功,redo log记录就一定在硬盘里，不会有任何数据丢失。

如果事务执行期间MySQL挂了或宕机，这部分日志丢了，但是事务并没有提交，所以日志丢了也不会有损失。可以保证ACID的D，数据绝对不会丢失，但是**效率最差**的。

建议使用默认值，虽然操作系统宕机的概率理论小于数据库宕机的概率，但是一般既然使用了事务，那么数据的安全相对来说更重要些。|

![image-20220126170043228](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126170043228.png)

**小结**:  `innodb_flush_log_at_trx_commit=2`

为2时，只要事务提交成功，redo log buffer中的内容只写入文件系统缓存（ page cache )

如果仅仅只是MysQL挂了不会有任何数据丢失（page cache不归内存管了），但是**操作系统宕机可能会有1秒数据的丢失**，这种情况下无法满足ACID中的D。但是数值2肯定是效率最高的。

![image-20220126170251477](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126170251477.png)

**小结**:  ` innodb_flush_log_at_trx_commit=0` 

为0时，master thread中每1秒进行一次重做日志的fsync操作，因此实例crash最多丢失1秒钟内的事务。(master thread是负责将缓冲池中的数据异步刷新到磁盘，保证数据的一致性)

数值0的话，是一种折中的做法，它的IO效率理论是高于1的，低于2的，这种策略也有丢失数据的风险，这种情况下也无法满足ACID中的D。

### 1.7 写入redo log buffer 过程  

#### 1.补充概念: Mini-Transaction

MysQL把对底层页面中的一次原子访问的过程称之为一个**Mini-Transaction**，简称**mtr**，比如，**向某个索引对应的B+树中插入一条记录的过程就是一个Mini-Transaction**。一个所谓的mtr可以包含一组redo日志在进行崩溃恢复时这一组redo日志作为一个不可分割的整体。

一个事务可以包含若干条语句，每一条语句其实是由若干个mtr组成，每一个mtr又可以包含若干条redo日志，画个图表示它们的关系就是这样:

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172158478.png" alt="image-20220126172158478" style="zoom:60%;" />

#### 2. redo 日志写入log buffer  

![image-20220126172413359](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172413359.png)

每个mtr都会产生一组redo日志，用示意图来描述一下这些mtr产生的日志情况：  

![image-20220126172424274](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172424274.png)

不同的事务可能是 并发 执行的，所以 T1 、 T2 之间的 mtr 可能是 交替执行 的。  

![image-20220126172438820](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172438820.png)

#### 3. redo log block的结构图  

![image-20220126172453846](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172453846.png)

![image-20220126172501143](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172501143.png)

### 1.8 redo log file  

#### 1. 相关参数设置

- **innodb_log_group_home_dir** ：指定 redo log 文件组所在的路径，默认值为 ./ ，表示在数据库的数据目录下。MySQL的默认数据目录（ var/lib/mysql ）下默认有两个名为 ib_logfile0 和ib_logfile1 的文件，log buffer中的日志默认情况下就是刷新到这两个磁盘文件中。此redo日志文件位置还可以修改。
- **innodb_log_files_in_group**：指明redo log file的个数，命名方式如：ib_logfile0，iblogfile1...iblogfilen。默认2个，最大100个。  

![image-20220126172627228](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172627228.png)

- **innodb_flush_log_at_trx_commit**：控制 redo log 刷新到磁盘的策略，默认为1。
- **innodb_log_file_size**：单个 redo log 文件设置大小，默认值为 48M 。最大值为512G，注意最大值指的是整个 redo log 系列文件之和，即（innodb_log_files_in_group * innodb_log_file_size ）不能大于最大值512G。  

![image-20220126172718213](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172718213.png)

根据业务修改其大小，以便容纳较大的事务。编辑my.cnf文件并重启数据库生效，如下所示  

```
[root@localhost ~]# vim /etc/my.cnf
innodb_log_file_size=200M  
```

#### 2. 日志文件组  

![image-20220126172743759](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172743759.png)

总共的redo日志文件大小其实就是： `innodb_log_file_size × innodb_log_files_in_group `。

采用循环使用的方式向redo日志文件组里写数据的话，会导致后写入的redo日志覆盖掉前边写的redo日志？当然！所以InnoDB的设计者提出了checkpoint的概念。  

#### 3. checkpoint  

![image-20220126172808119](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172808119.png)

如果 write pos 追上 checkpoint ，表示**日志文件组满了**，这时候不能再写入新的 redo log记录，MySQL 得停下来，清空一些记录，把 checkpoint 推进一下。  

![image-20220126172825343](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126172825343.png)

### 1.9 redo log小结

相信大家都知道redo log的作用和它的刷盘时机、存储形式:

InnoDB的更新操作采用的是**Write Ahead Log**(预先日志持久化)策略，即**先写日志，再写入磁盘**。

![image-20220126173138431](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126173138431.png)

## 2. Undo日志  

**redo log**是事务**持久性**的保证，**undo log**是事务**原子性**的保证。在事务中 **更新数据** 的 **前置操作** 其实是要先写入一个 undo log 。

### 2.1 如何理解Undo日志

事务需要保证 **原子性** ，也就是事务中的操作**要么全部完成，要么什么也不做**。但有时候事务执行到一半会出现一些情况，比如：

- 情况一：事务执行过程中可能遇到各种错误，比如 **服务器本身的错误** ， **操作系统错误** ，甚至是突然**断电**导致的错误。
- 情况二：程序员可以在事务执行过程中手动输入 **ROLLBACK** 语句结束当前事务的执行。

以上情况出现，我们需要把数据改回原先的样子，这个过程称之为 **回滚** ，这样就可以造成一个假象：这个事务看起来什么都没做，所以符合 **原子性** 要求。  

每当我们要对一条记录做改动时(这里的**改动**可以指**INSERT、DELETE、UPDATE**)，都需要"留一手"—-把回滚时所需的东西记下来。比如:

- 你**插入一条记录**时，至少要把这条记录的主键值记下来，之后回滚的时候只需要把这个主键值对应的记录删掉就好了。(对于每个INSERT,InnoDB存储引擎会完成一个DELETE)
- 你**删除了一条记录**，至少要把这条记录中的内容都记下来，这样之后回滚时再把由这些内容组成的记录插入到表中就好了。(对于每个DELETE,InnoDB存储引擎会执行一个INSERT)
- 你**修改了一条记录**，至少要把修改这条记录前的旧值都记录下来，这样之后回滚时再把这条记录更新为旧值就好了。(对于每个UPDATE，InnoDB存储引擎会执行一个相反的UPDATE，将修改前的行放回去)

MysQL把这些为了回滚而记录的这些内容称之为**撤销日志**或者**回滚日志**(即undo log)。注意，由于查询操作( SELECT）并不会修改任何用户记录，所以**在查询操作执行时，并不需要记录相应的undo日志**。

此外，undo log **会产生redo log**，也就是undo log的产生会伴随着redo log的产生，这是因为undo log也需要持久性的保护。

### 2.2 Undo日志的作用  

- **作用1：回滚数据**

用户对undo日志可能有误解:undo用于将数据库物理地恢复到执行语句或事务之前的样子。但事实并非如此。undo是逻辑日志，因此只是将数据库逻辑地恢复到原来的样子。所有修改都被逻辑地取消了，但是**数据结构和页本身在回滚之后可能大不相同**。

这是因为在多用户并发系统中，可能会有数十、数百甚至数千个并发事务。数据库的主要任务就是协调对数据记录的并发访问。比如，一个事务在修改当前一个页中某几条记录，同时还有别的事务在对同一个页中另几条记录进行修改。因此，不能将一个页回滚到事务开始的样子，因为这样会影响其他事务正在进行的工作。

- **作用2：MVCC**  

undo的另一个作用是MVCC，即在InnoDB存储引擎中MIcc的实现是通过undo来完成。当用户读取一行记录时，若该记录已经被其他事务占用，当前事务可以通过undo读取之前的行版本信息，以此实现非锁定读取。

### 2.3 undo的存储结构

#### 1. 回滚段与undo页

InnoDB对undo log的管理采用段的方式，也就是 **回滚段（rollback segment）** 。每个回滚段记录了**1024** 个 **undo log segment** ，而在每个undo log segment段中进行 undo页 的申请。

- 在 InnoDB1.1版本之前 （不包括1.1版本），只有一个rollback segment，因此支持同时在线的事务限制为 1024 。虽然对绝大多数的应用来说都已经够用。
- 从1.1版本开始InnoDB支持最大 **128个rollback segment** ，故其支持同时在线的事务限制提高到了 **128*1024** 。  

#### 2. 回滚段与事务

1. 每个事务只会使用一个回滚段，一个回滚段在同一时刻可能会服务于多个事务。

2. 当一个事务开始的时候，会制定一个回滚段，在事务进行的过程中，当数据被修改时，原始的数据会被复制到回滚段。

3. 在回滚段中，事务会不断填充盘区，直到事务结束或所有的空间被用完。如果当前的盘区不够用，事务会在段中请求扩展下一个盘区，如果所有已分配的盘区都被用完，事务会覆盖最初的盘区或者在回滚段允许的情况下扩展新的盘区来使用。

4. 回滚段存在于undo表空间中，在数据库中可以存在多个undo表空间，但同一时刻只能使用一个undo表空间。

5. 当事务提交时，InnoDB存储引擎会做以下两件事情：

   将undo log放入列表中，以供之后的purge操作

   判断undo log所在的页是否可以重用，若可以分配给下个事务使用

6.  回滚段中的数据分类  

#### 3. 回滚段中的数据分类

1. **未提交的回滚数据(uncommitted undo information)**:该数据所关联的事务并未提交，用于实现读一致性，所以该数据不能被其他事务的数据覆盖。
2. **已经提交但未过期的回滚数据(committed undo information):**该数据关联的事务已经提交，但是仍受到undo retention参数的保持时间的影响。
3. **事务已经提交并过期的数据(expired undo information)**∶事务已经提交，而且数据保存时间已经超过undo retention参数指定的时间，属于已经过期的数据。当回滚段满了之后，会优先覆盖"事务已经提交并过期的数据"。

事务提交后并不能马上删除undo log及undo log所在的页。这是因为可能还有其他事务需要通过undo log来得到行记录之前的版本。故事务提交时将undo log放入一个链表中，是否可以最终删除undo log及undo log所在页由purge线程来判断。

### 2.4 undo的类型

在InnoDB存储引擎中，undo log分为：

- insert undo log
- update undo log

### 2.5 undo log的生命周期

#### 1. 简要生成过程

**只有Buffer Pool的流程：**  

![image-20220126192950145](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126192950145.png)

**有了Redo Log和Undo Log之后：**  

![image-20220126193014963](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126193014963.png)

#### 2. 详细生成过程  

![image-20220126193323589](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126193323589.png)

当我们执行INSERT时：  

```sql
begin;
INSERT INTO user (name) VALUES ("tom");  
```

![image-20220126193345615](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126193345615.png)

**当我们执行UPDATE时：**  

![image-20220126193356469](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126193356469.png)

```sql
UPDATE user SET id=2 WHERE id=1;  
```

![image-20220126193411418](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126193411418.png)

#### 3. undo log是如何回滚的

以上面的例子来说，假设执行rollback，那么对应的流程应该是这样：

1. 通过undo no=3的日志把id=2的数据删除
2. 通过undo no=2的日志把id=1的数据的deletemark还原成0
3. 通过undo no=1的日志把id=1的数据的name还原成Tom
4. 通过undo no=0的日志把id=1的数据删除  

#### 4. undo log的删除

- 针对于insert undo log

因为insert操作的记录，只对事务本身可见，对其他事务不可见。故该undo log可以在事务提交后直接删除，不需要进行purge操作。

- 针对于update undo log

该undo log可能需要提供MVCC机制，因此不能在事务提交时就进行删除。提交时放入undo log链表，等待purge线程进行最后的删除。  

### 2.6 小结  

![image-20220126193510769](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126193510769.png)

**undo log是逻辑日志**，对事务回滚时，只是将数据库**逻辑地恢复到原来的样子**。
**redo log是物理日志**，记录的是数据页的物理变化，undo log不是redo log的逆过程。  
