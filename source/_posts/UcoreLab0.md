---
title: UcoreLab0
mathjax: true
tags:
  - 操作系统
  - Ucore
categories:
  - OS
  - Ucore
abbrlink: 34866
date: 2021-11-12 08:25:43
---

# Ucore

## Lab0 实验准备

### 实验环境设置

 #### Linux常用指令

1. 在屏幕上输出字符：echo

```shell
echo "hello world"
```

2. 显示文件内容：cat、less、more

```shell
cat <filename>
```

`注：在本文中所有带<>符号的参数都无需加""，直接加对应的符号`

less 和 more显示比较大的文本文件内容

<!--more-->

3. 复制文件：cp

```shell
cp <filename.txt> <filename_copy.txt>
```

4. 移动文件：mv

```shell
mv <filename.txt> <filename_new.txt>
```

5. 建立一个空文本文件：touch

```shell
touch demo.txt
```

6. 删除文件：rm

```shell
rm -rf # -r是递归删除 -f是强制删除
rm -i <filename.txt> # 系统在执行删除操作之前输出一条确认提示

# 特别注意 如果每次都忘记输入i的话 可以让rm的 -i变成默认参数
alias rm = 'rm -i'
```

7. 查询文件列表：ls

```shell
ls -p # 显示某一项的类型 ，可以使文件、文件夹、快捷链接等
ls -a # 列出包括隐藏文件在内的所有文件
```

8. 查询当前进程：ps

```shell
ps
# 将会列出系统当前运行的所有进程
ps -a # 列出系统当前运行的所有进程，包括其他用户启动的


[ucore]:~$ ps
PID TTY          TIME CMD
21071 pts/1    00:00:00 bash
22378 pts/1    00:00:00 ps
```

#### Linux下的一些特性

1. 重定向

实现将命令产生的输出流指向一个文件而不是默认的终端

```shell
ls  >  <filename.txt>
#ls命令产生的输出 将会写入filename中，如果该文件不存在的话，将创建该文件
# 如果filename文件存在的话，那么将会覆盖原来的文件内容 那如果文件已经存在，但是我们想要将文件写到已有文件内容的最后该怎么办？ 使用  >> 
command >> filename 
ls >>  <filename.txt>

#另一种重定向方式
command < filename

# 讲一个文件的内容作为将要执行的命令的输入
# 已有文件 file.txt 内容为 1.txt 2.txt 6.txt 5.txt <Ctrl-D>
sort < file.txt

# 显示效果
1.txt 2.txt 5.txt 6.txt 
```

2. 管道

Linux 的 nb之处在于将几个简单的命令组合成复杂的功能，它是如何实现的呢？答案是管道，管道符号为 | 

```shell
grep -i command < myfile | sort > result.text

# 命令含义
# 搜索myfile中的命令，并将输出分类并写入到分类文件到result.text中

ls -l | less
```

3. 后台进程

CLI不是系统的串行接口，可以在执行其他命令时给出系统命令

要启动一个进程到后台，追加一个“&”到命令后面

```shell
sleep 60 &
# 睡眠命令在后台运行 您依然可以与计算机交互
# 除了不同步启动命令以外 最好将'&' 理解成 ';'
```

如果现在你有一个命令会占用你非常多时间，我想让这个命令在后台运行
只需要在命令运行时按下ctrl+z，他就会停止，然后输入bg使其转入后台，输入fg命令可以使其转回前台

```shell
sleep 60
<ctrl-z> # ctrl+z 键
bg
fg

# <ctrl-c> 为kill一个前台进程
```

#### 需要了解和使用的一些软件

1. 编辑器

+ gedit 

  gedit是gnmoe桌面环境下兼容UTF-8的文本编辑器

  优点：语法高亮，中文支持很好，简单易用

+ vim

  vim天下第一好吧，unix下的vi的改进版本，功能极为强大，unbutu建议升级到vim最新版本

  [1] 了解vim的常用命令即使用，暂时空缺，下次再补

  [2] 配置文件

  ```shell
  set nocompatible
  set encoding=utf-8
  set fileencodings=utf-8,chinese
  set tabstop=4
  set cindent shiftwidth=4
  set backspace=indent,eol,start
  autocmd Filetype c set omnifunc=ccomplete#Complete
  autocmd Filetype cpp set omnifunc=cppcomplete#Complete
  set incsearch
  set number
  set display=lastline
  set ignorecase
  syntax on
  set nobackup
  set ruler
  set showcmd
  set smartindent
  set hlsearch
  set cmdheight=1
  set laststatus=2
  set shortmess=atI
  set formatoptions=tcrqn
  set autoindent  
  ```

  将删除配置文件保存到：

  ```shell
  ~/.vimrc
  ```

  注：.vimrc默认情况不可见，ls -a，如果'~'目录下不存在，可以手动创建

2. 源码阅读工具

   understand

   商业软件、跨平台

3. 源码比较工具

+ diff、meld

  diff：用于比较不同目录或不同文件的区别

  diff是命令行工具，使用非常简单

  `meld是一个图形GUI程序，非常好用，可以直接比较两个文件夹之间的差异，非常直观`

+ patch

  patch是打补丁工具 他也是一个命令行工具

4. 硬件模拟器 

   qemu

   只需要了解到最简单的`make qemu`即可

#### 编程开发调试中的基本工具 gcc、gdb、make、makefile 

1. gcc的基本用法

   安装gcc编译环境

   ```shell
   sudo apt-get install build-essential
   ```

   编译简单的c程序

   ```shell
   # hello.c 文件内容
   
   #include <stdio.h>
   int main()
   {
   	printf("hello,world\n");
   	return 0;
   }
   
   #在命令行下
   gcc -Wall hello.c -o hello
   
   #参数解释
   将hello.c中的代码编译成机器码并存储在可执行文件hello中
   机器码的文件名 是通过-o来指定的 这个参数通常作为最后一个参数
   如果被省略，那么默认输出为a.out
   
   # -Wall 参数 为开启编译器几乎所有的常用的警告
   
   ./hello # 执行文件 ./指代当前目录
   ```

2. AT&T汇编基本语法

   AT&T格式的汇编与Intel格式的汇编有一点差异，主要在以下几个方面

   ```shell
   # 寄存器命名原则
           AT&T: %eax                      Intel: eax
   # 源/目的操作数顺序 
           AT&T: movl %eax, %ebx           Intel: mov ebx, eax
   # 常数/立即数的格式　
           AT&T: movl $_value, %ebx        Intel: mov eax, _value
   # 把value的地址放入eax寄存器
           AT&T: movl $0xd00d, %ebx        Intel: mov ebx, 0xd00d
   # 操作数长度标识 
           AT&T: movw %ax, %bx             Intel: mov bx, ax
   # 寻址方式 
           AT&T:   immed32(basepointer, indexpointer, indexscale)
           Intel:  [basepointer + indexpointer × indexscale + imm32)
   ```

   如果操作系统处于保护模式下，使用的是32位的线性地址，在计算地址的时候不用考虑segmen:offset的问题

   上面的地址应该为：

   `imm322 + basepointer + indexpointer × indexscale`

   实例：

   ```shell
   # 直接寻址
   		AT&T: foo                      Intel: [foo]
   		foo是一个全局变量，加上$表示地址引用，不加代表值引用
   		对于局部变量，可以使用堆栈指针引用
   # 寄存器间接寻址
   		AT&T: (%eax)                    Intel: [eax]
   		
   # 变址寻址
   		AT&T: _variabl(%eax)           Intel: [eax + _variable]
   		AT&T: _array( ,%eax, 4)        Intel: [eax × 4 + _array]
   		AT&T: _array(%ebx, %eax,8)     Intel: [ebx + eax × 8 + _array]
   ```

   