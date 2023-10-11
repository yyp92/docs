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
