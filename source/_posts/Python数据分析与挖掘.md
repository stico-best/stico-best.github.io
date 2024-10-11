---
title: Python数据分析与挖掘
tags: 5
abbrlink: c1c7733e
date: 2022-06-15 16:08:58
---

**数据挖掘与分析部分模块的实例及原理应用**

<!--more-->

# Numpy学习

| 姓名 | 语文 | 英语 | 数学 |
| :--: | :--: | :--: | :--: |
| 张飞 |  66  |  65  |  30  |
| 关羽 |  95  |  85  |  98  |
| 赵云 |  93  |  92  |  96  |
| 黄忠 |  90  |  88  |  77  |
| 典韦 |  80  |  90  |  90  |

## 题目具体要求

1、使用 Numpy 统计团队中成员的语文、英语、数学的平均成绩、最小成绩、最大成绩、方差、标准差。

2、把这些人的总成绩排序，得出名次进行成绩输出。



## **Numpy基本原理**

Numpy 是 Python 语言的一个第三方库，其支持大量高维度[数组](https://so.csdn.net/so/search?q=数组&spm=1001.2101.3001.7020)与矩阵运算。此外，NumPy 也针对数组运算提供大量的数学函数。Numpy 中定义的最重要的对象是称为 `ndarray` 的 N 维数组类型。 它描述相同类型的元素[集合](https://so.csdn.net/so/search?q=集合&spm=1001.2101.3001.7020)。 可以使用基于零的索引访问集合中的项目。

## 实现代码

```python
import numpy as np

persontype = np.dtype({'names': ['name', 'age', 'chinese', 'math', 'english', 'total'], 'formats': ['S32', 'i', 'i', 'i', 'i', 'i']})
peoples = np.array([("zhang fei", 18, 66, 30, 65, 0), ("guan yu", 19, 95, 98, 85, 0), ("zhao yun", 20, 93, 96, 92, 0), ("huang zhong", 21, 90, 77, 88, 0)], dtype=persontype)

ages = peoples[:]['age']
chineses = peoples[:]['chinese']
maths = peoples[:]['math']
englishs = peoples[:]['english']
total = peoples[:]['total']

print("各项平均值")
print("年龄：", np.mean(ages))
print("语文：", np.mean(chineses))
print("数学：", np.mean(maths))
print("英语：", np.mean(englishs))

print("-" * 10)

print("各项最大值") 
print("年龄：", np.amax(ages))
print("语文：", np.amax(chineses))
print("数学：", np.amax(maths))
print("英语：", np.amax(englishs))

print("-" * 10)

print("各项最小值")
print("年龄：", np.amin(ages))
print("语文：", np.amin(chineses))
print("数学：", np.amin(maths))
print("英语：", np.amin(englishs))

print("-" * 10)

print("各项标准差")
print("年龄：", np.std(ages))
print("语文：", np.std(chineses))
print("数学：", np.std(maths))
print("英语：", np.std(englishs))

print("-" * 10)

print("各项方差")
print("年龄：", np.var(ages))
print("语文：", np.var(chineses))
print("数学：", np.var(maths))
print("英语：", np.var(englishs))

print("-" * 10)

print("总成绩排序")
peoples[:]['total'] = chineses + maths + englishs
rank = sorted(peoples, key=lambda t: t[4], reverse=True)
for i in rank:
    print(i)
```

## 输出结果

```markdown
各项平均值
年龄： 19.5
语文： 86.0
数学： 75.25
英语： 82.5

各项最大值
年龄： 21
语文： 95
数学： 98
英语： 92

各项最小值
年龄： 18
语文： 66
数学： 30
英语： 65

各项标准差
年龄： 1.118033988749895
语文： 11.683321445547923
数学： 27.380421837510102
英语： 10.404326023342406

各项方差
年龄： 1.25
语文： 136.5
数学： 749.6875
英语： 108.25

总成绩排序
(b'zhao yun', 20, 93, 96, 92, 281)
(b'huang zhong', 21, 90, 77, 88, 255)
(b'guan yu', 19, 95, 98, 85, 278)
(b'zhang fei', 18, 66, 30, 65, 161)
```

------

# Pandas学习

学习数据结构：Series 和 DataFrame

Series 是个定长的字典序列。说是定长是因为在存储的时候，相当于两个 ndarray，这也是和字典结构最大的不同。因为在字典的结构里，元素的个数是不固定的。

Series 有两个基本属性：index 和 values。在 Series 结构中，index 默认是 0,1,2,……递增的整数序列，当然也可以自己来指定索引，比如 index=[‘a’, ‘b’, ‘c’, ‘d’]。

DataFrame 类型数据结构类似数据库表，它包括了行索引和列索引，可以将 DataFrame 看成是由相同索引的 Series 组成的字典类型。

(二)  Pandas数据清洗

1.删除 DataFrame 中的不必要的列或行；

2.重命名列名 columns，让列表名更容易识别；

3.去重复的值数据采集可能存在重复的行；

4.格式问题；

5.查找空值。

------

# Kaggle竞赛-Titanic乘客生存预测

决策树与AdaBoost实验

##  **目标任务**

（1） 学习决策树、AdaBoost的原理，特性；

（2） 学习使用Python编写决策树和AdaBoost分类器；

（3） 学习使用sklearn训练决策树和AdaBoost，熟悉数据挖掘的一般流程.

------

## **实验数据**

Kaggle数据挖掘竞赛平台之Titanic 乘客生存预测。问题描述：泰坦尼克海难是著名的十大灾难之一，究竟多少人遇难，各方统计的结果不一。Kaggle数据挖掘竞赛平台的数据集数据集格式为 csv，一共有两个文件：train.csv 是训练数据集，包含特征信息和存活与否的标签；test.csv: 测试数据集，只包含特征信息。

在训练集中，包括了以下字段，它们具体为：

| 字段        | 描述                     |
| :---------- | ------------------------ |
| PassengerId | 乘客编号                 |
| Survived    | 是否幸存                 |
| Pclass      | 船票等级                 |
| Name        | 乘客姓名                 |
| Sex         | 乘客性别                 |
| SibSp       | 亲戚数量（兄妹、配偶数） |
| Parch       | 亲戚数量（父母、子女数） |
| Ticket      | 船票号码                 |
| Fare        | 船票价格                 |
| Cabin       | 船舱                     |
| Embarked    | 登陆港口                 |

------

## **生存预测的关键流程**

要对训练集中乘客的生存进行预测，这个过程可以划分为两个重要的阶段：

准备阶段：首先需要对训练集、测试集的数据进行探索，分析数据质量，并对数据进行清洗，然后通过特征选择对数据进行降维，方便后续分类运算；

分类阶段：首先通过训练集的特征矩阵、分类结果得到分类器，然后将分类器应用于测试集。然后对分类器的准确性进行分析，并对模型进行可视化。

## **实验过程**

### 实验导入模块

```python
import xgboost as xgb
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import AdaBoostClassifier
from sklearn.metrics import accuracy_score
from sklearn.model_selection import cross_val_score
import pandas as pd
import numpy as np
import csv

```



### **数据探索**

-  info():了解数据表的基本情况，包括行数、列数、每列的数据类型、数据完整度；

-  describe():了解数据表的统计情况，包括总数、平均值、标准差、最小值、最大值等；

-  describe(include=[‘O’]):查看字符串类型（非数字）的整体情况；

-  head():查看前几行数据（默认是前 5 行）；

-  tail():查看后几行数据（默认是最后 5 行）。


具体代码如下：

```python
import pandas as pd
# 数据加载
train_data = pd.read_csv('Titanic.train.csv')
test_data = pd.read_csv('Titanic.test.csv')

# 数据探索
print(train_data.info())
print('-' * 30)
print(train_data.describe())
print('-' * 30)
print(train_data.describe(include=['O']))  # include后面是大写的O字母
print('-' * 30)
```

数据读取结果：

```markdown
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   PassengerId  891 non-null    int64  
 1   Survived     891 non-null    int64  
 2   Pclass       891 non-null    int64  
 3   Name         891 non-null    object 
 4   Sex          891 non-null    object 
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64  
 7   Parch        891 non-null    int64  
 8   Ticket       891 non-null    object 
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    object 
 11  Embarked     891 non-null    object 
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB
None
```



### **数据清洗**

通过数据探索，我们发现 Age、Fare 和 Cabin 这三个字段的数据有所缺失。其中 Age 为年龄字段，是数值型，我们可以通过平均值进行补齐；Fare 为船票价格，是数值型，我们也可以通过其他人购买船票的平均值进行补齐。具体实现的代码如下：

```python
# 使用平均年龄来填充年龄中的nan值
train_data['Age'].fillna(train_data['Age'].mean(), inplace=True)
test_data['Age'].fillna(test_data['Age'].mean(), inplace=True)

# 使用票价的均值填充票价中的nan值
train_data['Fare'].fillna(train_data['Fare'].mean(), inplace=True)
test_data['Fare'].fillna(test_data['Fare'].mean(), inplace=True)
print(train_data['Embarked'].value_counts())

# 使用登录最多的港口来填充登录港口的nan值
train_data['Embarked'].fillna('S', inplace=True)
test_data['Embarked'].fillna('S', inplace=True)
```

### **特征选择**

特征选择是分类器的关键。特征选择不同，得到的分类器也不同。那么该选择哪些特征做生存的预测呢？

通过数据探索发现，PassengerId 为乘客编号，对分类没有作用，可以放弃；Name 为乘客姓名，对分类没有作用，可以放弃；Cabin 字段缺失值太多，可以放弃；Ticket 字段为船票号码，杂乱无章且无规律，可以放弃。其余的字段包括：Pclass、Sex、Age、SibSp、Parch 和 Fare，这些属性分别表示了乘客的船票等级、性别、年龄、亲戚数量以及船票价格，可能会和乘客的生存预测分类有关系。具体是什么关系，我们可以交给分类器来处理。因此先将 Pclass、Sex、Age 等这些其余的字段作特征，放到特征向量 features 里。

代码如下：

```python
# 模块3：特征选择
features = ['Pclass', 'Sex', 'Age', 'SibSp', 'Parch', 'Fare', 'Embarked']
train_features = train_data[features]
train_labels = train_data['Survived']
test_features = test_data[features]
from sklearn.feature_extraction import DictVectorizer
dvec = DictVectorizer(sparse=False)
train_features = dvec.fit_transform(train_features.to_dict(orient='record'))
print(dvec.feature_names_)
```

数据读取结果：

```
S    644
C    168
Q     77
U      2
Name: Embarked, dtype: int64
```



### **分类模型及模型的评价**

1） 使用 sklearn 中的决策树模型。使用 ID3 算法使用DecisionTreeClassifier 时，设置 criterion=‘entropy’，然后使用 fit 进行训练，将特征值矩阵和分类标识结果作为参数传入，得到决策树分类器。

2） 使用 sklearn 中的集成学习模型Adaboost()

3） 采用 K 折交叉验证准确率。sklearn 的 model_selection 模型选择中提供了 cross_val_score 函数。

Ø K 折交叉验证的原理：

拿出大部分样本进行训练，少量的用于分类器的验证。K 折交叉验证，就是做 K 次交叉验证，每次选取 K 分之一的数据作为验证，其余作为训练。轮流 K 次，取平均值。

代码如下：

```python
# 决策树弱分类器
dt_stump = DecisionTreeClassifier(max_depth=7, min_samples_leaf=5)
dt_stump.fit(train_features, train_labels)
print(u'决策树弱分类器准确率为 %.4lf' % np.mean(cross_val_score(dt_stump, train_features, train_labels, cv=10)))
# 决策树分类器
dt = DecisionTreeClassifier()
dt.fit(train_features, train_labels)
print(u'决策树分类器准确率为 %.4lf' % np.mean(cross_val_score(dt, train_features, train_labels, cv=10)))

# AdaBoost分类器
ada = AdaBoostClassifier()
ada.fit(train_features, train_labels)
print(u'AdaBoost 分类器准确率为 %.4lf' %
np.mean(cross_val_score(ada, train_features, train_labels, cv=15)))

# XGBoost分类器
xgb = xgb.XGBClassifier(max_depth=8, min_child_weight=3)
xgb.fit(train_features, train_labels)
print(u'XGBoost 分类器准确率为 %.4lf' %
np.mean(cross_val_score(ada, train_features, train_labels, cv=15)))
```

### 各个分类器准确率

```markdown
决策树弱分类器准确率为 0.8228
决策树分类器准确率为 0.7778
AdaBoost 分类器准确率为 0.8183
XGBoost 分类器准确率为 0.8183
```

------

# 银行客户违约信用分析

对银行客户基本信息数据集进行分析，提取不同信息进行特征分析和预测，通过多种分类器算法预测客户违约行为

```python
import pandas as pd
import xlwt
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.feature_extraction import DictVectorizer
from sklearn.tree import DecisionTreeClassifier
import numpy as np
from sklearn.model_selection import cross_val_score
from sklearn.ensemble import AdaBoostClassifier
import xgboost as xgb
from xgboost import XGBClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.naive_bayes import MultinomialNB#多项式朴素贝叶斯分类器
from sklearn.svm import LinearSVC
from sklearn.naive_bayes import BernoulliNB
```

## 数据集读取转换csv文件预处理

```python
# datafile = 'data.xls'
resultfile = 'data.csv'
data = pd.read_excel('data.xls')
data.to_csv(resultfile)
## excel转成csv表头中文乱码
```

## 数据探索

```py
datafile = 'data.csv'
data = pd.read_csv(datafile)
print(data.info())
data1 = data.describe()
data1.to_excel('data_dexcribe.xls')
```

{% asset_img 1.png  %}

```py
import matplotlib.pyplot as plt
weiyue = pd.value_counts(data['违约'])[1]
not_weiyue = pd.value_counts(data['违约'])[0]

# 绘图
fig = plt.figure(figsize = (8, 5))  # 设置画布大小
plt.rcParams['font.sans-serif'] = ['KaiTi']
plt.bar(x=range(2), height=[weiyue, not_weiyue,], width=0.4, alpha=0.8, color='skyblue')
plt.xticks([index for index in range(2)], ['1', '0'])
plt.xlabel('是否违约')
plt.ylabel('客户人数')
plt.title('客户是否违约柱状图')
plt.show()
plt.close()
## 输出可见违约：未违约大概2:5
```

{% asset_img 2.png  %}

```py
# 绘制 是否违约客户 与 年龄 的分布
import seaborn as sns
## 提取年龄
age = data['年龄'].dropna()
age = age.astype('int64')
## 绘制客户年龄分布箱型图
fig = plt.figure()
fig, axes = plt.subplots(1, 3, figsize=(10, 5))
sns.distplot(data["年龄"], ax = axes[0], axlabel='所有客户年龄分布')
sns.distplot(data.loc[data["违约"]==0]["年龄"], ax = axes[1], axlabel = '非违约客户年龄分布')
sns.distplot(data.loc[data["违约"]==1]["年龄"], ax = axes[2], axlabel = '违约客户年龄分布')
plt.show()
plt.close()
```

{% asset_img 3.png  %}

```python
## 客户年龄中存在300岁，需要数据清洗删除异常值，用年龄均值填充
# plt.title('客户是否违约柱状图')
# plt.show()
# plt.close()
# 均值填充年龄
data['年龄'].fillna(data['年龄'].mean(), inplace=True)
# 删除年龄大于80的数据
data.drop(data[(data['年龄'] > 80)].index.tolist(), inplace=True)
print(data["年龄"].describe())
# 将性别数据分别用female=1，male=0表示
data.loc[data["性别"] == "male", "性别"] = 0
data.loc[data["性别"] == "female", "性别"] = 1
```

性别特性处理：

```markdown
count    699.000000
mean      34.863308
std        7.967906
min       20.000000
25%       29.000000
50%       34.000000
75%       40.000000
max       56.000000
Name: 年龄, dtype: float64
```

```py
# 计算相关性矩阵
data_corr = data
dt_corr = data_corr.corr(method = 'pearson')
print('相关性矩阵为：\n', dt_corr)

# 绘制热力图
import seaborn as sns
plt.subplots(figsize=(10, 10))
sns.heatmap(dt_corr, annot=True, vmax=1, square=True, cmap='Blues')
plt.show()
plt.close()
```

{% asset_img 4.png  %}

```python
from sklearn.feature_extraction import DictVectorizer
# 通过分析选择合适的特征用于预测
features = ['年龄', '教育', '工龄', '地址', '负债率', '信用卡负债', '收入']
features_train = data[features]
train_label = data['违约']
dev = DictVectorizer(sparse=False)
# 转化成字典型
features_train = dev.fit_transform(features_train.to_dict(orient='record'))
```

```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(features_train,train_label,test_size=0.3, random_state=23,shuffle=True)
```

```py
from sklearn.neural_network import MLPClassifier
from sklearn.metrics import confusion_matrix, classification_report
import sklearn.metrics as metrics
#import sklearn.metrics.roc
def Confusion_matrix(model):
    print(confusion_matrix(y_test, model.predict(X_test)))  # 混淆矩阵
    print(classification_report(y_test, model.predict(X_test)))  # 分类报告
```

```python
import itertools
# 混淆矩阵可视化
def plot_confusion_matrix(cm, classes, normalize = False, title = 'Confusion matrix"', cmap = plt.cm.Blues) :
    plt.figure()
    plt.imshow(cm, interpolation = 'nearest', cmap = cmap)
    plt.title(title)
    plt.colorbar()
    tick_marks = np.arange(len(classes))
    plt.xticks(tick_marks, classes, rotation = 0)
    plt.yticks(tick_marks, classes)
 
    thresh = cm.max() / 2.
    for i, j in itertools.product(range(cm.shape[0]), range(cm.shape[1])) :
        plt.text(j, i, cm[i, j],
                 horizontalalignment = 'center',
                 color = 'white' if cm[i, j] > thresh else 'black')
 
    plt.tight_layout()
    plt.ylabel('True label')
    plt.xlabel('Predicted label')
    plt.show()
```

## 不同分类器处理结果呈现

### 决策树与决策树弱分类器

```py
#决策树分类器
dt = DecisionTreeClassifier(max_depth=3,min_samples_leaf=1)
dt.fit(features_train, train_label)

#决策树弱分类器
dt_stump =DecisionTreeClassifier()
dt_stump.fit(features_train,train_label)

# 决策树分类器
dt.fit(X_train, y_train)
predict_y = dt.predict(X_test)

cm1 = confusion_matrix(y_test, predict_y)
class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm1, classes=class_names, title='决策树分类')

Confusion_matrix(dt)
```

```py
# 决策树分类器
dt.fit(X_train, y_train)
predict_y = dt.predict(X_test)

cm1 = confusion_matrix(y_test, predict_y)
class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm1, classes=class_names, title='决策树分类')

Confusion_matrix(dt)
```

决策树分类器 混淆矩阵：

{% asset_img 5.png   %}

决策树分类器 分类报告：

{% asset_img 6.png   %}

```py
# 决策树弱分类器
#dt_stump = DecisionTreeClassifier()
dt_stump.fit(X_train, y_train)
predict_y = dt_stump.predict(X_test)
#score_y = dt_stump.decision_function(X_test)
cm1 = confusion_matrix(y_test, predict_y)
class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm1, classes=class_names, title='决策树弱分类')

Confusion_matrix(dt_stump)
```

决策树弱分类器 混淆矩阵：

{% asset_img 7.png   %}

决策树弱分类器 分类报告：

{% asset_img 8.png   %}

### AdaBoost

```py
#AdaBoost分类器 基分类器提升（循环）次数,随机种子设置,
ada = AdaBoostClassifier(n_estimators = 10,random_state = 10,learning_rate=1.1)
ada.fit(features_train,train_label)

ada.fit(X_train, y_train)
predict_y = ada.predict(X_test)

cm2 = confusion_matrix(y_test, predict_y)

class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm2, classes=class_names, title='AdaBoost分类器')

Confusion_matrix(ada)
```

AdaBoost分类器 混淆矩阵：

{% asset_img 9.png   %}

AdaBoost分类器 分类报告：

{% asset_img 10.png   %}

### XGBoost

```py
#XGBoost分类器
#xgb = XGBClassifier(n_estimators = 350, learning_rate=0.2)
xgb = XGBClassifier()
xgb.fit(features_train,train_label)

xgb.fit(X_train, y_train)
predict_y = xgb.predict(X_test)
#score_y = dt_stump.decision_function(X_test)
cm3 = confusion_matrix(y_test, predict_y)
class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm3, classes=class_names, title='XGBoost分类器')

Confusion_matrix(xgb)
```

XGBoost分类器 混淆矩阵：

{% asset_img 11.png   %}

XGBoost分类器 分类报告：

{% asset_img 12.png   %}

### 逻辑回归

```python
# 逻辑回归分类器
log = LogisticRegression()
log.fit(features_train, train_label)

# 逻辑回归分类器
log = LogisticRegression()
log.fit(X_train, y_train)
predict_y = log.predict(X_test)
score_y = log.decision_function(X_test)
cm = confusion_matrix(y_test, predict_y)
class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm, classes=class_names, title='逻辑回归')

Confusion_matrix(log)
```

逻辑回归 混淆矩阵：

{% asset_img 13.png   %}

逻辑回归 分类报告：

{% asset_img 14.png   %}

### 朴素贝叶斯

```python
# 多项朴素贝叶斯分类器
mul = MultinomialNB()
#mul.fit(features_train, train_label)
mul.fit(X_train, y_train)

#mul.fit(X_train, y_train)
predict_y = mul.predict(X_test)
#score_y = mul.decision_function(X_test)
cm4 = confusion_matrix(y_test, predict_y)
class_names = ['违约', '未违约']
# 显示混淆矩阵
plot_confusion_matrix(cm4, classes=class_names, title='多项朴素贝叶斯')

Confusion_matrix(mul)
```

朴素贝叶斯 混淆矩阵：

{% asset_img 15.png   %}

朴素贝叶斯 分类报告：

{% asset_img 16.png   %}

## 结果整合

{% asset_img 17.png   %}

------

# GMM聚类

EM算法处理王者荣耀英雄属性数据集

## 使用EM算法工具完成聚类分析算法

实验目的： 学习使用EM算法工具完成聚类分析算法

```python
#实验使用的工具包
# -*- coding: utf-8 -*-
import pandas as pd
import csv
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.mixture import GaussianMixture
from sklearn.preprocessing import StandardScaler
 
```

### 数据读取

任务一：数据加载，避免中文乱码问题完成

```python
#数据读取,请填写相应的代码！
data_ori = pd.read_csv('heros.csv', encoding='gb18030')
features = [u'最大生命',u'生命成长',u'初始生命',u'最大法力', u'法力成长',u'初始法力',u'最高物攻',u'物攻成长',u'初始物攻',u'最大物防',u'物防成长',u'初始物防', u'最大每5秒回血', u'每5秒回血成长', u'初始每5秒回血', u'最大每5秒回蓝', u'每5秒回蓝成长', u'初始每5秒回蓝', u'最大攻速', u'攻击范围']
data = data_ori[features]

# 对英雄属性之间的关系进行可视化分析
# 设置 plt 正确显示中文
plt.rcParams['font.sans-serif']=['SimHei'] # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus']=False # 用来正常显示负号
```

### 数据探索

任务二：编写计算特征之间相似度的代码

用热力图呈现 features_mean 字段之间的相关性 

```python
#填写相应代码！！
corr = data[features].corr()
print(corr)
             最大生命      生命成长      初始生命      最大法力      法力成长      初始法力      最高物攻  \
最大生命     1.000000  0.977408  0.761300 -0.220133 -0.207130 -0.260449 -0.246412   
生命成长     0.977408  1.000000  0.658106 -0.224112 -0.212271 -0.260464 -0.268377   
初始生命     0.761300  0.658106  1.000000 -0.189349 -0.178674 -0.222314 -0.140356   
最大法力    -0.220133 -0.224112 -0.189349  1.000000  0.998958  0.988178 -0.180428   
法力成长    -0.207130 -0.212271 -0.178674  0.998958  1.000000  0.980154 -0.175029   
初始法力    -0.260449 -0.260464 -0.222314  0.988178  0.980154  1.000000 -0.195800   
最高物攻    -0.246412 -0.268377 -0.140356 -0.180428 -0.175029 -0.195800  1.000000   
物攻成长    -0.230507 -0.248988 -0.121609 -0.197617 -0.191883 -0.213851  0.986098   
初始物攻    -0.195840 -0.223728 -0.161278  0.003153  0.003966  0.000371  0.536409   
最大物防     0.862472  0.879106  0.529737 -0.210425 -0.200722 -0.239802 -0.227352   
物防成长     0.832795  0.847654  0.528719 -0.226732 -0.217018 -0.255893 -0.182300   
初始物防     0.746460  0.764736  0.403032 -0.106082 -0.098780 -0.128992 -0.316263   
最大每5秒回血  0.951013  0.943635  0.688104 -0.224234 -0.212305 -0.260875 -0.290216   
每5秒回血成长  0.967369  0.972436  0.647438 -0.190258 -0.177943 -0.228721 -0.246922   
初始每5秒回血  0.829468  0.798746  0.701547 -0.268561 -0.258510 -0.298217 -0.346468   
最大每5秒回蓝 -0.286851 -0.297569 -0.224674  0.872025  0.853143  0.922108 -0.193140   
每5秒回蓝成长 -0.207165 -0.212225 -0.179819  0.996253  0.997483  0.976856 -0.179123   
初始每5秒回蓝 -0.279098 -0.292439 -0.199537  0.450477  0.415945  0.559600 -0.145060   

             物攻成长      初始物攻      最大物防      物防成长      初始物防   最大每5秒回血   每5秒回血成长  \
最大生命    -0.230507 -0.195840  0.862472  0.832795  0.746460  0.951013  0.967369   
生命成长    -0.248988 -0.223728  0.879106  0.847654  0.764736  0.943635  0.972436   
初始生命    -0.121609 -0.161278  0.529737  0.528719  0.403032  0.688104  0.647438   
最大法力    -0.197617  0.003153 -0.210425 -0.226732 -0.106082 -0.224234 -0.190258   
法力成长    -0.191883  0.003966 -0.200722 -0.217018 -0.098780 -0.212305 -0.177943   
初始法力    -0.213851  0.000371 -0.239802 -0.255893 -0.128992 -0.260875 -0.228721   
最高物攻     0.986098  0.536409 -0.227352 -0.182300 -0.316263 -0.290216 -0.246922   
物攻成长     1.000000  0.388718 -0.218394 -0.170773 -0.317801 -0.276067 -0.231988   
初始物攻     0.388718  1.000000 -0.151566 -0.143719 -0.139327 -0.207319 -0.191102   
最大物防    -0.218394 -0.151566  1.000000  0.983223  0.808768  0.864672  0.883474   
物防成长    -0.170773 -0.143719  0.983223  1.000000  0.687920  0.836301  0.849445   
初始物防    -0.317801 -0.139327  0.808768  0.687920  1.000000  0.743957  0.776385   
最大每5秒回血 -0.276067 -0.207319  0.864672  0.836301  0.743957  1.000000  0.983556   
每5秒回血成长 -0.231988 -0.191102  0.883474  0.849445  0.776385  0.983556  1.000000   
初始每5秒回血 -0.335171 -0.219081  0.746610  0.731842  0.611008  0.937169  0.858750   
最大每5秒回蓝 -0.226294  0.078473 -0.269346 -0.282500 -0.160954 -0.267749 -0.247764   
每5秒回蓝成长 -0.196605  0.005237 -0.202790 -0.217446 -0.105629 -0.202148 -0.169121   
初始每5秒回蓝 -0.184345  0.132174 -0.253111 -0.260324 -0.168099 -0.250934 -0.251700   

          初始每5秒回血   最大每5秒回蓝   每5秒回蓝成长   初始每5秒回蓝  
最大生命     0.829468 -0.286851 -0.207165 -0.279098  
生命成长     0.798746 -0.297569 -0.212225 -0.292439  
初始生命     0.701547 -0.224674 -0.179819 -0.199537  
最大法力    -0.268561  0.872025  0.996253  0.450477  
法力成长    -0.258510  0.853143  0.997483  0.415945  
初始法力    -0.298217  0.922108  0.976856  0.559600  
最高物攻    -0.346468 -0.193140 -0.179123 -0.145060  
物攻成长    -0.335171 -0.226294 -0.196605 -0.184345  
初始物攻    -0.219081  0.078473  0.005237  0.132174  
最大物防     0.746610 -0.269346 -0.202790 -0.253111  
物防成长     0.731842 -0.282500 -0.217446 -0.260324  
初始物防     0.611008 -0.160954 -0.105629 -0.168099  
最大每5秒回血  0.937169 -0.267749 -0.202148 -0.250934  
每5秒回血成长  0.858750 -0.247764 -0.169121 -0.251700  
初始每5秒回血  1.000000 -0.280945 -0.246737 -0.225670  
最大每5秒回蓝 -0.280945  1.000000  0.856248  0.827604  
每5秒回蓝成长 -0.246737  0.856248  1.000000  0.418679  
初始每5秒回蓝 -0.225670  0.827604  0.418679  1.000000  
```



```python
#热力图可视化！！
plt.figure(figsize=(14,14))
# annot=True 显示每个方格的数据
sns.heatmap(corr, annot=True)
plt.show()
```

{% asset_img GMM.png %}

从图中可以看出“最大生命”，“生命成长”，“初始生命”这三个属性的相关性大，我们只 需保留一个属性即可。 features_remain数组保留了特征选择的属性，这样将原本的20个属性降维到了13个属性。 相关性大cong的属性保留一个，因此可以对属性进行降维

```python
#将原本的20个属性降维到13个属性。请填写代码！
features = [u'最大生命',u'生命成长',u'初始生命',u'最大法力', u'法力成长',u'初始法力',u'最高物攻',u'物攻成长',u'初始物攻',u'最大物防',u'物防成长',u'初始物防', u'最大每5秒回血', u'每5秒回血成长', u'初始每5秒回血', u'最大每5秒回蓝', u'每5秒回蓝成长', u'初始每5秒回蓝', u'最大攻速', u'攻击范围']
data = data_ori[features]
```

### 数据清洗

任务三： “最大攻速”这个属性值是百分数，不适合做矩阵运算，需要将百分数转化成小数。 “攻击范围”这个字段的取值为远程或者近战，也不适合矩阵运算； 

解决办法：将取值做个映射，用1代表远程，0代表近战。 

示例：map({'远程':1,'近战':0})

```python
data[u'最大攻速'] = data[u'最大攻速'].apply(lambda x:float(x.strip('%'))/100)
data[u'攻击范围'] = data[u'攻击范围'].map({'远程':1,'近战':0})
<ipython-input-73-cb0648147d53>:2: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  data[u'最大攻速'] = data[u'最大攻速'].apply(lambda x:float(x.strip('%'))/100)
<ipython-input-73-cb0648147d53>:3: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  data[u'攻击范围'] = data[u'攻击范围'].map({'远程':1,'近战':0})
```

采用 Z-Score 规范化数据，保证每个特征维度的数据均值为 0，方差为 1

```python
ss = StandardScaler()
data = ss.fit_transform(data)
```

构造GMM聚类模型，GaussianMixture()

```python
gmm = GaussianMixture(n_components=30)

# 训练数据
gmm.fit(data)

prediction = gmm.predict(data)
print(prediction)
[13 24 11 10  2 25 20 11  7 24 18 14 10  0  1 18 13  7  2 12 22 12 12 12
 12 12 12 21 16 26 24  9  9 16 26  9 16  6  4 15  9 16 16  9 16 15 14  4
 24 16 28  5  2  3  3 13 29  5 23  2 29 19 17 13  8  5 27  5 21]
 
 # 将分组结果输出到 CSV 文件中
data_ori.insert(0, '分组', prediction)
data_ori.to_csv('.hero_out.csv', index=False, sep=',')

from sklearn.metrics import calinski_harabasz_score
print(calinski_harabasz_score(data, prediction))
```

------

# 航空公司客户价值分析

{% asset_img 航空.png  %}

------

# 比特币价格预测

比特币价格走势预测

```python
# -*- coding: utf-8 -*-
# 比特币走势预测，使用时间序列ARMA
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.arima_model import ARMA
import warnings
from itertools import product
from datetime import datetime
warnings.filterwarnings('ignore')
```

```python
# 数据加载
df = pd.read_csv('./bitcoin_2012-01-01_to_2018-10-31.csv')
# 将时间作为df的索
df.Timestamp = pd.to_datetime(df.Timestamp)
df.index = df.Timestamp
# 数据探索
print(df.head(10))
```

```python
数据探索部分打印内容：
            Timestamp      Open      High       Low     Close  Volume_(BTC)  \
Timestamp                                                                     
2011-12-31 2011-12-31  4.465000  4.482500  4.465000  4.482500     23.829470   
2012-01-01 2012-01-01  4.806667  4.806667  4.806667  4.806667      7.200667   
2012-01-02 2012-01-02  5.000000  5.000000  5.000000  5.000000     19.048000   
2012-01-03 2012-01-03  5.252500  5.252500  5.252500  5.252500     11.004660   
2012-01-04 2012-01-04  5.200000  5.223333  5.200000  5.223333     11.914807   
2012-01-05 2012-01-05  6.281429  6.286190  6.281429  6.286190      4.514373   
2012-01-06 2012-01-06  6.435000  6.445000  6.435000  6.445000      2.420196   
2012-01-07 2012-01-07  6.800000  6.800000  6.800000  6.800000      0.295858   
2012-01-08 2012-01-08  6.950000  6.950000  6.950000  6.950000      2.500000   
2012-01-09 2012-01-09  6.584167  6.584167  6.581667  6.581667      1.857481   

            Volume_(Currency)  Weighted_Price  
Timestamp                                      
2011-12-31         106.330084        4.471603  
2012-01-01          35.259720        4.806667  
2012-01-02          95.240000        5.000000  
2012-01-03          58.100651        5.252500  
2012-01-04          63.119578        5.208159  
2012-01-05          27.987370        6.284127  
2012-01-06          15.914658        6.438999  
2012-01-07           2.011834        6.800000  
2012-01-08          17.300000        6.950000  
2012-01-09          12.306798        6.582770  
```

```python
# 按照月，季度，年来统计
df_month = df.resample('M').mean()
df_Q = df.resample('Q-DEC').mean()
df_year = df.resample('A-DEC').mean()
# 按照天，月，季度，年来显示比特币的走势
fig = plt.figure(figsize=[15, 7])
plt.rcParams['font.sans-serif']=['SimHei'] #用来正常显示中文标签
plt.suptitle('比特币金额（美金）', fontsize=20)
plt.subplot(221)

plt.plot(df.Weighted_Price, '-', label='按天')
plt.legend()
plt.subplot(222)

plt.plot(df.Weighted_Price, '-', label='按月')
plt.legend()
plt.subplot(223)

plt.plot(df.Weighted_Price, '-', label='按季度')
plt.legend()
plt.subplot(224)

plt.plot(df.Weighted_Price, '-', label='按年')
plt.legend()
plt.show()

```

按照天、月、季度、年来统计显示比特币的走势：

{% asset_img bit1.png    %}

```python
# 设置参数范围
ps = range(0, 3)
qs = range(0, 3)
parameters = product(ps, qs)
parameters_list = list(parameters)
# 寻找最优ARMA模型参数，即best_aic最小
results = []
best_aic = float("inf") # 正无穷
for param in parameters_list:
    try:
        model = ARMA(df_month.Weighted_Price,order=(param[0], param[1])).fit()
    except ValueError:
        print('参数错误:', param)
        continue
    aic = model.aic
    if aic < best_aic:
        best_model = model
        best_aic = aic
        best_param = param
    results.append([param, model.aic])
# 输出最优模型
result_table = pd.DataFrame(results)
result_table.columns = ['parameters', 'aic']
print('最优模型:', best_model.summary())
# 比特币预测
df_month2 = df_month[['Weighted_Price']]
date_list = [datetime(2018, 11, 30), datetime(2018, 12, 31), datetime(2019, 1, 31),
             datetime(2019, 2, 28), datetime(2019, 3, 31),
             datetime(2019, 4, 30), datetime(2019, 5, 31), datetime(2019, 6, 30)]
future = pd.DataFrame(index=date_list, columns=df_month.columns)
df_month2 = pd.concat([df_month2, future])
df_month2['forecast'] = best_model.predict(start=0, end=91)
# 比特币预测结果显示
plt.figure(figsize=(20,7))
df_month2.Weighted_Price.plot(label='实际金额')
df_month2.forecast.plot(color='r', ls='--', label='预测金额')
plt.legend()
plt.title('比特币金额(月)')
plt.xlabel('时间')
plt.ylabel('美金')
plt.show()
```

{% asset_img bit2.png   %}

比特币预测结果：

{% asset_img bit3.png   %}
