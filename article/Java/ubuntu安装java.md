1. 更新软件包所i你

   1. `sudo apt update`

2. 安装jdk

   1. `sudo apt install openjdk-8-jdk`
   2. `sudo apt install openjdk-11-jdk`

3. 设置默认版本

   1. `sudo update-alternatives --config java`

      1. ```txt
         There are 2 choices for the alternative java (providing /usr/bin/java).
         
           Selection    Path                                            Priority   Status
         ------------------------------------------------------------
         * 0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode
           1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode
           2            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode
         
         Press <enter> to keep the current choice[*], or type selection number:
         ```

      2. 

4. 配置JAVA_HOME变量

   1. `sudo update-alternatives --config java`查看当前java版本
   2. 编辑`/etc/environment`文件
   3. 添加`JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"`到末尾
   4. `source /etc/environment`激活配置
   5. `echo $JAVA_HOME`检查

