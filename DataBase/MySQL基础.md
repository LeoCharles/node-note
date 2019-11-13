# MySQL 学习笔记

## 数据库简介

数据库（Database）是按照数据结构来组织、存储和管理数据的仓库。

关系型数据库（RDBMS），是建立在关系模型基础上的数据库，借助于集合代数等数学概念和方法来处理数据库中的数据。

+ 数据库: 数据库是一些关联表的集合。
+ 数据表: 表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
+ 行：一行（=元组，或记录）是一组相关的数据。
+ 列: 一列(数据元素) 包含了相同的数据。
+ 冗余：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
+ 主键：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
+ 外键：外键用于关联两个表。
+ 复合键：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
+ 索引：索引是对数据库表中一列或多列的值进行排序的一种结构。可快速访问数据库表中的特定信息。

## 数据类型

### 数值类型

数值类型分为：

+ 整数类型：`TINYINT`、`SMALLINT`、`MEDIUMINT`、`INT`、`BIGINT`

+ 浮点数类型：`FLOAT`、`DOUBLE`

+ 定点类型：`DECIMAL(M,D)`

| 数据类型      | 存储空间 | 取值范围（有符号 ）                          |     取值范围（有符号 ）     | 说明         |
| - | - | - | - | - | - |
| TINYINT      | 1个字节  | (-128, 127)                                 | (0, 255)                  | 很小的整数    |
| SMALLINT     | 2个字节  | (-32768, 32767)                             | (0, 65535)                | 小整数        |
| MEDIUMINT    | 3个字节  | (-8388608, 8388607)                         | (0, 16777215)             | 中等大小整数  |
| INT          | 4个字节  | (-2147483648, 2147483647)                   | (0, 4294967295)           | 普通大小整数  |
| BIGINT       | 8个字节  | (-9223372036854775808, 9223372036854775807) | (0, 18446744073709551615) | 大整数        |
| FLOAT        | 4个字节  | /                                           | /                         | 单精度浮点数值 |
| DOUBLE       | 8个字节  | /                                           | /                         | 双精度浮点数值 |
| DECIMAL(M,D) | 可变长度 | 依赖于 M 和 D 的值                           | 依赖于 M 和 D 的值         | 小数值        |

一般整数都支持正负值，不过可以使用可选的属性 `UNSIGNED`，使其不允许负值，这样可以使用存储的正数上限提高一倍。

还可以指定 `AUTO_INCRMENT` 属性，让该字段可以变成一个可以自动增长的序列。

```sql
CREATE TABLE test_users(
  id INT UNSIGNED NOT NULL AUTO_INCRMENT PRIMARY KEY,
  name VARCHAR(32) NOT NULL
);
```

## 日期/时间类型

表示日期的数据类型：`YEAR`、`TIME`、`DATE`、`DTAETIME`、`TIMESTAMP`

| 数据类型     | 存储空间 | 取值范围                                          | 格式                | 说明            |
| - | - | - | - | - |
| YEAR        | 1个字节  | 1901 ~ 2155                                       | YYYY                | 年份值          |
| TIME        | 3个字节  | -838:59:59 ~ 838:59:59                            | HH:MM:SS            | 时间值或持续时间 |
| DATE        | 3个字节  | 1000-01-01 ~ 9999-12-31                           | YYYY-MM-DD          | 日期值          |
| DATETIME    | 8个字节  | 000-01-01 00:00:00 ~ 9999-12-31 23:59:59          | YYYY-MM-DD HH:MM:SS | 日期和时间值     |
| TIMESTAMP   | 4个字节  | 1980-01-01 00:00:01 UTC ~ 2040-01-19 03:14:07 UTC | YYYY-MM-DD HH:MM:SS | 时间戳          |

## 字符串类型

字符串数据类型可以分为二进制字符串类型和非二进制字符串类型两种：

+ 非二进制字符串： `CHAR`、`VARCHAR`、`TINYTEXT`、`TEXT`、`MEDIUMTEXT`、`LONGTEXT`

+ 二进制字符串： `BINARY`、`VARBINARY`、`TINYBLOB`、`BLOB`、`MEDIUMBLOB`、`LONGBLOB`

| 数据类型     | 存储空间                           | 说明                       |
| - | - | - |
| CHAR(M)      | M 字节，1 <= M <= 255               | 定长非二进制字符串     |
| VARCHAR(M)   | L + 1 字节，L <= M 和 1 <= M <= 255 | 变长非二进制字符串     |
| TINYTEXT     | L + 1 字节，L < 2^8                 | 非常小的非二进制字符串 |
| TEXT         | L + 2 字节，L < 2^16                | 小的非二进制字符串     |
| MEDIUMTEXT   | L + 3 字节，L < 2^24                | 中等长度非二进制字符串 |
| LONGTEXT     | L + 3 字节，L < 2^32                | 极大非二进制字符串     |
| BINARY(M)    | M 字节                              | 定长二进制字符串      |
| VARBINARY(M) | M + 1 字节                          | 变长二进制字符串      |
| TINYBLOB     | L + 1 字节，L < 2^8                 | 非常小的 BLOB        |
| BLOB         | L + 2 字节，L < 2^16                | 小的 BLOB            |
| MEDIUMBLOB   | L + 3 字节，L < 2^24                | 中等大小的 BLOB      |
| LONGBLOB     | L + 3 字节，L < 2^32                | 非常大的 BLOB        |

BLOB 用于存储二进制字符串，比如图片、声音等数据。

TEXT 用于存储非二进制字符串，所以 TEXT 系列的类型存储与解析与字符集有关。

## 数据库基本操作

### 创建数据库

使用 `CREATE DATABASE` 语句创建数据库，语法：

```sql
CREATE DATABASE [IF NOT EXISTS] <数据库名> [[DEFAULT] CHARACTER SET <字符集名>] [[DEFAULT] COLLATE <校对规则名>];
```

语法说明：

+ <数据库名>：数据库名称必须符合操作系统的文件夹命名规则，注意在 MySQL 中不区分大小写。
+ IF NOT EXISTS：在创建数据库之前进行判断，只有该数据库目前尚不存在时才能执行操作。此选项可以用来避免数据库已经存在而重复创建的错误。
+ [DEFAULT] CHARACTER SET：指定数据库的默认字符集。字符集是用来定义 MySQL 存储字符串的方式。
+ [DEFAULT] COLLATE：指定字符集的默认校对规则。校对规则定义了比较字符串的方式，解决排序和字符分组的问题。

实例：

```sql
-- 判断数据库是否存在，创建名为 test_db 的数据库
CREATE DATABASE IF NOT EXISTS test_db;

-- 指定字符集和认校对规则 utf8_chinese_ci（简体中文，不区分大小写）
CREATE DATABASE IF NOT EXISTS test_db DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_chinese_ci;
```

### 查看数据库

使用 `SHOW DATABASES` 语句查看数据库，语法：

```sql
SHOW DATABASES [LIKE '数据库名'];
```

语法说明：

+ LIKE 从句是可选项，用于匹配指定的数据库名称。LIKE 从句可以部分匹配，也可以完全匹配。
+ 数据库名由单引号 `''` 包裹。

实例：

```sql
-- 查看所有数据库
SHOW DATABASES;

-- 使用 LIKE 从句，查看与 test_db 完全匹配的数据库：
SHOW DATABASES LIKE 'test_db';
```

### 删除数据库

使用 `DROP DATABASE` 语句删除已创建的数据库，语法：

```sql
DROP DATABASE [IF EXISTS] <数据库名>;
```

### 选择数据库

使用 `USE` 语句用来完成一个数据库到另一个数据库的跳转，语法：

```sql
USE <数据库名>;
```

## 数据表操作

### 创建数据表

使用 `CREATE TABLE` 语句创建数据表，该语句命令比较多，其主要是由表创建定义、表选项和分区选项所组成，语法：

```sql
CREATE TABLE <表名> ([表定义选项])[表选项][分区选项];
```

语法说明：

+ 表定义选项的格式为：`<列名1> <类型1> [,…] <列名n> <类型n>`
+ 表名不区分大小写，不能使用 SQL 语言中的关键字，如DROP、ALTER、INSERT等。
+ 表中每个列（字段）的名称和数据类型，如果创建多个列，要用逗号隔开。

实例：

```sql
-- 如果数据库中存在 user_accounts表，就删除掉
DROP TABLE IF EXISTS `user_accounts`;
CREATE TABLE `user_accounts` (
  `id`             int(100) unsigned NOT NULL AUTO_INCREMENT primary key,
  `nickname`       varchar(32)       NOT NULL DEFAULT '' COMMENT '用户昵称',
  `password`       varchar(32)       NOT NULL DEFAULT '' COMMENT '用户密码',
  `reset_password` tinyint(32)       NOT NULL DEFAULT 0 COMMENT '用户类型：0－不需要重置密码；1-需要重置密码',
  `mobile`         varchar(20)       NOT NULL DEFAULT '' COMMENT '手机号',
  `create_at`      timestamp(6)      NOT NULL DEFAULT CURRENT_TIMESTAMP(6),
  `update_at`      timestamp(6)      NOT NULL DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP(6),
  -- 创建唯一索引，不允许重复
  UNIQUE INDEX idx_user_mobile(`mobile`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='用户表信息';
```

数据类型的属性：

+ NULL：数据列可包含 NULL 值
+ NOT NULL：数据列不允许包含NULL值
+ DEFAULT：默认值
+ PRIMARY KEY：主键
+ AUTO_INCREMENT：自动递增，适用于整数类型
+ UNSIGNED：是指数值类型只能为正数
+ CHARACTER SET name：指定一个字符集
+ COMMENT：对表或者字段说明

### 查看数据表

使用 `SHOW TABLES` 语句查看当前数据库下所有的数据表。

使用 `DESCRIBE / DESC <表名>` 语句可以查看表的字段信息，包括字段名、字段数据类型、是否为主键、是否有默认值等。

### 删除数据表

使用 `DROP TABLE` 语句删除数据表。

语法： `DROP TABLE [IF EXISTS] <表名> [ , <表名1> , <表名2>, ...];`

### 修改数据表

使用 `ALTER TABLE` 语句修改表。

常用的修改表的操作有修改表名、修改字段数据类型或字段名、增加和删除字段、修改字段的排列位置、更改表的存储引擎、删除表的外键约束等。

语法：

```sql
ALTER TABLE <表名> [修改选项];
```

修改选项：

```sql
{ ADD COLUMN <列名> <类型>
| CHANGE COLUMN <旧列名> <新列名> <新列类型>
| ALTER COLUMN <列名> { SET DEFAULT <默认值> | DROP DEFAULT }
| MODIFY COLUMN <列名> <类型>
| DROP COLUMN <列名>
| RENAME TO <新表名> }
```

#### 添加字段

语法：`ALTER TABLE <表名> ADD COLUMN <新字段名> <数据类型> [约束条件] [FIRST|AFTER 已存在的字段名];`

+ FIRST 为可选参数，将新添加的字段设置为表的第一个字段。
+ AFTER 为可选参数，将新添加的字段添加到指定的已存在的字段名的后面。

实例：

```sql
-- 添加 age 字段
ALTER TABLE user_accounts ADD COLUMN age TINYINT;
```

#### 修改字段名

语法：`ALTER TABLE <表名> CHANGE COLUMN <旧字段名> <新字段名> <新数据类型>;`

实例：

```sql
-- 修改 nickname 字段名
ALTER TABLE user_accounts CHANGE COLUMN nickname username VARCHAR(25);
```

#### 修改字段数据类型

语法：`ALTER TABLE <表名> MODIFY <字段名> <数据类型>;`

实例：

```sql
-- 修改 nickname 数据类型
ALTER TABLE user_accounts MODIFY nickname VARCHAR(30);
```

#### 删除字段

语法：`ALTER TABLE <表名> DROP <字段名>;`

实例：

```sql
-- 删除 username 字段
ALTER TABLE user_accounts DROP username;
```

## 增删改查

### 查询数据

从数据表中查询数据的基本语句为 `SELECT` 语句，语法：

```sql
SELECT {* | <字段名>} FROM <表1>, <表2>, …
[WHERE <表达式>]
[GROUP BY <group by definition>]
[HAVING <expression> [{<operator> <expression>}…]]
[ORDER BY <order by definition>]
[LIMIT[<offset>,] <row count>]
```

各子句含义：

+ `{ * | <字段列名> }` 表示查询的字段，其中字段列至少包含一个字段名称，如果要查询多个字段，多个字段之间要用逗号隔开，最后一个字段后不要加逗号，通配符表示查询所有字段。
+ `FROM <表1>, <表2>, …`，表示查询数据的来源，可以是单个或多个。
+ `WHERE` 可选项，用来设定查询条件。
+ `GROUP BY <字段>` 将查询出来的数据用指定的字段进行分组。
+ `HAVING` 用来指定一组行或聚合的过滤条件，通常与 `GROUP BY` 子句一起使用。
+ `ORDER BY <字段>` 对查询出来的数据进行升序（ASC）或降序（DESC）排序。
+ `LIMIT` 用来设定返回的数据条数。

```sql
-- 查询所有数据
SELECT * FROM user_accounts;
-- 查询指定的字段
SELECT nickname, mobile, age FROM user_accounts WHERE age > 18;
```

### 新增数据

`INSERT` 语句有两种语法形式，分别是 `INSERT VALUES` 语句和 `INSERT SET` 语句。

语法1：`INSERT INTO <表名> (<列名1> … , <列名n> ) VALUES (<值1>, … , <值n>);`

语法2：`INSERT INTO <表名> SET <列名1> = <值1>, <列名2> = <值2>, … ;`

若向表中的所有列插入数据，直接采用 `INSERT <表名> VALUES (<值1>, … , <值n>)` 即可，所以的列名均可省略。

使用 `INSERT INTO … SELECT … FROM` 语句可以从一个或多个表中取出数据，并将这些数据作为行数据插入表中。

实例：

```sql
-- 使用 insert valus 插入数据
INSERT INTO user_accounts (nickname, mobile) VALUES ('leo', 15522223333);

-- 使用 insert set 插入数据
INSERT INTO user_accounts SET nickname = 'Bob', mobile = 13877779999;

-- 从 users 表中查询数据，并将值插入 user_accounts 表
INSERT INTO user_accounts (nickname, mobile) SELECT nickname, mobile FROM users;
```

+ `WHERE` 子句

  `SELECT field1, field2,...fieldN FROM table_name1, table_name2...[WHERE condition1 [AND [OR]] condition2... ;`

  + `WHERE` 子句类似于程序语言中的 `if` 条件，根据 MySQL 表中的字段值来读取指定的数据。
  + 可以使用 `AND` 或者 `OR` 指定一个或多个条件。
  + `WHERE` 子句也可以运用于 SQL 的 `DELETE` 或者 `UPDATE` 命令。

+ 修改数据

  `UPDATE table_name SET field1=new_value1, field2=new_value2 [WHERE Clause];`

  + 可以同时更新一个或多个字段。
  + 可以在 `WHERE` 子句中指定任何条件。
  + 可以在一个单独表中同时更新数据。

+ 删除数据

  `DELETE FROM table_name [WHERE Clause]`

  + 如果没有指定 `WHERE` 子句，MySQL 表中的所有记录将被删除。
  + 可以在 `WHERE` 子句中指定任何条件。
  + 可以在单个表中一次性删除记录。

+ `LIKE` 子句

  `WHERE` 子句中可以使用等号 `=` 来设定获取数据的条件，`LIKE` 子句中使用百分号 `%` 来表示任意字符，类似于正则表达式中的星号 `*`。

  `SELECT field FROM table_name WHERE field LIKE condition;`

  + 可以在 `WHERE` 子句中指定任何条件。
  + 可以在 `WHERE` 子句中使用 `LIKE` 子句。
  + 可以使用 `LIKE` 子句代替等号 `=`。
  + `LIKE` 通常与 `%`一同使用，类似于一个元字符的搜索。
  + 可以使用 `AND` 或者 `OR` 指定一个或多个条件。
  + 可以在 `DELETE` 或 `UPDATE` 命令中使用 `WHERE...LIKE` 子句来指定条件。

+ 正则表达式

  使用 `REGEXP` 操作符来进行正则表达式匹配。

+ `UNION` 操作符

  `UNION` 操作符用于连接两个以上的 `SELECT` 语句的结果组合到一个结果集合中。多个 `SELECT` 语句会删除重复的数据。

  ```php
  SELECT expression1, expression2 FROM table_name
  [WHERE conditions]
  UNION [ALL | DISTINCT]
  SELECT expression1, expression2 FROM table_name
  [WHERE conditions];
  ```

  + `expression1, expression2`: 要检索的列。
  + `tables`: 要检索的数据表。
  + `WHERE conditions`: 可选， 检索条件。
  + `DISTINCT`: 可选，删除结果集中重复的数据。默认情况下 `UNION` 操作符已经删除了重复数据，所以 `DISTINCT` 修饰符对结果没影响。
  + `ALL`: 可选，返回所有结果集，包含重复数据。

+ 排序

  使用 `ORDER BY` 子句将查询数据排序后再返回数据。

  `SELECT field FROM table_name ORDER BY field [ASC | DESC];`

  + 可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。
  + 可以设定多个字段来排序。
  + 可以使用 `ASC` 或 `DESC` 关键字来设置查询结果是按升序或降序排列。 默认情况下，它是按升序排列。
  + 可以添加 `WHERE...LIKE` 子句来设置条件。

+ 分组

  `GROUP BY` 语句根据一个或多个列对结果集进行分组。在分组的列上我们可以使用 `COUNT`, `SUM`, `AVG` 等函数。

  `WITH ROLLUP` 可以实现在分组统计数据基础上再进行相同的统计（`COUNT`, `SUM`, `AVG`...）。

  ```php
  SELECT field, func(field)
  FROM table_name
  WHERE field operator value
  GROUP BY field;
  ```

+ 多表查询

  在 `SELECT`, `UPDATE` 和 `DELETE` 语句中使用 `JOIN` 来联合多表查询。

  `JOIN` 按照功能大致分为如下三类:

  + `INNER JOIN`（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
  + `LEFT JOIN`（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
  + `RIGHT JOIN`（右连接）：获取右表所有记录，即使左表没有对应匹配的记录。

+ 事务

  事务主要用于处理操作量大，复杂度高的数据。
  事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。

  事务控制语句：

  + `BEGIN` 或 `START TRANSACTION`： 显式地开启一个事务；
  + `COMMIT` 或 `COMMIT WORK`：提交事务，并使已对数据库进行的所有修改成为永久性的；
  + `ROLLBACK` 或 `ROLLBACK WORK`：回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
  + `SAVEPOINT identifier`，`SAVEPOINT` 允许在事务中创建一个保存点，一个事务中可以有多个 `SAVEPOINT`；
  + `RELEASE SAVEPOINT identifier` 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
  + `ROLLBACK TO identifier` 把事务回滚到标记点；
  + `SET TRANSACTION` 用来设置事务的隔离级别。

  处理事务的两种方法：

  + 用 `BEGIN`, `ROLLBACK`, `COMMIT` 来实现
    + `BEGIN` 开始一个事务
    + `ROLLBACK` 事务回滚
    + `COMMIT` 提交事务
  
  + 直接用 `SET` 来改变 `MySQL` 的自动提交模式
    + `SET AUTOCOMMIT=0` 禁止自动提交
    + `SET AUTOCOMMIT=1` 开启自动提交