# lambda

## 基本语法

(parameters)->expression

or

(parameters)->{statements;}



函数式接口就是指定一个抽象方法的接口，常见的比如

```java
public interface Comparator<T>{int compare(T o1, T o2);}

public interface Runnable{ void run();}

public interface Callable<V> { V call();}

// 可以使用@FunctionalInterface注解
```



lambda允许直接内联函数式接口



java8引入的函数式接口

```java
@FunctionalInterface
public interface Predicate<T>{boolean test(T t);}
// 接受T，并通过t得到一个布尔表达式，比如 fileter

@FunctionalInterface
public interface Consumer<T> {void accept(T t);}
// 接受T，并对t执行一些操作 ， 比如 forEach print

@FunctionalInterface
public interface Function<T,R>{
    R apply(T t);
}
// 常用于map
```





## 异常

函数式接口不允许抛出受检异常



## 类型检查、推断、限制、局部变量



lambda表达式只能捕获给的局部变量一次

```java
int number = 1;
Runnable r ()->System.out.println(numer);// error 引用的外部变量必须是final的或者逻辑上是final的
number = 2;
```

原因：

局部变量在栈帧上，类变量在堆上，lambda在另一个线程使用的时候，可能在这个局部变量被回收后去访问，那么访问的实际是这个变量的副本？？



## 方法引用

复用现有的函数式接口

思想：如果是直接调用某个方法，那么直接用名称来调用，而不是描述怎么调用

一些例子

| lambda                                 | 引用                            |
| -------------------------------------- | ------------------------------- |
| (Apple a)-> a.getWeight()              | Apple::getWeight                |
| ()->Thread.currentThread().dumpStack() | Thread.currentThread::dumpStack |
| (str, it)->str.substring(i)            | String::substring               |



条件

1. 静态方法
2. 任意类型实例方法的引用
3. 现有对象实例的方法引用



构造函数引用

无参

一个参数

两个参数

n个参数



# 函数式数据处理



流的概念

用声明性的方式来处理集合



定义

从支持数据处理操作的源生成的元素序列

java.util.stream.Stream

流只能遍历一次，再次便利会抛出异常。



测试例子

```java
@Data
public class Dish {
    private final String name;
    private final boolean vegetarian;
    private final int calories;
    private final Type type;


    public enum Type {MEAT,FISH, OTHER,}
}
   

private List<Dish> menu = Arrays.asList(
            new Dish("pork", false, 800, Dish.Type.MEAT),
            new Dish("beef", false, 700, Dish.Type.MEAT),
            new Dish("chicken", false, 400, Dish.Type.MEAT),
            new Dish("french fries", true, 530, Dish.Type.OTHER),
            new Dish("rice", true, 350, Dish.Type.OTHER),
            new Dish("season fruit", true, 120, Dish.Type.OTHER),
            new Dish("pizza", true, 550, Dish.Type.OTHER),
            new Dish("prawns", false, 300, Dish.Type.FISH),
            new Dish("salmon", false, 450, Dish.Type.FISH));


```



## 创建流

来自容器，容器由一个stream方法可以得到流



## 流操作

分为中间操作和终端操作

### 中间操作

中间操作的结果依旧是流

流的操作是线性的，如下

```java
@Test
    public void test() {
        List<String> collect = menu.stream()
                .filter(d -> {
                    System.out.println("filtering " + d.getName());
                    return d.getCalories() > 300;
                })
                .map(d -> {
                    System.out.println("mapping " + d.getName());
                    return d.getName();
                })
                .limit(3)
                .collect(Collectors.toList());

        System.out.println(collect);
    }

filtering pork
mapping pork
filtering beef
mapping beef
filtering chicken
mapping chicken
[pork, beef, chicken]
```



### 终端操作

只要结果不是流就是终端操作



# 使用流



## 筛选和切片

filter、distinct、limit、skip、

## 映射

map:对流中每个元素应用函数

```java
    @Test
    public void test_map() {
        List<String> words = Arrays.asList("Java 8", "Lambdas", "In", "Action");
        List<Integer> wordLengths = words.stream()
                .map(String::length)
                .collect(toList());
        System.out.println(wordLengths);
    }
// [6, 7, 2, 6]
```



flatmap:扁平化











