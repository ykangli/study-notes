# MyBatis框架

------

## #{ }和${ }的区别是什么？

`MyBatis`中使用`parameterType`向`SQL`语句**传参**，`parameterType`支持的类型可以是基本类型`int,String,HashMap`和`java`自定义类型。

在SQL中**引用这些参数**的时候，可以使用**两种方式：**

1. **#{parameterName}**
2. **${parameterName}**

**#{ } 方式**

\#{}: 解析为`SQL`时，会将形参变量的值取出，并**自动给其添加引号**。 例如：当实参`username="Amy"`时，传入下`Mapper`映射文件后

```xml
......
    <select id="findByName" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM user WHERE username=#{value}
    </select>
    ....
```

SQL将解析为：

```sql
SELECT * FROM user WHERE username="Amy"
```

**${} 方式**

${}: 解析为SQL时，将形参变量的值直接取出，**直接拼接显示在SQL中**

例如：当实参username="Amy"时，传入下Mapper映射文件后

```xml
......
    <select id="findByName" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM user WHERE username=${value}
    </select>
    ....
```

SQL将解析如下：

```sql
SELECT * FROM user WHERE username=Amy
```

显而该SQL无法正常执行，故需要在mppaer映射文件中的${value}前后手动添加引号，如下所示:

```xml
......
    <select id="findByName" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM user WHERE username='${value}'
    </select>
    ....
```

SQL将解析为:

```sql
SELECT * FROM user WHERE username='Amy'
```

### SQL 注入

`${}`方式是将形参和SQL语句直接拼接形成完整的SQL命令后，再进行编译 **（先拼接后编译）**，所以可以通过精心设计的形参变量的值，来改变原SQL语句的使用意图从而产生安全隐患，存在**SQL注入隐患**。现举例说明：

现有Mapper映射文件如下：

```xml
......
    <select id="findByName" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM user WHERE username='${value}'
    </select>
    ....
```

当 username = " ' OR 1=1 OR ' " 传入后，${}将变量内容直接和SQL语句进行拼接，结果如下:

```sql
SELECT * FROM user WHERE username='' OR 1=1 OR '';
```

显而易见，上述语句将把整个数据库内容直接暴露出来了

`#{}`方式则是先用占位符代替参数将SQL语句先进行**预编译**，然后再将参数中的内容替换进来。由于SQL语句已经被预编译过，其SQL意图将无法通过非法的参数内容实现更改，其参数中的内容，无法变为SQL命令的一部分。故，**#{}可以防止SQL注入而${}却不行**

### 适用场景

由于SQL注入的原因，${}和#{}在都可以使用的场景下，很明显推荐使用#{}。这里除了上文的WHERE语句例子，再介绍一个LIKE模糊查询的场景(username = "Amy"):

```xml
<select id="findAddByName" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM user WHERE username LIKE '%${value}%'
    </select>
```

该SQL解析为：

```sql
SELECT * FROM user WHERE username LIKE '%Amy%';
```

上述通过${}虽然可以实现对包含"Amy"对模糊查询，但是不安全，可以改用#{}，如下所示：

```xml
<select id="findAddByName" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM USER WHERE username LIKE CONCAT('%', #{username}, '%')
    </select>
```

该SQL解析为下文所示，其效果和上文方式一致

```sql
SELECT * FROM USER WHERE username LIKE CONCAT('%', 'Amy','%');
```

### 只能使用${ }的场景

由于#{}会给参数内容自动加上引号，会在有些需要表示**字段名、表名**的场景下，**SQL将无法正常执行**。现举一例说明：

期望查询结果按sex字段升序排列，参数String orderCol = "sex",mapper映射文件使用#{}：

```xml
<select id="findAddByName3" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM USER WHERE username LIKE '%Am%' ORDER BY #{value} ASC
    </select>
```

则SQL解析及执行结果如下所示，很明显 ORDER 子句的字段名错误的被加上了引号，致使查询结果没有按期排序输出

```sql
SELECT * FROM USER WHERE username LIKE '%Am%' ORDER BY 'sex' ASC;
```



![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-8422c3a75cc036904ba7e6f3b4a7cb76_720w.jpg)



这时，现改为${}测试效果：

```xml
<select id="findAddByName3" parameterType="String" resultMap="studentResultMap">
        SELECT * FROM USER WHERE username LIKE '%Am%' ORDER BY ${value} ASC
    </select>
```

则SQL解析及执行结果如下所示：

```sql
SELECT * FROM USER WHERE username LIKE '%Am%' ORDER BY sex ASC;
```



![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-75b5faefa4fc9c9cdd4c3477774029b3_720w.jpg)

## MyBatis 是如何进行分页的？分页插件的原理是什么？
