---
title: "MySQL 语句速查笔记"
date: 2025-12-10T08:45:19+08:00
draft: false
comments: true
tags: ["MySQL", "数据库", "SQL"] 
categories: ["数据库"] 
description: "MySQL 常用语句全面速查手册，涵盖基础到高级的所有常用操作"
summary: "MySQL 常用语句全面速查手册，涵盖从基础 CRUD 到高级特性（索引、事务、存储过程、窗口函数等）的所有常用操作，适合快速查阅和刷题使用"
---



## 一、数据库操作

### 1.1 创建和删除数据库

```sql
-- 创建数据库
CREATE DATABASE database_name;
CREATE DATABASE IF NOT EXISTS database_name CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 删除数据库
DROP DATABASE database_name;
DROP DATABASE IF EXISTS database_name;

-- 查看所有数据库
SHOW DATABASES;

-- 选择数据库
USE database_name;

-- 查看当前数据库
SELECT DATABASE();
```

## 二、数据表操作

### 2.1 创建表

```sql
-- 基础创建表
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100),
    age INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- 创建表时指定引擎和字符集
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2),
    stock INT DEFAULT 0
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 从查询结果创建表
CREATE TABLE users_backup AS SELECT * FROM users;
```

### 2.2 修改表结构

```sql
-- 添加列
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
ALTER TABLE users ADD COLUMN address TEXT AFTER email;

-- 修改列
ALTER TABLE users MODIFY COLUMN age TINYINT;
ALTER TABLE users CHANGE COLUMN username user_name VARCHAR(60);

-- 删除列
ALTER TABLE users DROP COLUMN phone;

-- 重命名表
RENAME TABLE users TO members;
ALTER TABLE members RENAME TO users;

-- 添加主键
ALTER TABLE users ADD PRIMARY KEY (id);

-- 删除主键
ALTER TABLE users DROP PRIMARY KEY;

-- 添加索引
ALTER TABLE users ADD INDEX idx_username (username);
ALTER TABLE users ADD UNIQUE INDEX idx_email (email);

-- 删除索引
ALTER TABLE users DROP INDEX idx_username;
```

### 2.3 查看表信息

```sql
-- 查看所有表
SHOW TABLES;

-- 查看表结构
DESC users;
DESCRIBE users;
SHOW COLUMNS FROM users;

-- 查看创建表的语句
SHOW CREATE TABLE users;

-- 查看表状态
SHOW TABLE STATUS LIKE 'users';
```

### 2.4 删除和清空表

```sql
-- 删除表
DROP TABLE users;
DROP TABLE IF EXISTS users;

-- 清空表数据（保留结构）
TRUNCATE TABLE users;
DELETE FROM users;  -- 与 TRUNCATE 的区别是可以回滚
```

## 三、数据操作（CRUD）

### 3.1 插入数据（INSERT）

```sql
-- 插入单条数据
INSERT INTO users (username, email, age) VALUES ('张三', 'zhangsan@example.com', 25);

-- 插入多条数据
INSERT INTO users (username, email, age) VALUES 
    ('李四', 'lisi@example.com', 28),
    ('王五', 'wangwu@example.com', 30);

-- 插入所有列（可省略列名）
INSERT INTO users VALUES (NULL, '赵六', 'zhaoliu@example.com', 22, NOW(), NOW());

-- 插入或更新（存在则更新）
INSERT INTO users (id, username, email) VALUES (1, '张三', 'new@example.com')
ON DUPLICATE KEY UPDATE email = 'new@example.com';

-- 忽略重复插入错误
INSERT IGNORE INTO users (username, email) VALUES ('张三', 'zhangsan@example.com');

-- 从其他表插入数据
INSERT INTO users_backup SELECT * FROM users WHERE age > 25;
```

### 3.2 查询数据（SELECT）

#### 基础查询

```sql
-- 查询所有列
SELECT * FROM users;

-- 查询指定列
SELECT username, email FROM users;

-- 使用别名
SELECT username AS name, email AS mail FROM users;

-- 去重查询
SELECT DISTINCT age FROM users;

-- 限制结果数量
SELECT * FROM users LIMIT 10;
SELECT * FROM users LIMIT 10, 20;  -- 跳过前10条，取20条
SELECT * FROM users LIMIT 20 OFFSET 10;  -- 同上
```

#### WHERE 条件查询

```sql
-- 基本条件
SELECT * FROM users WHERE age > 25;
SELECT * FROM users WHERE username = '张三';
SELECT * FROM users WHERE email IS NULL;
SELECT * FROM users WHERE email IS NOT NULL;

-- 多条件组合
SELECT * FROM users WHERE age > 20 AND age < 30;
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
SELECT * FROM users WHERE username = '张三' OR username = '李四';
SELECT * FROM users WHERE username IN ('张三', '李四', '王五');
SELECT * FROM users WHERE username NOT IN ('张三', '李四');

-- 模糊查询
SELECT * FROM users WHERE username LIKE '张%';     -- 以"张"开头
SELECT * FROM users WHERE username LIKE '%三';     -- 以"三"结尾
SELECT * FROM users WHERE username LIKE '%张%';    -- 包含"张"
SELECT * FROM users WHERE username LIKE '张_';     -- 张+一个字符
SELECT * FROM users WHERE username NOT LIKE '张%';
```

#### 排序和分组

```sql
-- 排序
SELECT * FROM users ORDER BY age ASC;   -- 升序（默认）
SELECT * FROM users ORDER BY age DESC;  -- 降序
SELECT * FROM users ORDER BY age DESC, username ASC;  -- 多列排序

-- 分组
SELECT age, COUNT(*) as count FROM users GROUP BY age;
SELECT age, AVG(age) as avg_age FROM users GROUP BY age;

-- HAVING 过滤分组结果
SELECT age, COUNT(*) as count FROM users GROUP BY age HAVING count > 5;
```

#### 聚合函数

```sql
-- 计数
SELECT COUNT(*) FROM users;
SELECT COUNT(DISTINCT age) FROM users;

-- 求和、平均、最大、最小
SELECT SUM(age) FROM users;
SELECT AVG(age) FROM users;
SELECT MAX(age) FROM users;
SELECT MIN(age) FROM users;

-- 多个聚合函数
SELECT COUNT(*) as total, AVG(age) as avg_age, MAX(age) as max_age FROM users;
```

#### 连接查询（JOIN）

```sql
-- 内连接（INNER JOIN）
SELECT users.username, orders.order_no 
FROM users 
INNER JOIN orders ON users.id = orders.user_id;

-- 左连接（LEFT JOIN）
SELECT users.username, orders.order_no 
FROM users 
LEFT JOIN orders ON users.id = orders.user_id;

-- 右连接（RIGHT JOIN）
SELECT users.username, orders.order_no 
FROM users 
RIGHT JOIN orders ON users.id = orders.user_id;

-- 全连接（FULL JOIN，MySQL不直接支持，需要用UNION）
SELECT users.username, orders.order_no FROM users LEFT JOIN orders ON users.id = orders.user_id
UNION
SELECT users.username, orders.order_no FROM users RIGHT JOIN orders ON users.id = orders.user_id;

-- 自连接
SELECT a.username, b.username as friend 
FROM users a 
INNER JOIN users b ON a.friend_id = b.id;

-- 多表连接
SELECT u.username, o.order_no, p.product_name
FROM users u
INNER JOIN orders o ON u.id = o.user_id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id;
```

#### 子查询

```sql
-- WHERE 子查询
SELECT * FROM users WHERE id IN (SELECT user_id FROM orders WHERE amount > 1000);

-- FROM 子查询
SELECT avg_age FROM (SELECT AVG(age) as avg_age FROM users GROUP BY city) as subquery;

-- EXISTS 子查询
SELECT * FROM users WHERE EXISTS (SELECT 1 FROM orders WHERE orders.user_id = users.id);

-- 标量子查询
SELECT username, (SELECT COUNT(*) FROM orders WHERE user_id = users.id) as order_count FROM users;
```

#### 联合查询（UNION）

```sql
-- UNION（去重）
SELECT username FROM users WHERE age > 30
UNION
SELECT username FROM admins WHERE age > 30;

-- UNION ALL（不去重，性能更好）
SELECT username FROM users WHERE age > 30
UNION ALL
SELECT username FROM admins WHERE age > 30;
```

### 3.3 更新数据（UPDATE）

```sql
-- 更新单列
UPDATE users SET age = 26 WHERE username = '张三';

-- 更新多列
UPDATE users SET age = 26, email = 'new@example.com' WHERE username = '张三';

-- 批量更新
UPDATE users SET age = age + 1 WHERE age < 30;

-- 使用表达式更新
UPDATE users SET updated_at = NOW() WHERE id = 1;

-- 基于其他表更新
UPDATE users u 
INNER JOIN orders o ON u.id = o.user_id 
SET u.total_orders = u.total_orders + 1 
WHERE o.created_at > '2024-01-01';

-- 条件更新（CASE）
UPDATE users SET level = CASE
    WHEN age < 18 THEN '未成年'
    WHEN age < 60 THEN '成年'
    ELSE '老年'
END;
```

### 3.4 删除数据（DELETE）

```sql
-- 删除指定数据
DELETE FROM users WHERE id = 1;

-- 批量删除
DELETE FROM users WHERE age < 18;

-- 删除所有数据
DELETE FROM users;

-- 基于其他表删除
DELETE u FROM users u
INNER JOIN orders o ON u.id = o.user_id
WHERE o.status = 'cancelled';
```

## 四、索引管理

### 4.1 创建索引

```sql
-- 普通索引
CREATE INDEX idx_username ON users(username);

-- 唯一索引
CREATE UNIQUE INDEX idx_email ON users(email);

-- 复合索引
CREATE INDEX idx_name_age ON users(username, age);

-- 全文索引
CREATE FULLTEXT INDEX idx_content ON articles(content);

-- 在创建表时添加索引
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    INDEX idx_username (username),
    UNIQUE INDEX idx_email (email)
);
```

### 4.2 查看和删除索引

```sql
-- 查看索引
SHOW INDEX FROM users;

-- 删除索引
DROP INDEX idx_username ON users;
ALTER TABLE users DROP INDEX idx_username;
```

## 五、约束管理

### 5.1 主键约束

```sql
-- 添加主键
ALTER TABLE users ADD PRIMARY KEY (id);

-- 删除主键（需要先删除 AUTO_INCREMENT 属性）
ALTER TABLE users MODIFY id INT;
ALTER TABLE users DROP PRIMARY KEY;
```

### 5.2 外键约束

```sql
-- 添加外键
ALTER TABLE orders 
ADD CONSTRAINT fk_user_id 
FOREIGN KEY (user_id) REFERENCES users(id);

-- 添加外键时设置级联操作
ALTER TABLE orders 
ADD CONSTRAINT fk_user_id 
FOREIGN KEY (user_id) REFERENCES users(id)
ON DELETE CASCADE 
ON UPDATE CASCADE;

-- 删除外键
ALTER TABLE orders DROP FOREIGN KEY fk_user_id;
```

### 5.3 其他约束

```sql
-- 唯一约束
ALTER TABLE users ADD UNIQUE (email);

-- 非空约束
ALTER TABLE users MODIFY username VARCHAR(50) NOT NULL;

-- 检查约束（MySQL 8.0.16+）
ALTER TABLE users ADD CONSTRAINT chk_age CHECK (age >= 0 AND age <= 150);

-- 默认值约束
ALTER TABLE users ALTER COLUMN status SET DEFAULT 'active';
```

## 六、视图

```sql
-- 创建视图
CREATE VIEW active_users AS 
SELECT id, username, email FROM users WHERE status = 'active';

-- 创建或替换视图
CREATE OR REPLACE VIEW active_users AS 
SELECT id, username, email, age FROM users WHERE status = 'active';

-- 查询视图
SELECT * FROM active_users;

-- 查看视图定义
SHOW CREATE VIEW active_users;

-- 删除视图
DROP VIEW active_users;
DROP VIEW IF EXISTS active_users;
```

## 七、事务处理

```sql
-- 开启事务
START TRANSACTION;
-- 或
BEGIN;

-- 提交事务
COMMIT;

-- 回滚事务
ROLLBACK;

-- 设置保存点
SAVEPOINT sp1;
ROLLBACK TO sp1;

-- 完整示例
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- 设置自动提交
SET autocommit = 0;  -- 关闭自动提交
SET autocommit = 1;  -- 开启自动提交
```

## 八、存储过程和函数

### 8.1 存储过程

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE GetUserById(IN userId INT)
BEGIN
    SELECT * FROM users WHERE id = userId;
END //
DELIMITER ;

-- 调用存储过程
CALL GetUserById(1);

-- 带输出参数的存储过程
DELIMITER //
CREATE PROCEDURE GetUserCount(OUT total INT)
BEGIN
    SELECT COUNT(*) INTO total FROM users;
END //
DELIMITER ;

CALL GetUserCount(@count);
SELECT @count;

-- 删除存储过程
DROP PROCEDURE IF EXISTS GetUserById;

-- 查看存储过程
SHOW PROCEDURE STATUS;
SHOW CREATE PROCEDURE GetUserById;
```

### 8.2 函数

```sql
-- 创建函数
DELIMITER //
CREATE FUNCTION GetUserAge(userId INT) RETURNS INT
BEGIN
    DECLARE userAge INT;
    SELECT age INTO userAge FROM users WHERE id = userId;
    RETURN userAge;
END //
DELIMITER ;

-- 使用函数
SELECT GetUserAge(1);

-- 删除函数
DROP FUNCTION IF EXISTS GetUserAge;
```

## 九、触发器

```sql
-- 创建 BEFORE INSERT 触发器
DELIMITER //
CREATE TRIGGER before_user_insert
BEFORE INSERT ON users
FOR EACH ROW
BEGIN
    SET NEW.created_at = NOW();
END //
DELIMITER ;

-- 创建 AFTER UPDATE 触发器
DELIMITER //
CREATE TRIGGER after_user_update
AFTER UPDATE ON users
FOR EACH ROW
BEGIN
    INSERT INTO user_logs (user_id, action, created_at) 
    VALUES (OLD.id, 'update', NOW());
END //
DELIMITER ;

-- 查看触发器
SHOW TRIGGERS;

-- 删除触发器
DROP TRIGGER IF EXISTS before_user_insert;
```

## 十、用户和权限管理

```sql
-- 创建用户
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'username'@'%' IDENTIFIED BY 'password';  -- 允许任何主机

-- 授予权限
GRANT ALL PRIVILEGES ON database_name.* TO 'username'@'localhost';
GRANT SELECT, INSERT, UPDATE ON database_name.table_name TO 'username'@'localhost';

-- 刷新权限
FLUSH PRIVILEGES;

-- 查看用户权限
SHOW GRANTS FOR 'username'@'localhost';

-- 撤销权限
REVOKE INSERT, UPDATE ON database_name.* FROM 'username'@'localhost';

-- 修改密码
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';
SET PASSWORD FOR 'username'@'localhost' = PASSWORD('new_password');

-- 删除用户
DROP USER 'username'@'localhost';
```

## 十一、备份和恢复

```sql
-- 使用 mysqldump 备份（在命令行执行）
mysqldump -u username -p database_name > backup.sql
mysqldump -u username -p database_name table_name > table_backup.sql
mysqldump -u username -p --all-databases > all_databases.sql

-- 恢复数据库（在命令行执行）
mysql -u username -p database_name < backup.sql

-- 导出为 CSV
SELECT * FROM users 
INTO OUTFILE '/tmp/users.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n';

-- 从 CSV 导入
LOAD DATA INFILE '/tmp/users.csv'
INTO TABLE users
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

## 十二、性能优化相关

### 12.1 EXPLAIN 分析查询

```sql
-- 分析查询执行计划
EXPLAIN SELECT * FROM users WHERE username = '张三';

-- 详细分析
EXPLAIN EXTENDED SELECT * FROM users WHERE username = '张三';
SHOW WARNINGS;

-- JSON 格式
EXPLAIN FORMAT=JSON SELECT * FROM users WHERE username = '张三';
```

### 12.2 查询优化

```sql
-- 使用索引提示
SELECT * FROM users USE INDEX (idx_username) WHERE username = '张三';
SELECT * FROM users FORCE INDEX (idx_username) WHERE username = '张三';
SELECT * FROM users IGNORE INDEX (idx_username) WHERE username = '张三';

-- 优化 COUNT
SELECT COUNT(1) FROM users;  -- 比 COUNT(*) 快一点

-- 分页优化（避免大 OFFSET）
SELECT * FROM users WHERE id > 10000 ORDER BY id LIMIT 20;
```

### 12.3 查看状态和变量

```sql
-- 查看数据库状态
SHOW STATUS;
SHOW STATUS LIKE 'Threads%';

-- 查看系统变量
SHOW VARIABLES;
SHOW VARIABLES LIKE 'max_connections';

-- 查看进程列表
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;

-- 杀死进程
KILL process_id;
```

## 十三、常用字符串和日期函数

### 13.1 字符串函数

```sql
-- 字符串连接
SELECT CONCAT('Hello', ' ', 'World');
SELECT CONCAT_WS('-', '2024', '12', '10');

-- 字符串长度
SELECT LENGTH('Hello'), CHAR_LENGTH('你好');

-- 大小写转换
SELECT UPPER('hello'), LOWER('HELLO');

-- 截取字符串
SELECT SUBSTRING('Hello World', 1, 5);
SELECT LEFT('Hello World', 5), RIGHT('Hello World', 5);

-- 替换和删除
SELECT REPLACE('Hello World', 'World', 'MySQL');
SELECT TRIM('  Hello  '), LTRIM('  Hello'), RTRIM('Hello  ');

-- 查找位置
SELECT POSITION('World' IN 'Hello World');
SELECT LOCATE('World', 'Hello World');
```

### 13.2 日期时间函数

```mysql
-- 获取当前时间
SELECT NOW(), CURDATE(), CURTIME();
SELECT CURRENT_TIMESTAMP(), CURRENT_DATE(), CURRENT_TIME();

-- 日期格式化
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s');
SELECT DATE_FORMAT(NOW(), '%Y年%m月%d日');

-- 日期计算
SELECT date_add('2023-01-01', interval 1 day) 输出 '2023-01-02'
SELECT date_sub('2023-01-01', interval 1 day) 输出 '2022-12-31'
SELECT datediff('2023-02-01', '2023-01-01') 输出31

-- 提取日期部分
SELECT YEAR(NOW()), MONTH(NOW()), DAY(NOW());
SELECT HOUR(NOW()), MINUTE(NOW()), SECOND(NOW());

-- 时间戳转换
SELECT UNIX_TIMESTAMP(NOW());
SELECT FROM_UNIXTIME(1702195200);
```



### 13.3 数学和其他函数

```sql
-- 数学函数
SELECT ROUND(3.14159, 2), CEIL(3.2), FLOOR(3.8);
SELECT ABS(-5), MOD(10, 3), POWER(2, 3);
SELECT RAND(), RAND()*100;

-- 条件函数
SELECT IF(age > 18, '成年', '未成年') FROM users;
SELECT IFNULL(email, '无邮箱') FROM users;
SELECT COALESCE(NULL, NULL, 'default', 'value');

-- CASE 表达式
SELECT username,
    CASE 
        WHEN age < 18 THEN '未成年'
        WHEN age < 60 THEN '成年'
        ELSE '老年'
    END as age_group
FROM users;
```

## 十四、JSON 操作（MySQL 5.7+）

```sql
-- 创建 JSON 列
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    attributes JSON
);

-- 插入 JSON 数据
INSERT INTO products VALUES (1, 'iPhone', '{"color": "black", "storage": "128GB"}');

-- 查询 JSON 数据
SELECT JSON_EXTRACT(attributes, '$.color') as color FROM products;
SELECT attributes->'$.color' as color FROM products;
SELECT attributes->>'$.color' as color FROM products;  -- 去除引号

-- 修改 JSON 数据
UPDATE products SET attributes = JSON_SET(attributes, '$.color', 'white') WHERE id = 1;
UPDATE products SET attributes = JSON_INSERT(attributes, '$.price', 999) WHERE id = 1;
UPDATE products SET attributes = JSON_REMOVE(attributes, '$.storage') WHERE id = 1;

-- JSON 数组操作
SELECT JSON_ARRAY('a', 'b', 'c');
SELECT JSON_CONTAINS('["a", "b", "c"]', '"a"');
SELECT JSON_LENGTH('["a", "b", "c"]');
```

## 十五、窗口函数（MySQL 8.0+）

```sql
-- ROW_NUMBER：行号
SELECT username, age, ROW_NUMBER() OVER (ORDER BY age DESC) as row_num FROM users;

-- RANK：排名（有并列，跳号）
SELECT username, score, RANK() OVER (ORDER BY score DESC) as rank FROM students;

-- DENSE_RANK：密集排名（有并列，不跳号）
SELECT username, score, DENSE_RANK() OVER (ORDER BY score DESC) as rank FROM students;

-- 分组窗口
SELECT department, username, salary,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank
FROM employees;

-- 累计求和
SELECT username, amount,
    SUM(amount) OVER (ORDER BY created_at) as running_total
FROM transactions;

-- LAG 和 LEAD：访问上下行
SELECT username, salary,
    LAG(salary, 1) OVER (ORDER BY id) as prev_salary,
    LEAD(salary, 1) OVER (ORDER BY id) as next_salary
FROM employees;
```

## 十六、常见问题和技巧

### 16.1 查找重复数据

```sql
-- 查找重复的用户名
SELECT username, COUNT(*) as count 
FROM users 
GROUP BY username 
HAVING count > 1;

-- 删除重复数据（保留ID最小的）
DELETE u1 FROM users u1
INNER JOIN users u2 
WHERE u1.id > u2.id AND u1.username = u2.username;
```

### 16.2 随机获取数据

```sql
-- 随机获取一条数据
SELECT * FROM users ORDER BY RAND() LIMIT 1;

-- 更高效的随机（大表适用）
SELECT * FROM users WHERE id >= (SELECT FLOOR(MAX(id) * RAND()) FROM users) LIMIT 1;
```

### 16.3 行转列

```sql
-- 使用 CASE WHEN
SELECT 
    name,
    SUM(CASE WHEN subject = '语文' THEN score ELSE 0 END) as chinese,
    SUM(CASE WHEN subject = '数学' THEN score ELSE 0 END) as math
FROM scores
GROUP BY name;
```

### 16.4 列转行

```sql
-- 使用 UNION ALL
SELECT name, '语文' as subject, chinese as score FROM scores
UNION ALL
SELECT name, '数学' as subject, math as score FROM scores;
```

## 十七、常用管理命令

```sql
-- 查看 MySQL 版本
SELECT VERSION();

-- 查看当前用户
SELECT USER(), CURRENT_USER();

-- 查看数据库大小
SELECT 
    table_schema AS 'Database',
    ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Size (MB)'
FROM information_schema.tables
GROUP BY table_schema;

-- 查看表大小
SELECT 
    table_name AS 'Table',
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)'
FROM information_schema.tables
WHERE table_schema = 'database_name'
ORDER BY (data_length + index_length) DESC;

-- 修复表
REPAIR TABLE users;

-- 优化表
OPTIMIZE TABLE users;

-- 分析表
ANALYZE TABLE users;

-- 检查表
CHECK TABLE users;
```

------

## 总结

这份笔记涵盖了 MySQL 从基础到进阶的大部分常用操作，当然也适合在刷题中补充，包括：

- 数据库和表的管理
- 完整的 CRUD 操作
- 索引和约束
- 视图、事务、存储过程
- 用户权限管理
- 性能优化技巧
- 常用函数和 JSON、窗口函数！