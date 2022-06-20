# time命令的妙用

## time命令的作用

Linux中time命令用于测量命令的执行时间或者系统资源的使用情况。手册中的原话是：time a simple command or give resource usage.

Q：为什么time不是用来显示系统时间的命令呢？

A：因为显示系统时间的命令是date

![image-20220414145532808](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220414145532808.png)

## time命令的用法

**简单用法**

```shell
time 命令名
```

如：![image-20220414154428110](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220414154428110.png)

这里的hello是一个向文件内输入一万次hello world的程序。

同样地，也可以用于测量命令的执行时间，如：

![image-20220414155004084](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220414155004084.png)

time命令的输出内容中有三个统计时间：real，user和sys，三个时间的含义如下：

1. real:从进程开始执行到执行完成所耗费的CPU总时间，该时间包括进程执行时实际使用的CPU时间，进程耗费在阻塞上的时间（如等待完成I/O操作）和其它进程所耗费的时间（因为Linux是多进程系统，此进程在执行的过程中，可能会有别的进程抢占CPU）。
2. user：进程执行时用户态代码所耗费的CPU时间，该时间仅指进程执行时实际使用的CPU时间，而不包括其它进程所使用的时间和本进程阻塞所消耗的时间。
3. sys：进程在内核态运行所耗费的CPU时间，也就是执行内核系统调用所耗费的CPU时间。

因此，我们大致可以得出这样的一个结论：进程真正执行的时间是多少，答案就是$ user + sys $的时间，$ real=user+sys$吗，并不然，至少我们可以看出real是包含阻塞时间的，而user和sys都不包含，因此$ real>user+sys $应该是常态。但会不会相等和小于呢？下面我们来详细地分析一下。

## 消失的时间

很容易就可以想到，当没有其他进程来抢占CPU，本进程也没有在I/O设备上发生阻塞时，就会有$ real = user+sys $。

我们来详细看看什么时候会出现$ real \neq user + sys $的情况。

1. $ real > user +sys $

```shell
[roc@roclinux ~]$ time sudo find / -name php.ini
 
real    0m0.193s
user    0m0.076s
sys 0m0.115s
```

这里可以很明显地看到 $ real > user +sys $,这是很正常的，原因正是前面所提到的，real包含阻塞时间而user和sys均不包含。

2. $ real < user + sys $

   在单核CPU系统中，这个关系式是不可能成立的。但如果我们的系统是多核CPU的话，user和sys都只会计算主核所消耗的时间，而多个核参与了计算工作，使得real减少，这样就有可能出现 $ real<user+sys$ 的情况。

   ## 找不到的resource usage

   Q:time的man手册中说，它不仅可以测量运行时间，还可以测量内存，I/O等的使用情况，但为什么上面示例中的time命令的结果没有显示出这些信息呢？
   
   A:原因在于之前所用的time命令实际上是bash的内置命令，功能比较弱，而更强大的time命令隐藏在/usr/bin/目录下，可以用`which time`来查看是否安装了time这个工具，如果没有，就先安装：
   
   ```shell
   yum install time
   ```
   
   安装完成后，我们需要在time前加上一个反斜杠，用来调用这个更加强大的time命令，而非bash内置的time命令。并且一般我们会加上参数 -v 打印出更加详细的信息。
   
   

![image-20220414162217599](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220414162217599.png)

Q:输出结果中的第五行出现了elapsed(wall clock)time，什么是elapse time，什么又是wall clock time。

A: Elapsed time翻译成中文是该程序运行的平台时间，$ Elapsed\  time = 进程结束的时刻 - 进程起始的时刻 $ 

wall clock time: 这里表示Elapsed time使用wall clock time来计时的。至于wall clock time，直译为挂钟时间，是现实时间，由变量xtime来记录，系统每次启动时会从CMOS上的RTC时间读入xtime，这个值是“自1970-01-01起经历的秒数，本秒中经历的纳秒数”。

和它相对的是monotonic time，字面意思为单调时间，实际上是指系统启动以后流逝的时间，由变量jiffies来记录。系统每次启动时jiffies会被初始化为0，每个timer interrupt,jiffies加1，也就是说它代表了系统启动后流逝的tick数。

Q：wall clock time和monotonic time有什么区别

A:从形式上看：wall clock time 是系统时间，是绝对时间，是现实中的时间，而monotonic time是相对时间，是相对系统启动时的时间。

从准确性来看：wall clock time是可以被修改的，甚至可以被写入硬件中永久保存，但monotonic time量如其名，单调递增，不可修改。

Q:为什么这里的Elapsed time=0，难道程序运行没有花费时间吗？

A：在time命令的输出中，Elapsed time是通过系统调用gettimeofday获取到的结束时间和开始时间相减得到的，因此对于运行时间较短的任务进行计时时，会产生一定的误差，time命令输出的时间统计精度基本在10ms级，也就是说，运行时间小于10ms的程序，time也无法做到精确计时。

