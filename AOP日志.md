# AOP日志

------

## 依赖配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.12.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.hmdp</groupId>
    <artifactId>hm-dianping</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>hm-dianping</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>


        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3</version>
        </dependency>
        <!--hutool-->
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.7.17</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

## Controller

```java
package com.hmdp.controller;


import cn.hutool.core.util.StrUtil;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.hmdp.aop.TestLogAnnotation;
import com.hmdp.dto.Result;
import com.hmdp.entity.Shop;
import com.hmdp.service.IShopService;
import com.hmdp.utils.SystemConstants;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;

/**
 * <p>
 * 前端控制器
 * </p>
 *
 * @author 虎哥
 * @since 2021-12-22
 */
@RestController
@RequestMapping("/shop")
public class ShopController {


    @GetMapping("/test/{id}/{name}")
    @TestLogAnnotation(module = "测试", action = "测试AOP日志")
    public Result test(@PathVariable("id") Long id, @PathVariable("name") String name) {
        return Result.ok(id + name);
    }
}
```

## 开发注解

```java
package com.hmdp.aop;

import java.lang.annotation.*;

/**
 * @author ykangli
 * @version 1.0
 * @date 2022/8/12 21:17
 */
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface TestLogAnnotation {
    String module() default "";
    String action() default "";
}
```

## 开发AOP

```java
package com.hmdp.aop;

import cn.hutool.json.JSON;
import cn.hutool.json.JSONUtil;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

/**
 * @author ykangli
 * @version 1.0
 * @date 2022/8/12 21:22
 */
@Component
@Aspect   //使用@Aspect注解可以声明一个切面
@Slf4j
public class TestLogAspect {
    //声明一个切点
    @Pointcut("@annotation(com.hmdp.aop.TestLogAnnotation)")
    public void pt() {
    }

    //声明环绕通知
    @Around("pt()")
    public Object log(ProceedingJoinPoint point) throws Throwable {
        long beginTime = System.currentTimeMillis();
        //执行方法
        Object result = point.proceed();
        //执行时长(毫秒)
        long time = System.currentTimeMillis() - beginTime;
        //保存日志
        recordLog(point, time);
        return result;

    }

    private void recordLog(ProceedingJoinPoint joinPoint, long time) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        TestLogAnnotation logAnnotation = method.getAnnotation(TestLogAnnotation.class);
        log.info("=====================log start================================");
        log.info("module:{}",logAnnotation.module());
        log.info("action:{}",logAnnotation.action());

        //请求的方法名
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = signature.getName();
        log.info("request method:{}",className + "." + methodName + "()");

//        //请求的参数
        Object[] args = joinPoint.getArgs();
        String params = JSONUtil.toJsonStr(args[0]);
        log.info("params:{}",params);
        log.info("excute time : {} ms",time);
        log.info("=====================log end================================");
    }
}
```

日志打印结果：

```
2022-08-12 21:34:02.911  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : =====================log start================================
2022-08-12 21:34:02.911  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : module:测试
2022-08-12 21:34:02.911  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : action:测试AOP日志
2022-08-12 21:34:02.911  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : request method:com.hmdp.controller.ShopController.test()
2022-08-12 21:34:02.938  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : params:{}
2022-08-12 21:34:02.939  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : excute time : 3 ms
2022-08-12 21:34:02.939  INFO 31096 --- [nio-8081-exec-1] com.hmdp.aop.TestLogAspect               : =====================log end================================
```



使用order定义切面顺序
