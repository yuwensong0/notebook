#### 生命周期

##### 四种方法可以实现

1. 通过指定```init-method```和```destroy-method```

   ```java
   @Bean(initMethod = "init", destroyMethod = "destroy")
   public Person person() {
       return new Person();
   }
   ```

2. 通过注解``` @PostConstruct```和```@PreDestroy```

   ```java
   public class Person {
       public Person(){
           System.out.println("person construction");
       }
       @PostConstruct
       public void init() {
           System.out.println("person init");
       }
       @PreDestroy
       public void destroy() {
           System.out.println("person destroy");
       }
   }
   ```

3. 通过实现接口```InitializingBean```和```DisposableBean```

   ```java
   public class Person implements InitializingBean, DisposableBean {
       public Person(){
           System.out.println("person construction");
       }
   
       @Override
       public void destroy() throws Exception {
           
       }
   
       @Override
       public void afterPropertiesSet() throws Exception {
   
       }
   }
   ```

4. 实现自定义的```BeanPostProcessor```并加入容器中

   ```postProcessBeforeInitialization```方法将在```init-method```和```@PostConstruct```等这些初始化方法之前，构造方法执行之后执行

   ```postProcessAfterInitialization```方法将在```init-method```和```@PostConstruct```等这些初始化方法之后执行

   ```java
   @Component
   public class MyBeanPostProcessor implements BeanPostProcessor {
       @Override
       public Object postProcessBeforeInitialization(Object bean, String 					beanName) throws BeansException {
           System.out.println("MyBeanPostProcessor" + beanName);
           return bean;
       }
   
       @Override
       public Object postProcessAfterInitialization(Object bean, String 					beanName) throws BeansException {
           System.out.println("MyBeanPostProcessor" + beanName);
           return bean;
       }
   }
   ```

   #### 常用的注解

   ```java
   Condition@ComponentScans({
           @ComponentScan(value = "com.example", includeFilters = {
                   @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Controller.class})
           }, useDefaultFilters = false),
           @ComponentScan(value = "com.example", excludeFilters = {
                   @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Service.class})
           })
   })
   @Configuration
   @PropertySource(value = "classpath:/app.properties")
   @Scope
   @Lazy
   @Conditional(value = MyCondition.class)
   @Import({Person.class})
   @Profile("dev") // -Dspring.profiles.active=dev
   public class MainConfig {
   
       public Person person() {
           return new Person();
       }
   }
   ```

   Spring 声明式事务需要的三个条件：

   1. 标注哪些方法需要事务支持，利用```@Transactional```注解

      ```java
      @Service
      public class UserService {
          @Autowired
          private UserDao userDao;
          @Autowired
          private BadService badService;
          @Transactional
          public void insert() {
              userDao.insert();
              System.out.println("插入完成。。。");
              badService.bad();
          }
      }
      ```
   
   2. 开启事务支持功能，利用```@EnableTransactionManagement```
   
      ```java
      @EnableTransactionManagement
      public class TxConfig {
      
      }
      ```
   
   3. 添加事务管理器到容器中
   
      ```java
      @Configuration
      @ComponentScan("com.example.tx")
      @EnableTransactionManagement
      public class TxConfig {
          @Bean
          public DataSource dataSource() throws PropertyVetoException {
              ComboPooledDataSource comboPooledDataSource = new 							ComboPooledDataSource();
              comboPooledDataSource.setUser("root");
              comboPooledDataSource.setPassword("root");
              comboPooledDataSource.setJdbcUrl("jdbc:mysql://localhost/test");
              comboPooledDataSource.setDriverClass("com.mysql.jdbc.Driver");
              return comboPooledDataSource;
          }
          @Bean
          public JdbcTemplate jdbcTemplate(DataSource dataSource) {
              JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
              return jdbcTemplate;
          }
          @Bean
          public PlatformTransactionManager 												platformTransactionManager(DataSource dataSource) {
              PlatformTransactionManager platformTransactionManager = new 				DataSourceTransactionManager(dataSource);
              return platformTransactionManager;
          }
      
      }
      ```
   
   
   
   ```dispatcherServlet```的路径拦截：
   
   / 拦截包括静态资源但是不包括```jsp```文件
   
   /* 拦截所有，包括`jsp`文件

#### 常用注解

`PropertySource("classpath:xxx.properties")`非application.properties文件属性文件

`ConfigurationProperties(prefix="xxx")`yaml前缀绑定

`ImportResource(locations={"xx.xml, aaa.xml"})`配置文件

