Linux学习

参考鸟哥的linux私房菜



切换管理员

```bash
su -
```



# 基础命令



grep



find 



## 系统状态检测



ifconfig 网卡、网络信息

uname [-a]系统内核、系统架构信息

uptime 系统负载

free [-h] 内存使用情况

who 当前用户信息

last 访问记录

ping 测试网络连通性

tracepath 显式数据包经过的路由信息

netstat 网络、路由表等网络信息

history 命令历史



## 查找定位文件命令

pwd 当前工作目录

cd 切换目录

cd - 返回上一次所在的目录

cd .. 进入上一级目录

cd ~ 进入家目录



ls 显式目录文件信息

tree 树状图展示目录内容

find 查找文件位置

locate 快速搜索文件所在的位置

whereis 搜索二进制程序所在的位置

which 



## 文本编辑命令

cat 查看

more 查看

head 查看

tail 查看

tr 替换

wc 单词统计

stat 文件的具体信息

grep 提取文本内容

cut 按列提取

diff 比较差异

uniq 去除重复行

sort 排序



## 文件目录管理

touch

mkdir

cp

mv

rm

dd

file

tar 打包或者解压





# shell脚本编程



变量

声明

`name=value`

或者从表达式中获取

```bash
name=$(xxx)
name=`xxxx`
```



使用

`$name`



数学运算

```bash
expr 1+2

$[1+2]
```



状态码



退出

`exit code`





条件

```bash
if command
then 
	commands
fi

if command
then
	commands
else
	commands
fi

```



`test`命令

use

`test condition`



数值比较

没有使用符号，而是用

| 表达式    | 含义 |
| --------- | ---- |
| n1 -eq n2 | 相等 |



字符串比较



文件比较

| 表达式  | 含义              |
| ------- | ----------------- |
| -d file | 存在 且是一个目录 |
| -e file | 存在              |
| -f file | 存在 并且是个文件 |





if-then的高级特性

`((xxxx))`

复杂的表达式

`[[xxx]]`

可以进行模式匹配



case 



循环

for

```bash
for var in list
do 
	commands
done

```







函数

```bash
function name{
	commands
	[return ]
}
```





命令行参数

$0是程序名，$1是第一个参数，$2是第二个参数，依次类推

第十个是 ${10}

特殊变量`$#`含有脚本运行时携带的命令行参数的个数



`$*`和`$@`变量可以用来轻松访问所有的参数。这两个变量都能够在单个变量中存储所有的命
令行参数。
`$*`变量会将命令行上提供的所有参数当作一个单词保存。这个单词包含了命令行中出现的每
一个参数值。基本上`$*`变量会将这些参数视为一个整体，而不是多个个体。
另一方面，`$@`变量会将命令行上提供的所有参数当作同一字符串中的多个独立的单词。这样
你就能够遍历所有的参数值，得到每个参数。这通常通过for命令完成。



数组



## sed1



