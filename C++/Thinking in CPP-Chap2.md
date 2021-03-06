# Chap2——初见对象与C语言回眸

类：用户定义的数据类型，有**封装**的特点：

- 外部无需知晓类的内部构造，调用其public的成员函数即可实现既定功能
- 对外隐藏实现细节，对类内部实现细节进行改写不会影响到外部对其的调用，即所谓“面向接口编程”。

本章主要对iostream、string和vector进行简介。

## 2-1. 基本数据类型与执行控制语句、运算符

### 2-1-1. 指针与引用

- \*在定义指针时，优先和变量结合。int \*pa, pb, pc;中，只有pa是指针。
- 引用可被用来传递参数地址，使用引用在语法构成上更清晰。
- 引用是对指针操作的语法级封装，说白了就是一颗语法糖。（用传值的语法起到了传指针的作用）
- 不能引用void类型

### 2-1-2. 作用域

- 一堆匹配的左右大括号，其中定义变量的作用域由其限制。
- C语言强制在作用域开始位置定义所有变量，C++提倡需要定义时再定义。

### 2-1-3. 各种变量

- global：全局变量在所有函数体外定义，可在其他部分或其他源文件中（使用extern标识标明）使用。
- auto：自动局部变量存储在栈上，随着函数的返回而消亡。auto类型是所有局部变量的默认类型，不需显示指明auto。
- register：寄存器（register）局部变量是建议编译器优先将该变量绑定到寄存器上，加快访问速度。但由于寄存器数量有限，不一定能实现。所以加上这个标识符对编译器来说仅仅只是一种建议而已。另外，现代编译器都很牛逼，会自动进行代码优化，一般不建议手工指定寄存器变量。
- static local：静态（static）局部变量存储在全局数据段，在编译时就已经被赋值了（类对象会在启动时调用其构造函数），所以其并不随着所属函数执行结束而消亡。但在作用域之外不可见。
- extern：外部变量。该符号告诉编译器：从本文件开始到当前位置都找不到该符号的定义，请你再到该文件后面或其他源文件中找找。
- **static关键字的另一层含义：在作用域外不可见！**用于全局符号，则其只在该源文件内可见（即所谓文件作用域）。编译器对加了static关键字的符号执行**内部链接**，即该符号仅在该源文件内有效；而不加static关键字的全局符号则放入全局符号表，链接时如果发现某个目标文件的未决符号表中有extern属性的未决符号，便允许跨目标文件交叉引用，这就是所谓的**外部链接**。在C语言中，可以不适用extern声明外部符号（即默认符号为外部符号）
- const：常变量，本质是一个变量，只是在编译时检查不允许对其值的改变。
- volatile：为编译器优化提供指示，告知编译器该变量值可能会改变，不应进行变量折叠或常量传播等优化操作。

### 2-1-4. 运算符与优先级

- 左右移符号可以和=结合。
- 优先级难记，括号保平安。

### 2-1-5. C++中的类型转换

- static_cast<转换后类型>(待转换类型)：包括三种情况：同类型（比如数字）向上或者向下转换，以及为void\*类型正本清源，平反雪耻。包括隐式类型转换、可能丢失信息的转换，void*变换等等，总之强制转换除外。
- reinterpret_cast<转换后类型>(待转换类型)：强制类型转换
- dynamic_cast<转换后类型>(待转换类型)：确认安全后的向下转型
- const_cast<转换后类型>(待转换类型)：去const或volatile属性

### 2-1-1. 殊途同归——解释器与编译器

- 解释器：同声传译，拿着源码直接翻译成机器码，翻译一行执行一行。或者先翻译成中间语言，再找个认识中间语言的解释器干活。
- 编译器：书面翻译，整体翻译为机器指令后开干。

2-1-2.编译器的使命：

预编译——编译（两次编译，语法分析、汇编）——链接

## 2-2. 声明与定义

- 声明：为函数或变量提供符号信息
- 定义：为变量分配内存，为函数生成代码

需要注意的是：

1. C++中允许多处声明，但定义只能有一处，否则链接时会报重定义错误（ODR规则）。
2. 在之前未声明的情况下，定义的同时也即声明了该函数或变量。

### 2-2-1. 函数声明

函数声明所必要的信息：

- 函数名称
- 参数类型（个数、顺序和每个参数的类型）
- 返回值

注意：

1. 形参名称在声明中并不是必须的。
2. **对于形参列表为空的函数，C++编译器将其解释为无参函数，C语言编译器将其解释为可带任意参数的函数。**（假的吧？！兄弟伙验证一波，如果是假的咱们搞篇檄文声讨Bruce叔叔）
3. extern关键字对于函数声明来说是多余的。

例子：

```c++
bool BruceIsWrong(int page, int line, int row);
```

### 2-2-2. 函数定义

函数声明所必要的信息：

- 函数声明的所有必要信息
- 函数形参变量名（必须要）
- 函数的实现代码（用大括号包起来）

```c++
bool BruceIsWrong(int page, int line, int row)
{
    //请在此处开始papapa
}
```

### 2-2-3. 变量的声明

extern关键字向编译器表明：定义在别处，这里只能告诉你有这个符号而已。

```c++
extern int nChap;
```

### 2-2-4. 变量的定义

变量的定义就是去掉extern关键字，告诉编译器，就在这里给老子把这个名的变量对应的内存空间给分配喽。在定义时还可以顺带对其初始化。

```c++
int nNumber;
const double PI = 3.141592653;
```

## 2-3. 换个角度看Hello World

```c++
#include <iostream>
using namespace std;

int main()
{
    cout << "Hello world!" << endl;
  	return 0;
}
```

还记得这段简单的不能再简单，很可能是大多数人学习C++时敲出的第一个程序吗？时隔多年后再回味，让我们细品其每一个部分。

### 2-3-1. include预编译指令——站在前人的肩膀上

当年学C++时，

- include \<cstdio\>
  - 到默认头文件路径下去找名为cstdio的头文件，并把该文件内容替换到此
  - 用于包含标准库或添加入头文件路径的库的头文件
  - 对于ANSI C的头文件，C++标准库提供一套功能相同的，在其旧名头部添加“c”字符并在其尾部去掉“.h”的新库：如stdio.h变为了cstdio
  - 旧版本的C++头文件形如：iostream.h，其中的内容并未封装到std命名空间中。
  - 不推荐使用老版本的头文件，更不推荐新老混用。
- include "resource.h"
  - 优先在当前目录搜索目标头文件
  - 若在当前目录下没有找到，再按include\<resource.h\>的规则找

### 2-3-2. namespace——道不同，不相为谋

现在程序开发早就过了那种单打独斗，一个人就可以撑起一个软件的年代，大家讲求都是团队合作、协同开发。既然多人分工，就难免出现“撞车”问题。比如，某甲和某乙都觉得nTotalNumber这名字好，于是就在各自负责的那部分里以此为名创建了一个全局变量：

- moujia.cpp：

  ```c++
  int nTotalNumber = 23333333;
  ```

- mouyi.cpp

  ```c++
  int nTotalNumber = 6666666;
  ```

他们在各自的单元测试中都没发现任何问题，结果合并代码一编译——链接器懵逼了：嗨呀，你们给我搞出了两个全局变量还叫一模一样的名字？！一方面，不可能给变量、函数取个名字还注册个专利；另一方面，也不可能用其他方式区分（别提什么moujia_nTotalNumber或者moujia_nTotalNumber_201709120043之类的，一来即便如此也无法完全保证没有重复的，二来这种命名，怕是不想干这行了出来报复社会的吧）。估计写C++编译器那帮人也遇到这问题了，所以他们就搞出来一个namespace。不同的人将各自的代码写到不同的namespace中，使用时加上namespace名称，也就解决冲突了。比如：

```cpp
namespace zhangsan
{
    int nTotalNumber = 23333333;
}

namespace lisi
{
    int nTotalNumber = 6666666;
}

int nTryCount = lisi::nTotalNumber + 1;
int nErrorCount = zhangsan::nTotalNumber + 1;
```

由于写标准库的那部分人也害怕他们在标准库中定义的符号名与使用者定义的符号名撞车~~，导致郁闷至极的使用者拿着刀在他们家门口准备砍人（大雾）~~便决定将C++标准库的代码全部封装到一个名为std的命名空间中。

有如下方式使用该命名空间中的内容：

- 完全打开：using namespace std;  则std命名空间下的所有符号名今后都可以毫无忌惮地使用了
- 部分打开：using std::cout; 则将std命名空间下的cout暴露出来，但其他部分仍然没有暴露出来
- 指名道姓： std::cout << "Hello world" << std::endl; 在使用时在作用域标识符前指明其所属的命名空间

### 2-3-3. main()函数与字符串常量——还是熟悉的味道

#### main()函数

main()函数是我们的老朋友了，是控制台版本的C/C++程序默认会用到的。

- main函数是用户代码的入口函数，遵循cdecl的调用约定，因此其压入的argc、argv乃至VC增加的envp等参数可以不用写出，也可以根据需要用相应类型的变量进行接收

- main函数这种有无参数皆可的技巧绝对是链接器对其进行了某种程度的容忍，普通函数采用普通方法恐怕办不到这样（除非调用时用内联汇编将参数压栈，并由调用方完成堆栈平衡）

  ```c++
  int __cdecl mymain(int a, int b)
  {
  	cout << a << b << endl;
  	return 0;
  }
  // 在调用时
  int nRet = 0;
  _asm
  {
  	push 2
  	push 1
  	call mymain
  	mov nRet, eax
  	add esp, 0x8
  }
  ```

  采用此种方式，即便去掉mymain函数的参数，程序也一样可以通过编译并运行：

  ```c++
  int __cdecl mymain()
  {
  	cout << "Hello world" << endl;
  	return 0;
  }
  ```

#### 字符串常量

字符串常量即用双引号括起来的字符序列，在Bruce的书中，也将字符串常量称为字符数组。但我不习惯这样称呼，以后笔记中还是叫字符串常量。它更是我们的老朋友了：

- 字符串常量不可直接修改，因为常量是只读的

- 在C++中，字符串常量仍然是C风格的字符串，出于兼容性考虑，并没有升级为字符串对象。

- 预编译过程可对两个临接的字符串常量自动进行拼接，如：

  ```c++
  cout << "All sentenses will be shown in a single line"
  		"including this row."
  ```

### 2-3-4. iostream与fstream——最熟悉的陌生人

- iostream中包含了cin、cout和cerr三个I/O管道对象，分别表征标准输入、标准输出和标准错误。

- fstream中包含了ifstream和ofstream对象，分别表征文件输入、文件输出。在使用时，要先关联文件。如：

  ```c++
  ifstream ifsTempFile("temp.dat");
  ```

由于二者都是流式对象，因此在使用上保持了较高的一致性。

- 流式对象重载了<<操作符，意义是将右侧内容发送到左侧

- 流式对象重载了>>操作符，意义是将左侧内容保存到右侧

- 标准库中的各类型都对上述运算符进行了重载，所以能保证I/O过程中的简洁与高效

- 整数进制转换：

  ```cpp
  cout << "in decimal:" << dec << 15 << endl;
  cout << "in hex:" << hex << 22 << endl;
  cout << "#27char:" << char(27) << endl;	//输出ASCII码表中编号为27的符号
  ```

### 2-3-5.  vector——有容乃大

vector是C++标准库提供的一个有序线性存储的容器，本质上是一个模板类。有如下常用套路：

- 创建与追加元素

```c++
//如：vector<存储类型> 向量名;
//以“后宫佳丽三千人”为例：
vector<CBeauty> vecHouGong;
for (int i = 0; i < 3000; i++)
{
    vecHouGong.push_back(new CBeauty("No." + i));
}
```

- 访问方式之[]：vector重载了[]操作符，可以支持数组式的访问，这种访问是可读可写的

```c++
srand((unsigned)time(NULL));
nTicket = rand() % 3000;
CBeauty cBeautyTonight = vecHouGong[nTicket];
//省去若干不和谐的代码
```

