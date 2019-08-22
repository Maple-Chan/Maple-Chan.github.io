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

​		在一定程度上，数组与指针是可以对等的，但是今天（2019-4-22）发现了一个不同。

​		数组在想要获得元素长度的时候可以根据 sizeof(array) / sizeof(array[0]) 进行获得，而指针则不行，因为指针在32位上 sizeof(pointer) == 4；64位上sizeof(pointer) == 8; 这是sizeof在使用过程当中需要注意的问题。

​		当然，如果用malloc 进行内存空间申请的话，可以用sizeof进行获取分配的数据长度，通过除以元素长度可获得元素个数，因为malloc会分配额外空间进行存储数据大小。

​	（malloc 语法：        char *mPtr = NULL; mPtr = (char *)malloc( size * sizeof(char));  ）







