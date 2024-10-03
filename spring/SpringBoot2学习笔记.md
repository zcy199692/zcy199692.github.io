# SpringBoot2 学习笔记

## maven的配置文件修改

修改D:\software\apache-maven-3.8.4\conf\的settings.xml文件，增加

```xml
<mirrors>
	<mirror>
		<id>nexus-aliyun</id>
		<mirrorOf>central</mirrorOf>
		<name>Nexus aliyun</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public</url>
	</mirror>
</mirrors>

<profiles>
	<profile>
		<id>jdk-1.8</id>

		<activation>
			<activeByDefault>true</activeByDefault>
			<jdk>1.8</jdk>
		</activation>

		<properties>
			<maven.compiler.source>1.8</maven.compiler.source>
			<maven.compiler.target>1.8</maven.compiler.target>
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
		</properties>
	</profile>
</profiles>
```

可以在idea的Setting中应用这个配置文件

## 创建maven工程

### 引入依赖

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.3.4.RELEASE</version>
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```

### 创建主程序

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }
}
```

### 编写业务

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String handle01(){
        return "Hello, Spring Boot 2!";
    }
}
```

注：HelloController类必须在MainApplication的包或子包下，否则无法扫描到

### 设置配置

maven工程的resource文件夹中创建application.properties文件。

### 打包部署

在pom.xml添加

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

在IDEA的Maven插件上点击运行 clean 、package，把helloworld工程项目的打包成jar包，

打包好的jar包被生成在helloworld工程项目的target文件夹内。

用cmd运行java -jar boot-01-helloworld-1.0-SNAPSHOT.jar，既可以运行helloworld工程项目。

将jar包直接在目标服务器执行即可。

## 依赖管理

- 父项目做依赖管理

```xml
依赖管理
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.3.4.RELEASE</version>
</parent>

上面项目的父项目如下：
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-dependencies</artifactId>
	<version>2.3.4.RELEASE</version>
</parent>

它几乎声明了所有开发中常用的依赖的版本号，自动版本仲裁机制

```



- 开发导入starter场景启动器：
  1.见到很多 spring-boot-starter-* ： *就某种场景
  2.只要引入starter，这个场景的所有常规需要的依赖我们都自动引入
  3.见到的 *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。

- 无需关注版本号，自动版本仲裁

  1. 引入依赖默认都可以不写版本
  2. 引入非版本仲裁的jar，要写版本号。

- 可以修改默认版本号

  1. 查看spring-boot-dependencies里面规定当前依赖的版本 用的 key。
  2. 在当前项目里面重写配置，如下面的代码。

  ```xml
  <properties>
  	<mysql.version>5.1.43</mysql.version>
  </properties>
  
  ```



IDEA快捷键：

- `ctrl + shift + alt + U`：以图的方式显示项目中依赖之间的关系。
- `alt + ins`：相当于Eclipse的 Ctrl + N，创建新类，新包等。



## 自动配置

- 自动配好Tomcat
- 自动配好SpringMVC
- 自动配好Web常见功能，如：字符编码问题
- 默认的包结构
  - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置
  - 想要改变扫描路径
    - @SpringBootApplication(scanBasePackages=“com.lun”)
    - @ComponentScan 指定扫描路径

## 注解

### @Configuration

```java
/**
 * 1、配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
 * 2、配置类本身也是组件
 * 3、proxyBeanMethods：代理bean的方法
 *      Full(proxyBeanMethods = true)（保证每个@Bean方法被调用多少次返回的组件都是单实例的）（默认）
 *      Lite(proxyBeanMethods = false)（每个@Bean方法被调用多少次返回的组件都是新创建的）
 */
@Configuration(proxyBeanMethods = false) //告诉SpringBoot这是一个配置类 == 配置文件
public class MyConfig {

    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}
```

- 配置类组件之间**无依赖关系**用Lite模式加速容器启动过程，减少判断
- 配置类组件之间**有依赖关系**，方法会被调用得到之前单实例组件，用Full模式（默认）

IDEA快捷键：

- `Alt + Ins`:生成getter，setter、构造器等代码。
- `Ctrl + Alt + B`:查看类的具体实现代码。

### @Bean、@Component、@Controller、@Service、@Repository

- @Bean：表示一个方法实例化、配置或者初始化一个Spring IoC容器管理的新对象。在@Configuration注解的配置类中的方法使用。具体使用参考https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-bean-annotation
- @Component: 自动被component扫描。 表示被注解的类会自动被component扫描。
- @Repository: 用于持久层的component，主要是数据库存储库。
- @Service: 表示被注解的类是位于业务层的业务component。
- @Controller:表明被注解的类是控制component，主要用于展现层 。

### @ComponentScan

改变扫描路径

- @SpringBootApplication(scanBasePackages=“com.lun”)
- @ComponentScan 指定扫描路径：@ComponentScan("com.lun")

### @Import

@Import({User.class, DBHelper.class})给容器中**自动创建出这两个类型的组件**、默认组件的名字就是全类名

### @Conditional条件装配

**条件装配：满足Conditional指定的条件，则进行组件注入**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210205005453173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE4NjMwMjQ=,size_16,color_FFFFFF,t_70#pic_center)

### @ImportResource导入Spring配置文件

比如，公司使用bean.xml文件生成配置bean，然而你为了省事，想继续复用bean.xml，@ImportResource粉墨登场。

```java
@ImportResource("classpath:beans.xml")
public class MyConfig {
...
}
```

### @ConfigurationProperties配置绑定

Spring Boot一种配置配置绑定：

@ConfigurationProperties + @Component

假设有配置文件application.properties

```yaml
mycar.brand=BYD
mycar.price=100000
```

只有在容器中的组件，才会拥有SpringBoot提供的强大功能

```java
@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {
...
}
```

Spring Boot另一种配置配置绑定：

@EnableConfigurationProperties + @ConfigurationProperties

1. 开启Car配置绑定功能
2. 把这个Car这个组件自动注册到容器中

```java
@EnableConfigurationProperties(Car.class)
public class MyConfig {
...
}

@ConfigurationProperties(prefix = "mycar")
public class Car {
...
}
```

## Lombok

Lombok用标签方式代替构造器、getter/setter、toString()等鸡肋代码。

spring boot已经管理Lombok。引入依赖：

```xml
 <dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
</dependency>
```

IDEA中File->Settings->Plugins，搜索安装Lombok插件。

```java
@NoArgsConstructor
//@AllArgsConstructor
@Data
@ToString
@EqualsAndHashCode
public class User {

    private String name;
    private Integer age;

    private Pet pet;

    public User(String name,Integer age){
        this.name = name;
        this.age = age;
    }
}
```

## dev-tools

添加依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

在IDEA中，项目修改以后：Ctrl+F9进行Build，dev-tools可以自动重启进行更新。

## Spring Initailizr

在IDEA中，菜单栏New -> Project -> Spring Initailizr。

帮我们创好项目结构，引入依赖，创建好主程序类。



## web场景

### 静态资源规则和定制化

只要静态资源放在类路径下： called /static (or /public or /resources or /META-INF/resources

访问 ： 当前项目根路径/ + 静态资源名

原理： 静态映射/**。

请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面。

也可以改变默认的静态资源路径，/static，/public,/resources, /META-INF/resources失效

```yaml
resources:
  static-locations: [classpath:/haha/]
```

静态资源访问前缀：

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
```

当前项目 + static-path-pattern + 静态资源名 = 静态资源文件夹下找

### 欢迎页

静态资源路径下 index.html

注：可以配置静态资源路径，但是不可以配置静态资源的访问前缀，否则导致 index.html不能被默认访问

### 自定义Favicon

指网页标签上的小图标。

favicon.ico 放在静态资源目录下即可。

不可以配置静态资源的访问前缀

### 请求处理

- @xxxMapping;
  - @GetMapping
  - @PostMapping
  - @PutMapping
  - @DeleteMapping

- Rest风格支持（使用**HTTP**请求方式动词来表示对资源的操作）
- 以前：
  - /getUser 获取用户
  - /deleteUser 删除用户
  - /editUser 修改用户
  - /saveUser保存用户
- 现在： /user
  - GET-获取用户
  - DELETE-删除用户
  - PUT-修改用户
  - POST-保存用户
- 核心Filter：HiddenHttpMethodFilter

Rest风格支持的用法：

- 开启页面表单的Rest功能
- 页面 form的属性method=post，隐藏域 _method=put、delete等（如果直接get或post，无需隐藏域）
- 编写请求映射

```yaml
spring:
  mvc:
    hiddenmethod:
      filter:
        enabled: true   #开启页面表单的Rest功能
```

```js
<form action="/user" method="get">
    <input value="REST-GET提交" type="submit" />
</form>

<form action="/user" method="post">
    <input value="REST-POST提交" type="submit" />
</form>

<form action="/user" method="post">
    <input name="_method" type="hidden" value="DELETE"/>
    <input value="REST-DELETE 提交" type="submit"/>
</form>

<form action="/user" method="post">
    <input name="_method" type="hidden" value="PUT" />
    <input value="REST-PUT提交"type="submit" />
<form>
```

```java
@GetMapping("/user")
//@RequestMapping(value = "/user",method = RequestMethod.GET)
public String getUser(){
    return "GET-张三";
}

@PostMapping("/user")
//@RequestMapping(value = "/user",method = RequestMethod.POST)
public String saveUser(){
    return "POST-张三";
}

@PutMapping("/user")
//@RequestMapping(value = "/user",method = RequestMethod.PUT)
public String putUser(){
    return "PUT-张三";
}

@DeleteMapping("/user")
//@RequestMapping(value = "/user",method = RequestMethod.DELETE)
public String deleteUser(){
    return "DELETE-张三";
}
```

- Rest原理（表单提交要使用REST的时候）
  - 表单提交会带上\_method=PUT
  - 请求过来被HiddenHttpMethodFilter拦截
  - 请求是否正常，并且是POST
  - 获取到\_method的值。
  - 兼容以下请求；PUT.DELETE.PATCH
    原生request（post），包装模式requesWrapper重写了getMethod方法，返回的是传入的值。
    过滤器链放行的时候用wrapper。以后的方法调用getMethod是调用requesWrapper的。

- Rest使用客户端工具。
  - 如PostMan可直接发送put、delete等方式请求。

IDEA快捷键：

- Ctrl + Alt + U : 以UML的类图展现类有哪些继承类，派生类以及实现哪些接口。
- Crtl + Alt + Shift + U : 同上，区别在于上条快捷键结果在新页展现，而本条快捷键结果在弹窗展现。
- Ctrl + H : 以树形方式展现类层次结构图。

常用注解：

- @PathVariable 路径变量
- @RequestHeader 获取请求头
- @RequestParam 获取请求参数（指问号后的参数，url?a=1&b=2）
- @CookieValue 获取Cookie值
- @RequestAttribute 获取request域属性
- @RequestBody 获取请求体[POST]

使用用例：

```java
@RestController
public class ParameterTestController {


    //  car/2/owner/zhangsan
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String,Object> getCar(@PathVariable("id") Integer id,
                                     @PathVariable("username") String name,
                                     @PathVariable Map<String,String> pv,
                                     @RequestHeader("User-Agent") String userAgent,
                                     @RequestHeader Map<String,String> header,
                                     @RequestParam("age") Integer age,
                                     @RequestParam("inters") List<String> inters,
                                     @RequestParam Map<String,String> params,
                                     @CookieValue("_ga") String _ga,
                                     @CookieValue("_ga") Cookie cookie){

        Map<String,Object> map = new HashMap<>();

//        map.put("id",id);
//        map.put("name",name);
//        map.put("pv",pv);
//        map.put("userAgent",userAgent);
//        map.put("headers",header);
        map.put("age",age);
        map.put("inters",inters);
        map.put("params",params);
        map.put("_ga",_ga);
        System.out.println(cookie.getName()+"===>"+cookie.getValue());
        return map;
    }


    @PostMapping("/save")
    public Map postMethod(@RequestBody String content){
        Map<String,Object> map = new HashMap<>();
        map.put("content",content);
        return map;
    }
}
```

@RequestAttribute用例：

```java
@Controller
public class RequestController {

    @GetMapping("/goto")
    public String goToPage(HttpServletRequest request){

        request.setAttribute("msg","成功了...");
        request.setAttribute("code",200);
        return "forward:/success";  //转发到  /success请求
    }

    @GetMapping("/params")
    public String testParam(Map<String,Object> map,
                            Model model,
                            HttpServletRequest request,
                            HttpServletResponse response){
        map.put("hello","world666");
        model.addAttribute("world","hello666");
        request.setAttribute("message","HelloWorld");

        Cookie cookie = new Cookie("c1","v1");
        response.addCookie(cookie);
        return "forward:/success";
    }

    ///<-----------------主角@RequestAttribute在这个方法
    @ResponseBody
    @GetMapping("/success")
    public Map success(@RequestAttribute(value = "msg",required = false) String msg,
                       @RequestAttribute(value = "code",required = false)Integer code,
                       HttpServletRequest request){
        Object msg1 = request.getAttribute("msg");

        Map<String,Object> map = new HashMap<>();
        Object hello = request.getAttribute("hello");
        Object world = request.getAttribute("world");
        Object message = request.getAttribute("message");

        map.put("reqMethod_msg",msg1);
        map.put("annotation_msg",msg);
        map.put("hello",hello);
        map.put("world",world);
        map.put("message",message);

        return map;
    }
}
```

## 单元测试

- 使用添加JUnit 5，添加对应的starter：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

- Spring的JUnit 5的基本单元测试模板

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;//注意不是org.junit.Test（这是JUnit4版本的）
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class SpringBootApplicationTests {

    @Autowired
    private Component component;
    
    @Test
    //@Transactional 标注后连接数据库有回滚功能
    public void contextLoads() {
		Assertions.assertEquals(5, component.getFive());
    }
}
```

### 常用测试注解

- @Test：表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
- @ParameterizedTest：表示方法是参数化测试。
- @RepeatedTest：表示方法可重复执行。
- @DisplayName：为测试类或者测试方法设置展示名称。
- @BeforeEach：表示在每个单元测试之前执行。
- @AfterEach：表示在每个单元测试之后执行。
- @BeforeAll：表示在所有单元测试之前执行。
- @AfterAll：表示在所有单元测试之后执行。
- @Tag：表示单元测试类别，类似于JUnit4中的@Categories。
- @Disabled：表示测试类或测试方法不执行，类似于JUnit4中的@Ignore。
- @Timeout：表示测试方法运行如果超过了指定时间将会返回错误。
- @ExtendWith：为测试类或测试方法提供扩展类引用。
  

```java
import org.junit.jupiter.api.*;

@DisplayName("junit5功能测试类")
public class Junit5Test {


    @DisplayName("测试displayname注解")
    @Test
    void testDisplayName() {
        System.out.println(1);
        System.out.println(jdbcTemplate);
    }
    
    @ParameterizedTest
    @ValueSource(strings = { "racecar", "radar", "able was I ere I saw elba" })
    void palindromes(String candidate) {
        assertTrue(StringUtils.isPalindrome(candidate));
    }
    

    @Disabled
    @DisplayName("测试方法2")
    @Test
    void test2() {
        System.out.println(2);
    }

    @RepeatedTest(5)
    @Test
    void test3() {
        System.out.println(5);
    }

    /**
     * 规定方法超时时间。超出时间测试出异常
     *
     * @throws InterruptedException
     */
    @Timeout(value = 500, unit = TimeUnit.MILLISECONDS)
    @Test
    void testTimeout() throws InterruptedException {
        Thread.sleep(600);
    }


    @BeforeEach
    void testBeforeEach() {
        System.out.println("测试就要开始了...");
    }

    @AfterEach
    void testAfterEach() {
        System.out.println("测试结束了...");
    }

    @BeforeAll
    static void testBeforeAll() {
        System.out.println("所有测试就要开始了...");
    }

    @AfterAll
    static void testAfterAll() {
        System.out.println("所有测试以及结束了...");

    }

}

```

### 断言

断言Assertion是测试方法中的核心部分，用来对测试需要满足的条件进行验证。这些断言方法都是org.junit.jupiter.api.Assertions的静态方法。检查业务逻辑返回的数据是否合理。所有的测试运行结束以后，会有一个详细的测试报告。

- 简单断言：用来对单个值进行简单的验证

assertEquals	判断两个对象或两个原始类型是否相等
assertNotEquals	判断两个对象或两个原始类型是否不相等
assertSame	判断两个对象引用是否指向同一个对象
assertNotSame	判断两个对象引用是否指向不同的对象
assertTrue	判断给定的布尔值是否为 true
assertFalse	判断给定的布尔值是否为 false
assertNull	判断给定的对象引用是否为 null
assertNotNull	判断给定的对象引用是否不为 null

- 数组断言：通过 assertArrayEquals 方法来判断两个对象或原始类型的数组是否相等。
- 组合断言：`assertAll()`方法接受多个 `org.junit.jupiter.api.Executable` 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言。

```java
@Test
@DisplayName("assert all")
public void all() {
 assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
 );
}
```

- 异常断言：`Assertions.assertThrows()`，配合函数式编程就可以进行使用

```java
@Test
@DisplayName("异常测试")
public void exceptionTest() {
    ArithmeticException exception = Assertions.assertThrows(
           //扔出断言异常
            ArithmeticException.class, () -> System.out.println(1 % 0));
}
```

- 超时断言：JUnit5还提供了Assertions.assertTimeout()为测试方法设置了超时时间。

```java
@Test
@DisplayName("超时测试")
public void timeoutTest() {
    //如果测试方法时间超过1s将会异常
    Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
```

- 快速失败：通过 fail 方法直接使得测试失败

```java
@Test
@DisplayName("fail")
public void shouldFail() {
	fail("This should fail");
}
```

### 前置条件

JUnit 5 中的前置条件（assumptions【假设】）类似于断言，不同之处在于不满足的**断言assertions**会使得测试方法失败，而**不满足的前置条件只会使得测试方法的执行终止**。

前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

```java
@DisplayName("前置条件")
public class AssumptionsTest {
    private final String environment = "DEV";

    @Test
    @DisplayName("simple")
    public void simpleAssume() {
        assumeTrue(Objects.equals(this.environment, "DEV"));
        assumeFalse(() -> Objects.equals(this.environment, "PROD"));
    }

    @Test
    @DisplayName("assume then do")
    public void assumeThenDo() {
        assumingThat(
            Objects.equals(this.environment, "DEV"),
            () -> System.out.println("In DEV")
        );
    }
}
```

assumeTrue 和 assumFalse 确保给定的条件为 true 或 false，不满足条件会使得测试执行终止。

assumingThat 的参数是表示条件的布尔值和对应的 Executable 接口的实现对象。只有条件满足时，Executable 对象才会被执行；当条件不满足时，测试执行并不会终止。
