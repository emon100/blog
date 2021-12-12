---
title: C++指针和引用的终极书写规范
url: 354.html
id: 354
categories:
  - 技术研究
tags:
  - C
  - C++
date: 2019-07-30 20:41:00
---
# 前言  

-----

我的代码规范：*，&都应该放到变量名前面而不是类型名后，这样子在连续定义时有好处。

`int* a;`✖

`int *a;`✔

# 正文

-----

## 一：指针与引用

### 指针

先明确一点，如int \*a, a并不应该理解为指向int的指针，而应理解为int \*类型的指针，所以指针的类型其实应该如下:

```C
int * 
const int * 

//下面两个声明加了括号是由于编译器判断符号用了贪心法则，不加括号声明的意义会完全变化  

void (*)(int,double)

//函数指针声明范例

//所以void (*ptr)(int,int)这种是声明一个返回值为void,参数为int，double的**函数指针**ptr 

//而void *ptr(int,int)声明了一个返回值为void *的**函数**  

int (*)[10] //指向int[10]为基本单位数组的指针（行指针）

//下面是一个恐怖的**函数指针+强制类型转换**的例子，摘自《C陷阱与缺陷》:

(void (*)())0; //把0这个地址转换为void ()类型函数的地址
```

### 引用

`int &p;//int类型的引用`

`int *&p;//int *类型指针的引用`

## 二：const带来了难题

明确了这些，现在加上const修饰符。如何判断常量指针和指向常量的指针还有常引用呢。  

### 指针  

其实只需要注意看const是在*的前面还是后面。  

const在*前面的情况，如

`const int *a` 

`int const *a`

其实是一个东西，都是**指向常量**const int 的指针**变量**，也就是说指针变量类型是 const int *.注意！这个时候指针变量是可以指向不同的位置的。

const在*后面的情况，如int * const a，意思是这是一个int *类型的指针**常量**。这个指针在初始化后不可修改。

### 引用  

现在如果是引用，其实是一样的。我们要明确有两件事：  

1.只有**指针的引用**而没有**引用的指针**。只有常引用没有引用常量  

2.const int &a 与int const &a等价，因为反正引用也只能指向一个东西，这个const只是用来说明不能用引用修改原来的值。

我们是对某个类型声明引用变量，故&前为这个引用的原本类型+可能存在的修饰符const  

按照上述规则来看看如何声明一个const int *类型的指针的引用
```C
const int *a; const int *&b=a; //声明完成 
//这个引用意味着可以通过b修改指针a的值
```

### 常引用

const int * const &b=a;//OK 

int const* const &b=a;//OK int * const &b=a//NO!!这个意思是一个指向int *类型的常引用。不能用来引用const int *类型! 

const const int * &b=a;//NO！！！！两个const不能放一起

const类型的普通引用和常引用很容易弄混，但我们只需要明白一件事就好：int const &a 与const int &a等价（const和类型位置没什么关系），而编译器又读不懂连续的两个const。所以我们的方法是：  

1.如果发现引用的类型是const int*。 

2.就放置const到类型后面使得不会连续出现两个const  

所以我们在这里先放const int *(引用类型)再放const(表明常引用)。  

总结：指针和引用书写规范属于很trivial的东西，我关注纯粹是因为应试，结果研究了这么多，唉。

参考资料: [https://www.cnblogs.com/chezxiaoqiang/archive/2012/09/24/2700241.html](https://www.cnblogs.com/chezxiaoqiang/archive/2012/09/24/2700241.html) (强烈推荐)