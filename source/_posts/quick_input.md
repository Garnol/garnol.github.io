---
title: 「OI」读入优化
description: “一些在算法竞赛中可用的读入优化技巧”
date: 2021-06-03 16:13:00
categories:
 - 笔记
tags:
 - 算法竞赛
 - 笔记
---

## 关于本文

原文是老博客上在2018年写的，应该是老博客里最有技术含量的文章了……即使到现在来看也比较满意，而且文章内容也值得一记，故重新搬运到新博客上。

当然，可能会顺带做些措辞上的修改

## 更新摘要

本文于2018/10/28更新一次，内容如下：

- 将模板中定义的`read`更改为`readin`，因为在某些网站评测时（如COGS）使用`read`会与系统关键词重名，保险起见进行更正。
- Top 1与Top 2的一句话对比。

- 以及，经过少量的实验~~WA~~，发现如果输入要转化为长整型时，使用快读可能反而拖慢速度，但这一情况仍未经系统验证。

以下是正文

## 概览

以下各方法大致按速度由慢到快排序，

## 常规操作

常规操作是最常用的方法。虽然速度略慢，但胜在泛用性广。

### Top 4. cin

如题，即C++中iostream的cin读入，最方便，但速度也最慢，对数据量大的题目大概率T。

```c++
#include <iostream>
int main()
{
    int x;
    std::cin >>x;
    return 0;
}
```



### Top 3. scanf  ||  关闭同步的cin

一般情况下，cin无法处理的情况可以直接用scanf，速度快上不少，而且格式化读入或许会有些意想不到的好处。

```c++
#include <cstdio>
int main()
{
    int x;
    scanf("%d", &x);
    return 0;
}
```

当然，如果你还是喜欢cin的话，这里有一个STL的黑魔法来给cin提速。事实上，iostream本身的速度是可以与stdio匹敌的，然而为了兼容性，默认会开启iostream与stdio的同步，这大大减慢了iostream的速度。故，我们可以手动关闭同步，从而为iostream提速。提速后的iostream与stdio速度差不多，但注意不要与stdio混用。

```c++
#include <iostream>
int main()
{
    std::ios::sync_with_stdio(false);
    std::cin.tie(0);
    int x;
    cin >>x;
    return 0;
}
```

## 黑魔法

虽然Top 3能满足多数需求，但我们时常会遇到题目输入大量整数的情况，数据规模动不动就几百几千万。面对这样的大数据，常规的方法就难免超时了，这时，我们就需要对输入进行优化。

注意，以下方法仅针对~~只有~~整数读入，而浮点数的读入原理相似，如有需要，自行百度。

### Top 2. 普通快读

由于scanf在读入时对数据类型进行了很多无用的判断，在输入只有整数时，我们可以手动用getchar函数读入字符，然后将字符处理成整数。实现过程大致就是依次读入字符，若为数字，就将其添加在整数末尾，并用flag变量来判断正负。这就是一般快读的思路，其思路和代码都比较简单，而且比scanf快几倍，可以当作模板背下来。

但是，经过少量的实验~~重复提交~~，发现如果输入要转化为长整型时，使用快读可能反而拖慢速度，但这一情况仍未经系统验证，不保证正确性。

```c++
#include <cstdio>
inline int read()
{
    int x = 0, flag = 1;
    char c = getchar();
    while(c < '0' || c > '9')
    {
        if(c == '-')
            flag = -1;
        c = getchar();
    }
    while(c >= '0' && c <= '9')
    {
        x = x*10+c-'0';
        c = getchar();
    }
    return flag*x;
}
int main()
{
    int x;
    x = read();
    return 0;
}
```

~~拒绝压行，如有压行需要自行解决~~

### Top 1. 急速劲读

#### 代码

虽然Top 2已经很快了，但是江湖中还流传着一种比之更快的快读。

这种快读的实现不是很容易想到，我们先看代码，再逐步解释：

```c++
#include <cstdio>
inline char getc()
{
    static char buf[1<<18], *fs, *fe;
    return (fs == fe && (fe = (fs = buf)+fread(buf, 1, 1<<18, stdin)), fs == fe)? EOF: *fs++;
}
int read()
{
    int x = 0, flag = 1;
    char ch;
    ch = getc();
    while(ch<'0' || ch>'9')
    {
        if(ch == '-')
            flag = -1;
        ch = getc();
    }
    while(ch>='0' && ch<='9')
    {
        x = x*10+ch-'0';
        ch = getc();
    }
    return x*flag;
}
int main()
{
    int x;
    x = read();
    return 0;
}
```

不难发现，read函数和Top 2的读入函数几乎相同。而关键就在于多出的getc函数。这两大行乍一看很难懂，我们一步步解释。

#### 前置知识

对于一般快读来说，我们用getchar代替scanf进行读入，从而加速。然而，多次打开关闭输入流也是要耗时的，于是我们可以更进一步，减少从输入流读入的次数，从而让快读更加的~~暴力~~劲爽。可以借助缓冲区的概念，即先将数据读入一个“缓冲区”中，然后再对“缓冲区”进行操作。

所以我们要用stdio的这个函数：

```c++
size_t fread(void *buf, size_t size, size_t nmemb, FILE *stream)
```

其四个参数分别为：读入的数组、读入的每个元素的大小、元素的个数、读入的文件流。返回值为成功读取的元素的总数。

所以我们可以直接用`fread(buf, 1, 1<<18, stdin)`从标准输入中读入大量的数据，存入一个数组。这样我们后面就可以直接从数组中“读入”，而不是再次从输入流中读入。

#### 详解

getc函数压行严重，我们先将其展开：

```c++
inline char getc()
{
    static char buf[1>>18], *fs, *fe;
    if (fs == fe) 
    {
        fs = buf;
        fe = fs + fread(buf, 1, 1>>18, stdin);
        if (fs == fe) 
            return EOF;
    }
    return *fs++;
}
```


先看第一行：`static char buf[100000], *fs = buf, *fe = buf;`

static声明了静态变量，静态变量的生命周期直至程序结束，且初始化只有第一次有效，可当作全局变量。其中，buf是缓冲数组，fs、fe分别代表起始指针和末尾指针。

在第一次调用函数时，fs和fe均指向buf开始位置，于是进入if语句块中。接着对fs进行更新，接着执行`fe = fs + fread(buf, 1, 1>>18, stdin);`，即从标准输入中读入大量数据存入buf。由于fread返回的是读入元素的总数，故最终fe就指向了buf中最后一个元素的位置。

此时，经过一次读入，若仍有`fs == fe`的话，则说明没有读入任何数据，即输入已经读入完毕，故直接返回`EOF`。

最后，`return *fs++;`，这句先返回fs指向的元素，然后将fs递增，实现“读入一个字符”的效果。在之后的调用中，fs不断递增，直至fe位置。此时再调用函数时，便会进入if语句块，再进行一次读入。

综上，getc函数代替了常规快读中的getchar，从而使速度又得到了一个提升。

接下来，让我们对这个函数压压行，使之缩成开始时那鬼畜一行。

变量的声明直接跳过，看下面部分。

先看这两行：

```c++
        fs = buf;
        fe = fs + fread(buf, 1, 1>>18, stdin);
```

我们知道，赋值表达式的值就是等号右边的值。所以我们可以将这两行合并：

```c++
fe = (fs = buf)+fread(buf, 1, 1<<18, stdin)
```

于是就剩下了两个if。其中，外层的if我们可以用`? :`代替，而内层的if，由于需要先重新算fs和fe，所以我们将外层判断和上面这行放在一起。具体而言，我们可以用`&&`将判断和赋值放在一起，从而利用`&&`短路的特性实现先判断，若判断成功再赋值。

```c++
fs == fe && (fe = (fs = buf)+fread(buf, 1, 1<<18, stdin))
```

然后要加上内层的if，我们直接用逗号运算符把两块连接在一起：

```c++
(fs == fe && (fe = (fs = buf)+fread(buf, 1, 1<<18, stdin)), fs == fe)
```

最后加上`? :`

```c++
return (fs == fe && (fe = (fs = buf)+fread(buf, 1, 1<<18, stdin)), fs == fe)? EOF: *fs++;
```

至此，这种方法讲解完毕。但最后要注意的是，使用这种方法的话，**就不能再用任何stdio或iostream的读入了**，所以在使用前一定要好好权衡一下。这也是该方法的局限所在，相对而言，Top2虽然较慢，但泛用性更强，也算是各分千秋了。

### Top 0. 光速超读

是的……还有比急速劲读更快的方法，当然实际上就是急速劲读的改进，做法不难想到：直接将所有数据一次性读入，然后一次性处理，这里不再阐述。

## 最终模板

最终的模板，我选择了Top1，将其整合在一个类中，代码如下：

```c++
#include<cstdio>
class Read
{
private:
    inline char getc()
    {
        static char buf[1<<18], *fs, *fe;
        return (fs == fe && (fe = (fs = buf)+fread(buf, 1, 1<<18, stdin)), fs == fe)? EOF: *fs++;
    }
public:
    inline int operator()()
    {
        int x = 0, flag = 1;
        char c;
        c = getc();
        while(c<'0' || c>'9')
        {
            if(c == '-')
                flag = -1;
            c = getc();
        }
        while(c>='0' && c<='9')
        {
            x = x*10+c-'0';
            c = getc();
        }
        return x*flag;
    }
}readin;
```

用法类似`x = readin()`

## 后记

用了两个中午，终于搞定了。中间也查了很多资料，算是第一篇认认真真写的笔记吧。感觉自己对各方面的理解深刻了不少~~（因为本来就没有多少理解）~~。

希望这篇文章能帮助到你

以上