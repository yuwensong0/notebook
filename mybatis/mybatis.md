#### mybatis对于查询结果对于实体类的赋值

说明，一般实体类中的字段现在用的都是对象，即使是基本类型，用的也是对应的包装类，这就导致书写sql后如果没有对查询出来的字段做ifnull处理，那么会导致数据库中字段值为null时，默认的实体类的字段也是null，导致后续需要对null进行判断，否则会发生空指针异常。

通过mybatis源码分析，mybatis对于将查询结果赋值给实体类时，是否会将null进行赋值，受mybatis的配置影响，在默认实现的结果集封装代码中：```org.apache.ibatis.executor.resultset.DefaultResultSetHandler```

有结果集的映射（有resultMap）：

```java
if (value != null || (configuration.isCallSettersOnNulls() && !metaObject.getSetterType(property).isPrimitive())) {  
    // gcode issue #377, call setter on nulls (value is not 'found') 
    metaObject.setValue(property, value);
}
```

没结果集的映射（有resultType）:

```java
if (value != null || (configuration.isCallSettersOnNulls() && !mapping.primitive)) {  
	// gcode issue #377, call setter on nulls (value is not 'found')  
    metaObject.setValue(mapping.property, value);
}
```

如果```isCallSetterOnNulls```配置为true且setter方法的入参为对象，非基本类型，那么会将null赋值给该字段。

