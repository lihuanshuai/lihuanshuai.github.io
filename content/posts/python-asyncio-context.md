+++
title = 'Python Asyncio 上下文'
date = 2024-07-01T23:26:51+08:00
draft = false
+++

在 Python 中，我们通常使用 asyncio 模块来编写异步代码。在编写异步代码时，我们经常需要处理上下文的问题，即如何在异步任务中传递上下文信息。Python 3.7 引入了 `contextvars` 模块，它提供了一种简单的方法来在异步任务中传递上下文信息。本文将介绍如何在 Python 中使用 `contextvars` 模块实现上下文传递。

## 上下文

在编写异步代码时，我们经常需要在异步任务中传递上下文信息。比如，我们可能需要在异步任务中获取当前用户的信息，或者在异步任务中记录日志信息。 要实现这个功能，如果我们使用全局变量，那么可能会导致线程安全问题。因为异步任务可能会在不同的线程中执行，这样就会导致全局变量的值在不同的线程中不一致。而且，从理解代码的角度来看，这种方式也不够直观且不易维护。那么通过传递参数的方式呢？显然也不行，不仅会导致代码的耦合度增加，也不利于手指的健康。

那么，有没有一种更好的方法来实现上下文传递呢？答案是有的，Python 3.7 引入了 `contextvars` 模块，它提供了一种简单的方法来在异步任务中传递上下文信息。`contextvars` 模块提供了两个类：`ContextVar` 和 `Token`，它们可以帮助我们在异步任务中传递上下文信息。

## ContextVar

`ContextVar` 类是 `contextvars` 模块的核心类，它用于定义一个上下文变量。`ContextVar` 类有两个方法：`get` 和 `set`，分别用于获取和设置上下文变量的值。`ContextVar` 类还有一个属性 `name`，用于获取上下文变量的名称。

`ContextVar` 类和 `threading.local` 类有一些相似之处，但是 `ContextVar` 类更加适用于异步任务。`ContextVar` 类的值是与当前任务相关的，而不是与当前线程相关的。这样，我们就可以在异步任务中传递上下文信息，而不用担心线程安全问题。

下面是一个简单的例子，用于定义一个上下文变量：

```python
import contextvars

user = contextvars.ContextVar('user')
```

在这个例子中，我们使用 `ContextVar` 类定义了一个上下文变量 `user`。然后，我们可以使用 `get` 方法获取上下文变量的值，使用 `set` 方法设置上下文变量的值。

```python
async def task():
    user.set('Alice')
    await inner_task()

async def inner_task():
    # 获取上下文变量的值
    print(user.get())
```

在这个例子中，我们设置了上下文变量 `user` 的值为 `Alice`，然后使用 `get` 方法获取上下文变量的值。

### 原理

`ContextVar` 类的实现原理是基于 `contextvars.copy_context` 函数。`copy_context` 函数用于获取当前上下文的快照，然后将这个快照传递给 `Task` 类的 `_step` 方法。`_step` 方法在每次调用时都会在任务的上下文中运行协程。

也就是说，`ContextVar` 类的值是与当前 `Task` 相关的，而不是与当前线程相关的。这样，我们就可以在异步任务中传递上下文信息，而不用担心线程安全问题。

这样，也解释了为什么在异步代码中，不能使用 `threading.local` 来传递上下文信息，因为不同的协程可能会在同一个线程中执行，这样就会导致 `threading.local` 的值在不同的协程中不一致。

相关 Python 源码如下：

```python
# asyncio/task.py
class Task:
	def __init__(self, coro):
		...
		# Get the current context snapshot.
		self._context = contextvars.copy_context()
		self._loop.call_soon(self._step, context=self._context)

	def _step(self, exc=None):
		...
		# Every advance of the wrapped coroutine is done in
		# the task's context.
		self._loop.call_soon(self._step, context=self._context)
		...
```

在这个例子中，我们可以看到 `ContextVar` 是和 `Task` 绑定的，这样就可以在异步任务中传递上下文信息。

这样我们可能有个疑问，如果我们代码里没有创建过 Task，那么 `ContextVar` 的怎么办？其实，`loop.run_until_complete` 会创建一个 Task，所以我们可以放心使用 `ContextVar`。

## Token

`Token` 类是 `contextvars` 模块的另一个核心类，它用于完善 `ContextVar` 类的功能。`Token` 类有两个属性：`var` 和 `old_value`，分别用于获取上下文变量和上下文变量的旧值。

下面是一个简单的例子，用于获取上下文变量的旧值：

```python
import contextvars

user = contextvars.ContextVar('user')

async def task():
    user.set('Xen')
    token = user.set('Zoe')
    print(user.get())  # Zoe
    user.reset(token)  # 重置上下文变量的值
    print(user.get())  # Xen

asyncio.run(task())
```

在这个例子中，我们使用 `Token` 类获取了上下文变量的旧值，并使用 `reset` 方法重置了上下文变量的值。

## 总结

`contextvars` 模块提供了一种简单的方法来在异步任务中传递上下文信息。`ContextVar` 类用于定义上下文变量，`Token` 类用于获取上下文变量的旧值。通过 `contextvars` 模块，我们可以更加方便地在异步任务中传递上下文信息。

## 参考资料

1. [PEP 567 -- Context Variables](https://peps.python.org/pep-0567/)
1. [contextvars — Context Variables](https://docs.python.org/3/library/contextvars.html)
1. [Python asyncio](https://docs.python.org/3/library/asyncio.html)
