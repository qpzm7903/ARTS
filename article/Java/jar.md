

Jar是什么

The Java™ Archive (JAR) file 

> enables you to bundle multiple files into a single archive file.



## jar命令

直接通过man查看即可

## Jar基础



| Operation                                                    | Command                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| To create a JAR file                                         | `jar cf *jar-file input-file(s)*`                            |
| To view the contents of a JAR file                           | `jar tf *jar-file*`                                          |
| To extract the contents of a JAR file                        | `jar xf *jar-file*`                                          |
| To extract specific files from a JAR file                    | `jar xf *jar-file archived-file(s)*`                         |
| To run an application packaged as a JAR file (requires the [`Main-class`](https://docs.oracle.com/javase/tutorial/deployment/jar/appman.html) manifest header) | `java -jar *app.jar*`                                        |
| To invoke an applet packaged as a JAR file                   | `<applet code=*AppletClassName.class*        archive="*JarFileName.jar*"        width=*width* height=*height*> </applet> ` |

This section shows you how to perform the most common JAR-file operations, with examples for each of the basic features:

### 创建

### 查看

### 解压

### 运行







### Manifest Files

mainifest files是key value形式



设置程序的入口

```java
Main-Class: classname
```



```shell
jar cfm *jar-file Manifest.txt input-file(s)*
```



or

```sh
jar cfe app.jar MyApp MyApp.class
// e 参数，创建或则和Main-Class属性，可以用于创建或者更新Jar文件
```





## Demo



```java
Class App{
    public static main(String[] args){
        System.out.println("hello world");
    }
}
```



```sh
javac App.java
echo "Main-Class: App" > manifest.txt
jar cfm app.jar Manifest.txt App.class
java -jar app.jar
>>> hello world

```

or

```shell
javac App.java
jar cfe app.jar App App.class
java -jar app.jar
>>> hello world
```









## 扫描和校验Jar文件



## Jar相关的API

- [The **java.util.jar** package](https://docs.oracle.com/javase/8/docs/api/java/util/jar/package-summary.html)
- [The **java.net.JarURLConnection** class](https://docs.oracle.com/javase/8/docs/api/java/net/JarURLConnection.html)
- [The **java.net.URLClassLoader** class](https://docs.oracle.com/javase/8/docs/api/java/net/URLClassLoader.html)



通过jarFile打开Jar包，然后遍历JarFile的每一项

##  参考

https://docs.oracle.com/javase/tutorial/deployment/jar/index.html



## Jar文件规范

https://docs.oracle.com/javase/8/docs/technotes/guides/jar/jar.html#The_META-INF_directory

一个压缩文件，可包含`META-INF`目录。

可以使用jar命令，或者使用java.util.jar API.



### `META-INF`

> used to store package and extension configuration data, including security, versioning, extension and services.

- MANIFEST.MF
  -  used to define extension and package related data.
- INDEX.LIST
  - 加速加载
- x.SF
  - 签名文件
- x.DSA
- services/
  - stores all the service provider configuration files



#### MANIFEST

主要属性

| key               | value | description                                                  |
| ----------------- | ----- | ------------------------------------------------------------ |
| Manifest-Version  |       |                                                              |
| Created-By        |       |                                                              |
| Signature-Version |       |                                                              |
| Class-Path        |       | 空格分隔，用于表明此应用依赖路径                             |
| Main-Class        |       | The value of this attribute is the class name of the main application class which the launcher will load at startup time.<br />比如 java -jar app.jar 的时候，就是以此属性指定的类来启动的。 |
|                   |       |                                                              |









## TODO

创建jar包的时候怎么把依赖的jar包也打进去，maven是怎么做的
