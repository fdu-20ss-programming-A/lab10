# LAB 10

> 本次LAB目标：
>
> 1. 回顾递归作业
> 2. 回顾文件IO
> 3. Project引导

## 获取及提交Lab

**获取**：通过 `https://github.com/fdu-20ss-programming-A/lab10或者`超星平台`获取。

**提交**：超星平台上已经发布了LAB10作业，同学需要将每题的**代码**、**运行结果**、个人对题目的思考体会（可选）以**图片**或者**文档**（ word 或者 pdf ）的形式在超星LAB10对应的作业区域提交即可。

**提交物**：代码、运行结果，每题可整理为一份 word 或者 pdf 文档，也可以将两者截在一张图片上提交。

**截止时间**：2020年12月15日 23:59:59



## LAB 9 作业

第一题希望大家完成一个**泰波那契数**（Tribonacci）计算程序。和斐波那契数相似，泰波那契数也与前几项有关。泰波那契数的定义如下：

> T0 = 0,T1 = 1, T2 = 1
> 且在 n >= 0 的条件下
> T{n+3} = Tn + T{n+1} + T{n+2}

例如，T3=T0+T1+T2=2，T4=T1+T2+T3=4。

编写**递归**程序计算T1，T5，T10，T20，T30，T40。学有余力的同学可以尝试计算T80。

**运行结果**

>Tribonacci of 1:1
>Tribonacci of 5:7
>Tribonacci of 10:149
>Tribonacci of 20:66012
>Tribonacci of 30:29249425
>Tribonacci of 40:12960201916
>Tribonacci of 80:499562128250021937152

**参考答案**

递归版本：

```c
#include <stdio.h>

double tribonacci(int val){
    if(val == 0){
        return 0;
    }
    if(val <= 2){
    	return 1;
    }
    return tribonacci(val-1)+tribonacci(val-2)+tribonacci(val-3);
}

int main(){
    int array[] = {1,5,10,20,30,40,80};
    for(int i = 0; i < sizeof(array)/sizeof(int); i++){
        printf("Tribonacci of %d:%.0lf\n",array[i],tribonacci(array[i]));
    }
    return 0;
}
```

使用缓冲实现：(储存已经计算过的结果避免重复计算)

```c
#include <stdio.h>

double buffer[100] = {0};

double tribonacci(int val){
    if(val == 0){
        return 0;
    }
    if(buffer[val] != 0){
        return buffer[val];
    }
    buffer[val] = tribonacci(val-1)+tribonacci(val-2)+tribonacci(val-3);
    return buffer[val];
}

int main(){
    int array[] = {1,5,10,20,30,40,80};
    buffer[0] = 0;
    buffer[1] = 1;
    buffer[2] = 1;
    for(int i = 0; i < sizeof(array)/sizeof(int); i++){
        printf("Tribonacci of %d:%.0lf\n",array[i],tribonacci(array[i]));
    }
    return 0;
}
```

实现大数运算：

```c
#include <stdio.h>
/* 
Big number: 
last bit represents valid position.
*/
#define BIGNUM_LENGTH 101

int buffer[100][BIGNUM_LENGTH] = {0};

/* add t2 to t1 */
void add(int t1[BIGNUM_LENGTH], int t2[BIGNUM_LENGTH]){
    int c = 0,temp = 0;
    for(int i = 0; i < BIGNUM_LENGTH-2; i++){
        temp = t1[i];
        t1[i] = (temp+t2[i]+c)%10;
        c = (temp+t2[i]+c)/10;
    }
}

void tribonacci(int val){
    if(buffer[val][100] != 0){
        return;
    }
    tribonacci(val-1);
    add(buffer[val],buffer[val-1]);
    add(buffer[val],buffer[val-2]);
    add(buffer[val],buffer[val-3]);
    buffer[val][BIGNUM_LENGTH-1] = 1;
}

void printBigNumber(int num[BIGNUM_LENGTH]){
    int i = 99;
    if(num[100] == 0){
        printf("0\n");
        return;
    }
    int flag = 1;
    while(i >= 0){
        if(num[i] && flag){
            flag = 0;
        }
        if(!flag){
            printf("%d",num[i]);
        }
        i--;
    }
    if(flag){
        printf("0");
    }
    printf("\n");
}

int main(){
    int array[] = {0,1,5,10,20,30,40,80};
    buffer[0][0] = 0;
    buffer[0][BIGNUM_LENGTH-1] = 1;
    buffer[1][0] = 1;
    buffer[1][BIGNUM_LENGTH-1] = 1;
    buffer[2][0] = 1;
    buffer[2][BIGNUM_LENGTH-1] = 1;
    for(int i = 0; i < sizeof(array)/sizeof(int); i++){
        tribonacci(array[i]);
        printBigNumber(buffer[array[i]]);
    }
    return 0;
}
```



## 回顾文件读写操作

> 本节我们将介绍 C 程序员如何创建、打开、关闭文本文件或二进制文件。

### 打开文件

可以使用 **fopen( )** 函数来创建一个新的文件或者打开一个已有的文件，这个调用会初始化类型 **FILE** 的一个对象，类型 **FILE** 包含了所有用来控制流的必要的信息。下面是这个函数调用的原型：

```c
FILE *fopen( const char * filename, const char * mode );
```

在这里，**filename** 是字符串，用来命名文件，访问模式 **mode** 的值可以是下列值中的一个：

| 模式 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| r    | 打开一个已有的文本文件，允许读取文件。                       |
| w    | 打开一个文本文件，允许写入文件。如果文件不存在，则会创建一个新文件。在这里，您的程序会从文件的开头写入内容。如果文件存在，则该会被截断为零长度，重新写入。 |
| a    | 打开一个文本文件，以追加模式写入文件。如果文件不存在，则会创建一个新文件。在这里，您的程序会在已有的文件内容中追加内容。 |
| r+   | 打开一个文本文件，允许读写文件。                             |
| w+   | 打开一个文本文件，允许读写文件。如果文件已存在，则文件会被截断为零长度，如果文件不存在，则会创建一个新文件。 |
| a+   | 打开一个文本文件，允许读写文件。如果文件不存在，则会创建一个新文件。读取会从文件的开头开始，写入则只能是追加模式。 |

### 关闭文件

为了关闭文件，请使用 **fclose()** 函数。函数的原型如下：

```c
int fclose( FILE *fp );
```

如果成功关闭文件，**fclose()** 函数返回零，如果关闭文件时发生错误，函数返回 **EOF**。这个函数实际上，会清空缓冲区中的数据，关闭文件，并释放用于该文件的所有内存。EOF 是一个定义在头文件 **stdio.h** 中的常量。

### 写入文件

除了函数**fputc()**和**fputs()**之外，可以使用 **fprintf()** 函数把一个字符串写入到文件中。函数的原型如下：

```c
int fprintf( FILE *fp,const char *format, ... ) 
```

如果成功，则返回写入的字符总数，否则返回一个负数。

示例程序如下，

```c
#include <stdio.h>
 
int main()
{
   FILE *fp = NULL;
 
   fp = fopen("test.txt", "w+");
   fprintf(fp, "This is testing for fprintf...\n");
   fprintf(fp, "%s %s %s %d", "We", "are", "in", 2020);
   fclose(fp);
}
```

当上面的代码被编译和执行时，它会在工作目录中创建一个新的文件 **test.txt**，并写入两个字符串

### 读取文件

除了函数**fgetc()**和**fgets()**之外，还可以使用 **fscanf()** 函数从文件中读取字符串。函数的原型如下：

```c
int fscanf( FILE *fp, const char *format, ... ) 
```

如果成功，该函数返回成功匹配和赋值的个数。如果到达文件末尾或发生读错误，则返回 EOF。

示例程序如下，

```c
#include <stdio.h>
 
int main()
{
   FILE *fp = NULL;
   char buff[255];
 
   fp = fopen("test.txt", "r");
   fscanf(fp, "%s", buff);
   printf("1: %s\n", buff );
    
   fclose(fp);
}
```

当上面的代码被编译和执行时，它会读取上一部分创建的文件，产生下列结果：

```
1: This
```



## 上机作业1

问题

> 分数可以表示为“分子/分母”的形式。编写一个程序，要求用户输入一个分数，然后将其约分为最简分式。
>
> 最简分式是指分子和分母不具有可以约分的成分了。如6/12可以被约分为1/2。当分子大于分母时，不需要表达为整数又分数的形式，即11/8还是11/8；而当分子分母相等时，仍然表达为1/1的分数形式。
>
> 该程序中包含两个函数void reduce(int *a, int *b)，int * reduce(int a,int b)
>
> 一个传入的参数是两个指针，直接修改指针的值作为返回值，另一个传入的参数是两个值，返回的是包含两个值的一维数组
>
> 第二个函数直接声明int result[2]，然后返回这个result不会得到正确结果，可以
>
> 1. 在函数内声明static int result[2]
> 2. 声明result[2]为全局变量
> 3. int * result; result = (int *)malloc(2);
>
> 原因可见于 https://blog.csdn.net/gavechan/article/details/45542913

限制

> 输入都是正整数

示例

> 120/30=4/1
> 2/3=2/3
> 3/0=NaN



## Project引导

LAB8大家完成了一个支持推箱子的2D小游戏，地图方面用了直接写在代码中的静态的二维数组代替，但最终项目要求将地图存储在.map文件中（将.map文件看作txt处理就好），从不同的.map文件中读取其中信息，再构建出相应的地图二维数组，这就涉及到了文件读写操作。

整个项目中需要进行文件读写的功能点就是加载地图和存/读档，这两个功能的内在逻辑其实是类似的。

对于加载地图，事先在文件中存入地图信息，格式如下所示，

```
8 8 4
00011100
00014100
11113100
14235111
11132341
00121111
00141000
00111000
```

首行表示地图的行数与列数以及箱子的数量。地图文件中，0 代表墙外的空地，1 代表墙，2 代表墙内的空地，3 代表箱子，4 代表箱子要推去的目标，5代表玩家。根据首行得知地图数组的大小，以及箱子数组的大小，再依次将数字矩阵读入到数组中即可。

类似地，当玩家需要存档时，将当前游戏的状态记录到指定文件中。

- 可以只实现一个存档，此时只需将每一次的存档都放在同一个文件，每一次存档写入文件前清除掉文件已有内容，即清除掉上一次存档
- 也可以实现多个存档如save1.map，save2.map....，每一次存档可指定存入哪一个文件，游戏初始读档时可指定读入哪一场游戏

存档文件的内容，可从方便实现的角度设计（如要写入的内容方便获取，方便下一次初始化重建地图，重新取得玩家、箱子坐标等）

- 可以模仿地图文件，首行表示地图的行数与列数以及箱子的数量。0 代表墙外的空地，1 代表墙，2 代表墙内的空地，3 代表箱子，4 代表箱子要推去的目标，5代表玩家，6 代表目标已经有箱子放在上面, 7 代表玩家在目标上面。即玩家在哪、箱子是否已在目标上这样的信息直接蕴含在数字矩阵中
- 也可以按第一种思路输出后，再在文件末尾添加玩家、箱子在地图上的坐标等信息



## 上机作业2

从1.map文件中读出信息，构建二维数组后将数组打印出来，然后再将数组写入到文件save1.map中

1.map文件由同学自行手动创建，内容如下：

```
8 8
00011100
00014100
11113100
14235111
11132341
00121111
00141000
00111000
```

save1.map文件格式如下：

```
地图的行数 地图的列数 箱子数
地图矩阵
......
地图矩阵
```

本次LAB需要提交**完整代码**以及**核心功能实现的截图**（本次为打印出的数组的截图，以及save1.map文件的截图）。（已经完成Project的同学在提交的作业中说明，并提交对应功能的截图即可）

