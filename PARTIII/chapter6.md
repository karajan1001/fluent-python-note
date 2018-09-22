# CHAPTER 6 Design Patterns with First-Class Functions
## Case Study: Refactoring Strategy
传统方法实现策略模式
```python
# python 中 定义抽象类的方法
from abc import ABC
class Promotion(ABC)
```

### Function-Oriented Strategy
一个plain 方程不像一个抽象类，可以被更多上下文使用。策略方程内部不会包含内部状态。直接将函数名传递给变量。

### Choosing the Best Strategy: Simple Approach
最简单的方法：
定义一个list，里面包含所有策略。
然后遍历所有策略。

### Finding Strategies in a Module
更好的方法是使用策略发现利用**global**
```python
promos = [globals()[name] for name in globals() if name.endswith('_promo')
and name != 'best_promo']
```

## Command
`Commands are an object-oriented replacement for call‐ backs.`

