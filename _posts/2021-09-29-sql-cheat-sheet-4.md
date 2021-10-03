---
title: SQL Cheat Sheet 4
tags: [SQL]
---

## 管理 MySQL
MySQL Client 的可执行程序是 mysql，MySQL Server的可执行程序是mysqld。

在 MySQL Client 中输入的 SQL 语句通过 TCP 连接发送到MySQL Server。默认端口号是3306，即如果发送到本机 MySQL Server，地址是 `127.0.0.1:3306`。

也可以只安装 MySQL Client，然后连接到远程 MySQL Server。假设远程 MySQL Server 的IP地址是`10.0.1.99`，使用 `-h` 指定IP或域名：
```
mysql -h 10.0.1.99 -u root -p
```

## 数据库命令
列出所有数据库：
```
SHOW DATABASES;
```

创建一个新数据库：
```
CREATE DATABASE test;
```

删除一个数据库：
```
DROP DATABASE test;
```

对一个数据库进行操作时，要首先将其切换为当前数据库：
```
USE test;
```

## 表命令
列出当前数据库的所有表：
```
SHOW TABLES;
```

查看一个表的结构：
```
DESC students;
```

查看创建表的SQL语句：
```
SHOW CREATE TABLE students;
```

创建表：
```
CREATE TABLE statistics (
    id BIGINT NOT NULL AUTO_INCREMENT,
    class_id BIGINT NOT NULL,
    average DOUBLE NOT NULL,
    PRIMARY KEY (id)
);
```

删除表：
```
DROP TABLE students;
```

给 `students` 表新增一列 `birth`：
```
ALTER TABLE students ADD COLUMN birth VARCHAR(10) NOT NULL;
```

修改 `birth` 列，把列名改为 `birthday`，类型改为 `VARCHAR(20)`：
```
ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL;
```

删除列：
```
ALTER TABLE students DROP COLUMN birthday;
```

## 实用SQL语句
插入一条新记录，但如果记录已经存在，就先删除原记录，再插入新记录：
```
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

插入一条新记录，但如果记录已经存在，就更新该记录：
```
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;
```

插入一条新记录，但如果记录已经存在，就忽略此操作。若 `id=1` 的记录不存在，`INSERT` 语句将插入新记录，否则，不执行任何操作：
```
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

对一个表进行快照，即复制一份当前表的数据到一个新表，可以结合 `CREATE TABLE` 和 `SELECT`：
```
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
```

如果查询结果集需要写入到表中，可以结合 `INSERT` 和 `SELECT`，将 `SELECT` 语句的结果集直接插入到指定表中。

创建一个统计成绩的表 `statistics`，记录各班的平均成绩：
```
CREATE TABLE statistics (
    id BIGINT NOT NULL AUTO_INCREMENT,
    class_id BIGINT NOT NULL,
    average DOUBLE NOT NULL,
    PRIMARY KEY (id)
);
```

```
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id;
```

## 强制使用指定索引
在查询的时候，数据库系统会自动分析查询语句，并选择一个最合适的索引。但是很多时候，数据库系统的查询优化器并不一定总是能使用**最优索引**。如果我们知道如何选择索引，可以使用 `FORCE INDEX` 强制查询使用指定的索引：
```
SELECT * FROM students FORCE INDEX (idx_class_id) WHERE class_id = 1 ORDER BY id DESC;
```
