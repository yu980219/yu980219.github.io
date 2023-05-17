---
title: ShardingSphere-JDBC
tags: [分库分表]
categories:
  - 后端
  - 分库分表
index_img: /img/default.jpeg
date: 2022-07-23 12:48:19
---
# ShardingSphere-JDBC

FROM：索尔老师

## 一、ShardingSphere介绍

### 1.Apache ShardingSphere

Apache ShardingSphere 是一套开源的分布式数据库解决方案组成的生态圈，它由 JDBC、Proxy 和 Sidecar（规划中）这 3 款既能够独立部署，又支持混合部署配合使用的产品组成。它们均提供标准化的数据水平扩展、分布式事务和分布式治理等功能，可适用于如 Java 同构、异构语⾔、云原生等各种多样化的应用场景。

Apache ShardingSphere 旨在充分合理地在分布式的场景下利用关系型数据库的计算和存储能力，而并非实现一个全新的关系型数据库。 关系型数据库当今依然占有巨大市场份额，是企业核心系统的基石，未来也难于撼动，我们更加注重在原有基础上提供增量，而非颠覆。

ShardingSphere 已于2020年4月16日成为 Apache 软件基金会的顶级项目。

### 2.ShardingSphere-JDBC

定位为轻量级 Java 框架，在 Java 的 JDBC 层提供的额外服务。 它使用客户端直连数据库，以 jar 包形式提供服务，无需额外部署和依赖，可理解为增强版的 JDBC 驱动，完全兼容JDBC 和各种 ORM 框架。

- 适用于任何基于 JDBC 的 ORM 框架，如：JPA, Hibernate, Mybatis, Spring JDBCTemplate 或直接使用 JDBC；
- 支持任何第三方的数据库连接池，如：DBCP, C3P0, BoneCP, HikariCP 等；
- 支持任意实现 JDBC 规范的数据库，目前支持 MySQL，PostgreSQL，Oracle，SQLServer 以及任何可使用 JDBC 访问的数据库。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230517235839013.png)

### 3.ShardingSphere-Proxy

定位为透明化的数据库代理端，提供封装了数据库⼆进制协议的服务端版本，用于完成对异构语⾔的支持。 目前提供 MySQL 和 PostgreSQL（兼容 openGauss 等基于 PostgreSQL 的数据库）版本，它可以使用任何兼容MySQL/PostgreSQL 协议的访问客户端（如：MySQLCommand Client, MySQL Workbench, Navicat 等）操作数据，对 DBA 更加友好。

- 向应用程序完全透明，可直接当做 MySQL/PostgreSQL 使用；
- 适用于任何兼容 MySQL/PostgreSQL 协议的的客户端。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518000240949.png)

### 4.ShardingSphere-Sidecar（TODO）

定位为 Kubernetes 的云原生数据库代理，以 Sidecar 的形式代理所有对数据库的访问。 通过无中心、零侵入的方案提供与数据库交互的啮合层，即 `Database Mesh `，又可称数据库网格。

Database Mesh 的关注重点在于如何将分布式的数据访问应用与数据库有机串联起来，它更加关注的是交互，是将杂乱无章的应用与数据库之间的交互进行有效地梳理。 使⽤Database Mesh，访问数据库的应用和数据库终将形成一个巨大的网格体系，应用和数据库只需在网格体系中对号入座即可，它们都是被啮合层所治理的对象。

![image-20230518000358776](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518000358776.png)

|            | ShardingSphere-JDBC | ShardingSphere-Proxy | ShardingSphere-Sidecar |
| ---------- | ------------------- | -------------------- | ---------------------- |
| 数据库     | 任意                | MySQL/PostgreSQL     | MySQL/PostgreSQL       |
| 连接消耗数 | 高                  | 低                   | 高                     |
| 异构语⾔   | 仅 Java             | 任意                 | 任意                   |
| 性能       | 损耗低              | 损耗略高             | 损耗低                 |
| 无中心化   | 是                  | 否                   | 是                     |
| 静态入口   | 无                  | 有                   | 无                     |

## 二、快速开始

### 1.部署第一台MySQL服务器

使⽤docker部署第一台MySQL服务器，先来快速体验ShardingSphere带来的分表功能。在之后的分库分表中，再加入第⼆台MySQL服务器。

```yml
version: '3.1'
services:
  mysql-0:
    image: mysql
    container_name: mysql-0
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
```

使用`docker-compose up -d`命令启动

### 2.创建数据库

创建名为`db_device_0`的数据库。

### 3.创建物理表

逻辑上tb_device表示的是描述设备信息的表，为了体现分表的概念，把tb_device表分成了两张。于是tb_device就是逻辑表，⽽tb_device_0和tb_device_1就是该逻辑表的物理表。

- 创建`tb_device_0`表

```mysql
CREATE TABLE `tb_device_0` (
 `device_id` bigint NOT NULL AUTO_INCREMENT,
 `device_type` int DEFAULT NULL,
 PRIMARY KEY (`device_id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb3;
```

- 创建`tb_device_1`表

```mysql
CREATE TABLE `tb_device_1` (
 `device_id` bigint NOT NULL AUTO_INCREMENT,
 `device_type` int DEFAULT NULL,
 PRIMARY KEY (`device_id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb3;
```

### 4.创建ShardingSphere项目并引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project
    xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.qf</groupId>
    <artifactId>my-sharding-jdbc-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>my-sharding-jdbc-demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.7.RELEASE</spring-boot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.22</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.1.1</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.3.7.RELEASE</version>
                <configuration>
                    <mainClass>com.qf.my.sharding.jdbc.demo.MyShardingJdbcDemoApplication</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

### 5.编写配置⽂件application.properties

```properties
# 配置真实数据源
spring.shardingsphere.datasource.names=ds1

# 配置第 1 个数据源
spring.shardingsphere.datasource.ds1.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.ds1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds1.url=jdbc:mysql://172.16.253.84:3306/db_device_0?serverTimezone=Asia/Shanghai
spring.shardingsphere.datasource.ds1.username=root
spring.shardingsphere.datasource.ds1.password=123456

# 配置物理表
spring.shardingsphere.sharding.tables.tb_device.actual-data-nodes=ds1.tb_device_$->{0..1}

# 配置分表策略：根据device_id作为分片的依据（分片键）
spring.shardingsphere.sharding.tables.tb_device.table-strategy.inline.sharding-column=device_id
spring.shardingsphere.sharding.tables.tb_device.table-strategy.inline.algorithm-expression=tb_device_$->{device_id%2}

# 开启SQL显示
spring.shardingsphere.props.sql.show = true
```

### 6.创建逻辑表对应的实体

```java
package com.qf.my.sharding.jdbc.demo.entity;
import lombok.Data;

@Data
public class TbDevice {
	private long deviceId;
	private int deviceType;
}
```

### 7.使用MyBatis-Plus做映射

```java
package com.qf.my.sharding.jdbc.demo.mapper;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.qf.my.sharding.jdbc.demo.entity.TbDevice;

public interface DeviceMapper extends BaseMapper<TbDevice> {
}
```

### 8.配置Springboot启动类

关键是配置MapperScan

```java
package com.qf.my.sharding.jdbc.demo;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.qf.my.sharding.jdbc.demo.mapper")
public class MyShardingJdbcDemoApplication {
	public static void main(String[] args) {
		SpringApplication.run(MyShardingJdbcDemoApplication.class,args);
	}
}
```

### 9.编写单元测试

尝试插入10条device数据，因为分片键是device_id，且分片策略是 `tb_device_$->{device_id%2}` ，因此会根据device_id来决定该条数据会插入到哪张物理表中。

```java
package com.qf.my.sharding.jdbc.demo;
import com.qf.my.sharding.jdbc.demo.entity.TbDevice;
import com.qf.my.sharding.jdbc.demo.mapper.DeviceMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class MyShardingJdbcDemoApplicationTests {
    
	@Autowired
	private DeviceMapper deviceMapper;
    
	@Test
	void initData(){
		for (int i = 0; i < 10; i++) {
			TbDevice device = new TbDevice();
			device.setDeviceId((long) i);
			device.setDeviceType(i);
			deviceMapper.insert(device);
		}
	}
}
```

发现，根据分片策略，这10条数据中id是奇数的数据将会被插入到tb_device_1表中，id是奇数的数据将会被插入到tb_device_0表中。

## 三、尝试分库分表

### 1.准备第二台MySQL服务器

使⽤docker创建第⼆台MySQL服务器。

```yml
version: '3.1'
services:
  mysql-1:
    image: mysql
    container_name: mysql-1
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
```

创建db_device_1数据库。并在数据库中创建两张物理表：

- 创建tb_device_0表

```mysql
CREATE TABLE `tb_device_0` (
 `device_id` bigint NOT NULL AUTO_INCREMENT,
 `device_type` int DEFAULT NULL,
 PRIMARY KEY (`device_id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb3;
```

- 创建tb_device_1表

```mysql
CREATE TABLE `tb_device_1` (
 `device_id` bigint NOT NULL AUTO_INCREMENT,
 `device_type` int DEFAULT NULL,
 PRIMARY KEY (`device_id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb3;
```

### 2.配置数据库源

提供两个数据源，将之前搭建的两台MySQL服务器作为数据源。

```properties
# 配置真实数据源
spring.shardingsphere.datasource.names=ds0,ds1

# 配置第 1 个数据源
spring.shardingsphere.datasource.ds0.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.ds0.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds0.url=jdbc:mysql://172.16.253.84:3306/db_device_0?serverTimezone=Asia/Shanghai
spring.shardingsphere.datasource.ds0.username=root
spring.shardingsphere.datasource.ds0.password=123456

# 配置第 2 个数据源
spring.shardingsphere.datasource.ds1.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.ds1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds1.url=jdbc:mysql://172.16.253.85:3306/db_device_1?serverTimezone=Asia/Shanghai
spring.shardingsphere.datasource.ds1.username=root
spring.shardingsphere.datasource.ds1.password=123456
```

### 3.配置数据库表的分片策略

```properties
# 配置分库的分片策略，根据device_id进行分片，奇偶不同进入不同的数据库：ds1,ds2
spring.shardingsphere.sharding.default-database-strategy.inline.sharding-column=device_id
spring.shardingsphere.sharding.default-database-strategy.inline.algorithm-expression=ds$->{device_id % 2}

# 根据groovy脚本配置数据源+表名
spring.shardingsphere.sharding.tables.tb_device.actual-data-nodes=ds$->{0..1}.tb_device_$->{0..1}

# 配置分表策略
spring.shardingsphere.sharding.tables.tb_device.table-strategy.inline.sharding-column=device_id
spring.shardingsphere.sharding.tables.tb_device.table-strategy.inline.algorithm-expression=tb_device_$->{device_id%2}

# 开启显示sql语句
spring.shardingsphere.props.sql.show = true
```

相比之前的配置，这次加入了两个数据库的分片策略，根据device_id的奇偶特性决定存入哪个数据库中。同时，使用groovy脚本确定了数据库和表之间的关系。

```groovy
ds$->{0..1}.tb_device_$->{0..1}
```

等效于：

```
ds0.tb_device_0
ds0.tb_device_1
ds1.tb_device_0
ds1.tb_device_1
```

此时再运行测试用例，发现device_id的奇数数据会存入 `ds1.tb_device_1` 表中，偶数数据会存入 `ds0.tb_device_0` 表中。

### 4.在分库分表下做查询

```java
@Test
 void queryDeviceByID(){
 QueryWrapper<TbDevice> queryWrapper = new QueryWrapper<>();
 queryWrapper.eq("device_id",1L);
 List<TbDevice> deviceList =
deviceMapper.selectList(queryWrapper);
 System.out.println(deviceList);
 }
```

### 5.分库分表存在的问题

```java
@Test
void queryDeviceByID(){
	QueryWrapper<TbDevice> queryWrapper = new QueryWrapper<>();
	queryWrapper.eq("device_id",1L);
	List<TbDevice> deviceList =
	deviceMapper.selectList(queryWrapper);
	System.out.println(deviceList);
}
```

使⽤device_id做范围查询时，发现报错了：inline的分片策略没有办法支持范围查询。

\### Cause: java.lang.IllegalStateException: Inline strategy cannot support this type sharding:RangeRouteValue(columnName=device_id, tableName=tb_device, valueRange=[1‥10])

接下来需要掌握的是分片策略

## 四、分库分表核心知识点

### 1.核心概念

在了解分片策略之前，先来了解以下几个重点概念：逻辑表、真实表、数据节点、绑定表、⼴播表。

- 逻辑表

  水平拆分的数据库（表）的相同逻辑和数据结构表的总称。例：订单数据根据主键尾数拆分为10张表，分别是 `t_order_0` 到 `t_order_9` ，他们的逻辑表名为 `t_order` 。

- 真实表

  在分片的数据库中真实存在的物理表。即上个示例中的 `t_order_0` 到 `t_order_9` 。

- 数据节点

  数据分片的最小单元。由数据源名称和数据表组成，例： `ds_0.t_order_0` 。

- 绑定表

  指分片规则一致的主表和子表。例如： `t_order` 表和 `t_order_item` 表，均按照 `order_id` 分片，则此两张表互为绑定表关系。绑定表之间的多表关联查询不会出现笛卡尔积关联，关联查询效率将大大提升。举例说明，如果SQL为：

  ```mysql
  SELECT i.* FROM t_order o JOIN t_order_item i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  ```

  在不配置绑定表关系时，假设分片键 order_id 将数值10路由至第0⽚，将数值11路由至第1片，那么路由后的SQL应该为4条，它们呈现为笛卡尔积：

  ```mysql
  SELECT i.* FROM t_order_0 o JOIN t_order_item_0 i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  SELECT i.* FROM t_order_0 o JOIN t_order_item_1 i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  SELECT i.* FROM t_order_1 o JOIN t_order_item_0 i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  SELECT i.* FROM t_order_1 o JOIN t_order_item_1 i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  ```

  在配置绑定表关系后，路由的SQL应该为2条：

  ```mysql
  SELECT i.* FROM t_order_0 o JOIN t_order_item_0 i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  SELECT i.* FROM t_order_1 o JOIN t_order_item_1 i ON o.order_id=i.order_id WHERE o.order_id in (10, 11);
  ```

  其中 `t_order` 在FROM的最左侧，ShardingSphere将会以它作为整个绑定表的主表。 所有路由计算将会只使用主表的策略，那么 `t_order_item` 表的分片计算将会使用 `t_order` 的条件。故绑定表之间的分区键要完全相同。

- ⼴播表

  指所有的分片数据源中都存在的表，表结构和表中的数据在每个数据库中均完全一致。适用于数据量不大且需要与海量数据的表进行关联查询的场景，例如：字典表。

### 2.分片及分片策略

#### 1）分片键

用于分片的数据库字段，是将数据库(表)水平拆分的关键字段。例：将订单表中的订单主键的尾数取模分片，则订单主键为分片字段。 SQL中如果无分片字段，将执行全路由，性能较差。 除了对单分片字段的支持，ShardingSphere也支持根据多个字段进行分片。

#### 2）分片算法

通过分片算法将数据分片，支持通过 `=` 、 `>=` 、 `<=` 、 `>` 、 `<` 、 `BETWEEN` 和 `IN` 分片。分片算法需要应用方开发者自行实现，可实现的灵活度非常高。

目前提供4种分片算法。由于分片算法和业务实现紧密相关，因此并未提供内置分片算法，而是通过分片策略将各种场景提炼出来，提供更高层级的抽象，并提供接口让应用开发者自行实现分片算法。

- 精确分片算法

  对应`PreciseShardingAlgorithm`，用于处理使用单一键作为分片键的=与IN进行分片的场景。需要配合`StandardShardingStrategy`使用。

- 范围分片算法

  对应`RangeShardingAlgorithm`，用于处理使用单一键作为分片键的BETWEEN AND、>、<、>=、<=进行分片的场景。需要配合`StandardShardingStrategy`使用。

- 复合分片算法

  对应C`omplexKeysShardingAlgorithm`，用于处理使用多键作为分片键进行分片的场景，包含多个分片键的逻辑较复杂，需要应用开发者自行处理其中的复杂度。需要配合`ComplexShardingStrategy`使用。

- Hint分片算法

  对应`HintShardingAlgorithm`，用于处理使⽤Hint⾏分片的场景。需要配合`HintShardingStrategy`使用

#### 3）分片策略

包含分片键和分片算法，由于分片算法的独立性，将其独立抽离。真正可用于分片操作的是分片键 + 分片算法，也就是分片策略。目前提供5种分片策略。

- 标准分片策略

  对应StandardShardingStrategy。

  提供对SQL语句中的=, >, <, >=, <=, IN和BETWEEN AND的分片操作支持。

  StandardShardingStrategy只支持单分片键，提供PreciseShardingAlgorithm和RangeShardingAlgorithm两个分片算法。

  PreciseShardingAlgorithm是必选的，用于处理=和IN的分片。

  RangeShardingAlgorithm是可选的，用于处理BETWEEN AND, >, <, >=, <=分片，如果不配置RangeShardingAlgorithm，SQL中的BETWEEN AND将按照全库路由处理。

- 复合分片策略

  对应ComplexShardingStrategy。

  复合分片策略。提供对SQL语句中的=, >, <, >=, <=, IN和BETWEEN AND的分片操作支持。

  ComplexShardingStrategy⽀持多分片键，由于多分片键之间的关系复杂，因此并未进行过多的封装，而是直接将分片键值组合以及分片操作符透传至分片算法，完全由应用开发者实现，提供最大的灵活度。

- 行表达式分片策略

  对应InlineShardingStrategy。

  使⽤Groovy的表达式，提供对SQL语句中的=和IN的分片操作支持，只支持单分片键。

  对于简单的分片算法，可以通过简单的配置使用，从而避免繁琐的Java代码开发，如: `t_user_$->{u_id % 8}` 表示t_user表根据u_id模8，而分成8张表，表名称为 `t_user_0` 到 `t_user_7` 。

- Hint分片策略

  对应HintShardingStrategy。通过Hint指定分片值而非从SQL中提取分片值的方式进行分片的策略。

- 不分片策略

  对应NoneShardingStrategy。不分片的策略。

### 3.分片策略的实现

#### 1）Standard标准分片策略的精准分片

在Standard标准分片策略可以分别配置在分库和分表中。配置时需要指明分片键，精确分片或范围分片。

- 配置分库的精确分片

  ```properties
  spring.shardingsphere.sharding.default-databasestrategy.standard.sharding-column=device_id
  spring.shardingsphere.sharding.default-databasestrategy.standard.precise-algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.database.MyDatabaseStandardPreciseAlgorithm
  ```

  需要提供一个实现精确分片算法的实现类，其中精确分片的逻辑可以与inline中的行表达式用意相同。

  ```java
  package com.qf.my.sharding.jdbc.demo.sharding.algorithm.database;
  import org.apache.shardingsphere.api.sharding.standard.PreciseShardingAlgorithm;
  import org.apache.shardingsphere.api.sharding.standard.PreciseShardingValue;
  import java.util.Collection;
  
  public class MyDatabaseStandardPreciseAlgorithm implements PreciseShardingAlgorithm<long> {
      
      /**
       * 数据库的标准分片策略
       * @param collection 具体的物理库
       * @param preciseShardingValue 分片条件
       * @return
       */
      @Override
      public String doSharding(Collection<String> collection,PreciseShardingValue<long> preciseShardingValue) {
          //获得逻辑表名
          String logicTableName =preciseShardingValue.getLogicTableName();
          //分片键，列名
          String columnName = preciseShardingValue.getColumnName();
          //分片键的具体值
          long value = preciseShardingValue.getValue();
          //根据分片策略：ds$->{device_id % 2} 做精确分片
          String shardingKey = "ds" + (value % 2);
          if(!collection.contains(shardingKey)){
              throw new UnsupportedOperationException("数据库:"+shardingKey+"不存在");
          }
          return shardingKey;
  	}
  }
  ```
  
- 配置分表的精确分片

  ```properties
  # standard
  spring.shardingsphere.sharding.tables.tb_device.tablestrategy.standard.sharding-column=device_id
  # 精确查找的算法实现类
  spring.shardingsphere.sharding.tables.tb_device.tablestrategy.standard.precise-algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.table.MyTableStandardPreciseAlgorithm
  ```

  同时，需要提供分表的精确分片算法的实现类。

  ```java
  package com.qf.my.sharding.jdbc.demo.sharding.algorithm.table;
  import org.apache.shardingsphere.api.sharding.standard.PreciseShardingAlgorithm;
  import org.apache.shardingsphere.api.sharding.standard.PreciseShardingValue;
  import java.util.Collection;
  
  public class MyTableStandardPreciseAlgorithm implements PreciseShardingAlgorithm<long> {
       @Override
       public String doSharding(Collection<String> collection,PreciseShardingValue<long> preciseShardingValue) {
          //获得逻辑表名
          String logicTableName =preciseShardingValue.getLogicTableName();
          //分片键，列名
          String columnName = preciseShardingValue.getColumnName();
          //分片键的具体值
          long value = preciseShardingValue.getValue();
          //根据分片策略：tb_device_$->{device_id%2} 做精确分片
          String shardingKey = logicTableName + "_" + (value % 2);
          if (!collection.contains(shardingKey)) {
              throw new UnsupportedOperationException("表:" + shardingKey+ "不存在");
          }
  		return shardingKey;
  	}
  }
  ```
  
  尝试在之前的精确查找测试用例，发现与之前的效果相同，根据id定位到某个库的某张表中。
  
  ```java
  @Test
  void queryDeviceByID(){
  	QueryWrapper<TbDevice> queryWrapper = new QueryWrapper<>();
  	queryWrapper.eq("device_id",1L);
  	List<TbDevice> deviceList =	deviceMapper.selectList(queryWrapper);
  	System.out.println(deviceList);
  }
  ```

#### 2）Standard标准分片策略的范围分片

- 配置分库的范围分片

  ```properties
  spring.shardingsphere.sharding.default-database-strategy.standard.range-algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.database.MyDatabaseStandardRangeAlgorithm
  ```

  提供范围查询算法的实现类。

  ```java
  package com.qf.my.sharding.jdbc.demo.sharding.algorithm.database;
  import org.apache.shardingsphere.api.sharding.standard.RangeShardingAlgorithm;
  import org.apache.shardingsphere.api.sharding.standard.RangeShardingValue;
  import java.util.Arrays;
  import java.util.Collection;
  
  public class MyDatabaseStandardRangeAlgorithm implements RangeShardingAlgorithm<Long> {
   /**
   * select * from tb_device where id between (1,10);
   * 由于范围查询，需要在两个库的两张表中查。
   * @param collection
   * @param rangeShardingValue 提供了这次查询的条件 1,10
   * @return 返回要进行范围查询的库名
   */
   @Override
   public Collection<String> doSharding(Collection<String> collection,RangeShardingValue<Long> rangeShardingValue) {
   return Arrays.asList("ds0","ds1");
   }
  }
  ```
  
- 配置分表的范围分片

  ```properties
  spring.shardingsphere.sharding.tables.tb_device.tablestrategy.standard.range-algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.table.MyTableStandardRangeAlgorithm
  ```

  提供范围查询算法的实现类：

  ```java
  package com.qf.my.sharding.jdbc.demo.sharding.algorithm.table;
  import org.apache.shardingsphere.api.sharding.standard.RangeShardingAlgorithm;
  import org.apache.shardingsphere.api.sharding.standard.RangeShardingValue;
  import java.util.Arrays;
  import java.util.Collection;
  
  public class MyTableStandardRangeAlgorithm implements RangeShardingAlgorithm<long> {
  @Override
  public Collection<String> doSharding(Collection<String> collection,RangeShardingValue<long> rangeShardingValue) {
          //返回两种物理表
          String logicTableName = rangeShardingValue.getLogicTableName();
          return Arrays.asList(logicTableName+"_0",logicTableName+"_1");
  	}
  }
  ```
  
  此时，再运行范围查询的测试用例，发现成功了。
  
  ```java
  @Test
  void queryDeviceByRange(){
  	QueryWrapper<TbDevice> queryWrapper = new QueryWrapper<>();
  	queryWrapper.between("device_id",1,10);
  	List<TbDevice> deviceList =	deviceMapper.selectList(queryWrapper);
  	System.out.println(deviceList);
  }
  ```

#### 3) Complex分片策略

- 问题的出现

  ```java
  @Test
  void queryDeviceByRangeAndDeviceType(){
  	QueryWrapper<TbDevice> queryWrapper = new QueryWrapper<>();
  	queryWrapper.between("device_id",1,10);
  	queryWrapper.eq("device_type", 5);
  	List<TbDevice> deviceList =deviceMapper.selectList(queryWrapper);
  	System.out.println(deviceList);
  }
  ```
  
  在对device_id进行范围查询的同时，需要根据device_type做精确查找，发现此时也需要查两个库的三张表，但是奇数的device_type只会在奇数库的奇数表中，此时冗余了多次不必要的查询。
  
  ```
  INFO 5879 --- [ main] ShardingSphere-SQL : Actual SQL: ds0 ::: SELECT device_id,device_type FROM tb_device_0 
  WHERE device_id BETWEEN ? AND ? AND device_type = ? ::: [1, 10, 5]
  INFO 5879 --- [ main] ShardingSphere-SQL : Actual SQL: ds0 ::: SELECT device_id,device_type FROM tb_device_1 
  WHERE device_id BETWEEN ? AND ? AND device_type = ? ::: [1, 10, 5]
  INFO 5879 --- [ main] ShardingSphere-SQL : Actual SQL: ds1 ::: SELECT device_id,device_type FROM tb_device_0 
  WHERE device_id BETWEEN ? AND ? AND device_type = ? ::: [1, 10, 5]
  INFO 5879 --- [ main] ShardingSphere-SQL : Actual SQL: ds1 ::: SELECT device_id,device_type FROM tb_device_1 
  WHERE device_id BETWEEN ? AND ? AND device_type = ? ::: [1, 10, 5]
  ```
  
  为了解决冗余的多次查找，可以使⽤complex的分片策略。
  
- complex的分片策略

  支持多个字段的分片策略。

  ```properties
  # 分库的分片策略
  spring.shardingsphere.sharding.default-database-strategy.complex.sharding-columns=device_id,device_type
  spring.shardingsphere.sharding.default-database-strategy.complex.algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.database.MyDatabaseComplexAlgorithm
  # 分表的分片策略
  spring.shardingsphere.sharding.tables.tb_device.table-strategy.complex.sharding-columns=device_id,device_type
  spring.shardingsphere.sharding.tables.tb_device.table-strategy.complex.algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.table.MyTableComplexAlgorithm
  ```

  配置分库的算法实现类

  ```java
  package com.qf.my.sharding.jdbc.demo.sharding.algorithm.database;
  import com.google.common.collect.Range;
  import org.apache.shardingsphere.api.sharding.complex.ComplexKeysShardingAlgorithm;
  import org.apache.shardingsphere.api.sharding.complex.ComplexKeysShardingValue;
  import java.util.ArrayList;
  import java.util.Collection;
  import java.util.Map;
  
  public class MyDatabaseComplexAlgorithm implements ComplexKeysShardingAlgorithm<Integer> {
  	@Override
  	public Collection<String> doSharding(Collection<String> collection,ComplexKeysShardingValue<Integer> complexKeysShardingValue) {
  		//获得这一次查询的device_type的所有值。
  		Collection<Integer> deviceTypes =complexKeysShardingValue.getColumnNameAndShardingValuesMap().get("devic
  e_type");
  		//存放指定的库
  		Collection<String> databases = new ArrayList<>();
  		for (Integer deviceType : deviceTypes) {
  			//根据deviceType的奇偶选择哪个数据库
  			String database = "ds" + (deviceType % 2);
  			databases.add(database);
  		}
  		return databases;
  	}
  }
  ```
  
  配置分表的算法实现类
  
  ```java
  package com.qf.my.sharding.jdbc.demo.sharding.algorithm.table;
  import org.apache.shardingsphere.api.sharding.complex.ComplexKeysShardingAlgorithm;
  import org.apache.shardingsphere.api.sharding.complex.ComplexKeysShardingValue ;
  import java.util.ArrayList;
  import java.util.Collection;
  
  public class MyTableComplexAlgorithm implements ComplexKeysShardingAlgorithm<Integer> {
  	@Override
  	public Collection<String> doSharding(Collection<String> collection,ComplexKeysShardingValue<Integer> complexKeysShardingValue) {
  		//获得这一次查询的device_type的所有值。
  		Collection<Integer> deviceTypes =complexKeysShardingValue.getColumnNameAndShardingValuesMap().get("devic
  e_type");
  		//存放指定的表
  		Collection<String> tables = new ArrayList<>();
  		for (Integer deviceType : deviceTypes) {
  			//根据deviceType的奇偶选择哪个数据库
  			String tableName =complexKeysShardingValue.getLogicTableName()+ "_" + (deviceType % 2);
  			tables.add(tableName);
  		}
  		return tables;
  	}
  }
  ```

#### 4) Hint强制路由策略

hint可以不根据sql语句特性，强制路由到某个库的某个表中。

```properties
#hint
spring.shardingsphere.sharding.tables.tb_device.table-strategy.hint.algorithm-class-name=com.qf.my.sharding.jdbc.demo.sharding.algorithm.table.MyTableHintAlgorithm
```

配置hint算法的实现类

```java
package com.qf.my.sharding.jdbc.demo.sharding.algorithm.table;
import org.apache.shardingsphere.api.sharding.hint.HintShardingAlgorithm;
import org.apache.shardingsphere.api.sharding.hint.HintShardingValue;
import java.util.Arrays;
import java.util.Collection;

public class MyTableHintAlgorithm implements HintShardingAlgorithm<long> {
    
 @Override
 public Collection<String> doSharding(Collection<String> collection,HintShardingValue<long> hintShardingValue) {
	//根据指定参数强制路由
	String tableName = hintShardingValue.getLogicTableName() + "_"+ hintShardingValue.getValues().toArray()[0];
	if (!collection.contains(tableName)) {
		throw new UnsupportedOperationException("表：" + tableName+ "，不存在");
	}
	return Arrays.asList(tableName);
}
}
```

编写单元测试。这次查询都会查询两个数据库的tb_device_0这张表。

```java
@Test
void queryByHint(){
	HintManager hintManager = HintManager.getInstance();
	//指定强制路由的表
	hintManager.addTableShardingValue("tb_device",0);
	List<TbDevice> deviceList = deviceMapper.selectList(null);
	System.out.println(deviceList);
	hintManager.close();
}
```

### 4.绑定表

先来模拟笛卡尔积的出现。

- 创建 `tb_device_info` 表：

  ```mysql
  CREATE TABLE `tb_device_info_0` (
   `id` bigint NOT NULL,
   `device_id` bigint DEFAULT NULL,
   `device_intro` varchar(255) COLLATE utf8mb4_general_ci DEFAULT NULL,
   PRIMARY KEY (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
  ```

- 配置 tb_device 和 tb_device_info 表的分片策略。

  ```properties
  # tb_device表的分片策略
  spring.shardingsphere.sharding.tables.tb_device.actual-data-nodes=ds$->{0..1}.tb_device_$->{0..1}
  spring.shardingsphere.sharding.tables.tb_device.table-strategy.inline.sharding-column=device_id
  spring.shardingsphere.sharding.tables.tb_device.table-strategy.inline.algorithm-expression=tb_device_$->{device_id%2}
  # # tb_device_info表的分片策略
  spring.shardingsphere.sharding.tables.tb_device_info.actual-data-nodes=ds$->{0..1}.tb_device_info_$->{0..1}
  spring.shardingsphere.sharding.tables.tb_device_info.table-strategy.inline.sharding-column=device_id
  spring.shardingsphere.sharding.tables.tb_device_info.table-strategy.inline.algorithm-expression=tb_device_info_$->{device_id%2}
  ```

  两张表的分片键都是device_id。

- 编写测试用例，插入数据

  ```java
  @Test
   void testInsertType(){
  	for (int i = 0; i < 10; i++) {
  		TbDevice device = new TbDevice();
  		device.setDeviceId((long) i);
  		device.setDeviceType(i);
  		deviceMapper.insert(device);
  		TbDeviceInfo deviceInfo = new TbDeviceInfo();
  		deviceInfo.setDeviceId((long) i);
  		deviceInfo.setDeviceIntro(""+i);
  		deviceInfoMapper.insert(deviceInfo);
  	}
  }
  ```

- join查询时出现笛卡尔积

  ```java
  package com.qf.my.sharding.jdbc.demo.mapper;
  import com.baomidou.mybatisplus.core.mapper.BaseMapper;
  import com.qf.my.sharding.jdbc.demo.entity.TbDeviceInfo;
  import org.apache.ibatis.annotations.Select;
  import java.util.List;
  
  public interface DeviceInfoMapper extends BaseMapper<TbDeviceInfo> {
   @Select("select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info a left join tb_device b on a.device_id = b.device_id ")
   public List<TbDeviceInfo> queryDeviceInfo();
  }
  ```
  
  出现笛卡尔积的查询
  
  ```java
  @Test
   void testQueryDeviceInfo(){
  	List<TbDeviceInfo> deviceInfos =deviceInfoMapper.queryDeviceInfo();
  	deviceInfos.forEach( deviceInfo ->System.out.println(deviceInfo));
  }
  ```
  
  结果：
  
  ```
  2022-03-28 21:09:17.413 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds0 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_1 a left join tb_device_0 b on a.device_id = b.device_id
  2022-03-28 21:09:17.413 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds0 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_1 a left join tb_device_1 b on a.device_id = b.device_id
  2022-03-28 21:09:17.414 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds0 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_0 a left join tb_device_0 b on a.device_id = b.device_id
  2022-03-28 21:09:17.414 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds0 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_0 a left join tb_device_1 b on a.device_id = b.device_id
  2022-03-28 21:09:17.414 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds1 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_1 a left join tb_device_0 b on a.device_id = b.device_id
  2022-03-28 21:09:17.414 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds1 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_1 a left join tb_device_1 b on a.device_id = b.device_id
  2022-03-28 21:09:17.414 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds1 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_0 a left join tb_device_0 b on a.device_id = b.device_id
  2022-03-28 21:09:17.414 INFO 14006 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds1 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_0 a left join tb_device_1 b on a.device_id = b.device_id
  TbDeviceInfo(id=1508423776753684482, deviceId=0, deviceIntro=0)
  TbDeviceInfo(id=1508423776938233858, deviceId=2, deviceIntro=2)
  TbDeviceInfo(id=1508423777097617410, deviceId=4, deviceIntro=4)
  TbDeviceInfo(id=1508423777210863618, deviceId=6, deviceIntro=6)
  TbDeviceInfo(id=1508423777328304130, deviceId=8, deviceIntro=8)
  TbDeviceInfo(id=1508423776753684482, deviceId=0, deviceIntro=0)
  TbDeviceInfo(id=1508423776938233858, deviceId=2, deviceIntro=2)
  TbDeviceInfo(id=1508423777097617410, deviceId=4, deviceIntro=4)
  TbDeviceInfo(id=1508423777210863618, deviceId=6, deviceIntro=6)
  TbDeviceInfo(id=1508423777328304130, deviceId=8, deviceIntro=8)
  TbDeviceInfo(id=1508423776858542081, deviceId=1, deviceIntro=1)
  TbDeviceInfo(id=1508423777030508546, deviceId=3, deviceIntro=3)
  TbDeviceInfo(id=1508423777152143362, deviceId=5, deviceIntro=5)
  TbDeviceInfo(id=1508423777273778177, deviceId=7, deviceIntro=7)
  TbDeviceInfo(id=1508423777378635778, deviceId=9, deviceIntro=9)
  TbDeviceInfo(id=1508423776858542081, deviceId=1, deviceIntro=1)
  TbDeviceInfo(id=1508423777030508546, deviceId=3, deviceIntro=3)
  TbDeviceInfo(id=1508423777152143362, deviceId=5, deviceIntro=5)
  TbDeviceInfo(id=1508423777273778177, deviceId=7, deviceIntro=7)
  TbDeviceInfo(id=1508423777378635778, deviceId=9, deviceIntro=9)
  ```
  
- 配置绑定表

  配置绑定表：

  ```properties
  spring.shardingsphere.sharding.binding-tables[0]=tb_device,tb_device_info
  ```

  再次查询，不再出现笛卡尔积：

  ```
  2022-03-28 21:10:48.549 INFO 14661 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds0 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_0 a left join tb_device_0 b on a.device_id = b.device_id
  2022-03-28 21:10:48.550 INFO 14661 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds0 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_1 a left join tb_device_1 b on a.device_id = b.device_id
  2022-03-28 21:10:48.550 INFO 14661 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds1 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_0 a left join tb_device_0 b on a.device_id = b.device_id
  2022-03-28 21:10:48.550 INFO 14661 --- [ main]
  ShardingSphere-SQL : Actual SQL: ds1 ::: select a.id,a.device_id,a.device_intro,b.device_type from tb_device_info_1 a left join tb_device_1 b on a.device_id = b.device_id
  TbDeviceInfo(id=1508423776753684482, deviceId=0, deviceIntro=0)
  TbDeviceInfo(id=1508423776938233858, deviceId=2, deviceIntro=2)
  TbDeviceInfo(id=1508423777097617410, deviceId=4, deviceIntro=4)
  TbDeviceInfo(id=1508423777210863618, deviceId=6, deviceIntro=6)
  TbDeviceInfo(id=1508423777328304130, deviceId=8, deviceIntro=8)
  TbDeviceInfo(id=1508423776858542081, deviceId=1, deviceIntro=1)
  TbDeviceInfo(id=1508423777030508546, deviceId=3, deviceIntro=3)
  TbDeviceInfo(id=1508423777152143362, deviceId=5, deviceIntro=5)
  TbDeviceInfo(id=1508423777273778177, deviceId=7, deviceIntro=7)
  TbDeviceInfo(id=1508423777378635778, deviceId=9, deviceIntro=9)
  ```

### 5.广播表

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518004837728.png)

现在有这么一个场景，device_type列对应的tb_device_type表中的数据，不应该被分表，两个库中都应该有全量的该表的数据。

- 在两个数据库中创建 tb_device_type 表

  ```mysql
  CREATE TABLE `tb_device_type` (
   `type_id` int NOT NULL AUTO_INCREMENT,
   `type_name` varchar(255) COLLATE utf8mb4_general_ci DEFAULT NULL,
   PRIMARY KEY (`type_id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
  ```

- 配置⼴播表

  ```properties
  #⼴播表配置
  spring.shardingsphere.sharding.broadcast-tables=tb_device_type
  spring.shardingsphere.sharding.tables.t_dict.key-generator.column=type_id
  spring.shardingsphere.sharding.tables.t_dict.key-generator.type=SNOWFLAKE
  ```

- 编写测试用例

  ```java
  @Test
   void testAddDeviceType(){
  	TbDeviceType deviceType1 = new TbDeviceType();
  	deviceType1.setTypeId(1);
  	deviceType1.setTypeName("人脸考勤");
  	deviceTypeMapper.insert(deviceType1);
  	TbDeviceType deviceType2 = new TbDeviceType();
  	deviceType2.setTypeId(2);
  	deviceType2.setTypeName("人脸通道");
  	deviceTypeMapper.insert(deviceType2);
  }
  ```

## 五、实现读写分离

### 1.搭建主从同步数据库

- 主从同步原理

Master将数据写入到binlog⽇志中。Slave读取主节点的Binlog数据到本地的relaylog⽇志⽂件中。此时，Slave持续不断的与Master同步，且数据存在于relaylog中，而并非落在数据库。于是Slave开启一条线程，专门讲relaylog中的数据写入到数据库中。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518005118415.png)

- 准备Master主库

  ```yml
  version: '3.1'
  services:
    mysql:
      restart: always
      image: mysql:5.7.25
      container_name: mysql
      ports:
        - 3306:3306
      environment:
        TZ: Asia/Shanghai
        MYSQL_ROOT_PASSWORD: 123456
      command:
        --character-set-server=utf8mb4
        --collation-server=utf8mb4_general_ci
        --explicit_defaults_for_timestamp=true
        --lower_case_table_names=1
        --max_allowed_packet=128M
        --server-id=47
        --log_bin=master-bin
        --log_bin-index=master-bin.index
        --skip-name-resolve
        --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
      volumes:
        - mysql-data:/var/lib/mysql
        
  volumes:
    mysql-data:
  ```

  注意其中的配置：

  ```
  服务id：server-id=47
  开启binlog：log_bin=master-bin
  binlog索引：log_bin-index=master-bin.index
  ```

  通过 `show master status` 命令查看并记录⽂件名和偏移量。

  ![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518005437414.png)

- 准备Slave从库：

  ```yml
  version: '3.1'
  services:
    mysql:
      restart: always
      image: mysql:5.7.25
      container_name: mysql
      ports:
        - 3306:3306
      environment:
        TZ: Asia/Shanghai
        MYSQL_ROOT_PASSWORD: 123456
      command:
        --character-set-server=utf8mb4
        --collation-server=utf8mb4_general_ci
        --explicit_defaults_for_timestamp=true
        --lower_case_table_names=1
        --max_allowed_packet=128M
        --server-id=48
        --relay-log-index=slave-relay-bin.index
        --relay-log=slave-relay-bin
        --log-bin=mysql-bin
        --log-slave-updates=1
        --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
      volumes:
        - mysql-data:/var/lib/mysql
        
  volumes:
    mysql-data:
  ```

  注意其中的关键配置：

  ```
  服务id：server-id=48
  开启中继日志：relay-log-index=slave-relay-bin.index
  开启中继日志：relay-log=slave-relay-bin
  ```

  启动从库后进入到从库，并依次执行如下命令：

  ```mysql
  #登录从服务
  mysql -u root -p;
  #设置同步主节点：
  CHANGE MASTER TO
  MASTER_HOST='172.16.253.31',
  MASTER_PORT=3306,
  MASTER_USER='root',
  MASTER_PASSWORD='123456',
  MASTER_LOG_FILE='master-bin.000003',
  MASTER_LOG_POS=154;
  #开启slave
  start slave;
  ```

  至此，主从同步集群搭建完成。

  在主库中创建 db_device 数据库，并在库中创建表：

  ```mysql
  CREATE TABLE `tb_user` (
   `id` bigint(20) NOT NULL AUTO_INCREMENT,
   `name` varchar(255) DEFAULT NULL,
   PRIMARY KEY (`id`)
  ) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4;
  ```

### 2.使⽤sharding-jdbc实现读写分离

- 编写配置⽂件

  ```properties
  # 配置真实数据源
  spring.shardingsphere.datasource.names=m0,s0
  
  # 配置主数据源
  spring.shardingsphere.datasource.m0.type=com.alibaba.druid.pool.DruidDataSource
  spring.shardingsphere.datasource.m0.driver-class-name=com.mysql.cj.jdbc.Driver
  spring.shardingsphere.datasource.m0.url=jdbc:mysql://172.16.253.73:3306/db_device?serverTimezone=Asia/Shanghai
  spring.shardingsphere.datasource.m0.username=root
  spring.shardingsphere.datasource.m0.password=123456
  
  # 配置从数据源
  spring.shardingsphere.datasource.s0.type=com.alibaba.druid.pool.DruidDataSource
  spring.shardingsphere.datasource.s0.driver-classname=com.mysql.cj.jdbc.Driver
  spring.shardingsphere.datasource.s0.url=jdbc:mysql://172.16.253.74:3306/db_device?serverTimezone=Asia/Shanghai
  spring.shardingsphere.datasource.s0.username=root
  spring.shardingsphere.datasource.s0.password=123456
  
  # 分配读写规则
  spring.shardingsphere.sharding.master-slave-rules.ds0.master-data-source-name=m0
  spring.shardingsphere.sharding.master-slave-rules.ds0.slave-data-source-names[0]=s0
  
  # 确定实际表
  spring.shardingsphere.sharding.tables.tb_user.actual-data-nodes=ds0.tb_user
  
  # 确定主键生成策略
  spring.shardingsphere.sharding.tables.t_dict.key-generator.column=id
  spring.shardingsphere.sharding.tables.t_dict.key-generator.type=SNOWFLAKE
  
  # 开启显示sql语句
  spring.shardingsphere.props.sql.show = true
  ```

- 测试写数据

  ```java
  @Test
  void testInsertUser(){
  	for (int i = 0; i < 10; i++) {
           TbUser user = new TbUser();
           user.setName(""+i);
           userMapper.insert(user);
   	}
  }
  ```

  此时，所有的数据只会往主库中写，然后再同步到从库。

- 测试读数据

  ```java
  @Test
  void testQueryUser(){
  	List<TbUser> tbUsers = userMapper.selectList(null);
  	tbUsers.forEach( tbUser -> System.out.println(tbUser));
  }
  ```

  此时，所有的数据都读自于从库。

## 六、实现原理-连接模式

ShardingSphere 采用一套自动化的执行引擎，负责将路由和改写完成之后的真实 SQL 安全且高效发送到底层数据源执行。 它不是简单地将 SQL 通过 JDBC 直接发送至数据源执行；也并非直接将执行请求放入线程池去并发执行。它更关注平衡数据源连接创建以及内存占用所产生的消耗，以及最大限度地合理利用并发等问题。 执行引擎的目标是自动化的平衡资源控制与执行效率。

### 1.连接模式

从资源控制的⻆度看，业务方访问数据库的连接数量应当有所限制。它能够有效地防⽌某一业务操作过多的占用资源，从而将数据库连接的资源耗尽，以致于影响其他业务的正常访问。 特别是在一个数据库实例中存在较多分表的情况下，一条不包含分片键的逻辑 SQL 将产生落在同库不同表的大量真实 SQL ，如果每条真实SQL都占用一个独立的连接，那么一次查询无疑将会占用过多的资源。

从执行效率的⻆度看，为每个分片查询维持一个独立的数据库连接，可以更加有效的利用多线程来提升执行效率。 为每个数据库连接开启独立的线程，可以将 I/O 所产生的消耗并行处理。为每个分片维持一个独立的数据库连接，还能够避免过早的将查询结果数据加载至内存。 独立的数据库连接，能够持有查询结果集游标位置的引用，在需要获取相应数据时移动游标即可。

以结果集游标下移进行结果归并的方式，称之为流式归并，它无需将结果数据全数加载至内存，可以有效的节省内存资源，进而减少垃圾回收的频次。 当无法保证每个分片查询持有一个独立数据库连接时，则需要在复用该数据库连接获取下一张分表的查询结果集之前，将当前的查询结果集全数加载至内存。 因此，即使可以采用流式归并，在此场景下也将退化为内存归并。

一方⾯是对数据库连接资源的控制保护，一方⾯是采用更优的归并模式达到对中间件内存资源的节省，如何处理好两者之间的关系，是 ShardingSphere 执行引擎需要解决的问题。 具体来说，如果一条 SQL 在经过 ShardingSphere 的分片后，需要操作某数据库实例下的 200张表。 那么，是选择创建 200 个连接并行执行，还是选择创建一个连接串行执行呢？效率与资源控制又应该如何抉择呢？

针对上述场景，ShardingSphere 提供了一种解决思路。 它提出了连接模式（ConnectionMode）的概念，将其划分为内存限制模式（MEMORY_STRICTLY）和连接限制模式（CONNECTION_STRICTLY）这两种类型。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518005933107.png)

#### 1）内存限制模式

使用此模式的前提是，ShardingSphere 对一次操作所耗费的数据库连接数量不做限制。 如果实际执行的 SQL 需要对某数据库实例中的 200 张表做操作，则对每张表创建一个新的数据库连接，并通过多线程的方式并发处理，以达成执行效率最大化。 并且在 SQL 满⾜条件情况下，优先选择流式归并，以防⽌出现内存溢出或避免频繁垃圾回收情况。

#### 2）连接限制模式

使用此模式的前提是，ShardingSphere 严格控制对一次操作所耗费的数据库连接数量。 如果实际执行的 SQL 需要对某数据库实例中的 200 张表做操作，那么只会创建唯一的数据库连接，并对其 200 张表串行处理。 如果一次操作中的分片散落在不同的数据库，仍然采用多线程处理对不同库的操作，但每个库的每次操作仍然只创建一个唯一的数据库连接。 这样即可以防⽌对一次请求对数据库连接占用过多所带来的问题。该模式始终选择内存归并。

内存限制模式适用于 OLAP 操作，可以通过放宽对数据库连接的限制提升系统吞吐量； 连接限制模式适用于 OLTP 操作，OLTP 通常带有分片键，会路由到单一的分片，因此严格控制数据库连接，以保证在线系统数据库资源能够被更多的应用所使用，是明智的选择。

### 2.自动化执行引擎

ShardingSphere 最初将使用何种模式的决定权交由用户配置，让开发者依据自己业务的实际场景需求选择使用内存限制模式或连接限制模式。

这种解决方案将两难的选择的决定权交由用户，使得用户必须要了解这两种模式的利弊，并依据业务场景需求进行选择。 这无疑增加了用户对 ShardingSphere 的学习和使用的成本，并非最优方案。

这种一分为⼆的处理方案，将两种模式的切换交由静态的初始化配置，是缺乏灵活应对能力的。在实际的使用场景中，⾯对不同 SQL 以及占位符参数，每次的路由结果是不同的。 这就意味着某些操作可能需要使用内存归并，而某些操作则可能选择流式归并更优，具体采用哪种方式不应该由用户在 ShardingSphere 启动之前配置好，而是应该根据 SQL 和占位符参数的场景，来动态的决定连接模式。

为了降低用户的使用成本以及连接模式动态化这两个问题，ShardingSphere 提炼出自动化执行引擎的思路，在其内部消化了连接模式概念。 用户无需了解所谓的内存限制模式和连接限制模式是什么，而是交由执行引擎根据当前场景自动选择最优的执行方案。

自动化执行引擎将连接模式的选择粒度细化至每一次 SQL 的操作。 针对每次 SQL 请求，自动化执行引擎都将根据其路由结果，进行实时的演算和权衡，并自主地采用恰当的连接模式执行，以达到资源控制和效率的最优平衡。 针对自动化的执行引擎，用户只需配置maxConnectionSizePerQuery 即可，该参数表示一次查询时每个数据库所允许使用的最大连接数。

执行引擎分为准备和执行两个阶段。

### 3.准备阶段

顾名思义，此阶段用于准备执行的数据。它分为结果集分组和执行单元创建两个步骤。

结果集分组是实现内化连接模式概念的关键。执行引擎根据maxConnectionSizePerQuery配置项，结合当前路由结果，选择恰当的连接模式。 具体步骤如下：

- 将 SQL 的路由结果按照数据源的名称进行分组。

- 通过下图的公式，可以获得每个数据库实例在 `maxConnectionSizePerQuery` 的允许范围内，每个连接需要执行的 SQL 路由结果组，并计算出本次请求的最优连接模式。

  ![image-20230518010213578](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518010213578.png)

  在 maxConnectionSizePerQuery 允许的范围内，当一个连接需要执行的请求数量大于 1时，意味着当前的数据库连接无法持有相应的数据结果集，则必须采用内存归并； 反之，当一个连接需要执行的请求数量等于 1 时，意味着当前的数据库连接可以持有相应的数据结果集，则可以采用流式归并。

  每一次的连接模式的选择，是针对每一个物理数据库的。也就是说，在同一次查询中，如果路由至一个以上的数据库，每个数据库的连接模式不一定一样，它们可能是混合存在的形态。

## 七、实现原理-归并引擎

将从各个数据节点获取的多数据结果集，组合成为一个结果集并正确的返回至请求客户端，称为结果归并。

ShardingSphere 支持的结果归并从功能上分为遍历、排序、分组、分页和聚合 5 种类型，它们是组合而非互斥的关系。 从结构划分，可分为流式归并、内存归并和装饰者归并。流式归并和内存归并是互斥的，装饰者归并可以在流式归并和内存归并之上做进一步的处理。

由于从数据库中返回的结果集是逐条返回的，并不需要将所有的数据一次性加载至内存中，因此，在进行结果归并时，沿用数据库返回结果集的方式进行归并，能够极大减少内存的消耗，是归并方式的优先选择。

流式归并是指每一次从结果集中获取到的数据，都能够通过逐条获取的方式返回正确的单条数据，它与数据库原生的返回结果集的方式最为契合。遍历、排序以及流式分组都属于流式归并的一种。

内存归并则是需要将结果集的所有数据都遍历并存储在内存中，再通过统一的分组、排序以及聚合等计算之后，再将其封装成为逐条访问的数据结果集返回。

装饰者归并是对所有的结果集归并进行统一的功能增强，目前装饰者归并有分页归并和聚合归并这 2 种类型。

### 1.遍历归并

它是最为简单的归并方式。 只需将多个数据结果集合并为一个单向链表即可。在遍历完成链表中当前数据结果集之后，将链表元素后移一位，继续遍历下一个数据结果集即可。

### 2.排序归并

由于在 SQL 中存在 `ORDER BY` 语句，因此每个数据结果集自身是有序的，因此只需要将数据结果集当前游标指向的数据值进行排序即可。 这相当于对多个有序的数组进行排序，归并排序是最适合此场景的排序算法。

ShardingSphere 在对排序的查询进行归并时，将每个结果集的当前数据值进行比较（通过实现 Java 的 Comparable 接口完成），并将其放入优先级队列。 每次获取下一条数据时，只需将队列顶端结果集的游标下移，并根据新游标重新进入优先级排序队列找到自己的位置即可。

通过一个例子来说明 ShardingSphere 的排序归并，下图是一个通过分数进行排序的示例图。 图中展示了 3 张表返回的数据结果集，每个数据结果集已经根据分数排序完毕，但是 3个数据结果集之间是无序的。 将 3 个数据结果集的当前游标指向的数据值进行排序，并放入优先级队列，t_score_0 的第一个数据值最大，t_score_2 的第一个数据值次之，t_score_1的第一个数据值最小，因此优先级队列根据 t_score_0，t_score_2 和 t_score_1 的方式排序队列。



![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518010426647.png)

下图则展现了进行 next 调用的时候，排序归并是如何进行的。 通过图中我们可以看到，当进行第一次 next 调用时，排在队列⾸位的 t_score_0 将会被弹出队列，并且将当前游标指向的数据值（也就是 100）返回至查询客户端，并且将游标下移一位之后，重新放入优先级队列。 而优先级队列也会根据 t_score_0 的当前数据结果集指向游标的数据值（这⾥是 90）进行排序，根据当前数值，t_score_0 排列在队列的最后一位。 之前队列中排名第⼆的t_score_2 的数据结果集则自动排在了队列⾸位。

在进行第⼆次 next 时，只需要将目前排列在队列⾸位的 t_score_2 弹出队列，并且将其数据结果集游标指向的值返回至客户端，并下移游标，继续加入队列排队，以此类推。 当一个结果集中已经没有数据了，则无需再次加入队列。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518010459607.png)

可以看到，对于每个数据结果集中的数据有序，而多数据结果集整体无序的情况下，ShardingSphere 无需将所有的数据都加载至内存即可排序。 它使用的是流式归并的方式，每次 next 仅获取唯一正确的一条数据，极大的节省了内存的消耗。

从另一个⻆度来说，ShardingSphere 的排序归并，是在维护数据结果集的纵轴和横轴这两个维度的有序性。 纵轴是指每个数据结果集本身，它是天然有序的，它通过包含 ORDER BY 的SQL 所获取。 横轴是指每个数据结果集当前游标所指向的值，它需要通过优先级队列来维护其正确顺序。 每一次数据结果集当前游标的下移，都需要将该数据结果集重新放入优先级队列排序，而只有排列在队列⾸位的数据结果集才可能发生游标下移的操作。

### 3.分组归并

分组归并的情况最为复杂，它分为流式分组归并和内存分组归并。 流式分组归并要求 SQL 的排序项与分组项的字段以及排序类型（ASC 或 DESC）必须保持一致，否则只能通过内存归并才能保证其数据的正确性。

举例说明，假设根据科目分片，表结构中包含考生的姓名（为了简单起见，不考虑重名的情况）和分数。通过 SQL 获取每位考生的总分，可通过如下 SQL：

```mysql
SELECT name, SUM(score) FROM t_score GROUP BY name ORDER BY name;
```

在分组项与排序项完全一致的情况下，取得的数据是连续的，分组所需的数据全数存在于各个数据结果集的当前游标所指向的数据值，因此可以采用流式归并。如下图所示。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518010609103.png)

进行归并时，逻辑与排序归并类似。 下图展现了进行 next 调用的时候，流式分组归并是如何进行的。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518010634421.png)

通过图中我们可以看到，当进行第一次 next 调用时，排在队列⾸位的 t_score_java 将会被弹出队列，并且将分组值同为 “Jerry” 的其他结果集中的数据一同弹出队列。 在获取了所有的姓名为 “Jerry” 的同学的分数之后，进行累加操作，那么，在第一次 next 调用结束后，取出的结果集是 “Jerry” 的分数总和。 与此同时，所有的数据结果集中的游标都将下移至数据值“Jerry” 的下一个不同的数据值，并且根据数据结果集当前游标指向的值进行重排序。 因此，包含名字顺着第⼆位的 “John” 的相关数据结果集则排在的队列的前列。

流式分组归并与排序归并的区别仅仅在于两点：

- 它会一次性的将多个数据结果集中的分组项相同的数据全数取出。
- 它需要根据聚合函数的类型进行聚合计算。

对于分组项与排序项不一致的情况，由于需要获取分组的相关的数据值并非连续的，因此无法使用流式归并，需要将所有的结果集数据加载至内存中进行分组和聚合。 例如，若通过以下 SQL 获取每位考生的总分并按照分数从高至低排序：

```mysql
SELECT name, SUM(score) FROM t_score GROUP BY name ORDER BY score DESC;
```

那么各个数据结果集中取出的数据与排序归并那张图的上半部分的表结构的原始数据一致，是无法进行流式归并的。

当 SQL 中只包含分组语句时，根据不同数据库的实现，其排序的顺序不一定与分组顺序一致。 但由于排序语句的缺失，则表示此 SQL 并不在意排序顺序。 因此，ShardingSphere 通过 SQL 优化的改写，自动增加与分组项一致的排序项，使其能够从消耗内存的内存分组归并方式转化为流式分组归并方案。

### 4.聚合归并

无论是流式分组归并还是内存分组归并，对聚合函数的处理都是一致的。 除了分组的 SQL 之外，不进行分组的 SQL 也可以使用聚合函数。 因此，聚合归并是在之前介绍的归并类的之上追加的归并能力，即装饰者模式。聚合函数可以归类为比较、累加和求平均值这 3 种类型。

比较类型的聚合函数是指 `MAX` 和 `MIN` 。它们需要对每一个同组的结果集数据进行比较，并且直接返回其最大或最小值即可。

累加类型的聚合函数是指 `SUM` 和 `COUNT` 。它们需要将每一个同组的结果集数据进行累加。

求平均值的聚合函数只有 `AVG` 。它必须通过 SQL 改写的 `SUM` 和 `COUNT` 进行计算，相关内容已在 SQL 改写的内容中涵盖，不再赘述。

### 5.分页归并

上⽂所述的所有归并类型都可能进行分页。 分页也是追加在其他归并类型之上的装饰器，ShardingSphere 通过装饰者模式来增加对数据结果集进行分页的能力。 分页归并负责将无需获取的数据过滤掉。

ShardingSphere 的分页功能比较容易让使用者误解，用户通常认为分页归并会占用大量内存。 在分布式的场景中，将 `LIMIT 10000000, 10` 改写为 `LIMIT 0, 10000010` ，才能保证其数据的正确性。 用户非常容易产生 ShardingSphere 会将大量无意义的数据加载至内存中，造成内存溢出⻛险的错觉。 其实，通过流式归并的原理可知，会将数据全部加载到内存中的只有内存分组归并这一种情况。 而通常来说，进行 OLAP 的分组 SQL，不会产生大量的结果数据，它更多的用于大量的计算，以及少量结果产出的场景。 除了内存分组归并这种情况之外，其他情况都通过流式归并获取数据结果集，因此 ShardingSphere 会通过结果集的next 方法将无需取出的数据全部跳过，并不会将其存入内存。

但同时需要注意的是，由于排序的需要，大量的数据仍然需要传输到 ShardingSphere 的内存空间。 因此，采用 LIMIT 这种方式分页，并非最佳实践。 由于 LIMIT 并不能通过索引查询数据，因此如果可以保证 ID 的连续性，通过 ID 进行分页是比较好的解决方案，例如：

```mysql
SELECT * FROM t_order WHERE id > 100000 AND id <= 100010 ORDER BY id;
```

或通过记录上次查询结果的最后一条记录的 ID 进行下一页的查询，例如：

```mysql
SELECT * FROM t_order WHERE id > 10000000 LIMIT 10;
```

归并引擎的整体结构划分如下图。

![](https://gitee.com/haktiong/picture-warehouse/raw/master/images/shardingSphere-JDBC/image-20230518010917074.png)