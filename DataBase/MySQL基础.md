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
| - | - | - | - | - |
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

### 日期/时间类型

表示日期的数据类型：`YEAR`、`TIME`、`DATE`、`DTAETIME`、`TIMESTAMP`

| 数据类型     | 存储空间 | 取值范围                                          | 格式                | 说明            |
| - | - | - | - | - |
| YEAR        | 1个字节  | 1901 ~ 2155                                       | YYYY                | 年份值          |
| TIME        | 3个字节  | -838:59:59 ~ 838:59:59                            | HH:MM:SS            | 时间值或持续时间 |
| DATE        | 3个字节  | 1000-01-01 ~ 9999-12-31                           | YYYY-MM-DD          | 日期值          |
| DATETIME    | 8个字节  | 000-01-01 00:00:00 ~ 9999-12-31 23:59:59          | YYYY-MM-DD HH:MM:SS | 日期和时间值     |
| TIMESTAMP   | 4个字节  | 1980-01-01 00:00:01 UTC ~ 2040-01-19 03:14:07 UTC | YYYY-MM-DD HH:MM:SS | 时间戳          |

### 字符串类型

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

使用 `CREATE DATABASE` 语句创建数据库。

语法：

```sql
CREATE DATABASE [IF NOT EXISTS] <数据库名> [[DEFAULT] CHARACTER SET <字符集名>] [[DEFAULT] COLLATE <校对规则名>];
```

+ <数据库名>：数据库名称必须符合操作系统的文件夹命名规则，注意在 MySQL 中不区分大小写。
+ IF NOT EXISTS：在创建数据库之前进行判断，只有该数据库目前尚不存在时才能执行操作。此选项可以用来避免数据库已经存在而重复创建的错误。
+ [DEFAULT] CHARACTER SET：指定数据库的默认字符集。字符集是用来定义 MySQL 存储字符串的方式。
+ [DEFAULT] COLLATE：指定字符集的默认校对规则。校对规则定义了比较字符串的方式，解决排序和字符分组的问题。

```sql
-- 判断数据库是否存在，创建名为 test_db 的数据库
CREATE DATABASE IF NOT EXISTS test_db;
-- 指定字符集和认校对规则 utf8_chinese_ci（简体中文，不区分大小写）
CREATE DATABASE IF NOT EXISTS test_db DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_chinese_ci;
```

### 查看数据库

使用 `SHOW DATABASES` 语句查看数据库。

语法：`SHOW DATABASES [LIKE '数据库名'];`

+ LIKE 从句是可选项，用于匹配指定的数据库名称。LIKE 从句可以部分匹配，也可以完全匹配。
+ 数据库名由单引号 `''` 包裹。

```sql
-- 查看所有数据库
SHOW DATABASES;
-- 使用 LIKE 从句，查看与 test_db 完全匹配的数据库：
SHOW DATABASES LIKE 'test_db';
```

### 删除数据库

使用 `DROP DATABASE` 语句删除已创建的数据库。

语法：`DROP DATABASE [IF EXISTS] <数据库名>;`

### 使用数据库

使用 `USE` 语句用来完成一个数据库到另一个数据库的跳转。

语法：`USE <数据库名>;`

## 数据表操作

### 创建数据表

使用 `CREATE TABLE` 语句创建数据表，该语句命令比较多，其主要是由表创建定义、表选项和分区选项所组成。

语法：`CREATE TABLE <表名> ([表定义选项])[表选项][分区选项];`

+ 表定义选项的格式为：`<列名1> <类型1> [,…] <列名n> <类型n>`
+ 表名不区分大小写，不能使用 SQL 语言中的关键字，如DROP、ALTER、INSERT等。
+ 表中每个列（字段）的名称和数据类型，如果创建多个列，要用逗号隔开。

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

语法：`ALTER TABLE <表名> [修改选项];`

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

```sql
-- 添加 age 字段
ALTER TABLE user_accounts ADD COLUMN age TINYINT;
```

#### 修改字段名

语法：`ALTER TABLE <表名> CHANGE COLUMN <旧字段名> <新字段名> <新数据类型>;`

```sql
-- 修改 nickname 字段名
ALTER TABLE user_accounts CHANGE COLUMN nickname username VARCHAR(25);
```

#### 修改字段数据类型

语法：`ALTER TABLE <表名> MODIFY <字段名> <数据类型>;`

```sql
-- 修改 nickname 数据类型
ALTER TABLE user_accounts MODIFY nickname VARCHAR(30);
```

#### 删除字段

语法：`ALTER TABLE <表名> DROP <字段名>;`

```sql
-- 删除 username 字段
ALTER TABLE user_accounts DROP username;
```

## 查询数据

从数据表中查询数据的基本语句为 `SELECT` 语句。

语法：

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
+ `HAVING` 用于对 `WHERE` 和 `GROUP BY` 查询出来的分组进行过滤，查出满足条件的分组结果。
+ `ORDER BY <字段>` 对查询出来的数据进行升序（ASC）或降序（DESC）排序。
+ `LIMIT` 用来设定返回的数据条数。如果是两个数字，第一个是偏移量（默认是0），第二个是返回的条数。

```sql
-- 查询所有数据
SELECT * FROM user_accounts;
-- 查询指定的字段
SELECT nickname, mobile, age FROM user_accounts;
```

### 条件查询

使用 `WHERE` 子句来指定查询条件。

语法：`WHERE <查询条件> {<判定运算1>，<判定运算2>，…}`

+ 使用 `AND` 或者 `OR` 指定一个或多个条件。
+ `WHERE` 子句的字符串比较是不区分大小写的，可以使用 `BINARY` 关键字来设定区分大小写。

```sql
-- 日期字段查询
SELECT create_at FROM user_accounts WHERE create_at < '2019-11-14';
-- 多条件查询
SELECT * FROM user_accounts WHERE age >= 18 AND sex='female';
SELECT * FROM user_accounts WHERE age < 20 OR nickname='Tom';
-- 使用 BINARY 区分大小写
SELECT nickname FROM user_accounts WHERE BINARY nickname='bob';
```

### 模糊查询

使用 `LIKE` 来替换 `=` 进行模糊查询。

语法：`<表达式1> [NOT] LIKE <表达式2>`

+ 百分号（%）：百分号匹配任何字符串，并且该字符串可以出现任意次，百分号不匹配空值。
+ 下划线（_）：下画线只匹配单个字符，而不是多个字符，也不是 0 个字符。

```sql
-- 查询所有 B 开头的昵称
SELECT nickname FROM user_accounts WHERE nickname LIKE 'B%';
-- 查询所有 m 结尾的昵称
SELECT nickname FROM user_accounts WHERE nickname LIKE '%m';
-- 查询包含字母 o 的昵称
SELECT nickname FROM user_accounts WHERE nickname LIKE '%o%';
-- 查询包含字母 o 的三个字符的昵称
SELECT nickname FROM user_accounts WHERE nickname LIKE '_O_';
```

### 别名

当表名很长或者执行一些特殊查询的时候，为了方便操作或者需要多次使用相同的表时，可以用 `AS` 关键字为表指定别名，用这个别名代替表原来的名称。

也可以指定列的别名，替换字段或表达式。

语法1：`<表名> [AS] <别名>`，`AS` 关键字为可选

语法2：`<列名> [AS] <列别名>`

```sql
-- 指定表别名
SELECT user.nickname, user.id FROM user_accounts AS user;
-- 指定列别名
SELECT nickname AS username FROM user_accounts;
```

### 分组

使用 `GROUP BY` 子句，将结果集中的数据行根据选择列的值进行逻辑分组，实现对每个组而不是对整个结果集进行整合。

语法：`GROUP BY { <列名> | <表达式> | <位置> } [ASC | DESC]`

+ `<列名>`：指定用于分组的列。可以指定多个列，彼此间用逗号分隔。
+ `<表达式>`：指定用于分组的表达式。在分组的列上我们可以使用 `COUNT`, `SUM`, `AVG` 等函数。
+ `<位置>`：指定用于分组的选择列在 `SELECT` 语句结果集中的位置，通常是一个正整数。
+ `ASC|DESC`：`ASC` 表示按升序分组（默认值），`DESC` 表示按降序分组。这两个关键字必须位于对应的列名、表达式、列的位置之后。

`GROUP_CONCAT()` 将 `GROUP BY` 产生的分组结果中同一个分组中的值连接起来，返回一个字符串结果。

```sql
-- age 按降序分组，nickname 按升序分组
SELECT age, nickname FROM user_accounts GROUP BY age DESC, nickname ASC;
-- 按 age 分组，并将同一组的昵称连接起来
SELECT age, GROUP_CONCAT(nickname) AS username FROM user_accounts GROUP BY age;
-- 按 sex 分组，并统计每组的数量
SELECT sex, COUNT(*) AS user_count FROM user_accounts GROUP BY sex;
```

### 过滤

使用 `GROUP BY` 子句分组数据后，还可以使用 `HAVING` 子句过滤分组。

语法：`HAVING <条件>`

`HAVING` 子句和 `WHERE` 子句非常相似，`HAVING` 子句支持 `WHERE` 子句中所有的操作符和语法，但是两者存在几点差异：

+ `WHERE` 子句主要用于过滤数据行，而 `HAVING` 子句主要用于过滤分组。
+ `WHERE` 子句不可以包含聚合函数，`HAVING` 子句中的条件可以包含聚合函数。
+ `WHERE` 子句会在数据分组前进行过滤，`HAVING` 子句是在数据分组后进行过滤。

```sql
-- 根据 age 分组，并过滤出 nickname 数量大于等于 2 的
SELECT age, GROUP_CONCAT(nickname) AS names FROM user_accounts GROUP BY age HAVING COUNT(nickname) >=2;
```

### 排序

使用 `ORDER BY` 子句用来进行排序。

语法：`ORDER BY {<列名> | <表达式> | <位置>} [ASC|DESC]`

+ `ORDER BY` 子句中可以包含子查询。
+ 当排序的值中存在空值时，`ORDER BY` 子句会将该空值作为最小值来对待。
+ 当在 `ORDER BY` 子句中指定多个列进行排序时，会按照列的顺序从左到右依次进行排序。

```sql
-- 默认升序排列
SELECT * FROM user_accounts ORDER BY age;
-- 降序排列
SELECT * FROM user_accounts ORDER BY id DESC;
-- 先按 age 升序排列，再根据 nickname 按降序排列
SELECT age, nickname FROM user_accounts ORDER BY age, nickname DESC;
```

### 分页

使用 `LIMT` 来限制返回的条数。

语法： `<LIMIT> [<位置偏移量>,] <行数>` 或 `LIMIT <位置偏移量> OFFSET <行数>`

`LIMIT` 接受一个或两个数字参数。参数必须是一个整数常量。

如果给定两个参数，第一个参数指定第一个返回记录行的偏移量，第二个参数指定返回记录行的最大数目。

```sql
-- 查询第一页，返回 5 条, MySQL 5.7 中可以使用 `LIMIT 0 OFFSET 5`
SELECT * FROM user_accounts LIMIT 0, 5;
```

### 去重

可以用 `DISTINCT` 关键字去重。

语法：`SELECT DISTINCT <字段名> FROM <表名>`

```sql
-- 返回的 age 字段值 不得重复
SELECT DISTINCT age FROM user_accounts;
```

### 范围查询

使用 `BETWEEN <值1> AND <值2>` 来查询某个范围内的值，该操作符需要两个参数，即范围的开始值和结束值。

```sql
SELECT * FROM user_accounts WHERE create_at BETWEEN '2019-11-11' AND '2019-11-14';
```

使用 `IN (<值1>, <值2>)` 用来指定范围，范围中的每一条，都进行匹配。

```sql
SELECT * FROM user_accounts WHERE nickname IN ('Leo', 'Bob');
```

### 正则表达式查询

使用 `REGEXP` 操作符来进行正则表达式匹配。

+ `^`：匹配字符串的开始位置。示例：`'^b'` 匹配以字母 b 开头的字符串，如：book、big、banana、 bike 等。
+ `&`：匹配字符串的结束位置。示例：`'st$'` 匹配以 st 结尾的字符串，如：test、resist、persist 等。
+ `.`：匹配除 "\n" 之外的任何单个字符单个字符。示例：`'b.t'` 匹配任何 b 和 t 之间有一个字符，如 bit、bat、but、bite 等。
+ `*`：匹配零个或多个在它前面的字符。示例：`'f*n'` 匹配字符 n 前面有任意个字符 f，如：fn、fan、faan、abcn 等。
+ `+`：匹配前面的字符 1 次或多次。示例：`'ba+'` 匹配以 b 开头，后面至少紧跟一个 a，如：ba、bay、bare、battle 等。
+ `字符串`：匹配包含指定字符的字符串。示例：`'fa'`，匹配 fan、afa、faad 等。
+ `[字符集合]`：匹配字符集合中的任何一个字符。示例：`'[xz]'`匹配 x 或者 z，如：dizzy、zebra、x-ray、 extra 等。
+ `[^]`：匹配不在括号中的任何字符。示例：`'[^abc]'` 匹配任何不包 含 a、b 或 c 的字符串，如： desk、fox、f8ke 等。
+ `字符串{n,}`：匹配前面的字符串至少 n 次。示例：`b{2}` 匹配 2 个或更多的 b，如：bbb、 bbbb、 bbbbbbb 等。
+ `字符串{n,m}`：匹配前面的字符串至少 n 次， 至多 m 次。示例：`b{2,4}` 匹配最少 2 个，最多 4 个 b。如：bbb、 bbbb 等。
+ `p1|p2|p3`：匹配 p1 或 p2 或 p3。示例：`'(z|f)ood'` 匹配 zood 或 food。

```sql
-- 查询以 L 开头的 nickname
SELECT * FROM user_accounts WHERE nickname REGEXP '^L';
-- 查询以 L 或 B开头，或者以 y 结尾的 nickname
SELECT * FROM user_accounts WHERE nickname REGEXP '^[LB]|y$';
-- 查询至少包含一个 o 的 nickname
SELECT * FROM user_accounts WHERE nickname REGEXP 'o+';
```

### 联合查询

`UNION` 操作符用于连接两个以上的 `SELECT` 语句的结果组合到一个结果集合中。多个 `SELECT` 语句会删除重复的数据。

语法：

```sql
SELECT {* | <字段名>} FROM <表名> [WHERE conditions]
UNION [ALL | DISTINCT]
SELECT{* | <字段名>} FROM <表名> [WHERE conditions];
```

+ `DISTINCT`: 可选，删除结果集中重复的数据。默认情况下 `UNION` 操作符已经删除了重复数据，所以 `DISTINCT` 修饰符对结果没影响。
+ `ALL`: 可选，返回所有结果集，包含重复数据。

```sql
-- 联合查询 user_accounts 表的 nickname 和 users 表的 name，并全部返回
SELECT nickname FROM user_accounts UNION ALL SELECT name AS nickname FROM users;
```

### 子查询

子查询指一个查询语句嵌套在另一个查询语句内部的查询，在 `SELECT` 子句中先计算子查询，子查询结果作为外层另一个查询的过滤条件，查询可以基于一个表或者多个表。

子查询中常用的操作符有 `ANY（SOME）`、`ALL`、`IN` 和 `EXISTS`。

子查询可以添加到 `SELECT``、UPDATE` 和 `DELETE` 语句中，而且可以进行多层嵌套。子查询也可以使用比较运算符，如`<`、`<=`、`>`、`>=`、`!=`等。

### 连接查询

使用 `JOIN` 把来自两个或多个表的行结合起来。即先确定一个主表作为结果集，然后，把其他表的行有选择性地连接在主表结果集上。

#### 内连接

内连接是系统默认的表连接，所以在 `FROM` 子句后可以省略 `INNER` 关键字，只用关键字 `JOIN`。

使用内连接后，`FROM` 子句中的 `ON` 子句可用来设置连接表的条件。

语法：`SELECT <列名1，列名2 …> FROM <表名1> INNER JOIN <表名2> [ ON子句]`

+ 先确定主表，使用 `FROM <表名1>`，再确定需要连接的表，使用 `INNER JOIN <表名2>`。
+ 确定连接条件，使用 `ON <条件...>`。
+ 可再加上 `WHERE`、`ORDER BY` 等子句。

```sql
-- 用户表中只有 公司 id 没有公司名，通过用户表的 公司 id 在公司表中找到对应的公司名。
SELECT u.id, u.nickname, c.name AS company_name FROM user_accounts u INNER JOIN company c ON u.c_id = c.id;
```

`INNER JOIN` 只返回同时存在于两张表的行数据。

#### 左连接

左连接使用关键字 `LEFT OUTER JOIN` 或者 `LEFT JOIN`。

从左表返回所有的行，即使右表中没有匹配。如果右表中没有匹配，则结果为 NULL，即左连接的结果集中的 NULL 值表示右表中没有找到与左表相符的记录。

```sql
SELECT u.id, u.nickname, c.name AS company_name FROM user_accounts u LEFT JOIN company c ON u.c_id = c.id;
```

#### 右连接

右连接使用关键字 `RIGHT OUTER JOIN` 或者 `RIGHT JOIN`。

从右表返回所有的行，即使左表中没有匹配。如果左表中没有匹配，则结果为 NULL。

```sql
SELECT u.id, u.nickname, c.name AS company_name FROM user_accounts u RIGHT JOIN company c ON u.c_id = c.id;
```

## 新增/修改/删除数据

### 新增数据

使用 `INSERT` 语句向数据库已有的表中插入一行或者多行元组数据。有两种语法形式，分别是 `INSERT VALUES` 和 `INSERT SET`。

语法1：`INSERT INTO <表名> (<列名1> … , <列名n> ) VALUES (<值1>, … , <值n>);`

语法2：`INSERT INTO <表名> SET <列名1> = <值1>, <列名2> = <值2>, … ;`

若向表中的所有列插入数据，直接采用 `INSERT <表名> VALUES (<值1>, … , <值n>);` 即可，所以的列名均可省略。

使用 `INSERT INTO ... SELECT ... FROM ...` 语句可以从一个或多个表中取出数据，并将这些数据作为行数据插入表中。

```sql
-- 使用 insert valus 插入数据
INSERT INTO user_accounts (nickname, mobile) VALUES ('leo', 15522223333);
-- 使用 insert set 插入数据
INSERT INTO user_accounts SET nickname = 'Bob', mobile = 13877779999;
-- 从 users 表中查询数据，并将值插入 user_accounts 表
INSERT INTO user_accounts (nickname, mobile) SELECT nickname, mobile FROM users;
```

### 修改数据

使用 `UPDATE` 语句来修改、更新一个或多个表的数据。

语法： `UPDATE <表名> SET 字段1=值1 [, 字段2=值2, … ] [WHERE 子句 ][ORDER BY 子句] [LIMIT 子句];`

+ `SET 子句`：用于指定表中要修改的列名及其列值。其中，每个指定的列值可以是表达式，也可以是该列对应的默认值。如果指定的是默认值，可用关键字 `DEFAULT` 表示列值。
+ `WHERE 子句`：可选项。用于限定表中要修改的行。若不指定，则修改表中所有的行。
+ `ORDER BY 子句`：可选项。用于限定表中的行被修改的次序。
+ `LIMIT` 子句：可选项。用于限定被修改的行数。

```sql
UPDATE user_accounts SET age = 17 WHERE id = 1;
```

### 删除数据

使用 `DELETE` 语句来删除表的一行或者多行数据。

语法：`DELETE FROM <表名> [WHERE 子句] [ORDER BY 子句] [LIMIT 子句]`

+ `ORDER BY 子句`：可选项。表示删除时，表中各行将按照子句中指定的顺序进行删除。
+ `WHERE 子句`：可选项。表示为删除操作限定删除条件，若省略该子句，则代表删除该表中的所有行。
+ `LIMIT 子句`：可选项。用于告知服务器在控制命令被返回到客户端前被删除行的最大值。

```sql
DELETE FROM user_accounts WHERE id = 1;
-- 删除所有
DELETE FROM user_accounts;
```
