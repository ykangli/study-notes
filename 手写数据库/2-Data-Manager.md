---
title: 2-手写数据库之Data Manager(数据管理器)
date: 2022-02-04 16:40:32
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/s8958042.jpg
categories: project
---

# 2-手写数据库之Data Manager(数据管理器)

DM为KangDB中最底层的模块，直接管理数据库文件(DB, database)和日志文件(LF, log file)。 

DM的主要职责大致有下面这些: 

1) 对DB进行Cache, 提高KangDB访问的速率; 
2) 管理日志文件，保证KangDB的可恢复性（在发生错误时可以根据日志进行恢复）; 
3) 分页管理DB, 并提供适当的抽象给上层模块, 使上层模块不用关心DB的读写细节。

**数据库模型如下图：**

![数据库模型](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220127112245276.png)
