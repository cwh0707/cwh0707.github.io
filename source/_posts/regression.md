---
title: regression
mathjax: true
tags:
  - regression
categories:
  - 深度学习
  - 回归预测
abbrlink: 49227
date: 2021-12-13 08:08:08
---

## HomeWork1

```python
import sys
import pandas as pd
import numpy as np

#读取数据
data = pd.read_csv('./train.csv',encoding = 'big5')

#数据预处理部分 对于字符型的数据进行替换
data = data.iloc[:, 3:] #取出所有的数据部分
data[data == 'NR'] = 0  #将数据中的为NR的部分置为0
raw_data = data.to_numpy()

#特征提取部分1
#将每个月的数据读取出来，每个月的数据存储在month_data中，注意需要按照特定的格式来
month_data = {}
for month in range(12):
    sample = np.empty([18, 480]) #先初始化每个月的数据 18个维度 一天24小时一个月取20天
    for day in range(20):
        sample[:, day * 24 : (day + 1) * 24] = raw_data[18 * (20 * month + day) : 18 * (20 * month + day + 1), :]
    month_data[month] = sample

#每个月有480h 我们将每9个小时看做是一个data，那么就可以用480-9=471个数据
#所以一年共有 12*471个data 对于每个data是一个18*9的特征features
x = np.empty([12 * 471, 18 * 9], dtype = float) #将18*9平铺成一维的了
y = np.empty([12 * 471, 1],dtype = float)  # 想要预测的值
for month in range(12):
    for day in range(20):
        for hour in range(24):
            if day == 19 and hour > 14:  #
                continue
            x[month * 471 + day * 24 + hour, :] = month_data[month][:,day * 24 + hour: day * 24 + hour + 9].reshape(1,-1)
            y[month * 471 + day * 24 + hour, 0] = month_data[month][9,day * 24 + hour + 9]
print(x)
print(y)

# 标准化
mean_x = np.mean(x, axis= 0)  # 计算每行的均值
std_x = np.std(x, axis= 0)   # 计算每行的方差
for i in range(len(x)):  # 12 * 471 默认取row
    for j in range(len(x[0])):  # 18*9
        if std_x[j] != 0:
            x[i][j] = (x[i][j] - mean_x[j]) / std_x[j]  #归一化


tmp = np.empty([172,471])
print(len(tmp))  #得到行
print(len(tmp[0])) #得到列


# 分割一部分数据集作为验证集
import math
x_train_set = x[: math.floor(len(x) * 0.8), :]  #取前百分之80的数据作为训练集
y_train_set = y[: math.floor(len(x) * 0.8), :]
x_validation = x[math.floor(len(x) * 0.8):, :]
y_validation = y[math.floor(len(x) * 0.8):, :]

# 训练部分
dim = 18 * 9 + 1
w = np.zeros([dim, 1])
x = np.concatenate((np.ones([12 * 471, 1]), x),axis = 1).astype(float)
learning_rate = 100
item_time = 1000
adagrad = np.zeros([dim, 1])
eps = 0.0000000001
for t in range(item_time):
    loss = np.sqrt(np.sum(np.power(np.dot(x,w)-y,2))/471/12) #rmse
    if(t % 100 == 0):
        print(str(t) + ":" + str(loss))
    gradient = 2 * np.dot(x.transpose(), np.dot(x, w) - y)
    adagrad += gradient ** 2
    w = w -learning_rate * gradient / np.sqrt(adagrad + eps)
np.save('weight.npy', w)

# 测试
testdata = pd.read_csv("./test.csv",header = None,encoding='big5')
test_data = testdata.iloc[: , 2:]
test_data[test_data == 'NR'] = 0
test_data = test_data.to_numpy()
test_x = np.empty([240 , 18 * 9],dtype=float)
for i in range(240):
    # test_x[i, :] = test_data[18 * i:18*(i+1),:].reshape(1,-1)
    test_x[i, :] = test_data[18 * i: 18 * (i + 1), :].reshape(1, -1)
for i in range(len(test_x)):
    for j in range(len(test_x[0])):
        if std_x[j] != 0:
            test_x[i][j] = (test_x[i][j] - mean_x[j]) / std_x[j]
test_x = np.concatenate((np.ones([240,1]),test_x),axis= 1).astype(float)
print(test_x)

#预测部分
w = np.load('weight.npy')
ans_y = np.dot(test_x, w)


#保存结果到csv部分中去
import csv
with open('submit.csv',mode='w',newline='') as submit_files:
    csv_writer = csv.writer(submit_files)
    header = ['id','value']
    print(header)
    csv_writer.writerow(header)
    for i in range(240):
        row = ['id_' + str(i), ans_y[i][0]]
        csv_writer.writerow(row)
```

