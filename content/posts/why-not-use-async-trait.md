+++
title = '为什么不要使用 async-trait?'
date = 2024-06-30T23:34:42+08:00
tags = ['Rust', 'Async', 'Trait', 'async-trait', 'trait_variant']
draft = false
+++

## async-trait

编写 Rust 异步代码时，我们通常会使用 `async` 关键字来定义异步函数，这样可以让函数返回一个 `Future` 对象，然后我们可以通过 `.await` 方法来等待这个 `Future` 对象的结果。但是有时候我们可能会想要在 trait 中定义异步函数，这时候，作为以前的标准做法，我们会使用 `async-trait` crate 来实现这个功能。

`async-trait` crate 提供了一个 `async_trait` 宏，可以让我们在 trait 中定义异步函数。

```rust
use async_trait::async_trait;

#[async_trait]
pub trait MyTrait {
    async fn my_method(&self) -> u32;
}
```

在这个例子中，我们在 `MyTrait` trait 中定义了一个异步函数 `my_method`，然后使用 `async_trait` 宏来标记这个 trait。

## 内置的异步 trait

不过，1.75 版本的 Rust 已经支持了异步 trait，所以我们不再需要使用 `async-trait` crate 来定义异步函数了。我们可以直接在 trait 中使用 `async` 关键字来定义异步函数。

示例 1：

```rust
pub trait MyTrait {
    async fn my_method(&self) -> u32;
}
```

这样就可以了。

实现这个 trait 时，我们可以直接使用 `async` 关键字来定义异步函数。

```rust
struct MyStruct;

impl MyTrait for MyStruct {
    async fn my_method(&self) -> u32 {
        42
    }
}
```

他们的区别在于：

1. 内置的异步 trait 是 Rust 1.75 版本引入的新特性，不需要额外的依赖，而 `async-trait` crate 是一个第三方库，需要额外的依赖。
2. 内置的异步 trait 更加简洁和易读，不需要使用宏来定义异步函数，而 `async-trait` crate 需要使用宏来定义异步函数。
3. 内置的异步 trait 可以直接在 trait 中使用 `async` 关键字来定义异步函数，而 `async-trait` crate 需要使用 `async_trait` 宏来标记 trait。
4. 内置的异步 trait 可以减少代码的编译时间，因为不需要额外的依赖，而 `async-trait` crate 需要额外的依赖，可能会增加代码的编译时间。

## 注意事项

此外，还有一些需要注意的地方：

### 默认实现

如果 trait 中需要提供默认实现，那么不能直接在 trait 中使用 `async` 关键字来定义异步函数，可以返回一个 `Future` 对象，然后在实现中使用 `async` 关键字来定义异步函数。

```rust
use std::future::Future;

pub trait MyTrait {
    fn my_method(&self) -> impl Future<Output = ()> {
        async {
            // do something
        }
    }
}
```

这里，`my_method` 提供了一个默认实现，如果想要写成这样：

```rust
pub trait MyTrait {
    async fn my_method(&self) {
        // do something
    }
}
```

编译器会报错，也许未来的 Rust 版本会支持这种写法。

### Send

在示例 1 中，如果我们这样写，会收到一个编译警告：

```rust
warning: use of `async fn` in public traits is discouraged as auto trait bounds cannot be specified
...
= note: you can suppress this lint if you plan to use the trait only in your own code, or do not care about auto traits like `Send` on the `Future`
help: you can alternatively desugar to a normal `fn` that returns `impl Future` and add any desired bounds such as `Send`, but these cannot be relaxed without a breaking API change
```

这是因为在 trait 中使用 `async` 关键字定义异步函数时，编译器无法推断出 `Future` 对象是否实现了 `Send` trait，所以会给出一个警告。如果我们的异步代码不需要在多线程中使用，可以忽略这个警告:

```rust
#[allow(async_fn_in_trait)]
pub trait MyTrait {
    async fn my_method(&self) -> u32;
}
```

这样，编译器就不会给出警告了。

如果我们的异步代码需要在多线程中使用，比如在 `tokio` 默认的运行时中运行，那么我们需要在 trait 中使用 `async` 关键字定义异步函数时，添加 `Send` trait bound。这里我们可以使用 [trait_variant](https://crates.io/crates/trait_variant) crate 来实现这个功能。

```rust
#[trait_variant::make(Send)]
pub trait MyTrait {
    async fn my_method(&self) -> u32;
}
```

这样，相当于给 `Future` 对象添加了 `Send` trait bound，也就是：

```rust
pub trait MyTrait: Send {
    fn my_method(&self) -> impl Future<Output = u32> + Send;
}
```

这样，我们就可以在多线程中使用这个异步函数了。

### 动态分发

目前，内置的异步 trait 不支持动态分发，也就是不能使用 trait 对象来调用异步函数。如果我们需要动态分发，可以使用 async-trait crate 来实现这个功能。当然，一般情况下，我们不需要动态分发，所以内置的异步 trait 已经足够满足我们的需求了。

## 总结

1.75 版本的 Rust 已经支持了异步 trait，不再需要使用 `async-trait` crate 来定义异步函数了。我们可以直接在 trait 中使用 `async` 关键字来定义异步函数。内置的异步 trait 更加简洁和易读，不需要额外的依赖，可以减少代码的编译时间。内置的异步 trait 使用起来需要注意一些细节，比如默认实现、`Send` trait bound、动态分发等，但整体来说，还是比 `async-trait` crate 更加易于理解和使用。

## 参考

1. [async-trait](https://crates.io/crates/async-trait)
1. [Rust 1.75 Release Notes](https://blog.rust-lang.org/2023/12/28/Rust-1.75.0.html)
1. [Announcing `async fn` and return-position `impl Trait` in traits | Rust Blog](https://blog.rust-lang.org/2023/12/21/async-fn-rpit-in-traits.html)
1. [trait_variant](https://crates.io/crates/trait_variant)
1. [Rust async book](https://rust-lang.github.io/async-book/03_async_await/01_chapter.html)
