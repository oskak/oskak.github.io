---
title: SQL Cheat Sheet 2
tags: [SQL]
---

## 条件查询
`SELECT` 语句可以通过 `WHERE` 条件来设定查询条件，查询结果是满足查询条件的记录。

```
SELECT * FROM students WHERE score >= 80;
SELECT * FROM students WHERE score >= 80 AND gender = 'M';
SELECT * FROM students WHERE score >= 80 OR gender = 'M';
SELECT * FROM students WHERE NOT class_id = 2;
```

要组合三个或者更多的条件，用小括号 `()` 表示如何进行条件运算：
```
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

如果不加括号，条件运算按照 `NOT`、`AND`、`OR` 的优先级进行。

常用的条件表达式：

| 条件               | 举例             |
| :---------------- | :-------------- |
| 使用 `<>` 判断不相等 | score <> 80     |
| 使用 `LIKE` 判断相似 | name LIKE 'ab%' |

## 投影查询
定义：让结果集仅包含指定列。

```
SELECT id, score, name FROM students;
```

## 排序
我们使用 `SELECT` 查询时，查询结果集通常是按照 `id` 排序的，也就是根据主键排序。如果我们要根据其他条件排序，可以加上 `ORDER BY` 子句。例如按照成绩从低到高进行排序：
```
SELECT id, name, gender, score FROM students ORDER BY score;
```

按照成绩从高到低进行排序：
```
SELECT id, name, gender, score FROM students ORDER BY score DESC;
```

如果 `score` 列有相同的数据，要进一步排序，可以继续添加列名。例如，使用 `ORDER BY score DESC, gender` 表示先按 `score` 列倒序，如果有相同分数的，再按 `gender` 列排序：
```
SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;
```

默认的排序规则是 `ASC`：“升序”，即从小到大。如果有 `WHERE` 子句，那么 `ORDER BY` 子句要放到 `WHERE` 子句后面：
```
SELECT id, name, gender, score
FROM students
WHERE class_id = 1
ORDER BY score DESC;
```

## 分页查询
分页实际上就是从结果集中“截取”出第M~N条记录。这个查询可以通过 `LIMIT <M> OFFSET <N>` 子句实现。

我们把结果集分页，每页3条记录。要获取第1页的记录：
```
# 对结果集从0号记录开始，最多取3条。注意SQL记录集的索引从0开始

SELECT id, name, gender, score
FROM students
ORDER BY score DESC
LIMIT 3 OFFSET 0;
```

`OFFSET` 超过了查询的最大数量并不会报错，而是得到一个空的结果集。使用 `LIMIT <M> OFFSET <N>` 分页时，随着 `N` 越来越大，查询效率也会越来越低。

## 聚合查询
定义：使用聚合函数进行查询。

查询 `students` 表一共有多少条记录：
```
SELECT COUNT(*) FROM students;
SELECT COUNT(*) num FROM students;
SELECT COUNT(*) boys FROM students WHERE gender = 'M';
```

一些聚合函数：

| 函数               | 说明             |
| :---------------- | :-------------- |
| SUM | 计算某一列的合计值 |
| AVG | 计算某一列的平均值 |
| MAX | 计算某一列的最大值 |
| MIN | 计算某一列的最小值 |

每页3条记录，通过聚合查询获得总页数：
```
SELECT CEILING(COUNT(*) / 3) FROM students;
```

## 分组聚合
统计各班的学生数量：
```
SELECT class_id, COUNT(*) num FROM students GROUP BY class_id;
```

也可以使用多个列进行分组。例如，我们想统计各班的男生和女生人数：
```
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;
```

## 多表查询
查询多张表的语法是：
```
SELECT * FROM students, classes;
```

这种多表查询又称笛卡尔查询。使用笛卡尔查询时要非常小心，由于结果集是目标表的行数乘积，对两个各自有100行记录的表进行笛卡尔查询将返回1万条记录。

来给两个表各自的 `id` 和 `name` 列起别名：
```
SELECT
    students.id sid,
    students.name,
    students.gender,
    students.score,
    classes.id cid,
    classes.name cname
FROM students, classes;
```

SQL还允许给表设置一个别名：
```
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c;
```

## 连接查询
连接查询是另一种类型的多表查询。连接查询对多个表进行 `JOIN` 运算。简单地说，就是先确定一个主表作为结果集，然后，把其他表的行有选择性地“连接”在主表结果集上。

我们想要选出 `students` 表的所有学生信息，可以用一条简单的 `SELECT` 语句完成：
```
SELECT s.id, s.name, s.class_id, s.gender, s.score FROM students s;
```

最常用的一种内连接：
```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;
```

`INNER JOIN` 只返回同时存在于两张表的行数据。`RIGHT OUTER JOIN` 返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以 `NULL` 填充剩下的字段。`LEFT OUTER JOIN` 则返回左表都存在的行。

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
RIGHT OUTER JOIN classes c
ON s.class_id = c.id;
```

`FULL OUTER JOIN` 会把两张表的所有记录全部选择出来，并且自动把对方不存在的列填充为 `NULL`：
```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
FULL OUTER JOIN classes c
ON s.class_id = c.id;
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1177760294764384/1218728424164736)
