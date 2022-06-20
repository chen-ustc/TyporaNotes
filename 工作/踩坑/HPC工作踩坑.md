# HPC工作中遇到的问题总结

## MPI编程

### 环境配置

1. 无锡超算官网: http://nsccwx.cn/
2. VPN账号：swx86，密码：nsccwx@7
3. WIFI密码：wxcc123456
4.  

---

2022年5月6日

今天入职，更新登录服务器和办公路径

1. 服务器IP：41.0.0.188
2. 服务器账号：swsoftware
3. 服务器密码：Swrjs666!
4. 目前办公路径：/online3/chenwei

---

青岛超算节点

URL: https://144.123.46.68:4670/portal_default/index.html

VPN: 144.123.46.68:4670

VPN_user: vpn_swnsccwx
VPN_passwd: qdsys@lion1

VSN_IP: 40.0.1.12
VSN_user: swnsccwx
VSN_user: Swrjs666!

ssh swyyz@40.0.0.1

---



1. 服务器IP：41.0.0.188 服务器账号：qiny01 密码：INDQVcEF
2. 目前办公路径：/home/export/online3/qiny01/yeyj

---

每次工作之前要先装载国产cpu的编译器，命令为：

```
module load sw/compiler/gcc710
```

编译命令：

```
mpicc in.c -o out
```

参数-o表示直接生成结果文件，它可能是目标文件，也可能是可执行文件

运行时的命令不能用mpirun，而要用bsub提交到作业调度系统执行，命令为：

```
bsub -I -q q_sw_expr -n 8 -sw3run swrun-5a ./a.out
```

使用bsub提交过作业之后，以后可以使用！bsub简化提交作业的命令

-q 表示要提交到的队列名称

-n 表示要使用的进程数

-sw3run swrun-5a 是加载器命令，如果不加此命令会导致如下报错：

![image-20220408133108876](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220408133108876.png)

另外还有可选参数：

-cgsp： 指定每个进程所要使用的从核个数

-share_size: 指定单个进程所要使用的用户空间大小（以MB为单位）

---

申威的众核系统是一个主核（CPU）带着64个从核（线程），是一种有些类似于GPU的架构，MPI编程只用到了进程层级，因此从核是没有用到的。

## Fortran

- gfortran是gcc的一部分，g95是一个基于gcc完成的编译器，g77已经停止开发。
- gfortran是gnu fortran，是用于gnu/linux环境中的编译器，ifort是Intel fortran，是基于intel x86结构的编译器，
- 

## 课题归纳

### hello world

今天用MPI写了一个hello world的程序，在虚拟机上用mpirun跑通，但是在超算平台上却报错，出现了问题。源代码如下：

```c
#include<stdio.h>
#include<mpi.h>

int main(int argc,char *argv[])
{
    int rank;
    
    MPI_Init(&argc,&argv);
    MPI_Comm_rank(MPI_COMM_WORLD,&rank);
    
    printf("hello world from %d processor\n",rank);
    MPI_Finalize();
    return 0;
}
```

我个人理解：

MPI_Init调用中第一个取地址符是将结果写回到argc中，而第二个取地址符则相当于*，代表访问argv字符数组。

1. 关于main函数中的两个参数

   main函数可以调用其他函数，那么是谁调用main函数并给它传参呢，是操作系统，其中字符数组argv的第一个元素为main函数所存放的文件路径，其他的元素可以由用户自己定义进行传值。而argc便是argv数组中元素的个数。

2. 在MPI编程中，main函数的标准写法应该为：

   ```c
   int main(int argc, char *argv[])
   void main(int argc, char *argv[])
   int main(argc,argv)
   	int argc;
   	char **argv;//或者char *argv[];
   ```

3. MPI_Init的固定格式为MPI_Init(&argc,&argv);

4. 记得常用\n换行

---

平台上不能用mpirun，而要用bsub命令提交到作业调度系统中才行，但是直接按照教程所写的命令格式提交仍然会报错，问了叶老师他跟我讲是因为当前目录下的module是x86架构，用x86架构的编译器编译出来的程序，再用国产架构的作业调度系统进行提交当然会出出问题，因此用到了` module avail`这个命令查看存在哪些module，然后选择sw/compiler/gcc710进行安装，安装命令为`module load sw/compiler/gcc710`，之后可以用`which mpicc`来查看module是否装载成功，然后只需要重新编译，提交运行，但是提交之后又报了个不一样的错，现在正在排查中。

### 矩阵乘法及其并行实现

设一个m行n列的矩阵和一个n行k列的矩阵相乘

首先m*n的矩阵可以用一个一维数组来表示，具体方法为：

```
datatype *a;
a=(datatype*)malloc(sizeof(datatype)*m*n);
```

a~ij~的位置为a[i*m+j]

两矩阵相乘时，需要用一个三重循环来实现，最外层for循环在一趟中m*n矩阵的行保持不变，中层for循环在一趟中n *k矩阵的列保持不变，最后一个循环设置一个变量，遍历第一个矩阵的行，遍历第二个矩阵的列，并使之相乘。代码为：

```c
/*a矩阵为m*n,b矩阵为n*k*/
for(int i = 0; i < m; i++)
	for(int j = 0; j < k; j++)
	{
		result[i*k+j]=0;//将结果矩阵初始化
		for(int p = 0;p < n;p++)
			result[i*k+j]+=a[i*n+p]*b[p*k+j];
	}
```

附上MPI用scatter和gather完成矩阵相乘的并行程序代码：

```c
#include<stdio.h>
#include<string.h>
#include<mpi.h>
#include <malloc.h>
int main(void){
int comm_sz;
int my_rank;
int size=1000;
int *a,*b,*result,*local_a,*local_result;
int m,n,k;//a矩阵为m*n，b矩阵为n*k
int local_m;
a=(int*)malloc(sizeof(int)*size*size);
b=(int*)malloc(sizeof(int)*size*size);
result=(int*)malloc(sizeof(int)*size*size);
//由于不知道俩矩阵的大小，但最大为1000*1000，所以先将所有初始化为1000*1000

MPI_Init(NULL,NULL);
MPI_Comm_size(MPI_COMM_WORLD,&comm_sz);
MPI_Comm_rank(MPI_COMM_WORLD,&my_rank);
//0号进程用于读取矩阵，分发数据，一部分计算和聚集打印数据
if(my_rank==0){
    printf("Enter the numbers of first matrix's rows :");
    scanf("%d",&m);
    printf("Enter the numbers of first maxtrix's columns :");
    scanf("%d",&n);
    int i,j;

    for(i=0;i<m*n;i++)
    {
        printf("Enter the first matrix one by one :");
        scanf("%d",&a[i]);
    }


    printf("Enter the numbers of second maxtrix's columns :");
    scanf("%d",&k);

    for(j=0;j<k*n;j++)
    {
        printf("Enter the second matrix one by one :");
        scanf("%d",&b[j]);
    }

    //由于只有0号进程读取到m，n，k，但是其他进程计算中都需要用到，所以将它们广播到其他进程
    MPI_Bcast(&m,1,MPI_INT,0,MPI_COMM_WORLD);
    MPI_Bcast(&n,1,MPI_INT,0,MPI_COMM_WORLD);
    MPI_Bcast(&k,1,MPI_INT,0,MPI_COMM_WORLD);

    //分发数据
    local_m=m/comm_sz;
    local_a=(int *)malloc(sizeof(int)*local_m*n);    
    MPI_Scatter(a,local_m*n,MPI_INT,local_a,local_m*n,MPI_INT,0,MPI_COMM_WORLD);

    //将b矩阵广播到其他进程
    MPI_Bcast(b,n*k,MPI_INT,0,MPI_COMM_WORLD);

    //进行分得的部分计算
    local_result=(int *)malloc(sizeof(int)*local_m*k);
    int p;
    for(i=0;i<local_m;i++)
        for(j=0;j<k;j++)
        {
            local_result[i*k+j]=0;
            for(p=0;p<n;p++)
                local_result[i*k+j]+=local_a[i*n+p]*b[p*k+j];
        }
    //将各进程结果聚集到result矩阵
    MPI_Gather(local_result,local_m*k,MPI_INT,result,local_m*k,MPI_INT,0,MPI_COMM_WORLD);

    //不能整除的情况下a剩余行的处理
    int rest=m%comm_sz;
    if(rest!=0){
        for(i=m-1-rest;i<m;i++)
            for(j=0;j<k;j++)
            {
                result[i*k+j]=0;
                for(p=0;p<n;p++)
                    result[i*k+j]+=a[i*n+p]*b[p*k+j];
            }
    }
    free(a);
    free(b);
    free(local_a);
    free(local_result);

    将result中结果打印出来
    for(i=0;i<m;i++)
    {
        for(j=0;j<k;j++)
            printf("%d ",result[i*k+j]);
        printf("\n");
    }
    free(result);
}else{
    //其他进程所做的工作和0号进程的计算是一模一样的，所以可以直接复制代码
    int i,j;
    MPI_Bcast(&m,1,MPI_INT,0,MPI_COMM_WORLD);
    MPI_Bcast(&n,1,MPI_INT,0,MPI_COMM_WORLD);
    MPI_Bcast(&k,1,MPI_INT,0,MPI_COMM_WORLD);           

    local_m=m/comm_sz;
    local_a=(int *)malloc(sizeof(int)*local_m*n);    
    MPI_Scatter(a,local_m*n,MPI_INT,local_a,local_m*n,MPI_INT,0,MPI_COMM_WORLD);

    MPI_Bcast(b,n*k,MPI_INT,0,MPI_COMM_WORLD);
    local_result=(int *)malloc(sizeof(int)*local_m*k);
    int p;
    for(i=0;i<local_m;i++)
        for(j=0;j<k;j++)
        {
            local_result[i*k+j]=0;
            for(p=0;p<n;p++)
                local_result[i*k+j]+=local_a[i*n+p]*b[p*k+j];
        }
    MPI_Gather(local_result,local_m*k,MPI_INT,result,local_m*k,MPI_INT,0,MPI_COMM_WORLD);
    free(a);
    free(b);
    free(local_a);
    free(local_result);
    free(result);
}
MPI_Finalize();
return 0;
}       
```

在这个题目中需要注意一点，==矩阵的SIZE数一定要大于申请的进程数==，否则会导致a，b，local_a等动态申请的内存空间没有被使用导致无法free从而报错。

### 稀疏矩阵的乘法

**第一步 实现稀疏矩阵乘法的串行化程序**



## 错误总结

### 报错及原因总结

1. **报错内容**：syntax error: unexpected word (expecting ")")

   

![image-20220410113254166](https://raw.githubusercontent.com/chen-ustc/clouding/master/image-20220410113254166.png)

编译器有问题，当前环境是国产CPU架构，但用的是x86的编译器，因而报错。

**解决方法**：加载国产编译器，命令为：

```
module load sw/compiler/gcc710
```

2. 报错内容：line truncated

   在x86向linux进行软件移植的过程中，重新编译程序的时候会出现line truncated报错，其原因是因为一行的内容过长，而linux的编译器在编译时会将一行直接截断导致出现这个报错，这个报错还会因为行被截断的关系导致引起后面的一连串的错误，

   解决方法为在编译选项中加上`ffixed-line-length-none`或者`ffree-line-length-none`或者两个都加上

3. 情境：同时有.f90和.f文件的情况下，分别用mpif90和gfortran去编译，生成.o文件的过程中没有什么问题，但是链接的过程中ld会报错

   报错内容:Relocations in generic ELF (EM: 62)

   分析原因：可能申威中的gfortran和mpif90编译器用到的指令集不尽相同，导致生成的.o文件无法链接。解决方法：用mpif90去编译.f文件，就可以顺利链接生成可执行文件了。也就是说==即使是一个串行程序，甚至没有加mpi的头文件，仍然是可以用mpi的编译器来编译的，因为mpi编译器是gcc的超集，==

4. 报错内容：CATCHSIG: Myid = 10(CPU 1026,CG 2), si_signo = 8(Floating Point Exception: PC = 0x4ff0601700) 
   [vn001026:mpi_rank_10][MPIDI_CH3_Abort] application called MPI_Abort(MPI_COMM_WORLD, 1000) - process 10: Invalid argument (22)

   解决办法：降低优化等级，从O3到O2.

   5. 报错内容：No enough space to allocate for detax,y,z!

      可能原因：共享内存大小不够用或者程序中将变量数据写死（指过大）也可能导致这个错误

      解决办法：增加核数或者修改代码





   

   

   

   






### bug及原因总结

#### scanf读不进数据

2022年4月10日

在超算平台测试数据时发现scanf读不进去数据，但是在虚拟机上测试的时候是没有问题的，我猜想可能的原因：

1. 可能是并行程序的某些特性导致的。又写了一个串行程序，发现仍然存在无法读入的问题，因而排除这种可能性。
2. 可能是平台上输入用的不是scanf，不知道对不对，到时候去问问叶老师。
3. 

​	
