# 2、基本概念

### 预处理指令

#include <xxx.h>

### 函数

### 变量

类型

声明

赋值

初始化





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

- 预处理
  - 执行#开头的命令
- 编译
  - 编程机器码
- 链接
  - 把目标代码和附加的代码整合



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







# 编写大规模程序

源文件 

- 函数定义
- 外部变量
- 包含main函数

头文件

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



构建多文件程序

- 编译
  - 每个源文件单独编译，头文件不需要编译，得到.o 文件

- 链接
  - 把目标文件和库函数的代码结合起来得到可执行文件



手动构建

```
gcc -o fmt fmt.c line.c word.c
./fmt <quote > newquote
```







makefile

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



声明



程序设计



低级程序设计



标准库



输入、输出



错误处理



其他库函数











# 开发环境



windows + wsl + vscode

