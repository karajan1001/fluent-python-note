# CHAPTER 18 Concurrency with asyncio

并发是指的同时处理很多事情，并行是指的同时进行很多运算。两者有一定的关联，但是不完全一样。并发是架构层面上的，并行是执行层面上的。本章介绍`asyncio`一种使用事件循环驱动的并发包。本章可以结合[500 lines 中的协程爬虫案例](http://www.aosabook.org/en/500L/a-web-crawler-with-asyncio-coroutines.html)一起看

本章的重点：
- 比较单线程程序和异步程序。
- 比较`asyncio.Future`和`concurrent.futures.Future`
- 给出17章的并发下载任务的异步版本。
- 如何利用异步编程在不使用线程和进程的情况下处理高并发的网络应用。
- 为了协程相比回调函数在处理异步时是一个巨大进步。
- 如何使用线程防止event loop的阻塞。
- 实现异步服务和思考高并发网页引用。
- 为何异步对于python生态是一个巨大冲击。

## Thread Versus Coroutine: A Comparison

使用多线程的这代码没啥难懂的，唯一需要注意的是，上次自己写进度条没有写`sys.stdout.flush`这是状态无法及时更新的主要原因

> 在异步编程中，除非你想要把主线程堵塞，否则永远不要使用`time.sleep(...)`。 如果需要等待一段实现，使用`yield from asyn cio.sleep(DELAY)`来代替。

> 装饰器`@asyncio.coroutine`不是必须的但是却很有必要，因为它会让我们在调试时获得更多信息。

比较线程和协程不同点可以知道：
- `asyncio.Task`和`threading.Thread`地位相当。
- Task驱动协程，Thread驱动可函数。
- 我们不需要亲自创建Task，而通过`asyncio.async`来创建。
- 调用`asyncio.async`时协程就已经开始运行，而线程需要显示的命令来开始运行。
- 线程不需要结束语句，因为线程可以从任意时刻结束。而Task需要显示`Task.cancel`来结束。
- 协程需要用`loop.run_until_complete`启动。

线程因为调度是系统完成可以随时打断，所以需要对数据加锁保证数据安全。而协程的调度是自己控制，同时只有一个任务运行，取消时必定处于yield from等待状态。

### asyncio.Future: Nonblocking by Design

两个`Future`包实现的接口类似，但是两者实现的原理不同，所以无法互换。Future 都是表示一个未来执行的代码。不过`asyncio`输入协程，返回一个task。task的暂停时候用`yield from`返回中间结果, 将控制权返还给event\_loop。

### Yielding from Futures, Tasks, and Coroutines
有两种方式生成task
```python
asyncio.async(coro_or_future, *, loop=None)
BaseEventLoop.create\_task(coro)
```

## Downloading with asyncio and aiohttp

asyncio官方网络请求只支持`tcp`和`udp`模式, 我们需要借助第三方库来实现http请求。需要注意点为：

```python
# 需要异步的函数使用协程。
@asyncio.coroutine 
def get_flag(cc): 
    resp = yield from aiohttp.request('GET', url) # http请求用aiohttp

to_do = [download_one(cc) for cc in sorted(cc_list)]  # 任务协程列表
wait_coro = asyncio.wait(to_do) # 一个协程，只有在所有任务结束后才会结束
res, _ = loop.run_until_complete(wait_coro) # 开始运行，返回结果为已完成的协程和未完成的协程。
loop.close()  # 关闭event loop
```
> 这里要注意我们并没有创建一个协程，然后最后关闭，仅仅获得了一个它的引用，所以不应该使用`contentmanager`和`with`来开启和关闭它。

## Running Circling Around Blocking Calls

运算时间对比如下所示:

| Device | L1  cache | L2 cache | RAM |  disk | network |
|:---:|:---:|:---:|:---:|:---:|:---:|
| CPU cycles | 3 | 14 | 250 | 41,000,000 |  240,000,000 |
| Proportional “human” scale | 3 seconds | 14 seconds | 250 seconds | 1.3 years | 7.6 years  |

为了防止IO堵塞线程，有两种方法：
1. 开启单独的线程读写IO。
2. 使用非阻塞性异步调用。

传统异步编程使用回调函数的方式进行。异步内容注册一个回调函数，然后在for loop 中等待事件的发生。事件发生后使用回调函数处理相应的数据。而在协程的for loop中，使用协程的`.send()`与传统的回调函数其实是一样的。而且协程可以避免地狱（下文中会提到）。

> 目前asyncio没有提供异步文件读写，如果要达到最大性能，需要启动线程池来进行文件读写工作。

## Enhancing the asyncio downloader Script

 虽然异步和多线程并发在下载任务上总耗时差别不大，但是因为异步并发更高所以更容易被当成dos攻击。

### Using asyncio.as\_completed
`asyncio.Semaphore`可以用来控制并发总量。可以通过锁来控制同时启动并发数。`.acqure`会加一而`.release`减一。当锁数量为0时就会阻塞。
```python
semaphore = asyncio.Semaphore(concur_req) # 初始化 
with (yield from semaphore): # 自动加减锁
     image = yield from get_flag(base_url, cc)
```

要用tqdm来进展的话，需要将整个函数包装成一个协程。这样loop的时候tqdm才会不断更新进展。
```python
to_do_iter = asyncio.as_completed(to_do)  # 获取完成结果的生成器
to_do_iter = tqdm.tqdm(to_do_iter, total=len(cc_list)) # 更新完成结果
```

### Using an Executor to Avoid Blocking the Event Loop

文件读写也会阻塞IO，一个解决方案是使用executor来执行文件IO。

```python
loop = asyncio.get_event_loop()  # 获得当前的event loop 的引用
loop.run_in_executor(None, save_flag, image, cc.lower() + '.gif') # 第一个参数是执行线程，如果没有则从默认线程池中抽取一个线程。
```

## From Callbacks to Futures and Coroutines
本节对比协程和回调函数，回调函数处理异常非常麻烦，因为函数返回后清空了所有信息，而结果返回时如果出错难以复原当时的场景。

### Doing Multiple Requests for Each Download


