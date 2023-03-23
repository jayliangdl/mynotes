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

使用AnnotationConfigApplicationContext启动时，默认情况下它会扫描指定的配置类及其子包中的所有组件，并将它们注册到Spring的bean容器中。但是，如果你只是在某个类上添加了@Component注解，可能是因为这个类所在的包没有被扫描到。

你可以通过以下方式来解决这个问题：

1. 确认你的配置类中是否有@ComponentScan注解，如果有，请检查该注解中是否指定了包名或者扫描路径，如果没有指定，则默认扫描当前类所在的包及其子包。

2. 如果你没有在配置类中添加@ComponentScan注解，或者指定的包名不正确，可以在@Configuration注解中指定扫描路径，例如：

```
@Configuration 
@ComponentScan(basePackages = {"com.example"}) 
public class AppConfig { 、
// ... 
}
```

3. 如果你已经将@ComponentScan注解添加到了配置类中，但是仍然不能将组件注册到Spring的bean容器中，可以尝试在启动AnnotationConfigApplicationContext时，手动将该类添加到bean容器中，例如：

```
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(); context.register(MyComponent.class); 
context.refresh();
```

\`JAVA\`

这样，即使该类所在的包没有被扫描到，也可以将它注册到Spring的bean容器中。