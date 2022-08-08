

## 核心理念

### 双亲委派

> The `ClassLoader` class uses a delegation model to search for classes and resources. Each instance of `ClassLoader` has an associated parent class loader. When requested to find a class or resource, a `ClassLoader` instance will delegate the search for the class or resource to its parent class loader before attempting to find the class or resource itself. The virtual machine's built-in class loader, called the "bootstrap class loader", does not itself have a parent but may serve as the parent of a `ClassLoader` instance.





## 核心方法



### loadClass

加载类





### findClass

> Finds the class with the specified [binary name](https://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#name). This method should be overridden by class loader implementations that follow the delegation model for loading classes, and will be invoked by the [`loadClass`](https://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#loadClass(java.lang.String)) method after checking the parent class loader for the requested class. The default implementation throws a `ClassNotFoundException`.



自定义类加载器应该实现的



### defineClass

> he method [`defineClass`](https://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#defineClass(java.lang.String, byte[], int, int)) converts an array of bytes into an instance of class `Class`.



## 核心实现

### UrlClassLoader



## 扩展

可以继承UrlClassLoader，然后初始化的时候传入想要加载的jar包路径，然后用这个classloader就可以加载指定jar包里的类了。





## 参考

https://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html

