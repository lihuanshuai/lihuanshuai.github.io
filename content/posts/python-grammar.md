+++
title = 'Python 语法介绍'
date = 2024-06-14T00:13:47+08:00
tags = ['Python', 'Grammar']
draft = false

[params]
mermaid = true
+++

# Python 语法介绍

语法（Grammar），是一门编程语言的基础，它指定了解释器如何解释源代码。Python 语法简洁明了，易于学习和使用，但是也有一些特殊的地方，需要特别注意。

## Grammar 语法

Python 的语法是由一系列规则组成的，这些规则定义了 Python 语言的语法结构。Python 的语法规则是由 Python Enhancement Proposals (PEP) 中的 [PEP 617](https://peps.python.org/pep-0617/) 定义的。

下面是 Python 语法的一部分：

```python
file: [statements] ENDMARKER
interactive: statement_newline
eval: expressions NEWLINE* ENDMARKER
func_type: '(' [type_expressions] ')' '->' expression NEWLINE* ENDMARKER
```

如上所示，`file` 是一个规则名字，`[statements] ENDMARKER` 是规则的表达式。其中，`[statements]` 表示 `statements` 是可选的，`[...]` 表示可选的规则；`ENDMARKER` 这种大写的名字表示一个 token，这里 `ENDMARKER` 表示文件结束符。

类似的，第三行中，`NEWLINE*` 表示零个或多个换行符，`NEWLINE` 表示一个换行符, `*` 表示零个或多个。

第四行中，`(`、`)`、`'->'` 是符号，单引号`'`围起来的是关键字。

这样，我们可以解读上述规则：

`file` 是 Python 代码的入口规则，`interactive` 是交互式 Python 代码的入口规则，`eval` 是表达式的入口规则，`func_type` 是函数类型的规则。

`file` 规则由 `statements` 和 `ENDMARKER` 组成，`statements` 是一系列语句的集合，`ENDMARKER` 是文件结束符。

`interactive` 规则由 `statement_newline` 组成，`statement_newline` 是一个语句后面跟着一个换行符。

`eval` 规则由 `expressions`、`NEWLINE*` 和 `ENDMARKER` 组成，`expressions` 是一系列表达式的集合，`NEWLINE*` 是零个或多个换行符，`ENDMARKER` 是文件结束符。

`func_type` 规则由 `(`、`[type_expressions]`、`)`、`'->'`、`expression`、`NEWLINE*` 和 `ENDMARKER` 组成，`type_expressions` 是一系列类型表达式的集合，`expression` 是一个表达式。

这样，我们可以通过这些规则来解析 Python 代码的顶层结构。

那么 statements、expression、assignment 等规则又是什么呢？

### 语句（Statements）

语句是 Python 代码的基本构建块，它们用于执行特定的操作。Python 语句包括复合语句（compound statements）和简单语句（simple statements）。

复合语句由多个简单语句组成，通常使用缩进来表示语句的层次结构。例如，`if` 语句、`for` 语句、`while` 语句等都是复合语句。

简单语句是 Python 代码的基本单元，它包括赋值语句、表达式语句、`import` 语句、`return` 语句等。

```python
statements: statement+

statement: compound_stmt  | simple_stmts

statement_newline:
    | compound_stmt NEWLINE
    | simple_stmts
    | NEWLINE
    | ENDMARKER

simple_stmts:
    | simple_stmt !';' NEWLINE  # Not needed, there for speedup
    | ';'.simple_stmt+ [';'] NEWLINE

simple_stmt:
    | assignment
    | type_alias
    | star_expressions
    | return_stmt
    | import_stmt
    | raise_stmt
    | 'pass'
    | del_stmt
    | yield_stmt
    | assert_stmt
    | 'break'
    | 'continue'
    | global_stmt
    | nonlocal_stmt

compound_stmt:
    | function_def
    | if_stmt
    | class_def
    | with_stmt
    | for_stmt
    | try_stmt
    | while_stmt
    | match_stmt
```

如上所示，`statements` 是一系列语句的集合，`statement` 是一个复合语句或简单语句，`statement_newline` 是一个语句后面跟着一个换行符。其中，`|` 表示或, `+` 表示一个或多个，`*` 表示零个或多个，`!` 表示不包含。

`simple_stmts` 是一个简单语句或多个简单语句的集合，`simple_stmt` 是一个简单语句，包括赋值语句、类型别名、表达式、`return` 语句、`import` 语句、`raise` 语句、`pass` 语句、`del` 语句、`yield` 语句、`assert` 语句、`break` 语句、`continue` 语句、`global` 语句、`nonlocal` 语句。

`compound_stmt` 是一个复合语句，包括函数定义、`if` 语句、类定义、`with` 语句、`for` 语句、`try` 语句、`while` 语句、`match` 语句。

### 表达式（Expressions）

表达式是 Python 代码的基本构建块，它们用于计算值。Python 表达式包括赋值表达式、条件表达式、逻辑表达式、算术表达式、位运算表达式等。

```python
expressions:
    | expression (',' expression )+ [',']
    | expression ','
    | expression

expression:
    | disjunction 'if' disjunction 'else' expression
    | disjunction
    | lambdef

yield_expr:
    | 'yield' 'from' expression
    | 'yield' [star_expressions]

star_expressions:
    | star_expression (',' star_expression )+ [',']
    | star_expression ','
    | star_expression

star_expression:
    | '*' bitwise_or
    | expression

star_named_expressions: ','.star_named_expression+ [',']

star_named_expression:
    | '*' bitwise_or
    | named_expression

assignment_expression:
    | NAME ':=' ~ expression

named_expression:
    | assignment_expression
    | expression !':='

disjunction:
    | conjunction ('or' conjunction )+
    | conjunction

conjunction:
    | inversion ('and' inversion )+
    | inversion

inversion:
    | 'not' inversion
    | comparison
```

如上所示，`expressions` 是一系列表达式的集合，`expression` 是一个表达式，包括赋值表达式、条件表达式、逻辑表达式、算术表达式、位运算表达式等。其中，`.` 表示连接，`+` 表示一个或多个，`*` 表示零个或多个，`!` 表示不包含，`~` 表示断言。
