# 区别

## 数据类型Datetime和Timestamp的区别

> MySQL converts TIMESTAMP values from the current time zone to UTC for storage, and back from UTC to the current time zone for retrieval. (This does not occur for other types such as DATETIME.) By default, the current time zone for each connection is the server's time. The time zone can be set on a per-connection basis. As long as the time zone setting remains constant, you get back the same value you store. If you store a TIMESTAMP value, and then change the time zone and retrieve the value, the retrieved value is different from the value you stored. This occurs because the same time zone was not used for conversion in both directions. The current time zone is available as the value of the time_zone system variable. 

- [11.3.5 Automatic Initialization and Updating for TIMESTAMP and DATETIME](https://dev.mysql.com/doc/refman/5.7/en/timestamp-initialization.html)
- [datetime](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)

演示

```sql
mysql> select @@time_zone;
+-------------+
| @@time_zone |
+-------------+
| +08:00      |
+-------------+
mysql> create table demo02(a date, b timestamp);
Query OK, 0 rows affected
Time: 0.021s
mysql> insert into demo02 values('2018-02-01 19:13:12', '2018-02-01 19:13:12');
Query OK, 1 row affected
Time: 0.004s
mysql> select * from demo02;
+---------------------+---------------------+
| a                   | b                   |
+---------------------+---------------------+
| 2018-02-01 19:13:12 | 2018-02-01 19:13:12 |
+---------------------+---------------------+
1 row in set
Time: 0.004s
mysql> set time_zone = '+7:00';
Query OK, 0 rows affected
Time: 0.000s
mysql> select * from demo02;
+---------------------+---------------------+
| a                   | b                   |
+---------------------+---------------------+
| 2018-02-01 19:13:12 | 2018-02-01 18:13:12 |
+---------------------+---------------------+
1 row in set
Time: 0.005s
```

## 数据类型Char和Varchar的区别

> The CHAR and VARCHAR types are similar, but differ in the way they are stored and retrieved. They also differ in maximum length and in whether trailing spaces are retained.
>
> When CHAR values are stored, they are right-padded with spaces to the specified length. When CHAR values are retrieved, trailing spaces are removed unless the PAD_CHAR_TO_FULL_LENGTH SQL mode is enabled.
> VARCHAR values are not padded when they are stored. Trailing spaces are retained when values are stored and retrieved, in conformance with standard SQL.
> [1](https://dev.mysql.com/doc/refman/5.7/en/char.html)

演示

```sql
mysql> create table demo03(a char(10), b varchar(10));
Query OK, 0 rows affected
Time: 0.021s
mysql> insert into demo03 values (' suhua ', ' suhua ');
Query OK, 1 row affected
Time: 0.004s
mysql> select * from demo03;
+--------+---------+
| a      | b       |
+--------+---------+
|  suhua |  suhua  |
+--------+---------+
1 row in set
Time: 0.015s
mysql> select length(a), length(b) from demo03;
+-----------+-----------+
| length(a) | length(b) |
+-----------+-----------+
| 6         | 7         |
+-----------+-----------+
1 row in set
Time: 0.016s
mysql> select concat('(', a, ')'), concat('(', b, ')') from demo03;
+---------------------+---------------------+
| concat('(', a, ')') | concat('(', b, ')') |
+---------------------+---------------------+
| ( suhua)            | ( suhua )           |
+---------------------+---------------------+
1 row in set
Time: 0.017s
```

### Change、Midify和ALTER的区别

The CHANGE, MODIFY, and ALTER clauses enable the names and definitions of existing columns to be altered. They have these comparative characteristics:

- CHANGE:

    - Can rename a column and change its definition, or both.

    - Has more capability than MODIFY, but at the expense of convenience for some operations. CHANGE requires naming the column twice if not renaming it.

    - With FIRST or AFTER, can reorder columns.

- MODIFY:

    - Can change a column definition but not its name.

    - More convenient than CHANGE to change a column definition without renaming it.

    - With FIRST or AFTER, can reorder columns.

- ALTER: Used only to change a column default value.

CHANGE is a MySQL extension to standard SQL. MODIFY is a MySQL extension for Oracle compatibility.

- [Renaming, Redefining, and Reordering Columns](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html)

演示

```sql
mysql> create table demo(a int);
Query OK, 0 rows affected
Time: 0.030s
mysql> desc demo;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| a     | int(11) | YES  |     | <null>  |       |
+-------+---------+------+-----+---------+-------+
1 row in set
Time: 0.008s
mysql> alter table demo change a b smallint;
Query OK, 0 rows affected
Time: 0.056s
mysql> desc demo;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| b     | smallint(6) | YES  |     | <null>  |       |
+-------+-------------+------+-----+---------+-------+
1 row in set
Time: 0.007s
mysql> alter table demo change b b int;
Query OK, 0 rows affected
Time: 0.064s
mysql> desc demo;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| b     | int(11) | YES  |     | <null>  |       |
+-------+---------+------+-----+---------+-------+
1 row in set
Time: 0.017s
mysql> alter table demo modify b bigint;
Query OK, 0 rows affected
Time: 0.070s
mysql> desc demo;
+-------+------------+------+-----+---------+-------+
| Field | Type       | Null | Key | Default | Extra |
+-------+------------+------+-----+---------+-------+
| b     | bigint(20) | YES  |     | <null>  |       |
+-------+------------+------+-----+---------+-------+
1 row in set
Time: 0.007s
mysql> alter table demo alter b set default 0;
Query OK, 0 rows affected
Time: 0.006s
mysql> desc demo;
+-------+------------+------+-----+---------+-------+
| Field | Type       | Null | Key | Default | Extra |
+-------+------------+------+-----+---------+-------+
| b     | bigint(20) | YES  |     | 0       |       |
+-------+------------+------+-----+---------+-------+
1 row in set
Time: 0.017s
```

## int 和 int(5) 的区别

> When used in conjunction with the optional (nonstandard) attribute ZEROFILL, the default padding of spaces is replaced with zeros. For example, for a column declared as INT(4) ZEROFILL, a value of 5 is retrieved as 0005.

- [11.2.5 Numeric Type Attributes](https://dev.mysql.com/doc/refman/5.7/en/numeric-type-attributes.html)

演示

```sql
mysql> create table demo(a int, b int(5));
Query OK, 0 rows affected (0.03 sec)

mysql> desc demo;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| a     | int(11) | YES  |     | NULL    |       |
| b     | int(5)  | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> insert into demo values(1, 1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from demo;
+------+------+
| a    | b    |
+------+------+
|    1 |    1 |
+------+------+
1 row in set (0.00 sec)

mysql> alter table demo modify a int zerofill;
Query OK, 1 row affected (0.07 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> alter table demo modify b int(5) zerofill;
Query OK, 1 row affected (0.06 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> desc demo;
+-------+---------------------------+------+-----+---------+-------+
| Field | Type                      | Null | Key | Default | Extra |
+-------+---------------------------+------+-----+---------+-------+
| a     | int(10) unsigned zerofill | YES  |     | NULL    |       |
| b     | int(5) unsigned zerofill  | YES  |     | NULL    |       |
+-------+---------------------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> select * from demo;
+------------+-------+
| a          | b     |
+------------+-------+
| 0000000001 | 00001 |
+------------+-------+
1 row in set (0.00 sec)

mysql> insert into demo values (1, 1111111);
Query OK, 1 row affected (0.00 sec)

mysql> select * from demo;
+------------+---------+
| a          | b       |
+------------+---------+
| 0000000001 |   00001 |
| 0000000001 | 1111111 |
+------------+---------+
2 rows in set (0.00 sec)
```

## float、double和decimal的区别

> 浮点数如果不写精度和标度，则会按照实际精度值显示，如果有精度和标度，则会自动将四舍五入后的结果插入，系统不会报错；
> 定点数如果不写精度和标度，则按照默认值 decimal(10,0) 来进行操作，并如果数据超越了精度和标度值，系统会报错。

演示

```sql
mysql> create table demo (a float(5, 2), b double(5, 2), c decimal(5, 2));
Query OK, 0 rows affected (0.02 sec)

mysql> desc demo;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| a     | float(5,2)   | YES  |     | NULL    |       |
| b     | double(5,2)  | YES  |     | NULL    |       |
| c     | decimal(5,2) | YES  |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> insert into demo values (1.23, 1.23, 1.23);
Query OK, 1 row affected (0.01 sec)

mysql> select * from demo;
+------+------+------+
| a    | b    | c    |
+------+------+------+
| 1.23 | 1.23 | 1.23 |
+------+------+------+
1 row in set (0.00 sec)

mysql> insert into demo values (1.234, 1.234, 1.23);
Query OK, 1 row affected (0.01 sec)

mysql> select * from demo;
+------+------+------+
| a    | b    | c    |
+------+------+------+
| 1.23 | 1.23 | 1.23 |
| 1.23 | 1.23 | 1.23 |
+------+------+------+
2 rows in set (0.00 sec)

mysql> insert into demo values (1.234, 1.234, 1.234);
Query OK, 1 row affected, 1 warning (0.01 sec)

mysql> show warnings;
+-------+------+----------------------------------------+
| Level | Code | Message                                |
+-------+------+----------------------------------------+
| Note  | 1265 | Data truncated for column 'c' at row 1 |
+-------+------+----------------------------------------+
1 row in set (0.00 sec)

mysql> select * from demo;
+------+------+------+
| a    | b    | c    |
+------+------+------+
| 1.23 | 1.23 | 1.23 |
| 1.23 | 1.23 | 1.23 |
| 1.23 | 1.23 | 1.23 |
+------+------+------+
3 rows in set (0.00 sec)

mysql> alter table demo modify a float;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> alter table demo modify b double;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> alter table demo modify c decimal;
Query OK, 3 rows affected, 3 warnings (0.18 sec)
Records: 3  Duplicates: 0  Warnings: 3

mysql> desc demo;
+-------+---------------+------+-----+---------+-------+
| Field | Type          | Null | Key | Default | Extra |
+-------+---------------+------+-----+---------+-------+
| a     | float         | YES  |     | NULL    |       |
| b     | double        | YES  |     | NULL    |       |
| c     | decimal(10,0) | YES  |     | NULL    |       |
+-------+---------------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> insert into demo values (1.234, 1.234, 1.234);
Query OK, 1 row affected, 1 warning (0.02 sec)

mysql> show warnings;
+-------+------+----------------------------------------+
| Level | Code | Message                                |
+-------+------+----------------------------------------+
| Note  | 1265 | Data truncated for column 'c' at row 1 |
+-------+------+----------------------------------------+
1 row in set (0.00 sec)

mysql> select * from demo;
+-------+-------+------+
| a     | b     | c    |
+-------+-------+------+
|  1.23 |  1.23 |    1 |
|  1.23 |  1.23 |    1 |
|  1.23 |  1.23 |    1 |
| 1.234 | 1.234 |    1 |
+-------+-------+------+
4 rows in set (0.00 sec)
```
