# 初识gdb

视频教程：https://www.bilibili.com/video/BV1EK411g7Li?p=7

**linux环境下c语言程序的调试**

==断点的含义在于程序会在运行到断点的前一行时停止==

要使用gdb调试工具需要在编译时加入参数-g

```shell
gcc hello.c -o hello -g
```

**查看gdb的使用文档**

```shell
man gdb
```



**gdb中的常用命令：**

```shell
gdb hello //在gdb模式下运行hello可执行文件
```

```shell
break 9 //在第九行打断点
b 9//b是break的简写，同样表示在第九行打断点
break main //在main函数处打断点
b main//同上
info breakpoint
info breakpoints
info b//查看已经设置的断点的信息
```

```
delete 1
d 1
//删除编号为1的断点
```

```shell
list //查看源代码，会显示十行，再次输入可再显示十行，主要是为了方便打断点
l 
```

```
run
r
//运行程序
```

```
next
n
//向下执行一行
```

```
continue
c
//继续执行，在断点处不想一行一行执行而是直接执行到下一个断点时用continue
```

```
quit
q
//退出gdb模式
```

```
print a
p a
//查看变量a当前的值
print &a
p &a
//查看变量a当前的地址
```

```
step function
s function
//进入函数进行debug
```

```
set logging on
//打开debug的日志记录功能，打开之后gdb会将你的调试过程写入到gdb.txt文件中以供查看
```

```
gdb -p PID
//调试正在运行的进程
```

