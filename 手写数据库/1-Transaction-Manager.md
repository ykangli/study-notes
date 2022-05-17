---
title: 1-手写数据库之Transaction Manager(事务管理器)
date: 2022-01-27 19:02:15
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/00002.jpg
categories: project
---

# 1-手写数据库之Transaction Manager(事务管理器)

TM对事务进行管理, 能够让其他模块查询事务的状态。

**数据库模型如下图：**

![数据库模型](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220127112245276.png)

TM是怎么样标识各个事务的呢? TM会为每个事务, 分配一个特定的XID, 作为其标识. 且这个XID是递增的, 后开始的事务XID要大于先开始的事务。**Transaction Manager（TM） 通过维护 XID 文件来维护事务的状态**，并提供接口供其他模块来查询某个事务的状态。

## XID文件

在KangDB 中，每一个事务都有一个 **XID**，这个 ID **唯一标识**了这个事务。事务的 XID 从 **1** 开始标号，并自增，不可重复。并特殊规定 **XID 0 是一个超级事务（Super Transaction）**。因此如果上级模块想在不申请事务的情况下, 对数据库做一些修改, 则它可以将XID设置为0。**XID 为 0 的事务的状态永远是 committed**。 

**Transaction Manager 维护了一个 XID 格式的文件，用来记录各个事务的状态**。KangDB 中，每个事务都有下面的三种状态：

- **active**	事务正在进行，尚未结束

- **committed**	事务已提交 

- **aborted**	事务回滚，进入中止状态

  **关于事务状态的具体内容详见 《数据库系统概念-第六版》P634**
  
  <img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220114115758371.png" alt="事务的状态"  />

XID 文件给每个事务分配了 **1** 个字节的空间，用来**保存其状态**。同时，在 XID 文件的头部，还保存了一个 **8** 字节的数字 (**用 long 类型来记录，Java中 long 类型占8个字节** )，记录了这个 XID 文件管理的事务的个数。于是，事务 xid 在文件中的状态就存储在 (xid-1)+8 字节处，xid-1 是因为 xid 0（Super XID） 的状态不需要记录。

### TransactionManager 接口

TransactionManager 提供了一些接口供其他模块调用，用来创建事务和查询事务状态。具体如下：

```java
package kangdb.backend.tm;

import kangdb.backend.utils.Panic;
import kangdb.common.Error;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

/**
 * @author ykangli
 * @version 1.0
 * @date 2022/1/27 20:04
 * TransactionManager 对事务进行管理, 能够让其他模块查询事务的状态。
 */
public interface TransactionManager {

    /**
     * 开始一个事务，并返回XID
     * @return xid  (每一个事务都有一个 XID，这个 ID 唯一标识了这个事务)
     */
    long begin();

    /**
     * 提交xid事务
     * @param xid 事务的xid
     */
    void commit(long xid);

    /**
     * 回滚xid事务
     * @param xid 事务的xid
     */
    void abort(long xid);

    /**
     * 查询一个事务的状态是否是正在进行的状态
     * @param xid 事务的xid
     * @return java.lang.boolean
     */
    boolean isActive(long xid);

    /**
     *  查询一个事务的状态是否是已提交
     * @param xid 事务的xid
     * @return java.lang.boolean
     */
    boolean isCommitted(long xid);

    /**
     * 查询一个事务的状态是否是已取消
     * @param xid 事务的xid
     * @return java.lang.boolean
     */
    boolean isAborted(long xid);

    /**
     * 关闭TM
     */
    void close();

    /**
     * 创建一个 xid 文件并创建 Transaction Manager(TM)
     * @param path xid文件路径
     * @return TransactionManagerImpl对象
     */
    public static TransactionManagerImpl create(String path) {
        File file = new File(path + TransactionManagerImpl.XID_SUFFIX);
        try {
            //创建文件失败
            if (!file.createNewFile()) {
                Panic.panic(Error.FileExistsException);
            }
        } catch (Exception e) {
            Panic.panic(e);
        }
        //该函数确定程序是否可以读取或写入由抽象路径名表示的文件。
        if(!file.canRead() || !file.canWrite()) {
            Panic.panic(Error.FileCannotRWException);
        }

        FileChannel fc = null;
        RandomAccessFile raf = null;
        try {
            raf = new RandomAccessFile(file, "rw");
            fc = raf.getChannel();
        } catch (FileNotFoundException e) {
            Panic.panic(e);
        }

        //写只有前8字节Header的空文件，即设置 xidCounter 为 0
        ByteBuffer buffer = ByteBuffer.wrap(new byte[TransactionManagerImpl.LEN_XID_HEADER_LENGTH]);
        try {
            fc.position(0);
            fc.write(buffer);
        } catch (IOException e) {
            Panic.panic(e);
        }
        return new TransactionManagerImpl(raf, fc);
    }
}
```

- **public static TransactionManagerImpl create(String path)**

创建`xxx.xid` 文件，并创建 TM，设置xidCounter 为0。

- **public static TransactionManagerImpl open(String path)**

从一个已有的 xid 文件来创建 TM。

### TransactionManagerImpl实现类

- **[Java RandomAccessFile类](http://c.biancheng.net/view/1137.html)：动态读取文件内容**

所谓动态读取是指从文件的**任意位置开始访问文件**，而不是必须从文件开始位置读取到文件末尾。动态读取需要用到 Java 中的 RandomAccessFile 类。

RandomAccessFile 是 Java 输入/输出流体系中功能最丰富的文件内容访问类，它提供了众多的方法来访问文件内容，它既可以读取文件内容，也可以向文件输出数据。由于 **RandomAccessFile 可以从任意位置访问文件**，所以在只需要访问文件部分内容的情况下，使用 RandonAccessFile 类是一个很好的选择。

RandomAccessFile 对象包含了一个记录指针，用以标识当前读写处的位置，当程序新创建一个 RandomAccessFile 对象时，该对象的文件记录指针位于文件头（也就是 0 处），**当读/写了 n 个字节后，文件记录指针将会向后移动 n 个字节**。除此之外，RandonAccessFile **可以自由移动该记录指针**，既可以向前移动，也可以向后移动。

```
java.io.RandomAccessFile.length()方法 返回当前文件的长度，以字节为单位
```

- **NIO**

文件读写都采用了 NIO 方式的 FileChannel

- **private void checkXIDCounter()**

在构造函数创建了一个 TransactionManager 之后，首先要对 XID 文件进行校验，以保证这是一个合法的 XID 文件。校验的方式也很简单，通过文件头的 8 字节数字**(long 类型) **反推文件的理论长度，与文件的实际长度做对比。如果不同则认为 XID 文件不合法。

```java
/**
     * 检查XID文件是否合法
     * 读取XID_FILE_HEADER中的xidcounter，根据它计算文件的理论长度，对比实际长度
     */
    private void checkXIDCounter() {
        long fileLen = 0;
        try {
            //文件的实际长度
            fileLen = file.length();
        } catch (IOException e1) {
            Panic.panic(Error.BadXIDFileException);
        }
        if (fileLen < LEN_XID_HEADER_LENGTH) {
            Panic.panic(Error.BadXIDFileException);
        }
        //从堆空间中分配一个容量大小为 LEN_XID_HEADER_LENGTH 的byte数组作为缓冲区的byte数据存储器
        ByteBuffer buf = ByteBuffer.allocate(LEN_XID_HEADER_LENGTH);
        try {
            fc.position(0);
            fc.read(buf);
        } catch (IOException e) {
            Panic.panic(e);
        }
        //初始时 xidCounter应该为0，刚开始没有事务开启
        this.xidCounter = Parser.parseLong(buf.array());
        //理论上文件的长度
        long end = getXidPosition(this.xidCounter + 1);
        if (end != fileLen) {
            Panic.panic(Error.BadXIDFileException);
        }
    }
```

初始时，该 `xxx.xid` 文件的长度就为 **8**个字节，记录的xidCounter = 0。 当开启事务时，`xxx.xid` 文件的前八个字节就会变化，相应的xidCounter 也就会变化。

对于校验没有通过的，会直接通过 panic 方法，强制停机。在一些基础模块中出现错误都会如此处理，无法恢复的错误只能直接停机。

```java
package kangdb.backend.utils;

/**
 * @author ykangli
 * @version 1.0
 * @date 2022/1/27 21:30
 * 通过 panic 方法，强制停机。（终止当前正在运行的Java虚拟机）
 */
public class Panic {
    public static void panic(Exception err) {
        err.printStackTrace();
        // status 为 0：表示正常退出程序，也就是结束当前正在运行中的java虚拟机。
        // status 为 1 或 -1 或 任何其他非零值 ：表示非正常退出当前程序。
        System.exit(1);
    }
}l
```

- **private long getXidPosition(long xid)**

```java
/**
 * 根据事务xid取得其在xid文件中对应的位置
 */
private long getXidPosition(long xid) {
    return LEN_XID_HEADER_LENGTH + (xid - 1) * XID_FIELD_SIZE;
}
```

事务 xid 在文件中的状态就存储在 (xid-1)+8 字节处，xid-1 是因为 xid 0（Super XID） 的状态不需要记录。

- **private void updateXID(long xid, byte status)**

```java
/**
 * 更新xid事务的状态为status
 */
private void updateXID(long xid, byte status) {
    long offset = getXidPosition(xid);
    byte[] bytes = new byte[XID_FIELD_SIZE];
    bytes[0] = status;
    ByteBuffer buffer = ByteBuffer.wrap(bytes);
    try {
        fc.position(offset);
        fc.write(buffer);
    } catch (IOException e) {
        Panic.panic(e);
    }
    try {
        //nio中方法，强制同步缓存内容到文件中
        fc.force(false);
    } catch (IOException e) {
        Panic.panic(e);
    }
}
```

所有文件操作，在执行后都**需要立刻刷入文件中**，防止在崩溃后文件丢失数据，**fileChannel** 的` force() `方法，强制同步缓存内容到文件中，类似于 BIO 中的 flush() 方法。force 方法的参数是一个布尔，表示是否同步文件的元数据（例如最后修改时间等）。

- **public long begin()**

`begin()` 方法会开始一个事务，更具体的，首先设置 xidCounter+1 事务的状态为 committed，随后 xidCounter 自增，并更新文件头。

最后利用 `TransactionManagerImpl` 来实现两个方法`public static TransactionManagerImpl create(String path)` 和 `public static TransactionManagerImpl open(String path)`，分别表示创建一个 xid 文件并创建 TM 和从一个已有的 xid 文件来创建 TM。

为了方便管理和使用，没有将这两个方法写在实现类 `TransactionManagerImpl` 里面，而是将其写在接口`TransactionManager`里，也就是**静态方法**，详见 `TransactionManagerImpl` 中的代码。

