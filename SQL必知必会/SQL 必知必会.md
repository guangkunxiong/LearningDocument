# 检索数据

SQL语句结束最好用分号分隔。

最好别使用`*`通配符。虽然使用通配符能让你自己省事，不用明确列出所需列，但检索不需要的列通常会降低检索速度和应用程序的性能。

DISTINCT 关键字 返回不同的,作用于所有的列，不仅仅是跟在后面的一列

```
SELECT DISTINCT vend_id FROM Products;
```

使用`SELECT TOP 5`语句，只检索前5行数据。

MySQL、MariaDB、PostgreSQL或者SQLite，需要使用`LIMIT` 子句，

```
SELECT prod_name
FROM Products
LIMIT 5;
-- 后五行
SELECT prod_name
FROM Products
LIMIT 5 OFFSET 5;
```

|  操作符   |        说明        |
| :-------: | :----------------: |
|    `=`    |        等于        |
|   `<>`    |       不等于       |
|   `!=`    |       不等于       |
|    `<`    |        小于        |
|   `<=`    |      小于等于      |
|   `!<`    |       不小于       |
|    `>`    |        大于        |
|   `>=`    |      大于等于      |
|   `!>`    |       不大于       |
| `BETWEEN` | 在指定的两个值之间 |
| `IS NULL` |      为NULL值      |

`!=`和`<>`通常可以互换。但是，并非所有DBMS都支持这两种不等于操作符。如果有疑问，请参阅相应的DBMS文档。