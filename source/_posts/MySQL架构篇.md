# **MySQL架构篇**

## **一、MySQL服务器环境**

- OS：CentOS 7.6

- MySQL：MySQL 5.7.30

## **二、MySQL架构图**

### **2.1 逻辑架构图**

![逻辑架构图](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%873.png)

- **Connectors连接器：**负责跟客户端建立连接
- **Management Serveices & Utilities系统管理和控制工具**
- **Connection Pool连接池：**管理用户连接，监听并接收连接的请求，转发所有连接的请求到线程管理模块
- **SQL Interface SQL接口：**接受用户的SQL命令，并且返回SQL执行结果
- **Parser解析器：**SQL传递到解析器的时候会被解析器验证和解析
- **Optimizer查询优化器：**SQL语句在查询之前会使用查询优化器对查询进行优化
- **Cache和Buffer查询缓存：**在MySQL5.7中包含缓存组件。在MySQL8中移除了
- **Pluggable Storage Engines存储引擎：**存储引擎就是存取数据、建立与更新索引、查询数据等技术的实现方法

### **2.2 MySQL日志文件**

MySQL是通过文件系统对数据索引后进行存储的，MySQL从物理结构上可以分为**日志文件**和**数据及索引文件**。MySQL在Linux中的数据索引文件和日志文件通常放在/var/lib/mysql目录下。MySQL通过日志记录了数据库操作信息和错误信息。

**常用日志文件如下：**

1.  错误日志：/var/log/mysql-error.log

2.  二进制日志：/var/lib/mysql/mysql-bin

3.  查询日志：

4.  慢查询日志：slow\_query\_log.log

5.  事务重做日志：redo log

6.  中继日志：relay log

7. ...

可以通过命令查看当前数据库中的日志使用信息：

```mysql
mysql> show variables like 'log_%';
```

![日志信息](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%872.png)

#### **错误日志：error log**

默认开启，错误日志记录了运行过程中**遇到的所有严重的错误信息**，以及 MySQL**每次启动和关闭的详细信息**。错误日志所记录的信息是可以通过**log\_error和log\_warnings配置**来定义的。从5.5.7以后无法关闭错误日志。

- log\_error：指定错误日志存储位置

- log-warnings：是否将警告信息输出到错误日志中。

  - log\_warnings 为0， 表示不记录告警信息。

  - log\_warnings 为1， 表示告警信息写入错误日志。

  - log\_warnings 大于1， 表示各类告警信息，例如：有关网络故障的信息和重新连接信息写入错误日志。

```mysql
log_error=/var/log/mysql-error.log
log_warnings=2
```

#### **二进制日志：bin log**

默认关闭，需要通过以下配置进行开启。binlog记录了数据库所有的ddl语句和dml语句，但不包括select语句内容，语句以事件的形式保存，描述了数据的变更顺序，binlog还包括了每个更新语句的执行时间信息。如果是DDL语句，则直接记录到binlog日志，而DML语句，必须通过事务提交才能记录到binlog日志中。

binlog主要用于实现mysql**主从复制、数据备份、数据恢复。**

配置中mysql-bin是binlog日志文件的basename，binlog日志文件的完整名称：mysql-bin.000001。

```mysql
server_id=42
log-bin=mysql-bin
```

>数据定义语言：简称DDL(Data Definition Language)
>
> - 作用：用来定义数据库对象：数据库，表，列等。
>
> - 关键字：create，alter，drop等
>
> 数据操作语言：简称DML(Data Manipulation Language)，
>
> - 作用：用来对数据库中表的记录进行更新。
> - 关键字：insert，delete，update等
>
> 数据查询语言：简称DQL(Data Query Language)
>
> -  作用：用来查询数据库中表的记录。
> - 关键字：select，from，where等
>
> 数据控制语言：简称DCL(Data Control Language)
>
> - 作用：用来定义数据库的访问权限和安全级别，及创建用户。

#### **通用查询日志：general query log**

**默认关闭，**由于通用查询日志会记录用户的所有操作，其中还包含增删查改等信息，在并发操作大的环境下会产生大量的信息从而导致不必要的磁盘IO，会影响mysql的性能的。

如果不是为了调试数据库，不建议开启查询日志。

```mysql
# 查询通用查询日志变量信息
mysql> show global variables like '%general_log%';
```

开启方式：

```mysql
# 启动开关
general_log={ON\|OFF}
# 日志文件变量，而general_log_file如果没有指定，默认名是host_name.log
general_log_file=/var/lib/mysql/host_name.log
```

####  **慢查询日志：slow query log**

**默认关闭，**通过以下设置开启。记录执行时间超过**long\_query\_time**秒的所有查询，便于收集查询时间比较长的SQL语句。

查看阈值

```mysql
show global status like '%Slow_queries%';
show variables like '%slow_query%';
show variables like 'long_query_time%';
```

配置慢查询开启

```mysql
# 开启慢查询日志
slow_query_log=ON
# 慢查询的阈值，单位秒
long_query_time=10
# 日志记录文件
# 如果没有给出file_name值，默认为主机名，后缀为-slow.log。
# 如果给出了文件名，但不是绝对路径名，文件则写入数据目录。
slow_query_log_file=slow_query_log.log
```

###  **2.3 MySQL数据文件**

查看MySQL数据文件：

```mysql
show variables like '%datadir%';
```

**ibdata文件：**使用**系统表空间**存储表数据和索引信息，所有表共同使用一个或者多个ibdata文件。

**InnoDB存储引擎的数据文件：**

- **.frm文件：**主要存放与表相关的数据信息，主要包括**表结构的定义信息**
- **.ibd：**使用独享表空间存储表**数据和索引**信息，一张表对应一个ibd文件。

**MyISAM存储引擎的数据文件：**

-  **.frm文件：**主要存放与表相关的数据信息，主要包括表结构的定义信息

- **.myd文件：**主要用来存储表数据信息。
- **.myi文件：**主要用来存储表数据文件中任何索引的数据树。

###  **2.4 案例：一条SQL语句的完整执行流程**

####  **Sql语句执行流程**

分析SQL语句如下：

```mysql
select c_id,first_name,last_name from customer where c_id=14;
```

大体来说，MySQL 可以分为 **Server层**和**存储引擎层**两部分：

1. **Server层**

   - 包括：连接器、查询缓存、分析器、优化器、执行器等
   
   
      - 涵盖 MySQL的大多数核心服务功能
   
   
      - 所有的内置函数（如日期、时间、数学和加密函数等），所有跨存储引擎的功能都在这一层实现
   
        - 比如：存储过程、触发器、视图等
   


2. **存储引擎层：**


      - 负责数据的存储和提取


      - 可插拔式存储引擎：InnoDB、MyISAM、Memory 等


      - 最常用存储引擎是InnoDB


      -  从MySQL 5.5版本开始，默认是InnoDB



![执行流程](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%871.png)

#### **第一步：连接到数据库**

**首先会连接到这个数据库上，这时候接待你的就是连接器。**

```shell
-- 连接命令
mysql -h127.0.0.1 -P3306 -uroot -p
```

连接完成后，如果你没有后续的动作，这个连接就处于空闲状态。客户端如果太长时间没动静，连接器就会自动将它断开。这个时间是由参数 wait\_timeout 控制的默认值是 8 小时。

```mysql
mysql> show processlist;
# 其中的 Command 列显示为“Sleep”的这一行，就表示现在系统里面有一个空闲连接。
```

![show processlist](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%874.png)

#### **第二步：查缓存**

MySQL 拿到一个查询请求后，会先到查询缓存看看，之前是不是执行过这条语句。之前执行过的语句及其结果可能会以 key-value 对的形式，被直接缓存在内存中。key 是查询的语句hash之后的值，value是查询的结果。

- 如果你的查询语句在缓存中，会被直接返回给客户端。

- 如果语句不在查询缓存中，就会继续后面的执行阶段。执行完成后，执行结果会被存入查询缓存中。


如果查询命中缓存，MySQL 不需要执行后面的复杂操作就可以直接返回结果，效率会很高！但是不建议使用MySQL的内置缓存功能

#####  **1）案例：查询缓存**

 查询缓存默认是关闭的状态

```mysql
# 1）查看是否开启缓存
mysql> show variables like 'query_cache_type';
# 2）查看缓存的命中次数：
mysql> show status like 'qcache_hits';
# 3）开启缓存
在/etc/my.cnf文件中修改“query_cache_type”参数
值为`0或OFF`会禁止使用缓存。
值为`1或ON`将启用缓存，但以`SELECT SQL_NO_CACHE`开头的语句除外。
值为`2或DEMAND`时，只缓存以`SELECT SQL_CACHE`开头的语句。
```

修改配置文件 my.cnf ，在文件中增加如下内容开启缓存：

```mysql
query_cache_type=2
```

查询SQL：

```mysql
mysql> select * from city where city_id = 1;
```

**清空查询缓存**

可以使用下面三个SQL来清理查询缓存：

```mysql
FLUSH QUERY CACHE; # 清理查询缓存内存碎片。
RESET QUERY CACHE; # 从查询缓存中移出所有查询。
FLUSH TABLES; # 关闭所有打开的表，同时该操作将会清空查询缓存中的内容。
```

#####  **2）为什么不建议使用MySQL的查询缓存？**

因为查询缓存往往弊大于利

- **成本高：**查询缓存的失效非常频繁，只要有对一个表的更新，这个表上所有的查询缓存都会被清空。因此很可能你费劲地把结果存起来，还没使用呢，就被一个更新全清空了。
- **命中率不高：**对于更新压力大的数据库来说，查询缓存的命中率会非常低。除非你的业务就是有一张静态表，很长时间才会更新一次。比如，一个系统配置表，那这张表上的查询才适合使用查询缓存。
- **功能并不如专业的缓存工具更好：**redis、memcache、ehcache\...

好在 MySQL 也提供了这种**按需使用**的方式。你可以将参数 query\_cache\_type 设置成 DEMAND，这样对于默认的 SQL 语句都不使用查询缓存。而对于你确定要使用查询缓存的语句，可以用 SQL\_CACHE 显式指定，像下面这个语句一样：

```mysql
mysql> select sql_cache * from city where city_id = 1;
```

> 注意：MySQL 8.0 版本直接将查询缓存的整块功能删掉了

####  **第三步：分析SQL语句**

如果查询缓存没有命中，接下来就需要进入正式的查询阶段了。客户端程序发送过来的请求，实际上只是一个字符串而已，所以MySQL服务器程序首先需要对这个字符串做分析，判断请求的语法是否正确，然后从字符串中将要查询的表、列和各种查询条件都提取出来，本质上是对一个SQL语句编译的过程，涉及**词法解析**、**语法分析**、**预处理器**等。

- **词法分析：**词法分析就是把一个完整的 SQL 语句分割成一个个的字符串
- **语法分析：**语法分析器根据词法分析的结果做语法检查，判断你输入的SQL 语句是否满足 MySQL语法。
- **预处理器：**预处理器则会进一步去检查解析树是否合法，比如表名是否存在，语句中表的列是否存在等等，在这一步MySQL会检验用户是否有表的操作权限。

#####  **1）词法分析：**

比如：这条简单的SQL语句，会被分割成10个字符串

```mysql
# 分隔前
select c_id,first_name,last_name from customer where c_id=14;
# 分隔后
select，c_id,first_name,last_name，from，customer，where，c_id，=，14
```

MySQL 同时需要识别出这个SQL语句中的字符串**分别是什么，代表什么**。

- 把"select"这个关键字识别出来，这是一个查询语句
- 把“customer”识别成“表名 customer”
- 把“c_id识别成“列 c_id”。

##### **2）语法分析：**

如果语法正确就会根据 MySQL语法规则与SQL 语句生成一个数据结构，这个数据结构我们把它叫做**解析树**。

> "You have an error in your SQL syntax"错误提醒就是在这个位置出现的。

比如：下面这个语句

```mysql
mysql> select c_id,first_name,last_name form customer where c_id=14;

[Err] 1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'customer where c_id=14' at line 1
# 错误原因：from 写为 form
```

解析树举例：

![解析树](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%875.png)

#####  **3）预处理器：**

预处理器则会进一步去检查**解析树**是否合法，比如表名是否存在，语句中表的列是否存在等等，在这一步MySQL会检验用户是否有表的操作权限。

预处理之后会得到一个新的解析树，然后调用对应执行模块

####  **第四步：优化SQL语句**

**优化器顾名思义就是对查询进行优化。作用是根据解析树生成不同的执行计划，然后选择最优的执行计划。**

MySQL 里面使用的是基于成本模型的优化器，哪种**执行计划Explain**执行时成本最小就用哪种。而且它是io\_cost和cpu\_cost的开销总和，它通常也是我们评价一个查询的执行效率的一个常用指标。

```mysql
# 查看上次查询成本开销，默认值是0
show status like 'Last_query_cost';
```

优化器可以做哪些优化呢？

1.  当有多个索引可用的时候，决定使用哪个索引；

2.  在一个语句有多表关联（join）的时候，决定各个表的连接顺序，以哪个表为基准表。

**举个栗子：**

（1）比如hello数据库中表customer上执行下面的语句，这个语句用到了两个索引 last\_name 和 address\_id 。

```mysql
# hello数据库中表customer
explain select * from customer where last_name='刘皇叔' and address_id=11;
```

-  既可以使用last\_name索引查询，然后过滤列address\_id
- 也可以使用address\_id索引查询，然后过滤列last\_name。

两种执行计划的结果是一样的，但是执行效率会有所不同，**而优化器的作用就是决定选择使用哪一个方案**。

使用explain工具可以查看优化器的执行计划

![explain](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%876.png)

注意：优化器最多就是辅助，作用很有限，我们的SQL语句不能依赖于MySQL的优化器去调优！如果SQL语句垃圾，则没有可优化的空间。优化SQL的根本在于掌握MySQL分析与调优知识。

####  **第五步：执行SQL语句**

#####  **1）判断执行权限**

开始执行的时候，要先判断一下你对这个表customer有没有执行查询的权限，如果没有，就会返回没有权限的错误。

#####  **2）调用存储引擎接口查询**

如果有权限，就使用指定的存储引擎打开表开始查询。执行器会根据表的引擎定义，去使用这个引擎提供的查询接口提取数据。

- **c_id**是主键执行流程：
  - 调用 InnoDB 引擎接口，从主键索引中检索c_id=14的记录。
  - 主键索引等值查询只会查询出一条记录，直接将该记录返回客户端。
  - 至此，这个语句就执行完成了。
  
- **c_id**不是主键执行流程：全表扫描

  - 调用 InnoDB 引擎接口取这个表的第一行，判断c_id 值是不是 14，如果不是则跳过，如果是
  - 则将这行缓存在结果集中；
  - 调用引擎接口取“下一行”，重复相同的判断逻辑，直到取到这个表的最后一行。
  - 执行器将上述遍历过程中所有满足条件的行组成的结果集返回给客户端。

  - 至此，这个语句就执行完成了。

**举个栗子：**

比如：我们新建一个用户mysql\_user，只有表actor的查询权限，没有表customer的查询权限。

```mysql
CREATE USER `mysql_user`@`localhost` IDENTIFIED BY '123456@heroA';
GRANT Select ON TABLE `hello`.`test` TO `mysql_user`@`localhost`;
```

使用这个用户mysql\_user连接mysql，执行下面的查询语句，就会返回没有权限的错误。

```mysql
mysql -umysql_user -p123456@heroA
mysql> select * from customer where c_id=14;
```

![无权限错误](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%877.png)

#####  **3）案例：存储引擎查看与设置**

在MySQL中可以使用不同的存储引擎

```mysql
# 查看支持的存储引擎
> show engines
```

![show engines](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%878.png)

只有InnoDB引擎支持事务、行锁、外键。

在选择引擎时尽可能使用InnoDB引擎。

- MyISAM：早期版本默认的引擎。
- Memory：所有的数据都是保存在内存中。

```mysql
# 使用其他引擎，在mysql中默认使用InnoDB引擎，一个数据库中不同的表可以使用不同的引擎。
create table t_myisam(a int primary key, b int) engine=myisam;
```

## 三、**MySQL的存储引擎之InnoDB**

###  **3.1 存储引擎种类**

| **存储引擎** | **说明**                                                     |
| ------------ | ------------------------------------------------------------ |
| **MyISAM**   | 高速引擎，拥有较高的插入，查询速度，**但不支持事务**         |
| **InnoDB**   | **5.5版本后MySQL的默认数据库存储引擎，支持事务和行级锁**，比MyISAM处理速度稍慢 |
| ISAM         | MyISAM的前身，MySQL5.0以后不再默认安装                       |
| MRG_MyISAM   | 将多个表联合成一个表使用，在超大规模数据存储时很有用         |
| **Memory**   | **内存存储引擎，拥有极高的插入，更新和查询效率。**但是会占用和数据量成正比的内存空间。只在内存上保存数据，意味着数据可能会丢失。 |
| Falcon       | 一种新的存储引擎，支持事务                                   |
| Archive      | 将数据压缩后进行存储，非常适合存储大量的独立的，作为历史记录的数据，但是只能进行插入和查询操作 |
| CSV          | CSV 存储引擎是基于 CSV 格式文件存储数据(应用于跨平台的数据交换) |

**引擎怎么选择？**

归纳为一句话：**除非需要用到某些InnoDB不具备的特性，并且没有其他办法可以替代，否则都应该选择InnoDB引擎。**也就是说，大部分情况下都选择InnoDB。

**InnoDB和MyISAM存储引擎区别：**

| **比较项**   | **Innodb**                                   | **Myisam**                                            |
| :----------- | :------------------------------------------- | ----------------------------------------------------- |
| **存储文件** | .frm 表定义文件<br />.ibd 数据文件和索引文件 | .frm 表定义文件<br />.myd 数据文件<br />.myi 索引文件 |
| **锁**       | 表锁、行锁                                   | 表锁                                                  |
| **事务**     | 支持                                         | 不支持                                                |
| **CRDU**     | 读、写                                       | 读多                                                  |
| **索引结构** | B+ Tree                                      | B+ Tree                                               |

### **3.2 InnoDB架构图**

![InnoDB架构图](C:/Users/Administrator/Desktop/markdown%E5%9B%BE%E7%89%87%E4%B8%B4%E6%97%B6%E5%AD%98%E5%82%A8/%E5%9B%BE%E7%89%8710.png)

上图详细展示了InnoDB存储引擎的体系架构，从图中可见，InnoDB存储引擎由**内存结构、磁盘结构**两部分组成。

### **3.3 内存结构**

InnoDB 内存结构主要分为如下四个区域：

1.  Buffer Pool 缓冲池

2.  Change Buffer 修改缓冲

3.  Adaptive Hash Index 自适应索引

4.  Log Buffer 日志缓冲

#### **1）缓冲池(Buffer Pool)**

缓冲池Buffer Pool 用于加速数据的访问和修改，通过将热点数据缓存在内存的方法，最大限度地减少磁盘 IO，加速热点数据读写。

- 默认大小为128M，Buffer Pool 中数据**以页为存储单位**，其实现的数据结构是**以页为单位的单链表**。

- 由于内存的空间限制，Buffer Pool 仅能容纳最热点的数据。

- Buffer Pool 使用LRU算法（Least Recently Used 最近最少使用）淘汰非热点数据页。

- 对于 Buffer Pool 中数据的查询，InnoDB 直接读取返回；

- 对于 Buffer Pool 中数据的修改，InnoDB 直接在 Buffer Pool 中修改，并将修改写入 redo Log 中，当数据页被 LRU 算法淘汰时写入磁盘，若持久化前系统崩溃，则在重启后使用 redo Log 进行恢复。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8713.png)

```mysql
# 查看innodb存储引擎状态，包含缓冲池、修改缓冲、自适应哈希状态信息、日志缓冲等信息...
mysql> show engine innodb status;
# 查看InnoDB的Buffer Pool大小
mysql> show variables like 'innodb_buffer_pool_size';
```

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8712.png)

#### **2）修改缓冲(Change Buffer)**

Change Buffer（在 MySQL 5.6 之前叫 insert buffer，简称 ibuf ）是 InnoDB 5.5 引入的一种优化策略。**Change Buffer 用于加速非热点数据中二级索引的写入操作。**由于二级索引数据的不连续性，导致修改二级索引时需要进行频繁的磁盘 IO 消耗大量性能，Change Buffer 缓冲对二级索引的修改操作，同时将写操作录入 redo log 中，在缓冲到一定量或系统较空闲时进行`merge`操作将修改写入磁盘中。 Change Buffer 在系统表空间中有相应的持久化区域。

Change Buffer 大小默认占 Buffer Pool 的 25%，最大50%，在引擎启动时便初始化完成。**其物理结构为一棵名为**`ibuf`**的 B Tree。**

> **二级索引**就是辅助索引，除了聚簇索引之外的所有索引都是二级索引。
>
> **聚簇索引**也叫聚集索引，索引组织表，指的**是一种数据存储方式，指数据与索引的数据结构存储在一起**。如 InnoDB 的主键索引中所有叶子节点都存储了对应行的数据。因为数据肯定只是存储在一个地方，所以一个表只能有一个聚集索引。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL进阶/%E5%9B%BE%E7%89%8715.png)

- 周期性合并二级索引页

- 周期性净化磁盘中二级索引页


![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8716.png)

#### **3）自适应哈希索引(AHI)**

**自适应哈希索引**（Adaptive Hash Index，**AHI**）**用于实现对于热数据页的一次查询。**是建立在索引之上的索引！使用聚簇索引进行数据页定位的时候需要根据索引树的高度从根节点走到叶子节点，通常需要 3 到 4 次查询才能定位到数据。InnoDB 根据对索引使用情况的分析和索引字段的分析，通过自调优Self-tuning的方式为索引页建立或者删除哈希索引。

AHI 的大小为 Buffer Pool 的 1/64，在 MySql 5.7 之后支持分区，以减少对于全局 AHI 锁的竞争，默认分区数为 8。

AHI 所作用的目标是频繁查询的数据页和索引页，而由于数据页是聚簇索引的一部分，因此 AHI 是建立在索引之上的索引，**对于二级索引，若命中 AHI，则将直接从 AHI 获取二级索引页的记录指针，再根据主键沿着聚簇索引查找数据；若聚簇索引查询同样命中 AHI，则直接返回目标数据页的记录指针，此时就可以根据记录指针直接定位数据页**。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8717.png)

```mysql
# 查看innodb存储引擎状态，包含自适应哈希状态信息
mysql> show engine innodb status;
# 查看是否开启自适应哈希配置，默认是开启的
mysql> show variables like 'innodb_adaptive_hash_index';
```

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8718.png)

#### **4）日志缓冲(Log Buffer)**

**InnoDB 使用 Log Buffer 来缓冲日志文件的写入操作。**内存写入加上日志文件顺序写的特点，使得 InnoDB 日志写入性能极高。

对于任何修改操作，都将录入诸如 redo log 与 undo log 这样的日志文件中，因此日志文件的写入操作非常频繁，却又十分零散。这些文件都存储在磁盘中，因此日志记录将引发大量的磁盘 IO。Log Buffer 将分散的写入操作放在内存中，通过定期批量写入磁盘的方式提高日志写入效率和减少磁盘 IO。

 注意：这种将分散操作 改为 批量操作的优化方式将增加数据丢失的风险！

### **3.4 磁盘文件之表空间**

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8719.png)

**在磁盘中，InnoDB 将所有数据都逻辑地存放在一个空间中，称为表空间（Tablespace）。表空间由段（Segment）、区（extent）、页（Page）组成。**

- 开启独立表空间innodb_file_per_table=1，每张表的数据都会存储到一个**独立表空间**，即 表名.ibd 文件
- 关闭独占表空间innodb_file_per_table=0，则所有基于InnoDB存储引擎的表数据都会记录到**系统表空间**，即 ibdata1 文件

表空间是 InnoDB 物理存储中的最高层，目前的表空间类别包括：

-  系统表空间（System Tablespace）

-  独立表空间（File-per-table Tablespace）

-  通用表空间（General Tablespace）

-  回滚表空间（Undo Tablespace）

-  临时表空间（The Temporary Tablespace）


![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8720.png)

#### **1）系统表空间**

**系统表空间是 InnoDB 数据字典、双写缓冲、修改缓冲和回滚日志的存储位置**，如果关闭独立表空间，它也将存储所有表数据和索引。

它默认下是一个初始大小 12MB、名为 ibdata1 的文件，系统表空间所对应的文件由`innodb_data_file_path`定义。

**指定系统表空间文件自动增长后，其增长大小由**`innodb_autoextend_increment`**设置（默认为64MB）且不可缩减，即使删除系统表空间中存储的表和索引，此过程释放的空间仅仅是在表空间文件中标记为已释放而已，并不会缩减其在磁盘中的大小**。

- **数据字典（Data Dictionary）**： 数据字典是由**各种表对象的元数据信息**（表结构，索引，列信息等）组成的内部表
- **双写缓冲（Doublewrite Buffer）**：双写缓冲用于保证写入磁盘时页数据的完整性，防止发生**部分写失效问题**。非常重要，在3.6小节重点介绍
- **修改缓冲（Change Buffer）**： 内存中 Change Buffer 对应的持久化区域
- **回滚日志（Undo Log）**：实现事务进行 **回滚** 操作时对数据的恢复。是实现**多版本并发控制（MVCC）**重要组成。在事务篇详细讲解

#### **2）独立表空间**

**独立表空间用于存放每个表的数据、索引和插入缓冲 Bitmap 页。**其他类型的信息，如：回滚信息、插入缓冲索引页、系统事务信息、二次写缓冲等仍存放于系统表空间内。因此即使用了独立表空间，系统表空间也会不断增长。

开启独立表空间（File-per-table TableSpace）（ innodb\_file\_per\_table=ON ）之后，InnoDB 会为每个数据库单独创建子文件夹，数据库文件夹内为每个数据表单独建立一个表空间文件 table.ibd 。同时创建一个 table.frm 文件用于保存表结构信息。

每个独立表空间的初始大小是 96KB。

#### **3）其他**

##### 1. **通用表空间**

通用表空间（General Tablespace）是一个由 CREATE TABLESPACE 命令创建的共享表空间，创建时必须指定该表空间名称和 ibd 文件位置，ibd 文件可以放置于任何 MySQL 有权限的地方。该表空间内可以容纳多张数据表，同时在创建时可以指定该表空间所使用的默认引擎。

**通用表空间存在的目的是为了在系统表空间与独立表空间之间作出平衡**。系统表空间与独立表空间中的表可以向通用表空间移动，反之亦可，但系统表空间中的表无法直接与独立表空间中的表相互转化。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8721.png)

##### **2. Undo 表空间**

Undo TableSpace 用于存放一个或多个 undo log 文件。**默认** **undo log** **存储与系统表空间中，MySql5.6 以后支持自定义 Undo log 表空间并存储所有 undo log**。一旦用户定义了 Undo Tablespace，则系统表空间中的 Undo log 区域将失效。对于 Undo Tablespace 的启用必须在 MySQL 初始化前设置， Undo Tablespace 默认大小为 10MB。Undo Tablespace 中的 Undo log 表可以进行 truncate 操作。

##### **3. 临时表空间**

MySQL 5.7 之前临时表存储在系统表空间中，这样会导致 ibdata 在使用临时表的场景下疯狂增长。**5.7版本之后 InnoDB 引擎从系统表空间中抽离出临时表空间（Temporary Tablespace），用于独立保存临时表数据及其回滚信息**。该表空间文件路径由`innodb_temp_data_file_path` 指定，但必须继承`innodb_data_home_dir`。

### **3.5 磁盘文件之存储结构**

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8722.png)

#### **1）段【Segment】**

**表空间由各个段（Segment）组成，创建的段类型分为数据段、索引段、回滚段等**。由于 InnoDB 采用聚簇索引与 B+ 树的结构存储数据，所以事实上数据页和二级索引页仅仅只是 B+ 树的叶子节点，因此数据段称为 Leaf node segment，**索引段其实指的是** **B+** **树的非叶子节点，称为** **Non-Leaf node segment**。一个段会包含多个区，至少会有一个区，段扩展的最小单位是区。【在索引篇会详细讲解B+数据结构】

-  **数据段**称为 Leaf node segment


-  **索引段**称为 Non-Leaf node segment


#### **2）区【Extent】**

 **区（Extend）是由连续的页组成的空间，大小固定为 1MB**，由于默认页大小为 16K，因此一个区默认存储 64 个连续的页。如果页大小调整为 4K，则 256 个连续页组成一个区。**为了保证页的连续性，** **InnoDB 存储引擎会一次从磁盘申请 4 ~ 5 个区**。

#### **3）页【Page】**

**页（Page）是 InnoDB 的基本存储单位**，每个页大小默认为 16K，从 InnoDB1.2.x 版本开始，可通过设置 innodb\_page\_size 修改为 4K、8K、16K。InnoDB 首次加载后便无法更改。

```mysql
# 查看MySQL页大小
show variables like 'innodb_page_size';
```

MySQL规定一个页上最少存储2个数据项。如果向一个页插入数据时，这个页已将满了，就会从区中分配一个新页。如果向索引树叶子节点中间的一个页中插入数据，如果这个页是满的，就会发生页分裂。

操作系统管理磁盘的最小单位也是页，是操作系统读写磁盘最小单位，Linux中页一般是4K，可以通过命令查看。

```shell
# 默认 4096 4K
getconf PAGE_SIZE
```

所以InnoDB从磁盘中读取一个数据页时，操作系统会分4次从磁盘文件中读取数据到内存。写入也是一样的，需要分4次从内存写入到磁盘中。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/MYSQL%E8%BF%9B%E9%98%B6/%E5%9B%BE%E7%89%8723.png)

#### **4）行【Row】**

InnoDB的数据是以行为单位存储的，1个页中包含多个行。在MySQL5.7中，InnoDB提供了4种行格式：Compact、Redundant、Dynamic和Compressed行格式，Dynamic为MySQL5.7默认的行格式。

创建表时可以指定行格式：

```mysql
CREATE TABLE t1 (c1 INT) ROW_FORMAT=DYNAMIC;
#修改表的行格式
ALTER TABLE tablename ROW_FORMAT=行格式名称;
#修改默认行格式
SET GLOBAL innodb_default_row_format=DYNAMIC;
#查看表行格式
SHOW TABLE STATUS LIKE 't1';
```

> InnoDB行格式官网：[<span class="underline">https://dev.mysql.com/doc/refman/5.7/en/innodb-row-format.html</span>](https://dev.mysql.com/doc/refman/5.7/en/innodb-row-format.html)
