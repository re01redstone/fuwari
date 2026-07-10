---
title: 并查集
published: 2026-03-05
description: 几乎O(1)的处理集合
image: ""
tags: []
category: ""
draft: false
lang: ""
---
# 什么是并查集?
并查集是一种数据结构, 当需要维护一个集合的时候, 并查集可以以**几乎**$O(1)$的的代价实现集合的合并和查询属于(是否在同一集合内)的操作.
# 实现
首先, 可以把集合抽象为一个森林, 用一个数组`fa`存储, 其中`fa[i]`表示i的父亲.

最开始, 所有元素都是一颗以自己为根的树, 存储为`fa[i]=i`, 即i为自己的父亲(根节点)
- 合并 - 将b的祖先指向a的祖先
- 查询 - 判断a和b是不是同一个祖先
那么, 首先就需要找到`fa[i]`的祖先是谁.
## find(x)
其实很简单.
1. 顺着`fa[x]`找节点的父亲
2. 当`fa[x]=x`时说明i为自己的父亲(根节点), 返回x
当然, 也可以在找的过程中优化树的结构, 我们让中途的`fa[x]`直接指向祖先, 下次就不用找很多次.
### 代码实现
```cpp
int find(int x){
    if(fa[x]==x){// 当fa[x]=x时说明i为自己的父亲(根节点)
        return x;
    }
    return fa[x]=find(fa[x]);// 继续找父亲的父亲, 顺便将返回的祖先赋值
}
```
或者用三目运算符直接写成一行
```cpp
int find(int x) return fa[x]==x?x:fa[x]=find(fa[x]);
```
所以, 两个操作即为:
- 合并 - `fa[find(a)]=find(b)`, *为什么不是*`fa[a]=find(b)`--因为当a属于b集合时, a的所有父亲也合并进去了
- 查询 - `find(a)==find(b)`, 也可以写成`find(fa[a])==find(fa[b])`不过没区别, 而且有病:)
# P3367 【模板】并查集

## 题目描述

如题，现在有一个并查集，你需要完成合并和查询操作。

## 输入格式

第一行包含两个整数 $N,M$ ,表示共有 $N$ 个元素和 $M$ 个操作。

接下来 $M$ 行，每行包含三个整数 $Z_i,X_i,Y_i$ 。

当 $Z_i=1$ 时，将 $X_i$ 与 $Y_i$ 所在的集合合并。

当 $Z_i=2$ 时，输出 $X_i$ 与 $Y_i$ 是否在同一集合内，是的输出 
 `Y` ；否则输出 `N` 。

## 输出格式

对于每一个 $Z_i=2$ 的操作，都有一行输出，每行包含一个大写字母，为 `Y` 或者 `N` 。

## 输入输出样例 #1

### 输入 #1

```
4 7
2 1 2
1 1 2
2 1 2
1 3 4
2 1 4
1 2 3
2 1 4
```

### 输出 #1

```
N
Y
N
Y

```

### 示例代码
```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll N=1e6;
ll fa[N],n,m;
int find(ll x){// 找爹函数
    if(fa[x]==x){// 当fa[x]=x时说明i为自己的父亲(根节点)
        return x;
    }
    return fa[x]=find(fa[x]);// 继续找父亲的父亲, 顺便将返回的祖先赋值
}
int main(){
    cin>>n>>m;
    for(int i=1;i<=n;i++) fa[i]=i;
    for(int i=1;i<=m;i++){
        int z,x,y;
        cin>>z>>x>>y;
        if(z==1){// 合并
            fa[find(x)]=fa[find(y)];//将x的祖先指向y的祖先
        }else{// 查询
            if(find(x)==find(y)){// 判断a和b是不是同一个祖先
                cout<<"Y"<<"\n";
            }
            else{
                cout<<"N"<<"\n";
            }
        }
    }
    return 0;
}
```