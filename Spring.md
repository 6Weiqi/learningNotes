Spring 容器也叫 IoC 容器，**组件**的创建、配置、之间的依赖和生命周期管理交由容器负责，开发者只需关心组件的使用。

Spring 容器可以看作是 `ApplicationContext` 接口实例，有不同的实现类，会一次性创建所有的 Bean，`BeanFactory` 作为它的父接口，是按需创建 Bean。

AOP 的原理是动态代理，通过代理类将切面织入（Weaving）到业务类中

Spring Boot 是一个基于 Spring 的套件，它帮我们预组装了 Spring 的一系列组件，以便以尽可能少的代码和配置来开发基于 Spring 的 Java 应用程序；Spring 只提供了一系列组件，不成架构体系，而 Spring Boot 提供了开箱即用的应用程序架构，基于 Spring Boot 的预置结构继续开发，省时省力。