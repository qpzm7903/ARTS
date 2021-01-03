linux命令 tree

[toc]

# 在windows10上的git-bash使用

windows上的git-bash不支持tree命令，需要独立安装

## 下载tree命令的二进制包

1. 进入[Tree for windows](http://gnuwin32.sourceforge.net/packages/tree.htm)，下载`sources`安装包
2. 安装后将安装目录下的tree.exe放到git-bash的目录`C:\Program Files\Git\usr\bin`下

## 测试

在git-bash中输入tree

```bash
$ tree -L 1
.
|-- aspectJDemo.iml
|-- compile.sh
|-- pom.xml
|-- readme.md
|-- src
`-- target

```

##  常用命令

`tree -d` 只显示文件夹

```bash
$ tree -d
.
`-- src
    `-- main
        |-- java
        |                                   `-- org
        |                                   `-- example
        `-- resources
            `-- META-INF

7 directories

```



`tree -L n` 显式项目的层级

```bash
$ tree -L 2
.
|-- aspectJDemo.iml
|-- compile.sh
|-- pom.xml
|-- readme.md
`-- src
    `-- main

```



[更多参考](http://gnuwin32.sourceforge.net/packages/tree.htm)



# windows cmd上的命令上自带的tree也可以查看目录树

- tree/? :提示

```bash
D:tree/?
以图形显示驱动器或路径的文件夹结构。

TREE [drive:][path] [/F] [/A]

   /F   显示每个文件夹中文件的名称。
   /A   使用 ASCII 字符，而不使用扩展字符。
```

