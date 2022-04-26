# Spring框架

------

## 什么是 Spring 框架?

一般说 Spring 框架指的都是 `Spring Framework`，是一款开源的轻量级 Java 开发框架，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发。

 Spring 自带 IOC（Inverse of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)、可以很方便地对数据库进行访问、对单元测试支持比较好、支持`RESTful Java `应用程序的开发。

## 列举一些重要的 Spring 模块？

![Spring主要模块](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/jvme0c60b4606711fc4a0b6faf03230247a.png)

**Spring Core**

核心模块， Spring 其他所有的功能基本都需要依赖于该模块，主要提供 IoC 依赖注入功能的支持。

**Spring Aspects**

该模块为与 AspectJ 的集成提供支持。

**Spring AOP**

提供了面向切面的编程实现。

**Spring Web**

Spring Web 由 4 个模块组成：

- spring-web ：对 Web 功能的实现提供一些最基础的支持。
- spring-webmvc ： 提供对 Spring MVC 的实现。
- spring-websocket ： 提供了对 WebSocket 的支持，WebSocket 可以让客户端和服务端进行双向通信。
- spring-webflux ：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是完全异步.

**Spring Test**

Spring 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC)的帮助，单元测试和集成测试变得更简单。

Spring 的测试模块对 JUnit（单元测试框架）、TestNG（类似 JUnit）、Mockito（主要用来 Mock 对象）、PowerMock（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

## Spring IOC & AOP

### IOC(控制反转)

**IOC（Inverse of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。Spring IOC 解决的是**对象管理和对象依赖的问题，**核心思想就是**将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理，再具体一点就是交给Spring的IOC容器管理**。

IOC底层原理是利用工厂模式实现， 具体包括 xml解析、反射等。 

![image-20220426155345614](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220426155345614.png)

- **控制** ：指的是对象创建（实例化、管理）的权力
- **反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

将对象之间的相互依赖关系交给 IOC 容器来管理，并由 IOC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IOC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好**配置文件/注解**即可，完全不用考虑对象是如何被创建出来的。

**DI (依赖注入) 是IOC 的实现方法。**

在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IOC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

![img](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/frc-365faceb5697f04f31399937c059c162.png)

先明白软件设计的一个重要思想：**依赖倒置原则（Dependency Inversion Principle ）**，就是顶层需要什么，底层就去实现这个需求，顶层不管具体怎么去实现。如果不这样设计，而实反过来上层依赖下层，那么下层的实现一更改，就会“牵一发而动全身”。

例：

此时为上层建筑依赖下层建筑，或者说是下层控制上层：

![preview](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-8ec294de7d0f9013788e3fb5c76069ef_r.jpg)

每一个类的构造函数都直接调用了底层代码的构造函数。假设我们需要改动一下轮胎（Tire）类，把它的尺寸变成动态的，而不是一直都是30。我们需要这样改：

![preview](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-64e8b19eeb70d9cf87c27fe4c5c0fc81_r.jpg)

由于我们修改了轮胎的定义，为了让整个程序正常运行，我们需要做以下改动：

![preview](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-82e0c12a1b26f7979ed9241e169affda_r.jpg)

这种设计却需要**修改整个上层所有类的构造函数**！在软件工程中，**这样的设计几乎是不可维护的**

因此需要进行控制反转，即上层控制下层。用依赖注入（Dependency Injection）这种方式来实现控制反转。**所谓依赖注入，就是把底层类作为参数传入上层类，实现上层类对下层类的“控制**”。

用**构造方法传递的依赖注入方式**重新写车类的定义：

![preview](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-c920a0540ce0651003a5326f6ef9891d_r.jpg)

![preview](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-99ad2cd809fcb86dd791ff7f65fb1779_r.jpg)

**控制反转（Inversion of Control）** 就是依赖倒置原则的一种代码设计的思路。具体采用的方法就是所谓的**依赖注入（Dependency Injection）**，也就是Spring通过 DI（依赖注入）实现了IOC。

依赖注入有三种方式： setter注入、构造方法注入、基于注解的注入

##### 🔥**IOC 解决了以下问题**

- 创建了许多重复对象，造成大量资源浪费；
- 更换实现类需要改动多个地方；
- 创建和配置组件工作繁杂，给组件调用方带来极大不便。

### AOP（面向切面的编程）

当有重复代码出现时，可以就将其封装出来然后复用。我们通过分层、分包、分类来规划不同的逻辑和职责，就像之前讲解的三层架构。但这里的复用的都是**核心业务逻辑**，并不能复用一些**辅助逻辑**，比如：日志记录、性能统计、安全校验、事务管理，等等。这些辅助逻辑往往贯穿你整个核心业务，传统 OOP 很难将其封装：

```java
public class UserServiceImpl implements UserService {
    @Override
    public void doService() {
        System.out.println("---安全校验---");
        System.out.println("---性能统计 Start---");
        System.out.println("---日志打印 Start---");
        System.out.println("---事务管理 Start---");

        System.out.println("业务逻辑");

        System.out.println("---事务管理 End---");
        System.out.println("---日志打印 End---");
        System.out.println("---性能统计 End---");
    }
}
```

**这些逻辑是所有业务方法都要加上**，想想都恐怖。

OOP 是**至上而下**的编程方式，犹如一个[树状图](https://www.zhihu.com/search?q=树状图&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"355778555"})，A调用B、B调用C，或者A继承B、B继承C。这种方式对于业务逻辑来说是合适的，通过调用或继承以复用。而**辅助逻辑就像一把闸刀横向贯穿**所有方法，如图2-4所示：

![preview](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/v2-1704f1f350fbe7b8b8b2e33d2eac178f_r.jpg)

**代理模式用来增加或增强原有功能再适合不过了**，但切面逻辑的难点不是**不修改原有业务**，而是**对所有业务生效**。对一个业务类增强就得新建一个代理类，对所有业务增强，每个类都要新建代理类，这无疑是一场灾难。

面向切面编程（Aspect-oriented programming，缩写为 AOP）正是为了解决这一问题而诞生的技术。

AOP 不是 OOP 的对立面，它是对 OOP 的一种补充。OOP 是纵向的，AOP 是横向的，两者相结合方能构建出良好的程序结构。AOP 技术，**让我们能够不修改原有代码，便能让切面逻辑在所有业务逻辑中生效**。

我们只需声明一个切面，写上切面逻辑：

```java
@Aspect // 声明一个切面
@Component
public class MyAspect {
    // 原业务方法执行前
    @Before("execution(public void com.rudecrab.test.service.*.doService())")
    public void methodBefore() {
        System.out.println("===AspectJ 方法执行前===");
    }

    // 原业务方法执行后
    @AfterReturning("execution(* com.rudecrab.test.service..doService(..))")
    public void methodAddAfterReturning() {
        System.out.println("===AspectJ 方法执行后===");
    }
}
```

无论你有一个业务方法，还是一万个业务方法，对我们开发者来说只需编写一次切面逻辑，就能让所有业务方法生效，极大提高了我们的开发效率。

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，却为**业务模块所共同调用的逻辑或责任**（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

Spring AOP 就是**基于动态代理**的，如果要代理的对象实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去**创建代理对象**，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个**被代理对象的子类**来作为代理

`CGLIB是一个功能强大，高性能的代码生成包。它为没有实现接口的类提供代理，为JDK的动态代理提供了很好的补充。`

##### 🔥**AOP 解决了以下问题**

- 切面逻辑编写繁琐，有多少个业务方法就需要编写多少次。

##  Spring Bean

### 什么是 Spring Bean？

Bean 代指的就是那些被 IOC 容器所管理的对象。

我们需要告诉 IOC 容器帮助我们管理哪些对象，这个是通过**配置元数据**来定义的。配置元数据可以是 **XML 文件、注解或者 Java 配置类。**

### Bean 的作用域有哪些?

Spring 中 Bean 的作用域通常有下面几种：

- **singleton** : 唯一 bean 实例，Spring 中的 bean 默认都是单例的，对单例设计模式的应用。
- **prototype** : 每次通过Spring容器获取bean时，都会创建一个新的 bean 实例。
- **request** : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
- **session** : 每一次来自**新 session** 的 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。
- **global-session** ： 全局 session 作用域，仅仅在基于 portlet 的 web 应用中才有意义，Spring5 已经没有了。Portlet 是能够生成语义代码(例如：HTML)片段的小型 Java Web 插件。它们基于 portlet 容器，可以像 servlet 一样处理 HTTP 请求。但是，与 servlet 不同，每个 portlet 都有不同的会话。

### @Component 和 @Bean 的区别是什么？

- `@Component` 注解作用于**类**，而`@Bean`注解作用于**方法。**

- `@Component`通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中

  在实际的开发中，有时候可能由于位置的问题导致Spring扫描不到，造成没有此类的异常出现，所以一般使用使用 `@ComponentScan` 注解自定义要扫描的路径，从而使的Spring能顺利找到。

  `@Bean` 注解通常是我们在标有`@Component`注解的方法中定义产生这个 bean,`@Bean`告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。

- `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现。

### 一个类声明为 Bean 的注解有哪些?

- `@Component` ：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应**持久层**即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应**服务层**，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC **控制层**，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

### 注入 Bean 的注解有哪些？

Spring 内置的 `@Autowired` 以及 JDK 内置的 `@Resource` 和 `@Inject` 都可以用于注入 Bean。

| Annotaion    | Package                            | Source       |
| ------------ | ---------------------------------- | ------------ |
| `@Autowired` | `org.springframework.bean.factory` | Spring 2.5+  |
| `@Resource`  | `javax.annotation`                 | Java JSR-250 |
| `@Inject`    | `javax.inject`                     | Java JSR-330 |

`@Autowired` 和`@Resource`使用的比较多一些。

### @Autowired 和 @Resource 的区别是什么？

#### @Autowired

`Autowired` 属于 **Spring 内置的注解**，默认的注入方式为`byType`（根据类型进行匹配），也就是说会优先根据**接口类型**去匹配并注入 Bean （接口的实现类）。

**这会有什么问题呢？** 当一个接口存在多个实现类的话，`byType`这种方式就无法正确注入对象了，因为这个时候 Spring 会同时找到多个满足条件的选择，默认情况下它自己不知道选择哪一个。

这种情况下，注入方式会变为 `byName`（根据名称进行匹配），这个名称通常就是类名（首字母小写）。就比如说下面代码中的 `smsService` 就是我这里所说的名称，这样应该比较好理解了吧。

```java
// smsService 就是我们上面所说的名称
@Autowired
private SmsService smsService;
```

举个例子，`SmsService` 接口有两个实现类: `SmsServiceImpl1`和 `SmsServiceImpl2`，且它们都已经被 Spring 容器所管理。

如果名称与类名首字母小写后不同，那么就无法找到Bean

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Autowired
private SmsService smsService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Autowired
private SmsService smsServiceImpl1;
// 正确注入  SmsServiceImpl1 对象对应的 bean
// smsServiceImpl1 就是我们上面所说的名称
@Autowired
@Qualifier(value = "smsServiceImpl1")
private SmsService smsService;
```

#### @Resource 

`@Resource`属于 JDK 提供的注解，默认注入方式为 `byName`。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为`byType`。

`@Resource` 有两个比较重要且日常开发常用的属性：`name`（名称）、`type`（类型）。

如果仅指定 `name` 属性则注入方式为`byName`，如果仅指定`type`属性则注入方式为`byType`，如果同时指定`name` 和`type`属性（不建议这么做）则注入方式为`byType`+`byName`。

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Resource
private SmsService smsService;
// 正确注入 SmsServiceImpl1 对象对应的 bean
@Resource
private SmsService smsServiceImpl1;
// 正确注入 SmsServiceImpl1 对象对应的 bean（比较推荐这种方式）
@Resource(name = "smsServiceImpl1")
private SmsService smsService;
```

### `@RestController`和`@Controller`

### `@Controller` 返回一个页面

单独使用 `@Controller` 不加 `@ResponseBody`的话一般使用在要返回一个视图的情况，这种情况属于比较传统的Spring MVC 的应用，对应于前后端不分离的情况。

![图片](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/640)

`@RestController`返回JSON 或 XML 形式数据

`@RestController`注解是`@Controller`和`@ResponseBody`的合集,表示这是个控制器 bean`。`

`@RestController`只返回对象，对象数据直接以 JSON 或 XML 形式写入 HTTP 响应(Response)中，这种情况属于 RESTful Web服务，这也是目前日常开发所接触的最常用的情况（前后端分离）。

## Spring MVC

### 说说自己对于 Spring MVC 了解?

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其**核心思想**是通过将业务逻辑、数据、显示分离开来。

MVC是一种**设计模式**，或者也有人说他是一种**软件设计规范。**

Spring MVC 是 Spring 中的一个很重要的模块，主要赋予 Spring 快速构建 MVC 架构的 Web 程序的能力。

Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行更简洁的 Web 层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)。****

## Spring 框架中用到了哪些设计模式？

- **工厂设计模式** : Spring 使用工厂模式通过 `BeanFactory`、`ApplicationContext` **创建 bean 对象**。
- **代理设计模式** : `Spring AOP` 功能的实现。
- **单例设计模式** : Spring 中的 Bean 默认都是**单例的**。
- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。

## Spring,Spring MVC,Spring Boot 之间什么关系?

一般说 Spring 框架指的都是 `Spring Framework`，是一款开源的轻量级 Java 开发框架，它是**很多模块的集合**，使用这些模块可以很方便地协助我们进行开发。

其中最重要的是 **Spring-Core（主要提供 IOC 依赖注入功能的支持） 模块**， Spring 中的其他模块（比如 Spring MVC）的功能实现基本都需要依赖于该模块。

**Spring MVC 是 Spring 中的一个很重要的模块**，主要赋予 Spring 快速构建 **MVC 架构的 Web 程序的能力**。MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将**业务逻辑**、**数据**、**显示**分离开来。

使用 Spring 进行开发时，**各种配置过于麻烦**比如开启某些 Spring 特性时，需要用 XML 或 Java 进行显式配置。于是，Spring Boot 诞生了！

Spring Boot 只是简化了配置，如果你需要构建 MVC 架构的 Web 程序，你还是需要使用 Spring MVC 作为 MVC 框架，只是说 Spring Boot 帮你简化了 Spring MVC 的很多配置，真正做到开箱即用！