# SQL Server

```sql
-- 指定数据库
use HR;

-- 查询表所有信息
select * from person;

-- 创建新表
create table person(
    id nvarchar(20) primary key,
    name nvarchar(4),
)

-- 加载指定数据库
exec sp_attach_db 'db1','D:\backup\db2.mdf','D:\backup\db2_log.ldf'

-- 备份数据库后缀.bak
backup database handle to disk='D:\handle.bak'

-- 恢复数据库
use master

restore database handle from disk ='D:\handle.bak' with replace,norecovery

restore log handle from disk ='D:\handle_log.bak' with recovery,stopat='2020-03-30 07:00:43'

restore database handle with recovery

-- 删除表
drop  table employee

-- 修改表，添加字段，带默认值
alter table person add  birthday datetime not null default '2019-8-8' 

-- 修改表，修改字段
alter table person alter column name nvarchar(4) not null 

-- 修改表，删除字段
alter table person drop column birthday 

-- 修改表，设置主键
alter table person add constraint pk_主键 primary key (id) 

-- 修改表，添加约束
alter table person add constraint ck_性别 check (性别='男' or 性别='女') 

-- 修改表，禁用约束
alter table person nocheck constraint ck_性别 

-- 修改表，删除约束
alter table person drop ck_性别 

-- 查询表的所有字段名
select name from syscolumns Where ID=OBJECT_ID('person') 

-- distinct 去重查询
select distinct 性别 from person 

-- union 合并结果集，上下
select * from person where 姓名='李白' union select * from person where 姓名='杜甫' 

-- in 关键字，in 中数据量不能超过1k条
select * from person where 姓名 in('杜甫','杜牧')  

-- like 关键字
select * from person where 姓名 like '%李%' 

-- 结果添加到新表temp
select * into person from person where 姓名='李白' union select * from person where 姓名='杜福' 

-- 创建空表，结构跟person一样，除了约束和标识等
select * into person from person where 0=1 

-- 多字段排序
select * from person order by age desc,name desc 

--查询重复记录
select * from person where id in (
    select id from person group by id having count(id)>1
)

-- 插入记录
insert into person select * from table2 

-- 删除记录
delete from person where age=60

-- 删除 person 中，id 在 other_tb.id中的记录
delete from person where exists(
    select id from other_tb where other_tb.id=person.id
)

-- 删除other_tb中，id不在person.id中的记录
delete from other_tb where id not in (
    select id from person
)

-- 表格记录全部删除
delete person 

-- update语句
update person set sex='男' where name='李白'
update person set person.phone_number= boss.phone_number from person,boss where person.sex='' and person.id=boss.id

-- 本地临时表：命名以#开头的表，创建它的用户可以使用，用户断开连接后SQL Server自动删除，C# connection打开然后关闭后就会自动删除
-- 全局临时表：命名以##开头的表，任何连接用户使用，所有使用该表的用户都断开连接后SQL Server自动删除

select * into #person from person

-- 使用函数
select min(出生日期),max(出生日期) from person  

select * from person where 出生日期 between '1994/1/1' and '1995/12/12' --日期类查询

select * from person where 产品处 is null --查询空值


select b.name,c.name from sysobjects a,syscolumns b,systypes c where
a.id=b.id and a.name='person' and a.xtype='u' and b.xtype=c.xtype  --字段及类型

-- 数字加单引号
select 工号 as '1' from person 

--批量数据（如id)从数据库中捞匹配数据：
--批量数据放入一个临时表，然后join：
select person.* from person join person on person. id=person.id

-- 创建新表
create table person(
    id nvarchar(20) primary key
)

-- 查询字段内容最大值长度
select max(len(name)) from person
select name from person where len(name)=4
```

varchar(4) ：可以存放2个汉字或四个英文字符
nvarchar(4) ：可以存4个汉字或4个其它字符

ldf 文档太大处理方法（先备份数据库）：
1.分离数据库文档为.mdf和.ldf
2.数据库名右键-tasks-detach-drop connection
3.删除数据库的.ldf文档或将其改名
4.databases右键-attached-添加.mdf文档，然后删除not found 的ldf，确定
5.右键数据库，属性，文档，重新设置ldf大小限制

数据库导数据OLEDB.12.0未注册解决方法：安装AccessDatabaseEngine2007
