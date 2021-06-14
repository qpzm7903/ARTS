

## 是什么

1、字节码工具，修改编译后的字节码



## 在哪些场景用了

1、spring 中security、aop

2、hibernate中的懒加载对象等

3、测试框架，比如mockito



## 怎么用



## 技术组成部分

### 1、Enhancer

> most used class of the cglib library

作用：允许生成非接口类的代理，动态生成子类，拦截所有方法

样例：



注意点：

不要使用匿名内部类，这可能导致这个匿名内部类无法进行垃圾回收，内存泄漏。



### 2、Immutable Bean

不可变的对象







## 同类工具

1、ASM



## 参考

http://mydailyjava.blogspot.com/2013/11/cglib-missing-manual.html



https://www.baeldung.com/cglib



https://juejin.cn/post/6844903584082067470