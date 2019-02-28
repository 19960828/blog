---
title: "进击的堆：最大索引堆"
date: 2018-09-26
categories: 数据结构和算法
tags:
- C与C++
- 数据结构
permalink: "2018-09-26-index-heap"
---

> 我们为什么需要实现索引堆？

堆结构的数据增删操作，需要`swap`操作。虽然可以被优化成每次一次赋值，然而**当元素类型是复杂数据机构（例如：类、浮点数、结构体等），赋值操作的消耗不容小觑。**

因此，如果可以通过交换整数数据，来实现堆的数据操作，就会大大提高程序性能。索引堆就是为此而生！

除此之外，**借助索引堆，还能实现原地修改堆中的元素**，并且，不需要全部重新建堆！

<!-- more -->

## 1. 为什么需要索引堆？

堆结构的数据增删操作，需要`swap`操作。虽然可以被优化成每次一次赋值，然而当元素类型是复杂数据机构（例如：类、浮点数、结构体等），赋值操作的消耗不容小觑。

因此，如果可以通过交换整数数据，来实现堆的数据操作，就会大大提高程序性能。而索引堆就是为此而生。

## 2. 堆和索引堆

*友情链接：[《堆、堆排序和优先队列的那些事》](https://godbmw.com/passage/58)*

在堆的基础上，增加一个整数数组`indexes`。

`indexes[i]`就代表：堆中第i个元素在所属数组中的位置。

如下图所示, `index[1]`代表堆中的第1个元素是`data`中的第10个元素。

![](/images/算法与数学/进击的堆：最大索引堆/1.png)

因此，有了`indexes`数组，`data`数组不要变动，只需要维护`indexes`数组即可表示堆中的数据顺序。

## 3. 反向查找

当我们需要改变原来`data`数组中的一个数据并且维护堆的结构，需要反向索引来帮助。

堆中引入`reverse`数组。`reverse[i]`代表索引i在`indexes`数组的位置。

如下图所示, `reverse[1]`代表1在`indexes`中的位置是8。

![](/images/算法与数学/进击的堆：最大索引堆/2.png)

**借助反向查找，当我们修改第9个元素的时候，访问`reverse[9]`便可以知道`data[9]`在堆中的位置是2。时间复杂度降低到`O(1)`。**

为了方便理解，请看后面实现的`MaxIndexHeap.h`中的`void change(int i, Item new_item)`函数。

## 4. 代码实现

### 4.1 实现最大堆

**`MaxIndexHeap.h`代码如下：**

```cpp
//
// Created by godbmw.com on 2018/9/26.
//

#ifndef MAXHEAP_INDEXMAXHEAP_H
#define MAXHEAP_INDEXMAXHEAP_H

#include <iostream>
#include <algorithm>
#include <cassert>
#include <typeinfo>

using namespace std;

template <typename Item>
class IndexMaxHeap {
private:
    Item* data; // 堆数据存放
    int* indexes;
    int* reverse;
    int count; // 堆目前所含数据量大小
    int capacity; // 堆容量大小

    void shift_up(int k) {
        while( k > 1 && data[indexes[k/2]] < data[indexes[k]]) {
//          交换堆中2个元素的位置, 但是不操作data数组
//          相当于交换 int 型数据，比交换Item更有效
            swap(indexes[k/2], indexes[k]);
//          交换后reverse的对应值
//          更新在indexes中的位置
            reverse[indexes[k/2]] = k/2;
            reverse[indexes[k]] = k;
            k /= 2;
        }
    }

    void shift_down(int k) {
        while( 2*k <= count ) {
            int j = 2*k;
            if( j+1 <= count && data[indexes[j+1]] > data[indexes[j]]) {
                j+=1;
            }
            if(data[indexes[k]] >= data[indexes[j]]) {
                break;
            }
            swap(indexes[k], indexes[j]);
            reverse[indexes[k]] = k;
            reverse[indexes[j]] = j;
            k = j;
        }
    }
public:
    IndexMaxHeap(int capacity) {
        this->data = new Item[capacity + 1]; // 堆中数据从索引为1的位置开始存储
        this->indexes = new int[capacity + 1];
        this->reverse = new int[capacity + 1];
        for(int i = 0; i < this->capacity + 1; ++i) {
            this->reverse[i] = -1;
        }
        this->count = 0;
        this->capacity = capacity;
    }
    ~IndexMaxHeap(){
        delete[] this->data;
        delete[] this->indexes;
        delete[] this->reverse;
    }
//    返回堆中元素个数
    int size() {
        return this->count;
    }
//    返回布尔值：堆中是否为空
    bool is_empty() {
        return this->count == 0;
    }

//    向堆中插入元素Item, i是元素索引
    void insert(int i, Item item) {
        assert(this->count < this->capacity);
        assert( i >= 0 && i <= this->capacity);
//        对外部用户而言, 是从索引0开始的
        i += 1;
        this->data[i] = item;
        this->indexes[this->count + 1] = i;
        this->reverse[i] = this->count + 1;
        this->count++;
        this->shift_up(this->count);
    }

//    取出最大值
    Item extract_max() {
        assert(this->count > 0);
        Item ret = this->data[this->indexes[1]]; // 取出根节点
        swap(this->indexes[1], this->indexes[this->count]); // 将根节点元素和最后元素交换
        this->reverse[this->indexes[1]] = 1;
        this->reverse[this->indexes[this->count]] = -1;
        this->count --; // 删除最后一个元素
        this->shift_down(1); // shift_down 将元素放到应该在的位置
        return ret;
    }

    int extract_max_index() {
        assert(this->count > 0);
        int ret = this->indexes[1] - 1;
        swap(this->indexes[1], this->indexes[this->count]);
        this->reverse[this->indexes[1]] = 1;
        this->reverse[this->indexes[this->count]] = -1;
        this->count --;
        this->shift_down(1);
        return ret;
    }

    bool contain(int i) {
        assert( i >= 0 && i <= this->capacity);
        return reverse[i+1] != -1;
    }

    Item get_item(int i) {
        assert(this->contain(i));
        return this->data[i+1];
    }

    void change(int i, Item new_item) {
        assert(this->contain(i));
        i += 1;
        this->data[i] = new_item;
//        找到 indexes[j] = i, j 表示data[i]在堆中的位置
//        之后shift_up 和 shift_down
//        for(int j = 1; j <= this->count; ++j) {
//            if(this->indexes[j] == i) {
//                this->shift_up(j);
//                this->shift_down(j);
//                return;
//            }
//        }

//      利用 reverse 实现反向查找, 从 O(n) => O(1)
        int j = this->reverse[i];
        this->shift_up(j);
        this->shift_down(j);
    }
};

#endif //MAXHEAP_INDEXMAXHEAP_H
```

### 4.2 测试代码

**`main.cpp`代码如下：**

```cpp
#include <iostream>
#include <ctime>
#include <algorithm>
#include "MaxHeap.h"
#include "IndexMaxHeap.h"
#include "SortHelper.h"

#define HEAP_CAPACITY 10
#define MAX_NUM 100

using namespace std;
int main() {
    IndexMaxHeap<int> index_max_heap = IndexMaxHeap<int>(HEAP_CAPACITY);
    srand(time(NULL));
    for(int i = 0; i < HEAP_CAPACITY - 2 ; i++) {
        index_max_heap.insert(i, rand() % MAX_NUM);
    }
    cout<<endl;

    index_max_heap.change(1, 109);

    while( !index_max_heap.is_empty() ) {
        cout<< index_max_heap.extract_max() << " ";
    }
    cout<<endl;
    return 0;
}
```

