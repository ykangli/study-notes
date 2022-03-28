# StringTable

------

## String基本特性

- **`String` 类中使用 `final` 关键字修饰字符数组来保存字符串， 表示不可继承。**

🐛 修正 ： 我们知道被 `final` 关键字修饰的类不能被继承，修饰的方法不能被重写，修饰的变量是基本数据类型则值不能改变，修饰的变量是引用类型则不能再指向其他对象。因此，`final` 关键字修饰的数组保存字符串并不是 `String` 不可变的根本原因，因为这个数组保存的字符串是可变的（`final` 修饰引用类型变量的情况）。

- **JDK 8中用` char[ ] `保存字符串，JDK 9改为 `byte[ ]`**

![image-20220325111511043](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220325111511043.png)

- String实现了`Serializable`接口表示**字符串支持序列化**，String实现了`Comparable`接口表示**String可比较大小**。

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    private final char value[];
	//...
}
```

- **字符串常量池不会存储相同内容的字符串**

String的`String Pool`是一个固定大小的`Hashtable`，默认值大小长度是1009。如果放进string Pool的string非常多，就会造成Hash冲突严重，从而导致链表会很长，而链表长了后直接会造成的影响就是当调用`String.intern`时性能会大幅下降。

使用`-XX:StringTablesize`可设置`StringTable`的长度

在jdk6中`StringTable`是固定的，就是1009的长度，所以如果常量池中的字符串过多就会导致效率下降很快。`StringTableSize`设置没有要求在jdk7中，StringTable的长度默认值是60013，1009是可设置的最小值。

## **String 真正不可变原因**

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    private final char value[];
	//...
}
```

1. 保存字符串的数组被 `final` 修饰且为**私有的**，并且`String` 类没有提供/暴露修改这个字符串的方法。
2. `String` 类被 `final` 修饰导致其不能被继承，进而避免了子类破坏 `String` 不可变。

## 字符串拼接操作

- 常量与常量的拼接结果在常量池中，原理是编译期优化

常量指的是`"ab" ` 或者使用` final String s1 = "ab"`

```java
//示例一： 字符串常量
@Test
public void testString() {
    String a = "hello";
    String b = "world";
    String c = "hello" + "world";
    String d = a + b;
    System.out.println(c == d);
}
输出：false  //c的拼接是在字符串常量池中进行；d的拼接是变量的拼接，在堆中（JDK 1.7以前可以说是在堆中，1.7及其之后字符串常量池被移到堆中后，可以表述为在堆中非字符串常量池的位置，见下图比较）
```

![image-20220323203150001](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323203150001.png)

![image-20220323203212611](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220323203212611.png)

```java
//示例二： 常量引用：用final修饰的字符串就是在编译期可知的，编译期就会将以上代码优化
@Test
public void testString() {
    final String a = "hello";
    final String b = "world";
    String c = "helloworld";
    String d = a + b;
    System.out.println(c == d);
}
输出：true //final修饰认为是常量，在字符串常量池中拼接
```

- 常量池中不会存在相同内容
- 只要其中有一个是变量，结果就在堆中。变量拼接原理是StringBuilder.append( )

![image-20220325121528350](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220325121528350.png)

- 如果拼接的结果调用`intern( )`方法，则主动将常量池中没有的字符串放入池中，并返回此对象地址。

## 使用 + 拼接 和 StringBuilder.append()

1. **使用“+”进行字符串的拼接**		

   编译器不会创建单个 `StringBuilder` 以复用，会导致创建过多的 `StringBuilder` 对象，内存占用大。

   如果进行GC，也需要花费时间。

2. **StringBuilder.append()**

​		自始至终只创建一个`StringBuilder`对象

**上边的例子也告诉我们，字符串拼接底层不一定使用的是StringBuilder.append()，如果拼接符号左右两边都是字符串常量或者常量引用，则使用编译期优化。**

## String.intern()方法

String.intern()是一个**Native方法**，底层调用C++的 `StringTable::intern` 方法，源码注释：当调用 intern 方法时，如果常量池中已经该字符串，则返回池中的字符串；否则将此字符串添加到常量池中，并返回字符串的引用。

**字符串常量池比较特殊，它的主要使用方法有两种：**

1. 直接使用双引号声明出来的 `String` 对象会直接存储在字符串常量池中。
2. 如果不是用双引号声明的 `String` 对象，使用 `String` 提供的 `intern()` 方法也有同样的效果。`String.intern()` 是一个 Native 方法，它的作用是：如果字符串常量池中已经包含一个等于此 String 对象内容的字符串，则返回常量池中该字符串的引用；如果没有，JDK1.7 之前（不包含 1.7）的处理方式是在常量池中创建与此 `String` 内容相同的字符串，并返回常量池中创建的字符串的引用，JDK1.7 以及之后，**字符串常量池被从方法区拿到了堆中**，jvm 不会在常量池中创建该对象，而是将**堆中这个对象的引用**直接放到常量池中，减少不必要的内存开销。

以下面两段代码为例深入解析：[美团技术团队深入解析 String.intern()](https://tech.meituan.com/2014/03/06/in-depth-understanding-string-intern.html)

```java
public static void main(String[] args) {
    String s = new String("1");
    s.intern();
    String s2 = "1";
    System.out.println(s == s2);

    String s3 = new String("1") + new String("1");
    s3.intern();
    String s4 = "11";
    System.out.println(s3 == s4);
}
```

打印结果是

- jdk6 下`false false`
- jdk7 下`false true`

然后将`s3.intern();`语句下调一行，放到`String s4 = "11";`后面。将`s.intern();` 放到`String s2 = "1";`后面。是什么结果呢?

```java
public static void main(String[] args) {
    String s = new String("1");
    String s2 = "1";
    s.intern();
    System.out.println(s == s2);

    String s3 = new String("1") + new String("1");
    String s4 = "11";
    s3.intern();
    System.out.println(s3 == s4);
}
```

打印结果为：

- jdk6 下`false false`
- jdk7 下`false false`

![image-20220326101913864](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220326101913864.png)

![image-20220326102002353](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220326102002353.png)

![image-20220326105056526](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220326105056526.png)

**第一段代码解释：**

- 在第一段代码中，先看 s3和s4字符串。`String s3 = new String("1") + new String("1");`，这句代码中现在生成了2最终个对象，是字符串常量池中的“1” 和 JAVA Heap 中的 s3引用指向的对象。中间还有2个匿名的`new String("1")`我们不去讨论它们。此时s3引用对象内容是”11”，但此时常量池中是没有 “11”对象的。
- 接下来`s3.intern();`这一句代码，是将 s3中的“11”字符串放入 String 常量池中，因为此时常量池中不存在“11”字符串，因此常规做法是跟 jdk6 图中表示的那样，在常量池中生成一个 “11” 的对象，关键点是 jdk7 中常量池不在 Perm 区域了，这块做了调整。**常量池中不需要再存储一份对象了，可以直接存储堆中的引用。这份引用指向 s3 引用的对象。 也就是说引用地址是相同的**（就是认为此时常量池中的就是之前在堆中创建的那一个）。
- 最后`String s4 = "11";` 这句代码中”11”是显示声明的，因此会直接去常量池中创建，创建的时候发现已经有这个对象了，此时也就是指向 s3 引用对象的一个引用。所以 s4 引用就指向和 s3 一样了。因此最后的比较 `s3 == s4` 是 true。
- 再看 s 和 s2 对象。 `String s = new String("1");` 第一句代码，生成了2个对象。常量池中的“1” 和 JAVA Heap 中的字符串对象。`s.intern();` 这一句是 s 对象去常量池中寻找后发现 “1” 已经在常量池里了。
- 接下来`String s2 = "1";` 这句代码是生成一个 s2的引用指向常量池中的“1”对象。 结果就是 s 和 s2 的引用地址明显不同。

**第二段代码解释：**

- 第二段代码，从上边第二幅图中观察。第一段代码和第二段代码的改变就是 `s3.intern();` 的顺序是放在`String s4 = "11";`后了。这样，首先执行`String s4 = "11";`声明 s4 的时候常量池中是不存在“11”对象的，执行完毕后，“11“对象是 s4 声明产生的新对象。然后再执行`s3.intern();`时，常量池中“11”对象已经存在了，因此 s3 和 s4 的引用是不同的。
- 第二段代码中的 s 和 s2 代码中，`s.intern();`，这一句往后放也不会有什么影响了，因为对象池中在执行第一句代码`String s = new String("1");`的时候已经生成“1”对象了。下边的s2声明都是直接从常量池中取地址引用的。 s 和 s2 的引用地址是不会相等的。

## **String s1 = new String("abc");这句话创建了几个字符串对象？**

会创建 1 或 2 个字符串：

- 如果字符串常量池中已存在字符串常量“abc”，则只会在堆空间创建一个字符串常量“abc”。
- 如果字符串常量池中没有字符串常量“abc”，那么它将首先在字符串常量池中创建（**在编译期 new 方式创建的字符串就会被放入到编译期的字符串常量池中**），然后在堆空间中创建，栈中的引用指向堆中创建的这个对象，因此将创建总共 2 个字符串对象。

JDK6及以前的内存结构：

![image-20220326094134990](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220326094134990.png)

JDK7（JDK 8之后 无永久代）：

![image-20220326094247695](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220326094247695.png)

**验证：**

```java
String s1 = new String("abc");// 堆内存的地址值
String s2 = "abc";
System.out.println(s1 == s2);// 输出 false,因为一个是堆内存，一个是常量池的内存，故两者是不同的。
System.out.println(s1.equals(s2));// 输出 true
```

**结果：**

```text
false
true
```

**那么问题来了，以下这段代码的执行结果为 true 还是 false？**

```java
String s1 = new String("javaer-wang");
String s2 = new String("javaer-wang");
System.out.println(s1 == s2);
输出：false
```



