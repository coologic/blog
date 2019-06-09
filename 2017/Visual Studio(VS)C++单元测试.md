---
title: " Visual Studio(VS)C++单元测试\t\t"
tags:
  - vs
  - 代码覆盖率
  - 单元测试
url: 516.html
id: 516
categories:
  - C/C++
date: 2017-12-02 13:51:13
---

新建一个待测项目MyProgram
-----------------

![](http://wx3.sinaimg.cn/mw690/a8dbb8d6ly1fm2cjx7ehnj20ae08k0sz.jpg) 新建了一个“Win32控制台应用程序”，在其内新建了“my_math.h”文件，为了方便没有建立类和.cpp文件，用一个简单的函数做范例。

//my_math.h
#pragma once
int add(int a, int b) {
  return a + b;
}

### 新建一个测试项目MyProgramTest

此处要选择Visual C++->测试->本机单元测试项目，填写好名称，点击确定即可，不需要其他配置就会在当前解决方案下新建出项目。 ![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fm2cjxo28tj20o70bv3z0.jpg)

> 注意新建的时候直接右键选择当前解决方案-添加-新建项目，这样默认为当前解决方案

系统默认生成了四个文件“stdafx.h”、“stdafx.cpp”(Standard Application Framework Extensions)预编译头文件，“targetver.h”运行环境定义头文件，“unittest1.cpp”测试文件。前三个不用管，直接看第四个测试文件即可。

### 必要的配置

新建完成MyProgramTest项目以后，在属性-连接器-输入-附加依赖项中添加“..\\MyProgram\\Debug\\*.obj” ![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fm2cjyax42j20uf0kcta3.jpg) 建议使用相对路径，使用*表明所有.obj后缀文件。注意只需要配置单元测试项目，不需要对原项目做任何修改。

> obj文件（Microsoft推出的程序编译中间代码文件），程序编译时生成的中间代码文件。目标文件，一般是程序编译后的二进制文件，再通过链接器和资源文件链接就成可执行文件了。OBJ只给出了程序的相对地址，而可执行文件是绝对地址。

XXXtext.cpp测试文件说明
-----------------

#include "stdafx.h"
#include "CppUnitTest.h"
#include "../MyProgram/my_math.h" //添加原始项目的头文件，建议相对路径
using namespace Microsoft::VisualStudio::CppUnitTestFramework;
namespace MyProgramTest {//MyProgram项目单元测试 
    TEST_CLASS(UnitTest1) {//测试类
    public:		
        TEST_METHOD(TestMethod1) {//测试函数
        // TODO: 在此输入测试代码
            Assert::AreEqual(15, add(5, 10));
        }
    };
}

自己包含原始项目被测函数头文件"#include "../MyProgram/my\_math.h" //添加原始项目的头文件，建议相对路径" UnitTest1为测试类名，TEST\_CLASS为VS提供的测试类宏定义

#define TEST_CLASS(className) \
ONLY\_USED\_AT\_NAMESPACE\_SCOPE class className : public ::Microsoft::VisualStudio::CppUnitTestFramework::TestClass<className>

TestMethod1为测试函数名，TEST_METHOD为VS提供的测试函数宏定义 Assert为断言类，其提供了AreEqual、AreSame、AreNotEqual、AreNotSame、IsNull、IsNotNull、IsTrue、IsFalse等多个方法以供测试中进行断言

Assert.Inconclusive()//表示一个未验证的测试；
Assert.AreEqual()    //测试指定的值是否相等，如果相等，则测试通过；
AreSame()            //用于验证指定的两个对象变量是指向相同的对象，否则认为是错误
AreNotSame()         //用于验证指定的两个对象变量是指向不同的对象，否则认为是错误
Assert.IsTrue()      //测试指定的条件是否为True，如果为True，则测试通过；
Assert.IsFalse()     //测试指定的条件是否为False，如果为False，则测试通过；
Assert.IsNull()      //测试指定的对象是否为空引用，如果为空，则测试通过；
Assert.IsNotNull()   //测试指定的对象是否为非空，如果不为空，则测试通过；

> 若需要多个测试函数，只需要在public:下建立多个TEST_METHOD即可 若需要多个测试类，可以新建一些cpp文件，注意包含vs单元测试文件CppUnitTest.h

运行单元测试
------

> 单元测试运行，不需要提前先编译原始程序，运行测试时会自动编译。

在菜单栏-测试-运行选择运行所有测试即可 ![](http://wx4.sinaimg.cn/mw690/a8dbb8d6ly1fm2cjyqj3wj20e406gq3h.jpg) 选择此项后会先编译目标项目，然后执行所有测试类中的**public中的测试函数**(有不需要测试的可以临时改为private)。 测试结果会在测试资源管理器显示 ![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fm2cjzaw7zj207n0b774m.jpg) 若此窗口不自动弹出，可在菜单栏-测试-窗口打开 ![](http://wx1.sinaimg.cn/mw690/a8dbb8d6ly1fm2cjzn29kj20d205o0t7.jpg)

其他
--

### 并行测试

“测试资源管理器”搜索框左侧的三个双向箭头按钮，点击他会进入选中状态，即开启了并行测试功能。

### 代码覆盖率测试

对于VS2015 Enterprise版本在测试菜单下“分析代码覆盖率”可以利用当前单元测试分析对原始项目的代码覆盖率。

> VS2015 Community版本没有分析代码覆盖率功能

在网上找到的秘钥，不知道是否有效，可以用来试一试代码覆盖率测试。。。。 专业版：HMGNV-WCYXV-X7G9W-YCX63-B98R2 企业版：HM6NR-QXX7C-DFW2Y-8B82K-WTYJV 试用完成后请购买正版，支持正版