# C 介绍

**c 的标准库,包含数百个函数,用于输入输出,字符串处理,存储分配以及其他一些操作**

Lint 代码检查

# C 基本概念

预处理指令,函数,变量,语句

```c
#include <stdio.h>

int main()
{
    printf("starting");
    return 0;
}
```

## 编译链接

C 程序转化为可执行程序需要 3 个步骤:

1. 预处理 预处理器执行#开头的指令.它类似于编辑器,给程序添加内容,也可以对程序进行修改
2. 编译 修改后的程序进入编译器,编译器把程序翻译层机器指令(目标代码).但是此时还不可以运行
3. 链接 链接器把由编译器产生的目标代码和任何其他附加代码整合在一起,这样最终产生可执行的程序,这些附加代码包括很多程序中用到的库函数

上面过程往往时自动实现的

`cc pun.c`自动编译链接输出一个可执行文件

`gcc -Wall -o pun pun.c` -Wall 可以是 gcc 比平常更彻底的检查程序并且警告可能发生的问题

## 简单程序的通用形式

```c
指令

int main()
{
    语句
    return 0;
}
```

### 指令

在编译 C 程序之前,*预处理器*会首先对 C 程序进行编辑.

`#include <stdio.h>`指令说明,编译前把`stdio.h`中的信息包含到程序中

### 函数

函数分为:标准库函数和自定义函数

C 语言中每个程序必须包含一个 main 函数,在执行程序是系统会自动调用 main 函数,main 函数结束时给操作系统返回一个整数值

### 语句

C 语句必须以分号结尾,_指令都是一行所以指令不需要分号结尾_

### 显示字符串

printf 函数显示一条**字符串字面量**,字符串字面量用双引号包围的一系列字符.printf 不会自动换行,所以要用`\n`

### 注释

`/**/`

## 变量和赋值

### 类型

变量类型决定了变量的取值范围.不同机器上取值范围会有所不同

### 声明

变量在使用之前必须先声明

```c
int height;
float profit;
/*如果类型相同可以在同一行进行声明*/
int width,height,volume;
float profit,loss;
```

### 显示变量的值

`printf` `%d`整数 `%.2f`保留两位小数

### 初始化

当程序开始执行时,某些变量自动设置为零,而大多数则不会,所以一般会在声明时直接初始化一个值

### 读取输入

`scanf`读取数据,他也需要格式化

`scanf("%d",&i);`读取整数.`%d`表示读取整数,i 是一个 int 型变量,读取的整数存入 i 地址中

## 定义常量

常量在执行过程中不会变:

使用宏定义`#define CUBIC_IIN_PER_LB 166`,预处理器会把,程序中用宏表示的值替换掉

`weight=(volume+CUBIC_IIN_PER_LB)/CUBIC_IIN_PER_LB --> weight=(volume+166)/166`

宏还可以使用表达式,但是**必须使用括号** `#define CUBIC_IIN_PER_LB (5.0 * 9.5)`

一般宏定义的常量都是大写

# 格式化输入输出

scanf 和 printf 是 C 使用最频繁的两个函数,用来支持格式化的读写

## printf 函数

`printf(格式串,表达式1,表达式2,...)`

格式串包含:普通字符和转换说明.转换说明以`%`开头.跟随在%后面的信息指定了把数值从内部(二进制)形式转换成打印(字符)形式的方法.例如:`%d`表示吧 int 型数值从二进制形式转换为十进制数字组成的字符串,`%f`对 float 进行转换

### 转换说明

转换说明可以有`%m.pX`或`%-m.pX`,m 和 p 都是整形常量,X 是字母,m 和 p 都是可选,如果省略 p 那么分割 m 和 p 的小数点也忽略掉

m 要显示的最小字符宽度,如果打印的数值比 m 个字符少,那么值在字段内是右对齐的 例如:`printf("%4d",12)`那么显示`..12`缺少部分用空格表示 `%-m.pX`表示左对齐.

p 是精度,依赖于 X

* d int 型,p 表示显示的数字的最少个数,不足则在前面用 0 补位,默认 p=1
* e 浮点数的科学计数法,p 表示小数点后应该出现的个数,默认 p=0 不显示小数点
* f 定点十进制形式的小数,p 与 e 一样含义
* g 表示指数形式或者定点十进制形式的浮点数
* `u o x 代替 d` u 表示 10 进制,o 表示八进制,x 表示 16 进制
  * 短整型和长整型需要再加上 h 或者这 l 例如:`%hd`表示短整型,`%lx`表示长整形 16 进制
* 对于 double float 使用`%lf` 对于 long float 使用`%Lf`
* c 字符
* s 字符串

转移符:

* \a 报警
* \b 回退
* \n 换行
* \t tab

## scanf

避免使用 scanf 使用字符读取,然后进行格式转换

# 表达式

避免使用模糊难以看懂的表达式

## 赋值运算符

`v=e`当 e 的类型与 v 不同时,e 被转换为 v 类型

# 选择语句

比较时产生的结果是 0 或者 1

判断是非零值为真,

`> < >= <= == !=`和`! && ||`

`&& 和 ||`都是短路运算

## if

```
if (condition) statement ;
else statement;

if (condition) {
    statements;
}
else if (condition) {
    statements;
}
else {
    statements;
}
```

**悬空 else** 两个 if 后面跟了一个 else,那么 else 属于最近的一个 if,或者使用`{}`隔离

`condition ? yes : no`

## switch

```
switch (condition){
    case value1: statements;
                break;
    case value2: statements;
                break;
    default: statements;
            break;
}

switch (condition){
    case value1:
    case value2:
    case value3: statements;
                break;
    case value4: statements;
                break;
    default: statements;
            break;
}
/* value1 value2 value3 都会走到value3的执行语句*/
```

break 跳出 switch 结构,不在继续向下

## 循环

### while

```
while (condition) statement;
while (condition){
    statements;
}
```

### do

```
do{
    statements;
}while (condition);
```

### for

```
for(;;){
    statements;
}
```

### break 和 continue

break 只能跳出当前结构,不能跳出外层结构

### goto

```
label_name:
```

goto 语句很少使用,但是 continue,break 和 return 都是受限制的 goto 语句

**但是当 break 和 continue 不能跳出外层结构时,goto 语句就可以解决这个问题**

```c
#include <stdio.h>
int main()
{
    int d = 10;
    for (; d < 100; d++)
    {
        if (d == 20)
        {
            printf("current value = %d \n", d);
            goto done;
        }
    }
    done:
    {
        printf("left");
    }
    return 0;
}
```

# 基本类型

不同机器字节数不同,例如 16 位,32 位,64 位

* short int
* unsigned short int
* int
* unsigned int
* long int
* unsigned long int
* float
* double
* long double

八进制 以`0`开头

十六进制 以`0x`开头

## 转义序列

* `\a` 警报
* `\b` 退格符
* `\f` 换页符
* `\n` 换行
* `\r` 回车
* `\t` 横向制表符
* `\v` 纵向制表符
* `\\`反斜线
* `\?` 问好
* `\'` 单引号
* `\"` 双引号

## 字符型

**不同的机器会有不同的字符集**

ASCII 用 7 位表示 128 个字符,一些计算机把 ASCII 用八位来表示 256 个字符.其他一些机器使用完全不同的字符集,例如 Unicode

C 语言会按照小整数的方式来处理字符

```c
/*读取字符*/
while(getchar()!='\n');
```

### sizeof

确定存储类型值所需要的空间的大小

### 类型转换

隐式转换和显示转换

隐式转换在计算赋值时发生

显示转换使用`(type) var`

## 类型定义

`#define BOOL int`可以用来定义新的类型

`typedef`可以更好的定义类型.例如`typedef int boolean`

# 数组

C 语言支持两中聚合变量,数组和结构.

获取数组长度`sizeof(a)/sizeof(a[0])`;`#define size sizeof(a)/sizeof(a[0])`

# 函数

C 语言参数都是值传递,在调用时,计算出实际参数的值,并把它赋值给形式参数,在函数执行过程中,对形式参数的修改不会影响实际参数的值

```
返回类型 函数名(形式参数){
    声明
    语句
}
```

在 main 函数调用之前需要对函数进行声明

如果不声明,那么 main 函数中会对调用的函数的参数类型进行提升(float-->double,char 和 short--->int)

**但参数是一维数组时,不能通过 sizeof(a)/sizeof(a[0])来计算数组的长度,所以要传递数组大小**

**如果是二维数组,那么第二位一定要提供大小**

## return 语句

`return 表达式`

`void`和`return;`不返回

## 程序终止

main 函数中的 return 表示退出程序的状态码,也可以用 exit(状态码)

## 递归函数

C 程序员一般不使用递归

# 程序结构

## 局部变量

* **自动存储期限**.调用函数时,自动分配局部变量的存储单元,函数返回时,收回分配
* 程序块作用域:从变量的声明的点开始一直到闭合函数体的末尾.因为局部变量的作用域不能延伸到函数之外

**静态存储期限**:如果局部变量添加 static 声明,那么在整个程序执行期间都会保留变量的值;**但是只在函数体内是一直保留该值的,外部无法调用,**

```
#include <stdio.h>

#define True 1;
#define False 0;

void test();

int main()
{
    static int i=0;
    test(i);
    test(i);
    return 0;
}

void test(int i)
{
    i += 1;
    printf("%d\n",i);
}
```

上面打印的都是 1

```
#include <stdio.h>

#define True 1;
#define False 0;

void test();

int main()
{
    static int i=0;
    test(&i);
    test(&i);
    return 0;
}

void test(int *i)
{
    *i+=1;
    printf("%d\n",*i);
}
```

使用指针,可以修改该值;

## 外部变量

外部变量是声明在函数体外的

* 静态存储期限.就如同声明为 static 一样
* 文件作用域:从变量声明的点开始一直到闭合文件的末尾,所以跟随在其后的所有函数都可以访问他

外部变量的缺点:

* 一处修改所有地方都受到影响
* 不容易复用外部变量

## 程序块

`{语句}`

程序块中的变量存储期限是自动的,进入时分配存储单元,离开时解除分配;不能再块外面访问变量

函数体也是程序块

## 作用域

变量的作用域在块范围或者文件范围,程序块内会临时隐藏外部作用域,离开块是外部作用域又恢复

## 构建 C 程序

```
#include 指令
#define 指令
类型定义
外部变量声明
除了main函数之外的函数原型
main函数的定义
其他函数的定义
```

# 指针

## 指针作为实际参数

形式参数的的值是实际参数的值,他们指向同一内存地址,所以形参改变内存值.

## const 保护实际参数,即使是指针也不允许修改值

## 指针作为返回值

```c
int *max(int *a,int *b){
    return b?*a>*b:a;
}
//这里返回较小值得指针
```

# 指针和数组

相同类型的指针可以指向数组

```c
int a[10],*p;
p=&a[0]

//数组名表示第一个数组元素
p=a //也是可以的
```

## 指针操作

指向数组位置改变

```c
p=&a[2];
q=p+3;
p+=6;
```

```c
p=&a[8];
q=p-3;
p-=6;
```

```c
p=&a[8];
q=&a[1];
i=p-q;//i=7
//获得是p和q之间的距离,类似于a[i],a[j],i-j
```

```c
p=&a[5];
q=&a[1];
p>q//指针比较是比较他们的位置,位置大则大
```

## 指针用于数组

```c
#define N 10
int a[N],sum,*p;
sum=0;
for(p=&a[0];p<&a[N];p++){
    sum+=*p;
}
// 这里数组元素位置为0到N-1;但是取址&A[N]是正确的;因为循环不会蚕食检查a[N]的值
```

```c
a[i++]=j;
//相等于
*p++=j;//*(p++)
```

-`*p++`或者`*(p++)`:自增前表达式的值是`*p`,然后自增 p,**p 指向下一个位置** -`(*p)++`:自增前表达式的值是`*p`,然后自增`*p`;**p 并没有移动到下一个位置,只是修改当前位置的值**

* `*++p`或者`*(++p)`;先自增 p,自增后表达式的值是`*p`;**先指向下一个位置,然后修改下一个位置的值**
* `++*p`或者`++(*p)`;先自增*p,自增后表达式的值是`*p`;**只是修改当前位置的值**

**数组名可以作为指针,但是不可以修改数组指针值`a++ //错误`**

## 指针和多维数组

对于二维数组实际上存储的位置还是连续的,所以用 p 指向`a[0][0]`,然后`p++`,就可以访问全部的数组元素

```c
int *p;
for(p=a[0][0];p<&a[rows][cols];p++){
    *p=0;
}
```

处理独立的行

```c
int a[rows][cols],*p,i;

for(p=a[i];p<&a[i]+cols;p++){
    *p=0;
}
```

## 多维数组名做指针

# 字符串

## 连续的字符换可以使用`\`链接,或者分多行

```c
printf("adsfasdfdf \
asdfasdfasd\
")
printf("adsfasdfdf"
"asdfasdfasd"
"")
```

## 如何存储字符串字面量

在字符串末尾添加**空字符`\0`**,ASCII 中空字符是`\0`,

在字符串尾部添加`\0`来表示结束

所以比定义的空间要多一个存储空间

```
char *a = "123123123";
printf("%c", *a);
printf("%c", *(a+1));
printf("%c", *(a+2));
printf("%c", *(a+3));
printf("%c", a[0]);
printf("%c", a[1]);
printf("%c", a[2]);
printf("%c", a[3]);
```

## 访问字符串中的字符

```c
int count_space(const char *s){
    int count=0;
    for(;*s!='\0';s++){
        if(*s==' '){
            count++;
        }
    }
    return count;
}
```

## C 字符串库

```c
// 利用=操作进行复制是不可以的
char str1[10],str2[10];
str1="abc";//错误
str2=str1;//错误

//比较字符串内容
if(str1==str2){}; //错误,这里实在比较指针手否相等
```

**string.h**提供字符串操作方法

`char *strcpy(char *s1,const char *s2)`返回 s1 指针,将 s2 字符串内容复制费 s1

`char *strcat(char *s1,const char *s2)`返回 s1 指针,将 s2 追加到 s1 后面

`int strcmp(const char *s1,const char s2)`比较两个字符串是否相等,`s1<s2 <0`;`s1>s2 >0`;`s1=s2 =0`

`size_t strlen(const char *s)`;返回字符串长度,`size_t`是无符号整数类型,**除非处理很长的字符串否则不需要考虑该类型**

## 字符串惯用法

### 搜索字符串的结尾

```c
size_t strlen(const char *s){
    size_t n;
    for(n=0;*s!='\0',s++){
        n++;
    }
    // while(*s++) n++;或者while (*s) s++;也可以;这里测试的是*s++字符,如果字符为空,那么就停止
    return n;
}
```

```c
char *strcat(char *s1,const char *s2){
    char *p=s1;
    while(*p) p++;
    while (*p++=*s2++);//这里while语句会测试赋值表达式的值,也就是测试复制的字符,如果字符为空,那么就停止
    return s1;
}
```

## 字符串数组

## 命令行参数

`main(int argc,char *argv[])`;argc 参数个数,`*argv[]`,指向命令行参数的指针数组,argv[0]指向程序名

argv[argc]是一个附件元素,始终是一个空指针,**空指针不指向任何内容**

**宏 NULL 代表空指针**

# 预处理器

预处理器的行为是由指令控制的,指令释义`#`开头的一些命令

`#define`定义一个宏,用来代表其他东西的一个名字

`#include`打开一个特定的文件,将他的内容作为正在编译的文件的一部分包含进来,可以简单看做用源文件内容覆盖当前这条指令

## 预处理指令

* 宏定义:`#define;#undef`
* 文件包含:`#include`
* 条件编译:`#if,#ifdef,#ifndef,#elif,#else,#endif`

`#error,#line,#pragma`较少用到

指令规则:

* 以`#`开头
* 指令和符号之间可以插入任意多空格
* 指令在第一个换行符处结束,除非明确指出要继续,使用`\`继续
* 指令可以出现在程序的任何地方
* 注释可以与指令在同一行:`#define size int /*注释*/`

## 宏定义

**#define 可以不加替换列表,因为他们只是用来做判断 #if defined(XXX)**

`#define 标识符 替换列表`

`#define 标识符(x1,x2,x3) 替换列表`

例如:`#define max(x,y) ((x)>(y)?(x):(y)) #define is_even(n) ((n)%2==0)`;

替换:`i=max(j+k,m-n);if(is_even(i)) i++;`

结果:`i=((j+k)>(m-n)?(j+k):(m-n));if(((n)%2==0)) i++;`

**参数用()包含起来**

宏缺点:

* 编译后程序会变大
* 没有类型检查
* 无法用指针指向一个宏
* 可能不止一次计算它的参数

## `#`运算符

`#`运算符将一个宏的参数转换为字符串字面量

只能用在带参数的的宏的替换列表中

例如:`#define print_int(x) printf(#x " = %d\n",x)`

变为:`printf("i/j" " = %d\n",i/j);`

## `##`运算符

`##`可以将两个几号'粘'在一起,成为一个记号;

例如:`define mk_id(n) i##n`

`int mk_id(1),mk_id(2),mk_id(3);`变成`int i1,i2,i3;`

```c
#define generic_max(type)      \
type type##_max(type x,type y) \
{                              \
    return x>y?x:y;            \
}
```

## 宏的通用属性

* 宏可以包含对另一个宏的调用`#define PI 3.14159 #define tow_pi (2*PI)`
* 宏的作用范围通常到出现这个宏的文件末尾
* 宏不可以被定义两次,除非两次定义一样

## 宏定义中`()`

如果不加`()`,由于优先级不同导致,不能按照正常替换顺序,结合

## 创建较长的宏

```
#define echo(s) \
do{             \
gets(s);        \
puts(s);        \
}while(0)
```

## 预定义宏

C 中预定义了一些有用的宏

* `__LINE__` 被编译的文件的行数,当前行
* `__FILE__` 被编译文件的名字
* `__DATE__` 编译的日期(格式"Mmm dd yyyy")
* `__TIME__` 编译的时间(格式"hh:mm:ss")
* `__STDC__` 如果编译器接受标准 C 那么值为 1

`__LINE__`和`__FILE__`有助于定位错误位置

例如:

```c
#define check_zero(divisor) \
if (fivisor==0) \
printf("file: %s --> line: %d",__FILE__,__LINE__)
```

## 条件编译

```c
#define DEBUG 1
#if DEBUG //这里会测试DEBUG的值,非0时保存其中的代码
printf("debug model")
#endif
```

**如果没有定义 DEBUG 时,`#if DEBUG`会失败,但是`#if !DEBUG`会成功**

## defined 运算符

仅当被测试的宏被定义是,返回真;`#if defined(DEBUG)`括号可以不要`#if defined DEBUG` 反向测试`#if !defined(DEBUG)`

可以用`#ifdef`和`#ifndef`代替

## `#elif 和 #else`

## 其他指令

`#error 消息`如果预处理器遇到一个`#error`指令会立即终止编译二不去找其他错误

它一般和条件编译指令一起检查编译过程中不应该出现的情况

`#line n`或者`#line n "文件名"` 他会改变`__LINE__`宏 很少用到

`#pragma 记号`

# 编写大规模程序

## 头文件

`#include <文件名>`搜寻系统头文件所在的目录

`#include "文件名"`搜索当前目录,然后搜索系统目录

**不要在自己编写的头文件时使用<>**

文件名可以只是用绝对位置

## 共享宏定义和类型定义

多数大规模程序包含几个源文件共享宏定义和类型定义

## 共享函数原型

源文件包含函数 f 的调用,但是 f 是定义在另一个文件 foo.c 文件中的,那么调用 f 之前如果没有声明是危险的,

那么把 foo.c 函数原型放在头文件中,这样其他文件直接包含头文件就可以了

## 共享变量声明

把共享变量和函数共享类似;`int i`这样不仅声明了变量 i,而且编译器为 i 留出了空间

为了声明没有定义的变量 i,需要在变量晟敏的开始出加上`extern`.`extern int i`,这样说明 i 是在其他地方定义的,不需要为 i 分配空间

extern 定义数组时,可以忽略数组的长度`extern int a[];`

通常把共享变量放在头文件中

**共享变量有重大的缺点**

## 嵌套包含

## 保护头文件

如果一个文件包含两个文件,二这两个文件又都包含第三个文件,那么可能导致编译错误

如果直播暗含宏定义,函数原型 或者 变量声明,不会有问题

但是包含类型定义就会编译错误

为了防止文件多次包含,使用`#ifndef`和`#endif`来把文件的内容闭合起来

```c
#ifndef Py_ODICTOBJECT_H
    #define Py_ODICTOBJECT_H
    #ifdef __cplusplus
    extern "C" {
    #endif

    /* OrderedDict */

    #ifndef Py_LIMITED_API

    typedef struct _odictobject PyODictObject;

    #endif

    #ifdef __cplusplus
    }
    #endif
#endif /* !Py_ODICTOBJECT_H */
```

## 把程序划分成多个文件

## 构建多文件程序

`gcc -o fmt fmt.c line.c word.c`这里指定输出文件为 fmt,编译三个文件,并且链接他们

### makefile

把所有文件名列在命令行不可行

mekefile 不仅列出了作为程序部分的文件,而且还描述文件之间的依赖性

```
fmt:fmt.o  word.o line.o
    cc -o fmt fmt.o word.o line.o

fmt.o:fmt.c word.h line.h
    cc -c fmt.c
word.o:word.c word.h
    cc -c word.c
line.o:line.c line.h
    cc -c line.c
```

# 结构联合枚举

```c
struct name{
    int a;
    char b;
} name1,name2;
struct name{
    int a;
    char b;
} name1={1,'1'},name2={2,'2'};

struct name name1={1,'11};//不能丢失struct
```

## 结构类型定义

```c
typedef struct{
    int number;
    char name[name_len];
    int on_hand;
} Part;
//这里定义了类型Part
Part p1,p2;//定义类型之后就不能添加struct了
```

```c
union {
    int number;
    char name[name_len];
    int on_hand;
} u;
```

union 共享一段存储空间,具体占的空间大小依赖于赋值操作

## 使用联合来构造混合的数据结构

```c
typedef union{
    int i;
    float f;
}Number;
Number n;
n.i=5;
n.f=93.3;
```

可以通过 struct 来记录当前 union 存储的类型

```c
typedef struct{
    int kind;
    union{
        int i;
        float f;
    }u;
}Number;
n.kind=INT_KIND;
n.u.i=82;
```

## 枚举

`enum{clubs,diamonds,hearts,spades} s1,s2;`

`typedef enum{False,True} Bool;`

枚举是被作为整数来处理的,`typedef enum{False=0,True=1} Bool;`

```c
typedef struct{
    enum {Int=0,Float=1} kind;
    union{
        int i;
        float f;
    }u;
}Number;
n.kind=Int;
n.u.i=100;
```

# 指针的高级应用

## 动态存储分配

内存分配函数在 stdlib 中

* malloc:分配内存块,但是不对内存块初始化
* calloc:分配内存块,并对内存块进行清除
* realloc:调整先前分配的内存块

申请内存时无法知道计划存储的内存块的数据类型,所以返回`void*`, `void*`型的值是通用指针,本质上他只是内存地址

当调用内存分配函数时,无法定位满足我们需要的足够大的内存块.函数会返回**空指针**,把返回值存储在指针变量 p 中以后,要判断 p 是否为空指针

NULL 表示空指针 `if(p=malloc(10000)==NULL){}`

NULL 在 6 个头文件中都有定义:locale,stddef,stdio,stdlib,string,time

所有非空指针都为真,空指针为假

## 动态分配字符串

`void malloc(size_t size)`原型

给 n 个字符的字符串分配空间 `p=malloc(n+1);`

`void*`可以赋值给任意类型的指针,但是可以强制转换`p=(char *)malloc(n+1);`

## 字符串函数中使用动态存储分配

```c
char *concat(const char *s1,const char *s2){
    char *result;
    result=malloc(strlen(s1)+strlen(s2)+1);
    if(result==NULL){
        printf("can not mallock");
    }
    strcpy(result,s1);
    strcat(result,s2);
    return result;
}
```

## malloc 为数组分配存储空间

`int *a;a=malloc(n*sizeof(int));`

## calloc

初始化

## realloc

`void *realloc(void *ptr,size_t size)`

size表示新的内存块大小

**realloc之后要对指向内存块的所有指针进行更新**


## 释放存储

如果指向存储区的指针,改变了指向,那么要在改变之前释放存储区`void *free(void *ptr)`

如果直接释放存储区,那么指针将会变成**悬空指针**

## 链表

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node Node;
struct Node
{
    int value;
    Node *next;
};

int main()
{
    Node *first = malloc(10 * sizeof(Node));
    Node new = {10, NULL};
    first[0] = new;
    first[1] = new;
    first[1].value = 20;
    first[1].next = &first[0];
    printf("%d", first[1].next->value);
    return 0;
}
```

## 指向指针的指针

## 指向函数的指针

函数占用内存单元,所以函数都有地址

### 函数指针作为实际参数

`double intergrate(double (*f)(double),double a,double b);`

`(*f)`说明f是指向函数的指针

`result=integrate(sin,0.0,PI/2)`
sin是个函数,当函数名后面没有跟着圆括号时,C语言编译器会产生指向函数的指针来代替产生函数调用的代码

类似于数组a,a本身作为指向数组的指针,但是a[i]就表示数组的一个元素,所以f和f(x)

在integrate内部可以调用f:`sum+=(*f)(x)`


**C函数库中一些功能抢单的函数要求把函数指针作为实际参数**

### qsort

`void qsort(void *base,size_t nmemb,zise_t size,int (*compar)(const void * ,const void *));`

## 函数指针的其他用途

声明函数指针:`void (*pf)(int);` pf可以指向任何返回为空,并且只有一个int类型参数的函数

调用pf:`(*pf)(i);`或者`pf(i)`

函数指针数组:`void (*file_cmd[])(void) = {new_cmd,open_cmd,close_cmd}`

调用函数指针数组:`(*file_cmd[n])()`或者`file_cmd[n]()`

# 声明





