# WindChill 查询

```java
ClassAttribute 
如查询WTPart的partType属性 
ClassAttribute classAttr = new ClassAttribute(WTPart. class, "partType ");
```

```
SQLFunction 
这个类代表SQL的函数表达式
SQLFunction currentDate = SQLFunction .newSQLFunction(SQLFunction.SYSDATE);
```

```java
TableColumn 
代表表的字段表达式，如wtpart表中的字段“IDA2A2”
TableColumn dummyColumn = new TableColumn("wtpart", "IDA2A2");
```

