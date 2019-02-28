---
title: 测试数据之自动生成
date: 2018-09-19
categories: 数据结构和算法
tags:
- 自动化
- C与C++
permalink: "2018-09-19-auto-init-data"
---

> 最近在学习排序算法的时候，需要利用程序自动生成测试数据，代码和思路整理在这篇文章里面。

对于排序算法来说，自动生成测试数据的类型需要有以下几种：

1. `[a, b]`范围内的`n`个随机数据，比如：1、2、100、-1...
2. `n`个近乎有序的数据，比如：1、2、3、7、5、6、4...
3. `n`个近乎相同的数据，比如：1、1、1、2、2、2、2、2...

如果排序算法在这三种数据类型中的跑分都和理论分析一样，才能保证算法较好的稳健性。

<!-- more -->

> 最近在学习排序算法的时候，需要利用程序自动生成测试数据，代码和思路整理在这篇文章里面。

### 1. 设计思路

因为会被很多排序算法调用，所以，数据自动生成代码应该放在`.h`头文件中。为了防止命名冲突，函数被封装在“命名空间”中（代码中命名空间是: `SortTestHelper`）。

而对于排序来说，自动生成数据的类型需要有以下几种：

1. `[a, b]`范围内的`n`个随机数据，比如：1、2、100、-1...
2. `n`个近乎有序的数据，比如：1、2、3、7、5、6、4...
3. `n`个近乎相同的数据，比如：1、1、1、2、2、2、2、2...

除此之外，还需要数组浅拷贝、打印的函数，以及验证是否排序成功和测试排序时间的函数。

### 2. 用的知识点

1. `srand(time(NULL))` 与 `rand()` : 设立随机种子，生成随机数
2. `clock()` : 用来算法运行前后的计算时钟周期差值，再除以`CLOCKS_PER_SEC` 即为运行秒数。
3. 函数式编程 : 使用函数指针，方便调用和测试排序函数

其中`rand()`函数能生成 0 到`MAX_INT`之间的随机整数。如果想生成`[0, n)`之间的整数，需要取余操作：`int x = rand() % n;`；如果想生成`[left, right]`之间的整数，需要进行偏移：`int x = rand() % (right - left + 1) + left;`

### 3. 代码实现

```cpp
//
// Created by GodBMW.com on 2018/9/11.
//

#ifndef BASESORT_SORTHELPER_H
#define BASESORT_SORTHELPER_H

#include <iostream>
#include <string>
#include <ctime>
#include <cassert>

using namespace std;

namespace SortTestHelper {
    // 生成[left, right]范围内n个随机数
    template <typename T>
    T* generateRandomArray(int n, int left, int right) {
        assert( left <= right );
        T *arr = new T[n];
        srand(time(NULL)); // set random seed
        for(int i = 0; i < n; i++) {
            arr[i] = rand() % (right - left + 1) + left;
        }
        return arr;
    }

    // 生成[0, n)范围内n个近乎有序的随机数
    template <typename T>
    T* generateNearlyOrderedArray(int n, int swap_times) {
        T *arr = new T[n];
        srand(time(NULL));
        // 先生成长度为n的有序数组
        for(int i = 0; i < n; i++) {
            arr[i] = i + 1;
        }
        // 随机选取其中2个数据，交换 swap_times 次
        for(int i = 0; i < swap_times; i++) {
            int pos_x = rand() % n;
            int pos_y = rand() % n;
            swap(arr[pos_x], arr[pos_y]);
        }
        return arr;
    }

    template <typename T>
    T* copyArray(T arr[], int length) {
      T* brr = new T[length]; // 注意检查brr数组大小
      copy(arr, arr + length, brr);
      return brr;
    }

    template <typename T>
    void printArray(T arr[], int length) {
        for(int i = 0; i < length; i++) {
            cout<< arr[i] << " ";
        }
        cout<< endl;
        return;
    }

    template <typename T>
    bool isSorted(T arr[], int length) {
        for(int i = 0; i < length-1; i++) {
            if(arr[i] > arr[i + 1]) {
                return false;
            }
        }
        return true;
    }

    // 第三个参数是函数指针，传入后，可以在本函数内运行被传入的函数
    template <typename T>
    void testSort(T arr[], int length, void(*sort)(T[], int), string name) {
        clock_t startTime = clock();
        sort(arr, length);
        clock_t endTime = clock();

        assert(isSorted(arr, length));
        cout << name << " : " << double(endTime - startTime) / CLOCKS_PER_SEC << " seconds" << endl;
        // endTime - startTime: 时钟周期
        return;
    }

}

#endif //BASESORT_SORTHELPER_H
```

我们没有实现第二部分所说的 生成`n`个近乎相同的数据，比如：1、1、1、2、2、2、2、2...。因为可以借助 `generateRandomArray` 函数，比如:`SortTestHelper::generateRandomArray(10000, 0, 10)`。生成[0,10]区间内 10000 个整数，那么不就是近乎相同的吗？
