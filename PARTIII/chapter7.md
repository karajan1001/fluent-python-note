# CHAPTER 7 Function Decorators and Closures
函数的装饰器可以让我们增强函数的功能，但是这涉及到闭包的概念。

## Decorators 101
装饰器就是一个可调用函数，接受另一个函数作为变量，然后处理返回它，或者替换他 。
## When Python Executes Decorators
> 在import 时运行，早于任何其他函数

## Decorator-Enhanced Strategy Pattern
可以使用装饰器来为策略模式添加新策略。
```python
def promotion(promo_func): 
    promos.append(promo_func) 
    return promo_func
```

## Variable Scope Rules
函数内部对全局变量赋值时最好使用**global**
```
>>> def f3(a): 
... global b 
... print(a) 
... print(b) 
... b=9
```

## Closures
闭包，可以在函数外面调用函数内部的嵌套函数。闭包中可以包含函数中的参数，这些参数在函数执行完成后依然保留。比如：
```python
def make_averager(): 
	series = []
	def averager(new_value): 
		series.append(new_value) 
		total = sum(series) 
		return total/len(series)
	return averager
```

## The nonlocal Declaration
闭包如果有元素和嵌入的函数中重名，类似函数和全局变量一样，要使用nonlocal来表示
```python
def make_averager(): 
    count = 0
    total = 0
    def averager(new_value): 
        nonlocal count, total 
        count += 1
        total += new_value 
        return total / count
    return averager
```

## Implementing a Simple Decorator
一个自动打印log的函数
略
### How It Works
decorator返回新函数会覆盖老函数中的**\_\_doc\_\_**和 **\_\_name\_\_**等内容。这时候可以用
```python
@functool.warps(func)
```
来复制func中的内容到新函数。
## Decorators in the Standard Library
```python
@property
@classmethod
@staticmethod
```

### Memoization with functools.lru_cache
```python
@functools.lru_cache() #
```
可以缓存函数的结果，减少重复计算。除了用在递归外，还可以用来缓存减少页面访问。lru_cache使用一个字典来缓存所有内容。

### Generic Functions with Single Dispatch 
这个装饰器是用来重载函数的。
```python
@singledispatch
def htmlize(obj):
	content = html.escape(repr(obj)) 
	return '<pre>{}</pre>'.format(content)
@htmlize.register(str) 
def _(text):
	content = html.escape(text).replace('\n', '<br>\n') 
	return '<p>{0}</p>'.format(content)
@htmlize.register(numbers.Integral) 
def _(n):
	return '<pre>{0} (0x{0:x})</pre>'.format(n)
@htmlize.register(tuple) 
@htmlize.register(abc.MutableSequence) 
def _(seq):
	inner = '</li>\n<li>'.join(htmlize(item) for item in seq) 
	return '<ul>\n<li>' + inner + '</li>\n</ul>'
```

## Stacked Decorators
```python
@f1
@f2
def f():
```
等价于 $ f = d1(d2(f)) $

## Parameterized Decorators
方法是用一个decorator factory 生产基于参数的decorator

### A Parameterized Registration Decorator
```python
def register(active=True): 
	def decorate(func):
		print('running register(active=%s)->decorate(%s)' % (active, func))
		if active: 
			registry.add(func)
		else: 
			registry.discard(func)
		return func 
	return decorate
```

### The Parameterized Clock Decorator

