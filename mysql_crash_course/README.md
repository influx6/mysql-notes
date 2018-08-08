# MySQL Crash Course

Book link: http://www.forta.com/books/0672327120/

## Import db

```sql
create database crashcourse
use crashcourse
source /path/to/create.sql
source /path/to/populate.sql
```

## SQL

```sql
# 查看服务状态
show status
# 查看所有数据库
show databases
# 使用库
use crashcourse
# 查看所有表
show tables
# 查看库创建语句
show create database crashcourse
# 查看表创建语句
show create table customers
# 显示授权列表
show grants
# 查看服务器错误消息
show errors
# 查看服务器警告消息
show warnings
# 查看 show 命令帮助文档
help show

# 查看所有列
show columns from customers
describe customers (alias)

# 查看 select 命令帮助文档
help select
# 查询非重复行，如查询多列，则要求多列都需要一致时才会去重掉
select distinct vend_id from products
# 限制返回行数
select * from customers limit 0, 2
select * from customers limit 2 offset 0
# 完全限定库名和表名
select customers.cust_name from crashcourse.customers

# 排序
select * from customers order by cust_name asc, cust_id desc
```

## 摘录

不指定排序方式的说明

> 其实，检索出的数据并不是以纯粹的随机顺序显示的。
> 如果不排序，数据一般将以它在底层表中出现的顺序显示。这可以是数据最初添加到表中的顺序。
> 但是，如果数据后来进行过更新或删除，则此顺序将会受到MySQL重用回收存储空间的影响。
> 因此，如果不明确控制的话，不能（也不应该）依赖该排序顺序。
> 关系数据库设计理论认为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有意义。

大小写排序问题

> 在对文本性的数据进行排序时，A与a相同吗？a位于B之前还是位于Z之后？这些问题不是理论问题，其答案取决于数据库如何设置。
> 在字典（dictionary）排序顺序中，A被视为与a相同，这是MySQL（和大多数数据库管理系统）的默认行为。
> 但是，许多数据库管理员能够在需要时改变这种行为（如果你的数据库包含大量外语字符，可能必须这样做）。
> 这里，关键的问题是，如果确实需要改变这种排序顺序，用简单的ORDER BY子句做不到。你必须请求数据库管理员的帮助。

## 灵感

- 如何修改 auto_increment 当前的自动增量和步长？