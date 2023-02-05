# Redis 数据类型

## String

Redis 字符串存储字节序列，包括文本、序列化对象和二进制数组。因此，字符串是最基本的 Redis 数据类型。

它们通常用于缓存，但它们支持额外的功能，也可以实现计数器和执行按位运算。

### string示例

- 在 Redis 中存储然后检索字符串：

``` redis
> SET user:1 salvatore
OK
> GET user:1
"salvatore"
```

- 存储序列化的 JSON 字符串并将其设置为从现在起 100 秒后过期：

``` redis
> SET ticket:27 "\"{'username': 'priya', 'ticket_id': 321}\"" EX 100
```

- 增加一个计数器：

``` redis
> INCR views:page:2
(integer) 1
> INCRBY views:page:2 10
(integer) 11
```

### string限制

默认情况下，单个 Redis 字符串最大为**512 MB**。

### string基本命令

#### 查询和设置字符串

- `SET` 存储一个字符串值
- `SETNX`仅当键不存在时才存储字符串值。用于实现锁。
- `GET`查询字符串值。
- `MGET`在单个操作中检索多个字符串值。

#### 管理计数器

- `INCRBY`原子地递增（并在传递负数时递减）存储在给定键处的计数器。
- `INCRBYFLOAT`浮点计数器。

#### 按位运算

参照bitmaps

### string性能

大多数字符串操作都是`O(1)`，这意味着它们非常高效。

但是，请注意 `SUBSTR`、`GETRANGE` 和 `SETRANGE` 命令，它们可以是 O(n)。处理大型字符串时，这些随机访问字符串命令可能会导致性能问题。

## List

Redis 列表是字符串值的链接列表。

Redis 列表经常用于：

- 实现栈和队列。
- 为后台工作系统构建队列管理。

例子：

- 将列表视为队列（先进先出）：

``` redis
> LPUSH work:queue:ids 101
(integer) 1
> LPUSH work:queue:ids 237
(integer) 2
> RPOP work:queue:ids
"101"
> RPOP work:queue:ids
"237"
```

- 将列表视为堆栈（先进后出）：
  
``` redis
> LPUSH work:queue:ids 101
(integer) 1
> LPUSH work:queue:ids 237
(integer) 2
> LPOP work:queue:ids
"237"
> LPOP work:queue:ids
"101"
```

- 检查列表的长度：

``` redis
> LLEN work:queue:ids
(integer) 0
```

- 以原子方式从一个列表中弹出一个元素并推送到另一个列表(v6.2.0)：

``` redis
> LPUSH board:todo:ids 101
(integer) 1
> LPUSH board:todo:ids 273
(integer) 2
> LMOVE board:todo:ids board:in-progress:ids LEFT LEFT
"273"
> LRANGE board:todo:ids 0 -1
1) "101"
> LRANGE board:in-progress:ids 0 -1
1) "273"
```

- 要创建一个永远不会超过 100 个元素的上限列表，可以在每次调用 `LPUSH` 后调用 `LTRIM`(*会移除掉溢出元素*)：

``` redis
> LPUSH notifications:user:1 "You've got mail!"
(integer) 1
> LTRIM notifications:user:1 0 99
OK
> LPUSH notifications:user:1 "Your package will be delivered at 12:01 today."
(integer) 2
> LTRIM notifications:user:1 0 99
OK
```

### List限制

Redis 列表的最大长度为 2^32 - 1 (4,294,967,295) 个元素。

### List基本命令

- `LPUSH` 添加一个新元素到列表的头部；`RPUSH`添加到尾部。
- `LPOP` 从列表的头部移除并返回一个元素；`RPOP`做同样的事情，但从列表的尾部开始。
- `LLEN` 返回列表的长度。
- `LMOVE` 原子地将元素从一个列表移动到另一个列表。
- `LTRIM` 将列表减少到指定的元素范围。

### List阻塞命令

支持多种阻塞命令。

如：

- `BLPOP`从列表的头部移除并返回一个元素。如果列表为空，则命令将阻塞，直到元素可用或达到指定的超时为止。
- `BLMOVE`原子地将元素从源列表移动到目标列表。如果源列表为空，则该命令将阻塞，直到有新元素可用为止。

### List性能

访问其头部或尾部的列表操作的复杂度为`O(1)`，这意味着它们**非常高效**。

**但是，操作列表中元素的命令通常是 O(n)。这些示例包括`LINDEX`、`LINSERT`和`LSET`。运行这些命令时要小心，主要是在对大型列表进行操作时。**

## Set

Sets是唯一字符串（成员）的无序集合。

可以使用 Redis Sets来高效地:

- 跟踪唯一项目 *（例如，跟踪访问给定博客文章的所有唯一 IP 地址）*。
- 表示关系 *（例如，具有给定角色的所有用户的集合）。*
- 执行常见的集合运算，*例如交集、并集和差集*。

### Sets示例

- 存储用户 123 和 456 的收藏书 ID 集：(value不会重复)

```redis
> SADD user:123:favorites 347
(integer) 1
> SADD user:123:favorites 561
(integer) 1
> SADD user:123:favorites 742
(integer) 1
> SADD user:456:favorites 561
(integer) 1
```

- 检查用户 123 是否喜欢书籍 742 和 299

``` redis
> SISMEMBER user:123:favorites 742
(integer) 1
> SISMEMBER user:123:favorites 299
(integer) 0
```

- 用户 123 和 456 有没有共同喜欢的书？(*返回数组*)

```redis
> SINTER user:123:favorites user:456:favorites
1) "561"
```

- 用户 123 收藏了多少本书？

```redis
> SCARD user:123:favorites
(integer) 3
```

### Sets限制

Redis 集的最大大小为 2^32 - 1 (4,294,967,295) 个成员。

### Sets基本命令

- `SADD`将新成员添加到集合中。
- `SREM`从集合中删除指定的成员。
- `SISMEMBER`测试集合成员的字符串。
- `SINTER`返回两个或多个集合共有的成员集合（即交集）。
- `SCARD`返回集合的大小（又名基数）。

[完整命令列表](https://redis.io/commands/?group=set)

### Sets性能

大多数集合操作（包括添加、删除和检查项是否为集合成员）都是 O（1）。这意味着它们非常高效。

但是，对于具有数十万或更多成员的大型集，在运行 `SMEMBERS` 命令时应格外小心。此命令为 O（n），并**在单个响应中返回整个集合**。作为替代方法，请考虑 `SSCAN`，它允许您以迭代方式检索集合的所有成员。

> 对大型数据集（或流数据）进行的成员资格检查可能会占用大量内存。如果担心内存使用并且不需要完美的精度，请考虑使用 [Bloom 过滤器或 Cuckoo 过滤器](https://redis.io/docs/stack/bloom/)作为集合的替代方案。

## Hash

Redis hashes是结构化为字段值对集合的记录类型。
可以使用hash来表示基本对象和存储计数器分组等。
