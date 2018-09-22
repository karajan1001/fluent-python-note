# CHAPTER 4 Text versus Bytes
## Character Issues
python 的字符串都是 unicode 格式。unicode是人读的，utf-8是机器读的 ，

## Byte Essentials
### Structs and Memory Views
memory view 不允许创建或者储存比特流，但可以让你解包其他方式创造的数据。比如读取，jpg等图像文件的信息。

```python
>>> import struct
>>> fmt = '<3s3sHH' #
>>> with open('filter.gif', 'rb') as fp: 
...      img = memoryview(fp.read()) # ...
>>> header = img[:10] #
>>> bytes(header) # b'GIF89a+\x02\xe6\x00'
>>> struct.unpack(fmt, header) # (b'GIF', b'89a', 555, 230)
>>> del header #
>>> del img
```

## Basic Encoders/Decoders
ASCII 或者 gb2312无法encode所有字符，而UTF 系列则可以。UTF-16 包含emoji，UTF-8则是互联网上最常用的格式。

## Understanding Encode/Decode Problems
### Coping with UnicodeEncodeError
将text转为bytes时，因为unicode比bytes格式拥有更多的字符，如果text中的字符无法在bytes格式中找到就会产生这种错误。这时候三种处理方法。
```python
>>> city.encode('cp437', errors='ignore')
b'So Paulo'  # 忽视错误
>>> city.encode('cp437', errors='replace')
b'S?o Paulo' # 替换错误
>>> city.encode('cp437', errors='xmlcharrefreplace')
b'S&#227;o Paulo' #用xml空白替换
```

### Coping with UnicodeDecodeError
如果bytes用某种格式（比如utf-8）无法解释，就会产生这种错误，很多遗留8bytes格式可以解释任何bytes流而不报错。

### SyntaxError When Loading Modules with Unexpected Encoding
python3 默认解码器是utf-8,python2是ascii，如果在windows下打开.py文件经常会报错无法解析文件，只要在文件头加上一行# coding: cp1252就可以解决

### How to Discover the Encoding of a Byte Sequence
用**chardetct**可以帮助你知道bytes stream 的编码格式

### BOM: A Useful Gremlin
比特流的顺序，在很多编码会写在最前面。


## Handling Text Files
处理 文本文件的最好方法就是尽快将其解码。为了让代码能在各种环境下运行，永远不要使用默认的encoder。

### Encoding Defaults: A Madhouse
Python默认的encoding格式很混乱。所以
`Therefore, the best advice about encoding defaults is: do not rely on them.`

## Normalizing Unicode for Saner Comparisons
unicode 中NFC （紧凑模式） 的 **é** 和 NFD（分解模式）**\u0301**显示一样，但是 == 无法等价，需要归一化。 `NFC模式是W3C是推荐格式`
```python
>>> from unicodedata import normalize
>>> s1 = 'café' # composed "e" with acute accent
>>> s2 = 'cafe\u0301' # decomposed "e" and acute accent >>> len(s1), len(s2)
(4, 5)
>>> len(normalize('NFC', s1)), len(normalize('NFC', s2)) (4, 4)
>>> len(normalize('NFD', s1)), len(normalize('NFD', s2)) (5, 5)
>>> normalize('NFC', s1) == normalize('NFC', s2)
```
`NFKC and NFKD `需要谨慎使用，因为它们可能会改变愿意。

### Case folding 
会返回所有的小写字母，包括非英文字母
```python
str.casefold()
```

### Utility Functions for Normalized Text Matching
标准化比较的有用函数
```python
from unicodedata import normalize def nfc_equal(str1, str2):
return normalize('NFC', str1) == normalize('NFC', str2)
def fold_equal(str1, str2):
return (normalize('NFC', str1).casefold() ==
            normalize('NFC', str2).casefold())
```
### Extreme “Normalization”: Taking Out Diacritics

[URL](http://en.wikipedia.org/wiki/S%C3%A3o_Paulo)中的%C3%A3是**ã**去除所有的Diacritics字母有助于提高阅读体验

```python
single_map = str.maketrans("""‚ƒ„†ˆ‹‘’“”•–— ̃›""", """'f"*^<''""---~>""")
multi_map = str.maketrans({
    '€': '<euro>',
    '...': '...',
    'Œ': 'OE',
    'TM': '(TM)',
    'œ': 'oe',
    '‰': '<per mille>',
    '‡': '**',
})
multi_map.update(single_map)
def dewinize(txt):
"""Replace Win1252 symbols with ASCII chars or sequences""" return txt.translate(multi_map)
def asciize(txt):
no_marks = shave_marks_latin(dewinize(txt)) no_marks = no_marks.replace('ß', 'ss')
return unicodedata.normalize('NFKC', no_marks)
```

## Sorting Unicode Text
非ASCII 字符在各地排列顺序不同，所以sorting时必须使用setlocal函数指定当地语言。但是这依赖系统对local设定的支持。

### Sorting with the Unicode Collation Algorithm
可以使用第三方库消除对系统的依赖。
```python
import pyuca
coll = pyuca.Collator()
sorted_fruits = sorted(fruits, key=coll.sort_key)
```
## The Unicode Database
```python
format(unicodedata.numeric(char), '5.2f') # 其中数字那一列
```

![bd804bade1c97e36b7d828c928fabd60.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13375)

## Dual-Mode str and bytes APIs
