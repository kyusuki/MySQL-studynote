### 一、插入数据
  1. `insert`插入数据
    a.批量插入
        `insert into 表名 values(...);`
    b.主键顺序插入
    c.手动提交事务
  ```
       start transaction;
       insert into 表名 values(...);
       insert into 表名 values(...);
       ...
       commit;
  ```
  2. 大批量插入数据
    如果一次性需要插入大批量数据，使用`insert`语句插入性能较低，此时可以使用`MySQL`数据库提供的`load`指令进行插入
```
   #客户端连接服务端时，加上参数 --local-infile
   mysql --local-infile -u root -p
   #设置全局参数local_infile，开启从本地加载文件导入数据的开关
   set global local_infile=1;
   #执行load指令将准备好的数据，加载到表结构中
   load data local infile '文件路径' into table '表名' fields terminated by ',' lines terminated by '\n';
```

### 二、主键优化
  1. 数据组织方式
    在`InnoDB`存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式称为索引组织表
  2. 页分裂
    页可以为空，也可以填充一半，也可以填满。每个页包含了2~n行数据（如果一行数据过大，会出现行溢出），根据主键排列
    两种现象：
        主键顺序插入：
        主键乱序插入（可能发生页分裂现象）：
  3. 页合并
    当删除一行记录时，实际上记录并没有被物理删除，只是该记录被标记为删除，并且它的空间变得允许被其他记录声明使用。
    当页中删除的记录达到`MERGE_THRESHOLD`（默认为页的50%），`InnoDB`会开始寻找最靠近的页（前或后）是否可以将两个页合并并以优化空间使用
  4. 主键设计原则
    a.满足业务需求的情况下，尽量降低主键的长度
    b.插入数据时，尽量选择顺序插入，选择使用`auto_increment`自增管理
    c.尽量不要使用`UUID`做主键或者是其他自然主键
    d.业务操作时，避免对主键的修改

### 三、order by优化
  1. `Using filesort`：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区`sort buffer`中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫`FileSort`排序
  2. `Using index`：通过有序索引顺序扫描直接返回有序数据，这种情况即为`using index`，不需要额外排序，操作效率高
  3. `order by`优化原则
    a.根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
    b.尽量使用覆盖索引
    c.多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（`ASC/DESC`）
    d.若不可避免的出现`filesort`，大量数据排序时，可以适当增大排序缓冲区大小`sort_buffer_size`

### 四、group by优化
  1. `group by`优化原则
    a.在分组操作时，可以通过索引来提高效率
    b.分组操作时，索引的使用也是满足最左前缀法则

### 五、limit优化
  1. 常见问题就是如果`limit 200000,10`，此时需要`MySQL`排序前`200010`记录，仅仅返回`200000-200010`的记录，其余记录丢弃，查询排序的代价非常大
  2. 主键/索引分页
    慢写法
    `select * from user order by id limit 200000,10;`
    优化写法
    `select * from user where id>200000 order by id limit 10;`
  3. 延迟关联
    慢写法
    `select * from user order by id limit 500000,10;`
    优化写法
    `select u.* from user as u,(select id from user order by id limit 500000,10) as a where u.id=a.id;`

### 六、count优化
  1. `MyISAM`引擎将一个表的总行数存在磁盘中，因此执行`count(*)`的时候会直接返回这个数，效率更高
  2. `InnoDB`引擎相对于更加麻烦，其执行`count(*)`的时候，需要将数据一行一行地从引擎里面读出来，然后累积计数
  3. `count`的几种用法
    a.`count()`是一种集合函数，对于返回的结果集，一行行地判断，若`count`函数的参数不是`NULL`，累计值就加1，否则不加，最后返回累计值
    b.用法：`count(*)`、`count(主键)`、`count(字段)`、`count(1)`
  4. `count(*)`
    `InnoDB`引擎并不会将全部字段取出来，而是专门做了优化，不取值，服务器直接按行进行累加
  5. `count(主键)`
    `InnoDB`引擎会遍历整张表，将每一行的主键`id`值都取出来，返回给服务器。服务器拿到主键后，直接按行进行累加（主键不可能为`null`）
  6. `count(字段)`
    没有`not null`约束：`InnoDB`引擎会遍历整张表将每一行的字段值都取出来，返回给服务器，服务器判断是否为`null`，若不为`null`，计数累加
    有`not null`约束：`InnoDB`引擎会遍历整张表将每一行的字段值取出来返回给服务器，直接按行进行累加
  7. `count(1)`
    `InnoDB`引擎遍历整张表，但不取值。服务器对于返回的每一行，放一个数字“1”进去，直接按行进行累加

### 七、update优化
  1. `InnoDB`的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级为表锁
  2. `update`优化原则
    a.`where`条件必须命中索引
    b.禁止批量大范围更新
    c.尽量不更新索引字段
    d.不要`update set`无用字段
    e.大事务拆小事务
    f.关联表`update`优化
    g.避免更新主键、聚簇索引