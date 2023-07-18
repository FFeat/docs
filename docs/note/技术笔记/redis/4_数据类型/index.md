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

### Hash例子

- 将基本用户配置文件表示为hash：

```redis
> HSET user:123 username martina firstName Martina lastName Elisa country GB
(integer) 4
> HGET user:123 username
"martina"
> HGETALL user:123
1) "username"
2) "martina"
3) "firstName"
4) "Martina"
5) "lastName"
6) "Elisa"
7) "country"
8) "GB"
```

- 存储设备 777 对服务器执行 ping 操作、发出请求或发送错误的次数的计数器：

```redis
> HINCRBY device:777:stats pings 1
(integer) 1
> HINCRBY device:777:stats pings 1
(integer) 2
> HINCRBY device:777:stats pings 1
(integer) 3
> HINCRBY device:777:stats errors 1
(integer) 1
> HINCRBY device:777:stats requests 1
(integer) 1
> HGET device:777:stats pings
"3"
> HMGET device:777:stats requests errors
1) "1"
2) "1"
```

### Hash基本命令

- `HSET`设置散列上一个或多个字段的值。
- `HGET`返回给定字段的值。
- `HMGET`返回一个或多个给定字段的值。
- `HINCRBY`通过提供的整数增加给定字段的值。

[完整列表](https://redis.io/commands/?group=hash)

### Hash性能

大多数 Redis 哈希命令的复杂度为 `O(1)`。

一些命令,*例如`HKEYS`、`HVALS`和`HGETALL`是 `O(n)`，其中`n`是字段值对的数量。*

### Hash限制

每个哈希最多可以存储 4,294,967,295 (2^32 - 1) 个字段值对。

实际上，hash仅受托管 Redis 部署的 VM 上的总内存的限制。

## 排序的Set

Redis Sorted Sets是按关联分数排序的唯一字符串（成员）的集合。**当多个字符串具有相同的分数时，字符串按字典顺序排列**。

排序集的一些用例包括：

- 排行榜。*例如，可以使用排序集轻松维护大型在线游戏中最高分的有序列表。*
- 速率限制器。*特别是，可以使用排序集来构建滑动窗口速率限制器，以防止过多的 API 请求*

### Sorted sets示例

- 随着玩家分数的变化更新实时排行榜：

``` redis
> ZADD leaderboard:455 100 user:1
(integer) 1
> ZADD leaderboard:455 75 user:2
(integer) 1
> ZADD leaderboard:455 101 user:3
(integer) 1
> ZADD leaderboard:455 15 user:4
(integer) 1
> ZADD leaderboard:455 275 user:2
(integer) 0
```

> `ZADD`在最后一次调用中，更新了Score.

- 获取前 3 名玩家的分数：

``` redis
> ZRANGE leaderboard:455 0 2 REV WITHSCORES
1) "user:2"
2) "275"
3) "user:3"
4) "101"
5) "user:1"
6) "100"
```

`REV`反转参数仅6.2版本能支持，其他版本用`ZRevrange`命令

- 用户 2 的等级是多少？

``` redis
> c leaderboard:455 user:2
(integer) 0
```

`ZREVRANK` 降序的排名

### Sorted Sets基本命令

-`ZADD`将新成员和关联的分数添加到已排序的集合中。如果该成员已经存在，则更新分数。
-`ZRANGE`返回在给定范围内排序的有序集合的成员。
-`ZRANK`返回升序成员的排名
-`ZREVRANK`返回降序成员的排名

### Sorted Sets性能

大多数有序集合操作的复杂度为 `O(log(n))`，其中`n`是成员数。

`ZRANGE`运行具有较大返回值（*例如，数万或更多*）的命令时要小心。此命令的时间复杂度为`O(log(n) + m)`，其中`m`是返回的结果数

## Stream

Redis Streams是一种数据结构，其作用类似于仅附加日志。您可以使用流实时记录和同时联合事件
使用场景包括：

1. 事件溯源 *（例如，跟踪用户操作、点击等）*
2. 传感器监控 *（例如，现场设备的读数）*
3. 通知 *（例如，将每个用户的通知记录存储在单独的流中）*

> Redis 为每个stream条目生成一个唯一的 ID。可以使用这些 ID 稍后检索它们的关联条目，或者读取和处理流中的所有后续条目。

Redis 流支持多种修剪策略（*以防止流无限制地增长*）和不止一种消费策略（*请参阅XREAD、XREADGROUP和XRANGE*）。

### Stream示例

- 向流中添加多个温度读数

``` redis
> XADD temperatures:us-ny:10007 * temp_f 87.2 pressure 29.69 humidity 46
"1658354918398-0"
> XADD temperatures:us-ny:10007 * temp_f 83.1 pressure 29.21 humidity 46.5
"1658354934941-0"
> XADD temperatures:us-ny:10007 * temp_f 81.9 pressure 28.37 humidity 43.7
"1658354957524-0"
```

- 读取从 ID 开始的前两个流条目1658354934941-0：

``` redis
 XRANGE temperatures:us-ny:10007 1658354934941-0 + COUNT 2
```

- 读取最多 100 个新的流条目，从流的末尾开始，如果没有条目被写入，则最多阻塞 300 毫秒：

``` redis
 XREAD COUNT 100 BLOCK 300 STREAMS temperatures:us-ny:10007 $
```

`$`符号返回从阻塞开始起，收到的集合

### Sorted Sets基本命令

- `XADD`向流中添加一个新条目。
- `XREAD`读取一个或多个条目，从给定位置开始并及时向前移动。
- `XRANGE`返回两个提供的条目 ID 之间的条目范围。
- `XLEN`返回流的长度。
请参阅[流命令的完整列表](https://redis.io/commands/?group=stream)。

### Sorted Sets性能

向流中添加一个条目是`O(1)`。访问任何单个条目的时间复杂度为`O(n)`，其中`n`是 `ID`的长度。由于流 `ID` 通常很短且长度固定，因此这有效地减少了恒定时间查找。有关原因的详细信息，请注意流是作为[基数树](https://en.wikipedia.org/wiki/Radix_tree)实现的。

简而言之，Redis 流提供高效的插入和读取。有关详细信息，请参阅每个命令的时间复杂度。

## geospatial

可以存储坐标并进行搜索。**此数据结构可用于查找给定半径或边界框内的附近点。**

### geospatial示例

假设正在构建一个移动应用程序，可以让您找到离当前位置最近的所有电动汽车充电站。

- 将多个位置添加到地理空间索引：

``` redis
> GEOADD locations:ca -122.27652 37.805186 station:1
(integer) 1
> GEOADD locations:ca -122.2674626 37.8062344 station:2
(integer) 1
> GEOADD locations:ca -122.2469854 37.8104049 station:3
(integer) 1
```

- 查找给定位置 5 公里半径范围内的所有位置，并返回到每个位置的距离(*v6.2.0*)：

``` redis
> GEOSEARCH locations:ca FROMLONLAT -122.2612767 37.7936847 BYRADIUS 5 km WITHDIST
1) 1) "station:1"
   2) "1.8523"
2) 1) "station:2"
   2) "1.4979"
3) 1) "station:3"
   2) "2.2441"
```

### geospatial基本命令

- `GEOADD`将位置添加到给定的地理空间索引（*请注意，经度在使用此命令的纬度之前*）。
- `GEOSEARCH`返回具有给定半径或边界框的位置。

## HyperLogLog

HyperLogLog 是一种估计集合基数的数据结构。作为一种概率数据结构，HyperLogLog 以完美的准确性换取高效的空间利用。

Redis HyperLogLog 实现最多使用 12 KB，并提供 0.81% 的标准错误。

### HyperLogLog示例

-向 HyperLogLog 添加一些项目：

``` redis
> PFADD members 123
(integer) 1
> PFADD members 500
(integer) 1
> PFADD members 12
(integer) 1
```

- 估计集合中的成员数：

``` redis
> PFCOUNT members
```

### HyperLogLog基本命令

- `PFADD`向 HyperLogLog 添加一个项目。
- `PFCOUNT`返回集合中项目数的估计值。
- `PFMERGE`将两个或多个 HyperLogLog 合并为一个。

### HyperLogLog性能

写入 ( `PFADD`) 和读取 ( `PFCOUNT`) HyperLogLog 是在恒定的时间和空间内完成的。合并 HLL 的时间复杂度为 `O(n)`，其中n是草图的数量。

### HyperLogLog限制

HyperLogLog 可以估计最多包含 18,446,744,073,709,551,616 (2^64) 个成员的集合的基数。

## bitmaps

Redis 位图是字符串数据类型的扩展，可让您将字符串视为位向量。您还可以对一个或多个字符串执行按位运算。

bitmap用例的一些示例包括：

- 集合成员对应于整数 0-N 的情况的有效集合表示。
- 对象权限，其中每一位代表一个特定的权限，类似于文件系统存储权限的方式。

### bitmaps示例

假设在现场部署了 1000 个传感器，标记为 0-999。想要快速确定给定传感器是否在一小时内对服务器执行了 ping 操作。

可以使用其键引用当前小时的位图来表示此场景。

- 传感器 123 在 2024 年 1 月 1 日 00:00 时对服务器执行 ping 操作。

``` redis
> SETBIT pings:2024-01-01-00:00 123 1
(integer) 0
```

- 传感器 123 是否在 2024 年 1 月 1 日 00:00 时 ping 服务器？

``` redis
> GETBIT pings:2024-01-01-00:00 123
1
```

- 服务器456呢？

``` redis
> GETBIT pings:2024-01-01-00:00 456
0
```

### bitmaps基本命令

- `SETBIT`将提供的偏移量处的位设置为 0 或 1。
- `GETBIT`返回给定偏移量处的位值。
- `BITOP`允许您对一个或多个字符串执行按位运算。

### bitmaps性能

`SETBIT`和`GETBIT`是`O(1)`。
`BITOP`是 `O(n)`，其中`n`是比较中最长字符串的长度。

## bitfields

Redis 位域允许设置、递增和获取任意位长度的整数值。*例如，可以对从无符号 1 位整数到有符号 63 位整数的任何内容进行操作。*

这些值使用二进制编码的 Redis 字符串存储。**位域支持原子读、写和递增操作，使它们成为管理计数器和类似数值的不错选择。**

### bitfields示例

假设正在跟踪在线游戏中的活动。您想要为每个玩家维护两个关键指标：金币总量和杀死的怪物数量。因为您的游戏很容易上瘾，所以这些计数器的宽度至少应为 32 位。

可以用每个玩家一个位域来表示这些计数器。

- 新玩家以 1000 金币开始教程（偏移量 0 中的计数器）。

``` redis
 BITFIELD player:1:stats SET u32 #0 1000
```

- 杀死怪后，加上获得的 50 金币并增加“被杀”计数器（偏移量 1）。

``` redis
BITFIELD player:1:stats INCRBY u32 #0 50 INCRBY u32 #1 1
```

- 支付铁匠 999 金币购买传说中生锈的匕首。

``` redis
> BITFIELD player:1:stats INCRBY u32 #0 -999
1) (integer) 51
```

- 读取玩家的统计数据：

``` redis
BITFIELD player:1:stats GET u32 #0 GET u32 #1
```

### bitfields基本命令

- `BITFIELD`以原子方式设置、递增和读取一个或多个值。
- `BITFIELD_RO`是`BITFIELD`的只读变体。

### bitfields性能

 `BITFIELD`是`O(n)`，其中n是访问的计数器的数量。
