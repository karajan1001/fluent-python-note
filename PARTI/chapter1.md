# Chapter 1 Python Data Model
使得代码`Pythonic` 的关键是**data model**，应该让它们和python语言中原生的方法更相似。

**dunder method**：dunder表示双下划线，一般对应一种魔术方法，比如

```python
a['s'] = a.__getitem__('s')
```

而实现一些dunder方法后，就能享受到python中的各种便利，比如实现*getitem*后可以使用**slicing [1:2]**, **for loop for in**，**member judgement in**这些build in 方法。不过需要引起注意的是：

> **dunder method**不应该直接被调用，除非在superclass *init*中

另一注意事项:
> **%r相比%s**可以显示数字和数字字符串的区别。要求方便重建**%r**对应魔术方法*repr*。而*str*对应关键字**str**。

