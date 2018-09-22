# CHAPTER 12 Inheritance: For Good or For Worse
本章两个重点

1. 不要从内置类型继承。
2. 从多个类继承和方法顺序。

## Subclassing Built-In Types Is Tricky
内置类型的子类会引起混乱。内置类型做了很多优化会忽视子类型中的方法重写。导致出现一些奇怪的错误。所以我们应该使用`collection`中的`UserDict, UserSet, UserList`等方法。


## Multiple Inheritance and Method Resolution Order
任何多继承语言都要考虑多个父类中相同名称方法的问题。这个问题如下图又被成为**钻石问题（diamond problem）**

![2280515e6b7460d05dca9e8a843b4dc5.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13377)


python中的先后顺序如上图，此顺序又叫MRO顺序，在**cls.\_\_mro\_\_**中有写明。对于缺失的方法会按照MRO顺序检查。super()方法会取得MRO顺序中的父类。


## Multiple Inheritance in the Real World

![c238a34b4726bbaae8004cc74761c4f8.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13378)


## Coping with Multiple Inheritance
多继承很容易使得程序和意义复杂化。为了不让这种情况发生有如下一些方法可以采用

### 1. Distinguish Interface Inheritance from Implementation Inheritance

继承主要两种情况
1. 继承接口：此时双方为**is-a** 关系
1. 继承实现：此时主要为了代码复用

`为了代码复用的集成应该用组合composition或者委托delegation来实现。`

### 2. Make Interfaces Explicit with ABCs
如果一个类需要定义接口，使用ABC

### 3. Use Mixins for Code Reuse
如果一个类中设计为其方法被一些不想关的类复用，可以使用mixin 混合类。或者decorate来实现。

### 4. Make Mixins Explicit by Naming
如果有混合类，则应该在命名上给出标注。

### 5. An ABC May Also Be a Mixin; The Reverse Is Not True
ABC 可能是一个混合类，但是混合类不一定是ABC。

### 6. Don’t Subclass from More Than One Concrete Class
不用从多个具体类中集成。

### 7. Provide Aggregate Classes to Users
```python
class Widget(BaseWidget, Pack, Place, Grid):
    """Internal class.Base class for a widget which can be positioned with the geometry managers Pack, Place or Grid."""
	pass
```
提供聚合类可以省略很多复杂的打包工作。

### 8. “Favor Object Composition Over Class Inheritance.”
集成很容易被滥用。更多时候使用 组合和委托更好。

### Tkinter: The Good, the Bad, and the Ugly

## A Modern Example: Mixins in Django Generic Views

