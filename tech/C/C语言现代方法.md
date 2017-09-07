# C介绍

**c的标准库,包含数百个函数,用于输入输出,字符串处理,存储分配以及其他一些操作**

Lint 代码检查

# C基本概念

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

C程序转化为可执行程序需要3个步骤:
1. 预处理 预处理器执行#开头的指令.它类似于编辑器,给程序添加内容,也可以对程序进行修改
2. 编译 修改后的程序进入编译器,编译器把程序翻译层机器指令(目标代码).但是此时还不可以运行
3. 链接 链接器把由编译器产生的目标代码和任何其他附加代码整合在一起,这样最终产生可执行的程序,这些附加代码包括很多程序中用到的库函数

上面过程往往时自动实现的

`cc pun.c`自动编译链接输出一个可执行文件

`gcc -Wall -o pun pun.c ` -Wall可以是gcc比平常更彻底的检查程序并且警告可能发生的问题

## 简单程序的通用形式

```
指令

int main()
{
    语句
    return 0;
}
```

### 指令

在编译C程序之前,*预处理器*会首先对C程序进行编辑.

`#include <stdio.h>`指令说明,编译前把`stdio.h`中的信息包含到程序中

### 函数

函数分为:标准库函数和自定义函数

C语言中每个程序必须包含一个main函数,在执行程序是系统会自动调用main函数,main函数结束时给操作系统返回一个整数值

### 语句

C语句必须以分号结尾,*指令都是一行所以指令不需要分号结尾*

### 显示字符串

printf函数显示一条**字符串字面量**,字符串字面量用双引号包围的一系列字符.printf不会自动换行,所以要用`\n`

### 注释

`/**/`

## 变量和赋值

### 类型

变量类型决定了变量的取址范围.不同机器上取值范围会有所不同

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

`scanf("%d",&i);`读取整数.`%d`表示读取整数,i是一个int型变量,读取的整数存入i地址中

## 定义常量

常量在执行过程中不会变:

使用宏定义`#define CUBIC_IIN_PER_LB 166`,预处理器会把,程序中用宏表示的值替换掉

`weight=(volume+CUBIC_IIN_PER_LB)/CUBIC_IIN_PER_LB --> weight=(volume+166)/166`

宏还可以使用表达式,但是**必须使用括号** `#define CUBIC_IIN_PER_LB (5.0 * 9.5)`



# 格式化输入输出

scanf和printf是C使用最频繁的两个函数,用来支持格式化的读写

## printf函数

`printf(格式串,表达式1,表达式2,...)`

格式串包含:普通字符和转换说明.转换说明以`%`开头.跟随在%后面的信息指定了把数值从内部(二进制)形式转换成打印(字符)形式的方法.例如:`%d`表示吧int型数值从二进制形式转换为十进制数字组成的字符串,`%f`对float进行转换

### 转换说明

转换说明可以有`%m.pX`或`%-m.pX`,m和p都是整形常量,X是字母,m和p都是可选,如果省略p那么分割m和p的小数点也忽略掉

m要显示的最小字符宽度,如果打印的数值比m个字符少,那么值在字段内是右对齐的 例如:`%4d,12`那么显示`..12`缺少部分用空格表示 `%-m.pX`表示左对齐.

p是精度,依赖于X

- d int型,p表示显示的数字的最少个数,不足则在前面用0补位,默认p=1
- e 浮点数的科学计数法,p表示小数点后应该出现的个数,默认p=0 不显示小数点
- f  定点十进制形式的小数,p与e一样含义
- g 表示指数形式或者定点十进制形式的浮点数
- u o x 代替d u表示10进制,o表示八进制,x表示16进制
    - 短整型和长整型需要再加上h或者这l 例如:`%hd`表示短整型,`%lx`表示长整形16进制
- 对于double float使用`%lf` 对于long float 使用`%Lf`
- c 字符

转移符:

- \a 报警
- \b 回退
- \n 换行
- \t tab

## scanf

避免使用scanf 使用字符读取,然后进行格式转换


# 表达式

避免使用模糊难以看懂的表达式

# 选择语句

比较时产生的结果是0或者1

判断是非零值为真,

` > < >= <= == != `和`! && ||`

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

**悬空else** 两个if后面跟了一个else,那么else属于最近的一个if,或者使用`{}`隔离

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
break 跳出switch结构,不在继续向下


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

### break和continue

break只能跳出当前结构,不能跳出外层结构

### goto

```
label_name:
```
goto 语句很少使用,但是continue,break和return都是受限制的goto语句

**但是当break和continue不能跳出外层结构时,goto语句就可以解决这个问题**

# 基本类型

不同机器字节数不同,例如16位,32wei,64位

- short int  
- unsigned short int
- int 
- unsigned int
- long int
- unsigned long int
- float
- double
- long double


八进制 以`0`开头

十六进制 以`0x`开头

## 字符型

**不同的机器会有不同的字符集**

ASCII用7位表示128个字符,一些计算机把ASCII用八位来表示256个字符.其他一些机器使用完全不同的字符集,例如Unicode

C语言会按照小整数的方式来处理字符

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

C语言支持两中聚合变量,数组和结构. 