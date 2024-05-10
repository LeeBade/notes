# Python的世界

## 列表与字典

- 列表与切片
  
```python

a＝[1,2,3,4,5]#生成列表

len(a)#获取列表的长度

a[0]#访问第一个元素的值

a[0:2]#获取切片:索引[0，2]
a[0:]#获取索引0之后的所有元素
a[:3]#获取索引3之前的所有元素
a[0:-1]#倒数第二个元素标记为-1

```

- 字典：键值对

```python

me={'height':180}#生成字典

me['height']#访问

me['weight'] = 70


```

## 基础数据类型

- bool：true、false
  - 合法运算：and、or、not

## 结构语法 

- if else

```python

hungry=False
if hungry:
  print("I'm hungry")#使用空白字符进行缩进  
else:
  print("I'm not hungry")
  print("I'm sleepy")

```

- for

```python

for i in [1,2,3]:
  print(i)

```

## 函数

```PYTHON

def hello(object):
  print("Hello "+ object+"!")

hello("cat")

```

## 类

- class由构造函数、函数、域组成
- 通过self.属性名的方式添加域
- 要求每个构造器和函数的第一个参数为self

```python
class Man:
  def __init__(self,name): 
    self.name=name
    print("Initialized!")
  def hello(self):
    print("Hello"+ self.name+"1")
  def goodbye(self):
    print("Good-bye"+self.name+"!")


```

# 外部库

## NumPy

- 用途：数值计算；提供高级算法以及矩阵操作
- n维数组矩阵运算
  - 张量：一般化后的数组

```python

x = np.array([1.0,2.0,3.0])#生成NumPy一维数组，接受Python列表

A = np.array([[1,2],[3,4]])
```

- 广播:允许$m\times n$矩阵与$n\times p$矩阵进行矩阵运算

```python


```
## Matplotlib

- 实验结果可视化