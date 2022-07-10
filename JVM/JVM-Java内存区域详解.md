## Java内存模型（Java Memory Model，JMM）

**Java虚拟机规范**中定义了**Java内存模型**（Java Memory Model，JMM），用于屏蔽掉各种**硬件**和**操作系统**的**内存访问差异**，以实现让Java程序在**各种平台下都能达到一致**的并发效果，JMM规范了**Java虚拟机**与**计算机内存**是如何**协同工作**的：规定了**一个线程**如何和何时可以看到由其他线程修改过后的**共享变量的值**，以及在必须时如何同步的访问共享变量。

## Java内存区域详解

### JVM

Java 虚拟机（JVM）是运行 **Java 字节码的虚拟机**。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是**使用相同的字节码，它们都会给出相同的结果**。字节码和不同系统的 JVM 实现是 Java 语言“一次编译，随处可以运行”的关键所在。

### 什么是字节码?采用字节码的好处是什么?

在 Java 中，**JVM 可以理解的代码就叫做字节码**（即扩展名为 `.class` 的文件），它**不面向任何特定的处理器，只面向虚拟机**。Java 语言通过字节码的方式，在一定程度上**解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点**。而且，由于字节码并不针对一种特定的机器，因此，Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。

“Write Once, Run Anywhere（一次编写，随处运行）”

**Java 程序从源代码到运行的过程如下图所示：**

![image-20220322112246834](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322112246834.png)

### 为什么说 Java 语言“编译与解释并存”？

高级编程语言按照程序的执行方式分为两种：

- **编译型** ：[编译型语言open in new window](https://zh.wikipedia.org/wiki/編譯語言) 会通过[编译器open in new window](https://zh.wikipedia.org/wiki/編譯器)将**源代码一次性翻译成可被该平台执行的机器码**。一般情况下，编译语言的执行速度比较快，开发效率比较低。常见的编译性语言有 C、C++、Go、Rust 等等。
- **解释型** ：[解释型语言open in new window](https://zh.wikipedia.org/wiki/直譯語言)会通过[解释器open in new window](https://zh.wikipedia.org/wiki/直譯器)**一句一句**的将代码解释（interpret）为机器代码后再执行。解释型语言开发效率比较快，执行速度比较慢。常见的解释性语言有 Python、JavaScript、PHP 等等。

![image-20220322112501495](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322112501495.png)

因为 Java 语言既具有编译型语言的特征，也具有解释型语言的特征。因为 Java 程序要经过先编译，后解释两个步骤，由 Java 编写的程序需要先经过编译步骤，生成字节码（`.class` 文件），这种字节码必须由 Java 解释器来解释执行。

## 运行时数据区域

Java 虚拟机在执行 Java 程序的过程中会把它管理的内存划分成若干个不同的数据区域。JDK 1.8 和之前的版本略有不同，下图为JDK 1.8之前。

![image-20220322145729806](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322145729806.png)

灰色的为单独线程私有的，红色的是多个线程共享的。

![image-20220322150047892](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322150047892.png)

### 程序计数器

程序计数器是一块较小的内存空间，可以看作是当**前线程所执行的字节码的行号指示器**。

程序计数器用来存储指向下一条指令的地址，也就是将要执行的指令代码。由执行引擎读取下一条指令。

![image-20220322150610376](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322150610376.png)

**字节码解释器工作时通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等功能都需要依赖这个计数器来完成。**

另外，为了**线程切换后能恢复到正确的执行位置**，**每条线程都需要有一个独立的程序计数器，各线程之间计数器互不影响，独立存储**，我们称这类内存区域为“线程私有”的内存。

#### **程序计数器主要有两个作用**

1. JVM的**字节码解释器**通过改变程序计数器来**依次读取指令**，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
2. 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而**当线程被切换回来的时候能够知道该线程上次运行到哪儿了**。

**注意：程序计数器是唯一一个不会出现 `OutOfMemoryError` 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡。**



![image-20220322151334578](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322151334578.png)

程序计数器不存在GC和OOM

### Java 虚拟机栈

与程序计数器一样，**Java 虚拟机栈也是线程私有的，它的生命周期和线程相同**，描述的是 Java 方法执行的内存模型，每次方法调用的数据都是通过栈传递的。

**作用:** 栈内存中主要保存**局部变量、基本数据类型变量**`（基本数据类型的数据的数据都直接存在栈中）`以及**堆内存中某个对象的引用变量**，并参与方法的调用和返回。

![image-20220407120251010](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220407120251010.png)

Java 虚拟机栈是由一个个**栈帧**组成，而每个栈帧中都拥有：**局部变量表、操作数栈、动态链接、方法出口信息。**

**局部变量表主要存放了编译期可知的各种数据类型**（boolean、byte、char、short、int、float、long、double）、**对象引用**（reference 类型，它不同于对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或其他与此对象相关的位置）。

**方法/函数如何调用？**

Java 栈可以类比数据结构中栈，Java 栈中保存的主要内容是**栈帧**，每一次函数调用都会有一个对应的栈帧被压入 Java 栈，每一个函数调用结束后，都会有一个栈帧被弹出。**方法与栈帧一一对应。**

![image-20220322153215186](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322153215186.png)

![image-20220322154812825](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322154812825.png)

Java 方法有两种返回方式：

1. return 语句。
2. 抛出异常。

不管哪种返回方式都会导致栈帧被弹出。

#### OOM

**虚拟机栈不存在GC，但存在OOM**

**Java 虚拟机栈会出现两种错误：`StackOverFlowError` 和 `OutOfMemoryError`。**

- **`StackOverFlowError`：** 若 Java 虚拟机栈的内存大小不允许动态扩展，那么**当线程请求栈的深度超过当前 Java 虚拟机栈的最大深度**的时候，就抛出 StackOverFlowError 错误。
- **`OutOfMemoryError`：** Java 虚拟机栈的内存大小可以动态扩展， 如果虚拟机在动态扩展栈时无法申请到足够的内存空间，则抛出`OutOfMemoryError`异常。

#### 栈帧

![image-20220322155508016](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322155508016.png)

每个栈帧存储着：

- **局部变量表** 	主要存储方法参数和定义在方法体内的局部变量（基本数据类型、对象引用）
- **操作数栈**
- 动态链接（指向运行常量池的方法引用）
- 方法返回地址
- 一些附加信息

### 本地方法栈

本地方法栈线程私有。

和虚拟机栈所发挥的作用非常相似，区别是： **虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。** 在 HotSpot 虚拟机中和 Java 虚拟机栈合二为一。

本地方法被执行的时候，在本地方法栈也会创建一个栈帧，用于存放该本地方法的**局部变量表、操作数栈、动态链接、出口信息**。

方法执行完毕后相应的栈帧也会出栈并释放内存空间，**也会出现 `StackOverFlowError` 和 `OutOfMemoryError` 两种错误。**

### 堆

Java 虚拟机所管理的内存中最大的一块，Java 堆是所有**线程共享的一块内存区域**，在虚拟机启动时创建。**此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存。**

- 数组和对象可能永远不会存储在栈上，因为栈帧种保存引用，这个引用指向对象或者数组在堆中的位置。
- 在方法结束后，**堆中的对象不会马上被移除，仅在垃圾收集的时候才会被移除**。
- Java 堆是垃圾收集器管理的主要区域，因此也被称作**GC 堆（Garbage Collected Heap）**。

![image-20220322171103761](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322171103761.png)

从垃圾回收的角度，由于现在收集器基本都采用分代垃圾收集算法，所以 Java 堆还可以细分为：新生代和老年代；再细致一点有：Eden、Survivor、Old 等空间。**进一步划分的目的是更好地回收内存，或者更快地分配内存。**

![image-20220322172749557](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322172749557.png)

![image-20220322173053590](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322173053590.png)

在 JDK 7 版本及 JDK 7 版本之前，堆内存被通常分为下面三部分：

1. 新生代内存(Young Generation)
2. 老生代(Old Generation)
3. 永久代(Permanent Generation)

**JDK 8 版本之后 PermGen 已被 Metaspace(元空间) 取代，元空间使用的是直接内存。**

#### 堆空间大小的设置和查看

- -Xms 用来设置堆空间（年轻代 + 老年代）的初始内存大小

​		-X 是JVM的运行参数    	-Xms 6122k     -Xms6m

​		ms是memory start

- -Xmx 用来设置堆空间（年轻代 + 老年代）的最大内存大小
- **默认初始为物理电脑内存的 1/64，最大内存大小为物理电脑内存的 1 / 4** 
- 手动设置  -Xms600m  -Xmx600m       开发中建议将初始堆内存和最大堆内存设置成相同的值

#### OOM

堆这里最容易出现的就是 `OutOfMemoryError` 错误，并且出现这种错误之后的表现形式还会有几种，比如：

1. **`java.lang.OutOfMemoryError: GC Overhead Limit Exceeded`** ： 当 JVM 花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。
2. **`java.lang.OutOfMemoryError: Java heap space`** :假如在创建新的对象时, 堆内存中的空间不足以存放新创建的对象, 就会引发此错误。(和配置的最大堆内存有关，且受制于物理内存大小。最大堆内存可通过`-Xmx`参数配置，若没有特别配置，将会使用默认值，详见：[Default Java 8 max heap sizeopen in new window](https://stackoverflow.com/questions/28272923/default-xmxsize-in-java-8-max-heap-size))

Java VisualVM 

![image-20220323105705873](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323105705873.png)

#### 年轻代和老年代

**存储在JVM种的对象可被划分为两类：**

- 一类是生命周期较短的瞬时对象，这类对象的创建和消亡非常迅速
- 另一类对象的生命周期非常常，在某些极端情况下与JVM生命周期保持一致

![image-20220323110159453](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323110159453.png)

**配置新生代和老年代在堆结果中的占比：**

- 默认 -XX:NewRatio=2，表示新生代占1，老年代占2，新生代占整个堆的 1 / 3
- 可以修改 -XX:NewRatio=4，表示表示新生代占1，老年代占4，新生代占整个堆的 1 / 5

#### 对象分配的过程

![image-20220323111912745](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323111912745.png)

大部分情况，**对象都会首先在 Eden 区域分配**

Eden满的时候会出发`YGC / Minor GC` ，S0满的时候不触发GC，但不意味着S0不会进行GC。当Eden满的时候出发GC 会进行Eden和S0的回收。

![image-20220323111954393](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323111954393.png)

在一次新生代垃圾回收后，如果对象还存活，则会进入 S0 或者 S1（看谁空），并且对象的年龄还会加 1(Eden 区->Survivor 区后对象的初始年龄变为 1)，当它的年龄增加到一定程度（**默认为 15 岁**），就会被晋升到老年代中。对象晋升到老年代的年龄阈值，可以通过参数 `-XX:MaxTenuringThreshold` 来设置。

![image-20220323113110376](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323113110376.png)

##### 流程图

![image-20220323113218064](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323113218064.png) 

**针对不同年龄段的对象分配原则如下所示：**

- 优先分配到Eden

- **大对象直接分配到老年代**   为了避免为大对象分配内存时由于分配担保机制带来的复制而降低效率。

  尽量避免程序中出现过多的大对象

- 长期存活的对象分配到老年代

- 动态对象年龄判断	如果Survivor区中**相同年龄的所有对象大小的总和大于Survivor空间的一半**，年龄大于或等于该年龄的对象可以直接进入老年代，无需等到MaxTenuringThreshold中要求的年龄

- 空间分配担保	-XX：HandlePromotionFailure

#### Minot GC、 Major GC、 Full GC

针对 HotSpot VM 的实现，它里面的 GC 其实准确分类只有两大种：

部分收集 (Partial GC)：

- 新生代收集（Minor GC / Young GC）：只对新生代进行垃圾收集；
- 老年代收集（Major GC / Old GC）：只对老年代进行垃圾收集。需要注意的是 Major GC 在有的语境中也用于指代整堆收集；
- 混合收集（Mixed GC）：对整个新生代和部分老年代进行垃圾收集。

整堆收集 (Full GC)：收集整个 Java 堆和方法区。

![rf-hotspot-vm-gc.69291e6e](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/rf-hotspot-vm-gc.69291e6e.png)

发生Minor GC时，虚拟机会检测之前每次晋升到老年代的平均大小是否大于老年代的剩余空间大小。如果大于，则不会进行Minor GC，而是进行一次**Full GC（老年代GC）**，如果小于，则查看**HandlePromotionFailure设置是否允许担保失败**，如果允许，那只会进行一次**Minor GC**，如果不允许，则改为进行一次**Full GC**。

空间分配担保是为了确保在 Minor GC 之前老年代本身还有容纳新生代所有对象的剩余空间。《深入理解 Java 虚拟机》第三章对于空间分配担保的描述如下：

> JDK 6 Update 24 之前，在发生 Minor GC 之前，虚拟机必须先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果这个条件成立，那这一次 Minor GC 可以确保是安全的。如果不成立，则虚拟机会先查看 `-XX:HandlePromotionFailure` 参数的设置值是否允许担保失败(Handle Promotion Failure);如果允许，那会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试进行一次 Minor GC，尽管这次 Minor GC 是有风险的;如果小于，或者 `-XX: HandlePromotionFailure` 设置不允许冒险，那这时就要改为进行一次 Full GC。
>
> JDK 6 Update 24 之后的规则变为只要老年代的连续空间大于新生代对象总大小或者历次晋升的平均大小，就会进行 Minor GC，否则将进行 Full GC。

理解：发生Minor GC时，**虚拟机会检测之前每次晋升到老年代的平均大小是否大于老年代的剩余空间大小**？ 怎么理解？？？？

比较的是之前晋升的对象大小的平均值，就像一个大小为100的空间已经晋升了10个对象，占了95大小的空间，那平均值就是9.5，这时候剩余空间就只剩5，5小于9.5，所以要进行一次full GC

#### 堆空间的参数设置

![image-20220323172148036](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323172148036.png)

### 方法区

方法区与 Java 堆一样，是**各个线程共享的内存区域**，它用于存储已被虚拟机加载的**类信息**、**常量、静态变量**、即时编译器编译后的代码等数据。

**对于方法区的基本理解：**

- 方法区(Method Area）与Java堆一样，是**各个线程共享的内存区域。**
- 方法区在JVM启动的时候被创建，并且它的实际的物理内存空间中和Java堆区一样都可以是不连续的。
- 方法区的大小，跟堆空间一样，可以选择固定大小或者可扩展。
- 方法区的大小决定了系统可以保存多少个类，如果系统定义了太多的类，导致方法区溢出，虚拟机同样会抛出内存溢出错误:`java.lang.outOfMemoryError:PermGen space`或者`java.lang.outofMemoryError: Metaspace`
               加载大量的第三方的jar包;Tomcat部署的工程过多(30-50个）;大量动态的生成反射类
- 关闭JVM就会释放这个区域的内存。

#### 方法区和永久代的关系

Java 虚拟机规范》只是规定了有方法区这么个概念和它的作用，并没有规定如何去实现它。那么，在不同的 JVM 上方法区的实现肯定是不同的了。 **方法区和永久代的关系很像 Java 中接口和类的关系，类实现了接口，而永久代就是 HotSpot 虚拟机对虚拟机规范中方法区的一种实现方式。** 也就是说，**永久代是 HotSpot 的概念**，方法区是 Java 虚拟机规范中的定义，是一种规范，而永久代是一种实现，一个是标准一个是实现，其他的虚拟机实现并没有永久代这一说法。

#### 元空间

JDK 1.8 中，完全废除了永久代的概念，改用在本地内存中实现的元空间（Metaspace）来代替

![image-20220323180314030](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323180314030.png)

- 元空间的本质和永久代类似，都是对JVM规范中方法区的实现。不过元空间与永久代最大的区别在于:**元空间不在虚拟机设置的内存中，而是使用本地内存**
- 永久代、元空间二者并不只是名字变了，内部结构也调整了。
- 根据《Java虚拟机规范》的规定，如果方法区无法满足新的内存分配需求时，将抛出OOM异常。

#### 方法区大小与OOM

**JDK 1.8之前**

```java
-XX:PermSize=N //方法区 (永久代) 初始大小
-XX:MaxPermSize=N //方法区 (永久代) 最大大小,超过这个值将会抛出 OutOfMemoryError 异常:java.lang.OutOfMemoryError: PermGen
```

**JDK 1.8及之后**

JDK 1.8 的时候，方法区（HotSpot 的永久代）被彻底移除了（JDK1.7 就已经开始了），取而代之是元空间，元空间使用的是直接内存。

```java
-XX:MetaspaceSize=N //设置 Metaspace 的初始（和最小大小）
-XX:MaxMetaspaceSize=N //设置 Metaspace 的最大大小
```

与永久代很大的不同就是，如果不指定大小的话，随着更多类的创建，虚拟机会耗尽所有可用的系统内存。

#### 方法区的内部结构

![image-20220323181217112](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323181217112.png)

用于存储已被虚拟机加载的**类型信息**、**常量、静态变量**、即时编译器编译后的代码等数据。

**类型信息：**

对每个加载的类型（类class、接口interface、枚举enum、注解annotation)，JVM必须在方法区中存储以下类型信息:

1. 这个类型的完整有效名称(全名=包名.类名)
2. 这个类型直接父类的完整有效名(对于interface或是java.lang.object，都设有父类)③这个类型的修饰符(public,abstract, final的某个子集)
3. 这个类型直接接口的一个有序列表

**域（Field）信息**

- JVM必须在方法区中保存类型的所有域的相关信息以及域的声明顺序。
- 域的相关信息包括:域名称、域类型、域修饰符(public, private,protected,static,final,volatile, transient的某个子集)

**方法信息**

JVM必须保存所有方法的以下信息，同域信息一样包括声明顺序:

- 方法名称
- 方法的返回类型(或void)方法参数的数量和类型(按顺序)
- 方法的修饰符(public, private, protected,static, final,synchronized, native, abstract的一个子集)
- 方法的字节码(bytecodes)、操作数栈、局部变量表及大小(abstract和native方法除外)
- 异常表(abstract和native方法除外)

>每个异常处理的开始位置、结束位置、代码处理在程序计数器中的偏移地址、
>被捕获的异常类的常量池索引

#### 运行时常量池

运行时常量池是方法区的一部分。Class 文件中除了有类的版本、字段、方法、接口等描述信息外，还有**常量池表**（用于存放编译期生成的各种字面量和符号引用），常量池表这部分内容将在类加载后存放到方法区的运行时常量池中。

既然运行时常量池是方法区的一部分，自然受到方法区内存的限制，当常量池无法再申请到内存时会抛出 OutOfMemoryError 错误。

#### 方法区的演进

new 出来的对象实例本身是一直在堆中的，而对象名是在方法区

1. JDK1.7 之前运行时常量池逻辑包含字符串常量池存放在方法区, 此时 hotspot 虚拟机对方法区的实现为永久代

   ![image-20220323203150001](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323203150001.png)

2. JDK1.7 **字符串常量池、静态变量被从方法区拿到了堆中**, 这里没有提到运行时常量池,也就是说**字符串常量池被单独拿到堆**,运行时常量池**剩下的东西还在方法区**, 也就是 hotspot 中的永久代 。

![image-20220323203212611](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323203212611.png)

1. JDK1.8 hotspot 移除了永久代用元空间(Metaspace)取而代之, **这时候字符串常量池、静态变量还在堆**, 运行时常量池还在方法区, 只不过方法区的实现从永久代变成了元空间(Metaspace)。类型信息，字段，方法，常量保存在本地内存的元空间。

![image-20220323203241539](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323203241539.png)

#### 为什么要将永久代 (PermGen) 替换为元空间 (MetaSpace) 呢?

1. **整个永久代有一个 JVM 本身设置的固定大小上限，无法进行调整**，而元空间使用的是直接内存，受本机可用内存的限制，虽然元空间仍旧可能溢出，但是**比原来出现的几率会更小。**

   > 当元空间溢出时会得到如下错误： `java.lang.OutOfMemoryError: MetaSpace`

   你可以使用 `-XX：MaxMetaspaceSize` 标志设置最大元空间大小，默认值为 unlimited，这意味着它**只受系统内存的限制**。

   `-XX：MetaspaceSize` 调整标志定义元空间的初始大小，如果未指定此标志，则 Metaspace 将根据运行时的应用程序需求动态地重新调整大小。

2. 元空间里面存放的是类的元数据，这样加载多少类的元数据就不由之前的永久代中的 `MaxPermSize` 控制了, 而**由系统的实际可用空间来控制，这样能加载的类就更多了**。

3. 在 JDK8，合并 HotSpot 和 JRockit 的代码时, **JRockit 从来没有一个叫永久代的东西**, 合并之后就没有必要额外的设置这么一个永久代的地方了，也就是为了统一各JVM形式。

![image-20220323210312554](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323210312554.png)

#### 直接内存

直接内存并不是虚拟机运行时数据区的一部分，也不是虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用。而且也可能导致 OutOfMemoryError 错误出现。

直接内存是在Java堆外的、直接向系统申请的内存区间。

JDK1.4 中新加入的 **NIO(New Input/Output) 类**，引入了一种**基于通道（Channel与缓存区（Buffer）的 I/O 方式**，它可以直接使用 Native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样就能在一些场景中显著提高性能，因为避免了在 Java 堆和 Native 堆之间来回复制数据。

访问直接内存的速度会优于Java堆，即读写性能高：

- 出于性能考虑，读写频繁的场合考虑使用直接内存
- Java的NIO库允许Java程序使用直接内存，用作缓冲区

本机直接内存的分配不会受到 Java 堆的限制，但是，既然是内存就会受到本机总内存大小以及处理器寻址空间的限制。

![image-20220325105558543](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220325105558543.png)

![image-20220325105624615](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220325105624615.png)



## 附：本地方法接口

![image-20220322163738165](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322163738165.png)

![image-20220322163807551](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220322163807551.png)

简单地讲，一个Native Method就是一个Java调用非Java代码的接口。

