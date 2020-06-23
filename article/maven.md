# maven



## 安装



### windows

[https://maven.apache.org/download.cgi]()下载安装包

解压到本地目录。

配置环境变量，因为以来Java，所以Java需要提前安装好。

添加系统变量`M2_HOME --> D:\software\maven\apache-maven-3.6.3`

然后在系统变量PATH中加入`%M2_HOME%\bin`

打开个cmd验证。 `mvn -v`



### setting文件

setting文件分为全局和用户范围。

1. $M2_HOME/conf/settings.xml 是全局范围的
2. ${user.home}/.m2/settings.xml 是用户范围的

前者又被叫做全局配置，后者被称为用户配置。如果两者都存在，它们的内容将被合并，并且用户范围的settings.xml优先。

### 配置本地仓库

默认路径在 `${user.home}/.m2/repository`，所以如果不配置的话，我的电脑上就是`C:\Users\qpzm7903\.m2\repository`。

在maven的安装目录下找到conf目录，把setting文件copy到.m2目录下，并且指定本地仓库位置

`<localRepository>D:\software\maven\repository</localRepository>`



### 配置远程仓库



### 配置镜像 



### 配置代理

```xml
<settings>
	<proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
  </proxies>
</settings>  
```



### 配置全局插件









## POM文件

### 标签

```xml
<modelVersion>4.0.0</modelVersion>
```

指定POM模型的版本，maven2和3都是4.0.0

| 标签      | 意义                                          |
| --------- | --------------------------------------------- |
| groupId   | 这个i项目属于哪个组，比如com.googlecode.myapp |
| artifacId | 在该项目组中的唯一Id                          |
| version   | 就是版本，有SNAPSHOT、RELEASE两种             |
| name      | 友好显示的项目名称                            |
| packing   | 打包方式，比如jar，war                        |

groupId，artifactId，version在maven中唯一定义了一个组件。



### 指定jar包的main方法

比如spring boot项目启动的时候会指定了main方法，这是在spring boot的依赖pom文件里定义好了的

如下（省略了一些，只保留主要的）

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
            <mainClass>${start-class}</mainClass>
          </transformer>
        </transformers>
      </configuration>
    </execution>
  </executions>
</plugin>
```

打包后在Jar包的META-INF 目录下会有MANIFEST.MF文件，里面会有main函数的信息，比如spring boot项目打出来的包是：

```tex
Manifest-Version: 1.0
Spring-Boot-Classpath-Index: BOOT-INF/classpath.idx
Implementation-Title: jpademo
Implementation-Version: 1.0-SNAPSHOT
Start-Class: com.example.jpademo.JpademoApplication
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Build-Jdk-Spec: 1.8
Spring-Boot-Version: 2.3.1.RELEASE
Created-By: Maven Jar Plugin 3.2.0
Implementation-Vendor: Pivotal Software, Inc.
Main-Class: org.springframework.boot.loader.JarLauncher
```



### 依赖

一个完整的依赖为

```xml
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
            <type>jar</type>
            <optional>true</optional>
            <exclusions>
                <exclusion>
                    <groupId>org.hamcrest</groupId>
                    <artifactId>hamcrest-core</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

| 标签       | 涵义         |
| ---------- | ------------ |
| type       | 依赖的类型   |
| scope      | 依赖的范围   |
| optional   | 标记可选     |
| exclusions | 排除传递依赖 |

### 依赖范围

| 类型     | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| compile  | 默认的范围，编译、测试、运行三种classpath都有效              |
| test     | 只对测试classpath有效，在编译测试代码、运行测试的时候需要    |
| provided | 对编译和测试classpath有效，运行时无效，比如servlet-api，运行时容器以提供，就不需要 |
| runtime  | 测试和运行时classpath有效，比如JDBC驱动实现，编译时主代码无效 |
| system   | 系统依赖范围，和provided范围一致                             |

| scope    | 对于编译classpath有效 | 对于测试classpath有效 | 对于运行时classpath有效 |
| -------- | --------------------- | --------------------- | ----------------------- |
| compile  | Y                     | Y                     | Y                       |
| test     | N                     | Y                     | N                       |
| provided | Y                     | Y                     | N                       |
| runtime  | N                     | Y                     | Y                       |
| system   | Y                     | Y                     | N                       |

对于classpath有效是什么意思？也就是这个依赖用了这个范围，那么这个在对应的作用下，这个组件就加到classpath中使用。

### 传递性依赖

一个是范围冲突，一个是版本冲突，一个是路径冲突

首先是依赖范围的传递性影响

|          | compile  | test | provided     | runtime     |
| -------- | -------- | ---- | ------------ | ----------- |
| compile  | compile  | N    | N            | **runtime** |
| test     | test     | N    | N            | test        |
| provided | provided | N    | **provided** | provided    |
| runtime  | runtime  | N    | N            | runtime     |

第一列是直接依赖，第一行是第二直接依赖，中间是两者的碰撞



### 依赖调解

