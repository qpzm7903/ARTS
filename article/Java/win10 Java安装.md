# win10 Java安装

## Java8

1、在官网下载jdk安装包，安装即可

2、配置CLASSPATH、JAVA_HOME

2.1、配置系统环境变量JAVA_HOME，value为java的安装路径，我这里是

`C:\Program Files\Java\jdk1.8.0_251`

2.2、配置系统环境变量CLASSPATH

`.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`

2.3、在系统环境变量Path里添加

`%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;`

