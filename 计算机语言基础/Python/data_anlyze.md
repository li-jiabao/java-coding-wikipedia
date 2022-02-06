---
title: data_anlyze
author: lijiabao
date: 2020-11-13 00:57:50.025757800 +0800
category: python_notebook
categories: python_notebook
tags: python_notebook
---
#### 数据清洗的步骤
1. 读取数据：`pd.read_****()`
2. 数据预览：`data.info(),data.describe()`
3. 检查null值：`data1 = data.copy(),data1.isnull().sum()`
4. 补全空值：
```python
data1['age'].fillna(data1['age'].median(), inplace=True)
data1['ddd'].fillna(data1['ddd'].mode()[0], inplace=True)
data1.isnull().sum()
```
5. 去掉一些无用的数据列，增加一些必须的数据列
```python
del_columns = ['hhh', 'ddd']
data1.drop(del_column, axis=1, inplace=True)  # 删除列
data1['新增的'] = data1['age'] + data1['sss'] + 1 # 增加列
data1['新增的分箱的列'] = pd.qcut(data1['sss'], 4)  # 分箱
data1['agecut'] = pd.cut(data1['age'].astype(int), 6)  # 分箱
```
6. `sklearn的labelencode编码处理让算法模型可以认出，并用get_dummies()将长dataframe变为宽dataframe`
7. 最后再检查一下清洗完的数据