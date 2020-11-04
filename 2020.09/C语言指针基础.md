# 指针小结——C语言

> 最近发现自己C语言基础还是很薄弱，去广图借了本《C指针原理揭秘——基于底层实现机制》深入学下指针

##  命令行输入参数

> 大家想必都在windows中使用过ping 127.0.0.1 -t，其中这些参数怎么来的呢？看下面

``` C
#include <stdio.h>
#include <stdlib.h>
int main(int argc,char **argv)
{
        while(*++argv!=NULL)
                printf("%s\n",*argv);
         return 0;
}
```

我们平时直接就int main()，入口参数什么都不填，但在这里的（int argc,char **argv）代表了不一样的意思

其中argc代表的是输入的命令的个数，其中命令本身也是参数之一，那argc>=1

而argv就是一个指针，指向参数数组元素，利用argv指针特点，可判断参数数组中参数的个数，也就是说循环向后移动argv指针，当指向NULL的时候就代表参数列表结束

上个例子中使用了解引用的操作，就把所有的参数取出来。

## 常量指针

###  字符串常量指针

```C
#include <stdio.h>
int main(int argc,char **argv)
{
    printf("%s","abcdefgh"+2);
    return 0;
}
```

在这里运行结果就可以得到"cdefgh"，字符常量可以直接作为指针基址，加上偏移步长（向右跳过的字符数），可以得到余下的字符串的起始地址。

### const指针

1. 指针指向的内容不可变，但指针本身可以改变

   ```C
   const int* a;
   int const *a;
   ```

2. 指针本身不能变，指向的内容可以修改

   ```C
   int* const a;
   ```

3. 指针本身不能改变，指向的内容也不能改变

   ```C
   const int* const a;
   ```

很多时候很容易混淆，怎么记住呢？主要看const和*的位置。

const在前，*在后，就叫常量指针，顾名思义就是常量的指针，指针指向的是常量咯！

*在前，const灾后，就叫指针常量，顾名思义就是指针是常量，指针是常量咯！

### 函数指针

1. 函数指针

格式：

```C
返回类型 （*函数指针变量名）（参数列表）
```

如：

```C
int (*myfunc)(int a,int b);
```

2. 函数指针数组

格式：

```C
返回类型 （*函数指针变量名[]）（参数列表）
```

如：

```C
#include <stdio.h>
#include <stdlib.h>

int add(int a,int b)
{
        return a+b;
}

int sub(int a,int b)
{
        return a-b;
}

int main(int argc,char **argv)
{
    int (*operate_func[])(int,int)={add,sub};
        int myresult=0;
        int oper=atoi(*++argv);
        printf("%d\n",oper);
        int mynum;
        while(*++argv!=NULL)
        {
                mynum=atoi(*argv);
                printf("%d ",mynum);
                myresult=operate_func[oper](myresult,mynum);
        }
        printf("\n%d\n",myresult);
        return 0;
 }
```

第一个元素operate_func[0]指向add函数的起始地址

第二个元素operate_func[1]指向sub函数的起始地址

### 文件指针

#### 函数介绍

* fopen

  * r 只能从文件中读数据，该文件必须存在，否则打开失败

  * w 只能向文件写数据，文件不存在则创建它，存在则先删除它然后再重建一个新文件

  * a 向文件增加新数据（不删除原有数据），若文件不存在则打开失败，打开时位置指针将移动到文件末尾

  * r+ 可读/写数据，该文件必须先存在，否则打开失败

  * w+ 可读/写数据，用该模式打开一个新建文件，先向该文件写数据，然后就可以读取该文件中的数据

  * a+ 可读/写数据，原来的文件不被删去，位置指针移到文件末尾

    > 打开文件需要在使用文件方式前加上字母'b'，表示以二进制形式打开文件，如'wb'表示以二进制方式写入文件

* fclose

  > 关闭文件流，并释放文件指针和相关的缓冲区。如果是以可写方式打开文件，则该函数会将缓冲区内剩余的数据输出到磁盘文件中。
  >
  > 格式：fclose(文件指针名)；

* fgets

  > fgets函数从文件指针中读取数据，每次读取一行，读取的数据被保存在字符指针指向的字符缓冲区中，每次最多读取（缓冲区大小-1）个字符，最后一个字符是字符串的结束符"\0"，函数执行成功将返回缓冲区指针，若失败货都到文件结尾则返回NULL。
  >
  > 格式：fgets(指向字符缓冲区首地址的字符指针，字符缓冲区大小，文件指针)、

* fgetc

  > fgetc函数从文件指针指向的文件中读取一个字符，读取一个字节后，文件的位置指针（定位当前文件的内部位置）后移一个字节。fgetc返回读取到的字符，若返回EOF则表示到了文件结尾，或者出现了错误。
  >
  > 格式：fgetc(函数指针)；

* fputs

  > fputc函数向指定的文件写入一个字符串（不自动写入字符串结束符结束标记符'\0'），成功写入后，文件位置指针会自动后移，函数返回为一个非负整数，否则返回EOF。
  >
  > 格式：fputs(字符串缓冲区首地址，文件指针);

* fputc

  > fputc将字符写到文件指针所指向的文件的当前写指针的位置，当正确写入一个字符或一个字节的数据后，文件内部写指针会自动后移一个字节的位置。
  >
  > 格式：fputc(字符，文件指针);

#### 文件指针

> C语言通常用一个指针变量指向一个文件，该指针成为文件指针，通过文件指针就可以对它所指的文件进行各种操作。

格式：

```C
FILE *指针变量标识符
```

> FILE为大写，它是由系统定义的一个结构，该结构含有文件名、文件状态和文件当前位置等信息，编写C程序的时候不需要知道这个结构的细节。

#### 实例

* 获取指定文件的内容

 ```c
  1 #include <stdio.h>
  2 #include <stdlib.h>
  3 
  4 int main(int argc,char **argv)
  5 {
  6         int exit_status=EXIT_SUCCESS;
  7         while(*++argv!=NULL)
  8         {
  9                 FILE *input=fopen(*argv,"r");
 10                 if(input==NULL)
 11                 {
 12                         perror(*argv);
 13                         exit_status=EXIT_FAILURE;
 14                         continue;
 15                 }
 16                 printf("\n%s内容如下：\n",*argv);
 17                 int ch;
 18                 while((ch=fgetc(input))!=EOF)
 19                 {
 20                         printf("%c",ch);
 21                 }
 22                 if(fclose(input)!=0)
 23                 {
 24                         perror(*argv);
 25                         exit_status=EXIT_FAILURE;
 26                 }
 27         }
 28         return exit_status;
 29 }
 ```

* 读取多个文件

```c
  1 #include <stdio.h>
  2 #include <stdlib.h>
  3 
  4 int main(int argc,char **argv)
  5 {
  6         int exit_status=EXIT_SUCCESS;
  7         while(*++argv!=NULL)
  8         {
  9                 FILE *input=fopen(*argv,"r");
 10                 if(input==NULL)
 11                 {
 12                         perror(*argv);
 13                         exit_status=EXIT_FAILURE;
 14                         continue;
 15                 }
 16                 printf("\n%s内容如下：\n",*argv);
 17                 char mytext[500];
 18                 while(fgets(mytext,500,input)!=NULL)
 19                 {
 20                         printf("%s",mytext);
 21                 }
 22                 if(fclose(input)!=0)
 23                 {
 24                         perror(*argv);
 25                         exit_status=EXIT_FAILURE;
 26                 }
 27         }
 28         return exit_status;
 29 }
```

* 输入文字追加到文本文件

```c
  1 #include <stdio.h>
  2 #include <stdlib.h>
  3 #include <string.h>
  4 
  5 int main(int argc,char **argv)
  6 {
  7         int exit_status=EXIT_SUCCESS;
  8         while(*++argv!=NULL)
  9         {
 10                 FILE *output=fopen(*argv,"a");
 11                 if(output==NULL)
 12                 {
 13                         perror(*argv);
 14                         exit_status=EXIT_FAILURE;
 15                         continue;
 16                 }
 17                 char mytext[500];
 18                 int ch='\n';
 19                 while(1)
 20                 {
 21                         printf("请输入文字：");
 22                         scanf("%s",&mytext);
 23                         if(strcmp(mytext,"%end%")!=0)
 24                         {
 25                                 fputs(mytext,output);
 26                                 fputc(ch,output);
 27                         }
 28                         else
 29                                 break;
 30                 }
 31                 if(fclose(output)!=0)
 32                 {
 33                         perror(*argv);
 34                         exit_status=EXIT_FAILURE;
 35                 }
 36         }
 37         return exit_status;
 38 }
```

> 刚打完球，太累了，这个以后看着补全吧，哈哈，看完发现挺简单的txtx