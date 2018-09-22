# CHAPTER 16 Coroutines
yield关键词有两种用法，
一种是生成器，采用**yield item**的模式一次**next**生产一个物品。
另一种是协程，采用**item = yield**的模式一次**send**消费一个物品。

• The behavior and states of a generator operating as a coroutine
• Priming a coroutine automatically with a decorator
• How the caller can control a coroutine through the .close() and .throw(...) methods of the generator object
• How coroutines can return values upon termination
• Usage and semantics of the new yield from syntax
• A use case: coroutines for managing concurrent activities in a simulation

## How Coroutines Evolved from Generators
python先是增加了 a = yield  和 .send 作为协程
又增加了yield from简化协程。

## Basic Behavior of a Generator Used as a Coroutine
协程有四种状态，创建，运行，暂停，关闭
刚创建时必须用next()或者.send(None)启动。
![02163c37573c385448971dfe5626562f.png](evernotecid://60614192-C7C5-4611-9CAC-5D15AB1BE3D8/appyinxiangcom/7169492/ENResource/p13772)

## Example: Coroutine to Compute a Running Average
可以实现和第七章闭包类似的功能。


## Decorators for Coroutine Priming
协程求平均，必须在创建后先next一次，才能send数据。可以用decorator辅助完成这次next操作。

## Coroutine Termination and Exception Handling
关闭coroutine的方法有:
1. send无法处理的数据比如None,比如Exception
2. 调用.throw(Exception)
3. 调用.close()
coroutine结束最好用try: finnally进行后处理。

## Returning a Value from a Coroutine

协程可以有返回值，运行到返回值时会raise StopIteration错误并且

## Using yield from
yield from 可以打开一个双向通道，简化外部函数调用和内部生成器之间的交互。

## The Meaning of yield from
```python
_i = iter(EXPR) try:
_y = next(_i)
except StopIteration as _e:
_r = _e.value else:
while 1:
_s = yield _y
try:
_y = _i.send(_s)
except StopIteration as _e: _r = _e.value
break
RESULT = _r
```
next 等价于.send(None)

## Use Case: Coroutines for Discrete Event Simulation
`Coroutines are a natural way of expressing many algorithms, such as simulations, games, asynchronous I/O, and other forms of event-driven programming or co-operative mul‐ titasking.10`

> A simulation shows how to implement concur‐ rent activities using coroutines instead of threads—and this will greatly help when we tackle asyncio with in Chapter 18.

### About Discrete Event Simulations

DES 类似回合游戏，等待下一个事件的发生。
continuous simuliaiton 则是类似及时游戏，有个内置时钟一直运行。

### The Taxi Fleet Simulation
