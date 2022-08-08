# Spring framework
#spring-framework
#spring

## 1.1 ntroduction to the Spring IoC Container and Beans



the `BeanFactory` provides the configuration framework and basic functionality

the `ApplicationContext` adds more enterprise-specific functionality

类图如下

![[Pasted image 20220513061957.png]]

ApplicationContext是BeanFactory，同时又增加了很多接口
## 1.2. Container Overview
The `org.springframework.context.ApplicationContext` interface represents the Spring IoC container and is responsible for - - - 
- instantiating
- configuring
- assembling the beans

将元数据（ Configuration Metadata）、类（POJOs）输入appplicationContext，构建好整个bean池子，需要用的时候就拿来用。

元数据
- XML
- 注解
- java code

如图
![[Pasted image 20220513063323.png]]

常用的 
- FileSystemXMLApplicationContext
- classPathXmlApplicationContext

### 1.2.1 Configuration metadata
最传统：XDM形式
注解形式：spring 2.5引入
基于java的配置：spring3.0引入 see the [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html), [`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html), [`@Import`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html), and [`@DependsOn`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html) annotations.



#### 1.2.1a xml配置
例如
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">  
 <bean id="test" class="com.example.spring_ioc_demo.test.bean.TestBean">  
 <!-- collaborators and configuration for this bean go here -->  
 <property name="testBeanRef" ref="testRef"/>  
 </bean>  
  
 <bean id="testRef" class="com.example.spring_ioc_demo.test.bean.TestBeanRef">  
  
 </bean>  
</beans>
```

bean标签定义一个bean，有id和指定的class全限定名。可以给属性也注入，适用property，对应属性名，以及ref引用另一个bean的id。

xml文件还可以通过路径引入其他的xml文件，这样可以进行模块化，低耦合等。例如
```xml
<import resource="mqs.xml"/>
```


### 1.2.1b 初始化容器
`org.springframework.context.support.ClassPathXmlApplicationContext`是`ApplicationContext`的一个常用实现。
读取并获取bean如下
```java
public static void main(String[] args) {  
 ApplicationContext context = new ClassPathXmlApplicationContext("services.xml");  
 TestBean test = (TestBean) context.getBean("test");  
}
```


### 1.2.2 组合xml
一般而言，不可能把所有东西都放在一个xml里，通过分而治之的方法，将各个模块的bean分配在不同的xml里，在一个顶层的xml里组合起来。例如
```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

注意：
- 不要使用相对路径或者绝对路径
- 推荐使用classpath路径

### 1.2.3 使用容器
容器初始化后，可以使用容器。容器的作用无非获取bean。
Context是个BeanFactory，那么看看BeanFactory有哪些方法
![[Pasted image 20220513064926.png]]
可以看到很多getBean方法。
例如
```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

## 1.3 Bean Overview
Bean被加载到container里面，需要有metadata的记录。spring里面关于bean的信息有如下记录：
- 全限定名
- 行为配置，比如生命周期阶段，是单例，还是原型等
- 引用其他的bean
- 其他配置

为了记录这些，`org.springframework.beans.factory.config.BeanDefinition`刻画了一个bean的定义的字段信息。

#todo 
`org.springframework.beans.factory.support.DefaultListableBeanFactory`作用是什么？

### 1.3.1 naming beans
在一个container里，所有的bean id都是唯一的，如果要有多余的名称，可以有别名。spring里的bean id默认是类名转小驼峰形式。


### 1.3.2 instantiating beans
通过构造函数

通过静态工厂方法

通过工厂方法

## 1.4 依赖
构造不仅仅是new一个对象，还是解决bean之间的依赖问题。


### 1.4.1 DI 依赖注入
dependency injection
主要有构造器注入，还有setter注入，甚至于字段注入

那么用构造器注入还是setter注入？构造器注入适合强依赖，setter适合非强依赖。构造器注入对测试更友好，setter注入对运行更友好。


### 1.4.1a 依赖处理过程
container 构造的适合，单例的bean会立马被构造出来，其他的会庸懒加载的形式。
如果存在bean的依赖循环，spring会抛出错误。
构造器循环依赖无法解决，但是可以改为setter注入解决。


### 1.4.2 依赖和配置的细节

具体是XDM配置的一些细节，用到的时候可以查看
比如集合类型属性的bean设置
以及更简便的p-namespace，c-namspace，也就是property、construct的缩写

### 1.4.3 dependens-on
用于解决依赖的顺序，被依赖的肯定要先初始化。

### 1.4.4 lazy-initialized beans
XML配置
```xml
lazy-init="true"
```


### 1.4.5 autowiring collaborators
推荐适用自动织入。
在XML中，通过autowire进行配置。autowire又四种模式。
- no
- byName
	- 也就是通过字段名寻找对应的bean
- byType
	- 通过类类型
- constructor
	- 类似byType，但是在构造器上

自动织入的限制和优点
- 不支持原始类型
- 精确度不如显示注入
- 遇到多个bean时可能会冲突

解决冲突的办法
- 适用显式注入
- primary bean
- 


### 1.4.6 方法注入
可以实现aware接口，拿到context，从里面获取bean。
或者适用方法注入，方法注入
- XML中适用look-method标签，
- 或
- 注解中适用Lookup注解

## 1.5 bean scopes
- 单例
- 原型
- 请求级
- 会话
- application
- websocket

## 1.6 customizing the nature of a bean
- [生命周期回调](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#beans-factory-lifecycle)
- [`ApplicationContextAware` and `BeanNameAware`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#beans-factory-aware)
- [其他 `Aware`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#aware-list)


### 1.6.1 lifecycle callbacks
JSR-250 `@PostConstruct` and `@PreDestroy`

>  you can implement the Spring `InitializingBean` and `DisposableBean` interfaces


#####   Initialization Callbacks
XML中通过init-method
```xml
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
```

或者实现`org.springframework.beans.factory.InitializingBean`这个接口。然后实现afterPropertiesSet方法
或者适用JSR250的 `@PostConstruct` 注解定义。

所以这个初始化回调只是个回调？能什么呢？

推荐使用@PostConstruct注解，和spring无关

##### Destruction Callbacks
类似

可自定义默认的initi、destroymethod

### 1.6.2. `ApplicationContextAware` and `BeanNameAware`
> When an `ApplicationContext` creates an object instance that implements the `org.springframework.context.ApplicationContextAware` interface, the instance is provided with a reference to that `ApplicationContext`.

但是不推荐使用，因为和spring耦合，且不复合依赖反转。


1.6.3 `Aware`接口
上下文、类加载器、BeanFacotry。。。
好多

## 1.7. Bean Definition Inheritance
暂时不知道用来做什么

## 1.8. Container Extension Points
容器扩展点

### 1.8.1. Customizing Beans by Using a `BeanPostProcessor`

```java
public interface BeanPostProcessor {  
    @Nullable  
    default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {  
        return bean;  
    }  
  
    @Nullable  
    default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
        return bean;  
    }  
}
```

可以看到，在bean的初始化前后会有回调函数。所以实现这个接口，就可以对这个bean做定制化处理。spring的一些代理就是用这个做的。

然后在做单元测试的时候也可以使用它，mock相关的东西。

### 1.8.2. Customizing Configuration Metadata with a `BeanFactoryPostProcessor`

predefined
- PropertyOverrideConfigurer
- PropertySourcesPlaceholderConfigurer
	- 可以替换文件里的占位符，比如xml文件里的数据库配置，写在properties文件里，在xml里是占位符，通过这个可以替换。
- PropertyOverrideConfigurer
	- 可以有默认值的placeholder
#### `BeanFactoryPostProcessor`
甚至可以对BeanFactory做处理，也就是可以处理元数据。
接口如下

```java
@FunctionalInterface  
public interface BeanFactoryPostProcessor {  
  
   /**  
 * Modify the application context's internal bean factory after its standard * initialization. All bean definitions will have been loaded, but no beans * will have been instantiated yet. This allows for overriding or adding * properties even to eager-initializing beans. * @param beanFactory the bean factory used by the application context  
 * @throws org.springframework.beans.BeansException in case of errors  
 */ void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;  
  
}
```

实现这个接口，就可以得到一个spring应用里所有的bean定义了

### 1.8.3. Customizing Instantiation Logic with a `FactoryBean`
#todo  
用来做什么的？

- 用于复杂的初始化
- XML用于复杂的初始化会很冗长

## 1.9. Annotation-based Container Configuration
XML配置和注解式配置都有自己使用的场景，没有谁更优。
XML对代码无侵入性
注解对代码有侵入性


- @Required
	- spring建议直接在bean的init方法或者其他方式，避免和spring耦合
- @Autowired
	- 和JSR330的@Inject类似，并且spring兼容JSR330的@Autowired
- @Primary
	- 优先级
- @Qulifier
	- 指定bean名称
- @Resource
	- JSR250标准
	- 通过名称寻找
- @Value
	- `PropertySourcesPlaceholderConfigurer`
	- application.properties
	- application.ylm
## 1.10. Classpath Scanning and Managed Components
- @Component
	- 通用注解
- @Repository
- @Service
- @Controller

后三个是更泛化的，适合在不同的层次使用，比如service层的就应该标记@Service，这样也方便其他比如aop的处理。
并且在实现上，后三个注解就是组合了Component以及其他注解的。

注意，默认的扫描路径就是[`@SpringBootApplication`](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.using-the-springbootapplication-annotation)所在的main类路径。

#### 1.10.9 generate an index of candidate componentss
生成bean的索引，可以增加快启动时间
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context-indexer</artifactId>
        <version>5.3.19</version>
        <optional>true</optional>
    </dependency>
</dependencies>
```

### 1.10.3. Automatically Detecting Classes and Registering Bean Definitions



### 1.11 使用JSR330标准注解
依赖
```xml
<dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
    <version>1</version>
</dependency>

```


- @Inject
	- field level, method level, constructor-arg level
	- 可以注入Optional，@Nullable
		- `@Inject publid void setService (Optional<Service> service);`
		- `@Inject publid void setService(@Nullable Service service);`
- @Named
	- 指定依赖名称
		- @Maned("main") SomeService


@Autowired , @Inject , @Component , @Scope等注解比较

[比较](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#beans-standard-annotations-limitations)



## 1.12 Java-based container configuration

#### basic concepts

主要是
- @Configuration
	- 作用于类上
- @Bean
	- 作用于方法上
		- 方法的返回值将会作为一个bean被spring管理
	- 在@Configuration的类里
	- 在任何的@Component注解类里都可以，但是一般在@Comfiguration注解的类
	- 



#### 使用 `AnnotationConfigApplicationContext`初始化容器

此容器支持@Configuration注解，以及其他JSR330的注解

用起来也很简单
```java
public static void main(String[] args) { 
	ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class); 
	MyService myService = ctx.getBean(MyService.class);
	 myService.doStuff(); 
 }
```



也可以通过无参数构造器构造容器，然后通过register的方法注册bean，最后通过reflesh方法刷新容器
```java
public static void main(String[] args) {
	AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(); 
	ctx.register(AppConfig.class, OtherConfig.class); 
	ctx.register(AdditionalConfig.class); 
	ctx.refresh(); MyService myService = ctx.getBean(MyService.class); 
	myService.doStuff(); 
	}
```

另外，@Configuration注解的类可以配合@ComponentScan使用，增加自动扫描。或者手动通过容器类增加扫描路径


#### @Import
可以将@Configuration注解的类组合起来。


#### 组合XML和Java配置
不是所有的配置都适合用Java类来做。两者各取其优点。

使用AnnotationConfigApplicationContext，结合@ImportResource导入XML配置。

或者 @Configuration结合@ImportResource注解


### 1.13 环境抽象

[Environment](https://docs.spring.io/spring-framework/docs/5.3.19/javadoc-api/org/springframework/core/env/Environment.html)

环境抽象，管理了profile和properties

profile抽象

profile是逻辑上 的分组，可以组织bean，不同的bean在不同的profile里生效。

profile可以通过@Profile注解指定bean的profile

可以通过环境变量、Environment接口等方式指定profile，可以同时生效多个profile。默认的是default。


PropertySource抽象
> A `PropertySource` is a simple abstraction over any source of key-value pairs

不同的`PropertySource`实现，就是从不同的地方获取value，以及对多个`PropertySource`的管理

不同的实现都可以添加、注册到`Environment`里去。
[`MutablePropertySource`](https://docs.spring.io/spring-framework/docs/5.3.19/javadoc-api/org/springframework/core/env/MutablePropertySources.html)
提供了丰富的api管理`PropertySource`

mutable：易变的，可变的


#### `@PropertySource`
此注解可以灵活的将配置文件注册到上下文中，例如
```java
@Configuration
@PropertySource("classpath:/com/myco/app.properties")
public class AppConfig {

    @Autowired
    Environment env;

    @Bean
    public TestBean testBean() {
        TestBean testBean = new TestBean();
        testBean.setName(env.getProperty("testbean.name"));
        return testBean;
    }
}
```

@PropertySource甚至可以用`${}` placeHolder。



### 1.14. Registering a `LoadTimeWeaver`
spring用于动态加载class的类
但是开发者可以用来做什么呢？#todo



### 1.16. The `BeanFactory`



## Resource
spring如何处理资源、在spring里如何使用资源？

原因：Java提供的相关工具太少


### 2.2. The `Resource` Interface

？？
###   2.3. Built-in `Resource` Implementations
内置实现

-   [`UrlResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-urlresource)
    
-   [`ClassPathResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-classpathresource)
    
-   [`FileSystemResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-filesystemresource)
    
-   [`PathResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-pathresource)
    
-   [`ServletContextResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-servletcontextresource)
    
-   [`InputStreamResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-inputstreamresource)
    
-   [`ByteArrayResource`](https://docs.spring.io/spring-framework/docs/5.3.19/reference/html/core.html#resources-implementations-bytearrayresource)



## 5 AOP
概念
- Aspect
- Join point
	- spring中，就是方法
- advice
	- 行为，有before、after、aroud等
- point cut
	- 切点，一个断言，用于匹配join point
- target object
	- 被aspect进行advice的类
- aop proxy
	- 用于实现aop，spring中使用jdk的动态代理或者cglib动态代理
- weaving
	- 植入，将advice链接到target的过程