# Concurrency with Futures

本章介绍`futures`包，用于表示并发执行操作。

## Example: Web Downloads in Three Styles

下载网页的三种不同方式。

1. 顺序执行，依次下载。
2. 使用多线程`futures`包一次请求，在数据到达时处理。
3. 使用`asyncio`异步请求。

## A Sequential Download Script

同步方案没有任何新内容，依次请求图片，返回后打印然后保存。

## Downloading with concurrent.futures


