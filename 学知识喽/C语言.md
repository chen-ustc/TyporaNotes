# C语言

## 不熟悉的知识点

### 开发环境

**编译器**

gcc是linux操作系统下的C语言编译器，MinGW是编写gcc的公司制作的windows版gcc，DevC就自带MinGW编译器。vc和visual studio有其自己的编译器。

**集成开发环境-IDE**

Integrated Development Environment

包括代码编辑器，编译器，调试器和图形用户界面工具等，集成了代码编写功能，分析功能，编译功能，调试功能等的一体化开发软件套。

**linux环境下c语言程序的编译**

常用命令：

```shell
gcc hello.c -o hello
gcc -o hello hello.c
```

这两种操作是一致的，参数 -o表示生成可执行文件

**运行c程序**

运行时只需要写出可执行文件的文件路径即可

```shell
./hello
```



### scanf坑好多

- scanf本质上是一种模式匹配函数，程序员必须检查转换说明的数量是否与输入变量的数量相匹配。并检查每个转换是否符合相对应的变量，和printf一样，编译器无法监察处可能的匹配不当。

- 大坑之&，在输入单个元素的时候都要用&，包括数组元素。如：

  ```c
  scanf("%d",&a[i]);
  scanf（"%d",a+i);
  scanf("%lf",&t);
  ```

  可以这样理解：&（取地址符）用于创建一个指向变量的指针，在数组的规定中，数组名a可以看做一个指针，因而a+i是合法的，但是数组元素a[i]只是一个变量而并非指针，因此必须要添加&。

  另外，比较常见的不加取地址符的用法是用在输入字符串时，如：

  ```c
  char a[10];
  scanf("%s",a);
  ```

  这种情况是不需要加取地址符的。

- 大坑之\n，printf之后一般是需要加\n的，但是在scanf语句后加\n通常不是一个好的主意，若是想要在scanf之后换行，可以另起一行，加上`printf("\n")`。



### malloc的用法

malloc的作用是申请一块连续的内存空间，函数原型为void *malloc（int size），是指申请空间大小为size个字节的内存，返回的是一个void类型的指针，void类型指针可以强制转换为其他各种类型的指针。malloc标准库函数包含在头文件stdlib.h中。



malloc的初始化方式为：

```c
#include<stdio.h>
#include<stdlib.h>

//略去其他操作
int *p=(int *)malloc(sizeof(int)*n);
for(int i=0;i<n-1;i++)
{
	scanf("%d",&p[i]);
}
//略去其他操作
free(p);
```

对malloc申请的字符串数组应该用strcpy的方式进行赋值。

### 怎样计算程序的运行时间

c中的计时函数是`clock()`与其相关的数据类型是`clock_t`，clock()函数包含在time.h头文件中，其返回的数值单位为毫秒。使用方式：

```c
#include<time.h>

clock_t start,finish;
start=clock();
/* do some works */
finish=clock();

printf("total time is %lf",(double)(finish-start)/CLOCKS_PER_SEC);
/*单位为秒，其中CLOCKS_PER_SEC是time.h中定义的常数，其数值表示一秒钟是多少个CPU的时钟周期，若不除以CLOCKS_PER_SEC，则显示的数值为消耗的时钟周期数。*/;
```

### 为什么scanf double时一定要用%lf，而printf double时%lf和%f都可以

double占8个字节，float占4个字节

1. **能不能用%f来读double数**

   很明显不能，因为double占得字节数更多，将double数放在float空间里，势必会造成数据的丢失

2. 那既然double比float大，**那能不能用%lf来读float呢**

   也不能，因为浮点型数据和整形数据的存储方式存在根本性的不同，float和double有其自己专门的数据排列格式，如下：

   ![img](https://pic4.zhimg.com/v2-d6941e770df391cbd86939e3080619df_b.png)

   因此如果用%lf来读float，符号位不会出错，但是其接码，尾数，都会出现问题，即存储空间足够，但会导致读取数据时因格式问题读到错误的数据。

**综上而言，scanf中double只能用%lf来读，float也只能用%f来读，==不能==出现混用的现象**

那为什么printf中输出double时可以用%f呢？

因为在printf中，无论输出float型或者double型，都会隐式地将其转换为double类型进行输出，因此可以用%f。

但无论如何，**最好的做法都是无论输入输出，都是double类型用%lf，float类型用%f，既清晰又能够避免混淆**

### 结构体

#### 结构体的声明

结构体的声明方式为：

```c
struct{
	int number;
	char name;
	int age;
}stu1,stu2;
//注意最后面的分号一定不能丢
```

这是最朴素的声明方式，但是这种方式有一定的局限性，那就是不能随处添加结构体变量，只能在声明出添加。对此，有两种解决办法：

其一，结构标记法：

```c
struct student{
	int number;
	char name;
	int age;
};
//这里的student称为结构标记（structure tag）

struct student stu1,stu2;
//在用结构标记来声明变量时一定不能漏掉前面的struct

student stu1,stu2;  /***WRONG***/
```

其二，typedef定义法：

```c
typedef struct{
	int number;
	char name;
	int age;
}Student;

Student stu1,stu2;
//用typedef定义的类型名可以像内置类型一样使用，因此这样是合法的。
```

#### 结构体变量的初始化

方法一，声明时赋值：

和数组一样，结构体变量也可以在声明的同时进行初始化，初始化时要注意==要赋的值必须框在花括号内==且==必须按照声明的顺序进行赋值==。如：

```c
struct{
	int number;
	char name[10];
	int age;
}stu1={1,"jhon",20},
 stu2={2,"peter",24},
 stu3={,,,};
//C99中，初始化式的成员数可以少于结构体中的变量数，和数组一样，任何未被赋值的成员都用0作为它的初始值，特别的，对于字符数组，会用空字符串进行赋值。
```

方法二，用 . 操作符进行赋值：

```c
struct{
	int number;
	char name[10];
	int age;
}stu1,stu2;

scanf("%d %s %d",&stu1.number,&stu1.name,&stu1.age);

```

#### 结构体变量的常见操作

其一： . 操作符

常用于标准化输入输出，取值等操作。

其二： 赋值操作

如上述定义的stu1和stu2，他们可以进行`stu1=stu2`操作，这种操作的含义为：

```c
stu1.number=stu2.number;
stu1.name=stu2.name;
stu1.age=stu2.age;
```

但要注意这种赋值操作只能在两个==兼容==的结构体变量之间进行，即它们是由同一个结构体定义的。

另外需要强调的是，==除了两个兼容结构体的赋值运算，C语言没有提供其他用于整个结构体的任何操作==，特别是**不能**用==和!=来判定两个结构体是否相等。

#### 结构体作为参数和返回值

结构体作为参数和返回值是可行的，但是给函数传递结构体和函数返回结构体都要求生成结构体中所有成员的副本，这些操作对程序强加了一定数量的系统开销，特别是结构体很大的时候，为了避免这类系统开销，有时用传递指向结构体的指针来代替传递结构体本身是很明智的做法，同样，函数也可以返回指向结构体的指针而非结构体本身。

#### 嵌套的数组和结构体

##### 结构体的嵌套

结构体的嵌套就是在结构体中嵌套使用另一个结构体，这种使用方法是非常常见的。如存储一个人的个人信息：

```c
struct person_name{
	char last_name[2];
	char first_name[3];
};

sturct person{
	struct person_name name;
	int age;
	char sex;
}stu1,stu2;

/***对于结构体嵌套结构体的情况，要访问name这个成员就需要用两次.操作符***/
stu1.name.first_name='明'；
```

这样做的好处在于如果你要给函数传参，使其显示名字，这样就只需要传递一个参数而非三个。

##### 结构体和数组的互相嵌套

结构体和数组互相嵌套是没有限制的，数组可以以结构体作为元素，结构体的内部也可以包含数组作为成员。两种都很常见，结构体中包含数组的例子前面已经写过很多，只需要再补充一下访问结构体中数组的特定元素的语句为`char tmp=person_name.first_name[2]`，而结构数组则常用作简单的数据库：

```c
struct student{
	int age;
	char sex;
	char name[10];
};

struct student array[100];
/*这里的array就是一个结构数组*/

/*访问某个学生的年龄*/
int tmp=array[20].age;
```

**结构数组的初始化**

方法一：

和二维数组类似的，可以直接用花括号的形式进行赋值：

```c
struct student array[100]=
{
{10,'男',"小明"},
{12,'女',"小红"},
{14,'男',"小刚"}
};
//其余未赋值的元素会置0
```

缺点在于不灵活，只能按照从头到尾的顺序进行赋值。为解决这个弊端，可以用 . 操作符进行初始化：

```c
struct student array[100]=
{
[23].age=12,[23].name[0]='杨',[23].sex='女'
}；
```

优点在于灵活，可以选择特定的元素进行赋值，且不必非要按照声明中的成员顺序进行赋值。

### memset为什么只能赋值为0或-1

memset的用法：可以对连续的内存空间进行批量的赋值，其语法为：

```c
memset(a,0,sizeof(int)*n);
//其中a为首地址
```

但为什么memset只能赋值为0或者-1呢，这是因为memset是以字节为单位来完成赋值的，也就是说，对于上面代码框内的操作，int占4个字节，若我们将值设为1，则其赋给数组a的值为0x01010101，并不等于1，只有当其赋值为0或者1时，0的二进制为全0，-1的二进制为全1，这种赋值方法不会导致赋值出错。

### 链表

动态存储分配malloc对于建立线性表，树，图等链式数据结构是非常有用的，链表是由一连串的结构体（称为结点）组成的。

#### 声明结点类型

```c
struct node{
	int value; //data stored in the node
	struct node *next;//pointer to the next node
}
```

需要注意的是，在链表声明结点类型的过程中，==必须使用结构标记而不能使用typedef的方式来实现==这是因为这里的声明出现了递归，而typdef定义的变量类型在递归调用之后，这会导致next的类型出现错误从而声明失败。

#### 创建头结点

```c
struct node *head = NULL;
```

要注意head只是一个指向结构体的指针，不管是用于寻找链表的head还是用于辅助插入结点的new_node，二者都只是一个指针，而非结构体变量，是不存在数据域的。

**`new_node->data`就相当于`(*new_node).data`包含了间接寻址找到new_node所指结构体和选择结构体成员data两个步骤，需要注意的是如果要使用第二种方法，必须要加括号，因为运算符.的优先级要高于运算符***

> NULL不是C语言基本类型，其定义在stddef.h文件中，作为最基本的语言依赖宏存在。但是随着C/C++的发展，很多文件只要涉及了系统或者标准操作都会将NULL作为标准宏声明或者包含。所以几乎包含任意的C++标准头文件都会把这个宏引入。实际测试发现只要引入了`#include<stdio.h>`就可以使用NULL了。

#### 创建结点

创建结点包含以下三个步骤：

1. 为结点分配内存单元
2. 把数据存储到结点中
3. 把结点插入到链表中

```c
struct node *new_node;
new_node = (struct node *)malloc(sizeof(struct node));
new_node->data = 1;
new_node->next = head;//head = NULL
head = new_node;
//大致思路为用辅助指针new_node来创建新的结构体，然后让该结构体的指针域指向原来头结点指向的位置，再让head = new_node,即头结点也指向新创建的结点
new_node = (stmalloc(sizeof(struct node));
new_node->data = 2;
new_node->next = head;
head = new_node;
//用头插法插入两个结点
```

我们可以将头插法封装成一个函数：

```c
struct node *add_to_list(struct node *head,int n)
{
	struct node *new_node;
	
	if(new_node == NULL)
	{
		printf("ERROR: malloc failed in add_to_list");
		exit(EXIT_FALIURE);
	}
	else
	{
		new_node->data = n;
		new_node->next = head;
		return new_node;
	}
}

调用：
head = add_to_list(head,10);
```

那有没有办法可以直接在函数中修改head，而不是在函数调用过程中实现呢。像其他所有参数一样，指针也是按值传递的，因此在函数中操作的实际上是head的一个副本，因此这样即使修改了head也不会在函数体外起作用。故而我们可以用指向head的指针作为参数从而实现函数体内对head的操作：

```c
void add_to_list(struct node **head,int n)
{
	struct node *new_node;
	new_node =(struct node *)malloc(sizeof(srtuct node));
	if(new_node == NULL)
	{
		printf("ERROR:malloc failed in add_to_list\n");
		exit(EXIT_FALIURE);
	}
	else
	{
		new_node->data = n;
		new_node->next = *head;
		*head = new_node;
	}
}

调用：
add_to_list(&head,10);
//注意这里要用取地址符来实现指向head的指针
```

#### 搜索链表

搜索链表的常用语法：

```c
struct node *p;
for(p=head; p != NULL; p=p->next)
/* do some works */
```

我们也可以对搜索操作进行封装：

```c
struct node *search_list(struct node *head,n)
{
    struct node *p;
    for(p=head; p!=NULL; p=p->next)
    {
        if(p->value == n)
            return p;
    }
    return NULL;
}
```

#### 删除结点

删除结点也包含三个步骤：

1. 定位要删除的结点
2. 改变前一个结点的指针域，使其绕过要删除的结点
3. 调用free回收删除结点所占用的内存空间

这里面存在一些问题，比如如果用一个指针来定位要删除的结点位置的话，在第二步就会发现没有办法访问前一个结点从而导致删除操作无法进行，因此我们可以采用双指针的方法，用cur和prev两个指针来定位要删除的结点和它前面的那个结点。

同样地我们可以对此操作进行封装：

```c
struct node *delete_list_node(struct node **head, int n)
//用void也行吧，我感觉
{
	struct node *cur,prev;
	for(cur = head,prev = NULL; cur != NULL&cur->value != n; prev = cur, cur = cur->next);
    //一个for循环就可以找到要删除的结点的位置
    if(cur == NULL)//没有找到n
        return head;
    if(prev == NULL)//n在第一个结点中
        *head = （*head）->next;
    //要注意这里的head是指向指针的指针，他所指向的*head是个指针而不是结构体，因此要用*head
    //另外->和.的优先级都要比*高，因此一定要用括号把*head括起来
    else
        prev->next=cur->next;//n在其他结点中
    free(cur);
    return head;
			
}

调用：
delete_list_node(&head,10);
```

### 文件（做完稀疏矩阵之后写）

### 伪随机数生成函数

C语言提供了rand和srand两个函数用于生成伪随机数，其中srand为rand服务。重点学习rand。

**rand,srand函数原型**

```c
void srand(unsigned int seed)
int rand(void)
```

rand函数包含在stdlib.h头文件中，用于生成一个随机的位于**0~RAND_MAX（定义在<stdlib.h>中的宏）**区间内的 **非负整数**，但事实上，rand函数返回的数并不是随机的，这些数是由seed值通过数学运算产生的，rand默认的seed值为1，srand可以修改rand的seed值。有如下两种情况：

```c
#include<stdlib.h>

int x = rand();
```

srand未被调用或者在rand之后被调用，则rand的seed值为1.

``` c
#include<stdlib.h>

srand(2);
int x = rand();
```

srand在rand之前被调用，则rand的seed值为srand所定义的值。

Q:那么是不是无法产生真正的随机数呢？

A:是的，但是我们可以想办法让它的随机性更大，比如给出一种可以在srand函数中使seed随机化。最常用的方法是调用time函数，使每次的seed都不一样。

```c
#include<stdlib.h>
#include<time.h>

for(int i = 0; i<10; i++)
{
	srand(time(0));
	printf("%d",rand());
}
```

**rand生成指定区间内的随机整数**

```c
rand()%(b-a)+a
//生成[a,b)区间内的非负整数
rand()%(b-a+1)+a
//生成[a,b]区间内的非负整数
rand()%m
//生成[0,m)区间内的非负整数
rand()%(m+1)
//生成[0,m]区间内的非负整数
```

**rand生成制定区间内的随机浮点数**

```c
x + (float)(rand() % RAND_MAX) / RAND_MAX *(y - x)
//随机生成[x,y)之间的浮点数
x + (float)rand() / RAND_MAX * (y - x)
//随机生成[x,y]之间的浮点数
```

以下只是我自己个人的一点想法：

rand只能生成非负整数或者浮点数吗，我想到了一种办法,就以[x,y)区间内的随机正负数为例

```c
(x + rand()%(y - x)) - (x + y) / 2 
```

这样结果中就会同时包含正数和负数了，但是就上例而言，显然负数全都是在区间的左半边数的相反数，而正数都在右半边，因此非常地“不随机”，但可以通过修改中间的游标（代码段中的2的值）来修改 正负数的分布区间。暂时就想到这么多，若是以后有了更好的想法再来补充。

### 指针和引用的区别

**什么是指针**

在C语言中，指针全称为指针变量，是用来存储内存地址的一种变量。在程序中，一般通过指针来访问其指向的内存地址中的内容。

指针的使用语法为：

```c
int x = 10, y = 10;
int *ptr;
ptr = &x;
ptr = &y;//legal,指针可以更改指向
```



**什么是引用**

引用是C++提出的一种新的使用变量的方式，实际上就是给变量起一个别名，通过这个别名来引用实际的变量。

引用的使用语法为：

```c++
int x = 10, y = 10;
int &q = x;
y = 100;
//此时x的值也会变成100
&q = y;//illeagal,引用不能更改成其他变量的别名
```

标准C不支持变量的引用。

指针和引用这两个概念没有很多的相关性。指针是实实在在的变量，有自己的内存空间，可以指向任何有效的变量。引用是一种形式和方法，定义的引用变量，实际上是原实际变量的一个别名。引用变量本身没有自己的实际存储空间，操作引用变量，就是在操作实际变量。

**指针的引用的比较总结**

1. 指针是一个实体，引用是一个别名。但引用的底层是以指针的形式实现的，可以理解为一个不可更改指向的指针。

2. 指针可以不初始化，通过赋值可以指向任意同类型的内存。但是引用必须初始化，而且引用一旦引用为某一块内存空间，就再也不能引用其它内存了。

3. 在进行sizeof操作时，sizeof（ptr）在32位系统下永远是4个字节，而sizeof引用计算的是它所引用内存块的大小。

4. 引用是变量或者内存单元的别名，而不能作为常量的别名。

   ```c
   int &a = 10;//error
   ```

   ### C程序的内存模型

   当一个C程序在运行过程中，其内存分为三个部分：
   
   1. 全局变量
   
   2. `malloc()`在堆区分配的对象
   
   3. 栈区`stack`
   
      栈区由一个个小的栈帧(stack frame)组成，每个栈帧中有一个程序计数器PC(program counter)和局部变量。
   
   而全局变量和malloc所分配的对象都是共享的。
   
   

## 常见函数或宏定义所在的的头文件

### stdlib

- rand,srand函数和RAND_MAX宏定义
- exit（n)，EXIT_SUCCESS和EXIT_FAILURE宏
- system函数
- bsearch函数和qsort函数
- 



## 报错集锦

[Warnig] `integer overflow in expression` [-Woverflow]

**报错原因**：

整数溢出，比如下面的情况：

```c
#define SIZE 163840

MPI_Scatter（sendbuf，SIZE*SIZE,MPI_DOUBLE,recvbuf,SIZE*SIZE,MPI_DOUBLE,source,comm）；
```

或者这种情况：

```c
long long c=163840*163840;

/***
这里即使相乘的结果是赋给一个long long类型的数，但仍然会出现未定义的行为
***/
```



在上面的例子中，SIZE会被默认定义为一个int型常量。对两个int型的常量进行算数运算时，结果必须仍然能用int类型来表示，但$ 163840 \times 163840 = 26843545600 $,超出了int类型的最大值2147483647，而因此上面的程序执行时就会报错，错误信号为integer overflow in expression;

**整数溢出的行为**

整数溢出的行为要根据操作数是有符号型还是无符号型来确定，有符号整数在溢出时，程序的行为是未定义的，无符号数在溢出时，其结果是有定义的，其值为正确答案对2^n^取模。

如上述的第二种情况，结果如图：

![image-20220411160948978](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220411160948978.png)

其结果是未定义的。

**解决办法**

用后缀对常量的数据类型进行转换，例如LL表示转换为long long类型，ULL表示转换为unsigned long long 类型，不区分大小写。且根据C的运算法则，只需要改变一个常量的类型，另一个会自动进行向上转化。举例：

![image-20220411161225549](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220411161225549.png)

```c
#define SIZE 163840LL
```

