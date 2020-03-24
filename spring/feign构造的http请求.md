#### 接口的不同写法导致feign构造的不同http请求

```java
@RestController
@FeignClient(name = "service-pruduct")
public interface ConsumerController {
    // 第一种情况
    @RequestMapping("withoutRequestParam")
    String hello1(String name);
	// 第二种情况
    @RequestMapping("withRequestParam")
    String hello11(@RequestParam("name") String name);
	// 第三种情况
    @RequestMapping("withRequestBody")
    String hello112(@RequestBody Person person);
}
```

##### 第一种情况，接收参数没有用@RequestParam修饰

由于没有指定请求类型，所以post和get请求都可以接受。

- 当请求时get请求时：

http://localhost:8762/withoutRequestParam?name=xiaoming 

通过抓包发现feign构造的请求是post请求，Content-Type是text/plain;charset=UTF-8，请求体是xiaoming，不是以键值对形式发送。

- 当请求是post时：

http://localhost:8762/withoutRequestParam?name=xiaoming 

参数可以放在url后面，或者是设置Content-Type为 application/x-www-form-urlencoded ，在请求体中参数用键值对形式发送，通过抓包发现feign构造的请求体跟上面的get请求一样，请求是post请求，Content-Type是text/plain;charset=UTF-8，请求体是xiaoming，不是以键值对形式发送。

- 原因：

因为没有用@RequestParam修饰，feign默认行为会是当作参数被@RequestBody修饰一样，为什么Content-Type会是text/plain;charset=UTF-8，这是因为String name这种参数单一参数在接收参数被@RequestBody修饰时候，就是设置成这样的，如果有多个参数会怎么样，

```java
@RequestMapping(value = "withoutRequestParam", method = RequestMethod.GET)
String hello1(String name, Integer age);
```

springMVC不会报错，但是feign构造会报错：Method has too many Body parameters。这是因为@RequestBody只能修饰一个参数，虽然说feign在构造时候似乎会加上@RequestBody修饰，但是对于springMVC来说，进行参数绑定时并不受feign的影响，参数并不会从body中去取，如果将参数改为一个对象接受：

```java
@RequestMapping(value = "withoutRequestParam", method = RequestMethod.GET)
String hello1(Person person);
```

此时通过抓包发现feign构造的请求将Content-Type被设置为了application/json；同时请求体中也是person的json字符串，

##### 第二种情况，接收参数用@RequestParam修饰

由于没有指定请求类型，所以post和get请求都可以接受

- 当请求时get请求时：

 http://localhost:8762/withRequestParam?name=xiaoming 

通过抓包发现feign构造的请求时get请求，request.uri是 /withRequestParam?name=xiaoming，参数直接跟在url后面

- 当请求是post时：

http://localhost:8762/withRequestParam?name=xiaoming 

通过抓包发现feign构造的请求时get请求，request.uri是 /withRequestParam?name=xiaoming，参数直接跟在url后面

- 可以发现由于没有指定请求方法，feign构造的请求都是get请求。

- 如果明确指定了请求方法：

```java
@RequestMapping(value = "withRequestParam",method = RequestMethod.POST)
String hello115(@RequestParam("name") String name);
@RequestMapping(value = "withRequestParam", method = RequestMethod.GET)
String hello11(@RequestParam("name") String name);
```

- get请求时feign构造的请求跟上面的一样，也是get请求

- post请求时feign构造的请求是post请求，参数会跟在url后面，同时没有Content-Type这一请求头。

##### 第三种情况，接收参数用@RequestBody修饰，

- 由于@RequestBody修饰，请求即使没明确指定，也必须是post，且Content-Type为application/json，否则springMVC将报错，由于也只有这一种情况，feign的构造的请求也没有歧义，就是构造请求为post，Content-Type为application/json，请求体为接受到的参数对象的json字符串。