#### 1. 在idea中启动tomcat的web项目，出现

```shell
07-Mar-2020 13:45:54.748 淇℃伅 [main] 
07-Mar-2020 13:45:54.748 淇℃伅 [main] 
07-Mar-2020 13:45:54.752 淇℃伅 [main] 
```

中文乱码，这是因为在apache-tomcat-8.5.47/conf/logging.properties这个配置文件下的设置的日志默认编码是UTF-8

```shell
1catalina.org.apache.juli.AsyncFileHandler.level = FINE
1catalina.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
1catalina.org.apache.juli.AsyncFileHandler.prefix = catalina.
1catalina.org.apache.juli.AsyncFileHandler.encoding = UTF-8
2localhost.org.apache.juli.AsyncFileHandler.level = FINE
2localhost.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
2localhost.org.apache.juli.AsyncFileHandler.prefix = localhost.
2localhost.org.apache.juli.AsyncFileHandler.encoding = UTF-8

3manager.org.apache.juli.AsyncFileHandler.level = FINE
3manager.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
3manager.org.apache.juli.AsyncFileHandler.prefix = manager.
3manager.org.apache.juli.AsyncFileHandler.encoding = UTF-8

4host-manager.org.apache.juli.AsyncFileHandler.level = FINE
4host-manager.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
4host-manager.org.apache.juli.AsyncFileHandler.prefix = host-manager.
4host-manager.org.apache.juli.AsyncFileHandler.encoding = UTF-8

java.util.logging.ConsoleHandler.level = FINE
java.util.logging.ConsoleHandler.formatter = org.apache.juli.OneLineFormatter
java.util.logging.ConsoleHandler.encoding = UTF-8
```

在idea的控制台窗口在windows下是GKB编码的，因此中文出现乱码。解决办法是将上述的配置文件由UTF-8改为GBK或者将编码设置的一项注释掉，注释到后发现利用的是GBK编码，应该是读取了操作系统的编码方式。

如果部署到Linux服务器的话，编码就用默认的，Linux的控制台输出可以设置为UTF-8。

#### 2. catalina.home和catalina.base

在idea中启动的tomcat，配置文件读取到的catalina.base发现是

```shell
Using CATALINA_BASE:   "C:\Users\yws\.IntelliJIdea2019.2\system\tomcat\Unnamed_spring_security_management"
Using CATALINA_HOME:   "C:\app\apache-tomcat-8.5.47"
Using CATALINA_TMPDIR: "C:\app\apache-tomcat-8.5.47\temp"
Using JRE_HOME:        "C:\Program Files\Java\jdk1.8.0_231"
Using CLASSPATH:       "C:\app\apache-tomcat-8.5.47\bin\bootstrap.jar;C:\app\apache-tomcat-8.5.47\bin\tomcat-juli.jar"
```

catalina.home是/bin目录的父目录，而catalina.base这是idea自己生成的一层目录

而在linux下启动服务，如果只有一份tomcat实例，则catalina.home和catalina.base指向相同目录都是/bin目录的父目录

