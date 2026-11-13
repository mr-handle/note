# MySQL

## 安装

### Docker安装MySQL

```sh
# 安装
docker pull mysql:8.0.29

# 启动
docker run -p 3306:3306 --name mysql01 -e MYSQL_ROOT_PASSWORD=mysql123 -d mysql:8.0.29 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

- compose.yaml

```yaml
name: projectname
services: 
    mysql:
        container_name: mysql_container_name
        image: mysql:8.0.29
        ports:
            - "3306:3306"
        environment:
            - MYSQL_ROOT_PASSWORD: root_passwdord
            - MYSQL_DATABASE: dbname
            - MYSQL_USER: user_dbname
            - MYSQL_PASSWORD: user_password
        volumes:
            - mysql-data:/var/lib/mysql
            - mysql-conf:/etc/mysql/conf.d
        networks: 
            - projectname-net
        restart: always
networks: 
    projectname-net: 
        name: projectname-net
volumes: 
    mysql-data: 
        name: mysql-data
    mysql-conf: 
        name: mysql-conf
```

### windows安装（免安装版）

以下安装步骤均在管理员身份的dos窗口中执行。

1. 初始化数据库生成空的登录密码：

   ```cmd
   mysqld --initialize -insecure –user=mysql
   ```

2. 安装 mysql（服务）：

   ```cmd
       mysqld --install mysql
   ```

3. 启动 mysql 服务：

   ```cmd
   net start mysql
   ```

4. 登录 mysql 服务器，初始化没有生成密码，提示输入密码直接按回车登录：

   ```cmd
   mysql -u root -P 端口 -p
   ```

5. 修改 root 账户密码：

   ```cmd
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'mysql123';
   ```

6. 关闭服务：

   ```cmd
   net stop mysql
   ```

7. 移除 mysql（服务）：

   ```cmd
   mysqld --remove mysql
   ```

### RedHat系列Linux安装MySQL

```sh
# 查看是否安装了mysql相关的组件
rpm -qa | grep mariadb

# 卸载mysql相关组件
# --nodeps：就算被其它包依赖了也强行卸载
rpm -e --nodeps mariadb-libs

# 下载mysql-xxx.rpm-bundle.tar,然后解压
# 依次安装以下的rpm包
rpm -ivh mysql-community-common-xxx.rpm
rpm -ivh mysql-community-libs-xxx.rpm
rpm -ivh mysql-community-client-xxx.rpm
rpm -ivh mysql-community-server-xxx.rpm

# 启动mysql
sudo systemctl start mysqld

# 查看mysql初始账号密码
grep "password" /var/log/mysqld.log
```

## 登录

标识密码的p要小写，指定端口号的P要大写

```sql
mysql -u root -p

-- 指定ip和端口号登录
mysql -u root -p -h 127.0.0.1 -P 3306

-- 修改密码
set password = Password('你的密码');

-- 远程访问数据库
-- 修改host字段的值为需要远程连接数据库的主机ip地址或者直接修改成%
-- host的值为'%'表示所有主机可以通过该用户访问数据库
update user set host='%' where user='dbadmin';
flush privileges;
```

## phpMyAdmin

phpMyAdmin是mysql的数据库管理工具，可以通过浏览器进行操作

官网：<https://www.phpmyadmin.net/>

- 安装docker版phpmyadmin

```sh
docker pull phpmyadmin:5.2.3 
```

- compose.yaml

```yaml
phpmyadmin:
    image: phpmyadmin:5.2.3
    container_name: phpmyadmin
    ports:
        - 8080:80
    environment:
        PMA_ARBITRARY: 1
```

- 访问<http://你的ip:8080/>

## MySQL字符集

- 层级：server（MySQL 实例级别）、database（库级别）、table（表级别）、column（字段级别），优先级从左往右依次增大

- 查看数据库字符集

```sql
select default_character_set_name, default_collation_name
from information_schema.schemata 
where schema_name = 'db_name';

-- 显示数据库支持的字符集
show charset;
```

### 连接字符集

- character_set_client ：描述了客户端发送给服务器的 SQL 语句使用的是什么字符集。
- character_set_connection ：描述了服务器接收到 SQL 语句时使用什么字符集进行翻译。
- character_set_results ：描述了服务器返回给客户端的结果使用的是什么字符集

- 查看连接字符集

```sql
select * from performance_schema.session_variables
where variable_name in (
'character_set_client', 'character_set_connection',
'character_set_results', 'collation_connection'
) 
order by variable_name;
```

### 排序字符集

- utf8mb4_unicode_ci，是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序
- utf8mb4_general_ci，没有实现Unicode排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致，在比较和排序的时候更快
- 使用的时候统一只用某一种就行

### JDBC驱动字符集

- characterEncoding
- characterSetResults

- MySQL连接串

```properties
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# useUnicode=true&characterEncoding=UTF-8会自动映射为MySQL的utf8mb4
# 一般设置characterEncoding就可以了，新版MySQL驱动会自动处理好整个通信链路的字符集
# 它会向 MySQL 服务器发送一条关键的命令："SET NAMES utf8mb4;"
# 这条命令的效果等同于同时设置了以下三个会话变量，确保数据从客户端到服务端再返回客户端的全链路字符集一致：character_set_client、character_set_connection和character_set_results
spring.datasource.url=jdbc:mysql://localhost:3306/handle?useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=mysql123
```

## 数据类型

![mysql数据类型](/images/mysql数据类型.png)

char: 定长，非常适合存储密码的哈希值

varchar: 字符串列的最大长度比平均长度大很多，列的更新很少时使用

- 同财务相关的金额类数据必须使用 decimal 类型（精准浮点数，在计算时不会丢失精度）

- 存储时间两种方式
    - DATETIME + UTC：最推荐的“折中方案”
        - 存入时，应用层把用户时间转为 UTC，然后以标准格式（如 '2024-01-01 00:00:00'）写入 DATETIME 字段
        - 读取时：数据库原样返回 '2024-01-01 00:00:00'，应用层知道“这是 UTC 时间”，再转回用户本地时间
        - 可读性强：如果你直接去数据库查表（SELECT *），你看到的 2024-01-01 00:00:00 是人能看懂的格式，方便排查问题
    - BIGINT + UTC：极致的“性能方案”
        - 存入时：应用层计算时间戳数值（如 1704067200000），直接存入 BIGINT
        - 读取时：拿到数字，前端 JS 直接 new Date(1704067200000) 就能自动根据浏览器时区显示
        - 人眼不可读：如果你直接去数据库查表（SELECT *），看到 1704067200 是一脸懵的，必须写函数转换才能看懂

```sql
select 
  -- 1. 先除以1000转为秒（MySQL只认秒级）
  -- 2. from_unixtime 转为日期格式
  -- 3. convert_tz 从 UTC 转到 北京时间
  -- convert_tz 的第二个参数（源时区）必须明确
  -- 如果你的 from_unixtime 默认输出的是 UTC 时间，这里就填 'UTC'；如果数据库本身配置成了东八区，这里就要填 '+08:00'
  convert_tz(from_unixtime(create_time_ms / 1000), 'UTC', 'Asia/Shanghai') AS beijing_time
from tableName;
```

## MySQL用户操作

```sql
-- 创建用户账号并指定密码 
-- identified by指定的口令为纯文本，MySQL将在保存到user表之前对其进行加密
create user dbadmin identified by 'dbadmin123!!';

-- 更改口令，新口令必须传递到Password()函数进行加密
set password for dbadmin = Password('dbadmin123');

-- 在不指定用户名时，set password更新当前登录用户的口令
set password = Password('dbadmin123');

-- 修改账户名1
rename user old_username to new_username;

-- 修改账户名2
update user set user='new_username' where user='old_username';
flush privileges;

-- 删除用户账号和所有相关的账号权限
drop user dbadmin;

-- 显示当前用户
select user();

-- mysql数据库有一个名为user的表，它包含所有用户账号
use mysql;

-- user表的user列存储用户登录名
select user from user;
```

## 权限操作

```sql
-- 查看赋予用户账号的权限
-- USAGE表示根本没有权限
-- 用户定义为user@host MySQL的权限用用户名和主机名结合定义。
-- 如果不指定主机名，则使用默认的主机名%（授予用户访问权限而不管主机名）。
show grants for dbadmin;

-- 设置访问权限
-- GRANT和REVOKE可在几个层次上控制访问权限：
-- 整个服务器，使用GRANT ALL和REVOKE ALL；
-- 整个数据库，使用ON database.*；
-- 特定的表，使用ON database.table；
-- 特定的列
-- 特定的存储过程
-- GRANT要求你至少给出以下信息：要授予的权限；被授予访问权限的数据库或表；用户名。
grant select,create,alter,insert,update,delete,drop on database_name.* to dbadmin;

-- GRANT的反操作为REVOKE，用它来撤销特定的权限
-- 被撤销的访问权限必须存在，否则会出错
revoke select on database_name.* from dbadmin;
```

## 数据库操作

```sql
-- 创建数据库
create database [if not exists] `database_name` character set utf8mb4 collate utf8mb4_unicode_ci;

-- 修改数据库
alter database `database_name` [character set charset_name] [collate collation_name]

-- 删除数据库，同时删除该数据库相关的目录及其目录内容
drop database [if exists] 数据库名

-- 显示可用的数据库列表
show databases [like 'han%'];

-- 查看当前（选择的）数据库
select database();

-- 显示创建数据库的sql语句
show create database 数据库名称;

-- 选择（打开）数据库
use 数据库名称;
```

## 表操作

### 建表

建表：字符集utf8mb4 ，排序规则utf8mb4_unicode_ci, InnoDB引擎，增加事务处理，MyISAM引擎，高效处理插入和查询

```sql
create table account (
    id int unsigned not null auto_increment,
    name nvarchar(16) not null unique,
    password char(32) not null,
    salt char(128) not null,
    latestLoginCity nvarchar(32) not null default '',
    latestLoginIp char(16) not null default '',
    latestLoginTime datetime not null default now(),
    latestLogOutTime datetime not null default now(),
    primary key (id)
    # auto_increment=1 设置自增初始值
)auto_increment=1,character set = utf8mb4, collate = utf8mb4_unicode_ci, engine=InnoDB;
```

### 改表

```sql
-- 添加字段
alter table account add age int(3);

-- 修改字段，id自增到 int unsigned 最大值后不再允许插入
alter table account change column id id int unsigned auto_increment;

-- 删除字段
alter table account drop column age;

-- 添加主键
alter table account add primary key (id);

-- 删除主键
alter table account drop primary key;
```

### 删表

```sql
-- 如果表存在则删除
drop table if exists payment;
```

### 清空表数据

```sql
-- 删除指定数据
delete from account where name='Tom';

-- 清空表数据，产生binlog日志，可以回滚
delete from account;
-- 清空表数据，不产生日志，不能回滚，会把表的自增值重置和索引恢复到初始大小等
truncate table account;
```

## CRUD

### 插入

```sql
-- 插入一行
insert into account (name, password)  values ('Jack', 'Jack123');
-- 插入多行
insert into account (name, password)  values ('Jack', 'Jack123'), ('Tom', 'Tom123');

-- 插入或替换，插入一条新记录，但如果记录已经存在，就先删除原记录，再插入新记录
replace into students (id, class_id, name, gender, score) values (1, 1, '小明', 'f', 99);

-- 插入或更新，插入一条新记录，但如果记录已经存在，就更新该记录，更新的字段由update指定
insert into students (id, class_id, name, gender, score) values (1, 1, '小明', 'f', 99) on duplicate key update name='小明', gender='f', score=99;

-- 插入或忽略，插入一条新记录，但如果记录已经存在，就啥事也不干直接忽略
insert ignore into students (id, class_id, name, gender, score) values (1, 1, '小明', 'f', 99);
```

### 查询

```sql
-- 强制使用指定索引，很多时候，数据库系统的查询优化器并不一定总是能使用最优索引。
-- 如果我们知道如何选择索引，可以使用FORCE INDEX强制查询使用指定的索引
-- 指定索引的前提是索引idx_class_id必须存在
SELECT * FROM students FORCE INDEX (idx_class_id) WHERE class_id = 1 ORDER BY id DESC;

-- 分组过滤排序查询
select name, count(*) as repeat_name
from account
where name is not null
group by name
having count(*) > 1;

-- 子查询：将一个select的结果作为另一个sql语句的数据来源或判断条件
-- 子查询要放在()内
-- 子查询常用在from或where后边
-- from后边的子查询相当于一张临时表，需要使用as起一个表名
select t1.name 
from (select name from account where age > 18) as t1
where name in (
     select name from account where gender = 1
)

-- join连接查询
-- sql先根据on生成一张临时表，然后再根据where对临时表进行筛选
-- inner join：内连接（默认连接方式），只有当两个表都存在满足条件的记录时才会返回行
-- left [outer] join： 左(外)连接，返回左表中的所有行，即使右表中没有满足条件的行也是如此
-- right [outer] join：右(外)连接，返回右表中的所有行，即使左表中没有满足条件的行也是如此
-- full [outer] join ：全(外)连接，只要其中有一个表存在满足条件的记录，就返回行
select account.*,user.* 
from account
inner join user
on account.name=user.name 

-- 如果两张表的关联字段名相同，也可以使用using来代替on
select account.*,user.* 
from account
inner join user
using(name)

-- union组合查询
-- 所有查询的列数和列顺序必须相同
-- 所有查询中涉及表的列的数据类型必须相同或兼容
-- union结果集中的列名总是等于union中第一个select语句中的列名
-- union去重，union all不去重
select name from account
union all
select name from user
```

## 其他操作

```sql
-- 查看数据库端口
show global variables like 'port';

--显示可用数据库列表
show databases;

--选择数据库
use 数据库名;

--显示数据库的表
show tables;

--显示表的字段1
show columns from account;

--显示表的字段1
describe account;

-- 拼接字段
select concat(id,'(',name,')') from account order by id;

-- 显示数据库版本
select version();

-- 显示当前时间
select now();

-- 显示数据库支持的存储引擎
show engines;

-- 查看MySQL当前默认的存储引擎
show variables like '%storage_engine%';

-- 查看数据库中某个表使用的存储引擎
show table status from database_name where name='account';

-- 查看mysql默认隔离级别
select @@transaction_isolation;

-- 显示当前选择的数据库内可用表的列表
show tables;

-- 显示创建表的sql语句
show create table payment;

-- 显示表的字段信息
show columns from account;
describe account;

-- 显示广泛的服务器状态信息
show status;

-- 显示创建表的sql语句
show create table account;

-- 显示服务器错误消息
show errors;

-- 显示服务器警告消息
show warnings;

-- 模糊查询
select * from account where name like "%" 'ku' "%";

-- 限制结果
-- 分页时一定要配合 order by 使用
-- limit 偏移量，返回行数 （limit只有一个参数时表示的是返回行数，此时偏移量默认为零）
select * from employee order by id asc limit 0, 20;

-- MySQL 5支持 limit 的另一种替代语法：limit size offset rowindex
select * from employee order by id asc limit 20 offset 0;

-- 正则表达式
-- MySQL中的正则表达式匹配（自版本3.23.4后）大写和小写都匹配
-- 可添加 binary 关键字区分大小写

-- 检索列name包含文本'R'的所有行
select * from account where name regexp binary 'R';

-- '.' 表示匹配任意一个字符
select * from account where password regexp '.13';

-- '|' 表示or匹配
select * from account where id regexp '[1|2]';

-- '[]' 表示匹配几个字符之一，'-' 定义一个范围
select * from account where password regexp '[0-9]';

-- 为了匹配特殊字符，必须用'\\特殊字符'

-- 拼接字段,列值为id(name)
select concat(id,'(',name,')') as aaa from account order by id;

-- 连接表
-- 内部联结（等值联结），inner join， on 为连接条件（同where）
select account.*, employee.sex, employee.age from account inner join employee on account.id = employee.id;

-- 自联结
-- 自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。
-- 应该试一下自联结和子查询两种方法，以确定哪一种的性能更好。
-- 子查询 select * from employee where sex = (select sex from employee where sex='1' limit 0, 1);
select e1.* from employee as e1, employee as e2 where e1.id = e2.id and e2.sex='1';

-- 自然联结，自然联结排除多次出现的列，使每个列只返回一次。
-- 一般是通过对某张表使用通配符（SELECT *），对所有其他表的列使用明确的子集来完成的。
-- 内部联结基本都是自然联结
select account.*, employee.sex, employee.age from account,employee where account.id = employee.id;

-- 外部联结,包含了那些在相关表中没有关联行的行。
-- 在使用 outer join 时，必须使用 right 或 left 关键字指定包括其所有行的表
-- right 指出的是包括 outer join 右边的表所有行，left 指出的是包括 outer join 左边的表所有行。
select account.*, employee.sex, employee.age from account left outer join employee on account.id = employee.id;

-- group by 分组数据
-- 分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。
-- group by 子句必须出现在 where 子句之后，order by 子句之前
select sex, count(*) as number from employee group by sex;

-- coalesce(a, b, ...)：如果a!=null则返回a，如果a==null则返回b，...，如果都为null则返回null
select coalesce(2, 'b') as someColumnName; 

-- with rollup 可以得到每个分组以及每个分组汇总后的值
select coalesce(sex, 'sum') as category, sex, count(*) from employee group by sex with rollup;

-- having 过滤分组，（用法与where类似，区别是 having 过滤分组，where 过滤行，where 在数据分组前进行过滤，having 在数据分组后进行过滤。）
select sex, count(*) as number from employee group by sex having number >= 2;

-- coalesce，where，group by，with rollup，having，order by 一起使用
select coalesce(sex, 'sum') as category, count(*) as number from employee where age>=18 group by sex  with rollup having number >= 3 order by sex asc;

-- 测试
select 3*2;
select rtrim(' abc ') as rtrim;
select ltrim(' abc ');
select trim(' abc ');
-- 返回当前日期时间
select now();
select upper('abc');
select lower('ABC');
select length('abc');
select curdate();
select curtime();
select year(curdate());
select month(curdate());
select abs(-3);
select pi();

-- 聚集函数(汇总数据)
-- COUNT(*)对表中行的数目进行计数，不管表列中包含的是空值（NULL）还是非空值
select count(*) from account;
select sum(age) from employee;
select distinct id from account;

-- 普通查询
select * from account;

-- DELETE语句从表中删除行，甚至是删除表中所有行
delete from account;

-- TRUNCATE实际是删除原来的表并重新创建一个表，而不是逐行删除表中的数据
truncate account;

-- 重命名表
rename table acc to account;

create table if not exists `user` (
    id bigint not null auto_increment comment 'id',
    name varchar(32) not null comment 'name',
    password nvarchar(16) not null,
    primary key(id)
)
engine = innodb
default character set = utf8mb4
collate = utf8mb4_unicode_ci
comment ='用户表';

-- 返回最后一个AUTO_INCREMENT值
select last_insert_id();


select * from account;

-- 刷新表，清除缓存，同时防止备份时候有新数据写入
flush tables with read lock;
unlock tables;

-- 查看数据库端口
show global variables like 'port';


-- 远程访问数据库
-- 修改host字段的值为需要远程连接数据库的主机ip地址或者直接修改成%
-- host的值为'%'表示所有主机可以通过该用户访问数据库
update user set host='%' where user='dbadmin';
flush privileges;

create table user(
 id int unsigned auto_increment primary key,

    -- gmt 表示格林威治时间，北京是GMT+8
    -- gmt_create表示主动式创建
    gmt_create datetime not null,
    
    -- gmt_modified 过去分词表示被动式更新
    gmt_modified datetime not null default now(),
    
    name varchar(16) not null,
    
    -- tinyint unsigned范围：0-255
    -- 括号中的数字，不表示存储长度（范围），表示的是显示宽度
    -- tinyint(1)  和 tinyint(3) 没什么区别，占用字节都是一位，
    -- 对存储的值123来说，tinyint(1) 只显示一位数字，tinyint(3) 显示三位数字
    -- tinyint(3) zerofill ，当插入的数据少于3位的时候，如存储值为1，则显示001
    -- tinyint() 显示长度设置后期版本会舍弃
    is_male tinyint unsigned  not null,
    age tinyint unsigned  not null,
    
    -- 强制小数类型为 decimal
    -- decimal(n, m)表示数值中共有n位数，其中整数n-m位，小数m位
    -- decimal(n, m) unsigned 无符号设置后期版本会舍弃
    height decimal(5, 2)  not null,
    weight decimal(5, 2)  not null,

    -- 余额
    balance decimal(12, 2)  not null
    
    -- auto_increment=1 设置自增初始值
)auto_increment=1,CHARACTER SET = utf8mb4, COLLATE = utf8mb4_unicode_ci, ENGINE=MyISAM;

select * from user;
```

## 索引

### 创建索引

```sql
-- 创建普通索引
create index index_name on `table_name` (`column`);

-- 创建唯一索引
create unique index index_name on `table_name` (`column`);

-- 创建组合索引
create index idx_columnName1_columnName2 on `table_name` (columnName1, columnName2)
```

### 添加索引

```sql
-- 添加主键索引
alter table `table_name` add primary key (`column`)

-- 添加唯一索引
alter table `table_name` add unique (`column`)

-- 添加普通索引
alter table `table_name` add index index_name (`column`)

-- 添加多列索引
alter table `table_name` add index index_name ( `column1`, `column2`, `column3` )

-- 添加全文索引
alter table `table_name` add fulltext (`column`)
```

### 删除索引

```sql
alter table `table_name` drop index index_name;
```

- 查询时关联列类型不一致会自动进行数据类型隐式转换，会造成列上的索引失效
- 不同的字符集进行比较前需要进行转换会造成索引失效
- 更新十分频繁、区分度不高（如性别）的字段不适合建索引
- 建立组合索引，区分度高（重复度低）的放左边，能更加有效地过滤数据
- 字段长度小的列放在联合索引的最左侧（因为字段长度越小，一页能存储的数据量越大，IO 性能也就越好）
- 使用最频繁的列放到联合索引的左侧（这样可以比较少的建立一些索引）
- 在定义联合索引时，如果 a 列要用到范围查找的话，就要把 a 列放到联合索引的右侧，使用 left join 或 not exists 来优化 not in 操作，因为 not in 也通常会使用索引失效

- 查询字段的区分度

```sql
-- 越接近1，区分度越高
select count(distinct columnName) / count(*) as columnName_rate from tableName
```

- 回表：通过辅助索引拿到主键后，再回到主键索引查询的过程，需要尽量减少回表次数，提高查询效率
- 覆盖索引（包含了所有查询字段：where,select,order by,group by 包含的字段的索引）避免回表
- 给有大量数据的表新建索引：新建一张表+建索引+导入旧表数据+废弃旧表

## 事务

### 事务隔离级别

读未提交、读已提交、可重复读、可串行化

- 脏读：一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读
- 不可重复读：在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致
- 幻读：在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了

### 事务处理

- 事务处理用来管理insert、update和delete语句，不能回退select、create或drop操作
- MySQL默认是隐式提交
- 当commit或rollback语句执行后，事务会自动关闭（MySQL变回隐式提交）

```sql
select * from account;

-- 开始事务
start transaction;
delete from account;
select * from account;

-- rollback 命令用来回退（撤销）MySQL语句
rollback;
select * from account;

-- 在事务处理块中，提交不会隐含地进行。为进行明确的提交，使用 COMMIT 语句
select * from account;
start transaction;
delete from account where id=4294967295;
select * from account;
commit;
select * from account;

-- 使用保留点
-- 每个保留点都取标识它的唯一名字，以便在回退时，MySQL知道要回退到何处
-- rollback to 用于回滚到指定的保留点；如果没有设置保留点，则回退到start transaction语句处
-- 保留点在事务处理完成（执行一条rollback或commit）后自动释放
-- 也可以用RELEASESAVEPOINT明确地释放保留点
savepoint point1;

-- 回退到保留点
rollback to point1;

-- 释放保留点
release savepoint point1;

-- 更改默认的提交行为
-- autocommit标志是针对每个连接而不是服务器的
-- 设置autocommit为0（假）指示MySQL不自动提交更改
set autocommit = 0;
start transaction;
select * from account;
insert into account (name,password) values('raidon','kien');
insert into account (name,password) values('nintendo','nn134');
rollback;
```

## 并发事务的控制方式

MySQL 中并发事务的控制方式无非就两种：锁 和 MVCC

锁可以看作是悲观控制的模式

多版本并发控制（MVCC，Multiversion concurrency control）可以看作是乐观控制的模式

### 锁

锁控制方式下会通过锁来显式控制共享资源而不是通过调度手段，MySQL 中主要是通过`读写锁`来实现并发控制

- InnoDB的行锁是通过锁住索引来实现的

- 索引命中，加的是行锁

```sql
-- 假设id是主键
update user set user_name = #{userName} where id=#{id}
```

- 索引没命中，加的是表锁

```sql
-- 假设user_age没有加索引
update user set user_name = #{userName} where user_age=#{userAge}
```

## 共享锁和排它锁

共享锁（S 锁）：又称读锁，事务在读取记录的时候获取共享锁，允许多个事务同时获取（锁兼容）

排他锁（X 锁）：又称写锁/独占锁，事务在修改记录的时候获取排他锁，不允许多个事务同时获取。如果一个记录已经被加了排他锁，那其他事务不能再对这条记录加任何类型的锁

```sql
-- 共享锁
select ... lock in share mode;

-- 排他锁
select ... for update;
```

### 意向锁（Intention Lock）

意向锁用来快速判断是否可以对某个表使用表锁

意向锁是由数据引擎自己维护的，用户无法手动操作意向锁，在为数据行加共享/排他锁之前，InnoDB 会先获取该数据行所在在数据表的对应意向锁

意向共享锁（Intention Shared Lock，IS 锁）：事务有意向对表中的某些记录加共享锁（S 锁），加共享锁前必须先取得该表的 IS 锁

意向排他锁（Intention Exclusive Lock，IX 锁）：事务有意向对表中的某些记录加排他锁（X 锁），加排他锁之前必须先取得该表的 IX 锁

### InnoDB的行锁

记录锁（Record Lock）：属于单个行记录上的锁

间隙锁（Gap Lock）：锁定一个范围，不包括记录本身

临键锁（Next-Key Lock）：Record Lock+Gap Lock，锁定一个范围，包含记录本身，主要目的是为了解决幻读问题。记录锁只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁

在 InnoDB 默认的隔离级别 REPEATABLE-READ 下，行锁默认使用的是 Next-Key Lock

但是，如果操作的索引是唯一索引或主键，InnoDB 会对 Next-Key Lock 进行优化，将其降级为 Record Lock，即仅锁住索引本身，而不是范围

### MVCC

MVCC 是多版本并发控制方法，即对一份数据会存储多个版本，通过事务的可见性来保证事务能看到自己应该看到的版本

通常会有一个全局的版本分配器来为每一行数据设置版本号，版本号是唯一的

MVCC 在 MySQL 中实现所依赖的手段主要是: 隐藏字段、read view、undo log

undo log : 用于记录某行数据的多个版本的数据

read view 和 隐藏字段 : 用来判断当前版本数据的可见性

### 当前读和快照读

快照读（一致性非锁定读）就是单纯的 SELECT 语句

快照读的情况下，如果读取的记录正在执行 UPDATE/DELETE 操作，读取操作不会因此去等待记录上 X 锁的释放，而是会去读取行的一个快照

只有在事务隔离级别读已提交和可重读下，InnoDB 才会使用一致性非锁定读：

在 读已提交 级别下，对于快照数据，一致性非锁定读总是读取被锁定行的最新一份快照数据

在 可重读 级别下，对于快照数据，一致性非锁定读总是读取本事务开始时的行数据版本

快照读比较适合对于数据一致性要求不是特别高且追求极致性能的业务场景

当前读 （一致性锁定读）就是给行记录加 X 锁或 S 锁，常见sql如下

```sql
-- 对读的记录加一个X锁
select ... for update

-- 对读的记录加一个S锁
select...lock in share mode

-- 对读的记录加一个S锁
select...for share

-- 对修改的记录加一个X锁
insert ...
update ...
delete ...
```

## 删除重复数据

```sql
-- 查看重复数量1
select columnName,count(1) from tableName group by columnName having count(1) > 1

-- 查看重复数量2
select columnName,count(columnName) from tableName group by columnName having count(columnName) > 1

-- 查看重复数据
select * from tableName where columnName in (
    select columnName from tableName group by columnName having count(1) > 1
)

-- 重复数据全部删除
delete from tableName where columnName in (
    select t.columnName from (
        select columnName from tableName group by columnName having count(1) > 1
    ) t
)

-- 重复数据保留一条，其它删了
delete from tableName where id not in (
    select t.id from (
        select min(id) as id from tableName group by columnName
    ) t
)

```

## sql优化

- where从句中禁止对列进行函数转换和计算，会导致索引失效
- 没有重复值时用union all

### SQL的执行计划

执行计划是指一条 SQL 语句在经过 MySQL 查询优化器的优化会后，具体的执行方式

可以使用 EXPLAIN 命令来分析 SQL 的 执行计划

EXPLAIN 并不会真的去执行相关的语句，而是通过 查询优化器 对语句进行分析，找出最优的查询方案，并显示对应的信息。

EXPLAIN 适用于 SELECT, DELETE, INSERT, REPLACE, 和 UPDATE语句，我们一般分析 SELECT 查询较多

```sql
explain select语句
```

- explain输出的各个列的含义

|列名|描述|
|:-|:-|
|id|select 查询的序列标识符|
|select_type|select 关键字对应的查询类型|
|table|用到的表名|
|partitions|匹配的分区，对于未分区的表，值为 NULL|
|type|表的访问方法|
|possible_keys|可能用到的索引|
|key|实际用到的索引|
|key_len|所选索引的长度|
|ref|当使用索引等值查询时，与索引作比较的列或常量|
|rows|预计要读取的行数|
|filtered|按表条件过滤后，留存的记录数的百分比|
|Extra|附加信息|

## 存储过程

```sql
-- 如果使用的是mysql命令行实用进程，需要临时更改命令行实用进程的语句分隔符
-- 除\符号外，任何字符都可以用作语句分隔符
delimiter //

-- 创建存储过程
create procedure find()
begin
    select * from  employee;
end //

-- 恢复默认的语句分隔符
delimiter ;

-- 使用存储过程
call find();

-- 删除存储过程
drop procedure if exists find;

delimiter //
-- 存储过程将保存结果的3个变量名，所有MySQL变量都必须以@开始
-- IN（传递给存储过程）、OUT（从存储过程传出）
create procedure finduser(in userId int unsigned, out userName  varchar(8), out password varchar(16))
begin
    select name into userName from account where id= userId; 
    select account.password into password from account where id= userId; 
end //
delimiter ;

drop procedure if exists finduser;

call finduser(2, @name, @password);

select @name,@password;
```

## MySQL数据库备份

### 逻辑备份

备份的是SQL语句，效率较低，用于中小型企业

```sh
# 备份到sql文档，表名为空则复制整个数据库
mysqldump -u root -p -P 3306 handle account employee spidata --single-transaction > d:/handle_backup.sql

# 从sql文档恢复
mysql -u root -p -P 3306 handle < d:/handle_backup.sql
```

## 找回mysql的root密码

```sh
# 适用mysql5.7
# 追加一行：skip-grant-tables
vim /etc/my.cnf

# 重启
systemctl restart mysqld

# 登录，输入空密码就行
mysql -u root -p

# 选择mysql数据库
use mysql;

# 设置新密码
update user set authentication_string=password("具体密码") where user = 'root';

# 刷新权限
flush privileges;

# 退出
exit

# 删除或注释：skip-grant-tables
vim /etc/my.cnf

# 重启
systemctl restart mysqld
```

## 读写分离

部署多台数据库，选择其中的一台作为主数据库，其他的一台或者多台作为从数据库。

保证主数据库和从数据库之间的数据是实时同步的，这个过程也就是我们常说的主从复制。

系统将写请求交给主数据库处理，读请求交给从数据库处理。

- 实现方式有两种
    - 代理方式：如使用MySQL Router
    - 组件方式（推荐，用得最多）：如使用ShardingSphere-JDBC

## 主从复制原理

MySQL binlog(binary log 即二进制日志文件) 主要记录了 MySQL 数据库中数据的所有变化(数据库执行的所有 DDL 和 DML 语句)

因此，根据主库的 MySQL binlog 日志就能够将主库的数据同步到从库中

当然，除了主从复制之外，binlog 还能帮助我们实现数据恢复

- 主从复制过程
    - 主库将数据库中数据的变化写入到 binlog

    - 从库连接主库

    - 从库会创建一个 I/O 线程向主库请求更新的 binlog

    - 主库会创建一个 binlog dump 线程来发送 binlog ，从库中的 I/O 线程负责接收

    - 从库的 I/O 线程将接收的 binlog 写入到 relay log 中

    - 从库的 SQL 线程读取 relay log 同步数据到本地（也就是再执行一遍 SQL ）

MySQL 主从复制是依赖于 binlog 。另外，常见的一些同步 MySQL 数据到其他数据源的工具（比如 canal）的底层一般也是依赖 binlog

Redis 也是通过主从复制实现的读写分离

## 如何避免主从延迟

- 对于极少数必须强一致的业务，强制将读请求路由到主库处理

```java
// ShardingSphere-JDBC 强制读主库
HintManager hintManager = HintManager.getInstance();
hintManager.setMasterRouteOnly();
// 继续JDBC操作
```

- 延迟读取，在完成写请求之后，避免立即进行请求操作。比如你支付成功之后，跳转到一个支付成功的页面，当你点击返回之后才返回自己的账户。

## 分库分表

读写分离主要应对的是数据库读并发，而分库分表应对的是数据库存储压力

- 分库：将数据库中的数据分散到不同的数据库上，可以垂直分库，也可以水平分库
    - 垂直分库：把单一数据库按照业务进行划分，不同的业务使用不同的数据库，进而将一个数据库的压力分摊到多个数据库
    - 水平分库：按“行”将数据拆分到不同的数据库实例中（如订单库1，订单库2，...）
- 分表：对单表的数据进行拆分，可以是垂直拆分，也可以是水平拆分
    - 垂直分表：对数据表列的拆分，把一张列比较多的表拆分为多张表
    - 水平分表：对数据表行的拆分，把一张行比较多的表拆分为多张表（如订单表1，订单表2，...)，可以解决单一表数据量过大的问题

水平拆分只能解决单表数据量大的问题，为了提升性能，我们通常会选择将拆分后的多张表放在不同的数据库中。也就是说，水平分表通常和水平分库同时出现。
