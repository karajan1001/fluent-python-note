# CHAPTER 3 Dictionaries and Sets
## Generic Mapping Types

![dd567178b6ad29c25e110cd1a7c4a3e1.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13373)


Dict 类型的key必须可以 hash

## dict Comprehensions
和list 类似，dict方法也可以使用Comprehension构建
```python
 {country: code for code, country in DIAL_CODES}
```

## Overview of Common Mapping Methods

```python
a = {}
a.setdefault('a', []).append(1)
# 比
s = a.get('a', [])
s.append(1)
a['a'] = s
## 更好
```

## Mappings with Flexible Key Lookup
两种方法应对确定缺省值
手写：
**a.__missing__**
使用missing的时候记得要把contain也顺便写了，不然A['a'] 可以取到 'a' in A: 可能会失败。
或者使用
**collections.defaultdict**

## Variations of dict
### orderdict
带有顺序的字典
### chainmap
 嵌套作用域

```python
import builtins
pylookup = ChainMap(locals(), globals(), vars(builtins))

x = {'a': 1, 'b': 2}
y = {'b': 10, 'c': 11}
z = collections.ChainMap(y, x)
>> z
a 1
b 10
c 11
```

### Subclassing UserDict
如果继承自UserDict而不是dict的话**__contain__**方法只取归一化后的值的值就可以了

## Immutable Mappings
不可变字典
```python
>>> from types import MappingProxyType >>>d={1:'A'}
>>> d_proxy = MappingProxyType(d)
>>> d_proxy
mappingproxy({1: 'A'})
>>> d_proxy[1]
'A'
>>> d_proxy[2] = 'x'
```

## Set Theory
下面方法可以查看各种命令的执行分解。
``` python
>>> from dis import dis >>> dis('{1}')
```

![68002970f036d2595b69087294ed7150.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13374)

## dict and set Under the Hood

Python字典会根据数据大小增减hash table中元素的数量，所以在for loop一个dict的时候更改它们的key会导致dict key的顺序变化，导致for loop 无法完成遍历。
