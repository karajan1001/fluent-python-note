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
会启动多个线程，运行完完成一个任务后自动开始下一个任务。但是map有一个问题，它的返回结果顺序和map中的顺序相同，如果第一个结果没有出来，那就算其他线程早已计算完毕，最后结果依然不会返回。如果需要结果计算完毕就返回则应该使用`Executor.submit`和`futures.as_completed`。

> 而且`Executor.submit`和`futures.as_completed`两者比map更为灵活，他们支持提交不同的函数和参数。甚至它们可以同时提供线程和进程。

## Downloads with Progress Display and Error Handling

`TQDM`可以将过程动画化，帮助显示当前进度状态，效果如[视频](https://www.youtube.com/watch?v=M8Z65tAl5l4)所示 

> 要注意高并发很容被当成DOS攻击。

### Error Handling in the flags2 Examples


### Using futures.as\_completed

其他部分和之前相似，但是`executor`不是使用`map`执行脚本，而是使用
```python
to_do_map = {} 
for cc in sorted(cc_list):
    # 提交任务
    future = executor.submit(download_one, cc, base_url, verbose) 
to_do_map[future] = cc # 任务和参数对应
done_iter = futures.as_completed(to_do_map) # 生成器，当每个任务完成时返回相应的值
done_iter = tqdm.tqdm(done_iter, total=len(cc_list)) # 给出完成列表和总数。
for future in done_iter:
    res = future.result() # 拿到结果
```

使用一个字典保存任务和参数可以帮助你在乱序执行时依然可以弄清楚返回结果和对应的任务。

### Threading and Multiprocessing Alternatives

如果是IO密集任务，线程可以有效结果，不过很多更复杂的任务可能需要从`threading`,`lock`等中手动生成。CPU密集型任务则需要用到多进程库，复杂任务同样需要自己DIY，此时需要注意数据在进程间的通信。
