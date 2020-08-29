---
layout: post
category: "database"
title:  "mysql表分区"
tags: [Database]
---



> 表分区优点

- 和单个磁盘或者文件系统相比，可以存储更多数据
- 优化查询，在Where子句中包含分区条件时（不包含分区键，将扫描所有分区），可以只扫描必要的一个或者多个分区来提高查询效率；
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

	1.分区键必须包含在主键中
	2.如有唯一索引，分区键必须包含在唯一索引中

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
	4、重命名：rename table test1 to test


> 存储过程新增分区  

	DELIMITER ||
	-- 删除存储过程
	drop procedure if exists auto_set_partitions ||
	-- 注意：使用该存储过程必须保证相应数据库表中至少有一个手动分区
	-- 创建存储过程[通过数据库名和对应表名]-建多少个分区，分区时间间隔为多少
	-- databasename：创建分区的数据库
	-- tablename：创建分区的表的名称
	-- partition_number：一次创建多少个分区
	-- partitiontype：分区类型[0按天分区，1按月分区，2按年分区]
	-- gaps：分区间隔，如果分区类型为0则表示每个分区的间隔为 gaps天；
	--       如果分区类型为1则表示每个分区的间隔为 gaps月
	--            如果分区类型为2则表示每个分区的间隔为 gaps年
	create procedure auto_set_partitions (in databasename varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,in tablename varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci, in partition_number int, in partitiontype int, in gaps int)
	L_END:
	begin     
	    declare max_partition_description varchar(255) default '';
	    declare p_name varchar(255) default 0;       
	    declare p_description varchar(255) default 0;   
	    declare isexist_partition varchar(255) default 0; 
	 declare i int default 1;
	  
	 -- 查看对应数据库对应表是否已经有手动分区[自动分区前提是必须有手动分区]
	    select partition_name into isexist_partition from information_schema.partitions where table_schema = databasename  and table_name = tablename limit 1;
	    -- 如果不存在则打印错误并退出存储过程
	    if isexist_partition <=> "" then
	       select "partition table not is exist" as "ERROR";
	       leave L_END;
	    end if;
	 
	    -- 获取最大[降序获取]的分区描述[值]
	    select partition_description into max_partition_description  from information_schema.partitions where table_schema = databasename  and table_name = tablename order by partition_description desc limit 1;
	   
	    -- 如果最大分区没有,说明没有手动分区,则无法创建自动分区
	    if max_partition_description <=> "" then
	       select "partition table is error" as "ERROR";
	       leave L_END;
	    end if;
	 
	    -- 替换前后的单引号[''两个引号表示一个单引号的转义]
	    -- set max_partition_description = REPLACE(max_partition_description, '''', '');
	     -- 或使用如下语句
	     set max_partition_description = REPLACE(max_partition_description-1, '\'', '');
	 
	   -- 自动创建number个分区
	    while (i <= partition_number) do
	                 if (partitiontype = 0) then
	                     -- 每个分区按天递增,递增gaps天
	                     set p_description = DATE_ADD(FROM_DAYS(max_partition_description), interval i*gaps day); 
	                 elseif (partitiontype = 1) then
	                     -- 每个分区按月递增,递增gaps月
	                     set p_description = DATE_ADD(FROM_DAYS(max_partition_description), interval i*gaps month); 
	                 else 
	                     -- 每个分区按年递增,递增gaps年
	                     set p_description = DATE_ADD(FROM_DAYS(max_partition_description), interval i*gaps year);
	                 end if;
	                 -- 删除空格
	                 set p_name = REPLACE(p_description, ' ', '');
	                 -- 例如10.20的记录实际是less than 10.21
	                 set p_description = DATE_ADD(p_description, interval 1 day); 
	                 -- 如果有横杆替换为空
	          set p_name = REPLACE(p_name, '-', '');
	                 -- 删除时间冒号
	                 set p_name = REPLACE(p_name, ':', '');
	                 -- alter table tablename add partition ( partition pname values less than ('2017-02-20 10:05:56') );
	          set @sql=CONCAT('ALTER TABLE ', tablename ,' ADD PARTITION ( PARTITION p', p_name ,' VALUES LESS THAN (TO_DAYS(\'', p_description ,'\')))');
	                 -- set @sql=CONCAT('ALTER TABLE ', tablename ,' ADD PARTITION ( PARTITION p', p_name ,' VALUES LESS THAN (TO_DAYS(\'', p_description ,'\')))');
	                 -- 打印sql变量
	          -- select @sql;
	                 -- 准备sql语句
	          PREPARE stmt from @sql;
	                 -- 执行sql语句
	          EXECUTE stmt;
	                 -- 释放资源
	          DEALLOCATE PREPARE stmt;
	                 -- 递增变量
	          set i = (i + 1) ;
	 
	    end while;          
	end ||
	-- 恢复语句中断符
	DELIMITER ;

加入定时任务  

	#每月新增一个分区、按月新增、每次新增一个月

	DELIMITER ||
	drop event if exists auto_set_partitions  ||
	create event auto_set_partitions 
	on schedule every 1 MONTH
	starts '2018-10-30 00:00:00'
	do
	BEGIN
	    call auto_set_partitions('qif', 'qif_test', 1, 1, 1);
	END ||
	DELIMITER ;