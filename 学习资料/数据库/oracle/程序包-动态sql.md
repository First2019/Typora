alter session set NLS_DATE_FORMAT='yyyy-mm-dd hh24:mi:ss';--修改当前会话时间格式，且采用24小时制
set serveroutput ON;

### oracle程序包

用于逻辑组合相关的PL/SQL类型，将功能或业务相同或相似的存储过程，函数及类型等进行的封装
包是数据库对象，相当于一个容器
**包的组成：**
包的申明部分
包体

优点：
模块化设计，实现信息隐藏，良好的性能体验，首次打开整个包被加载到内存中
减少磁盘I/O;

#### 包的申明

```
create or replace package 包名
is
  申明变量
  申明常量
  类型：游标
  申明存储过程
  procedure 过程名（参数列表）
  function
end 包名;
```

drop package pac_pk1;--删除包

```
create or replace package pac_pk1
is
    sname varchar2(50);
    T constant varchar(10):='TAB';-- 常量
    type ity is table of varchar(30);
    procedure p1;
    function fn(n number) return number;
end pac_pk1;
```

#### 创建包体

```mysql
create or replace package body pac_pk1
is
-- 实现过程
  procedure p1
  is 
    begin
      --输出一个乘法表
      for i in 1 .. 9 loop 
        for j in 1 .. i loop
          dbms_output.put_line(i||'*'||j||'='||i*j);
          end loop;
          dbms_output.put_line('');
          end loop;
    end;
  function fn(n number)
    return number
    is 
      s number:=1;
      begin
        for i in 1..n loop
        s:=i*s;
        end loop;
        return s;
  end;      
end;
```

```mysql
begin
  pac_pk1.sname:='210';--变量赋值
  --对于包体中的申明，外部程序不能访问
  --dbms_output.put_line(pac_pk1.sname);
end;
exec pac_pk1.p1;
select pac_pk1.fn(4) from dual;
```

### 动态SQL

#### PL/SQL中如何正确的使用sql语句

1.1
**select语句**不能单独使用，要和into结合使用
1.2  以下类型的语句可用
**DML**（insert update delete）
**TCL**（事务控制语句 commit rollback savepoint）
1.3
**DDL语句**（数据定义create）不能直接在plsql中使用,可以在动态sql中使用

```mysql
declare 
  sql_str varchar(1000);
begin
  sql_str:='create table test009(var_id number)';
  sql_str:=replace(sql_str,')',',');
  sql_str:=sql_str||'sal number(8,2))';
  dbms_output.put_line(sql_str);
  execute immediate sql_str;
end;
desc test009;
```

#### DML语句使用

```mysql
declare 
  var_id number:=123;
  var_sal NUMBER(8,2):=100;
  sql_str varchar(1000);
begin
  sql_str:='insert into test009 values('||var_id||','''||var_sal||''')';
  --两个连续''识别为字符串中的一个';
  dbms_output.put_line(sql_str);
  --execute immediate sql_str;
end;
```

**使用占位符解决单引号拼接问题**

```mysql
declare 
  var_id number:=456;
  var_sal NUMBER(8,2):=200;
  sql_str varchar(1000);
begin
  sql_str:='insert into test009 values(:b0,:b1)';--有冒号
  dbms_output.put_line(sql_str);
  execute immediate sql_str using var_id,var_sal;--变量赋值
end;
select * from test009;
```

#### select

```mysql
declare 
  var_id number:=456;
  var_sal NUMBER(8,2):=200;
  sql_str varchar(1000);
begin
  sql_str:='select sal from test009 where var_id=:b0';--有冒号
  dbms_output.put_line(sql_str);
  execute immediate sql_str into var_sal using var_id;--变量赋值
  dbms_output.put_line(var_sal);
end;
select * from test009;
```

