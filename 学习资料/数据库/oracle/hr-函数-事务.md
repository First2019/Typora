set SERVEROUTPUT ON;
alter session set NLS_DATE_FORMAT='yyyy-mm-dd hh24:mi:ss';-- 修改当前会话时间格式，且采用24小时制

```
declare uname varchar2(20);
num NUMBER(4,2);--小数部分截取两位，整数部分必须小于等于2位
begin
  num:=1.232;
  DBMS_OUTPUT.PUT_LINE(num);
END;
```

**表复制**

```
create table test as select * from departments;
insert into test select * from departments; 
```

drop table test;
truncate table test;--得到一张空表，回滚无效
rollback;--事务回滚
commit;--提交
commit后回滚失效，未commit时数据存在缓存区内；

### 事务保存点

```mysql
insert into test values(1,'第一行',12,21);--事务开始
insert into test values(1,'第二行',12,21);
savepoint a;--定义事务保存点a；
insert into test values(1,'第三行',12,21);
rollback to savepoint a;--回滚到事务保存点a
commit;--最后提交
使用rollback将全部回滚
```

```mysql
BEGIN
  commit;--使设置事务属性在首行
  --设置了事务名称
  set TRANSACTION read only name 'ds';--只读事务
  SELECT * FROM employees;
  commit;
END;
commit;
```

### 隔离级别

set TRANSACTION ISOLATION LEVEL SERIALIZABLE;--设置事务隔离级别，串行隔离
set TRANSACTION ISOLATION LEVEL READ COMMITTED; --设置事务隔离级别，读已提交隔离
Raise_application_error(-20326,'Might not change '||'emp table during Nonworking hours');

### 字符串函数

```
select substr('abcdef',1,2)from dual;--下标从1开始
--substr()前下标可取负数，表示退到字符末尾往前，但不能实现首位相连，后数字表获取字符数
select lower('wdSA') from dual;--转换字符为小写
select upper('wdSA') from dual;--转换字符为大写
select length('sdsds') from dual;--计算字符数
select lengthb('sdsds三1') from dual;--计算字节数
select replace('JACK and JUE','J','BL') FROM DUAL;--将J换成BL
select trim(' as sd ')from dual;--去除首尾空格。
select replace(' as sd ',' ','')from dual;--去除所有空格
--lpad--左填充指定字符
--rpad--右填充指定字符
select LPAD('Page1',6,'*.') FROM DUAL;--数字指定字符总长度，左填充指定字符
select rpad('Page1',4,'*.') FROM DUAL;--数字指定字符总长度，右填充指定字符
select to_number('1234')from dual;
select instr('sadf','d')from dual;--找到字符下标,1开始
select mod(12,5)from dual;--求余
--concat('',''); ' ' || '' --都是字符拼接
select concat('sq','hello') from dual;
```

### 数值函数

```mysql
select round(45.926,-1) from dual; --45.93 四舍五入,后参数指明精度，可取负数
select round(45.926,-1) from dual;--50
select trunc(45.926,2) from dual;  --45.92 截取，后参数指明精度,可取负数
select trunc(45.926,-1) from dual;
select mod(6,5)from dual;--2 求余，前分子，后分母
select extract(year from sysdate) from dual;--提取日期
decode(job,
       'ds',sal+1000,
       'manager',sal+800,
       sal+400);--最后返回当前列
-- 日期相减得到相差天数
-- 日期加一个数字仍为日期
select SYSDATE,
       LAST_DAY(SYSDATE) "Last",--别名
       LAST_DAY(SYSDATE) - SYSDATE "Days Left"
  FROM DUAL;
```

select userenv('language') from dual;
--修改本次会话为支持中文
alter session set nls_date_language='SIMPLIFIED CHINESE';  
alter session set NLS_DATE_FORMAT='yyyy-mm-dd hh24:mi:ss';

### 日期函数

```
select MONTHS_BETWEEN
       (TO_DATE('02-02-1995','MM-DD-YYYY'),
        TO_DATE('03-02-1994','MM-DD-YYYY') ) "Months"
  FROM DUAL;
select add_months('2019-12-2',2)from dual;
select NEXT_DAY('2020-02-26','星期五') "NEXT DAY" FROM DUAL;--下一个星期五
select round(sysdate,'month'),round(sysdate,'year')from dual;--日期四舍五入
select round(months_between(sysdate,'2019-4-26'),0) from dual;--计算相差月份，前减后

select sysdate+1 from dual;
select to_char(sysdate,'mm') from dual;--获取月份
select to_char(sysdate,'hh24') from dual;--获取时
select to_char(sysdate,'yyyy') from dual;--获取年
select to_char(sysdate,'mm-dd') from dual;
select to_char(46213,'L999,999') from dual;--加符号
select to_char(462135466,'$999,999,999') from dual;
select * from employees where to_char(hire_date,'mm-dd')=to_char(sysdate,'mm-dd');
--nullif(a,b)a=b返回空，否则返回a
select nullif('sac','sac') from dual;--a=b返回空，否则返回a
update emp set comm=nvl2(comm,comm,0)+100;

select COALESCE(null,10, 5) from dual;
```

coalesce函数的参数是列，结果是取出第一个不为空的列的数据。
依次参考各参数表达式，遇到非null值即停止并返回该值。如果所有的表达式都是空值，最终将返回一个空值
nvl(a,b)--a为空返回b，否则返回a
nvl2(a,b,c)当a=null的时候，返回c，否则返回b--统计含空值的列
COALESCE(expression1,...n) 与此 CASE 函数等价：
CASE
WHEN (expression1 IS NOT NULL) THEN expression1
...
WHEN (expressionN IS NOT NULL) THEN expressionN
ELSE NULL

多行函数，where后不能使用组函数，组函数自动忽略空值
select count(DISTINCT manager_id) from employees;
select DISTINCT(manager_id) from employees;
select sum(DISTINCT manager_id) from employees;
select * from employees;--where job_id like '%REP%';

rollup(deptno,empjob);   相当于进行了三次分组
第一次把deptno,empjob两个约束条件，即deptno相同,empjob也相同的分为一组
第二次只用deptno一个条件再次分组，在上一次结果之上再分组，把deptno相同分为一组
将前两次结果分为一组
分完组后，执行sum()，为每次分组求和

```
select deptno,empjob,sum(sal)
  from emp
    where deptno is not null and empjob is not null
      group by ROLLUP(deptno,empjob)
        order by 1;
```


