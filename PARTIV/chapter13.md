# CHAPTER 13 Operator Overloading: Doing It Right
运算符重载可以让用户自定义对象使用诸如 + - () . []之类的运算符。

本章会介绍
• How Python supports infix operators with operands of different types
• Using duck typing or explicit type checks to deal with operands of various types
• How an infix operator method should signal it cannot handle an operand
• The special behavior of the rich comparison operators (e.g., ==, >, <=, etc.)
• The default handling of augmented assignment operators, like +=, and how to over‐ load them

## Operator Overloading 101
运算符重载有三种情况无法进行。
1. 内置类型的运算符无法重载
2. 无法新建运算符。
3. **is not or** 无法重载。

## Unary Operators 
对于单运算符**+ - ~** 永远不要修改self本身还是返回一个新对象。
1. \+ 对应 \_\_pos\_\_
1. \- 对应 \_\_neg\_\_
1. ~ 对应 \_\_invert\_\_

## Overloading + for Vector Addition
> 按照数据模型章节的说法，+ 应该被看作是拼接，\*应该被看成是重复。
```python
a + b # 对应 a.__add__(b) 如果 a 没有 __add__ 对象 则会调用 b.__radd__(a) 为了 b+a也能实现，在对象a中也需要定义
def __radd__(self, other):
    return self + other
```

## Overloading * for Scalar Multiplication
和 + 类似

![3836a49ccf025f255280351292e17337.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13379)

## Rich Comparison Operators

![eb79cbede60f94db525d29dbd6e915b0.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13380)


## Augmented Assignment Operators
```python
__add__ # 返回两个对象之和的新对象
__iadd__ # 返回本身，同时改变自身
```
