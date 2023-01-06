
# SQL

## 客户端执行sql
```powershell
sqlcmd.exe -S 172.16.30.233 -U sa -P P@ssword123 -d dbName -i script.sql
  & "C:\Program Files\Microsoft SQL Server\110\Tools\Binn\SQLCMD.EXE" -S 172.16.30.233\uv_data_dev -U dev -P liancheng -d uv_case_storage -i "C:\Users\lihe\Documents\script.sql"
```


## lock
```sql
select 
request_session_id spid,                             -- （死锁进程id）
OBJECT_NAME(resource_associated_entity_id) tableName -- （死锁进程名称）
from sys.dm_tran_locks 
where resource_type='OBJECT';

-- kill pid

```

## sqlserver
### 行列倒置

```sql
create table tb_RowConvertToColumn
(
   username nvarchar(100) null,
   course nvarchar(100) null,
   score numeric(10,2) null
)
insert into tb_RowConvertToColumn(username,course,score) values('张三','语文',82)
insert into tb_RowConvertToColumn(username,course,score) values('张三','数学',85)
insert into tb_RowConvertToColumn(username,course,score) values('张三','外语',90)
insert into tb_RowConvertToColumn(username,course,score) values('李四','语文',86)
insert into tb_RowConvertToColumn(username,course,score) values('李四','数学',82)
insert into tb_RowConvertToColumn(username,course,score) values('李四','外语',92)
insert into tb_RowConvertToColumn(username,course,score) values('王五','语文',82)
insert into tb_RowConvertToColumn(username,course,score) values('王五','数学',94)
insert into tb_RowConvertToColumn(username,course,score) values('王五','外语',82)
```

1. 静态行列倒置
```sql
select username 姓名,
       MAX(case course when '语文' then score else 0 end) 语文,
       MAX(case course when '数学' then score else 0 end) 数学,
       MAX(case course when '外语' then score else 0 end) 外语
from tb_RowConvertToColumn
group by username
order by username
```
2. 静态行列倒置(高级语法,2005以上)
```sql
select username 姓名,语文,数学,外语 from tb_RowConvertToColumn pivot(max(score) for course in(语文,数学,外语)) a
select * from tb_RowConvertToColumn pivot(max(score)for course in (语文,数学,外语)) a
```
3. 动态行列倒置
```sql
declare @sql nvarchar(2000)
select distinct course into #tb_group from tb_RowConvertToColumn order by course desc--表头及排序
select @sql=ISNULL(@sql+',','')+'MAX(case course when '''+course+''' then score else 0 end) ['+course+']'
 from #tb_group
set @sql='select username 姓名,'+@sql
        +' from tb_RowConvertToColumn a'
        +' group by username'
exec(@sql)
drop table #tb_group

declare @sql nvarchar(2000)
select @sql=ISNULL(@sql+',','')+course
from tb_RowConvertToColumn
group by course
set @sql='select * from tb_RowConvertToColumn pivot (max(score) for course in ('+@sql+')) a'
exec(@sql)
```

### 列转行
```sql
create table tb_ColumnConvertToRow
(
   [姓名] nvarchar(100) null,
   [语文] nvarchar(100) null,
   [数学] nvarchar(100) null,
   [外语] nvarchar(100) null
)

insert into tb_ColumnConvertToRow(姓名,语文,数学,外语) values('李四',82,92,86)
insert into tb_ColumnConvertToRow(姓名,语文,数学,外语) values('王五',94,82,82)
insert into tb_ColumnConvertToRow(姓名,语文,数学,外语) values('张三',85,90,82)
```
1. 静态倒置
```sql
--1.静态sql列转行,当列头较少时使用
select * from 
(
   select 姓名,课程='语文',分数=语文 from tb_ColumnConvertToRow
   union all
   select 姓名,课程='数学',分数=数学 from tb_ColumnConvertToRow
   union all 
   select 姓名,课程='外语',分数=外语 from tb_ColumnConvertToRow
) a
```
2. 高级语法倒置
```sql
select 姓名,课程,分数 from tb_ColumnConvertToRow unpivot(分数 for 课程 in (语文,数学,外语)) a
```

修改
```sql
EXEC sp_rename '表名.[原列名]', '新列名', 'column';
```


- 组成
	- 逻辑结构
		- Server
			- 连接器
			- 解析器
			- 分析器
			- 执行器
			- 查询缓存
		- Engines(存储引擎)
			- InnoDB数据文件
				- .frm文件：主要存放与表相关的数据信息,主要包括表结构的定义信息
				- .ibd：使用独享表空间存储表数据和索引信息，一张表对应一个ibd文件。
				- ibdata文件：使用共享表空间存储表**数据和索引**信息，所有表共同使用一个或者多个ibdata文
				  件。
			- MyIsam数据文件
				- .frm文件：主要存放与表相关的数据信息,主要包括表结构的定义信息
				- .myd文件：主要用来存储表数据信息。
				- .myi文件：主要用来存储表**数据**文件中任何索引的数据树。
	- 物理结构
		- MySQL是通过文件系统对数据和索引进行存储的。
		- MySQL从物理结构上可以分为日志文件和数据索引文件。
		- MySQL在Linux中的数据索引文件和日志文件都在/var/lib/mysql目录下。
		- 日志文件采用顺序IO方式存储、数据文件采用随机IO方式存储。
			- 类型
				- 错误日志(error log)
				- 二进制日期(bin log)
				- 慢日志(slow query log)
				- 重做日志(redo log)
				- 回滚日志(rollback log)
				- 中继日期(relay log)
- 概念
	- 1.DDL（Data Definition Language）：数据定义语言，用来定义数据库对象：库、表、列等；
		- 表结构
			- desc tableName
			- auto_increment
				- delete重置
				- truncate不重置
			- ```sql
			  SELECT auto_increment 
			  FROM information_schema.tables 
			  where table_schema='db_honest_platform' and table_name='agent';
			  
			  select last_insert_id();
			  ```
	- 2.DML（Data Manipulation Language）：数据操作语言，用来定义数据库记录（数据）；
	- 3.DQL（Data Query Language）：数据查询语言，用来查询记录（数据）；
		- 分页
		  collapsed:: true
			- 物理分页(limit)
			- 逻辑分页
		- 关系
		  collapsed:: true
			- CROSS JOIN （交叉连接）
			- INNER JOIN （内连接或等值连接）。
			- OUTER JOIN （外连接）
				- LEFT JOIN 或者 LEFT OUTER JOIN
	- 4.DCL（Data Control Language）：数据控制语言，用来定义访问权限和安全级别。
- 索引
	- ```sql
	  SHOW INDEX FROM table_name \G
	  ```
	- 索引是在存储引擎中实现的，也就是说不同的存储引擎，会使用不同的索引
	  MyISAM和InnoDB存储引擎：只支持B+ TREE索引， 也就是说默认使用BTREE，不能够更换
	  MEMORY/HEAP存储引擎：支持HASH和BTREE索引
- 执行次序
	- from/join
	- where
	- group by
	- having
	- select
	- distinct
	- order by
	- limit/offset
- ## 实现row_number()
- ```sql
  select x.*, (@r := @r + 1) as rank
  from (
      select e.id, e.plat_number, e.created_at
      from `database`.apply_license e
      where channel =3 and applyType=1 and created_at >='2022-01-01'
      order by e.id desc
      limit 100) x, (select @r := 0) r;
  ```
- select z.id, z.channel, z.created_at, z.rank
  from (
           select x.*, @rownum := @rownum + 1, if(@part = x.channel, @r := @r + 1, @r := 1) as rank, @part:=x.channel
           from (
               select e.id, e.plat_number, e.created_at, e.channel
               from `database`.apply_license e
               where channel in (1, 2, 3) and applyType=1 and created_at >='2022-01-01'
               order by e.id, e.channel desc) x, (select @rownum := 0, @part := null, @r := 0) rt) z
- ## 分阶段统计
- ```sql
  select elt(interval(d.yb,0, 100, 500, 1000), 'less100', '100to500', '500to1000', 'more1000') as yb_level, count(d.id) as cnt
  from k1 d
  group by elt(interval(d.yb, 0, 100, 500, 1000), 'less100', '100to500', '500to1000', 'more1000K');
  
  -- 等同于
  
  SELECT
  
  (
  
  CASE
  
  WHEN age >= 1
  
  AND age <= 20 THEN
  
  "1-20"
  
  WHEN age > 20
  
  AND age <= 30 THEN
  
  "21-30"
  
  WHEN age > 30
  
  AND age <= 40 THEN
  
  "31~40"
  
  ELSE
  
  "40+"
  
  END
  
  ) AS age_area,
  
  count(name) AS num
  
  FROM
  
  a
  
  WHERE
  
  sex = 1
  
  GROUP BY
  
  (
  
  CASE
  
  WHEN age >= 1
  
  AND age <= 20 THEN
  
  "1-20"
  
  WHEN age > 20
  
  AND age <= 30 THEN
  
  "21-30"
  
  WHEN age > 30
  
  AND age <= 40 THEN
  
  "31~40"
  
  ELSE
  
  "40+"
  
  END
  
  );
  
  
  ```
- 累计求和
	- ```sql
	  -- mssql
	  select t.ITEM_BUSINESSNUMBER,
	         t.YYYYMM,
	         sum(t.应收评估费)                                                               as traffics,
	         SUM(sum(t.当月到账金额)) OVER (PARTITION BY ITEM_BUSINESSNUMBER ORDER BY YYYYMM) as totals
	  from (SELECT b.item_businessNumber,
	               sum(ITEM_VALFEE)  as                    '应收评估费',
	               sum(item_thisfee) AS                    '当月到账金额',
	               CONVERT(nvarchar(7), item_thisdate, 23) YYYYMM
	        FROM tlk_skgl a
	                 join tlk_skgl_detail b on a.id = b.parent
	        group by CONVERT(nvarchar(7), item_thisdate, 23), b.item_businessNumber) t
	  group by ITEM_BUSINESSNUMBER, YYYYMM;
	  ```


- # SqlServer
- ## 索引
	- 主键
		- collapsed:: true
		  ```sql
		  -- 创建主键索引
		  ALTER TABLE table_name ADD CONSTRAINT pk_table_name PRIMARY KEY (column);
		  go
		  
		  -- 撤销主键索引
		  ALTER TABLE table_name DROP CONSTRAINT pk_table_name;
		  go
		  
		  -- 创建唯一索引
		  create unique index idx_guid_二手房成交数据08_new on [uv_case_storage].[dbo].[二手房成交数据08_new](guid);
		  -- 创建普通索引
		  create index ix_com_Employee_IDCreditCardID on Employee(ID,Credit_Card_ID);
		  
		  ```
	- 死锁
		- ```sql
		  -- 查询死锁
		  select 
		  request_session_id spid,                              （死锁进程id）
		  OBJECT_NAME(resource_associated_entity_id) tableName  （死锁进程名称）
		  from sys.dm_tran_locks 
		  where resource_type='OBJECT'                          （数据类型所有）
		  
		  
		  -- 处理死锁
		  declare @spid int                                      //声明id
		  Set     @spid = 62                                     //设置死锁id
		  declare @sql varchar(1000)                             //声明sql 
		  set     @sql='kill '+cast(@spid as varchar)            //设置 kill掉死锁进程
		  exec    (@sql)                                         //执行
		  
		  ```
## 问题集

* CPU100%（慢sql）

```sql

-- Q：如何定位问题
-- A：DMV (dynamic management view)
-- 1. 资源不足
-- 2. 资源紧张


-- sql占CPU排名
SELECT TOP 10
dest.[text] AS 'sql语句'
FROM sys.[dm_exec_requests] AS der
CROSS APPLY
sys.[dm_exec_sql_text](der.[sql_handle]) AS dest
WHERE [session_id]>50
ORDER BY [cpu_time] DESC;

-- 表的索引情况
select db_name(us.database_id) as db_name
,object_schema_name(us.object_id)+'.'+object_name(us.object_id) as table_name
,i.name as index_name
,i.type_desc as index_type_desc
,us.user_seeks
,us.user_scans
,us.user_lookups
,us.user_updates
from sys.dm_db_index_usage_stats us 
inner join sys.indexes i 
on us.object_id=i.object_id and us.index_id=i.index_id
where us.database_id=db_id() 
--us.database_id=db_id('database_name')
--and us.object_id=object_id('schema_name.table_name')
order by us.user_seeks desc;


-- 吃cpu的sql
SELECT TOP 10
[session_id],
[request_id],
[start_time] AS '开始时间',
[status] AS '状态',
[command] AS '命令',
dest.[text] AS 'sql语句',
DB_NAME([database_id]) AS '数据库名',
[blocking_session_id] AS '正在阻塞其他会话的会话ID',
[wait_type] AS '等待资源类型',
[wait_time] AS '等待时间',
[wait_resource] AS '等待的资源',
[reads] AS '物理读次数',
[writes] AS '写次数',
[logical_reads] AS '逻辑读次数',
[row_count] AS '返回结果行数'
FROM sys.[dm_exec_requests] AS der
CROSS APPLY
sys.[dm_exec_sql_text](der.[sql_handle]) AS dest
WHERE [session_id]>50 AND DB_NAME(der.[database_id])='MISDB'
ORDER BY [cpu_time] DESC

-- 查询某个表的索引被使用情况
SELECT index_id, range_scan_count, row_lock_count, page_lock_count
FROM sys.dm_db_index_operational_stats(DB_ID('<db name>'),OBJECT_ID('table name'), NULL, NULL)


-- 查询正在被阻塞的sql
--request_mode表示查询要请求的锁，resource_description中的mode=x表示该查询被阻塞的锁
SELECT
t1.resource_type,
'database' = DB_NAME(resource_database_id),
'blk object' = t1.resource_associated_entity_id,
t1.request_mode,
t1.request_session_id,
t2.blocking_session_id,
t2.wait_duration_ms,
(SELECT SUBSTRING(text, t3.statement_start_offset/2+1,
    (CASE WHEN t3.statement_end_offset = -1 THEN LEN(CONVERT(nvarchar(max), text)) * 2
    ELSE t3.statement_end_offset END -t3.statement_end_offset)/2)
FROM sys.dm_exec_sql_text(sql_handle)) AS query_text,
t2.resource_description
FROM sys.dm_tran_locks AS t1, sys.dm_os_waiting_tasks AS t2,sys.dm_exec_requests AS t3
WHERE t1.lock_owner_address = t2.resource_address AND
t1.request_request_id = t3.request_id AND
t2.session_id = t3.session_id;

-- 找出是否存在一个或多个等待获取“tempdb”中页面锁存器的线程
SELECT session_id,wait_type, wait_duration_ms, resource_description
FROM sys.dm_os_waiting_tasks
WHERE wait_type LIKE 'PAGE%LATCH_%' AND resource_description LIKE '2:%';

-- 查询来确认“tempdb”中当前引发最多分配和回收操作的执行查询
SELECT TOP 10
t1.session_id, t1.request_id, t1.task_alloc, t1.task_dealloc,t2.plan_handle,
(SELECT SUBSTRING(text, t2.statement_start_offset/2 + 1,
    (CASE WHEN statement_end_offset = -1
          THEN LEN(CONVERT(nvarchar(MAX), text)) * 2
          ELSE statement_end_offset
    END - t2.statement_start_offset)/2)
FROM sys.dm_exec_sql_text(sql_handle)) AS query_text
FROM (SELECT session_id, request_id,
    SUM(internal_objects_alloc_page_count +
    user_objects_alloc_page_count) AS task_alloc,
    SUM(internal_objects_dealloc_page_count +
    user_objects_dealloc_page_count) AS task_dealloc
FROM sys.dm_db_task_space_usage
GROUP BY session_id, request_id) AS t1,
sys.dm_exec_requests AS t2
WHERE t1.session_id = t2.session_id AND
(t1.request_id = t2.request_id) AND t2.session_id > 50
ORDER BY t1.task_alloc DESC;

-- 查询找出当前执行事务或当前执行事务等待的锁
SELECT request_session_id AS spid, resource_type AS rt,resource_database_id AS rdb,
(CASE resource_type
    WHEN 'OBJECT' THENobject_name(resource_associated_entity_id)
    WHEN 'DATABASE' THEN ' '
    ELSE (SELECT object_name(object_id) FROM sys.partitionsWHERE hobt_id=resource_associated_entity_id)
END) AS objname,
resource_description as rd,
request_mode as rm,
request_status AS rs
FROM sys.dm_tran_locks;


```
- ```powershell
  # 1. find sqlcmd.exe
  # 利用ssms直接执行sql文件，应用于大的sql
  # -S 服务器 -U 用户名 -P 密码 -i 脚本 -d 数据库
  sqlcmd.exe -S 172.16.30.233 -U sa -P P@ssword123 -d dbName -i script.sql
  & "C:\Program Files\Microsoft SQL Server\110\Tools\Binn\SQLCMD.EXE" -S 172.16.30.233 -U sa -P Lcdatacenter_888 -d Test -i "C:\Users\lihe\Documents\script.sql"
  & "C:\Program Files\Microsoft SQL Server\110\Tools\Binn\SQLCMD.EXE" -S 172.16.30.233\uv_data_dev -U dev -P liancheng -d obpm_LianCheng_Data -i "C:\Users\lihe\Documents\script.sql"
  ```
- Geo类型
	- ```sql
	  -- 构造点
	  SELECT geometry::STGeomFromText('POINT(121.304528 31.316559)',0)
	  -- 是否闭合
	  select * 
	  from industry_land_price_2013_20210108_工业调整_bak 
	  where id=107 and bd_shape.STIsClosed()=1;
	  
	  
	  
	  -- 点是否在面内 大.STContains(小)
	  DECLARE @g geometry;
	  select * from industry_land_price_2013
	  where shape.STContains(geometry::STGeomFromText('POINT(121.304528 31.316559)',0))=1;
	  
	  ```
- 行列转换
	- ```sql
	  -- 行列转置
	  create table tb_RowConvertToColumn
	  (
	     username nvarchar(100) null,
	     course nvarchar(100) null,
	     score numeric(10,2) null
	  )
	  insert into tb_RowConvertToColumn(username,course,score) values('张三','语文',82)
	  insert into tb_RowConvertToColumn(username,course,score) values('张三','数学',85)
	  insert into tb_RowConvertToColumn(username,course,score) values('张三','外语',90)
	  insert into tb_RowConvertToColumn(username,course,score) values('李四','语文',86)
	  insert into tb_RowConvertToColumn(username,course,score) values('李四','数学',82)
	  insert into tb_RowConvertToColumn(username,course,score) values('李四','外语',92)
	  insert into tb_RowConvertToColumn(username,course,score) values('王五','语文',82)
	  insert into tb_RowConvertToColumn(username,course,score) values('王五','数学',94)
	  insert into tb_RowConvertToColumn(username,course,score) values('王五','外语',82)
	  
	  -- 方式1（选择指定的列，遍历MAX其值，作为列）
	  select username 姓名,
	         MAX(case course when '语文' then score else 0 end) 语文,
	         MAX(case course when '数学' then score else 0 end) 数学,
	         MAX(case course when '外语' then score else 0 end) 外语
	  from tb_RowConvertToColumn
	  group by username
	  order by username;
	  
	  -- 未知值，采用动态拼接SQL
	  declare @sql nvarchar(2000)
	  select distinct course into #tb_group from tb_RowConvertToColumn order by course desc--表头及排序
	  select @sql=ISNULL(@sql+',','')+'MAX(case course when '''+course+''' then score else 0 end) ['+course+']'
	   from #tb_group
	  set @sql='select username 姓名,'+@sql
	          +' from tb_RowConvertToColumn a'
	          +' group by username'
	  exec(@sql)
	  drop table #tb_group;
	  -- 使用语法糖
	  declare @sql nvarchar(2000)
	  select @sql=ISNULL(@sql+',','')+course
	  from tb_RowConvertToColumn
	  group by course
	  set @sql='select * from tb_RowConvertToColumn pivot (max(score) for course in ('+@sql+')) a'
	  exec(@sql);
	  
	  -- 语法糖
	  select username 姓名,语文,数学,外语 
	  from tb_RowConvertToColumn pivot(max(score) for course in(语文,数学,外语)) a;
	  -- 或者这样
	  select * 
	  from tb_RowConvertToColumn pivot(max(score)for course in (语文,数学,外语)) a;
	  
	  /* ================================= */
	  -- 列转行
	  create table tb_ColumnConvertToRow
	  (
	     [姓名] nvarchar(100) null,
	     [语文] nvarchar(100) null,
	     [数学] nvarchar(100) null,
	     [外语] nvarchar(100) null
	  )
	  
	  insert into tb_ColumnConvertToRow(姓名,语文,数学,外语) values('李四',82,92,86);
	  insert into tb_ColumnConvertToRow(姓名,语文,数学,外语) values('王五',94,82,82);
	  insert into tb_ColumnConvertToRow(姓名,语文,数学,外语) values('张三',85,90,82);
	  
	  
	  --1.静态sql列转行,当列头较少时使用
	  select * from 
	  (
	     select 姓名,课程='语文',分数=语文 from tb_ColumnConvertToRow
	     union all
	     select 姓名,课程='数学',分数=数学 from tb_ColumnConvertToRow
	     union all 
	     select 姓名,课程='外语',分数=外语 from tb_ColumnConvertToRow
	  ) a
	  
	  -- 列转行
	  select 姓名,课程,分数 
	  from tb_ColumnConvertToRow unpivot(分数 for 课程 in (语文,数学,外语)) a;
	  
	  
	  // 行转列
	  
	  CREATE table test
	  (id int,name nvarchar(20),quarter int,number int)
	  insert into test values(1,N'苹果',1,1000)
	  insert into test values(1,N'苹果',2,2000)
	  insert into test values(1,N'苹果',3,4000)
	  insert into test values(1,N'苹果',4,5000)
	  insert into test values(2,N'梨子',1,3000)
	  insert into test values(2,N'梨子',2,3500)
	  insert into test values(2,N'梨子',3,4200)
	  insert into test values(2,N'梨子',4,5500)
	  select * from test
	  
	  select ID,NAME,
	  [1] as '一季度',
	  [2] as '二季度',
	  [3] as '三季度',
	  [4] as '四季度'
	  from
	  test
	  pivot
	  (
	  sum(number)
	  for quarter in
	  ([1],[2],[3],[4])
	  )
	  as pvt
	  
	  
	  // 列转行
	  create table test2
	  (id int,name varchar(20), Q1 int, Q2 int, Q3 int, Q4 int)
	  insert into test2 values(1,'苹果',1000,2000,4000,5000)
	  insert into test2 values(2,'梨子',3000,3500,4200,5500)
	  select * from test2
	  
	  --列转行
	  select id,name,quarter,number
	  from
	  test2
	  unpivot
	  (
	  number
	  for quarter in
	  ([Q1],[Q2],[Q3],[Q4])
	  )
	  as unpvt
	  
	  ```
- sql写md5
	- ```sql
	  /*
	  对比结果
	  String md5Password = DigestUtils.md5DigestAsHex("SH2410643961942114304".getBytes());
	  */
	  -- demo
	  select lower(right(sys.fn_varbintohexstr(hashbytes('MD5', dbo.NCharToUTF8Binary('text',1))), 32))
	  
	  -- script
	  create function dbo.NCharToUTF8Binary(@txt NVARCHAR(max), @modified bit)
	  returns varbinary(max)
	  as
	  begin
	  -- Note: This is not the fastest possible routine. 
	  -- If you want a fast routine, use SQLCLR
	      set @modified = isnull(@modified, 0)
	      -- First shred into a table.
	      declare @chars table (
	      ix int identity primary key,
	      codepoint int,
	      utf8 varbinary(6)
	      )
	      declare @ix int
	      set @ix = 0
	      while @ix < datalength(@txt)/2  -- trailing spaces
	      begin
	          set @ix = @ix + 1
	          insert @chars(codepoint)
	          select unicode(substring(@txt, @ix, 1))
	      end
	  
	      -- Now look for surrogate pairs.
	      -- If we find a pair (lead followed by trail) we will pair them
	      -- High surrogate is \uD800 to \uDBFF
	      -- Low surrogate  is \uDC00 to \uDFFF
	      -- Look for high surrogate followed by low surrogate and update the codepoint   
	      update c1 set codepoint = ((c1.codepoint & 0x07ff) * 0x0800) + (c2.codepoint & 0x07ff) + 0x10000
	      from @chars c1 inner join @chars c2 on c1.ix = c2.ix -1
	      where c1.codepoint >= 0xD800 and c1.codepoint <=0xDBFF
	      and c2.codepoint >= 0xDC00 and c2.codepoint <=0xDFFF
	      -- Get rid of the trailing half of the pair where found
	      delete c2 
	      from @chars c1 inner join @chars c2 on c1.ix = c2.ix -1
	      where c1.codepoint >= 0x10000
	  
	      -- Now we utf-8 encode each codepoint.
	      -- Lone surrogate halves will still be here
	      -- so they will be encoded as if they were not surrogate pairs.
	      update c 
	      set utf8 = 
	      case 
	      -- One-byte encodings (modified UTF8 outputs zero as a two-byte encoding)
	      when codepoint <= 0x7f and (@modified = 0 OR codepoint <> 0)
	      then cast(substring(cast(codepoint as binary(4)), 4, 1) as varbinary(6))
	      -- Two-byte encodings
	      when codepoint <= 0x07ff
	      then substring(cast((0x00C0 + ((codepoint/0x40) & 0x1f)) as binary(4)),4,1)
	      + substring(cast((0x0080 + (codepoint & 0x3f)) as binary(4)),4,1)
	      -- Three-byte encodings
	      when codepoint <= 0x0ffff
	      then substring(cast((0x00E0 + ((codepoint/0x1000) & 0x0f)) as binary(4)),4,1)
	      + substring(cast((0x0080 + ((codepoint/0x40) & 0x3f)) as binary(4)),4,1)
	      + substring(cast((0x0080 + (codepoint & 0x3f)) as binary(4)),4,1)
	      -- Four-byte encodings 
	      when codepoint <= 0x1FFFFF
	      then substring(cast((0x00F0 + ((codepoint/0x00040000) & 0x07)) as binary(4)),4,1)
	      + substring(cast((0x0080 + ((codepoint/0x1000) & 0x3f)) as binary(4)),4,1)
	      + substring(cast((0x0080 + ((codepoint/0x40) & 0x3f)) as binary(4)),4,1)
	      + substring(cast((0x0080 + (codepoint & 0x3f)) as binary(4)),4,1)
	  
	      end
	      from @chars c
	  
	      -- Finally concatenate them all and return.
	      declare @ret varbinary(max)
	      set @ret = cast('' as varbinary(max))
	      select @ret = @ret + utf8 from @chars c order by ix
	      return  @ret
	  
	  end
	  go
	  ```
- 异常解决方案
	- ```
	  -- UPDATE 失败，因为下列 SET 选项的设置不正确: ‘QUOTED_IDENTIFIER‘。
	  
	  --> 解决方案：
	  在sql 语句前面加： 
	  SET QUOTED_IDENTIFIER ON
	  ```
- 逗号拆分多行
	- ```SQL
	  SELECT T.id
	    ,SUBSTRING(T.[value],R.number,CHARINDEX(',',T.[value]+',',R.number)-R.number) AS value
	    ,T.class
	    ,ROW_NUMBER() OVER (PARTITION BY T.id ORDER BY T.id) AS code
	  FROM TA AS T
	  JOIN (
	      SELECT TOP 10 number FROM  [master].dbo.spt_values  
	      WHERE [type]='P' ORDER BY number)AS R ON (CHARINDEX(',',','+T.[value],R.number)=R.number);
	      
	      
	  select stuff((select ','+ITEM_RANAME from TLK_小区信息管理  where item_projecttype='1' and ITEM_AIRAID='100180' for xml path('')),1,1,'') as name
	  ```
-
- 特殊符号
	- ```SQL
	  SQL的换行
	  
	  制表符 CHAR(9)
	  
	  换行符 CHAR(10)
	  
	  回车 CHAR(13)
	  
	  
	  PRINT 'SQL'+CHAR(13)+'ROAD'
	  PRINT 'SQL'+CHAR(10)+'ROAD'
	  PRINT 'SQL'+CHAR(9)+'ROAD'
	  
	  
	  --查看内存状态
	  dbcc memorystatus
	  
	  --查看哪个引起的阻塞，blk
	  EXEC sp_who active
	  
	  --查看锁住了那个资源id，objid
	  EXEC sp_lock
	  
	  ```


- ## 性能定位
- ### 通过sql的方式查询慢sql(毫秒)
- ```sql
  SELECT TOP 100
  (total_elapsed_time / execution_count)/1000 N'平均时间ms' ,total_elapsed_time/1000 N'总花费时间ms' ,total_worker_time/1000 N'所用的CPU总时间ms' ,total_physical_reads N'物理读取总次数' ,total_logical_reads/execution_count N'每次逻辑读次数' ,total_logical_reads N'逻辑读取总次数' ,total_logical_writes N'逻辑写入总次数' ,execution_count N'执行次数' ,SUBSTRING(st.text, (qs.statement_start_offset/2) + 1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(st.text) ELSE qs.statement_end_offset END - qs.statement_start_offset)/2) + 1) N'执行语句' ,creation_time N'语句编译时间' ,last_execution_time N'上次执行时间' FROM sys.dm_exec_query_stats AS qs CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) st
  
  ORDER BY total_elapsed_time / execution_count DESC;
  ```
- ### 通过可视化界面查询慢sql(远程和本地均可)
	- SQL Server Profiler
		- RPC:Completed 和 SQL:BatchCompleted。
		  RPC:Completed textdata要勾上，其他列使用默认就可以。
		- Duration:设置大于3000以上
		- DatabaseName
	- 数据保存方式
		- 插入数据库
		- 看完就销毁
		- 参数说明
			- ```
			  TextData：依赖于跟踪中捕获的事件类的文本值
			  CPU:事件使用的CPU时间（单位毫秒）
			  NTUserName：Windows用户名
			  Reads：由服务器代表事件读取逻辑磁盘的次数
			  Writes:由服务器代表事件写入物理磁盘的次数
			  Duration：事件占用的时间。尽管服务器以微秒计算持续时间，SQL Server Profiler确能以毫秒为单位显示该值，具体情况取决于‘工具’>‘选项’对话框中的设置 （见下图1）
			  SPID：SQL Server为客户端的相关进程分配的服务器进程ID
			  StartTime：事件启动事件
			  EndTime：事件结束事件。对指示事件开始的事件类。


```shell

scp -i ~/.ssh/purple_code -r /Users/lihe/workspace/hp-h5/dist root@bcp-test.fdcjj.org:/opt/bcp-app/h5

scp -i ~/.ssh/purple_code -r /Users/lihe/workspace/hp-h5/dist root@bcp-n1.fdcjj.org:/opt/bcp-app/h5

scp -i ~/.ssh/purple_code -r /Users/lihe/workspace/hp-h5/dist root@bcp-n2.fdcjj.org:/opt/bcp-app/h5  

ssh -i ~/.ssh/purple_code root@bcp-test.fdcjj.org:/opt/bcp-app/hp_h5

ssh -i ~/.ssh/purple_code root@bcp-n1.fdcjj.org

```



```sql

truncate table MSrepl_commands；  
truncate table MSrepl_transactions

DBCC SHRINKDATABASE('distribution')

```


