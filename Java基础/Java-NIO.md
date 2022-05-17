---
title: Java-NIO
date: 2022-02-02 14:54:42
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/w_java71c8.jpg
---

#  第一章 BIO、NIO、AIO课程介绍

## 1.1 课程说明

 在Java的软件设计开发中，通信架构是不可避免的，我们在进行不同系统或者不同进程之间的数据交互，或者在高并发下的通信场景下都需要用到网络通信相关的技术，对于一些经验丰富的程序员来说，Java早期的网络通信架构存在一些缺陷，**其中最令人恼火的是基于性能低下的同步阻塞式的I/O通信（BIO）**，随着互联网开发下通信性能的高要求，Java在2002年开始支持了非阻塞式的I/O通信技术(NIO)。大多数读者在学习网络通信相关技术的时候，都只是接触到零碎的通信技术点，没有完整的技术体系架构，以至于对于Java的通信场景总是没有清晰的解决方案。本次课程将通过大量清晰直接的案例从最基础的BIO式通信开始介绍到NIO , AIO，读者可以清晰的了解到阻塞、同步、异步的现象、概念和特征以及优缺点。本课程结合了大量的案例让读者可以快速了解每种通信架构的使用。

## 1.2 本课程学习要求

- 本课程不太适合完全0基础学员。
- 至少需要掌握: Java SE基础编程，如Java多线程，Java IO流编程，Java网络基础知识（如：IP , 端口，协议），常用的Java设计模式要有一定的了解。
- 能熟练掌握Java OOP编程，有一定的编程思维。

## 1.3 通信技术整体解决的问题

- 局域网内的通信要求。
- 多系统间的底层消息传递机制。
- 高并发下，大数据量的通信场景需要。
- 游戏行业。无论是手游服务端，还是大型的网络游戏，Java语言都得到越来越广泛的应用。

#  第二章 Java的I/O演进之路

##  2.1 I/O 模型基本说明

I/O 模型：就是用什么样的通道或者说是通信模式和架构进行数据的传输和接收，很大程度上决定了程序通信的性能，Java 共支持 3 种网络编程的/IO 模型：**BIO、NIO、AIO** 实际通信需求下，要根据不同的业务场景和性能需求决定选择不同的I/O模型

##  2.2 I/O模型

#### Java BIO

**同步并阻塞**(传统阻塞型)，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器 端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销 【简单示意图】

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220202150239257.png" alt="image-20220202150239257" style="zoom:67%;" />

####  Java NIO

Java NIO ： **同步非阻塞**，服务器实现模式为一个线程处理多个请求(连接)，即客户端发送的连接请求都会注 册到多路复用器上，多路复用器轮询到连接有 I/O 请求就进行处理 【简单示意图】

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220202150345643.png" alt="image-20220202150345643" style="zoom:67%;" />

####  Java AIO

Java AIO(NIO.2) ：**异步非阻塞**，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理，一般适用于连接数较多且连接时间较长的应用

##  2.3 BIO、NIO、AIO 适用场景分析

1、**BIO** 方式适用于**连接数目比较小且固定的架构**，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序简单易理解。 

2、**NIO** 方式适用于**连接数目多且连接比较短（轻操作）**的架构，比如聊天服务器，弹幕系统，服务器间通讯等。 编程比较复杂，JDK1.4 开始支持。

3、**AIO** 方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用 OS 参与并发操作， 编程比较复杂，JDK7 开始支持。

#  第三章 JAVA BIO深入剖析

## 3.1 Java BIO 基本介绍

- Java BIO 就是传统的 java io 编程，其相关的类和接口在 java.io
- BIO(blocking I/O) ： 同步阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，可以通过**线程池机制**改善(实现多个客户连接服务器).

##  3.2 Java BIO 工作机制

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220202151042311.png" alt="image-20220202151042311" style="zoom: 80%;" />

##  3.3 传统的BIO编程实例回顾

 网络编程的基本模型是Client/Server模型，也就是两个进程之间进行相互通信，其中服务端提供位置信（绑定IP地址和端口），客户端通过连接操作向服务端监听的端口地址发起连接请求，基于TCP协议下进行三次握手连接，连接成功后，双方通过网络套接字（Socket）进行通信。

 传统的同步阻塞模型开发中，服务端ServerSocket负责绑定IP地址，启动监听端口；客户端Socket负责发起连接操作。连接成功后，双方通过输入和输出流进行**同步阻塞式通信**。 基于BIO模式下的通信，客户端 - 服务端是完全同步，完全耦合的。

###  客户端案例如下

```java
package com.itheima._02bio01;

import java.io.OutputStream;
import java.io.PrintStream;
import java.net.Socket;
/**
    目标: Socket网络编程。

    Java提供了一个包：java.net下的类都是用于网络通信。
    Java提供了基于套接字（端口）Socket的网络通信模式，我们基于这种模式就可以直接实现TCP通信。
    只要用Socket通信，那么就是基于TCP可靠传输通信。

    功能1：客户端发送一个消息，服务端接口一个消息，通信结束！！

    创建客户端对象：
        （1）创建一个Socket的通信管道，请求与服务端的端口连接。
        （2）从Socket管道中得到一个字节输出流。
        （3）把字节流改装成自己需要的流进行数据的发送
    创建服务端对象：
        （1）注册端口
        （2）开始等待接收客户端的连接,得到一个端到端的Socket管道
        （3）从Socket管道中得到一个字节输入流。
        （4）把字节输入流包装成自己需要的流进行数据的读取。

    Socket的使用：
        构造器：public Socket(String host, int port)
        方法：  public OutputStream getOutputStream()：获取字节输出流
               public InputStream getInputStream() :获取字节输入流

    ServerSocket的使用：
        构造器：public ServerSocket(int port)

    小结：
        通信是很严格的，对方怎么发你就怎么收，对方发多少你就只能收多少！！

 */
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("==客户端的启动==");
        // （1）创建一个Socket的通信管道，请求与服务端的端口连接。
        Socket socket = new Socket("127.0.0.1",8888);
        // （2）从Socket通信管道中得到一个字节输出流。
        OutputStream os = socket.getOutputStream();
        // （3）把字节流改装成自己需要的流进行数据的发送
        PrintStream ps = new PrintStream(os);
        // （4）开始发送消息
        ps.println("我是客户端，我想约你吃小龙虾！！！");
        ps.flush();
    }
}
```

###  服务端案例如下

```java
package com.itheima._02bio01;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * 服务端
 */
public class ServerDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("==服务器的启动==");
        // （1）注册端口
        ServerSocket serverSocket = new ServerSocket(8888);
        //（2）开始在这里暂停等待接收客户端的连接,得到一个端到端的Socket管道
        Socket socket = serverSocket.accept();
        //（3）从Socket管道中得到一个字节输入流。
        InputStream is = socket.getInputStream();
        //（4）把字节输入流包装成自己需要的流进行数据的读取。
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        //（5）读取数据
        String line ;
        while((line = br.readLine())!=null){
            System.out.println("服务端收到："+line);
        }
    }
}
```

###  小结

- 在以上通信中，服务端会一直等待客户端的消息，如果客户端没有进行消息的发送，服务端将一直进入阻塞状态。
- 同时服务端是按照行获取消息的，这意味着客户端也必须按照行进行消息的发送，否则服务端将进入等待消息的阻塞状态！

##  3.4 BIO模式下多发和多收消息

在1.3的案例中，**只能实现客户端发送消息，服务端接收消息**，并不能实现反复的收消息和反复的发消息，我们只需要在客户端案例中，加上反复按照行发送消息的逻辑即可！案例代码如下：

```java
package com.itheima._03bio02;

import java.io.OutputStream;
import java.io.PrintStream;
import java.net.Socket;
import java.util.Scanner;

/**
    目标: Socket网络编程。

    功能1：客户端可以反复发消息，服务端可以反复收消息

    小结：
        通信是很严格的，对方怎么发你就怎么收，对方发多少你就只能收多少！！

 */
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("==客户端的启动==");
        // （1）创建一个Socket的通信管道，请求与服务端的端口连接。
        Socket socket = new Socket("127.0.0.1",8888);
        // （2）从Socket通信管道中得到一个字节输出流。
        OutputStream os = socket.getOutputStream();
        // （3）把字节流改装成自己需要的流进行数据的发送
        PrintStream ps = new PrintStream(os);
        // （4）开始发送消息
        Scanner sc = new Scanner(System.in);
        while(true){
            System.out.print("请说:");
            String msg = sc.nextLine();
            ps.println(msg);
            ps.flush();
        }
    }
}
```

###  服务端代码如下

```java
package com.itheima._03bio02;

import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.ServerSocket;
import java.net.Socket;

/**
 * 服务端
 */
public class ServerDemo {
    public static void main(String[] args) throws Exception {
        String s = "886";
        System.out.println("886".equals(s));
        System.out.println("==服务器的启动==");
        //（1）注册端口
        ServerSocket serverSocket = new ServerSocket(8888);
        //（2）开始在这里暂停等待接收客户端的连接,得到一个端到端的Socket管道
        Socket socket = serverSocket.accept();
        //（3）从Socket管道中得到一个字节输入流。
        InputStream is = socket.getInputStream();
        //（4）把字节输入流包装成  自己需要的流进行数据的读取。
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        //（5）读取数据
        String line ;
        while((line = br.readLine())!=null){
            System.out.println("服务端收到："+line);
        }
    }
}
```

###  小结

- 本案例中确实可以实现客户端多发多收
- 但是**服务端只能处理一个客户端的请求**，因为服务端是单线程的。**一次只能与一个客户端进行消息通信**。

##  3.5 BIO模式下接收多个客户端

### 概述

在上述的案例中，一个服务端只能接收一个客户端的通信请求，**那么如果服务端需要处理很多个客户端的消息通信请求应该如何处理呢**，此时我们就需要**在服务端引入线程**了，也就是说**客户端每发起一个请求，服务端就创建一个新的线程**来处理这个客户端的请求，这样就实现了一个客户端一个线程的模型，图解模式如下：

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220202152550833.png" alt="image-20220202152550833" style="zoom: 80%;" />

###  客户端案例代码如下

```java
/**
    目标: 实现服务端可以同时接受多个客户端的Socket通信需求

    思路：是服务端每接受一个客户端socket请求对象之后都交给一个独立的线程来处理客户端的数据交互需求

 */
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("==客户端的启动==");
        // （1）创建一个Socket的通信管道，请求与服务端的端口连接。
        Socket socket = new Socket("127.0.0.1",7777);
        // （2）从Socket通信管道中得到一个字节输出流。
        OutputStream os = socket.getOutputStream();
        // （3）把字节流改装成自己需要的流进行数据的发送
        PrintStream ps = new PrintStream(os);
        // （4）开始发送消息
        Scanner sc = new Scanner(System.in);
        while(true){
            System.out.print("请说:");
            String msg = sc.nextLine();
            ps.println(msg);
            ps.flush();
        }
    }
}
```

###  服务端案例代码如下

```java
/**
    服务端
 */
public class ServerDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("==服务器的启动==");
        // （1）注册端口
        ServerSocket serverSocket = new ServerSocket(7777);
        while(true){
            //（2）开始在这里暂停等待接收客户端的连接,得到一个端到端的Socket管道
            Socket socket = serverSocket.accept();
            new ServerReadThread(socket).start();
            System.out.println(socket.getRemoteSocketAddress()+"上线了！");
        }
    }
}

class ServerReadThread extends Thread{
    private Socket socket;

    public ServerReadThread(Socket socket){
        this.socket = socket;
    }

    @Override
    public void run() {
        try{
            //（3）从Socket管道中得到一个字节输入流。
            InputStream is = socket.getInputStream();
            //（4）把字节输入流包装成自己需要的流进行数据的读取。
            BufferedReader br = new BufferedReader(new InputStreamReader(is));
            //（5）读取数据
            String line ;
            while((line = br.readLine())!=null){
                System.out.println("服务端收到："+socket.getRemoteSocketAddress()+":"+line);
            }
        }catch (Exception e){
            System.out.println(socket.getRemoteSocketAddress()+"下线了！");
        }
    }
}
```

###  小结

- 1.每个Socket接收到，都会创建一个线程，线程的竞争、切换上下文影响性能；
- 2.每个线程都会占用栈空间和CPU资源；
- 3.并不是每个socket都进行IO操作，无意义的线程处理；
- 4.客户端的并发访问增加时。服务端将呈现1:1的线程开销，访问量越大，系统将发生线程栈溢出，线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务。

##  3.6 伪异步I/O编程

### 概述

在上述案例中：客户端的并发访问增加时。服务端将呈现1:1的线程开销，访问量越大，系统将发生线程栈溢出，线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务。

接下来我们采用一个伪异步I/O的通信框架，采用**线程池**和**任务队列**实现，当客户端接入时，将客户端的Socket封装成一个Task(该任务实现java.lang.Runnable线程任务接口)交给后端的线程池中进行处理。JDK的线程池维护一个消息队列和N个活跃的线程，对消息队列中Socket任务进行处理，由于线程池可以设置消息队列的大小和最大线程数，因此，它的资源占用是可控的，无论多少个客户端并发访问，都不会导致资源的耗尽和宕机。

图示如下:

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220202153828332.png" alt="image-20220202153828332" style="zoom:80%;" />

###  客户端源码分析

```java
public class Client {
   public static void main(String[] args) {
      try {
         // 1.简历一个与服务端的Socket对象：套接字
         Socket socket = new Socket("127.0.0.1", 9999);
         // 2.从socket管道中获取一个输出流，写数据给服务端 
         OutputStream os = socket.getOutputStream() ;
         // 3.把输出流包装成一个打印流 
         PrintWriter pw = new PrintWriter(os);
         // 4.反复接收用户的输入 
         BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
         String line = null ;
         while((line = br.readLine()) != null){
            pw.println(line);
            pw.flush();
         }
      } catch (Exception e) {
         e.printStackTrace();
      }
   }
}
```

###  线程池处理类

```java
// 线程池处理类
public class HandlerSocketThreadPool {
   
   // 线程池 
   private ExecutorService executor;
   
   public HandlerSocketThreadPool(int maxPoolSize, int queueSize){
      
      this.executor = new ThreadPoolExecutor(
            3, // 8
            maxPoolSize,  
            120L, 
            TimeUnit.SECONDS,
            new ArrayBlockingQueue<Runnable>(queueSize) );
   }
   
   public void execute(Runnable task){
      this.executor.execute(task);
   }
}
```

###  服务端源码分析

```java
/**
	目标：开发实现伪异步通信架构
/

public class Server {
   public static void main(String[] args) {
      try {
         System.out.println("----------服务端启动成功------------");
         ServerSocket ss = new ServerSocket(9999);

         // 一个服务端只需要对应一个线程池
         HandlerSocketThreadPool handlerSocketThreadPool =
               new HandlerSocketThreadPool(3, 1000);

         // 客户端可能有很多个
         while(true){
            Socket socket = ss.accept() ; // 阻塞式的！
            System.out.println("有人上线了！！");
            // 每次收到一个客户端的socket请求，都需要为这个客户端分配一个
            // 独立的线程 专门负责对这个客户端的通信！！
            handlerSocketThreadPool.execute(new ReaderClientRunnable(socket));
         }

      } catch (Exception e) {
         e.printStackTrace();
      }
   }

}
class ReaderClientRunnable implements Runnable{

   private Socket socket ;

   public ReaderClientRunnable(Socket socket) {
      this.socket = socket;
   }

   @Override
   public void run() {
      try {
         // 读取一行数据
         InputStream is = socket.getInputStream() ;
         // 转成一个缓冲字符流
         Reader fr = new InputStreamReader(is);
         BufferedReader br = new BufferedReader(fr);
         // 一行一行的读取数据
         String line = null ;
         while((line = br.readLine())!=null){ // 阻塞式的！！
            System.out.println("服务端收到了数据："+line);
         }
      } catch (Exception e) {
         System.out.println("有人下线了");
      }
   }
}
```

###  小结

- 伪异步io采用了线程池实现，因此避免了为每个请求创建一个独立线程造成线程资源耗尽的问题，但由于底层依然是采用的同步阻塞模型，因此无法从根本上解决问题。
- 如果单个消息处理的缓慢，或者服务器线程池中的全部线程都被阻塞，那么后续socket的i/o消息都将在队列中排队。新的Socket请求将被拒绝，客户端会发生大量连接超时。

##  3.7 基于BIO形式下的文件上传

###  目标

支持任意类型文件形式的上传。

### 客户端开发

```java
package com.itheima.file;

import java.io.DataOutputStream;
import java.io.FileInputStream;
import java.io.InputStream;
import java.net.Socket;

/**
    目标：实现客户端上传任意类型的文件数据给服务端保存起来。

 */
public class Client {
    public static void main(String[] args) {
        try(
                InputStream is = new FileInputStream("C:\\Users\\dlei\\Desktop\\BIO,NIO,AIO\\文件\\java.png");
        ){
            //  1、请求与服务端的Socket链接
            Socket socket = new Socket("127.0.0.1" , 8888);
            //  2、把字节输出流包装成一个数据输出流
            DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
            //  3、先发送上传文件的后缀给服务端
            dos.writeUTF(".png");
            //  4、把文件数据发送给服务端进行接收
            byte[] buffer = new byte[1024];
            int len;
            while((len = is.read(buffer)) > 0 ){
                dos.write(buffer , 0 , len);
            }
            dos.flush();
            Thread.sleep(10000);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

###  服务端开发

```java
package com.itheima.file;

import java.net.ServerSocket;
import java.net.Socket;

/**
    目标：服务端开发，可以实现接收客户端的任意类型文件，并保存到服务端磁盘。
 */
public class Server {
    public static void main(String[] args) {
        try{
            ServerSocket ss = new ServerSocket(8888);
            while (true){
                Socket socket = ss.accept();
                // 交给一个独立的线程来处理与这个客户端的文件通信需求。
                new ServerReaderThread(socket).start();
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

```java
package com.itheima.file;

import java.io.DataInputStream;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.net.Socket;
import java.util.UUID;

public class ServerReaderThread extends Thread {
    private Socket socket;
    public ServerReaderThread(Socket socket){
        this.socket = socket;
    }
    @Override
    public void run() {
        try{
            // 1、得到一个数据输入流读取客户端发送过来的数据
            DataInputStream dis = new DataInputStream(socket.getInputStream());
            // 2、读取客户端发送过来的文件类型
            String suffix = dis.readUTF();
            System.out.println("服务端已经成功接收到了文件类型：" + suffix);
            // 3、定义一个字节输出管道负责把客户端发来的文件数据写出去
            OutputStream os = new FileOutputStream("C:\\Users\\dlei\\Desktop\\BIO,NIO,AIO\\文件\\server\\"+
                    UUID.randomUUID().toString()+suffix);
            // 4、从数据输入流中读取文件数据，写出到字节输出流中去
            byte[] buffer = new byte[1024];
            int len;
            while((len = dis.read(buffer)) > 0){
                os.write(buffer,0, len);
            }
            os.close();
            System.out.println("服务端接收文件保存成功！");

        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

###  小结

客户端怎么发，服务端就怎么接收
