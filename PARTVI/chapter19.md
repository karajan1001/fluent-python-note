# CHAPTER 19 Dynamic Attributes and Properties

python提供了`__getattr__`和`__setattr__`方法可以动态调用任意属性。这种动态属性的特点是一种元编程的方式。

## Data Wrangling with Dynamic Attributes
### Exploring JSON-Like Data with Dynamic Attributes
本脚本可以递归将 json 格式的内容转化为动态属性。对不存在的属性将报错处理。

```python
@classmethod
def build(cls, obj):
    if isinstance(obj, abc.Mapping):
        return cls(obj)
    elif isinstance(obj, abc.MutableSequence):
        return [cls.build(item) for item in obj] else:
    return obj

```

递归实现主要用了类型判断，判断是字典型还是序列型。还有 mapping 变为 动态属性的转换过程是在调用时完成。而不是先转换好，调用只是取个数据。

### The Invalid Attribute Name Problem

对于属性名不符合属性命名规则的情况，可以使用`str.isidentifier()`来判断，并进行一定方式的规则转换。

### Flexible Object Creation with \_\_new\_\_

和其他语言不通`__init__`并不是构造实例的函数。实际上类是由一个`classmethod` `__new__`构造出来的。`__init__`本身不返回任何值，它只做初始化。而`__new__`则必须返回一个实例。实际上 python 初始化的过程的伪代码为：
```python
def object_maker(the_class, some_arg): 
    new_object = the_class.__new__(some_arg) 
    if isinstance(new_object, the_class):
        the_class.__init__(new_object, some_arg) 
    return new_object
```

所以我们可以将上面`build`函数中的内容转移到`__new__`中不过此时

```python
cls(obj)
```
要替换为：
```python
super().__new__(cls)
```

### Restructuring the OSCON Feed with shelve
为了解决检索问题可以使用`shelve`模块。`shelve`模块包含了序列化工具`pickle`模块。`shelve.open`可以将对象存储在dbm中便于查询。

### Linked Record Retrieval with Properties

快速将 python 中`dict`转化为属性的方式：

```python
def __init__(self, **kwargs):
    self.__dict__.update(kwargs)
```

> python没有类型检查，所以一定要小心属性名覆盖掉原本的方法或者属性名。

## Using a Property for Attribute Validation
### LineItem Take #1: Class for an Item in an Order
python的属性不会对类型和数值进行检查可能引起一些bug。如果采用java的方式使用setter和getter解决这样问题。但是不够优雅，不够pythonic。

### LineItem Take #2: A Validating Property
```python
# 表示下面函数名是一个属性。可以用.weight直接获取
@property 
def weight(self): 
    return self.__weight # 属性实际的存储地址

# 属性setter方法在这里面可以进行类型和数值检查。
@weight.setter
def weight(self, value): 
    if value > 0:
        self.__weight = value 
    else:
        raise ValueError('value must be > 0')
```
有两种对属性定义抽象的方法:
1. 属性工厂 (本章）
2. 描述类 (下一章）

## A Proper Look at Properties
`property`的定义为。
```python
property(fget=None, fset=None, fdel=None, doc=None)
```
是一个工厂类。几个变量分别为，`get函数`，`get函数`，`get函数`和说明文档。


### Properties Override Instance Attributes
实例属性 attributes 覆盖类属性 attributes , 但是类的属性 properties 会覆盖 attributes.

### Property Documentation
property下面的注释可以展示在help文档中。

## Coding a Property Factory
使用工厂方法可以批量生成特定属性。
```python
weight = quantity('weight')

def quantity(storage_name): 
    def qty_getter(instance): # 使用instance代替self更为容易读懂。
        return instance.__dict__[storage_name]
    def qty_setter(instance, value): 
        if value > 0:
            instance.__dict__[storage_name] = value
        else:
            raise ValueError('value must be > 0')
    return property(qty_getter, qty_setter)
```

## Handling Attribute Deletion
删除属性比较少见，我们可以也使用低级api`__delattr__`代替它。

## Essential Attributes and Functions for Attribute Handling
### Special Attributes that Affect Attribute Handling
- `__class__`: 对象类的引用。有些方法是类方法，所以只会使用类属性而不会使用实例属性。

- `__dict__`: 属性 attributes 的字典。

- `__slots__`: 拥有该属性的对象没有`__dict__`。是一个元包。

### Built-In Functions for Attribute Handling

- dir([object]): 列出 `__dict__` 中的属性的 keys。

- getattr(object, name[, default]): 获得对象的属性。

- hasattr(object, name): 对象是否拥有属性。

- setattr(object, name, value): 对象的属性赋值。

- vars([object]): 列出 `__dict__` 中的属性的 key value 对, 在没有object时 vars 等价于 locals 获得本地属性词典。

### Special Methods for Attribute Handling
所有的`.attributes` 和 `getattr` 实际上都调用了这些特殊方法。

- `__delattr__(self, name)`: 删除属性 `del obj.attr` 会使用这个方法。

- `__dir__(self)`: `dir(obj)` 会引用这个方法，列出所有属性名。

- `__getattribute__(self, name)`: 获得对象中的属性值，如果属性值不存在则报错， `obj.attr` 和 `getattr` 会调用这个方法。

- `__getattr__(self, name)`: `_getattribute__`报错后会使用这个方法, 看属性是否有默认值。

- `__setattr__(self, name, value)`: 需要设置属性值时调用的方法，`obj.attr = 1` 和 `setattr`会调用它。

> getattr 只有在不存在的属性使用。另外两种要处理存在和不存在两种情况，所以更难用对。使用property比直接使用低级api更简单方便。
