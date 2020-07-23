#### 1. get请求，参数仅存在查询参数中

利用`postman`发送`get`请求时如果设置有请求体：

 不管是`application/x-www-form-urlencoded` 还是`application/json`，通过抓包可以发现其实请求体中是有数据的，但是tomcat服务器不会对此解析，后台看到的`ostData=null`，所以在这里面设置的键值对不会生，`controller`中用基本类型接受或者用实体类属性接受查询参数中的值，可以用`@RequestParam`注解修饰进行更多特性的控制

#### 2. post请求 `Content-Type=application/x-www-form-urlencoded`

参数在查询参数中会被`org.apache.catalina.connector.Request#parseParameters()`这个方法会先解析查询参数然后判断`contentType`是否是`application/x-www-form-urlencoded`

```java
if (!("application/x-www-form-urlencoded".equals(contentType))) {
    success = true;
    return;
}
```

如果是，这接下的流程对`postData`的键值对进行解析，因此如果请求参数中跟请求体中有相同的健，这时对应的值会是数组，由于请求体中的数据后解析，因此排在后面，所以如果只取一个值，会先用查询参数中的值，在`controller`中参数绑定的写法跟第一种情况一样

#### 3. post请求  `Content-Type=application/json` 

查询参数同样会被`tomcat`解析，但是如果`controller`中的参数被`@RequestBody`修饰，值绑定时候会涉及反序列化，`@RequestBody`修饰的值绑定的来源都是请求体中的`json`数据，如果不被注解修饰或者用`@RequestParam`修饰，则值来源于查询参数



#### 4.`@RequestParam`注解使用注意事项

只能修饰基本类型，如果想用一个实体类封装过多的参数，并且参数的来源的查询参数，则不能在实体类前用`@RequestParam`修饰，有个默认修饰的参数必须有值，可以设置`required=false`可以为空值

#### 5.参数解析

get请求：

http://localhost:6050/qualify/selectByPrimaryKey?pkId和http://localhost:6050/qualify/selectByPrimaryKey?pkId=

当有pkId这个键存在不管有没有 '=' 号，后台获取的参数都是pkId=""，空字符串，而如果没有pkId这个键的话，后台获取的参数是pkId=null

如果用post请求，@RequestBody表示从请求体找值，如果后台是个实体类接受，如果传{}，那么后台所有的属性都是null，如果不传请求体，即请求体为空，那么会报错，如果json和后台入参不匹配，也会报错，比如传个[ ]。

