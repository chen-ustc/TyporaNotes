# 鸟哥的linux基础私房菜



## 磁盘

首先我们需要对文件系统，分区，磁盘的概念有一个清楚的认知。

磁盘指的是物理层面上的一块硬盘。而文件系统和分区都是在磁盘的基础上建立起来的逻辑层面的概念。

分区是指将一个磁盘的存储空间进行人为的划分，在逻辑上分成多个区域，filesystem本来是指管理和调度存储单元的形式，传统意义上一个分区中只有一种filesystem，但事实上现在一个分区中经常会存在多种filesystem，因而文件系统又多了另一层含义，分区内挂载在同一位置，有相同的filesystem的一部分存储单元的集合。

### df命令

df命令可以查看所有文件系统的信息，常用的参数为-h。

![image-20220607155740034](D:/note_img/image-20220607155740034.png)

### du命令

du命令可以显示当前目录下的所有文件或目录占用磁盘的信息

### lsblk命令

意为 ls block ，可以列出系统中所有磁盘的信息。

常用参数：

- -p 列出完整的设备文件名，而不仅列出最后的名字

### blkid命令

需要superuser权限。可以列出磁盘的uuid（universally unique identifier），uuid可以用来挂载或者使用这个设备或文件系统。

![image-20220607160740119](D:/note_img/image-20220607160740119.png)

### gdisk/fdisk工具

gdisk和fdisk工具是用来给磁盘分区的软件。其中gdisk用于gpt分区表的磁盘，而fdisk适用于mbr分区表的磁盘。

**使用流程：**

1. 用lsblk或者blkid来找到磁盘

2. 再用`parted /dev/xxx print`查看磁盘的分区表信息

3. 选择用gdisk还是fdisk来怼分区进行操作.

   <font color='green'>其中2和3都是需要sudo操作的</font>

### mkfs命令

make filesystem 用于创建文件系统,也就是格式化某个设备.

用法:`mkfs.ext4 /dev/sda`

可以通过输入mkfs,双击tab的方式查看其支持的文件系统.

### xfs_repair和fsck.ext4命令

用来检查和修复文件系统

 