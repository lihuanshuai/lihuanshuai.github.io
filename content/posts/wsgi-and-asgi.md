+++
title = 'WSGI and ASGI'
date = 2024-07-03T01:07:46+08:00
tags = ['Python', 'WSGI', 'ASGI']
draft = false
+++

WSGI (Web Server Gateway Interface) 和 ASGI (Asynchronous Server Gateway Interface) 是 Python Web 应用程序的两种标准接口。它们定义了 Web 服务器和 Web 应用程序之间的通信协议，使得 Web 应用程序可以在不同的 Web 服务器上运行。本文将介绍 WSGI 和 ASGI 的概念、特点和用法。

## WSGI

WSGI 是 Python Web 应用程序的标准接口，它定义了 Web 服务器和 Web 应用程序之间的通信协议。WSGI 的核心思想是将 Web 应用程序封装为一个可调用对象，这个对象接受两个参数：`environ` 和 `start_response`，并返回一个可迭代对象，用于生成响应内容。

### WSGI 应用程序

WSGI 应用程序是一个可调用对象，它接受两个参数：`environ` 和 `start_response`，并返回一个可迭代对象。`environ` 是一个包含 HTTP 请求信息的字典，`start_response` 是一个用于发送 HTTP 响应头的函数。

```python
def application(environ, start_response):
    status = '200 OK'
    headers = [('Content-Type', 'text/plain')]
    start_response(status, headers)
    return [b'Hello, World!']
```

在这个例子中，我们定义了一个简单的 WSGI 应用程序 `application`，它接受 `environ` 和 `start_response` 两个参数，并返回一个包含 `Hello, World!` 的可迭代对象。

`environ` 参数是一个包含 HTTP 请求信息的字典，它包含了 HTTP 请求的方法、路径、查询字符串、请求头等信息。常用的键有:

- `REQUEST_METHOD`: HTTP 请求方法，如 `GET` 或 `POST`。
- `PATH_INFO`: 请求路径，如 `/hello`。
- `QUERY_STRING`: 查询字符串，如 `name=world`。
- `CONTENT_TYPE`: 请求头中的 `Content-Type` 字段。
- `CONTENT_LENGTH`: 请求头中的 `Content-Length` 字段。
- `SERVER_NAME`: 服务器名称，如 `example.com`。
- `SERVER_PORT`: 服务器端口，如 `80`。
- `SERVER_PROTOCOL`: HTTP 协议版本，如 `HTTP/1.1`。
- `HTTP_` 开头的键: 请求头，如 `HTTP_USER_AGENT`。

### WSGI 服务器

WSGI 服务器是一个遵循 WSGI 协议的 Web 服务器，它负责接收 HTTP 请求、调用 WSGI 应用程序、发送 HTTP 响应。常见的 WSGI 服务器有 [Gunicorn][], [uWSGI][], [mod_wsgi][] 等。

[Gunicorn]: https://gunicorn.org/

[uWSGI]: https://uwsgi-docs.readthedocs.io/en/latest/

[mod_wsgi]: https://modwsgi.readthedocs.io/en/develop/

## ASGI

<!-- ASGI is a spiritual successor to [WSGI][], the long-standing Python standard for compatibility between web servers, frameworks, and applications.

[[WSGI]] succeeded in allowing much more freedom and innovation in the Python web space, and ASGI’s goal is to continue this onward into the land of asynchronous Python.

[WSGI]: https://www.python.org/dev/peps/pep-3333/

[^asgi/introduction]

[^asgi/introduction]: [Introduction — ASGI 3.0 documentation](https://asgi.readthedocs.io/en/latest/introduction.html) -->

ASGI 是 Python Web 应用程序的新标准接口，它是 WSGI 的升级版，支持异步处理和长连接。ASGI 的核心思想是将 Web 应用程序封装为一个可调用对象，这个对象接受两个参数：`scope` 和 `receive`，并返回一个可调用对象，用于处理请求。

### ASGI 应用程序

ASGI 应用程序是一个可调用对象，它接受两个参数：`scope` 和 `receive`，并返回一个可调用对象。`scope` 是一个包含请求信息的字典，`receive` 是一个用于接收请求消息的函数。

```python
async def application(scope, receive):
    await receive()
    return lambda: None
```

在这个例子中，我们定义了一个简单的 ASGI 应用程序 `application`，它接受 `scope` 和 `receive` 两个参数，并返回一个空的可调用对象。

`scope` 参数是一个包含请求信息的字典，它包含了请求类型、路径、查询字符串、请求头等信息。常用的键有:

- `type`: 请求类型，如 `http` 或 `websocket`。
- `path`: 请求路径，如 `/hello`。
- `query_string`: 查询字符串，如 `name=world`。
- `headers`: 请求头，如 `User-Agent: Mozilla/5.0`.
- `client`: 客户端信息，如 `
- `server`: 服务器信息，如 `localhost:8000`.

### ASGI 服务器

ASGI 服务器是一个遵循 ASGI 协议的 Web 服务器，它负责接收请求、调用 ASGI 应用程序、发送响应。常见的 ASGI 服务器有 [Daphne][], [Hypercorn][], [Uvicorn][] 等。

[Daphne]: https://github.com/django/daphne

[Hypercorn]: https://pgjones.gitlab.io/hypercorn/

[Uvicorn]: https://www.uvicorn.org/

## 参考资料

1. [PEP 3333 -- Python Web Server Gateway Interface v1.0.1](https://www.python.org/dev/peps/pep-3333/)
1. [PEP 3336 -- WSGI 1.0.1 Specification](https://www.python.org/dev/peps/pep-3336/)
1. [WSGI.org](https://wsgi.readthedocs.io/en/latest/)
1. [ASGI 3.0 documentation](https://asgi.readthedocs.io/en/latest/)
