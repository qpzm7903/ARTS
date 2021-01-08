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



### aspectJ概念

[join point](https://www.eclipse.org/aspectj/doc/released/progguide/semantics-joinPoints.html)：连接点，java代码中已定义好的点，包含构造器调用，字段访问，方法调用等

[pointcut](https://www.eclipse.org/aspectj/doc/released/progguide/semantics-pointcuts.html):切点，可以选择某些切入点，并且暴露该切入点所在的上下文的一些值。

advice:A piece of advice is code。在在切点的切入点执行，可以访问切点暴露出来的值，可以由before，after、aroud来声明。



### 基于Java注解的AspectJ

1、注解

2、类型

3、签名

4、运行时类型匹配和上下文

5、包和参数注解

6、注解继承和切点匹配

### spring aop 和 aspectJ

### spring aop独有的语法



## 参考

[aspectJ maven 插件](https://www.mojohaus.org/aspectj-maven-plugin/examples/weaveDirectories.html)

[aspectJ](https://www.eclipse.org/aspectj/doc/released/progguide/index.html)
[blog](https://blog.mythsman.com/post/5d301cf2976abc05b34546be/)

[javac+lombok后使用aspectJ二进制织入](https://stackoom.com/question/3r9If/%E5%9C%A8Javac-Lombok%E9%98%B6%E6%AE%B5%E4%B9%8B%E5%90%8E%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8AspectJ-Maven%E8%BF%9B%E8%A1%8C%E4%BA%8C%E8%BF%9B%E5%88%B6%E7%BC%96%E7%BB%87)

[基于java注解的aspectJ](https://www.eclipse.org/aspectj/doc/released/adk15notebook/annotations-aspectmembers.html)

[AspectJ in Android （二），AspectJ 语法](https://www.jianshu.com/p/691acc98c0b8)



[aspectJ官网](https://www.eclipse.org/aspectj/docs.php)