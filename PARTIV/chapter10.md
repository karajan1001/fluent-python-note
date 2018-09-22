# CHAPTER 10 Sequence Hacking, Hashing, and Slicing
设计一个多维空间向量（比如文本向量）
## Vector: A User-Defined Sequence Type
使用组合而不是继承
## Vector Take #1: Vector2d Compatible
reprlib 可以让多余的序列字符用省略号表示
```python
> import reprlib
> reprlib.repr([1,2,3,4,5,6])
[1,2,3,...] 
```
## Protocols and Duck Typing
如同之前所示，我们可以从头建立一个序列对象，只要实现了序列所需的所有协议，这又叫鸭子类型。只要满足鸭子的特点就是鸭子，而不需要继承或者严格定义。也就是它的行为像鸭子所以它是鸭子，而不用定义它是鸭子，所以它才是鸭子。

## Vector Take #2: A Sliceable Sequence
序列需要有**\_\_len\_\_**和**\_\_getitem\_\_**两种方法。不过按照第一章的方法返回的是其他类型，比如一个扑克序列返回是一个list而不是另一个扑克序列。

### How Slicing Works
```python
>>> class MySeq:
... 	def __getitem__(self, index): ... return index #
...
>>> s = MySeq()
>>> s[1] # 对单个数字返回本身
1
>>> s[1:4] # 对a:b返回一个slice对象 从a到b，步长为空
slice(1, 4, None)
>>> s[1:4:2] # a:b:c返回slice对象a,b,c
slice(1, 4, 2)
>>> s[1:4:2, 9] # 对逗号间隔的东西返回一个元包，包含多个slice，或者数字
(slice(1, 4, 2), 9)
>>> s[1:4:2, 7:9] #
(slice(1, 4, 2), slice(7, 9, None))
```
还有**slice**中的 **indices**方法会自动根据start stop step进行转换，indice中填写的是目标序列的长度。

### A Slice-Aware __getitem__

正确的做法是判断数据类型。如果是slice 如何，如果是整数如何，如果是其他如何处理。


## Vector Take #3: Dynamic Attribute Access
如果要给list中的元素别名，需要实现**getitem**和**setitem**方法。

## Vector Take #4: Hashing and a Faster ==
使用reduce方法时最好用(func, iter, init)，有init在iter为空时不至于报错。

## Vector Take #5: Formatting
