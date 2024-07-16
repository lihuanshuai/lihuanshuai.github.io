+++
title = 'Numpy 基础（一）：数组'
tags = ['Python', 'Numpy']
date = 2024-07-03T23:32:34+08:00
draft = false

[params]
math = true
+++

[numpy][] 是一个用于科学计算的基础包。它是一个提供多维数组对象、各种派生对象（如掩码数组和矩阵）以及一系列用于数组快速操作的例程的 Python 库，包括数学、逻辑、形状操作、排序、选择、I/O、离散傅立叶变换、基本线性代数、基本统计操作、随机模拟等等。

在 NumPy 包的核心是 ndarray 对象。它封装了 n 维同类数据类型的数组，许多操作都是为了性能而在编译代码中执行的。

[numpy]: https://numpy.org/doc/stable/user/whatisnumpy.html

## ndarray

NumPy 的主要对象是同类多维数组(`ndarray`)。它是一个元素（通常是数字）的表，所有元素都是相同类型的，由非负整数元组索引。在 NumPy 中，维度被称为轴(axes)。

```python
# 数组有 2 个轴。第一个轴的长度为 2，第二个轴的长度为 3。
[[1., 0., 0.],
 [0., 1., 2.]]
```

用数学术语来说，我们可以说这个数组是一个两行三列的矩阵。在 NumPy 中，每个数组的维度被称为轴。数组的轴数被称为秩。

$$
\begin{bmatrix}
    1 & 0 & 0 \\
    0 & 1 & 2 \\
\end{bmatrix}
$$

NumPy 的数组类被称为 `ndarray`。它也被别名 `array`。请注意，`numpy.array` 不同于标准 Python 库类 `array.array`，后者只处理一维数组并提供较少的功能。`ndarray` 对象的更重要的属性有：

- `ndarray.ndim` 数组的轴数（维度）。
- `ndarray.shape` 数组的维度。这是一个整数元组，指示每个维度的大小。对于一个有 n 行 m 列的矩阵，`shape` 将是 `(n,m)`。因此，`shape` 元组的长度是轴数，也就是 `ndim`。
- `ndarray.size` 数组的元素总数。这等于 `shape` 元素的乘积。
- `ndarray.dtype` 描述数组中元素类型的对象。可以使用标准 Python 类型创建或指定 dtype。此外，NumPy 提供了自己的类型。`numpy.int32`、`numpy.int16` 和 `numpy.float64` 是一些例子。
- `ndarray.itemsize` 数组中每个元素的字节大小。例如，类型为 `float64` 的元素数组的 `itemsize` 为 8（=64/8），而类型为 `complex32` 的元素数组的 `itemsize` 为 4（=32/8）。它等同于 `ndarray.dtype.itemsize`。
- `ndarray.data` 包含数组实际元素的缓冲区。通常，我们不需要使用此属性，因为我们将使用索引设施访问数组中的元素。

## 数组创建

我们可以使用 `array` 函数从常规 Python 列表或元组创建数组。

```python
import numpy as np

a = np.array([2,3,4])
print(a)
# [2 3 4]
```

`array` 将序列转换为数组，如果序列中的元素是序列，则将创建多维数组。

```python
b = np.array([(1.5,2,3), (4,5,6)])
print(b)
# [[1.5 2.  3. ]
#  [4.  5.  6. ]]
```

数组的类型也可以在创建时显式指定。

```python
c = np.array( [ [1,2], [3,4] ], dtype=complex )
print(c)
# [[1.+0.j 2.+0.j]
#  [3.+0.j 4.+0.j]]
```

通常，数组的元素是从列表中推断出来的。

## 打印数组

当你打印数组时，NumPy 以与嵌套列表类似的方式显示它，但具有以下布局：

- 最后一个轴从左到右打印，
- 次后一个轴从顶向下打印，
- 其余的也从顶向下打印，每个切片用空行分隔。

一维数组被打印为行，二维数组为矩阵，三维数组为矩阵列表。

## 基本操作

数组的算术运算是按元素的。新数组被创建并且被填充结果。

```python
a = np.array( [20,30,40,50] )
b = np.arange( 4 )
c = a-b
print(c)
# [20 29 38 47]
```

与许多矩阵语言不同，乘法运算符 `*` 在 NumPy 数组中按元素运算。矩阵乘积可以使用 `@` 运算符或 `dot` 函数或方法来计算。

```python
A = np.array( [[1,1],
               [0,1]] )
B = np.array( [[2,0],
               [3,4]] )
print(A * B)
# [[2 0]
#  [0 4]]
print(A @ B)
# [[5 4]
#  [3 4]]
print(A.dot(B))
# [[5 4]
#  [3 4]]
```

一些操作，例如 `+=` 和 `*=`，用于修改现有数组而不是创建新数组。

```python
a = np.ones((2,3), dtype=int)
b = np.random.random((2,3))
a *= 3
print(a)
# [[3 3 3]
#  [3 3 3]]
b += a
print(b)
# [[3.417022    3.72032449 3.00011437]
#  [3.30233257 3.14675589 3.09233859]]
```

当使用不同类型的数组进行操作时，结果数组的类型对应于更一般或更精确的数组（称为向上转换的行为）。

## 通用函数

NumPy 提供了常见的数学函数，如 sin、cos 和 exp。在 NumPy 中，这些被称为“通用函数”(`ufunc`)。在 NumPy 中，这些函数在数组上按元素操作，产生一个数组作为输出。

```python
B = np.arange(3)
print(np.exp(B))
# [1.         2.71828183 7.3890561 ]
print(np.sqrt(B))
# [0.         1.         1.41421356]
C = np.array([2., -1., 4.])
print(np.add(B, C))
# [2. 0. 6.]
```

## 索引、切片和迭代

一维数组可以像列表和其他 Python 序列一样进行索引、切片和迭代。

多维数组可以有每个轴的一个索引。这些索引以逗号分隔的元组给出：

当提供的索引少于轴数时，缺少的索引被视为完整切片`:`

```python
b[-1]   # 最后一行。等价于 b[-1, :]
```

`b[i]` 中括号内的表达式被视为 `i`，后面跟着足够多的 `:` 来表示剩余的轴。NumPy 还允许您使用点来写成 `b[i, ...]`。

**点**(`...`)表示需要产生完整索引元组所需的冒号数。例如，如果 `x` 是一个具有 5 个轴的数组，则

- `x[1, 2, ...]` 等价于 `x[1, 2, :, :, :]`，
- `x[..., 3]` 等价于 `x[:, :, :, :, 3]`，
- `x[4, ..., 5, :]` 等价于 `x[4, :, :, 5, :]`。

在多维数组上进行迭代是相对于第一个轴的：

但是，如果要对数组中的每个元素执行操作，可以使用 `flat` 属性，它是数组所有元素的迭代器：

## 形状操作

要更改数组的形状，您可以使用各种命令。请注意，以下三个命令都返回一个修改后的数组，但不更改原始数组。

```python
a = np.floor(10*np.random.random((3,4)))
print(a)
# [[2. 8. 0. 6.]
#  [4. 5. 1. 1.]
#  [8. 9. 3. 6.]]
print(a.shape)
# (3, 4)
print(a.ravel())
# [2. 8. 0. 6. 4. 5. 1. 1. 8. 9. 3. 6.]

a.shape = (6, 2)
print(a)
# [[2. 8.]
#  [0. 6.]
#  [4. 5.]
#  [1. 1.]
#  [8. 9.]
#  [3. 6.]]
print(a.T)
# [[2. 0. 4. 1. 8. 3.]
#  [8. 6. 5. 1. 9. 6.]]
print(a.reshape(3,4))
# [[2. 8. 0. 6.]
#  [4. 5. 1. 1.]
#  [8. 9. 3. 6.]]
print(a)
# [[2. 8.]
#  [0. 6.]
#  [4. 5.]
#  [1. 1.]
#  [8. 9.]
#  [3. 6.]]
```

`ravel` 函数返回数组的一个扁平副本。`reshape` 函数返回一个带有新形状的数组，而 `T` 函数返回数组的转置。

## 复制和视图

当操作数组时，有时会返回输入数据的副本，有时则不会。这通常是一个问题，因为它可能会导致内存问题。

```python
a = np.arange(12)
b = a
# a 和 b 是相同的数组对象
b is a
# True
b.shape = 3,4
print(a.shape)
# (3, 4)
```

Python 传递可变对象作为引用，因此函数调用不会复制。

```python
def f(x):
    print(id(x))
print(id(a))
# 1396543580040
f(a)
# 1396543580040
```

视图方法会返回一个新的数组对象，但与原始数组共享数据。

```python
c = a.view()
c is a
# False
c.base is a
# True
c.flags.owndata
# False
c.shape = 2,6
print(a.shape)
# (3, 4)
c[0,4] = 1234
print(a)
# [[   0    1    2 1234]
#  [   4    5    6    7]
#  [   8    9   10   11]]
```

切片数组返回一个视图。

```python
s = a[ : , 1:3]
s[:] = 10
print(a)
# [[   0   10   10 1234]
#  [   4   10   10    7]
#  [   8   10   10   11]]
```

## 高级索引和索引技巧

NumPy 提供比 Python 序列更多的索引功能。除了索引整数和切片，正如我们之前看到的，数组可以被整数数组和布尔数组索引。

### 整数数组索引

当我们使用切片索引数组时，结果始终是原始数组的子数组。整数数组索引允许我们使用另一个数组中的数据构造任意数组。

```python
a = np.arange(12)**2
i = np.array( [ 1,1,3,8,5 ] )
print(a[i])
# [ 1  1  9 64 25]
```

当使用多个索引数组时，结果是具有相同形状的数组，但是每个索引数组的元素对应于要选择的元素。

```python
j = np.array( [ [ 3, 4], [ 9, 7 ] ] )
print(a[j])
# [[ 9 16]
#  [81 49]]
```

我们还可以给出索引数组的多维数组，每个维度对应于要选择的元素。

```python
palette = np.array( [ [0,0,0],                # 黑色
                      [255,0,0],              # 红色
                      [0,255,0],              # 绿色
                      [0,0,255],              # 蓝色
                      [255,255,255] ] )       # 白色
image = np.array( [ [ 0, 1, 2, 0 ],           # 每个值作为颜色索引
                    [ 0, 3, 4, 0 ]  ] )
print(palette[image])
# [[[  0   0   0]
#  [255   0   0]
#  [  0 255   0]
#  [  0   0   0]]
#
#  [[  0   0   0]
#  [  0   0 255]
#  [255 255 255]
#  [  0   0   0]]]
```

我们还可以使用布尔数组索引。当索引数组的长度与被索引数组的长度不同时，将引发错误。

```python
a = np.arange(12).reshape(3,4)
b = a > 4
print(b)
# [[False False False False]
#  [False False  True  True]
#  [ True  True  True  True]]
print(a[b])
# [ 5  6  7  8  9 10 11]
```

这个属性在分配值时非常有用。

```python
a[b] = 0
print(a)
# [[ 0  1  2  3]
#  [ 4  0  0  0]
#  [ 0  0  0  0]]
```

## 迭代数组

迭代数组的最直接方法是使用 `for` 循环。然而，如果要对数组中的每个元素执行操作，可以使用 `flat` 属性，它是数组所有元素的迭代器。

```python
for element in a.flat:
    print(element)
```

## 参考

1. [NumPy quickstart — NumPy v1.21 Manual](https://numpy.org/doc/stable/user/quickstart.html)
2. [What is NumPy? — NumPy v1.21 Manual](https://numpy.org/doc/stable/user/whatisnumpy.html)
4. [NumPy: the absolute basics for beginners — NumPy v1.21 Manual](https://numpy.org/doc/stable/user/absolute_beginners.html)