#### 1. `yyyy-MM-dd HH:mm:ss`中的`HH`是24小时显示，`hh`是12小时显示

#### 2. 日期格式化与转换

- ##### 前端数据发给后端

```java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```

`@DateTimeFormat`注解用于前端转字符串日期给后端，后端转换为Date类型，但是只有在参数转换过程中不涉及反序列化时生效，比如Get请求

```java
@GetMapping("/date")
public Date date(@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") Date date {
    return date;
}
```

```java
@GetMapping("/echo")
public Person echo(Person person) {
    return person;
}
// Person属性上添加注解
@JsonFormat(pattern = "yyyy=MM-dd HH:mm:ss", timezone = "GMT+8")
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```

但是如果涉及到反序列化，比如Post请求获取请求体中的参数，会利用`@JsonFormat`的注解信息进行解析

```java
@PostMapping("/echo")
public Person echo(@RequestBody Person person, 
                  @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") Date date) {
    System.out.println(date);
    return person;
}
```

`Person`中的`Date`类型利用`@JsonFormat`注解信息解析，第二个Date参数则是获取`url`的参数利用`@DateTimeFormat`的注解信息进行解析

- ##### 后端数据发给前端

当请求返回给前端时，涉及序列化，则是利用`@JsonFormat`的注解信息进行日期格式化

#### 3. 常见的`jackson`注解

```java
@JsonIgnoreProperties(value = {"account"}, allowSetters = true, allowGetters = true, ignoreUnknown = true)
public class Person {
    @JsonIgnore
    private Integer age;
    @JsonFormat(pattern = "yyyy=MM-dd HH:mm:ss", timezone = "GMT+8")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private Date birthday;
}
```



