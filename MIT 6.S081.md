# MIT 6.S081
#6-S081

## lecture 1

- xv6
- risc-v
- qemu



## Lab1

课程工具 https://pdos.csail.mit.edu/6.S081/2022/tools.html
课程实验 https://pdos.csail.mit.edu/6.S081/2022/labs/util.html

工具版本
QEMU 5.1+, 
GDB 8.3+, 
GCC, 
and Binutils.


问题
使用mac 13.0.1 版本安装似乎默认版本不对，还是使用VM的方式安装了。

所以 mac 13.0.1 使用virtual box 试看



###  MAC 13   +  Virtual Box




### win10 VMVare

镜像版本 ubuntu-20.04.3-desktop-amd64.iso

安装后 
```shell
sudo apt-get install git build-essential gdb-multiarch qemu-system-misc gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu
```

是最简单的，安装完依赖就可以了。


更新源
```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

sudo vim /etc/apt/sources.list



sudo apt-get update

```

```

```text
#添加阿里源
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
#添加清华源
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse multiverse
```


### 实验

