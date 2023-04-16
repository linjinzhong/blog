---
title: c++
urlname: cplusplus
top: false
categories:
    - 编程语言
tags: 
    - c++
author: jesonlin
date: 2022-10-10 20:30:13
updated: 2022-10-10 29:30:13
---

C++是C语言的继承，是静态类型的、编译式的、通用的、大小写敏感的、不规则的面向对象的高级程序设计语言。

<!-- more -->

## 简介
1. C++ 完全支持面向对象的程序设计
    1. 封装  
        封装是将数据和方法组合在一起，对外部隐藏实现细节，只公开对外提供的接口。这样可以提高安全性、可靠性和灵活性。
    2. 继承  
        继承是从已有类中派生出新类，新类具有已有类的属性和方法，并且可以扩展或修改这些属性和方法。这样可以提高代码的复用性和可扩展性。
    3. 多态  
        多态是指同一种操作作用于不同的对象，可以有不同的解释和实现。它可以通过接口或继承实现，可以提高代码的灵活性和可读性。
    4. 抽象  
        抽象是从具体的实例中提取共同的特征，形成抽象类或接口，以便于代码的复用和扩展。抽象类和接口可以让程序员专注于高层次的设计和业务逻辑，而不必关注底层的实现细节。

2. 标准的 C++ 由三个重要部分组成
    1. 核心语言，提供了所有构件块，包括变量、数据类型和常量等等。
    2. C++ 标准库，提供了大量的函数，用于操作文件、字符串等。
    3. 标准模板库（STL），提供了大量的方法，用于操作数据结构等。
3. 重要版本迭代
    c++98 -> c++03 -> c++11 -> c++14 -> c++17

## 安装配置
```bash
# centos7 自带 gcc 是4.8.5 不支持c++14、17
yum install gcc gcc-c++

# 升级 gcc 为5.4.0
cd /usr/local/src
wget https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2
yum install biz2
tar -jxvf gcc-5.4.0.tar.bz2
cd gcc-5.4.0
./contrib/download_prerequisites
mkdir build && cd build
../configure -prefix=/usr/local --enable-checking=release --enable-languages=c,c++ --disable-multilib
make
make install
gcc -v
cd /usr/bin/
mv gcc gcc_back
mv g++ g++_back
ln -s /usr/local/bin/gcc gcc
ln -s /usr/local/bin/g++ g++
gcc -v

find / -name libstdc++.so.6*
# 结果中找最高版本，查看GLIBCXX是否满足要求
strings /usr/local/lib64/libstdc++.so.6.0.21 | grep GLIBCXX
cp /usr/local/lib64/libstdc++.so.6.0.21 /usr/lib64/libstdc++.so.6.0.21
rm -f /usr/lib64/lib
ln -s /usr/lib64/libstdc++.so.6.0.21 /usr/lib64/libstdc++.so.6
```

## 程序结构
```
#include<iostream>  // 头文件
using namespace std;  // 命名空间

// main()是程序入口
int main() {
    /* 程序入口 
    * 跨行
    */
    cout << "hello world!" << endl;
    return 0;
}

```


## 编译运行
```bash
# 默认生成可执行文件 a.out
g++ helloworld.cpp
./a.out

# 指定可执行文件的文件名
g++ helloworld.cpp -o helloworld
./helloworld

# 指定编译器版本
# -g 生成调试信息。GNU 调试器可利用该信息。
# -Wall 生成所有警告信息。
g++ -g -Wall -std=c++11 helloworld.cpp -o helloworld

# 程序 g++ 是将 gcc 默认语言设为 C++ 的一个特殊的版本
gcc helloworld.cpp -lstdc++ -o helloworld
```


## 核心语言 & 基本语法
1. 常量
    ```
    // 枚举名week，枚举变量wk，标识符monday，
    // 变量只能是枚举标识符
    enum week
    {
        MONDAY,     // 首个值为0
        TUESDAY=5,  // 标识符对应值
        WEDNESDAY,   // 默认为上一个值+1

    } wk;  // 直接声明枚举变量
    // 也可以后声明
    // week wk2;
    // 声明加定义
    // week wk2=MONDAY;
    ```
2. 类型转换
    ```c++
    // 静态类型转换
    // 静态将float类型转换为int类型，不进行运行时类型检查
    int i = 10;
    float f = static_cast<float>(i); 

    // 动态转换-基类指针或引用转换为派生类，运行时检查
    class Base {};
    class Derived: public Base {};
    Base * ptr_base = new Derived;
    Derived* prt_derived = dynamic_case<Derived*>(ptr_base);

    // 常量转换
    const int i = 10;
    int& r = const_case<int&>(i);

    // 重新解释转换
    int i = 10;
    float f = reinterpret_cast<>float&(i)
    ```
3. 变量
    ```c++
    // 变量种类
    bool char int float doble void wchar_t

    // 变量声明-在任何地方声明一个变量
    extern int a, b;
    // 变量定义
    int a, b;
    // 变量初始化
    a = 10;
    b= 20;

    // 函数声明
    int func();

    int main()
    {
        // 函数调用
        int i = func();
    }

    // 函数定义
    int func()
    {
        return 0;
    }

    // 左值：指向内存位置的表达式，可以出现在赋值号的左边或右边
    // 右值：存储在内存中某些地址的数值，无法赋值，只能出现在赋值号的右边
    // 变量是左值，数值型的字面值是右值
    ```
4. 常量
    ```c++
    // 整数常量
    85         // 十进制
    0213       // 八进制 
    0x4b       // 十六进制 
    30         // 整数 
    30u        // 无符号整数 
    30l        // 长整数 
    30ul       // 无符号长整数

    // 浮点常量
    3.14159       // 合法的 
    314159E-5L    // 合法的 

    // 布尔常量
    true / false

    // 字符常量
    L'x'  // 宽字符常量，存储在wchar_t类型
    'x'  // 窄字符常量，存储在char类型

    // 字符串常量
    string aa = "hello, \
    world";

    // 定义常量
    #define LENGTH 10
    const int LENGTH = 20;

    // 修饰符
    // 数据类型修饰符
    signed int num1 = -10; // 定义有符号整型变量 num1，初始值为 -10
    unsigned int num2 = 20; // 定义无符号整型变量 num2，初始值为 20
    short int num1 = 10; // 定义短整型变量 num1，初始值为 10
    long int num2 = 100000; // 定义长整型变量 num2，初始值为 100000
    long long int num1 = 10000000000; // 定义长长整型变量 num1，初始值为 10000000000
    float num1 = 3.14f; // 定义单精度浮点数变量 num1，初始值为 3.14
    double num2 = 2.71828; // 定义双精度浮点数变量 num2，初始值为 2.71828
    bool flag = true; // 定义布尔类型变量 flag，初始值为 true
    char ch1 = 'a'; // 定义字符类型变量 ch1，初始值为 'a'
    wchar_t ch2 = L'你'; // 定义宽字符类型变量 ch2，初始值为 '你'

    // 存储类
    // static 
    // 指示编译器在程序的生命周期内保持局部变量的存在
    // 当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。
    // static只是表示修饰的值是静态的，更多是限制作用域，而值本身可修改

    // extern 用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。当您使用 'extern' 时，对于无法初始化的变量，会把变量名指向一个之前定义过的存储位置。
    ```
5. 运算符-优先级从高到底
    |类别|  运算符   |  结合性| 
    | :---: | :---: | :---: |
    |后缀|  () [] -> . ++ - -   |从左到右|
    |一元|  + - ! ~ ++ - - (type)* & sizeof     |从右到左|
    |乘除|  * / %   |从左到右| 
    |加减|  + -     |从左到右| 
    |移位|  << >>   |从左到右| 
    |关系|  < <= > >=   |从左到右| 
    |相等|  == !=   |从左到右| 
    |位与| AND  &   |从左到右| 
    |位异或| XOR     ^   |从左到右| 
    |位或| OR   |   |从左到右| 
    |逻辑与| AND     &&  |从左到右| 
    |逻辑或| OR  ||  |从左到右| 
    |条件|  ?:  |从右到左| 
    |赋值|  = += -= *= /= %=>>= <<= &= ^= |=    |从右到左| 
    |逗号|  ,   |从左到右|

6. 函数
    ```c++
    // 匿名表达式
    [capture](参数) -> return_type {函数体}
    [](int x, int y) {return x < y;}
    []{++global_x;}
    [](int x, int y) -> int {int z = x + y; return z;}
    // capture可以为如下
    []      // 沒有定义任何变量。使用未定义变量会引发错误。
    [x, &y] // x以传值方式传入（默认），y以引用方式传入。
    [&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
    [=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
    [&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
    [=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。

    // 对于[=]或[&]的形式，lambda 表达式可以直接使用 this 指针。但是，对于[]的形式，如果要使用 this 指针，必须显式传入：
    [this]() { this->someFunc(); }();

    // e.g.
    void abssort(float* x, unsigned n) {
        std::sort(x, x + n,
            // Lambda expression begins
            [](float a, float b) {
                return (std::abs(a) < std::abs(b));
            } // end of lambda expression
        );
    }
    float x[] = {1.1, 0.8, 0.2, 0.5};
    abssort(x, 4);
    ```
7. 字符串
    ```c++
    // c风格字符串实际上是使用 null 字符 \0 终止的一维字符数组。
    // 一个以 null 结尾的字符串，包含了组成字符串的字符。
    // 由于在数组的末尾存储了空字符，所以字符数组的大小比字符串本身字符数多一个

    // #include<cstring> 大量的函数用来操作以 null 结尾的字符串:
    strcpy(s1, s2);  // 复制字符串 s2 到字符串 s1。
    strcat(s1, s2);  // 连接字符串 s2 到字符串 s1 的末尾。
    strlen(s1);  // 返回字符串 s1 的长度。
    strcmp(s1, s2);  // 如果 s1 和 s2 是相同的，则返回 0；如果 s1<s2 则返回值小于 0；如果 s1>s2 则返回值大于 0。
    strchr(s1, ch);  // 返回一个指针，指向字符串 s1 中字符 ch 的第一次出现的位置
    strstr(s1, s2);  // 返回一个指针，指向字符串 s1 中字符串 s2 的第一次出现的位置。

    // #include <string>
    string str = "hello";
    str += ", world";
    str.size();
    ```
8. 时间
    ```
    // 10位时间戳
    time_t now = time(0);
    cout << "1970 到目前经过秒数:" << now << endl;
 
    // tm结构
    tm *ltm = localtime(&now);
 
    // 输出 tm 结构的各个组成部分
    cout << "年: "<< 1900 + ltm->tm_year << endl;
    cout << "月: "<< 1 + ltm->tm_mon<< endl;
    cout << "日: "<<  ltm->tm_mday << endl;
    cout << "时间: "<< ltm->tm_hour << ":";
    cout << ltm->tm_min << ":";
    cout << ltm->tm_sec << endl;
    ```
9. 结构体
    ```c++
    struct Books
    {
       char  title[50];
       char  author[50];
       char  subject[100];
       int   book_id;
    } book;
    strcpy(book.title, "abc");
    cout << book.title << endl;
    ```

## 类 & 对象
1. 类成员函数
    类的成员函数是指那些把定义和原型写在类定义内部的函数，就像类定义中的其他变量一样。
2. 类访问修饰符
    类成员可以被定义为 public、private 或 protected。默认情况下是定义为 private，类继承也有该修饰符，默认也是private。
    1. public
        公有成员在程序中类的外部是可访问的。您可以不使用任何成员函数来设置和获取公有变量的值
    2. private
        私有成员变量或函数在类的外部是不可访问的，甚至是不可查看的。只有类和友元函数可以访问私有成员。派生类也不能。默认情况下，类的所有成员都是私有的。
    3. protect
        protected（受保护）成员变量或函数与私有成员十分相似，但有一点不同，protected（受保护）成员在派生类（即子类）中是可访问的。
    4. 在继承中的特点（分别以继承修饰符和基类各个修复符中的最严为准）
        1. public:  
            基类：public/protect/private
            子类：public/protect/private
        2. protect:  
            基类：public/protect/private
            子类：protect/protect/protect            
        3. private:
            基类：public/protect/private
            子类：private/private/private
3. 构造函数 & 析构函数
    类的构造函数是一种特殊的函数，在创建一个新的对象时调用。类的析构函数也是一种特殊的函数，在删除所创建的对象时调用。
4. 拷贝构造函数
    拷贝构造函数，是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。如果在类中没有定义拷贝构造函数，编译器会自行定义一个。如果类带有指针变量，并有动态内存分配，则它必须有一个拷贝构造函数。
    1. 通过使用另一个同类型的对象来初始化新创建的对象。
    2. 复制对象把它作为参数传递给函数。
    3. 复制对象，并从函数返回这个对象。
5. 友元函数
    友元函数可以访问类的 private 和 protected 成员。
    1. 类的友元函数是定义在类外部，但有权访问类的所有私有（private）成员和保护（protected）成员。尽管友元函数的原型有在类的定义中出现过，但是友元函数并不是成员函数。友元类整个类及其所有成员都是友元。
        ```c++
        class Box
        {
                double width;
            public:
                double length;
                // 友元函数
                friend void printWidth( Box box );
                // 友元类
                friend class BigBox;
                void setWidth( double wid );
        };
        ```
6. 内联函数
    1. 通过内联函数，编译器试图在调用函数的地方扩展函数体中的代码。
    2. 一个函数定义为内联函数，则需要在函数名前面放置关键字 inline，在调用函数之前需要对函数进行定义。
    3. 如果已定义的函数多于一行，编译器会忽略 inline 限定符。
7. C++ 中的 this 指针
    1. 每个对象都有一个特殊的指针 this，它指向对象本身。
    2. this 指针是所有成员函数的隐含参数。因此，在成员函数内部，它可以用来指向调用对象。
    3. 友元函数没有 this 指针，因为友元不是类的成员。只有成员函数才有 this 指针。
8. C++ 中指向类的指针
    指向类的指针方式如同指向结构的指针。实际上，类可以看成是一个带有函数的结构。
9. C++ 类的静态成员
    1. 类的数据成员和函数成员都可以被声明为静态的。
    2. 我们可以使用 static 关键字来把类成员定义为静态的。当我们声明类的成员为静态时，这意味着无论创建多少个类的对象，静态成员都只有一个副本。
    3. 静态成员函数即使在类对象不存在的情况下也能被调用，静态函数只要使用类名加范围解析运算符 :: 就可以访问。
    4. 静态成员在类的所有对象中是共享的。如果不存在其他的初始化语句，在创建第一个对象时，所有的静态数据都会被初始化为零。
    5. 静态成员函数与普通成员函数的区别
        1. 静态成员函数没有 this 指针，只能访问静态成员（包括静态成员变量和静态成员函数）。
        2. 普通成员函数有 this 指针，可以访问类中的任意成员；而静态成员函数没有 this 指针。


## 类特点
1. 封装-把数据和操作数据的函数捆绑在一起的机制
    封装是面向对象编程中的把数据和操作数据的函数绑定在一起的一个概念，这样能避免受到外界的干扰和误用，从而确保了安全。数据封装引申出了另一个重要的 OOP 概念，即数据隐藏。
2. 抽象-仅向用户暴露接口而把具体的实现细节隐藏起来的机制。
    好处  
    1. 类的内部受到保护，不会因无意的用户级错误导致对象状态受损。
    2. 类实现可能随着时间的推移而发生变化，以便应对不断变化的需求，或者应对那些要求不改变用户级代码的错误报告。
3. 继承
    1. 继承允许我们依据另一个类来定义一个类，这使得创建和维护一个应用程序变得更容易。这样做，也达到了重用代码功能和提高执行效率的效果。
    2. 一个派生类继承了所有的基类方法，但下列情况除外：
        1. 基类的构造函数、析构函数和拷贝构造函数。
        2. 基类的重载运算符。
        3. 基类的友元函数
    3. 派生类在继承基类的成员变量时，会单独开辟一块内存保存基类的成员变量，因此派生类自己的成员变量即使和基类的成员变量重名，但是也不会引起冲突。
4. 重载
    1. 在同一作用域中的某个函数和运算符指定多个定义，分别称为函数重载和运算符重载。
    2. 重载声明是指一个与之前已经在该作用域内声明过的函数或方法具有相同名称的声明，但是它们的参数列表和定义（实现）不相同。
    3. 选择最合适的重载函数或重载运算符的过程，称为重载决策。
    4. 重载运算符
        ```c++
        // 一元重载 !obj、-obj、--obj、++obj、obj++
        CLASS_NAME operator- () 
        // 二元重载 +、-、x、/
        CLASS_NAME operator+(const CLASS_NAME& b)
        // 关系运算符  < 、 > 、 <= 、 >= 、 ==
        bool operator <(const CLASS_NAME& b)
        ```
5. 多态
    1. 当类之间存在层次结构，并且类之间是通过继承关联时，就会用到多态。
    2. 基类指针执行派生类对象时，
        1. 静态链接：基类指针调用基类方法获取基类数据。
        2. 动态链接：基类函数使用virtual声明，编译器根据指针指向的对象动态绑定到对象方法，获取对象数据。
    3. 可以发现派生类的函数和数据与基类是不同空间。
        ```c++
        class A
        {
            public:
                void set(int aa) {a = aa;};
                int get() {return this->a;};
            private:
                int a;
        };

        class B: public A
        {
            public:
                // void set(int aa) {a = aa;};
                int get() {return this->a;};
            private:
                int a;
        };

        int main(void)
        {
            B bb;
            bb.set(1);  // 基类A的a=1
            // 派生类B的get方法覆盖了基类同名方法，返回派生类B的a初始化默认值0
            cout << "bb.get()" << bb.get() << endl;
            return 0;
        }
        ```
    4. 虚函数
        派生类中如果与基类同名，则放在不同空间，派生类中如果没有，则拷贝一份基类放在不同空间。理论上，基类和派生类只能调用各自的方法，但是基类指针指向派生类对象时，要想让基类指针调用派生类方法，需要在基类该方法前加virtual。
    5. 纯虚函数
        1. 如果类中至少有一个函数被声明为纯虚函数，则这个类就是抽象类。纯虚函数是通过在声明中使用 "= 0" 来指定的。
        2. 抽象类不能被用于实例化对象，它只能作为接口使用。
        3. 子类需要被实例化，则必须实现每个纯虚函数。


## 高级
1. 动态内存
    ```c++
    double* pvalue  = NULL; // 初始化为 null 的指针
    pvalue  = new double;   // 为变量请求内存
    *pvalue = 29494.99;     // 在分配的地址存储值
    cout << "Value of pvalue : " << *pvalue << endl;
    delete pvalue;         // 释放内存

    char* pvalue  = NULL;   // 初始化为 null 的指针
    pvalue  = new char[20]; // 为变量请求内存

    // 一维数组，动态分配,数组长度为 m
    int *array = new int[m];
    //释放内存
    delete [] array;

    // 二维数组
    int m = 2;
    int n = 3;
    int **array = new int *[m];
    // 动态分配
    for(int i = 0; i < m; i++)
    {
        array[i] = new int[n];
    }
    // 释放
    for(int i = 0; i < m; i++)
    {
        delete [] array[i];
    }
    delete [] array;
    return 0;
    ```
2. 模版
    1. 模板是泛型编程的基础，泛型编程即以一种独立于任何特定类型的方式编写代码。
    2. 可以使用模板来定义函数和类。
    ```c++
    // 函数模版
    template <typename T>
    inline T const& Max(T const& a, T const& b)
    {
        return a > b ? a : b;
    }

    // 类模板
    template <class T>
    class Stack 
    {
        private:
            vector<T> elems;
        public:
            void push(T const&);
            void pop();
            T top() const;
            bool empty() const{
                return elems.empty()
            }
    }
    
    template <class T>
    void Stack<T>::push (T const& elem) 
    { 
        // 追加传入元素的副本
        elems.push_back(elem);    
    }

    template <class T>
    void Stack<T>::pop () 
    { 
        if (elems.empty()) { 
            throw out_of_range("Stack<>::pop(): empty stack"); 
        }
        // 删除最后一个元素
        elems.pop_back();         
    }

    int main() 
    { 
        try { 
            Stack<int>         intStack;  // int 类型的栈 
            Stack<string> stringStack;    // string 类型的栈 
     
            // 操作 int 类型的栈 
            intStack.push(7); 
            cout << intStack.top() <<endl; 
     
            // 操作 string 类型的栈 
            stringStack.push("hello"); 
            cout << stringStack.top() << std::endl; 
            stringStack.pop(); 
            stringStack.pop(); 
        } 
        catch (exception const& ex) { 
            cerr << "Exception: " << ex.what() <<endl; 
            return -1;
        } 
    }
    ```
3. 预处理器
    1. 预处理器是一些指令，指示编译器在实际编译之前所需完成的预处理。
    2. 所有的预处理器指令都是以井号（#）开头，只有空格字符可以出现在预处理指令之前。预处理指令不是 C++ 语句，所以它们不会以分号（;）结尾。
    3. define预处理器
        ```c++
        #define MIN(a,b) (a<b ? a : b)     
        int main ()
        {
           int i, j;
           i = 100;
           j = 30;
           cout <<"较小的值为：" << MIN(i, j) << endl;
         
            return 0;
        }
        ```
    4. 条件编译
        ```c++
        #if 0
           不进行编译的代码
        #endif
        #define DEBUG
        #ifdef DEBUG
           cerr <<"Trace: Inside main function" << endl;
        #endif

        // # 和 ## 预处理运算符在 C++ 和 ANSI/ISO C 中都是可用的。# 运算符会把 replacement-text 令牌转换为用引号引起来的字符串。
        #define MKSTR( x ) #x
        cout << MKSTR(HELLO C++) << endl;  // 返回字符串 HELLO C++

        // 拼接
        #define concat(a, b) a ## b
        int xy = 100;
        cout << concat(x, y);  // 等价于cout << xy;
        ```
4. 多线程
    ```
    #include <iostream>
    // 必须的头文件
    #include <pthread.h>
     
    using namespace std;
     
    #define NUM_THREADS 5
     
    // 线程的运行函数
    void* say_hello(void* args)
    {
        cout << "Hello Runoob！" << endl;
        return 0;
    }
     
    int main()
    {
        // 定义线程的 id 变量，多个变量使用数组
        pthread_t tids[NUM_THREADS];
        for(int i = 0; i < NUM_THREADS; ++i)
        {
            //参数依次是：创建的线程id，线程参数，调用的函数，传入的函数参数
            int ret = pthread_create(&tids[i], NULL, say_hello, NULL);
            if (ret != 0)
            {
               cout << "pthread_create error: error_code=" << ret << endl;
            }
        }
        //等各个线程退出后，进程才结束，否则进程强制结束了，线程可能还没反应过来；
        pthread_exit(NULL);
    }
    ```
## C++ 标准库


## C++ 标准模版库
1. C++ 标准模板库的核心包括以下三个组件：
    1. 容器
        容器是用来管理某一类对象的集合。C++ 提供了各种不同类型的容器，比如 deque、list、vector、map 等。
    2. 算法
        算法作用于容器。它们提供了执行各种操作的方式，包括对容器内容执行初始化、排序、搜索和转换等操作。
    3. 迭代器
        迭代器用于遍历对象集合的元素。这些集合可能是容器，也可能是容器的子集。
2. 容器
    1. vector-数组，又称变长数组，
        ```c++
        #include <vector>
        #include <algorithm>
        // 在GCC的实现中，vector扩容是2倍扩容的

        vector<int> v;  // 定义一个vector，其中的元素为int类型
        vector<int> v[N];  // 定义一个vector数组，其中有N个vector
        vector<int> v(len);  // 定义一个长度为len的vector，初始化每个元素为0
        vector<int> v(len, x);  // 定义一个长度为len的vector，初始化每个元素为x
        vector<int> v2(v1);  // 用v1给v2赋值，v1的类型为vector
        vector<int> v2(v1.begin(), v1.begin() + 3);  // 将v1中第0~2三个元素赋值给v2
        vector<int> v = { 1, 2, 3 };  // 初始化vector，v:{1, 2, 3}

        vector<int>::iterator it = v.begin();  // 定义vector的迭代器，指向begin()
        v.push_back(4);  // 在vector的尾部插入元素4，v:{1, 2, 3, 4}
        v.pop_back();  // 删除vector的最后一个元素，v:{1, 2, 3}
        lower_bound(v.begin(), v.end(), 4);  // 返回第一个大于等于4的元素的迭代器
        upper_bound(v.begin(), v.end(), 4);  // 返回第一个大于4的元素的迭代器
        v.front();  // 返回vector中的第一个元素
        v.back();  // 返回vector中的最后一个元素
        v.begin();  // 返回vector第一个元素的迭代器
        v.end();  // 返回vector最后一个元素后一个位置的迭代器
        v.size();  // 返回vector中元素的个数
        v.empty();  // 返回vector是否为空，若为空则返回true否则返回false
        v.clear();  // 清空vector
        v.erase(v.begin());  // 删除迭代器it所指向的元素，即删除第一个元素
        v.erase(v.begin(), v.begin() + 2);  // 删除区间[v.begin(), v.begin() + 2)的所有元素
        v.insert(v.begin(), 1);  // 在迭代器it所指向的位置前插入元素1，返回插入元素的迭代器

        // 根据下标进行遍历
        for (int i = 0; i < v.size(); i++)
            cout << v[i] << ' ';
        // 使用迭代器遍历
        for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
            cout << *it << ' ';
        // for_each遍历(C++11)
        for (auto x : v)
            cout << x << ' ';
        ```
    2. stack-栈，后进先出
        ```c++
        #include <stack>
        // 除了最顶端以外，没有任何方法可以存取，stack不允许有遍历行为

        stack<int> stk;  // 定义一个stack，其中元素的类型为int
        stack<int> stk[N];  // 定义一个stack数组，其中有N个stack

        stk.push(x);  // 在stack中插入元素x
        stk.pop();  // 弹出stack的栈顶元素
        stk.top();  // 返回stack的栈顶元素
        stk.size();  // 返回stack中元素的个数
        stk.empty();  // 返回stack是否为空，若为空则返回true否则返回false
        ```
    3. string-字符串
        ```c++
        #include <string>
        #include <algorithm>
        // string和vector<char>在数据结构、内存管理等方面都是相同的
        // string可以使用c_str()函数转换为C风格的字符串
        
        string str;  // 定义一个空的字符串
        string str[N];  // 定义一个string数组，其中有N个string
        string str(5, 'a');  // 使用5个字符'a'初始化
        string str("abc");  // 使用字符串初始化
        string str = {'a','b','c'};  // 字符常量初始化，str="123"

        str.push_back('d');  // 在string尾部插入字符，"abcabcd"
        str.pop_back();  // 删除string尾部的字符，"abcabc"
        str.length();  // 返回string中字符的个数
        str.size();  // 作用与length()相同
        str.empty();  // 返回string是否为空，若为空返回true否则返回false
        str.substr(1);  // 返回string中从下标为1开始至末尾的子串，"bcabc"
        str.substr(0, 2);  // 返回string中从下标为0开始长度为2的子串，"ab"
        str.insert(1, 2, 'x');  // 在下标为1的字符前插入2个字符'x'，"axxbcabc"
        str.insert(1, "yy");  // 在下标为1的字符前插入字符串"yy"，"ayyxxbcabc"
        str.erase(1, 4);  // 删除从位置1开始的4个字符，"abcabc"
        str.find('b');  // 返回字符'b'在string中第一次出现的位置，返回1，若不存在则返回-1
        str.find('b', 2);  // 返回从位置2开始字符'b'在string中第一次出现的位置，返回4
        str.find("bc");  // 同上，返回字符串第一次出现的位置，返回1，若不存在则返回-1
        str.find("bc", 2);  // 返回4
        str.rfind('b');  // 反向查找，原理同上，返回4，若不存在则返回-1
        str.rfind('b', 3);  // 返回1
        str.rfind("bc");  // 返回4，若不存在则返回-1
        str.rfind("bc", 3);  // 返回1
        stoi(str);  // 返回str的整数形式
        to_string(value);  // 返回value的字符串形式，value为整型、浮点型等
        str[0];  // 用下标访问string中的字符

        // string中erase()与remove()的用法
        string str1, str2, str3, str4, str5;
        str1 = str2 = str3 = str4 = str5 = "I love AcWing! It's very funny!";
        str1.erase(15);  // 删除[15,end())的所有元素，"I love AcWing!"
        str2.erase(6, 11);  // 从第6个元素(包括)开始往后删除11个元素，"I love's very funny!"
        str3.erase(str3.begin() + 2);  // 删除迭代器所指的元素，"I ove AcWing! It's very funny!"
        str4.erase(str4.begin() + 7, str4.end() - 11);  // 删除[str4.begin()+7,str4.end()-11)的所有元素，"I love very funny!"
        str5.erase(remove(str5.begin(), str5.end(), 'n'), str5.end());  // 删除[str5.begin(),str5.end())中所有字符'n'，"I love AcWig! It's very fuy!"
        // remove是algorithm里的
        ```
    4. queue/priirity_queue-队列/优先队列
        ```c++
        #include <queue>
        // queue 又称队列，是一种先进先出（First In First Out，FIFO）的数据结构
        // priority_queue又称优先队列，同样定义在<queue>头文件中，与queue不同的地方在于我们可以自定义其中数据的优先级，优先级高的排在队列前面，优先出队。
        // priority_queue的本质是用堆实现的，默认是大根堆。

        queue<int> que;  // 定义一个queue，其中元素的类型为int
        queue<int> que[N];  // 定义一个queue数组，其中有N个queue
        priority_queue<int> bigHeap;  // 定义一个大根堆
        priority_queue<int, vector<int>, less<int> > bigHeap;  // 定义一个大根堆
        priority_queue<int, vector<int>, greater<int> > smallHeap;  // 定义一个小根堆

        que.push(x);  // 在queue的队尾插入元素x
        que.pop();  // 出队queue的队头元素
        que.front();  // 返回queue的队头元素
        que.back();  // 返回queue的队尾元素
        que.size();  // 返回queue中元素的个数
        que.empty();  // 返回queue是否为空，若为空则返回true否则返回false
        bigHeap.top();  // 返回priority_queue的队头元素
        ```
    5. deque-双端队列
        ```c++
        #include <deque>
        // vector容器是单向开口的连续内存空间，deque则是一种双向开口的连续线性空间。
        // vector也可以在头尾两端插入元素，但是在其头部进行插入操作效率很低。
        // vector最大的差异一是在于deque允许使用常数项时间在头部进行元素的插入和删除操作，二是在于deque没有容量的概念，因为它是动态的以分段连续空间组合而成，随时可以增加一段新的空间并链接起来。

        deque<int> deq;  // 定义一个deque，其中的元素为int类型
        deque<int> deq[N];  // 定义一个deque数组，其中有N个deque
        deque<int> deq(len);  // 定义一个长度为len的deque
        deque<int> deq(len, x);  // 定义一个长度为len的deque，初始化每个元素为x
        deque<int> deq2(deq1);  // 用deq1给v2赋值，deq2的类型为deque
        deque<int> deq2(deq1.begin(), deq1.begin() + 3);  // 将deq1中第0~2三个元素赋值给deq2
        deque<int> deq = { 1, 2, 3 };  // 初始化vector，v:{1, 2, 3}
        deque<int>::iterator it = deq.begin();  // 定义vector的迭代器，指向begin()
        deq.push_back(4);  // 在deque的尾部插入元素4，v:{1, 2, 3, 4}
        deq.pop_back();  // 删除deque的尾部元素，v:{1, 2, 3}
        deq.push_front(4);  // 在deque的头部插入元素4，v:{4, 1, 2, 3}
        deq.pop_front();  // 删除deque的头部元素，v:{1, 2, 3}
        deq.size();  // 返回deque中元素的个数
        deq.empty();  // 返回deque是否为空，若为空则返回true否则返回false
        deq.front();  // 返回deque中的第一个元素
        deq.back();  // 返回deque中的最后一个元素
        deq.begin();  // 返回deque第一个元素的迭代器
        deq.end();  // 返回deque最后一个元素后一个位置的迭代器
        deq.clear();  // 清空deque
        deq.erase(deq.begin());  // 删除迭代器it所指向的元素，即删除第一个元素
        deq.erase(deq.begin(), deq.begin() + 2);  // 删除区间[v.begin(), v.begin() + 2)的所有元素
        deq.insert(deq.begin(), 1);  // 在迭代器it所指向的位置前插入元素1，返回插入元素的迭代器
        ```
    6. map/multimap
        ```c++
        #include <utility>      // std::pair
        #include <map>
        // map和multimap的底层实现机制都是红黑树。
        // map所有的元素都是pair，同时拥有键值和实值（即(key, value)对），
        // 所有的元素都会根据元素的键值自动排序。
        // map不允许两个元素有相同的键值。multimap和map的操作类似，唯一区别是multimap的键值允许重复。

        map<string, int> mp;  // 定义一个将string映射成int的map
        map<string, int> mp[N];  // 定义一个map数组，其中有N个map
        multimap<string, int> mulmp;  // 定义一个将string映射成int的multimap
        multimap<string, int> mulmp[N];  // 定义一个multimap数组，其中有N个multimap
        // multimap由于可以存在相同key，所有没有[]取key对应value值的方法

        mp["abc"] = 3;  // 将"abc"映射到3
        mp["ab"]++;  // 将"ab"所映射的整数++
        mp.insert(make_pair("cd", 2));  // 插入元素
        mp.insert({ "ef", 5 });  // 同上
        mp.size();  // 返回map中元素的个数
        mp.empty();  // 返回map是否为空，若为空返回true否则返回false
        mp.clear();  // 清空map
        mp.erase("ef");  // 清除元素{"ef", 5}
        mp["abc"];  // 返回"abc"映射的值
        mp.begin();  // 返回map第一个元素的迭代器
        mp.end();  // 返回map最后一个元素后一个位置的迭代器
        mp.find("ab");  // 返回第一个键值为"ab"的迭代器，若不存在则返回mp.end()
        mp.find({ "abc", 3 });  // 返回元素{"abc", 3}的迭代器，若不存在则返回mp.end()
        mp.count("abc");  // 返回第一个键值为"abc"的元素数量1，由于map元素不能重复因此count返回值只有0或1
        mp.count({ "abc", 2 });  // 返回第一个键值为"abc"的元素数量1，注意和find不一样，count只判断第一个键值
        mp.lower_bound("abc");  // 返回第一个键值大于等于"abc"的元素的迭代器，{"abc", 3}
        mp.upper_bound("abc");  // 返回第一个键值大于"abc"的元素的迭代器，{"cd", 2}

        // 使用迭代器遍历
        for (map<string, int>::iterator it = mp.begin(); it != mp.end(); it++)
            cout << (*it).first << ' ' << (*it).second << endl;
        // for_each遍历(C++11)
        for (auto x : mp)
            cout << x.first << ' ' << x.second << endl;
        // 扩展推断范围的for_each遍历(C++17)
        for (auto &[k, v] : mp)
            cout << k << ' ' << v << endl;
        ```
    7. set/mutiset-集合
        ```c++
        #include <set>
        // 元素是有序且不重复的。
        // multiset允许有重复元素。
        // set和multiset的底层实现都是红黑树。

        set<int> st;  // 定义一个set，其中的元素类型为int
        set<int> st[N];  // 定义一个set数组，其中有N个set
        multiset<int> mulst;  // 定义一个multiset
        multiset<int> mulst[N];  // 定义一个multiset数组，其中有N个multiset
        
        st.insert(5);  // 插入元素5
        st.insert(6);  // 同上
        st.insert(7);  // 同上
        st.size();  // 返回set中元素的个数
        st.empty();  // 返回set是否为空，若为空返回true否则返回false
        st.erase(6);  // 清除元素6
        st.begin();  // 返回set第一个元素的迭代器
        st.end();  // 返回set最后一个元素后一个位置的迭代器
        st.clear();  // 清空set
        st.find(5);  // 返回元素5的迭代器，若不存在则返回st.end()
        st.count(5);  // 返回元素5的个数1，由于set元素不会重复，因此count返回值只有0或1
        st.lower_bound(5);  // 返回第一个键值大于等于5的元素的迭代器，返回元素5的迭代器
        st.upper_bound(5);  // 返回第一个键值大于5的元素的迭代器，返回元素7的迭代器
        ```
    8. unordered_map/unordered_set
        ```c++
        #include <unordered_map>
        #include <unordered_set>

        // 无序，采用的是hash表结构，拥有快速检索的功能。
        // unordered_map/unordered_set 增删改查的时间复杂度为O(1)
        // 不支持lower_bound()/upper_bound()函数
        // map/set增删改查的时间复杂度为O(logn)

        unordered_set<int> st;  // 定义一个unordered_set，其中的元素类型为int
        unordered_set<int> st[N];  // 定义一个unordered_set数组，其中有N个unordered_set
        unordered_map<int, int> mp;  // 定义一个unordered_map
        unordered_map<int, int> mp[N];  // 定义一个unordered_map数组，其中有N个unordered_map
        
        st.insert(5);  // 插入元素5
        st.insert(6);  // 同上
        st.insert(7);  // 同上
        st.size();  // 返回unordered_set中元素的个数
        st.empty();  // 返回unordered_set是否为空，若为空返回true否则返回false
        st.erase(6);  // 清除元素6
        st.find(5);  // 返回元素5的迭代器，若不存在则返回st.end()
        st.count(5);  // 返回元素5的个数，由于unordered_set元素不会重复，因此count返回值只有0或1
        st.begin();  // 返回unordered_set第一个元素的迭代器
        st.end();  // 返回unordered_set最后一个元素后一个位置的迭代器
        st.clear();  // 清空unordered_set
        mp.insert(make_pair(1, 2));  // 插入元素{1, 2}
        mp.insert({ 3, 4 });  // 同上
        mp.size();  // 返回unordered_map中元素的个数
        mp.empty();  // 返回unordered_map是否为空，若为空返回true否则返回false
        mp.erase(3);  // 清除元素{3, 4}
        mp.find(1);  // 返回第一个键值为1的迭代器，若不存在则返回mp.end()
        mp.count(1);  // 返回第一个键值为1的元素数量，由于unordered_map元素不能重复因此count返回值只有0或1
        mp.begin();  // 返回unordered_map第一个元素的迭代器
        mp.end();  // 返回unordered_map最后一个元素后一个位置的迭代器
        mp.clear();  // 清空unordered_map

        // 使用迭代器遍历
        for (unordered_set<int>::iterator it = st.begin(); it != st.end(); it++)
            cout << (*it) << ' ';
        // for_each遍历(C++11)
        for (auto x : st)
            cout << x << ' ';

        // 使用迭代器遍历
        for (unordered_map<int, int>::iterator it = mp.begin(); it != mp.end(); it++)
            cout << (*it).first << ' ' << (*it).second << endl;
        // for_each遍历(C++11)
        for (auto x : mp)
            cout << x.first << ' ' << x.second << endl;
        // 扩展推断范围的for_each遍历(C++17)
        for (auto &[k, v] : mp)
            cout << k << ' ' << v << endl;
        ```


## STL 算法
```c++
#include <algorithm>

int a[5] = { 1, 2, 3, 4, 5 };
int b[5] = { 0 };

// 排序算法
sort(a, a + 5);  // 将区间[0, 5)内元素按字典序从小到大排序
sort(a, a + 5, greater<int>());  // 将区间[0, 5)内元素按字典序从大到小排序
reverse(a, a + 5);  // 将区间[0, 5)内元素翻转

// 查找与统计算法
find(a, a + 5, 3);  // 在区间[0, 5)内查找等于3的元素，返回迭代器，若不存在则返回end()
binary_search(a, a + 5, 2);  // 二分查找区间[0, 5)内是否存在元素2，若存在返回true否则返回false
count(a, a + 5, 3);  // 返回区间[0, 5)内元素3的个数

// 可变序列算法
copy(a, a + 2, a + 3);  // 将区间[0, 2)的元素复制到以a+3开始的区间，即[3, 5)
replace(a, a + 5, 3, 4);  // 将区间[0, 5)内等于3的元素替换为4
fill(a, a + 5, 1);  // 将1写入区间[0, 5)中(初始化数组函数)
int* pend = unique(a, a + 4);  // 将相邻元素间的重复元素全部移动至末端，返回去重之后数组最后一个元素之后的地址
int* pend = remove(a, a + 4, 3);  // 将区间[0, 5)中的元素3移至末端，返回新数组最后一个元素之后的地址
for (int* p = c; p != pend; p++)
    cout << *p << ' ';
cout << endl;
```


## REFERENCE
[官网](https://cplusplus.com/)
[菜鸟教程-c++](https://www.runoob.com/cplusplus/cpp-tutorial.html)
[算法竞赛C++ STL容器、算法、迭代器详解](https://www.acwing.com/blog/content/10558/)