---
title: maven配置多仓库
date: 2022-02-04 16:32:48
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/OIP-C.jpg
categories: Maven
---

[maven官方仓库]([http://repo1.maven.org/maven2/](http://link.zhihu.com/?target=http%3A//repo1.maven.org/maven2/) )国内访问较慢，一般配置[maven镜像阿里云仓库](https://maven.aliyun.com/repository/public)，但阿里maven仓库有些依赖找不到，还得去官方仓库中去找，因此我们需要配置多个仓库，当第一个仓库没有时候就去第二个仓库中找，确保项目的依赖都可以成功下载。

下面为具体的maven配置指南：

1. 打开 maven 的配置文件（ windows 机器一般在 maven 安装目录的**conf/settings.xml**），在<mirrors></mirrors>标签中添加 mirror 子节点，加入阿里仓库作为中央仓库

```xml
<mirror>
   	<id>aliyun</id>
    	<mirrorOf>central</mirrorOf>
    	<name>aliyun</name>
    	<url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

2. 在<profiles></profiles>标签里加入需要的私人仓库

```xml
<!--profile和repository里的id唯一，不可重复-->
<profile>
        <id>gf</id>
        <repositories>
            <repository>
                <id>maven-default</id>
                <url>http://repo1.maven.org/maven2/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
    </profile>
    <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
    </profile>
```

3. 在最后的`</setting>`标签里填入

```xml
   <activeProfiles>
    <activeProfile>aliyun</activeProfile>
    <activeProfile>gf</activeProfile>
  </activeProfiles>
```

