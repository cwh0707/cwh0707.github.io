---
title: test
date: 2021-10-13 08:00:39
tags: Acwing
---

## hello 

```c++
#include <iostream>
#include <cstdio>

using namespace std;

int main()
{
    for(int i=0;i<10;i++)
    {
        printf("hello,world!\n");
    }
    return 0;
}
```

---



<!--more-->

```c++
#include <iostream>
using namespace std;

typedef long long LL;
const int N = 100010;

int q[N],tmp[N];

LL res = 0;

void merge(int q[],int l,int r){
    //递归边界 当只有一个数或者为0个数返回
    if(l>=r) return ;
    int mid = l+r >> 1;
    //划分区间
    merge(q,l,mid);
    merge(q,mid+1,r);
    //双指针，k存储的是tmp中已经拍好序的数量
    int k=0,i=l,j=mid+1;
    while(i<=mid && j<=r){
        if(q[i] <= q[j]){
            tmp[k++] = q[i++];
        }else{
            tmp[k++] = q[j++];
            //计算q[j] j对应位置的逆序对数
            res += mid - i+1;
        }
    }
    //扫尾操作
    while(i<=mid){
        tmp[k++] = q[i++];
    }
    while(j<=r){
        tmp[k++] = q[j++];
    }
    //复制操作，哪里来的回哪去
    for(int i=l,j=0;i<=r;i++,j++){
        q[i] = tmp[j]; //注意j无须++
    }
}

int main(){
    int n;
    scanf("%d",&n);
    for(int i = 0;i<n;i++){
        scanf("%d",&q[i]);
    }
    merge(q,0,n-1);
    cout << res << endl;
    return 0;
}

```

