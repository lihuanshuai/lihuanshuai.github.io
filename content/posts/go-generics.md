+++
title = 'Go 泛型'
date = 2024-07-01T00:51:01+08:00
tags = ['Go', 'Generics']
draft = false
+++

Go 1.18 版本引入了泛型，这是 Go 语言的一个重大更新。泛型是一种编程范式，它允许我们编写更加通用和灵活的代码。

Go 通过类型参数来实现泛型。类型参数是一种特殊的参数，它可以接受任意类型的值。我们可以在函数、结构体、接口等地方使用类型参数。

## 类型参数

类型参数是一种特殊的参数，它可以接受任意类型的值。我们可以在函数、结构体、接口等地方使用类型参数。

```go
package main

import "fmt"

func Print[T any](v T) {
    fmt.Println(v)
}

func main() {
    Print(42)
    Print("hello")
}
```

在这个例子中，我们定义了一个 `Print` 函数，它接受一个类型参数 `T`，然后打印这个参数的值。我们可以在调用这个函数时传入任意类型的值。

## 类型约束

类型约束是一种限制类型参数的方式，它可以让我们指定类型参数必须满足的条件。我们可以使用类型约束来限制类型参数的行为，比如指定类型参数必须实现某个接口。

```go
package main

import "fmt"

type Stringer interface {
    String() string
}

func Print[T Stringer](v T) {
    fmt.Println(v.String())
}

type MyString string

func (s MyString) String() string {
    return string(s)
}

func main() {
    Print(MyString("hello"))
}
```

在这个例子中，我们定义了一个 `Stringer` 接口，它包含一个 `String` 方法。然后我们定义了一个 `Print` 函数，它接受一个类型参数 `T`，并要求这个类型参数必须实现 `Stringer` 接口。最后我们定义了一个 `MyString` 类型，并实现了 `String` 方法，然后调用 `Print` 函数。

Go 还提供了一些内置的类型约束，比如 `comparable`、`ordered`、`numeric` 等。我们可以使用这些内置的类型约束来限制类型参数的行为。

```go
package main

import "fmt"

func Min[T comparable](a, b T) T {
    if a < b {
        return a
    }
    return b
}

func main() {
    fmt.Println(Min(42, 24))
}
```

此外，我们还可以使用 `any` 关键字来指定类型参数必须是任意类型。

## 总结

Go 泛型是一个非常强大的特性，它可以让我们编写更加通用和灵活的代码。通过类型参数和类型约束，我们可以实现更加灵活的代码复用和更加安全的类型检查。

## 参考资料

1. [Tutorial: Getting started with generics - The Go Programming Language](https://go.dev/doc/tutorial/generics)
