---
layout: post
category: "database"
title:  "mysql表分区"
tags: [Database]
---



> 表分区优点

- 和单个磁盘或者文件系统相比，可以存储更多数据
- 优化查询，在Where子句中包含分区条件时，可以只扫描必要的一个或者多个分区来提高查询效率；
- 对于已经过期的数据，可以通过删除与这些数据有关的分区来快速删除数据
- 跨多个磁盘来分散数据查询，以获得更大的查询吞吐


> 表分区限制  

- 最大分区数目不能超过1024  
- 如果含有唯一索引或者主键，则分区列必须包含在所有的唯一索引或者主键在内  
- 不支持外键  
- 不支持全文索引（fulltext）

<!-- more -->

> MySQL中分区有如下几种类型  


- Range分区：指定区间的范围，把数据分配到不同的分区
- List分区：类似于Range分区，但是List是基于枚举出的值列表分区
- Hash分区：基于给定的分区个数，把数据分配到不同的分区
- Key分区：类似于Hash分区


> 按创建时间创建range分区

	CREATE TABLE `partition_test` (
	`id` VARCHAR (30) NOT NULL,
	`name` VARCHAR (255) DEFAULT NULL,
	`create_time` datetime NOT NULL,
	PRIMARY KEY (`id`, `create_time`)
	) ENGINE = INNODB DEFAULT CHARSET = utf8 
	PARTITION BY RANGE (TO_DAYS(create_time))(
		PARTITION p20190101
		VALUES
			LESS THAN (TO_DAYS('2019-01-01')) ENGINE = INNODB,
			PARTITION p20200101
		VALUES
			LESS THAN (TO_DAYS('2020-01-01')) ENGINE = INNODB,
			PARTITION p20210101
		VALUES
			LESS THAN (TO_DAYS('2021-01-01')) ENGINE = INNODB
	);
	
	1、create_time必须是主键
	2、如对p20210101赋值less than maxvalue。有MAXVALUE值后，后续加分区是不可行的
	3、分区只能在最后新增，即必须大于p20210101中的2021-01-01，不能从中间新增



> 查询表的分区情况  

	select
	partition_name part,
	partition_expression expr,
	partition_description descr,
	table_rows
	from information_schema.partitions where
	table_schema = schema()
	and table_name='partition_test';



> 新增表分区  

	ALTER TABLE partition_test add PARTITION (
	PARTITION p4
	VALUES
		LESS THAN (TO_DAYS('2021-02-01'))
	);



> 删除分区、同时删除分区对应数据

	ALTER TABLE partition_test DROP PARTITION p0



> 对已有数据的表进行分区

	1、先创建一个新的表test1、并对改表进行分区
	2、拷贝数据：insert into test1 select * from test
	3、删除原表test：drop table test
	4、重命名：rename test1 test