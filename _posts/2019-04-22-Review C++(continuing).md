---
layout: post
title:  "Review C++(continuing)"
date:   2019-04-24
excerpt: "Some tips on C++ programming"
tag:
- C++
---

<center><H2><b>Review C++</b></H2></center><br>
### C++ 注意点记录：

**数组与指针：**

在一定程度上，数组与指针是可以对等的，但并不是完全一样。

数组在想要获得元素长度的时候可以根据 sizeof(array) / sizeof(array[0]) 进行获得，而指针则不行，因为指针在32位上 sizeof(pointer) == 4；64位上sizeof(pointer) == 8; 这是sizeof在使用过程当中需要注意的问题。

当然，如果用malloc 进行内存空间申请的话，可以用sizeof进行获取分配的数据长度，通过除以元素长度可获得元素个数，因为malloc会分配额外空间进行存储数据大小。

（malloc 语法：        char *mPtr = NULL; mPtr = (char *)malloc( size * sizeof(char));  ）


<br><br>
**C++类中的const成员函数**

> 也就是基于const的重载

``` C++
Class A {
    int function ();
    int function () const;
};

```

在A类当中，函数function发生了重载。该重载调用的结果为，只有A的const对象才会调用const声明的函数，A类的非const对象在通常情况下调用的是非const版本的那个函数。


**设计目的**：该重载使得C++中const对象可以调用对应的const成员函数。一个对象如果被const声明，那么他将不能被修改，而如果可以调用非const的成员函数那么就不能保证const对象的不变性。因此，对于需要有const对象的类就需要为他提供const成员函数。非const对象可以调用const成员函数和非const成员函数。

**原因**：按照函数重载的定义，函数名相同而形参表有本质不同的函数称为重载。在类中，由于隐含的this形参的存在，const版本的function函数使得作为形参的this指针的类型变为指向const对象的指针，而非const版本的使得作为形参的this指针就是正常版本的指针。此处是发生重载的本质。重载函数在最佳匹配过程中，对于const对象调用的就选取const版本的成员函数，而普通的对象调用就选取非const版本的成员函数。

在非const对象调用const成员函数的时候会报错。




