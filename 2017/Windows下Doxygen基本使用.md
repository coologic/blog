---
title: " Windows下Doxygen基本使用\t\t"
tags:
  - doxygen
url: 520.html
id: 520
categories:
  - 其他
date: 2017-12-03 16:25:08
---

什么是Doxygen?
-----------

Doxygen?是一个程序的文件产生工具，可将程序中的特定批注转换成为说明文件。通常我们在写程序时，或多或少都会写上批注，但是对于其它人而言，要直接探索程序里的批注，与打捞铁达尼号同样的辛苦。大部分有用的批注都是属于针对函式，类别等等的说明。所以，如果能依据程序本身的结构，将批注经过处理重新整理成为一个纯粹的参考手册，对于后面利用您的程序代码的人而言将会减少许多的负担。不过，反过来说，整理文件的工作对于您来说，就是沉重的负担。 Doxygen?就是在您写批注时，稍微按照一些它所制订的规则。接着，他就可以帮您产生出漂亮的文档了。 因此，Doxygen?的使用可分为两大部分。首先是特定格式的批注撰写，第二便是利用Doxygen的工具来产生文档。 目前Doxygen可处理的程序语言包含： C/C++、Java、IDL?(Corba,?Microsoft及KDE-DCOP类型) 而可产生出来的文档格式有： HTML、XML、LaTeX、RTF 而其中还可衍生出不少其它格式。HTML可以打包成CHM格式，而LaTeX可以透过一些工具产生出PS或是PDF文档。

安装Doxygen
---------

·?1.1?安装?Doxygen?1.7.4(Windows) ·?1.2?安装?graphviz?2.28.0(Windows) graphviz?是一个由AT&T实验室启动的开源工具包，用于绘制DOT语言脚本描述的图形。Doxygen?使用?graphviz?自动生成类之间和文件之间的调用关系图，如不需要此功能可不安装该工具包。 ·?1.3?安装?Windows?Help?Workshop?1.32 Doxygen?使用这个工具可以生成?CHM?格式的文档。

Doxygen的配置
----------

Doxygen?产生文档可以分为三个步骤。一是在程序代码中加上符合Doxygen所定义批注格式。二是使用Doxywizard进行配置。三是使用Doxygen来产生批注文档。 Doxygen?1.7.4?主界面如下图?1?所示。 ![](http://img.blog.csdn.net/20140212135410640)

?说明：1，Doxygen?工作目录，就是用来存放配置文件的目录。

???????2，递归搜索源文件目录需要选上。

### 选择?wizard?标签下的?Output?Topics

关配置说明如下图?2?所示。

![](http://img.blog.csdn.net/20140212152655109)

### 选择?wizard?标签下的?Diagrams?Topics

相关配置说明如下图?3?所示。

![](http://img.blog.csdn.net/20140212152734296)

说明：如果选择这个选项之前需要先安装了?Graphviz?工具包。

### 选择?expert?标签下的?Project?Topics

相关配置说明如下图?4?所示。

![](http://img.blog.csdn.net/20140212152957562) 说明：编码格式，UTF-8?是首选。如果需要显示中文则选择GB2312. TAB\_SIZE?主要是帮助文件中代码的缩进尺寸，譬如@code和@endcode段中代码的排版，建议设置成4。 OPTIMIZE\_OUTPUT\_FOR\_C?这个选项选择后，生成文档的一些描述性名称会发生变化，主要是符合C习惯。如果 是纯C代码，建议选择。 SUBGROUPING这个选项选择后，输出将会按类型分组。

### 选择?expert?标签下的?Build

![](http://img.blog.csdn.net/20140212153154000)

Build页面，这个页面是生成帮助信息中比较关键的配置页面：

EXTRACT_ALL?表示：输出所有的函数，但是private和static函数不属于其管制。

EXTRACT_PRIVATE?表示：输出private函数。

EXTRACT_STATIC?表示：输出static函数。同时还有几个EXTRACT，相应查看文档即可。

HIDE\_UNDOC\_MEMBERS?表示：那些没有使用doxygen格式描述的文档（函数或类等）就不显示了。当然，如果EXTRACT_ALL被启用，那么这个标志其实是被忽略的。

INTERNAL_DOCS?主要指：是否输出注解中的@internal部分。如果没有被启动，那么注解中所有的@internal部分都

将在目标帮助中不可见。

CASE\_SENSE\_NAMES?表示：是否关注大小写名称，注意，如果开启了，那么所有的名称都将被小写。对于C/C++这种

字母相关的语言来说，建议永远不要开启。

HIDE\_SCOPE\_NAMES?表示：域隐藏，建议永远不要开启。

SHOW\_INCLUDE\_FILES?表示：是否显示包含文件，如果开启，帮助中会专门生成一个页面，里面包含所有包含文件的列

表。

INLINE_INFO?：如果开启，那么在帮助文档中，inline函数前面会有一个inline修饰词来标明。

SORT\_MEMBER\_DOCS?：如果开启，那么在帮助文档列表显示的时候，函数名称会排序，否则按照解释的顺序显

示。

GENERATE_TODOLIST?：是否生成TODOLIST页面，如果开启，那么包含在@todo注解中的内容将会单独生成并显

示在一个页面中，其他的GENERATE选项同。

SHOW\_USED\_FILES?：是否在函数或类等的帮助中，最下面显示函数或类的来源文件。

SHOW_FILES?：是否显示文件列表页面，如果开启，那么帮助中会存在一个一个文件列表索引页面。

### 选择?expert?标签下的?Input?Topics

相关配置说明如下图?5?所示。

![](http://img.blog.csdn.net/20140212153415765)

说明：输入的源文件的编码，要与源文件的编码格式相同。如果源文件不是UTF-8编码最好转一下。

总结：

我查看到我的代码文件是GB2312的（查看方法（以VS2008为例）：文件->高级保存选项)。此时如果这里还是选择UTF-8，那么会导致编译出来的CHM文件中的中文会有乱码。

![](http://img.blog.csdn.net/20140213135831984)

### 选择?expert?标签下的?HTML?Topics

相关配置说明如下图?6?所示。

![](http://img.blog.csdn.net/20140212153607484) 说明：1，CHM\_FILE文件名需要加上后缀（xx.chm）。 2，如果在?Wizard?的?Output?Topics?中选择了?prepare?for?compressed?HTML?(.chm)选项，此处就会要求选择?hhc.exe?程序的位置。在?windows?help?workshop?安装目录下可以找到?hhc.exe。 3，为了解决DoxyGen生成的CHM文件的左边树目录的中文变成了乱码，CHM\_INDEX\_ENCODING中输入GB2312即可。 4，GENERATE\_CHI?表示索引文件是否单独输出，建议关闭。否则每次生成两个文件，比较麻烦。 5，TOC_EXPAND?表示是否在索引中列举成员名称以及分组（譬如函数，枚举）名称。

### 选择?Run?标签

相关配置说明如下图?7?所示。 ![](http://img.blog.csdn.net/20140212154101390)

点击?Run?doxygen?按钮，?Doxygen?就会从源代码中抓取符合规范的注释生成你定制的格式的文档。

写正确格式的批注
--------

并非所有程序代码中的批注都会被Doxygen?所处理。您必需依照正确的 格式撰写。原则上，Doxygen?仅处理与程序结构相关的批注，如 Function，Class?，档案的批注等。对于Function内部的批注则不做 处理。Doxygen可处理下面几种类型的批注。 JavaDoc类型： /** *?...?批注?... */ Qt类型： /*! *?...?批注?... */ 单行型式的批注： ///?...?批注?... 或 //!?...?批注?... 要使用哪种型态完全看自己的喜好。以笔者自己来说，大范围的注 解我会使用JavaDoc?型的。单行的批注则使用"///"?的类型。 此外，由于Doxygen?对于批注是视为在解释后面的程序代码。也就是 说，任何一个批注都是在说明其后的程序代码。如果要批注前面的程 式码则需用下面格式的批注符号。 /*!<?...?批注?...?*/ /**<?...?批注?...?*/ //!<?...?批注?... ///<?...?批注?... 上面这个方式并不适用于任何地方，只能用在class?的member或是 function的参数上。 举例来说，若我们有下面这样的class。 class?MyClass?{ public: int?member1?; int?member2: void?member\_function(); }; 加上批注后，就变成这样： /** *?我的自订类别说明?... */ class?MyClass?{ public: int?member1?;?///<?第一个member说明?... int?member2:??///<?第二个member说明?... int?member\_function(int?a,?int?b); }; /** *?自订类别的member\_funtion说明?... * *?@param?a?参数a的说明 *?@param?b?参数b的说明 * *?@return?传回a+b。 */ int?MyClass::member\_function(?int?a,?int?b?) { return?a+b?; } 当您使用Doxygen?产生说明文档时，Doxygen?会帮您parsing?您的程 式码。并且依据程序结构建立对应的文件。然后再将您的批注，依据 其位置套入于正确的地方。您可能已经注意到，除了一般文字说明外 ，还有一些其它特别的指令，像是@param及@return?等。这正是 Doxygen?另外一个重要的部分，因为一个类别或是函式其实都有固定 几个要说明的部分。为了让Doxygen?能够判断，所有我们就必需使用 这些指令，来告诉Doxygen?后面的批注是在说明什么东西。Doxygen 在处理时，就会帮您把这些部分做特别的处理或是排版。甚至是制作 参考连结。 首先，我们先说明在Doxygen?中对于类别或是函数批注的一个特定格 式。 /** *?class或function的简易说明... * *?class或function的详细说明... *?... */ 上面这个例子要说的是，在Doxygen?处理一个class?或是function注 解时，会先判断第一行为简易说明。这个简易说明将一直到空一行的 出现。或是遇到第一个"."?为止。之后的批注将会被视为详细说明。 两者的差异在于Doxygen?在某些地方只会显示简易说明，而不显示详 细说明。如：class?或function的列表。 另一种比较清楚的方式是指定@brief的指令。这将会明确的告诉 Doxygen，何者是简易说明。例如： /** *?@brief?class或function的简易说明... * *?class或function的详细说明... *?... */ 除了这个class?及function外，Doxygen?也可针对档案做说明，条件 是该批注需置于档案的前面。主要也是利用一些指令，通常这部分注 解都会放在档案的开始地方。如： /*!?\\file?myfile.h \\brief?档案简易说明 详细说明. \\author?作者信息 */ 如您所见，档案批注约略格式如上，请别被"\\"?所搞混。其实，"\\" 与"@"?都是一样的，都是告诉Doxygen?后面是一个指令。两种在 Doxygen?都可使用。笔者自己比较偏好使用"@"。 接着我们来针对一些常用的指令做说明：

@file

档案的批注说明。

@author

作者的信息

@brief

用于class?或function的批注中，后面为class?或function的简易说明。

@param

格式为 @param?arg_name?参数说明 主要用于函式说明中，后面接参数的名字，然后再接关于该参数的说明。

@return

后面接函数传回值的说明。用于function的批注中。说明该函数的传回值。

@retval

格式为 @retval?value?传回值说明 主要用于函式说明中，说明特定传回值的意义。所以后面要先接一个传回值。然后在放该传回值的说明。

Doxygen?所支持的指令很多，有些甚至是关于输出排版的控制。您可从Doxygen的使用说明中找到详尽的说明。 下面我们准备一组example.h?及example.cpp?来说明Doxygen?批注的使用方式： example.h: /** *?@file?本范例的include档案。 * *?这个档案只定义example这个class。 * *?@author?garylee@localhost */ #define?EXAMPLE\_OK??0???///<?定义EXAMPLE\_OK的宏为0。 /** *?@brief?Example?class的简易说明 * *?本范例说明Example?class。 *?这是一个极为简单的范例。 * */ class?Example?{ private: int?var1?;?///<?这是一个private的变数 public: int?var2?;?///<?这是一个public的变数成员。 int?var3?;?///<?这是另一个public的变数成员。 void?ExFunc1(void); int?ExFunc2(int?a,?char?b); char?\*ExFunc3(char?\*c)?; }; example.cpp: /** *?@file?本范例的程序代码档案。 * *?这个档案用来定义example这个class的 *?member?function。 * *?@author?garylee@localhost */ /** *?@brief?ExFunc1的简易说明 * *?ExFunc1没有任何参数及传回值。 */ void?Example::ExFunc1(void) { //?empty?funcion. } /** *?@brief?ExFunc2的简易说明 * *?ExFunc3()传回两个参数相加的值。 * *?@param?a?用来相加的参数。 *?@param?b?用来相加的参数。 *?@return?传回两个参数相加的结果。 */ int?ExFunc2(int?a,?char?b) { return?(a+b); } /** *?@brief?ExFunc3的简易说明 * *?ExFunc3()只传回参数输入的指标。 * *?@param?c?传进的字符指针。 *?@retval?NULL?空字符串。 *?@retval?!NULL?非空字符串。 */ char?*?ExFunc2(char?*?c) { return?c; }