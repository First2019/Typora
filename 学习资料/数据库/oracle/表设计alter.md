```
create table newtab as select orderid,oredrdate from orders;-- 创建新表并放入数据
create table newtab as select orderid,oredrdate from orders where 1=2;-- 复制表结构
select * from user_constraints where table_name='ORDERS';-- 查看表约束,表名必须大写
alter table seal_use_apply drop constraint seal_use_apply_uniq;-- 删除约束
```

### 查询表约束

```
SELECT A.TABLE_NAME,A.CONSTRAINT_NAME,A.COLUMN_NAME,B.CONSTRAINT_TYPE,b.search_condition
    FROM USER_CONS_COLUMNS A, USER_CONSTRAINTS B
         WHERE A.CONSTRAINT_NAME = B.CONSTRAINT_NAME and
         B.table_name='TT';
```

### 批量插入

insert into tablename(lie1,lie2) select lie1,lie2 from tablename;
create table tt(
  cid number,
  cname varchar2(20) constraint name not null,--name为指定约束名称
  orderid number constraint fk_id references orders(orderid)--外键类型一致
);

```
create table ts(
  "uid" number  -- **双引号使用关键字**
);
```

desc ts;
desc tt;

desc ts;
desc tt;

### 已有字段添加非空约束

alter table bkeep5 modify  department_id not null;
alter table tt add sal number not null;
alter table tt add ts number not null unique check(ts>10);
alter table tt drop column ts;
删表需要权限：grant drop table tou1;

### 约束类型

**not null**,**unique**,**primary key**,**foreign key**,**check**;

### ALTER TABLE语句

用于修改已经存在的表的设计。
语法：ALTER TABLE table ADD COLUMN field type[(size)] [NOT NULL] [CONSTRAINT index]

```
ALTER TABLE orders ADD CONSTRAINT multifieldindex;--添加约束。
ALTER TABLE orders DROP COLUMN field;
ALTER TABLE orders DROP CONSTRAINT indexname;
```

说明：table参数用于指定要修改的表的名称。
ADD CONSTRAINT 为SQL的保留字，使用它将向表中添加索引及约束。
DROP COLUMN 为SQL的保留字，使用它将向表中删除字段。
DROP CONSTRAINT 为SQL的保留字，使用它将向表中删除索引。

ALTER TABLE 表名 ADD 列名 数据类型; 
ALTER TABLE 表名 MODIFY 列名 数据类型; --modify修饰，修改，添加修改列数据类型
ALTER TABLE 表名 RENAME COLUMN 当前列名 TO 新列名; --重命名
ALTER TABLE 表名 DROP COLUMN 列名; -- column 列，纵队
ALTER TABLE 当前表名 RENAME TO (新表名);
Alter Table Employ Add (weight Number(38,0));--增加一个列：
**修改一个列的数据类型**(一般限于修改长度，修改为一个不同类型时有诸多限制)
Alter Table Employ Modify weight Number(13,2) not null;
Alter Table Emp Rename Cloumn weight To weight_new;--给列改名：
ALTER TABLE emp DROP COLUMN weight_new;--删除一个列：
ALTER TABLE bouns RENAME TO bonus_new;--将一个表改名：
alter USER user IDENTIFIEDBY 'newpassword' REPLACE 'oldpassword';--改密码