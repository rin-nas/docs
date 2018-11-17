

# Нумерация строк без использования переменных и rownum()

```
-- 48 sec. in MyISAM,
-- 44 sec. in InnoDB,
-- 29 sec. in Memory;
-- 5574 rows total
-- EXPLAIN
SELECT t1.*, COUNT(t2.name) AS tag_id
FROM `_tmp` AS t1 LEFT JOIN `_tmp` AS t2 ON t1.name > t2.name
GROUP BY t1.`name`;
```

  Тот же запрос с использованием подзапроса работает в 2-3 раза быстрее:
```
-- 25 sec. in MyISAM,
-- 21 sec. in InnoDB,
-- 9 sec. in Memory;
-- 5574 rows total
-- EXPLAIN
SELECT t1.*, (SELECT COUNT(*) FROM `_tmp` AS t2 WHERE t1.name > t2.name) AS tag_id
FROM `_tmp` AS t1
```
Эксперименты были сделаны на MySQL 5.0.x.
