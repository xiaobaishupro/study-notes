### Mysql理解

#### 1.mysql常用命令

1. 连接数据库：mysql -u username -p password -h hostname dbname
2. 显示数据库列表：show databases;
3. 创建数据库：create database dbname;
4. 删除数据库：drop database dbname;
5. 选择数据库：use dbname;
6. 显示数据表列表：show tables;
7. 创建数据表：create table tablename (column1 datatype, column2 datatype, …);
8. 删除数据表：drop table tablename;
9. 插入数据：insert into tablename (column1, column2, …) values (value1, value2, …);
10. 更新数据：update tablename set column1=value1, column2=value2, … where condition;
11. 删除数据：delete from tablename where condition;
12. 查询数据：select column1, column2, … from tablename where condition;
13. 排序数据：select column1, column2, … from tablename order by column1 asc/desc;
14. 分组数据：select column1, count(*) from tablename group by column1;
15. 连接查询：select column1, column2, … from tablename1 inner join tablename2 on tablename1.column1=tablename2.column1;
16. 改变表结构：alter table tablename add column columnname datatype;
17. 查看表结构：describe tablename;

#### 2.mysql三大范式

- 第一范式：每个列都不可以再拆分。
- 第二范式：在第一范式的基础上，非主键列完全依赖于主键，而不能是依赖于主键的一部分。
- 第三范式：在第二范式的基础上，非主键列只依赖于主键，不依赖于其他非主键。

#### 3.数据库使用的函数

- count(*/column)：返回行数
- sum(column)： 返回指定列中唯一值的和
- max(column)：返回指定列或表达式中的数值最大值
- min(column)：返回指定列或表达式中的数值最小值
- avg(column)：返回指定列或表达式中的数值平均值
- date（Expression）: 返回指定表达式代表的日期值

#### 4.数据类型

VARCHAR存储的内容超出设置的长度时，内容会被截断。
CHAR是定长的，根据定义的字符串长度分配足够的空间。

**使用策略：**
 对于经常变更的数据来说，CHAR比VARCHAR更好，因为CHAR不容易产生碎片。
 对于非常短的列，CHAR比VARCHAR在存储空间上更有效率。
 使用时要注意只分配需要的空间，更长的列排序时会消耗更多内存。
 尽量避免使用TEXT/BLOB类型，查询时会使用临时表，导致严重的性能开销。

`日期和时间类型`，尽量使用timestamp，空间效率高于datetime，
用整数保存时间戳通常不方便处理。
如果需要存储微妙，可以使用bigint存储。

#### 5.存储引擎

**MySQL存储引擎MyISAM与InnoDB区别**

- 存储引擎Storage engine：MySQL中的数据、索引以及其他对象是如何存储的，是一套文件系统的实现。
- 常用的存储引擎有以下：
  - **Innodb引擎**：Innodb引擎提供了对数据库ACID事务的支持。并且还提供了行级锁和外键的约束。它的设计的目标就是处理大数据容量的数据库系统。
  - **MyIASM引擎**(原本Mysql的默认引擎)：不提供事务的支持，也不支持行级锁和外键。
  - **MEMORY引擎**：所有的数据都在内存中，数据的处理速度快，但是安全性不高。

**MyISAM索引与InnoDB索引的区别**

- InnoDB索引是聚簇索引，MyISAM索引是非聚簇索引。
- InnoDB的主键索引的叶子节点存储着行数据，因此主键索引非常高效。
- MyISAM索引的叶子节点存储的是行数据地址，需要再寻址一次才能得到数据。
- InnoDB非主键索引的叶子节点存储的是主键和其他带索引的列数据，因此查询时做到覆盖索引会非常高效。

**存储引擎选择**

- 如果没有特别的需求，使用默认的`Innodb`即可。
- MyISAM：以读写插入为主的应用程序，比如博客系统、新闻门户网站。
- Innodb：更新（删除）操作频率也高，或者要保证数据的完整性；并发量高，支持事务和外键。比如OA自动化办公系统。

#### 6.索引

**什么是索引？**

- 索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。
- 索引是一种数据结构。数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询、更新数据库表中数据。索引的实现通常使用B树及其变种B+树。
- 更通俗的说，索引就相当于目录。为了方便查找书中的内容，通过对内容建立索引形成目录。索引是一个文件，它是要占据物理空间的。

**索引有哪些优缺点？**

索引的优点：

- 可以大大加快数据的检索速度，这也是创建索引的最主要的原因。
- 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

索引的缺点：

- 时间方面：创建索引和维护索引要耗费时间，具体地，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，会降低增/改/删的执行效率；
- 空间方面：索引需要占物理空间。

**索引的数据结构（b树，hash）**

- 索引的数据结构和具体存储引擎的实现有关，在MySQL中使用较多的索引有**Hash索引**，**B+树索引**等，而我们经常使用的InnoDB存储引擎的默认索引实现为：B+树索引。对于哈希索引来说，底层的数据结构就是哈希表，因此在绝大多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余大部分场景，建议选择BTree索引。

**1、B树索引**

- mysql通过存储引擎取数据，基本上90%的人用的就是InnoDB了，按照实现方式分，InnoDB的索引类型目前只有两种：BTREE（B树）索引和HASH索引。B树索引是Mysql数据库中使用最频繁的索引类型，基本所有存储引擎都支持BTree索引。通常我们说的索引不出意外指的就是（B树）索引（实际是用B+树实现的，因为在查看表索引时，mysql一律打印BTREE，所以简称为B树索引）



![在这里插入图片描述](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/171735c6ee6f5752~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)



- 查询方式：
  - 主键索引区:PI(关联保存的时数据的地址)按主键查询,
  - 普通索引区:si(关联的id的地址,然后再到达上面的地址)。所以按主键查询,速度最快
- B+tree性质：
  1. n棵子tree的节点包含n个关键字，不用来保存数据而是保存数据的索引。
  2. 所有的叶子结点中包含了全部关键字的信息，及指向含这些关键字记录的指针，且叶子结点本身依关键字的大小自小而大顺序链接。
  3. 所有的非终端结点可以看成是索引部分，结点中仅含其子树中的最大（或最小）关键字。
  4. B+ 树中，数据对象的插入和删除仅在叶节点上进行。
  5. B+树有2个头指针，一个是树的根节点，一个是最小关键码的叶节点。



**创建索引时需要注意什么？**

- 非空字段：应该指定列为NOT NULL，除非你想存储NULL。在mysql中，含有空值的列很难进行查询优化，因为它们使得索引、索引的统计信息以及比较运算更加复杂。你应该用0、一个特殊的值或者一个空串代替空值；
- 取值离散大的字段：（变量各个取值之间的差异程度）的列放到联合索引的前面，可以通过count()函数查看字段的差异值，返回值越大说明字段的唯一值越多字段的离散程度高；
- 索引字段越小越好：数据库的数据存储以页为单位一页存储的数据越多一次IO操作获取的数据越大效率越高。

