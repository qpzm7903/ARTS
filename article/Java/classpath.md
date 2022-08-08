## classpath作用

运行时JVM搜索class的路径



## classpath设置

可以通过环境变量`CLASSPATH`全局指定

也可以通过运行时 `-cp` or `-classpath`指定



推荐运行时指定，这样可以为每个程序指定，而不使用全局导致相互影响。

### classpath分隔符

windows

;

linux

:

## classpath顺序

按照声明循序寻找



## classpath路径类型



### JAR文件、ZIP文件

指定到jar文件或zip文件

假设要寻找的类时 `Cool.class`,其包路径是 `utility.myapp`

此包所在文件系统上的路径是 `c:\java\myClasses\utility\myapp`

将其打包成`myClasses.jar`也就是说从包路径的根目录打包,打包后jar包出现在myclasses路径下

此时指定的classpath为

`java -classpath c:\java\myClasses\myClasses.jar utility.myapp.Cool`



### 没有包路径的class文件

指定到包含此class文件的根目录

### 有包路径的class文件

假设要寻找的类时 `Cool.class`,其包路径是 `utility.myapp`

此包所在文件系统上的路径是 `c:\java\myClasses\utility\myapp`

那么在设置classpath的时候需要设置为

`java -classpath c:\java\myClasses utility.myapp.Cool`



## 默认classpath

默认为当前路径，使用`-cp`指定的时候可以覆盖掉，如果需要加入则配置一个`.`





## classpath 通配符 *



## 参考



https://docs.oracle.com/javase/tutorial/essential/environment/paths.html

https://docs.oracle.com/javase/8/docs/technotes/tools/windows/classpath.html#BEHBDEEB