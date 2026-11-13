# Oracle

```sql
-- 修改字段名
alter table student rename column password to pwd;

-- 修改字段数据类型
alter table student modify password varchar(16) not null;
```

## 函数

- upper('value')

将参数值转成大写后返回

- lower('VALUE')

将参数值转成小写后返回

- to_char(create_time,'yyyy-MM-dd HH24:mi:ss')

将字段值转为指定格式字符串

- to_date('2022-02-02 13:14:20','yyyy-MM-dd HH24:mi:ss')

将参数值转为指定格式的日期

- nvl(表达式1，表达式2)

把一个空值（null）转换成一个实际的值。如果表达式1为空值，返回表达式2的值，否则返回表达式1的值。表达式1和表达式2的数据类型必须为同一个类型。

- nvl2(表达式1，表达式2，表达式3)

如果表达式1为空，返回值为表达式3的值。如果表达式1不为空，返回值为表达式2的值

- round(number, 小数位数)

四舍五入

- replace(source, 被替换的字符, 替换成什么字符)

## 分页查询

```sql
select * from (
    select rownum rowNumber, total.* from (
        select ... order by ...
    ) total
    where rownum < pageNumber * pageSize
)
where rowNumber > (pageNumber - 1) * pageSize
```

## sql

```sql
--查询当前用户下是否有某个表，表名要大写
select table_name from user_tables where table_name=upper('temp_tb')

--复制表结构及数据，不创建索引
create table temp_tb as select * from person

--替换字段中的字符值
update TEMP_G6011119_20191205 set id=replace(id,'G6',' ')
update temp_G6011119_20191224 set BIRTH_DATE = to_date(replace(to_char(BIRTH_DATE,'yyyy/MM/dd'),'20','11'),'yyyy/MM/dd') where to_char(BIRTH_DATE,'yyyy/MM/dd') like '%1994%'

--修改字段
alter table temp_G6011119_20191204 modify(BIRTH_DATE NVARCHAR2(16))  

--日期类型查询与修改

-- dual是存在于oracle实实在在的表,常用于select中没有目标表的查询
-- trunc(sysdate) 当前日期0时0分0秒
select trunc(sysdate) from dual;
select sysdate from dual;
select to_date(sysdate,'yyyy-MM-dd HH24:mi:ss') from dual;
select to_char(sysdate,'yyyy-MM-dd HH24:mi:ss') from dual;

--当前时间N秒前的数据
select * from cpesmt.jt_reflow_data where work_time between sysdate+3/(24*60*60) and sysdate order by work_time asc

select * from  temp_G6011119_20191204 where to_char(BIRTH_DATE,'yyyy/MM/dd') like '%1994%'

update  temp_G6011119_20191204 set BIRTH_DATE=to_date('2000/10/10','yyyy/MM/dd') where to_char(BIRTH_DATE,'yyyy/MM/dd') like '%1994%'


--创建表注释或者为表重新注释
comment on table temptb is '临时表，会自动删除，请管理员不要手动删除'

--查询表注释
select table_name,comments from user_tab_comments where table_name=upper('temptb')

--删除表
drop table temp_tb

--查询字段及字段类型
select column_name,data_type  from all_tab_cols  where table_name=upper('temp_tb')
--查询表的所有字段名，表名要大写
select column_name from user_tab_columns where table_name=upper('temp_tb')

--查询字段值长度最长的记录的长度
select max(length(CONTROL_TYPE_NAME)) from  EMP_INFO

--创建会话级临时表,当用户退出会话结束时（connection打开后关闭算会话结束），Oracle自动清除临时表中数据，但保留表结构
create global temporary table temp_tb on commit preserve rows as select * from person

-- 声明方法
declare
    i number(2) := 10;
    s varchar2(8) := '张三';
    uname Account.userName%type;
    urow Account%rowtype;
begin
    dbms_output.put_line(i);
    dbms_output.put_line(s);
    select userName into uname from Account where uid = 1;
    dbms_output.put_line(uname);
    select * into urow from Account where uid = 1;
    dbms_output.put_line(urow.uid || urow.userName);
end;

-- if判断
declare
    i number(2) := &i;
begin
    if i > 0 then
        dbms_output.put_line('正数');
    elsif i = 0 then
        dbms_output.put_line('0')
    else
        dbms_output.put_line('负数')
    end if;
end;
```
