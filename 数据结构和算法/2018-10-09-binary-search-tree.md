---
title: 二叉搜索树的实现与常见用法
date: 2018-10-09
categories:
- 算法与数学
tags:
- 数据结构
- C与C++
---

> 二叉搜索树是一颗空树，或者具有以下性质的二叉树：

- 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值
- 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值
- 任意节点的左、右子树也分别为二叉查找树
- 没有键值相等的节点

这种数据结构可以高效解决查找问题、实现字典结构，值得一提的是，这些操作的算法复杂度均是：$O(\log_2 n)$

<!-- more -->

## 1. 为什么需要二叉搜索树？

> 选择数据结构的核心在于解决问题，而不是为了使用而使用。

由于二叉搜索树的定义和特性，它可以高效解决以下问题：

- 查找问题：二分查找
- 高级结构：字典结构实现
- 数据变动：节点的插入、删除
- 遍历问题：前序、中序、后序和层次遍历
- 数值运算：`ceil`、`floor`、找到第 n 大的元素、找到指定元素在排序好的数组的位置 等等

值得一提的是，除了遍历算法，上述各种问题的算法时间复杂度都是 : $O(\log_2 n)$

## 2. 二叉搜索树的定义和性质

二叉搜索树是一颗空树，或者具有以下性质的二叉树：

- 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值
- 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值
- 任意节点的左、右子树也分别为二叉查找树
- 没有键值相等的节点

**需要注意的是，二叉搜索树不一定是一颗完全二叉树，因此，二叉搜索树不能用数组来存储。**

## 3. 二叉搜索树的实现

第 3 部分实现的测试代码地址：[https://gist.github.com/dongyuanxin/d0803a8821c6797e9ce8522a676cf44b](https://gist.github.com/dongyuanxin/d0803a8821c6797e9ce8522a676cf44b)。

这是 Github 的 GIST，请自备梯子。

### 3.1 树结构实现

借助`struct`和指针模拟树的结构，并且将其封装到`BST`这个类之中：

```cpp
// BST.h
// Created by godbmw.com on 2018/9/27.
//

#ifndef BINARYSEARCH_BST_H
#define BINARYSEARCH_BST_H

#include <iostream>
#include <queue>

using namespace std;

template <typename Key, typename Value>
class BST {
private:
    struct Node {
        Key key;
        Value value;
        Node  *left;
        Node *right;

        Node(Key key, Value value) {
            this->key = key;
            this->value = value;
            this->left = NULL;
            this->right = NULL;
        }

        Node(Node* node) {
            this->key = node->key;
            this->value = node->value;
            this->left = node->left;
            this->right = node->right;
        }
    };

    Node *root;
    int count;

public:
    BST() {
        this->root = NULL;
        this->count = 0;
    }
    ~BST() {
        this->destroy(this->root);
    }
    int size() {
        return this->count;
    }
    bool isEmpty() {
        return this->root == NULL;
    }
};

#endif //BINARYSEARCH_BST_H
```

### 3.2 实现节点插入

插入采取递归的写法，思路如下：

1. 递归到底层情况：新建节点，并且返回
2. 非底层情况：如果当前键等于插入键，则更新当前节点的值；小于，进入当前节点的左子树；大于，进入当前节点的右子树。

```cpp
private:
    Node* insert(Node* node, Key key, Value value) {
        if(node == NULL) {
            count++;
            return new Node(key, value);
        }

        if(key == node->key) {
            node->value = value;
        } else if( key < node->key) {
            node->left = insert(node->left, key, value);
        } else {
            node->right = insert(node->right, key, value);
        }
        return node;
    }

public:
    void insert(Key key, Value value) {
        this->root = this->insert(this->root, key, value);
    }
```

### 3.3 实现节点的查找

查找包含 2 个函数：`contain`和`search`。前者返回布尔型，表示树中是否有这个节点；后者返回指针类型，表示树中节点对应的值。

**`search`为什么返回值的指针类型呢：**

- 如果要查找的节点不存在，指针可以直接返回`NULL`。
- 如果返回`Node*`，就破坏了类的封装性。原则上，内部数据结构不对外展示。
- 如果查找的节点存在，返回去键对应的值，用户可以修改，并不影响树结构。

```cpp
private:
    bool contain(Node* node, Key key) {
        if(node == NULL) {
            return false;
        }
        if(key == node->key) {
            return true;
        } else if(key < node->key) {
            return contain(node->left, key);
        } else {
            return contain(node->right, key);
        }
    }

    Value* search(Node* node, Key key) {
        if(node == NULL) {
            return NULL;
        }
        if(key == node->key) {
            return &(node->value);
        } else if (key < node->key) {
            return search(node->left, key);
        } else {
            return search(node->right, key);
        }
    }
public:
    bool contain(Key key) {
        return this->contain(this->root, key);
    }

//    注意返回值类型
    Value* search(Key key) {
        return this->search(this->root, key);
    }
```

### 3.4 遍历实现

前序、中序和后序遍历的思路很简单，根据定义，直接递归调用即可。

对于层次遍历，需要借助队列`queue`这种数据结构。思路如下：

1. 首先，将根节点放入队列
2. 如果队列不空，进入循环
3. 取出队列头部元素，输出信息。并将这个元素出队
4. 将这个元素非空的左右节点依次放入队列
5. 检测队列是否为空，不空的进入第 3 步；空的话，跳出循环。

```cpp
private:
    void pre_order(Node* node) {
        if(node != NULL) {
            cout<<node->key<<endl;
            pre_order(node->left);
            pre_order(node->right);
        }
    }

    void in_order(Node* node) {
        if(node != NULL) {
            in_order(node->left);
            cout<<node->key<<endl;
            in_order(node->right);
        }
    }

    void post_order(Node *node) {
        if(node != NULL) {
            post_order(node->left);
            post_order(node->right);
            cout<<node->key<<endl;
        }
    }

    void level_order(Node* node) {
        if(node == NULL) {
            return;
        }
        queue<Node*> q;
        q.push(node);
        while(!q.empty()) {
            Node* node = q.front();
            q.pop();
            cout<< node->key <<endl;
            if(node->left) {
                q.push(node->left);
            }
            if(node->right) {
                q.push(node->right);
            }
        }
    }

public:
    void pre_order() {
        this->pre_order(this->root);
    }

    void in_order() {
        this->in_order(this->root);
    }

    void post_order() {
        this->post_order(this->root);
    }

    void level_order() {
        this->level_order(this->root);
    }
```

### 3.5 实现节点删除

为了方便实现，首先封装了获取最小键值和最大键值的两个方法：`minimum`和`maximum`。

删除节点的原理很简单（_忘了什么名字，是一个计算机科学家提出的_），思路如下：

1. 如果左节点为空，删除本节点，返回右节点。
2. 如果右节点为空，删除本节点，返回左节点。
3. 如果左右节点都为空，是 1 或者 2 的子情况。
4. **如果左右节点都不为空，找到当前节点的右子树的最小节点，并用这个最小节点替换本节点。**

为什么第 4 步这样可以继续保持二叉搜索树的性质呢？

显然，右子树的最小节点，能满足小于右子树的所有节点，并且大于左子树的全部节点。

如下图所示，要删除`58`这个节点，就应该用`59`这个节点替换：

![](/images/算法与数学/二叉搜索树的实现与常见用法/1.png)

```cpp
private:
//    寻找最小键值
    Node* minimum(Node* node) {
        if(node->left == NULL) {
            return node;
        }
        return minimum(node->left);
    }
//    寻找最大键值
    Node* maximum(Node* node) {
        if(node->right == NULL) {
            return node;
        }
        return maximum(node->right);
    }
    Node* remove_min(Node* node) {
        if(node->left == NULL) {
            Node* right = node->right;
            delete node;
            count--;
            return right;
        }
        node->left = remove_min(node->left);
        return node;
    }

    Node* remove_max(Node* node) {
        if(node->right == NULL) {
            Node* left = node->left;
            delete node;
            count--;
            return left;
        }
        node->right = remove_max(node->right);
        return node;
    }
//    删除掉以node为根的二分搜索树中键值为key的节点
//    返回删除节点后新的二分搜索树的根
    Node* remove(Node* node, Key key) {
        if(node == NULL) {
            return NULL;
        }
        if(key < node->key) {
            node->left = remove(node->left, key);
        } else if(key > node->key){
            node->right = remove(node->right, key);
        } else {
//            key == node->key
            if(node->left == NULL) {
                Node* right = node->right;
                delete node;
                count--;
                return right;
            }
            if(node->right == NULL) {
                Node *left = node->left;
                delete node;
                count--;
                return left;
            }
//            node->right != NULL && node->left != NULL
            Node* successor = new Node(minimum(node->right));
            count++;
//            "count --" in "function remove_min(node->right)"
            successor->right = remove_min(node->right);
            successor->left = node->left;
            delete node;
            count--;
            return successor;
        }
        return node;
    }
public:
//    寻找最小键值
    Key* minimum() {
        if(this->count == 0) return NULL;
        Node* min_node = this->minimum(this->root);
        return &(min_node->key);
    }

//    寻找最大键值
    Key* maximum() {
        if(this->count == 0) return NULL;
        Node* max_node = this->maximum(this->root);
        return &(max_node->key);
    }
    void remove_min() {
        if(this->root == NULL) {
            return;
        }
        this->root = this->remove_min(this->root);
    }

    void remove_max() {
        if(this->root == NULL) {
            return;
        }
        this->root = this->remove_max(this->root);
    }
    void remove(Key key) {
        this->root = remove(this->root, key);
    }
```

### 3.6 数值运算：`floor`和`ceil`

`floor`和`ceil`分别是地板和天花板的意思。在一个数组中，对于指定元素`n`，如果数组中存在`n`，那么`n`的两个值就是它本身；如果不存在，那么分别是距离最近的小于指定元素的值 和 距离最近的大于指定元素的值。

```cpp
private:
    Node* floor(Node* node, Key key) {
        if(node == NULL) {
            return NULL;
        }

//        key等于node->key：floor的结果就是node本身
        if(node->key == key) {
            return node;
        }

//        key小于node—>key：floor的结果肯定在node节点的左子树
        if(node->key > key) {
            return floor(node->left, key);
        }

//        key大于node->key：右子树可能存在比node->key大，但是比key小的节点
//        如果存在上述情况，返回这个被选出来的节点
//        否则，函数最后返回node本身
        Node* tmp = floor(node->right, key);
        if(tmp != NULL) {
            return tmp;
        }

        return node;
    }

    Node* ceil(Node* node, Key key) {
        if(node == NULL) {
            return NULL;
        }
        if(node->key == key) {
            return node;
        }

        if(node->key < key) {
            return ceil(node->right, key);
        }

        Node* tmp = ceil(node->left, key);
        if(tmp != NULL) {
            return tmp;
        }

        return node;
    }
public:
    Key* floor(Key key) {
        Key* min_key = this->minimum();
        if(this->isEmpty() || key < *min_key) {
            return NULL;
        }
//        floor node
        Node *node = floor(this->root, key);
        return &(node->key);
    }

    Key* ceil(Key key) {
        Key* max_key = this->maximum();
        if(this->isEmpty() || key > *max_key) {
            return NULL;
        }
//        ceil node
        Node* node = ceil(this->root, key);
        return &(node->key);
    }
```

## 4. 代码测试

第 3 部分实现的测试代码地址：[https://gist.github.com/dongyuanxin/759d16e1ce87913ad2f359d49d5f5016](https://gist.github.com/dongyuanxin/759d16e1ce87913ad2f359d49d5f5016)。

这是 Github 的 GIST，请自备梯子。

## 5. 拓展延伸

**考虑一种数据类型，如果是基本有序的一组数据，一次`insert`进入二叉搜索树，那么，二叉搜索树就退化为了链表。此时，上述所有操作的时间复杂度都会退化为 $O(log_2 N)$。**

为了避免这种情况，就有了红黑树等数据结构，来保证树的平衡性：左右子树的高度差小于等于 1。

## 6. 致谢

本篇博客是总结于慕课网的[《学习算法思想 修炼编程内功》](https://coding.imooc.com/class/chapter/71.html)的笔记，强推强推强推。

[二分搜索树的删除节点操作](https://blog.csdn.net/qq_19782019/article/details/78882392)
