---
title: SQL Cheat Sheet 1
tags: [SQL]
---

## Download MySQL
[MySQL Community Downloads](https://dev.mysql.com/downloads/mysql/)

Add MySQL path to zsh:
```
export PATH=${PATH}:/usr/local/mysql/bin/
```

Log in MySQL as root:
```
mysql -u root -p
```

## 主键
主键的作用是通过某个字段区分出不同的记录。主键最好不要二次修改。选取主键的一个基本原则是：不使用任何业务相关的字段作为主键。主键也不应该允许 `NULL`。

常见的可作为 `id` 字段的类型：
- 自增整数类型
- 全局唯一 `GUID` 类型

联合主键：两个或更多的字段被设置为主键。没有必要的情况下，我们尽量不使用联合主键，因为它给关系表带来了复杂度的上升。

## 外键
外键通过定义外键约束实现：
```
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);
```

通过定义外键约束，关系数据库可以保证无法插入无效的数据。即如果 `classes` 表不存在 `id=99` 的记录，`students` 表就无法插入 `class_id=99` 的记录。

由于外键约束会降低数据库性能，大部分互联网应用程序并不设置外键约束，而是仅靠程序自身来保证逻辑的正确性。这种情况下，`class_id` 仅仅是一个普通的列，只是它起到了外键的作用而已。

删除一个外键约束：
```
ALTER TABLE students
DROP FOREIGN KEY fk_class_id;
```

## 多对多
通过一个表的外键关联到另一个表，我们可以定义出**一对多**关系。有些时候，还需要定义“多对多”关系。例如，一个老师可以对应多个班级，一个班级也可以对应多个老师。

多对多关系实际上是通过两个一对多关系实现的，即通过一个中间表。

## 一对一
定义：一个表的记录对应到另一个表的唯一一个记录。例如，`students` 表的每个学生可以有自己的联系方式，如果把联系方式存入另一个表 `contacts`，我们就可以得到一个“一对一”关系。

实际上，一对一关系准确地说，是 `contacts` 表一对一对应 `students` 表。

有一些应用会把一个大表拆成两个一对一的表，目的是把经常读取和不经常读取的字段分开，以获得更高的性能。例如，把一个大的用户表分拆为用户基本信息表 `user_info` 和用户详细信息表 `user_profiles`。大部分时候，只需要查询 `user_info` 表，并不需要查询 `user_profiles` 表，这样就提高了查询速度。

## 索引
索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。通过使用索引，可以让数据库系统不必扫描整个表，而是直接定位到符合条件的记录，大大加快了查询速度。

如果要经常根据 `score` 列进行查询，就可以对 `score` 列创建索引：
```
ALTER TABLE students
ADD INDEX idx_score (score);
```

索引的效率取决于索引列的值是否散列，即该列的值如果越互不相同，那么索引效率越高。反过来，如果记录的列存在大量相同的值，例如 `gender` 列，大约一半的记录值是 `M`，另一半是 `F`，对该列创建索引就没有意义。

可以对一张表创建多个索引。索引的优点是提高查询效率，缺点是在插入、更新和删除记录时，需要同时修改索引。因此，索引越多，插入、更新和删除记录的速度就越慢。

对于主键，关系数据库会自动对其创建主键索引。使用主键索引的效率是最高的，因为主键会保证绝对唯一。

## 唯一索引
某些列根据业务要求，具有唯一性约束。这个时候，可以给该列添加一个唯一索引：
```
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);
```

也可以只对某一列添加一个唯一约束而不创建唯一索引：
```
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1177760294764384/1218728424164736)
