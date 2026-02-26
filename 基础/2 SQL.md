一、SQL通用语法
  1.SQL语句可以单行或多行书写，以分号结尾
  2.SQL语句可以使用空格或缩进来增强语句的可读性
  3.MySQL数据库的==SQL语句不区分大小写==，关键字建议使用大写
  4.注释：
    a.单行注释：`--注释内容`  或  `#注释内容`（MySQL特有）
    b.多行注释：`/*注释内容*/`


二、SQL分类

| 分类  | 全称                         | 说明                          |
| --- | -------------------------- | --------------------------- |
| DDL | Date Definition Language   | 数据定义语言，用来定义数据库对象（数据库，表，字段）  |
| DML | Date Mainpulation Language | 数据操作语言，用来对数据库表中的数据进行增删改     |
| DQL | Date Query Language        | 数据查询语言，用来查询数据库中表的记录         |
| DCL | Date Control Language      | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |


三、DDL-数据库操作
  1.查询
    查询所有数据库
    `SHOW DATABASES;`
    查询当前数据库
    `SELECT DATABASE();`
  2.创建
    `CREATE DATABASE[IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];`
  3.删除
    `DROP DATABASE[IF EXISTS] 数据库名;`
  4.使用
    `USE 数据库名;`

四、DDL-表操作
  1.查询
    查询当前数据库所有表
    `SHOW TABLES;`
    查询表结构
    `DESC 表名;`
    查询指定表的建表语句
    `SHOW CREATE TABLE 表名;`
  2.创建
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