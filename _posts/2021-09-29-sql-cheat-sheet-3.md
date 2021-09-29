---
title: SQL Cheat Sheet 3
tags: [SQL]
---

## 插入数据 INSERT
例如，向 `students` 表插入一条新记录：
```
INSERT INTO students (class_id, name, gender, score) VALUES (2, '大牛', 'M', 80);
```

注意到我们并没有列出 `id` 字段，这是因为 `id` 字段是一个自增主键，它的值可以由数据库自己推算出来。如果一个字段有默认值，那么在 `INSERT` 语句中也可以不出现。

一次性添加多条记录：
```
INSERT INTO students (class_id, name, gender, score) VALUES
  (1, '大宝', 'M', 87),
  (2, '二宝', 'M', 81);
```

## 更新数据 UPDATE
更新 `id=1` 的记录：
```
UPDATE students SET name='大牛', score=66 WHERE id=1;
```

在 `UPDATE` 语句中，更新字段时可以使用表达式。例如，把所有80分以下的同学的成绩加10分：
```
UPDATE students SET score=score+10 WHERE score<80;
```

如果 `WHERE` 条件没有匹配到任何记录，`UPDATE` 语句不会报错，也不会有任何记录被更新。

`UPDATE` 语句可以没有 `WHERE` 条件，例如：
```
UPDATE students SET score=60;
```

整个表的所有记录都会被更新。在执行 `UPDATE` 语句时要非常小心。可以先用 `SELECT` 语句来测试 `WHERE` 条件是否筛选出了期望的记录集，然后再用 `UPDATE` 更新。

## 删除数据 DELETE
删除 `students` 表中 `id=1` 的记录：
```
DELETE FROM students WHERE id=1;
```

如果 `WHERE` 条件没有匹配到任何记录，`DELETE` 语句不会报错，也不会有任何记录被删除。

和 `UPDATE` 类似，不带 `WHERE` 条件的 `DELETE` 语句会删除整个表的数据：
```
DELETE FROM students;
```

## References
- [廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/1177760294764384/1179611171910432)
