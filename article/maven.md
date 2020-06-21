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



### 配置全局插件









