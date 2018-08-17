# 区别

## 数据类型Datetime和Timestamp的区别

> MySQL converts TIMESTAMP values from the current time zone to UTC for storage, and back from UTC to the current time zone for retrieval. (This does not occur for other types such as DATETIME.) By default, the current time zone for each connection is the server's time. The time zone can be set on a per-connection basis. As long as the time zone setting remains constant, you get back the same value you store. If you store a TIMESTAMP value, and then change the time zone and retrieve the value, the retrieved value is different from the value you stored. This occurs because the same time zone was not used for conversion in both directions. The current time zone is available as the value of the time_zone system variable. 
> [1](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)

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