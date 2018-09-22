# CHAPTER 8 Object References, Mutability, and Recycling
## Variables Are Not Boxes
变量不是盒子，不是装了数据的盒子而是一个reference，相当于盒子上的标签。

## Identity, Equality, and Aliases
a = b 表示变量的别名。id(a) 返回 a 对应对象独一无二的身份证。
### Choosing Between == and is
== 比较两个对象的值，而 is 只比较两者身份证

### The Relative Immutability of Tuples


## Copies Are Shallow by Default
如果要复制一个list的话。
```python
l2 = list(l1)
l2 = l1[:] 
```
都提供简单方法。不过这两种方法都是浅拷贝。盒子虽然不一样了，盒子里面的东西还是同一个id。对不可变对象没有问题，对可变对象如果对象变了，另一个对象也会跟着变。
### Deep and Shallow Copies of Arbitrary Objects
```python
copy.copy() # 浅拷贝
copy.deepcopy() # 深拷贝
```

## Function Parameters as References
Python中唯一的传递参数方法叫**共享**。也就是参数传递时，取得了一份参数的拷贝，也就是是内部变量是外部传入参数的别名。

### Mutable Types as Parameter Defaults: Bad Idea
如下例子中`passengers=[]`只会在创建时运行一次。这会导致之后调用每次调用都引用的同一个list。这些默认值只会在函数定义时运行一次。存储在**\_\_func\_\_.\_\_defualt\_\_**中
```python
class HauntedBus:
	"""A bus model haunted by ghost passengers"""
	def __init__(self, passengers=[]):
		self.passengers = passengers
```

### Defensive Programming with Mutable Parameters
 如果不希望改变传入内容可以使用一份copy
```python
self.passengers = list(passengers)
```

## del and Garbage Collection
当数据最后一个引用不指向数据后，没有方法可以再度引用数据，这时候数据会触发垃圾回收。还有使用del 也可能引起这样的问题。CPython 中的垃圾回收算法是引用计数，CPython 2.0 中使用新算法，可以让循环引用但是没有其他引用指向他们的环状结构被垃圾回收正确处理掉。

```
>>> import weakref
>>>s1={1,2,3}
>>>s2=s1
>>> def bye():
... print('Gone with the wind...') ...
>>> ender = weakref.finalize(s1, bye) >>> ender.alive
True
>>> del s1
>>> ender.alive
True
>>> s2 = 'spam'
Gone with the wind... >>> ender.alive False

```

## Weak References
有些时候我们希望能控制变量的寿命，这时候要使用缓存。Weak references 并不会增加一个物品的引用次数。

## The WeakValueDictionary Skit


### Limitations of Weak References

## Tricks Python Plays with Immutables 
```
>>>t1=(1,2,3) 
>>>t3=(1,2,3) # 
>>>t3ist1 # 
False
>>> s1 = 'ABC' 
>>>s2='ABC' # 
>>>s2iss1# True
```
> 不可变对象为了节省空间都是应用
