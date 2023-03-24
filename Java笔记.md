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

### **_问：除了Conditional这种方式外，还有其他方式某个Bean要在某种情况下才被加载（注册给spring管理）吗？_**

除了使用@Conditional注解外，还可以使用Spring的Profile功能来有条件地加载Bean。Profile是一种将Bean注册到特定环境中的机制，它可以让你定义一组Bean，这些Bean只有在特定环境下才会被加载并注册到Spring的bean容器中。

要使用Profile功能，需要在Bean上使用@Profile注解来指定所属的环境，例如：

```
@Component 
@Profile("test") 
public class MyTestBean { 
    // ... 
}
```

在上面的示例中，@Profile("test")表示这个Bean只在test环境下才会被注册到Spring的bean容器中。如果你在其他环境下启动应用程序，这个Bean将不会被加载。

在启动应用程序时，可以通过设置spring.profiles.active属性来指定要加载的环境，例如：

```
java -jar myapp.jar --spring.profiles.active=test
```

这将启动应用程序，并将环境设置为test，这样所有带有@Profile("test")注解的Bean将会被加载并注册到Spring的bean容器中。

需要注意的是，如果不指定spring.profiles.active属性，则默认情况下所有不带@Profile注解的Bean都会被加载并注册到Spring的bean容器中。因此，在使用Profile功能时，要确保你只将必须的Bean标记为特定环境的Bean，以避免不必要的资源浪费。

### **_问：如果使用profile的方式，是否每个Bean都要指定它的profile，如果大部分类都是默认在什么情况都需要加载，只是少部分类是需要指定，那使用profile方案时候都需要将所有类加上profile吗？_**

使用Profile的方式可以在Spring中为Bean定义提供基于条件的选择，而不必修改Bean本身。因此，您只需要将特定Bean的Profile设置为需要的值，而不必将所有Bean都设置为Profile。

如果大部分类都是默认情况下需要加载，只有一小部分需要根据Profile来决定是否加载，那么您可以在配置文件中指定默认Profile，然后对于需要根据Profile来决定是否加载的Bean，您可以在其对应的@Configuration或@Component注解上指定特定的Profile，如下所示：

```
@Configuration
@Profile("dev") // 只有在dev环境下才加载这个配置类
public class MyConfig {
    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```

在上面的示例中，MyConfig类只有在"dev"环境下才会被加载，并且仅当它被加载时，MyBean才会被注册到Spring的bean容器中。

如果您没有在配置文件中指定Profile，则默认Profile为"default"，这意味着没有指定Profile的Bean将会在默认情况下被加载。

因此，您只需要在需要根据Profile来决定是否加载的Bean上指定Profile，而不必将所有Bean都设置为Profile。

### **_除了Conditional和Profile这两种方式外，还有其他方式某个Bean要在某种情况下才被加载（注册给spring管理）吗？_**

除了使用@Conditional注解和Profile功能外，还可以使用Spring的BeanFactoryPostProcessor接口来有条件地加载Bean。

BeanFactoryPostProcessor是Spring提供的一个接口，用于在Bean加载之前修改或添加Bean定义。通过实现这个接口，你可以在Bean加载之前判断某些条件，然后决定是否加载某些Bean。

以下是一个示例，展示如何在BeanFactoryPostProcessor中根据特定条件来加载Bean：

```
@Component 
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor { 
@Override 
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException { 
        String env = System.getProperty("myapp.env"); 
        if (env != null && env.equals("test")) { 
            BeanDefinition beanDefinition = BeanDefinitionBuilder.genericBeanDefinition(MyTestBean.class).getBeanDefinition(); 
            beanFactory.registerBeanDefinition("myTestBean", beanDefinition); 
        } 
    } 
}
```

在上面的示例中，MyBeanFactoryPostProcessor实现了BeanFactoryPostProcessor接口，并在postProcessBeanFactory()方法中判断当前环境是否为测试环境。如果是测试环境，则通过BeanDefinitionBuilder来构造MyTestBean的Bean定义，并将它注册到Spring的bean容器中。

要使用这个方式，需要在应用程序中手动添加MyBeanFactoryPostProcessor组件，并设置特定的系统属性，例如：

```
@SpringBootApplication 
public class MyApp { 
   public static void main(String[] args) {
     System.setProperty("myapp.env", "test");
     SpringApplication.run(MyApp.class, args); 
   } 
}
```

在上面的示例中，我们手动设置了myapp.env属性的值为"test"，这样在应用程序启动时，MyBeanFactoryPostProcessor将会被加载，并根据myapp.env属性的值来判断是否加载MyTestBean。

需要注意的是，这种方式需要手动添加组件，并且对Bean定义的处理是在Bean加载之前进行的，因此可能会对应用程序的性能产生一定的影响。因此，建议在需要对Bean进行复杂的处理或需要在Bean加载之前进行一些初始化操作时使用这种方式。

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

### **_问：在spring中，怎么把一个类交给Spring管理？有哪几种方式？_**

在spring中，将一个类交给Spring管理有以下几种方式：

1\. 通过XML配置文件，使用<bean>元素配置bean。

例如：

```
 <bean id="demoService" class="com.demo.service.DemoServiceImpl"> <property name="demoDao" ref="demoDao"/> </bean>
```

2\. 通过Java注解配置bean，使用@Component、@Service、@Repository、@Controller等注解配置bean。

```
@Service
public class DemoServiceImpl implements DemoService {
    // ...
}
```

3\. 通过Java API配置bean，使用BeanDefinition、BeanDefinitionRegistry等API将bean注册到IoC容器。

```
BeanDefinitionBuilder builder = BeanDefinitionBuilder.genericBeanDefinition(DemoServiceImpl.class);
builder.addPropertyReference("demoDao", "demoDao");
BeanDefinition beanDefinition = builder.getBeanDefinition();
BeanDefinitionRegistry registry = new DefaultListableBeanFactory();
registry.registerBeanDefinition("demoService", beanDefinition);
```

### 如果某一些类的初始化方法是带参数的，且参数是需要有一些业务逻辑计算才能定下来的，我使用编程的方式（非xml或非注解的方式），我应该怎么实现让这些类可以初始化并交给Spring管理？

在这种情况下，可以使用Spring的BeanFactoryPostProcessor回调机制来实现。BeanFactoryPostProcessor提供了一种方法，用来在BeanFactory中定义bean之前，为bean进行拦截，并在bean定义被加载之前进行处理。 可以通过实现BeanFactoryPostProcessor接口，重写postProcessBeanFactory()方法，定义一个bean的定义，添加bean的构造函数参数，然后将bean的定义注册到IoC容器中。例如：

```


public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        // 定义一个bean的定义
        BeanDefinitionBuilder builder = BeanDefinitionBuilder.genericBeanDefinition(DemoServiceImpl.class);
        // 添加bean的构造函数参数
        builder.addConstructorArgValue("参数1");
        builder.addConstructorArgValue("参数2");
        // 将bean的定义注册到IoC容器中
        beanFactory.registerBeanDefinition("demoService", builder.getBeanDefinition());
    }
}
```

### **_哪些注解可以让类交给Spring管理？_**

@Component、@Service、@Repository、@Controller等注解配置bean。

### **_哪些注解可以从Spring容器中获取到一个Bean的实例？_**

@Autowired  @Resource

哪些方法可以从Spring容器中获取到一个Bean的实例？

- 注解方式（@Autowired @Resource）

- 从ApplicationContext获取

- 

### **_@Autowired和@Resource的区别有哪些？_**

`@Autowired`和`@Resource`注解都可以用于自动装配Bean，但是它们之间还是有一些区别的：

1. 来源不同

`@Autowired`是Spring框架提供的注解，而`@Resource`是JavaEE标准注解，可以与Spring框架集成。

2. 自动装配方式不同

`@Autowired`默认按照类型进行自动装配，如果有多个同类型的Bean，则会按照名称进行匹配；而`@Resource`默认按照名称进行自动装配，如果没有指定名称，则会按照类型进行匹配。如果使用`@Resource`注解需要指定名称，可以使用`name`属性，例如：

```
@Resource(name = "myService")
private MyService myService;
```