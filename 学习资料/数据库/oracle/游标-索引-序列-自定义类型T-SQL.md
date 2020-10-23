SET AUTOCOMMIT off;--取消数据库自动提交
经过测试，read committed 更新行时，只对操作行加锁，其他用户操作其他行不会阻塞
dba角色：具有所有系统权限，不具备sysdba,sysoper特权（启动和关闭数据库）
alter session set NLS_DATE_FORMAT='yyyy-mm-dd hh24:mi:ss';--修改当前会话时间格式，且采用24小时制
set serveroutput ON;
identified ->标识
desc orders;

```mysql
create table orders(
  orderid number,
  oredrdate date,
  username varchar2(20)
)
```

```mysql
alter table orders modify (orderid primary key);--为已有列添加约束
select * from orders;
select * from newtab;
drop table newtab;
create table newtab as select orderid,oredrdate from orders;--创建新表并放入数据
```

```
alter table orders modify (orderid primary key);--为已有列添加约束
select * from orders;
select * from newtab;
drop table newtab;
create table newtab as select orderid,oredrdate from orders;--创建新表并放入数据
insert into orders values(10,TO_DATE('2020-2-21', 'yyyy-mm-dd'),'小李');
insert into orders values(11,TO_DATE('2020-2-20', 'yyyy-mm-dd'),'小王');
```

order by 2 asc;根据第二列升序排列 asc可以省略
**批量插入**
insert into tablename(lie1,lie2) select lie1,lie2 from tablename;

### 自定义类型

type 类型名 is record
{
 字段1 类型1,
 字段2 类型2
}
声明行记录类型

```
declare
type myc is record(--record：记录
  od number,
  dat date,
  na varchar2(20)
);
my myc;--定义变量
BEGIN
  select * into my from orders where orderid=10;
  dbms_output.put_line(my.dat);
END;
```

### plsql定义标签

 <<abc>>

 当内部块变量和外部变量重名时，要使用外部变量时使用：标签名.变量名 方式访问，如abc.x;

```mysql
<<xc>>
declare x number:=10;
BEGIN
  declare x number:=11;
  BEGIN
    dbms_output.put_line(x);
    dbms_output.put_line(xc.x);
  END;
END;
```

exec 执行
局部变量使用declare定义，必须在代码块中使用；
格式：declare num number;赋值：num:=3;
return 1;无条件终止查询或者批，后面语句不执行，整数可省略
waitfor 暂时停止直到到达或已过所设时间
BEGIN
  waitfor time '17:04:10' select * from orders;
END;

### 视图

grant create view to hr;
grant drop any view to hr;
create or replace view view1
as
  select * from employees
  with check option;
**with only read**;只读视图
**with check option**: 指定对视图执行的dml操作必须满足“视图子查询”的条件即,对通过视图进行的增删改操作进行"检查",要求增删改操作的数据, 
必须是select查询所能查询到的数据,否则不允许操作并返回错误提示. 默认情况下, 在增删改之前"并不会检查"这些行是否能被select查询检索到. 

### 创建序列

用于多个用户来产生唯一的数值的数据库对象
/*
increment by;序列变化的步进，负数表示递减，默认为1
start with起始值，默认为1
MAXvalue 最大值，默认为，NOMAXVALUE
minvalue默认不限制，为nominvalue
cycle 值达到限制时是否循环，nocycle:不循环，cycle：循环
cyche 缓存序列的个数，数据库异常中断可能导致不连续，默认为20，不使用缓存可设置nocyche，表示缓存区保存数字个数

将序列用于表
nextval和currval是伪列
nextval返回序列中下一个有效值，任何用户可使用--调用后指针后移
currval存放序列中当前值
*/

```
create sequence s_orderid
    increment by 2
    MAXvalue 100;
drop sequence s_orderid;
select s_orderid.nextval from dual;
BEGIN
  DBMS_OUTPUT.PUT_LINE(s_orderid.nextval);
END;
```

### 索引

```
create index x_deptno--创建索引
  on emp(deptno);
create index X_s--多列索引
  on emp(deptno,sal);
drop index x_deptno;--删除索引
```

存储表查询时的目录表，使用B树
**rowid**:行号，表被加载到实例后，每一行都有一个行号
select ROWID FROM employees;
索引只会影响到查询
删除表时自动删除
通过指针加速查询
对于经常修改的列不适合做索引，经常查询的列（条件列，外键列）适合做索引
可以在一列或多列上

总结:
索引概念:独立于表的模式对象，可以存储在与表不同的磁盘或表空间中
索引被删除或损坏不会对表产生影响，索引只会影响到查询
索引一旦建立，Oracle会对其自动管理和维护，由数据库决定何时使用索引，用户不用在查询中指定使用哪个索引

在删除表时，所有基于该表的索引会被自动删除
索引其实就是通过指针来加速Oracle服务器的查询速度
可以减少磁盘I/O
Oracle索引类型:
索引分类: 
B树索引(默认)-->增、 删、改频繁,Oracle的索引算法就是B树，树形结构的数据用查询算法可以非常高效的找到ROWID(行地址)，从而找到数据。
位图索引(矩阵)

#### 索引应用场景

什么时候创建索引? (面试必问)
以下情况适合创建索引1 :
1.列中数据值分布范国很广(比如一-本书，如果只有两页，虽然可以建一个目录,但意义不大)
2.列经常在where子名或连接条件中出现(掌握)
3.表经常被访问且数据量很大
4.主键就是一 个唯一 索引.所以在建表时大多都会指定一 个主键

### 查询计划

explain plan for
执行查询计划
select * from table(dbms_xplan.display);

### 游标

其实就是用于存放查询出来的多条记录的一个临时变量，我们可以从这个变量中取出我们需要的信息字段。
游标有两种类型：显式游标和隐式游标。
**游标的属性有以下四种**
1.SQL%ROWCOUNT    返回值为一个整型数字  代表DML语句成功执行的数据行数   
2.SQL%FOUND   布尔型 值为TRUE代表插入、删除、更新或单行查询操作成功   
3.SQL%NOTFOUND    布尔型 值为true表示插入、删除、更新或单行查询操作失败。
4.SQL%ISOPEN  布尔型 DML执行过程中为真，结束后为假，是否打开了游标  

fetch-取得，
declare 
cursor name1 ()
is select语句
打开游标
open name1
从游标取数据
fetch name1 返回当前指针处记录，并将指针后移
游标属性：
show parameter cursor;  查看游标属性、sys
show parameter cursor;--查看游标属性、sys
设置最大可打开游标数
alter system set open_cursors=800 scope=both;
scope值：both-既改实例也改参数文件，memory-只改实例，下次不生效，
spfile-只改参数文件，必须重启数据库生效

```
declare
  CURSOR cur_addsal is
    select empno,empjob from emp;
    pempno number;
    pjob varchar2(20);
begin
  rollback;--先回滚其他操作
  open cur_addsal;
  if cur_addsal%isopen--已经打开
  then
  loop
      fetch cur_addsal into pempno,pjob;
      exit when cur_addsal%notfound;
      if pjob='PRESIDENT' then
        update emp set sal=nvl(sal,0)+1000 where empno=pempno;
      elsif pjob='MANAGER' then
        update emp set sal=nvl(sal,0)+800 where empno=pempno;
      else
        update emp set sal=nvl(sal,0)+400 where empno=pempno;
      end if;
      dbms_output.put_line('编号:'||pempno ||' 工作: ' || pjob);
  end loop;
  close cur_addsal;--关闭游标
  else
    dbms_output.put_line('打开失败');
  end if;
  commit;
end;
```

#### 带参数游标

```
declare
    vrows sys_refcursor;
    vrow orders%rowtype;
begin
    open vrows for select * from orders;
        loop
            fetch vrows into vrow;
            exit when vrows%notfound;
            dbms_output.put_line('姓名:'||vrow.username ||' ID: ' || vrow.orderid);
        end loop;
    close vrows;
end;
```



```mysql
DECLARE
    ADD_SQL    VARCHAR2(1000);        --定义添加字段的语句
    ADD_TABLE_NAME  VARCHAR2(50);     --定义获取的表名
    CURSOR ADD_TABLE_FIELD IS   --取名添加表字段
    SELECT TABLE_NAME FROM orders; --where table_name like 'WRI%SYNOPSIS$'; -- 查出指定的表出来
BEGIN
    OPEN ADD_TABLE_FIELD;
    LOOP
        -- 提取一行数据到ADD_TABLE_FIELD
        FETCH ADD_TABLE_FIELD  INTO ADD_TABLE_NAME;
        -- 判断是否读取到，没读取到就退出
        -- %notfound是没有取到的意思
        EXIT WHEN ADD_TABLE_FIELD%NOTFOUND;                         
        -- 下面sql语句中，表名两边都要有空格，不然不会执行语句的，即:[table ']和[' add]不能写成             [table']和['add]
        ADD_SQL:= 'alter table ' || ADD_TABLE_NAME || ' add 修改人 varchar2(20)';
        EXECUTE IMMEDIATE ADD_SQL;--执行该语句                         
    END LOOP;--关闭游标
    CLOSE ADD_TABLE_FIELD;
END;  
```

### Oracle中三种循环(For、While、Loop)

```oracle
-- 1.ORACLE中的GOTO用法
DECLARE
  x number;
BEGIN
  x := 9;
  <<repeat_loop>> -- 循环点
  x := x - 1;
  DBMS_OUTPUT.PUT_LINE(X);
  IF x > 0 THEN
    GOTO repeat_loop; -- 当x的值小于9时,就goto到repeat_loop
  END IF;
END;
```

