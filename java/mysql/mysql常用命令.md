## mysql常用命令

- MySQL常用语句
  1. 表（或者数据库）的CRUD
  2. 表数据的CRUD，其中表数据查询使用最多，也更复杂。查询可以按照单表还是多表可以分为：单表SELECT查询和多表的联结查询（INNER JOIN, LEFT JOIN, RIGHT JOIN和FULL JOIN）以及组合查询UNION和UNION ALL
  3. SQL语句中各个关键字的执行顺序
- MySQL的高级功能
  1. 存储过程
  2. 事务处理
  3. 触发器

# 1. 表(或数据库)操作语句

## 1.1. 查询表（或数据库）

1. **获取所有可用的数据库**：`SHOW DATABASES`；
2. **选择数据库**：`USE customers`；
3. 用于显示数据库服务器的状态信息：`SHOW STATUS`；
4. 用来显示授权用户的安全权限：`SHOW GRANTS`；
5. 用来显示数据库服务器或警告信息：`SHOW ERRORS` 或者 `SHOW WARNINGS`；
6. **用于显示创建数据库时的创建语句**：`SHOW CREATE DATABASE customers`；
7. **用于显示创建表时的创建语句**：`SHOW CREATE TABLE customers`；
8. **获取当前所选的数据库中所有可用的表**：`SHOW TABLES`；
9. **获取表中所有列的信息**：`SHOW COLUMNS FROM tableName`；同时DESCRIBE语句有相同的效果：`DESCRIBE tableName`；

## 1.2. 新建表（或）数据库

1. **新建数据库**：`CREATE DATABASE customers`;

2. **创建表可以使用CREATE TABLE语句**:

   ```sql
    CREATE TABLE customers(
      cust_id INT NOT NULL AUTO_INCREMENT,
      cust_name CHAR(50) NOT NULL,
      cust_age INT NULL DEFAULT 18,
      PRIMARY KEY(cust_id)
    )ENGINE=INNODB;
   ```

   有这样一些细节：

   1. 允许**NULL**值，则说明在插入行数据时允许不给出该列的值，而**NOT NULL**则表示在插入或者更新该列数据，必须明确给出该列的值；
   2. **DEFAULT**表示该列的默认值，在插入行数据时，若没有给出该列的值就会使用其指定的默认值；
   3. **PRIMARY KEY**用于指定主键，主键可以指定一列数据，而可以由多列数据组合构成，如`PRIMARY KEY(cust_id,cust_name)`；
   4. **ENGINE**用于指定引擎类型。常见的引擎类型有这些：（1）InnoDB是一个支持可靠的事务处理的引擎，但是不支持全文本搜索；（2）MyISAM是一个性能极高的引擎，它支持全文本搜索，但是不支持事务处理；（3）MEMORY在功能上等同于MyISAM，但由于数据存储在内存中，速度很快（特别适合于临时表）；

3. 在创建表的时候可以使用**FOREIGN KEY**来创建外键，即一个表中的FOREIGN KEY指向另一个表中PRIMARY KEY。外键FOREIGN KEY用于约束破坏表的联结动作，保证两个表的数据完整性。同时也能防止非法数据插入外键列，因为该列值必须指向另一个表的主键。实例为：

   ```sql
    CREATE TABLE Orders
    (
    Id_O int NOT NULL,
    OrderNo int NOT NULL,
    Id_P int,
    PRIMARY KEY (Id_O),
    FOREIGN KEY (Id_P) REFERENCES Persons(Id_P)
    )
   ```

## 1.3 删除表（或数据库）

1. **删除数据库**：`DROP DATABASE customers`；
2. **删除表**，使用DROP TABLE子句：`DROP TABLE customers`。

## 1.4 更新表

1. 更新表结构信息可以使用**ALTER TABLE**子句，如为表增加一列：`ALTER TABLE vendors ADD vend_name CHAR(20)`；另外经常用于定义外键，如：

   ```sql
    ALTER TABLE customers 
    ADD CONSTRAINT fk_custormer_orders
    FOREIGN KEY(cust_id) REFERENCES orders (order_cust)
   ```

2. **重命名表，使用RENAME子句**。`RENAME TABLE backup_customers TO customers, backup_vendors TO vendors`；更改多个表名，之间用逗号间隔

# 2 表数据操作语句

## 2.1 查询表数据

> **基本查询语句**

1. 根据过滤条件查询表中的单列或者多列或者全部列的信息**SELECT FROM WEHERE**：`SELECT cust_id,cust_name FROM customers WHERE cust_id=10086`；其中过滤条件操作符有：=，<>,!=,<,<=,>,>=,BETWEEN AND,IS NULL；
2. 为查询出的某一列信息去重**DISTINCT**：`SELECT DISTINCT cust_name FROM customers`；
3. 限制单列查询结果的行数：`SELECT cust_name FROM customers LIMIT 5`；LIMIT后跟一个数值，表示从第0行开始取，共取5行数据；如果LIMIT 5，5表示从第5行（数据库中实际第6行记录）开始取，共取5行数据。注意：数据是从第0行开始计数的；
4. **ORDER BY**子句取一个或者多个列，据此对输出进行排序：`SELECT cust_id,cust_name FROM customers ORDER BY cust_id DESC, cust_name`;
5. **IN**操作符用来指定条件范围，范围中的每个条件都可以进行匹配：`SELECT cust_id, cust_name FROM customers WHERE cust_id IN (1000,2000)`。另外，NOT操作符可以和IN操作符配合使用，用于表示检索出不符合条件的所有数据；
6. **LIKE**操作符用来表明模糊查询，与之配合使用的通配符有**%**，%表示任何字符出现任何次数；`_`，`_`表示只能匹配一个字符：`SELECT cust_id,cust_name FROM customers WHERE cust_name LIKE '%happy%'`；
7. 使用分组查询并可以满足一定的分组过滤条件**GROUP BY HAVING**。如检索总计订单金额大于等于50的订单号和订单总金额，并按总金额进行排序：`SELECT order_num,SUM(quantity*item_price) AS order_total FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price)>=50 ORDER BY order_total`
8. **WHERE和HAVING的比较**。WHERE是行级过滤，而HAVING是组级过滤。被WHERE过滤掉的数据不会出现在分组中。WHERE中通配符以及多个WHERE子句的连接同样适用于HAVING子句；
9. GROUP BY的使用注意事项： （1）GROUP BY子句中可以嵌套分组（即通过多个列进行分组`GROUP BY cust_id, cust_name`），但是进行数据汇总时，是在最后规定的分组上进行；（2）GROUP BY子句中列出的每个列都必须是检索列或者是有效的表达式。（3）如果有NULL值，将值NULL作为一个分组进行返回，如果有多行NULL值，它们将分为一组
10. 嵌套其他查询中的查询，称之为**子查询**。执行过程由里向外，里层查询结果作为外层查询的条件：`SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'happy')`。当然，多表的查询可以是用联结查询。

> **联结查询**

1. 内联结用又称之为内部联结，是基于两个表 之间的的相等测试。如果不加过滤条件，会造成“笛卡尔积”。`SELECT vend_name,prod_name,prod_price FROM vendors INNER JOIN products ON vendors.vend_id=products.vend_id`；同样可以使用WHERE进行多表联结查询，但是更推荐使用INNER JOIN等联结方式；
2. 外部联结包括左外联结**LEFT JOIN**和右外联结**RIGHT JOIN**和全连接**FULL JOIN**。例如查询每个客户的订单数：`SELECT customers.cust_id,orders.orders_num FROM customers LEFT JOIN orders ON orders.cust_id =customers.cust_id`；LEFT JOIN 会全部返回左表数据，RIGHT JOIN会全部返回右表数据，FULL JOIN会将左右两个表的数据全部返回；
3. 联结查询与聚集函数一起使用。如查询每个客户的订单数：`SELECT customers.cust_name,customers.cust_id,COUNT(orders.order_num) AS num_ord FROM customers INNER JOIN orders ON customers.cust_id=orders.cust_id GROUP BY customers.cust_id`；

> **组合查询**

1. 多个查询（SELECT）可以使用UNION将多个查询结果进行合并成一个结果集返回，**UNIO**N必须包含两个及两个以上的SELECT查询，并且每个传必须包含相同的列、表达式或聚集函数，数据类型不必完全相同，MySQL会进行隐式的类型转换。`SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price>5 UINON SELECT vend_id,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002)`;
2. **UNION**返回的是去重后的结果，如果不需要去重则可以使用**UNION ALL**；
3. 可以多组合查询使用ORDER BY进行排序，但是是针对的最终的结果集进行排序，而不是其中单个SELECT查询进行排序，因此对于组合查询来说ORDER BY子句只有一个。`SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price>5 UINON SELECT vend_id,prod_id,prod_price FROM products WHERE vend_id IN (1001,1002) ORDER BY vend_id`

> **使用函数对数据进行处理**

1. 拼接列名：`SELECT Concat (vendName,'(',vendCountry,')') FROM vendors ORDER BY vendName`；
2. 执行算术表达式计算：`SELECT prodId, quantity,price, quantity`*`price AS expandedPrice FROM orderItems`；
3. 文本处理函数如Upper(),LTrim(),RTrim()等函数。比如使用Upper函数将文本转换成大写：`SELECT vendName, Upper(vendName) FROM vendors ORDER BY vendName`；
4. 时间和日期处理函数，如Date(),Day()等。`SELECT custId, orderNum FROM orders WHERE Date(orderDate)='2015-09-01'`；
5. 数值处理函数，如Abs(),Cos()等；
6. 常用的聚集函数。如AVG(),COUNT(),MAX(),MIN()以及SUM()。`SELECT COUNT(*) AS numbers, MIN(prod_price) AS price_min, MAX(prod_price) AS price_max,AVG(prod_price) AS price_avg FROM products`；

## 2.2 插入表数据

1. 向表中插入行数据可以使用INSERT INTO子句，更安全的方式是指定列名。`INSERT INTO customers (cust_name, cust_email) VALUES('happy','happy@gmail.com')`；在INSERT INTO子句中能够省略列名的前提条件是：该列可以允许定义为NULL值或者在定义该列时给出去了默认值；
2. 如果插入多行数据可以将多组值用逗号进行分隔即可。`INSERT INTO customers (cust_name, cust_email) VALUES('happy','happy@gmail.com'),('smart','smart@gmail.com')`；
3. 将查询出来的数据插入表中，可以使用INSERT SELECT语句。`INSERT INTO customers(cust_id,cust_contact) SELECT cust_id, cust_contact FROM customers WHERE cust_id>5`；其中SELECT中可以带WHERE过滤条件；INSERT SELECT通常被用于复制表数据

## 2.3 更新表数据

1. 如果要更新表数据的话，使用UPDATE子句：`UPDATE customers SET cust_name ='happy',cust_email='happy@gmail.com' WHERE cust_id = 1001`；
2. **注意：如果不加WHERE条件指定到某一行的话，会更新表中某一列全部的数据**。

## 2.4 删除表数据

1. 如果从表中删除数据的话，可以使用DELETE子句。`DELETE FROM customers WHERE cust_id = 10086`；删除的数据必定是表中行数据，而不是某一列。因此，与UPDATE子句相比，DELETE子句并不需要指定是哪一列，而仅仅只需要指定具体的表名即可；
2. **注意：如果不添加WHERE指定条件的话，会将整个表中所有行数据全部删除。另外，DELETE只是删除表中的数据，而不会删除表结构信息；**
3. 如果想删除表中全部的数据，可以使用**TRUNCATE**，比DELETE删除效率更高；

# 3.创建索引的三种方式

- 第一种方式：在执行CREATE TABLE时创建索引

  ```sql
  CREATE TABLE user_index2 ( id INT auto_increment PRIMARY KEY, first_name VARCHAR (16), last_name VARCHAR (16), id_card VARCHAR (18), information text, KEY name (first_name, last_name), FULLTEXT KEY (information), UNIQUE KEY (id_card) );
  ```

- 第二种方式：使用ALTER TABLE命令去增加索引

  ```pgsql
  ALTER TABLE table_name ADD INDEX index_name (column_list);
  ```

  - ALTER TABLE用来创建普通索引、UNIQUE索引或PRIMARY KEY索引。
  - 其中table_name是要增加索引的表名，column_list指出对哪些列进行索引，多列时各列之间用逗号分隔。
  - 索引名index_name可自己命名，缺省时，MySQL将根据第一个索引列赋一个名称。另外，ALTER TABLE允许在单个语句中更改多个表，因此可以在同时创建多个索引。

- 第三种方式：使用CREATE INDEX命令创建

  ```pgsql
  CREATE INDEX index_name ON table_name (column_list);
  ```

  - CREATE INDEX可对表增加普通索引或UNIQUE索引。（但是，不能创建PRIMARY KEY索引）

#### 如何删除索引

- 根据索引名删除普通索引、唯一索引、全文索引：`alter table 表名 drop KEY 索引名`

  ```sas
  alter table user_index drop KEY name;
  alter table user_index drop KEY id_card;
  alter table user_index drop KEY information;
  ```

- 删除主键索引：`alter table 表名 drop primary key`（因为主键只有一个）。这里值得注意的是，如果主键自增长，那么不能直接执行此操作（自增长依赖于主键索引）：



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6ef8bc6a4~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 需要取消自增长再行删除：

  ```pgsql
  alter table user_index
  -- 重新定义字段
  MODIFY id int,
  drop PRIMARY KEY
  ```

- 但通常不会删除主键，因为设计主键一定与业务逻辑无关。

