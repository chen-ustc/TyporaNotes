[toc]

# Fortran学习笔记

## 什么是fortran

### fortran编译器

现在主流的编译器有GNU的gfortran，Intel的ifort,NVIDIA的PGI fortran等。

<font color='green'>PGI是意法半导体旗下的全资子公司，在高性能计算编译器技术上有悠久的历史，NVIDIA在2013年收购PGI，其核心任务也变成为NVIDIA平台进行优化服务。其支持cuda fortran的编译器PGI fortran现也已更名为nvfortran,何nvc,nvc++一起放在NVIDIA的hpc_sdk中.</font>

### 特点

- 接近自然的数学语言
- 矩阵运算功能强大,自有复数运算
- 语法严格,适合严谨的科学计算
- 可视化开发效率低,效果不理想
- 比较底层,各种算法功能都需要自己实现

### 版本

- F77
- F90
- F95
- F2003
- F2008

### 工具选择

- 集成开发环境
  - 简单易学,可视化操作
  - 工程管理
  - 不易于交流,不易于理解概念
- 命令行
  - 生涩难学,纯字符界面
  - Makefile 管理
  - 易于交流,易于理解概念

![image-20220610105517216](D:/note_img/image-20220610105517216.png)



### 参考书籍

- Fortran95程序设计
- Modern Fortran Explained

### 学习fortran需要明白的一些东西

- implicit none
- 学fortran是为了解决实际的数学,物理问题,而不是编写漂亮的界面
- 经常回顾自己以前写过的程序,并尝试重写,把自己学到的新知识运用进去
- 保存好你写过的所有程序

## 常见编译环境的操作

### debug和release

**Debug**

- 生成的文件较大
- 生成的文件运行速度较慢
- 允许调试
- 基本上没有进行优化

**release**

- 生成的文件较小
- 生成的文件运行速度较快
- 不允许调试
- 进行了合理的优化

## 常见问题的检测和排查

### 正确地认识错误

- 高级语言语法中不会给出任何的错误信息
- 错误包含编译错误,链接错误和运行时错误,它们分别由编译器,链接器和运行时库抛出

### 编译错误

- 编译错误种类最多,但也最容易解决
- syntax error是最基础的编译错误
- 编译错误只看第一个,后面无论有多少个,都可以先不管<font color='green'>,因为一个编译错误可能会引起其它的错误</font>

### 链接错误

- 链接是拼接各代码的过程,是编译器检查代码完整性的过程
- 链接错误的种类最少,设计用户代码部分容易解决,设计编译器运行时库,第三方库,混编时不易解决
- 链接错误也可以先看第一个,后面无论有多少个,可以先不管

### 运行时错误

- 运行时错误是运行时库动态检查的错误
- 运行时错误种类较少,大多数比较难解决
- 复杂的程序几乎无法完全避免运行时错误
- 在极端情况下,运行时库给出的运行时错误信息并不一定准确
- 运行时错误一旦触发,程序就中止,此时可以配合调试器动态检查原因



## Fortran的源代码格式

### 固定格式和自由格式

由于早期历史问题,fortran早期使用固定格式.但现在已经废止,现在更推荐使用自由格式

### 程序源代码的结构

```mermaid
graph LR
字符-->token-->语句-->程序单元-->模块-->程序
```

程序单元是指为了执行某一特定任务的具有一定独立性的代码区域.包含main program,subroutine,function

<font color='red'>一组相关的程序单元和一组相关联的变量,可以组成一个模块</font>



- 程序单元应该尽可能彼此独立,充分考虑重复利用率

- 程序单元间的变量,一般是互相不通的
- implicit none应该写在每一个程序单元中

### 语句类型

- 结构语句

  仅作为代码结构块的划分使用,都能够与end进行配对

- 声明语句

  在编译时,告知编译器关于变量的信息,包括声明module的use语句和定义变量的语句,<font color='red'>在一个程序单元中,声明语句必须放在所有执行语句的前面</font>

- 执行语句

### 变量的定义和声明

Fortran是强变量类型的编程语言

- 变量类型必须在编译时确定,且不允许改变
- 最好加入impilicit none,显式声明所有变量
- 整形无误差,实型有误差

**变量的定义格式:**

数据类型(属性),形容词,形容词 :: 变量名(数组外形) = 值



关于属性,实型变量可以定义其kind值,而字符型可以定义len = xxx

以下两种情况中,冒号是不能缺省的:

- 有形容词,因为双冒号表示形容词的结束
- 在定义变量的同时赋值

但其实这两者本质上是一样的,因为fortran在定义时赋值会默认赋予变量save属性,因此冒号不能缺省.

**常见的形容词及其用法:**

| 名称        | 用途                                |
| ----------- | ----------------------------------- |
| allocatable | 可以动态分配空间                    |
| dimension   | 数组形状大小                        |
| external    | 外部函数                            |
| intent      | 形参属性                            |
| intrinsic   | 允许内部函数作实参                  |
| optional    | 可选形参                            |
| parameter   | 声明常量和枚举类型                  |
| pointer     | 指针                                |
| private     | 模块私有对象                        |
| public      | 模块中的对象在模块外也可见          |
| save        | 声明静态变量                        |
| target      | 目标对象,指针只能指向具有target属性 |



## 语法

### 数据类型

数据类型本质上就是将人类熟知的字符用用某种方式编码为01组合,数据类型本质上就是一种编码方式.如常见的Unicode,utf-8,.avi,.mp4等等,本质上都可以看做是一种数据类型.

而编程语言中的数据类型相较之下更为狭义.但本质上仍然是相同的.

**fortran标准数据类型**

- 整形 `integer(kind = xx) ::`
- 实型(浮点型) `real (king = xx) ::`
- 复数型(两个real的组合) `complex(kind = xx) ::`
- 逻辑性/布尔型 `logical(kind = xx) ::`
- 字符型 `character(kind = xx, len = xx) ::`
- 派生数据类型(上述类型的组合) `type(xx) ::`

kind是区分同一种数据类型,但不同长度或精度,或编码方式的一种代号,在编译时决定.kind对不同的变量类型,表达的意思也不一样.

<font color='red'>常数也有kind值</font>

**kind值对不同数据类型的影响**

- 对integer,kind会影响整数能够表示的最大范围
- 对real和complex,kind影响实数的最大范围和最小精度
- 对character,kind表示编码方式,通常为ASCII编码
- 对logical,kind表示长度,但一般意义上而言,并没有什么影响.

对于integer和real,一般编译器默认的kind值为4

对于character类型,其kind值许多编译器只支持1,也就是ASCII编码,这也就意味着,在fortran的源代码中,字符串变量的值不能定义为汉字或其他ASCII码不支持的字符.

### 整形

对人类而言,整形是最自然的表达.==整形需要注意的是其最大范围,尤其在阶乘,指数等可能产生较大数据的计算中,必须要事先做出评估==,另外,和C一样,==整形和整形的计算结果依然是整形==.

### 字符型

字符串的长度可以固定也可以进行分配

fortran的字符串结尾没有结束符\0,==所以在字符串操作中,需要注意,有必要时需要使用trim去除尾部的空格==(<font color='blue'>这一句话并不是很理解它的意思,fortran中的字符串貌似并非以空格结尾,尾部的空格从何而来,为什么要去除尾部的空格,什么操作需要去除尾部的空格</font>)

**字符型和整形实型可以相互转化,但和C不同,fortran中的转化是通过内部文件来实现的**:

read(字符串,\*)整形或实型变量 字符串->数字

write(字符串,\*)整形或实型变量 数字->字符串

字符型允许使用子字符串对字符串进行操作,如

```fortran
character(len = 12) :: c = "my name is chenwei"
c( : 4) = "test" !testme is chenwei
```

利用字符串可以实现“批量文件处理”和“动态format”等功能

<font color='red'>和C不同,fortran中字符串常量既可以用双引号,也可以用单引号括起来,均表示一个字符串</font>

### 浮点数

==计算机的资源始终是有限的,永远也无法准确地描述无穷的实数,在数值计算中,误差总是存在的.==

浮点数包括real和complex两种数据类型

计算机中浮点数的规则有很多,如IBM标准,IEEE标准等,其中使用最多的是IEEE标准.其实现原理详见计算机组成原理之浮点数在计算机中的表示.

单精度浮点数的精度大约为6.9位,双精度大约为15.35位

==由于浮点数存在误差,因此我们应该尽量避免进行以下操作:==

1. 对浮点数进行相等判断, 而是应该进行误差的判断

   `if(abs(a-1.3) > 1.0e-4)`

2. 用浮点数作为数组的下标

3. 用浮点数作为循环变量

==在科学计算中,我们并不害怕误差,但是害怕误差的放大和积累==

如求幂,累加等.因此在数值计算中,设计合理的,健壮的算法,对科学计算是非常重要的.

### 流程控制

#### 条件判断

最常见的结构:

1. if then 结构

```fortran
if(expression) then 
...
else if(expressoin) then
...
else
...
end if
```

需要注意的是if结构中的逻辑表达式的结果必须是一个单值,而不能是数组.若要对两个数组进行比较,则需要用all或者any等函数进行转换.

另外if结构可以进行署名

```fortran
name: if(expression) then
...
else if
...
else
...
end if name
```



2. select case 结构

```fortran
select case(expression)
	case(A)
	...
	case(B)
	...
	case default
	...
end select
```

3. select type结构

自fortran 2003起,增加了select type结构,用于实现多态

#### 循环

最常见的结构:

1. do结构

```fortran
do 100, 1, -1
...
end do
```

类似于C中的for循环,100和1是起始和终止值,-1代表步长

和if类似地,do循环也可以进行署名

2. do while 结构

```fortran
do while(expression)
...
end do
```

3. 隐式do循环

一般并非用于流程控制,而是用于简化循环式的赋值语句.

4. 循环控制:cycle,exit

cycle == continue,忽略本轮循环的剩余内容,直接进入下一轮循环

exit == break,忽略循环的剩余内容,跳出此层循环

一般情况下,cycle和exit都是跳出离语句最近的一层循环,但若是需要跳出多层,则需要用到署名:

```fortran
outer: do 1, 10
	inner: do 1, 10
	if(conditionA) then
		cycle outer
	else if(conditionB) then
		exit outer
	end if
	end do inner
end do outer
```

==除前面提到的循环变量必须为整形之外,fortran还要求循环进行过程中,循环变量的值不允许被人为地修改,但实际上,即使在循环体内人为地修改循环变量,也不会影响循环的次数==

**一些不该使用的语法**

在流程控制中,还可以使用goto,computed goto,continue和行号结尾的do循环,arithmetic if等,这些语法可能会在一些老的代码中出现,能够看懂就好,自己写代码的过程中应该尽可能规避这种写法.

### 聚合数据

在学习数组的用法之前,首先英爱了解一下聚合数据的概念,常见的聚合数据有以下几类:

- 枚举(parameter)
- 数组(dimension)
- 结构体(type)
- 类(class,type derived)

#### 枚举

枚举类型的变量用parameter形容词进行定义,用法和C中的枚举类型是类似的,都是为了一些数量较少的变量所定义的类型.但是需要注意的是二者之间的区别:==C中enum定义的枚举常量有默认值,是从0开始的整数,也可以自定义,也就是说,可以不用人为地对枚举常量进行初始化,但是在fortran中不同,枚举类型必须在**定义的同时**人为地进行初始化,否则会报错==

示例代码:

```fortran
program enum
    implicit none
    integer, parameter :: sun = 0, mon = 1, tue = 2, wed = 3, thu = 4, fri = 5, sat = 6

    integer today
        
do
    write(*,*)'please input a integer number between 0 and 6:'
    read(*,*)today

    select case(today)
    case(mon : fri)
        print*,'today is workday'
    case(sun,sat)
        print*,'today is weekend'
    case default
        print*,'wrong input, program has terminated'
        stop
    end select
end do

end program enum
```

#### 数组

用dimension进行定义,可以用data进行初始化. 

**和C中数组的区别:**

- fortran中的数组下标是从1开始,而C是从0开始
- fortran中的数组默认是列优先存储的,而C是行优先
- fortran中对数组初始化需要用到data关键字,而C可以直接用赋值语句进行赋值

示例代码:

```fortran
program array
    implicit none
    
    integer i, j
    integer, dimension(2,2) :: arr

    data arr /1,2,3,4/

    do i = 1, 2
        do j = 1, 2
        write(*,*)arr(j, i)
        end do
    end do
    print*,'porgram terminated'

end program array
```

**数组的分类:**

1. 固定大小的数组

*一维数组*

- 声明:

  有两种方式,用dimension关键字或者在数组名后跟括号说明数组的大小.

  ```fortran
  real(8), dimension(5) A
  real(8) B(8)
  ```

- 初始化

  - 声明时初始化

    ```fortran
    real(8), dimension(3) :: A = (/1.0,2.0,3.0)
    real(8) :: B(3) = (/1.0,2.0,3.0)
    ```

    但是其不方便之处在于必须要给每个元素都赋值,否则会报错
    
  - 用data关键词或者赋值语句单独进行初始化
  
    ```fortran
    !************************
    !用data语句进行赋值
    !************************
    integer, dimension(8) A
    
    data A /1,2,3,4,5,6,7,8/
    data A(2 : 5) /2,3,4,5/
    
    !***********************
    !直接赋值
    !***********************
    integer A(8)
    
    A = (/1,2,3,4,5,6,7,8/)
    A(2 : 5) = (/2,3,4,5/)
    ```
  
    二者的区别在于用等号赋值必须要在/外带括号,而用data关键字进行赋值时不用
    
    ==未被赋值的元素会默认赋值为0,这点和C是一样的==
    
  - 用循环语句进行赋值
  
    - 显示循环
    - 隐式循环
  
- 数组的引用

  - 单个元素: A(i)
  - 多个元素: A(i : j)
  - 按固定步长引用元素: A(i : j : step)
  - 按整形数组引用: A(B),<font color='green'>B中元素可以重复,但若是用此方法赋值,则不能重复</font>

- 数组的输出

  - 用数组名进行输出,会输出数组中的所有元素
  - 用循环进行输出

*二维数组*

- 声明

```fortran
integer, dimension(2,2) A
integer A(2,2)
```

- 初始化

  - 声明时初始化

  ```fortran
  integer,dimension(2,2) :: A = (/1,2,3,4/)
  ```

  同样地,声明时初始化必须要对全部元素进行赋值,==赋值顺序遵从列优先的原则==

  - data关键字

  ```fortran
  integer A(2,2)
  
  data A /1,2,3,4/
  ```

  - = 赋值

  和一维数组不同,不能对二维数组直接用 = 进行赋值,要用reshape关键字才可以.

  ```fortran
  integer A(2,2)
  
  A = reshape((/1,2,3,4/),(2,2))
  ```

  两层括号,内层前半部分为要赋值的元素,后半部分为数组的形状.但同样需要对所有元素进行赋值

  - 用循环进行赋值

#### 结构体

结构体定义和使用的方式与C非常相像,只有一些小小的区别:

- 定义结构体变量时需要给结构体名加括号
- 调用结构体中元素用%而不是.
- 暂时还不知道是否能够结合指针使用,目前C中->的用法在fortran中是不存在的



示例代码:

```fortran
program struct
    implicit none
    !这个程序是用来练习结构体的语法
    type student
    integer id
    character(len = 15) name
    character(len = 6) gender
    integer age
    end type

    type(student) stu1
    stu1 % id = 001
    stu1 % name = 'chenwei'
    stu1 % gender = 'male'
    stu1 % age = 24

    write(*,*)'student info:'
    write(*,*)'name:        ',stu1 % name
    write(*,*)'gender       ',stu1 % gender
    write(*,*)'age          ',stu1 % age
    print*,'program terminated'

    stop

end program struct                       
```

#### 类

object-oriented program ,暂时不深入学习

### 函数和子例程程序

函数和子例程的区别:

| function                 | subroutine                 |
| ------------------------ | -------------------------- |
| 有返回值                 | 无返回值                   |
| 调用方式为var = 函数名() | 调用方式为:call 子程序名() |
| 可以通过形参输出数据     | 可以通过形参输出数据       |
| 允许有多个输出数据       | 允许有多个输出数据         |
| 可以包含文件操作         | 可以包含文件操作           |

==由于Fortran默认是传址的,所以可以通过形参输出数据达到返回值的作用==

**因此实际上,函数和子例程几乎是没有什么区别的**

### 实参和形参

实参和形参是调用者与子程序之间互换数据的最直接方式

调用者指定实参(actual arguments)

子程序指定形参(dummy arguments)

**使用时应该注意的一些点:**

- 严格匹配形参和实参,包括数据类型,kind值,数组的维度等.

- ==大多数情况下,实参和形参应该用不同的名字==,实参命名一般根据该参数的功能,在块的最前部指定,应为此过程中其作用体现的较为精确的命名,而形参的命名应更加普适 ,让人能够根据函数名和参数名一眼看出此函数的用处为佳.

  举例:
```fortran
  !实参
  call 结婚(郭靖,黄蓉)

  !形参
  subroutine 结婚(男方,女方)

  ...

  end subroutine
```

### 传递数组和结构体

Fortran中的数组,除所有元素之外,还包含了维度,每个维度的上下限,大小等信息.

假设有以下代码:

```fortran
real :: a(10)
real :: b(10,10)

call sub(a,b)
```

在源码层面上来看,只有两个参数,但实际编译后,传递的除了第一个元素的地址外,还可能包括前述的全部或部分信息,

**传递数组的三种方式**

- 自动数组(auto arrays)

  ```fortran
  subroutine sub(a,m,n)
  	integer m,n
  	real a(m,n)
  	a = 2
  end subroutine sub
  ```
  
  
