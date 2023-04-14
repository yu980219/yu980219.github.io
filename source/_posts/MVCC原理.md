---
title: MVCC原理
tags: [MVCC]
categories:
  - 后端
  - MySQL
  - MySQL进阶
index_img: /img/default.jpeg
date: 2022/9/21 10:48:00
---

# MVCC原理

[【IT老齐030】这可能是最直白的MySQL MVCC机制讲解啦!](https://www.bilibili.com/video/BV1hL411479T/?spm_id_from=333.999.0.0&vd_source=b1d026ff22cbf6f06b334c7d53529a2a)

在MySQL InnoDB存储引擎下，RC（读已提交）、RR（可重复读）基于MVCC（多版本并发控制）进行并发事务控制。

MVCC是基于“数据版本”对并发事务进行访问。

## 一、示例

![多个事务执行](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230206234203521.png)

前面三个事务按照时间线依次执行，事务ID依次自增。

而第4个事务相对比较特殊，它没做任何数据调整，直接进行了两次读取操作，然而两次读取的时机非常的微妙。

第1次读取，在事务A提交后，事务B提交前执行的。

第2次读取，同样的SQL，是在事务C提交之前执行的。

此时事务A和事务B都已经结束了，针对于当前的处理来说，如果采用RR（可重复读）的隔离级别来说，RR可以解决不可重复读的问题，所以前后执行它都得到了同一个结果：

> RR级别：Select1 = 张三，Select2 = 张三

而RC是读已提交，是不能保证不可重复读的。

> RR级别：Select1 = 张三，Select2 = 张小三

## 二、基本知识

![UNDO_LOG版本链](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230206234827436.png)

### 1. 什么是UNDO_LOG？

undo_log就是回滚日志的意思，在我们进行事务处理中，如果我们进行回滚操作，能够进行还原，在innoDB存储引擎中，采用UNDO_LOG链的方式来保存一个一个的数据变化。

### 2. 版本链是如何组织的？

以示例为例，最上面一行保存了当前表中的数据，因为事务执行完以后，最后的结果为“张老三”。

![最后的结果](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230206235419152.png)

当前的事务编号 trx_id为3，“张老三”的记录下，作为innoDB会额外的附加两个字段，`trx_id`和`DB_ROLL_PTR`。

> trx_id 代表最后一次更新的时候的事务编号
>
> DB_ROLL_PTR 是一个指针，指向了上次进行版本变化时的数据。

在3号id执行前，由2号id提交为“张小三”。

所以第二行保存了由2号事务处理时所产生的镜像。

在2号id之前还有1号事务的更新操作，name更新为“张三”。

无论1号事务是否提交，都会被记到版本链中。

![事务操作时的版本链](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207000040876.png)

还有一个最原始的数据。

因为“张三丰”是最原始的数据，所以最后一行没有事务编号，指针也是空的。

![原始数据](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207000142491.png)

到这里就明白了，原始数据为“张三丰”，经过了三次更新，更新为了“张老三”，每一次数据都采用事务编号作为版本来进行标识，这样就能有效的标识出每一个版本。

### 3. UNDO_LOG不是会被删除吗？

**Q：中间数据万一被删了版本链不就断了吗？**

A：UNDO_LOG版本链并不是用完了立即删除的，而是MySQL确保版本链数据不再被“引用”后再进行删除。

所以UNDO_LOG版本链除了一些不可控因素外，应该都是完整的。

### 4. 什么是ReadView？

ReadView是“快照读”SQL执行时MVCC提取数据的依据。

（1）**快照读**就是最普通的Select查询的SQL语句

（2）**当前读**指执行下列语句时进行数据的方式：

Insert、Update、Delete、

Select... for update（写锁）

Select... lock in share mode（读锁）

-----

**ReadView**是一个数据结构，包含4个字段。

- **m_ids**：当前活跃的事务编号集合
- **min_trx_id**：最小活跃事务编号
- **max_trx_id**：预分配事务编号，当前最大事务编号+1
- **creator_trx_id**：ReadView创建者的事务编号

## 三、MVCC分析

### 1. 读已提交（RC）

在每一次执行快照读的时候生成一次ReadView

![RC级别ReadView分析](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207001231486.png)

第一次Select，当前活跃的事务id为2、3、4

最小的事务id=2

预生成的事务编号=5

创建者编号=4



第二次Select，活跃的事务id为3、4，2已经被提交。

最小的事务id=3

预生成的事务编号=5

创建者编号=4

#### 数据提取的过程

![RC级别数据提取的过程-第一次Select](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207001544237.png)

将每一个版本的数据代入到右侧的判断规则来，如果在右侧有符合条件的，直接把当前版本的数据返回。如果右侧的条件都不满足，按照版本链向下依次尝试，知道获得满足条件的结果。

1. 张老三

   (1) 3=4 不成立，向下判断

   (2) 3<2 不成立，向下判断

   (3) 3>5 不成立，向下判断

   (4) 2<=3<=5 && 3 not in {2,3,4} 不成立。

2. 张小三

   (1) 2=4 不成立，向下判断

   (2) 2<2 不成立，向下判断

   (3) 2>5 不成立，向下判断

   (4) 2<=2<=5 && 2 not in {2,3,4} 不成立。

3. 张三

   (1) 1=4 不成立，向下判断

   (2) 1<2 成立，说明数据已经被提交了，返回结果**张三**。

![RC级别数据提取的过程-第二次Select](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207002728887.png)

1. 张老三

   (1) 3=4 不成立，向下判断

   (2) 3<3 不成立，向下判断

   (3) 3>5 不成立，向下判断

   (4) 3<=3<=5 && 3 not in {3,4} 不成立。

2. 张小三

   (1) 2=4 不成立，向下判断

   (2) 2<3 成立，说明数据已经被提交了，返回结果**张小三**。

总结：读已提交的隔离级别下，第一次读的是“张三”，第二次读的是“张小三”，显然出现了不可重复读的现象。所以在RC的级别下是不能解决不可重复读的。

### 2. 可重复读（RR）

仅在第一次执行快照读的时候生成ReadView，后续快照读会复用第一次的ReadView。（有例外）

![第二次Select复用了第一次的ReadView](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207003055059.png)

总结：所以第一次执行得到的是“张三”，第二次执行得到的也是“张三”，ReadView复用的方式解决了不可重复读的情况。

### 3. RR级别下使用MVCC能避免幻读吗？

能，但不完全能！

因为作为MVCC并不是采用锁的方式完全的对事务数据进行的隔离，而是通过版本控制的方式变相的实现了解决幻读的功能。

在连续多次读的情况下，ReadView会产生复用，没有幻读问题。

*特例：当两次**快照读**之间存在**当前读**，ReadView会重新生成，导致幻读。*

![前后两个事务加入了当前读](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MVCC/image-20230207003711816.png)