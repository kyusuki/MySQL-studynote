### 一、SQL通用语法
  1. `SQL`语句可以单行或多行书写，以分号结尾
  2. `SQL`语句可以使用空格或缩进来增强语句的可读性
  3. `MySQL`数据库的==SQL语句不区分大小写==，关键字建议使用大写
  4. 注释：
    a.单行注释：`--注释内容`  或  `#注释内容`（`MySQL`特有）
    b.多行注释：`/*注释内容*/`

### 二、SQL分类

| 分类  | 全称                         | 说明                          |
| --- | -------------------------- | --------------------------- |
| DDL | Date Definition Language   | 数据定义语言，用来定义数据库对象（数据库，表，字段）  |
| DML | Date Mainpulation Language | 数据操作语言，用来对数据库表中的数据进行增删改     |
| DQL | Date Query Language        | 数据查询语言，用来查询数据库中表的记录         |
| DCL | Date Control Language      | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |




# DDL
### 一、DDL-数据库操作
  1. 查询
    查询所有数据库
    `SHOW DATABASES;`
    查询当前数据库
    `SELECT DATABASE();`
  2. 创建
    `CREATE DATABASE[IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];`
  3. 删除
    `DROP DATABASE[IF EXISTS] 数据库名;`
  4. 使用
    `USE 数据库名;`

### 二、DDL-表操作
  1. 查询
    查询当前数据库所有表
    `SHOW TABLES;`
    查询表结构
    `DESC 表名;`
    查询指定表的建表语句
    `SHOW CREATE TABLE 表名;`
  2. 创建
```
    CREATE TABLE 表名(
        字段1 字段1类型 [COMMENT 字段1注释],
        字段2 字段2类型 [COMMENT 字段2注释],
        字段3 字段3类型 [COMMENT 字段3注释],
        ...
        字段n 字段n类型 [COMMENT 字段n注释]
    ) [COMMENT 表注释];
    
    注：[...]为可选参数，最后一个字段后面没有逗号，实际书写中没有中括号
```

### 三、DDL-表操作-数据类型
  `MySQL`中的数据类型有很多，主要分为三类：数值类型、字符串类型、日期时间类型
  1. 数值类型

| 类型                        | 大小      | 描述         |
| ------------------------- | ------- | ---------- |
| TINYINT / tinyint         | 1 byte  | 小整数值       |
| SMALLINT / smallint       | 2 bytes | 大整数值       |
| MEDIUMINT / mediumint     | 3 bytes | 大整数值       |
| INT或INTEGER / int或integer | 4 bytes | 大整数值       |
| BIGINT / bigint           | 8 bytes | 极大整数值      |
| FLOAT / float             | 4 bytes | 单精度浮点数值    |
| DOUBLE / double           | 8 bytes | 双精度浮点数值    |
| DECIMAL / decimal         |         | 小数值（精确定点数） |
  例：`score double(4,1)` 前面的4表示整个数的长度，后面的1表示保留位数

2. 字符串类型

| 类型                      | 大小（bytes）    | 描述              |
| ----------------------- | ------------ | --------------- |
| CHAR / char             | 0-255        | 定长字符串           |
| VARCHAR / varchar       | 0-65535      | 变长字符串           |
| TINYBLOB / tinyblob     | 0-255        | 不超过255个字符的二进制数据 |
| TINYTEXT / tinytext     | 0-255        | 短文本字符串          |
| BLOB / blob             | 0-65535      | 二进制形式的长文本数据     |
| TEXT / text             | 0-65535      | 长文本数据           |
| MEDIUMBLOB / mediumblob | 0-1677215    | 二进制形式的中等长度文本数据  |
| MEDIUMTEXT / mediumtext | 0-1677215    | 中等长度文本数据        |
| LONGBLOB / longblob     | 0-4294967295 | 二进制形式的极大文本数据    |
| LONGTEXT / longtext     | 0-4294967295 | 极大文本数据          |
  例：`char(10)`表示定长为10的字符串，即使只含有一个字符，其他位置用空格表示，性能高
  例：`varchar(10)`表示变长的字符串，会自动计算使用空间，性能较差

  3. 日期类型

| 类型        | 大小(bytes) | 格式                  | 描述           |
| --------- | --------- | ------------------- | ------------ |
| DATE      | 3         | YYYY-MM-DD          | 日期值          |
| TIME      | 3         | HH:MM:SS            | 时间值或持续时间     |
| YEAR      | 1         | YYYY                | 年份值          |
| DATETIME  | 8         | YYYY-MM-DD HH:MM:SS | 混合日期和时间值     |
| TIMESTAMP | 4         | YYYY-MM-DD HH:MM:SS | 混合日期和时间值，时间戳 |

### 四、DDL-表操作-修改
  1. 添加字段
    `ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释][约束];`
  2. 修改字段
    修改数据类型
    `ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);`
    修改字段名和字段类型
    `ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释][约束];`
  3. 删除字段
    `ALTER TABLE 表名 DROP 字段名;`
  4. 修改表名
    `ALTER TABLE 表名 RENAME TO 新表名;`

### 五、DDL-表操作-删除
  1. 删除表
    `DROP TABLE[IF EXISTS] 表名;`
  2. 删除指定表，并重新创建该表（数据全无，已不是原来的表）
    `TRUNCATE TABLE 表名;`




# DML
### 一、DML-添加数据
  1. 给指定字段添加数据
    `INSERT INTO 表名(字段名1,字段名2,...) VALUES(值1,值2,...);`
  2. 给全部字段添加数据
    `INSERT INTO 表名 VALUES(值1,值2,...);`
  3. 批量添加数据
    `INSERT INTO 表名(字段名1,字段名2,...) VALUES(值1,值2,...),(值1,值2,...),(值1,值2,...);`
    `INSERT INTO 表名 VALUES(值1,值2,...),(值1,值2,...),(值1,值2,...);`
  4. 注意：
    a.插入数据时，指定的字段顺序要与值的顺序是一一对应的
    b.字符串和日期型数据应该包含在引导中
    c.插入的数据大小，应该在字段的规定范围内

### 二、DML-修改数据
  1. 修改数据
	`UPDATE 表名 SET 字段名1=值1,字段名2=值2,...[WHERE 条件];`
  注意：修改语句的条件可以有，也可以没有。如果没有条件，则会修改整张表的所有数据
  2. 删除数据
    `DELETE FROM 表名 [WHERE 条件];`
  注意：
    DELETE语句的条件可以有，也可以没有。如果没有条件，则会删除整张表的所有数据
    DELETE语句不能删除某一个字段的值（可以是使用UPDATE）




# DQL
### 一、DQL-基本查询
  1. 查询多个字段
    `SELECT 字段1,字段2,字段3... FROM 表名;`
    `SELECT * FROM 表名;`
  2. 设置别名
    `SELECT 字段1 [AS 别名1],字段2 [AS 别名2]... FROM 表名;`
  3. 去除重复记录
    `SELECT DISTINCT 字段列表 FROM 表名;`

### 二、DQL-条件查询
  1. 语法
    `SELECT 字段列表 FROM 表名 WHERE 条件列表;`
  2. 条件

| 比较运算符         | 功能                        |
| ------------- | ------------------------- |
| > >= < <= =   |                           |
| <>或!=         | 不等于                       |
| BETWEEN...AND | 在某个范围之内（含最小、最大值，且不能反写）    |
| IN(...)       | 在in之后的列表中的值，多选一           |
| LIKE 占位符      | 模糊匹配（ _ 匹配单个字符，% 匹配任意个字符） |
| IS NULL       | 是NULL                     |

| 逻辑运算符     | 功能             |
| --------- | -------------- |
| AND 或 &&  | 并且（多个条件同时成立）   |
| OR 或 \|\| | 或者（多个条件任意一个成立） |
| NOT 或 !   | 非，不是           |

### 三、DQL-聚合函数
  1. 说明
    将==一列==数据作为一个整体，进行纵向计算，不会计算`null`值
  2. 常见聚合函数

| 函数    | 功能   |
| ----- | ---- |
| COUNT | 统计数量 |
| MAX   | 最大值  |
| MIN   | 最小值  |
| AVG   | 平均值  |
| SUM   | 求和   |
  3. 语法
    `SELECT 聚合函数(字段列表) FROM 表名 [WHERE 条件];`

### 四、DQL-分组查询
  1. 语法
    `SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];`
  2. `where`与`having`区别
    a.执行时机不同：`where`是分组之前进行过滤，不满足`where`条件，不再参与分组；`having`是分组之后对结果进行过滤
    b.判断条件不同：`where`不能对聚合函数进行判断，而`having`可以
  3. 注意
    a.执行顺序：`where`>聚合函数>`having`
    b.分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

### 五、DQL-排序查询
  1. 语法
    `SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1,字段2 排序方式2...;`
  2. 排序方式
    `ASC`：升序（默认）
    `DESC`：降序
  3. 注意
    如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序

### 六、DQL-分页查询
  1. 语法
    `SELECT 字段列表 FROM 表名 LIMIT 起始索引,查询记录数;`
  2. 注意
    a.起始索引从0开始，起始索引=（查询页码-1）* 每页显示记录数
    b.分页查询是数据库的方言，不同的数据库有不同的实现，`MySQL`中`LIMIT`
    c.如果查询的是第一页数据，起始索引可以省略，直接简写为`limit 10`

### 七、DQL-执行顺序
  `FROM`>`WHERE`>`GROUP BY`>`HAVING`>`SELECT`>`ORDER BY`>`LIMIT`




# DCL
### 一、DCL-管理用户
  1. 查询用户
    `USE mysql;`
    `SELECT * FROM user;`
  2. 创建用户
    `CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`
  3. 修改用户密码
    `ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';`
  4. 删除用户
    `DROP USER '用户名'@'主机名';`
  5. 注意
    a.主机名可以使用%通配，指定用户可以通过任意主机访问服务器
    b.管理用户主要对于`DBA`（数据库管理员）使用

### 二、DCL-权限控制
  1. 常用权限

| 权限                 | 说明         |
| ------------------ | ---------- |
| ALL，ALL PRIVILEGES | 所有权限       |
| SELECT             | 查询数据       |
| INSERT             | 插入数据       |
| UPDATE             | 修改数据       |
| DELETE             | 删除数据       |
| ALTER              | 修改表        |
| DROP               | 删除数据库/表/视图 |
| CREATE             | 创建数据库/表    |
  2. 查询权限
    `SHOW GRANTS FOR '用户名'@'主机名';`
  3. 授予权限
    `GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`
  4. 撤销权限
    `REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';`
  5. 注意
    a.多个权限之间，使用逗号分隔
    b.授权时，数据库名和表名可以使用 * 进行通配，代表所有