Spring 容器也叫 IoC 容器。

Spring 容器可以看作是 `ApplicationContext` 接口实例，有不同的实现类，容器初始化时会一次性创建所有的  Bean；`BeanFactory` 作为它的父接口，初始化时不会创建所有的 Bean，获取时按需创建 Bean。

*Bean* 相当于一个组件，其作用是实现某个特定的功能。

# IOC

**组件**的生命周期管理、配置、之间的依赖交由容器管理，开发者只需关心组件的使用

Ioc 是一种设计思想，为的是设计出低耦合的程序。[有了 IoC 容器后，**创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是 松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活**](https://pdai.tech/md/spring/spring-x-framework-ioc.html#ioc%E8%83%BD%E5%81%9A%E4%BB%80%E4%B9%88)。

通过 `@Scope` 指定 Bean 的生命周期，容器默认创建的 Bean 是 singleton，指定 prototype 表示每次都获取一个新的 Bean，request 表示 Bean 的生命周期和在一次请求同步，session 表示 Bean 的生命周期和一次会话同步。

## xml 配置

略

## Java 配置

创建 Java 配置类来创建第三方的 Bean。

`@Configuration` （类上）加 `@Bean` （方法上），用到时可以和普通 Bean 一样[注入](#注解注入)。

## 注解配置

提前配置 `@ComponentScan`（类上），容器会自动扫描带有 `@Component`，`@Controller`，`@Service`，`@Repository` 这四个注解的类。



# DI

容器将组件间的依赖关系注入组件之中。

下面的注入方式全部基于 Java 代码及注解。

`@Autowired` 为 Spring 自带的注解，默认按照类型进行注入，只能有一个类型匹配的 Bean；如果有多个类型符合的 Bean，则需要配合 `@Qualifier` 指定 Bean 的名称来表明注入的是哪个 Bean，此时 `@Autowired` 按照名称进行注入；或者在其中一个 Bean 上使用 `@Primary` 来优先使用这个 Bean。

`@Resource` 为 JDK 自带的注解，默认按照类型进行注入，出现多个类型匹配的 Bean 时，需要指定 Bean 的名称来表明注入的是哪个 Bean。

## setter 注入

```java
public class Foo {
  private Bar bar;
  
  // @Autowired 可省略
  public void setBar(Bar bar) {
    this.bar = bar;
  }
  
  ...
}
```

## 构造注入

```java
public class Foo {
  private Bar bar;
  
  // @Autowired 可省略
  public Foo(Bar bar) {
    this.bar = bar;
  }
  
}
```

## 注解注入

```java
public class Foo {
  
  @Autowired
  private Bar bar;
  
}
```



# AOP

AOP 的原理是[动态代理](readme.md#动态代理)，通过代理类将切面织入（Weaving）到业务类中，将常用方法从业务方法中剥离出来；具体来说，对接口类型使用 JDK 动态代理，对普通类使用 CGLIB 创建子类；使用注解来对方法、类装配切面，可以防止切面被装配到不必要的地方；**访问注入的 Bean 时，总是调用方法而非访问字段，因为注入 Bean 的实际类型是代理类，CGLIB 创建的代理类不会初始化继承的成员变量，只会代理非 `final` 修饰的方法**

Spring 不依赖 AOP 框架，Spring 集成了 AspectJ 来实现对 AOP 的支持。

## AspectJ

`@EnableAspectJAutoProxy` 修饰配置类。

`@Aspect` 修饰切面类。

`@Before`、`@Around`、`@After` 指定切入点表达式来织入指定业务方法：根据类、方法名、参数列表、注解修饰。



# Spring Boot

> Spring Boot 是一个基于 Spring 的套件，它帮我们预组装了 Spring 的一系列组件，以便以尽可能少的代码和配置来开发基于 Spring 的 Java 应用程序；Spring 只提供了一系列组件，不成架构体系，而 Spring Boot 提供了开箱即用的应用程序架构，基于 Spring Boot 的预置结构继续开发，省时省力。

## @SpringBootApplication

提供了自动装配和注解扫描的功能

### @Import

将指定类纳入容器管理

# MQ

消息队列，生产者发送消息给 MQ，消费者从 MQ 获取消息。主要解决三个方面的问题：

* 解耦

当前应用的接口调用了多个系统的接口，又不需要知道其他接口的返回结果时；当前应用只需要生产消息，其他系统从 MQ 中消费，从而与其他系统解耦

* 异步

同步高延时请求场景：将原本同步调用多系统互不相关的操作改为向多个 MQ 发消息，其他系统从各自 MQ 中消费，达到异步效果

* 削峰

将高峰期的 QPS（Query Per Second）先发到 MQ，系统再根据自身性能处理，避免直接崩溃，高峰期一过，再处理积压的消息

不过，MQ 的加入也会导致一些问题，比如当前应用调用多系统接口，当前应用返回成功了，但不清楚其他系统的调用结果，可能导致数据不一致