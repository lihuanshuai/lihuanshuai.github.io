+++
title = 'Python asyncio 优雅退出'
date = 2024-07-01T01:06:25+08:00
tags = ['Python', 'asyncio', '优雅退出']
draft = false
+++

在 Python 中，我们通常使用 asyncio 模块来编写异步代码。在编写异步代码时，我们经常需要处理优雅退出的问题，即在程序退出时，如何正确地关闭异步任务。本文将介绍如何在 Python 中使用 asyncio 模块实现优雅退出。

## 优雅退出

在编写异步代码时，我们通常会创建一些异步任务，这些任务可能会在程序退出时还没有完成。常见的报错如 `Task was destroyed but it is pending!` 就是因为任务在程序退出时还没有完成，导致任务被销毁了。为了避免这种情况，我们需要在程序退出时正确地关闭异步任务。

要解决这个问题，我们有两种方法：

### 方法一：等待所有任务完成

第一种方法是等待所有任务完成。我们可以使用 `asyncio.gather` 函数来等待所有任务完成，然后再退出程序。

```python
import asyncio

def main():
    // ...
    loop = asyncio.get_event_loop()
    pending = asyncio.all_tasks()
    loop.run_until_complete(asyncio.gather(*pending))
    loop.close()
```

在这个例子中，我们使用 `asyncio.all_tasks` 函数来获取所有的任务，然后使用 `asyncio.gather` 函数来等待所有任务完成。这样就可以保证所有任务在程序退出时都已经完成了。

### 方法二：取消所有任务

第二种方法是取消任务。我们可以使用 `asyncio.Task.cancel` 方法来取消任务，然后再退出程序。

```python
import asyncio

async def main():
    // ...
    tasks = asyncio.all_tasks()
    try:
        await asyncio.gather(*tasks)
    except asyncio.CancelledError:
        for task in tasks:
            task.cancel()
```

在这个例子中，我们使用 `asyncio.all_tasks` 函数来获取所有的任务，然后使用 `asyncio.gather` 函数来等待所有任务完成。如果有任务被取消了，我们可以在 `except asyncio.CancelledError` 分支中取消所有任务。

实践中，我们可以结合信号处理来实现优雅退出。我们可以在收到信号时，先取消所有任务，然后再退出程序。

```python
import asyncio
import signal

async def async_main():
    // ...
    tasks = asyncio.all_tasks()
    try:
        await asyncio.gather(*tasks)
    except asyncio.CancelledError:
        for task in tasks:
            task.cancel()

def main():
    loop = asyncio.get_event_loop()
    main_task = asyncio.create_task(async_main())
    loop.add_signal_handler(signal.SIGINT, main_task.cancel)
    try:
		loop.run_until_complete(main_task)
	finally:
		loop.close()

if __name__ == '__main__':
    main()
```