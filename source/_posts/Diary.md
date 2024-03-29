---
title: Diary
mathjax: true
tags: Acwing
categories:
  - Acwing
  - 二叉树
password: whyzznsb.
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
abbrlink: 37773
date: 2021-10-15 19:13:11
---

### 题目描述

给定你一个长度为n的整数数列。

请你使用归并排序对这个数列按照从小到大进行排序。

并将排好序的数列按顺序输出。
<!--more-->

输入格式

输入共两行，第一行包含整数 n。

第二行包含 n 个整数（所有整数均在1~109范围内），表示整个数列。

输出格式

输出共一行，包含 n 个整数，表示排好序的数列。

数据范围

1 ≤ n ≤ 100000


#### 样例

输入样例
```
5
3 1 2 4 5
```
输出样例

```
1 2 3 4 5
```

----------

### 算法思想

归并排序的算法步骤如下：

1. 先确定分界点，由于归并排序的时间复杂度是确定并不像快速排序那样不一定，所以分界点的取值为中间值也就是 l + r  >> 1

2. 划分区间，将区间划分为左半部分和右半部分，[l,mid],[mid+1,r]

3. **核心步骤** 两个子区间合并

```
1.先判断两个区间是否归并完成，根据双指针的i，j来判断将哪个值放入临时数组tmp中去

2.若某一区间已经全部完成排序，但还有一个序列未完成排序，那么将对应那个序列后面的所有元素全部放置在tmp中去

3.将tmp的值复制到q数组当中去，注意复制的边界情况
```

#### 时间复杂度

O(nlogn)

#### C++ 代码
```c++
#include <iostream>
using namespace std;

const int N = 100010;
int q[N];

int tmp[N];

void merge_sort(int q[],int l,int r){
    //递归边界 当边界划分的时候划分到1或者0的时候需要退出
    if(l >= r) return ;  
    int mid = l+r >> 1;
    //划分区间 
    merge_sort(q,l,mid);
    merge_sort(q,mid+1,r);
    //合并操作
    int k = 0;  //保存当前tmp数组中已经含有多少个值的情况
    int i = l,j = mid+1;  //双指针 一个指向第一个序列的第一个元素，一个指向第二个序列的第一个元素
    while (i <= mid && j <= r) {//说明序列还未合并完成
        if(q[i] <= q[j]) tmp[k++] = q[i++]; 
        else tmp[k++] = q[j++];  //挑选两者之间最小的值放到tmp最前面的位置上面去
    }
    while(i<=mid) //说明第一个序列还未完全排序
        tmp[k++] = q[i++];
    while(j<=r) 
        tmp[k++] = q[j++];
        
    //函数应该要有返回值，在这里将得到的值放到q中即可
    for(int i=l,j=0;i<=r;i++,j++) //从划分区间段的最小边界l开始复制，而不是从i=0开始，应该从区间角度来看问题
        q[i] = tmp[j];
}

int main(){
    int n;
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        scanf("%d",&q[i]);
    }
    merge_sort(q,0,n-1);
    for(int i=0;i<n;i++){
        printf("%d ",q[i]);
    }
    return 0;
}
```

### latex公式测试

display行间公式
$$
i\hbar\frac{\partial}{\partial t}\psi=-\frac{\hbar^2}{2m}\nabla^2\psi+V\psi
$$
inline行内公式

行间公式 inline $O(f(n))=log_2^{n}$测试

