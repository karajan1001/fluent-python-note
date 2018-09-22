# CHAPTER 11 Interfaces: From Protocols to ABCs

## Interfaces and Protocols in Python Culture
就算没有ABC每个类也有自己的接口（所有非私有非保护方法）。接口在smalltalk中叫协议接口的定义为：
`A useful complementary definition of interface is: the subset of an object’s public meth‐ ods that enable it to play a specific role in the system.`

## Python Digs Sequences
Python数据类型的哲学是尽可能使用python自带接口。简单实现**getitem**就可以实现 **for in**之类的简单循环操作了。

## Monkey-Patching to Implement a Protocol at Runtime
要实现random.shuffle的功能还需要实现**setitem**

## Alex Martelli’s Waterfowl
goose typing 和 duck typing

## Subclassing an ABC
我们一般不会自己写一个新类，而是会继承ABC
## ABCs in the Standard Library

### ABCs in collections.abc

![c126497e23740d60f2d0191a9d840866.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13376)


### The Numbers Tower of ABCs
Numbers 包含

- Number
- Complex
- Real
- Rational
- Integral

## Defining and Using an ABC
abc.ABC 只有在创造自己的ABC时才会引用
ABC既可以包含抽象方法又可以包含具体方法。如果有未实现的抽象方法则会报错。

### ABC Syntax Details
装饰器`@abc.abstractmethod`应该被放在贴近函数的地方

### Subclassing the Tombola ABC

### A Virtual Subclass of Tombola
使用**register**可以制造ABC的虚子类。虚子类可以被isinstance等识别，但是它不会任何ABC中的方法。


## How the Tombola Subclasses Were Tested

## Usage of register in Practice

## Geese Can Behave as Ducks
因为有**subclasshook** 所以实现了里面所有方法的类都会被当成ABC的子类。

> 避免使用isinstance，如果有需要使用try: except代替
