# SpringMVC 

## 一、Spring MVC简介

​	Spring 为展现层提供的基于 MVC 设计理念的优秀的 ,Web 框架，是目前最主流的 MVC 框架之一,Spring MVC 通过一套 MVC 注解，让 POJO 成为处理请求的控制器，而无须实现任何接口,支持 REST 风格的 URL 请求;

### 1.1 前期准备

**1、添加相关依赖**

```xml
<dependencies>
    <!--基础依赖-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>2.3.1</version>
        <scope>provided</scope>
    </dependency>

    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.1.1</version>
    </dependency>


    <!--    SpringMVC 相关依赖-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${Spring-version}</version>
        <scope>compile</scope>
    </dependency>
    
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

2、配置Spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--民名空间的引入需要特别注意-->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
      http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--    配置自定扫描包-->
    <context:component-scan base-package="com.restart"/>

    <!--    配置视图解析器：如何把handler 方法的返回值解析为是假的物理视图-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>


</beans>
```

3、配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <!-- 配置 DispatcherServlet -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置 DispatcherServlet 的一个初始化参数: 配置 SpringMVC 配置文件的位置和名称 -->
        <!--
            实际上也可以不通过 contextConfigLocation 来配置 SpringMVC 的配置文件, 而使用默认的.
            默认的配置文件为: /WEB-INF/<servlet-name>-servlet.xml
        -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>

        <!--    表示记载配置文件时，就载入相关的类-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

4、操作类

```java
@Controller
public class HelloWord {
    /**
     *1 、 使用@requestMapping配置请求处理器
     * 2、返回值会通过视图解析器，解析为实际视图，对于，InternalResourceViewResolver
     * 通过 prefix+returnval+后缀 这样得到实际视图
     * /WEB-INF/views/success.jsp
     * @return
     */
    @RequestMapping("/helloword")
    public String hello(){
        System.out.println("hello word");
        return "success";
    }
}
```

## 二、RequestMapping 

### 2.1 RerquestMapping 相关处理	

Spring MVC 使用 @RequestMapping 注解为控制器指定可以处理哪些 URL 请求;

在控制器的类定义及方法定义处都可标注;

@RequestMapping

- 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录;

- 方法处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录;

```java
@RequestMapping
@Controller
public class SpringMvcTest {
    /**
     * requestMapping 除了可以修饰方法还可以修饰类
     * 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录;
     * 方法定义处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录;
     * @return
     */
    @RequestMapping("/testrequestMapping")
    public String testRequestMaping(){
        System.out.println("success");
        return "success";
    }
}
```

### 2.2 RequestMapping 参数详解

```java
@RequestMapping("/springmvc")
@Controller
public class SpringMvcTest {
    /**
     * requestMapping 除了可以修饰方法还可以修饰类
     * 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录;
     * 方法定义处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录;
     * @return
     */
    @RequestMapping("/testmapping")
    public String testRequestMaping(){
        System.out.println("success");
        return "success";
    }

    /**
     * 使用method 指定请求的参数
     * @return
     */
    @RequestMapping(value = "/methodtest",method = RequestMethod.GET)
    public String testRequestMapingMethod(){
        System.out.println("method test");
        return "success";
    }

    /**
     *–
     * !param1: 表示请求不能包含名为 param1 的请求参数 –
     * param1 != value1: 表示请求包含名为 param1 的请求参数，但其– 值
     * 不能为 value1
     * {“param1=value1”, “param2”}: 请求必须包含名为 param1 和param2 –
     * 的两个请求参数，且 param1 参数的值必须为 value1
     * 
     * header 与Params 的用法相似
     * @return
     */
    @RequestMapping(value = "/paramtest",method = RequestMethod.GET,params={"username","age!=10"})
    public String testRequest(){
        System.out.println("method test");
        return "success";
    }


}
```

### 2.3  @PathVariable

```java
/**
 * 可以映射url中的占位符到目标方法中；
 *正因为这种特性，使得SpringMVC 支持REST 风格；
 *
 * REST Representional State Trandfer ,资源表现层状态化，
 * 是目前鼻尖流行的一种互联网软件价格，她的结构清晰
 * 资源：网络上的实体，或者说网络上的具体信息，（文本，图片，）可以用一个URI 来表示
 
 * 表现层：把资源具体呈现出来的形式，叫做它的表现层 
 * （Representation）。比如，文本可以用 txt 格式表现，也可以用 HTML 格
 * 式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。
 *
 
 * 状态转化：（State Transfer）：每• 发出一个请求，就代表了客户端和服务器的一
 * 次交互过程。HTTP协议，是一个无状态协议，即所有的状态都保存在服务器
 * 端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“
 * 状态转化”（State Transfer）。而这种转化是建立在表现层之上的，所以就是 “
 * 表现层状态转化”。具体说，就是 HTTP 协议里面，四个表示操作方式的动
 * 词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET 用来获
 * 取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。
 *
 *
 * @param id
 * @return
 */
@RequestMapping(value = "/variabletest/{id}", method = RequestMethod.GET)
public String testPathVariable(@PathVariable("id") Integer id) {
    System.out.println("testPathVariable"+" "+id);
    return SUCCESS;
}
```

 * REST (Representional State Trandfer ),资源表现层状态化，是目前鼻尖流行的一种互联网软件价格，她的结构清晰
 * **资源**：网络上的实体，或者说网络上的具体信息，（文本，图片，）可以用一个URI 来表示

 * **表现层**：把资源具体呈现出来的形式，叫做它的表现层 （Representation）。比如，文本可以用 txt 格式表现，也可以用 HTML 格式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。
 * **状态转化**：（State Transfer）：每发出一个请求，就代表了客户端和服务器的一次交互过程。HTTP协议，是一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”（State Transfer）。而这种转化是建立在表现层之上的，所以就是 “表现层状态转化”。具体说，**就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。**



### 2.4 **HiddenHttpMethodFilter**

​	 **HiddenHttpMethodFilter**：浏览器 form 表单只支持 GET 与 POST 请求，而DELETE、PUT 等 method 并不支持，Spring3.0 添加了一个过滤器，可以将这些请求转换为标准的 http 方法，使得支持 GET、POST、PUT 与DELETE 请求。

```xml
<!--    配置HiddenHttpMethodFilter 过滤器，可以将post请求转换为 post请求转化为 put delete 请求-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```html
<form action="springmvc/testRest/1" method="post">
    <input type="hidden" name="_method" value="PUT"/>
    <input type="submit" value="TestRest PUT"/>
</form>
<br>

<form action="springmvc/testRest/1" method="post">
    <input type="hidden" name="_method" value="DELETE"/>
    <input type="submit" value="TestRest DELETE"/>
</form>
<br>

<form action="springmvc/testRest" method="post">
    <input type="submit" value="TestRest Post"/>
</form>
<br>
```

```java
/**
 * Rest 风格的URL
 * 以CRUD 为例：
 * 增 /order POST        POST
 * 删除  /order/1 DELETE  delete?id=1
 * 改   /order/1  PUT    update?id=1
 * 查   /order/1 GET     get?id=1
 *
 * 如何发送PUT和DELETE请求
 * 1、配置 HiddenHttpMethodFilter
 * 2、发送POST 请求
 * 3、在需要发送POST请求时携带 name="_method"的隐藏域，值为 DELETE 或者PUT
 *
 * 在SpringMVC的方法中如何得到id呢？
 * 使用@PathVariable 注解
 *
 *
 * @param id
 * @return
 */
@RequestMapping(value = "/testRest/{id}",method = RequestMethod.PUT)
public String testRestPut(@PathVariable Integer id){
    System.out.println("test restPut"+" "+id);
    return SUCCESS;
}


@RequestMapping(value = "/testRest/{id}",method = RequestMethod.DELETE)
public String testRestDelete(@PathVariable Integer id){
    System.out.println("test restDelete"+" "+id);
    return SUCCESS;
}


@RequestMapping(value = "/testRest",method = RequestMethod.POST)
public String testRest(){
    System.out.println("test restRest POST");
    return SUCCESS;
}


@RequestMapping(value = "/testRest/{id}",method = RequestMethod.GET)
public String testRest(@PathVariable Integer id){
    System.out.println("test restGet"+" "+id);
    return SUCCESS;
}
```

## 三、映射请求参数&请求参数

​	Spring MVC 通过分析处理方法的签名，将 HTTP 请求信 息绑定到处理方法的相应人参中。Spring MVC 对控制器处理方法签名的限制是很宽松的， 几乎可以按喜欢的任何方式对方法进行签名。必要 时可以对方法及方法入参标注相应的注解（@PathVariable@RequestParam、@RequestHeader 等）、Spring MVC 框架会将 HTTP 请求的信息绑定到相应的方法入参中，并根据方法的返回值类型做出相应的后续处理。

### 3.1 使用 @RequestParam 绑定请求参数值

```html
<a href="springmvc/testrequestparam?username=cen&age=12">test requestparam</a><br>
```

```java
/**
 * @RequsetParam 来映射请求参数
 * value 为请求参数的参数名
 * required 该参数，是否必须
 * defaultValue 默认值
 */
@RequestMapping(value = "/testrequestparam")
public String testRequestParam(@RequestParam(value = "username",required = false) String username,
                               @RequestParam(value = "age",required = false,defaultValue = "0") Integer age) {
    System.out.println("testrequestparam"+" "+ " username:"+username);
    System.out.println("testrequestparam"+" "+ " age:"+age);
    return SUCCESS;
}
```

### 3.2 使用 @RequestHeader 绑定请求报头的属性值

```html
<a href="springmvc/testrequestheader">test requestheader</a><br>
```

```java
/**
 * @RequestHeader 处理请求头相关信息
 */
@RequestMapping(value = "/testrequestheader")
public String testRequestHeader(@RequestHeader(value = "Accept-Language") String Accept){
    System.out.println("testRequestHeader,Accept-Language:"+Accept);
    return SUCCESS;
}
```

### 3.3使用 @CookieValue 绑定请求中的 Cookie 值

```html
<a href="springmvc/testcookievalue">test cookievalue</a><br>
```

```java
@RequestMapping(value = "/testcookievalue")
public String testCookieValue(@CookieValue(value = "JSESSIONID") String SessionID){
    System.out.println("testCookieValue:sessionId"+SessionID);
    return SUCCESS;
}
```

### 3.4 使用 POJO 对象绑定请求参数值

Spring MVC 会按请求参数名和 POJO 属性名进行自动匹配，自动为该对象填充属性值。支持级联属性。

```java
package com.restart.beans;

public class User {
    private Integer id;

    private String username;
    private String password;

    private String email;
    private int age;

    private Address address;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }
    
    @Override
    public String toString() {
        return "User [id=" + id + ", username=" + username + ", password="
                + password + ", email=" + email + ", age=" + age + "]";
    }

    public User(String username, String password, String email, int age) {
        super();
        this.username = username;
        this.password = password;
        this.email = email;
        this.age = age;
    }

    public User(Integer id, String username, String password, String email,
                int age) {
        super();
        this.id = id;
        this.username = username;
        this.password = password;
        this.email = email;
        this.age = age;
    }

    public User() {}

}

public class Address {
    private String province;
    private String city;

    public String getProvince() {
        return province;
    }

    public void setProvince(String province) {
        this.province = province;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    @Override
    public String toString() {
        return "Address [province=" + province + ", city=" + city + "]";
    }

}

```

```html
<form action="springmvc/testpojo" method="post">
            username: <input type="text" name="username"/>
    <br>
            password: <input type="password" name="password"/>
    <br>
            email: <input type="text" name="email"/>
    <br>
            age: <input type="text" name="age"/>
    <br>
            city: <input type="text" name="address.city"/>
    <br>
            province: <input type="text" name="address.province"/>
    <br>
    <input type="submit" value="Submit"/>
</form>
```

```java
/**
 *
 *SpringMVC  会按照参数名和POJO的属性进行自动的匹配
 * 自动为该对象填充属性值，并且支持级连属性
 *
 */
@RequestMapping(value = "/testpojo")
public String testPOJO(User user){
    System.out.println("test pojo:"+user);
    return SUCCESS;
}
```

### 3.5 使用 Servlet API 作为入参

- HttpServletRequest 

- HttpServletResponse 

- HttpSession 

- java.security.Principal 

- Locale 

- InputStream 

- OutputStream 

- Reader 

- Writer

## 四、处理模型数据

​	数据处理模式，是指业务处理完数据之后，将业务数据以何种形式传递，显示；如何将业务数据放在模型对象当中；

Spring MVC 提供了以下几种途径输出模型数据：

- ModelAndView: 处理方法返回值类型为 ModelAndView 时, 方法体即可通过该对象添加模型数据；
- Map 及 Model: 入参为org.springframework.ui.Model、org.springframework.ui.ModelMap 或 java.uti.Map 时，处理方法返回时，Map 中的数据会自动添加到模型中。
- @SessionAttributes: 将模型中的某个属性暂存到 HttpSession 中，以便多个请求之间可以共享这个属性
- @ModelAttribute: 方法入参标注该注解后, 入参的对象就会放到数据模型中

### 4.1 ModelAndView

​	控制器处理方法的返回值如果为 ModelAndView, 则其既 包含视图信息，也包含模型数据信息；

```java
/**
 *ModelAndView 其中可以包含，Model以及View；
 * 其中包含视图与模型信息；
 * SpringMVC  会把ModelAndView,model中的数据会把数据放到Request域中；
 */
@RequestMapping(value = "/testmodelandview")
public ModelAndView testModelAndView() {
    String viewName = SUCCESS;
    ModelAndView modelAndView = new ModelAndView(viewName);

    //添加数据到 ModelAndView中
    modelAndView.addObject("time",new Date());
    return modelAndView;
}
```

```html
<a href="springmvc/testmodelandview">test modelandview</a><br>
```

### 4.2 Map及Model

```java
/***
 *目标方法可以添加Map类型的参数；实际上可以是 Model类型，或者是 ModelAndView类型
 */
@RequestMapping(value = "/testmap")
public String testMap(Map<String,Object> map){
    map.put("names", Arrays.asList("Tom","Jerry","Mike"));
    return SUCCESS;
}
```

```html
<a href="springmvc/testmap">test map</a><br>
```

### 4.3  @SessionAttribute  注解

​	若希望在多个请求之间共用某个模型属性数据，则可以在 控制器类上标注一个 @SessionAttributes, Spring MVC 将在模型中对应的属性暂存到 HttpSession 中。

```java
/**
 *@SessionAttributes 除了可以通过属性名指定需要放到会话中的属性外，还可以通过模型属性的对象类型指定哪些模型属性需要放到会话中;
 * 
 * 注意：该注解只能放在上，而不能放在方法上
 */
@RequestMapping(value = "/testsessionattribute")
public String testSessionAttribute(Map<String,Object> map){

    User tom = new User("Tom", "123", "tom@123.com", 15);
    map.put("user",tom);
    return SUCCESS;
}
```

### 4.4 ModelAttribute 方法

![image-20210111131816349](/Users/anner/Library/Application Support/typora-user-images/image-20210111131816349.png)

在方法定义上使用 @ModelAttribute 注解：Spring MVC 在调用目标处理方法前，会先逐个调用在方法级上标注了@ModelAttribute 的方法；

在方法的入参前使用  @ModelAttribute 注解：

- 可以从隐含对象中获取隐含的模型数据中获取对象，再将请求参数 

- 绑定到对象中，再传入入参将方法入参对象添加到模型中 

```java
/**
 * 1、@ModelAttribute 标记的方法，会在每个目标方法执行志气啊被SpringMVC调用，把对象放入map 中，键为user：
 * 2、Spring MVC 从MAp中，并把表单中，并把请求参数给User对应饿属性值；
 * 3、Spring MVC把上述的对象传入目标方法的参数。
 
 注意：
 在@modelAttribute 修饰的方法中，放入Map时需要和目标方法传入的参数的一个字母小写的字符串一致；
 */

@ModelAttribute
public void getUser(@RequestParam(value="id",required=false) Integer id,
                    Map<String, Object> map){
    System.out.println("modelAttribute method");
    if(id != null){
        //模拟从数据苦衷获取对象
        User user = new User(1, "Tom", "123456", "tom@atguigu.com", 12);
        System.out.println("从数据库中获取一个对象：: " + user);

        map.put("user", user);
    }
}

@RequestMapping("/testModelAttribute")
public String testModelAttribute(User user){
    System.out.println("User: " + user);
    return SUCCESS;
}
```

```html
<!--
        模拟修改操作
        1. 原始数据为: 1, Tom, 123456,tom@atguigu.com,12
        2. 密码不能被修改.
        3. 表单回显, 模拟操作直接在表单填写对应的属性值
        -->
<form action="springmvc/testModelAttribute" method="Post">
<input type="hidden" name="id" value="1"/>
        username: <input type="text" name="username" value="Tom"/>
<br>
        email: <input type="text" name="email" value="tom@atguigu.com"/>
<br>
        age: <input type="text" name="age" value="12"/>
<br>
<input type="submit" value="Submit"/>
</form>
<br><br>
```

![image-20210223135047916](/Users/anner/Library/Application Support/typora-user-images/image-20210223135047916.png)

## 五、视图以及视图解析器

​	请求处理方法执行完成后，最终返回一个 ModelAndView 对象。**对于那些返回 String，View 或 ModeMap 等类型的处理方法，Spring MVC 也会在内部将它们装配成一个ModelAndView 对象，**它包含了逻辑名和模型对象的视图Spring MVC 借助视图解析器（ViewResolver）得到最终的视图对象（View），最终的视图可以是 JSP ，也可能是Excel、JFreeChart 等各种表现形式的视图

​	**视图的作用是渲染模型数据**，将模型里的数据以某种形式呈现给客 户。视图对象由视图解析器负责实例化。由于视图是无状态的，所以他们不会有线程安全的问题;

![image-20210111141445408](/Users/anner/Library/Application Support/typora-user-images/image-20210111141445408.png)

### 5.2 视图解析器

SpringMVC 为逻辑视图名的解析提供了不同的策略，可 以在 Spring WEB 上下文中配置一种或多种解析策略，并指定他们之间的先后顺序。每一种映射策略对应一个具体的视图解析器实现类。

视图解析器的作用比较单一：将逻辑视图解析为一个具体 的视图对象。所有的视图解析器都必须实现 ViewResolver 接口；

**JSP 是最常见的视图技术，**可以使用InternalResourceViewResolver 作为视图解析器：

```xml
<!--    配置视图解析器：如何把handler 方法的返回值解析为是假的物理视图-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
```

**JSTL VIEW**:若项目中使用了 JSTL，则 SpringMVC 会自动把视图由**InternalResourceView 转为 JstlView**

**自定义视图：**

**第一步：** 在Spring 配置文件中配置

```xml
<!--BeanNameViewResolver 使用视图的名称解析视图
通过Order 属性类解析视图的有衔接，Order值越小优先级越高
-->
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
    <property name="order" value="100"/>
</bean>

    <!-- 在实际开发中通常都需配置 mvc:annotation-driven 标签 -->
    <mvc:annotation-driven></mvc:annotation-driven>
```

**第二步：**业务实现 实现view接口

```java
@Component
public class HelloView implements View {
    @Override
    public String getContentType() {
        return "text/html";
    }

    @Override
    public void render(Map<String, ?> map, HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        httpServletResponse.getWriter().println("hello view ,Time"+ new Date());
    }
}
```

第三步：业务逻辑实现

```java
@RequestMapping("/testview")
public String testView(){
    System.out.println("test view");
    return "helloView";  //返回值为类名。首字母小写
}
```

**测试：**

```html
<a href="springmvc/testview">test view</a><br>
```

### 5.3 转发重定向

一般情况下，控制器方法返回字符串类型的值会被当成逻 辑视图名处理；

如果返回的字符串中带 forward: 或 redirect: 前缀时，SpringMVC 会对他们进行特殊处理**：将 forward: 和redirect:** 当成指示符，其后的字符串作为 URL 来处理；

```java
@RequestMapping("/testRedirect")
public String testRedirect(){
    System.out.println("testRedirect");
    return "redirect:/index.jsp";
}
```

```html
<a href="springmvc/testRedirect">test testRedirect</a><br>
```

 ## 六、REST 风格CRUD

1、引入相关JAR包；

![image-20210223151209631](/Users/anner/Library/Application Support/typora-user-images/image-20210223151209631.png)

2、相关配置文件：

**Web.xml**

- SpringDispatcherServlet
- HiddenHttpMethordFilter

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--    配置HiddenHttpMethodFilter 可以将 post 请求转换为 DELETE  PUT 请求-->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--  1、  配置dispatcherServlet-->
    <servlet>
        <servlet-name>SpringDispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringDispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>


</web-app>
```

**Springmvc.xml**

- Component  自动扫描的包
- 视图解析器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--     配置扫描注解包-->
    <context:component-scan base-package="com.restart"/>

    <!--    配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--    配置视图解析器:bean  name 解析视图
    order 来进行优先级设置-->
    <bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
        <property name="order" value="100"/>
    </bean>
    <mvc:annotation-driven></mvc:annotation-driven>

</beans>
```

## 七、数据转化

![image-20210116145813249](/Users/anner/Library/Application Support/typora-user-images/image-20210116145813249.png)

## 八、MVC  Annotation - driven

**<mvc:annotation-driven />** 会自动注 册RequestMappingHandlerMapping、RequestMappingHandlerAdapter 与

ExceptionHandlerExceptionResolver 三个bean；

**还将提供以下支持：** 

- 支持使用 ConversionService 实例对表单参数进行类型转换 

- 支持使用 @NumberFormat annotation、@DateTimeFormat 

- 注解完成数据类型的格式化

- 支持使用 @Valid 注解对 JavaBean 实例进行 JSR 303 验证 

- 支持使用 @RequestBody 和 @ResponseBody 注解

## 九、数据校验

JSR 303 是 Java 为 Bean 数据合法性校验提供的标准框架，JSR 303 通过在 Bean 属性上标注类似于 @NotNull、@Max 

等标准的注解指定校验规则，并通过标准的验证接口对 Bean 进行验证；

- Spring 4.0 拥有自己独立的数据校验框架，同时支持 JSR 303 标准的校验框架。
- Spring 在进行数据绑定时，可同时调用校验框架完成数据校 验工作。在 Spring MVC 中，可直接通过注解驱动的方式进行数据校验；
- Spring 的 LocalValidatorFactroyBean 既实现了 Spring 的Validator 接口，也实现了 JSR 303 的 Validator 接口。只要在 Spring 容器中定义了一个LocalValidatorFactoryBean，即可将其注入到需要数据校验的 Bean 中；
- Spring 本身并没有提供 JSR303 的实现，所以必须将JSR303 的实现者的 jar 包放到类路径；

**步骤：**

- 使用JSR 303 验证标准；
- 加入hibernate validator 验证框架
- 在Spring MVC配置文件中添加<mvc:annotation-driven/>
- 在bean 属性上添加 @Valid注解



## 十、Spring MVC  处理Json

**步骤：**

- 导入jar包：

```xml
<!-- json相关 依赖-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.0</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.10.0</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.11.0</version>
</dependency>
```

- 方法实现

```html
<a href="testJson">list all empl</a><br>
```

```js
<script type="text/javascript">
        $(function(){
                $("#testJson").click(function(){
                        var url=this.href;
                        var args={};
                        $.post(url,args,function(data){
                                for(var i=0;i<data.length;i++){
                                        var id=data[i].id;
                                        var lastName=data[i].lastName;

                                        alert(id+": "+lastName);
                                }
                                });
                        return false;
                        });
        })
</script>
```

```java
@ResponseBody
@RequestMapping("/testJson")
public Collection<Employee> testJson(){
    return employeeDao.getAll();
}
```

 

**原理解析：**

​	HttpMessageConverter<T> 是 Spring3.0 新添加的一个接 口，负责将请求信息转换为一个对象（类型为 T），将对象（类型为 T）输出为响应信息；

![image-20210118192219268](/Users/anner/Library/Application Support/typora-user-images/image-20210118192219268.png)

**使用 HttpMessageConverter<T>** 将请求信息转化并绑定到处理方法的入 参中或将响应结果转为对应类型的响应信息，Spring 提供了两种途径：

- 使用 @RequestBody / @ResponseBody – 对处理方法进行标注

- 使用 HttpEntity<T> / ResponseEntity<T> 作为处理方法的入参或返回– 值

