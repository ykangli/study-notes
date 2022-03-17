---
title: 电脑已有jdk 8，再配置一个jdk 17
date: 2022-01-26 22:52:14
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/R-C.jpg
---

如何安装两个以上版本jdk，并可以自由切换。以jdk1.8和jdk-17为例

## 一、目标：

现在用的 jdk1.8 做开发，私下里想了解和测试最新版 jdk-17 的特性，所以需要两个(甚至多个)版本的 jdk（以 jdk1.8 和 jdk-17 和为例），而且要做到可以随时切换版本。

## 二、当前情况

目前电脑中已有一个 jdk1.8

## 步骤：

1. 将 jdk 17 压缩包解压
2. 打开环境变量，找到系统变量中的**Path**
3. 点击编辑把`%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;`复制到变量值的最前面，然后确定

![image-20220126225728779](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126225728779.png)

4. 新建两个JAVA_HOME，值为jdk的路径

| 变量        | 值           | 说明                                            |
| ----------- | ------------ | ----------------------------------------------- |
| JAVA_HOME   | %JAVA_HOME8% | 修改此处变量值中的数字来达到启用所对应的jdk     |
| JAVA_HOME17 | jdk17路径    | 12为我的jdk版本；后面的路径是你自己jdk的主目录  |
| JAVA_HOME8  | jdk8路径     | 8也为我的jdk版本；后面的路径是你自己jdk的主目录 |

![image-20220126225556573](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126225556573.png)

### 切换jdk

修改JAVA_HOME变量值中的数字来达到启用所对应的jdk

![image-20220126225831540](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126225831540.png)

### 测试

WIN+R输入cmd打开命令提示符，键入
`java -version`

![image-20220126225911133](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126225911133.png)

切换版本后，要打开一个新的cmd进行测试

![image-20220126225938216](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220126225938216.png)
