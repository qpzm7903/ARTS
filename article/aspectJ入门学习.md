# aspectJ入门学习

使用平台:`windows10`

aspectJ提供三种方式织入切面内容

- 编译时织入
  - 通过ajc直接编译源文件
- 编译后织入
  - 通过ajc编译经过javac编译后的字节码文件
- 载入时织入

## 通过maven快速创建项目

pom依赖

```xml
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.8.9</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjtools</artifactId>
            <version>1.8.9</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.9</version>
        </dependency>
```





得到如下结构
```text
└─src
    └─main
        └─java
          └─org
              └─example
```
在example中添加两个文件 Aop.java与App.java
```java
package org.example;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public class Aop {
    @Pointcut("execution(* org.example.App.say(..))")
    public void joinPoint() {
    }

    @Before("joinPoint()")
    public void before() {
        System.out.println("before say");
    }

    @After("joinPoint()")
    public void after() {
        System.out.println("after say");
    }
}
```
```java
package org.example;

/**
 * Hello world!
 */
public class App {
    public static void main(String[] args) {
        App app = new App();
        app.say();
    }

    public void say() {
        System.out.println("app say hello");
    }
}
```
## 编译时织入

执行如下命令

```bash
#!/usr/bin/env bash
ASPECTJ_TOOLS=D:/software/maven/repository/org/aspectj/aspectjtools/1.8.9/aspectjtools-1.8.9.jar
ASPECTJ_RT=D:/software/maven/repository/org/aspectj/aspectjrt/1.8.9/aspectjrt-1.8.9.jar
java -jar $ASPECTJ_TOOLS  -source 1.8   -target 1.8 -cp ".;$ASPECTJ_RT" -sourceroots src/main/java -d target/classes
java -cp "$ASPECTJ_RT;target/classes" org/example/App
```

此时目录结构如下

```bash
>tree/f
├─src
│  └─main
│      └─java
│          └─org
│              └─example
│                      Aop.java
│                      App.java
│
└─target
    └─classes
        └─org
            └─example
                    Aop.class
                    App.class
```

从target/classes目录下查看App.class文件

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package org.example;

public class App {
    public App() {
    }

    public static void main(String[] args) {
        App app = new App();
        app.say();
    }

    public void say() {
        try {
            Aop.aspectOf().before();
            System.out.println("app say hello");
        } catch (Throwable var2) {
            Aop.aspectOf().after();
            throw var2;
        }

        Aop.aspectOf().after();
    }
}

```

可以看到切面内容已经被织入。

## 编译后织入

首先尝试
```bash
javac -cp "D:/software/maven/repository/org/aspectj/aspectjrt/1.8.9/aspectjrt-1.8.9.jar" -d target/classes src/main/java/org/example/*.java
```
得到的字节码文件里面
```java
public class App {
    public App() {
    }

    public static void main(String[] var0) {
        App var1 = new App();
        var1.say();
    }

    public void say() {
        System.out.println("app say hello");
    }
}
```
没有织入后的痕迹

然后使用编译后织入的方式
```bash
#!/usr/bin/env bash
ASPECTJ_TOOLS=D:/software/maven/repository/org/aspectj/aspectjtools/1.8.9/aspectjtools-1.8.9.jar
ASPECTJ_RT=D:/software/maven/repository/org/aspectj/aspectjrt/1.8.9/aspectjrt-1.8.9.jar
ASPECTJ_WEAVER=D:/software/maven/repository/org/aspectj/aspectjweaver/1.8.9/aspectjweaver-1.8.9.jar
echo "compiling by javac"
javac -cp "$ASPECTJ_RT" -d target/classes src/main/java/org/example/*.java
echo "compiling by ajc after javac"
java -jar $ASPECTJ_TOOLS -cp "$ASPECTJ_RT"  -source 1.8 -target 1.8 -inpath target/classes -d target/classes
echo "running"
java -cp "$ASPECTJ_RT;target/classes"  org.example.App
```
输出

```bash
compiling by javac
compiling by ajc after javac
running
before say
app say hello
after say
```

这个时候输出的结果已经是切面后的结果。
查看字节码文件，当然内容已经织入了。

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package org.example;

public class App {
    public App() {
    }

    public static void main(String[] var0) {
        App var1 = new App();
        var1.say();
    }

    public void say() {
        try {
            Aop.aspectOf().before();
            System.out.println("app say hello");
        } catch (Throwable var2) {
            Aop.aspectOf().after();
            throw var2;
        }

        Aop.aspectOf().after();
    }
}
```

## 载入时织入
不管是编译期织入，还是编译后织入，都依赖于ajc编译器，载入时织入时通过java agent机制在内存中修改字节码，达到切面的效果。

首先得告诉类加载器，什么类是切面类，需要优先处理，因为切面类会影响其他类

在资源路径src/main/resources/META-INF添加aop.xml
```xml
<aspectj>
    <aspects>
        <aspect name="org.example.Aop"/>
    </aspects>
</aspectj>
```

然后使用javac编译源文件，使用java agent 运行
```bash
#!/usr/bin/env bash

ASPECTJ_TOOLS=D:/software/maven/repository/org/aspectj/aspectjtools/1.8.9/aspectjtools-1.8.9.jar
ASPECTJ_RT=D:/software/maven/repository/org/aspectj/aspectjrt/1.8.9/aspectjrt-1.8.9.jar
ASPECTJ_WEAVER=D:/software/maven/repository/org/aspectj/aspectjweaver/1.8.9/aspectjweaver-1.8.9.jar
echo "compiling by maven clean compile"
mvn clean compile
echo "running"
java -javaagent:$ASPECTJ_WEAVER -cp "$ASPECTJ_RT;target/classes" org.example.App
```
输出
```bash
before say
app say hello
after say
```

## 通过maven插件编译，避免每次都写脚本

pom依赖添加两个插件

第一插件时为了方便运行结果。

第二个插件是实际aspectJ织入的插件。

```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>org.example.App</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>aspectj-maven-plugin</artifactId>
                <version>1.11</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>       <!-- use this goal to weave all your main classes -->
<!--                            <goal>test-compile</goal>  &lt;!&ndash; use this goal to weave all your test classes &ndash;&gt;-->
                        </goals>
                    </execution>
                </executions>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <complianceLevel>1.8</complianceLevel>
                </configuration>
            </plugin>
```

在根目录执行

```bash
mvn clean package 
java -jar target/aspectJDemo-1.0-SNAPSHOT.jar

->>
before say
app say hello
after say

```



所以使用这个插件是编译时织入，还是编译后织入呢？

默认时编译时织入的，但是可以配置成编译后织入

在[weaving a'lready  compiled classes](https://www.mojohaus.org/aspectj-maven-plugin/examples/weaveDirectories.html)说明了怎么配置



### 编译时织入

这时候在IDEA中直接运行即可，因为java compilier自动修改为ajc。起到编译时织入的效果。

pom文件的build配置如下

```xml
<build>
        <plugins>
            <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.7.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>aspectj-maven-plugin</artifactId>
                <version>1.10</version>
                <configuration>
                    <complianceLevel>1.8</complianceLevel>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
                <executions>
                    <execution>
                        <phase>process-classes</phase>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```





## lombok 和 aspectJ一起使用---编译后织入

引入lombok依赖，并且App.java文件修改为

```java
package org.example;

import lombok.Data;

/**
 * Hello world!
 */
@Data
public class App {
    private String name;
    public static void main(String[] args) {
        App app = new App();
        app.setName("aspectJ");
        app.say();
    }

    public void say() {
        System.out.println("app say hello " + name);
    }
}

```



aspectJ不管在编译时织入还是编译后织入，都会依赖于ajc编译器，而ajc不支持lombok。所以想在用lombok的时候可以采用编译后织入的方式，也就是先用javac编译，在用ajc编译织入。aspectJ插件配置如下

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>aspectj-maven-plugin</artifactId>
    <version>1.11</version>
    <executions>
        <execution>
            <phase>process-classes</phase>
            <goals>
                <goal>compile</goal>       <!-- use this goal to weave all your main classes -->
            </goals>
        </execution>
    </executions>
    <configuration>
        <forceAjcCompile>true</forceAjcCompile>
        <sources/>
        <weaveDirectories>
            <weaveDirectory>${project.build.outputDirectory}</weaveDirectory>
        </weaveDirectories>
        <source>1.8</source>
        <target>1.8</target>
        <complianceLevel>1.8</complianceLevel>
    </configuration>
</plugin>
```

接着

```bash
 mvn clean package; java -jar target/aspectJDemo-1.0-SNAPSHOT.jar
 ->
...编译信息忽略
before say
app say hello aspectJ
after say

```





## lombok 和 aspectJ一起使用--载入时织入

资源文件里面配置`aop.xml`

`pom.xml`中去除aspectJ的插件，接着运行

```bash
#!/usr/bin/env bash

ASPECTJ_TOOLS=D:/software/maven/repository/org/aspectj/aspectjtools/1.8.13/aspectjtools-1.8.13.jar
ASPECTJ_RT=D:/software/maven/repository/org/aspectj/aspectjrt/1.8.13/aspectjrt-1.8.13.jar
ASPECTJ_WEAVER=D:/software/maven/repository/org/aspectj/aspectjweaver/1.8.13/aspectjweaver-1.8.13.jar
echo "compiling by maven clean compile"
mvn clean compile
echo "running"
java -javaagent:$ASPECTJ_WEAVER -cp "$ASPECTJ_RT;target/classes" org.example.App
```

输出

```
...忽略编译信息
before say
app say hello aspectJ
after say
```



如果用了maven的shade插件制定main class，也可以直接

```bash
#!/usr/bin/env bash

ASPECTJ_TOOLS=D:/software/maven/repository/org/aspectj/aspectjtools/1.8.13/aspectjtools-1.8.13.jar
ASPECTJ_RT=D:/software/maven/repository/org/aspectj/aspectjrt/1.8.13/aspectjrt-1.8.13.jar
ASPECTJ_WEAVER=D:/software/maven/repository/org/aspectj/aspectjweaver/1.8.13/aspectjweaver-1.8.13.jar
mvn clean package
java -javaagent:$ASPECTJ_WEAVER -jar target/aspectJDemo-1.0-SNAPSHOT.jar
```

输出

```bash
...忽略编译信息
before say
app say hello aspectJ
after say
```



载入时织入的方式查看字节码文件，里面是没有织入的痕迹的。



# aspectJ

[join point](https://www.eclipse.org/aspectj/doc/released/progguide/semantics-joinPoints.html)：连接点，java代码中已定义好的点，包含构造器调用，字段访问，方法调用等

[pointcut](https://www.eclipse.org/aspectj/doc/released/progguide/semantics-pointcuts.html):切点，可以选择某些切入点，并且暴露该切入点所在的上下文的一些值。

advice:A piece of advice is code。在在切点的切入点执行，可以访问切点暴露出来的值，可以由before，after、aroud来声明。



## 语法

### pointCut

### 1、call

expression:call (method signatur) 

表达式之间可以通过逻辑符号连接

expression可以构成一个pointCut

在java中，通过注解的方式，比如

```java
@Pointcut("call(void org.example.App.say())")
public void callJoinPoint() {

}
```



通配符

1、App中，返回值为void，方法开头为hava，入参为空

```java
    @Pointcut("call(void org.example.App.have*())")
    public void testWildCards1() {
      
    }
```



2、返回值任意，方法任意，参数任意

```java
@Pointcut("call(* org.example.App.* (..))")
public void testWildCards2() {
}
```

### 2、execution

### 3、call vs. execution

从字面意义上看：

call是调用，调用是外部的

execution是执行，执行是内部的

那么实际的织入代码有什么区别呢？

以下面两个简单的类看看

```java
package org.example.difference;

public class Car {
    public static void main(String[] args) {
        Car car = new Car();
        car.driving();
    }
    public void driving() {
        System.out.println("the car is driving");
    }
}
package org.example.difference;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class CarAop {
    @Before("call(void Car.driving())")
    public void callAdvice() {
        System.out.println("before call car.driving()");
    }

    @Before("execution(void Car.driving())")
    public void executionAdvice() {
        System.out.println("before execution car.driving()");
    }
}

Car的字节码如下
public class Car {
    public Car() {
    }

    public static void main(String[] args) {
        Car car = new Car();
        CarAop.aspectOf().callAdvice();
        car.driving();
    }

    public void driving() {
        CarAop.aspectOf().executionAdvice();
        System.out.println("the car is driving");
    }
}
```

可以显然看到：

call是在方法调用的地方织入的

execution是在方法内部织入的



### 暴露加入点的上下文

比如暴露参数

```java
    @Before("call(* org.example.App.add (int ,int  )) && args(a,b)")
    public void testArgs(int a,int b) {
        System.out.println(String.format("before App.add(%s,%s", a, b));
    }
```

暴露函数的调用对象

```java
    @Before("call(* org.example.App.add (..)) && target(app)")
    public void testTarget(App app) {
        System.out.println(String.format("before App:" + app));
    }
```



非绑定参数情况下，也可以直接获取JoinPoint，从里面获取入参。

```java
    @Before("call(* org.example.App.add (..))")
    public void testJoinPoint(JoinPoint joinPoint) {
        System.out.println(String.format("before App,joinPoint.getTarget()->" + joinPoint.getTarget()));
        System.out.println(String.format("before App,joinPoint.getArgs() ->" + joinPoint.getArgs()));
        System.out.println(String.format("before App,joinPoint.getSignature() ->" + joinPoint.getSignature()));
        System.out.println(String.format("before App,joinPoint.getKind() ->" + joinPoint.getKind()));
        System.out.println(String.format("before App,joinPoint.getThis() ->" + joinPoint.getThis()));
        System.out.println(String.format("before App,joinPoint.getSourceLocation() ->" + joinPoint.getSourceLocation()));
        System.out.println(String.format("before App,joinPoint.getStaticPart() ->" + joinPoint.getStaticPart()));
    }
```



### 其他切入点

1. 捕获异常处理上的连接点，异常时Throwable以及其子类，只能用before设置advice，

   语法:`pointcut [切入点名字](参数列表): handler(异常类);`




```java
public class AC {
    public static void main(String[] args) {
        AC ac = new AC();
        ac.open();
    }

    @ExAnno
    public void open() {
        try {
            throw new IllegalArgumentException("open ac fail");
        } catch (IllegalArgumentException e) {
            System.out.println("AC arise exception" + e.getMessage());
        }

    }
}
@AspectJ
...
    @Before("handler(IllegalArgumentException)")
    public void exception3() {
        System.out.println("handler(IllegalArgumentException)");
    }

字节码
public class AC {
    public AC() {
    }

    public static void main(String[] args) {
        AC ac = new AC();
        ac.open();
    }

    @ExAnno
    public void open() {
        try {
            throw new IllegalArgumentException("open ac fail");
        } catch (IllegalArgumentException var3) {
            ACAp.aspectOf().exception3();
            System.out.println("AC arise exception" + var3.getMessage());
        }
    }
}
```



1. 当执行的执行的代码属于某个类

   `within(Class)`

2. cflow

   `cflow(pointCut)`

   捕获在该pointCut下的所有加入点

   示例：

   cflow会包含切入点本身，所以要通过 within(切面的类) 排除掉，不然会栈溢出

   因为切面织入后，也属于控制流的一部分，避免切面再次切自己家，所以要加限制。

   cflow(pointCut) && !within(aop类)

   

3. cfowblow

   `cflowbelow`不包括初始连接点，cflow包含

   

### advice

> pointcuts pick out join points. But they don't *do* anything apart from picking out join points.
>
> Advice brings together a pointcut (to pick out join points) and a body of code (to run at each of those join points).

有几种advice

1. before

   > before advice on a method call join point runs before the actual method starts running, just after the arguments to the method call are evaluated.

2. after

   > *After advice* on a particular join point runs after the program proceeds with that join point,
   >
   > runs after returning *or* throwing, like Java's `finally`

3. after returning

4. after throwing




### 通配符

- 常用通配符

| 通配符 | 意义                                  | 示例                                                         |
| ------ | ------------------------------------- | ------------------------------------------------------------ |
| *      | 表示除”.”以外的任意字符串             | java.*.Date：可以表示java.sql. Date,java.util. Date          |
| ..     | 表示任意子package或者任意参数参数列表 | java..*:表示java任意子包；void getName(..):表示方法参数为任意类型任意个数 |
| +      | 表示子类                              | java..*Model+:表示java任意包中以Model结尾的子类              |



### [语法总结](https://www.eclipse.org/aspectj/doc/released/progguide/semantics-pointcuts.html#matching)

```
MethodPattern = 
  [ModifiersPattern] TypePattern 
        [TypePattern . ] IdPattern (TypePattern | ".." , ... ) 
        [ throws ThrowsPattern ]
ConstructorPattern = 
  [ModifiersPattern ] 
        [TypePattern . ] new (TypePattern | ".." , ...) 
        [ throws ThrowsPattern ]
FieldPattern = 
  [ModifiersPattern] TypePattern [TypePattern . ] IdPattern
ThrowsPattern = 
  [ ! ] TypePattern , ...
TypePattern = 
    IdPattern [ + ] [ [] ... ]
    | ! TypePattern
    | TypePattern && TypePattern
    | TypePattern || TypePattern
    | ( TypePattern )  
IdPattern =
  Sequence of characters, possibly with special * and .. wildcards
ModifiersPattern =
  [ ! ] JavaModifier  ...
```



## 基于Java注解的AspectJ

1、注解

2、类型

3、签名

4、运行时类型匹配和上下文

5、包和参数注解

6、注解继承和切点匹配

## spring aop 和 aspectJ 



>  Spring AOP旨在通过Spring IoC提供一个简单的AOP实现，以解决编码人员面临的最常出现的问题。这并不是完整的AOP解决方案，它只能用于Spring容器管理的beans。

连接点支持

| join point                  | Spring Aop support | AspectJ suppert |
| --------------------------- | ------------------ | --------------- |
| Method call                 | no                 | yes             |
| method execution            | yes                | yes             |
| constructor call            | no                 | yes             |
| constructor execution       | no                 | yes             |
| static initialzer execution | no                 | yes             |
| Object initialization       | no                 | yes             |
| Field reference             | no                 | yes             |
| Field assignment            | no                 | yes             |
| Handler execution           | no                 | yes             |
| advice execution            | no                 | yes             |



Spring提供了4种类型的AOP支持：
基于代理的经典Spring AOP；
纯POJO切面；
@AspectJ注解驱动的切面；
注入式AspectJ切面（适用于Spring各版本）



前三种都是Spring AOP实现的变体，Spring AOP构建在动态代理基础之上，因此，Spring对
AOP的支持局限于方法拦截。



Spring不能像之前那样使用<bean>声明来创建一个bean实例——它已经在运行时由AspectJ创建完成了。Spring需要通过aspectOf()工厂方法获得切面的引用，然后像<bean>元素规定的那样在该对象上执行依赖注入



打断点的时候查看对象，发现时个CGLB的代理对象。所以只是用了aspectJ的语法，实现理念，自己实现了动态代理。

1、通过javaconfig



2、通过xml来装配

## spring aop独有的语法



## 参考

[aspectJ maven 插件](https://www.mojohaus.org/aspectj-maven-plugin/examples/weaveDirectories.html)

[aspectJ](https://www.eclipse.org/aspectj/doc/released/progguide/index.html)
[blog](https://blog.mythsman.com/post/5d301cf2976abc05b34546be/)

[javac+lombok后使用aspectJ二进制织入](https://stackoom.com/question/3r9If/%E5%9C%A8Javac-Lombok%E9%98%B6%E6%AE%B5%E4%B9%8B%E5%90%8E%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8AspectJ-Maven%E8%BF%9B%E8%A1%8C%E4%BA%8C%E8%BF%9B%E5%88%B6%E7%BC%96%E7%BB%87)

[基于java注解的aspectJ](https://www.eclipse.org/aspectj/doc/released/adk15notebook/annotations-aspectmembers.html)

[AspectJ in Android （二），AspectJ 语法](https://www.jianshu.com/p/691acc98c0b8)

[aspectJ官网](https://www.eclipse.org/aspectj/docs.php)

[aspectJ quick reference](https://www.eclipse.org/aspectj/doc/released/progguide/quick.html)

[spring使用AspectJ实现注解式AOP面向切面编程](https://blog.csdn.net/pan_junbiao/article/details/104614416)