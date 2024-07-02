+++
title = 'Go Http Server'
date = 2024-07-03T00:17:52+08:00
draft = false
+++

Go 语言的 `net/http` 包提供了一个 HTTP 服务器，可以用来处理 HTTP 请求。对于一个不那么复杂的应用，我们完全可以只使用这个包来构建一个完整的 Web 服务器。

## 创建一个简单的 HTTP 服务器

一个简单的 HTTP 服务器可以通过以下代码来创建：

```go
package main

import (
	"errors"
	"fmt"
	"io"
	"net/http"
	"os"
)

func getRoot(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("got / request\n")
	io.WriteString(w, "This is index!\n")
}
func getHello(w http.ResponseWriter, r *http.Request) {
	fmt.Printf("got /hello request\n")
	io.WriteString(w, "Hello, World!\n")
}

func main() {
	http.HandleFunc("/", getRoot)
	http.HandleFunc("/hello", getHello)

	err := http.ListenAndServe(":3333", nil)
	if errors.Is(err, http.ErrServerClosed) {
		fmt.Printf("server closed\n")
	} else if err != nil {
		fmt.Printf("error starting server: %s\n", err)
		os.Exit(1)
	}
}
```

在这个例子中，我们定义了两个处理函数 `getRoot` 和 `getHello`，分别处理 `/` 和 `/hello` 路径的请求。然后我们使用 `http.HandleFunc` 函数将这两个处理函数注册到默认的多路复用器上。最后我们使用 `http.ListenAndServe` 函数启动一个 HTTP 服务器，监听 `:3333` 端口。

## 多路复用器

在上面的例子中，我们使用了默认的多路复用器。多路复用器是一个 HTTP 处理器，它根据请求的路径来调用不同的处理函数。如果我们想要自定义多路复用器，我们可以使用 `http.NewServeMux` 函数来创建一个新的多路复用器，然后使用 `http.Handle` 函数将处理函数注册到这个多路复用器上。

```go
package main

import (
    "errors"
    "fmt"
    "io"
    "net/http"
    "os"
)

func getRoot(w http.ResponseWriter, r *http.Request) {
    fmt.Printf("got / request\n")
    io.WriteString(w, "This is index!\n")
}

func getHello(w http.ResponseWriter, r *http.Request) {
    fmt.Printf("got /hello request\n")
    io.WriteString(w, "Hello, World!\n")
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", getRoot)
    mux.HandleFunc("/hello", getHello)

    err := http.ListenAndServe(":3333", mux)
    if errors.Is(err, http.ErrServerClosed) {
        fmt.Printf("server closed\n")
    } else if err != nil {
        fmt.Printf("error starting server: %s\n", err)
        os.Exit(1)
    }
}
```

在这个例子中，我们创建了一个新的多路复用器 `mux`，然后使用 `mux.HandleFunc` 函数将处理函数注册到这个多路复用器上。最后我们使用 `http.ListenAndServe` 函数启动一个 HTTP 服务器，监听 `:3333` 端口。

## 多个 HTTP 服务器

有时候我们可能需要在同一个程序中运行多个 HTTP 服务器。比如我们可能有一个公共网站和一个私有管理网站，我们想要从同一个程序中运行这两个网站。由于默认的 HTTP 服务器只能有一个，我们无法使用默认的 HTTP 服务器来实现这个功能。在这种情况下，我们可以使用 `http.Server` 类型来创建多个 HTTP 服务器。

```go
package main

import (
    "context"
    "errors"
    "fmt"
    "io"
    "net"
    "net/http"
    "os"
)

const keyServerAddr = "serverAddr"

func getRoot(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()

    fmt.Printf("%s: got / request\n", ctx.Value(keyServerAddr))
    io.WriteString(w, "This is index!\n")
}

func getHello(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()

    fmt.Printf("%s: got /hello request\n", ctx.Value(keyServerAddr))
    io.WriteString(w, "Hello, World!\n")
}

func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", getRoot)
    mux.HandleFunc("/hello", getHello)

    ctx, cancelCtx := context.WithCancel(context.Background())
    serverOne := &http.Server{
        Addr:    ":3333",
        Handler: mux,
        BaseContext: func(l net.Listener) context.Context {
            ctx = context.WithValue(ctx, keyServerAddr, l.Addr().String())
            return ctx
        },
    }
    serverTwo := &http.Server{
        Addr:    ":4444",
        Handler: mux,
        BaseContext: func(l net.Listener) context.Context {
            ctx = context.WithValue(ctx, keyServerAddr, l.Addr().String())
            return ctx
        },
    }
    go func() {
        err := serverOne.ListenAndServe()
        if errors.Is(err, http.ErrServerClosed) {
            fmt.Printf("server one closed\n")
        } else if err != nil {
            fmt.Printf("error listening for server one: %s\n", err)
        }
        cancelCtx()
    }()
    go func() {
        err := serverTwo.ListenAndServe()
        if errors.Is(err, http.ErrServerClosed) {
            fmt.Printf("server two closed\n")
        } else if err != nil {
            fmt.Printf("error listening for server two: %s\n", err)
        }
        cancelCtx()
    }()

    <-ctx.Done()
}
```

在这个例子中，我们创建了两个 HTTP 服务器 `serverOne` 和 `serverTwo`，分别监听 `:3333` 和 `:4444` 端口。我们使用 `BaseContext` 函数来设置上下文信息，然后在处理函数中获取这个上下文信息。

## 处理查询字符串

查询字符串是一个用户可以使用的方式，来影响 HTTP 响应的一种方式。查询字符串是一个添加到 URL 末尾的一组值。它以 `?` 字符开始，使用 `&` 作为分隔符添加额外的值。查询字符串值通常用作一种过滤或自定义 HTTP 服务器发送的响应结果的方式。例如，一个服务器可能使用 `results` 值，允许用户指定类似 `results=10` 这样的值，表示他们想要在结果列表中看到 10 个项目。

```go
func getRoot(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()

    hasFirst := r.URL.Query().Has("first")
    first := r.URL.Query().Get("first")
    hasSecond := r.URL.Query().Has("second")
    second := r.URL.Query().Get("second")

    fmt.Printf("%s: got / request. first(%t)=%s, second(%t)=%s\n",
        ctx.Value(keyServerAddr),
        hasFirst, first,
        hasSecond, second)
    io.WriteString(w, "This is index!\n")
}
```

在这个例子中，我们使用 `r.URL.Query().Has` 和 `r.URL.Query().Get` 函数来获取查询字符串的值。`Has` 函数用于检查查询字符串中是否包含某个值，`Get` 函数用于获取查询字符串中的值。

## 读取请求体

当创建一个基于 HTTP 的 API 时，用户可能需要发送比 URL 长度限制更多的数据，或者我们的页面可能需要接收不是关于数据应该如何解释的数据，比如过滤器或结果限制。在这些情况下，通常会将数据包含在请求体中，并使用 `POST` 或 `PUT` HTTP 请求发送。

```go
func getRoot(w http.ResponseWriter, r *http.Request) {
    // ...
    second := r.URL.Query().Get("second")

    body, err := ioutil.ReadAll(r.Body)
    if err != nil {
        fmt.Printf("could not read body: %s\n", err)
    }

    fmt.Printf("%s: got / request. first(%t)=%s, second(%t)=%s, body:\n%s\n",
        ctx.Value(keyServerAddr),
        hasFirst, first,
        hasSecond, second,
        body)
    io.WriteString(w, "This is index!\n")
}
```

在这个例子中，我们使用 `ioutil.ReadAll` 函数来读取请求体中的数据。这个函数返回一个字节切片，包含请求体中的数据。

## 获取表单数据

很长一段时间以来，使用表单发送数据是用户向 HTTP 服务器发送数据并与网站交互的标准方式。尽管表单现在不像过去那样流行，但它们仍然有许多用途，作为用户向网站提交数据的一种方式。`*http.Request` 类型的值在 `http.HandlerFunc` 中也提供了一种访问这些数据的方式，类似于它提供了访问查询字符串和请求体的方式。在这个例子中，我们将更新我们的 `getHello` 程序，从表单中接收用户的姓名，并用他们的姓名回复他们。

```go
func getHello(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()

    fmt.Printf("%s: got /hello request\n", ctx.Value(keyServerAddr))

    myName := r.PostFormValue("myName")
    if myName == "" {
        myName = "HTTP"
    }
    io.WriteString(w, fmt.Sprintf("Hello, %s!\n", myName))
}
```

在这个例子中，我们使用 `r.PostFormValue` 函数来获取表单中的数据。这个函数返回表单中指定字段的值。

## 使用头部和状态码响应

HTTP 协议使用一些用户通常看不到的特性来发送数据，以帮助浏览器或服务器进行通信。其中之一是状态码，用于服务器告诉 HTTP 客户端服务器是否认为请求成功，或者服务器或客户端是否出现了问题。

HTTP 服务器和客户端之间另一种通信方式是使用头部字段。头部字段是一个键和值，客户端或服务器将其发送给对方，以让对方了解自己的情况。HTTP 协议预定义了许多头部，比如 `Accept`，客户端使用它来告诉服务器它可以接受和理解的数据类型。我们也可以使用 `x-` 前缀定义自己的头部字段，然后是名称的其余部分。

```go
func getHello(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()

    fmt.Printf("%s: got /hello request\n", ctx.Value(keyServerAddr))

    myName := r.PostFormValue("myName")
    if myName == "" {
        w.Header().Set("x-extra-field", "myName")
        w.WriteHeader(http.StatusBadRequest)
        return
    }
    io.WriteString(w, fmt.Sprintf("Hello, %s!\n", myName))
}
```

在这个例子中，我们使用 `w.Header().Set` 函数来设置自定义头部字段，然后使用 `w.WriteHeader` 函数设置状态码。在这个例子中，我们设置了一个 `x-extra-field` 头部字段，然后设置了一个 `400` 状态码。

## 总结

在这篇文章中，我们介绍了如何使用 Go 语言的 `net/http` 包来创建一个简单的 HTTP 服务器。我们还介绍了如何使用多路复用器来处理不同的请求路径，以及如何创建多个 HTTP 服务器。通过这些例子，我们可以更好地理解 Go 语言的 HTTP 服务器是如何工作的。

## 参考资料

1. [How To Make an HTTP Server in Go | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-make-an-http-server-in-go)
2. [Serving Static Sites with Go – Alex Edwards](https://www.alexedwards.net/blog/serving-static-sites-with-go)
3. [Go by Example: HTTP Servers](https://gobyexample.com/http-servers)
