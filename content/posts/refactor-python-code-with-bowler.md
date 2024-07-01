+++
title = '使用 Bowler 重构 Python 代码'
date = 2024-07-01T01:35:37+08:00
tags = ['Python', 'Bowler', '重构']
draft = false
+++

在 Python 中，我们经常需要对代码进行重构，以提高代码的可读性和可维护性。Bowser 是一个 Python 代码重构工具，它可以帮助我们自动化重构代码。

## Bowler

Bowler 是一个 Python 代码重构工具，它基于 Python 标准库中的 lib2to3 构建，只需要很少的第三方依赖，比如 click，用于常见组件。Bowler 可以在语法树级别上操作 Python 代码，可以进行安全的、大规模的代码修改，同时保证生成的代码可以编译和运行。Bowler 提供了一个简单的命令行接口和一个 Python 中的流畅 API，用于在代码中生成复杂的代码修改。

## 安装 Bowler

要安装 Bowler，可以使用 pip 命令：

```bash
pip install bowler
```

## 使用 Bowler

使用 Bowler 非常简单，只需要两步：

1. 创建一个 Python 脚本，用于定义代码修改规则。

2. 运行 Bowler 命令，指定要修改的文件和代码修改规则。

下面是一个简单的例子，用于将所有的 `foo` 函数改为 `bar` 函数：

```python
from bowler import Query

(
    Query(".")
    .select_function("foo")
    .rename("bar")
    .execute()
)
```

在这个例子中，我们使用 `Query` 类创建了一个查询对象，然后使用 `select_function` 方法选择了所有的 `foo` 函数，最后使用 `rename` 方法将这些函数重命名为 `bar` 函数。最后，我们调用 `execute` 方法执行这个查询。

## Bowler API

### Query

Bowler 提供了一个流式的查询 API，用于构建和执行重构脚本。每个方法都返回查询对象作为结果，允许您轻松地将多个方法调用连接在一起，而无需为每个方法调用分配查询对象或引用该变量。

Bowler API 中的一些常用方法：

- `Query()`：创建一个查询对象。
- `.select()`：选择一个节点。
- `.fixer()`：定义一个修复器。
- `.filter()`：过滤节点。
- `.modify()`：修改节点。
- `.process()`：处理节点。
- `.execute()`：执行查询。
- `.diff()`：显示修改的差异。
- `.write()`：写入修改。
- `.dump()`：显示修改。

### 选择器

选择器是使用 lib2to3 模式语法定义的，用于在语法树中搜索匹配该模式的元素，并在找到匹配时捕获特定的嵌套节点。模式可以嵌套，具有交替分支，允许任意复杂的匹配。我们通过它列出语法元素，可选地后跟包含嵌套匹配表达式的尖括号来完成的。可以使用 `any` 关键字来匹配语法元素，而 `*` 表示重复零次或多次的元素。比如：

```python
pattern = f"""
expr_stmt<
    attr_name='{name}' attr_value=any*
>
"""
```

或者

```python
pattern = f"""
    classdef<
        any*
        suite<
            any* funcdef any*
        >
    >
"""
```

表达式的匹配到的根元素默认情况下将被传递给过滤器和修饰器。通过在表达式中的元素前面加上所需的名称和等号来捕获嵌套元素，这些元素将显示在过滤器和修饰器的 Capture 参数中。方括号可以表示可选（零个或一个）元素。

```python
pattern = f"""
    classdef<
        "class" name=NAME ["(" [ancestors=arglist] ")"] ":"
        suite<
            any*
            funcdef< "def" func_name=NAME any* >
            any*
        >
    >
"""
```

要捕获语法元素的多种可能的排列，可以使用 `|` 与括号结合表达式。可以为每个表达式重用捕获名称，并且可以进一步嵌套替代表达式。

比如，要在单个选择器中查找函数定义和函数调用：

```python
pattern = f"""
    (
        funcdef< "def" name=NAME "(" [args=typedargslist] ")" any* >
    |
        power< name=NAME trailer< "(" [args=arglist] ")" > any* >
    )
"""
```

### 过滤器

过滤器是一个函数，它接受一个节点并返回一个布尔值，用于确定是否应该对该节点执行修复器。过滤器可以用于过滤节点，以便只对满足特定条件的节点执行修复器。比如：

```python
from bowler import LN, Capture, Filename

# 过滤器
# node: 匹配的语法树元素
# capture: 捕获的子元素
# filename: 要修改的文件
# 返回值: True 表示应该修改，False 表示不应该修改
def some_filter(node: LN, capture: Capture, filename: Filename) -> bool:
    ...
```

### 修复器

修复器是一个函数，它接受一个节点和一个捕获对象，并返回一个节点，用于修改、添加、删除或替换原始匹配的语法树元素。修复器可以在语法树中的任何位置进行修改，可以在匹配的元素之上或之下进行修改，并且可以包含多个修改。

```python
from bowler import LN, Capture, Filename

# 修复器
# node: 匹配的语法树元素
# capture: 捕获的子元素
# filename: 要修改的文件
# 返回值: 修改后的语法树元素
def modifier(node: LN, capture: Capture, filename: Filename) -> Optional[LN]:
    ...
```

## 注意事项

使用 Bowler 时，需要注意以下几点：

1. 在修改代码之前，最好先备份代码，以防修改出错。

2. 在修改代码之后，最好进行代码审查，以确保修改的代码符合预期。

3. 在修改代码之后，最好运行测试用例，以确保修改的代码没有引入新的 bug。

4. 在修改代码之后，最好提交代码，以便其他人可以查看修改的代码。

## 总结

使用 Bowler 可以帮助我们自动化重构 Python 代码，提高代码的可读性和可维护性。在使用 Bowler 时，需要注意备份代码、代码审查、运行测试用例和提交代码等事项，以确保修改的代码符合预期。

## 参考资料

1. [Bowler Documentation](https://pybowler.io/)
1. [Bowler GitHub](https://github.com/facebookincubator/bowler)
