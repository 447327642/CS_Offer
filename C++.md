
# 面向对象程序设计

面向对象程序设计的核心思想是数据抽象（封装）、继承和多态（动态绑定）

* 数据抽象：把客观事物封装成抽象的类，同时将类的接口和实现分离。（优点：可以隐藏实现细节，使得代码模块化）
* 继承：定义相似的类型，并对其相似关系建模。（优点：可以扩展已存在的代码模块）
* 多态：一定程度上忽略相似类型的区别，以统一的方式使用它们的对象。

## 类

类的基本思想是数据抽象和封装，数据抽象是一种依赖于接口和实现分离的编程技术。类的接口包括用户所能执行的操作，类的实现则包括类的数据成员、负责接口实现的函数体以及定义类所需的各种私有函数。

## 继承

继承是类的重要特性。通过继承联系在一起的类构成一种层次关系，通常在层次关系的根部有一个基类，其他类则直接或者间接地从基类继承而来，这些继承得到的类称为派生类。基类负责定义在层次关系中所有类公同拥有的成员，而每个派生类定义各自特有的成员。

派生类可以继承定义在基类中的成员，但是派生类的成员函数不一定有权访问从基类继承而来的成员，访问权限受下面因素影响。

* 继承方式；
* 基类成员的访问权限(即public/private/protected)。

继承有三种方式，即公有(Public)继承、私有(Private)继承、保护(Protected)继承。（私有成员不能被继承）

* 公有继承就是将基类的公有成员变为自己的公有成员，基类的保护成员变为自己的保护成员。
* 保护继承是将基类的公有成员和保护成员变成自己的保护成员。
* 私有继承是将基类的公有成员和保护成员变成自己的私有成员。

三种继承方式的比较

![][1]

一个派生类对象包含有多个组成部分：一个含有派生类自己定义的（非静态）成员的子对象，以及一个与该派生类继承的基类对应的子对象，如果有多个基类，那么对应的子对象有多个。（C++ 没有明确规定派生类的对象在内存中怎样分布）

因为在派生类对象中含有与其基类对应的组成部分，所以可以把派生类的对象当成基类对象来使用，而且也可以将基类的指针或引用绑定到派生类对象中基类部分上。这种转换通常称为 `派生类到基类的（derived-to-base）` 类型转换。**派生类向基类的转换是否可访问由使用该转换的代码决定，同时派生类的派生访问说明符也会有影响。**（C++ Primer 访问控制与继承）

具体代码示例参见 [C++_Inheritance.cpp](C++_Code/C++_Inheritance.cpp)

如果基类定义了一个`静态成员`，则在整个继承体系中只存在该成员的唯一定义。不论从基类派生出来多少个派生类，对于每个静态成员来说都只存在唯一的实例。

［[派生类存储，隐式转换](http://www.nowcoder.com/questionTerminal/c85f9e15e6a4410a930581ae12b9a341)］

## 多态

C++ 中，基类必须将它的两种成员函数区分开来：一种是基类希望其派生类进行覆盖的函数，另一种是基类希望派生类直接继承而不要改变的函数。对于前者，基类通常将其定义为`虚函数（virtual）`。当我们使用指针或引用调用虚函数时，该引用将被动态绑定。根据引用或指针所绑定的对象不同，该调用可能执行基类的版本，也可执行某个派生类的版本。（成员函数如果没有被声明为虚函数，则其解析过程发生在编译时而非运行时）

基类通过在其成员函数的声明语句之前加上 virtual 关键字使得该函数执行动态绑定，**任何构造函数之外的非静态函数都可以是虚函数**。如果基类把一个函数声明为虚函数，则该函数在派生类中隐式地也是虚函数（**派生类可以不重写虚函数，必须重写纯虚函数**）。［C++ primer P528］

C++中的虚函数的作用主要是实现多态机制。关于多态，简而言之就是用父类型的指针指向其子类的实例，然后通过父类的指针调用子类的成员函数。这种技术可以让父类的指针有“多种形态”，这是一种泛型技术。

虚函数（Virtual Function）是通过一张虚函数表（Virtual Table）来实现的。每个包含有虚函数的类有一个虚表，在有虚函数的类的实例中保存了虚表的指针，所以，当我们用父类的指针来操作一个子类的时候，这张虚函数表像一个地图一样，指明了实际所应该调用的函数。

C++的编译器保证虚函数表的指针存在于对象实例中最前面的位置（这是为了保证取到虚函数表的有最高的性能）。

此外：内联函数也不能是虚函数，因为其在编译时展开。虚函数是动态绑定，运行期决定的，所以内联函数不能是虚函数。

［[虚函数地址分配](http://www.nowcoder.com/questionTerminal/d50dbed9a0f44e8092f86927cb7c259f)］

参考  
[C++ 虚函数表解析](http://blog.csdn.net/haoel/article/details/1948051)

## 构造函数初始值列表

定义变量时习惯对其初始化，而非先定义、再赋值。

    string foo = "Hello";   // 定义并初始化
    string bar;             // 默认初始化为空 string 对象
    bar = "Hello";          // 为 bar 赋一个新值

就对象的数据成员来说，如果没有在构造函数的初始化列表中显式地初始化成员，则该成员在构造函数之前执行默认初始化，在构造函数中进行的是赋值操作。但是如果成员是 const 或者引用的时候，或者成员属于某种类类型且该类没有定义默认构造函数时，`必须`进行初始化。

    class ConstRef{
    public:
        // 正确, 使用构造函数初始化列表显式地初始化引用和 const 成员.
        ConstRef(int num):const_i(num), ref_j(num){}
        /*
        ConstRef(int num){
            const_i = num;  // 错误,不能给 const 赋值
            ref_j = num;    // 错误, ref_j 没有初始化
        }
        */
    private:
        const int const_a = 0;
        const int const_i;
        int &ref_j;
    };

构造函数初始值列表只说明用于初始化成员的值，而不限定初始化的具体执行顺序。成员的初始化顺序与它们`在类定义中的出现顺序一致`，第一个成员先被初始化，然后第二个，以此类推。如果一个成员是用另一个成员来初始化的，那么两个成员的初始化顺序就很关键了！（可能的话，**尽量避免使用某些成员初始化其他成员**）。

［[初始化顺序](http://www.nowcoder.com/questionTerminal/fb01e2436c6d453abbbf9801f794165b)］

## 构造函数，析构函数

异常抛出问题：

1. 不建议在构造函数中抛出异常；
2. 构造函数抛出异常时，析构函数将不会被执行，需要手动的去释放内存
3. 析构函数不应该抛出异常；
4. 当析构函数中会有一些可能发生异常时，那么就必须要把这种可能发生的异常完全封装在析构函数内部，决不能让它抛出函数之外；
5. 从析构函数抛出异常，C++运行时系统会处于无法决断的境遇，因此C++语言担保，当处于这一点时，会调用 terminate()来杀死进程。

## 友元特征

友元关系是单向的，不是对称，不能传递。
关于传递性，有人比喻：父亲的朋友不一定是儿子的朋友。
那关于对称性，是不是：他把她当朋友，她却不把他当朋友？

［[友元特征](http://www.nowcoder.com/questionTerminal/f1491d455d28443e9c1a0c01ddb9d6ab)］

## 派生类构造函数：

派生类构造函数调用顺序如下

1. 基类构造函数。如果有多个基类，则构造函数的调用顺序是基类在类派生表中出现的顺序。
2. 若派生类中包含对象成员，还要进行对象成员初始化。如果有多个成员类对象则构造函数的调用顺序是对象在类中被声明的顺序。
3. 派生类构造函数。
    
析构函数正好和构造函数相反。 ([constructor_derived_class.cpp](C++_Code/constructor_derived_class.cpp))

参考： [构造函数：C++](https://msdn.microsoft.com/zh-cn/library/s16xw1a8.aspx)

# 关键字
## extern 关键字

全局变量(外部变量)是在函数的外部定义的，它的作用域为从变量的定义处开始，到本程序文件的末尾。在此作用域内，全局变量可以为本文件中各个函数所引用。编译时将全局变量分配在静态存储区。

有时需要用**extern来声明全局变量，以扩展全局变量的作用域**。

1. 在一个文件内声明全局变量

    `提前引用声明`：如果外部变量不在文件的开头定义，其有效的作用范围只限于定义处到文件结尾了。如果在定义点之前的函数想引用该全局变量，则应该在引用之前用关键字extern对该变量作外部变量声明，表示该变量是一个将在下面定义的全局变量。

2. 在多文件的程序中声明外部变量
    
    如果一个程序包含两个文件，在两个文件中都要用到同一个外部变量num，不能分别在两个文件中各自定义一个外部变量num。正确的做法是：在任一个文件中定义外部变量num，而在另一文件中用extern对num作外部变量声明。即
    
        extern int num;
    
    编译系统由此知道num是一个已在别处定义的外部变量，它先在本文件中找有无外部变量num，如果有，则将其作用域扩展到本行开始(如上节所述)，如果本文件中无此外部变量，则在程序连接时从其他文件中找有无外部变量num，如果有，则把在另一文件中定义的外部变量num的作用域扩展到本文件，在本文件中可以合法地引用该外部变量num。

## extern c 编译器的函数名修饰机制

C++ 为了与C兼容，在符号的管理上，有一个用来声明或定义一个 C 的符号的`extern "C"`关键字用法：

    extern "C"{
        int func(int);
        int var;
    }

C++编译器会将在extern "C" 的大括号内部的代码当作C语言代码处理。所以很明显，上面的代码中，C++的名称修饰机制将不会起作用。它声明了一个C的函数func，定义了一个整形全局变量var。

参考： [extern "C"](http://book.51cto.com/art/200904/121028.htm)

## volatile（透彻理解 cpu 的寄存器缓存机制）

volatile 关键字是一种类型修饰符，用它声明的类型变量表示编译器对访问该变量的代码就不再进行优化，从而可以提供对特殊地址的稳定访问。
    
volatile 指出变量是随时可能发生变化的，每次使用的时候必须从它所在的内存地址中读取，即使它前面的指令刚刚从该处读取过数据，而且读取的数据立刻被保存。而优化做法是，由于编译器发现两次读数据之间程序没有对变量进行过操作，它会自动使用上次读的数据。这样一来，如果是一个寄存器变量或者一个`端口数据`就会出错（它们的值由程序直接控制之外的过程控制），所以说volatile可以保证对特殊地址的稳定访问。

volatile 关键字不能保证全局变量多线程安全，因为 volatile 仅仅是告诫 compiler 不要对这个变量作优化，每次都要从 memory 取数值，而不是从register。

参考： [详解C中volatile关键字](http://www.cnblogs.com/yc_sunniwell/archive/2010/06/24/1764231.html)

## static 关键字

static 用法

1. 在函数体内，一个被声明为静态的变量在这一函数被调用过程中维持上一次的值不变，即只初始化一次（该变量存放在静态变量区，而不是栈区）。
2. 在模块内（但在函数体外），一个被声明为静态的变量可以被模块内所用函数访问，但不能被模块外访问。（注：模块可以理解为文件）
3. 在模块内，一个被声明为静态的函数只可被这一模块内的其它函数调用。那就是，这个函数被限制在声明它的模块的本地范围内使用。

《C和指针》中说static有两层含义：`指明存储属性；改变链接属性`。（1）全局变量（包括函数）加上static关键字后，链接属性变为internal，也就是将他们限定在了本作用域内；（2）局部变量加上static关键字后，存储属性变为静态存储，不存储在栈区，下一次将保持上一次的尾值。除此之外，C++中还有新用法：

* 在类中的static成员变量意味着它为该类的所有实例所共享，也就是说当某个类的实例修改了该静态成员变量，其修改值为该类的其它所有实例所见；
* 在类中的static成员函数属于整个类所拥有，这个函数不接收this指针，因而只能访问类的static成员变量(当然，可以通过传递一个对象来访问其成员)。

## register 关键字

一般情况下，变量的值是存放在内存中的。当程序中用到哪一个变量的值时，由控制器发出指令将内存中该变量的值送到CPU中的运算器。经过运算器进行运算，如果需要存数，再从运算器将数据送到内存存放。

为提高执行效率，C++允许将局部变量的值放在CPU中的寄存器中，需要用时直接从寄存器取出参加运算，不必再到内存中去存取。这种变量叫做寄存器变量，用关键字register作声明。

    int fac(int n)
    {
       register int i,f=1; //定义i和f是寄存器变量
       for(i=1;i<=n;i++) f=f*i;
       return f;
    }

定义f和i是存放在寄存器的局部变量，如果n的值大，则能节约许多执行时间。不过要注意在程序中定义寄存器变量对编译系统只是建议性(而不是强制性)的。此外，现在的优化编译系统能够识别使用频繁的变量，自动地将这些变量放在寄存器中。

## const 关键字

1. 欲阻止一个变量被改变，可以使用const关键字。在定义该const变量时，通常需要对它进行初始化，因为以后就没有机会再去改变它了；
2. const 的引用，对常量的引用不能用作修改它绑定的对象，但是由于对象本身可能是非常量，所以允许通过其他途径改变值。
3. 对指针来说，可以指定指针本身为常量（const pointer, `常量指针`），也可以指定指针所指的对象为常量（pointer to const, `指向常量的指针`），或二者同时指定为const；
4. 在一个函数声明中，const可以修饰形参，表明它是一个输入参数，在函数内部不能改变其值；
5. 对于类的成员函数，有时候必须指定其返回值为const类型，以使得其返回值不为“左值”。

## final 关键字

final关键字可用于修饰类、变量和方法。final修饰的类不能被继承，final修饰的方法不能被重写，final修饰的变量不可被修改，一旦获得初始值，该变量就不能被重新赋值。
    
    class NoDerived final  { /* */ };
    class Base { /* */ };               // Base 必须定义后才能作为基类
    class Last final: Base { /* */ };
    class Bad: Last  { /* */ };         // 错误，Last 是 final的
    class Bad_2: NoDerived { /* */ };   // 错误，NoDerived 是 final的

［[final 描述错误](http://www.nowcoder.com/questionTerminal/8272c92814ca40c39f9a534485c90be2)］

# 指针相关

## 指针与引用：

指针与引用区别如下：

1. 指针是一个变量，只不过这个变量存储的是一个地址，指向内存的一个存储单元；而引用只不过是变量的一个别名而已。
2. 指针的值可以为空，也可能指向一个不确定的内存空间，但是引用的值不能为空，并且引用在定义的时候必须初始化为特定对象；
3. 指针的值在初始化后可以改变，即指向其它的存储单元，而引用在进行初始化后就不可以改变引用对象了；
4. 指针可以有多级，但是引用只能是一级；
5. sizeof引用得到的是所指向的变量(对象)的大小，而sizeof指针得到的是指针本身的大小；

## 指针和const

指向常量的指针（`pointer to const`）不能用于改变其所指对象的值，要想存放常量对象的地址，只能使用指向常量的指针。

    const int *a = 3;
    int const *a = 3;
    int const* a = 3;

指针本身是对象，因此可以把指针本身定为常量。常量指针（`const pointer`）必须初始化，而且一旦初始化完成，则它的值（也就是存放在指针中的那个地址）就不能再改变了。

    int errNumb = 0;
    int *const curErr = &errNumb;

也可以定义一个指向常量的常量指针（const pointer to const）。

    const double pi=3.14;
    const double * const pip = &pi;

为了判断const到底对谁起作用（即谁是const的），可以用以下简单规则：**const只对它左边的东西起作用，当const本身就是最左边的修饰符时，它才会对右边的东西起作用**。

## 指针和数组

通常情况下，使用取地址符来获取指向某个对象的指针，取地址符可以用于任何对象。数组的元素也是对象，对数组使用下标运算符得到该数组指定位置的元素。因此，像其他对象一样，对数组的元素使用取地址符就能得到指向该元素的指针：

    string nums[] = {"one", "two", "threee"};
    string *p = &nums[0];
    string *p2 = nums;       // 等价于 p2 = &nums[0]

一维数组：

    a <=> &a[0]        a+1 <=> &a[1]
    *a <=> a[0]        *(a+1) <=> a[1]

二维数组：

    a[0] <=>&a[0][0]  a[1] <=> &a[1][0]  a[1]+1 <=> &a[1][1]
    *a[0] <=>a[0][0]  *a[1]<=>a[1][0]    *(a[1]+1 )<=>a[1][1]

大多数表达式中，使用数组类型的对象名称其实就是使用一个指向该数组首元素的指针。

* `指针数组`(array of pointers)：即用于存储指针的数组，也就是数组元素都是指针
* `数组指针`(a pointer to an array)：即指向数组的指针

还要注意的是他们用法的区别，下面举例说明。

    int* (a[4]);         // 指针数组：数组a中的元素都为int型指针
    int* a[4];           // 指针数组：和上面是一样的，因为[]优先级高于*。
    int (*a)[4];         // 数组指针：指向数组a的指针

［[二维数组运算结果](http://www.nowcoder.com/questionTerminal/8e78bcf76d0241938dc5f08ed8b2a065)］  
［[多维数组下标操作](http://www.nowcoder.com/questionTerminal/7b4220df2f8c4729b3a716ce7cd2056d)］

## 函数指针

函数指针指向的是函数而非对象，和其他指针一样，函数指针指向某种特定类型。函数的类型由它的返回类型和形参类型共同决定，与函数名无关。

当我们把函数名作为一个值使用时，该函数自动地转换为指针。和数组类似，虽然不能直接返回一个函数，但是能返回指向函数类型的指针。

［[函数指针定义](http://www.nowcoder.com/questionTerminal/960f8047a9ee4a6f8227768f3bc2734d)］

《C++ Primer》 P221

## 产生野指针原因

“野指针”不是NULL指针，是指向“垃圾”内存的指针。“野指针”的成因主要有三种：

1. 指针变量没有被初始化。指针变量刚被创建时不会自动成为NULL指针，它的缺省值是随机的，它会乱指一气。所以，指针变量在创建的同时应当被初始化，要么将指针设置为NULL，要么让它指向合法的内存。
2. 指针p被free或者delete之后，没有置为NULL，让人误以为p是个合法的指针。
3. 指针操作超越了变量的作用域范围。

［[指针赋值语句](http://www.nowcoder.com/questionTerminal/b6f566ba692442d4b7cce66bf6804c53)］

# 存储相关

## 内存对齐、struct 结构

许多实际的计算机系统对基本类型数据在内存中存放的位置有限制，它们会要求这些数据的首地址的值是某个数k（通常它为4或8）的倍数，这就是所谓的内存对齐。

每个特定平台上的编译器都有自己的默认“对齐系数”（32位机一般为4，64位机一般为8）。我们可以通过预编译命令#pragma pack(k)，k=1,2,4,8,16来改变这个系数，其中k就是需要指定的“对齐系数”；也可以使用#pragma pack()取消自定义字节对齐方式。

struct 或者 union 成员对齐规则如下：

1. 第一个数据成员放在offset为0的地方，每个成员按照对齐系数和自身占用字节数中，二者比较小的那个进行对齐；
2. 在数据成员完成各自对齐以后，struct或者union本身也要进行对齐，对齐将按照对齐系数和struct或者union中最大数据成员长度中比较小的那个进行；

先局部成员对齐，然后再全局对齐。（[memory_align.cpp](C++_Code/memory_align.cpp)）

参考： [C、C++内存对齐](http://www.jellythink.com/archives/413)

## 联合体存储特点以及CPU字节序

`联合体`：在C/C++程序的编写中，当多个基本数据类型或复合数据结构要占用同一内存块时，就要用到联合体。联合体变量所占内存长度是各成员中最长的成员占的内存长度。union所有成员都从低地址开始存放。

`Little endian` 和 `Big endian` 是CPU 存放数据的两种不同顺序。对于整型、长整型等数据类型，Big endian 认为第一个字节是最高位字节（按照从低地址到高地址的顺序存放数据的高位字节到低位字节）；而Little endian 则相反，它认为第一个字节是最低位字节（按照从低地址到高地址的顺序存放数据的低位字节到高位字节）。

例如，假设从内存地址0x0000开始有以下数据：0x12 0x34 0xab 0xcd。如果我们去读取一个地址为 0x0000 的四个字节变量，若字节序为big-endian，则读出结果为0x1234abcd；若字节序位little-endian，则读出结果为 0xcdab3412。

一般来说，x86系列CPU 都是little-endian 的字节序，PowerPC通常是Big endian，还有的CPU 能通过跳线来设置CPU 工作于Little endian 还是Big endian 模式。

我们可以利用 union 的存储特点，写一个简单的程序来判定当前 CPU 的字节序([union_endian.cpp](C++_Code/union_endian.cpp))。

［[判断 CPU大小端](http://www.nowcoder.com/questionTerminal/a903ebe93add411d9c94b114f5fabb36)］  
［[溢出，大小端](http://www.nowcoder.com/questionTerminal/c7298be0b2ce42698b80987631cf8fca)］  
［[大小端，网络发送](http://www.nowcoder.com/questionTerminal/d7c1ff50fab44443b61903eccd791f1d)］  

## sizeof 运算符
    
sizeof 运算符返回`一条表达式或者一个类型名字所占的字节数`，sizeof 运算符满足右结合律，所得的值是一个 size_t 类型的常量表达式。sizeof 运算符的运算对象有两种形式：

1. sizeof *(type)*
2. sizeof *expr*

第二种形式中，返回的是表达式结果类型的大小（注意**，sizeof 并不实际计算其运算对象的值**）。

    Sales_data data, *p;        
    sizeof(Sales_data); // 存储 Sales_data 类型的对象所占空间的大小
    sizeof data;        // data 的类型的大小，即 Sales_data
    sizeof p;           // 指针所占空间的大小
    sizeof *p;          // p所指类型的空间大小，即sizeof(Sales_data)
    sizeof data.revenue;// Sales_data 的 revenue 成员对应类型的大小
    sizeof Sales_data::revenue;// 另一种获取 revenue 大小的方式

在 sizeof 的运算对象中解引用一个无效指针仍然是一种安全的行为，因为指针实际上并没有被真正使用，sizeof 并不需要真的解引用指针也能知道它所指对象的类型。这是一件可以在程序运行前（编译时）完成的事情，所以，sizeof(*p)直接就被具体数字给取代了，在运行时也就不会有了解引用这个表达式。

    int i = 10;
    printf("%d\n",i);                           // 10
    // sizeof 并不计算 i++
    printf("%d\n",sizeof(i++));                 // 4
    printf("%d\n",i);                           // 10
    
sizeof 运算符的结果部分地依赖于其作用的类型：

* 对 char 或者类型为 char 的表达式执行 sizeof 运算，结果得 1；
* 对饮用类型执行 sizeof 运算得到被引用对象所占空间的大小；
* 对指针执行 sizeof 运算得到指针本身所占空间的大小；
* 对解引用指针执行 sizeof 运算得到指针指向的对象所占空间的大小，指针不需要有效；
* 对数组执行 sizeof 运算得到整个数组所占空间的大小（sizeof 不会把数组转换成指针来处理，可以用数组的大小除以单个元素的大小得到数组中元素的个数）。
* 对 string 对象或 vector 对象执行 sizeof 运算只返回该类型固定部分的大小（**24**），不会计算对象占用了多少空间；

[sizeof_demo.cpp](C++_Code/sizeof_demo.cpp)  
［[字符数组，八进制坑](http://www.nowcoder.com/questionTerminal/a7b35bc367604e73823d2dded6496e38)］

参考：
[sizeof() a vector](http://stackoverflow.com/questions/2373189/sizeof-a-vector)

## C++内存堆栈

堆区，栈区的区别：

* 管理方式：对于栈来讲，是由编译器自动管理；对于堆来说，分配释放工作由程序员控制，容易造成内存泄露。
* 空间大小：一般来讲在32位系统下，堆内存可以达到4G的空间，从这个角度来看堆内存几乎是没有什么限制的。但是对于栈来讲，一般都是有一定的空间大小的。
* 碎片问题：对于堆来讲，频繁的new/delete势必会造成内存空间的不连续，从而造成大量的碎片，使程序效率降低。对于栈来讲，则不会存在这个问题。
* 生长方向：对于堆来讲，向着内存地址增加的方向增长；对于栈来讲，向着内存地址减小的方向增长。
* 分配方式：堆都是动态分配的，没有静态分配的堆。栈有2种分配方式：静态分配和动态分配。
* 分配效率：计算机在底层对栈提供支持，分配专门的寄存器存放栈的地址，压栈出栈都有专门的指令执行，这就决定了栈的效率比较高。堆则是C/C++函数库提供的，它的机制是很复杂的，效率比栈要低得多。

## 常见的内存错误

常见内存错误以及解决办法:

* 内存分配未成功，却使用了它。（在使用内存之前检查指针是否为NULL）
* 内存分配虽然成功，但是尚未初始化就引用它。（无论用何种方式创建数组，都应该初始化）
* 内存分配成功并且已经初始化，但操作越过了内存的边界。例如在使用数组时经常发生下标“多1”或者“少1”的操作。特别是在for循环语句中，循环次数很容易搞错，导致数组操作越界。
* 忘记了释放内存，造成内存泄露。动态内存的申请与释放必须配对，程序中malloc与free的使用次数一定要相同，否则肯定有错误（new/delete）。
* 释放了内存却继续使用它。

## new 和 delete 操作

有时候需要一次为很多对象分配/释放内存，为此 C++ 提供了 new/delete 操作符。为了让 new 分配一个对象数组，需要在类型名后跟一对方括号，在其中指明要分配的对象的数目。

    int *pia = new int[10];
    typedef int arrT[10];   // arrT 表示 10 个int的数组类型 
    int *p = new arrT;      // 分配一个 10 个 int 的数组，p指向第一个int。编译器执行时和第一句完全一样

虽然通常称new T[] 分配的内存为动态数组，但当我们用new分配一个数组时，并未得到一个`数组类型的对象`，而是得到一个相应元素类型的**指针**。由于分配的内存并不是一个数组类型，因此不能对动态数组调用 begin 或 end，也不能用范围 for 语句来处理动态数组中的元素，sizeof 的结果也和真正的数组类型的对象不同。

默认情况下，new 分配的对象，不管是单个分配的还是数组中的，都是默认初始化的。不过也可以对数组中的元素进行值初始化，方法是在大小之后跟一对空括号。

    int *pia = new int[10];
    int *pia2 = new int[10](0);

使用new操作符来分配对象内存时会经历三个步骤：

1. 调用operator new 函数（对于数组是operator new[]）分配一块足够大的，原始的，未命名的内存空间以便存储特定类型的对象。
2. 编译器运行相应的构造函数以构造对象，并为其传入初值。
3. 对象构造完成后，返回一个指向该对象的指针。

当我们使用一条 delete 表达式删除一个动态分配的对象时：

    delete sp;          // 销毁 *sp, 然后释放 sp 指向的内存空间
    delete [] arr;      // 销毁数组中的元素，然后释放对应的内存空间

实际上执行了两步操作：

1. 对 sp 所指的对象或者 arr 所指的数组中的元素执行对应的析构函数。
2. 编译器调用标准库operator delete(或operator delete[])函数释放内存空间。

数组中的元素按逆序销毁，即最后一个元素首先被销毁，然后是倒数第二个，依次类推。当释放一个指向数组的指针时，空方括号对是必需的：它指示编译器此指针指向一个对象数组的第一个元素。**如果在 delete 一个指向数组的指针时忽略了方括号，或者在 delete 一个指向单一对象的指针时使用了方括号，其行为是未定义的。**

［[内存管理错误代码](http://www.nowcoder.com/questionTerminal/84598a88502c499d995db941c5fb62a2)］

## new 和 malloc 的对比

1. new/delete是C++操作符，malloc/free是C/C++函数。
2. 使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算，而malloc则需要显式地指出所需内存的尺寸。
3. new/delete会调用对象的构造函数/析构函数以完成对象的构造/析构。而malloc只分配空间。
4. new 操作符内存分配成功时，返回的是对象类型的指针，类型严格与对象匹配，无须进行类型转换，故new是符合类型安全性的操作符。而malloc内存分配成功则是返回void * ，需要通过强制类型转换将 void* 指针转换成我们需要的类型。
5. 效率上：malloc的效率高一点，因为只分配了空间。
6. opeartor new /operator delete 可以被重载，而malloc/free并不允许重载。

参考  
[细说new与malloc的10点区别](http://www.cnblogs.com/QG-whz/p/5140930.html)

## 浮点数存储

整数可以很方便地用二进制来存储，对于小数来说，直观上来看，转为二进制也很简单，分整数部分和小数部分分别转换，比如十进制数 9.3125 表示为二进制为 1001.0101。（整数部分用2除，取余数；小数部分用2乘，取整数位。）

    0.3125 * 2 = 0.625 整数位是 0   --> .0
    0.625 * 2 = 1.25   整数位是 1   --> .01
    0.25 * 2 ＝ 0.5    整数位是 0   --> .010
    0.5 * 2 = 1.0      整数位是 1   --> .0101

通常，浮点数可以被表示成 N = S * r^j ，其中 S 为尾数，j为阶码，r是基数。`阶码是整数，阶符和阶码的位数合起来反映浮点数的表示范围及小数点的实际位置。尾数是小数，其位数反映了浮点数的精度。`

在计算机中，基数一般取 2，所以数 N=11.0101，可以写为：

0.110101 * 2^2  
1.10101 * 2^1  
1101.01 * 2^-2  

为了提高精度以及便于浮点数的比较，在计算机中规定浮点数的尾数用纯小数表示，此外将尾数最高位为1的浮点数称为`规格化数`，即 N=0.110101 为N的规格化形式。

C++ 编译器中浮点数存储采用的是 IEEE 754标准，它定义的单精度浮点数的长度为 32 位，按位域可划分为：符号位、阶码位与尾数位。

单精度浮点数位域划分如下：

     31----------------------22----------------------------------------------------------0
      |                       |                                                          |
      X X X X    X X X X    X X X X    X X X X    X X X X    X X X X    X X X X    X X X X
      | |-------------------| |----------------------------------------------------------|
    符号        阶码                                     尾数

其中：

* `符号位`：取 0 表示正数，取 1 表示负数。
* `阶码`：阶码用移码表示，阶码的真值都被加上一个常数（偏移量），单精度偏移量为 127 (0x7f) 。
* `尾数`：尾数长度在图示中是 23 位，但实际上却是 24 位，还有一个位是“不可见”的，其值固定为 1，也就是说 IEEE 754 标准所定义的浮点数，其有效数字是介于 1 与 2 之间的小数。规格化尾数时注意小数范围为 1 到 2.

下面以数字 0.75 为例：

> 十进制 0.75 转换为二进制 -- 0.11  
> 二进制 0.11 规格化-------- 1.1*2^-1  
> 计算阶码----------------- -1+127=126  
> 符号位 指数部分    尾数部分  
> 0 ----01111110 -10000000000000000000000  
> 以单精度浮点格式存储该数  0011 1111 0100 0000 0000 0000 0000 0000  

一个十进制数能否用二进制浮点数精确表示，关键在于小数部分。我们来看一个最简单的小数 0.1，它会得到一个无限循环的二进制小数 0.000110011...，用有限位无法表示无限小数，因此无法用IEEE 754 浮点数精确表示。

IEEE 754 标准所定义的单精度浮点数所表示的数的范围大约为 -2^128 ~ 2^128 （-10^38 ～ 10^38 ），因为尾数的最大值是接近 2，而指数的范围是 [-127, 127]，那么这个范围就可以表示为2*2^127 。单精度浮点数的精度为小数点后面 5～6（2^23=8388608）个十进制位。

    float b = 54.00001;
    float c = 54.000001;
    cout << setprecision(10) << b << endl; // 54.00001144
    cout << setprecision(10) << c << endl; // 54

参考  
[IEEE 754 浮点数的表示精度探讨](http://www.cnblogs.com/bossin/archive/2007/04/08/704567.html)  
[IEEE 754浮点数在机器中的格式](http://blog.csdn.net/glgoober/article/details/6209881)  
[一个浮点数跨平台产生的问题](http://coolshell.cn/articles/11235.html)  

# 其它语言特征

## 四种类型转换

* reinterpret_cast：一个指针转化为其他类型的指针时，不做类型检测，操作结果是一个指针指向另一个指针的值的二进制拷贝；

        class A{}; 
        class B{}; 
        A* a = new A;
        B* b = reinterpret_cast(a);
        
* static_cast：允许执行隐式转换和相反的转换操作，父类转换为子类是强制转换，而子类转换为父类就是隐式转换；

        class Base {}; 
        class Derive:public Base{}; 
        Base* a = new Base; 
        Derive *b = static_cast(a);

* dynamic_cast：用于对象的指针和引用，当用于多态类型转换时，允许隐式转换及相反的过程中。与static_cast的不同之处在于，在相反的转换过程中，dynamic_cast会检测操作的有效性，如果返回的不是被请求的有效完整对象，则返回null，反之返回这个有效的对象，如果是引用返回无效时则会抛出bad_cast异常；
* const_cast：这个转换操作会操纵传递对象的const属性，或者设置或者移除该属性。

        class C{}; 
        const C* a = new C; 
        C *b = const_cast(a);

## include 头文件

 `#inlcude <>` 只搜索系统目录，不会搜索本地目录。比如你自己写一个头文件，用#include <>会出错；`#inlude ""` 首先搜索本地目录，如果本地目录没有才会搜索系统目录。
 
 因此语句`#include <stdlib.h>` 是正确的，而且程序编译速度比`#include “stdlib.h”`要快

［[include 路径](http://www.nowcoder.com/questionTerminal/8272c92814ca40c39f9a534485c90be2)］

## 按值传递和按引用传递

`传值参数`：当初始化一个非引用类型的变量时，初始值被拷贝给变量，此时对变量的改动不会影响到初始值。

指针的行为和其他非引用类型一样，执行指针拷贝操作时，拷贝的是指针的值，拷贝之后，两个指针是不同的指针。因为指针可以使我们间接地访问所指向的对象，所以通过指针可以修改它所指向的对象的值。

［[复杂的参数传递](http://www.nowcoder.com/questionTerminal/2b09b944ce7342ab8ca645690afd207b)］  
［[指针传递](http://www.nowcoder.com/questionTerminal/960f8047a9ee4a6f8227768f3bc2734d)］

`传引用参数`：通过使用引用形参，允许函数改变一个或者多个实参的值。使用引用可以避免拷贝，因为有时候拷贝较大的类对象或者容器对象比较低效，甚至有的类类型（包括 IO 类型在内）根本不支持拷贝操作。

## 赋值还是构造

［[拷贝构造还是赋值](http://www.nowcoder.com/questionTerminal/cf1a3145d1b946c1861c9d10b8629665)］

## 数组形参

数组作为形参时，会退化为指针，这是因为数组的两个性质：

1. 不允许拷贝数组（无法以值传递的方式使用数组参数）；
2. 使用数组时会将其转换为指针。

所以给函数传递指针时，实际上传递的是指向数组首元素的指针。

    // 尽管形式不同，但是这三个 print 函数是等价的
    void print(const int*);
    void print(const int[]);   
    void print(const int[10]); // 纬度表示期望数组含有多少元素，实际并不一定

［[数组合法参数](http://www.nowcoder.com/questionTerminal/e2ac8bddb9e5434a92511320221c8513)］

## If 判断语句

零值的比较

`bool`：根据布尔类型的语义，零值为“假”（记为FALSE），任何非零值都是“真”（记为TRUE）

    if (flag)  
    if (!flag)  

int 型变量：

    if (n == 0)  
    if (n != 0)  

float 型变量：浮点型变量并不精确，所以不可将float变量用“==”或“！=”与数字比较，应该设法转化成“>=”或“<=”形式。
    
    const float EPSINON = 0.00001;  
    if ((x>=-EPSINON) && (x<=EPSINON)) // EPSINON 是允许的误差（即精度） 

指针变量：

    if (p == NULL)  
    if (p != NULL)
    
以下是一些不良的风格：

    if (p == 0)        // 容易让人误解p是整型变量  
    if ( n )              // 会让人误解 n 是布尔变量 
    if (x == 0.0)   // 错误的写法

不良风格很多都能通过编译，但是语句并不能很好的表达与零值进行比较的逻辑依据。

［[相等判断语句](http://www.nowcoder.com/questionTerminal/230d0664d5104b73b4c9b4fa51c5e735)］

## unsigned 溢出

当一个算术表达式中既有无符号数又有有符号数时，就会将有符号值转换为无符号值。

    unsigned u=10;
    int i=-42;
    cout << "i+i: " << i+i << endl; // -84
    cout << "i+u: " << i+u << endl; // 4294967264
    
第二个表达式中，相加前首先把整数 －42 转换成无符号数。把负数转换成无符号数类似于直接给无符号数赋一个负值，结果等于这个负数加上无符号数（ 2^32 ）的模。

    unsigned char c = -1, d = -2, e=0xff;
    printf("%d, %d", c, d, e);         // 255, 254, 255

［[For 循环次数](http://www.nowcoder.com/questionTerminal/7183f3428a444efe8a3f91247ddf6b7a)］

## 逗号运算符

C++ 提供一种特殊的运算符，逗号运算符，它的优先级别最低。

    表达式1，表达式2，表达式3，...... ，表达式n

逗号表达式的特点：

1. 逗号表达式的运算过程为：从左往右逐个计算表达式。
2. 逗号表达式作为一个整体，它的值为最后一个表达式（也即表达式n）的值。
3. 逗号运算符的优先级别在所有运算符中最低。

（3+5，6+8）的值是14，（`a=3*5,a*4`）的值是60，原因在于赋值运算优先级高于逗号表达式。

下列程序的结果为

    int x,y,z;
    x=y=1;
    z=x++,y++,++y;
    printf("%d,%d,%d\n",x,y,z); // 231

    int a,b;
    b=( a=1,a+1,a++);
    printf("%d,%d\n",a,b)；// 2, 1

［[逗号表达式的值](http://www.nowcoder.com/questionTerminal/5971372060a24eac874d43b830864189)］

## 其它问题记录

［[代码膨胀问题](http://www.nowcoder.com/questionTerminal/f6ee5023f5334873980247cf496aa641)］  
［[C++ 重载函数原型](http://www.nowcoder.com/questionTerminal/dcb7cdf4d47747faa3be0d14d3b886e2)］  
［[C++不是类型安全](http://www.nowcoder.com/questionTerminal/f80ec593dcbd44e7a13975b53e9bdaab)］  

# 编程实现

## String 类的实现

类的构造函数，赋值函数，析构函数。

    class String
    {
    public:
        String(const char *str=NULL);//普通构造函数
        String(const String &str);//拷贝构造函数
        String & operator =(const String &str);//赋值函数
        ~String();//析构函数
    private:
        char* m_data;//用于保存字符串w
    };

具体实现（[String.cpp](C++_Code/String.cpp)）

# C++ 11 新特性

Lambda、变参模板、auto、decltype、constexpr、智能指针、列表初始化、正则表达式、线程库、静态断言、委托构造。


# 更多阅读

[C/C++内存管理详解](http://chenqx.github.io/2014/09/25/Cpp-Memory-Management/)  
[那些不能遗忘的知识点回顾——C/C++系列](http://www.cnblogs.com/webary/p/4754522.html)  
[C语言的整型溢出问题](http://www.nowcoder.com/test/question/done?tid=2482392&qid=14466#summary)



[1]: http://7xrlu9.com1.z0.glb.clouddn.com/C++_1.png


