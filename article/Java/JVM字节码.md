[oracle 文档](https://docs.oracle.com/javase/specs/jvms/se9/html/jvms-6.html)



## JVM数据类型

java是静态类型

字节码操作指令也被影响.

比如

- iadd
- ladd
- fadd
- dadd



定义的类型有

- 原始类型
  - byte
  - short
  - int
  - long
  - char
  - float
  - double
  - boolean被当作int处理
- 参考类型
  - class types
  - array types
  - interface types



## 基于栈的结构

基于栈所以让字节码指令比较简单,如果是基于寄存器就比较复杂.



PC register: 对于每个正在执行的java线程,PC寄存器存储了当前执行的指令



JVM stack:存储局部变量,方法参数,返回值



Heap:存储共享变量,比如Class的实例,数组的实例,这里也会有垃圾回收



Method area:对于每个加载的class,存储方法的代码,以及符号表,常量池

![method_area.png](D:\workspace\github\ARTS\article\Java\JVM字节码.assets\method_area.png)



stack frames:调用方法入栈,方法结束出栈

- 局部变量
- 操作数栈,存储指令的操作数,或者中间变量

![stack_frame_zoom.png](D:\workspace\github\ARTS\article\Java\JVM字节码.assets\stack_frame_zoom.png)



## 探索



每个java文件都是由指令构成的

```java
opcode (1 byte)      operand1 (optional)      operand2 (optional)      ...	
```



指令可以对当前方法的栈帧的操作数栈或者局部变量数组操作

比如

```java
public static void main(String[] args) {
    int a = 1;
    int b = 2;
    int c = a + b;
}
```

使用javac编译

```
javac Test.java
```



使用javap命令查看字节码

```sh
javap -v Test.class
```



得到

```shell
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: iconst_1
         1: istore_1
         2: iconst_2
         3: istore_2
         4: iload_1
         5: iload_2
         6: iadd
         7: istore_3
         8: return
      LineNumberTable:
        line 4: 0
        line 5: 2
        line 6: 4
        line 7: 8
```



