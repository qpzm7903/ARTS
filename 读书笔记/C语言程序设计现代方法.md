#C语言 
#c
# 2、基本概念

### 预处理指令

#include <xxx.h>

### 函数

### 变量

类型

声明

赋值

初始化

### 逻辑运算符
`||`
`&&`
`!`

非0则为TRUE
0为FALSE

逻辑运算符有短路的情况，如果能计算表达式的值，则不会继续进行计算


### 位移运算





### 语句

### 注释

单行 

多行 /* */



### 输入输出

scanf

printf



### 常量

#define name value



### 标识符

区分大小写



关键字







## 编译和链接

- 预处理\预编译
  - 执行#开头的命令
  - 得到`.i`文件
- 编译
  - 编程机器码
  - 得到`.s`文件
- 汇编
	- 得到`.o`文件
- 链接
  - 把目标代码和附加的代码整合
  - 得到`.out`文件，也就是可执行文件



## 编译器

unix 

cc



linux

gcc

- -o 参数 用于指定可执行文件名



# 

# 基本类型

整形

浮点型

字符型

sizeof

类型转换

类型定义





# 数组

一维

多维



# 函数



```c
/* 定义 */
return_type funtion_name(parameter 形式参数...){
    语句...
    return x;
}
/* 调用 */
function_name(实际参数)
```



限制：

- 无法返回数组
- 如果忽略返回类型，编译器会假定为int
- 返回为void说明没有返回值
- 值传递



先声明函数，类似接口，再定义函数,比如

```c
/* 声明 */
float average(float a, float b);
int main(){
    average(1,2);
}
/* 定义 */
float average(float a, float b){
    return (a+b)/2;
}


```



数组参数

```c
/* c没有为函数提供简便的方法确定数组的长度 */
int f(int a[]){
    
}

/* 所以一般都传进去 */
int f(int a[], int length){
    
}
```





# 程序结构

局部变量和外部变量

局部变量

- 自动存储期限，脱离作用域自动回收



外部变量

- 声明在函数体外
- 静态存储期限，一直持有





# 指针

指针变量

- 可以存储内存地址



```c
/* 声明 指针有类型，指向唯一一种类型*/
int *p;
float *f;
```



取地址符 和 取值符

```c
int a = 10;
int *p = &a; // 获取a的内存地址，赋值给p指针
// 
assert *p == a;
```



指针作为实际参数

```c
/* c是值传递，函数想要改变变量，这种特性就比较麻烦,但是指针出现可以一定程度上解决这个问题 */

void decompose(float x, int *int_part, float *frac_part){
    *int_part = (int)x;
    *frac_part = x - *int_part;
}
int i;
float f;
decompose(3.14159,&i,&f);
printf("%d ----  %f", i, f);// output -> 3 ----  0.141590
```





const 修饰符

- 用于表示函数不会修改传递进来的指针指向的对象



指针作为返回值

```c
int *max(int *a, int *b){
    if(*a > *b){
        return a;
    }else{
        return b;
    }
}
int *p;
int x = 10;
int y = 200;
p = max(x,y);
printf("%d",*p);
```







# 指针和数组

当指针指向数组时，c允许对指针进行算数运算，从而操作数组，可以代替数组的下标操作。



指针的算数运算,只有指针指向数组的时候，才有效

```c
int a[10], *p;
p = &a[0];
*p = 5;

/* p 指向 a[0]  --->  p+j 指向 a[0+j] */
/* p 指向 a[4]  --->  p-j 指向 a[4-j] */
/*
   int *p = &a[3];
   int *q = &a[6];
   int *z = q-p;  // 等价于 *z = &a[3]
*/
```



和 ++ 运算符

```c
int a[10],*p;
p = &a[0];
*p++ = 1; // 对于编译器是  *(p++) = 1;
```



数组名作为指针

- 可以用数组名作为指向数组第一个元素的指针
- 但是不能给数组名赋值其他的指针

```
int a[10];
*a = 8;  // stores 8 in a[0]
*(a+1) = 10; // store 10 in a[1];

```



指针和多维数组

```c
int *p;
int a[10][10];
p = &a[0][0]; // or p = &a[0];  因为 a[i]  等价于 *(a+i)  那么 &a[i][0] 等价于 &(*(a[i] + 0))  等价于  &*a[i]) 等价于 a[i]
```




## 指针的进一步理解
c里的变量都是指针
指针的的类型是什么，则指针指向的内容就解释成什么。

```shell
➜  demo cat a.c
#include<stdio.h>
#include<assert.h>

int main(int argc, char **argv){
	int (*f)(int, char **) = main;
	if (argc != 0){
		char ***a = &argv, *first = argv[0], ch = argv[0][0];
		printf("arg = \"%s\"; ch = '%c'\n",first, ch);
		assert(***a == ch);
		f(argc-1, argv + 1);
	}
	return 0;
}
➜  demo gcc a.c 
➜  demo ./a.out 
arg = "./a.out"; ch = '.'
➜  demo ./a.out 1 2 3 4 hello
arg = "./a.out"; ch = '.'
arg = "1"; ch = '1'
arg = "2"; ch = '2'
arg = "3"; ch = '3'
arg = "4"; ch = '4'
arg = "hello"; ch = 'h'

```




# 字符串

char 和 char[] 以及 "xxxxx" 字符串字面量，实际是 char*类型的指针，指向以存储此字符串地址

```c
char *p;
p = "abc";  // 指向了 "abc"字符数组的第一个字符
```



输入输出

```c
    char p[10];
    scanf("%s", p); // 会跳过空白字符
    printf("%s\n", p);

	getchar(); // 获取一个字符
```



库函数

```c
#include <string.h>

// 1. strcopy
char *strcpy(char *target, const char *source);

// 2. strcat 字符串拼接
char *strcat(char *target, const char *source);

// 3. strcmp 
// 根据 s1 是否小于、等于、大于s2，返回小于，等于，大于0的值
int strcmp(const char *s1, const char *s2);

// 4. strlen
size_t strlen(const char *s);
```



命令行参数

```c
// 比如 ls -l 
// -l 就是参数
int main(int argc, char *argv[]){
    // argc  参数个数
    // argv 参数列表
}
```





# 预处理器



```c
// 宏定义
#define 

// 文件包含
#include 

// 条件编译
#if
#ifdef
#ifndef
#elif
#else
#endif
```



# 宏定义
宏展开，通过复制、粘贴改变代码的形态
#include - 粘贴




# 编写大规模程序

## 源文件 

- 函数定义
- 外部变量
- 包含main函数

## 头文件

- 可以在源文件之间共享的信息
  - 外部变量
  - 宏定义
  - 函数
  - 。。。



```c
// c语言库头文件
// 搜索系统头文件所在目录，比如 /usr/include
#include <filename>

// 其他头文件
// 搜索当前目录，然后搜索系统头文件目录，也可写绝对路径  也可以增加搜索目录 -I 参数
#include "filename"
```



#include 搜索路径





共享变量声明

```c
int i;  // no

extern int i; // yes
```



避免多次include

```c
#ifndef xxxx
#define xxxx
#endif
```



## 构建多文件程序

- 编译
  - 每个源文件单独编译，头文件不需要编译，得到.o 文件

- 链接
  - 把目标文件和库函数的代码结合起来得到可执行文件



手动构建

```
gcc -o fmt fmt.c line.c word.c
./fmt <quote > newquote
```







## makefile
#makefile

```
fmt: fmt.o word.o line.o
	gcc -o fmt fmt.o word.o line.o

fmt.o: fmt.c word.h line.h
	gcc -c fmt.c

word.o: word.c word.h
	gcc -c word.c

line.o: line.c line.h
	gcc -c line.c
```

每一组中，第一行都是依赖，第二行是实际执行的命令，依赖发生变化，那么重新执行就会只编译变化的部分。



重新构建

### 规则变量
http://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html#Automatic-Variables

$< $@ 是什么？[参考回答](https://stackoverflow.com/questions/3220277/what-do-the-makefile-symbols-and-mean)

$< 是第一个参数
$@ 是目标

### 参考文档
http://www.gnu.org/software/make/manual/html_node/index.html#SEC_Contents










# 结构 联合 枚举



## struct

类似其他语言的对象，有字段，类型

```c
strcut Part{
    int number;
    char name[NAME_LEN+1];
    int on_hand;
};
// 声明的同时，创建了两个实例

// 通过 . 操作符获取里面的字段
Part part = {222,"hello",10};
// 取地址则 &part1.number

#include <stdio.h>
#define NAME_LEN 100

struct Part
{
    int number;
    char name[NAME_LEN + 1];
    int on_hand;
};

int main()
{
    struct Part part = {222, "hello", 10};
    printf("%d\n",part.number);
    printf("%s\n",part.name);
    printf("%d\n",part.on_hand);
    return 0;
}

// 可以通过 typedef来定义名词,比如
typedef struct{
    int number;
    char name[100];
    int on_hand;
} Part;

// 使用的时候直接使用Part
```



注意结构体在函数中也是值传递，所以会复制一份，所以最好用指针传递

结构可以嵌套



## union

也是由一个或者多个成员构成，成员可能具有不同的数据类型，占据不同过得内存空间，但是编译器只为union中最大的成员分配足够的内存空间。

也就是说，union同一时刻，只能是其中的一个值，不像struct，可以每个字段都有值。

```c
union{
    int i;
    float f;
}u;
```

此外，union和struct类似

用途，可以省空间

## enumeration

```c
enum {A,B,C,D} s1;
```







# 指针的高级应用

动态内存分配、动态释放

- malloc 分配内存块，但不初始化
- calloc 分配内存块，并清除对应内存块
- realloc 调整先前分配的内存块
  - 必须是经过malloc or calloc or realloc获取的内存才能进行调整
- free 释放



函数返回的是void*指针，本质是通用指针

无法分配时，返回空指针，意思时指向空的指针，为 NULL

```c
p = malloc(10000);
if(p == NULL){
    // ...
}
```



## 动态分配字符串

```c
void *malloc(size_t size);

void *p;
p = (char*) malloc(3+1);
strcpy(p,"abc");

// 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main()
{
    char *p;
    p = (char *)malloc(3 + 1);
    strcpy(p, "abc");
    printf("%s\n", p);
}
```



## 为数组分配空间

```c
int *a;
int n = 10;
a = malloc(n * sizeof(int));
for (int i = 0; i < n; i++)
{
    printf("a[%d]=%d\n", i, a[i]);
}
```



## 给结构体分配内存

```c
```





## 释放内存

```c
void free(void *ptr);

void *p;
p = malloc(xxx);
free(p);
// p变成了空指针
```



动态分配在建立表、树、图等动态结构非常有用。





指向函数的指针
函数也占用内存，也有自己的地址，所以指针应该也能指向函数



```c
// *f 周围的括号说明f是个指向函数的指针，其反回类型位double，入参位double
double integrate(double (*f)(double), double a, double b);


double integrate(double (*f)(double), double a, double b){
    // *f 指向函数，后面加括号表示调用
    double sum = (*f)(a);
}
```



qsort里使用的函数指针

```c
void qsort (void *__base, size_t nmemb, size_t __size,
		   int (*compare)(const void*,const void *));
// base 指向数组的第一个元素
// nmemb 排序的数量
// 元素的大小
// compare 指向比较函数的一个指针

```



升序排序例子

```c
#include <stdio.h>
#include <stdlib.h>

int compare_arr(const void *p, const void *q)
{
    return *(int *)p > *(int *)q;
}

int main()
{
    int a[10];
    a[0] = 5;
    a[1] = 4;
    a[2] = 3;
    a[3] = 2;
    a[4] = 1;

    qsort(a, 5, sizeof(int), compare_arr);
    for (int i = 0; i < 5; i++)
    {
        printf("a[%d]=%d\n", i, a[i]);
    }
}
```











# 声明



# 程序设计



# 低级程序设计

## 按位运算

移位运算

```
<< 左移     右端补0
>> 右移     无符号数或非负数，左端补0，其他由实现定义
```



按位反、与、异或

```
~  按位反
&
^
|
```





# 标准库


## string
#string
https://www.unix.com/man-page/posix/7posix/string.h/


### strtok


### strtol
### atoi
conter a string to integer


### strcpy
复制字符串









# 输入、输出



# 错误处理

c语言不适合做

## assert函数

```c
#include <assert.h>

int main(){
    int a = 0;
    assert(a > 10);
}
>>> 
a.out: assert.c:5: main: Assertion `a > 10' failed.
Aborted
```



缺点，引入额外的检查，会增加运行时间。

禁用assert

```c
#define DEBUG
#include <assert.h>

```



`errno.h`







## 信号

信号宏，`signal.h`定义了一些列的宏。



`signal`函数

```c
void (*signal(int sig, void(*func)(int))(int);
```





## setjmp 和 longjmp



# 其他库函数











# 开发环境



windows + wsl + vscode



# 预编译
#预编译

以下两种有什么区别？
1. `#include <stdio.h>`
2. `#include "stdio.h"`


<> 只去系统目录找
"" 在当前目录找

但是可以指定系统目录，类似Java的ClassPath。 通过 `-IPath`，常用的`-I.`，表示把当前目录加到系统搜索目录下。


include的内容去哪里找？？
gcc时，通过verbose参数，可以看到完整的命令


把include的文件复制粘贴进来。

例如
在任意一处include一个东西，include的内容再次include一个字符串文件。

```shell
/mnt/c/Users/qpzm7903/workspace/os1 ᐅ cat a.c
int main(){
    printf(
        #include "a.inc"
    );
    return 0;
}
/mnt/c/Users/qpzm7903/workspace/os1 ᐅ cat a.inc
#include "b"

/mnt/c/Users/qpzm7903/workspace/os1 ᐅ cat b
"hello world\n"
/mnt/c/Users/qpzm7903/workspace/os1 ᐅ gcc a.c
a.c: In function ‘main’:
a.c:2:5: warning: implicit declaration of function ‘printf’ [-Wimplicit-function-declaration]
    2 |     printf(
      |     ^~~~~~
a.c:2:5: warning: incompatible implicit declaration of built-in function ‘printf’
a.c:1:1: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
  +++ |+#include <stdio.h>
    1 | int main(){
/mnt/c/Users/qpzm7903/workspace/os1 ᐅ ls
a.c  a.inc  a.out  b
/mnt/c/Users/qpzm7903/workspace/os1 ᐅ ./a.out
hello world
/mnt/c/Users/qpzm7903/workspace/os1 ᐅ
``` 




### 有趣的编译

```c
#include <stdio.h>

int main(){
#if aa == bb
     printf("yes\n");
#else
     printf("No\n");
#endif
}
```

实际会输出 yes

通过`gcc -E ` 查看预编译的结果

为什么 `aa == bb`?

因为本质是字符串替换，变量没有定义就使用，就是null，null和null相等。