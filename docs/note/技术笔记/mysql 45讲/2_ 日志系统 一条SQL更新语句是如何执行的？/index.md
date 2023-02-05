# 日志系统 一条SQL更新语句是如何执行的？

从一条更新语句开始：

先创建表

``` sql
create table T(
    id int primary key,
    num int
);
```

如果要将`id=2`这行值+1，sql是这么写：

``` sql
 update T set num=num+1
 where id=2;
```

更新语句走的MySQL流程跟查询语句是一样的。

1. 分析器会通过词法和语法解析知道这是一条更新语句。
2. 优化器决定要使用id这个索引。
3. 然后执行器负责具体执行，找到这一行，然后更新。

跟查询流程并不一样的是，更新流程还涉及两个重要的日志模块：

- redo log（重做日志）
- binlog（归档日志）

## 重要的日志模块：redo log
