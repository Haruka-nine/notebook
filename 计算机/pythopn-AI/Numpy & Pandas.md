
# Numpy

## 安装

```python
pip install numpy
```



## 属性

```python
import numpy as np  
  
array = np.array([[1,2,3],  
                  [2,3,4]])  
print(array)  
  
print('number of dim:',array.ndim)  #array的维数  
  
print('shape:',array.shape)   #array 的行和列  
  
print('size:',array.size)    #元素个数
```

```ad-info
title:输出结果
[[1 2 3]
 [2 3 4]]
number of dim: 2
shape: (2, 3)
size: 6
```

## 创建 array

```python
import numpy as np  
  
a = np.array([2, 23, 4], dtype=np.int_)  # 可以使dtype去定义array的类型  
  
print(a.dtype)  
  
a = np.array([2, 23, 4], dtype=np.int64)  
  
print(a.dtype)  
  
a = np.array([2, 23, 4], dtype=np.float_)  # 设置不同的类型去权衡精度和大小  
  
print(a.dtype)  
  
# 创建二维的array(矩阵)  
  
a = np.array([[2, 23, 4],  
              [2, 23, 4]])  # 这时一个两行三列的矩阵  
print(a)  
# 直接生成一个全为0的矩阵  
  
a = np.zeros((3, 4), dtype=np.int_)  
  
print(a)  
  
# 生成一个全部为1的矩阵  
  
a = np.ones((3, 4), dtype=np.int_)  
  
print(a)  
  
# 生成一个空矩阵(其实是无限接近与0的)  
  
a = np.empty((3, 4))  
  
print(a)  
  
# 生成一个有序的数列或矩阵  
  
a= np.arange(10,20,2)  # 起始为10，结束为20，步长为2  
  
print(a)  
  
a = np.arange(12).reshape((3,4))  # 生成0到11的3行4列的矩阵  
  
print(a)  
  
  
#生成一个线段  
  
a= np.linspace(1,10,5)  
  
print(a)  
  
# 同样线段也可以通过reshape更改他的形状  
  
a = np.linspace(1,10,6).reshape((2,3))  
  
print(a)
```

## 基础运算

```python
import numpy as np  
  
a = np.array([10, 20, 30, 40])  
b = np.arange(4)  
  
c = a - b  # array的减  
  
print(c)  
  
c = a + b  # array的加  
  
print(c)  
  
c = b ** 2  # array的平方  
  
print(c)  
  
c = 10 * np.sin(a)  # 对a中每一个元素求sin  
  
print(c)  
  
print(b < 3)  # 输出小于3的,小于3为true,大于3为false  
  
# 运行矩阵的乘法  
a = np.array([[1, 1],  
              [0, 1]])  
b = np.arange(4).reshape((2, 2))  
  
print(a)  
  
print(b)  
# 有两种乘法  
c = a * b  # 逐个相乘  
c_dot = np.dot(a, b)  # 按照矩阵相乘  
c_dot2 = a.dot(b)  # 上方矩阵乘法的另一种表示方法  
print(c)  
print(c_dot)  
print(c_dot2)  
  
# 创建随机矩阵  
a = np.random.random((2, 4))  
  
print(a)  
print(np.sum(a))  # 所有数的和  
print(np.min(a))  # 所有数中最小的  
print(np.max(a))  # 所有数中最大的  
  
print(np.sum(a, axis=1))  # axis等于1代表在每一列进行计算  
print(np.min(a, axis=0))  # axis等于0代表在每一行进行计算  
  
A = np.arange(2, 14).reshape((3, 4))  
  
print(np.argmin(A))  # 输出最小值的索引  
print(np.argmax(A))  # 输出最大值的索引  
print(np.mean(A))  # 计算平均值  
print(np.average(A))  # 同样计算平均值  
print(np.median(A)) # 输出中位数  
print(np.cumsum(A)) # 输出累加[ 2  5  9 14 20 27 35 44 54 65 77 90]  
print(np.diff(A))  #输出每两个数的差  
print(np.nonzero(A))  # 输出非零数的行array和列array  
  
A = np.arange(14,2,-1).reshape((3,4))  
print(A)  
print(np.sort(A))  # 逐行进行排序  
  
print(np.transpose(A))  #矩阵的反向，行变为列，列变为行  
print(A.T)  #同样为矩阵的反向  
  
print(np.clip(A,5,9))  #所有小于5的数变成5，所有大于9的数变成9，中间保存不变  
  
print(np.mean(A,axis=1))  
print(np.mean(A,axis=0))
```

```ad-note
title:axis
numpy的运算都可以添加参数axis
axis=1是对行进行计算
axis=0是对列进行计算
```

## 索引

```python
import numpy as np  
  
#一维的索引  
A = np.arange(3, 15)  
print(A)  
print(A[3])  
  
#二维的索引  
A = np.arange(3, 15).reshape((3,4))  
print(A)  
print(A[2]) #索引出第3行  
print(A[1][1]) #索引出第二行第二列  
print(A[1,1]) #与上方相同  
print(A[:,1]) # :代表全部，这代表第二列所有数  
print(A[1,:]) # :代表全部，这代表第二行所有数  
print(A[1,1:3]) # 输出第二行，第二个到第四个数  
  
for row in A :  #输出A的每一个行  
    print(row)  
  
for column in A.T: #先将A翻转再进行for，就是输出A的每一个列  
    print(column)  
  
print(A.flatten())  #将A转换为一行再进行for
for item in A.flat:  
    print(item)
```

```ad-note
title:A.flatten()和A.flat
A.flatten()返回的是一个array
而A.flat返回的是一个迭代器。
```

## array合并

```ad-attention
title:将横向数列变为竖向数列
我们是不可以使用transpose将横向数列变为竖向数列的

我们需要添加维度
A=np.array([1,1,1])
[1 1 1]
A[np.newaxis,:]  在后边添加一个维度
[[1 1 1]] (1, 3)
A[:,np.newaxis]  在前边添加一个维度
[[1]
 [1]
 [1]] (3, 1)
```

```python
import numpy as np  
  
A = np.array([1,1,1])  
B = np.array([2,2,2])  
  
C = np.vstack((A,B))#vertical stack (上下合并)  
  
D = np.hstack((A,B)) #horizontal stack (左右合并)  
  
print(A.shape,C.shape)  # A只有一行3列，合并后C有两行3列  
  
print(D,D.shape)  
  
print(A)  
print(A[np.newaxis,:],A[np.newaxis,:].shape)  
print(A[:,np.newaxis],A[:,np.newaxis].shape)  
  
A = np.array([1,1,1])[:,np.newaxis]  
B = np.array([2,2,2])[:,np.newaxis]  
  
C = np.vstack((A,B))#vertical stack (上下合并)  
  
D = np.hstack((A,B)) #horizontal stack (左右合并)  
  
print(D,D.shape)  
  
C = np.concatenate((A,B,B,A),axis=0)  # concatenate可以通过在后边使用axis来决定左右合并和上下合并  
  
print(C)
```

## array的分割

```ad-warning
split不能进行不等的分割，不能把4列分成3块
不等的分割需要使用array_split
```

```python
import numpy as np  
  
A = np.arange(12).reshape((3,4))  
print(A)  
  
print(np.split(A,2,axis=1))  
  
print(np.split(A,3,axis=0))  
  
print(np.array_split(A,3,axis=1))  #可以进行不等的分割  
  
print(np.vsplit(A,3))   #纵向分割  
print(np.hsplit(A,2))   #横向分割
```

## copy&deep copy

```ad-note
numpy的array赋值是关联的

进行赋值b = a 是完全的赋值，b就是a,a改变的同时b也会改变

如果只想要赋值而不想要进行关联的话，使用copy
b = a.copy()

```

```python
import numpy as np  
  
a = np.arange(4)  
print(a)  
b = a  
c = a  
d = b  
  
a[0] = 11  
print(a)  
print(b is a)  
print(b)  # 我们可以看到b就是完全的a，a改变b也会改变，同样，c,d 也会同时改变  
  
b = a.copy()  
print(a,b)  
  
a[3]=44  
print(a,b)  #可以看到这时b不会改变
```

# Pandas

## 属性
```python
import numpy as np  
import pandas as pd  
  
s  = pd.Series([1,3,6,np.nan,44,1])  
  
print(s)  
  
dates = pd.date_range('20160101',periods=6)  
print(dates)  
  
df = pd.DataFrame(np.random.randn(6,4),index=dates,columns=['a','b','c','d'])  
  
print(df)  
  
df1 = pd.DataFrame(np.arange(12).reshape((3,4)))  
print(df1)  
  
print(df1.dtypes)  #输出每一列的数据类型  
  
print(df1.index)  #输出每一行的名字  
print(df1.columns) #输出每一列的名字  
  
print(df1.values) #输出所有的值  
  
print(df1.describe())  #输出一些最大值，最小值，平均值等  
  
print(df1.T)  #翻转  
print(df.sort_index(axis=1,ascending=False)) #对列通过对列的名字进行排序，false代表使用倒的顺序  
  
print(df.sort_index(axis=0,ascending=False)) #对行通过对行的名字进行排序，false代表使用倒的顺序  
  
print(df.sort_values(by='c'))   #通过对某一列的数据进行行的排序，by是用来选择使用那一列
```

## 选择数据
```python
import numpy as np  
import pandas as pd  
  
s  = pd.Series([1,3,6,np.nan,44,1])  
  
print(s)  
  
dates = pd.date_range('20160101',periods=6)  
  
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])  
print(df)  
  
print(df['A'],df.A)  #列需要输入列名  
  
print(df[0:3],df['20160102':'20160104'])  #行需要表示第几行:第几行 或者 行名:行名  
  
  
#select by label:loc  通过标签名进行选择  
print(df.loc['20160102'])   #以行的标签名进行选择  
print(df.loc[:,['A','B']])  #以列的标签名进行选择，并输出全部的行  
print(df.loc['20160102',['A','B']])  
  
#select by position:iloc 通过位置进行选择  
print(df.iloc[3,1])  
  
print(df.iloc[3:5,[1,3]])  #同样可以进行切片  
  
print(df.iloc[[1,3,5],[1,3]])  #同样可以进行切片  
  
#Boolean indexing  
print(df[df.A>8])   #显示A>8的行列数据，不只选择列，所有行都会输出
```

## 设置值

```python
import numpy as np  
import pandas as pd  
  
s  = pd.Series([1,3,6,np.nan,44,1])  
  
print(s)  
  
dates = pd.date_range('20160101',periods=6)  
  
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])  
print(df)  
  
df.iloc[2,2] = 1111  
df.loc['20160101','B'] = 2222  
#df[df.A>4] = 0  #对A大于4的列所对应的行全部赋值  
#df.A[df.A>4] = 0  #只对A这一列大于4的值全部赋值  
  
df['F'] = np.nan  #为df添加新的一列，列名为F  
df['E'] = pd.Series([1,2,3,4,5,6],index=dates)  
print(df)
```


## 处理丢失数据

```python
import numpy as np  
import pandas as pd  
  
s  = pd.Series([1,3,6,np.nan,44,1])  
  
print(s)  
  
dates = pd.date_range('20160101',periods=6)  
  
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])  
print(df)  
  
df.iloc[0,1] = np.nan  
df.iloc[1,2] = np.nan  
  
print(df.dropna(axis=0,how='any'))    #对空数据进行丢弃，axis=1就是丢掉行，axis=0就是丢掉列  
  
#how = {'any','all'},any就是有任意一个为空就丢弃，all就是全为空再丢弃  
  
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])  
print(df)  
  
df.iloc[0,1] = np.nan  
df.iloc[1,2] = np.nan  
  
print(df.fillna(value=0))  #对空数据进行填充，填充值为value值  
  
df = pd.DataFrame(np.arange(24).reshape(6,4),index=dates,columns=['A','B','C','D'])  
print(df)  
  
df.iloc[0,1] = np.nan  
df.iloc[1,2] = np.nan  
  
print(df.isnull())  
print(np.any(df.isnull())==True)
```

## 导入导出
```python
import pandas as pd  
  
data=pd.read_csv('C:\\Users\\YYM\\Desktop\\student.csv')  
print(data)  
data.to_pickle('student_pickle')
```

## 合并 -concat

```python
import pandas as pd  
import numpy as np  
  
# concatenating  
# ignore index  
df1 = pd.DataFrame(np.ones((3,4))*0, columns=['a','b','c','d'])  
df2 = pd.DataFrame(np.ones((3,4))*1, columns=['a','b','c','d'])  
df3 = pd.DataFrame(np.ones((3,4))*2, columns=['a','b','c','d'])  
  
print(df1)  
print(df2)  
print(df3)  
res = pd.concat([df1, df2, df3], axis=0, ignore_index=True)  
print(res)  
  
# join, ('inner', 'outer')  
df1 = pd.DataFrame(np.ones((3,4))*0, columns=['a','b','c','d'], index=[1,2,3])  
df2 = pd.DataFrame(np.ones((3,4))*1, columns=['b','c','d', 'e'], index=[2,3,4])  
res = pd.concat([df1, df2], axis=1, join='outer')  
print(res)  
res = pd.concat([df1, df2], axis=1, join='inner')  
print(res)  
  
res = pd.concat([df1, df2], axis=0, join='outer')  
print(res)  
res = pd.concat([df1, df2], axis=0, join='inner')  
print(res)
```

## 合并-merge

```python
import pandas as pd

# merging two df by key/keys. (may be used in database)

# simple example

left = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],

'A': ['A0', 'A1', 'A2', 'A3'],

'B': ['B0', 'B1', 'B2', 'B3']})

right = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],

'C': ['C0', 'C1', 'C2', 'C3'],

'D': ['D0', 'D1', 'D2', 'D3']})

print(left)

print(right)

res = pd.merge(left, right, on='key')

print(res)

# consider two keys

left = pd.DataFrame({'key1': ['K0', 'K0', 'K1', 'K2'],

'key2': ['K0', 'K1', 'K0', 'K1'],

'A': ['A0', 'A1', 'A2', 'A3'],

'B': ['B0', 'B1', 'B2', 'B3']})

right = pd.DataFrame({'key1': ['K0', 'K1', 'K1', 'K2'],

'key2': ['K0', 'K0', 'K0', 'K0'],

'C': ['C0', 'C1', 'C2', 'C3'],

'D': ['D0', 'D1', 'D2', 'D3']})

print(left)

print(right)

res = pd.merge(left, right, on=['key1', 'key2'], how='inner') # default for how='inner'

# how = ['left', 'right', 'outer', 'inner']

res = pd.merge(left, right, on=['key1', 'key2'], how='left')

print(res)

# indicator

df1 = pd.DataFrame({'col1':[0,1], 'col_left':['a','b']})

df2 = pd.DataFrame({'col1':[1,2,2],'col_right':[2,2,2]})

print(df1)

print(df2)

res = pd.merge(df1, df2, on='col1', how='outer', indicator=True)

# give the indicator a custom name

res = pd.merge(df1, df2, on='col1', how='outer', indicator='indicator_column')

# merged by index

left = pd.DataFrame({'A': ['A0', 'A1', 'A2'],

'B': ['B0', 'B1', 'B2']},

index=['K0', 'K1', 'K2'])

right = pd.DataFrame({'C': ['C0', 'C2', 'C3'],

'D': ['D0', 'D2', 'D3']},

index=['K0', 'K2', 'K3'])

print(left)

print(right)

# left_index and right_index

res = pd.merge(left, right, left_index=True, right_index=True, how='outer')

res = pd.merge(left, right, left_index=True, right_index=True, how='inner')

# handle overlapping

boys = pd.DataFrame({'k': ['K0', 'K1', 'K2'], 'age': [1, 2, 3]})

girls = pd.DataFrame({'k': ['K0', 'K0', 'K3'], 'age': [4, 5, 6]})

res = pd.merge(boys, girls, on='k', suffixes=['_boy', '_girl'], how='inner')

print(res)
```

## 使用matplotlib画图

```python
import pandas as pd

import numpy as np

import matplotlib.pyplot as plt

# plot data

# Series

data = pd.Series(np.random.randn(1000), index=np.arange(1000))

data = data.cumsum()

##data.plot()

# DataFrame

data = pd.DataFrame(np.random.randn(1000, 4), index=np.arange(1000), columns=list("ABCD"))

data = data.cumsum()

# plot methods:

# 'bar', 'hist', 'box', 'kde', 'area', scatter', hexbin', 'pie'

ax = data.plot.scatter(x='A', y='B', color='DarkBlue', label="Class 1")

data.plot.scatter(x='A', y='C', color='LightGreen', label='Class 2', ax=ax)

plt.show()
```