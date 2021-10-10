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







# 指针



# 指针和数组



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

