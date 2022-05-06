# Java hashCode() 和 equals()的若干问题解答

------

## equals() 的作用

`equals() `的作用是 **用来判断两个对象是否相等**。`equals()`方法存在于`Object`类中，而`Object`类是所有类的直接或间接父类，因此所有的类都有`equals()`方法。

`Object` 类 `equals()` 方法：

```java
public boolean equals(Object obj) {
     return (this == obj);
}
```

`equals()` 方法存在两种使用情况：

1. 若某个类没有覆盖equals()方法，当它的通过equals()比较两个对象时，实际上是比较两个对象是不是同一个对象。这时，等价于通过“==”去比较这两个对象。
2. 我们可以覆盖类的equals()方法，来让equals()通过其它方式比较两个对象是否相等。通常的做法是：若两个对象的内容相等，则equals()方法返回true；否则，返回fasle。

### **“没有覆盖equals()方法”的情况**

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc equals()的测试程序。
 *
 * @author skywang
 * @emai kuiwu-wang@163.com
 */
public class EqualsTest1{

    public static void main(String[] args) {
        // 新建2个相同内容的Person对象，
        // 再用equals比较它们是否相等
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        System.out.printf("%s\n", p1.equals(p2));
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }
    }
}
```

**运行结果**：

```
false
```

**结果分析**：

​    我们通过 p1.equals(p2) 来“比较p1和p2是否相等时”。实际上，调用的Object.java的equals()方法，即调用的 (p1==p2) 。它是比较“p1和p2是否是同一个对象”。
​    而由 p1 和 p2 的定义可知，它们虽然内容相同；但它们是两个不同的对象！因此，返回结果是false。

### "**覆盖equals()方法**"**的情况**

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc equals()的测试程序。
 *
 * @author skywang
 * @emai kuiwu-wang@163.com
 */
public class EqualsTest2{

    public static void main(String[] args) {
        // 新建2个相同内容的Person对象，
        // 再用equals比较它们是否相等
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        System.out.printf("%s\n", p1.equals(p2));
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }

        /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
```

**运行结果**：

```
true
```

**结果分析**：

我们在EqualsTest2.java 中重写了Person的equals()函数：当两个Person对象的 name 和 age 都相等，则返回true。
因此，运行结果返回true。

讲到这里，顺便说一下java对equals()的要求。有以下几点：

```java
1. 对称性：如果x.equals(y)返回是"true"，那么y.equals(x)也应该返回是"true"。
2. 反射性：x.equals(x)必须返回是"true"。
3. 类推性：如果x.equals(y)返回是"true"，而且y.equals(z)返回是"true"，那么z.equals(x)也应该返回是"true"。
4. 一致性：如果x.equals(y)返回是"true"，只要x和y内容一直不变，不管你重复x.equals(y)多少次，返回都是"true"。
5. 非空性，x.equals(null)，永远返回是"false"；x.equals(和x不同类型的对象)永远返回是"false"。
```

现在，再回顾一下equals()的作用：判断两个对象是否相等。当我们重写equals()的时候，可千万不好将它的作用给改变了！

##  **equals() 与 == 的区别是什么？**

(1) **`==`** 对于基本类型和引用类型的作用效果是不同的：

- 对于基本数据类型来说，`==` 比较的是值。
- 对于引用数据类型来说，`==` 比较的是对象的内存地址。

> 因为 Java 只有值传递，所以，对于 == 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址。

(2) equals() : 它的作用也是**判断两个对象是否相等。**但它一般有两种使用情况(前面第1部分已详细介绍过)：
         情况1，类没有覆盖equals()方法。则通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象。
         情况2，类覆盖了equals()方法。一般，我们都覆盖equals()方法来两个对象的内容相等；若它们的内容相等，则返回true(即，认为这两个对象相等)。

## **hashCode() 的作用**

hashCode() 的作用是**获取哈希码**，也称为散列码；它实际上是返回一个int整数。这个**哈希码的作用**是确定该对象在哈希表中的索引位置。

hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。

虽然，每个Java类都包含hashCode() 函数。但是，**仅仅当创建某个“类的散列表”(关于“散列表”见下面说明)时，该类的hashCode() 才有用**

作用是：确定该类的每一个对象在散列表中的位置；其它情况下(例如，创建类的单个对象，或者创建类的对象数组等等)，类的hashCode() 没有作用。

上面的散列表，指的是：Java集合中本质是散列表的类，如HashMap，Hashtable，HashSet。

也就是说：**hashCode() 在散列表中才有用，在其它情况下没用。**在散列表中hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的位置。

## **hashCode() 和 equals() 的关系**

以“**类的用途**”来将“hashCode() 和 equals()的关系”分2种情况来说明。

### **不会创建“类对应的散列表”**

这里所说的“不会创建类对应的散列表”是说：我们不会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中，用到该类。例如，**不会创建该类的HashSet集合**。

在这种情况下，该类的“hashCode() 和 equals() ”没有半毛钱关系的！

这种情况下，equals() 用来比较该类的两个对象是否相等。而hashCode() 则根本没有任何作用，所以，不用理会hashCode()。

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc 比较equals() 返回true 以及 返回false时， hashCode()的值。
 *
 * @author skywang
 * @emai kuiwu-wang@163.com
 */
public class NormalHashCodeTest{

    public static void main(String[] args) {
        // 新建2个相同内容的Person对象，
        // 再用equals比较它们是否相等
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        System.out.printf("p1.equals(p3) : %s; p1(%d) p3(%d)\n", p1.equals(p3), p1.hashCode(), p3.hashCode());
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }

        /**
         * @desc 覆盖equals方法
         */
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
```

**运行结果**：

```
p1.equals(p2) : true; p1(1169863946) p2(1901116749)
p1.equals(p3) : false; p1(1169863946) p3(2131949076)
```

从结果也可以看出：**p1和p2相等的情况下，hashCode()也不一定相等。**

这种情况下，虽然没有重写hashCode()，但不影响功能的使用通过，仅重写equals()方法也能判断出来两个对象是否相等。

但当遇到 HashSet这种时，如果不重写 hashCode() 就会发生错误。

###  **会创建“类对应的散列表”**

这里所说的“会创建类对应的散列表”是说：我们会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中，用到该类。例如，会创建该类的HashSet集合。

在这种情况下，该类的“hashCode() 和 equals() ”是有关系的：

1.  如果两个对象相等，那么它们的hashCode()值一定相同。这里的相等是指，通过equals()比较两个对象时返回true。

2.  如果两个对象hashCode()相等，它们并不一定相等。
           

   因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。补充说一句：“两个不同的键值对，哈希值相等”，这就是哈希冲突。

​    此外，在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。
例如，创建Person类的HashSet集合，必须同时覆盖Person类的equals() 和 hashCode()方法。

 如果单单只是覆盖equals()方法。我们会发现，equals()方法没有达到我们想要的效果。

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc 比较equals() 返回true 以及 返回false时， hashCode()的值。
 *
 * @author skywang
 * @emai kuiwu-wang@163.com
 */
public class ConflictHashCodeTest1{

    public static void main(String[] args) {
        // 新建Person对象，
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);

        // 新建HashSet对象
        HashSet set = new HashSet();
        set.add(p1);
        set.add(p2);
        set.add(p3);

        // 比较p1 和 p2， 并打印它们的hashCode()
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        // 打印set
        System.out.printf("set:%s\n", set);
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return "("+name + ", " +age+")";
        }

        /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
```

**运行结果**：

```
p1.equals(p2) : true; p1(1169863946) p2(1690552137)
set:[(eee, 100), (eee, 100), (aaa, 200)]
```

**结果分析**：

​    我们重写了Person的equals()。但是，很奇怪的发现：HashSet中仍然有重复元素：p1 和 p2。为什么会出现这种情况呢？

​    这是因为虽然p1 和 p2的内容相等，但是它们的hashCode()不等；所以，HashSet在添加p1和p2的时候，认为它们不相等。

下面，我们同时覆盖equals() 和 hashCode()方法。

```java
import java.util.*;
import java.lang.Comparable;

/**
 * @desc 比较equals() 返回true 以及 返回false时， hashCode()的值。
 *
 * @author skywang
 * @emai kuiwu-wang@163.com
 */
public class ConflictHashCodeTest2{

    public static void main(String[] args) {
        // 新建Person对象，
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);
        Person p4 = new Person("EEE", 100);

        // 新建HashSet对象
        HashSet set = new HashSet();
        set.add(p1);
        set.add(p2);
        set.add(p3);

        // 比较p1 和 p2， 并打印它们的hashCode()
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        // 比较p1 和 p4， 并打印它们的hashCode()
        System.out.printf("p1.equals(p4) : %s; p1(%d) p4(%d)\n", p1.equals(p4), p1.hashCode(), p4.hashCode());
        // 打印set
        System.out.printf("set:%s\n", set);
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }

        /**
         * @desc重写hashCode
         */
        @Override
        public int hashCode(){
            int nameHash =  name.toUpperCase().hashCode();
            return nameHash ^ age;
        }

        /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
```

**运行结果**：

```
p1.equals(p2) : true; p1(68545) p2(68545)
p1.equals(p4) : false; p1(68545) p4(68545)
set:[aaa - 200, eee - 100]
```

**结果分析**：

​    这下，equals()生效了，HashSet中没有重复元素。
​    *比较p1和p2*，我们发现：它们的hashCode()相等，通过equals()比较它们也返回true。所以，p1和p2被视为相等。
​    *比较p1和p4*，我们发现：虽然它们的hashCode()相等；但是，通过equals()比较它们返回false。所以，p1和p4被视为不相等。

### 🔥总结

**hashCode方法的重写原则就是保证equals方法认定为相同的两个对象拥有相同的哈希值**”。

如果不能保证的话，在使用HashSet, Hashtable, HashMap等这些散列表的数据结构中进行操作时就会出现上面重复添加的错误。