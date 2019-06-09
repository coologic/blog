---
title: " C/C++运算符优先级\t\t"
tags:
  - c/c++
url: 1268.html
id: 1268
categories:
  - C/C++
date: 2018-09-19 17:01:16
---

介绍
--

刷题碰到了好几个这类问题，百度百科的，整理下来。 在一个表达式中可能包含多个有不同运算符连接起来的、具有不同数据类型的数据对象；由于表达式有多种运算，不同的运算顺序可能得出不同结果甚至出现错误运算错误，因为当表达式中含多种运算时，必须按一定顺序进行结合，才能保证运算的合理性和结果的正确性、唯一性。

C语言
---

优先级

运算符

名称或含义

使用形式

结合方向

说明

1

\[\]

数组下标

数组名\[整型表达式\]

左到右

()

圆括号

（表达式）/函数名(形参表)

.

成员选择（对象）

对象.成员名

->

成员选择（指针）

对象指针->成员名

2

-

负号运算符

-算术类型表达式

右到左

单目运算符

(type)

强制类型转换

(纯量数据类型)纯量表达式

++

自增运算符

++纯量类型可修改左值表达式

单目运算符

--

自减运算符

--纯量类型可修改左值表达式

单目运算符

*

取值运算符

*指针类型表达式

单目运算符

&

取地址运算符

&表达式

单目运算符

!

逻辑非运算符

!纯量类型表达式

单目运算符

~

按位取反运算符

~整型表达式

单目运算符

sizeof

长度运算符

sizeof 表达式

sizeof(类型)

3

/

除

表达式/表达式

左到右

双目运算符

*

乘

表达式*表达式

双目运算符

%

余数（取模）

整型表达式%整型表达式

双目运算符

4

+

加

表达式+表达式

左到右

双目运算符

-

减

表达式-表达式

双目运算符

5

<<

左移

整型表达式<<整型表达式

左到右

双目运算符

>>

右移

整型表达式>>整型表达式

双目运算符

6

>

大于

表达式>表达式

左到右

双目运算符

>=

大于等于

表达式>=表达式

双目运算符

<

小于

表达式<表达式

双目运算符

<=

小于等于

表达式<=表达式

双目运算符

7

==

等于

表达式==表达式

左到右

双目运算符

!=

不等于

表达式!= 表达式

双目运算符

8

&

按位与

整型表达式&整型表达式

左到右

双目运算符

9

^

按位异或

整型表达式^整型表达式

左到右

双目运算符

10

|

按位或

整型表达式|整型表达式

左到右

双目运算符

11

&&

逻辑与

表达式&&表达式

左到右

双目运算符

12

||

逻辑或

表达式||表达式

左到右

双目运算符

13

?:

条件运算符

表达式1? 表达式2: 表达式3

右到左

三目运算符

14

=

赋值运算符

可修改左值表达式=表达式

右到左

/=

除后赋值

可修改左值表达式/=表达式

*=

乘后赋值

可修改左值表达式*=表达式

%=

取模后赋值

可修改左值表达式%=表达式

+=

加后赋值

可修改左值表达式+=表达式

-=

减后赋值

可修改左值表达式-=表达式

<<=

左移后赋值

可修改左值表达式<<=表达式

>>=

右移后赋值

可修改左值表达式>>=表达式

&=

按位与后赋值

可修改左值表达式&=表达式

^=

按位异或后赋值

可修改左值表达式^=表达式

|=

按位或后赋值

可修改左值表达式|=表达式

15

,

逗号运算符

表达式,表达式,…

左到右

从左向右顺序结合

同一优先级的运算符，结合次序由结合方向所决定。

简单记就是：！ > 算术运算符 > 关系运算符 > && > || \> 赋值运算符

C++
---

运算符

名称/含义

范例

可重载性

**Group 1**(no associativity)

::

Scope resolution operator范围解析操作符

Class::age = 2;

NO

**Group 2**

()

Function call函数调用

isdigit('1')

YES

()

Member initalization成员舒适化

c\_tor(int x, int y) : \_x(x), _y(y*10){};

YES

\[\]

Array access访问数组

array\[4\] = 2;

YES

->

Member access from a pointer通过指针访问成员

ptr->age = 34;

YES

.

Member access from an object通过对象访问成员

obj.age = 34;

NO

++

Post-increment后增

for( int i = 0; i < 10; i++ ) cout << i;

YES

--

Post-decrement后减

for( int i = 10; i > 0; i-- ) cout << i;

YES

const_cast

Special cast类型转换

const\_cast<type\_to>(type_from);

NO

dynamic_cast

Special cast类型转换

dynamic\_cast<type\_to>(type_from);

NO

static_cast

Special cast类型转换

static\_cast<type\_to>(type_from);

NO

reinterpret_cast

Special cast类型转换

reinterpret\_cast<type\_to>(type_from);

NO

typeid

Runtime type information运行时类型

cout &laquo; typeid(var).name();

cout &laquo; typeid(type).name();

NO

**Group 3**(right-to-left associativity)从右向左结合

!

Logical negation

if( !done ) …

YES

not

Alternate spelling for !

~

Bitwise complement

flags = ~flags;

YES

compl

Alternate spelling for ~

++

Pre-increment

for( i = 0; i < 10; ++i ) cout << i;

YES

--

Pre-decrement

for( i = 10; i > 0; --i ) cout << i;

YES

-

Unary minus

int i = -1;

YES

+

Unary plus

int i = +1;

YES

*

Dereference

int data = *intPtr;

YES

&

Address of取地址

int *intPtr = &data;

YES

new

Dynamic memory allocation动态内存分配/动态数组内存分配

long *pVar = new long;

MyClass *ptr = new MyClass(args);

YES

new \[\]

Dynamic memory allocation of array动态内存分配/动态数组内存分配

long *array = new long\[n\];

YES

delete

Deallocating the memory动态内存释放/动态数组内存释放

delete pVar;

YES

delete \[\]

Deallocating the memory of array动态内存释放/动态数组内存释放

delete \[\] array;

YES

(type)

Cast to a given type强制类型转换

int i = (int) floatNum;

YES

sizeof

Return size of an object or type

int size = sizeof floatNum;

int size = sizeof(float);

NO

**Group 4**

->*

Member pointer selector

ptr->*var = 24;

YES

.*

Member object selector

obj.*var = 24;

NO

**Group 5**

*

Multiplication

int i = 2 * 4;

YES

/

Division

float f = 10.0 / 3.0;

YES

%

Modulus

int rem = 4 % 3;

YES

**Group 6**

+

Addition

int i = 2 + 3;

YES

-

Subtraction

int i = 5 - 1;

YES

**Group 7**

<<

Bitwise shift left

int flags = 33 << 1;

YES

>>

Bitwise shift right

int flags = 33 >> 1;

YES

**Group 8**

<

Comparison less-than

if( i < 42 ) …

YES

<=

Comparison less-than-or-equal-to

if( i <= 42 ) ...

YES

>

Comparison greater-than

if( i > 42 ) …

YES

>=

Comparison greater-than-or-equal-to

if( i >= 42 ) ...

YES

**Group 9**

==

Comparison equal-to

if( i == 42 ) ...

YES

eq

Alternate spelling for ==

!=

Comparison not-equal-to

if( i != 42 ) …

YES

not_eq

Alternate spelling for !=

**Group 10**

&

Bitwise AND按位与

flags = flags & 42;

YES

bitand

Alternate spelling for &

**Group 11**

^

Bitwise exclusive OR (XOR)按位异或

flags = flags ^ 42;

YES

xor

Alternate spelling for ^

**Group 12**

|

Bitwise inclusive (normal) OR按位或

flags = flags | 42;

YES

bitor

Alternate spelling for |

**Group 13**

&&

Logical AND逻辑与

if( conditionA && conditionB ) …

YES

and

Alternate spelling for &&

**Group 14**

||

Logical OR逻辑或

if( conditionA || conditionB ) ...

YES

or

Alternate spelling for ||

**Group 15**(right-to-left associativity)

? :

Ternary conditional (if-then-else)三目运算符

int i = (a > b) ? a : b;

NO

**Group 16**(right-to-left associativity)

=

Assignment operator

int a = b;

YES

+=

Increment and assign

a += 3;

YES

-=

Decrement and assign

b -= 4;

YES

*=

Multiply and assign

a *= 5;

YES

/=

Divide and assign

a /= 2;

YES

%=

Modulo and assign

a %= 3;

YES

&=

Bitwise AND and assign

flags &= new_flags;

YES

and_eq

Alternate spelling for &=

^=

Bitwise exclusive or (XOR) and assign

flags ^= new_flags;

YES

xor_eq

Alternate spelling for ^=

|=

Bitwise normal OR and assign

flags |= new_flags;

YES

or_eq

Alternate spelling for |=

<<=

Bitwise shift left and assign

flags <<= 2;

YES

>>=

Bitwise shift right and assign

flags >>= 2;

YES

**Group 17**

throw

throw exception

throw EClass(“Message”);

NO

**Group 18**

,

Sequential evaluation operator逗号

for( i = 0, j = 0; i < 10; i++, j++ ) …

YES