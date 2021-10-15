---
title: code
date: 2021-10-15 09:08:34
tags: code
---

##### 最大异或数

```c++
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010,M = 31*N;
int son[M][2],idx;
int a[N];

void insert(int x)
{
    int p=0;
    for(int i=30;i>=0;i--)
    {
        int u = x >> i & 1;  //取最高位数值位 
        if(!son[p][u]) son[p][u] = ++idx; //创建新结点
        p = son[p][u];
    }
}

int search(int x)
{
    int p = 0;
    int res = 0;
    for(int i=30;i>=0;i--)
    {
        int u = x >> i & 1;
        if(son[p][!u])  //如果相反的存在
        {
            p = son[p][!u];
            res += 1 << i; //加一并往左移i位 很巧妙
        }
        else
        {
            p = son[p][u]; //继续往下走
            res << i; 
        }
    }
    return res;
}

int main()
{
    int n;
    cin >> n;
    int res = 0;
    for(int i = 0; i<n;i++)
    {
        cin >> a[i];
        insert(a[i]); //插入
    }
    for(int i=0;i<n;i++)
    {
        res = max(res,search(a[i]));
    }
    cout << res;
    return 0;
}
```