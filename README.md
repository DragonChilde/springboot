<a href="http://120.77.237.175:9080/photos/springboot">SpringBoot</a>

# SpringBoot入门 #

## SpringBoot HelloWorld ##

**1. 导入spring boot相关的依赖**

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.7.RELEASE</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

<!-- 这个插件，可以将应用打包成一个可执行的jar包；-->
 <!--将这个应用打成jar包，直接使用java -jar的命令进行执行；-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

**2. 编写一个主程序；启动Spring Boot应用**

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {
    public static void main(String[] args) {
        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class);
    }
}
```

**3. 编写相关的Controller、Service**

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello()
    {
      return  "Hello world";
    }
}
```

**注意:Application必须使用如下的架构,放在相应的包下才可以正常启动(不能只放在java目录下)**

![](http://120.77.237.175:9080/photos/springboot/01.png)

## SpringBoot特点 ##

### 依赖管理 ###

1. 父项目

   ```java
    <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.3.7.RELEASE</version>
    </parent>
   ```

   他的父项目是

   ```java
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-dependencies</artifactId>
       <version>2.3.7.RELEASE</version>
    </parent>
   他来真正管理Spring Boot应用里面的所有依赖版本；点击进去可以看到很多已经定义好版本的依赖,几乎声明了所有开发中常用的依赖的版本号,自动版本仲裁机制
   ```

   Spring Boot的版本仲裁中心；

   以后我们导入依赖默认是不需要写版本；（没有在dependencies里面管理的依赖自然需要声明版本号）

2. 启动器

	  ```java
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-web</artifactId>
	</dependency>
<!--点击进去可以看到已经配置好所需的MVC启动依赖-->
	```
	
	- 开发导入starter场景启动器：
	
	```java
	1、见到很多 spring-boot-starter-* ： *就某种场景
	2、只要引入starter，这个场景的所有常规需要的依赖我们都自动引入
	3、SpringBoot所有支持的场景
	https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter
	4、见到的  *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。
	5、所有场景启动器最底层的依赖
	<dependency>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter</artifactId>
	  <version>2.3.4.RELEASE</version>
	  <scope>compile</scope>
	</dependency>
	```
	
	- 无需关注版本号，自动版本仲裁
	
	  ```
	  1、引入依赖默认都可以不写版本
	  2、引入非版本仲裁的jar，要写版本号。
	  ```
	
	- 可以修改默认版本号
	
	  ```java
	  1、查看spring-boot-dependencies里面规定当前依赖的版本 用的 key。
	  2、在当前项目里面重写配置
	      <properties>
	          <mysql.version>5.1.43</mysql.version>
	      </properties>
	  ```
	
### 自动配置

- 自动配好Tomcat

  - 引入Tomcat依赖。

  - 配置Tomcat

    ```java
     <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-tomcat</artifactId>
          <version>2.3.7.RELEASE</version>
          <scope>compile</scope>
     </dependency>
    ```

- 自动配好SpringMVC

  - 引入SpringMVC全套组件
  - 自动配好SpringMVC常用组件（功能）

- 自动配好Web常见功能，如：字符编码问题

  - SpringBoot帮我们配置好了所有web开发的常见场景

- 默认的包结构

  - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置
  - 想要改变扫描路径，@SpringBootApplication(scanBasePackages=**"com.sb.annotation"**)
    - 或者@ComponentScan 指定扫描路径

  ```java
  @SpringBootApplication
  等同于
  @SpringBootConfiguration
  @EnableAutoConfiguration
  @ComponentScan("com.sb.annotation")
  ```

- 各种配置拥有默认值

  - 默认配置最终都是映射到某个类上，如：MultipartProperties
  - 配置文件的值最终会绑定某个类上，这个类会在容器中创建对象

- 按需加载所有自动配置项

  - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面

## 容器功能 

### 组件添加

#### @Configuration

- 基本使用
- **Full模式与Lite模式**
  - 示例
  - 最佳实战
    - 配置 类组件之间无依赖关系用Lite模式加速容器启动过程，减少判断
    - 配置类组件之间有依赖关系，方法会被调用得到之前单实例组件，用Full模式

```java
/**
 * 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
 * 2、配置类本身也是组件
 * 3、proxyBeanMethods：代理bean的方法(SpringBoot2.3新增)
 *      Full(proxyBeanMethods = true)、【保证每个@Bean方法被调用多少次返回的组件都是单实例的】
 *      Lite(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的组件都是新创建的】
 *      实际开发中,如果每次都需要组件依赖必须使用Full模式默认。其他默认可以选择Lite模式(好处是启动的时候不用每次都检查是否在容器中,直接创建一个新的容器,加快启动速度)
 */
@Configuration(proxyBeanMethods = true)
public class MyConfig {

    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user()
    {
        User zhangsan = new User("zhangsan", 20);
        //user组件依赖了Pet组件
        zhangsan.setPet(pet());
        return zhangsan;
    }

    @Bean("tom")
    public Pet pet()
    {
        return new Pet("Tomcat");
    }

}
```

```java
@SpringBootApplication
public class AnnotationApplication {

  public static void main(String[] args) {
    // 1.返回我们IOC容器
    ConfigurableApplicationContext run = SpringApplication.run(AnnotationApplication.class, args);

    // 2.查看容器里面的组件
    String[] names = run.getBeanDefinitionNames();

    for (String name : names) {
      System.out.println(name);
    }

    // 3. 从容器中获取组件
    Pet tom01 = run.getBean("tom", Pet.class);
    Pet tom02 = run.getBean("tom", Pet.class);
    System.out.println("组件: " + (tom01 == tom02));  //组件: true

    // 4.配置类也会加载进容器中com.sb.annotation.config.MyConfig$$EnhancerBySpringCGLIB$$76a3fdda@1a82d0f
    MyConfig myConfig = run.getBean(MyConfig.class);
    System.out.println(myConfig);

    //如果@Configuration(proxyBeanMethods = true)代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有。
    //保持组件单实例
    User user = myConfig.user();
    User user1 = myConfig.user();
    System.out.println(user == user1);  //true

    //如果@Configuration(proxyBeanMethods = false)SpringBoot跳过检查,每次都他建新的容器对象
    User user2 = run.getBean("user", User.class);
    Pet tom = run.getBean("tom", Pet.class);

    System.out.println("用户的宠物:" + (user2.getPet() == tom));   //用户的宠物:false
  }
}
```



#### @Bean、@Component、@Controller、@Service、@Repository

#### @ComponentScan、@Import

```java
@Import({Color.class})	//给容器中自动创建组件、默认组件的名字就是全类名
@Configuration(proxyBeanMethods = true)
public class MyConfig {
	...
}

@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.sb.annotation")		//扫描指定目录下的包
//上面三个注解的作用等同于@SpringBootApplication
//@SpringBootApplication
public class AnnotationApplication {
 	...   
}
```

#### @Conditional

条件装配：满足Conditional指定的条件，则进行组件注入

![](http://120.77.237.175:9080/photos/springboot/82.png)

```java
//@Conditional注解也可以放在配置类的方法上判断
//@ConditionalOnBean(name = "book")      //当容器中有book时才加载
@ConditionalOnMissingBean(name = "book") //当容器中没有book时才加载
public class MyConfig {
	....
}
```

### 原生配置文件引入

#### @ImportResource

```java
======================beans.xml=========================
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="haha" class="com.atguigu.boot.bean.User">
        <property name="name" value="zhangsan"></property>
        <property name="age" value="18"></property>
    </bean>

    <bean id="hehe" class="com.atguigu.boot.bean.Pet">
        <property name="name" value="tomcat"></property>
    </bean>
</beans>
```

```java
@ImportResource("classpath:beans.xml")	//导入已存在的beans的配置文件到容器中
public class MyConfig {

}
======================测试=================
    boolean haha = run.containsBean("haha");
    boolean hehe = run.containsBean("hehe");
    System.out.println("haha: "+haha);	//haha: true
    System.out.println("hehe: "+hehe);	//hehe: true
```

## 配置绑定 

如何使用Java读取到properties文件中的内容，并且把它封装到JavaBean中，以供随时使用；

```java
public class getProperties {
     public static void main(String[] args) throws FileNotFoundException, IOException {
         Properties pps = new Properties();
         pps.load(new FileInputStream("a.properties"));
         Enumeration enum1 = pps.propertyNames();//得到配置文件的名字
         while(enum1.hasMoreElements()) {
             String strKey = (String) enum1.nextElement();
             String strValue = pps.getProperty(strKey);
             System.out.println(strKey + "=" + strValue);
             //封装到JavaBean。
         }
     }
 }
```

### @ConfigurationProperties属性绑定

##### @EnableConfigurationProperties + @ConfigurationProperties

```java
@Configuration(proxyBeanMethods = true)
@EnableConfigurationProperties(Car.class)
//1、开启Car配置绑定功能
//2、把这个Car这个组件自动注册到容器中
public class MyConfig {
	...
}
======================
@ConfigurationProperties(prefix = "mycar")	//绑定配置文件mycar的属性到bean属性里
public class Car {
	....
}
```

##### @Component + @ConfigurationProperties

```java
=======application.properties========
mycar.brand=benz
mycar.price=100

====================
@ToString
@Data
@Component
@ConfigurationProperties(prefix = "mycar")	//绑定配置文件mycar的属性到bean属性里
public class Car {

    private String brand;
    private String price;
}

===================
Car car = run.getBean(Car.class);
System.out.println(car);	//Car(brand=benz, price=100)
```

> 以上两种使用效果都一样,第一种方式可作用在第三方的Bean文件里,别人的文件不方便修改的时候使用

## 自动配置原理 ##

#### 引导加载自动配置类

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {
    public static void main(String[] args) {
        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class);
    }
}
```


@**SpringBootApplication**:    Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
 	...   
}
```

- @**SpringBootConfiguration**:Spring Boot的配置类；标注在某个类上，表示这是一个Spring Boot的配置类；
	
	- @**Configuration**:配置类上来标注这个注解；配置类 -----  配置文件；配置类也是容器中的一个组件；@Component
	
- @**EnableAutoConfiguration**：开启自动配置功能；
- **@ComponentScan:** 指定扫描哪些包,Spring注解

以前我们需要配置的东西，Spring Boot帮我们自动配置；@**EnableAutoConfiguration**告诉SpringBoot开启自动配置功能；这样自动配置才能生效；


```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
	....
}
```

- @**AutoConfigurationPackage**：自动配置包,指定了默认的包规则

- @**Import**(AutoConfigurationPackages.Registrar.class)：

  Spring的底层注解@Import，给容器中导入一个组件；导入的组件由AutoConfigurationPackages.Registrar.class；

  将主配置类（@SpringBootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器；

  ![](http://120.77.237.175:9080/photos/springboot/02.jpg)

  从上图可以看到把当前应用下的所有子包注册进去

```java
1、利用getAutoConfigurationEntry(annotationMetadata);给容器中批量导入一些组件
2、调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入到容器中的配置类
3、利用工厂加载 Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader)；得到所有的组件
4、从META-INF/spring.factories位置来加载一个文件。
	默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件
    spring-boot-autoconfigure-2.3.7.RELEASE.jar包里面也有META-INF/spring.factories
    
```

![](http://120.77.237.175:9080/photos/springboot/03.jpg)
			
	//然后进入此方法	
	List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);

![](http://120.77.237.175:9080/photos/springboot/04.png)

![](http://120.77.237.175:9080/photos/springboot/05.jpg)

![](http://120.77.237.175:9080/photos/springboot/06.jpg)

有了自动配置类，免去了我们手动编写配置注入功能组件等的工作；

Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作=以前我们需要自己配置的东西，自动配置类都帮我们；

`spring-boot`一启动就要给容器中加载的所有配置类`spring-boot-autoconfigure-2.3.7.RELEASE.jar/META-INF/spring.factories`

#### 按需开启自动配置项

```java
虽然我们127个场景的所有自动配置启动的时候默认全部加载。xxxxAutoConfiguration
按照条件装配规则（@Conditional），最终会按需配置。
```

#### 修改默认配置

```java
@Bean
@ConditionalOnBean(MultipartResolver.class)  //容器中有这个类型组件
@ConditionalOnMissingBean(name = DispatcherServlet.MULTIPART_RESOLVER_BEAN_NAME) //容器中没有这个名字 multipartResolver 的组件
public MultipartResolver multipartResolver(MultipartResolver resolver) {
    //给@Bean标注的方法传入了对象参数，这个参数的值就会从容器中找。
    //SpringMVC multipartResolver。防止有些用户配置的文件上传解析器不符合规范,底层已经默认配置好上传组件的容器名
    // Detect if the user has created a MultipartResolver but named it incorrectly
    return resolver;
}
给容器中加入了文件上传解析器；
```

SpringBoot默认会在底层配好所有的组件。但是如果用户自己配置了以用户的优先

```java
@Bean
@ConditionalOnMissingBean
public CharacterEncodingFilter characterEncodingFilter() {
}
```

总结:

- SpringBoot先加载所有的自动配置类  `xxxxxAutoConfiguration`
- 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值。xxxxProperties里面拿。xxxProperties和配置文件进行了绑定
- 生效的配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了
- 定制化配置

- - 用户直接自己@Bean替换底层的组件
  - 用户去看这个组件是获取的配置文件什么值就去修改。

**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值  ----> application.properties**

## 使用Spring Initializer快速创建Spring Boot项目 ##

**IDEA：使用 Spring Initializer快速创建项目**

IDE都支持使用Spring的项目创建向导快速创建一个Spring Boot项目；

选择我们需要的模块；向导会联网创建Spring Boot项目；

默认生成的Spring Boot项目；

- 主程序已经生成好了，我们只需要我们自己的逻辑
- resources文件夹中目录结构
  - static：保存所有的静态资源； js css  images；
  - templates：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
  - application.properties：Spring Boot应用的配置文件；可以修改一些默认设置；

**Controller**

	//这个类的所有方法返回的数据直接写给浏览器，（如果是对象转为json数据）
	//相当于(@Controller和@ResponseBody的结合使用)
	@RestController
	public class HelloController {
	
	    @RequestMapping("/hello")
	    public String hello()
	    {
	        return "hello world quick!";
	    }
	}

## Lombok

```java
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
  </dependency>

idea中搜索安装lombok插件
```

```
/**
 * @Data 提供类所有属性的 get 和 set 方法，此外还提供了equals、canEqual、hashCode、toString 方法,如为final属性，则不会为该属性生成setter方法。
 * @Setter 为单个属性提供 set 方法;
 *    注解在 类 上，为该类所有的属性提供 set 方法，提供默认的空参构造方法。
 *    注解在 属性 上,只给该属性生成set方法,还有一个默认的空参构造方法
 * @Getter
 *    注解在 属性 上；为单个属性提供 get 方法;
 *    注解在 类 上，为该类所有的属性提供 get 方法，都提供默认的空参构造方法
 *
 * @Slf4j 注解在 类 上；为类提供一个 属性名为 log 的 Slf4j 日志对象，提供默认构造方法。
 * @AllArgsConstructor 注解在 类 上；为类提供一个全参的构造方法，加了这个注解后，类中不提供默认构造方法了
 * @NoArgsConstructor 注解在 类 上；为类提供一个无参的构造方法。
 * @EqualsAndHashCode 注解在 类 上, 可以生成 equals、canEqual、hashCode 方法
 *    与@Data相比除了没有生成 get 和 set 方法外,就是少了个toString方法
 * @NonNull 注解在 属性 上，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常，也会有一个默认的无参构造方法
 * @ToString  注解用在 类 上，可以生成所有参数的 toString 方法，还会生成默认的空参构造方法
 * @Cleanup 注解用在 变量 前面，可以保证此变量代表的资源会被自动关闭，
 *          默认是调用资源的 close() 方法，如果该资源有其它关闭方法，可使用 @Cleanup(“methodName”) 来指定要调用的方法，也会生成默认的构造方法
 * @RequiredArgsConstructor 注解用在 类 上，类中所有带有 @NonNull 注解的或者带有 final 修饰的成员变量生成对应的 构造方法
 * @Value 注解用在 类 上，会生成含所有参数的构造方法，get 方法，此外还提供了equals、hashCode、toString 方法
 * @SneakyThrows 注解用在 方法 上，可以将方法中的代码用 try-catch 语句包裹起来，
 *              捕获异常并在 catch 中用 Lombok.sneakyThrow(e) 把异常抛出，可以使用 @SneakyThrows(Exception.class) 的形式指定抛出哪种异常，也会生成默认的构造方法
 * @Synchronized 这个注解用在 类方法 或者 实例方法 上，效果和 synchronized 关键字相同，区别在于锁对象不同，
 *                对于类方法和实例方法，synchronized 关键字的锁对象分别是类的 class 对象和 this 对象，
 *                而 @Synchronized 的锁对象分别是 私有静态 final 对象 lock 和 私有 final 对象 lock，当然，也可以自己指定锁对象，此外也提供默认的构造方法
 */
```



# 配置文件 #

## 配置文件 ##

SpringBoot使用一个全局的配置文件，配置文件名是固定的；

- application.properties

- application.yml

配置文件的作用：修改SpringBoot自动配置的默认值；SpringBoot在底层都给我们自动配置好；

YAML（YAML Ain't Markup Language）

​	YAML  A Markup Language：是一个标记语言

​	YAML   isn't Markup Language：不是一个标记语言；

标记语言：

​	以前的配置文件；大多都使用的是  **xxxx.xml**文件；

​	YAML：**以数据为中心**，比json、xml等更适合做配置文件；

​	YAML：配置例子

	server:
	  port: 8081

​	XML：

	<server>
		<port>8081</port>
	</server>

## YAML语法 ##

### 基本语法 ###

k:(空格)v：表示一对键值对（空格必须有）；

以**空格**的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

	server:
	    port: 8081
	    path: /hello

属性和值也是大小写敏感；

### 值的写法 ###

#### 字面量：普通的值（数字，字符串，布尔） ####

k: v：字面直接来写；

	字符串默认不用加上单引号或者双引号；
	
	​		""：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思
	
	​				name:   "zhangsan \n lisi"：输出；zhangsan 换行  lisi
	
	​		''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据
	
	​				name:   ‘zhangsan \n lisi’：输出；zhangsan \n  lisi

#### 对象、Map（属性和值）（键值对） ####

k: v：在下一行来写对象的属性和值的关系；注意缩进

​		对象还是k: v的方式

	friends:
		lastName: zhangsan
		age: 20

行内写法：

	friends: {lastName: zhangsan,age: 18}

#### 数组（List、Set） ####

用- 值表示数组中的一个元素

	pets:
	 - cat
	 - dog
	 - pig

行内写法

	pets: [cat,dog,pig]


## 配置文件值注入 ##

**配置文件application.yml**

```java
user:
  #  单引号会将 \n作为字符串输出   双引号会将\n 作为换行输出
  #  双引号不会转义，单引号会转义
  boss: true
  birth: 2019/12/9
  age: 18
  #  interests: [篮球,足球]
  interests:
    - 篮球
    - 足球
    - 18
  animal: [阿猫,阿狗]
  #  score:
  #    english: 80
  #    math: 90
  score: {english:80,math:90}
  salarys:
    - 9999.98
    - 9999.99
  pet:
    name: 阿狗
    weight: 99.99
  allPets:
    sick:
      - {name: 阿狗,weight: 99.99}
      - name: 阿猫
        weight: 88.88
      - name: 阿虫
        weight: 77.77
    health:
      - {name: 阿花,weight: 199.99}
      - {name: 阿明,weight: 199.99}
  user-name: zhangsan
```

**javaBean**

```java
/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
 *      prefix = "user"：配置文件中哪个下面的所有属性进行一一映射
 *
 * 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
 *  @ConfigurationProperties(prefix = "user")默认从全局配置文件中获取值；
 *
 */
@Component
@ConfigurationProperties(prefix = "user")
@ToString
@Data
public class User {

    private String userName;
    private Boolean boss;
    private Date birth;
    private Integer age;
    private Pet pet;
    private String[] interests;
    private List<String> animal;
    private Map<String, Object> score;
    private Set<Double> salarys;
    private Map<String, List<Pet>> allPets;
}

@ToString
@Data
public class Pet {

  private String name;
  private Double weight;
}
```

```java
User(userName=zhangsan, boss=true, birth=Mon Dec 09 00:00:00 CST 2019, age=18, pet=Pet(name=阿狗, weight=99.99), interests=[篮球, 足球, 18], animal=[阿猫, 阿狗], score={english80=, math90=}, salarys=[9999.98, 9999.99], allPets={sick=[Pet(name=阿狗, weight=99.99), Pet(name=阿猫, weight=88.88), Pet(name=阿虫, weight=77.77)], health=[Pet(name=阿花, weight=199.99), Pet(name=阿明, weight=199.99)]})
```

可以导入配置文件处理器，以后编写配置就有提示了

```java
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
    
<!--打包的时候可以把此包进行排除,减小包的大小-->    
     <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```


**注意**:

1. 当SpringBoot Test无法导包报错时,可以在pom文件加入junit依赖解决

	 	<dependency>
    	    <!-- this is needed or IntelliJ gives junit.jar or junit-platform-launcher:1.3.2 not found errors -->
    	    <groupId>org.junit.platform</groupId>
    	    <artifactId>junit-platform-launcher</artifactId>
    	    <scope>test</scope>
    	</dependency>

2. 如Dog类为Null时,把Dog的构造方法去掉

成功打印输出
![](http://120.77.237.175:9080/photos/springboot/07.jpg)


### properties配置文件在idea中默认utf-8可能会乱码 ###

![](http://120.77.237.175:9080/photos/springboot/08.jpg)

**applicaion.properties**

	person.name=李四
	person.age=40
	person.birth=1986/01/01
	person.maps.k1=v1
	person.maps.k2=v2
	person.boss=true
	person.lists=a,b,c
	person.dog.name=小狗
	person.dog.age=1

### @Value获取值和@ConfigurationProperties获取值比较 ###

<table>
<tr>
	<td></td>
	<td>@ConfigurationProperties</td>
	<td>@Value</td>
</tr>
<tr>
	<td>功能</td>
	<td>批量注入配置文件中的属性</td>
	<td>一个个指定</td>
</tr>
<tr>
	<td>松散绑定（松散语法）</td>
	<td>支持</td>
	<td>不支持</td>
</tr>
<tr>
	<td>SpEL</td>
	<td>不支持</td>
	<td>支持</td>
</tr>
<tr>
	<td>JSR303数据校验</td>
	<td>支持</td>
	<td>不支持</td>
</tr>
<tr>
	<td>复杂类型封装</td>
	<td>支持</td>
	<td>不支持</td>
</tr>
</table>

- 松散绑定
	
- 当person.name配置成person.NAME时,@ConfigurationProperties可支持
	
- SpEL
	
- @ConfigurationProperties不法在配置文件里配置SPEL表达式,@Value可以,如下图
	
- 数据校验
	- @ConfigurationProperties支持数据校验
	![](http://120.77.237.175:9080/photos/springboot/09.jpg)
	- @Value不支持
	![](http://120.77.237.175:9080/photos/springboot/10.jpg)
- 复杂类型
	- @Value不支持复杂类型
	
		![](http://120.77.237.175:9080/photos/springboot/11.jpg)

配置文件yml还是properties他们都能获取到值；

如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；

![](http://120.77.237.175:9080/photos/springboot/12.jpg)

如果说，我们专门编写了一个javaBean来和配置文件进行映射，我们就直接使用@ConfigurationProperties；

### 配置文件注入值数据校验 ###

	@Component
	@ConfigurationProperties(prefix = "person")
	@Validated
	public class Person {
	
	    /**
	     * <bean class="Person">
	     *      <property name="name" value="字面量/${key}从环境变量、配置文件中获取值/#{SpEL}"></property>
	     * <bean/>
	     */
	
	   //lastName必须是邮箱格式
	    @Email
	    //@Value("${person.name}")
	    private String name;
	    //@Value("#{11*2}")
	    private Integer age;
	    //@Value("true")
	    private Boolean boss;
	
	    private Date birth;
	    private Map<String,Object> maps;
	    private List<Object> lists;
	    private Dog dog;

### @PropertySource&@ImportResource&@Bean ###

在实际开发过程中,没有可能把所有类的属性全部都配置在全局配置文件中,因为必须指定加载文件

@**PropertySource**：加载指定的配置文件；

![](http://120.77.237.175:9080/photos/springboot/13.jpg)

@**ImportResource**：导入Spring的配置文件，让配置文件里面的内容生效；

Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别；

![](http://120.77.237.175:9080/photos/springboot/14.jpg)

想让Spring的配置文件生效，加载进来；@**ImportResource**标注在一个配置类上

![](http://120.77.237.175:9080/photos/springboot/15.jpg)

**SpringBoot推荐给容器中添加组件的方式；推荐使用全注解的方式**

1. 配置类**@Configuration**------>Spring配置文件

2. 使用**@Bean**给容器中添加组件

		/**
		 * @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
		 *
		 * 在配置文件中用<bean><bean/>标签添加组件
		 *
		 */
		@Configuration
		public class MyConfig {
		
		    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
		    @Bean
		    public Hello hello02()
		    {
		        return new Hello();
		    }
		}

![](http://120.77.237.175:9080/photos/springboot/16.jpg)


#### 随机数 ####

	${random.value}、${random.int}、${random.long}
	${random.int(10)}、${random.int[1024,65536]}

#### 占位符获取之前配置的值，如果没有可以是用:指定默认值 ####

	person.name=李四${random.uuid}
	person.age=${random.int(30)}	//随机数最大为30
	person.birth=1986/01/01
	person.maps.k1=v1
	person.maps.k2=v2
	person.boss=true
	person.lists=a,b,c
	person.dog.name=${person.name}小狗
	person.dog.age=${random.int[1,10]}	//随机数1-10
	
	//Person{name='李四5030c478-191f-4df8-8bee-348053a3984c', age=13, boss=true, birth=Wed Jan 01 00:00:00 CST 1986, maps={k1=v1, k2=v2}, lists=[a, b, c], dog=Dog{name='李四1326e75a-9bcf-4eca-ab01-199838871cf4小狗', age=9}}

**注意:这里要为persson.properties定义为Spring的配置文件才可以正常使用占位符**

![](http://120.77.237.175:9080/photos/springboot/17.jpg)

## Profile ##

### 多Profile文件 ###

我们在主配置文件编写的时候，文件名可以是   application-{profile}.properties/yml

默认使用application.properties的配置；

### yml支持多文档块方式 ###

	spring:
	  profiles:
	    active: pro	#启用名为pro的配置
	
	---
	
	server:
	  port: 8084
	spring:
	  profiles: pro
	
	---
	
	server:
	  port: 8085
	spring:
	  profiles: dev #指定属于哪个环境

### 激活指定profile ###

1. 在配置文件中指定  spring.profiles.active=pro

	![](http://120.77.237.175:9080/photos/springboot/18.jpg)

2. 命令行java -jar springboot-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=pro；

3. 通过执行命令行

	![](http://120.77.237.175:9080/photos/springboot/19.jpg)

4. 虚拟机参数；

​	-Dspring.profiles.active=dev
​	
![](http://120.77.237.175:9080/photos/springboot/20.jpg)

## 配置文件加载位置 ##

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

–file:./config/

–file:./

–classpath:/config/

–classpath:/

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

SpringBoot会从这四个位置全部加载主配置文件；**互补配置**；

==我们还可以通过spring.config.location来改变默认的配置文件位置==

**注意:直接在配置文件里直接指定外部的配置文件是不会启作用的(以下是错误示范)**

![](http://120.77.237.175:9080/photos/springboot/23.jpg)

**项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；**

	 java -jar springboot-config02-0.0.1-SNAPSHOT.jar --spring.config.location = F:/application.properties

![](http://120.77.237.175:9080/photos/springboot/24.jpg)

**注意:这里的-file是基于项目下的根目录,项目下的module的目录不在此作用范围,application配置不起作用**

![](http://120.77.237.175:9080/photos/springboot/21.jpg)

更改配置项目的访问路径

	server.servlet.context-path=/bootconfig02

![](http://120.77.237.175:9080/photos/springboot/22.jpg)

## 外部配置加载顺序 ##

**==SpringBoot也可以从以下位置加载配置； 优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置==**

1. **命令行参数**

	所有的配置都可以在命令行上进行指定
	
		java -jar springboot-config02-0.0.1-SNAPSHOT.jar --server.port=8087  --server.context-path=/abc
	
	多个配置用空格分开； --配置项=值


2. 来自java:comp/env的JNDI属性

3. Java系统属性（System.getProperties()）

4. 操作系统环境变量

5. RandomValuePropertySource配置的random.*属性值

	==**由jar包外向jar包内进行寻找；**==

	==**优先加载带profile**==

6. **jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

7. **jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

	==**再来加载不带profile**==

8. **jar包外部的application.properties或application.yml(不带spring.profile)配置文件**

9. **jar包内部的application.properties或application.yml(不带spring.profile)配置文件**



10. @Configuration注解类上的@PropertySource

11. 通过SpringApplication.setDefaultProperties指定的默认属性

所有支持的配置加载来源；

<a href="https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/spring-boot-features.html#boot-features-external-config">[参考官方文档]</a>

## 自动配置原理 ##

<a href="https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/appendix-application-properties.html#common-application-properties">配置文件能配置的属性参照</a>

### 自动配置原理 ###

1. SpringBoot启动的时候加载主配置类，开启了自动配置功能 ==@EnableAutoConfiguration==
2. **@EnableAutoConfiguration 作用**
	-  利用EnableAutoConfigurationImportSelector给容器中导入一些组件？

	-  可以查看selectImports()方法的内容；
	
	-  List<String> configurations = getCandidateConfigurations(annotationMetadata,      attributes);获取候选的配置

		SpringFactoriesLoader.loadFactoryNames()
		扫描所有jar包类路径下  META-INF/spring.factories
		把扫描到的这些文件的内容包装成properties对象
		从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器中

	==将 类路径下  META-INF/spring.factories 里面配置的所有EnableAutoConfiguration的值加入到了容器中；==
	
		# Auto Configure
		org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
		org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
		org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
		org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
		org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
		org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
		org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
		org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
		org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
		org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
		org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
		org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
		org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveRestClientAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
		org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
		org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
		org.springframework.boot.autoconfigure.elasticsearch.rest.RestClientAutoConfiguration,\
		org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
		org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
		org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
		org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
		org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
		org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
		org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
		org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\
		org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\
		org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\
		org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
		org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
		org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
		org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
		org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
		org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
		org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
		org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
		org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
		org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
		org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
		org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
		org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
		org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
		org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
		org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
		org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\
		org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
		org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
		org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
		org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
		org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
		org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
		org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
		org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
		org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\
		org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
		org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
		org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\
		org.springframework.boot.autoconfigure.rsocket.RSocketMessagingAutoConfiguration,\
		org.springframework.boot.autoconfigure.rsocket.RSocketRequesterAutoConfiguration,\
		org.springframework.boot.autoconfigure.rsocket.RSocketServerAutoConfiguration,\
		org.springframework.boot.autoconfigure.rsocket.RSocketStrategiesAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration,\
		org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
		org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
		org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
		org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
		org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration,\
		org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration,\
		org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
		org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
		org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
		org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\
		org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
		org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\
		org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\
		org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\
		org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration,\
		org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration
	
	每一个这样的  xxxAutoConfiguration类都是容器中的一个组件，都加入到容器中；用他们来做自动配置；

3. 每一个自动配置类进行自动配置功能
4. 以**HttpEncodingAutoConfiguration（Http编码自动配置）**为例解释自动配置原理；

		@Configuration   //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
		@EnableConfigurationProperties(HttpProperties.class)  //启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中
		
		@ConditionalOnWebApplication //Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；    判断当前应用是否是web应用，如果是，当前配置类生效
		
		@ConditionalOnClass(CharacterEncodingFilter.class)  //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
		
		@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)  //判断配置文件中是否存在某个配置  spring.http.encoding.enabled；如果不存在，判断也是成立的
		//即使我们配置文件中不配置spring.http.encoding.enabled=true，也是默认生效的；
		
			public class HttpEncodingAutoConfiguration {
		  
		  	//他已经和SpringBoot的配置文件映射了
		  	private final HttpProperties.Encoding properties;
		  
		   //只有一个有参构造器的情况下，参数的值就会从容器中拿
		  	public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
				this.properties = properties;
			}
		  
		    @Bean   //给容器中添加一个组件，这个组件的某些值需要从properties中获取
			@ConditionalOnMissingBean //判断容器有没有这个组件？
			public CharacterEncodingFilter characterEncodingFilter() {
				CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
				filter.setEncoding(this.properties.getCharset().name());
				filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
				filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
				return filter;
			}

	根据当前不同的条件判断，决定这个配置类是否生效？

	一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；

5. 所有在配置文件中配置的属性都是在xxxxProperties类中封装；配置文件能配置什么就可以参照某个功能对应的这个属性类

		@ConfigurationProperties(prefix = "spring.http")//从配置文件中获取指定的值和bean的属性进行绑定
		public class HttpProperties {
		
		public static final Charset DEFAULT_CHARSET = StandardCharsets.UTF_8;

**精髓：**

​	**1）、SpringBoot启动会加载大量的自动配置类**

​	**2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；**

​	**3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）**

​	**4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；**

xxxxAutoConfigurartion：自动配置类；

给容器中添加组件

xxxxProperties:封装配置文件中相关属性；

### 细节 ###

1. **@Conditional派生注解（Spring注解版原生的@Conditional作用）**

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；


<table>
<tr>
	<td>@Conditional扩展注解</td>
	<td>作用（判断是否满足当前指定条件）</td>
</tr>

<tr>
	<td>@ConditionalOnJava</td>
	<td>系统的java版本是否符合要求</td>
</tr>
<tr>
	<td>@ConditionalOnBean</td>
	<td>容器中存在指定Bean；</td>
</tr>
<tr>
	<td>@ConditionalOnMissingBean</td>
	<td>容器中不存在指定Bean；</td>
</tr>
<tr>
	<td>@ConditionalOnExpression</td>
	<td>满足SpEL表达式指定</td>
</tr>
<tr>
	<td>@ConditionalOnClass</td>
	<td>系统中有指定的类</td>
</tr>
<tr>
	<td>@ConditionalOnMissingClass</td>
	<td>系统中没有指定的类</td>
</tr>
<tr>
	<td>@ConditionalOnSingleCandidate</td>
	<td>容器中只有一个指定的Bean，或者这个Bean是首选Bean</td>
</tr>
<tr>
	<td>@ConditionalOnProperty</td>
	<td>系统中指定的属性是否有指定的值</td>
</tr>

<tr>
	<td>@ConditionalOnResource</td>
	<td>类路径下是否存在指定资源文件</td>
</tr>
<tr>
	<td>@ConditionalOnWebApplication</td>
	<td>当前是web环境</td>
</tr>
<tr>
	<td>@ConditionalOnNotWebApplication</td>
	<td>当前不是web环境</td>
</tr>
<tr>
	<td>@ConditionalOnJndi</td>
	<td>JNDI存在指定项</td>
</tr>
</table>

**自动配置类必须在一定的条件下才能生效；**

我们怎么知道哪些自动配置类生效；

**==我们可以通过启用  debug=true属性；来让控制台打印自动配置报告==**，这样我们就可以很方便的知道哪些自动配置类生效；


	============================
	CONDITIONS EVALUATION REPORT
	============================


​	
​	Positive matches:（自动配置类启用的）
​	-----------------
​	
​	DispatcherServletAutoConfiguration matched:
​	  - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet' (OnClassCondition)
​	  - found 'session' scope (OnWebApplicationCondition)



	Negative matches:（没有启动，没有匹配成功的自动配置类）
	
	ActiveMQAutoConfiguration:
	  Did not match:
	     - @ConditionalOnClass did not find required class 'javax.jms.ConnectionFactory' (OnClassCondition)
	
	AopAutoConfiguration.AspectJAutoProxyingConfiguration:
	  Did not match:
	     - @ConditionalOnClass did not find required class 'org.aspectj.weaver.Advice' (OnClassCondition)


# 日志 #

## 日志框架 ##

**市面上的日志框架**

JUL、JCL、Jboss-logging、logback、log4j、log4j2、slf4j....

<table>
	<tr>
		<td>日志门面  （日志的抽象层）</td>
		<td>日志实现</td>
	</tr>
	<tr>
		<td>JCL（Jakarta  Commons Logging）(不用)    SLF4j（Simple  Logging Facade for Java）    jboss-logging(不用)</td>
		<td>Log4j  JUL（java.util.logging）  Log4j2  Logback</td>
	</tr>
</table>

左边选一个门面（抽象层）、右边来选一个实现；

日志门面：  SLF4J；

日志实现：Logback；

SpringBoot：底层是Spring框架，Spring框架默认是用JCL；

​	**==SpringBoot选用 SLF4j和logback==**

## SLF4j使用 ##

### 如何在系统中使用SLF4j ###

https://www.slf4j.org

以后开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法；

给系统里面导入slf4j的jar和  logback的实现jar

	import org.slf4j.Logger;
	import org.slf4j.LoggerFactory;
	
	public class HelloWorld {
	  public static void main(String[] args) {
	    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
	    logger.info("Hello World");
	  }
	}

![](http://120.77.237.175:9080/photos/springboot/25.png)

每一个日志的实现框架都有自己的配置文件。使用slf4j以后，**配置文件还是做成日志实现框架自己本身的配置文件；**

### 遗留问题 ###

me（slf4j+logback）, Spring（commons-logging）,Hibernate（jboss-logging）,MyBatis、xxxx

统一日志记录，即使是别的框架和我一起统一使用slf4j进行输出？

![](http://120.77.237.175:9080/photos/springboot/26.png)


**如何让系统中所有的日志都统一到slf4j；**

1. 将系统中其他日志框架先排除出去；
2. 用中间包来替换原有的日志框架；
3. 我们导入slf4j其他的实现


## SpringBoot日志关系 ##

SpringBoot使用它来做日志功能

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.2.5.RELEASE</version>
      <scope>compile</scope>
    </dependency>
底层依赖关系

![](http://120.77.237.175:9080/photos/springboot/27.jpg)

总结：

1. SpringBoot底层也是使用slf4j+logback的方式进行日志记录
2. SpringBoot也把其他的日志都替换成了slf4j；
3. 中间替换包？


		public class SLF4JLoggerContextFactory implements LoggerContextFactory {
			    private static final StatusLogger LOGGER = StatusLogger.getLogger();
			    private static LoggerContext context = new SLF4JLoggerContext();
		}
	
	![](http://120.77.237.175:9080/photos/springboot/28.png)

4. 如果我们要引入其他框架？一定要把这个框架的默认日志依赖移除掉？

	SpringBoot框架2.0前用的是commons-logging,2.0后已经没有了以下的依赖；

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<exclusions>
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>

**SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可**

## 日志使用 ##

### 默认配置 ###

SpringBoot默认帮我们配置好了日志；


    /*记录器*/
    Logger logger = LoggerFactory.getLogger(getClass());
    
    @Test
    void contextLoads() {
    
        /*调试不再需要直接打印出来*/
        //System.out.println();
    
        //日志的级别；
        //由低到高   trace<debug<info<warn<error
        //可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效
        logger.trace("这是trace日志...");
        logger.debug("这是debug日志...");
        //SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别；root级别
        logger.info("这是info日志...");
        logger.warn("这是warn日志...");
        logger.error("这是error日志...");
    }
    
    <!--
      日志输出格式：
    	%d表示日期时间，
    	%thread表示线程名，
    	%-5level：级别从左显示5个字符宽度
    	%logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
    	%msg：日志消息，
    	%n是换行符
    -->
    %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n

SpringBoot修改日志的默认配置

	#指定包路径的日志级别
	logging.level.com.springboot=trace
	
	#logging.path=
	# 不指定路径在当前项目下生成springboot.log日志
	# 可以指定完整的路径；
	#logging.file=F:/springboot.log
	
	# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
	logging.file.path=f:/spring/log
	
	#  在控制台输出的日志的格式
	logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
	# 指定文件中日志输出的格式
	logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n


<table>
<tr>
	<td>logging.file</td>
	<td>logging.path</td>
	<td>Example</td>
	<td>Description</td>
</tr>
<tr>
	<td>(none)</td>
	<td>(none)</td>
	<td></td>
	<td>只在控制台输出</td>
</tr>
<tr>
	<td>指定文件名</td>
	<td>(none)</td>
	<td>my.log</td>
	<td>输出日志到my.log文件</td>
</tr>
<tr>
	<td>(none)</td>
	<td>指定目录</td>
	<td>/var/log</td>
	<td>输出到指定目录的 spring.log 文件中</td>
</tr>
</table>

### 指定配置 ###

日志的默认配置已经在SpringBoot底层已经设置好了,


给类路径下放上每个日志框架自己的配置文件即可；SpringBoot就不使用他默认配置的了

![](http://120.77.237.175:9080/photos/springboot/29.jpg)

<table>
<tr>
	<td>Logging System</td>
	<td>Customization</td>
</tr>
<tr>
	<td>Logback</td>
	<td>`logback-spring.xml`, `logback-spring.groovy`, `logback.xml` or `logback.groovy`</td>
</tr>
<tr>
	<td>Log4j2</td>
	<td>`log4j2-spring.xml` or `log4j2.xml`</td>
</tr>
<tr>
	<td>JDK (Java Util Logging)</td>
	<td>logging.properties`</td>d
</tr>
</table>

logback.xml：不经由Spring框架,直接就被日志框架识别了

**logback-spring.xml**：日志框架就不直接加载日志的配置项，由SpringBoot解析日志配置，可以使用SpringBoot的高级Profile功能

	<springProfile name="staging">
	    <!-- configuration to be enabled when the "staging" profile is active -->
	</springProfile>

如:

  	<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
  	    <!--
  	    日志输出格式：
  			%d表示日期时间，
  			%thread表示线程名，
  			%-5level：级别从左显示5个字符宽度
  			%logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
  			%msg：日志消息，
  			%n是换行符
  	    -->
  	    <layout class="ch.qos.logback.classic.PatternLayout">
  	        <springProfile name="dev">
  	            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ----> [%thread] ---> %-5level %logger{50} - %msg%n</pattern>
  	        </springProfile>
  	        <springProfile name="!dev">
  	            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ==== [%thread] ==== %-5level %logger{50} - %msg%n</pattern>
  	        </springProfile>
  	    </layout>
  	</appender>

如果使用logback.xml作为日志配置文件，还要使用profile功能，会有以下错误

 `no applicable action for [springProfile]`


## 切换日志框架 ##

可以按照slf4j的日志适配图，进行相关的切换(了解)；

slf4j+log4j的方式；要把依赖的logback,log4j转slf4j的转换包去除

	<dependency>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter-web</artifactId>
	  <exclusions>
	    <exclusion>
	      <artifactId>logback-classic</artifactId>
	      <groupId>ch.qos.logback</groupId>
	    </exclusion>
	    <exclusion>
	      <artifactId>log4j-over-slf4j</artifactId>
	      <groupId>org.slf4j</groupId>
	    </exclusion>
	  </exclusions>
	</dependency>
	
	<dependency>
	  <groupId>org.slf4j</groupId>
	  <artifactId>slf4j-log4j12</artifactId>
	</dependency>

切换starters为log4j2,SpringBoot默认是使用spring-boot-starter-logging,要把依赖去掉


	   <dependency>
	        <groupId>org.springframework.boot</groupId>
	        <artifactId>spring-boot-starter-web</artifactId>
	        <exclusions>
	            <exclusion>
	                <artifactId>spring-boot-starter-logging</artifactId>
	                <groupId>org.springframework.boot</groupId>
	            </exclusion>
	        </exclusions>
	    </dependency>
	
		<dependency>
		  <groupId>org.springframework.boot</groupId>
		  <artifactId>spring-boot-starter-log4j2</artifactId>
		</dependency>


# Web开发 #

## 简介 ##

使用SpringBoot；

**1）、创建SpringBoot应用，选中我们需要的模块；**

**2）、SpringBoot已经默认将这些场景配置好了，只需要在配置文件中指定少量配置就可以运行起来**

**3）、自己编写业务代码；**

**自动配置原理？**

这个场景SpringBoot帮我们配置了什么？能不能修改？能修改哪些配置？能不能扩展？xxx

	xxxxAutoConfiguration：帮我们给容器中自动配置组件；
	xxxxProperties:配置类来封装配置文件的内容；

## SpringBoot对静态资源的映射规则 ##

	@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
	public class ResourceProperties {
		//可以设置和静态资源有关的参数，缓存时间等

---

	public class WebMvcAutoConfiguration {
	
		......
	
		@Override
		public void addResourceHandlers(ResourceHandlerRegistry registry) {
			if (!this.resourceProperties.isAddMappings()) {
				logger.debug("Default resource handling disabled");
				return;
			}
			Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
			CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
			if (!registry.hasMappingForPattern("/webjars/**")) {
				customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
						.addResourceLocations("classpath:/META-INF/resources/webjars/")
						.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
			}
			//静态资源文件夹映射
			String staticPathPattern = this.mvcProperties.getStaticPathPattern();
			if (!registry.hasMappingForPattern(staticPathPattern)) {
				customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
						.addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
						.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
			}
		}
	
		//配置欢迎页映射
		@Bean
		public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
				FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
			WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
					new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
					this.mvcProperties.getStaticPathPattern());
			welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
			return welcomePageHandlerMapping;
		}
	
	}

## 功能分析

#### 静态资源访问

##### webjar

自动映射 `/webjars/**` ，都去 `classpath:/META-INF/resources/webjars/` 找资源

webjars：以jar包的方式引入静态资源(https://www.webjars.org/)；

```
pom
<!--引入jquery-webjar-->在访问的时候只需要写webjars下面资源的名称即可
  <dependency>
        <groupId>org.webjars</groupId>
        <artifactId>jquery</artifactId>
        <version>3.5.0</version>
   </dependency>
```

![](http://120.77.237.175:9080/photos/springboot/31.png)

http://localhost:8080/webjars/jquery/3.5.0/jquery.js,后面地址要按照依赖里面的包路径

##### 静态资源目录

"/**" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射

```
"classpath:/META-INF/resources/", 
"classpath:/resources/",
"classpath:/static/", 
"classpath:/public/" 
"/"：当前项目的根路径
```

![](http://120.77.237.175:9080/photos/springboot/32.png)

访问 ： 当前项目根路径/ + 静态资源名 

http://localhost:8080/asserts/css/bootstrap.min.css	去静态资源文件夹里面找指定的的文件

原理： 静态映射/**。

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面

> **注意:以下配置会改变默认的静态资源路径**

```java
spring:
  mvc:
    static-path-pattern: /res/**

  resources:
    static-locations: [classpath:/hello/]
```

##### 静态资源访问前缀

```java
#默认无前缀
spring:
  mvc:
    static-path-pattern: /res/**
```

当前项目 + static-path-pattern + 静态资源名 = 静态资源文件夹下找

##### 欢迎页支持

静态资源文件夹下的所有index.html页面；被"/**"映射,localhost:8080/   找index页面

- 可以配置静态资源路径

- 但是不可以配置静态资源的访问前缀。否则导致 index.html不能被默认访问

  ```java
  spring:
  #  mvc:
  #    static-path-pattern: /res/**   这个会导致welcome page功能失效
  
    resources:
      static-locations: [classpath:/hello/]
  ```

- controller能处理/index

##### 自定义 `Favicon`

favicon.ico  都是在静态资源文件下找

```java
spring:
#  mvc:
#    static-path-pattern: /res/**   这个会导致 Favicon 功能失效
```

> **注意:在配置文件中如果指定了静态源的路径,会使默认的SpringBoot映射配置失效**

	spring.resources.static-locations=classpath:/hello,classpath:/abc


## 模板引擎 ##

JSP、Velocity、Freemarker、Thymeleaf

SpringBoot推荐的Thymeleaf；

语法更简单，功能更强大；

### 引入thymeleaf ###

	**pom**
	<!--使用的springBoot2以上的版本使用默认自带的thymeleaf版本就可以了,2.0以下才需指定特有版本-->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-thymeleaf</artifactId>
	</dependency>
	<!--
	切换thymeleaf版本
	<properties>
			<thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
			<!-- 布局功能的支持程序  thymeleaf3主程序  layout2以上版本 -->
			<!-- thymeleaf2   layout1-->
			<thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
	  </properties>
	-->

### Thymeleaf使用 ###

**源码配置**	

	@ConfigurationProperties(
	    prefix = "spring.thymeleaf"
	)
	public class ThymeleafProperties {
	    private static final Charset DEFAULT_ENCODING;
	    public static final String DEFAULT_PREFIX = "classpath:/templates/";
	    public static final String DEFAULT_SUFFIX = ".html";
	    private boolean checkTemplate = true;
	    private boolean checkTemplateLocation = true;
	    private String prefix = "classpath:/templates/";
	    private String suffix = ".html";
	    private String mode = "HTML";

只要我们把HTML页面放在classpath:/templates/，thymeleaf就能自动渲染；

使用：

1. 导入thymeleaf的名称空间

		<html xmlns:th="http://www.thymeleaf.org">
2. 使用thymeleaf语法；

		<!DOCTYPE html>
		<!--引入thymeleaf标签-->
		<html lang="en" xmlns:th="http://www.thymeleaf.org">
		<head>
		    <meta charset="UTF-8">
		    <title>success</title>
		</head>
		<body>
		<!--th:text 将p里面的文本内容设置为 -->
		<p th:text="${hello}">欢迎</p>
		</body>
		</html>

### 语法规则 ###

1. th:text；改变当前元素里面的文本内容；

	th：任意html属性；来替换原生属性的值


	![](http://120.77.237.175:9080/photos/springboot/33.png)

2. 表达式

		Simple expressions:（表达式语法）
		    Variable Expressions: ${...}：获取变量值；OGNL；
		    		1）、获取对象的属性、调用方法
		    		2）、使用内置的基本对象：
		    			#ctx : the context object.
		    			#vars: the context variables.
		                #locale : the context locale.
		                #request : (only in Web Contexts) the HttpServletRequest object.
		                #response : (only in Web Contexts) the HttpServletResponse object.
		                #session : (only in Web Contexts) the HttpSession object.
		                #servletContext : (only in Web Contexts) the ServletContext object.
		                
		                ${session.foo}
		            3）、内置的一些工具对象：
		#execInfo : information about the template being processed.
		#messages : methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
		#uris : methods for escaping parts of URLs/URIs
		#conversions : methods for executing the configured conversion service (if any).
		#dates : methods for java.util.Date objects: formatting, component extraction, etc.
		#calendars : analogous to #dates , but for java.util.Calendar objects.
		#numbers : methods for formatting numeric objects.
		#strings : methods for String objects: contains, startsWith, prepending/appending, etc.
		#objects : methods for objects in general.
		#bools : methods for boolean evaluation.
		#arrays : methods for arrays.
		#lists : methods for lists.
		#sets : methods for sets.
		#maps : methods for maps.
		#aggregates : methods for creating aggregates on arrays or collections.
		#ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).
		
		    Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
		    	补充：配合 th:object="${session.user}：
		   <div th:object="${session.user}">
		    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
		    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
		    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
		    </div>
		    
		    Message Expressions: #{...}：获取国际化内容
		    Link URL Expressions: @{...}：定义URL；
		    		@{/order/process(execId=${execId},execType='FAST')}
		    Fragment Expressions: ~{...}：片段引用表达式
		    		<div th:insert="~{commons :: main}">...</div>
		    		
		Literals（字面量）
		      Text literals: 'one text' , 'Another one!' ,…
		      Number literals: 0 , 34 , 3.0 , 12.3 ,…
		      Boolean literals: true , false
		      Null literal: null
		      Literal tokens: one , sometext , main ,…
		Text operations:（文本操作）
		    String concatenation: +
		    Literal substitutions: |The name is ${name}|
		Arithmetic operations:（数学运算）
		    Binary operators: + , - , * , / , %
		    Minus sign (unary operator): -
		Boolean operations:（布尔运算）
		    Binary operators: and , or
		    Boolean negation (unary operator): ! , not
		Comparisons and equality:（比较运算）
		    Comparators: > , < , >= , <= ( gt , lt , ge , le )
		    Equality operators: == , != ( eq , ne )
		Conditional operators:条件运算（三元运算符）
		    If-then: (if) ? (then)
		    If-then-else: (if) ? (then) : (else)
		    Default: (value) ?: (defaultvalue)
		Special tokens:
		    No-Operation: _ 

---

		<!DOCTYPE html>
		<!--引入thymeleaf标签-->
		<html lang="en" xmlns:th="http://www.thymeleaf.org">
		<head>
		    <meta charset="UTF-8">
		    <title>success</title>
		</head>
		<body>
		<!--th:text 将p里面的文本内容设置为 -->
		<!--th:text进行了转义-->
		<p th:text="${hello}">欢迎</p>
		<!--th:utext不进行转义-->
		<p th:utext="${hello}">欢迎</p>
		
		<hr>
		<!--th:each会迭代当前的标签,因此要把迭代的内容标注在所需迭代的标签里-->
		<h1 th:text="${user}" th:each="user:${users}"></h1>
		<hr/>
		<ul>
		    <li th:each="user:${users}">[[${user}]]</li>
		</ul>
		</body>
		</html>

**效果如下**:

![](http://120.77.237.175:9080/photos/springboot/34.jpg)

## SpringMVC自动配置 ##

https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/spring-boot-features.html#boot-features

###  Spring MVC auto-configuration ###


Spring Boot 自动配置好了SpringMVC

以下是SpringBoot对SpringMVC的默认配置

	org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration

自动配置在Spring的默认值之上添加了以下功能

- 包含`ContentNegotiatingViewResolver`和`BeanNameViewResolver`。--> 视图解析器
- 支持服务静态资源，包括对WebJars的支持（<a href="https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content">官方文档中有介绍</a>）。--> 静态资源文件夹路径
- 自动注册`Converter`，`GenericConverter`和`Formatterbeans`。--> 转换器，格式化器
- 支持`HttpMessageConverters`（<a href="https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters">官方文档中有介绍</a>）。--> SpringMVC用来转换Http请求和响应的；User---Json；
- 自动注册`MessageCodesResolver`（<a href="https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-message-codes">官方文档中有介绍</a>）。--> 定义错误代码生成规则
- 静态`index.html`支持。--> 静态首页访问
- 定制`Favicon`支持（<a href="https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon">官方文档中有介绍</a>）。--> 网站图标
- 自动使用`ConfigurableWebBindingInitializerbean`（<a href="https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer">官方文档中有介绍</a>）。


如果您想保留 Spring Boot MVC 的功能，并且需要添加其他 <a href="https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#mvc">MVC 配置</a>（拦截器，格式化程序和视图控制器等），可以添加自己的 `WebMvcConfigurer` 类型的 `@Configuration` 类，但不能带 `@EnableWebMvc` 注解。如果您想自定义 `RequestMappingHandlerMapping`、`RequestMappingHandlerAdapter` 或者 `ExceptionHandlerExceptionResolver` 实例，可以声明一个 `WebMvcRegistrationsAdapter` 实例来提供这些组件。

如果您想完全掌控 Spring MVC，可以添加自定义注解了 `@EnableWebMvc` 的 `@Configuration` 配置类。


#### 视图解析器 ####

视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发？重定向？）

- 自动配置了ViewResolver
- ContentNegotiatingViewResolver：组合所有的视图解析器的

![](http://120.77.237.175:9080/photos/springboot/35.png)

视图解析器从哪里来的？

![](http://120.77.237.175:9080/photos/springboot/36.png)

**可以自己给容器中添加一个视图解析器；自动的将其组合进来**


	@SpringBootApplication
	public class SpringbootWebApplication {
	
	    public static void main(String[] args) {
	        SpringApplication.run(SpringbootWebApplication.class, args);
	    }
	
	    @Bean
	    public ViewResolver myViewReolver()
	    {
	        return new MyViewResolver();
	    }
	
	    private static class MyViewResolver implements  ViewResolver{
	
	        @Override
	        public View resolveViewName(String s, Locale locale) throws Exception {
	            return null;
	        }
	    }
	}

![](http://120.77.237.175:9080/photos/springboot/37.jpg)

#### 转换器、格式化器 ####

- Converter：转换器； public String hello(User user)：类型转换使用Converter（表单数据转为user）
- Formatter 格式化器； 2017.12.17===Date；

		 @Bean
	    //在配置文件中配置日期格式化的规则
	    @ConditionalOnProperty(prefix = "spring.mvc", name = "date-format")
	    public Formatter<Date> dateFormatter() {
	        return new DateFormatter(this.mvcProperties.getDateFormat());//日期格式化组件
	    }

**自己添加的格式化器转换器，我们只需要放在容器中即可**

#### HttpMessageConverters ####

- `HttpMessageConverter`：SpringMVC用来转换Http请求和响应的；User---Json
- `HttpMessageConverters` 是从容器中确定；获取所有的HttpMessageConverter；

**自己给容器中添加HttpMessageConverter，只需要将自己的组件注册容器中（@Bean,@Component）**

#### MessageCodesResolver 定义错误代码生成规则####

#### ConfigurableWebBindingInitializer  ####

**我们可以配置一个ConfigurableWebBindingInitializer来替换默认的；（添加到容器）**

### 扩展SpringMVC ###

以前的配置文件中的配置

	<mvc:view-controller path="/hello" view-name="success"/>
	<mvc:interceptors>
	    <mvc:interceptor>
	        <mvc:mapping path="/hello"/>
	        <bean></bean>
	    </mvc:interceptor>
	</mvc:interceptors>

**现在编写一个配置类（@Configuration），是WebMvcConfigurer类型；不能标注@EnableWebMvc**

既保留了所有的自动配置，也能用我们扩展的配置

	//使用WebMvcConfigurer可以来扩展SpringMVC的功能
	@Configuration
	public class MyConfig implements WebMvcConfigurer {
	    @Override
	    public void addViewControllers(ViewControllerRegistry registry) {
			// super.addViewControllers(registry);
	   		 //浏览器发送 /atguigu 请求来到 success
	        registry.addViewController("/hello").setViewName("success");
	    }
	}

**原理**

1. `WebMvcAutoConfiguration`是SpringMVC的自动配置类
2. 在做其他自动配置时会导入；`@Import(**EnableWebMvcConfiguration**.class)`

	下面这个类是WebMvcAutoConfiguration中的一个内部类

	![](http://120.77.237.175:9080/photos/springboot/38.jpg)
	![](http://120.77.237.175:9080/photos/springboot/39.jpg)

	这里重点看下`DelegatingWebMvcConfiguration`

		@Configuration(
		proxyBeanMethods = false
		)
		public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
		    private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();
		
		    public DelegatingWebMvcConfiguration() {
		    }
		
			//从容器中获取所有的WebMvcConfigurer
		    @Autowired(
		        required = false
		    )
		    public void setConfigurers(List<WebMvcConfigurer> configurers) {
		        if (!CollectionUtils.isEmpty(configurers)) {
		            this.configurers.addWebMvcConfigurers(configurers);
		        }
		
		    }
			......
		
			/**
		     * 查看其中一个方法
		      * this.configurers：也是WebMvcConfigurer接口的一个实现类
		      * 看一下调用的configureViewResolvers方法 ↓
		      */
		    protected void configureViewResolvers(ViewResolverRegistry registry) {
		        this.configurers.configureViewResolvers(registry);
		    }
		
			......
		}

	---
	
		 public void configureViewResolvers(ViewResolverRegistry registry) {
		    Iterator var2 = this.delegates.iterator();
		
		    while(var2.hasNext()) {
		        WebMvcConfigurer delegate = (WebMvcConfigurer)var2.next();
				//将所有的WebMvcConfigurer相关配置都来一起调用；
		        delegate.configureViewResolvers(registry);
		    }
		
		}

3. 容器中所有的WebMvcConfigurer都会一起起作用
4. 我们的配置类也会被调用

效果：SpringMVC的自动配置和我们的扩展配置都会起作用

![](http://120.77.237.175:9080/photos/springboot/40.jpg)

### 全面接管SpringMVC ###

SpringBoot对SpringMVC的自动配置不需要了，所有都是我们自己配置；所有的SpringMVC的自动配置都失效了

**我们需要在配置类中添加@EnableWebMvc即可；**

![](http://120.77.237.175:9080/photos/springboot/41.jpg)

原理：

为什么`@EnableWebMvc`自动配置就失效了；

1. `@EnableWebMvc`的核心

		@Import({DelegatingWebMvcConfiguration.class})
		public @interface EnableWebMvc {
		}

2. `DelegatingWebMvcConfiguration`
	
		@Configuration(
		proxyBeanMethods = false
		)
		public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport { 
			....
		}
3. `WebMvcAutoConfiguration`

		@Configuration(proxyBeanMethods = false)
		@ConditionalOnWebApplication(type = Type.SERVLET)
		@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
		//重点是这个注解，只有当容器中没有这个类型组件的时候该配置类才会生效
		@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
		@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
		@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
				ValidationAutoConfiguration.class })
		public class WebMvcAutoConfiguration {
			...
		}

4. `@EnableWebMvc`将`WebMvcConfigurationSupport`组件导入进来
5. 导入的`WebMvcConfigurationSupport`只是SpringMVC最基本的功能

**注意:因此一般在开发中是不用`@EnableWebMvc`开启的,除非是不用加载这么多特定的组件,但一般所定义到的组件都是所需的**

## 如何修改SpringBoot的默认配置 ##

1. SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来；
2. 在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置
3. 在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置


## RestfulCRUD ##

### 默认访问首页 ###

template文件加不是静态资源文件夹，默认是无法直接访问的，所以要添加视图映射

	@Configuration
	public class MyConfig implements WebMvcConfigurer {
	    @Override
	    public void addViewControllers(ViewControllerRegistry registry) {
	        registry.addViewController("/").setViewName("login");
	        registry.addViewController("index").setViewName("login");
	        registry.addViewController("index.html").setViewName("login");
	    }
	
	}

### 国际化 ###

1. 编写国际化配置文件
2. 使用ResourceBundleMessageSource管理国际化资源文件
3. 在页面使用fmt:message取出国际化内容

步骤:

1. 编写国际化配置文件，抽取页面需要显示的国际化消息

	![](http://120.77.237.175:9080/photos/springboot/42.jpg)

2. SpringBoot自动配置好了管理国际化资源文件的组件

		@Configuration(proxyBeanMethods = false)
		@ConditionalOnMissingBean(name = AbstractApplicationContext.MESSAGE_SOURCE_BEAN_NAME, search = SearchStrategy.CURRENT)
		@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
		@Conditional(ResourceBundleCondition.class)
		@EnableConfigurationProperties
		public class MessageSourceAutoConfiguration {
		
				private static final Resource[] NO_RESOURCES = {};
			
				；
				@Bean
				@ConfigurationProperties(prefix = "spring.messages")
				public MessageSourceProperties messageSourceProperties() {
					//默认如果没有配置的话是加载类路径下的messages.properties
					return new MessageSourceProperties();
				}
			
				@Bean
				public MessageSource messageSource(MessageSourceProperties properties) {
					
					ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
					 //设置国际化资源文件的基础名（去掉语言国家代码的）
					if (StringUtils.hasText(properties.getBasename())) {
						messageSource.setBasenames(StringUtils
								.commaDelimitedListToStringArray(StringUtils.trimAllWhitespace(properties.getBasename())));
					}
					if (properties.getEncoding() != null) {
						messageSource.setDefaultEncoding(properties.getEncoding().name());
					}
					messageSource.setFallbackToSystemLocale(properties.isFallbackToSystemLocale());
					Duration cacheDuration = properties.getCacheDuration();
					if (cacheDuration != null) {
						messageSource.setCacheMillis(cacheDuration.toMillis());
					}
					messageSource.setAlwaysUseMessageFormat(properties.isAlwaysUseMessageFormat());
					messageSource.setUseCodeAsDefaultMessage(properties.isUseCodeAsDefaultMessage());
					return messageSource;
				}
			.....
		}


	---
	
		public class MessageSourceProperties {
		
			//我们的配置文件可以直接放在类路径下叫messages.properties
			/**
			 * Comma-separated list of basenames (essentially a fully-qualified classpath
			 * location), each following the ResourceBundle convention with relaxed support for
			 * slash based locations. If it doesn't contain a package qualifier (such as
			 * "org.mypackage"), it will be resolved from the classpath root.
			 */
			private String basename = "messages";
	
		}

3. 在配置文件中添加国际化文件的位置和基础名

		spring.messages.basename=i18n.login

	**如果配置文件中没有配置基础名，就在类路径下找基础名为message的配置文件**

4. 将页面文字改为获取国际化配置

		<!DOCTYPE html>
		<html lang="en" xmlns:th="http://www.thymeleaf.org">
			<head>
				<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
				<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
				<meta name="description" content="">
				<meta name="author" content="">
				<title>Signin Template for Bootstrap</title>
				<!-- Bootstrap core CSS -->
				<link href="asserts/css/bootstrap.min.css" th:href="@{/webjars/bootstrap/4.4.1-1/css/bootstrap.min.css}" rel="stylesheet">
				<!-- Custom styles for this template -->
				<link href="asserts/css/signin.css" th:href="@{/asserts/css/signin.css}"  rel="stylesheet">
			</head>
		
			<body class="text-center">
				<form class="form-signin" action="dashboard.html">
					<img class="mb-4" src="asserts/img/bootstrap-solid.svg" th:src="@{/asserts/img/bootstrap-solid.svg}"  alt="" width="72" height="72">
					<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
					<label class="sr-only" th:text="#{login.username}">Username</label>
					<input type="text" class="form-control" placeholder="Username" th:placeholder="#{login.username}" required="" autofocus="">
					<label class="sr-only" th:text="#{login.password}">Password</label>
					<input type="password" class="form-control" placeholder="Password" th:placeholder="#{login.password}" required="">
					<div class="checkbox mb-3">
						<label>
		          <input type="checkbox" value="remember-me">[[#{login.remember}]]
		        </label>
					</div>
					<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
					<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
					<a class="btn btn-sm">中文</a>
					<a class="btn btn-sm">English</a>
				</form>
		
			</body>
		
		</html>


	**原理**
	
	- 国际化Locale（区域信息对象）；
	- LocaleResolver（获取区域信息对象）；
	
			@Bean
			@ConditionalOnMissingBean//前提是容器中不存在这个组件,所以使用自己的对象就要配置@Bean让这个条件不成立（实现LocaleResolver 即可）
			@ConditionalOnProperty(prefix = "spring.mvc", name = "locale")//如果在application.properties中有配置国际化就用配置文件的,没有配置就用AcceptHeaderLocaleResolver 默认request中获取
			public LocaleResolver localeResolver() {
				if (this.mvcProperties.getLocaleResolver() == WebMvcProperties.LocaleResolver.FIXED) {
					return new FixedLocaleResolver(this.mvcProperties.getLocale());
				}
				AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
				localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
				return localeResolver;
			}


​			
​	默认的就是根据请求头带来的区域信息获取Locale进行国际化
​	
​	    public Locale resolveLocale(HttpServletRequest request) {
​	        Locale defaultLocale = this.getDefaultLocale();
​	        if (defaultLocale != null && request.getHeader("Accept-Language") == null) {
​	            return defaultLocale;
​	        } else {
​	            Locale requestLocale = request.getLocale();
​	            List<Locale> supportedLocales = this.getSupportedLocales();
​	            if (!supportedLocales.isEmpty() && !supportedLocales.contains(requestLocale)) {
​	                Locale supportedLocale = this.findSupportedLocale(request, supportedLocales);
​	                if (supportedLocale != null) {
​	                    return supportedLocale;
​	                } else {
​	                    return defaultLocale != null ? defaultLocale : requestLocale;
​	                }
​	            } else {
​	                return requestLocale;
​	            }
​	        }
​	    }

5. 点击链接切换国际化

	- **HTML**
	
	  		<a class="btn btn-sm" href="?l=zh_CN">中文</a>
	   	 <a class="btn btn-sm" href="?l=en_US">English</a>

	- **实现区域信息解析器**

			public class MyLocaleResolve implements LocaleResolver {
			    @Override
			    public Locale resolveLocale(HttpServletRequest httpServletRequest) {
			        String lan = httpServletRequest.getParameter("l");
			        Locale locale = Locale.getDefault();
			        if(!StringUtils.isEmpty(lan))
			        {
			            String[] strings = lan.split("_");
			             locale = new Locale(strings[0],strings[1]);
			
			        }
			        return locale;
			
			    }
			
			    @Override
			    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {
			
			    }
			}

		- **在配置类中将其注册到容器中**


				@Configuration
				public class MyConfig implements WebMvcConfigurer {
				    @Override
				    public void addViewControllers(ViewControllerRegistry registry) {
				        // super.addViewControllers(registry);
				        //浏览器发送 /atguigu 请求来到 success
				        registry.addViewController("/").setViewName("login");
				        registry.addViewController("index").setViewName("login");
				        registry.addViewController("index.html").setViewName("login");
				    }
				
				    @Bean
				    public LocaleResolver localeResolver()
				    {
				        return new MyLocaleResolve();
				    }
				
				}


		**注意:如果没有生效，请检查@Bean的那个方法的名称是否为localeResolver**


### 登陆 ###

开发期间模板引擎页面修改以后，要实时生效

1. 禁用模板引擎的缓存

		# 禁用缓存
		spring.thymeleaf.cache=false 


2. 页面修改完成以后ctrl+f9：重新编译；

登陆错误消息的显示

	<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}" ></p>


**Controller**

	@Controller
	public class LoginController {
	
	    @RequestMapping("/user/login")
	    public String login(@RequestParam("username") String username, @RequestParam("password") String password, HttpSession session)
	    {
	        if(username.equals("admin") && password.equals("111111"))
	        {
	            session.setAttribute("user",username);
	            return "redirect:/main.html";
	        } else {
	            session.setAttribute("msg","用户名或密码错误");
	            return "redirect:/index.html";
	        }
	
	    }
	}

#### 拦截器进行登陆检查 ####

1. 实现拦截器

		/*登录检查*/
		public class LoginHandleInterceptor implements HandlerInterceptor {
		
		    //目标方法执行之前
		    @Override
		    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		
		        Object user = request.getSession().getAttribute("user");
		        if(user == null)
		        {
		            //未登陆，返回登陆页面
		            request.setAttribute("msg","没有权限登录!");
		            request.getRequestDispatcher("/index.html").forward(request,response);
		            return false;
		        } else {
		            //已登陆，放行请求
		            return true;
		        }
		
		    }
		
		    @Override
		    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		
		    }
		
		    @Override
		    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		
		    }
		}

2. 注册拦截器

		@Configuration
		public class MyConfig implements WebMvcConfigurer {
		
		    //定义不拦截路径(把静态资源的路径加入到不拦截的路径之中)
		    String[] url = {"/","/index","/index.html","/user/login","/asserts/**","/webjars/**"};
		
		    @Override
		    public void addViewControllers(ViewControllerRegistry registry) {
		        // super.addViewControllers(registry);
		        //浏览器发送 /atguigu 请求来到 success
		        registry.addViewController("/").setViewName("login");
		        registry.addViewController("index").setViewName("login");
		        registry.addViewController("index.html").setViewName("login");
		        registry.addViewController("main.html").setViewName("dashboard");
		    }
	
	
	​	
	​	    //注册拦截器
	​	    public void addInterceptors(InterceptorRegistry registry) {
	​	        //添加不拦截的路径，SpringBoot已经做好了静态资源映射，所以我们不用管
	​	        registry.addInterceptor(new LoginHandleInterceptor()).excludePathPatterns(url);
	​	    }
	​	
	​	    @Bean
	​	    public LocaleResolver localeResolver()
	​	    {
	​	        return new MyLocaleResolve();
	​	    }
	
	}
	
	**注意:在spring2.0+的版本中，只要用户自定义了拦截器，则静态资源会被拦截。但是在spring1.0+的版本中，是不会拦截静态资源的**


### CRUD-员工列表 ###

#### RestfulCRUD：CRUD满足Rest风格 ####

URI：  /资源名称/资源标识       HTTP请求方式区分对资源CRUD操作

<table>
<tr>
	<td></td>
	<td>普通CRUD（uri来区分操作）</td>
	<td>RestfulCRUD</td>
</tr>
<tr>
	<td>查询</td>
	<td>getEmp</td>
	<td>emp---GET</td>
</tr>
<tr>
	<td>添加</td>
	<td>addEmp?xxx</td>
	<td>emp---POST</td>
</tr>
<tr>
	<td>修改</td>
	<td>updateEmp?id=xxx&xxx=xx</td>
	<td>emp/{id}---PUT</td>
</tr>
<tr>
	<td>删除</td>
	<td>deleteEmp?id=1</td>
	<td>emp/{id}---DELETE</td>
</tr>

</table>

#### 请求架构 ####

<table>
<tr>
	<td></td>
	<td>请求URI</td>
	<td>请求方式</td>
</tr>
<tr>
	<td>查询所有员工</td>
	<td>emps</td>
	<td>GET</td>
</tr>
<tr>
	<td>查询某个员工(来到修改页面)</td>
	<td>emp/1</td>
	<td>GET</td>
</tr>
<tr>
	<td>来到添加页面</td>
	<td>emp</td>
	<td>GET</td>
</tr>
<tr>
	<td>添加员工</td>
	<td>emp</td>
	<td>POST</td>
</tr>
<tr>
	<td>来到修改页面（查出员工进行信息回显）</td>
	<td>emp/1</td>
	<td>GET</td>
</tr>
<tr>
	<td>修改员工</td>
	<td>emp</td>
	<td>PUT</td>
</tr>
<tr>
	<td>删除员工</td>
	<td>emp/1</td>
	<td>DELETE</td>
</tr>
</table>

#### 语法 ####

	1、抽取公共片段
	<div th:fragment="copy">
	&copy; 2011 The Good Thymes Virtual Grocery
	</div>
	
	2、引入公共片段
	<div th:insert="~{footer :: copy}"></div>
	~{templatename::selector}：模板名::选择器
	~{templatename::fragmentname}:模板名::片段名
	
	3、默认效果：
	insert的公共片段在div标签中
	如果使用th:insert等属性进行引入，可以不用写~{}：
	行内写法可以加上：[[~{}]];[(~{})]；

三种引入公共片段的th属性
	
- **th:insert**：将公共片段整个插入到声明引入的元素中
- **th:replace**：将声明引入的元素替换为公共片段
- **th:include**：将被引入的片段的内容包含进这个标签中

		<footer th:fragment="copy">
		&copy; 2011 The Good Thymes Virtual Grocery
		</footer>
		
		引入方式
		<div th:insert="footer :: copy"></div>
		<div th:replace="footer :: copy"></div>
		<div th:include="footer :: copy"></div>
		
		效果
		<div>
		    <footer>
		    &copy; 2011 The Good Thymes Virtual Grocery
		    </footer>
		</div>
		
		<footer>
		&copy; 2011 The Good Thymes Virtual Grocery
		</footer>
		
		<div>
		&copy; 2011 The Good Thymes Virtual Grocery
		</div>

#### 后台页面抽取 ####

1. 将后台主页中的顶部导航栏作为片段，在list页面引入	

	**dashboard.html**
	
		<!--头部-->
		<nav class="navbar navbar-dark sticky-top bg-dark flex-md-nowrap p-0" th:fragment="topbar">
		    <a class="navbar-brand col-sm-3 col-md-2 mr-0" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#">[[${session.user}]]</a>
		    <input class="form-control form-control-dark w-100" type="text" placeholder="Search" aria-label="Search">
		    <ul class="navbar-nav px-3">
		        <li class="nav-item text-nowrap">
		            <a class="nav-link" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#">Sign out</a>
		        </li>
		    </ul>
		</nav>
	
	**list.html**
	
		<!--引入topbar-->
		<div th:replace="~{public/bar::topbar}"></div>

2. 使用选择器的方式 抽取左侧边栏代码

	**dashboard.html**

		<!--侧边栏-->
		<nav class="col-md-2 d-none d-md-block bg-light sidebar" id="sidebar">
			<div class="sidebar-sticky">
		   		 <ul class="nav flex-column">
		.....

	**list.html**

		<div class="row">
			<!--引入侧边栏-->
			<div th:replace="public/bar::#sidebar"></div>

#### 引入片段传递参数 ####

实现点击当前项高亮

在引入代码片段的时候可以传递参数，然后在sidebar代码片段模板中判断当前点击的链接

	~{templatename::selector(变量名=值)}
	
	/*或者在定义代码片段时，定义参数*/
	<nav th:fragment="topbar(A,B)"
	/*引入时直接传递参数*/
	~{templatename::fragmentname(A值,B值)}

**bar.html**

	 <li class="nav-item">
	    <a class="nav-link active" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#" th:href="@{/main.html}" th:class="${activeUri=='main.html'?'nav-link active':'nav-link'}">
	        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-home">
	            <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
	            <polyline points="9 22 9 12 15 12 15 22"></polyline>
	        </svg>
	        Dashboard <span class="sr-only">(current)</span>
	    </a>
	</li>
	
	 <li class="nav-item">
	    <a class="nav-link" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#" th:href="@{/emps}" th:class="${activeUri=='emps'?'nav-link active':'nav-link'}">
	        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-users">
	            <path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path>
	            <circle cx="9" cy="7" r="4"></circle>
	            <path d="M23 21v-2a4 4 0 0 0-3-3.87"></path>
	            <path d="M16 3.13a4 4 0 0 1 0 7.75"></path>
	        </svg>
	        员工列表
	    </a>
	</li>

**dashboard.html**

	<div th:replace="public/bar::#sidebar(activeUri='main.html')"></div>

**list.html**

	<div th:replace="public/bar::#sidebar(activeUri='emps')"></div>

**显示员工数据，添加增删改按钮**

	<tbody>
		<tr th:each="emp:${emps}">
			<td th:text="${emp.getId()}"></td>
			<td th:text="${emp.getLastName()}"></td>
			<td th:text="${emp.getGender()}==0?'女':'男'"></td>
			<td th:text="${#dates.format(emp.getBirth(), 'yyyy/MMM/dd HH:mm')}"></td>
			<td th:text="${emp.getDepartment().getDepartmentName()}"></td>
			<td><button class="btn btn-sm btn-primary">删除</button>
				<button class="btn btn-sm btn-primary">修改</button></td>
		</tr>
	</tbody>

#### 添加员工 ####

1. 创建员工添加页面 `add.html`

		<!--引入topbar-->
		<!--引入抽取的topbar-->
		<!--模板名：会使用thymeleaf的前后缀配置规则进行解析-->
		<div th:replace="~{public/bar::topbar}"></div>
		
		<div class="container-fluid">
		    <div class="row">
		        <!--引入侧边栏-->
		
		        <div th:replace="public/bar::#sidebar(activeUri='emps')"></div>
		        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
		            <h2>添加员工</h2>
		
		            <div class="table-responsive">
		                <!--需要区分是员工修改还是添加；-->
		                <form action="" th:action="@{/emp}" method="post">
		                    <div class="form-group">
		                        <label>LastName</label>
		                        <input type="text" name="lastName" class="form-control" placeholder="zhangsan">
		                    </div>
		                    <div class="form-group">
		                        <label>Email</label>
		                        <input type="email" name="email" class="form-control" placeholder="zhangsan@atguigu.com">
		                    </div>
		                    <div class="form-group">
		                        <label>Gender</label><br/>
		                        <div class="form-check form-check-inline">
		                            <input class="form-check-input" type="radio" name="gender"  value="1">
		                            <label class="form-check-label">男</label>
		                        </div>
		                        <div class="form-check form-check-inline">
		                            <input class="form-check-input" type="radio" name="gender"  value="0">
		                            <label class="form-check-label">女</label>
		                        </div>
		                    </div>
		                    <div class="form-group">
		                        <label>department</label>
		                        <!--提交的是部门的id-->
		                        <select class="form-control" name="department.id">
		                            <option th:each="dep:${deps}" th:text="${dep.departmentName}" th:value="${dep.id}"></option>
		
		                        </select>
		                    </div>
		                    <div class="form-group">
		                        <label>Birth</label>
		                        <input type="text" name="birth" class="form-control" placeholder="zhangsan">
		                    </div>
		                    <button type="submit" class="btn btn-primary">添加</button>
		                </form>
		            </div>
		        </main>
		    </div>
		</div>

2. 点击链接跳转到添加页面

		<a class="btn btn-sm btn-success" href="" th:href="@{/emp}">员工添加</a>

3. `EmpController`添加映射方法

		   //员工添加页面
		    @GetMapping("/emp")
		    public String toAddEmpPage(Model model)
		    {
		        //来到添加页面,查出所有的部门，在页面显示
		        Collection<Department> departments = departmentDao.getDepartments();
		        model.addAttribute("deps",departments);
		        return "/emp/add";
		    }

4. 修改页面遍历添加下拉选项

	 	<!--提交的是部门的id-->
    	<select class="form-control" name="department.id">
    	    <option th:each="dep:${deps}" th:text="${dep.departmentName}" th:value="${dep.id}"></option>
		
    	</select>

5. 表单提交，添加员工

	    //员工添加
	    //SpringMVC自动将请求参数和入参对象的属性进行一一绑定；要求请求参数的名字和javaBean入参的对象里面的属性名是一样的
	    @PostMapping("/emp")
	    public String addEmp(Employee employee)
	    {
	        System.out.println(employee);
	        //保存员工
	        employeeDao.save(employee);
	        // redirect: 表示重定向到一个地址  /代表当前项目路径
	        // forward: 表示转发到一个地址
	        return "redirect:/emps";
	    }

**注意:具体转发和重定为什么可以支持return返回,可参照`org.thymeleaf.spring5.view.ThymeleafViewResolver#createView`源码**

**注意:日期格式修改,表单提交的日期格式默认必须是yyyy/MM/dd的格式，可以在配置文件中修改格式**

	#设置表单提交的时间格式
	spring.mvc.date-format=yyyy-MM-dd

#### 修改员工 ####

1. 点击按钮跳转到编辑页面

		<a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.getId()}">修改</a>

2. 添加编辑页面，表单的提交要为post方式，提供_method参数

        <form action="" th:action="@{/emp}" method="post" >
            <!--发送put请求修改员工数据-->
            <!--
            1、SpringMVC中配置HiddenHttpMethodFilter;（SpringBoot自动配置好的）
            2、页面创建一个post表单
            3、创建一个input项，name="_method";值就是我们指定的请求方式
            -->
            <input type="hidden" name="_method" th:if="${emp!=null}" value="put"/>
            <input type="hidden" name="id" th:if="${emp!=null}" th:value="${emp.id}"/>
            <div class="form-group">
                <label>LastName</label>
                <input type="text" name="lastName" class="form-control" placeholder="zhangsan" th:value="${emp!=null}?${emp.lastName}">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input type="email" name="email" class="form-control" placeholder="zhangsan@atguigu.com" th:value="${emp!=null}?${emp.email}">
            </div>
            <div class="form-group">
                <label>Gender</label><br/>
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="radio" name="gender"  value="1" th:checked="${emp!=null}?${emp.gender==1}">
                    <label class="form-check-label">男</label>
                </div>
                <div class="form-check form-check-inline">
                    <input class="form-check-input" type="radio" name="gender"  value="0" th:checked="${emp!=null}?${emp.gender==0}">
                    <label class="form-check-label">女</label>
                </div>
            </div>
            <div class="form-group">
                <label>department</label>
                <!--提交的是部门的id-->
                <select class="form-control" name="department.id">
                    <option th:each="dep:${deps}" th:text="${dep.departmentName}" th:value="${dep.id}" th:selected="${emp!=null}?${emp.department.id == dep.id}"></option>
        
                </select>
            </div>
            <div class="form-group">
                <label>Birth</label>
                <input type="text" name="birth" class="form-control" placeholder="zhangsan" th:value="${emp!=null}?${#dates.format(emp.birth,'yyyy-MM-dd')}">
            </div>
            <button type="submit" class="btn btn-primary">添加</button>
        </form>

3. Controller转发到编辑页面，回显员工信息

	    //来到修改页面，查出当前员工，在页面回显
	    @GetMapping("/emp/{id}")
	    public String toEditEmpPage(@PathVariable("id") Integer id,Model model)
	    {
	        //页面要显示所有的部门列表
	        Collection<Department> departments = departmentDao.getDepartments();
	        Employee employee = employeeDao.get(id);
	        model.addAttribute("emp",employee);
	        model.addAttribute("deps",departments);
	        //回到修改页面(add是一个修改添加二合一的页面);
	        return "/emp/add";
	    }

4. 提交表单修改员工信息

		 //员工修改；需要提交员工id；
	    @PutMapping("/emp")
	    public String editEmp(Employee employee)
	    {
	        employeeDao.save(employee);
	        System.out.println(employee);
	        return "redirect:/emps";
	    }


#### 删除员工 ####

1. 点击删除提交发出delete请求

		//员工删除
		@DeleteMapping("/emp/{id}")
		public String deleteEmp(@PathVariable("id") Integer id)
		{
		    employeeDao.delete(id);
		    return "redirect:/emps";
		}

**注意:如果提示不支持POST请求，在确保代码无误的情况下查看是否配置启动HiddenHttpMethodFilter**

	#解决SpringBoot2.0以上,使用DELETE请求删除报不支持POST请求
	spring.mvc.hiddenmethod.filter.enabled=true

![](http://120.77.237.175:9080/photos/springboot/43.jpg)

### 错误处理机制 ###

当访问一个不存在的页面，或者程序抛出异常时

默认效果:

- 浏览器返回一个默认的错误页面， 注意看浏览器发送请求的`请求头`

	![](http://120.77.237.175:9080/photos/springboot/44.jpg)

- 其他客户端返回json数据，注意看`请求头`

	![](http://120.77.237.175:9080/photos/springboot/45.png)


查看`org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration`源码

这里是springboot错误处理的自动配置信息,给容器中添加了以下组件

1. DefaultErrorAttributes
2. BasicErrorController：处理默认/error请求
3. ErrorPageCustomizer
4. DefaultErrorViewResolver

#### ErrorPageCustomizer ####

	@Bean
	public ErrorPageCustomizer errorPageCustomizer(DispatcherServletPath dispatcherServletPath) {
		return new ErrorPageCustomizer(this.serverProperties, dispatcherServletPath);
	}

----

	private static class ErrorPageCustomizer implements ErrorPageRegistrar, Ordered {
	
		private final ServerProperties properties;
	
		private final DispatcherServletPath dispatcherServletPath;
	
		protected ErrorPageCustomizer(ServerProperties properties, DispatcherServletPath dispatcherServletPath) {
			this.properties = properties;
			this.dispatcherServletPath = dispatcherServletPath;
		}
	
		//注册错误页面
		@Override
		public void registerErrorPages(ErrorPageRegistry errorPageRegistry) {
			//getPath()获取到的是"/error"，见下图
			ErrorPage errorPage = new ErrorPage(
					this.dispatcherServletPath.getRelativePath(this.properties.getError().getPath()));
			errorPageRegistry.addErrorPages(errorPage);
		}
	
		@Override
		public int getOrder() {
			return 0;
		}
	
	}

![](http://120.77.237.175:9080/photos/springboot/46.jpg)

当请求出现错误后就会转发到/error

然后这个error请求就会被BasicErrorController处理；

#### BasicErrorController ####

	@Bean
	@ConditionalOnMissingBean(value = ErrorController.class, search = SearchStrategy.CURRENT)
	public BasicErrorController basicErrorController(ErrorAttributes errorAttributes,
			ObjectProvider<ErrorViewResolver> errorViewResolvers) {
		return new BasicErrorController(errorAttributes, this.serverProperties.getError(),
				errorViewResolvers.orderedStream().collect(Collectors.toList()));
	}

----

	/**
	  * 使用配置文件中server.error.path配置
	  * 如果server.error.path没有配置使用error.path
	  * 如果error.path也没有配置就使用/error
	  */
	@RequestMapping("${server.error.path:${error.path:/error}}")
	public class BasicErrorController extends AbstractErrorController {
		......
	
		@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)//产生html类型的数据；浏览器发送的请求来到这个方法处理
		public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
			HttpStatus status = getStatus(request);
			Map<String, Object> model = Collections
					.unmodifiableMap(getErrorAttributes(request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
			response.setStatus(status.value());
			//去哪个页面作为错误页面；包含页面地址和页面内容
			ModelAndView modelAndView = resolveErrorView(request, response, status, model);
			return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
		}
	
		//产生json数据，其他客户端来到这个方法处理
		@RequestMapping
		public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
			HttpStatus status = getStatus(request);
			//ResponseEntity的优先级高于@ResponseBody
			//在不是ResponseEntity的情况下去检查有没有@ResponseBody注解
			//如果响应类型是ResponseEntity可以不写@ResponseBody注解,写了也没有关系
			if (status == HttpStatus.NO_CONTENT) {
				return new ResponseEntity<>(status);
			}
			Map<String, Object> body = getErrorAttributes(request, isIncludeStackTrace(request, MediaType.ALL));
			return new ResponseEntity<>(body, status);
		}
	}

处理浏览器请求的方法 中，modelAndView存储到哪个页面的页面地址和页面内容数据

看一下调用的resolveErrorView方法

	protected ModelAndView resolveErrorView(HttpServletRequest request, HttpServletResponse response, HttpStatus status,
			Map<String, Object> model) {
		for (ErrorViewResolver resolver : this.errorViewResolvers) {
			 //从所有的ErrorViewResolver得到ModelAndView
			ModelAndView modelAndView = resolver.resolveErrorView(request, status, model);
			if (modelAndView != null) {
				return modelAndView;
			}
		}
		return null;
	}

ErrorViewResolver从哪里来的呢？

已经在容器中注册了一个DefaultErrorViewResolver

#### DefaultErrorViewResolver ####


	@Configuration(proxyBeanMethods = false)
	static class DefaultErrorViewResolverConfiguration {
	
		private final ApplicationContext applicationContext;
	
		private final ResourceProperties resourceProperties;
	
		DefaultErrorViewResolverConfiguration(ApplicationContext applicationContext,
				ResourceProperties resourceProperties) {
			this.applicationContext = applicationContext;
			this.resourceProperties = resourceProperties;
		}
	
		//注册默认错误视图解析器
		@Bean
		@ConditionalOnBean(DispatcherServlet.class)
		@ConditionalOnMissingBean(ErrorViewResolver.class)
		DefaultErrorViewResolver conventionErrorViewResolver() {
			return new DefaultErrorViewResolver(this.applicationContext, this.resourceProperties);
		}
	
	}

然后调用`ErrorViewResolver`的`resolveErrorView()`方法

	public class DefaultErrorViewResolver implements ErrorViewResolver, Ordered {
		...
	
		@Override
		public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
			//把状态码和model传过去获取视图
			ModelAndView modelAndView = resolve(String.valueOf(status.value()), model);
			 //上面没有获取到视图就使用把状态吗替换再再找，以4开头的替换为4xx，5开头替换为5xx，见下文（如果定制错误响应）
			if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
				modelAndView = resolve(SERIES_VIEWS.get(status.series()), model);
			}
			return modelAndView;
		}
	
		private ModelAndView resolve(String viewName, Map<String, Object> model) {
			 //默认SpringBoot可以去找到一个页面？  error/404
			String errorViewName = "error/" + viewName;
			 //模板引擎可以解析这个页面地址就用模板引擎解析
			TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName,
					this.applicationContext);
			if (provider != null) {
				//模板引擎可用的情况下返回到errorViewName指定的视图地址
				return new ModelAndView(errorViewName, model);
			}
			//模板引擎不可用，就在静态资源文件夹下找errorViewName对应的页面   error/404.html
			return resolveResource(errorViewName, model);
		}
		...
	}

如果模板引擎不可用，就调用resolveResource方法获取视图

	private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
		//获取的是静态资源文件夹
		for (String location : this.resourceProperties.getStaticLocations()) {
			try {
				Resource resource = this.applicationContext.getResource(location);
				//例：static/error.html
				resource = resource.createRelative(viewName + ".html");
				//存在则返回视图
				if (resource.exists()) {
					return new ModelAndView(new HtmlResourceView(resource), model);
				}
			}
			catch (Exception ex) {
			}
		}
		return null;
	}


#### 定制错误响应页面 ####

有模板引擎的情况下；将错误页面命名为 `错误状态码.html` 放在模板引擎文件夹里面的 error文件夹下发生此状态码的错误就会来到这里找对应的页面；

比如我们在template文件夹下创建error/404.html当浏览器请求是404错误，就会使用我们创建的404.html页面响应，如果是其他状态码错误，还是使用默认的视图，但是如果404.html没有找到就会替换成4XX.html再查找一次，看`DefaultErrorViewResolver`中的静态代码块

	static {
		Map<Series, String> views = new EnumMap<>(Series.class);
		views.put(Series.CLIENT_ERROR, "4xx");
		views.put(Series.SERVER_ERROR, "5xx");
		SERIES_VIEWS = Collections.unmodifiableMap(views);
	}
	
	.....
	
	@Override
	public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
		 //把状态码和model传过去过去视图
		ModelAndView modelAndView = resolve(String.valueOf(status.value()), model);
	  //上面没有获取到视图就把状态吗替换再找，以4开头的替换为4xx，5开头替换为5xx，见下文（如果定制错误响应）
		if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
			modelAndView = resolve(SERIES_VIEWS.get(status.series()), model);
		}
		return modelAndView;
	}

页面可以获取哪些数据?

#### DefaultErrorAttributes ####

再看一下`BasicErrorController`的`errorHtml`方法

	@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
	public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
		HttpStatus status = getStatus(request);
		Map<String, Object> model = Collections
				.unmodifiableMap(getErrorAttributes(request, isIncludeStackTrace(request, MediaType.TEXT_HTML)));
		response.setStatus(status.value());
		ModelAndView modelAndView = resolveErrorView(request, response, status, model);
		return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
	}

看一下调用的`getErrorAttributes()`方法

	protected Map<String, Object> getErrorAttributes(HttpServletRequest request, boolean includeStackTrace) {
		WebRequest webRequest = new ServletWebRequest(request);
		return this.errorAttributes.getErrorAttributes(webRequest, includeStackTrace);
	}

再看`this.errorAttributes.getErrorAttributes()`方法， `this.errorAttributes`是接口类型`ErrorAttributes`，实现类就一个`DefaultErrorAttributes`，看一下`DefaultErrorAttributes`的 `getErrorAttributes()`方法

	public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
		Map<String, Object> errorAttributes = new LinkedHashMap<>();
		errorAttributes.put("timestamp", new Date());
		addStatus(errorAttributes, webRequest);
		addErrorDetails(errorAttributes, webRequest, includeStackTrace);
		addPath(errorAttributes, webRequest);
		return errorAttributes;
	}

- timestamp：时间戳
- status：状态码
- error：错误提示
- exception：异常对象
- message：异常消息
- errors：JSR303数据校验的错误都在这里

![](http://120.77.237.175:9080/photos/springboot/47.png)

**注音:2.0以后默认是不显示exception的，需要在配置文件中开启**

原因:

![](http://120.77.237.175:9080/photos/springboot/48.jpg)

`org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration`

	@Bean
	@ConditionalOnMissingBean(value = ErrorAttributes.class, search = SearchStrategy.CURRENT)
	public DefaultErrorAttributes errorAttributes() {
		//从配置中获取
		return new DefaultErrorAttributes(this.serverProperties.getError().isIncludeException());
	}

没有模板引擎（模板引擎找不到这个错误页面），就会在静态资源文件夹下找；

如果以上都没有找到错误页面，就是默认来到SpringBoot默认的错误提示页面；

**defaultErrorView**

![](http://120.77.237.175:9080/photos/springboot/49.jpg)

![](http://120.77.237.175:9080/photos/springboot/50.jpg)

#### 如何定制错误的json数据 ####

1. 第一种方法，定义全局异常处理器类注入到容器中，捕获到异常返回json格式的数据

		public class UserNotExistException extends RuntimeException{
		
		    public UserNotExistException()
		    {
		        super("用户不存在");
		    }
		}

	----
		@ControllerAdvice
		public class MyExceptionHandler {
		
		    @ResponseBody
		    @ExceptionHandler(UserNotExistException.class)
		    public Map<String,Object> handleException(Exception e)
		    {
		        HashMap<String, Object> map = new HashMap<>();
		        map.put("code","user.notexist");
		        map.put("message",e.getMessage());
		        return map;
		    }
		}

	----

		@Controller
		public class HWErrorController {
		
		    //@ResponseBody
		    @GetMapping("/hello")
		    public String error(@RequestParam("user")String user)
		    {
		        if (user.equals("aaa"))
		        {
		            throw new UserNotExistException();
		        }
		        return "HELLO WORLD";
		    }
		}

	![](http://120.77.237.175:9080/photos/springboot/51.jpg)

	可以看到这种没有自适应效果,返回的都是JSON格式

2. 第二种方法，捕获到异常后转发到/error

		@ExceptionHandler(UserNotExistException.class)
		public String handleException(Exception e)
		{
		    HashMap<String, Object> map = new HashMap<>();
		    map.put("code","user.notexist");
		    map.put("message",e.getMessage());
		    return "forward:/error";
		}

	但这样异常被我们捕获然后转发，显示的状态码就是200，跳转到指定错误页面,所以在转发之前还要设置一下状态码

	    @ExceptionHandler(UserNotExistException.class)
	    public String handleException(Exception e, HttpServletRequest request)
	    {
	        HashMap<String, Object> map = new HashMap<>();
	        map.put("code","user.notexist");
	        map.put("message",e.getMessage());
	        //传入我们自己的错误状态码  4xx 5xx，否则就不会进入定制错误页面的解析流程
	        /**
	         * Integer statusCode = (Integer) request
	         .getAttribute("javax.servlet.error.status_code");
	         */
	        request.setAttribute("javax.servlet.error.status_code",500);
	        return "forward:/error";
	    }

	但是设置的数据就没有用了，只能使用默认的
3. 将我们的定制数据携带出去

	上面我们已经知道数据的来源是调用DefaultErrorAttributes的getErrorAttributes方法得到的，而这个DefaultErrorAttributes是在ErrorMvcAutoConfiguration配置类中注册的，并且注册之前会检查容器中是否已经拥有

		@Bean
		@ConditionalOnMissingBean(value = ErrorAttributes.class, search = SearchStrategy.CURRENT)
		public DefaultErrorAttributes errorAttributes() {
		    //从配置中获取
		    return new DefaultErrorAttributes(this.serverProperties.getError().isIncludeException());
		}

	所以我们可以只要实现ErrorAttributes接口或者继承DefaultErrorAttributes类，然后注册到容器中就行了

	出现错误以后，会来到/error请求，会被BasicErrorController处理，响应出去可以获取的数据是由getErrorAttributes得到的（是AbstractErrorController（ErrorController）规定的方法）；

	​	1. 完全来编写一个ErrorController的实现类【或者是编写AbstractErrorController的子类】，放在容器中；
	
	​	2. 页面上能用的数据，或者是json返回能用的数据都是通过errorAttributes.getErrorAttributes得到；
	
	​			容器中DefaultErrorAttributes.getErrorAttributes()；默认进行数据处理的；
	
	
	    @ExceptionHandler(UserNotExistException.class)
	    public String handleException(Exception e, HttpServletRequest request)
	    {
	        HashMap<String, Object> map = new HashMap<>();
	        map.put("code","user.notexist");
	        map.put("message",e.getMessage());
	        //传入我们自己的错误状态码  4xx 5xx，否则就不会进入定制错误页面的解析流程
	        /**
	         * Integer statusCode = (Integer) request
	         .getAttribute("javax.servlet.error.status_code");
	         */
	        request.setAttribute("javax.servlet.error.status_code",500);
	        request.setAttribute("ext",map);
	        return "forward:/error";
	    }
	
	------
	
		//给容器中加入我们自己定义的ErrorAttributes
		@Component
		public class MyErrorAttributes extends DefaultErrorAttributes {
		
		    //返回值的map就是页面和json能获取的所有字段
		    @Override
		    public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
		        //调用父类的方法获取默认的数据
		        Map<String, Object> map = super.getErrorAttributes(webRequest, includeStackTrace);
		        //从request域从获取到自定义数据
		        Map<String, Object> ext =  (Map<String, Object>)webRequest.getAttribute("ext", RequestAttributes.SCOPE_REQUEST);
		        map.putAll(ext);
		        return map;
		    }
		}


	最终的效果：响应是自适应的，可以通过定制ErrorAttributes改变需要返回的内容
	
	![](http://120.77.237.175:9080/photos/springboot/53.jpg)


### 配置嵌入式Servlet容器 ###
SpringBoot默认使用Tomcat作为嵌入式的Servlet容器

![](http://120.77.237.175:9080/photos/springboot/54.jpg)

#### 如何定制和修改Servlet容器的相关配置 ####

1. 修改和server有关的配置（`ServerProperties`【也是WebServerFactoryCustomizer】）；
	
		server.port=8081
		server.context-path=/crud
		
		server.tomcat.uri-encoding=UTF-8
		
		//通用的Servlet容器设置
		server.xxx
		//Tomcat的设置
		server.tomcat.xxx

2. 2.0以后改为**WebServerFactoryCustomizer**：嵌入式的Servlet容器的定制器；来修改Servlet容器的配置：嵌入式的Servlet容器的定制器；来修改Servlet容器的配置

	    //配置嵌入式的Servlet容器
	    @Bean
	    public WebServerFactoryCustomizer webServerFactoryCustomizer()
	    {
	        //定制嵌入式的Servlet容器相关的规则
	        return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
	            @Override
	            public void customize(ConfigurableWebServerFactory factory) {
	                factory.setPort(8081);
	            }
	        };
	    
	    }

**注意:代码方式的配置会覆盖配置文件的配置**

#### 注册Servlet三大组件 ####

由于`SpringBoot`默认是以jar包的方式启动嵌入式的`Servlet`容器来启动`SpringBoot`的web应用，没有`web.xml`文件

注册三大组件用以下方式

- **Servlet**

		public class MyServlet  extends HttpServlet {
		    @Override
		    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		
		        doPost(req,resp);
		    }
		
		    @Override
		    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		        resp.getWriter().write("hello world!");
		    }
		}
	
	向容器中添加`ServletRegistrationBean`

		   @Bean
		public ServletRegistrationBean myServlet()
		{
		    return new ServletRegistrationBean(new MyServlet(), "/myServlet");
		
		}

- **Filter**

		public class MyFilter implements Filter {
		    @Override
		    public void init(FilterConfig filterConfig) throws ServletException {
		    }
		
		    @Override
		    public void destroy() {
		    }
		
		    @Override
		    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
		        System.out.println("doFilter.....");
		    }
		}

	向容器中添加`FilterRegistrationBean`

	    @Bean
	    public FilterRegistrationBean myFilter()
	    {
	        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
	        filterRegistrationBean.setFilter(new MyFilter());
	        filterRegistrationBean.setUrlPatterns(Arrays.asList("/myFilter"));
	        return filterRegistrationBean;
	    }

- **Listener**

		public class MyListener implements ServletContextListener {
		    @Override
		    public void contextInitialized(ServletContextEvent sce) {
		        System.out.println("contextInitialized...web启动");
		    }
		
		    @Override
		    public void contextDestroyed(ServletContextEvent sce) {
		        System.out.println("contextDestroyed....项目销毁");
		    }
		}

	向容器中注入`ServletListenerRegistrationBean`

	    @Bean
	    public ServletListenerRegistrationBean myListener()
	    {
	        return new ServletListenerRegistrationBean<>(new MyListener());
	    
	    }

#### 替换为其他嵌入式Servlet容器 ####

如果要换成其他的就把Tomcat的依赖排除掉，然后引入其他嵌入式`Servlet`容器的以来，如`Jetty`，`Undertow`

默认支持：

- **Tomcat（默认使用）**

		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-web</artifactId>
			引入web模块默认就是使用嵌入式的Tomcat作为Servlet容器；
		</dependency>


- **Jetty**

		<!-- 引入web模块 -->
		 <dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-web</artifactId>
		    <exclusions>
		        <exclusion>
		            <artifactId>spring-boot-starter-tomcat</artifactId>
		            <groupId>org.springframework.boot</groupId>
		        </exclusion>
		    </exclusions>
		</dependency>
		
		<!--引入其他的Servlet容器-->
		<dependency>
		    <artifactId>spring-boot-starter-jetty</artifactId>
		    <groupId>org.springframework.boot</groupId>
		</dependency>

- **Undertow**

		<!-- 引入web模块 -->
		<dependency>
		   <groupId>org.springframework.boot</groupId>
		   <artifactId>spring-boot-starter-web</artifactId>
		   <exclusions>
		      <exclusion>
		         <artifactId>spring-boot-starter-tomcat</artifactId>
		         <groupId>org.springframework.boot</groupId>
		      </exclusion>
		   </exclusions>
		</dependency>
		
		<!--引入其他的Servlet容器-->
		<dependency>
		   <artifactId>spring-boot-starter-undertow</artifactId>
		   <groupId>org.springframework.boot</groupId>
		</dependency>


#### 嵌入式Servlet容器自动配置原理 ####

**注意:2.0不再使用`EmbeddedServletContainerAutoConfiguration`,用的是`ServletWebServerFactoryAutoConfiguration`**

**`ServletWebServerFactoryAutoConfiguration`：嵌入式的web服务器自动配置**

	@Configuration(proxyBeanMethods = false)
	@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
	@ConditionalOnClass(ServletRequest.class)
	@ConditionalOnWebApplication(type = Type.SERVLET)
	@EnableConfigurationProperties(ServerProperties.class)
	@Import({ ServletWebServerFactoryAutoConfiguration.BeanPostProcessorsRegistrar.class,
			ServletWebServerFactoryConfiguration.EmbeddedTomcat.class,
			ServletWebServerFactoryConfiguration.EmbeddedJetty.class,
			ServletWebServerFactoryConfiguration.EmbeddedUndertow.class })
	//导入BeanPostProcessorsRegistrar：Spring注解版；给容器中导入一些组件
	public class ServletWebServerFactoryAutoConfiguration {
		.....
	}

**`EmbeddedTomcat.class`**

	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass({ Servlet.class, Tomcat.class, UpgradeProtocol.class })//判断当前是否引入了Tomcat依赖
	/**
	判断当前容器没有用户自己定义ServletWebServerFactory：嵌入式的Servlet容器工厂；作用：创建嵌入式的Servlet容器
	**/
	@ConditionalOnMissingBean(value = ServletWebServerFactory.class, search = SearchStrategy.CURRENT)
	static class EmbeddedTomcat {
	
		@Bean
		TomcatServletWebServerFactory tomcatServletWebServerFactory(
				ObjectProvider<TomcatConnectorCustomizer> connectorCustomizers,
				ObjectProvider<TomcatContextCustomizer> contextCustomizers,
				ObjectProvider<TomcatProtocolHandlerCustomizer<?>> protocolHandlerCustomizers) {
			TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
			factory.getTomcatConnectorCustomizers()
					.addAll(connectorCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getTomcatContextCustomizers()
					.addAll(contextCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getTomcatProtocolHandlerCustomizers()
					.addAll(protocolHandlerCustomizers.orderedStream().collect(Collectors.toList()));
			return factory;
		}
	
	}

**`EmbeddedJetty`**

	/**
	 * Nested configuration if Jetty is being used.
	 */
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass({ Servlet.class, Server.class, Loader.class, WebAppContext.class })
	@ConditionalOnMissingBean(value = ServletWebServerFactory.class, search = SearchStrategy.CURRENT)
	static class EmbeddedJetty {
	
		@Bean
		JettyServletWebServerFactory JettyServletWebServerFactory(
				ObjectProvider<JettyServerCustomizer> serverCustomizers) {
			JettyServletWebServerFactory factory = new JettyServletWebServerFactory();
			factory.getServerCustomizers().addAll(serverCustomizers.orderedStream().collect(Collectors.toList()));
			return factory;
		}
	
	}

**`EmbeddedUndertow`**

	/**
	 * Nested configuration if Undertow is being used.
	 */
	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass({ Servlet.class, Undertow.class, SslClientAuthMode.class })
	@ConditionalOnMissingBean(value = ServletWebServerFactory.class, search = SearchStrategy.CURRENT)
	static class EmbeddedUndertow {
	
		@Bean
		UndertowServletWebServerFactory undertowServletWebServerFactory(
				ObjectProvider<UndertowDeploymentInfoCustomizer> deploymentInfoCustomizers,
				ObjectProvider<UndertowBuilderCustomizer> builderCustomizers) {
			UndertowServletWebServerFactory factory = new UndertowServletWebServerFactory();
			factory.getDeploymentInfoCustomizers()
					.addAll(deploymentInfoCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getBuilderCustomizers().addAll(builderCustomizers.orderedStream().collect(Collectors.toList()));
			return factory;
		}
	
	}


**`ServletWebServerFactory`：嵌入式的web服务器工厂**

		public interface ServletWebServerFactory {
		
			//获取嵌入式的Servlet容器
			WebServer getWebServer(ServletContextInitializer... initializers);
		
		}

其实现类

![](http://120.77.237.175:9080/photos/springboot/55.jpg)

**`WebServer`：嵌入式的web服务器实现**

![](http://120.77.237.175:9080/photos/springboot/56.jpg)

以`TomcatServletWebServerFactory`为例

	public WebServer getWebServer(ServletContextInitializer... initializers) {
		if (this.disableMBeanRegistry) {
			Registry.disableRegistry();
		}
		//创建一个Tomcat
		Tomcat tomcat = new Tomcat();
		//配置Tomcat的基本环境，（tomcat的配置都是从本类获取的，tomcat.setXXX）
		File baseDir = (this.baseDirectory != null) ? this.baseDirectory : createTempDir("tomcat");
		tomcat.setBaseDir(baseDir.getAbsolutePath());
		Connector connector = new Connector(this.protocol);
		connector.setThrowOnFailure(true);
		tomcat.getService().addConnector(connector);
		customizeConnector(connector);
		tomcat.setConnector(connector);
		tomcat.getHost().setAutoDeploy(false);
		configureEngine(tomcat.getEngine());
		for (Connector additionalConnector : this.additionalTomcatConnectors) {
			tomcat.getService().addConnector(additionalConnector);
		}
		prepareContext(tomcat.getHost(), initializers);
		//将配置好的Tomcat传入进去，返回一个WebServer；并且启动Tomcat服务器
		return getTomcatWebServer(tomcat);
	}


	protected TomcatWebServer getTomcatWebServer(Tomcat tomcat) {
		//初始化Tomcat服务器,当端口号大于0时自动启 动
		return new TomcatWebServer(tomcat, getPort() >= 0);
	}

**对嵌入式容器的配置修改是怎么生效的?**

#### 配置修改原理 ####

1. 使用配置类修改配置为何生效

	`ServletWebServerFactoryAutoConfiguration`在向容器中添加web容器时还添加了一个组件
	
	![](http://120.77.237.175:9080/photos/springboot/57.jpg)
	
	`BeanPostProcessorsRegistrar`：后置处理器注册器(也是给容器注入一些组件,可以看到下图注册了两个组件)
	
	![](http://120.77.237.175:9080/photos/springboot/58.jpg)
	
	**`WebServerFactoryCustomizerBeanPostProcessor`**
	
		public class WebServerFactoryCustomizerBeanPostProcessor implements BeanPostProcessor, BeanFactoryAware {
		
			....
			 //在Bean初始化之前
			@Override
			public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
				if (bean instanceof WebServerFactory) {
					postProcessBeforeInitialization((WebServerFactory) bean);
				}
				return bean;
			}
		
			@Override
			public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
				return bean;
			}
		
			@SuppressWarnings("unchecked")
			private void postProcessBeforeInitialization(WebServerFactory webServerFactory) {
				//获取所有的定制器，调用每一个定制器的customize方法来给Servlet容器进行属性赋值；
				LambdaSafe.callbacks(WebServerFactoryCustomizer.class, getCustomizers(), webServerFactory)
						.withLogger(WebServerFactoryCustomizerBeanPostProcessor.class)
						.invoke((customizer) -> customizer.customize(webServerFactory));
			}
				...
		}

2. 使用配置文件为何生效

	**`EmbeddedWebServerFactoryCustomizerAutoConfiguration`**
	
		@Configuration(proxyBeanMethods = false)
		@ConditionalOnWebApplication
		//把配置文件类注入进来
		@EnableConfigurationProperties(ServerProperties.class)
		public class EmbeddedWebServerFactoryCustomizerAutoConfiguration {
		
		@Configuration(proxyBeanMethods = false)
		@ConditionalOnClass({ Tomcat.class, UpgradeProtocol.class })
		public static class TomcatWebServerFactoryCustomizerConfiguration {
		
			@Bean
			public TomcatWebServerFactoryCustomizer tomcatWebServerFactoryCustomizer(Environment environment,
					ServerProperties serverProperties) {
				return new TomcatWebServerFactoryCustomizer(environment, serverProperties);
			}
		
		}


**总结**：

1. SpringBoot根据导入的依赖情况，给容器中添加相应的`XXXServletWebServerFactory`
2. 容器中某个组件要创建对象就会惊动后置处理器 `webServerFactoryCustomizerBeanPostProcessor`只要是嵌入式的是Servlet容器工厂，后置处理器就会工作；
3. 后置处理器，从容器中获取所有的WebServerFactoryCustomizer，调用定制器的定制方法给工厂添加配置

#### 嵌入式Servlet容器启动原理 ####

什么时候创建嵌入式的Servlet容器工厂？什么时候获取嵌入式的Servlet容器并启动Tomcat

以下以`EmbeddedWebServerFactoryCustomizerAutoConfiguration`的`Tomcat`启动为例

1. SpringBoot应用启动运行run方法

	![](http://120.77.237.175:9080/photos/springboot/59.jpg)

2. `createApplicationContext()`创建IOC容器对象，根据当前环境创建

	![](http://120.77.237.175:9080/photos/springboot/60.jpg)

3. `refreshContext(context);`SpringBoot刷新IOC容器【创建IOC容器对象，并初始化容器，创建容器中的每一个组件】
4. `AbstractApplicationContext`是web应用创建**ServletWebServerApplicationContext**，否则：**AnnotationConfigApplicationContext**
5. 刷新IOC容器中,web的ioc容器重写了onRefresh方法，查看`ServletWebServerApplicationContext`类的`onRefresh`方法,在方法中调用了`createWebServer()`;方法创建web容器

	![](http://120.77.237.175:9080/photos/springboot/61.jpg)

		@Override
		protected void onRefresh() {
			super.onRefresh();
			try {
				createWebServer();
			}
			catch (Throwable ex) {
				throw new ApplicationContextException("Unable to start web server", ex);
			}
		}

6. 取嵌入式的web容器工厂

	![](http://120.77.237.175:9080/photos/springboot/62.jpg)

	从ioc容器中获取ServletWebServerFactory 组件；**TomcatServletWebServerFactory**创建对象，后置处理器一看是这个对象，就获取所有的定制器来先定制Servlet容器的相关配置；

7. 接下来就是上面的上面的相关配置流程，在创建web容器工厂时会触发`webServerFactoryCustomizerBeanPostProcessor`
8. 嵌入式的Servlet容器创建对象并启动Servlet容器；
9. 嵌入式的Servlet容器启动后，再将ioc容器中剩下没有创建出的对象获取出来(Controller,Service等)；

#### 使用外置的Servlet容器 ####

嵌入式Servlet容器：应用打成可执行的jar

- 优点：简单、便携；
​- 缺点：默认不支持JSP、优化定制比较复杂

外置的Servlet容器：外面安装Tomcat---应用war包的方式打包

1. 必须创建一个war项目
2. 编写一个类继承`SpringBootServletInitializer`，并重写`configure`方法，调用参数的sources方法springboot启动类传过去然后返回

	public class ServletInitializer extends SpringBootServletInitializer {

	    @Override
	    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
	        return application.sources(SpringbootWebJspApplication.class);
	    }
	
	}

3. 把tomcat的依赖范围改为provided

		 <dependency>
	            <groupId>org.springframework.boot</groupId>
	            <artifactId>spring-boot-starter-tomcat</artifactId>
	            <scope>provided</scope>
	    </dependency>
4. project setting,可以看到当前项目下生成了`web.xml`

	![](http://120.77.237.175:9080/photos/springboot/63.jpg)

5. 在IDEA中可以配置外部Tomcat

	![](http://120.77.237.175:9080/photos/springboot/64.jpg)

6.在创建项目时使用Spring Initializr创建选择打包方式为war，1，2，3步骤会自动配置

**注意:要使用JSP时,要把页面和相应的配置写好**

![](http://120.77.237.175:9080/photos/springboot/65.jpg)

#### 原理 ####

- jar包：执行SpringBoot主类的main方法，启动ioc容器，创建嵌入式的Servlet容器
- war包：启动服务器，**服务器启动SpringBoot应用**【SpringBootServletInitializer】，启动ioc容器；

**规则**

1. 服务器启动（web应用启动）会创建当前web应用里面每一个jar包里面ServletContainerInitializer实例：
2. ServletContainerInitializer的实现放在jar包的META-INF/services文件夹下，有一个名为javax.servlet.ServletContainerInitializer的文件，内容就是ServletContainerInitializer的实现类的全类名
3. 还可以使用@HandlesTypes，在应用启动的时候加载我们感兴趣的类

**流程**

1. 启动Tomcat
2. 在spring-web-xxx.jar包中的METAINF/services下有javax.servlet.ServletContainerInitializer这个文件

		org.springframework.web.SpringServletContainerInitializer

3. `SpringServletContainerInitializer`将`@HandlesTypes(WebApplicationInitializer.class)`标注的所有这个类型的类都传入到`onStartup`方法的**`Set<Class<?>>`**；为这些`WebApplicationInitializer`类型的类创建实例

		@HandlesTypes({WebApplicationInitializer.class})
		public class SpringServletContainerInitializer implements ServletContainerInitializer {
		    public SpringServletContainerInitializer() {
		    }
		
		    public void onStartup(@Nullable Set<Class<?>> webAppInitializerClasses, ServletContext servletContext) throws ServletException {
				.....
			}
		.....
		}
4. 每一个`WebApplicationInitializer`都调用自己的`onStartup`,其实现类如下

	![](http://120.77.237.175:9080/photos/springboot/66.jpg)

5. 相当于我们的SpringBootServletInitializer的类会被创建对象，并执行onStartup方法
6. SpringBootServletInitializer实例执行onStartup的时候会createRootApplicationContext；创建容器

		protected WebApplicationContext createRootApplicationContext(ServletContext servletContext) {
			//1、创建SpringApplicationBuilder
			SpringApplicationBuilder builder = createSpringApplicationBuilder();
			builder.main(getClass());
			ApplicationContext parent = getExistingRootWebApplicationContext(servletContext);
			if (parent != null) {
				this.logger.info("Root context already created (using as parent).");
				servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, null);
				builder.initializers(new ParentContextApplicationContextInitializer(parent));
			}
			builder.initializers(new ServletContextApplicationContextInitializer(servletContext));
			builder.contextClass(AnnotationConfigServletWebServerApplicationContext.class);
			//调用configure方法，子类重写了这个方法，将SpringBoot的主程序类传入了进来
			builder = configure(builder);
			builder.listeners(new WebEnvironmentPropertySourceInitializer(servletContext));
			//使用builder创建一个Spring应用
			SpringApplication application = builder.build();
			if (application.getAllSources().isEmpty()
					&& MergedAnnotations.from(getClass(), SearchStrategy.TYPE_HIERARCHY).isPresent(Configuration.class)) {
				application.addPrimarySources(Collections.singleton(getClass()));
			}
			Assert.state(!application.getAllSources().isEmpty(),
					"No SpringApplication sources have been defined. Either override the "
							+ "configure method or add an @Configuration annotation");
			// Ensure error pages are registered
			if (this.registerErrorPageFilter) {
				application.addPrimarySources(Collections.singleton(ErrorPageFilterConfiguration.class));
			}
			//启动Spring应用
			return run(application);
		}
7. Spring的应用就启动并且创建IOC容器

		public ConfigurableApplicationContext run(String... args) {
			StopWatch stopWatch = new StopWatch();
			stopWatch.start();
			ConfigurableApplicationContext context = null;
			Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
			configureHeadlessProperty();
			SpringApplicationRunListeners listeners = getRunListeners(args);
			listeners.starting();
			try {
				ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
				ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
				configureIgnoreBeanInfo(environment);
				Banner printedBanner = printBanner(environment);
				context = createApplicationContext();
				exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
						new Class[] { ConfigurableApplicationContext.class }, context);
				prepareContext(context, environment, listeners, applicationArguments, printedBanner);
				//刷新IOC容器
				refreshContext(context);
				afterRefresh(context, applicationArguments);
				stopWatch.stop();
				if (this.logStartupInfo) {
					new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
				}
				listeners.started(context);
				callRunners(context, applicationArguments);
			}
			catch (Throwable ex) {
				handleRunFailure(context, ex, exceptionReporters, listeners);
				throw new IllegalStateException(ex);
			}
		
			try {
				listeners.running(context);
			}
			catch (Throwable ex) {
				handleRunFailure(context, ex, exceptionReporters, null);
				throw new IllegalStateException(ex);
			}
			return context;
		}


# SpringBoot与数据访问 #

## JDBC ##

**依赖**
		
   	<dependency>
   	    <groupId>org.springframework.boot</groupId>
   	    <artifactId>spring-boot-starter-jdbc</artifactId>
   	</dependency>
   	 <dependency>
   	        <groupId>mysql</groupId>
   	        <artifactId>mysql-connector-java</artifactId>
   	        <scope>runtime</scope>
   	</dependency>

**配置**

	spring:
	  datasource:
	    username: root
	    password: 123456
	    url: jdbc:mysql://120.77.237.175:9306/springboot?serverTimezone=Asia/Shanghai
	    driver-class-name: com.mysql.cj.jdbc.Driver


**测试**

	@SpringBootTest
	class SpringbootDataJdbcApplicationTests {
	
	    @Autowired
	    DataSource dataSource;
	
	    @Test
	    void contextLoads() throws SQLException {
	        System.out.println(dataSource.getClass());	//class com.zaxxer.hikari.HikariDataSource
	
	        System.out.println(dataSource.getConnection());	//HikariProxyConnection@1507604180 wrapping com.mysql.cj.jdbc.ConnectionImpl@12fcc71f
	    }
	
	}


`springboot`2.0以上默认是使用**`com.zaxxer.hikari.HikariDataSource`**作为数据源，2.0以下是用**`org.apache.tomcat.jdbc.pool.DataSource`**作为数据源；

数据源的相关配置都在DataSourceProperties里面

## 自动配置原理 ##

`jdbc`的相关配置都在**`org.springframework.boot.autoconfigure.jdbc`**包下

1. 参考**`DataSourceConfiguration`**，根据配置创建数据源，默认使用`Hikari`连接池；可以使用`spring.datasource.type`指定自定义的数据源类型；
2. springboot默认支持的连池
	- org.apache.tomcat.jdbc.pool.DataSource
	- com.zaxxer.hikari.HikariDataSource
	- org.apache.commons.dbcp2.BasicDataSource
3. 自定义数据源类型

		@Configuration(proxyBeanMethods = false)
		@ConditionalOnMissingBean(DataSource.class)
		@ConditionalOnProperty(name = "spring.datasource.type")
		static class Generic {
		
			@Bean
			DataSource dataSource(DataSourceProperties properties) {
				//使用DataSourceBuilder创建数据源，利用反射创建响应type的数据源，并且绑定相关属性
				return properties.initializeDataSourceBuilder().build();
			}
		
		}

## 启动应用执行sql ##

`SpringBoot`在创建连接池后还会运行预定义的SQL脚本文件，具体参考**`org.springframework.boot.autoconfigure.jdbc.DataSourceInitializationConfiguration`**配置类，

在该类中注册了`dataSourceInitializerPostProcessor`

**`org.springframework.boot.autoconfigure.jdbc.DataSourceInitializer`**
下面是获取schema脚本文件的方法

	void initSchema() {
		List<Resource> scripts = getScripts("spring.datasource.data", this.properties.getData(), "data");
		if (!scripts.isEmpty()) {
			if (!isEnabled()) {
				logger.debug("Initialization disabled (not running data scripts)");
				return;
			}
			String username = this.properties.getDataUsername();
			String password = this.properties.getDataPassword();
			runScripts(scripts, username, password);
		}
	}

----

	private List<Resource> getScripts(String propertyName, List<String> resources, String fallback) {
		if (resources != null) {
			return getResources(propertyName, resources, true);
		}
		String platform = this.properties.getPlatform();
		List<String> fallbackResources = new ArrayList<>();
		fallbackResources.add("classpath*:" + fallback + "-" + platform + ".sql");
		fallbackResources.add("classpath*:" + fallback + ".sql");
		return getResources(propertyName, fallbackResources, false);
	}


可以看出，如果我们没有在配置文件中配置脚本的具体位置，就会在`classpath`下找`schema-all.sql`和`schema.sql platform`获取的是`all`，`platform`可以在配置文件中修改

具体查看`createSchema()`方法和`initSchema()`方法

`initSchema()`方法获取的是`data-all.sql`，`data.sql`

我们也可以在配置文件中配置sql文件的位置

	#schema-*.sql、data-*.sql
	#默认规则：schema.sql，schema-all.sql；
	spring:
	  datasource:
	    schema:
	      - classpath:department.sql		#指定位置

**测试**

	DROP TABLE IF EXISTS `department`;
	CREATE TABLE `department` (
	  `id` int(11) NOT NULL AUTO_INCREMENT,
	  `departmentName` varchar(255) DEFAULT NULL,
	  PRIMARY KEY (`id`)
	) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

**注意:程序启动后发现表并没有被创建,通过看DataSourceInitializer源码发现**

![](http://120.77.237.175:9080/photos/springboot/67.jpg)

**默认配置**:

	private DataSourceInitializationMode initializationMode = DataSourceInitializationMode.EMBEDDED;

**修改配置**:

	spring:
	  datasource:
	    initialization-mode: always

- schema.sql：建表语句
- data.sql：插入数据


**注意：项目每次启动都会重新执行一次sql配置文件**

## 整合Druid数据源 ##

**依赖**

	 <dependency>
	    <groupId>com.alibaba</groupId>
	    <artifactId>druid-spring-boot-starter</artifactId>
	    <version>1.1.20</version>
	</dependency>

**增加配置**

	spring:
	  datasource:
		 type: com.alibaba.druid.pool.DruidDataSource  #配置Druid连接池

**测试**

	    @Test
	    void contextLoads() throws SQLException {
	        System.out.println(dataSource.getClass());	//class com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceWrapper
	
	        System.out.println(dataSource.getConnection());	//com.mysql.cj.jdbc.ConnectionImpl@34d52ecd
	    }


**增加Druid配置**

	spring:
	  datasource:
	    username: root
	    password: 123456
	    url: jdbc:mysql://120.77.237.175:9306/springboot?serverTimezone=Asia/Shanghai
	    driver-class-name: com.mysql.cj.jdbc.Driver
	    #schema-*.sql、data-*.sql
	    #默认规则：schema.sql，schema-all.sql；
	    schema:
	      - classpath:department.sql  #指定位置
	    initialization-mode: always
	
	    type: com.alibaba.druid.pool.DruidDataSource  #配置Druid连接池
	
	    druid:
	      # 连接池配置
	      # 配置初始化大小、最小、最大
	      initial-size: 1
	      min-idle: 1
	      max-active: 20
	      # 配置获取连接等待超时的时间
	      max-wait: 3000
	      validation-query: SELECT 1 FROM DUAL
	      test-on-borrow: false
	      test-on-return: false
	      test-while-idle: true
	      pool-prepared-statements: true
	      time-between-eviction-runs-millis: 60000
	      min-evictable-idle-time-millis: 300000
	      filters: stat,wall,slf4j
	      # 配置web监控,默认配置也和下面相同(除用户名密码，enabled默认false外)，其他可以不配
	      web-stat-filter:
	        enabled: true
	        url-pattern: /*
	        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*"
	      stat-view-servlet:
	        enabled: true
	        url-pattern: /druid/*
	        login-username: admin
	        login-password: root
	        allow: 127.0.0.1

可访问Druid后台监控SQL


## 整合MyBatis ##

### 依赖 ###

	 <dependency>
	    <groupId>org.mybatis.spring.boot</groupId>
	    <artifactId>mybatis-spring-boot-starter</artifactId>
	    <version>2.1.2</version>
	</dependency>

### 依赖关系 ###

![](http://120.77.237.175:9080/photos/springboot/68.jpg)

### 项目构建 ###

1. 在`resources`下创建`department.sql`和`employee.sql`，项目启动时创建表

		DROP TABLE IF EXISTS `department`;
		CREATE TABLE `department` (
		  `id` int(11) NOT NULL AUTO_INCREMENT,
		  `departmentName` varchar(255) DEFAULT NULL,
		  PRIMARY KEY (`id`)
		) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

	----

		DROP TABLE IF EXISTS `employee`;
		CREATE TABLE `employee` (
		  `id` int(11) NOT NULL AUTO_INCREMENT,
		  `lastName` varchar(255) DEFAULT NULL,
		  `email` varchar(255) DEFAULT NULL,
		  `gender` int(2) DEFAULT NULL,
		  `d_id` int(11) DEFAULT NULL,
		  PRIMARY KEY (`id`)
		) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

2. 添加Deparment和Employee实体类

		public class Department {
		    private Integer id;
		    private String departmentName;
		
		    public Department(Integer id, String departmentName) {
		        this.id = id;
		        this.departmentName = departmentName;
		    }
		
		    public Department() {
		    }
		
		    public Integer getId() {
		        return id;
		    }
		
		    public void setId(Integer id) {
		        this.id = id;
		    }
		
		    public String getDepartmentName() {
		        return departmentName;
		    }
		
		    public void setDepartmentName(String departmentName) {
		        this.departmentName = departmentName;
		    }
		
		    @Override
		    public String toString() {
		        return "Department{" +
		                "id=" + id +
		                ", departmentName='" + departmentName + '\'' +
		                '}';
		    }
		}

	----


		public class Employee {
		    private Integer id;
		    private String lastName;
		    private String email;
		    private Integer gender;
		    private Integer d_id;
		
		    public Employee(Integer id, String lastName, String email, Integer gender, Integer d_id) {
		        this.id = id;
		        this.lastName = lastName;
		        this.email = email;
		        this.gender = gender;
		        this.d_id = d_id;
		    }
		
		    public Employee() {
		    }
		
		    public Integer getId() {
		        return id;
		    }
		
		    public void setId(Integer id) {
		        this.id = id;
		    }
		
		    public String getLastName() {
		        return lastName;
		    }
		
		    public void setLastName(String lastName) {
		        this.lastName = lastName;
		    }
		
		    public String getEmail() {
		        return email;
		    }
		
		    public void setEmail(String email) {
		        this.email = email;
		    }
		
		    public Integer getGender() {
		        return gender;
		    }
		
		    public void setGender(Integer gender) {
		        this.gender = gender;
		    }
		
		    public Integer getD_id() {
		        return d_id;
		    }
		
		    public void setD_id(Integer d_id) {
		        this.d_id = d_id;
		    }
		
		    @Override
		    public String toString() {
		        return "Employee{" +
		                "id=" + id +
		                ", lastName='" + lastName + '\'' +
		                ", email='" + email + '\'' +
		                ", gender=" + gender +
		                ", d_id=" + d_id +
		                '}';
		    }

3. 配置数据源相关属性(可见前一节Druid)

### Mybatis增删改查(注解版) ###

**Mapper**

	@Mapper
	public interface DepartmentMapper {
	
	    @Select("select * from department where id =#{id}")
	    public Department getById(Integer id);


​		
​	    @Options(useGeneratedKeys = true,keyProperty = "id")	//指定自增Key,添加数据后可返回其字段值
​	    @Insert("insert into department (departmentName) values (#{departmentName})")
​	    public int add(Department department);
​	
​	    @Update("update department set departmentName = #{departmentName} where id = #{id}")
​	    public  int update(Department department);
​	
​	    @Delete("delete department where id = #{id}")
​	    public int delete(Integer id);
​	}

**Controller**

	@RestController
	public class DeptController {
	
	    @Autowired
	    private DepartmentMapper departmentMapper;


​	
​	    @GetMapping("/get/{id}")
​	    public Department getDept(@PathVariable("id") Integer id){
​	        return departmentMapper.getById(id);
​	    }
​	
​	    @GetMapping("/add")
​	    public Department addDept(Department department)
​	    {
​	         departmentMapper.add(department);
​	         return department;
​	    }
​	}

**测试**:

	http://localhost:8080/dept/4						//{"id":4,"departmentName":"aaa"}
	http://localhost:8080/dept?departmentName=aaa	//{"id":4,"departmentName":"aaa"}

### Mybatis配置 ###

**开启驼峰命名法**

	mybatis:
	  configuration:
	    map-underscore-to-camel-case: true

也可以通过向spring容器中注入**`org.mybatis.spring.boot.autoconfigure.ConfigurationCustomizer`**的方法设置`mybatis`参数

	@Configuration
	public class MybatisConfig {
	
	    @Bean
	    public ConfigurationCustomizer mybatisConfigurationCustomizer()
	    {
	        return new ConfigurationCustomizer() {
	            @Override
	            public void customize(org.apache.ibatis.session.Configuration configuration) {
	                configuration.setMapUnderscoreToCamelCase(true);
	            }
	        };
	        
	    }
	}


通过看**`org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration`**发现

![](http://120.77.237.175:9080/photos/springboot/69.jpg)

![](http://120.77.237.175:9080/photos/springboot/70.jpg)

### Mapper扫描 ###

使用`@mapper`注解的类可以被扫描到容器中，但是每个`Mapper`都要加上这个注解就是一个繁琐的工作，能不能直接扫描某个包下的所有`Mapper`接口呢，当然可以，在springboot启动类上加上`@MapperScan`

	@MapperScan("com.springboot.data.mybatis.springbootdatamybatis.mapper")
	@SpringBootApplication
	public class SpringbootDataMybatisApplication {
	
	    public static void main(String[] args) {
	        SpringApplication.run(SpringbootDataMybatisApplication.class, args);
	    }
	
	}


### 使用xml配置文件 ###

1. 创建`mybatis`全局配置文件

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	    <settings>
			 <!--启驼峰命名自动映射-->
	        <setting name="mapUnderscoreToCamelCase" value="true"/>
	    </settings>
	</configuration>

2. 创建`EmployeeMapper`接口

	public interface EmployeeMapper {

	    public List<Employee> getAll();
	    
	    public Employee getById(Integer id);
	}

3. 创建`EmployeeMapper.xml`映射文件

		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE mapper
		        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
		        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
		<mapper namespace="com.springboot.data.mybatis.springbootdatamybatis.mapper.EmployeeMapper">
		    <select id="getById" resultType="com.springboot.data.mybatis.springbootdatamybatis.entity.Employee">
		        select * from employee where id = #{id}
		  </select>
		</mapper>

4. 配置文件(`application.yaml`)中指定配置文件和映射文件的位置

		mybatis:
		  config-location: classpath:/mybatis/mybatis.xml #指定全局配置文件的位置
		  mapper-locations: classpath:/mybatis/mapper/*.xml #指定sql映射文件的位置

5. 创建`EmpController`

		@RestController
		public class EmpController {
		
		    @Autowired
		    private EmployeeMapper employeeMapper;
		
		    @GetMapping("/emp/{id}")
		    public Employee getEmpById(@PathVariable("id") Integer id){
		        return employeeMapper.getById(id);
				/**
					{"id":1,"lastName":"张三","email":"test@test.com","gender":1,"d_id":1}****
				**/
		    }
		}

# 整合SpringData JPA #


![](http://120.77.237.175:9080/photos/springboot/71.png)

**依赖**

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

1. 编写一个实体类（bean）和数据表进行映射，并且配置好映射关系

		//使用JPA注解配置映射关系
		@Entity //告诉JPA这是一个实体类（和数据表映射的类）
		@Table(name = "user")   //@Table来指定和哪个数据表对应;如果省略默认表名就是user；
		public class User {
		
		    @Id //这是一个主键
		    @GeneratedValue(strategy = GenerationType.IDENTITY) //这是和数据表对应的一个列
		    private Integer id;
		
		    @Column(name="last_name",length = 50)   //这是和数据表对应的一个列
		    private String lastName;
		
		    @Column //省略默认列名就是属性名
		    private String email;
		
			......
		}

2. DAO

		/**
		 * 继承JpaRepository来完成对数据库的操作
		 * 泛型是（实体类，主键）
		 */
		public interface UserRepository extends JpaRepository<User,Integer> {
		}

3. 配置文件

		spring:
		  datasource:
		    username: root
		    password: 123456
		    url: jdbc:mysql://120.77.237.175:9306/springboot?serverTimezone=Asia/Shanghai
		    driver-class-name: com.mysql.cj.jdbc.Driver
		  jpa:
		    hibernate:
		      ddl-auto: update
		    show-sql: true

4. Contoller

		@RestController
		public class UserController {
		
		@Autowired
		    private UserRepository userRepository;
		
		    @GetMapping("/user/{id}")
		    public User getUserById(@PathVariable("id") Integer id){
		        Optional<User> user = userRepository.findById(id);
		        return user.get();
				/**查询结果:
					{"id":1,"lastName":"李四","email":"test@test.com"}
				**/
		
				/**SQL打印:
					Hibernate: select user0_.id as id1_0_0_, user0_.email as email2_0_0_, user0_.last_name as last_nam3_0_0_ from user user0_ where user0_.id=?
				**/
		    }
		}

# 启动配置原理 #

![](http://120.77.237.175:9080/photos/springboot/72.png)

几个重要的事件回调机制,配置在META-INF/spring.factories

- **ApplicationContextInitializer**

- **SpringApplicationRunListener**

只需要放在ioc容器中

- **ApplicationRunner**

- **CommandLineRunner**

## 启动流程 ##

	 public static void main(String[] args) {
		//xxx.class：主配置类，（可以传多个）
	    SpringApplication.run(SpringbootStarterApplication.class, args);
	}

1. 从`run`方法开始，创建**`SpringApplication`**，然后再调用`run`方法

		/**(可配置的应用程序上下文)**/
		public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
			//调用下面的run方法
		    return run(new Class[]{primarySource}, args);
		}
		
		public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
		    return (new SpringApplication(primarySources)).run(args);
		}

2. 创建**`SpringApplication`**

		public SpringApplication(Class<?>... primarySources) {
			this(null, primarySources);
		}
	

		@SuppressWarnings({ "unchecked", "rawtypes" })
		public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
			this.resourceLoader = resourceLoader;
			Assert.notNull(primarySources, "PrimarySources must not be null");
			 //保存主配置类
			this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
			 //获取当前应用的类型，是不是web应用，见2.1
			this.webApplicationType = WebApplicationType.deduceFromClasspath();
			//从类路径下找到META‐INF/spring.factories配置的所有ApplicationContextInitializer；然后保存起来,见2.2
			setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
			//从类路径下找到META‐INF/spring.ApplicationListener；然后保存起来,原理同上
			setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
			 //从多个配置类中找到有main方法的主配置类，见下图(在调run方法的时候是可以传递多个配置类的)
			this.mainApplicationClass = deduceMainApplicationClass();
			//执行完毕，SpringApplication对象就创建出来了，返回到1处，调用SpringApplication对象的run方法,到3
		}
	2.1 判断是不是web 应用

		static WebApplicationType deduceFromClasspath() {
			if (ClassUtils.isPresent(WEBFLUX_INDICATOR_CLASS, null) && !ClassUtils.isPresent(WEBMVC_INDICATOR_CLASS, null)
					&& !ClassUtils.isPresent(JERSEY_INDICATOR_CLASS, null)) {
				return WebApplicationType.REACTIVE;
			}
			for (String className : SERVLET_INDICATOR_CLASSES) {
				if (!ClassUtils.isPresent(className, null)) {
					return WebApplicationType.NONE;
				}
			}
			return WebApplicationType.SERVLET;
		}

	2.2 `getSpringFactoriesInstances(ApplicationContextInitializer.class))`
		
		private <T> Collection<T> getSpringFactoriesInstances(Class<T> type) {
			 //调用下面重载方法，type：ApplicationContextInitializer.class
			return getSpringFactoriesInstances(type, new Class<?>[] {});
		}

	-----

		private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
			ClassLoader classLoader = getClassLoader();
			//获取key为ApplicationContextInitializer全类名的所有值，见下图2.2.1
			Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
			//根据拿到的类名集合，使用反射创建对象放到集合中返回 见下图 2.2.2
			List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
			AnnotationAwareOrderComparator.sort(instances);
			return instances;//返回到2 set
		}


	2.2.1
	
	![](http://120.77.237.175:9080/photos/springboot/73.jpg)
	
	上图`(List)loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList())`中调用重载的方法：
	
		   //把类路径下所有META‐INF/spring.factories中的配置都存储起来，并返回，见下图
		   (List)loadSpringFactories(classLoader)
	
	![](http://120.77.237.175:9080/photos/springboot/74.jpg)
	
	然后再调用**`getOrDefault(factoryTypeName, Collections.emptyList())`**方法，获取`key`为**`ApplicationContextInitializer`**类名的`value`集合
	
	![](http://120.77.237.175:9080/photos/springboot/75.jpg)
	
	2.2.2
	
		private <T> List<T> createSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes,
			ClassLoader classLoader, Object[] args, Set<String> names) {
			List<T> instances = new ArrayList<>(names.size());
			for (String name : names) {
				try {
					Class<?> instanceClass = ClassUtils.forName(name, classLoader);
					Assert.isAssignable(type, instanceClass);
					Constructor<?> constructor = instanceClass.getDeclaredConstructor(parameterTypes);
					T instance = (T) BeanUtils.instantiateClass(constructor, args);
					instances.add(instance);
				}
				catch (Throwable ex) {
					throw new IllegalArgumentException("Cannot instantiate " + type + " : " + name, ex);
				}
			}
			return instances;
		}
	
	![](http://120.77.237.175:9080/photos/springboot/76.jpg)

3. 调用SpringApplication对象的run方法

	![](http://120.77.237.175:9080/photos/springboot/77.png)

		public ConfigurableApplicationContext run(String... args) {
			StopWatch stopWatch = new StopWatch();
			stopWatch.start();
			 //声明IOC容器
			ConfigurableApplicationContext context = null;
			Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
			configureHeadlessProperty();
		   //从类路径下META‐INF/spring.factories获取SpringApplicationRunListeners，原理同2中获取ApplicationContextInitializer和ApplicationListener
			SpringApplicationRunListeners listeners = getRunListeners(args);
			//遍历上一步获取的所有SpringApplicationRunListener，调用其starting方法
			listeners.starting();
			try {
				 //封装命令行
				ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
				//准备环境，把上面获取到的listeners传过去，见3.1
				ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
				configureIgnoreBeanInfo(environment);
				 //打印Banner，就是控制台那个Spring字符画
				Banner printedBanner = printBanner(environment);
				//根据当前环境利用反射创建IOC容器,见3.2
				context = createApplicationContext();
				//从类路径下META‐INF/spring.factories获取SpringBootExceptionReporter，原理同2中获取ApplicationContextInitializer和ApplicationListener
				exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
						new Class[] { ConfigurableApplicationContext.class }, context);
				//准备IOC容器，见3.3
				prepareContext(context, environment, listeners, applicationArguments, printedBanner);
				//刷新IOC容器，可查看文章里嵌入式Servlet容器启动原理
				refreshContext(context);
				//这是一个空方法
				afterRefresh(context, applicationArguments);
				stopWatch.stop();
				if (this.logStartupInfo) {
					new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
				}
				//调用所有SpringApplicationRunListener的started方法
				listeners.started(context);
				 //见3.5 ，从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调ApplicationRunner先回调，再CommandLineRunner
				callRunners(context, applicationArguments);
			}
			catch (Throwable ex) {
				handleRunFailure(context, ex, exceptionReporters, listeners);
				throw new IllegalStateException(ex);
			}
		
			try {
				//调用所有SpringApplicationRunListener的running方法
				listeners.running(context);
			}
			catch (Throwable ex) {
				handleRunFailure(context, ex, exceptionReporters, null);
				throw new IllegalStateException(ex);
			}
			return context;
		}

	**容器创建完成，返回步骤1处，最后返回到启动类**
	
	3.1
		
		private ConfigurableEnvironment prepareEnvironment(SpringApplicationRunListeners listeners,
				ApplicationArguments applicationArguments) {
			//获取或者创建环境，有则获取，无则创建
			ConfigurableEnvironment environment = getOrCreateEnvironment();
			//配置环境
			configureEnvironment(environment, applicationArguments.getSourceArgs());
			ConfigurationPropertySources.attach(environment);
			//创建环境完成后，调用前面获取的所有SpringApplicationRunListener的environmentPrepared方法
			listeners.environmentPrepared(environment);
			bindToSpringApplication(environment);
			if (!this.isCustomEnvironment) {
				environment = new EnvironmentConverter(getClassLoader()).convertEnvironmentIfNecessary(environment,
						deduceEnvironmentClass());
			}
			ConfigurationPropertySources.attach(environment);
			//将创建好的environment返回
			return environment;
		}

	3.2

		protected ConfigurableApplicationContext createApplicationContext() {
		Class<?> contextClass = this.applicationContextClass;
			if (contextClass == null) {
				try {
					switch (this.webApplicationType) {
					case SERVLET:
						contextClass = Class.forName(DEFAULT_SERVLET_WEB_CONTEXT_CLASS);
						break;
					case REACTIVE:
						contextClass = Class.forName(DEFAULT_REACTIVE_WEB_CONTEXT_CLASS);
						break;
					default:
						contextClass = Class.forName(DEFAULT_CONTEXT_CLASS);
					}
				}
				catch (ClassNotFoundException ex) {
					throw new IllegalStateException(
							"Unable create a default ApplicationContext, please specify an ApplicationContextClass", ex);
				}
			}
			//将创建好的IOC容器返回
			return (ConfigurableApplicationContext) BeanUtils.instantiateClass(contextClass);
		}

	3.3

		private void prepareContext(ConfigurableApplicationContext context, ConfigurableEnvironment environment,
				SpringApplicationRunListeners listeners, ApplicationArguments applicationArguments, Banner printedBanner) {
			 //将创建好的环境放到IOC容器中
			context.setEnvironment(environment);
			//注册一些组件
			postProcessApplicationContext(context);
			/获取所有的ApplicationContextInitializer调用其initialize方法，这些ApplicationContextInitializer就是在2步骤中获取的，见3.3.1
			applyInitializers(context);
			//回调所有的SpringApplicationRunListener的contextPrepared方法，这些SpringApplicationRunListeners是在步骤3中获取的
			listeners.contextPrepared(context);
			//打印日志
			if (this.logStartupInfo) {
				logStartupInfo(context.getParent() == null);
				logStartupProfileInfo(context);
			}
			// Add boot specific singleton beans
			ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
			beanFactory.registerSingleton("springApplicationArguments", applicationArguments);
			if (printedBanner != null) {
				beanFactory.registerSingleton("springBootBanner", printedBanner);
			}
			if (beanFactory instanceof DefaultListableBeanFactory) {
				((DefaultListableBeanFactory) beanFactory)
						.setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
			}
			if (this.lazyInitialization) {
				context.addBeanFactoryPostProcessor(new LazyInitializationBeanFactoryPostProcessor());
			}
			// Load the sources
			Set<Object> sources = getAllSources();
			Assert.notEmpty(sources, "Sources must not be empty");
			load(context, sources.toArray(new Object[0]));
			//回调所有的SpringApplicationRunListener的contextLoaded方法
			listeners.contextLoaded(context);
		}

	`prepareContext`方法运行完毕，返回到步骤3，执行`refreshContext`方法

	3.3.1

		protected void applyInitializers(ConfigurableApplicationContext context) {
			for (ApplicationContextInitializer initializer : getInitializers()) {
				Class<?> requiredType = GenericTypeResolver.resolveTypeArgument(initializer.getClass(),
						ApplicationContextInitializer.class);
				Assert.isInstanceOf(requiredType, context, "Unable to call initializer.");
				initializer.initialize(context);
			}
		}

	3.4

		private void callRunners(ApplicationContext context, ApplicationArguments args) {
			List<Object> runners = new ArrayList<>();
			runners.addAll(context.getBeansOfType(ApplicationRunner.class).values());
			runners.addAll(context.getBeansOfType(CommandLineRunner.class).values());
			AnnotationAwareOrderComparator.sort(runners);
			for (Object runner : new LinkedHashSet<>(runners)) {
				if (runner instanceof ApplicationRunner) {
					callRunner((ApplicationRunner) runner, args);
				}
				if (runner instanceof CommandLineRunner) {
					callRunner((CommandLineRunner) runner, args);
				}
			}
		}

## 事件监听机制 ##

1. 创建`ApplicationContextInitializer`和`SpringApplicationRunListener`的实现类

		public class TestApplicationContextInitializer implements ApplicationContextInitializer {
		    @Override
		    public void initialize(ConfigurableApplicationContext configurableApplicationContext) {
		        System.out.println("TestApplicationContextInitializer initialize..."+configurableApplicationContext);
		    }
		}

	----

		public class TestSpringApplicationRunListener implements SpringApplicationRunListener {
		@Override
		public void starting() {
		    System.out.println("TestSpringApplicationRunListener starting...");
		}
		
		@Override
		public void environmentPrepared(ConfigurableEnvironment environment) {
		    System.out.println("TestSpringApplicationRunListener environmentPrepared...");
		}
		
		@Override
		public void contextPrepared(ConfigurableApplicationContext context) {
		    System.out.println("TestSpringApplicationRunListener contextPrepared...");
		}
		
		@Override
		public void contextLoaded(ConfigurableApplicationContext context) {
		    System.out.println("TestSpringApplicationRunListener contextLoaded...");
		}
		
		@Override
		public void started(ConfigurableApplicationContext context) {
		    System.out.println("TestSpringApplicationRunListener started...");
		}
		
		@Override
		public void running(ConfigurableApplicationContext context) {
		    System.out.println("TestSpringApplicationRunListener running...");
		}
		
		@Override
		public void failed(ConfigurableApplicationContext context, Throwable exception) {
		    System.out.println("TestSpringApplicationRunListener failed...");
		}
		
		}


2. 在`META-INF/spring.factories`文件中配置

		org.springframework.boot.SpringApplicationRunListener=\ com.springboot.starter.springbootstarter.listener.TestSpringApplicationRunListener
		com.springboot.starter.springbootstarter.listener.TestSpringApplicationRunListener=\ com.springboot.starter.springbootstarter.listener.TestApplicationContextInitializer

3. 创建`ApplicationRunner`实现类和`CommandLineRunner`实现类，注入到容器中

		@Component
		public class TestApplicationRunner implements ApplicationRunner {
		    @Override
		    public void run(ApplicationArguments args) throws Exception {
		        System.out.println("TestApplicationRunner ..."+args);
		    }
		}

	----

		@Component
		public class TestCommandLineRunner implements CommandLineRunner {
		    @Override
		    public void run(String... args) throws Exception {
		        System.out.println("TestCommandLineRunner..."+ Arrays.asList(args));
		    }
		}

**注意:启动时会报错,说是没有找到带`org.springframework.boot.SpringApplication`和`String`数组类型参数的构造器，给`TestSpringApplicationRunListener`添加这样的构造器**

![](http://120.77.237.175:9080/photos/springboot/78.jpg)

	//必须有的构造器
	public TestSpringApplicationRunListener(SpringApplication application, String[] args) {
	}
![](http://120.77.237.175:9080/photos/springboot/79.jpg)

# 自定义starter #

- 启动器只用来做依赖导入
- 专门来写一个自动配置模块；
- 启动器依赖自动配置模块，项目中引入相应的starter就会引入启动器的所有传递依赖

![](http://120.77.237.175:9080/photos/springboot/80.png)

## 启动器 ##

启动器模块是一个空 JAR 文件，仅提供辅助性依赖管理，这些依赖可能用于自动 装配或者其他类库

### 命名 ###

	模块名-spring-boot-starter

### 如何编写自动配置 ###

	@Configuration  //指定这个类是一个配置类
	@ConditionalOnXXX  //在指定条件成立的情况下自动配置类生效
	@AutoConfigureAfter  //指定自动配置类的顺序
	@Bean  //给容器中添加组件
	
	@ConfigurationPropertie结合相关xxxProperties类来绑定相关的配置
	@EnableConfigurationProperties //让xxxProperties生效加入到容器中
	public class XxxxAutoConfiguration {
		.....
	}

自动配置类要能加载,将需要启动就加载的自动配置类，配置在**`META-INF/spring.factories`**

	org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
	org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
	org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\

## 测试 ##

1. 创建一个自动配置模块，和创建普通`springboot`项目一样，不需要引入其他`starter(SpringBoot-Starter-Define-Configure)`

		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
		    <modelVersion>4.0.0</modelVersion>
		    <parent>
		        <groupId>org.springframework.boot</groupId>
		        <artifactId>spring-boot-starter-parent</artifactId>
		        <version>2.2.5.RELEASE</version>
		        <relativePath/> <!-- lookup parent from repository -->
		    </parent>
		    <groupId>com.springboot.starter.define.configure</groupId>
		    <artifactId>springboot-starter-define-configure</artifactId>
		    <version>0.0.1-SNAPSHOT</version>
		    <name>springboot-starter-define-configure</name>
		    <description>Demo project for Spring Boot</description>
		    <packaging>jar</packaging>
		
		    <properties>
		        <java.version>1.8</java.version>
		    </properties>
		
		    <dependencies>
		        <!--引入spring‐boot‐starter；所有starter的基本配置-->
		        <dependency>
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-starter</artifactId>
		        </dependency>
		
		        <!--可以生成配置类提示文件-->
		        <dependency>
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-configuration-processor</artifactId>
		            <optional>true</optional>
		        </dependency>
		    </dependencies>
		
		</project>

2. 创建配置类和自动配置类

		@ConfigurationProperties(prefix = "define.hello")
		public class HelloProperties {
		
		    private String prefix;
		    private String suffix;
		
		    public String getPrefix() {
		        return prefix;
		    }
		
		    public void setPrefix(String prefix) {
		        this.prefix = prefix;
		    }
		
		    public String getSuffix() {
		        return suffix;
		    }
		
		    public void setSuffix(String suffix) {
		        this.suffix = suffix;
		    }
		}

	----

		public class HelloService {
		
		    HelloProperties helloProperties;
		
		    public HelloProperties seHelloProperties(HelloProperties helloProperties){
		        return this.helloProperties = helloProperties;
		    }
		
		    public HelloProperties getHelloProperties()
		    {
		        return this.helloProperties;
		    }
		
		    public String sayHello(String name)
		    {
		        return this.helloProperties.getPrefix() + " ---- " + name + "----"+ this.helloProperties.getSuffix();
		    }
		
		}

	---

		@Configuration
		@ConditionalOnWebApplication    //web应用才生效
		@EnableConfigurationProperties(HelloProperties.class)   //让配置类生效，(注入到容器中)
		public class SpringbootStarterDefineConfigureApplication {
		
		    @Autowired
		    HelloProperties helloProperties;
		
		    @Bean
		    public HelloService helloService()
		    {
		        HelloService helloService = new HelloService();
		        helloService.seHelloProperties(helloProperties);
		        return helloService;
		    }
		    
		}

3. 在`resources`文件夹下创建`META-INF/spring.factories`

		org.springframework.boot.autoconfigure.EnableAutoConfiguration=\ com.springboot.starter.define.configure.SpringbootStarterDefineConfigureApplication

4. 安装到本地仓库
5. 创建starter，选择maven工程即可，只是用于管理依赖，添加对AutoConfiguration模块的依赖

	![](http://120.77.237.175:9080/photos/springboot/81.jpg)

		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0"
		         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
		    <parent>
		        <artifactId>SpringBoot</artifactId>
		        <groupId>com.spring</groupId>
		        <version>1.0-SNAPSHOT</version>
		    </parent>
		    <modelVersion>4.0.0</modelVersion>
		
		    <groupId>com.springboot.starter.define</groupId>
		    <artifactId>springboot-starter-define</artifactId>
		    <version>1.0-SNAPSHOT</version>
		
		    <dependencies>
		        <dependency>
		            <groupId>com.springboot.starter.define.configure</groupId>
		            <artifactId>springboot-starter-define-configure</artifactId>
		            <version>0.0.1-SNAPSHOT</version>
		        </dependency>
		    </dependencies>
		
		</project>

6. 安装到本地仓库
7. 在`SpringBoot-Starter`进行测试，必须要web场景，因为设置是web场景才生效

		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
		    <modelVersion>4.0.0</modelVersion>
		    <parent>
		        <groupId>org.springframework.boot</groupId>
		        <artifactId>spring-boot-starter-parent</artifactId>
		        <version>2.2.7.RELEASE</version>
		        <relativePath/> <!-- lookup parent from repository -->
		    </parent>
		    <groupId>com.springboot.starter</groupId>
		    <artifactId>springboot-starter</artifactId>
		    <version>0.0.1-SNAPSHOT</version>
		    <name>springboot-starter</name>
		    <description>Demo project for Spring Boot</description>
		
		    <properties>
		        <java.version>1.8</java.version>
		    </properties>
		
		    <dependencies>
		        <dependency>
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-starter-web</artifactId>
		        </dependency>
		
		        <dependency>
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-starter-test</artifactId>
		            <scope>test</scope>
		            <exclusions>
		                <exclusion>
		                    <groupId>org.junit.vintage</groupId>
		                    <artifactId>junit-vintage-engine</artifactId>
		                </exclusion>
		            </exclusions>
		        </dependency>
		
		        <dependency>
		            <groupId>com.springboot.starter.define</groupId>
		            <artifactId>springboot-starter-define</artifactId>
		            <version>1.0-SNAPSHOT</version>
		        </dependency>
		    </dependencies>
		
		    <build>
		        <plugins>
		            <plugin>
		                <groupId>org.springframework.boot</groupId>
		                <artifactId>spring-boot-maven-plugin</artifactId>
		            </plugin>
		        </plugins>
		    </build>
		
		</project>

8. 创建`Controller`

		@RestController
		public class HelloController {
		
		    @Autowired
		    HelloService helloService;
		
		    @GetMapping("/hello")
		    public String Hello()
		    {
		        return helloService.sayHello("张三");
		    }
		}


9. 在配置文件中配置

	define.hello.prefix= 你好
	define.hello.suffix= hello

10. 启动项目访问`/hello`

		你好 ---- 张三----hello