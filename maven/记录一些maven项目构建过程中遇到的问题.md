#### 1. 项目的pom.xml文件有依赖其他工程，但是在compile时却提示找不到在其他工程中的类

因为用的是Spring Initializer创建的spring boot工程，创建过程中在pom.xml文件中会引入：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

具体在执行maven生命周期函数过程中发生了什么不知道，但是一个解决办法是将所有项目中的pom.xml文件的这个插件删除掉，重新运行goal等函数。