---
title: 类的相关知识点-1
date: 2024-11-16 00:00:00 +0800
categories: [Knowledge]
tags: [Class]
math: true
---

1 函数前加上 `inline` 表示内联函数

`inline`:

定义在类中的成员函数默认都是 **内联的**，如果在类定义时就在类内给出函数定义，那当然最好。如果在类中未给出成员函数定义，而又想内联该函数的话，那在类外要加上 `inline`，否则就认为不是内联的。

```cpp
class A
{
    public:void Foo(int x, int y) {  } // 自动地成为内联函数
}
```

关键字 `inline` 必须与函数定义体放在一起才能使函数成为内联，仅将 `inline` 放在函数声明前面不起任何作用。

如下风格的函数 `Foo` 不能成为内联函数：
```cpp
inline void Foo(int x, int y); // inline 仅与函数声明放在一起
void Foo(int x, int y){}
```
而如下风格的函数 `Foo` 则成为内联函数：
```cpp
void Foo(int x, int y);
inline void Foo(int x, int y) {} // inline 与函数定义体放在一起
```

所以说，`inline` 是一种"用于实现的关键字"，而不是一种"用于声明的关键字"。

`inline` 的使用是有所限制的，`inline` 只适合涵数体内代码简单的涵数使用，不能包含复杂的结构控制语句例如 `while`、`switch`，并且不能内联函数本身不能是直接递归函数（即，自己内部还调用自己的函数）。


2 类的概念

&nbsp; &nbsp; &nbsp; &nbsp; 1) 类的友元函数可以访问类的私有成员 对，友元函数就是为了干这个的

&nbsp; &nbsp; &nbsp; &nbsp; 2) 类的成员函数可以访问类的私有成员 对，类的成员函数啥都可以干

&nbsp; &nbsp; &nbsp; &nbsp; 3) 类的成员默认权限为公有 错，默认为 `private` 私有

&nbsp; &nbsp; &nbsp; &nbsp; 4) 对象是类的具体实例 对，类是一种定义了属性（数据成员）和方法（成员函数）的代码模板，它描述了如何创建对象。而对象是根据类的定义创建的实例，包含了类定义的属性和方法。简单来说，类像是一张蓝图，对象是根据蓝图制造出来的产品。

3 构造函数

&nbsp; &nbsp; &nbsp; &nbsp; 1) 构造函数不可以重载 错，可以通过传入参数区分重载的多个构造函数

&nbsp; &nbsp; &nbsp; &nbsp; 2) 构造函数要有返回类型 错，不用返回

&nbsp; &nbsp; &nbsp; &nbsp; 3) 构造的函数名与类名相同 对

&nbsp; &nbsp; &nbsp; &nbsp; 4) 构造函数不能有参数 错

4 `this` 指针实现 C++ 对`封装`的语法支持

5 对于一个类 `Base`，`g()` 为此类的共有成员函数，指针 `p` 指向该类的一个对象，则访问成员函数 `g()` 的语句为 `p->g();`

6 类模版的概念

&nbsp; &nbsp; &nbsp; &nbsp; 1) 类模板的使用实际上是将类模板实例化成一个具体的对象 错，从未具体

&nbsp; &nbsp; &nbsp; &nbsp; 2) 可以从类模板派生出新的类 对

&nbsp; &nbsp; &nbsp; &nbsp; 3) 模板的类型参数由关键字 `class` 或 `typename` 及其后的标识符组成 对

&nbsp; &nbsp; &nbsp; &nbsp; 4) 定义一个类模板以关键字 `template` 开始 对

函数可以定义函数模板，同样地，对于类来说，也可以定义一个类模板。

类模板是针对成员数据类型不同的类的抽象，它不是一个具体实际的类，而是一个类型的类，一个类模板可以生成多种具体的类。

类模板的定义格式如下所示：

```cpp
template < typename T >
class ClassName {}
```

类模板中的关键字含义与函数模板相同。需要注意的是，类模板的模板参数不能为空。一旦声明类模板，就可以用类模板的参数名声明类中的成员变量和成员函数，即在类中使用数据类型的地方都可以使用模板参数名来声明。

定义类模板示例代码如下所示：

```cpp
template < typename T >
class A
{
public:
    T a;
    T b;
    T func(T a, T b);
};
```

上述代码中，在类 `A` 中声明了两个T类型的成员变量 `a` 和 `b`，还声明了一个返回值类型为 `T` 并带两个 `T` 类型参数的成员函数 `func()`。

定义了类模板就要使用类模板创建对象以及实现类中的成员函数，这个过程其实也是类模板实例化的过程，实例化出的具体类称为模板类。

如果用类模板创建类的对象，例如，用上述定义的类模板 `A` 创建对象，则在类模板 `A` 后面加上一个 `<>`，并在里面表明相应的类型，示例代码如下所示：

```cpp
A<int> a;
```

这样类 `A` 中凡是用到模板参数的地方都会被 `int` 类型替换。如果类模板有多个模板参数，创建对象时，多个类型之间要用逗号分隔开。

7 抽象类的概念

&nbsp; &nbsp; &nbsp; &nbsp; 1) 抽象类不能实例化 对，语法上，抽象类不能被实例化，这是语法规定（没有纯虚函数的实现啊）。 强制实例化一个抽象类的代码，编译器会报错。 其次，从继承和多态思想的角度而言，抽象基类的目的是建立一个公共接口，建立公共接口的唯一原因是它能对于每个不同的子类有不同的表示。 它建立一个基本的格式，用来确定什么是对于所有派生类是公共的——除此之外，别无用途。

&nbsp; &nbsp; &nbsp; &nbsp; 2) 派生类必须实现纯虚函数才能被实例化 对，不然你这个接口怎么用

&nbsp; &nbsp; &nbsp; &nbsp; 3) 包含纯虚函数的类称为抽象类 对，定义

&nbsp; &nbsp; &nbsp; &nbsp; 4) 抽象基类中除了包含纯虚函数外，不能包含其他的普通成员函数 错，其他的也可以有

8 类 `A` 的拷贝构造函数的原型是 `A(const A&);`

9 下列哪种情况不调用拷贝构造函数

&nbsp; &nbsp; &nbsp; &nbsp; 1) 一个对象作为函数参数，以值传递的方式传入函数体

&nbsp; &nbsp; &nbsp; &nbsp; 2) 一个对象作为函数返回值，以值传递的方式从函数返回

&nbsp; &nbsp; &nbsp; &nbsp; 3) 一个对象用于给另外一个对象进行初始化

&nbsp; &nbsp; &nbsp; &nbsp; 4) 一个对象被定义为另一个对象的引用 错，引用不会调用

在C++中，下面三种对象需要调用拷贝构造函数（有时也称“复制构造函数”）：

一个对象作为函数参数，以值传递的方式传入函数体；

```cpp
void g_fun(CExample c)
{
    cout << "g_func" << endl;
}
int main()
{
    g_fun(A);
}
```

一个对象作为函数返回值，以值传递的方式从函数返回；

```cpp
CExample g_fun()
{
    CExample temp(0);
    return temp;
}
int main()
{
    g_fun();
}
```

一个对象用于给另外一个对象进行初始化（常称为赋值初始化）；

```cpp
CExample A(100);
CExample B = A;
```

如果在前两种情况不使用拷贝构造函数的时候，就会导致一个指针指向已经被删除的内存空间。对于第三种情况来说，初始化和赋值的不同含义是拷贝构造函数调用的原因。

10 类的继承

|↓继承方式 原有类型→|`public`|`protected`|`private`|
|-|-|-|-|
|`public`|`public`|`protected`|`x`|
|`protected`|`protected`|`protected`|`x`|
|`private`|`private`|`private`|`x`|

`public`: 可以被任意实体访问
`protected`: 只允许本类及**子类**的成员函数访问
`private`: 只允许本类的成员函数访问

11 代码函数比较短并且被频繁调用可以使用**内联函数**

12 继承和派生概念

 &nbsp; &nbsp; &nbsp; &nbsp; 1) 派生类的缺省继承方式是 `private` 对，默认都 `private`

 &nbsp; &nbsp; &nbsp; &nbsp; 2) 一个派生类可以是另一个类的基类 对，可以复合

 &nbsp; &nbsp; &nbsp; &nbsp; 3) 派生类没有继承基类的私有成员 错，有，但是不可见，可以通过基类提供的非 `private` 的函数接口访问

 &nbsp; &nbsp; &nbsp; &nbsp; 4) 派生类继承了基类的共有成员和保护成员 对

13 当要释放 `ptr` 指向的动态数组对象时，所使用的语句是 `delete[] ptr;`

指针指向一个内存单元

```cpp
char *ch = new ch;
delete ch;
```
指针指向一维数组

```cpp
int N, *p;     
cin >> N; 
p = new int [N]; 
for(int i = 0; i < N; i++) 
{ 
   cin >> *(p + i); // 等价于 cin >> p[i] 或 i[p]
}
delete[] p;
```

14 C++ 中实现输出一个换行符并刷新流功能的操控符是 `endl`

15 友元函数的概念

 &nbsp; &nbsp; &nbsp; &nbsp; 1) 友元函数不能访问类的私有成员 错，访问 `private` 这是友元存在的意义

 &nbsp; &nbsp; &nbsp; &nbsp; 2) 友元函数是类的成员函数 错，不是

 &nbsp; &nbsp; &nbsp; &nbsp; 3) 友元函数破坏了类的封装性和隐蔽性 对，因为友元可以访问 `private` 了

 &nbsp; &nbsp; &nbsp; &nbsp; 4) 友元函数是一种静态函数 错

我们可以使用 `static` 关键字来把类成员定义为静态的。当我们声明类的成员为静态时，这意味着无论创建多少个类的对象，静态成员都只有一个副本。

静态成员在类的所有对象中是共享的。如果不存在其他的初始化语句，在创建第一个对象时，所有的静态数据都会被初始化为零。**我们不能把静态成员的初始化放置在类的定义中**，但是可以在类的外部通过使用范围解析运算符 `::` 来重新声明静态变量从而对它进行初始化，如下面的实例所示。

```cpp
#include <iostream>
 
using namespace std;
 
class Box
{
   public:
      static int objectCount;
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0)
      {
         cout <<"Constructor called." << endl;
         length = l;
         breadth = b;
         height = h;
         // 每次创建对象时增加 1
         objectCount++;
      }
      double Volume()
      {
         return length * breadth * height;
      }
   private:
      double length;     // 长度
      double breadth;    // 宽度
      double height;     // 高度
};
 
// 初始化类 Box 的静态成员
int Box::objectCount = 0;
 
int main(void)
{
   Box Box1(3.3, 1.2, 1.5);    // 声明 box1
   Box Box2(8.5, 6.0, 2.0);    // 声明 box2
 
   // 输出对象的总数
   cout << "Total objects: " << Box::objectCount << endl;
 
   return 0;
}
```

当上面的代码被编译和执行时，它会产生下列结果：

```
Constructor called.
Constructor called.
Total objects: 2
```

静态成员函数

如果把函数成员声明为静态的，就可以把函数与类的任何特定对象独立开来。静态成员函数即使在类对象不存在的情况下也能被调用，静态函数只要使用类名加范围解析运算符 `::` 就可以访问。

静态成员函数只能访问**静态成员数据**、其他**静态成员函数**和**类外部的其他函数**。

静态成员函数有一个类范围，他们不能访问类的 `this` 指针。

静态成员函数与普通成员函数的区别：

> 静态成员函数没有 `this` 指针，只能访问静态成员（包括静态成员变量和静态成员函数）。
> 普通成员函数有 `this` 指针，可以访问类中的任意成员。


下面的实例有助于更好地理解静态成员函数的概念：

```cpp
#include <iostream>
 
using namespace std;
 
class Box
{
   public:
      static int objectCount;
      // 构造函数定义
      Box(double l=2.0, double b=2.0, double h=2.0)
      {
         cout <<"Constructor called." << endl;
         length = l;
         breadth = b;
         height = h;
         // 每次创建对象时增加 1
         objectCount++;
      }
      double Volume()
      {
         return length * breadth * height;
      }
      static int getCount()
      {
         return objectCount;
      }
   private:
      double length;     // 长度
      double breadth;    // 宽度
      double height;     // 高度
};
 
// 初始化类 Box 的静态成员
int Box::objectCount = 0;
 
int main(void)
{
  
   // 在创建对象之前输出对象的总数
   cout << "Inital Stage Count: " << Box::getCount() << endl;
 
   Box Box1(3.3, 1.2, 1.5);    // 声明 box1
   Box Box2(8.5, 6.0, 2.0);    // 声明 box2
 
   // 在创建对象之后输出对象的总数
   cout << "Final Stage Count: " << Box::getCount() << endl;
 
   return 0;
}
```

当上面的代码被编译和执行时，它会产生下列结果：

```
Inital Stage Count: 0
Constructor called.
Constructor called.
Final Stage Count: 2
```

16 编写程序将一个十进制数 $N$ 转化为八进制，要求：定义 `Septinary` 类（八进制类），来表示一个八进制的数及其相关操作，和十进制的数“逢十进一”类似，八进制的数定义为“逢八进一"。<br/> &nbsp;`Septinary` 类的数据成员说明如下：<br/> &nbsp; &nbsp; &nbsp; &nbsp; 1) `char *data;` 存放八进制数的每一位数，data[0]为最高位，且八进制的位数不超过 99 位。<br/> &nbsp; &nbsp; &nbsp; &nbsp; 2) `CreateSeptinary` 函数由构造函数调用，用于将作为参数传入的十进制数转化为八进制数，并将八进制数的每一位存入 `data`。

```cpp
#include <bits/stdc++.h>
class Septinary
{
    friend std::ostream& operator<<(std::ostream &os, Septinary x);
private:
    char data[100];
    int cnt;
    void CreateSeptinary(int N)
    {
        if(!N)
        {
            data[cnt ++] = '0';
            data[cnt] = '\0';
            return ;
        }
        while(N)
        {
            data[cnt ++] = N % 8 + '0';
            N /= 8;
        }
        std::reverse(data, data + cnt);
        data[cnt] = '\0';
    }
public:
    Septinary(int N) : cnt(0)
    {
        CreateSeptinary(N);
    }
    ~Septinary()
    {

    }
};

std::ostream& operator<<(std::ostream &os, Septinary x)
{
    os << x.data;
    return os;
}

signed main()
{
    int N; 
    std::cin >> N;
    Septinary x(N);
    std::cout << x << std::endl;
    return 0;
}
```