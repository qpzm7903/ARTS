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





# 字符串



# 预处理器



# 编写大规模程序



# 结构 联合 枚举



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

