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