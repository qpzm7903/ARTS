# Java注解

### Java注解的概念

声明Java注解的时候有三种保留策略：

1. source file retention
   1. .class文件中看不到
   2. 
2. class-file retention
   1. 默认的级别，.class文件中能看到
3. runtime retention
   1. 运行时能看到

这三种界别，后者包含前者。



注解的继承

默认注解是不继承的。

注解上使用@Inherited注解可以继承，但是接口上的不继承。

## Java注解处理器

阅读文章[注解处理器](http://hannesdorfmann.com/annotation-processing/annotationprocessing101)

从这篇文章学习到了注解处理器的用法。注解处理器是作用于编译器的工具，我们可以给特定的注解写处理器，然后注册到javac。javac编译的时候会拿到我们注册的处理器去处理对应的注解。

可以用于生成代码。注解处理器分为两类，一是生成代码的，会在target目录下生成.java代码，而另一种是插入式注解处理器，可以在.class文件里面插入代码，比如常见的lombok插件就是这样实现的。

## 1 AbstractProcessor

编写自定义的注解处理器就是要继承javax.annotation.processing.AbstractProcessor这个类。

需要重写的方法有

1. init 初始化，获取一些工具
2. getSupportedAnnotationTypes 返回一个List<String> 表示要处理哪些注解
3. getSupportedSourceVersion 主持的源码版本
4. process 主要的处理方法



## 2 注册处理器

将注解处理器所在的模块打包，在jar包的根目录下加入如下的目录结构

```java
- META-INF
		- services
			- javax.annotation.processing.Processor
```

该文件里面写的是处理器的全限定名。例如

```
processors.FactoryProcessor
```

然后javac编译的时候，如果这个jar包在buildpath里面，就会用到这个jar包里面的处理器去处理相应的注解。

手动注册还是相对麻烦，google提供了一个工具可以自动将注解处理器写入到META-INF目录下。

maven 依赖如下

```
<dependency>
    <groupId>com.google.auto.service</groupId>
    <artifactId>auto-service</artifactId>
    <version>1.0-rc2</version>
</dependency>
```

只需要在继承了AbastractProcessor的类上接入注解

```
@AutoService(Processor.class)
```

然后打包的时候就会自动加入到MATE-INF文件目录下。

## 3 工厂模式的例子

餐馆的菜品都继承Meal类，使用工厂模式根据菜品生成对应的实例，通过注解的方式生成这些菜品的工厂类。



声明注解

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.CLASS)
public @interface Factory {

    /**
     * 工厂方法返回的类型
     */
    Class type();

    /**
     * 工厂方法入参
     */
    String id();
}
```

通过注解处理器生成的代码应该是这样的

```java
public class MealFactory {
  public Meal create(String id) {
    if (id == null) {
      throw new IllegalArgumentException("id is null!");
    }
    if ("mealName".equals(id)) {
      return new xxxMeal();
    }
    throw new IllegalArgumentException("Unknown id = " + id);
  }
}
```

将注解加到实现类或者子类上

```java
@Factory(
    id = "Margherita",
    type = Meal.class  
)
public class MargheritaPizza implements Meal {

  @Override
  public float getPrice() {
    return 6f;
  }
}
```

相当于一个map，这个id对应的类是Meal，生成的时候就会有Meal的factory。

另外注解是不继承的，所以在父类的注解不会在子类也有，需要在子类再自行添加。



处理器

首先继承抽象处理器，实现通用的方法

```java
@AutoService(Processor.class)
public class FactoryProcessor extends AbstractProcessor {

    private Types typeUtils;
    private Elements elementUtils;
    private Filer filer;
    private Messager messager;
    private Map<String, FactoryGroupedClasses> factoryClasses =
            new LinkedHashMap<String, FactoryGroupedClasses>();

    @Override
    public synchronized void init(ProcessingEnvironment processingEnv) {
        super.init(processingEnv);
        typeUtils = processingEnv.getTypeUtils(); // 处理TypeMirror
        elementUtils = processingEnv.getElementUtils(); // 处理 Element
        filer = processingEnv.getFiler(); // 生成文件
        messager = processingEnv.getMessager(); 
    }

    @Override
    public Set<String> getSupportedAnnotationTypes() {
        Set<String> annotations = new LinkedHashSet<String>();
        annotations.add(Factory.class.getCanonicalName());
        return annotations;
    }

    @Override
    public SourceVersion getSupportedSourceVersion() {
        return SourceVersion.latestSupported();
    }

    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
		...
    }

    /**
     * Prints an error message
     *
     * @param e   The element which has caused the error. Can be null
     * @param msg The error message
     */
    public void error(Element e, String msg) {
        messager.printMessage(Diagnostic.Kind.ERROR, msg, e);
    }
}
```



Elements and TypeMirrors

init方法中有Element[doc](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/element/Element.html) 和  TypeMirror的工具类，那么这两个是什么呢？

Java源码中的每个元素都是Element，如下所示

```java
package com.example;	// PackageElement

public class Foo {		// TypeElement

	private int a;		// VariableElement
	private Foo other; 	// VariableElement

	public Foo () {} 	// ExecuteableElement

	public void setA ( 	// ExecuteableElement
	                 int newA	// TypeElement
	                 ) {}
}
```