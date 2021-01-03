# aspectJ入门学习

aspectJ提供三种方式织入切面内容

- 编译时织入
  - 通过ajc直接编译源文件
- 编译后织入
  - 通过ajc编译经过javac编译后的字节码文件
- 载入时织入

## 通过maven快速创建项目
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

## 参考

[插件]{https://www.mojohaus.org/aspectj-maven-plugin/examples/weaveDirectories.html}
[aspectJ]{https://www.eclipse.org/aspectj/doc/released/progguide/index.html}
[blog]{https://blog.mythsman.com/post/5d301cf2976abc05b34546be/}