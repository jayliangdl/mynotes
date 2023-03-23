# Java笔记

### **_问：已有一个第三方JAR包，如果想要覆盖它里边的某个类的实现，可以怎么做？_**

答： 在项目里引用这个第三方包，并在本项目内新建一个类，package与类名都与想被覆盖的class相同，逻辑写自己的。

### **_问：Spring中有一个Bean要在某种情况下才被加载（注册给spring管理），例如在测试环境加载，在生产环境则不被加载。需要怎么做？_**

可以使用Spring中的@Conditional注解来实现有条件地加载Bean，@Conditional注解允许你定义一个或多个条件，只有满足条件时才会加载Bean。以下是一个简单的示例，展示如何根据当前环境来有条件地加载Bean：

```
@Component 
@Conditional(TestCondition.class) 
public class MyTestBean { 
    // ... 
}
```

在上面的示例中，@Conditional注解引用了TestCondition.class，它是一个自定义的Condition接口的实现类，用于判断当前环境是否为测试环境。如果当前环境是测试环境，则MyTestBean将被加载并注册到Spring的bean容器中。

TestCondition类的实现示例如下：

```
public class TestCondition implements Condition { 
    @Override 
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) { 
        String env = context.getEnvironment().getProperty("spring.profiles.active"); 
        return env != null && env.equals("test"); 
    } 
}
```

TestCondition类实现了Spring的Condition接口，它重写了matches()方法，该方法用于判断当前环境是否为测试环境。在上面的示例中，它通过context.getEnvironment().getProperty("spring.profiles.active")获取当前环境的名称，并判断是否为"test"。如果是测试环境，则返回true，否则返回false。

通过这种方式，你可以根据当前环境来有条件地加载Bean，可以定义多个Condition实现类，并在@Conditional注解中引用它们来实现更复杂的条件判断。

### **_问：Spring中有哪些常用的ApplicationContext？_**

- ClassPathXmlApplicationContext：可以通过在classpath中查找XML文件来装配bean。

- FileSystemXmlApplicationContext：可以通过指定XML文件的路径来装配bean。

- XmlWebApplicationContext：可以在Web应用程序中使用XML文件配置应用程序上下文。

- WebApplicationContext：是XmlWebApplicationContext的子类，它可以加载多个XML文件，并在Web应用程序中使用注解配置应用程序上下文。

- AnnotationConfigWebApplicationContext：可以在Web应用程序中使用注解配置应用程序上下文。

### **_问：如果我只是在项目的某个类中添加了@component注解，使用AnnotationConfigApplicationContext启动，好像并没有将此类注册到Spring的bean容器中。_**

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

这样，即使该类所在的包没有被扫描到，也可以将它注册到Spring的bean容器中。

### **_问：使用哪个ApplicationContext不需要指定@ComponentScan，就可以扫描到这个启动类下面的所有加了@Component的类呢？_**

在Spring Boot应用程序中，可以使用@SpringBootApplication注解来标记启动类，它包含了三个常用的注解：@Configuration、@EnableAutoConfiguration和@ComponentScan，其中@ComponentScan会自动扫描@SpringBootApplication所在的包及其子包中的所有组件。

因此，在使用@SpringBootApplication注解标记启动类的情况下，不需要再显式指定@ComponentScan注解或者ApplicationContext的实现类，就可以扫描到启动类所在包下的所有加了@Component注解的类，并将它们自动注册到Spring的bean容器中。

例如：

```
@SpringBootApplication 
public class MyApp { 
    public static void main(String[] args) {
         SpringApplication.run(MyApp.class, args);                            
    } 
}
```

这样就可以自动扫描MyApp类所在包及其子包中的所有组件，包括加了@Component注解的类，并将它们注册到Spring的bean容器中。

### **_问：除了SpringBootApplication，还有其他ApplicationContext不需要指定@ComponentScan，就可以扫描到这个启动类下面的所有加了@Component的类吗？_**

除了SpringBootApplication注解外，其他常用的ApplicationContext实现类都需要显式指定@Configuration和@ComponentScan注解，才能够自动扫描指定包及其子包下的所有组件。而SpringBootApplication注解包含了@Configuration、@EnableAutoConfiguration和@ComponentScan注解，因此可以省略@Configuration和@ComponentScan注解的配置。