---
title: python中的图的存储
mathjax: true
categories: '-社交网络'
abbrlink: 39894
date: 2021-11-08 08:12:45
tags:
---

### python中图的存储形式以及度的计算

#### 邻接表的方式 （adjacency list）

```python
import numpy as np
filename = "sns_datasets/karate/karate_edges.txt"

n = 34

# 使用邻接表的方式
def adjacency_list(filename):
    mydict = {}
    with open(filename) as f:
        for line in f.readlines():
            temp_list = line.split()
            start = temp_list[0];
            end = temp_list[1];

            # 存储start -> end
            if(start not in mydict):
                mydict[start] = []
            start_list = mydict[start]
            start_list.append(end)
            mydict[start] = start_list

            # 存储end结点 end -> start
            if(end not in mydict):
                mydict[end] = []
            end_list = mydict[end]
            end_list.append(start)
            mydict[end] = end_list
    print(mydict)
   
if __name__ == '__main__':
    #adjacency_list(filename)
    #adjacency_matric(filename)
    get_degree(filename)
```

<!--more-->

#### 输出结果

![image-20211106111559797](/Users/chenweiduo1512/Library/Application Support/typora-user-images/image-20211106111559797.png)

#### 邻接矩阵的形式 （adjacency matrix）

```python
import numpy as np
filename = "sns_datasets/karate/karate_edges.txt"

# 对于无向图来说
n = 34
def adjacency_matric(filename):
    # 使用邻接矩阵的形式必须要先初始化矩阵的大小
    # 如果在不知道矩阵的大小的时候，可以先算出来，就是里面结点的个数
    adjacency_Matric = np.zeros((n,n))
    with open(filename) as f:
        for line in f.readlines():
            temp_list = line.split()
            # 对于一个无向图来说
            adjacency_Matric[int(temp_list[0])-1][int(temp_list[1])-1] = 1
            adjacency_Matric[int(temp_list[1])-1][int(temp_list[0])-1] = 1
    print(adjacency_Matric)

```

#### 输出结果

![image-20211106112454590](/Users/chenweiduo1512/Library/Application Support/typora-user-images/image-20211106112454590.png)

#### 计算结点的度 compute degree

```python
# 计算结点的度
def get_degree(filename):
    mydict = {}
    with open(filename) as f:
        for line in f.readlines():
            temp_list = line.split()
            start = temp_list[0]
            end = temp_list[1]

            if start not in mydict:
                mydict[start] = 0
            if end not in mydict:
                mydict[end] = 0
            mydict[start] += 1
            mydict[end] += 1
    print(mydict)
```

#### 输出结果

![image-20211106112854921](/Users/chenweiduo1512/Library/Application Support/typora-user-images/image-20211106112854921.png)

