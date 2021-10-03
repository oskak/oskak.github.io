---
title: SQL Cheat Sheet 5
tags: [SQL]
---

## 数据库事务
定义：把多条语句作为一个整体进行操作的功能。

在执行SQL语句的时候，某些业务要求，一系列操作必须全部执行，而不能仅执行一部分。数据库事务可以确保该事务范围内的所有操作都可以**全部成功**或者**全部失败**。如果事务失败，那么效果就和没有执行这些SQL一样，不会对数据库数据有任何改动。

数据库事务具有 `ACID` 这4个特性：
- A：Atomic，原子性，将所有SQL作为原子工作单元执行，要么全部执行，要么全部不执行；
- C：Consistent，一致性，事务完成后，所有数据的状态都是一致的，即A账户只要减去了100，B账户则必定加上了100；
- I：Isolation，隔离性，如果有多个事务并发执行，每个事务作出的修改必须与其他事务隔离；
- D：Duration，持久性，即事务完成后，对数据库数据的修改被持久化存储。

对于单条SQL语句，数据库系统自动将其作为一个事务执行，这种事务被称为隐式事务。要手动把多条SQL语句作为一个事务执行，使用 `BEGIN` 开启一个事务，使用 `COMMIT` 提交一个事务，这种事务被称为显式事务。很显然多条SQL语句要想作为一个事务执行，就必须使用显式事务。

```
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

有些时候，我们希望主动让事务失败。这时，可以用 `ROLLBACK` 回滚事务，整个事务会失败：
```
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
ROLLBACK;
```

## 隔离级别 Read Uncommitted
Read Uncommitted 是隔离级别最低的一种事务级别。在这种隔离级别下，一个事务会读到另一个事务更新后但未提交的数据。如果另一个事务回滚，那么当前事务读到的数据就是脏数据。这就是脏读（Dirty Read）。

`students` 表的数据，该表仅一行记录：
```
mysql> select * from students;
+----+-------+
| id | name  |
+----+-------+
|  1 | Alice |
+----+-------+
1 row in set (0.00 sec)
```

然后，分别开启两个 MySQL 客户端连接，按顺序依次执行事务A和事务B：

| 时刻 | 事务A | 事务B |
| --- | ----- | ----- |
| 1   | SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; | SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED; |
| 2   | BEGIN; | BEGIN; |
| 3   | UPDATE students SET name = 'Bob' WHERE id = 1; | |
| 4   | | SELECT * FROM students WHERE id = 1; (Got 'Bob') |
| 5   | ROLLBACK; | |
| 6   | | SELECT * FROM students WHERE id = 1; (Got 'Alice') |
|7    | | COMMIT; |

## 隔离级别 Read Committed
在 Read Committed 隔离级别下，一个事务可能会遇到不可重复读（Non Repeatable Read）的问题。

不可重复读是指，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致。

`students` 表的数据：
```
mysql> select * from students;
+----+-------+
| id | name  |
+----+-------+
|  1 | Alice |
+----+-------+
1 row in set (0.00 sec)
```

分别开启两个 MySQL 客户端连接，按顺序依次执行事务A和事务B：

| 时刻 | 事务A | 事务B |
| --- | ----- | ----- |
| 1   | SET TRANSACTION ISOLATION LEVEL READ COMMITTED; | SET TRANSACTION ISOLATION LEVEL READ COMMITTED; |
| 2   | BEGIN; | BEGIN; |
| 3   | | SELECT * FROM students WHERE id = 1; (Got 'Alice') |
| 4   | UPDATE students SET name = 'Bob' WHERE id = 1; | |
| 5   | COMMIT; | |
| 6   | | SELECT * FROM students WHERE id = 1; (Got 'Bob') |
|7    | | COMMIT; |

## 隔离级别 Repeatable Read
在 Repeatable Read 隔离级别下，一个事务可能会遇到幻读（Phantom Read）的问题。

幻读是指，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了。

分别开启两个 MySQL 客户端连接，按顺序依次执行事务A和事务B：

| 时刻 | 事务A | 事务B |
| --- | ----- | ----- |
| 1   | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ; | SET TRANSACTION ISOLATION LEVEL REPEATABLE READ; |
| 2   | BEGIN; | BEGIN; |
| 3   | | SELECT * FROM students WHERE id = 99; (Got 'Empty') |
| 4   | INSERT INTO students (id, name) VALUES (99, 'Bob'); | |
| 5   | COMMIT; | |
| 6   | | SELECT * FROM students WHERE id = 99; (Got 'Empty') |
| 7   | | UPDATE students SET name = 'Alice' WHERE id = 99; |
| 8   | | SELECT * FROM students WHERE id = 99; (Got 'Alice') |
| 9   | | COMMIT; |

## 隔离级别 Serializable
Serializable 是最严格的隔离级别。在 Serializable 隔离级别下，所有事务按照次序依次执行，因此，脏读、不可重复读、幻读都不会出现。

虽然 Serializable 隔离级别下的事务具有最高安全性。但是由于事务是串行执行，效率会大大下降，应用程序性能会急剧降低。如果没有特别重要的情景，一般不会使用 Serializable 隔离级别。

## 默认隔离级别
如果没有指定隔离级别，数据库就会使用默认的隔离级别。在 MySQL 中，如果使用 InnoDB，默认的隔离级别是 Repeatable Read。

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1177760294764384/1245268341158240)
