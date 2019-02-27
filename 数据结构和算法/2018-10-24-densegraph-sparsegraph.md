---
title: 稠密图和稀疏图的最优实现及应用
date: 2018-10-24
categories:
- 算法与数学
tags:
- C与C++
- 数据结构
---

> 本文介绍了稠密图和稀疏图对应的实现方式：邻接矩阵和邻接表。

实现的亮点在于两种类的内部迭代器和函数接口对外一致，方便进一步编写遍历算法、解决图的相关问题。

<!-- more -->

## 1. 稀疏图和稠密图的界定

首先说明：这只是划分图种类的一种方法。不同于有向图和无向图等划分方法，稀疏图和稠密图的界定很有意思，并没有确定的界限。

比如，下面这张图是稠密图还是稀疏图？

![](/images/算法与数学/稠密图和稀疏图的最优实现及应用/1.png)

没有经验的人会将这张图划分为稠密图。**但是，仔细观察每个点可以发现：每个点连接的边只有 5 条边，而整张的点有几十个，5 个点显然只是这些点的一小部分。由此，可以界定这张图是“稀疏图”。**

根据这个经验，“完全图”可以被界定为“稠密图”。

## 2. 邻接表和邻接矩阵

图的实现通常有 2 中数据结构：邻接表和邻接矩阵。**这也是为什么前面要界定稀疏图和稠密图的原因：稀疏图常采取邻接表实现，稠密图常采取邻接矩阵实现。**

下面两张图分别是邻接表和邻接矩阵：

![](/images/算法与数学/稠密图和稀疏图的最优实现及应用/2.png)

![](/images/算法与数学/稠密图和稀疏图的最优实现及应用/3.png)

一般而言，图多采用邻接表实现，原因如下：

- 大部分图都是稀疏图
- 邻接表的空间复杂度是 $O(N)$，邻接矩阵的空间复杂度是 $O(N^{2})$
- 邻接表遍历的时间复杂度是 $O(E)$，邻接矩阵遍历的时间复杂是 $O(N^{2})$

_注：N 是节点个数，E 是边数_

## 3. 图的代码实现

实现针对稠密图和稀疏图的两种数据类型：邻接矩阵和邻接表。

两种图应该对外表现一致（相同的调用方法），以方便之后进行路径算法等算法的编写封装。

两种图应该实现初始化、给节点添加边、检查节点是否相连等函数。

除此之外，还需要在类的内部实现迭代器，以方便用户遍历访问图的节点和相邻节点。

### 3.1 实现迭代器

为了方便外部用户遍历图中的节点元素，访问节点的相邻节点，并且防止用户篡改图的节点数据。利用“迭代器模式”，分别针对稀疏图和稠密图的迭代器。

在下面的两种图的实现中，`DenseGraph`和`SparseGraph`两个类的内部均实现了`graph_iterator`类。此时，可以使用如下方法使用迭代器：

```cpp
DenseGraph g(10, false);
DenseGraph::graph_iterator iter(g,0); // 生成迭代器
// 逐个访问节点元素
for(int w = iter.begin(); !iter.end(); w = iter.next()) {
  cout<< w << endl;
}
```

### 3.2 实现稠密图

```cpp
// dense_graph.h
// Created by godbmw.com on 2018/10/12.
// 稠密图：邻接矩阵

#ifndef GRAPH_DENSE_GRAPH_H
#define GRAPH_DENSE_GRAPH_H

#include <iostream>
#include <vector>
#include <cassert>

using namespace std;

class DenseGraph {
private:
    int n; // 节点数
    int m; // 边数
    bool directed; // 是否有向
    vector<vector<bool>> g; // 邻接表具体数据

public:
    DenseGraph(int n, bool directed) {
        this->n = n;
        this->m = 0;
        this->directed = directed;
        g = vector<vector<bool>>(n, vector<bool>(n, false));
    }

    ~DenseGraph(){}

    int V() { return this->n; }

    int E() { return this->m; }

    void add_edge(int v, int w) {
        this->g[v][w] = true;

        if(this->has_edge(v,w)) return;

        if(!this->directed) {
            this->g[w][v] = true;
        }
        this->m++;
    }

    bool has_edge(int v, int w) {
        return this->g[v][w];
    }

    // 显示图的信息
    void show(){
        for( int i = 0 ; i < n ; i ++ ){
            for( int j = 0 ; j < n ; j ++ )
                cout<<g[i][j]<<"\t";
            cout<<endl;
        }
    }

    class graph_iterator {
    private:
        DenseGraph &G;
        int v;
        int index;
    public:
        graph_iterator(DenseGraph &graph, int v):G(graph) {
            this->v = v;
            this->index = -1;
        }

        ~graph_iterator(){}

//        返回图G中与顶点v相连接的第一个顶点
        int begin() {
            this->index = -1;
            return this->next();
        }

        int next() {
            this->index += 1;
            for(; this->index < G.V(); this->index++) {
                if(G.g[v][this->index]) {
                    return this->index;
                }
            }
            return -1;
        }

        bool end() {
            return this->index >= G.V();
        }
    };
};

#endif //GRAPH_DENSE_GRAPH_H
```

### 3.3 实现稀疏图

```cpp
// sparse_graph.h
// Created by godbmw.com on 2018/10/12.
// 稀疏图：邻接表

#ifndef GRAPH_SPARSE_GRAPH_H
#define GRAPH_SPARSE_GRAPH_H

#include <iostream>
#include <vector>
#include <cassert>

using namespace std;

class SparseGraph {
private:
    int n; // 节点数
    int m; // 边数
    bool directed; // 是否有向
    vector<vector<int>> g; // 邻接表具体数据

public:
    SparseGraph(int n, bool directed) {
        this->n = n;
        this->m = 0;
        this->directed = directed;
        g = vector<vector<int>>(n, vector<int>());
    }

    ~SparseGraph(){ }

    int V(){return n;}

    int E(){return m;}

//    节点v和w之间增加一条边
    void add_edge(int v, int w) {
//        邻接表 has_edge 操作时间复杂度为 O(n)
//        更好的做法是在创建完整张图后，再剔除平行边
//        if(this->has_edge(v, w)) return ;

        g[v].push_back(w);
        if(v != w && !directed) {
            g[w].push_back(v);
        }
        m++;
    }

//    图中v和w两个点之间是否有边
    bool has_edge(int v, int w) {
        for(int i = 0; i < g[v].size(); ++i) {
            if(g[v][i] == w) {
                return true;
            }
        }
        return false;
    }

    // 显示图的信息
    void show(){

        for( int i = 0 ; i < n ; i ++ ){
            cout<<"vertex "<<i<<":\t";
            for( int j = 0 ; j < g[i].size() ; j ++ )
                cout<<g[i][j]<<"\t";
            cout<<endl;
        }
    }

    class graph_iterator {
    private:
        SparseGraph &G;
        int v;
        int index;
    public:
        graph_iterator(SparseGraph &graph, int v):G(graph) {
            this->v = v;
            this->index = 0;
        }

        ~graph_iterator(){}

//        返回图G中与顶点v相连接的第一个顶点
        int begin() {
            this->index = 0;
            if(G.g[v].size()) {
                return G.g[v][this->index];
            }
            return -1;
        }

        int next() {
            this->index += 1;
            if( !this->end() ) {
                return G.g[v][this->index];
            }
            return -1;
        }

        bool end() {
            return this->index >= G.g[v].size();
        }
    };
};

#endif //GRAPH_SPARSE_GRAPH_H
```

## 4. 代码测试和结果

### 4.1 准备测试数据

准备了`graph_data_1.txt`和`graph_data_2.txt`两个图的数据。第一行的两个数据是节点个数和边的个数。剩下的行是相邻的两个节点的坐标。

`graph_data_1.txt`：

```
13 13
0 5
4 3
0 1
9 12
6 4
5 4
0 2
11 12
9 10
0 6
7 8
9 11
5 3
```

`graph_data_2.txt`：

```
6 8
0 1
0 2
0 5
1 2
1 3
1 4
3 4
3 5
```

### 4.2 读取测试数据

此时，就可以看到之前两个类对外统一接口的好处了。此时只需要写一套代码即可。

```cpp
// reader.h
// Created by godbmw.com on 2018/10/12.
// 从文件中读取图

#ifndef GRAPH_READER_H
#define GRAPH_READER_H

#include <stdio.h>
#include <iostream>
#include <fstream>
#include <string>
#include <sstream>
#include <cassert>

using namespace std;

template <typename Graph>
class Reader {
public:
//    从filename中读取图信息，并且存储图信息
    Reader(Graph &graph, const string &filename) {
        ifstream file(filename);
        string line;
        int V, E;

        if(file.is_open() == false) {
            perror("Fail to open the file ");
            exit(-1);
        }

//        从第一行读取图中的节点个数和边的个数
        assert( getline(file, line) );
        stringstream ss(line);
        ss>>V>>E;

        if( V != graph.V()) {
            cout<<"Error info\n";
            exit(-1);
        }

        for(int i = 0; i < E; i++) {
            assert(getline(file, line));
            stringstream ss(line);

            int a, b;
            ss>>a>>b;
            assert( a >= 0 && a < V );
            assert( b >= 0 && b < V );
            graph.add_edge( a , b );
        }
    }
};

#endif //GRAPH_READER_H
```

### 4.3 测试结果

编写我们的测试代码：

```cpp
#include <iostream>
#include <ctime>
#include <string>
#include "reader.h"
#include "sparse_graph.h"
#include "dense_graph.h"

using namespace std;

int main() {
    // 使用两种图的存储方式读取testG1.txt文件
    string filename = "F:/ClionProjects/Graph/graph_data_1.txt";
    SparseGraph g1( 13 , false );
    Reader<SparseGraph> readGraph1( g1 , filename );
    g1.show();

    DenseGraph g2( 13 , false );
    Reader<DenseGraph> readGraph2( g2 , filename );
    g2.show();

    cout<<endl;

    // 使用两种图的存储方式读取testG2.txt文件
    filename = "F:/ClionProjects/Graph/graph_data_2.txt";
    SparseGraph g3( 6 , false );
    Reader<SparseGraph> readGraph3( g3 , filename );
    g3.show();

    DenseGraph g4( 6 , false );
    Reader<DenseGraph> readGraph4( g4 , filename );
    g4.show();

    return 0;
}
```

测试结果如下图所示：

![](/images/算法与数学/稠密图和稀疏图的最优实现及应用/4.png)

## 5. 致谢

本篇博客是总结于慕课网的[《学习算法思想 修炼编程内功》](https://coding.imooc.com/class/chapter/71.html)的笔记，liuyubobobo 老师人和讲课都很 nice，欢迎去买他的课程。
