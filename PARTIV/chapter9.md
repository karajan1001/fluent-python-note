# CHAPTER 9 A Pythonic Object
本章主要教你如何将自定义的Python对象定义的如同内置对象一样。

## Object Representations
Python有两种将对象编译为字符串的方式：
> 1. repr() 返回开发者希望看到的字符串
> 2. str() 返回用户希望看到的字符串

还有 bytes 返回二进制字符串和format返回特殊格式的字符串

## Vector Class Redux
对一个一般对象
- repr: 会展示构建对象的语句
- print: 会调用str

要实现一个对象的魔术方法需要实现
**init, iter, repr, str, bytes, eq, abs, bool** 

## An Alternative Constructor
可以使用classmethod构建一个 从 bytes 构建对象的方法。

## classmethod Versus staticmethod
class method 最主要的用处就是除了标准构造函数外的其他构造函数。
而static method 不过是一个正好处于类中的普通函数。

## Formatted Displays
\_\_format\_\_可以自定义，比如时间中。
```python
>>> "It's now {:%I:%M %p}".format(now) "It's now 06:49 PM"
```
如果缺少此方法，则会调用**str(obj)**

## A Hashable Vector2d
如果想让我们的 Vector2d类可以hash，就需要让其只读。一个简单的办法是覆盖其属性名。
```python
@property
def x(self): 
    return self.__x
```

然后还要定义其hash方程
```python
def __hash__(self):
	return hash(self.x) ^ hash(self.y)
```

## Private and “Protected” Attributes in Python
带有\_\_的变量名在python中是私有变量，其在\_\_dict\_\_中的名称包含类名，这样可以避免错误引用。单\_的变量为受保护变量。

## Saving Space with the \_\_slots\_\_ Class Attribute
Python对象的属性被存储在\_\_dict\_\_中，但是dict是一种很浪费空间的存储方法，如果有成千上万个实例，最好使用\_\_slots\_\_来节省空间。slots将所有的属性名存储在元包中，元包中的属性名不可变。`slots和dict混用可能会引起混乱。`

## Overriding Class Attributes
