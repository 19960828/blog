---
title: "并查集：集合合并与元素查找"
date: 2018-10-11
categories: 数据结构和算法
tags:
- 数据结构
- C与C++
permalink: "2018-10-11-union-find"
---

> 在一些有N个元素的集合应用问题中，我们通常是在开始时让每个元素构成一个单元素的集合，然后按一定顺序将属于同一组的元素所在的集合合并，其间要反复查找一个元素在哪个集合中。

这个过程就涉及到：“合并”和“查找”这两个操作。

**利用并查集，可以实现用数组存储数据，并且查找操作和合并操作的时间复杂度近乎$O(1)$。**

<!-- more -->

## 1. 什么时候需要并查集？

在一些有 N 个元素的集合应用问题中，我们通常是在开始时让每个元素构成一个单元素的集合，然后按一定顺序将属于同一组的元素所在的集合合并，其间要反复查找一个元素在哪个集合中。

**这个过程就涉及到：“合并”和“查找”这两个操作。**

**利用并查集，可以实现用数组存储数据，并且查找操作和合并操作的时间复杂度近乎$O(1)$。**

## 2. 如何实现并查集？

### 2.1 实现查找操作

并查集是一种树形数据结构。在这些数据中，每个集合是一棵树，所有的集合在一起就形成了“森林”。

当然，之前说过要节省空间，借助数组就可以实现。**为了方便说明，这里数组的索引值就是数据本身，而索引 i 对应的数组的值`arr[i]`就是`i`的根节点。**

如下图所示。3、4、9 这三个元素都以 8 位根节点。**此时判断两个元素是否属于同一集合，只需要递归找到元素的根节点，比较根节点是否相同即可。**

![](/images/算法与数学/并查集：集合合并与元素查找/1.png)

### 2.2 实现合并操作

**这里的“合并”是指：将两个元素所在的集合合并为一个集合。**

这一步操作实现逻辑较复杂，假设有两个元素 p 和 q 需要合并到一个集合，思路如下：

1. 查找 p 和 q 的根节点，如果相同，两个元素已经是同一集合，跳出程序。如果不相同，往下执行。
2. 将其中一个根节点的重新指向另一个跟节点，完成集合合并操作。

## 3. 算法分析和优化

前面已经说了，“并查集”是一种树形数据结构。而我们的查找和合并操作其实都是建立在从叶节点向上递归查找根节点的操作上。

**因此，“并查集”的时间复杂度和树的深度有关，下面的优化操作也是为了让树的深度尽可能少，甚至变成 1 或者 2 层。**

### 3.1 合并优化

如`2.2`所陈述，这步操作： “将其中一个根节点的重新指向另一个跟节点，完成集合合并操作” ，其实可能会造成树的高度增加。例如下图两棵树：

![](/images/算法与数学/并查集：集合合并与元素查找/2.png)

如果是右边那棵树的根节点指向了左边树的根节点，那么，新形成的树的高度就是 4。**然而，左边那棵树的根节点如果指向右边那棵树的跟节点，树的高度就是 3。如此一来，形成的树的高度更低。**

![](/images/算法与数学/并查集：集合合并与元素查找/3.png)

**优化的方法就是：在“合并操作”的更改根节点指向的这步中，检测两棵树的高度，将高度较低的那颗树指向高度较高的树的根节点。所以，在初始化的时候，需要多一个数组`rank[]`，用来记录以 i 为根节点的树的高度。**

### 3.2 “路径压缩”

大名鼎鼎的路径压缩，就是在“查找”的过程中，将树的高度压缩成 2 层。如果对元素`p`调用了一次查找操作，那么以`p`为叶子节点的往上一直到根节点的所有节点，都会被压缩。

如下图所示，在执行`find(4)`操作后，整棵树的样子就变成了图右边的样子。

![](/images/算法与数学/并查集：集合合并与元素查找/4.png)

代码的实现，需要借助递归，请直接看`find()`方法。

## 4. 代码实现

关于并查集的数据结构封装在了头文件`union_find.h`中：

```cpp
// union_find.h
// Created by godbmw.com on 2018/10/9.
//

#ifndef UNIONFIND_UNION_FIND_H
#define UNIONFIND_UNION_FIND_H

#include <iostream>
#include <cassert>

using namespace std;

class UnionFind {
private:
    int count;
//    parent[i]：元素i父节点的索引值
    int *parent;
//     rank[i]：以i为根的集合所表示的树的层数
    int *rank;
public:
    UnionFind(int count) {
        this->count = count;
        parent = new int[count];
        rank = new int[count];
//        每个节点都是独立的，所以父节点索引就是自己
//        每个节点的树的高度都是1
        for(int i = 0; i < count; i++) {
            parent[i] = i;
            rank[i] = 1;
        }
    }

    ~UnionFind() {
        delete[] parent;
        delete[] rank;
    }

//    查找索引为p的元素的根节点的索引
    int find(int p) {
//        路径压缩：将层数为n( n>1 )的树压缩为层数为1的树
        if( p != this->parent[p]) {
            this->parent[p] = this->find( this->parent[p] );
        }
        return parent[p];
    }

//    查看索引分别为p和q的元素是否属于同一集合
    bool is_connected(int p, int q) {
        return this->find(p) == this->find(q);
    }

//    合并索引分别p和q的元素到一个集合
    void union_elements(int p, int q) {
        int p_root = this->find(p), q_root = this->find(q);

//        根节点索引值相同：已经属于同一集合
        if(p_root == q_root) return ;

        if( this->rank[p_root] < this->rank[q_root] ) {
//            合并后，q_root 的树的深度并没有改变
            this->parent[p_root] = q_root;
        } else if ( this->rank[q_root] < this->rank[p_root] ) {
//            合并后，p_root 的树的深度并没有改变
            this->parent[q_root] = p_root;
        } else {
//            合并后，q_root 的深度加 1
            this->parent[p_root] = q_root;
            this->rank[q_root] += 1;
        }
    }
};

#endif //UNIONFIND_UNION_FIND_H
```

## 5. 代码测试

直接上了 1 亿的数据量，并且执行了 1 一次合并操作和 1 亿次检查是否属于同一集合的操作。在我的电脑上耗时基本是 8s。有图有真相：

![](/images/算法与数学/并查集：集合合并与元素查找/5.png)

测试代码`main.cpp`如下：

```cpp
// main.cpp
// created by godbmw.com

#include <iostream>
#include <ctime>
#include "union_find.h"

#define N 100000000

using namespace std;

void calc_run_time() {
    srand(time(NULL));
    register int a, b;
    UnionFind uf = UnionFind(N);

    time_t start_time = clock();

    for(int i = 0; i < N; i++) {
        a = rand() % N;
        b = rand() % N;
        uf.union_elements(a, b);
    }

    for(int i = 0; i < N; i++) {
        a = rand() % N;
        b = rand() % N;
        uf.is_connected(a, b);
    }

    time_t end_time = clock();

    cout << double(end_time - start_time) / CLOCKS_PER_SEC<<" s"<<endl;
}

int main() {
    calc_run_time();

    return 0;
}
```
