---
title: python_data_anlysis_notes
author: lijiabao
date: 2020-11-13 10:32:06.720598300 +0800
category: python_notebook
categories: python_notebook
tags: python_notebook
---
## python数据分析笔记

```python
import re
import pandas as pd
import numpy as np

# pandas的属性设置：
pd.set_option('max_rows', 10)

datas = {'hello': np.random.rand(10)}
data = pd.DataFrame(datas)
# 1. 数据读取：
data = pd.read_csv('F:/data/csv_documents/box/thesis_data.xlsx')

# 2. 异常值或者特殊值的查询
data.isnull()   # 查询空值还可以series系列的isnull方法来查看列数据的空值情况
data.duplicated()    # 查询是否有重复值，还可以查询series的duplicated方法查看单列的重复值
data.drop_duplicates(['word'], keep='last')    # 去除列表的列中的重复值并保留最后一个重复值
data['word'].unique()    # 查询所有的数据中的唯一值，也有单列数据的该方法
data.isin(['hello', 1, 5])    # 查询是否有数据在该列表的数据中
data[data['word'].str.contains('王', na=False)]    # 模糊匹配查找word中包含王的数据
data[data['word'].between(1, 5)]    # 查找word列在1到5的行
# data[~((df['word'] < 1) | (data['word'] < 5))]等价于下面的
# ~代表取反也就是非，|代表或的意思
data[(data['word'] > 1 & data['word'] < 5)]   # 查找word列的大于1小于5的数据

# 3. 删除重复或缺失
data.drop_duplicates(['word', 'definition'], keep='first', inplace=True)
# 参数代表的意思：删除列表中两列的重复值并保留第一项重复，并保留更改到数据
data.dropna(subset=['word'], how='all', axis=0)
# 参数：subset是指删除空值的子列，how代表删除整行全部为空值的行，axis代表操作的方向为列还是行

# 4. 补全缺失
data.fillna('hhh')
data.fillna(method='bfill')
# 指定使用相邻的值来填充，使用method参数来指定，bfill是空值下面行的值填充
data.fiina({'word': 'hhh', 'definition': '你好'})
# 使用字典的数据来指定特定行列的填充值

# 5. 数据排序
data.sort_values(axis=1, ascending=True)
# 对列的数据进行排序操作
data.sort_indexs()
# 对数据的index进行排列操作
data.reset_index()
# 重置索引，对索引顺序出现混乱时可以使用


# 对接excel的python的pandas的常见操作

data = pd.read_excel('F:/data/csv_documents/box/thesis_data.xlsx')
# 1. 读取数据源

# 2. 查看一些基础信息
data.index   # 索引
data.columns   # 列名
data.dtypes   # 查看数据类型
data.ndim   # 查看数组维度
data.shape   # 查看数据的行列元组
data.describe()    # 查看描述信息
data.info()    # 查看详细信息
data.head(10)   # 查看前10行
data.tail(10)   # 查看后十行
data.loc['最大值']   # 查看指定行列数据
data.loc[-(data['最大值'] > 5)]    # 删除最大值列中大于5的行
data.rename(columns=list('heldfo'))   # 修改列名
data.iloc[1:2]   # 查看指定的行列数据

# 3. 描述性统计：
data.describe()
data.info()
data.unique()   # 查看唯一值
data.value_counts()   # 查看数值统计情况，也可指定行的该方法
bins = [i * 10 for i in range(1, 11)]
labels = [f'{(i - 1) * 10 - {i * 10}}' for i in range(1, 10)]
data_cut = data.cut(data['最大值'], bins, labels=labels)
# 将列数据含特定的分割组类进行分组分段统计
data_cut.value_counts(sort=False)
df = pd.concat([data, data_cut], axis=1, ignore_index=True)   # 利用合并增加一列
df_group = df['最大值'].groupby([df[0], df[1], df[7]])   # 多维数据的分组
df_fum = df_group.agg('count')    # 频数统计

# 4. 缺失值处理
df[df.isnull().values == True]    # 显示有缺失值的行
df_na = (df.isnull()).sum(axis=1)   # 统计每行的缺失值数量
df = pd.concat([df, df_na], axis=1)   # 拼接
df = df.loc[df['na_num'] >= 5]    # 筛选出满足条件的行
df.dropna()    # 删除含缺失值的行
df.dropna(how='any')    # 删除掉其中行全为空值的行
df.fiina(0)   # 填充固定值
df.fillna(df[df['最大值'].median()])   # 填充数据框架的特定数据特征值
df.fiina(0, method='pad')    # 填充前一条数据
df.fiina(0, method='bfill')   # 填充后一条数据

# 5. 数据筛选
df.loc[(df['最大值'] < 5) & (df['最大值'] > 1)]

# 6. 数据替换
df.map(lambda x: x.strip())   # 利用map函数进行特定的数据处理
df.replace({'最大值': '应届毕业生'}, '一年以下工作经验')   # 替换最大值列的应届毕业生为一年工作
df['最大值'].replace({'应届毕业生': '一年以下工作经验'})   # 选出列再替换也可

# 7. 数据排序
df.sort_values(by=['最大值', '最小值'], ascending=True)   # 对列表中的列进行排序

# 8. 数据关联
pd.merge(df, data, on='最大值', how='left')   # 对最大值进行左关联
pd.concat([df, data], axis=1, ignore_index=True)   # 合并
# 利用reduce对数据帧列表结合merge方法进行多表关联

# 9. 数据聚合
df_group = df['最大值'].groupby([df['最小值', '年均值']])  # 根据最小值和年均值对最大值进行聚合
df_group.agg(['max', 'median', 'sum'])    # 对df_group进行聚合运算，最大值，中位数，求和

# 10. 数据透视表
pd.pivot_table(
    df, index=['最大值'], columns=['最小值'],
    values=['年均值'], aggfunc=[np.sum, np.median, np.max], fill_value=0)
# index代表数据透视表的列，columns是行，values是进行分类的值
# aggfunc代表函数处理，fill_value是空值填充值


# 数据清洗
# 1. 插入数据
df['hello'] = [i for i in range(20)]
df.insert(0, '你好', [i + 10 for i in range(20)])  # 在列索引下增加一列名为**的数据
df.append(data, ignore_index=True, sort=False)    # 增加数据，要求列一致

# 2. 删除行列
df.drop([0, 1])   # 删除第一行第二行
df.drop(['hello'], axis=1)    # 删除列


# 3. 数据替换
df.replace(['dd', 'ss'], '', regex=True)   # 替换字符， 还可使用正则表达式
df.loc[0, 'hello'] = 0  # 查询到的数据可以直接进行复制修改

# 4. 数据查询
# 正则匹配进行查询
df[df.hello.str.match('[jk]', flags=re.IGNORENCE, na=False)]
card = ['A', 30]
# query()函数贼好用，学会用查询数据很方便
# 查询index大于小于5且hello列不包含A和30的数据，还可直接在表达式中使用统计函数
df.query(
    '1 <= index <= 5 and hello not in @card'
)
# 查询最大值大于最大值平均数且hello列为A的数据
df.query(
    '最大值 > 最大值.mean() and hello == "A"')

```