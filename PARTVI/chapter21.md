# CHAPTER 21 Class Metaprogramming

> 元编程超过了99%的编程者的需求。如果你不知道自己需要它，那你肯定不需要。

类元编程的主要作用是在运行时实时生成类。方程可以在运行时返回一个类，装饰器可以改变一个类，元编程可以帮助我们构建一整个簇的类。拥有各种性质。

> 除非进行python框架相关的工作，不然不要使用元编程。

## A Class Factory

一个典型的例子就是`collecitons.namedtuple`。

```python
def record_factory(cls_name, field_names): 

    try:
        field_names = field_names.replace(',', ' ').split() 
    except AttributeError: # no .replace or .split
        pass # assume it's already a sequence of identifiers 
    field_names = tuple(field_names)

    def __init__(self, *args, **kwargs):
        attrs = dict(zip(self.__slots__, args)) 
        attrs.update(kwargs)
        for name, value in attrs.items():
            setattr(self, name, value)

    def __iter__(self):
        for name in self.__slots__:
        yield getattr(self, name)

    def __repr__(self):
        values = ', '.join('{}={!r}'.format(*i) for i in zip(self.__slots__, self)) 
        return '{}({})'.format(self.__class__.__name__, values)

    cls_attrs = dict(__slots__ = field_names,
                     __init__  = __init__,
                     __iter__  = __iter__,
                     __repr__  = __repr__)

    return type(cls_name, (object,), cls_attrs) # 建造并返回一个新的类型，其

```

这里`type`是一个类，用来生成其他类，调用方法为
```python
MyClass = type('MyClass', (MySuperClass, MyMixin), {'x': 42, 'x2': lambda self: self.x * 2})
```
不过这个方法有个致命问题，生成的类无法序列化，为了解决这个问题，可以研究`collections.nameduple` 和 `pickle`

### A Class Decorator for Customizing Descriptors

和上面的`descriptor`一样，不过改变存储单元的名字。
```python
@entity
class LineItem:
    description = model.NonBlank() 
    weight = model.Quantity() 
    price = model.Quantity()

def entity(cls):
    for key, attr in cls.__dict__.items():
    if isinstance(attr, Validated):
        type_name = type(attr).__name__
        attr.storage_name = '_{}#{}'.format(type_name, key)
    return cls
```

类装饰器可以一定程度上代替元类，不过，他们不会对子类起作用。

## What Happens When: Import Time Versus Runtime
### The Evaluation Time Exercises

#### `import` 
- 我的结果:
100 400 700 1 2 6 7 `9 200` 14
- 答案：
100 400 700 1 2 6 7 `200 9` 14

> 先运行decorator 函数后运行下一个类的创建。

#### 运行脚本
- 我的结果:
100 400 700 1 2 6 7 200 9 11 3 5 12 300 13 10 14  
- 答案：
100 400 700 1 2 6 7 200 9 11 3 5 12 300 13 10 14 `4`

> `__del__` 在类销毁时会运行。

## Metaclasses 101
### The Metaclass Evaluation Time Exercise
## A Metaclass for Customizing Descriptors
## The Metaclass `__prepare__` Special Method

## Classes as Objects
