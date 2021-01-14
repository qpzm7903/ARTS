参考

[spring 实战]

使用idea直接创建spring工程

spring boot 版本 2.4.1

其中spring boot 对spring的核心依赖为

```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.3.2</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.2</version>
      <scope>compile</scope>
    </dependency>
```



# spring bean的配置方式

建议，自动化最方便。

1、组件扫描

要知道系统中哪些组件（component）

2、组件装配

组件之间的相互依赖，就是装配。

## XML中显式配置

根节点 beans

可以通过https://spring.io/tools的工具来创建xml

或者直接通过IDEA里面创建文件的时候选择类型为配置、spring配置。

结果如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

提示

文档根元素 "beans" 必须匹配 DOCTYPE 根 "null"。

配置文件的名称不能是application.xml

要是别的，因为application这个名字被spring占用了。



为了能在让spring能识别xml，在启动类上加

```java
@ImportResource(value = "classpath:applicationContext.xml")
```

让其知道我们用的是哪个xml来配置。

### bean声明

简单的声明

```xml
<bean id="cdByXmlConfiguration" class="com.qpzm7903.springdemo.soundsystem.support.CdByXmlConfig"/>
```

说明要创建的bean对象是说明，id是什么。

简单声明会使用默认构造器

#### 构造器

##### \<constructor-arg\>

比较直接，清晰

配置引用



配置字面量

##### -c

\<constructor-arg\>的替代方案比较方便，简短

要在beans的属性里面引入

```xml
xmlns:c="http://www.springframework.org/schema/c"
```





#### 属性

<property>

-p

要在beans的属性里面引入

```xml
xmlns:p="http://www.springframework.org/schema/p"
```



### util

```xml
xmlns:util="http://www.springframework.org/schema/util"
```

有很多用法



## Java中显式配置

## 隐式的Bean发现机制和自动装配

## 注解

### component 

组件，可以设置名称，默认为类名首字母小写。

### named

Spring支持将@Named作为@Component注解的替代方案。两者之间有一些细微的差异，但是在大多数场景中，它们是可以互相替换的。

但是named没有component那么强的语义，所以建议还是用compnent。



### ComponentScan

在spring boot中，似乎不用添加这个

但是什么时候需要用到？



### Autowired

是spring特有的注解

注入，不管是构造器、setter方法、spring都会尝试满足依赖。



### Inject

@Inject注解来源于Java依赖注入规范，该规范同时还为我们定义了@Named注解。在自动
装配中，Spring同时支持@Inject和@Autowired。



### Configuration

用于创建配置类，里面可以声明各种bean的创建



### Bean

> 带有@Bean注解的方法可以采用任何必要的Java功能来产生bean实例

在配置类中可以做到注入前的配置，相当于一个工厂。

打在方法上，入参是bean，出参也是bean





# 高级装配



环境于profile

最简单例子，不同的环境，数据库配置是不同的。



### 注解配置



#### @Profile

属性是环境名



XML配置





### 激活

如果没有激活，那么就用默认的

spring.profiles.active

spring.profiles.default



#### 激活方式



作为DispatcherServlet的初始化参数；
作为Web应用的上下文参数；
作为JNDI条目；
作为环境变量；
作为JVM的系统属性；
在集成测试类上，使用@ActiveProfiles注解设置







### 使用profile进行测试

比如测试的一套环境，通过配置隔离开



## 条件化的bean



## 处理自动装配的歧义

一个接口有多个实现的时候，注入会出现歧义，因为spring不知道要用哪个。

但是可以在bean声明的时候用上一些注解，

@Primary

或者xml中配置 primay 属性为true



@Qulifier

限定符，注入的时候可以设置qulifier属性，选择要注入哪个bean对象。

最基本的是用id，但是id是个string，有耦合并且还有松散的关系。



在bean的时候，比如使用@Component，也可以在bean上增加@Qulifier注解，定制该bean的id，在注入的时候可以使用，建议这个时候的id带有语义性质。



可以自定义@Qulifier注解

比如

```java
@Target({ElementType.FIELD, ElementType.TYPE, ElementType.METHOD, ElementType.CONSTRUCTOR})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Wyc {
}
```

自定义注解的方式也更加类型安全。

## bean的作用域

单例（Singleton）：在整个应用中，只创建bean的一个实例。 -- 默认作用域
原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新
的bean实例。
会话（Session）：在Web应用中，为每个会话创建一个bean实例。
请求（Rquest）：在Web应用中，为每个请求创建一个bean实例。



配置方式

1、注解 

@Scope

```java
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
```



2、xml中

对bean设置 scope属性，有prototye，singleton。。。



会话作用域





## 运行时注入

两种形式

1、属性占位符

2、spring表达式语言 SpEL

