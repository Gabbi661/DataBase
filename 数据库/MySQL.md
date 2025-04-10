
### MySQL基础
#### 什么是关系型数据库
顾名思义，关系型数据库（RDB，Relational Database）就是一种建立在关系模型的基础上的数据库。关系模型表明了数据库中所存储的数据之间的联系（一对一、一对多、多对多）。

关系型数据库中，我们的数据都被存放在了各种表中（比如用户表），表中的每一行就存放着一条数据（比如一个用户的信息）。

大部分关系数据库都使用SQL来操作数据库中的数据。
并且大部分数据库都支持事务的四大特性（ACID）。

#### 什么是SQL

SQL 是一种结构化查询语言(Structured Query Language)，专门用来与数据库打交道，目的是提供一种从数据库中读写数据的简单有效的方法。

几乎所有的主流关系数据库都支持 SQL ，适用性非常强。并且，一些非关系型数据库也兼容 SQL 或者使用的是类似于 SQL 的查询语言。

SQL 可以帮助我们：

- 新建数据库、数据表、字段；
- 在数据库中增加，删除，修改，查询数据；
- 新建视图、函数、存储过程；
- 对数据库中的数据进行简单的数据分析；
- 搭配 Hive，Spark SQL 做大数据；
- 搭配 SQLFlow 做机器学习；

#### 什么是MySQL
MySQL是一种关系型数据库，主要用于持久化存储我们系统中的一些数据。
MySQL的默认端口号是3306。

#### MySQL有什么优点
1. 成熟稳定，功能完善。
2. 开源免费。
3. 文档丰富，既有详细的官方文档，又有非常多优质文章可供参考学习。
4. 开箱即用，操作简单，维护成本低。
5. 兼容性好，支持常见的操作系统，支持多种开发语言。
6. 社区活跃，生态完善。
7. 事务支持优秀， InnoDB 存储引擎默认使用 REPEATABLE-READ 并不会有任何性能损失，并且，InnoDB 实现的 REPEATABLE-READ 隔离级别其实是可以解决幻读问题发生的。
8. 支持分库分表、读写分离、高可用。

### MySQL字段类型

MySQL 字段类型可以简单分为三大类：
- **数值类型**：整型（TINYINT、SMALLINT、MEDIUMINT、INT 和 BIGINT）、浮点型（FLOAT 和 DOUBLE）、定点型（DECIMAL）
- **字符串类型**：CHAR、VARCHAR、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT、TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB 等，最常用的是 CHAR 和 VARCHAR。
- **日期时间类型**：YEAR、TIME、DATE、DATETIME 和 TIMESTAMP 等。

#### 整数类型的UNSIGNED属性有什么用
MySQL 中的整数类型可以使用可选的 UNSIGNED 属性来表示不允许负值的无符号整数。使用 UNSIGNED 属性可以将正整数的上限提高一倍，因为它不需要存储负数值。

例如， TINYINT UNSIGNED 类型的取值范围是 0 ~ 255，而普通的 TINYINT 类型的值范围是 -128 ~ 127。INT UNSIGNED 类型的取值范围是 0 ~ 4,294,967,295，而普通的 INT 类型的值范围是 -2,147,483,648 ~ 2,147,483,647。

对于从 0 开始递增的 ID 列，使用 UNSIGNED 属性可以非常适合，因为不允许负值并且可以拥有更大的上限范围，提供了更多的 ID 值可用。

#### VARCHAR(100)和VARCHAR（10）的区别是什么？
VARCHAR(100)和 VARCHAR(10)都是变长类型，表示能存储最多 100 个字符和 10 个字符。因此，VARCHAR (100) 可以满足更大范围的字符存储需求，有更好的业务拓展性。而 VARCHAR(10)存储超过 10 个字符时，就需要修改表结构才可以。

虽说 VARCHAR(100)和 VARCHAR(10)能存储的字符范围不同，但二者存储相同的字符串，所占用磁盘的存储空间其实是一样的，这也是很多人容易误解的一点。

不过，VARCHAR(100) 会消耗更多的内存。这是因为 VARCHAR 类型在内存中操作时，通常会分配固定大小的内存块来保存值，即使用字符类型中定义的长度。例如在进行排序的时候，VARCHAR(100)是按照 100 这个长度来进行的，也就会消耗更多内存。

#### DECIMAL 和 FLOAT/DOUBLE的区别是什么？
DECIMAL 和 FLOAT 的区别是：**DECIMAL 是定点数，FLOAT/DOUBLE 是浮点数。DECIMAL 可以存储精确的小数值，FLOAT/DOUBLE 只能存储近似的小数值。**

DECIMAL 用于存储具有精度要求的小数，例如与货币相关的数据，可以避免浮点数带来的精度损失。

在 Java 中，MySQL 的 DECIMAL 类型对应的是 Java 类 `java.math.BigDecimal`。

#### 为什么不推荐使用TEXT和BLOB？
- 不能有默认值。
- 在使用临时表时无法使用内存临时表，只能在磁盘上创建临时表（《高性能 MySQL》书中有提到）。
- 检索效率较低。
- 不能直接创建索引，需要指定前缀长度。
- 可能会消耗大量的网络和 IO 带宽。
- 可能导致表上的 DML 操作变慢。

#### DATETIME和TIMESTAMP有什么区别？

DATETIME 类型没有时区信息，TIMESTAMP 和时区有关。

TIMESTAMP 只需要使用 4 个字节的存储空间，但是 DATETIME 需要耗费 8 个字节的存储空间。但是，这样同样造成了一个问题，Timestamp 表示的时间范围更小。

- DATETIME：1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
- Timestamp：1970-01-01 00:00:01 ~ 2037-12-31 23:59:59

#### NULL和' '的区别是什么/为什么 MySQL 不建议使用 `NULL` 作为列默认值？
`NULL` 跟 `''`(空字符串)是两个完全不一样的值，区别如下：

- `NULL` 代表一个不确定的值,就算是两个 `NULL`,它俩也不一定相等。例如，`SELECT NULL=NULL`的结果为 false，但是在我们使用`DISTINCT`,`GROUP BY`,`ORDER BY`时,`NULL`又被认为是相等的。
- `''`的长度是 0，是不占用空间的，而`NULL` 是需要占用空间的。
- `NULL` 会影响聚合函数的结果。例如，`SUM`、`AVG`、`MIN`、`MAX` 等聚合函数会忽略 `NULL` 值。 `COUNT` 的处理方式取决于参数的类型。如果参数是 `*`(`COUNT(*)`)，则会统计所有的记录数，包括 `NULL` 值；如果参数是某个字段名(`COUNT(列名)`)，则会忽略 `NULL` 值，只统计非空值的个数。
- 查询 `NULL` 值时，必须使用 `IS NULL` 或 `IS NOT NULLl` 来判断，而不能使用 =、!=、 <、> 之类的比较运算符。而`''`是可以使用这些比较运算符的。

#### Boolean类型如何表示？
MySQL 中没有专门的布尔类型，而是用 TINYINT(1) 类型来表示布尔值。TINYINT(1) 类型可以存储 0 或 1，分别对应 false 或 true。

### MySQL基础架构
![](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

从上图可以看出， MySQL 主要由下面几部分构成：

- **连接器：** 身份认证和权限相关(登录 MySQL 的时候)。
- **查询缓存：** 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。
- **分析器：** 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。
- **优化器：** 按照 MySQL 认为最优的方案去执行。
- **执行器：** 执行语句，然后从存储引擎返回数据。 执行语句之前会先判断是否有权限，如果没有权限的话，就会报错。
- **插件式存储引擎**：主要负责数据的存储和读取，采用的是插件式架构，支持 InnoDB、MyISAM、Memory 等多种存储引擎。InnoDB 是 MySQL 的默认存储引擎，绝大部分场景使用 InnoDB 就是最好的选择。

### MySQL存储引擎

#### MySQL支持哪些存储引擎？默认使用那个？
 MySQL 当前默认的存储引擎是 InnoDB。并且，所有的存储引擎中只有 InnoDB 是事务性存储引擎，也就是说只有 InnoDB 支持事务。

#### MySQL存储引擎架构了解吗？
MySQL 存储引擎采用的是 **插件式架构** ，支持多种存储引擎，我们甚至可以为不同的数据库表设置不同的存储引擎以适应不同场景的需要。==**存储引擎是基于表的，而不是数据库。**==

#### MyISAM和InnoDB有什么区别？
1.**是否支持行级锁**
MyISAM 只有表级锁(table-level locking)，而 InnoDB 支持行级锁(row-level locking)和表级锁,默认为行级锁。

**2、是否支持事务**

MyISAM 不提供事务支持。
InnoDB 提供事务支持，实现了 SQL 标准定义了四个隔离级别，具有提交(commit)和回滚(rollback)事务的能力。并且，InnoDB 默认使用的 REPEATABLE-READ（可重读）隔离级别是可以解决幻读问题发生的（基于 MVCC 和 Next-Key Lock）。

这里一直提到事务：

事务是什么？
**事务（Transaction）** 是 **一组** SQL 操作，它们要么 **全部成功**，要么 **全部失败回滚**，保证数据库数据的一致性和完整性。事务通常用于 **银行转账、订单支付** 等操作。

**事务的四大特性（ACID）**
事务遵循 **ACID** 原则：

1. **原子性（Atomicity）**
• **事务要么全部执行，要么全部回滚**，不能只执行一部分。
• 例如：银行转账，A 账户扣了钱，B 账户却没收到钱，这种情况是不能接受的。
2. **一致性（Consistency）**
• **事务执行后，数据库必须从一个一致状态变为另一个一致状态**。
• 例如：转账前后，A 和 B 账户的总余额应该保持不变。
3. **隔离性（Isolation）**
• **多个事务同时执行时，互不干扰，表现得像是串行执行一样**。
• 例如：两个用户同时购买同一件商品，必须保证库存扣减正确。
4. **持久性（Durability）**
• **事务一旦提交，修改的数据就会永久保存到数据库，即使系统崩溃也不会丢失**。
• 例如：银行系统中，转账成功后，即使服务器宕机，数据也不能丢失。

3. **是否支持外键**

MyISAM 不支持，而 InnoDB 支持。
外键对于维护数据一致性非常有帮助，但是对性能有一定的损耗。因此，通常情况下，我们是不建议在实际生产项目中使用外键的，在业务代码中进行约束即可！

4. **是否支持数据库异常崩溃后的安全恢复**

MyISAM不支持，而InnoDB支持
使用 InnoDB 的数据库在异常崩溃后，数据库重新启动的时候会保证数据库恢复到崩溃前的状态。这个恢复的过程依赖于 `redo log` 。

5. **是否支持 MVCC**
MyISAM 不支持，而 InnoDB 支持。
 MyISAM 连行级锁都不支持。MVCC 可以看作是行级锁的一个升级，可以有效减少加锁操作，提高性能。

6. 索引实现不一样
InnoDB 引擎中，其数据文件本身就是索引文件。相比 MyISAM，索引文件和数据文件是分离的，其表数据文件本身就是按 B+Tree 组织的一个索引结构，树的叶节点 data 域保存了完整的数据记录。

7. 性能有差别
 InnoDB的性能比MyISAM更强大。
 8. 数据缓存策略和机制实现不同。



### MySQL索引详解
#### 索引介绍

**索引是一种用于快速查询和检索数据的数据结构，其本质可以看成是一种排序好的数据结构。**

索引的作用就相当于书的目录。打个比方: 我们在查字典的时候，如果没有目录，那我们就只能一页一页的去找我们需要查的那个字，速度很慢。如果有目录了，我们只需要先去目录里查找字的位置，然后直接翻到那一页就行了。

索引底层数据结构存在很多种类型，常见的索引结构有: B 树， B+树 和 Hash、红黑树。在 MySQL 中，无论是 Innodb 还是 MyIsam，都使用了 B+树作为索引结构。

这里解释一下b树和b+树的区别，以及什么是b树和b+树

**1. B 树（B-Tree）**
**🔹 定义**
• B 树是一种 **自平衡的多叉搜索树**，每个节点最多可以有多个子节点，而不是像二叉搜索树那样只有两个。
• **每个节点存储多个键（key）和子节点指针（children）。**
• **所有的叶子节点在同一层，保持平衡。**
**🔹 结构**
• **一个节点可以有 m 个子节点，则该节点最多存储 m-1 个键**。
• **所有键值按升序存储，节点内的键值将数据范围划分成多个子树。**
• **根节点至少包含 2 个子节点（除非整棵树只有根）。**
• **每个非叶子节点至少包含 ⌈m/2⌉ 个子节点（根节点除外）。**
• **数据（key-value 对）既存储在内部节点，也存储在叶子节点。**
**2. B+ 树（B+ Tree）**
**🔹 定义**
• B+ 树是 B 树的变种，**所有数据都存储在叶子节点，内部节点仅存索引（key）。**
• **叶子节点之间用指针连接，形成有序链表。**
• **范围查询更高效，因为可以在叶子节点链表中顺序遍历。**
**🔹 结构**
• **内部节点（非叶子节点）** 只存储 **索引**，不存数据，索引用于指向子节点。
• **叶子节点** 存储 **所有数据**，且按顺序连接，形成 **链表**。
• **根节点至少包含 2 个子节点（除非整棵树只有根）。**
• **每个非叶子节点至少包含 ⌈m/2⌉ 个子节点（根节点除外）。**
• **叶子节点中的数据是顺序排列的，并且相邻叶子节点之间用指针连接。**

二者的区别：

|**特点**|**B 树**|**B+ 树**|
|---|---|---|
|**数据存储**|数据存储在 **所有节点**（包括内部节点和叶子节点）|数据 **仅存储在叶子节点**，内部节点只存索引|
|**查询效率**|查询复杂度 O(log n)，但可能需要访问多个层次|查询复杂度 O(log n)，**所有数据在叶子层，范围查询更快**|
|**范围查询**|**不太高效**，需要在树中遍历多个节点|**非常高效**，叶子节点有指针，可以直接顺序扫描|
|**叶子节点连接**|叶子节点之间**没有指针**|叶子节点**有链表指针**，便于顺序查找|
|**删除操作**|删除时可能影响树的结构，需要调整多个节点|只影响叶子节点，**不会影响内部索引结构**|

#### 索引的优缺点

- 优点 ： 
		 1.  使用索引能大大加快检索速度，减少IO次数
         2. 通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性
- 缺点：1. 创建索引和维护索引需要耗费许多时间，当对表中的数据进行增删改的时  候，如果数据有索引，那么索引也需要动态的修改，会降低 SQL 执行效率。
        2. 索引需要使用物理文件存储，也会耗费一定空间
    大多数情况下，索引查询能提高查询性能，但是如果数据库的数据量不大，使用索引也不能带来很大提升

#### 索引底层数据结构选型

##### Hash表

哈希表是键值对的集合，通过键(key)即可快速取出对应的值(value)，因此哈希表可以快速检索数据（接近 O（1））。
**为何能够通过 key 快速取出 value 呢？** 原因在于 **哈希算法**（也叫散列算法）。通过哈希算法，我们可以快速找到 key 对应的 index，找到了 index 也就找到了对应的 value。
```java
hash = hashfunc(key)
index = hash % array_size
```
![](https://oss.javaguide.cn/github/javaguide/database/mysql20210513092328171.png)

但是！哈希算法有个 **Hash 冲突** 问题，也就是说多个不同的 key 最后得到的 index 相同。通常情况下，我们常用的解决办法是 **链地址法**。链地址法就是将哈希冲突数据存放在链表中。就比如 JDK1.8 之前 `HashMap` 就是通过链地址法来解决哈希冲突的。不过，JDK1.8 以后`HashMap`为了减少链表过长的时候搜索时间过长引入了红黑树。

![](https://oss.javaguide.cn/github/javaguide/database/mysql20210513092224836.png)

为了减少 Hash 冲突的发生，一个好的哈希函数应该“均匀地”将数据分布在整个可能的哈希值集合中。

MySQL 的 InnoDB 存储引擎不直接支持常规的哈希索引，但是，InnoDB 存储引擎中存在一种特殊的“自适应哈希索引”（Adaptive Hash Index），自适应哈希索引并不是传统意义上的纯哈希索引，而是结合了 B+Tree 和哈希索引的特点，以便更好地适应实际应用中的数据访问模式和性能需求。自适应哈希索引的每个哈希桶实际上是一个小型的 B+Tree 结构。这个 B+Tree 结构可以存储多个键值对，而不仅仅是一个键。这有助于减少哈希冲突链的长度，提高了索引的效率。

既然哈希表这么快，**为什么 MySQL 没有使用其作为索引的数据结构呢？** 主要是因为 Hash 索引不支持顺序和范围查询。假如我们要对表中的数据进行排序或者进行范围查询，那 Hash 索引可就不行了。并且，每次 IO 只能取一个。

试想一种情况:
```sql
SELECT * FROM tb1 WHERE id < 500;
```
在这种范围查询中，优势非常大，直接遍历比 500 小的叶子节点就够了。而 Hash 索引是根据 hash 算法来定位的，难不成还要把 1 - 499 的数据，每个都进行一次 hash 计算来定位吗?这就是 Hash 最大的缺点了。

##### 二叉查找树（BST）
二叉查找树（Binary Search Tree）是一种基于二叉树的数据结构，它具有以下特点：

1. 左子树所有节点的值均小于根节点的值。
2. 右子树所有节点的值均大于根节点的值。
3. 左右子树也分别为二叉查找树。

当二叉查找树是平衡的时候，也就是树的每个节点的左右子树深度相差不超过 1 的时候，查询的时间复杂度为 O(log2(N))，具有比较高的效率。然而，当二叉查找树不平衡时，例如在最坏情况下（有序插入节点），树会退化成线性链表（也被称为斜树），导致查询效率急剧下降，时间复杂退化为 O（N）。

![斜树](https://oss.javaguide.cn/github/javaguide/cs-basics/data-structure/oblique-tree.png)

二查找树的性能非常依赖于它的平衡程度，这就导致它不适合作为MYSQL的底层索引的数据结构。

##### AVL树
二叉查找树（Binary Search Tree）是一种基于二叉树的数据结构，它具有以下特点：

1. 左子树所有节点的值均小于根节点的值。
2. 右子树所有节点的值均大于根节点的值。
3. 左右子树也分别为二叉查找树。

当二叉查找树是平衡的时候，也就是树的每个节点的左右子树深度相差不超过 1 的时候，查询的时间复杂度为 O(log2(N))，具有比较高的效率。然而，当二叉查找树不平衡时，例如在最坏情况下（有序插入节点），树会退化成线性链表（也被称为斜树），导致查询效率急剧下降，时间复杂退化为 O（N）。

![](https://oss.javaguide.cn/github/javaguide/cs-basics/data-structure/avl-tree.png)

AVL 树采用了旋转操作来保持平衡。主要有四种旋转操作：LL 旋转、RR 旋转、LR 旋转和 RL 旋转。其中 LL 旋转和 RR 旋转分别用于处理左左和右右失衡，而 LR 旋转和 RL 旋转则用于处理左右和右左失衡。

由于 AVL 树需要频繁地进行旋转操作来保持平衡，因此会有较大的计算开销进而降低了数据库写操作的性能。并且， 在使用 AVL 树时，每个树节点仅存储一个数据，而每次进行磁盘 IO 时只能读取一个节点的数据，如果需要查询的数据分布在多个节点上，那么就需要进行多次磁盘 IO。 **磁盘 IO 是一项耗时的操作，在设计数据库索引时，我们需要优先考虑如何最大限度地减少磁盘 IO 操作的次数。**

##### 红黑树
AVL 树采用了旋转操作来保持平衡。主要有四种旋转操作：LL 旋转、RR 旋转、LR 旋转和 RL 旋转。其中 LL 旋转和 RR 旋转分别用于处理左左和右右失衡，而 LR 旋转和 RL 旋转则用于处理左右和右左失衡。

由于 AVL 树需要频繁地进行旋转操作来保持平衡，因此会有较大的计算开销进而降低了数据库写操作的性能。并且， 在使用 AVL 树时，每个树节点仅存储一个数据，而每次进行磁盘 IO 时只能读取一个节点的数据，如果需要查询的数据分布在多个节点上，那么就需要进行多次磁盘 IO。 **磁盘 IO 是一项耗时的操作，在设计数据库索引时，我们需要优先考虑如何最大限度地减少磁盘 IO 操作的次数。**


![红黑树](https://oss.javaguide.cn/github/javaguide/cs-basics/data-structure/red-black-tree.png)


和 AVL 树不同的是，红黑树并不追求严格的平衡，而是大致的平衡。正因如此，红黑树的查询效率稍有下降，因为红黑树的平衡性相对较弱，可能会导致树的高度较高，这可能会导致一些数据需要进行多次磁盘 IO 操作才能查询到，这也是 MySQL 没有选择红黑树的主要原因。也正因如此，红黑树的插入和删除操作效率大大提高了，因为红黑树在插入和删除节点时只需进行 O(1) 次数的旋转和变色操作，即可保持基本平衡状态，而不需要像 AVL 树一样进行 O(logn) 次数的旋转操作。

**红黑树的应用还是比较广泛的，TreeMap、TreeSet 以及 JDK1.8 的 HashMap 底层都用到了红黑树。对于数据在内存中的这种情况来说，红黑树的表现是非常优异的。**

##### B树 & B+树
B 树也称 B-树,全称为 **多路平衡查找树** ，B+ 树是 B 树的一种变体。B 树和 B+树中的 B 是 `Balanced` （平衡）的意思。

目前大部分数据库系统及文件系统都采用 B-Tree 或其变种 B+Tree 作为索引结构。

**B 树& B+树两者有何异同呢？**

- B 树的所有节点既存放键(key) 也存放数据(data)，而 B+树只有叶子节点存放 key 和 data，其他内节点只存放 key。
- B 树的叶子节点都是独立的;B+树的叶子节点有一条引用链指向与它相邻的叶子节点。
- B 树的检索的过程相当于对范围内的每个节点的关键字做二分查找，可能还没有到达叶子节点，检索就结束了。而 B+树的检索效率就很稳定了，任何查找都是从根节点到叶子节点的过程，叶子节点的顺序检索很明显。
- 在 B 树中进行范围查询时，首先找到要查找的下限，然后对 B 树进行中序遍历，直到找到查找的上限；而 B+树的范围查询，只需要对链表进行遍历即可。

综上，B+树与 B 树相比，具备更少的 IO 次数、更稳定的查询效率和更适于范围查询这些优势。

#### 索引类型总结
- 按照数据结构维度划分
		 - BTree 索引：MySQL 里默认和最常用的索引类型。只有叶子节点存储 value，非叶子节点只有指针和 key。存储引擎 MyISAM 和 InnoDB 实现 BTree 索引都是使用 B+Tree，但二者实现方式不一样（前面已经介绍了）。
		- 哈希索引：类似键值对的形式，一次即可定位。
		- RTree 索引：一般不会使用，仅支持 geometry 数据类型，优势在于范围查找，效率较低，通常使用搜索引擎如 ElasticSearch 代替。
		- 全文索引：对文本的内容进行分词，进行搜索。目前只有 `CHAR`、`VARCHAR` ，`TEXT` 列上可以创建全文索引。一般不会使用，效率较低，通常使用搜索引擎如 ElasticSearch 代替。



- 按照底层存储方式角度划分：
		    - 聚簇索引（聚集索引）：索引结构和数据一起存放的索引，InnoDB 中的主键索引就属于聚簇索引。
		    - 非聚簇索引（非聚集索引）：索引结构和数据分开存放的索引，二级索引(辅助索引)就属于非聚簇索引。MySQL 的 MyISAM 引擎，不管主键还是非主键，使用的都是非聚簇索引。

- 按照应用维度划分：
			 - 主键索引：加速查询 + 列值唯一（不可以有 NULL）+ 表中只有一个。
			- 普通索引：仅加速查询。
			- 唯一索引：加速查询 + 列值唯一（可以有 NULL）。
			- 覆盖索引：一个索引包含（或者说覆盖）所有需要查询的字段的值。
			- 联合索引：多列值组成一个索引，专门用于组合搜索，其效率大于索引合并。
			- 全文索引：对文本的内容进行分词，进行搜索。目前只有 `CHAR`、`VARCHAR` ，`TEXT` 列上可以创建全文索引。一般不会使用，效率较低，通常使用搜索引擎如 ElasticSearch 代替。
			- 前缀索引：对文本的前几个字符创建索引，相比普通索引建立的数据更小，因为只取前几个字符。

#### 主键索引（Primary Key）
数据表的主键列使用的就是主键索引。

一张数据表有只能有一个主键，并且主键不能为 null，不能重复。

在 MySQL 的 InnoDB 的表中，当没有显示的指定表的主键时，InnoDB 会自动先检查表中是否有唯一索引且不允许存在 null 值的字段，如果有，则选择该字段为默认的主键，否则 InnoDB 将会自动创建一个 6Byte 的自增主键。

![[Pasted image 20250314090835.png]]
#### 二级索引
二级索引（Secondary Index）的叶子节点存储的数据是主键的值，也就是说，通过二级索引可以定位主键的位置，二级索引又称为辅助索引/非主键索引。

唯一索引，普通索引，前缀索引等索引都属于二级索引。

1. **唯一索引(Unique Key)**:唯一索引也是一种约束。唯一索引的属性列不能出现重复的数据，但是允许数据为 NULL，一张表允许创建多个唯一索引。 建立唯一索引的目的大部分时候都是为了该属性列的数据的唯一性，而不是为了查询效率。
2. **普通索引(Index)**:普通索引的唯一作用就是为了快速查询数据，一张表允许创建多个普通索引，并允许数据重复和 NULL。
3. **前缀索引(Prefix)**:前缀索引只适用于字符串类型的数据。前缀索引是对文本的前几个字符创建索引，相比普通索引建立的数据更小，因为只取前几个字符。
4. **全文索引(Full Text)**:全文索引主要是为了检索大文本数据中的关键字的信息，是目前搜索引擎数据库使用的一种技术。Mysql5.6 之前只有 MYISAM 引擎支持全文索引，5.6 之后 InnoDB 也支持了全文索引。

![[Pasted image 20250314090843.png]]

我很好奇为什么不能直接查一级索引，进行数据搜索，非要通过二级索引确定主键，再通过主键寻找数据
如果要查找的不是索引字段，数据库只能进行**全表扫描**，但最终仍然需要从**主键索引（聚簇索引）**中获取数据。原因是：
1. **InnoDB 采用聚簇索引存储**：数据是按照**主键顺序**存储的，不是按照 name 排序的。
2. **所有完整数据都存储在主键索引中**：
• **如果有二级索引**：先查二级索引，拿到 id，再去主键索引取完整数据。
• **如果没有索引**：只能直接遍历**整个主键索引**来找到数据。
所以，即使没有二级索引，**查询仍然会遵循「查索引 → 查主键」的模式**，只是方式不同：
• **有二级索引** → 先查索引，再查主键索引（索引回表）
• **没有二级索引** → 只能全表扫描，最终仍然需要访问主键索引。

==由于没有索引，数据库不得不扫描整个表，**查询效率低**，特别是数据量大时，性能会大幅下降。==

#### 聚簇索引与非聚簇索引
##### 聚簇索引（聚集索引）

聚簇索引（Clustered Index）即索引结构和数据一起存放的索引，并不是一种单独的索引类型。InnoDB 中的主键索引就属于聚簇索引。

聚簇索引的优缺点：

**优点**：
- **查询速度非常快**：聚簇索引的查询速度非常的快，因为整个 B+树本身就是一颗多叉平衡树，叶子节点也都是有序的，定位到索引的节点，就相当于定位到了数据。相比于非聚簇索引， 聚簇索引少了一次读取数据的 IO 操作。
- **对排序查找和范围查找优化**：聚簇索引对于主键的排序查找和范围查找速度非常快。
**缺点**：
- **依赖于有序的数据**：因为 B+树是多路平衡树，如果索引的数据不是有序的，那么就需要在插入时排序，如果数据是整型还好，否则类似于字符串或 UUID 这种又长又难比较的数据，插入或查找的速度肯定比较慢。
- **更新代价大**：如果对索引列的数据被修改时，那么对应的索引也将会被修改，而且聚簇索引的叶子节点还存放着数据，修改代价肯定是较大的，所以对于主键索引来说，主键一般都是不可被修改的。

##### 非聚簇索引（非聚集索引）
非聚簇索引(Non-Clustered Index)即索引结构和数据分开存放的索引，并不是一种单独的索引类型。二级索引(辅助索引)就属于非聚簇索引。MySQL 的 MyISAM 引擎，不管主键还是非主键，使用的都是非聚簇索引。

非聚簇索引的叶子节点并不一定存放数据的指针，因为二级索引的叶子节点就存放的是主键，根据主键再回表查数据。

非聚簇索引的优缺点
**优点**：
更新代价比聚簇索引要小 。非聚簇索引的更新代价就没有聚簇索引那么大了，非聚簇索引的叶子节点是不存放数据的。
**缺点**：
- **依赖于有序的数据**:跟聚簇索引一样，非聚簇索引也依赖于有序的数据
- **可能会二次查询(回表)**:这应该是非聚簇索引最大的缺点了。 当查到索引对应的指针或主键后，可能还需要根据指针或主键再到数据文件或表中查询。
这是 MySQL 的表的文件截图:

![MySQL 表的文件](https://oss.javaguide.cn/github/javaguide/database/mysql20210420165311654.png)
聚簇索引和非聚簇索引:
![聚簇索引和非聚簇索引](https://oss.javaguide.cn/github/javaguide/database/mysql20210420165326946.png)

##### 非聚簇索引一定回表查询吗（覆盖索引）？
**非聚簇索引不一定回表查询。**
试想一种情况，用户准备使用 SQL 查询用户名，而用户名字段正好建立了索引。
```sql'
 SELECT name FROM table WHERE name='guang19';
```
那么这个索引的 key 本身就是 name，查到对应的 name 直接返回就行了，无需回表查询。
即使是 MYISAM 也是这样，虽然 MYISAM 的主键索引确实需要回表，因为它的主键索引的叶子节点存放的是指针。但是！**如果 SQL 查的就是主键呢?**
```sql
SELECT id FROM table WHERE id=1;
```
主键索引本身的 key 就是主键，查到返回就行了。这种情况就称之为覆盖索引了。

#### 覆盖索引和联合索引
##### 覆盖索引
如果一个索引包含（或者说覆盖）所有需要查询的字段的值，我们就称之为 **覆盖索引（Covering Index）** 。

在 InnoDB 存储引擎中，非主键索引的叶子节点包含的是主键的值。这意味着，当使用非主键索引进行查询时，数据库会先找到对应的主键值，然后再通过主键索引来定位和检索完整的行数据。这个过程被称为“回表”。

**覆盖索引即需要查询的字段正好是索引的字段，那么直接根据该索引，就可以查到数据了，而无需回表查询。**

##### 联合索引
使用表中的多个字段创建索引，就是 **联合索引**，也叫 **组合索引** 或 **复合索引**。
以 `score` 和 `name` 两个字段建立联合索引：

```sql
ALTER TABLE `cus_order` ADD INDEX id_score_name(score, name);
```

##### 最左前缀匹配原则
最左前缀匹配原则指的是在使用联合索引时，MySQL 会根据索引中的字段顺序，从左到右依次匹配查询条件中的字段。如果查询条件与索引中的最左侧字段相匹配，那么 MySQL 就会使用索引来过滤数据，这样可以提高查询效率。
最左匹配原则会一直向右匹配，直到遇到范围查询（如 >、<）为止。对于 >=、<=、BETWEEN 以及前缀匹配 LIKE 的范围查询，不会停止匹配。
假设有一个联合索引`(column1, column2, column3)`，其从左到右的所有前缀为`(column1)`、`(column1, column2)`、`(column1, column2, column3)`（创建 1 个联合索引相当于创建了 3 个索引），包含这些列的所有查询都会走索引而不会全表扫描。
我们在使用联合索引时，可以将区分度高的字段放在最左边，这也可以过滤更多数据。

如果有索引 `联合索引（a，b，c）`，查询 `a=1 AND c=1`会走索引么？`c=1` 呢？`b=1 AND c=1`呢？
1. 查询 `a=1 AND c=1`：根据最左前缀匹配原则，查询可以使用索引的前缀部分。因此，该查询仅在 `a=1` 上使用索引，然后对结果进行 `c=1` 的过滤。
2. 查询 `c=1` ：由于查询中不包含最左列 `a`，根据最左前缀匹配原则，整个索引都无法被使用。
3. 查询`b=1 AND c=1`：和第二种一样的情况，整个索引都不会使用。

#### 索引下推
**索引下推（Index Condition Pushdown，简称 ICP）** 是 **MySQL 5.6** 版本中提供的一项索引优化功能，它允许存储引擎在索引遍历过程中，执行部分 `WHERE`字句的判断条件，直接过滤掉不满足条件的记录，从而减少回表次数，提高查询效率。

假设我们有一个名为 `user` 的表，其中包含 `id`, `username`, `zipcode`和 `birthdate` 4 个字段，创建了联合索引`(zipcode, birthdate)`。

- 没有索引下推之前，即使 `zipcode` 字段利用索引可以帮助我们快速定位到 `zipcode = '431200'` 的用户，但我们仍然需要对每一个找到的用户进行回表操作，获取完整的用户数据，再去判断 `MONTH(birthdate) = 3`。
- 有了索引下推之后，存储引擎会在使用`zipcode` 字段索引查找`zipcode = '431200'` 的用户时，同时判断`MONTH(birthdate) = 3`。这样，只有同时满足条件的记录才会被返回，减少了回表次数。

![](https://oss.javaguide.cn/github/javaguide/database/mysql/index-condition-pushdown-graphic-illustration.png)

再来讲讲索引下推的具体原理，先看下面这张 MySQL 简要架构图。
![](https://oss.javaguide.cn/javaguide/13526879-3037b144ed09eb88.png)

MySQL 可以简单分为 Server 层和存储引擎层这两层。Server 层处理查询解析、分析、优化、缓存以及与客户端的交互等操作，而存储引擎层负责数据的存储和读取，MySQL 支持 InnoDB、MyISAM、Memory 等多种存储引擎。

索引下推的**下推**其实就是指将部分上层（Server 层）负责的事情，交给了下层（存储引擎层）去处理。

我们这里结合索引下推原理再对上面提到的例子进行解释。

没有索引下推之前：

- 存储引擎层先根据 `zipcode` 索引字段找到所有 `zipcode = '431200'` 的用户的主键 ID，然后二次回表查询，获取完整的用户数据；
- 存储引擎层把所有 `zipcode = '431200'` 的用户数据全部交给 Server 层，Server 层根据`MONTH(birthdate) = 3`这一条件再进一步做筛选。

有了索引下推之后：

- 存储引擎层先根据 `zipcode` 索引字段找到所有 `zipcode = '431200'` 的用户，然后直接判断 `MONTH(birthdate) = 3`，筛选出符合条件的主键 ID；
- 二次回表查询，根据符合条件的主键 ID 去获取完整的用户数据；
- 存储引擎层把符合条件的用户数据全部交给 Server 层。

可以看出，**除了可以减少回表次数之外，索引下推还可以减少存储引擎层和 Server 层的数据传输量。**

最后，总结一下索引下推应用范围：

1. 适用于 InnoDB 引擎和 MyISAM 引擎的查询。
2. 适用于执行计划是 range, ref, eq_ref, ref_or_null 的范围查询。
3. 对于 InnoDB 表，仅用于非聚簇索引。索引下推的目标是减少全行读取次数，从而减少 I/O 操作。对于 InnoDB 聚集索引，完整的记录已经读入 InnoDB 缓冲区。在这种情况下使用索引下推 不会减少 I/O。
4. 子查询不能使用索引下推，因为子查询通常会创建临时表来处理结果，而这些临时表是没有索引的。
5. 存储过程不能使用索引下推，因为存储引擎无法调用存储函数。

#### 正确使用索引的一些建议
##### 选择合适的字段创建索引
- **不为 NULL 的字段**：索引字段的数据应该尽量不为 NULL，因为对于数据为 NULL 的字段，数据库较难优化。如果字段频繁被查询，但又避免不了为 NULL，建议使用 0,1,true,false 这样语义较为清晰的短值或短字符作为替代。
- **被频繁查询的字段**：我们创建索引的字段应该是查询操作非常频繁的字段。
- **被作为条件查询的字段**：被作为 WHERE 条件查询的字段，应该被考虑建立索引。
- **频繁需要排序的字段**：索引已经排序，这样查询可以利用索引的排序，加快排序查询时间。
- **被经常频繁用于连接的字段**：经常用于连接的字段可能是一些外键列，对于外键列并不一定要建立外键，只是说该列涉及到表与表的关系。对于频繁被连接查询的字段，可以考虑建立索引，提高多表连接查询的效率。

##### 被频繁更新的字段应该慎重建立索引
虽然索引能带来查询上的效率，但是维护索引的成本也是不小的。 如果一个字段不被经常查询，反而被经常修改，那么就更不应该在这种字段上建立索引了。

##### 限制每张表上的索引数量
索引并不是越多越好，建议单张表索引不超过 5 个！索引可以提高效率同样可以降低效率。
索引可以增加查询效率，但同样也会降低插入和更新的效率，甚至有些情况下会降低查询效率。
因为 MySQL 优化器在选择如何优化查询时，会根据统一信息，对每一个可以用到的索引来进行评估，以生成出一个最好的执行计划，如果同时有很多个索引都可以用于查询，就会增加 MySQL 优化器生成执行计划的时间，同样会降低查询性能。

##### 尽可能的考虑建立联合索引而不是单列索引

因为索引是需要占用磁盘空间的，可以简单理解为每个索引都对应着一颗 B+树。如果一个表的字段过多，索引过多，那么当这个表的数据达到一个体量后，索引占用的空间也是很多的，且修改索引时，耗费的时间也是较多的。如果是联合索引，多个字段在一个索引上，那么将会节约很大磁盘空间，且修改数据的操作效率也会提升。

##### 注意避免冗余索引
冗余索引指的是索引的功能相同，能够命中索引(a, b)就肯定能命中索引(a) ，那么索引(a)就是冗余索引。如（name,city ）和（name ）这两个索引就是冗余索引，能够命中前者的查询肯定是能够命中后者的 在大多数情况下，都应该尽量扩展已有的索引而不是创建新索引。

##### 避免索引失效

索引失效也是慢查询的主要原因之一，常见的导致索引失效的情况有下面这些：

- `SELECT *` 不会直接导致索引失效（如果不走索引大概率是因为 where 查询范围过大导致的），但它可能会带来一些其他的性能问题比如造成网络传输和数据处理的浪费、无法使用索引覆盖;
- 创建了组合索引，但查询条件未遵守最左匹配原则;
- 在索引列上进行计算、函数、类型转换等操作;
- 以 % 开头的 LIKE 查询比如 `LIKE '%abc';`;
- 查询条件中使用 OR，且 OR 的前后条件中有一个列没有索引，涉及的索引都不会被使用到;
- IN 的取值范围较大时会导致索引失效，走全表扫描(NOT IN 和 IN 的失效场景相同);
- 发生隐式转换;

##### 删除长期未使用的索引

删除长期未使用的索引，不用的索引的存在会造成不必要的性能损耗。

MySQL 5.7 可以通过查询 `sys` 库的 `schema_unused_indexes` 视图来查询哪些索引从未被使用。

##### 知道如何分析 SQL 语句是否走索引查询
我们可以使用 `EXPLAIN` 命令来分析 SQL 的 **执行计划** ，这样就知道语句是否命中索引了。执行计划是指一条 SQL 语句在经过 MySQL 查询优化器的优化会后，具体的执行方式。

`EXPLAIN` 并不会真的去执行相关的语句，而是通过 **查询优化器** 对语句进行分析，找出最优的查询方案，并显示对应的信息。


#### 索引失效场景
测试最左匹配原则 `sname`, `s_code`, `address`
```sql
select create_time from student where address = "上海" and sname = "变成派大星"  -- 会走索引吗？
```
 这道题走索引了，但是我一开始认为没有，因为sname在后面所以我认为没走。
 但是**因为优化器会自动调整`sname`, `s_code`的顺序**，所以这个是会走索引的。

要想理解联合索引的最左匹配原则，先来理解下索引的底层原理。索引的底层是一颗 B+树，那么联合索引的底层也就是一颗 B+树，只不过联合索引的 B+树节点中存储的是键值。由于构建一棵 B+树只能根据一个值来确定索引关系，所以数据库依赖联合索引最左的字段来构建 文字比较抽象 我们看一下

加入我们建立 A,B 联合索引 他们在底层储存是什么样子呢？

- 橙色代表字段 A
    
- 浅绿色 代表字段 B
    

图解：

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk4vcncjSVc4xae7k9o1QqUNOwGc2bOBGBAsIsb4ibRD505ibyzChlibb5Pw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我们可以看出几个特点

- A 是有顺序的 1，1，2，2，3，4
    
- B 是没有顺序的 1，2，1，4，1，2 这个是散列的
    
- 如果 A 是等值的时候 B 是有序的 例如 （1，1），（1，2） 这里的 B 有序的 （2，1）,(2,4) B 也是有序的
    

这里应该就能看出 如果没有 A 的支持 B 的索引是散列的 不是连续的

总结
如果创建 b,c,d 联合索引面
- 如果 我 where 后面的条件是`c = 1 and d = 1`为什么不能走索引呢 如果没有 b 的话 你查询的值相当于`*11` 我们都知道`*`是所有的意思也就是我能匹配到所有的数据

- 如果 我 where 后面是`b = 1 and d =1` 为什么会走索引呢？你等于查询的数据是 `1*1`我可以通过前面 1 进行索引匹配 所以就可以走索引

- 最左缀匹配原则的最重要的就是 第一个字段

##### select *
为什么不推荐使用 select *
- 增加查询分析器解析成本。
- 增减字段容易与 resultMap 配置不一致。
- 无用字段增加网络 消耗，尤其是 text 类型的字段。在阿里的开发手册中，大面的概括了上面几点。
##### 使用函数
因为索引保存的是索引字段的原始值，而不是经过函数计算后的值，自然就没办法走索引了。
不过，从 MySQL 8.0 开始，索引特性增加了函数索引，即可以针对函数计算后的值建立一个索引，也就是说该索引的值是函数计算后的值，所以就可以通过扫描索引来查询数据。

##### 计算操作
索引的数据结构通常是 **B+ 树**，它存储的是**原始字段值**，而不是计算后的值。因此，如果 WHERE 子句中对索引字段进行了计算，索引无法直接使用，而只能 **遍历所有记录并计算后再筛选**，这就变成了 **全表扫描**（索引失效）。


假设有一个表 users，并在 age 列上创建了索引：
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT,
    INDEX idx_age (age)  -- 在 age 列上创建索引
);
```

**(1) 正常情况下，索引生效**
```sql
SELECT * FROM users WHERE age = 30;

```

age 是索引列，SQL 直接用 **B+ 树** 在索引中查找 age = 30 的记录，查询效率很高。

**(2) 计算操作导致索引失效**
```sql
SELECT * FROM users WHERE age - 1 = 30;
```
🚨 **索引失效，变成全表扫描**
• age - 1 不是原始索引值，B+ 树查找时找不到 age - 1 这个索引。
• SQL 只能取出所有 age 列的值，依次计算 age - 1，然后进行判断。
• 由于无法用索引的 B+ 树直接查找，只能 **全表扫描**。


##### Like %

Like % 的解释

1. **%百分号通配符:** 表示任何字符出现任意次数(可以是 0 次).
    
2. **_下划线通配符:** 表示只能匹配单个字符,不能多也不能少,就是一个字符.
    
3. **like 操作符:** LIKE 作用是指示 mysql 后面的搜索模式是利用通配符而不是直接相等匹配进行比较.
    

**注意:** 如果在使用 like 操作符时,后面的没有使用通用匹配符效果是和=一致的,

`SELECT * FROM products WHERE products.prod_name like '1000';   `

2.匹配包含"Li"的记录(包括记录"Li") :

`SELECT* FROM products WHERE products.prod_name like '%Li%';   `

3.匹配以"Li"结尾的记录(包括记录"Li",不包括记录"Li ",也就是 Li 后面有空格的记录,这里需要注意)

`SELECT * FROM products WHERE products.prod_name like '%Li';   `

在左不走 在右走

`右：` 虽然走 但是索引级别比较低主要是模糊查询 范围比较大 所以索引级别就比较低

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk4sLVF5zgtA4M1gTqNOZEwwBj2hOqPVfiacVDgfzBldLPC6PQXmsafTjw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

`左：` 这个范围非常大 所以没有使用索引的必要了 这个可能不是很好优化 还好不是一直拼接上面的

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk49QylDUzTGodmdqe6iaxNtkFzUnIqwU16FiakzlRkfujuPW35CN0zjjEA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

|>>小总结
索引的时候和查询范围关系也很大 范围过大造成索引没有意义从而失效的情况也不少

##### 使用Or导致索引失效
在 WHERE 子句中，如果在 OR 前的条件列是索引列，而在 OR 后的条件列不是索引列，那么索引会失效 举个例子，比如下面的查询语句，b 是主键，e 是普通列，从执行计划的结果看，是走了全表扫描。

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk4D1vubYcefr2qCdrC31Rm41DetwjNWR2QOmzFFFPb3VAHuvLCsc1NyA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
这个的优化方式就是 在 Or 的时候两边都加上索引

就会使用索引 避免全表扫描

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk4becaUVKNCXV2dKElBjOgVW9qm1ic6PdAwFX8kMibad8bBrMaiaQiavSzkQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

##### in使用不当

首先使用 In 不是一定会造成全表扫描的 **IN 肯定会走索引，但是当 IN 的取值范围较大时会导致索引失效，走全表扫描**

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk4icDL76ibo6dctFpO58qOocbI7N2pMJzCfkWcvlsKrfy3QujVDOWGficPg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz/iaIdQfEric9Twmk46GNqLLRSboGCk9Pkk4djU99GJw1MQQlaY6myvFT0EWY7iaKFYZs9TRdmd8CKJuS9KTicgGN18g/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

in 在结果集 大于 30%的时候索引失效

**符合 IN 查询条件的记录数占总数据量的比例。**
• **如果符合 IN 条件的记录** **较少**（比如 < 30%），SQL 可能会**使用索引**，逐个查找符合条件的记录。
• **如果符合 IN 条件的记录** **较多**（比如 > 30%），SQL 可能会**放弃索引，进行全表扫描**，因为逐个索引查询反而会导致更大的开销。

### MySQL查询缓存

MySQL 查询缓存是查询结果缓存。执行查询语句的时候，会先查询缓存，如果缓存中有对应的查询结果，就会直接返回。

查询缓存会在同样的查询条件和数据情况下，直接返回缓存中的结果。但需要注意的是，查询缓存的匹配条件非常严格，任何细微的差异都会导致缓存无法命中。这里的查询条件包括查询语句本身、当前使用的数据库、以及其他可能影响结果的因素，如客户端协议版本号等。

**查询缓存不命中的情况：**

1. 任何两个查询在任何字符上的不同都会导致缓存不命中。
2. 如果查询中包含任何用户自定义函数、存储函数、用户变量、临时表、MySQL 库中的系统表，其查询结果也不会被缓存。
3. 缓存建立之后，MySQL 的查询缓存系统会跟踪查询中涉及的每张表，如果这些表（数据或结构）发生变化，那么和这张表相关的所有缓存数据都将失效。

**缓存虽然能够提升数据库的查询性能，但是缓存同时也带来了额外的开销，每次查询后都要做一次缓存操作，失效后还要销毁。** 因此，开启查询缓存要谨慎，尤其对于写密集的应用来说更是如此。如果开启，要注意合理控制缓存空间大小，一般来说其大小设置为几十 MB 比较合适。此外，还可以通过 `sql_cache` 和 `sql_no_cache` 来控制某个查询语句是否需要缓存：

```sql'
SELECT sql_no_cache COUNT(*) FROM usr;
```

### MySQL日志

#### MySQL中常见的事务有哪些

MySQL 主要有以下几种日志：
1. **binlog（归档日志 / 二进制日志）**：记录**所有对数据库执行的更改（增删改操作）**，用于**主从复制**和**数据恢复**。
2. **redo log（重做日志）**：InnoDB 存储引擎特有，记录事务的 **物理更改**，用于**事务持久化**，实现 **崩溃恢复**。
3. **undo log（回滚日志）**：用于**事务回滚**，以及**MVCC（多版本并发控制）实现一致性读**。
4. **slow query log（慢查询日志）**：记录执行**超过指定时间的 SQL 语句**，用于 SQL 性能优化。
5. **error log（错误日志）**：记录 MySQL 启动、运行时的错误信息、故障、警告、严重错误等。
6. **general log（通用日志）**：记录所有的 SQL 语句（无论成功与否），用于调试和审计。
#### 慢查询日志有什么用？
**作用**：
• 记录 **执行时间超过 long_query_time 设置的 SQL 语句**（默认 10s）。
• 用于 **SQL 性能优化**，帮助找出低效查询，优化索引、查询方式。

如何开启慢查询日志？
```sql
-- 开启慢查询日志
SET GLOBAL slow_query_log = 1;

-- 设置慢查询的时间阈值（单位：秒）
SET GLOBAL long_query_time = 2;

-- 查看慢查询日志存放路径
SHOW VARIABLES LIKE 'slow_query_log_file';
```

#### binlog主要记录了什么？
**binlog（归档日志）** **记录了所有对数据库的更改**（INSERT、UPDATE、DELETE 等 **DML 语句**），但不包含**SELECT、ROLLBACK**等语句。
**binlog 的作用**
1. **用于主从复制**：从库会**重放主库的 binlog**，实现数据同步。
2. **用于数据恢复**：如果数据库崩溃，可以**使用 binlog 进行增量恢复**。

binlog的三种格式

| **记录方式**           | **说明**                                  |
| ------------------ | --------------------------------------- |
| **STATEMENT**（语句级） | 记录 SQL 语句，主从复制可能会因 NOW() 等非确定性函数导致数据不一致 |
| **ROW**（行级）        | 记录每行数据的变更，更精确但日志量大                      |
| **MIXED**（混合模式）    | 默认模式，语句级和行级结合                           |
#### redo log如何保证事务的持久性？
**作用：****redo log 记录事务对数据页的物理更改**，即使数据库宕机，也能在重启后恢复数据，保证 **事务的持久性（WAL，Write-Ahead Logging）**。

**原理（WAL 机制）：**
1. **事务提交前，先写入 redo log（WAL 机制）**。
2. **InnoDB 先将数据更改写入 redo log，而不是直接写入磁盘**（提高性能）。
3. **数据库崩溃后，重启时重放 redo log，恢复数据**，保证事务不会丢失。
 **MySQL 发生宕机时，事务如何恢复？**
• 事务提交时，redo log 已写入 **持久化存储（磁盘）**。
• 数据库重启后，InnoDB 读取 redo log 并恢复数据。
如何优化 redo log？
• **调整 innodb_log_file_size** 以减少写入频率。
• **使用 SSD** 提高 redo log 写入速度。

#### 页修改之后为什么不直接刷盘？
直接写入磁盘的 **性能非常差**，因为磁盘 I/O 慢，数据库采用 **WAL（Write-Ahead Logging）** 机制，先写 **redo log**，再异步刷新数据页。

**数据刷盘的策略**
• **事务提交时，只写 redo log，不立即刷数据页。**
• **后台 checkpoint 触发后，才将数据页写入磁盘（减少磁盘 I/O）。**
• **宕机后，通过 redo log 进行恢复。**

#### binlog 和 redo log 有什么区别？

| **对比项**  | **binlog（二进制日志）**      | **redo log（重做日志）** |
| -------- | ---------------------- | ------------------ |
| **作用**   | **用于数据恢复、主从复制**        | **用于崩溃恢复、保证事务持久性** |
| **存储位置** | **Server 层**，适用于所有存储引擎 | **InnoDB 引擎特有**    |
| **记录内容** | **逻辑日志（SQL 语句或行数据）**   | **物理日志（数据页的变更）**   |
| **写入时机** | **事务提交后写入**            | **事务执行过程中写入**      |
| **是否可选** | **可关闭**                | **必须启用**           |

#### undo log如何保证事务的原子性？
**作用**
• **用于事务回滚（保证原子性 A）**：如果事务失败，undo log 可以回滚数据，保证事务的 **原子性**。
• **支持 MVCC（多版本并发控制）**：提供历史版本，实现**快照读**。

**原理**
1. **当事务修改数据时，先写 undo log，记录原始数据**。
2. **事务回滚时，依据 undo log 还原数据**，保证事务的原子性。
### MySQL事务

#### 何为事务
我们设想一个场景，这个场景中我们需要插入多条相关联的数据到数据库，不幸的是，这个过程可能会遇到下面这些问题：

- 数据库中途突然因为某些原因挂掉了。
- 客户端突然因为网络原因连接不上数据库了。
- 并发访问数据库时，多个线程同时写入数据库，覆盖了彼此的更改。
- ……

上面的任何一个问题都可能会导致数据的不一致性。为了保证数据的一致性，系统必须能够处理这些问题。事务就是我们抽象出来简化这些问题的首选机制。事务的概念起源于数据库，目前，已经成为一个比较广泛的概念。

**何为事务？** 一言蔽之，**事务是逻辑上的一组操作，要么都执行，要么都不执行。**

#### 何为数据库事务
大多数情况下，我们在谈论事务的时候，如果没有特指**分布式事务**，往往指的就是**数据库事务**。
数据库事务在我们日常开发中接触的最多了。如果你的项目属于单体架构的话，你接触到的往往就是数据库事务了。
**那数据库事务有什么作用呢？**
简单来说，数据库事务可以保证多个对数据库的操作（也就是 SQL 语句）构成一个逻辑上的整体。构成这个逻辑上的整体的这些数据库操作遵循：**要么全部执行成功,要么全部不执行** 。

![数据库事务示意图](https://oss.javaguide.cn/github/javaguide/mysql/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BA%8B%E5%8A%A1%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

另外，关系型数据库（例如：`MySQL`、`SQL Server`、`Oracle` 等）事务都有 **ACID** 特性：

![ACID](https://oss.javaguide.cn/github/javaguide/mysql/ACID.png)

1. **原子性**（`Atomicity`）：事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
2. **一致性**（`Consistency`）：执行事务前后，数据保持一致，例如转账业务中，无论事务是否成功，转账者和收款人的总额应该是不变的；
3. **隔离性**（`Isolation`）：并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
4. **持久性**（`Durability`）：一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

这里要额外补充一点：**只有保证了事务的持久性、原子性、隔离性之后，一致性才能得到保障。也就是说 A、I、D 是手段，C 是目的！**

![AID->C](https://oss.javaguide.cn/github/javaguide/mysql/AID-%3EC.png)

#### 并发事务带来了哪些问题
在典型的应用程序中，多个事务并发运行，经常会操作相同的数据来完成各自的任务（多个用户对同一数据进行操作）。并发虽然是必须的，但可能会导致以下的问题。

##### 脏读（Dirty read）
一个事务读取数据并且对数据进行了修改，这个修改对其他事务来说是可见的，即使当前事务没有提交。这时另外一个事务读取了这个还未提交的数据，但第一个事务突然回滚，导致数据并没有被提交到数据库，那第二个事务读取到的就是脏数据，这也就是脏读的由来。

这段话我的理解就是，一个事务读到了另一个事务没提交的东西，也就是无效的东西。

![脏读](https://javaguide.cn/assets/concurrency-consistency-issues-dirty-reading-C1rL9lNt.png)
##### 丢失修改（Lost to modify）
在一个事务读取一个数据时，另外一个事务也访问了该数据，那么在第一个事务中修改了这个数据后，第二个事务也修改了这个数据。这样第一个事务内的修改结果就被丢失，因此称为丢失修改。

例如：事务 1 读取某表中的数据 A=20，事务 2 也读取 A=20，事务 1 先修改 A=A-1，事务 2 后来也修改 A=A-1，最终结果 A=19，事务 1 的修改被丢失。

![丢失修改](https://javaguide.cn/assets/concurrency-consistency-issues-missing-modifications-D4pIxvwj.png)

##### 不可重复读（Unrepeatable read）
指在一个事务内多次读同一数据。在这个事务还没有结束时，另一个事务也访问该数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改导致第一个事务两次读取的数据可能不太一样。这就发生了在一个事务内两次读到的数据是不一样的情况，因此称为不可重复读。

例如：事务 1 读取某表中的数据 A=20，事务 2 也读取 A=20，事务 1 修改 A=A-1，事务 2 再次读取 A =19，此时读取的结果和第一次读取的结果不同。

![不可重复读](https://javaguide.cn/assets/concurrency-consistency-issues-unrepeatable-read-RYuQTZvh.png)

##### 幻读（Phantom read）
幻读与不可重复读类似。它发生在一个事务读取了几行数据，接着另一个并发事务插入了一些数据时。在随后的查询中，第一个事务就会发现多了一些原本不存在的记录，就好像发生了幻觉一样，所以称为幻读。

例如：事务 2 读取某个范围的数据，事务 1 在这个范围插入了新的数据，事务 2 再次读取这个范围的数据发现相比于第一次读取的结果多了新的数据。

##### 不可重复读和幻读有什么区别？
- 不可重复读的重点是内容修改或者记录减少比如多次读取一条记录发现其中某些记录的值被修改；
- 幻读的重点在于记录新增比如多次执行同一条查询语句（DQL）时，发现查到的记录增加了。

幻读其实可以看作是不可重复读的一种特殊情况，单独把幻读区分出来的原因主要是解决幻读和不可重复读的方案不一样。

举个例子：执行 `delete` 和 `update` 操作的时候，可以直接对记录加锁，保证事务安全。而执行 `insert` 操作的时候，由于记录锁（Record Lock）只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁（Gap Lock）。也就是说执行 `insert` 操作的时候需要依赖 Next-Key Lock（Record Lock+Gap Lock） 进行加锁来保证不出现幻读。

#### 并发事务的控制方式有哪些？
MySQL 中并发事务的控制方式无非就两种：**锁** 和 **MVCC**。锁可以看作是悲观控制的模式，多版本并发控制（MVCC，Multiversion concurrency control）可以看作是乐观控制的模式。

**锁** 控制方式下会通过锁来显式控制共享资源而不是通过调度手段，MySQL 中主要是通过 **读写锁** 来实现并发控制。

- **共享锁（S 锁）**：又称读锁，事务在读取记录的时候获取共享锁，允许多个事务同时获取（锁兼容）。
- **排他锁（X 锁）**：又称写锁/独占锁，事务在修改记录的时候获取排他锁，不允许多个事务同时获取。如果一个记录已经被加了排他锁，那其他事务不能再对这条记录加任何类型的锁（锁不兼容）。

读写锁可以做到读读并行，但是无法做到写读、写写并行。另外，根据根据锁粒度的不同，又被分为 **表级锁(table-level locking)** 和 **行级锁(row-level locking)** 。InnoDB 不光支持表级锁，还支持行级锁，默认为行级锁。行级锁的粒度更小，仅对相关的记录上锁即可（对一行或者多行记录加锁），所以对于并发写入操作来说， InnoDB 的性能更高。不论是表级锁还是行级锁，都存在共享锁（Share Lock，S 锁）和排他锁（Exclusive Lock，X 锁）这两类。

**MVCC** 是多版本并发控制方法，即对一份数据会存储多个版本，通过事务的可见性来保证事务能看到自己应该看到的版本。通常会有一个全局的版本分配器来为每一行数据设置版本号，版本号是唯一的。

MVCC 在 MySQL 中实现所依赖的手段主要是: **隐藏字段、read view、undo log**。

- undo log : undo log 用于记录某行数据的多个版本的数据。
- read view 和 隐藏字段 : 用来判断当前版本数据的可见性。

#### SQL标准定义了哪些事务隔离级别？
SQL 标准定义了四个隔离级别：

- **READ-UNCOMMITTED(读取未提交)** ：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
- **READ-COMMITTED(读取已提交)** ：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。
- **REPEATABLE-READ(可重复读)** ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **SERIALIZABLE(可串行化)** ：最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

|隔离级别|脏读|不可重复读|幻读|
|---|---|---|---|
|READ-UNCOMMITTED|√|√|√|
|READ-COMMITTED|×|√|√|
|REPEATABLE-READ|×|×|√|
|SERIALIZABLE|×|×|×|

#### MySQL的隔离级别是基于锁实现的吗？
MySQL 的隔离级别基于锁和 MVCC 机制共同实现的。
SERIALIZABLE 隔离级别是通过锁来实现的，READ-COMMITTED 和 REPEATABLE-READ 隔离级别是基于 MVCC 实现的。不过， SERIALIZABLE 之外的其他隔离级别可能也需要用到锁机制，就比如 REPEATABLE-READ 在当前读情况下需要使用加锁读来保证不会出现幻读。

#### MySQL的默认隔离级别是什么？
MySQL InnoDB 存储引擎的默认支持的隔离级别是 **REPEATABLE-READ（可重读）**。

### MySQL锁
锁是一种常见的并发事务的控制方式。

#### 表级锁和行级锁了解吗？有什么区别？

MyISAM 仅仅支持表级锁(table-level locking)，一锁就锁整张表，这在并发写的情况下性非常差。InnoDB 不光支持表级锁(table-level locking)，还支持行级锁(row-level locking)，默认为行级锁。

行级锁的粒度更小，仅对相关的记录上锁即可（对一行或者多行记录加锁），所以对于并发写入操作来说， InnoDB 的性能更高。

**表级锁和行级锁对比**：

- **表级锁：** MySQL 中锁定粒度最大的一种锁（全局锁除外），是针对非索引字段加的锁，对当前操作的整张表加锁，实现简单，资源消耗也比较少，加锁快，不会出现死锁。不过，触发锁冲突的概率最高，高并发下效率极低。表级锁和存储引擎无关，MyISAM 和 InnoDB 引擎都支持表级锁。
- **行级锁：** MySQL 中锁定粒度最小的一种锁，是 **针对索引字段加的锁** ，只针对当前操作的行记录进行加锁。 行级锁能大大减少数据库操作的冲突。其加锁粒度最小，并发度高，但加锁的开销也最大，加锁慢，会出现死锁。行级锁和存储引擎有关，是在存储引擎层面实现的。

#### 行级锁的使用有什么注意事项？
InnoDB 的行锁是针对索引字段加的锁，表级锁是针对非索引字段加的锁。当我们执行 `UPDATE`、`DELETE` 语句时，如果 `WHERE`条件中字段没有命中唯一索引或者索引失效的话，就会导致扫描全表对表中的所有行记录进行加锁。

#### InnoDB有哪几类行锁？
InnoDB 行锁是通过对索引数据页上的记录加锁实现的，MySQL InnoDB 支持三种行锁定方式：
- **记录锁（Record Lock）**：属于单个行记录上的锁。
- **间隙锁（Gap Lock）**：锁定一个范围，不包括记录本身。
- **临键锁（Next-Key Lock）**：Record Lock+Gap Lock，锁定一个范围，包含记录本身，主要目的是为了解决幻读问题（MySQL 事务部分提到过）。记录锁只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁。

**在 InnoDB 默认的隔离级别 REPEATABLE-READ 下，行锁默认使用的是 Next-Key Lock。但是，如果操作的索引是唯一索引或主键，InnoDB 会对 Next-Key Lock 进行优化，将其降级为 Record Lock，即仅锁住索引本身，而不是范围。**

#### 共享锁和排他锁呢？
不论是表级锁还是行级锁，都存在共享锁（Share Lock，S 锁）和排他锁（Exclusive Lock，X 锁）这两类：

- **共享锁（S 锁）**：又称读锁，事务在读取记录的时候获取共享锁，允许多个事务同时获取（锁兼容）。
- **排他锁（X 锁）**：又称写锁/独占锁，事务在修改记录的时候获取排他锁，不允许多个事务同时获取。如果一个记录已经被加了排他锁，那其他事务不能再对这条事务加任何类型的锁（锁不兼容）。

排他锁与任何的锁都不兼容，共享锁仅和共享锁兼容。


|     | S锁  | X锁  |
| --- | --- | --- |
| S锁  | 不冲突 | 冲突  |
| X锁  | 冲突  | 冲突  |


由于 MVCC 的存在，对于一般的 `SELECT` 语句，InnoDB 不会加任何锁。不过， 你可以通过以下语句显式加共享锁或排他锁。

```sql
# 共享锁 可以在 MySQL 5.7 和 MySQL 8.0 中使用
SELECT ... LOCK IN SHARE MODE;
# 共享锁 可以在 MySQL 8.0 中使用
SELECT ... FOR SHARE;
# 排他锁
SELECT ... FOR UPDATE;
```

#### 意向锁有什么作用？
如果需要用到表锁的话，如何判断表中的记录没有行锁呢，一行一行遍历肯定是不行，性能太差。我们需要用到一个叫做意向锁的东东来快速判断是否可以对某个表使用表锁。

意向锁是表级锁，共有两种：

- **意向共享锁（Intention Shared Lock，IS 锁）**：事务有意向对表中的某些记录加共享锁（S 锁），加共享锁前必须先取得该表的 IS 锁。
- **意向排他锁（Intention Exclusive Lock，IX 锁）**：事务有意向对表中的某些记录加排他锁（X 锁），加排他锁之前必须先取得该表的 IX 锁。

**意向锁是由数据引擎自己维护的，用户无法手动操作意向锁，在为数据行加共享/排他锁之前，InnoDB 会先获取该数据行所在在数据表的对应意向锁。**

意向锁之间是互相兼容的。

|  |IS 锁|IX 锁|
| ---  | ---  | ---  |
|IS 锁|兼容|兼容|
|IX 锁|兼容|兼容|

意向锁和共享锁和排它锁互斥（这里指的是表级别的共享锁和排他锁，意向锁不会与行级的共享锁和排他锁互斥）。

| |IS 锁|IX 锁|
|---|---|---|
|S 锁|兼容|互斥|
|X 锁|互斥|互斥|
#### 当前读和快照读有什么区别？
**快照读**（一致性非锁定读）就是单纯的 `SELECT` 语句，但不包括下面这两类 `SELECT` 语句：
```sql
SELECT ... FOR UPDATE
# 共享锁 可以在 MySQL 5.7 和 MySQL 8.0 中使用
SELECT ... LOCK IN SHARE MODE;
# 共享锁 可以在 MySQL 8.0 中使用
SELECT ... FOR SHARE;
```

快照即记录的历史版本，每行记录可能存在多个历史版本（多版本技术）。
快照读的情况下，如果读取的记录正在执行 UPDATE/DELETE 操作，读取操作不会因此去等待记录上 X 锁的释放，而是会去读取行的一个快照。
只有在事务隔离级别 RC(读取已提交) 和 RR（可重读）下，InnoDB 才会使用一致性非锁定读：
- 在 RC 级别下，对于快照数据，一致性非锁定读总是读取被锁定行的最新一份快照数据。
- 在 RR 级别下，对于快照数据，一致性非锁定读总是读取本事务开始时的行数据版本。
快照读比较适合对于数据一致性要求不是特别高且追求极致性能的业务场景。
**当前读** （一致性锁定读）就是给行记录加 X 锁或 S 锁。
当前读的一些常见 SQL 语句类型如下：
```sql
# 对读的记录加一个X锁
SELECT...FOR UPDATE
# 对读的记录加一个S锁
SELECT...LOCK IN SHARE MODE
# 对读的记录加一个S锁
SELECT...FOR SHARE
# 对修改的记录加一个X锁
INSERT...
UPDATE...
DELETE...
```

上面这堆有点晦涩难懂，我用我自己的话总结了一下（也就是快照读不在乎数据的变化 只要有数据他就能读回来 而当前读需要上锁 必须保证数据是最新的），chatgpt确认无误：
**快照读（Snapshot Read）**
• **不关心数据的最新状态**，只要事务启动时有数据，它就能读到 **历史版本（MVCC 机制）**。
• **不会加锁**，并发性能高，适用于**查询数据**。
• 适用于 **SELECT 语句**

**当前读（Current Read）**
• **必须保证读取的是最新数据**，所以需要**加锁**，防止其他事务修改。
• 适用于 **修改数据** 的场景，如 UPDATE、DELETE，以及带锁的 SELECT（FOR UPDATE 或 LOCK IN SHARE MODE）。


