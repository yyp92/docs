# mysql

## 常见用法

```bash
# 创建一个名称为tcb的数据库
CREATE DATABASE `tcb`

# 创建一个包含name、address字段的users表格与删除表格
CREATE TABLE `users` (`name` VARCHAR(255), `address` VARCHAR(255))
DROP TABLE `users`

# 向users表格里插入记录
INSERT INTO `users`(`name`, `address`) VALUES ('李东bbsky', '深圳')

# 查询users表格
SELECT * FROM `users`

# 限制查询到的记录数为20，建议记录比较多的数据表查询都需指定limit
SELECT * FROM `users` LIMIT 20

# 查询users表格里字段等于某个值的记录
SELECT * FROM `users` WHERE `name` = '李东bbsky'

# 将查询结果按名称来排序
SELECT * FROM `users` ORDER BY `name`

# 删除满足条件的记录
DELETE FROM `users` WHERE `address` = '深圳'

# 更新满足条件的记录的字段值
UPDATE `users` SET `address` = "广州" WHERE `address` = '深圳'

# 使用Join进行联表查询
SELECT `users.name` AS `user`, `products.name` AS `favorite` FROM `users` JOIN `products` ON `users.favorite_product` = products.id
```

## sql 类型

- DDL：创建数据库、创建表等修改结构的 sql 叫做 DDL（Data Definition Language）

- DML：增删改那种叫做 DML（Data Manipulate Language）

- DQL： 查询数据的叫做 DQL（Data Query Language）

## sql查询语法和函数

- **where**：查询条件，比如 where id=1
- **as**：别名，比如 select xxx as 'yyy'
- **and**: 连接多个条件
- **in/not in**：集合查找，比如 where a in (1,2)
- **between and**：区间查找，比如 where a between 1 and 10
- **limit**：分页，比如 limit 0,5
- **order by**：排序，可以指定先根据什么升序、如果相等再根据什么降序，比如 order by a desc,b asc
- **group by**：分组，比如 group by aaa
- **having**：分组之后再过滤，比如 group by aaa having xxx > 5
- **distinct**：去重

## sql 还可以用很多内置函数

- 聚合函数：avg、count、sum、min、max
- 字符串函数：concat、substr、length、upper、lower
- 数值函数：round、ceil、floor、abs、mod
- 日期函数：year、month、day、date、time
- 条件函数：if、case
- 系统函数：version、datebase、user
- 类型转换函数：convert、cast、date_format、str_to_date
- 其他函数：nullif、coalesce、greatest、least
