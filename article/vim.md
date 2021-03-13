# vim 入门



## 命令速查

![image-20210102215408664](image-20210102215408664.png)



# 配置

配置文件

windows：_vimrc

linux:.vimrc







备份和撤销文件

 Linux/macOS

```bash
set nobackup
set undodir=~/.vim/undodir
```

windows

```bash
set nobackup
set undodir=~\vimfiles\undodir
```



# 操作

## 移动光标

hkjl 分别←↓↑→

行尾 $

行首 0 或者 ^



一次移动超过一次字符

Vim 支持使用 b/w 和 B/W，来进行以单词为单位的跳转。它们的意思分别是 words Backward 和 Words forward，用来向后或向前跳转一个单词。小写和大写命令的区别在于，小写的跟编程语言里的标识符的规则相似，认为一个单词是由字母、数字、下划线组成的（不严格的说法），而大写的命令则认为非空格字符都是单词。



## 查找字符

f(find) 

t(till)



（） 上一句、下一句

{} 上一段，下一段



## 文本修改

c（change）

d 加动作来进行删除（dd 删除整行）；D 则相当于 d$，删除到行尾。

c 加动作来进行修改（cc 修改整行）；C 则相当于 c$，删除到行尾然后进入插入模
式。
s 相当于 cl，删除一个字符然后进入插入模式；S 相当于 cc，替换整行的内容。
i 在当前字符前面进入插入模式；I 则相当于 ^i，把光标移到行首非空白字符上然后进
入插入模式。
a 在当前字符后面进入插入模式；A 相当于 $a，把光标移到行尾然后进入插入模式。
o 在当前行下方插入一个新行，然后在这行进入插入模式；O 在当前行上方插入一个新
行，然后在这行进入插入模式。
r 替换光标下的字符；R 则进入替换模式，每次按键（直到 <Esc>）替换一个字符。
u 撤销最近的一个修改动作；U 撤销当前行上的所有修改。



## 文本对象选择



动作 + 附加键 + 范围

动作

	- c（change）

- d  (delete)

- y（copy）

- p（paste）

附加键

-  i（inner）表示所在的部分
- a （a word) 表示后续部分

范围

- w 单词
- 双引号 双引号范围
- 圆括号 圆括号范围
- ....

附加键前可以加数字，表示层级



翻页

pageUp

pageDown



vim----

ctrl+B backword

ctrl+F forward



翻半页

ctrl+D

ctrl+U



## 重复

`; `重复最近的字符查找（f、t 等）操作
`,` 重复最近的字符查找操作，反方向
`n `重复最近的字符串查找操作（/ 和 ?）
`N `重复最近的字符串查找操作（/ 和 ?），反方向
`. `重复执行最近的修改操作



## 打开多文件

vim file1 file2

多文件切换



:args：可以显示“参数”，即需要编辑的多个文件的列表
:args 文件名：使用新的文件名替换参数列表
:next（可缩写为 :n）：打开下一个文件；如当前文件修改（未存盘）则会报错中止，
但如果命令后面加 ! 则会放弃修改内容，其他命令也类似
:Next（缩写 :N）或 :previous（缩写 :prev）：打开上一个文件

:first 或 :rewind：回到列表中的第一个文件
:last：打开列表中的最后一个文件

## 多窗口编辑



窗口切割

:split or :sp  水平切割

后面可以加文件名，不加就切割当前文件

:vsplit :vp

竖直



多窗口移动

<C-W> 加方向键（h、j、k、l、<Left> 等等）可以在窗口之间跳转
<C-W>w 跳转到下一个（往右和往下）窗口，如果已经是右下角的窗口，则跳转到左上
角的窗口
<C-W>W 跳转到上一个（往左和往上）窗口，如果已经是左上角的窗口，则跳转到右下
角的窗口
<C-W>n 或 :new 打开一个新窗口
<C-W>c 或 :close 关闭当前窗口；当前窗口如果已经是最后一个则无效
<C-W>q 或 :quit 退出当前窗口，当最后一个窗口退出时则退出 Vim

<C-W>o 或 :only 只保留当前窗口，关闭其他所有窗口
<C-W>s 和 :split 作用相同，把当前窗口横向一分为二
<C-W>v 和 :vsplit 作用相同，把当前窗口纵向一分为二
<C-W>= 使得所有窗口大小相同（当调整过终端或图形界面 Vim 的窗口大小后特别有
用）
<C-W>_ 设置窗口高度，命令前的数字表示高度行数，默认为纵向占满（想专心编辑某
个文件时很有用）
<C-W>| 设置窗口宽度，命令前的数字表示宽度列数，默认为横向占满
<C-W>+ 增加窗口的高度，命令前的数字表示需要增加的行数，默认为 1
<C-W>- 减少窗口的高度，命令前的数字表示需要减少的行数，默认为 1
<C-W>> 增加窗口的宽度，命令前的数字表示需要增加的列数，默认为 1
<C-W><lt>（提醒，我们用 <lt> 表示“<”键）减少窗口的宽度，命令前的数字表示
需要增加的列数，默认为 1



# 定制

了解运行文件

## 目录

大部分 Unix 下面：/usr/share/vim/vim82
macOS Homebrew
下：/usr/local/opt/macvim/MacVim.app/Contents/Resources/vim/runtime
Windows 下：C:\Program Files (x86)\Vim\vim82





### 分类

syntax：Vim 的语法加亮文件
doc：Vim 的帮助文件
colors：Vim 的配色方案
plugin：Vim 的“插件”，即用来增强 Vim 功能的工具



## 用户的vim配置目录

优先使用用户的



## 包管理器

https://github.com/k-takata/minpac

windows下

`git clone https://github.com/k-takata/minpac.git %USERPROFILE%\vimfiles\pack\minpac\opt\minpac`

然后修改_vimrc文件，添加

```bash
if exists('*minpac#init')
  " Minpac is loaded.
  call minpac#init()
  call minpac#add('k-takata/minpac', {'type': 'opt'})
  " Other plugins
endif
if has('eval')
  " Minpac commands
  command! PackUpdate packadd minpac | source $MYVIMRC | call minpac#update('',{'do': 'call minpac#status()'})
  command! PackClean packadd minpac | source $MYVIMRC | call minpac#clean()
  command! PackStatus packadd minpac | source $MYVIMRC | call minpac#status()
endif

```

保存后重启vim，就可以使用 :PackUpdate :PackClean :PackStatus命令了。

先安装toppe/vim-eunuch试试

在other plugins下增加

`  call minpac#add('tpope/vim-eunuch')`
然后：w保存

：PackUpdate，会进入安装状态，等会就提示xxx插件已安装



接着安装

`  call minpac#add('yegappan/mru')`

most recently used 这个插件

安装后重启vim，图形编辑器的file栏目下会多出一个 `recent files`的按钮，可以查看最近编辑文件。





## 包目录结构

安装玩minpac,eunuch,mru之后，看看vimfiles下的pack目录长什么样

结构如下

```txt
`-- minpac
    |-- opt
    |   `-- minpac
    |       |-- autoload
    |       |   `-- minpac
    |       |-- doc
    |       |-- plugin
    |       |-- test
    |       `-- tools
    `-- start
        |-- mru
        |   |-- doc
        |   |-- plugin
        |   `-- test
        `-- vim-eunuch
            |-- doc
            `-- plugin

```

每个包（比如，mru、vim-eunuch）都有有自己的doc

vim 8 启动会加载pack/*/start下的包。

用户可以手动:packadd加载opt下的包

而minpac是一个vim8的包管理器。