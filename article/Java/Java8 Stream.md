# lambda 

# 作用

Java里面一种传递行为或代码的手段，看起来简介，可以提高编码效率。



## 基本语法

(parameters)->expression

or

(parameters)->{statements;}



parameters可以有参数类型



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



## 再哪里使用

函数式接口的地方



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



同一个lambda可以对应到多个函数式接口，只要方法签名一致就行。



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

场景

给个list`["hello","world"]`得到每个字母的的一个list

三个尝试

```java
        List<String[]> collect = Stream
                .of("hello", "world")
                .map(word -> word.split(""))
                .collect(toList());

        List<Stream<String>> collect1 = Stream
                .of("hello", "world")
                .map(word -> word.split(""))
                .map(Arrays::stream)
                .collect(toList());

        List<String> collect2 = Stream
                .of("hello", "world")
                .map(word -> word.split(""))
                .flatMap(Arrays::stream)
                .collect(toList());
```

看看flatmap的签名

```java
    <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
```

flatmap方法让你把一个流中的每个值都换成另一个流，然后把所有的流连接起来成为一个流。



## 查找与匹配

StreamAPI通过allMatch、anyMatch、noneMatch、findFirst和findAny方法提供了这样的工具。

这些操作都是会短路的，类似 and or



## 归约

reduce

### 元素求和

```java
    @Test
    public void test_reduce() {
        List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        Integer reduce = integers.stream().reduce(0, (a, b) -> a + b);
        System.out.println(reduce);
    }
```

看这个reduce的签名

```java
T reduce(T identity, BinaryOperator<T> accumulator);
```

doc里面写着等价于

```java
 T result = identity;
 for (T element : this stream)
     result = accumulator.apply(result, element)
 return result;
```
变体

```java
Optional<Integer> reduce1 = integers.stream().reduce((a, b) -> a + b);
```

返回的是Optional，因为没有初始值，可能为空。



### 求最大值、最小值

```java
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Optional<Integer> max = integers.stream().reduce((a, b) -> a > b ? a : b);
System.out.println(max.get());  // 10
max = integers.stream().reduce(Integer::max);
System.out.println(max.get()); // 10


Optional<Integer> min = integers.stream().reduce((a, b) -> a > b ? b : a);
System.out.println(min.get()); // 0
min = integers.stream().reduce(Integer::min);
System.out.println(min.get()); // 0
```



### 有状态操作、界限

排序、删除重复都是要知道之前的记录才能做

有些操作是区分界限的。

暂时不好解释

只有中间操作可能由状态与界



## 数值流

### 原始流

为了避免box和unbox，Stream提供了原始类型流。比如IntStream，DoubleStream，LongStream



常用方法由

mapToInt   -> IntStream

mapToDouble -> DoubleStream

mapToLong -> LongStream



### 数值范围

```java
    public void test_range_close() {
        System.out.println(IntStream.rangeClosed(1, 100).reduce(0, Integer::sum));
        System.out.println(IntStream.range(1, 100).reduce(0, Integer::sum));
    }
//5050
//4950

    @Test
    public void test() {
        Map<Boolean, List<Integer>> map = IntStream
                .range(0, 100)
                .boxed()
                .collect(Collectors.toList())
                .stream()
                .collect(Collectors.partitioningBy(i -> i % 2 == 0));
        for (Map.Entry<Boolean, List<Integer>> booleanListEntry : map.entrySet()) {
            System.out.println(booleanListEntry.getKey());
            booleanListEntry.getValue().forEach(System.out::println);
        }
    }

```



## 构建流

### 由值构建

Stream.of()



### 由数组构建

Attays.stream



### 文件构建



### 函数构建  -- 无限流

迭代

```java
Stream.iterate(0, n -> n + 2)
        .limit(10)
        .forEach(System.out::println);
```

比如斐波那契数列

```java
Stream.iterate(new int[]{0, 1}, t -> new int[]{t[1], t[0] + t[1]})
        .limit(10)
        .forEach(t -> System.out.println(t[1]));
```



## 收集

```java
<R, A> R collect(Collector<? super T, A, R> collector);
```

这是Stream的收集器声明。
Collectors提供了一些静态方法来收集。

比如

toList

toSet

groupingBy

joining



### 归约、汇总 

reduce和collect



### 分组

groupingBy

可以多级分组

分组用法

一般分组后的到的是

Map<key,List<T>>

可以使用groupingBy(xxx,Collectors.maping(Collectors.toList()))提供从T到R的映射





### 分区

collect(partitioningBy())

得到的是以boolean为key的map，可以保留filter的的两个结果

可以分区后再分组





### 收集器接口 -- 自定义

```java
public interface Collector<T, A, R>
```

T-流内元素的类型

A-累加器类型

R-收集对象的类型

相当于模板方法，里面会定义几个方法，在是实现类中使用。

```java
/**
 * A function that creates and returns a new mutable result container.
 *
 * @return a function which returns a new, mutable result container
 */
Supplier<A> supplier();

/**
 * A function that folds a value into a mutable result container.
 *
 * @return a function which folds a value into a mutable result container
 */
BiConsumer<A, T> accumulator();

/**
 * A function that accepts two partial results and merges them.  The
 * combiner function may fold state from one argument into the other and
 * return that, or may return a new result container.
 *
 * @return a function which combines two partial results into a combined
 * result
 */
BinaryOperator<A> combiner();

/**
 * Perform the final transformation from the intermediate accumulation type
 * {@code A} to the final result type {@code R}.
 *
 * <p>If the characteristic {@code IDENTITY_TRANSFORM} is
 * set, this function may be presumed to be an identity transform with an
 * unchecked cast from {@code A} to {@code R}.
 *
 * @return a function which transforms the intermediate result to the final
 * result
 */
Function<A, R> finisher();
```





# Optional最佳实践

Optional表达是一种概念，某个对象可能为空。这样就避免模型在概念上的检查

optional可以看作最多包含一个元素的stream

## 创建Optional对象

空的optional

工厂方法创建  Optional.empty();



非空值创建

Opational.of(val);



可接受null的optional

Optional.ofNullable(val);



map

```java
    public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Optional.ofNullable(mapper.apply(value));
        }
    }
```

会校验是否为空，然后再执行map的操作。

flatmap

```java
    public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Objects.requireNonNull(mapper.apply(value));
        }
    }
```

也会先执行空校验，再使用mapper转为另一个Optional



orElse

为空的情况返回一个默认值

orElseGet

为空的时候接收一个supplier

orElseThrow

为空的时候接收一个supplier





### 使用map从optional对象提取和转化值

类似stream的flatmap

```java

public class OptionalDemoTest {


    @Data
    public static class Person{
        private Optional<Car> car;

    }

    @Data
    public static class Car{
        private Optional<Insurance> insurance;
    }

    @Data
    public static class Insurance{
        private String name;
    }

    @Test
    public void test() {
        Person person = new Person();
        Optional<Person> optionalPerson = Optional.of(person);
        Optional<Optional<Car>> optionalOptionalCar = optionalPerson.map(Person::getCar);
//        optionalOptionalCar.map(Car::getInsurance); // error
        Optional<Car> optionalCar = optionalPerson.flatMap(Person::getCar);
        Optional<Insurance> insurance = optionalCar.flatMap(Car::getInsurance);
        String insuranceName = insurance.map(Insurance::getName).orElse("unKnow");
    }
}
```





### filter





