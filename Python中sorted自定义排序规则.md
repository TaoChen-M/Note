# Python中sorted自定义排序规则

Python中有两个排序函数，一个是sorted，另外一个是sort，默认都是增序。sort只能对list进行排序，并且是原地修改，如：

```python
l=[2,1,5,0]
l.sort()#0 1 2 5
```

sorted可以对任何容器进行排序，比如set，map，元组以及list，需要使用一个新的变量进行接收，如下所示：

```Python
#列表
l=[2,1,5,0]
l=sorted(l)#[0, 1, 2, 5]

#元组 元组和列表的一个区别是列表可以修改，元组不可修改
t=(2,4,5,0)
t=sorted(t)#[0, 2, 4, 5]

#字典
m={1:'hhh',2:"www",0:'ooo'}
m=sorted(m)#[0, 1, 2] 默认按照key进行排序，并将排序好的以列表的形式输出
m=sorted(m.values()) # 按照values进行排序，同样以列表的形式输出

#集合
s={1,4,5,7,0,2}
s=sorted(s)#[0, 1, 2, 4, 5, 7]
```

如何自定义sorted排序呢，主要是通过key和reverse这两个关键字实现。

key用来确定当前使用的排序规则，结合lambda表达式使用。reverse表示反转的意思。因为默认是增序，如果设置reverse为True，则是降序排序

```Python
l=["animal","blue",'dog','camera']
l=sorted(l,key=len)# 按照长度进行排序 ['dog', 'blue', 'animal', 'camera']
l=sorted(l,key=len,reverse=True)# ['animal', 'camera', 'blue', 'dog']

l=[[1,2],[4,5],[3,1],[5,10]]
l=sorted(l,key=lambda x:x[1])# [[3, 1], [1, 2], [4, 5], [5, 10]]
# lambda可以有多个传入参数，但是表达式只能有一个

m={1:'dog',4:'camera',0:'apple'}
mm=sorted(m.items(),key=lambda x:x[1])#[(0, 'apple'), (4, 'camera'), (1, 'dog')]
# x传入的是字典元组(1,'dog'),x[1]表示按照value排序，x[0]按照key排序，也可以对x[1]执行len(x[1])按照长度排序
```

