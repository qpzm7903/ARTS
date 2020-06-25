# JPA

jpa是什么？

https://docs.jboss.org/hibernate/jpa/2.2/api/overview-summary.html

# hibernate

### 入门

#### 使用原生的hibernate API--基于配置文件

声明在resource目录下，文件名为hibernate.cfg.xml

里面配置数据库相关配置，以及mapping文件，mapping文件和mybatis的的resultMap有点像，就是关于一个java对象和数据库表字段的映射关系。

比如官方提供的入门文档的例子是：

```xml
<hibernate-mapping package="org.hibernate.tutorial.hbm">

    <class name="Event" table="EVENTS">
        <id name="id" column="EVENT_ID">
            <generator class="increment"/>
        </id>
        <property name="date" type="timestamp" column="EVENT_DATE"/>
        <property name="title"/>
    </class>

</hibernate-mapping>
```



有了模型、hibernate配置文件，mapping文件，就可以使用hibernate去操作数据库了。

首先读取配置文件，然后得到一个SessionFacotry，类似于要获取一个Mybatis的SessionFacotry，里面都封装了对jdbc 的connection的操作。

```java
StandardServiceRegistry registry = new StandardServiceRegistryBuilder()
				.configure() // configures settings from hibernate.cfg.xml
				.build();
sessionFactory = new MetadataSources( registry ).buildMetadata().buildSessionFactory();
```

获取sessionFacotry后，工厂模式自然是提供对应的类，也就是session，然后用session将实例化的模型纳入hibernate的管理。同样前后要开启事务、关闭事务。

```java
Session session = sessionFactory.openSession();
		session.beginTransaction();
		session.save( new Event( "Our very first event!", new Date() ) );
		session.save( new Event( "A follow up event", new Date() ) );
		session.getTransaction().commit();
		session.close();
```



查询的时候需要写点类似sql的东西，比较接近面向对象的方式。

```java
session = sessionFactory.openSession();
		session.beginTransaction();
		List result = session.createQuery( "from Event" ).list();
		for ( Event event : (List<Event>) result ) {
			System.out.println( "Event (" + event.getDate() + ") : " + event.getTitle() );
		}
		session.getTransaction().commit();
		session.close();
```



#### 使用原生的hibernate API--基于注解

**类**

```JAVA
@Entity
@Table( name = "EVENTS" )
public class Event {
    ...
}
```

`@javax.persistence.Entity`注解标识一个entity

`@javax.persistence.Table`显式声明表名称，如果没有，就和类名相同

**属性**

```JAVA
@Id
@GeneratedValue(generator="increment")
@GenericGenerator(name="increment", strategy = "increment")
public Long getId() {
    return id;
}
```

`@javax.persistence.Id`主键





#### 参考

[官方入门文档](https://docs.jboss.org/hibernate/orm/5.4/quickstart/html_single/#preface)

### 进一步



#### 参考

https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html

# spring jpa



### 使用mysql8



```properties
spring.jpa.hibernate.ddl-auto=update
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/jpa_test
spring.datasource.username=root
spring.datasource.password=admin
```



### 错误1

Access to DialectResolutionInfo cannot be null when 'hibernate.dialect' not set

于是增加配置

```properties
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
```



### 错误2

InvalidConnectionAttributeException: The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the 'serverTimezone' configuration property) to use a more specifc time zone value if you want to utilize time zone support

修改配置

```properties
spring.datasource.url=jdbc:mysql://${MYSQL_HOST:localhost}:3306/jpa_test?characterEncoding=UTF-8&serverTimezone=UTC
```

于是启动正常