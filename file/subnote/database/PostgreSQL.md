# PostgreSQL

## 数据库连接串

```properties
jdbc.driverClassName=org.postgresql.Driver
jdbc.jdbcUrl=jdbc:postgresql://localhost:5432/handle
jdbc.username=postgres
jdbc.password=postgres123
```

## 安装docker版本PostgreSQL

```sh
docker pull postgres：16.4
```

### 启动PostgreSQL

- docker版本的pgsql启动成功后无需配置监听地址、端口（postgresql.conf）和ip（pg_hba.conf）

- 默认已经是监听所有地址，端口5432，ip：host all all all scram-sha-256

- 默认已经创建数据库postgres，用户postgres，密码登录时已经指定

```sh
# 启动数据库
docker run -p 5432:5432 --name postgres01 -e POSTGRES_PASSWORD=postgres123 \
    -v /data/postgresql:/var/lib/postgresql/data -d postgres:16.4
```

- compose.yaml

```yaml
postgresql:
    container_name: postgres01
    image: postgres:16.4
    ports:
        - "5432:5432"
    environment:
        - POSTGRES_PASSWORD=postgres123
    volumes:
        # PostgreSQL数据库版本17及以下的版本
        - /handle/data/postgresql:/var/lib/postgresql/data
        # PostgreSQL数据库版本18及以上的版本
        - /handle/data/postgresql:/var/lib/postgresql
    networks: 
        - my-docker-net
    restart: always
```

- 1.控制台登录Postgresql

```sh
# 进入数据库终端
docker exec -it postgres01 /bin/bash

# 切换用户为postgres
su - postgres

# 进入数据库
psql [postgres]
```

- 2.vscode安装数据库插件后连接数据库

nat网络，需要配置virtualbox端口映射，如5432:5432，则直接用127.0.0.1:5432连接

- 3.pgadmin4连接数据库

假设pgadmin4也是docker版本的并且和数据库在同一个虚拟系统上，则连接时直接用虚拟系统的ip+数据库端口连接

## pg控制台命令

|快捷键|功能|
|:-|:-|
|\password|设置密码|
|\h [sql命令]|查看sql命令的解释|
|\?|查看pgsql命令列表|
|\l|列出所有数据库|
|\c [database_name]|连接其它数据库|
|\d|列出当前数据库的所有表格|
|\d [tablename]|列出指定表的结构|
|\du|列出所有用户|
|q|退出|

## 备份

可以选择的备份格式：*.bak、*.sql、*.tar

- 单个数据库

```sh
# 备份数据库到bak文件
pg_dump dbname > dbname.bak

# 从bak文件恢复数据到指定数据库，数据库不存在时需要先创建数据库
psql dbname < dbname.bak
```

- 全部数据库

```sh
# 备份所有数据库到bak文件
pg_dumpall > pg.bak

# 从bak文件恢复所有数据库数据
psql -f pg.bak [-U] postgres
```

## 用户操作

```sql
-- 创建用户并设置密码
create user 'username' with password 'password';

-- 修改用户密码
alter user 'username' with password 'password';

-- 指定数据库的所有权限赋予指定用户
grant all privileges on database 'dbname' to 'username';

-- 赋予数据库的所有权限后，还要指定表的所有权限赋予指定用户，才可以读写表
grant all privileges on all tables in schema 'schema' to 'username';

-- 移除指定用户对于指定数据库的所有权限
revoke all privileges on database 'dbname' from 'username';

-- 删除用户
drop user 'username;
```

## 角色管理

pg没有区分用户和角色的概念，唯一区别就是创建用户和创建角色

```sql
-- 默认不具有登录属性
create role 'rolename';

alter role 'rolename' with login;

-- 默认具有登录属性
create user 'username';

-- 查询角色信息
select * from pg_roles;

-- 查询用户信息
select * from pg_user;
```

## 创建数据库

```sql
-- 当自定义的名字和关键字冲突时需加上英文双引号
create database "order";
comment on database "order" is '订单数据库'; 
```

## 创建表

```sql
create table account (
    id bigint,
    name varchar(32),
    password varchar(32),
    salt varchar(128),
    gender boolean,
    enabled boolean not null,
    creator bigint,
    modifier bigint,
    -- timestamptz在Java8中用OffsetDateTime对应
    creation_time timestamptz,
    modification_time timestamptz,
    version integer not null default 1,
    deleted boolean not null default false,
    primary key(id)
);

comment on table account is '账号表'; 
comment on column account.id is '主键';
comment on column account.name is '姓名';
comment on column account.gender is '性别';
comment on column account.enabled is '是否启用';
comment on column account.creator is '创建人';
comment on column account.modifier is '修改人';
comment on column account.creation_time is '创建时间';
comment on column account.modification_time is '修改时间';
comment on column account.version is '修改版本';
comment on column account.deleted is '是否已逻辑删除';
```

## pgadmin4

- 获取镜像

```sh
docker pull dpage/pgadmin4:<tag name>
```

- 启动容器

```sh
docker run -p 5050:80 \
-e 'PGADMIN_DEFAULT_EMAIL=user@domain.com' \
-e 'PGADMIN_DEFAULT_PASSWORD=pgadmin123' \
--name pgadmin4 \
-d dpage/pgadmin4:<tag name>
```
