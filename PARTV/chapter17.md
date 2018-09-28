# Concurrency with Futures

本章介绍`futures`包，用于表示并发执行操作。

## Example: Web Downloads in Three Styles

下载网页的三种不同方式。

1. 顺序执行，依次下载。
2. 使用多线程`futures`包一次请求，在数据到达时处理。
3. 使用`asyncio`异步请求。

### A Sequential Download Script

同步方案没有任何新内容，依次请求图片，返回后打印然后保存。

### Downloading with concurrent.futures

这个包对线程包和进程包进行了高级封装，使得我们可以只用向线程提交需要执行的函数，而不用关心其实现细节。

```python
from concurrent import futures
works = 10 # 线程数
with futures.ThreadPoolExecutor(workers) as executor:
    res = executor.map(download_one, sorted(cc_list)) # 线程池调用download_one, 参数为cc_list

return len(list(res))
```

### Where Are the Futures?

标准库中有两个地方存在`Futures`包, `concurrent.futures.Future`和`asyncio.Future`, 它们都用来表示未完成的计算。它们表示可暂停操作，暂停时它们的状态可以查询，而计算结束后它们的结果可以查询。

> 要记住永远不要直接创造它的实例。因为我们不应该改变它的状态。

## Blocking I/O and the GIL

因为python 有全局解释器锁存在，对于cpu密集型任务多线程并不能有效的提高性能。但是对于IO密集型任务，还是可以取得有效的进展。

## Launching Processes with concurrent.futures

`futures`包同样也可以调用多进程，以提高CPU利用率。`futures.ProcessPoolExecutor()`调用worker数量默认等于cpu数量，`os.cpu_count()`，使用多进程可以显著提高cpu密集型任务的速度，如果需要用`pypy`可以取得更好的效果。

## Experimenting with Executor.map
