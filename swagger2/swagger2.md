#### 项目引入

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>      
    <groupId>io.springfox</groupId>      
    <artifactId>springfox-swagger-ui</artifactId>      
    <version>2.9.2</version>
</dependency>
```

```java
@Configurationpublic
class Swagger2Config {    
    private ApiInfo apiInfo() {        
        return new ApiInfoBuilder()                
            .title("api文档")                
            .description("测试Swagger2的api文档")            
            .termsOfServiceUrl("").version("1.0")                
            .build();    
    }    
    @Bean    
    public Docket createRestApi() {        
        return new Docket(DocumentationType.SWAGGER_2)                
            .apiInfo(apiInfo())                
            .select()                
            // 修改成自己项目下存放controller类的包
            .apis(RequestHandlerSelectors.basePackage("com.example.springtest")) 
            .paths(PathSelectors.any())                
            .build();    
    }
}
```

#### 不指定请求方法

如果@RequestMapping不指定请求方法，swagger2将生成所有的请求接口包括：get, head, post, put, delete, option, patch

#### **Response content type** 

**Response content type** 页面这个值可以通过produces 来指定

```@PostMapping(value = "/person", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)```

#### example属性

在@ApiModelProperty(value = "生日", example = "2020-02-20 22:09:09")中的example可以指定swagger-ui中的默认值显示，example的类型为string， 如果该属性的值是数字类型，包括BigDecimal，会尝试类型转换，成功了就会显示数字，即在json中没有引号，如果无法转换，比如"abc"，就会显示"abc"，有引号。

#### 日期属性的处理

```javascript
@ApiModelProperty(value = "生日", example = "2020-02-20 22:09:09")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```

#### 忽略

 @ApiIgnore 忽略已有路径的请求

```java
@GetMapping("/hello")
@ApiIgnore
public void hello() {
    
}
```

@JsonIgnore 多重影响，ui页面不显示，同时json序列化和反序列化都会受影响

```java
@JsonIgnore
private Dog cat;
```

@ApiModelProperty(hidden = true) 只是ui页面不显示，影响范围只在swagger2内部

```java
@ApiModelProperty(hidden = true)
private Dog cat;
```

#### 文档生成界面

用实体类接收参数，swagger2会把实体类的所有属性列出，当作接口参数，这导致如果用一个全量的实体类去当作参数接收的载体，程序在功能上能实现正常运转，但是swagger自动生成的接口文档所有的接口参数都是这个类的属性，而有些属性一些接口并用不到，这会导致使用接口的人的疑惑。即使有一些方法可以忽略显示实体类的一些属性，但是由于所有接口都依赖这个实体类，导致这个实体类的改动的变化所有都会影响，无法为单个接口定制。目前看来一个简单的方法就是不同的接口使用定制的实体类，这回让接口文档生成变得好看，但是会导致类爆炸，同时实体类间的转换也变多。

