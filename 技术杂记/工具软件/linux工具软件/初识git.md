[TOC]



# 初识Git

## Git简介

### Git的产生历史

很多人都知道，Linus在1991年创建了开源的Linux，从此，Linux系统不断发展，已经成为最大的服务器系统软件了。

Linus虽然创建了Linux，但Linux的壮大是靠全世界热心的志愿者参与的，这么多人在世界各地为Linux编写代码，那Linux的代码是如何管理的呢？

事实是，在2002年以前，世界各地的志愿者把源代码文件通过diff的方式发给Linus，然后由Linus本人通过手工方式合并代码！

你也许会想，为什么Linus不把Linux代码放到版本控制系统里呢？不是有CVS、SVN这些免费的版本控制系统吗？因为Linus坚定地反对CVS和SVN，这些集中式的版本控制系统不但速度慢，而且必须联网才能使用。有一些商用的版本控制系统，虽然比CVS、SVN好用，但那是付费的，和Linux的开源精神不符。

不过，到了2002年，Linux系统已经发展了十年了，代码库之大让Linus很难继续通过手工方式管理了，社区的弟兄们也对这种方式表达了强烈不满，于是Linus选择了一个商业的版本控制系统BitKeeper，BitKeeper的东家BitMover公司出于人道主义精神，授权Linux社区免费使用这个版本控制系统。

安定团结的大好局面在2005年就被打破了，原因是Linux社区牛人聚集，不免沾染了一些梁山好汉的江湖习气。开发Samba的Andrew试图破解BitKeeper的协议（这么干的其实也不只他一个），被BitMover公司发现了（监控工作做得不错！），于是BitMover公司怒了，要收回Linux社区的免费使用权。

Linus可以向BitMover公司道个歉，保证以后严格管教弟兄们，嗯，这是不可能的。实际情况是这样的：

Linus花了两周时间自己用C写了一个分布式版本控制系统，这就是Git！一个月之内，Linux系统的源码已经由Git管理了！牛是怎么定义的呢？大家可以体会一下。

Git迅速成为最流行的分布式版本控制系统，尤其是2008年，GitHub网站上线了，它为开源项目免费提供Git存储，无数开源项目开始迁移至GitHub，包括jQuery，PHP，Ruby等等。

历史就是这么偶然，如果不是当年BitMover公司威胁Linux社区，可能现在我们就没有免费而超级好用的Git了。

### Git的两大特点 

 

- 版本控制；可以解决多人同时开发的代码问题，也可以找回历史代码。

- 分布式；Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的及其上，只需要有一个24小时开机的服务器，其他每个人都从这台服务器仓库中克隆一份代码到自己的电脑上，并且吧各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交，常用的代码托管平台有Gitee，Github，Gitlab等。

  

## 安装与配置

Git在Linux，Windows，MacOS中均可以使用，以CentOS为例，可以用`which git`来查看Git是否已经安装，若未安装，可以使用下面的命令进行安装：

```shell
yum install git
```

可以输入

```shell
git --version
```

来查看Git当前版本。

## 创建版本库

**创建的版本库会存在于你当前的工作目录**，因此在创建之前应当进入到合适的工作目录中，创建命令为`git init`，创建所得版本库文件为`.git`文件，可以看到这个文件是隐藏的，可以通过`ls -al`命令来查看`.git`文件。

## 版本的创建和回退

### 创建和回退命令

**创建**

在这里我们以`example.c`文件为例，对其进行版本控制。文件的初始内容为：

![image-20220415201754808](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415201754808.png)

将这个版本进行提交，提交一个版本的命令为：

```shell
git add hello.c
git commit -m 'version1.0'
```

然后我们对其进行一些修改，如给它添加一行。

![image-20220415202100190](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415202100190.png)

再进行提交：

```shell
git add example.c
git commit -m 'version2.0'
```

用`git log`命令可以查看已经提交的版本信息：

![image-20220415202317057](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415202317057.png)

可以看到我们已经成功提交了两个版本。

Q: 我不想看到Author和Date这些信息，我只想看到尽可能多的版本信息，可以实现吗？

A：可以，用到的命令为：

```shell
git log --pretty=oneline
```

![image-20220416151120088](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220416151120088.png)

此时它就只显示hash值和版本信息了。

**版本的回退**

Git的工作原理并非留存每个版本的一个副本，而是每次都记录此版本和上一个版本之间哪里做出了修改，因此Git中每一个版本都依赖于它的上一个版本。

现在工作目录下的`example,c`文件是最新版本的，那我们如何将它回退到之前的版本呢。这些版本存储就像一个链表，它有一个头指针HEAD，我们只需要调整它的头指针指向，就可以读取到历史版本了。

可以用`git reset`命令来调整HEAD指针的指向，HEAD^ 表示回退一个版本，HEAD^^ 表示回退两个版本，以此类推，当然当想要一次性回退许多个版本的时候，这种方法就显得不是很好用，这时候我们可以用`HEAD~n` 表示向前回退n个版本。

```shell
git reset --hard HEAD^
```

==注意一定是大写的HEAD，小写的会报错==

![image-20220415223339884](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415223339884.png)

可以看到example.c已经回退到了第一个版本。

**回退的回退**

当我们进行版本的回退后，再执行`git log`时version2.0已经看不到了，但是若是想再回到2.0要怎么操作呢？这时需要用到`git reflog`命令，它会显示你之前进行过的操作。如图：

![image-20220415223856354](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415223856354.png)

最前面的一串字符串就代表这个版本的hash值，可以通过此哈希值再回到2.0版本。

```shell
git reset --hard hashvalue
```



![image-20220415224014393](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415224014393.png)

Q: 创建版本时的说明信息一定要不一样吗？

A: 不一定，事实上Git主要是根据hash值对各个版本进行区分的，而不是通过你自己定义的说明信息，但是一般来讲，我们应该让说明信息富有意义而不是刻意使其重复。

### 工作区和暂存区

工作区（working directory）就是我们电脑中的目录，可以在任何一个目录下`git init`来创建一个版本库（repository），是一个叫`.git`的文件夹，repository中存放了许多东西，其中最为重要的就是称为stage（或者index）的暂存区，还有git为我们自动创建的第一个分支master，以及指向master的一个指针HEAD。

![image-20220415224930103](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415224930103.png)

Q：说暂存区很重要，为什么呢

A:  因为在我们的提交过程中，暂存区是必由的过程。提交的示意图如下：

![image-20220415225135978](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415225135978.png)

Q: 那为什么不直接进行提交而是需要先存放在暂存区呢？

A: 

1. 这相当于一个保存草稿的功能，你可以对一个文件不断地用add将其放到暂存区，相当于一个临时备份版本，这样不至于因为众多的小更改使得repo中出现过多的版本。
2. 当你同时对多个文件进行了修改，每个文件分次提交显然是比较费事的，这时你可以将它们分别add到暂存区，然后一次性commit到repository中，这就非常方便了。



`git status`命令可以查看当前工作区哪些文件未被添加到暂存区，也可以查看工作区的哪些文件发生了修改但未提交。

### 撤销修改

**撤销工作区的修改**

经常会出现这样的情况，我们修改了源代码的很多部分，但是突然发现有问题且问题难以解决，于是想回到从前，但是想改回去又不太现实，毕竟你可能已经不记得改了哪里以及原来的代码长什么样子了。于是Git可以来帮上你的忙。一条简单的命令，以example.c文件来举例：

```shell
git checkout -- example.c
```

简单的一行命令就会使工作区的全部修改被丢弃，直接回到最后一次提交的版本，用`git status`来查看可以发现工作区是clean的。

**撤销缓存**

当你大意地将代码已经add到了暂存区中，这时你突然想起一些问题，想把文件从暂存区中取回来，怎么办呢。这时可以用命令：

```shell
git reset HEAD example.c
```

撤销完成后会有这样的命令提示：

![image-20220415234435323](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220415234435323.png)

而这里的unstaged就是从暂存区撤销的意思，这里的stage就指暂存区。

**撤销repo中的版本**

参见第一小节，用` git reset --hard HEAD^ `进行回退。

### 对比文件的不同

**对比工作区和HEAD所指的同名文件的不同**

```shell
git diff HEAD -- example.c
注意--和example.c中间的空格是不能省略的
```

对比HEAD所指文件和example.c的不同。若指定的文件和HEAD所指文件不同名，则什么都不会返回。

**对比repo中两个版本某个文件的不同**

### 删除文件

删除文件也被看做是工作区的改动，可以使用`git checkout -- example.c`的方式来撤销删除操作，也可以`git rm exampl.c`将删除提交到暂存区，还可以`git commit -m '删除example.c'的方式在版本库中留下删除记录。但你仍然可以通过版本回退的方式让文件重新出现在你的工作区，复习一下版本回退的命令：

```shell
git reset --hard HEAD^
```

但需要注意的是工作区的**未被跟踪**的文件若是被删除，则无法通过Git找回，所以切记重要的文件一定要先至少`git add`到暂存区哦。

## 分支管理







