---
title: 类加载器及类加载过程
date: 2022-03-08 18:52:24
tags:
---

## 类加载子系统作用

![image-20220308192526085](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308192526085.png)

类加载子系统负责从文件系统或者网络中加载Class文件，class文件在文件开头有特定标识。 

**ClassLoader只负责class文件的加载**，至于它是否可以运行，则由ExecutionEngine决定。

加载的类信息存放于一块称为方法区的内存空间。除了类的信息外，方法区中还会存放运行时常量池信息，可能还包括字符串字面量和数字常量（这部分常量信息是Class文件中常量池部分的内存映射)

## 类加载器ClassLoader角色

![image-20220308192853175](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308192853175.png)

1. class file存在于本地硬盘上，可以理解为设计师画在纸上的模板，而最终这个模板在执行的时候是要加载到JVM当中来根据这个文件实例化出n个一模一样的实例。
2. class file 加载到JVM中，被称为DNA元数据模板，放在方法区。
3. 在.class文件-> JVM->最终成为元数据模板,此过程就要一个运输工具(类装载器class Loader),扮演一个快递员的角色。

## 类的加载过程

一个类的完整生命周期如下：

![image-20220308194525353](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308194525353.png)

类加载过程：**加载->链接->初始化**。

![image-20220308193107754](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308193107754.png)



链接过程又可分为三步：**验证->准备->解析**。

### 类的加载过程一——加载（Loading）

加载:
1．通过一个类的全限定名获取定义此类的二进制字节流
2．将这个字节流所代表的静态存储结构转化为方区的运行时数据结构
3．**在内存中生成一个代表这个类的java.lang.class对象**，作为方法区这个类的各种数据的访问入口

**补充:加载.class文件的方式**

- 从本地系统中直接加载
- 通过网络获取，典型场景: web Applet
- 从zip压缩包中读取，成为日后jar、war格式的基础运行时计算生成，使用最多的是:动态代理技术Ⅰ由其他文件生成，典型场景:JSP应用
- 从专有数据库中提取.class文件，比较少见
- 从加密文件中获取，典型的防Class文件被反编译的保护措施

一个非数组类的加载阶段（加载阶段获取类的二进制字节流的动作）是可控性最强的阶段，这一步我们可以去自定义类加载器去控制字节流的获取方式（重写一个类加载器的 `loadClass()` 方法）。数组类型不通过类加载器创建，它由 Java 虚拟机直接创建。

所有的类都由类加载器加载，**加载的作用就是将 `.class`文件加载到内存。**

### 类的加载过程二——链接（Linking）

#### 验证

![image-20220308194905019](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308194905019.png)

#### 准备

**准备阶段是正式为类变量分配内存并设置类变量初始值的阶段**，这些内存都将在**方法区**中分配。

#### 解析

解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程。解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用限定符 7 类符号引用进行。

符号引用就是一组符号来描述目标，可以是任何字面量。**直接引用**就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。在程序实际运行时，只有符号引用是不够的，举个例子：在程序执行方法时，系统需要明确知道这个方法所在的位置。Java 虚拟机为每个类都准备了一张方法表来存放类中所有的方法。当需要调用一个类的方法的时候，只要知道这个方法在方法表中的偏移量就可以直接调用该方法了。通过解析操作符号引用就可以直接转变为目标方法在类中方法表的位置，从而使得方法可以被调用。

综上，解析阶段是虚拟机将常量池内的符号引用替换为直接引用的过程，也就是得到类或者字段、方法在内存中的指针或者偏移量。

### 类的加载过程三——初始化（Initialization）

**类中的静态代码块就是在 类的初始化（Initialization）阶段被调用，还有静态变量的赋值，都是在这个阶段！！！**

初始化阶段是执行初始化方法 `<clinit> ()`方法的过程，是类加载的最后一步，**这一步 JVM 才开始真正执行类中定义的 Java 程序代码(字节码)。**

```
说明： <clinit> ()方法是编译之后自动生成的。
```

对于初始化阶段，虚拟机严格规范了有且只有 5 种情况下，必须对类进行初始化(只有主动去使用类才会初始化类)：

1. 当遇到new 、getstatic、putstatic或invokestatic这 4 条直接码指令时，比如 new一个类，读取一个静态字段(未被 final 修饰)、或调用一个类的静态方法时。
   - 当 jvm 执行 `new` 指令时会初始化类。即当程序创建一个类的实例对象。
   - 当 jvm 执行 `getstatic` 指令时会初始化类。即程序访问类的静态变量(不是静态常量，常量会被加载到运行时常量池)。
   - 当 jvm 执行 `putstatic` 指令时会初始化类。即程序给类的静态变量赋值。
   - 当 jvm 执行 `invokestatic` 指令时会初始化类。即程序调用类的静态方法。
2. 使用 `java.lang.reflect` 包的方法对类进行反射调用时如 `Class.forname("...")`, `newInstance()` 等等。如果类没初始化，需要触发其初始化。
3. 初始化一个类，如果其父类还未初始化，则先触发该父类的初始化。
4. 当虚拟机启动时，用户需要定义一个要执行的主类 (包含 `main` 方法的那个类)，虚拟机会先初始化这个类。
5. `MethodHandle` 和 `VarHandle` 可以看作是轻量级的反射调用机制，而要想使用这 2 个调用， 就必须先使用 `findStaticVarHandle` 来初始化要调用的类。
6. **「补充，来自[issue745open in new window](https://github.com/Snailclimb/JavaGuide/issues/745)」** 当一个接口中定义了 JDK8 新加入的默认方法（被 default 关键字修饰的接口方法）时，如果有这个接口的实现类发生了初始化，那该接口要在其之前被初始化。

## 类加载器的分类

![image-20220308195946102](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308195946102.png)

这里四者的关系是**包含关系**。不是上层下层，也**不是子父的继承关系**。

JVM 中内置了三个重要的 ClassLoader，**除了 BootstrapClassLoader 其他类加载器均由 Java 实现且全部继承自`java.lang.ClassLoader`：**

1. **BootstrapClassLoader(启动类加载器)** ：**最顶层的加载类**，由 C++实现，负责加载 `%JAVA_HOME%/lib`目录下的 jar 包和类或者被 `-Xbootclasspath`参数指定的路径中的所有类。 

   用来加载Java的核心类库，比如String.

   如果某一个类的加载器是null，说明该加载器是 **BootstrapClassLoader(启动类加载器)** 

2. **ExtensionClassLoader(扩展类加载器)** ：主要负责加载 `%JRE_HOME%/lib/ext` 目录下的 jar 包和类，或被 `java.ext.dirs` 系统变量所指定的路径下的 jar 包。

3. **AppClassLoader 或 SystemClassLoader(应用程序类加载器)** ：面向我们用户的加载器，负责加载当前应用 classpath 下的所有 jar 包和类。

注：

System class loader is a different name for Application class loader.

Source: https://blogs.oracle.com/sundararajan/entry/understanding_java_class_loading

## 用户自定义的类加载器

为什么要自定义的类加载器？

- 修改类加载的方式
- 扩展加载源
- 防止源码泄露   有了字节码文件.class很容易被反编译

**用户自定义类加载器实现步骤:**

1.开发人员可以通过继承抽象类java.lang.classLoader类的方式，实现自己的类加载器，以满足一些特殊的需求

2.在JDK1.2之前，在自定义类加载器时，总会去继承classLoader类并重写loadclass ()方法，从而实现自定义的类加载类，但是在JDK1.2之后已不再建议用户去覆盖loadclass ()方法，而是建议把自定义的类加载逻辑写在findclass ()方法中

3．在编写自定义类加载器时，如果没有太过于复杂的需求，可以直接继承
URLClassLoader类，这样就可以避免自己去编写findclass()方法及其获取字节码流的方式，使自定义类加载器编写更加简洁。

## ClassLoader的常用方法及其获取方式

除了 BootstrapClassLoader 其他类加载器均由 Java 实现且全部继承自`java.lang.ClassLoader`。

![image-20220308203649887](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308203649887.png)

![image-20220308203746184](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308203746184.png)

## 双亲委派机制

**![image-20220308204323759](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308204323759.png)**

**工作原理**

1. 如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行;
2. 如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归,请求最终将到达顶层的启动类加载器;
3. 如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载，这就是双亲委派模

### **举例解释：**

如果自定义一个类String，并把它放在项目的java.lang包中，然后我们去创建String对象，那么到底是使用我们自己定义的String类呢，还是使用JDK中的String呢？？？

1.自定义一个String

![image-20220308205202923](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308205202923.png)



2.测试到底使用了哪个String类？？？

![image-20220308205058863](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220308205058863.png)

上图打印结果为：

```
hello,atguigu.com
```

没有执行自己定义String类里面静态代码块，证明是使用了JDK核心库里面的String类，也解释了双亲委派机制。

因为加载自己定义的类是使用AppClassLoader（应用程序类加载器)，但其不会立马自己去加载，而实先去把这个请求委托给父类的加载器，一直向上到了**BootstrapClassLoader(启动类加载器)** ，BootstrapClassLoader(启动类加载器)可以**java.lang包**中的类，所以加载的是核心API中的String，而不是我们自己定义的。

### 双亲委派模型的优势

双亲委派模型保证了 Java 程序的稳定运行，可以**避免类的重复加载**。（JVM 区分不同类的方式不仅仅根据类名，相同的类文件被不同的类加载器加载产生的是两个不同的类），也保证了 Java 的核心 API 不被篡改。如果没有使用双亲委派模型，而是每个类加载器加载自己的话就会出现一些问题，比如我们编写一个称为 `java.lang.String` 类的话，那么程序运行的时候，系统就会出现多个不同的 `String` 类。

附录：

深入理解Java虚拟机》P228：对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立其在Java虚拟机中的唯一性，每一个类加载器，都拥有一个独立的类名称空间。这句话可以表达得更通俗一些：**比较两个类是否“相等”，只有在这两个类是由同一个类加载器加载的前提下才有意义，否则，即使这两个类来源于同一个Class文件，被同一个虚拟机加载，只要加载它们的类加载器不同，那么这两个类必定不相等**。接口类是一种特殊类，因此对于同一接口不同的类装载器装载所获得的类是不相同的。
