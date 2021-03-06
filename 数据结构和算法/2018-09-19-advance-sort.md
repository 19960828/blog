---
title: 高级排序算法实现与优化
date: 2018-09-19
categories: 数据结构和算法
tags:
- 算法
- C与C++
permalink: "2018-09-19-advance-sort"
---

> 本文主要介绍高级排序算法中的归并排序和快速排序。他们运用了分支思想，并且大多通过递归来实现。对于归并排序，分为自上向底和自底向上排序。对于快速排序，有常见的二路快排和系统级常用的三路快速排序。

我的电脑的配置是：12G、2.9GHz、I5处理器、Win10）。在DevC编译器下去排序 1个亿 的数据量。

优化后的快排是 27s。如果不进行任何优化，300s 到 350s，性能提升还是很显著的。

<!-- more -->

## 1. 谈谈高级排序

本文主要介绍高级排序算法中的归并排序和快速排序。他们运用了分支思想，并且大多通过递归来实现。

对于归并排序，分为自上向底和自底向上排序。对于快速排序，有常见的二路快排和系统级常用的三路快速排序。

## 2. 归并排序

### 2.1 设计和分析

在算法思想上：归并排序是分治法，所以需要等分数组，并且逐个完成排序，然后再合并在一起。而因为等分，所以树结构是平衡的（快速排序就不一定，需要进一步优化）。

在空间使用上：归并排序需要开启辅助空间，所以，在算法效率上自然比不上快速排序。

### 2.2 自顶向下的归并

#### 2.2.1 三处优化

第一处优化是关于选取中间索引值的问题。显然，使用`(l + r) / 2`可能会造成溢出。

所以，此处应该是：`int mid = l + (r - l) / 2;`。

同时，不能是 `r + (l - r) / 2` 。 比如: l = 0, r = 1 的时候，这条式子的结果和`(l + r) / 2`不同。**因为 c++的自动向下取整**。

第二处优化是关于递归到底层的时候，比如被切分出来的数据长度小于 15，此时可以使用插入排序来优化。

第三处优化是当归并前，先判断前一部分数据的最后一个值和后一部分数据最后一个值的大小关系，再决定是否优化。

#### 2.2.2 代码实现

实现中比较困难的部分是归并过程，在处理边界的时候，需要特别注意。示意图如下：

![](/images/算法与数学/高级排序算法实现与优化/1.png)

```cpp
// 将 arr[l, ... , mid] 和 arr[mid, ... , r]两个部分进行归并
template <typename T>
void __merge(T arr[], int l, int mid, int r) {
    T* aux = new T[r - l + 1]; // 辅助空间
    for(int i = l; i<=r; i++) {
        aux[i - l] = arr[i];
    }
    int i = l, j = mid + 1;
    for(int k = l; k <= r; k++) {
        if( i > mid) {
            arr[k] = aux[j - l];
            j++;
        } else if (j > r) {
            arr[k] = aux[i - l];
            i++;
        } else if(aux[i - l] < aux[j - l]) {
            arr[k] = aux[i - l];
            i++;
        } else {
            arr[k] = aux[j - l];
            j++;
        }
    }
    delete[] aux;
}

// 递归使用归并排序, 对arr[l, ... , r]的范围的数据进行排序
template <typename T>
void __mergeSort(T arr[], int l, int r) {
    // 递归到底层的情况
    if( r - l <= 15 ){
        SortBase::insertionSort(arr, l, r);
        return;
    }

    int mid = l + (r - l)/2;

    // 防止溢出：同时，不能是 r + (l - r) / 2 。 比如: l = 0, r = 1

    __mergeSort(arr, l, mid);
    __mergeSort(arr, mid + 1, r);
    if(arr[mid] > arr[mid + 1]) {
        __merge(arr, l, mid, r);
    }
}

template <typename T>
void mergeSort(T arr[], int n) {
    __mergeSort(arr, 0, n-1);
}
```

### 2.3 自底向上的归并

自底向上的归并排序不如自顶向下的归并好理解。但是可以不写递归函数，并且可以访问数组索引。

有道面试题：对于一个链表（每个节点存储一个数据），要求在 O(NlogN)时间内完成排序，并且使用常数级别的空间。利用的就是

先看自底向上的归并的实现，就会有思路了：

```cpp
template <typename T>
void mergeSortBU(T arr[], int n) {
    int min_size = -1;
    for(int sz = 1; sz <= n; sz += sz) {
        for(int i = 0; i + sz < n; i += sz + sz) { // i + sz < n: 保证第二部分的数组存在，并且 i + sz -1 不越界
//                 对 arr[i, ... ,i+sz-1] 和 [i+sz, ... ,i+2*sz-1] 进行归并
            if(arr[i + sz - 1] > arr[i + sz]) {
                __merge(arr, i, i + sz -1, min(i + sz + sz -1, n-1));
            }
        }
    }
}
```

这段代码是针对数组的，如果针对链表，只需要移动指针即可，而空间也可以新开一个指针空间做原地操作。[>>>请看这篇博文](https://www.cnblogs.com/bakari/p/4009526.html)

## 3. 快速排序

### 3.1 二路快速排序

#### 3.1.1 三处优化

第一处优化是关于递归到底层的时候，比如被切分出来的数据长度小于 15，此时可以使用插入排序来优化。

第二处优化是：随机选择标定元素。一般的快排选定的是最左边的元素作为标定元素，排序后的数组标定元素移动到应该所处的位置，其前面是比他小的元素，后面是比他大的元素。

然而，无法保证快速排序递归树的平衡度。比如：`2, 2, 2,..., 2, 1` 近乎有序且有大量重复的数组。如果选定最左边，**快速排序就会退化到 O(N\*N)**。如下图所示：

![](/images/算法与数学/高级排序算法实现与优化/2.png)

**优化方法是：随机选择一个元素，与第一个元素交换后作为标定元素。这样可以保证递归树深度的期望值是 logN。**

第三处优化是针对数组中有大量重复元素的情况。在执行`partition`操作的时候，判断是否移动交换元素的条件算上`=`即可。（具体可以看之后代码）

#### 3.1.2 代码实现

```cpp
template <typename T>
int __partition2(T arr[], int l, int r) {
    swap(arr[l], arr[rand()%(r - l + 1) + l]); // 随机化防止树不平衡
    T v = arr[l];

//        arr[l+1, ... , i) <= v; arr(j, ... , r] >= v
    int i = l + 1, j = r;
    while(true) {
        while(i <= r && arr[i] < v) i++;
        while(j >= l+1 && arr[j] > v) j--;
        if(i > j) break;
        swap(arr[i], arr[j]);
        i ++;
        j --;
    }
    swap(arr[l], arr[j]);

    return j;
}


template <typename T>
void __quickSort(T arr[], int l, int r) {
    if(r - l <= 15) {
        SortBase::insertionSort(arr, l, r);
        return;
    }
    int p = __partition2(arr, l, r);
    __quickSort(arr, l, p-1);
    __quickSort(arr, p+1, r);
}

template <typename T>
void quickSort(T arr[] ,int n) {
    srand(time(NULL));
    __quickSort(arr, 0, n-1);
}
```

### 3.2 三路快速排序

三路排序和二路不同的是：**将相同的元素单独放在一起，每次递归不再参与排序。**

代码中各个边界变量的含义如下图所示：

![](/images/算法与数学/高级排序算法实现与优化/3.png)

代码实现：

```cpp
template <typename T>
void __quickSort3Ways(T arr[], int l, int r) {
    if(r - l <= 15) {
        SortBase::insertionSort(arr, l, r);
        return;
    }

    swap(arr[l], arr[rand() % (r - l + 1) + l]);
    T v = arr[l];

    int lt = l; // arr[l + 1, ... , lt] < v
    int gt = r + 1; // arr[gt, ... ,r] > v
    int i = l + 1; // arr[lt + 1, ... , i) == v
    while( i < gt ) {
        if(arr[i] < v) {
            swap(arr[i], arr[lt + 1]);
            lt ++;
            i ++;
        } else if(arr[i] > v) {
            swap(arr[i], arr[gt - 1]);
            gt --;
        } else {
            i ++;
        }
    }
    swap(arr[l], arr[lt]);
    __quickSort3Ways(arr, l, lt-1);
    __quickSort3Ways(arr, gt, r);
}
```

## 4. 性能测试

### 4.1 测试随机数据

为了保证普适性，先测试大量随机数据的算法表现：

```cpp
#include <iostream>
#include "SortHelper.h"
#include "SortBase.h"
#include "SortAdvance.h"

using namespace std;

int main() {
    int n = 100000, left = 0, right = n;
    int *arr = SortTestHelper::generateRandomArray<int>(n, left, 5);
    int *brr = SortTestHelper::copyArray<int>(arr, n);
    int *crr = SortTestHelper::copyArray<int>(arr, n);
    int *drr = SortTestHelper::copyArray<int>(arr, n);

    SortTestHelper::testSort<int>(brr, n, SortAdvance::mergeSort<int>, "merge sort");
    SortTestHelper::testSort<int>(crr, n, SortAdvance::mergeSortBU<int>, "merge sort from bottom to up");
    SortTestHelper::testSort<int>(drr, n, SortAdvance::quickSort<int>, "quick sort");
    return 0;
}
```

结果如下：
![](/images/算法与数学/高级排序算法实现与优化/4.png)

对于特殊数据，例如含有大量重复元素的数组：

```cpp
// ...
int *arr = SortTestHelper::generateRandomArray<int>(n, left, 5);
// ...
```

结果如下图所示：
![](/images/算法与数学/高级排序算法实现与优化/5.png)

### 4.2 1亿数据量测试

因为使用的 CLion 做了安全限制，所以当数组大小开到 100w 的时候，就报出堆栈错误。

换用了 DevC 来跑 1 亿的数据，快排本来是 17s（忘记截图了），再跑就是 27s，如下图所示：

![](/images/算法与数学/高级排序算法实现与优化/6.png)

大家可以在自己电脑跑一下百度百科的快排，就知道优化的作用了 :)

## 5. 感谢

本篇博客是总结于慕课网的[《学习算法思想 修炼编程内功》](https://coding.imooc.com/class/chapter/71.html)的笔记，liuyubobobo 老师人和讲课都很 nice，欢迎去买他的课程。
