---
layout: post
title: Database Summary
date: 2019-08-01
categories: [technology]
tags: [life]
comments: false
---



# NoSQL vs SQL

Difference：

1. SQL is a kind of relational databse; NoSQL is not relational.
2. SQL is structure query with specific architecture; and NoSQL is non-structre with dynamic architecture.
3. SQL is table-based; NoSQL is key-value, document, graph, wide-column storage.
4. SQL is for multiple transaction; NoSQL is for non-structure like doc or Json.

**关系型数据库**是指采用了关系模型来组织数据的数据库，简单理解就是二维表格模型

**关系型数据库**是指采用了关系模型来组织数据的数据库，简单理解就是二维表格模型

[How to choose between NoSQL & SQL?](https://zhuanlan.zhihu.com/p/356058865)



# SQL

When：这时用CF NoSQL可能要处理大量的de-normalization，虽然disk便宜，但duplicated data太多的话可能也会爆容量。而且update时要处理de-norm data间consistency的问题。e.g. 一个data可能属于(row_key_A, column_key_A)同时也属于(row_key_B, column_key_B)，这样更新这data时就要同时更新这两个row。感觉这种情况选用SQL会较佳。

1. MySQL, index optimization, query optimization, index cluster. 
2. SQL: ACID, MVCC
3. Locks: 乐观锁、悲观锁、读锁、写锁、意向锁
4. Logs:
5. Data bakeup: 

- **数据库**：包括一个或多个表
- **表（关系 Relation）**：是以列和行的形式组织起来的数据的集合
- **列（属性 Attribute）**：在数据库中经常被称为字段
- **行（值组 Tuple）**：在数据库中经常被称为记录

**Advantage:**

- **事务一致性**：通过事务处理保持数据的一致性
- **复杂查询**：支持SQL，可以进行 JOIN 等复杂查询
- **容易理解**：二维表结构是非常贴近逻辑世界的一个概念，关系模型相对网状、层次等其他模型来说更容易理解
- **使用方便**：通用的 SQL 语言使得操作关系型数据库非常方便
- **易于维护**：丰富的完整性(实体完整性、参照完整性和用户定义的完整性)大大减低了数据冗余和数据不一致的概率

**Disadvantage:**

- **读写性能**：在数据量达到一定规模时，由于关系型数据库的系统逻辑非常复杂，为了维护一致性，使得其非常容易发生死锁等的并发问题，所以导致其读写速度下滑非常严重
- **表结构更新**：表结构可以在被定义之后更新，但是如果有比较大的结构变更的话就会变得比较复杂
- **高并发**：网站的用户并发性非常高，往往达到每秒上万次读写请求，对于传统关系型数据库来说，硬盘I/O是一个很大的瓶颈
- **海量数据**：对于关系型数据库来说，在一张包含海量数据的表中查询，效率是非常低的

事务就是「一组原子性的SQL查询」，或者说一个独立的工作单元。如果数据库引擎能够成功地对数据库应用该组查询的全部语句，那么就执行该组查询。如果其中有任何一条语句因为崩溃或其他原因无法执行，那么所有的语句都不会执行。也就是说，事务内的语句，要么全部执行成功，要么全部执行失败。事物的ACID。

- 原子性（atomicity)
  一个事务必须被视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作。
- 一致性（consistency)
  数据库总是从一个一致性的状态转换到另外一个一致性的状态。在前面的例子中，一致性确保了，即使在执行第三、四条语句之间时系统崩溃，CMBC账户中也不会损失100万，不然lemon要哭死，因为事务最终没有提交，所以事务中所做的修改也不会保存到数据库中。
- 隔离性（isolation)
  通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的。在前面的例子中，当执行完第三条语句、第四条语句还未开始时，此时如果有其他人准备给lemon的CMBC账户存钱，那他看到的CMBC账户里还是有100万的。
- 持久性（durability)
  一旦事务提交，则其所做的修改就会永久保存到数据库中。此时即使系统崩溃，修改的数据也不会丢失。持久性是个有点模糊的概念，因为实际上持久性也分很多不同的级别。有些持久性策略能够提供非常强的安全保障，而有些则未必。而且「不可能有能做到100%的持久性保证的策略」否则还需要备份做什么。



乐观锁：对于数据冲突保持一种乐观态度，操作数据时不会对操作的数据进行加锁，只有到数据提交的时候才通过一种机制来验证数据是否存在冲突。

悲观锁：对于数据冲突保持一种悲观态度，在修改数据之前把数据锁住，然后再对数据进行读写，在它释放锁之前任何人都不能对其数据进行操作，直到前面一个人把锁释放后下一个人数据加锁才可对数据进行加锁，然后才可以对数据进行操作，一般数据库本身锁的机制都是基于悲观锁的机制实现的。



dirty read: 在事务A修改数据之后提交数据之前，这时另一个事务B来读取数据，如果不加控制，事务B读取到A修改过数据，之后A又对数据做了修改再提交，则B读到的数据是脏数据，此过程称为脏读Dirty Read。

[SQL](https://mp.weixin.qq.com/s?__biz=MzkwOTE2OTY1Nw==&mid=2247486900&idx=1&sn=ef7c98dc6c5ff787387410e0fc0eb3c4&chksm=c13f8b22f6480234c1593c3d256758ab275c1864fc4c1c22fbe9970fb60814f41bf8046c016b&token=2073211753&lang=zh_CN#rd)

[SQL index optimization](https://mp.weixin.qq.com/s?__biz=MzkwOTE2OTY1Nw==&mid=2247487268&idx=1&sn=6c97cbcd5ad12695ed2ffd64a16fb6fd&chksm=c13f89b2f64800a48e7bbc39ee7f450a01ece6fc3d4aeaa27fad6ed8ce6a54381f44d1b83a77&token=2073211753&lang=zh_CN#rd)

[Ref 4](https://zhuanlan.zhihu.com/p/366840427)

[30 SQL problems](https://mp.weixin.qq.com/s/59W_sA6Q74RoQfYbBp0oAg)



# NoSQL

**选择：**

Data非常不relational (require no join or few joins)，这时用SQL 就有点浪费，可能会有不必要的overhead。

**分类：**

1. column orientation: Cassandra, HBase. **面向可扩展性的分布式数据库**：这类数据库想解决的问题就是传统数据库存在可扩展性上的缺陷，这类数据库可以适应数据量的增加以及数据结构的变化
2. document: MongoDB, CouchDB. **面向海量数据访问的面向文档数据库**：这类数据库的特点是，可以在海量的数据中快速的查询数据，典型代表为 MongoDB 以及 CouchDB
3. key value: Dynamo，ROMA, Redis, Memcached. **面向高性能并发读写的 key-value 数据库**：key-value数据库的主要特点即使具有极高的并发读写性能，Redis，Tokyo Cabinet，Flare 就是这类的代表.

**Advantage:**

- **读写性能**：无需经过 SQL 层的解析，读写性能很高。主要例子有Redis，由于其逻辑简单，而且纯内存操作，使得其性能非常出色，单节点每秒可以处理超过10万次读写操作；
- **简单的扩展**：基于键值对，数据没有耦合性，容易扩展。典型例子是 Cassandra，由于其架构是类似于经典的 P2P，所以能通过轻松地添加新的节点来扩展这个集群；
- **存储格式多**：支持key-value形式、文档形式、图片形式，而关系型数据库则只支持基础类型；
- **低廉的成本**：这是大多数分布式数据库共有的特点，因为主要都是开源软件，没有昂贵的License成本；

**Disadvantage:**

- **不提供对 SQL 的支持**：如果不支持 SQL 这样的工业标准，将会对用户产生一定的学习和应用迁移成本
- **支持的特性不够丰富**：现有产品所提供的功能都比较有限，大多数 NoSQL 数据库都不支持事务，也不像 MS SQL Server 和 Oracle 那样能提供各种附加功能，比如 BI 和报表等
- **现有产品的不够成熟**：大多数产品都还处于初创期，和关系型数据库几十年的完善不可同日而语

[Ref](https://zhuanlan.zhihu.com/p/63371253)

[Ref 2](https://zhuanlan.zhihu.com/p/208371888)



## KV Redis

[Redis](https://zhuanlan.zhihu.com/p/37982685)是内存型Key/Value系统，读写性能非常好，支持操作原子性，很适合用来做高速缓存。

Redis为内存型KV系统，处理的数据量要小于HBase与MongoDB

Redis很适合用来做缓存，但除此之外，它实际上还可以在一些“读写分离”的场景下作为“读库”来用，特别是用来存放Hadoop或Spark的分析结果。

Redis的读写性能在100,000 ops/s左右，时延一般为10～70微妙左右；而HBase的单机读写性能一般不会超过1,000ops/s，时延则在1～5毫秒之间。Redis的魅力还在于它不像HBase只支持简单的字符串，他还支持集合set，有序集合zset和哈希hash.

**Redis**和**MongoDB**是当前使用最广泛的NoSQL，而就Redis技术而言，它的性能十分优越，可以**支持每秒十几万此的读/写操作**，其性能远超数据库，并且还**支持集群、分布式、主从同步等**配置，原则上可以无限扩展，让更多的数据存储在内存中，更让人欣慰的是它还**支持一定的事务能力**，这保证了高并发的场景下数据的安全和一致性。



## KV LevelDB

**Features:**

- keys 和 values 是任意的字节数组。
- 数据按 key 值排序存储。
- 调用者可以提供一个自定义的比较函数来重写排序顺序。
- 提供基本的 `Put(key,value)`，`Get(key)`，`Delete(key)` 操作。
- 多个更改可以在一个原子批处理中生效。
- 用户可以创建一个瞬时快照(snapshot)，以获得数据的一致性视图。
- 在数据上支持向前和向后迭代。
- 使用 Snappy 压缩库对数据进行自动压缩
- 与外部交互的操作都被抽象成了接口(如文件系统操作等)，因此用户可以根据接口自定义的操作系统交互。

**Disadvantage:**

- 这不是一个 SQL 数据库，它没有关系数据模型，不支持 SQL 查询，也不支持索引。
- 同时只能有一个进程(可能是具有多线程的进程)访问一个特定的数据库。
- 该程序库没有内置的 client-server 支持，有需要的用户必须自己封装。

一个数据库同时只能被一个进程打开。leveldb 会从操作系统获取一把锁来防止多进程同时打开同一个数据库。在单个进程中，同一个 leveldb::DB 对象可以被多个并发线程安全地使用，也就是说，不同的线程可以在不需要任何外部同步原语的情况下，写入、获取迭代器或者调用 `Get`（leveldb 实现会确保所需的同步）。但是其它对象，比如 `Iterator` 或者 `WriteBatch` 需要外部自己提供同步保证，如果两个线程共享此类对象，需要使用自己的锁进行互斥访问。具体见对应的头文件。

leveldb 把相邻的 keys 组织在同一个 block 中(具体见后续文章针对 sstable 文件格式的描述)，block 是数据在内存和持久化存储传输之间的基本单位。默认的未压缩 block 大小大约为 4KB，经常批量扫描大量数据的应用可能希望把这个值调大，而针对数据只做“点读”的应用则可能希望这个值小一些。但是，没有证据表明该值小于 1KB 或者大于几个 MB 的时候性能会表现得更好。另外要注意的是，使用较大的 block size，压缩效率会更高效。

每个 block 在写入持久化存储之前都会被单独压缩。压缩默认是开启的，因为默认的压缩算法非常快，而且对于不可压缩的数据会自动关闭压缩功能，极少有场景会让用户想要完全关闭压缩功能，除非基准测试显示关闭压缩会显著改善性能。

Skip lists for the the key indexes.



## Column Cassandra

HBASE：

- 特别适用于简单数据写入（如“消息类”应用）和海量、结构简单数据的查询（如“详单类”应用）。特别地，适合稀疏表。（个人觉得存个网页内容是极好极好的）
- 作为MapReduce的后台数据源，以支撑离线分析型应用。

HBase存储容量大，一个表可以容纳上亿行、上百万列，可应对超大数据量要求扩展简单的需求。Hadoop的无缝集成，让HBase的数据可靠性和海量数据分析性能（MapReduce）值得期待。



## Doc MongoDB

MongoDB是高性能、无模式的文档型数据库，支持二级索引，非常适合文档化格式的存储及查询。MongoDB的官方定位是通用数据库，确实和MySQL有些像，现在也很流行，但它还是有事务、join等短板，在事务、复杂查询应用下无法取代关系型数据库。

是一个介于关系型和非关系型之间的一个产品吧，类SQL语言，支持索引；MongoDb在类SQL语句操作方面目前比HBase具备更多一些优势，有二级索引，支持相比于HBase更复杂的集合查找等。BSON的数据结构使得处理文档型数据更为直接。支持复杂的数据结构；MongoDb也支持mapreduce，但由于HBase跟Hadoop的结合更为紧密，Mongo在数据分片等mapreduce必须的属性上不如HBase这么直接，需要额外处理。

文档模型与面向对象的数据表达方式更相似更自然。与关系型数据库中表结构不同，文档中可以嵌入数组和子文档，就像程序中的数组和成员变量一样。这是关系型数据库三范式不允许的。但实际中我们经常看到一对多和一对一的数据。比如，一篇博客文章的 Tag 列表作为文章的一部分非常直观，而把 Tag 与文章的从属关系单独放一张表里就不那么自然。再比如，一个订单下面的收货地址，包括省、市、区、街道和门牌，作为一个子文档，与订单的信用卡地址很容易区分开。更方便的是，嵌入的数组和子文档之上可以直接建立索引，比如我可以很快找到所有 Tag 包含 MongoDB 的文章。

理解ACID与BASE的区别（ACID是关系型数据库强一致性的四个要求，而BASE是NoSQL数据库通常对可用性及一致性的弱要求原则，它们的意思分别是，ACID：atomicity, consistency, isolation, durability;BASE：Basically Available, Soft-state, Eventually Consistent。同时有意思的是ACID在英语里意为酸，BASE意思为碱）

理解持久化与非持久化的区别。这么说是因为有的NoSQL系统是纯内存存储的。

你必须意识到传统有关系型数据库与NoSQL系统在数据结构上的本质区别。传统关系型数据库通常是基于行的表格型存储，而NoSQL系统包括了列式存储（Cassandra）、key/value存储（Memcached）、文档型存储（CouchDB）以及图结构存储（Neo4j）

与传统关系数据库有统一的SQL语言操作接口不同，NoSQL系统通常有自己特有的API接口。

在架构上，你必须搞清楚，NoSQL系统是被设计用于成百上千台机器的集群中的，而非共享型数据库系统的架构。

在NoSQL系统中，可能你得习惯一下不知道你的数据具体存在何处的情况。

在NoSQL系统中，你最好习惯它的弱一致性。”eventually consistent”(最终一致性)正是BASE原则中的重要一项。比如在Twitter，你在Followers列表中经常会感受到数据的延迟。

在NoSQL系统中，你要理解，很多时候数据并不总是可用的。

你得理解，有的方案是拥有分区容忍性的，有的方案不一定有。

- 面向文档存储，基于JSON/BSON 可表示灵活的数据结构
- 动态 DDL能力，没有强Schema约束，支持快速迭代
- 高性能计算，提供基于内存的快速数据查询
- 容易扩展，利用数据分片可以支持海量数据存储
- 丰富的功能集，支持二级索引、强大的聚合管道功能，为开发者量身定做的功能，如数据自动老化、固定集合等等。
- 跨平台版本、支持多语言SDK..

[Ref](https://www.zhihu.com/question/20059632/answer/14981332)

[Ref 2](https://www.zhihu.com/question/360088581/answer/929150350)

[Ref 3](https://zhuanlan.zhihu.com/p/87722764)

