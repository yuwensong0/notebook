#### 小数点前后无法显示

```xml
to_char(d.CP_PRICE,'FM9999999999999990.00')
针对NUMBER(18,2),18位精度，两位小数点
javaType为String， jdbcType为DECIMAL或NUMERIC
仅需要在查询是转换即可，插入新增维持原样即可
```

