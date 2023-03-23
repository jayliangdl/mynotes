# Java笔记

### 问：已有一个第三方JAR包，如果想要覆盖它里边的某个类的实现，可以怎么做？

答： 在项目里引用这个第三方包，并在本项目内新建一个类，package与类名都与想被覆盖的class相同，逻辑写自己的。

### 问：Spring中有一个Bean要在某种情况下才被加载（注册给spring管理），例如在测试环境加载，在生产环境则不被加载。需要怎么做？

### 问：Spring中有哪些常用的ApplicationContext？

- ClassPathXmlApplicationContext：可以通过在classpath中查找XML文件来装配bean。

- FileSystemXmlApplicationContext：可以通过指定XML文件的路径来装配bean。

- XmlWebApplicationContext：可以在Web应用程序中使用XML文件配置应用程序上下文。

- WebApplicationContext：是XmlWebApplicationContext的子类，它可以加载多个XML文件，并在Web应用程序中使用注解配置应用程序上下文。

- AnnotationConfigWebApplicationContext：可以在Web应用程序中使用注解配置应用程序上下文。

### 问：如果我只是在项目的某个类中添加了@component注解，使用AnnotationConfigApplicationContext启动，好像并没有将此类注册到Spring的bean容器中。