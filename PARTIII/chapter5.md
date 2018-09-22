# CHAPTER 5 First-Class Functions

first class 的 四个原因
• Created at runtime
• Assigned to a variable or element in a data structure
• Passed as an argument to a function
• Returned as the result of a function

## Treating a Function Like an Object
functions 和其他对象一样，除了type为functions。
## Higher-Order Functions
将function 当做参数传给funtion的时就会出现高阶funtions
比如：

```python
fruits = ['strawberry', 'fig', 'apple', 'cherry', 'raspberry', 'banana']
sorted(fruits, key=len)
```
此时len就被当做参数传给sorted方程。

## Modern Replacements for map, filter, and reduce
list comprehension 和 generator可以某种程度上替代map filter的工作，而reduce则被sum取代。

## Anonymous Functions
匿名方程一般用在上下文list中，很少用于其他地方。

## The Seven Flavors of Callable Objects
其中使用（）调用的情况
- 用def定义的方程 比如def a():
- built-in 方程 比如 len(), sum()
- built-in method  比如 dict.get()
- Methods 类中的方法。
- Classes 构造函数。 `__new__`创建一个方法，然后`__init__`初始化它
- Class 实例。如果包含`__call__`
- 生成器。使用了yield的方程或者方法。返回一个生成器。
### User-Defined Callable Types
### Function Introspection
很多方程方法和普通class方法是一样的比如`__dict__`里面有方程的输入字典。

### From Positional to Keyword-Only Parameters
```python
def tag(name, *content, cls=None, **attrs):
# content 是个tupple
## attrs 是个字典
```

### Retrieving Information About Parameters
Python的输入有如下几种类型：
- POSITIONAL\_OR\_KEYWORD：普通变量 
- VAR\_POSITIONAL：\*A，tuple位置变量
- VAR\_KEYWORD：\*\*B，字典变量
- KEYWORD\_ONLY：只能用keyword，Python3新增类型
- POSITIONAL\_ONLY：Cython用的。

### Function Annotations
输入输出可以定义

```python
def clip(text:str, max_len:'int > 0'=80) -> str:
```
但是Python并不会进行任何强制类型检查，所有这些信息都是IDE，框架等工具使用的。

### Packages for Functional Programming
虽然Python不是函数式语言，但是依然有很多函数式的package可以帮助我们构建易于扩展的程序。
#### The operator Module

```python
from operator import mul # mul(a,b) = a*b
from operator import itemgetter # itemgetter(1) ~ a[1] list 
```

#### Freezing Arguments with functools.partial
