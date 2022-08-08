约定优于配置

1、安装



1.1 使用 choco安装

choco install gradle

默认安装到了

` 'C:\ProgramData\chocolatey\lib\gradle\tools'`





1.2 确认安装

gradle -v 



1.3 初始化工程 gradle init



1.4 运行测试



./gradlew check



1.5 编译

./gradlew build



1.6 运行

./gradlw run







2、 依赖管理

在build.gradle中

```gradle
dependencies {

}
```





3、项目管理



4、仓库管理

build.gradle