# Cookie&&Session

## 一、Cookie

 Cookie 是服务器通知客户端保存键值对的一种技术；

客户端有了cookie 之后每次请求都发送给服务器；

每个Cookie的大小盘都不能超过4kb;

### 1.1 cookie的创建

![image-20201211143156076](/Users/anner/Library/Application Support/typora-user-images/image-20201211143156076.png)

```java
protected void createCookie(javax.servlet.http.HttpServletRequest request,
                            javax.servlet.http.HttpServletResponse response) throws
        javax.servlet.ServletException, IOException {
    request.setCharacterEncoding("UTF-8");
    response.setContentType("text/html; charset-UTF-8");

    //1、创建cookie对象
    Cookie cookie = new Cookie("key", "value");

    //2、通知客户端保存cookie
    response.addCookie(cookie);

}
```

###  1.2  cookie 的获取

 ![image-20201211143849926](/Users/anner/Library/Application Support/typora-user-images/image-20201211143849926.png) 

```java
protected void getCookie(javax.servlet.http.HttpServletRequest request,
                            javax.servlet.http.HttpServletResponse response) throws
        javax.servlet.ServletException, IOException {
    Cookie[] cookies = request.getCookies();
    for (Cookie cookie:cookies
         ) {
        System.out.println(cookie.getName());
        System.out.println(cookie.getValue());
    }
}   
```

###  1.3 cookie 修改

```java
protected void updateCookie(javax.servlet.http.HttpServletRequest request,
                         javax.servlet.http.HttpServletResponse response) throws
        javax.servlet.ServletException, IOException {

    // 方案一：
    //1、创建同名的cookie对象，并赋予新的cookie值
    Cookie cookie = new Cookie("key", "new  value");

    //2、通知客户端保存cookie
    response.addCookie(cookie);


    //方案二：
    // 1、先查找到需要修改的cookie
    //2、调用setValue() 方法设置新的值
    //3、通知客户端保存cookie

}
```

### 1.4 cookie的生命周期

```Java
//setMaxAge(); 正值表示在指定秒数后过期； 
//负数表示，浏览器一关闭就被删除；
//零表示马上被删除
cookie.setMaxAge(60);
```

### 1.5 cookie 有效路径Path 设置

cookie的path 属性，可以有效的过滤，哪些cookie 可以发送给服务器，哪些不发；path属性可以同归请求地址类有效的过滤；

**cookie 对于指定目录中的所有页面及该目录子目录中的所有页面都是可见的。cookie 的路径必须包括设置 cookie 的 servlet，例如** */catalog***，它使 cookie 对于服务器上** */catalog* **下的所有目录都是可见的。**

```java
cookie.setPath(request.getContextPath()+"abc");// 表示工程路径下的+abc 目录下可见
```

 ### 1.6  免用户名登陆

![image-20201212163706867](/Users/anner/Library/Application Support/typora-user-images/image-20201212163706867.png)

## 二、session

​	session 是httpSession是一个接口；表示一个会话，用来维护客户端和服务器的之间的关联的一种技术；session是保存在服务器端的，每个客户端都有一个session，session常用来保存用户登陆之后的信息；

### 2.1 Session的创建和获取

Request.getSession() //这个是第一次创建是创建session,后面调用是，都是获取前面创建好的session；

Isnew()  判断是否是刚刚创建出来的；

每一个session都有一个ID值，并且这个ID是唯一的；

```java
protected void createSession(javax.servlet.http.HttpServletRequest request,
                             javax.servlet.http.HttpServletResponse response) throws
        javax.servlet.ServletException, IOException {
    // 1、创建和获取session对象
    HttpSession session = request.getSession();

    boolean isNew = session.isNew();// 2、判断session是否是新创建的

    session.getId(); // 3、获取会话ID

    // 4、向session中保存数据
    session.setAttribute("key","value");
    // 5、从session中获取数据
    Object value = session.getAttribute("key");

    //6、设置session的超时时间，超过指定时间，session就会销毁,以秒为单位，session的默认超时时间为1800秒（30分钟）；
    // 因为在tomcat 服务器中，web.xml 配置文件默认为，30分钟；
    // session 的超时，表示，客户端两次请求之间的最大间隔时常；
    session.setMaxInactiveInterval(60);
    
}
```

### 2.2 浏览器和Session之间的关联技术

![image-20201212172155271](/Users/anner/Library/Application Support/typora-user-images/image-20201212172155271.png)

session底层时基于cookie的；