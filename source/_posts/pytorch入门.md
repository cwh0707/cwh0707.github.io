---
title: pytorch入门
mathjax: true
abbrlink: 10490
date: 2022-11-26 00:30:04
tags: pytorch
categories:
---

# 张量 Tensor

## Tensor初始化

1. 根据给定数据初始化，也就是我的矩阵直接喂给你

`data=[[1, 2],[3, 4]]`

2. 通过使用Numpy数组生成

```
np_array = np.array(data)
x_np = torch.from_numpy(np_array)
```

注: 实现将一个numpy数组使用`from_numpy`实现从numpy转换为tensor

<!--more-->

3. 使用已有的Tensor生成新的Tensor

```
x_ones = torch.one_like(x_data) # 保留x_data的属性
print(f"Ones Tensor: \n{x_ones} \n")

x_rand = torch.rand_like(x_data, dtype = torch.float)  # 更改x_date的属性：int -> float
print(f"Random Tensor: \n {x_rand} \n")
```

+ 新的Tensor将继承已有Tensor的数据属性，也可以指定新的数据类型

+ `print(f"xxxx{}xxx")`使用format格式的print打印输出，其中{}存放的可以是变量也可以是表达式

4. 通过指定数据维度来生成Tensor

记住，shape返回的是一个tuple元组类型，描述一下张量的维数，我以前一直很好奇，tensorflow和torch之间的张量到底有什么不同？？

```python
shape = (2,3,)  # 对应于两行三列
rand_tensor = torch.rand(shape)
ones_tensor = torch.ones(shape)
zeros_tensor = torch.zeros(shape)

print(f"Random Tensor: \n {rand_tensor} \n")
print(f"Ones Tensor: \n {ones_tensor} \n")
print(f"Zeros Tensor: \n {zeros_tensor}")

‘’‘
>>>
Random Tensor:
 tensor([[0.0266, 0.0553, 0.9843],
         [0.0398, 0.8964, 0.3457]])

Ones Tensor:
 tensor([[1., 1., 1.],
         [1., 1., 1.]])

Zeros Tensor:
 tensor([[0., 0., 0.],
         [0., 0., 0.]])
’‘’
```

想一想，如果我的shape是一个三元组呢，去看看效果，例如`shape=（2,3,2）`

```python
shape = (2, 3, 2)
x_data = torch.rand(shape)
print(f"This is a random tensor.\n{x_data}")

>>>
This is a random tensor.
tensor([[[0.6546, 0.0505],
         [0.1988, 0.0101],
         [0.6763, 0.0885]],

        [[0.4273, 0.9512],
         [0.1284, 0.7970],
         [0.5022, 0.6480]]])
```

从以上我们可以看到，当shape是一个二元组的情况下的话，那么`(2,3,)`这代指的是2行3列的矩阵，但是对于`（2,3,2）`这种形式上来看的话，从结果分析来看可以看出第一个是第三维度的情况，反而生成了2个`3*2`的矩阵，所以在这里我个人感觉应该第一个参数应该是第三维的长度

## Tensor的运算

1. **Tensor的索引和切片**

```python
data = torch.ones(4, 4)
data[:, 1] = 0
print(data)

>>>
tensor([[1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.]])
```

对于一个tensor来说，首先，行维度和列维度两者之间需要使用`,`之间进行分隔开来，前面的代表行数，后面代表列数，对于`data[:,1]`代表的是所有行，第一列的数据。

```
data = torch.ones(4, 4)
data[1:3, 2:4] = 0
print(data)

>>>
tensor([[1., 1., 1., 1.],
        [1., 1., 0., 0.],
        [1., 1., 0., 0.],
        [1., 1., 1., 1.]])
```

ok，fine这我们了解了非常熟悉的切片操作，我们应该跟python自带的list做个比较来看看，对比学习一下

```python
list = [[[1, 2, 3],'cwh',False],[[4,3,1],'lyr',True]]
print(f"one line\n {list[0]} \ntwo line \n{list[1][0:2]} \n")

>>>
one line
 [[1, 2, 3], 'cwh', False] 
two line 
[[4, 3, 1], 'lyr']
```

首先我们需要跟传统的list进行一个简单的对比分析我们可以看到，list内部进行索引的值只能为一个integer类型，我们可以把它等价于c++中的`vector`，唯一比较特别的点是索引必须使用的类型应该是`[]`这种类型

2. Tensor的拼接（非常重要，很多bug都出自这里）

拼接的方式可太多种了，可以参考一下numpy的广播机制，但是写程序的思维还是要有的，只知道文档所在，但是写不出来自己的程序其实是一件很痛苦的事情。解决办法就是多练，多尝试把自己的想法转换成代码的形式。

+ torch.cat

```python
data = torch.rand(2,3)
col_data = torch.cat([data, data, data], dim = 1) # dim = 1 是按照列
row_data = torch.cat([data, data, data], dim = 0) # dim = 0
print(f'row data is \n {row_data}  \n col data is\n {col_data}')

>>>

row data is 
 tensor([[0.3420, 0.5119, 0.3018],
        [0.9372, 0.5363, 0.3074],
        [0.3420, 0.5119, 0.3018],
        [0.9372, 0.5363, 0.3074],
        [0.3420, 0.5119, 0.3018],
        [0.9372, 0.5363, 0.3074]])  
 col data is
 tensor([[0.3420, 0.5119, 0.3018, 0.3420, 0.5119, 0.3018, 0.3420, 0.5119, 0.3018],
        [0.9372, 0.5363, 0.3074, 0.9372, 0.5363, 0.3074, 0.9372, 0.5363, 0.3074]])
```

+ torch.stack

```python

```

3. Tensor的点积和矩阵乘法

首先我们区分一下点积和矩阵乘法，点积要求的是两个矩阵同样大小，例如同行数同列数，（3,4）和（3,4）

矩阵乘法：要求后面一个矩阵的行数等于前面的一个矩阵的列数

```python
# 逐个元素进行相乘 例如点积
data = torch.rand(3,4)
data_mul = data.mul(data)
print(f'raw data\n{data} \n mul data\n{data_mul}')
print(f'tensor * tensor \n {data * data}') # 点积可以直接使用乘法符号进行实现

print()

# 矩阵乘法计算
matrix = torch.rand(3, 4)
matrix_mul = matrix.matmul(matrix.T)
print(f'raw matrix\n{matrix} \n mul matrix\n{matrix_mul}')
print(f'matrix * matrix \n {matrix @ matrix.T}')

>>>

raw data
tensor([[0.2064, 0.1075, 0.9492, 0.8438],
        [0.1446, 0.6204, 0.3425, 0.1634],
        [0.3915, 0.3551, 0.9497, 0.8890]]) 
 mul data
tensor([[0.0426, 0.0116, 0.9010, 0.7120],
        [0.0209, 0.3848, 0.1173, 0.0267],
        [0.1533, 0.1261, 0.9019, 0.7904]])
tensor * tensor 
 tensor([[0.0426, 0.0116, 0.9010, 0.7120],
        [0.0209, 0.3848, 0.1173, 0.0267],
        [0.1533, 0.1261, 0.9019, 0.7904]])

raw matrix
tensor([[0.4177, 0.2848, 0.1641, 0.7694],
        [0.7248, 0.9181, 0.2790, 0.0580],
        [0.8787, 0.1998, 0.5819, 0.2855]]) 
 mul matrix
tensor([[0.8744, 0.6546, 0.7391],
        [0.6546, 1.4494, 0.9993],
        [0.7391, 0.9993, 1.2322]])
matrix * matrix 
 tensor([[0.8744, 0.6546, 0.7391],
        [0.6546, 1.4494, 0.9993],
        [0.7391, 0.9993, 1.2322]])

```

可以很好理解，`tensor.mul`理解为矩阵点乘（等价于*运算符），`tensor.matmul`理解为矩阵乘法（等价于@运算符）

4. 自动赋值计算

一般来说，自动赋值的话末尾为增加一个`_`作为后缀，这种就是对所有元素都进行一次相同的操作

```
print(data)
data_t = data.add_(5)
data_t

>>>

tensor([[0.2064, 0.1075, 0.9492, 0.8438],
        [0.1446, 0.6204, 0.3425, 0.1634],
        [0.3915, 0.3551, 0.9497, 0.8890]])
tensor([[5.2064, 5.1075, 5.9492, 5.8438],
        [5.1446, 5.6204, 5.3425, 5.1634],
        [5.3915, 5.3551, 5.9497, 5.8890]])
```

