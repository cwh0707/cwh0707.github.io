---
title: 数据结构day02
mathjax: true
tags:
  - 二分查找
  - 大整数加法、减法
categories:
  - 数据结构与算法
abbrlink: 50322
date: 2021-12-15 07:17:48
---

## 二分查找

### 整数二分

> 二分的本质是什么？
>
> 二分的本质是单调性吗？其实并不是的
>
> 有单调性的一定能二分，但能二分的题目并不一定强制要求有单调性
>
> 综上所述，我们可以得出结论二分的本质是边界问题
>
> 我们需要找到一个边界情况，可以清晰的将区间分成两段来处理
>
> 这里需要注意：二分是一定有结果的，我们每次二分之后一定要保证答案可以落在划分之后的区间里

<!-- more -->

#### 算法描述：

对于整数二分，需要处理两种情况，一种是分界点在左边，一种是分界点在右边，具体画图可以看自己的Note

> 对于寻找左边的边界点的情况

1. 先计算中间值 mid = l + r >> 1;

2. 写一个判断函数check，这个check( )内部的条件就是划分边界的条件

   if (check)

   为真：说明答案在[l , mid]之间，只要令 r = mid即可，更新区间

   为假：说明答案在[mid + 1, r]之间，只要令 l = mid + 1，更新区间

3. 直到 l == r 的时候，说明区间不可在划分，此时对应的元素就是想找到的答案

> 对于寻找右边的边界点的情况

1. 先计算中间值 mid = l + r + 1 >> 1;

2. 写一个判断函数check，这个check( )内部的条件就是划分边界的条件

   if (check)

   为真：说明答案在[mid , r]之间，只要令 l = mid即可，更新区间

   为假：说明答案在[l, mid - 1]之间，只要令 r = mid - 1，更新区间

3. 直到 l == r 的时候，说明区间不可在划分，此时对应的元素就是想找到的答案

说明：此处为什么需要取mid时要额外加1呢？

因为当出现l 与 r之间相差1的时候，如果进行 l + r >> 1的话，会发生死循环，可以带个样例模拟一下

> 对于如何记忆的问题来说：
>
> 只要判断check函数为true的情况
>
> l = mid   ==> 对应于 l + r + 1 >> 1;
>
> r = mid  ==> 对应于 l + r >> 1;

---



题目描述：

给定一个按照升序排列的长度为 n 的整数数组，以及 q 个查询。

对于每个查询，返回一个元素 k 的起始位置和终止位置（位置从 0 开始计数）。

如果数组中不存在该元素，则返回 `-1 -1`。



输入格式：

第一行包含整数 n 和 q，表示数组长度和询问个数。

第二行包含 n 个整数（均在 1∼10000 范围内），表示完整数组。

接下来 q 行，每行包含一个整数 k，表示一个询问元素。



输出格式：

共 q 行，每行包含两个整数，表示所求元素的起始位置和终止位置。

如果数组中不存在该元素，则返回 `-1 -1`。



数据范围：

1≤n≤100000
1≤q≤10000
1≤k≤10000



输入样例：

```
6 3
1 2 2 3 3 4
3
4
5
```



输出样例：

```
3 4
5 5
-1 -1
```

#### 思路：

+ 将最左边第一个>=x的数看做边界条件，找左边边界的情况
+ 将最右边第一个<=x的数看做边界条件，找右边边界的情况

#### C++代码实现

```c++
#include <iostream>

using namespace std;

const int N = 1e5 + 10;

int q[N];

int main()
{
    int n ,m;
    cin >> n >> m;
    for(int i = 0; i < n; i++ ) scanf("%d",&q[i]);
    while( m-- )
    {
        int k;
        cin >> k;

        int l = 0, r = n - 1;
        while(l < r)
        {
            int mid = (l + r) >> 1;
            if(q[mid] >= k) r = mid;
            else l = mid + 1;
        }
        
        if(q[l] != k) cout << "-1 -1" << endl;
        else 
        {
            cout << l << " ";
            int l = 0, r = n - 1;
            while( l < r)
            {
                int mid = (l + r + 1) >> 1;
                if(q[mid] <= k) l = mid;
                else r = mid - 1;
            }
            cout << l << endl;
        }
    }
    return 0;
}
```

### 浮点数二分

#### 算法描述：

> 浮点数二分不像整数二分一样，它无需判断是否是左边还是右边的问题
>
> 它可以严格遵守 l + r >> 1
>
> 容易出现问题的部分在于：在更新区间的时候需要注意区间的范围，对于是否需要mid + 1的情况需要额外考虑一下，容易产生超时的问题



题目描述：

对于大于1的正整数求它的算术平方根



#### C++代码实现

```c++
#include <iostream>

using namespace std;

int main()
{
    double x;
    cin >> x;
    double  l = 0, r = x;
    while( r - l >= 1e-8)
    {
        double mid = (l + r) / 2;
        if(mid * mid >= x)  r = mid;
        else l = mid; //注意这里还是mid 因为+1会导致超时，因为1范围对于区间来说还是太大了
    }
    cout << l << endl;
    return 0;
}
```

## 大整数加法、减法

### 大整数加法

> 本质上来说其实是一个模拟题，重点需要关注的是如何得到通式，以及如何处理一些大数据的输入输出问题

#### 需要解决的问题

1. 大整数的存储 

   对于存储问题，我们使用数组vector存储，并且是倒序存储，为什么是倒序呢？倒序的话数组末尾对应的最高位，这样在最后进位的时候方便添加最高位

2. 关于进位的问题，抽象出一个通式

   对于每一位可以得到 A[i] + B[i] + t , t为进位

3. 处理一些细节问题，例如前导0等

---



题目描述：

给定两个正整数（不含前导 0），计算它们的和。



输入格式：

共两行，每行包含一个整数。



输出格式：

共一行，包含所求的和。



数据范围：

1≤整数长度≤100000



输入样例：

```
12
23
```



输出样例：

```
35
```

#### C++代码实现

```c++
#include <iostream>
#include <vector>
using namespace std;

const int N = 1e5 + 10;

vector<int> add(vector<int> &A, vector<int> &B)
{
    vector<int> C; //保存结果
    int t = 0; //进位
    for(int i = 0; i < A.size() || i < B.size(); i++)
    {
         if( i < A.size())t += A[i];
         if( i < B.size())t += B[i];
         C.push_back(t % 10);
         t /= 10;
    }
    if( t != 0) C.push_back(1);
    return C;
}

int main()
{
    string a,b;
    //读入字符串
    cin >> a >> b;
    vector<int> A,B;
    //存储大整数
    for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0');
    for(int i = b.size() - 1; i >= 0; i--) B.push_back(b[i] - '0');
    vector<int> C = add(A,B);
    for(int i = C.size()-1; i >= 0; i --) cout << C[i];
    return 0;
}
```

### 大整数减法

> 同加法类似，需要多做一步判断就是需要先判断两个相减的数组哪个大，然后转换一下，如果小减大，转换为绝对值计算并加个负号就好了
>
> 需要考虑一些问题：
>
> 例如前导0如何消除
>
> 如何对于两个数组来判断两者的大小
>
> 对于借位如何来判断？
>
> 借位的通式 先令 t = 0；
>
> 先t = A[i] - t;然后判断B[i] 是否还有元素，如果有则 t = t - B[i];
>
> 然后保存的是（t + 10 ）% 10 这个写法两者都考虑到了（借位或者不借位）
>
> 更新 t 如果 t > 0 则够无需借位 t = 0
>
> 否则 t < 0 需要借位 则 t = 1



---



题目描述：

给定两个正整数（不含前导 0），计算它们的差，计算结果可能为负数。



输入格式：

共两行，每行包含一个整数。



输出格式：

共一行，包含所求的差。



数据范围：

1≤整数长度≤1e5



输入样例：

```
32
11
```



输出样例：

```
21
```

#### C++代码实现

```c++
#include <iostream>
#include <vector>

using namespace std;

bool cmp(vector<int> &A, vector<int> &B)
{
    if(A.size() != B.size()) return A.size() > B.size();
    for(int i = A.size() - 1; i >= 0 ;i--) //说明两者长度相同
    {
        if(A[i] != B[i])  return A[i] > B[i];
    }
    //两者都相同的情况
    return true;
}

vector<int> sub(vector<int> &A,vector<int> &B)
{
    vector<int> C;  //存储答案
    int t = 0; //借位
    for(int i = 0 ; i < A.size() ; i++)
    {
        t = A[i] - t; //首先被减数减去借位
        if( i < B.size()) t = t - B[i]; //如果B数组中对应位还有元素的话就要减去B[i]
        C.push_back( (t + 10) % 10);  //包含了有借位和没有借位的两种情况
        //更新t
        if( t < 0 ) t = 1; //有借位
        else t = 0;  //无借位
    }
    
    //删除前导0  需要考虑为0的情况
    while(C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}

int main()
{
    string a, b;
    cin >> a >> b;
    vector<int> A,B,C;
    //存储数组
    for(int i = a.size() - 1; i >= 0; i--) A.push_back(a[i] - '0');
    for(int i = b.size() - 1; i >= 0; i--) B.push_back(b[i] - '0');
    
    if(cmp(A, B))
    {
        C = sub(A, B);
        for(int i = C.size() - 1; i >= 0; i--) cout << C[i];
    }
    else
    {
        C = sub(B, A);
        cout << "-";
        for(int i = C.size() - 1; i >= 0; i--) cout << C[i];
    }
    return 0;
}
```

需要思考的输入输出有点多，需要小心，多加训练这种处理能力，就酱紫~

