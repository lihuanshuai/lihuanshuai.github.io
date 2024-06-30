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

那么 statements、expressions 等规则又是什么呢？

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

典型的赋值语句如下：

```python
assignment:
    | NAME ':' expression ['=' annotated_rhs ]
    | ('(' single_target ')'
         | single_subscript_attribute_target) ':' expression ['=' annotated_rhs ]
    | (star_targets '=' )+ (yield_expr | star_expressions) !'=' [TYPE_COMMENT]
    | single_target augassign ~ (yield_expr | star_expressions)

single_target:
    | NAME
    | '(' single_target ')'
    | single_subscript_attribute_target

single_subscript_attribute_target:
    | primary '[' subscript_list ']'
    | primary '.' NAME

subscript_list:
    | subscript (',' subscript)* [',']

subscript:
    | expression
    | [expression] ':' [expression] [sliceop]
    | [expression] ':' [expression] [sliceop] [sliceop]

sliceop:
    | ':' [expression]

augassign:
    | '+='
    | '-='
    | '*='
    | '@='
    | '/='
    | '%='
    | '&='
    | '|='
    | '^='
    | '<<='
    | '>>='
    | '**='
    | '//='

annotated_rhs:
    | expression ['=' annotated_rhs]

star_targets:
    | star_target (',' star_target)* [',']

star_target:
    | '*' single_target
    | '**' single_target

yield_expr:
    | 'yield' 'from' expression
    | 'yield' [star_expressions]

star_expressions:
    | star_expression (',' star_expression)* [',']

star_expression:
    | '*' bitwise_or
    | expression
```

如上所示，`assignment` 是一个赋值语句，包括变量名、表达式、注解等。`single_target` 是一个单目标，包括变量名、下标、属性等。`subscript_list` 是一个下标列表，包括下标和逗号。`subscript` 是一个下标，包括表达式、切片等。`sliceop` 是一个切片操作，包括冒号和表达式。`augassign` 是一个增量赋值操作，包括加等、减等、乘等、除等、取模等、按位与等、按位或等、按位异或等、左移等、右移等、幂等、整除等。`annotated_rhs` 是一个注解右值，包括表达式和等号。`star_targets` 是一个星号目标，包括星号目标和逗号。`star_target` 是一个星号目标，包括星号和单目标。`yield_expr` 是一个 yield 表达式，包括 yield from 表达式和 yield 表达式。`star_expressions` 是一个星号表达式，包括星号表达式和逗号。`star_expression` 是一个星号表达式，包括星号按位或表达式和表达式。

典型的类型别名如下：

```python
type_alias:
    | "type" NAME [type_params] '=' expression

type_params: '[' type_param_seq  ']'

type_param_seq: ','.type_param+ [',']

type_param:
    | NAME [type_param_bound]
    | '*' NAME
    | '**' NAME

type_param_bound: ':' expression
```

如上所示，`type_alias` 是一个类型别名，包括 type 关键字、变量名、类型参数、等号和表达式。`type_params` 是一个类型参数列表，包括左方括号、类型参数序列和右方括号。`type_param_seq` 是一个类型参数序列，包括类型参数和逗号。`type_param` 是一个类型参数，包括变量名、类型参数边界、星号变量名、双星号变量名。`type_param_bound` 是一个类型参数边界，包括冒号和表达式。

典型的 `return` 语句如下：

```python
return_stmt:
    | 'return' [star_expressions]
```

如上所示，`return_stmt` 是一个返回语句，包括 return 关键字和星号表达式。

典型的 `import` 语句如下：

```python
import_stmt:
    | import_name
    | import_from

import_name:
    | 'import' dotted_as_names

import_from:
    | 'from' relative_module 'import' ('*' | import_as_names | '(' import_as_names ')')

import_as_name:
    | NAME ['as' NAME]

dotted_as_name:
    | dotted_name ['as' NAME]

import_as_names:
    | import_as_name (',' import_as_name)* [',']

dotted_as_names:
    | dotted_as_name (',' dotted_as_name)*

relative_module:
    | '.'+ NAME

dotted_name:
    | NAME ('.' NAME)*
```

如上所示，`import_stmt` 是一个导入语句，包括 import_name 和 import_from。`import_name` 是一个导入名字，包括 import 关键字和点分隔名字。`import_from` 是一个从导入，包括 from 关键字、相对模块、import 关键字、星号、导入名字或导入名字列表。`import_as_name` 是一个导入名字，包括变量名和 as 变量名。`dotted_as_name` 是一个点分隔名字，包括点分隔名字和 as 变量名。`import_as_names` 是一个导入名字列表，包括导入名字和逗号。`dotted_as_names` 是一个点分隔名字列表，包括点分隔名字和逗号。`relative_module` 是一个相对模块，包括点号和变量名。`dotted_name` 是一个点分隔名字，包括变量名和点号。

典型的 `raise` 语句如下：

```python
raise_stmt:
    | 'raise' expression ['from' expression ]
    | 'raise'
```

如上所示，`raise_stmt` 是一个抛出异常语句，包括 raise 关键字、表达式和 from 表达式。

典型的 `del` 语句如下：

```python
del_stmt:
    | 'del' del_targets &(';' | NEWLINE)

del_targets: ','.del_target+ [',']

del_target:
    | t_primary '.' NAME !t_lookahead
    | t_primary '[' slices ']' !t_lookahead
    | del_t_atom

del_t_atom:
    | NAME
    | '(' del_target ')'
    | '(' [del_targets] ')'
    | '[' [del_targets] ']'
```

如上所示，`del_stmt` 是一个删除语句，包括 del 关键字和删除目标。`del_targets` 是一个删除目标列表，包括删除目标和逗号。`del_target` 是一个删除目标，包括主要目标、点号变量名、下标切片等。`del_t_atom` 是一个删除原子，包括变量名、括号删除目标、括号删除目标列表、方括号删除目标列表。

典型的 `yield` 语句如下：

```python
yield_stmt: yield_expr

yield_expr:
    | 'yield' 'from' expression
    | 'yield' [star_expressions]
```

如上所示，`yield_stmt` 是一个 yield 语句，包括 yield 表达式。

典型的 `assert` 语句如下：

```python
assert_stmt: 'assert' expression [',' expression ]
```

如上所示，`assert_stmt` 是一个断言语句，包括 assert 关键字、表达式和逗号表达式。

典型的 `global` 语句如下：

```python
global_stmt: global_stmt: 'global' ','.NAME+
```

如上所示，`global_stmt` 是一个全局语句，包括 global 关键字和变量名列表。

典型的 `nonlocal` 语句如下：

```python
nonlocal_stmt: 'nonlocal' ','.NAME+
```

如上所示，`nonlocal_stmt` 是一个非局部语句，包括 nonlocal 关键字和变量名列表。

典型的函数定义如下：

```python
function_def:
    | decorators function_def_raw
    | function_def_raw

function_def_raw:
    | 'def' NAME [type_params] '(' [params] ')' ['->' expression ] ':' [func_type_comment] block
    | ASYNC 'def' NAME [type_params] '(' [params] ')' ['->' expression ] ':' [func_type_comment] block

params:
    | parameters

parameters:
    | slash_no_default param_no_default* param_with_default* [star_etc]
    | slash_with_default param_with_default* [star_etc]
    | param_no_default+ param_with_default* [star_etc]
    | param_with_default+ [star_etc]
    | star_etc

slash_no_default:
    | param_no_default+ '/' ','
    | param_no_default+ '/' &')'
slash_with_default:
    | param_no_default* param_with_default+ '/' ','
    | param_no_default* param_with_default+ '/' &')'

star_etc:
    | '*' param_no_default param_maybe_default* [kwds]
    | '*' param_no_default_star_annotation param_maybe_default* [kwds]
    | '*' ',' param_maybe_default+ [kwds]
    | kwds

kwds:
    | '**' param_no_default

param_no_default:
    | param ',' TYPE_COMMENT?
    | param TYPE_COMMENT? &')'
param_no_default_star_annotation:
    | param_star_annotation ',' TYPE_COMMENT?
    | param_star_annotation TYPE_COMMENT? &')'
param_with_default:
    | param default ',' TYPE_COMMENT?
    | param default TYPE_COMMENT? &')'
param_maybe_default:
    | param default? ',' TYPE_COMMENT?
    | param default? TYPE_COMMENT? &')'
param: NAME annotation?
param_star_annotation: NAME star_annotation
annotation: ':' expression
star_annotation: ':' star_expression
default: '=' expression  | invalid_default

invalid_default:
    | '=' '...'
    | '=' 'None'

block:
    | NEWLINE INDENT statements DEDENT
    | simple_stmts

decorators: ('@' named_expression NEWLINE )+

func_type_comment:
    | NEWLINE TYPE_COMMENT &(NEWLINE INDENT)   # Must be followed by indented block
    | TYPE_COMMENT
```

如上所示，`function_def` 是一个函数定义，包括装饰器、函数定义原始。`function_def_raw` 是一个函数定义原始，包括 def 关键字、函数名、类型参数、左括号、参数、右括号、箭头表达式、冒号、函数类型注释和块。`params` 是一个参数，包括参数。`parameters` 是一个参数列表，包括无默认斜杠参数、无默认参数、有默认参数、星号等。`slash_no_default` 是一个无默认斜杠，包括无默认参数、斜杠、逗号。`slash_with_default` 是一个有默认斜杠，包括无默认参数、有默认参数、斜杠、逗号。`star_etc` 是一个星号等，包括星号无默认参数、星号无默认星号注释、星号逗号有默认参数、星号有默认参数、星号等。`kwds` 是一个关键字参数，包括双星号无默认参数。`param_no_default` 是一个无默认参数，包括参数、逗号类型注释、参数类型注释右括号。`param_no_default_star_annotation` 是一个无默认星号注释，包括参数星号注释、逗号类型注释、参数类型注释右括号。`param_with_default` 是一个有默认参数，包括参数默认值、逗号类型注释、参数类型注释右括号。`param_maybe_default` 是一个可能有默认参数，包括参数默认值、逗号类型注释、参数类型注释右括号。`param` 是一个参数，包括变量名和注释。`param_star_annotation` 是一个星号注释参数，包括变量名星号注释、逗号类型注释、参数类型注释右括号。`annotation` 是一个注释，包括冒号表达式。`star_annotation` 是一个星号注释，包括冒号星号表达式。`default` 是一个默认值，包括等号表达式和无效默认值。`invalid_default` 是一个无效默认值，包括等号省略号、等号 None。`block` 是一个块，包括换行缩进语句缩进、简单语句。`decorators` 是一个装饰器，包括装饰器表达式换行。

典型的 `if` 语句如下：

```python
if_stmt:
    | 'if' named_expression ':' block elif_stmt
    | 'if' named_expression ':' block [else_block]
elif_stmt:
    | 'elif' named_expression ':' block elif_stmt
    | 'elif' named_expression ':' block [else_block]
else_block:
    | 'else' ':' block
```

如上所示，`if_stmt` 是一个 if 语句，包括 if 表达式、冒号块、elif 语句。`elif_stmt` 是一个 elif 语句，包括 elif 表达式、冒号块、elif 语句。`else_block` 是一个 else 块，包括 else 冒号块。

典型的类定义如下：

```python
class_def:
    | decorators class_def_raw
    | class_def_raw

class_def_raw:
    | 'class' NAME [type_params] ['(' [arguments] ')' ] ':' block

arguments:
    | args [','] &')'

args:
    | ','.(starred_expression | ( assignment_expression | expression !':=') !'=')+ [',' kwargs ]
    | kwargs

kwargs:
    | ','.kwarg_or_starred+ ',' ','.kwarg_or_double_starred+
    | ','.kwarg_or_starred+
    | ','.kwarg_or_double_starred+

starred_expression:
    | '*' expression

kwarg_or_starred:
    | NAME '=' expression
    | starred_expression

kwarg_or_double_starred:
    | NAME '=' expression
    | '**' expression
```

如上所示，`class_def` 是一个类定义，包括装饰器、类定义原始。`class_def_raw` 是一个类定义原始，包括 class 关键字、类名、类型参数、左括号、参数、右括号、冒号、块。`arguments` 是一个参数，包括参数、逗号右括号。`args` 是一个参数列表，包括逗号星号表达式、赋值表达式或表达式等、逗号关键字参数。`kwargs` 是一个关键字参数，包括逗号关键字参数。`starred_expression` 是一个星号表达式，包括星号表达式。

典型的 `with` 语句如下：

```python
with_stmt:
    | 'with' '(' ','.with_item+ ','? ')' ':' block
    | 'with' ','.with_item+ ':' [TYPE_COMMENT] block
    | ASYNC 'with' '(' ','.with_item+ ','? ')' ':' block
    | ASYNC 'with' ','.with_item+ ':' [TYPE_COMMENT] block

with_item:
    | expression 'as' star_target &(',' | ')' | ':')
    | expression
```

如上所示，`with_stmt` 是一个 with 语句，包括普通 with 语句、异步 with 语句。`with_item` 是一个 with 项，包括表达式 as 星号目标逗号或右括号或冒号、表达式。

典型的 `for` 语句如下：

```python
for_stmt:
    | 'for' star_targets 'in' ~ star_expressions ':' [TYPE_COMMENT] block [else_block]
    | ASYNC 'for' star_targets 'in' ~ star_expressions ':' [TYPE_COMMENT] block [else_block]
```

如上所示，`for_stmt` 是一个 for 语句，包括普通 for 语句、异步 for 语句。

典型的 `try` 语句如下：

```python
try_stmt:
    | 'try' ':' block finally_block
    | 'try' ':' block except_block+ [else_block] [finally_block]
    | 'try' ':' block except_star_block+ [else_block] [finally_block]

finally_block:
    | 'finally' ':' block

except_block:
    | 'except' [expression ['as' NAME]] ':' block

except_star_block:
    | 'except' [expression ['as' NAME]] ':' block

else_block:
    | 'else' ':' block
```

如上所示，`try_stmt` 是一个 try 语句，包括 try 块、finally 块、except 块、else 块。

典型的 `while` 语句如下：

```python
while_stmt:
    | 'while' named_expression ':' block [else_block]
```

如上所示，`while_stmt` 是一个 while 语句，包括 while 表达式、冒号块、else 块。

典型的 `match` 语句如下：

```python
match_stmt:
    | "match" subject_expr ':' NEWLINE INDENT case_block+ DEDENT

subject_expr:
    | star_named_expression ',' star_named_expressions?
    | named_expression

case_block:
    | "case" patterns guard? ':' block

guard: 'if' named_expression

patterns:
    | open_sequence_pattern
    | pattern

pattern:
    | as_pattern
    | or_pattern

as_pattern:
    | or_pattern 'as' pattern_capture_target

or_pattern:
    | '|'.closed_pattern+

closed_pattern:
    | literal_pattern
    | capture_pattern
    | wildcard_pattern
    | value_pattern
    | group_pattern
    | sequence_pattern
    | mapping_pattern
    | class_pattern

# Literal patterns are used for equality and identity constraints
literal_pattern:
    | signed_number !('+' | '-')
    | complex_number
    | strings
    | 'None'
    | 'True'
    | 'False'

# Literal expressions are used to restrict permitted mapping pattern keys
literal_expr:
    | signed_number !('+' | '-')
    | complex_number
    | strings
    | 'None'
    | 'True'
    | 'False'

complex_number:
    | signed_real_number '+' imaginary_number
    | signed_real_number '-' imaginary_number

signed_number:
    | NUMBER
    | '-' NUMBER

signed_real_number:
    | real_number
    | '-' real_number

real_number:
    | NUMBER

imaginary_number:
    | NUMBER

capture_pattern:
    | pattern_capture_target

pattern_capture_target:
    | !"_" NAME !('.' | '(' | '=')

wildcard_pattern:
    | "_"

value_pattern:
    | attr !('.' | '(' | '=')

attr:
    | name_or_attr '.' NAME

name_or_attr:
    | attr
    | NAME

group_pattern:
    | '(' pattern ')'

sequence_pattern:
    | '[' maybe_sequence_pattern? ']'
    | '(' open_sequence_pattern? ')'

open_sequence_pattern:
    | maybe_star_pattern ',' maybe_sequence_pattern?

maybe_sequence_pattern:
    | ','.maybe_star_pattern+ ','?

maybe_star_pattern:
    | star_pattern
    | pattern

star_pattern:
    | '*' pattern_capture_target
    | '*' wildcard_pattern

mapping_pattern:
    | '{' '}'
    | '{' double_star_pattern ','? '}'
    | '{' items_pattern ',' double_star_pattern ','? '}'
    | '{' items_pattern ','? '}'

items_pattern:
    | ','.key_value_pattern+

key_value_pattern:
    | (literal_expr | attr) ':' pattern

double_star_pattern:
    | '**' pattern_capture_target

class_pattern:
    | name_or_attr '(' ')'
    | name_or_attr '(' positional_patterns ','? ')'
    | name_or_attr '(' keyword_patterns ','? ')'
    | name_or_attr '(' positional_patterns ',' keyword_patterns ','? ')'

positional_patterns:
    | ','.pattern+

keyword_patterns:
    | ','.keyword_pattern+

keyword_pattern:
    | NAME '=' pattern
```

如上所示，`match_stmt` 是一个匹配语句，包括匹配主题表达式、冒号换行缩进情况块缩进。`subject_expr` 是一个主题表达式，包括星号命名表达式逗号星号表达式或命名表达式。`case_block` 是一个情况块，包括情况模式保护冒号块。`guard` 是一个保护，包括 if 命名表达式。`patterns` 是一个模式，包括开放序列模式或模式。`pattern` 是一个模式，包括 as 模式或或模式。`as_pattern` 是一个 as 模式，包括或模式 as 模式捕获目标。`or_pattern` 是一个或模式，包括或闭合模式。`closed_pattern` 是一个闭合模式，包括文字模式、捕获模式、通配符模式、值模式、组模式、序列模式、映射模式、类模式。`literal_pattern` 是一个文字模式，用于相等性和标识性约束。`literal_expr` 是一个文字表达式，用于限制允许的映射模式键。`complex_number` 是一个复数，包括有符号实数加虚数或有符号实数减虚数。`signed_number` 是一个有符号数，包括数字或减号数字。`signed_real_number` 是一个有符号实数，包括实数或减号实数。`real_number` 是一个实数，包括数字。`imaginary_number` 是一个虚数，包括数字。`capture_pattern` 是一个捕获模式，包括模式捕获目标。`pattern_capture_target` 是一个模式捕获目标，不是下划线变量名不是点号左括号等。`wildcard_pattern` 是一个通配符模式，包括下划线。`value_pattern` 是一个值模式，包括属性不是点号左括号等。`group_pattern` 是一个组模式，包括模式。`sequence_pattern` 是一个序列模式。`open_sequence_pattern` 是一个开放序列模式。`maybe_sequence_pattern` 是一个可能序列模式。`maybe_star_pattern` 是一个可能星号模式。`star_pattern` 是一个星号模式。`mapping_pattern` 是一个映射模式。`items_pattern` 是一个项模式。`key_value_pattern` 是一个键值模式。`double_star_pattern` 是一个双星号模式。`class_pattern` 是一个类模式。

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

其中一些典型的表达式如下：

```python
# Comparison operators
# --------------------

comparison:
    | bitwise_or compare_op_bitwise_or_pair+
    | bitwise_or

compare_op_bitwise_or_pair:
    | eq_bitwise_or
    | noteq_bitwise_or
    | lte_bitwise_or
    | lt_bitwise_or
    | gte_bitwise_or
    | gt_bitwise_or
    | notin_bitwise_or
    | in_bitwise_or
    | isnot_bitwise_or
    | is_bitwise_or

eq_bitwise_or: '==' bitwise_or
noteq_bitwise_or:
    | ('!=' ) bitwise_or
lte_bitwise_or: '<=' bitwise_or
lt_bitwise_or: '<' bitwise_or
gte_bitwise_or: '>=' bitwise_or
gt_bitwise_or: '>' bitwise_or
notin_bitwise_or: 'not' 'in' bitwise_or
in_bitwise_or: 'in' bitwise_or
isnot_bitwise_or: 'is' 'not' bitwise_or
is_bitwise_or: 'is' bitwise_or

# Bitwise operators
# -----------------

bitwise_or:
    | bitwise_or '|' bitwise_xor
    | bitwise_xor

bitwise_xor:
    | bitwise_xor '^' bitwise_and
    | bitwise_and

bitwise_and:
    | bitwise_and '&' shift_expr
    | shift_expr

shift_expr:
    | shift_expr '<<' sum
    | shift_expr '>>' sum
    | sum

# Arithmetic operators
# --------------------

sum:
    | sum '+' term
    | sum '-' term
    | term

term:
    | term '*' factor
    | term '/' factor
    | term '//' factor
    | term '%' factor
    | term '@' factor
    | factor

factor:
    | '+' factor
    | '-' factor
    | '~' factor
    | power

power:
    | await_primary '**' factor
    | await_primary

# Primary elements
# ----------------

# Primary elements are things like "obj.something.something", "obj[something]", "obj(something)", "obj" ...

await_primary:
    | AWAIT primary
    | primary

primary:
    | primary '.' NAME
    | primary genexp
    | primary '(' [arguments] ')'
    | primary '[' slices ']'
    | atom

slices:
    | slice !','
    | ','.(slice | starred_expression)+ [',']

slice:
    | [expression] ':' [expression] [':' [expression] ]
    | named_expression

atom:
    | NAME
    | 'True'
    | 'False'
    | 'None'
    | strings
    | NUMBER
    | (tuple | group | genexp)
    | (list | listcomp)
    | (dict | set | dictcomp | setcomp)
    | '...'

group:
    | '(' (yield_expr | named_expression) ')'

# Lambda functions
# ----------------

lambdef:
    | 'lambda' [lambda_params] ':' expression

lambda_params:
    | lambda_parameters

# lambda_parameters etc. duplicates parameters but without annotations
# or type comments, and if there's no comma after a parameter, we expect
# a colon, not a close parenthesis.  (For more, see parameters above.)
#
lambda_parameters:
    | lambda_slash_no_default lambda_param_no_default* lambda_param_with_default* [lambda_star_etc]
    | lambda_slash_with_default lambda_param_with_default* [lambda_star_etc]
    | lambda_param_no_default+ lambda_param_with_default* [lambda_star_etc]
    | lambda_param_with_default+ [lambda_star_etc]
    | lambda_star_etc

lambda_slash_no_default:
    | lambda_param_no_default+ '/' ','
    | lambda_param_no_default+ '/' &':'

lambda_slash_with_default:
    | lambda_param_no_default* lambda_param_with_default+ '/' ','
    | lambda_param_no_default* lambda_param_with_default+ '/' &':'

lambda_star_etc:
    | '*' lambda_param_no_default lambda_param_maybe_default* [lambda_kwds]
    | '*' ',' lambda_param_maybe_default+ [lambda_kwds]
    | lambda_kwds

lambda_kwds:
    | '**' lambda_param_no_default

lambda_param_no_default:
    | lambda_param ','
    | lambda_param &':'
lambda_param_with_default:
    | lambda_param default ','
    | lambda_param default &':'
lambda_param_maybe_default:
    | lambda_param default? ','
    | lambda_param default? &':'
lambda_param: NAME

# LITERALS
# ========

fstring_middle:
    | fstring_replacement_field
    | FSTRING_MIDDLE
fstring_replacement_field:
    | '{' (yield_expr | star_expressions) '='? [fstring_conversion] [fstring_full_format_spec] '}'
fstring_conversion:
    | "!" NAME
fstring_full_format_spec:
    | ':' fstring_format_spec*
fstring_format_spec:
    | FSTRING_MIDDLE
    | fstring_replacement_field
fstring:
    | FSTRING_START fstring_middle* FSTRING_END

string: STRING
strings: (fstring|string)+

list:
    | '[' [star_named_expressions] ']'

tuple:
    | '(' [star_named_expression ',' [star_named_expressions]  ] ')'

set: '{' star_named_expressions '}'

# Dicts
# -----

dict:
    | '{' [double_starred_kvpairs] '}'

double_starred_kvpairs: ','.double_starred_kvpair+ [',']

double_starred_kvpair:
    | '**' bitwise_or
    | kvpair

kvpair: expression ':' expression

# Comprehensions & Generators
# ---------------------------

for_if_clauses:
    | for_if_clause+

for_if_clause:
    | ASYNC 'for' star_targets 'in' ~ disjunction ('if' disjunction )*
    | 'for' star_targets 'in' ~ disjunction ('if' disjunction )*

listcomp:
    | '[' named_expression for_if_clauses ']'

setcomp:
    | '{' named_expression for_if_clauses '}'

genexp:
    | '(' ( assignment_expression | expression !':=') for_if_clauses ')'

dictcomp:
    | '{' kvpair for_if_clauses '}'
```

如上所示，`comparison` 是一个比较运算符，包括按位或比较运算符对、按位或。`compare_op_bitwise_or_pair` 是一个比较运算符对，包括等于按位或、不等于按位或、小于等于按位或、小于按位或、大于等于按位或、大于按位或、不在按位或、在按位或、不是不在按位或、是按位或。`bitwise_or` 是一个按位或运算符，包括按位或按位异或。`bitwise_xor` 是一个按位异或运算符，包括按位异或按位与。`bitwise_and` 是一个按位与运算符，包括按位与移位表达式。`shift_expr` 是一个移位表达式，包括移位表达式移位和。`sum` 是一个求和表达式，包括求和项。`term` 是一个项，包括项因子。`factor` 是一个因子，包括加因子减因子取反因子幂。`power` 是一个幂，包括等待主要幂因子。`await_primary` 是一个等待主要，包括等待主要主要。`primary` 是一个主要，包括主要点号变量名、主要生成表达式、主要参数、主要下标切片、原子。`slices` 是一个切片，包括切片不逗号、逗号切片星号表达式逗号。`slice` 是一个切片，包括表达式冒号表达式冒号表达式或命名表达式。`atom` 是一个原子，包括变量名、True、False、None、字符串、数字、元组、组、生成表达式、列表、列表推导、字典、集合、字典推导、集合推导、省略号。`group` 是一个组，包括左括号 yield 表达式或命名表达式右括号。`lambdef` 是一个 lambda 函数。

### 注释（Comments）

注释是 Python 代码的一种注释形式，用于解释代码的含义。Python 注释包括行注释和块注释。

```python
comments:
    | comment+

comment:
    | LINE_COMMENT
    | BLOCK_COMMENT
```

如上所示，`comments` 是一系列注释的集合，`comment` 是一个注释，包括行注释和块注释。

## 参考资料

1. [10. Full Grammar specification](https://docs.python.org/3/reference/grammar.html)
