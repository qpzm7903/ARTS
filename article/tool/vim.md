# vim 入门
#vim

https://coolshell.cn/articles/5426.html



## 命令速查



# 配置
#vim-config
配置文件

windows：_vimrc

linux:`~/.vimrc`

或者通过 
```shell
$ echo $HOME
```
查看用户根目录名，然后再到根目录下修改`.vimrc`配置


`:help vimrc-intro` 可以查看帮助文档

## 帮助文档
`:help user-manual`






## 常用配置

```shell
# 支持语法高亮
syntax on
# 根据文件类型自动配置缩进
filetype plugin indent on
# 新行处理缩进
set autoindent
# tab展开为空格
set expandtab
# tab键4个空格
set tabstop=4
# 自动缩进空格数
set shiftwidth=4
# 退格
set backspace=2
# 修改配色
colorscheme murphy
```



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



## 基础配置

### 行号

```txt
:set number
```



### 语法高亮

```
syntax on
```



### tab键转为空格

```
set expandtab
set softtabstop=4 // 转为4个空格
```














# 操作

## 移动光标

hkjl 分别←↓↑→

行尾 $

行首 0 或者 ^



一次移动超过一次字符

Vim 支持使用 b/w 和 B/W，来进行以单词为单位的跳转。它们的意思分别是 words Backward 和 Words forward，用来向后或向前跳转一个单词。小写和大写命令的区别在于，小写的跟编程语言里的标识符的规则相似，认为一个单词是由字母、数字、下划线组成的（不严格的说法），而大写的命令则认为非空格字符都是单词。

### 跳转
`G`  buttom of file
`gg` start of file

`numberG` 跳转到指定行数


## 删除

`d` operator

motions
w 直到下一个单词
e 当前单词
$ 整行

`dd` 删除一整行


motions可以增加数量，比如
`d2w`  删除两个单词

配合 `0` `^` 可以实现删除到行首，配合 `$` 可以删除到行末

### 撤销 undo

`u`


撤销undo

`<C,R>`

### put 
y复制，然后`p`将复制的内容放置到当前光标处

### 替换
`rx`  输入r，然后输入替换的字符

`R`进入替换模式，输入即可


### change
`ce` 会删除单词，并进入插入模式
e可以替换成`w,$,0`等

## 查找字符

f(find) 

t(till)



（） 上一句、下一句

{} 上一段，下一段

## 搜索 /
在命令模式下输入`/`，接着输入搜索的字符回车即可。

从下往上搜
`?`


## 括号匹配搜索 %

`%`  跳转至括号的另一半


## 替换
`:s/old/new/g`

末尾的`g`表示全局
如果不加则表示删除第一个遇到的
`:s/old/new`


`:#,#s/old/new/g` 限制行号
`:%s/old/new/g` whole file
`:%s/old/new/gc` whole file and need confirm



参考https://harttle.land/2016/08/08/vim-search-in-file.html

### 在block模式下替换

在block模式下，按`:%s`会自动进入区块替换模式


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
r 替换光标下的字符；R 则进入替换模式，每次按键（直到 `<Esc>）`替换一个字符。
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

### 选择模式 v
选择后的内容可以保存，可以删除，可以复制等。


## 

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

# 多窗口与标签



### 窗口切割

- `:split or :sp`  水平切割
- `:vsplit :vp` 垂直分割

后面可以加文件名，不加就切割当前文件



### 多窗口移动

`<C-W>` 加方向键（h、j、k、l、`<Left>` 等等）可以在窗口之间跳转
`<C-W>w` 跳转到下一个（往右和往下）窗口，如果已经是右下角的窗口，则跳转到左上
角的窗口
`<C-W>W` 跳转到上一个（往左和往上）窗口，如果已经是左上角的窗口，则跳转到右下
角的窗口
`<C-W>n` 或 :new 打开一个新窗口
`<C-W>c` 或 :close 关闭当前窗口；当前窗口如果已经是最后一个则无效
`<C-W>q` 或 :quit 退出当前窗口，当最后一个窗口退出时则退出 Vim

`<C-W>o` 或 :only 只保留当前窗口，关闭其他所有窗口
`<C-W>s` 和 :split 作用相同，把当前窗口横向一分为二
`<C-W>v` 和 :vsplit 作用相同，把当前窗口纵向一分为二
`<C-W>=` 使得所有窗口大小相同（当调整过终端或图形界面 Vim 的窗口大小后特别有
用）
`<C-W>_` 设置窗口高度，命令前的数字表示高度行数，默认为纵向占满（想专心编辑某
个文件时很有用）
`<C-W>|` 设置窗口宽度，命令前的数字表示宽度列数，默认为横向占满
`<C-W>+` 增加窗口的高度，命令前的数字表示需要增加的行数，默认为 1
`<C-W>-` 减少窗口的高度，命令前的数字表示需要减少的行数，默认为 1
`<C-W>>` 增加窗口的宽度，命令前的数字表示需要增加的列数，默认为 1
`<C-W><lt>`（提醒，我们用 `<lt>` 表示“<”键）减少窗口的宽度，命令前的数字表示
需要增加的列数，默认为 1


## 标签
VIM中使用标签页来组织一个窗口的集合，类似于工作区一样。
![[Pasted image 20220805073636.png]]
类似这种效果



- `:tabnew`  创建标签
- 标签跳转
	- `gt`  or `:tabnext`下一个
	- `gT` or  `:tabprevious` 上一个
- 



## 执行外部命令

`:!command`

`:w filename` 保存当前文件



# 缓冲区
- 每个打开的文件都有一个缓冲区
- `:e filename` 打开其他文件
- `:b number` 跳转其他缓冲区
	- 简写 `:bnumber`
- `:ls`  可以查看
	- 1 缓冲区编号
	- `%` 缓冲区位于当前从窗口
	- a 该缓冲区处于活动状态，已被加载并可见
如下图
![[Pasted image 20220805072122.png]]



# 文件树
- `:Ex`  完整 `:Explore`
	- 实际使用的是netrw文件管理器，是vim自带的一个插件
- 操作
	- `enter` 打开
	- `-` 上一层目录
	- `D` 删除
	- `R` 重命名



# 定制

了解运行文件

## 目录

大部分 Unix 下面：/usr/share/vim/vim82
macOS Homebrew
下：/usr/local/opt/macvim/MacVim.app/Contents/Resources/vim/runtime
Windows 下：C:\Program Files (x86)\Vim\vim82



## 设置选项

`:set ic`  忽略搜索的大小写敏感
`:set hls is` 高亮搜索



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



配置参考

```vim

"""""""""""""""""""""""""""""""""""""""""""基本设置"""""""""""""""""""""""""""""""""""""""""""""
filetype on	"开启文件类型侦测
filetype indent on	"适应不同语言的缩进
syntax enable	"开启语法高亮功能
syntax on 	"允许使用用户配色


"""""""""""""""""""""""""""""""""""""""""""显示设置"""""""""""""""""""""""""""""""""""""""""""""
set laststatus=2        	"总是显示状态栏
set ruler               	"显示光标位置
set number              	"显示行号
set cursorline          	"高亮显示当前行
set cursorcolumn            "高亮显示当前列
set hlsearch                " 高亮搜索结果
exec "nohlsearch"
set incsearch               "边输边高亮
set ignorecase              "搜索时忽略大小写
set smartcase

set relativenumber          "其他行显示相对行号
"set scrolloff=5            "垂直滚动时光标距底部位置


"""""""""""""""""""""""""""""""""""""""""""编码设置"""""""""""""""""""""""""""""""""""""""""""""
set fileencodings=utf-8,gb2312,gbk,gb18030,cp936    " 检测文件编码,将fileencoding设置为最终编码
set fileencoding=utf-8                              " 保存时的文件编码
set termencoding=utf-8                              " 终端的输出字符编码
set encoding=utf-8                                  " VIM打开文件使用的内部编码


"""""""""""""""""""""""""""""""""""""""""""编辑设置"""""""""""""""""""""""""""""""""""""""""""""
set expandtab   	"扩展制表符为空格
set tabstop=4   	"制表符占空格数
set softtabstop=4	"将连续数量的空格视为一个制表符
set shiftwidth=4	"自动缩进所使用的空格数
"set textwidth=79	"编辑器每行字符数
set wrap            "设置自动折行
set linebreak       "防止单词内部折行
set wrapmargin=5      "指定折行处与右边缘空格数
set autoindent  	"打开自动缩进
set wildmenu    	"vim命令自动补全
```



##  神奇操作

`!%xxd"` 编辑16进制文件


# 复制粘贴、寄存器
- `y`  yank 复制
	- 默认复制到默认寄存器中
		- 默认寄存器是 `"`
		- 所以`y`的完整命令是`""y`
	- `ye` 复制到下一个单词结尾
	- 
- `p` 粘贴
	- 完整命令 `""p`


## 寄存器
复制、删除的东西都会存储到寄存器里。VIM支持多种寄存器，用数字、字母、特殊符号来标识。

- `a~z` 所标识的寄存器可以用于手动复制数据
- `"ayw`  复制一个单词到`a`寄存器
- `"ap` 从a寄存器中粘贴到指定位置

- `1-10`寄存器
	- 用于存储最后10此操作删除的历史记录，0是最后一次
	- 如果记性好，可以记住前几次操作的，然后恢复



- `:reg` 查看所有寄存器的内容
- `:reg name` 获取指定寄存器的内容


## 和外部世界交互
Vim内置了两个寄存器和外部世界交互

- `*` 寄存器表示系统粘贴板，在linux终端里是鼠标选择内容，mac、windows是当前的粘贴板内容
- `+`  寄存器（只用于linux） 用于windows ctrl+c  , ctrl+v 风格的组合键

# marks  文件内标记
#marks 

## 意义
类似书签一样，可以对文件内容打标签，然后在其他地方快速跳转到指定书签的位置。
书签分为局部和全局。


## 标签操作

### 创建标签
局部是通过`ma-z`来创建的，在当前文件内生效
全局是通过`mA-Z`来创建的，在所有文件内生效

### 跳转至标签
```shell
`a
or
'a
```

### 删除标签
```
d'a

d`a

```


### 改变标签
```shell
c`a 将当前行的标签设置为a

```



### 查看
```shell
:marks  // 列出所有当前的标签
:marks aB  // 列出a和B的位置
```


### 跳转至前一个标签，或者后一个
```shell
]'  ]` 跳转至下一个小写标签行
[' [`  上一个
```


### 特殊标签 ？？

```shell
`.   
`“ 
`0 跳转至最后一次编辑的地方
`1 跳转上一次编辑的地方 ,,, `0-9 类推
'' 调回去
`` 跳回去

```

## 参考
https://vim.fandom.com/wiki/Using_marks


# 插件
#vim-plugin


## 加载插件
在.vimrc中添加

```vim
# 加载所有插件
packloadall
# 为所有插件加载帮助文档
silent! helptags ALL 
```

## 插件管理demo


### nerdtree
```shell

mkdir -p ~/.vim/pack/plugins/start
git clone https://github.com/scrooloose/nerdtree.git ~/.vim/pack/plugins/start/nerdtree
 
```

使用插件

在vim中
```
:NERDTree
```
就会打开一个新的窗口，在里面展示目录，例如
![[Pasted image 20220805074105.png]]





### 参考
https://vimjc.com/vim-plug.html


## ctags
vim版本依赖 8.0以上
#todo 



# tab管理
参考  http://vimdoc.sourceforge.net/htmldoc/tabpage.html


？？怎么用