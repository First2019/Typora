### MyBatis 通过包含的jdbcType类型

```
BIT         FLOAT      CHAR           TIMESTAMP       OTHER       UNDEFINED

TINYINT     REAL       VARCHAR        BINARY          BLOB        NVARCHAR

SMALLINT    DOUBLE     LONGVARCHAR    VARBINARY       CLOB        NCHAR

INTEGER     NUMERIC    DATE           LONGVARBINARY   BOOLEAN     NCLOB

BIGINT      DECIMAL    TIME           NULL            CURSOR
```

### 传参

使用arg0,param1获得参数是一直有效的，xml不写传参类型

```mysql
<if test="flag != null and 'true'.toString() == flag.toString()">
​  flage=#{flag, jdbcType=BOOLEAN}
</if>
```

```
foreach元素的属性主要有item，index，collection，open，separator，close。
- item：集合中元素迭代时的别名，该参数为必选。
- index：在list和数组中,index是元素的序号，在map中，index是元素的key，该参数可选
- open：foreach代码的开始符号，一般是(和close=")"合用。常用在in(),values()时。该参数可选
- separator：元素之间的分隔符，例如在in()的时候，separator=","会自动在元素中间用“,“隔开，避免手动输入逗号导致sql错误，如in(1,2,)这样。该参数可选。
- close: foreach代码的关闭符号，一般是)和open="("合用。常用在in(),values()时。该参数可选。
- collection: 要做foreach的对象，作为入参时，List对象默认用"list"代替作为键，数组对象有"array"代替作为键，Map对象没有默认的键。当然在作为入参时可以使用@Param("keyName")来设置键，设置keyName后，list,array将会失效。 除了入参这种情况外，还有一种作为参数对象的某个字段的时候。举个例子：如果User有属性List ids。入参是User对象，那么这个collection = "ids".如果User有属性Ids ids;其中Ids是个对象，Ids有个属性List id;入参是User对象，那么collection = "ids.id"
```

未查询到数据行时返回null