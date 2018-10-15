# CHAPTER 14 Iterables, Iterators, and Generators
> generator 一定是iterator 因为其满足iterator 所有的api。 而反过来则不一定，因为有些iterator有全部数据，而generator 可以凭空造出数据，iterator 不能iter一个无限长序列，而generator 可以。


Python 中每个集合都可以iter ，而 iterator支持如下操作

- for 循环
- 集合构建和扩展
- 按行迭代文字数据
- List, dict, and set comprehensions
- Tuple 解包
- 解包在函数调用中带\*的变量

## Sentence Take #1: A Sequence of Words

```python
class Sentence:
    def __init__(self, text): self.text = text
        self.words = RE_WORD.findall(text) 
    def __getitem__(self, index):
        return self.words[index] 
    def __len__(self):
        return len(self.words) 
    def __repr__(self):
        return 'Sentence(%s)' % reprlib.repr(self.text)
```
这个Sentense 类可以使用for in迭代

### Why Sequences Are Iterable: The iter Function
python 迭代器迭代时调用方法iter(x)，此方法查看\_\_iter\_\_方法是否存在不存在也查看\_\_getitem\_\_是否存在，存在则构建迭代器从0开始迭代，如果都不存在则报错，否则构建一个迭代器

## Iterables Versus Iterators

可迭代：任何调用iter()函数返回迭代器的对象都是可迭代的。
Python 调用迭代器的方法为

```python
it = iter(b)
while True:
    try: 
        next(it)
    except StopIteration:
        del it
        break
```

此方法和for in b 等价 迭代器包含\_\_next\_\_方法供迭代使用。

## Sentence Take #2: A Classic Iterator
### Making Sentence an Iterator: Bad Idea
`经典迭代器，将可迭代对象和迭代器混淆（在可迭代类中加入\_\_next\_\_）是一个可怕的想法, 也是一个反设计模式的设计`:
一个迭代器设计模式为：
- 支持获取一个聚合体内部元素，不用暴露其内部表示方法。
- 支持对聚合体同时进行多个遍历。
- 支持统一的遍历模式。

其中第二条是将可迭代对象和迭代器混淆的主要问题。

> 迭代器永远不要直接操作对象，相反应该新建一个迭代器，防止多个迭代冲突。

## Sentence Take #3: A Generator Function
而如果要让代码更**pythontic**最好的方法是使用生成器。
```python
def __iter__(self):
    for word in self.words:
        yield word return
```
使生成器就可以不用如上面一样单独弄一个迭代类了。
### How a Generator Function Works
包含yield关键词的函数是generator 函数。调用该函数将会返回一个generator
> 生成器函数生成数据，函数有返回值，生成器函数的调用返回一个生成器。

## Sentence Take #4: A Lazy Implementation
可以不用事先把句子分词好：
```python
def __iter__(self):
    for match in RE_WORD.finditer(self.text):
    yield match.group()
```
迭代时自动寻找下一个词
## Sentence Take #5: A Generator Expression
生成器函数可以用生成器表达式替代，和list 合成一样算一种语法糖
```python
def __iter__(self):
    return (match.group() for match in RE_WORD.finditer(self.text))
```
调用方法为 **( for 可迭代对象)**

## Generator Expressions: When to Use Them
只有在代码行数很少， 使用generator expression 可读性更强时才应该使用他。

## Another Example: Arithmetic Progression Generator
可以使用generate function
```python
def aritprog_gen(begin, step, end=None): 
    result = type(begin + step)(begin) 
    forever = end is None
    index = 0
    while forever or result < end: 
        yield result
        index += 1
        result = begin + step * index

```

### Arithmetic Progression with itertools
更简单的方法是使用itertools中的工具,避免重复造轮子
**count**: 会一直迭代生成下去
**takewhile**: 则会在特定情况停下
```python
def aritprog_gen(begin, step, end=None): 
    first = type(begin + step)(begin) 
    ap_gen = itertools.count(first, step) 
    if end is not None:
        ap_gen = itertools.takewhile(lambda n: n < end, ap_gen) 
    return 
```

## Generator Functions in the Standard Library
**os.walk**: 遍历目录的生成器

## New Syntax in Python 3.3: yield from
yield from 不光是一个语法糖。作为语法糖时形成一个通道将内部的生成器和外部生成器相连。

## Iterable Reducing Functions
这些函数输入序列，输出单个数，这些函数被称为**reduce**,**folding**,**accumulating**函数。

## A Closer Look at the iter Function

iter除了调用\_\_iter\_\_返回生成器还有另一种用法。此时iter有两个参数，第一个参数是个有返回值的函数，第二个参数是一个标记，如果返回值等于第二个参数则raise StopIteration。
一个典型的使用例子。
```python
with open('mydata.txt') as fp:
    for line in iter(fp.readline, ''):
        process_line(line)
```

## Case Study: Generators in a Database Conversion Utility
ETL 中，读写分离，需要完成任务，将所有数据读入内存。如果数据量太大，就不得不使用generator函数来实现。


## Generators as Coroutines
**.send()**函数可以使得生成器与外部双向传递数据。
但是要注意以下几点，不要将迭代器和协程混淆。
> - Generators produce data for iteration
> - Coroutines are consumers of data
> - To keep your brain from exploding, you don’t mix the two concepts together
> - Coroutines are not related to iteration
> - Note: There is a use of having yield produce a value in a coroutine, but it’s not tied to iteration.14

