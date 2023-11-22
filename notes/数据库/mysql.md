# mysql

## 通过 docker 连接数据库

```bash
# 命令行输入，接着输入密码就会链接数据库
mysql -u root -p

# 数据库账号密码
root
123456
```

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

## Join 查询

INNER JOIN 是只返回两个表中能关联上的数据。

LEFT JOIN 是额外返回左表中没有关联上的数据。

RIGHT JOIN 是额外返回右表中没有关联上的数据。

## 关联主键的几种方式

- CASCADE： 主表主键更新，从表关联记录的外键跟着更新，主表记录删除，从表关联记录删除

- SET NULL：主表主键更新或者主表记录删除，从表关联记录的外键设置为 null

- RESTRICT：只有没有从表的关联记录时，才允许删除主表记录或者更新主表记录的主键 id

- NO ACTION： 同 RESTRICT，只是 sql 标准里分了 4 种，但 mysql 里 NO ACTION 等同于 RESTRICT。

## mysql 综合练习

```sql
-- create database practice;

-- use practice;

-- 创建 customers 表，用于存储客户信息
-- CREATE TABLE IF NOT EXISTS `customers` (
--  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '客户ID，自增长',
--  `name` varchar(255) NOT NULL COMMENT '客户姓名，非空',
--  PRIMARY KEY (`id`)
-- ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='客户信息表';

-- 创建 orders 表，用于存储订单信息
-- CREATE TABLE IF NOT EXISTS `orders` (
--  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '订单ID，自增长',
--  `customer_id` int(11) NOT NULL COMMENT '客户ID，非空',
--  `order_date` date NOT NULL COMMENT '订单日期，非空',
--  `total_amount` decimal(10,2) NOT NULL COMMENT '订单总金额，非空',
--  PRIMARY KEY (`id`),
--  FOREIGN KEY (`customer_id`) REFERENCES `customers` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
-- ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='订单信息表';

-- 创建 order_items 表，用于存储订单商品信息
-- CREATE TABLE IF NOT EXISTS `order_items` (
--  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '商品ID，自增长',
--  `order_id` int(11) NOT NULL COMMENT '订单ID，非空',
--  `product_name` varchar(255) NOT NULL COMMENT '商品名称，非空',
--  `quantity` int(11) NOT NULL COMMENT '商品数量，非空',
--  `price` decimal(10,2) NOT NULL COMMENT '商品单价，非空',
--  PRIMARY KEY (`id`),
--  FOREIGN KEY (`order_id`) REFERENCES `orders` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
-- ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='订单商品信息表';


-- 向 customers 表插入数据
INSERT INTO `customers` (`name`) 
    VALUES 
        ('张丽娜'),('李明'),('王磊'),('赵静'),('钱伟'),
        ('孙芳'),('周涛'),('吴洋'),('郑红'),('刘华'),
        ('陈明'),('杨丽'),('王磊'),('张伟'),('李娜'),
        ('刘洋'),('陈静'),('杨阳'),('王丽'),('张强');

-- 向 orders 表插入数据
INSERT INTO `orders` (`customer_id`, `order_date`, `total_amount`)
    VALUES
        (1, '2022-01-01',100.00),(1, '2022-01-02',200.00),
        (2, '2022-01-03',300.00),(2, '2022-01-04',400.00),
        (3, '2022-01-05',500.00),(3, '2022-01-06',600.00),
        (4, '2022-01-07',700.00),(4, '2022-01-08',800.00),
        (5, '2022-01-09',900.00),(5, '2022-01-10',1000.00);

-- 向 order_items 表插入数据
INSERT INTO `order_items` (`order_id`, `product_name`, `quantity`, `price`)
    VALUES
        (1, '耐克篮球鞋',1,100.00),
        (1, '阿迪达斯跑步鞋',2,50.00),
        (2, '匡威帆布鞋',3,100.00),
        (2, '万斯板鞋',4,50.00),
        (3, '新百伦运动鞋',5,100.00),
        (3, '彪马休闲鞋',6,50.00),
        (4, '锐步经典鞋',7,100.00),
        (5, '亚瑟士运动鞋',10,50.00),
        (5, '帆布鞋',1,100.00),
        (1, '苹果手写笔',2,50.00),
        (2, '电脑包',3,100.00),
        (3, '苹果手机',4,50.00),
        (4, '苹果耳机',5,100.00),
        (5, '苹果平板',7,100.00);


select * from customers;
select * from orders;
select * from order_items;


-- 需求 1: 查询每个客户的订单总金额
-- 客户的订单存在订单表里，可能有多个，这里需要 JOIN ON 关联两个表，然后用 GROUP BY 根据客户 id 分组，再通过 SUM 函数计算价格总和。
SELECT customers.name, SUM(orders.total_amount) AS total_amount 
    FROM customers
    INNER JOIN orders ON customers.id = orders.customer_id 
    GROUP BY customers.id;

-- 排序
SELECT customers.name, SUM(orders.total_amount) AS total_amount 
    FROM customers
    INNER JOIN orders ON customers.id = orders.customer_id 
    GROUP BY customers.id
    ORDER BY total_amount DESC;

-- 如果想取前 3 的，可以用 LIMIT：
SELECT customers.name, SUM(orders.total_amount) AS total_amount 
    FROM customers
    JOIN orders ON customers.id = orders.customer_id
    GROUP BY customers.id
    ORDER BY total_amount DESC 
    LIMIT 0,3;


-- 需求 2: 查询每个客户的订单总金额，并计算其占比
-- 每个客户的总金额的需求上面实现了，这里需要算占比，就需要通过一个子查询来计算全部订单的总金额，然后相除：
SELECT customers.name, SUM(orders.total_amount) AS total_amount, 
    SUM(orders.total_amount) / (SELECT SUM(total_amount) FROM orders) AS percentage 
    FROM customers
    INNER JOIN orders ON customers.id = orders.customer_id
    GROUP BY customers.id;


-- 需求 3：查询每个客户的订单总金额，并列出每个订单的商品清单
-- 这里在总金额的基础上，多了订单项的查询，需要多关联一个表：
SELECT customers.name, orders.order_date, orders.total_amount, 
    order_items.product_name, order_items.quantity, order_items.price
    FROM customers
    JOIN orders ON customers.id = orders.customer_id
    JOIN order_items ON orders.id = order_items.order_id
    ORDER BY customers.name, orders.order_date;


-- 需求 4：查询每个客户的订单总金额，并列出每个订单的商品清单，同时只显示客户名字姓“张”的客户的记录：
-- 总金额和商品清单的需求前面实现了，这里只需要加一个 WHERE 来过滤客户名就行：
SELECT customers.name, orders.order_date, orders.total_amount, 
    order_items.product_name, order_items.quantity, order_items.price
    FROM customers
    INNER JOIN orders ON customers.id = orders.customer_id
    INNER JOIN order_items ON orders.id = order_items.order_id
    WHERE customers.name LIKE '张%'
    ORDER BY customers.name, orders.order_date;


-- 需求 5: 查询每个客户的订单总金额，并列出每个订单的商品清单，同时只显示订单日期在2022年1月1日到2022年1月3日之间的记录
-- 这里比上面的需求只是多了日期的过滤，范围是一个区间，用 BETWEEN AND：
SELECT customers.name, orders.order_date,
    orders.total_amount, order_items.product_name,
    order_items.quantity, order_items.price
    FROM customers
    INNER JOIN orders ON customers.id = orders.customer_id
    INNER JOIN order_items ON orders.id = order_items.order_id
    WHERE orders.order_date BETWEEN '2022-01-01' AND '2022-01-03'
    ORDER BY customers.name, orders.order_date;


-- 需求 6：查询每个客户的订单总金额，并计算商品数量，只包含商品名称包含“鞋”的商品，商品名用-连接，显示前 3 条记录：
/*
    查询订单总金额和商品数量都需要用 group by 根据 customer.id 分组，过滤出只包含鞋的商品。
    把分组的多条商品名连接起来需要用 GROUP_CONCAT 函数。
    然后 LIMIT 3
*/
SELECT 
        c.name AS customer_name,
        SUM(o.total_amount) AS total_amount,
        COUNT(oi.id) AS total_quantity,
        GROUP_CONCAT(oi.product_name SEPARATOR '-') AS product_names
    FROM customers c
    JOIN orders o ON c.id = o.customer_id
    JOIN order_items oi ON o.id = oi.order_id
    WHERE oi.product_name LIKE '%鞋%'
    GROUP BY c.name
    ORDER BY total_amount DESC
    LIMIT 3;


-- 需求 7: 查询存在订单的客户
-- 这里使用子查询 + EXISTS 来实现：
SELECT * FROM customers c
    WHERE EXISTS (
         SELECT 1 FROM orders o WHERE o.customer_id = c.id
    );

-- 没有订单的客户
SELECT * FROM customers c
    WHERE NOT EXISTS (
         SELECT 1 FROM orders o WHERE o.customer_id = c.id
    );


-- 需求 8: 将王磊的订单总金额打九折
SELECT * FROM orders 
    JOIN customers ON orders.customer_id = customers.id
    WHERE customers.name = '王磊';

-- 更新它们为 90%：
UPDATE orders o SET o.total_amount = o.total_amount * 0.9
    WHERE o.customer_id IN (
        SELECT id FROM customers WHERE name = '王磊'
    );
```

## MySQL 的事务和隔离级别

### 例子

```sql
-- use practice;


-- SELECT * from orders;


-- 开启事务：
START TRANSACTION;
-- 修改数据
UPDATE order_items SET quantity=1 WHERE order_id=3;
UPDATE orders SET total_amount=200 WHERE id=3;


-- 执行下 ROLLBACK, 恢复数据
ROLLBACK;

-- 保存的点
-- SAVEPOINT aaa;


-- 查看数据a
SELECT * from orders;
SELECT * from order_items;
```

### 事务

**START TRANSACTION 开启事务后所有的 sql 语句都可以 ROLLBACK，除非执行了 COMMIT 完成这段事务。**

**还可以设置几个 SAVEPOINT，这样可以 ROLLBACK TO 任何一个 SAVEPOINT 的位置。**

### MYSQL 有 4 种事务隔离级别

- **READ UNCOMMITTED**：可以读到别的事务尚未提交的数据。
  
  - 这就有个问题，你这个事务内第一次读的数据是 aaa，下次读可能就是 bbb 了，这个问题叫做**不可重复读**。
  - 而且，万一你读到的数据人家又回滚了，那你读到的就是临时数据，这个问题叫做**脏读**。

- **READ COMMITTED**：只读取别的事务已提交的数据。
  
  - 这样是没有脏读问题了，读到的不会是临时数据。
  - 但是还是有可能你这个事务内第一次读的数据是 aaa，下次读可能是 bbb ，也就是不可重复读的问题依然存在。
  - 不只是数据不一样，可能你两次读取到的记录行数也不一样，这叫做**幻读**。

- **REPEATABLE READ**：在同一事务内，多次读取数据将保证结果相同。
  
  - 这个级别保证了读取到的数据一样，但是不保证行数一样，也就是说解决了不可重复读的问题，但仍然存在幻读的问题。

- **SERIALIZABLE**：在同一时间只允许一个事务修改数据。
  
  - 事务一个个执行，各种问题都没有了。
  - 但是负面影响就是性能很差，只能一个个的事务执行。

### 查询当前的事务隔离级别：

```sql
select @@transaction_isolation;
```

## 视图、存储过程、函数

```sql
-- 用之前的 customers、orders 表来建立视图：
-- CREATE VIEW customer_orders AS 
--     SELECT 
--         c.name AS customer_name, 
--         o.id AS order_id, 
--         o.order_date, 
--         o.total_amount
--     FROM customers c
--     JOIN orders o ON c.id = o.customer_id;


-- select * from customer_orders


-- 存储过程
/*
    首先 DELIMITER $$ 定义分隔符为 $$，因为默认是 ;
    这样中间就可以写 ; 了，不会中止存储过程的 sql。
    最后再恢复为之前的分隔符：DELIMITER ;
*/
DELIMITER $$
CREATE PROCEDURE get_customer_orders(IN customer_id INT)
BEGIN
        SELECT o.id AS order_id, o.order_date, o.total_amount
        FROM orders o
        WHERE o.customer_id = customer_id;
END $$
DELIMITER ;

-- 调用使用 CALL 存储过程(参数) 的形式：
CALL get_customer_orders(5);


-- 创建函数
/*
    但默认 mysql 是不允许创建函数的。
    需要先设置下这个变量：
*/
SET GLOBAL log_bin_trust_function_creators = 1;

-- 比如一个求平方的函数：
/*
    CREATE FUNCTION 声明函数的名字和参数 x，并且通过 RETURNS 声明返回值类型。
    BEGIN、END 中间的是函数体。
    先 DECLARE 一个 INT 类型的变量，然后 SET 它的值为 x * x，之后通过 RETURN 返回这个结果。
*/
DELIMITER $$
CREATE FUNCTION square(x INT)
RETURNS INT
BEGIN
    DECLARE result INT;
    SET result = x * x;
    RETURN result;
END $$
DELIMITER ;

-- 查看
select product_name, square(price) from order_items;


/*
    创建一个函数 get_order_total，参数为 INT 类型的 order_id，返回值为 DECIMAL(10, 2) 类型。
    声明 total 变量，执行查询订单详情表综合的 select 语句，把结果放到 total 变量里，也就是 SELECT INTO。
    最后 RETURN 这个 total 变量。
*/
DELIMITER $$
CREATE FUNCTION get_order_total(order_id INT)
RETURNS DECIMAL(10,2)
BEGIN
    DECLARE total DECIMAL(10,2);
    SELECT SUM(quantity * price) INTO total
        FROM order_items
        WHERE order_id = order_items.order_id;
    RETURN total;
END $$
DELIMITER ;

-- 调用：
select id, get_order_total(id) from orders;
```

- 视图就是把查询结果保存下来，可以对这个视图做查询，简化了查询语句并且也能隐藏一些字段。
  
  - 它增删改的限制比较多，一般只是来做查询。

- 存储过程就是把一段 sql 封装起来，传参数调用。

- 函数也是把一段 sql 或者其他逻辑封装起来，传参数调用，但是它还有返回值。
