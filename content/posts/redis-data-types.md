+++
title = 'Redis 数据类型'
date = 2024-07-02T02:14:04+08:00
tags = ['Redis', '数据类型']
draft = false
+++

Redis 是一个开源的内存数据库，它支持多种数据类型，包括字符串、列表、集合、有序集合和哈希表。这些数据类型可以满足不同的需求，比如缓存、计数、排序等。本文将介绍 Redis 的常用数据类型及其用法。

## 字符串

字符串是 Redis 最基本的数据类型，它可以存储任意类型的数据，比如文本、数字等。字符串的最大长度是 512MB。

### 设置字符串

如果要设置字符串，可以使用 `SET` 命令：

```bash
SET key value
```

例如：

```bash
SET name "Alice"
```

如果键已经存在，`SET` 命令会覆盖原来的值。

值可以是任意类型的数据，比如文本、二进制数据等。值的大小不能超过 512MB。

`SET` 命令还有一些选项，可以用作附加参数。比较常用的选项有：`EX`、`PX`、`NX` 和 `XX`，它们分别表示设置过期时间、设置过期时间（毫秒）、仅在键不存在时设置值和仅在键存在时设置值。

例如，要设置一个过期时间为 60 秒的字符串，可以使用 `SET` 命令的 `EX` 选项：

```bash
SET key value EX 60
```

`NX` 选项可以用 `SETNX` 命令代替：

```bash
SETNX key value
```

### 获取字符串

如果要获取字符串，可以使用 `GET` 命令：

```bash
GET key
```

如果键不存在，`GET` 命令会返回 `nil`。

实践中，我们会有更复杂的获取字符串的需求。比如，设置一个字符串并返回旧值，可以使用 `GETSET` 命令：

```bash
GETSET key value
```

更特别的，我们可能会用到 `INCR` 命令，它会将键的值增加 1：

```bash
INCR key
```

如果键不存在，`INCR` 命令会将键的值设置为 `1`。

修改的过程是原子的，即使多个客户端同时对同一个键执行 `INCR` 命令，也不会出现竞争条件。例如，不会出现客户端 A 读取到 "60"，客户端 B 读取到 "60"，然后同时增加到 "61"。这个过程的最终值总是 "62"，也就是读-增加-设置操作是在其他客户端不执行命令的情况下执行的。

类似的命令还有 `DECR`、`INCRBY` 和 `DECRBY`。

此外，我们还可能需要批量获取字符串，可以使用 `MGET` 命令：

```bash
MGET key1 key2 ...
```

`MGET` 命令会返回多个键的值。

如果要设置多个字符串的值，可以使用 `MSET` 命令：

```bash
MSET key1 value1 key2 value2 ...
```

`MSET` 命令会设置多个键的值。

### 删除字符串

如果要删除字符串，可以使用 `DEL` 命令：

```bash
DEL key
```

如果键不存在，`DEL` 命令会返回 `0`，否则返回 `1`。

其他的命令还有 `GETDEL` 等。

## 列表

列表是一个有序的字符串列表，它可以存储多个值。列表的最大长度是 2^32 - 1。

### 添加元素

要向列表中添加元素，可以使用 `LPUSH` 或 `RPUSH` 命令：

```bash
LPUSH key value1 value2 ...
RPUSH key value1 value2 ...
```

`LPUSH` 命令会将值插入到列表的头部，`RPUSH` 命令会将值插入到列表的尾部。

如果键不存在，`LPUSH` 或 `RPUSH` 命令会创建一个新的列表。

### 获取元素

要获取列表中的元素，可以使用 `LRANGE` 命令：

```bash
LRANGE key start stop
```

如果 `start` 和 `stop` 都是正数，`LRANGE` 命令会返回列表中从 `start` 到 `stop` 之间的元素。如果 `start` 是负数，表示从列表的尾部开始计数，如果 `stop` 是负数，表示从列表的尾部开始计数。这些规则基本上和 Python 的切片操作一样。

如果 `start` 大于 `stop`，`LRANGE` 命令会返回一个空列表。

如果键不存在，`LRANGE` 命令会返回一个空列表。

要获取列表中的第一个元素，可以使用 `LINDEX` 命令：

```bash
LINDEX key index
```

`LINDEX` 命令会返回列表中指定索引的元素。

如果键不存在，`LINDEX` 命令会返回 `nil`。

要获取列表的长度，可以使用 `LLEN` 命令：

```bash
LLEN key
```

`LLEN` 命令会返回列表的长度。

如果键不存在，`LLEN` 命令会返回 `0`。

### 删除元素

如果要删除列表中的元素，可以使用 `LPOP` 或 `RPOP` 命令：

```bash
LPOP key
RPOP key
```

`LPOP` 命令会删除并返回列表的第一个元素，`RPOP` 命令会删除并返回列表的最后一个元素。

如果键不存在，`LPOP` 或 `RPOP` 命令会返回 `nil`。

如果要移动元素，可以使用 `LMOVE` 命令：

```bash
LMOVE source destination LEFT|RIGHT LEFT|RIGHT
```

`LMOVE` 命令会将列表 `source` 中的第一个元素移动到列表 `destination` 中，并返回移动的元素。

如果 `source` 和 `destination` 是同一个列表，`LMOVE` 命令会将元素从列表的头部移动到尾部，或者从尾部移动到头部。

如果 `source` 不存在，`LMOVE` 命令会返回 `nil`。

如果 `destination` 不存在，`LMOVE` 命令会创建一个新的列表。

这个命令可以用来实现循环队列等功能。

如果只需要保留列表的一部分元素，可以使用 `LTRIM` 命令：

```bash
LTRIM key start stop
```

`LTRIM` 命令会保留列表中从 `start` 到 `stop` 之间的元素，其他元素会被删除。

## 集合

集合是一个无序的字符串集合，它可以存储多个值。集合的最大长度是 2^32 - 1。

### 添加元素

如果要向集合中添加元素，可以使用 `SADD` 命令：

```bash
SADD key member1 member2 ...
```

如果键不存在，`SADD` 命令会创建一个新的集合。

### 获取元素

如果要获取集合中的元素，可以使用 `SMEMBERS` 命令：

```bash
SMEMBERS key
```

`SMEMBERS` 命令会返回集合中的所有元素。

如果键不存在，`SMEMBERS` 命令会返回一个空集合。

当然，这个命令不是很实用，因为如果集合中的元素很多，返回的结果可能会很大，对网络和内存的开销也会很大。

替代的方法是使用 `SISMEMBER` 命令：

```bash
SISMEMBER key member
```

`SISMEMBER` 命令会检查集合中是否存在指定的元素。

如果键不存在，`SISMEMBER` 命令会返回 `0`。

如果只需要获取集合的元素个数，可以使用 `SCARD` 命令：

```bash
SCARD key
```

`SCARD` 命令会返回集合的元素个数。

如果键不存在，`SCARD` 命令会返回 `0`。

如果要获取集合的交集、并集或差集，可以使用 `SINTER`、`SUNION` 和 `SDIFF` 命令：

```bash
SINTER key1 key2 ...
SUNION key1 key2 ...
SDIFF key1 key2 ...
```

`SINTER` 命令会返回多个集合的交集，`SUNION` 命令会返回多个集合的并集，`SDIFF` 命令会返回多个集合的差集。

如果键不存在，`SINTER`、`SUNION` 和 `SDIFF` 命令会返回一个空集合。

### 删除元素

要删除集合中的元素，可以使用 `SREM` 命令：

```bash
SREM key member1 member2 ...
```

`SREM` 命令会删除集合中的指定元素。

如果键不存在，`SREM` 命令会返回 `0`，否则返回删除的元素个数。

## 有序集合

有序集合是一个有序的字符串集合，它可以存储多个值，并且每个值都有一个分数（浮点数）。有序集合的最大长度是 2^32 - 1。

这里的有序集合是按照分数排序的，如果两个元素的分数相同，那么按照字典序排序。

### 添加元素

要向有序集合中添加元素，可以使用 `ZADD` 命令：

```bash
ZADD key score1 member1 score2 member2 ...
```

如果键不存在，`ZADD` 命令会创建一个新的有序集合。

### 获取元素

要获取有序集合中的元素，可以使用 `ZRANGE` 命令：

```bash
ZRANGE key start stop
```

`ZRANGE` 命令会返回有序集合中从 `start` 到 `stop` 之间的元素。

如果键不存在，`ZRANGE` 命令会返回一个空的有序集合。

可以使用 `WITHSCORES` 参数来返回分数：

```bash
ZRANGE key start stop WITHSCORES
```

`ZRANGE` 命令会返回元素分数是从小到大排序的。如果要返回元素分数是从大到小排序的，可以使用 `ZREVRANGE` 命令。

如果需要按照分数范围获取元素，可以使用 `ZRANGEBYSCORE` 命令：

```bash
ZRANGEBYSCORE key min max
```

`ZRANGEBYSCORE` 命令会返回有序集合中分数在 `min` 和 `max` 之间的元素。

如果键不存在，`ZRANGEBYSCORE` 命令会返回一个空的有序集合。

可以使用一些特殊数值，比如 `-inf` 和 `+inf`，分别表示负无穷和正无穷。

如果要获取有序集合中的某个元素的分数，可以使用 `ZSCORE` 命令：

```bash
ZSCORE key member
```

`ZSCORE` 命令会返回有序集合中指定元素的分数。

如果键不存在，`ZSCORE` 命令会返回 `nil`。

如果要获取有序集合的排名，可以使用 `ZRANK` 命令：

```bash
ZRANK key member
```

`ZRANK` 命令会返回有序集合中指定元素的排名。

如果键不存在，`ZRANK` 命令会返回 `nil`。

如果只需要获取有序集合的元素个数，可以使用 `ZCARD` 命令：

```bash
ZCARD key
```

`ZCARD` 命令会返回有序集合的元素个数。

如果键不存在，`ZCARD` 命令会返回 `0`。

### 删除元素

如果要删除有序集合中的元素，可以使用 `ZREM` 命令：

```bash
ZREM key member1 member2 ...
```

`ZREM` 命令会删除有序集合中的指定元素。

如果键不存在，`ZREM` 命令会返回 `0`，否则返回 `1`。

如果要按照分数范围删除元素，可以使用 `ZREMRANGEBYSCORE` 命令：

```bash
ZREMRANGEBYSCORE key min max
```

`ZREMRANGEBYSCORE` 命令会删除有序集合中分数在 `min` 和 `max` 之间的元素。

如果键不存在，`ZREMRANGEBYSCORE` 命令会返回 `0`。

## 哈希表

哈希表是一个键值对集合，它可以存储多个字段和值。哈希表的最大长度是 2^32 - 1。

### 设置字段

要设置哈希表中的字段，可以使用 `HSET` 命令：

```bash
HSET key field1 value1 field2 value2 ...
```

如果键不存在，`HSET` 命令会创建一个新的哈希表。

`INCRBY` 命令也有一个对应的 `HINCRBY` 命令，可以用来增加哈希表中的字段值：

```bash
HINCRBY key field increment
```

`HINCRBY` 命令会将哈希表中指定字段的值增加 `increment`。

如果键不存在，`HINCRBY` 命令会创建一个新的哈希表。

### 获取字段

如果要获取哈希表中的字段，可以使用 `HGET` 命令：

```bash
HGET key field
```

`HGET` 命令会返回哈希表中指定字段的值。

如果键不存在，`HGET` 命令会返回 `nil`。

如果要获取哈希表中的多个字段，可以使用 `HMGET` 命令：

```bash
HMGET key field1 field2 ...
```

`HMGET` 命令会返回哈希表中指定字段的值，以数组的形式返回。

如果键不存在，`HMGET` 命令会返回一个空数组。

如果要获取哈希表中的所有字段，可以使用 `HGETALL` 命令：

```bash
HGETALL key
```

`HGETALL` 命令会返回哈希表中的所有字段和值。

如果键不存在，`HGETALL` 命令会返回一个空数组。

如果只需要获取哈希表中的字段个数，可以使用 `HLEN` 命令：

```bash
HLEN key
```

`HLEN` 命令会返回哈希表中的字段个数。

如果键不存在，`HLEN` 命令会返回 `0`。

### 删除字段

如果要删除哈希表中的字段，可以使用 `HDEL` 命令：

```bash
HDEL key field1 field2 ...
```

`HDEL` 命令会删除哈希表中的指定字段。

如果键不存在，`HDEL` 命令会返回 `0`，否则返回删除的字段个数。

如果要删除哈希表中的所有字段，可以使用 `HDEL` 命令：

```bash
HDEL key *
```

`HDEL` 命令会删除哈希表中的所有字段。

如果键不存在，`HDEL` 命令会返回 `0`。

## 其他

除了上面介绍的数据类型，Redis 还支持一些其他数据类型，比如位图、地理位置、流、JSON 等。这些数据类型可以满足更多的需求，比如统计、地理位置、消息队列等。

如果要使用这些数据类型，可以查看 Redis 的官方文档，了解更多的用法。

## 总结

Redis 支持多种数据类型，包括字符串、列表、集合、有序集合和哈希表。这些数据类型可以满足不同的需求，比如缓存、计数、排序等。在使用 Redis 时，我们需要根据实际需求选择合适的数据类型，并了解它们的用法。

## 参考资料

1. [Redis 数据类型](https://redis.io/topics/data-types)
