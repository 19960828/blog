---
title: 图遍历实现及其应用
date: 2018-10-27
categories:
- 算法与数学
tags:
- 算法
- C与C++
---

> 介绍一些如何利用深度优先解决联通分量、是否联通、查找路径等相关问题，如何利用广度优先查找最短路径、计算最短距离等相关问题。

本文代码中用到的`SparseGraph`和`DenseGraph`两个类的实现和封装，以及读取测试数据的类的封装，请见[《稠密图和稀疏图实现及应用》](https://godbmw.com/passage/82)中的讲解和实现。

<!-- more -->

## 1. 稠密图和稀疏图实现及其应用

下面的代码应用到的`SparseGraph`和`DenseGraph`两个类的实现和封装，以及读取测试数据的类的封装，请见[《稠密图和稀疏图实现及应用》](https://godbmw.com/passage/82)中的讲解和实现。

## 2. 深度优先遍历及其应用

深度优先的思路就不冗赘了（_网上博客文章一抓一大把_），记住是用递归找到所有点即可。

这里就记录下如何利用深度优先解决图的联通分量、打印指定点的路径等问题。

### 2.1 找到图的联通分量

思路如下：

1. 准备一个数组`id`：`id[i]`表示点 i 所在的联通分量的序号
2. 从图的第一个点开始循环，每个点进行深度优先遍历，并且将图的联通分量加 1
3. 每次对一个点进行遍历的时候，更新它对应的`id`数组的值
4. 如果遍历完一个联通分量，则会退出循环，回到第 2 步，继续执行。如果此时发现所有点都被遍历完了，则退出循环。

实现的功能：

- 判断图的联通分量的个数
- 判断任意两点是否属于同一联通分量 / 是否联通

```cpp
// component.h
// Created by godbmw.com on 2018/10/13.
//

#ifndef GRAPH_COMPONENT_H
#define GRAPH_COMPONENT_H

#include <iostream>
#include <cassert>

using namespace std;

template <typename Graph>
class Component {
private:
    Graph &G;
    bool *visited; // visit[i]: 点i是否被访问过
    int ccount; // 记录联通分量个数
    int *id; // id[i]: 点i所在的联通分量的标记

//    深度优先遍历
    void dfs(int v) {
        visited[v] = true;
        id[v] = ccount; // 每个节点的联通分量的身份标识就采用当前发现的联通分量的个数

        typename Graph::graph_iterator iter(G, v);
        for(int i = iter.begin(); !iter.end(); i = iter.next()) {
            if( !visited[i] ) dfs(i);
        }
    }

public:
    Component(Graph &graph): G(graph) {
        this->visited = new bool[G.V()];
        this->id = new int[G.V()];
        this->ccount = 0;

        for(int i = 0; i < G.V(); ++i) {
            this->visited[i] = false;
            this->id[i] = -1;
        }

//        求图的联通分量
        for(int i = 0; i < G.V(); i++) {
            if(!this->visited[i]) {
                // 开始针对点i进行深度优先遍历
                // 此过程中，和节点i联通的所有点都会被遍历并且更新相关id数组的标识
                ++this->ccount;
                this->dfs(i);
            }
        }
    }
    ~Component() {
        delete[] this->visited;
        delete[] this->id;
    }

//    返回联通分量
    int count() {
        return this->ccount;
    }

//    判断点v和w是否联通
    bool is_connected(int v, int w) {
        return this->id[v] == this->id[w];
    }
};


#endif //GRAPH_COMPONENT_H
```

### 2.2 打印指定点的路径

代码思路：

1. 准备一个数组`from`: `from[i]`表示节点 i 的上一个节点
2. 从起始点开始进行深度优先遍历
3. 在深度优先的过程中，更新节点在`from`数组中的相应值
4. 完成深度遍历的同时，`from`数组中也就填充完成了

实现功能：

- 打印从起始点到指定节点的路径
- 判断起始点是否可以达到指定节点

_注意：深度优先无法保证路径是最短的，但可以保证路径是可以达到的。_

```cpp
// path.h
// Created by godbmw.com on 2018/10/13.
//

#ifndef GRAPH_PATH_H
#define GRAPH_PATH_H

#include <iostream>
#include <cassert>
#include <vector>
#include <stack>

using namespace std;

template <typename Graph>
class Path{
private:
    Graph &G;
    int s; // 起始点
    bool *visited;
    int *from;

//    深度优先遍历
    void dfs(int v) {
        visited[v] = true;

        typename Graph::graph_iterator iter(G, v);
        for(int i = iter.begin(); !iter.end(); i = iter.next()) {
            if(!visited[i]) {
//                路径中节点i的前一个节点是v
                from[i] = v;
                dfs(i);
            }
        }
    }

public:
//    构造过程中，调用深度优先遍历，找到从s点到其他点的路径
    Path(Graph &graph, int s):G(graph) {
        this->s = s;
        this->visited = new bool[G.V()];
        this->from = new int[G.V()];

        for(int i = 0; i < G.V(); ++i) {
            this->visited[i] = false;
            this->from[i] = -1;
        }

        this->dfs(s);
    }

    ~Path() {
        delete[] this->visited;
        delete[] this->from;
    }

//    检测从s到w是否有路径
    bool has_path(int w) {
//        如果有路径，那么在深度遍历的过程中一定被访问过
        return this->visited[w];
    }

    void path(int p, vector<int> &vec) {
        vec.clear();
        if(!this->has_path(p)) return;

//        深度遍历是递归调用，所以，from[i]对应的是i节点的父节点
//        借助栈，栈顶到栈底就是路径起点到路径终点
//        再依次取出栈元素，放入队列(这里用的vector)即可

        stack<int> s;
        while(p != -1) {
            s.push(p);
            p = this->from[p];
        }

        while(!s.empty()) {
            vec.push_back(s.top());
            s.pop();
        }
    }

    void show_path(int w) {
        if(!this->has_path(w)) return;

        vector<int> vec;
        this->path(w, vec);

        if(vec.empty()) return;

        for(int i = 0; i < vec.size() - 1; ++i) {
            cout<<vec[i]<<" -> ";
        }

        cout<<vec.back()<< endl;
    }
};

#endif //GRAPH_PATH_H
```

## 3. 广度优先遍历及其应用

### 3.1 广度优先遍历的思路

1. 从任意点开始，将此点放入队列
2. 如果队列为空，结束程序；否则，往下执行
3. 取出队列中的第一个节点，标记访问过
4. 遍历此节点的邻接节点，如果没有被访问过，则放入队列
5. 回到第 2 步骤

_注意：需要借助队列这种数据结构_

### 3.2 找到最短路径

代码思路：仿照深度优先遍历打印路径，只需要在广度优先遍历的过程中标记这个节点的上一个节点即可。

实现功能：

- 打印出起始点到任一点的最短路径
- 找到起始点到任一节点的最短距离

```cpp
//
// Created by AsuraDong on 2018/10/13.
//

#ifndef GRAPH_SHORTEST_PATH_H
#define GRAPH_SHORTEST_PATH_H

#include <vector>
#include <queue>
#include <stack>
#include <iostream>
#include <cassert>

using namespace std;

template <typename Graph>
class ShortestPath {
private:
    Graph &G;
    int s; // 图的起始点
    bool *visited; // visit[i]: 节点i是否被访问过
    int *from; // from[i]: 路径上i的上一个节点
    int *ord; // ord[i]: 节点i在路径中的次序
public:
    ShortestPath(Graph &graph, int s):G(graph) {
        this->visited = new bool[G.V()];
        this->from = new int[G.V()];
        this->ord = new int[G.V()];

        for(int i = 0; i < G.V(); i++) {
            this->visited[i] = false;
            this->from[i] = -1;
            this->ord[i] = -1;
        }

        this->s = s;

        queue<int> q;
        q.push(s);
        this->visited[s] = true;
        this->ord[s] = 0;

        while(!q.empty()) {
            int v = q.front();
            q.pop();

            typename Graph::graph_iterator iter(G, v);
            for(int i = iter.begin(); !iter.end(); i = iter.next()) {
                if(this->visited[i]) continue;

                q.push(i);
                this->visited[i] = true;
                this->from[i] = v;
                this->ord[i] = this->ord[v] + 1;
            }
        }
    }

    ~ShortestPath(){
        delete[] this->visited;
        delete[] this->from;
        delete[] this->ord;
    }

//    点s到点w的最短路径长度
    int length(int w) {
        return this->ord[w];
    }

    bool has_path(int w) {
        return this->visited[w];
    }

//    点s到点w的最短路径
    void path(int p, vector<int> &vec){
        vec.clear();
        if(!this->has_path(p)) return;

        stack<int> s;
        while( p != -1 ){
            s.push(p);
            p = this->from[p];
        }

        while( !s.empty() ){
            vec.push_back( s.top() );
            s.pop();
        }
    }

    void show_path(int w) {
        if(!this->has_path(w)) return;

        vector<int> vec;
        this->path(w, vec);

        if(vec.empty()) return;

        for(int i = 0; i < vec.size() - 1; ++i) {
            cout<<vec[i]<<" -> ";
        }

        cout<<vec.back()<< endl;
    }

};

#endif //GRAPH_SHORTEST_PATH_H
```

## 4. 代码测试和结果

### 4.1 准备测试数据

准备了`graph_data_1.txt`、`graph_data_2.txt`和`graph_data_3.txt`3 个图的数据。第一行的两个数据是节点个数和边的个数。剩下的行是相邻的两个节点的坐标。

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

`graph_data_3.txt`：

```
7 8
0 1
0 2
0 5
0 6
3 4
3 5
4 5
4 6
```

### 4.2 测试深度优先及其应用

测试代码`main.cpp`如下：

```cpp
#include <iostream>
#include <ctime>
#include <string>
#include "reader.h"
#include "sparse_graph.h"
#include "dense_graph.h"
#include "component.h"
#include "path.h"
#include "shortest_path.h"

using namespace std;


int main() {
    // 使用两种图的存储方式读取testG1.txt文件
    string filename = "F:/ClionProjects/Graph/graph_data_1.txt";
    SparseGraph g1( 13 , false );
    Reader<SparseGraph> readGraph1( g1 , filename );
    Component<SparseGraph> component1(g1);
    cout<<"TestG1.txt, Using Sparse Graph, Component Count: "<<component1.count()<<endl;

    DenseGraph g2( 13 , false );
    Reader<DenseGraph> readGraph2( g2 , filename );
    Component<DenseGraph> component2(g2);
    cout<<"TestG1.txt, Using Dense Graph, Component Count: "<<component2.count()<<endl;

    cout<<endl;


    // 使用两种图的存储方式读取testG2.txt文件
    filename = "F:/ClionProjects/Graph/graph_data_2.txt";
    SparseGraph g3( 6 , false );
    Reader<SparseGraph> readGraph3( g3 , filename );
    Component<SparseGraph> component3(g3);
    cout<<"TestG2.txt, Using Sparse Graph, Component Count: "<<component3.count()<<endl;

    DenseGraph g4( 6 , false );
    Reader<DenseGraph> readGraph4( g4 , filename );
    Component<DenseGraph> component4(g4);
    cout<<"TestG2.txt, Using Dense Graph, Component Count: "<<component4.count()<<endl;

    return 0;
}
```

测试结果如下：

![](/images/算法与数学/图遍历实现及其应用/1.png)

### 4.3 测试广度优先及其应用

测试代码`main.cpp`如下所示：

```cpp
#include <iostream>
#include <ctime>
#include <string>
#include "reader.h"
#include "sparse_graph.h"
#include "dense_graph.h"
#include "component.h"
#include "path.h"
#include "shortest_path.h"

using namespace std;

int main() {
    string filename = "F:/ClionProjects/Graph/graph_data_3.txt";
    SparseGraph g = SparseGraph(7, false);
    Reader<SparseGraph> readGraph(g, filename);
    g.show();

    // 比较使用深度优先遍历和广度优先遍历获得路径的不同
    // 广度优先遍历获得的是无权图的最短路径
    Path<SparseGraph> dfs(g,0);
    cout << "DFS : ";
    dfs.show_path(6);

    ShortestPath<SparseGraph> bfs(g,0);
    cout << "BFS : ";
    bfs.show_path(6);

    cout << endl;

    filename = "F:/ClionProjects/Graph/graph_data_1.txt";
    SparseGraph g2 = SparseGraph(13, false);
    Reader<SparseGraph> readGraph2(g2, filename);
    g2.show();

    // 比较使用深度优先遍历和广度优先遍历获得路径的不同
    // 广度优先遍历获得的是无权图的最短路径
    Path<SparseGraph> dfs2(g2,0);
    cout << "DFS : ";
    dfs2.show_path(3);

    ShortestPath<SparseGraph> bfs2(g,0);
    cout << "BFS : ";
    bfs2.show_path(3);

    return 0;
}
```

测试结果如下所示：

![](/images/算法与数学/图遍历实现及其应用/2.png)
