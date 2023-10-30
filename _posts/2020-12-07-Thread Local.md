# ThreadLocal

```java
public class ThreadLocal<T>
extends Object
```

**该类提供线程局部变量。** 这些变量与它们的正常对应物的不同之处在于，访问其中的每个线程（通过其`get`或`set`方法）具有其自己的，独立初始化的变量副本。 `ThreadLocal`实例通常是希望将状态与线程相关联的类中的私有静态字段（例如，用户ID或事务ID）。

**ThreadLocal 的作用，它可以解决多线程的数据安全问题。**

ThreadLocal 它可以给当前线程关联一个数据（可以是普通变量，可以是对象，也可以是数组，集合）

## 一、Thread Local的相关特点

**ThreadLocal 的特点：**

1、ThreadLocal 可以为当前线程关联一个数据。（它可以像Map 一样存取数据，key 为当前线程）

2、每一个ThreadLocal 对象，只能为当前线程关联一个数据，如果要为当前线程关联多个数据，就需要使用多个

ThreadLocal 对象实例。

3、每个ThreadLocal 对象实例定义的时候，一般都是static 类型

4、ThreadLocal 中保存数据，在线程销毁后。会由JVM 虚拟自动释放。



```java
public class ThreadLocalTest {


//    public static Map<String, Object> date = new HashMap<String, Object>();
    public static ThreadLocal<Object> threadLocal = new ThreadLocal<Object>();

    private static final Random random = new Random(1000);

    public class Task implements Runnable {
        @Override
        public void run() {
            //run 方法中随机生成一个变量，作为线程绑定的数据
            Integer i = random.nextInt();

            String name = Thread.currentThread().getName();
            System.out.println(name + "开始之前" + "关联的数据为" + i);
//            date.put(name, i);
            threadLocal.set(i);

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            Object o = threadLocal.get();

//            Integer o = (Integer) date.get(name);
            System.out.println(name + "结束之前" + "关联的数据为" + o);

        }
    }

    @Test
    public void test01() {
        for (int i = 0; i < 3; i++) {
            new Thread(new Task()).start();
        }
    }

}
```

## 二、ThreadLocal 与事务控制

![image-20201218173056352](/Users/anner/Library/Application Support/typora-user-images/image-20201218173056352.png)

```java
public class JdbcUtils {

    private static DruidDataSource dataSource;
    private static ThreadLocal<Connection> conns = new ThreadLocal<>();

    static {

        try {
            Properties properties = new Properties();
            //1、读取配置文件
            InputStream is = JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
            //2、从流中加载数据
            properties.load(is);
            //3、创建数据库连接池
            dataSource = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取数据库连接
     * @return
     */
    public static Connection getConnection() {
        Connection conn = conns.get();

        if (conn==null){
            try {
                conn = dataSource.getConnection();
                conns.set(conn); // 保存到 ThreadLocal中，供后面的JDBC 操作使用
                conn.setAutoCommit(false);
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        return conn;
    }

    /**
     * 提交并且关闭连接
     */
    public static void commitAndClose(){
        Connection connection = conns.get();  //从ThreadLocal 对象中获取连接
        if (connection!=null){//如果连接不为空，则说明之前就有过连接
            try {
                connection.commit();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            } finally {
                try {
                    connection.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
        conns.remove();//执行完成之后，一定要remove,Tomcat 底层采用了线程池技术
    }

    /**
     * 回滚并关闭连接
     */
    public static void rollbackAndClose(){
        Connection connection = conns.get();  //从ThreadLocal 对象中获取连接
        if (connection!=null){//如果连接不为空，则说明之前就有过连接
            try {
                connection.rollback();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            } finally {
                try {
                    connection.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
        conns.remove();//执行完成之后，一定要remove,Tomcat 底层采用了线程池技术
    }

}
```



![image-20201218232631758](/Users/anner/Library/Application Support/typora-user-images/image-20201218232631758.png)

## 三、将所有请求加上Filter

第一步：

```java
public class TransactionFilter  implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        try {
            filterChain.doFilter(servletRequest,servletResponse);
            JdbcUtils.commitAndClose();
        } catch (Exception e) {
            JdbcUtils.rollbackAndClose();
            e.printStackTrace();
        }
    }

    @Override
    public void destroy() {

    }
}
```

第二步：

```xml
<filter>
    <filter-name>TransactionFilter</filter-name>
    <filter-class>com.restart.filter.TransactionFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>TransactionFilter</filter-name>
    <!-- /* 表示对所有的请求都加上 filter-->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

