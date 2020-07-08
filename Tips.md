# Tips

##  **Exception**使用建议

![1582459396181](/Tips.assets/1582459396181.png)

1. 不要试图通过异常来控制程序流程，比如开发一个接口，正确的做法是对入参进行非空验证，当参数为空的时候返回“参数不允许为空”，而不应该捕捉到空指针的时候返回错误提示。

2. 仅捕获有必要的代码，尽量不要用一个 try...catch 包住大段甚至整个方法内所有的代码，因为这样会影响 JVM 对代码进行优化，从而带来额外的性能开销。

3. 很多程序员喜欢 catch(Exception e)，其实应该尽可能地精确地指出是什么异常。

4. 不要忽略异常，捕捉到异常之后千万不能什么也不做，要么在 catch{...} 中输出异常信息，要么通过 Throw 或 throws 抛出异常，让上层代码处理。

5. 尽量不要在 catch{...} 中输出异常后，又向上层代码抛出异常，因为这样会输出多条异常信息，而且它们还是相同的，这样可能会产生误导。

6. 不要在 finally{...} 中写 return，因为 try{...} 在执行 return 之前执行 finally{...} ，如果 finally{...} 中有 return，那么将不再执行 try{...} 中的return。

## 重写hashcode与equals

```java
@Test
    public void hashTest() {

        Key key1 = new Key(1);
        Key key2 = new Key(1);

        HashMap<Key, String> hashMap = new HashMap<>();
        hashMap.put(key1, "key1");
        System.out.println(hashMap.get(key2));

        /*
         * HashMap查询数据时，会通过hash值定位到数组下标，在通过遍历数组上链表，通过equals进行比较，所以对于需要作为Key的对象都应该重写hashCode与equals方法
         */

    }

    class Key {
        private Integer id;

        public Key(Integer id) {
            this.id = id;
        }

        public Integer getId() {
            return id;
        }

        public boolean equals(Object o) {
            if (o == null || !(o instanceof Key)) {
                return false;
            } else {
                return this.getId().equals(((Key) o).getId());
            }
        }

        public int hashCode() {
            return id.hashCode();
        }
    }
```

## 判断奇偶引发的问题

```java
public boolean isOdd(int i) {
    return i % 2 == 1 || i % 2 == -1;
}
```

```java
public boolean isOdd(int i) {
    return i % 2 != 0;
}
```

```java
public boolean isOdd(int i) {
    return i >> 1 << 1 != i;
}
```

```java
public boolean isOdd(int i) {
    return (i & 1) == 1;
}
```

以上是对奇偶判断的逐渐优化，其实第二个与最后一个差距不大：==编译器会将对2的指数的取模操作，优化为位运算操作==

## springboot  @Async 失效原因

- 异步方法使用static修饰
- 异步类没有使用@Component注解（或其他注解）导致spring无法扫描到异步类
- 异步方法不能与被调用的异步方法在同一个类中
- 类中需要使用@Autowired或@Resource等注解自动注入，不能自己手动new对象
- 如果使用SpringBoot框架必须在启动类中增加@EnableAsync注解

##  @**Autowire**   HttpServletRequest不会有线程问题

1. 在controller中注入的request是jdk动态代理对象,ObjectFactoryDelegatingInvocationHandler的实例.当我们调用成员域request的方法的时候其实是调用了objectFactory的getObject()对象的相关方法.这里的objectFactory是RequestObjectFactory.
2. RequestObjectFactory的getObject其实是从RequestContextHolder的threadlocal中去取值的.
3. 请求刚进入springmvc的dispatcherServlet的时候会把request相关对象设置到RequestContextHolder的threadlocal中去.

## 不使用的对象，赋值为null

​	对于这种说法，不是强制性的，理解其原因就明白了

​	对于JVM来说，判断对象是否需要回收，其中有一个就是使用**栈中引用的对象。也就是说，只要堆中的这个对象，在栈中还存在引用，就会被认定是存活的**。（ 可达性分析算法 ）

```java
public static void main(String[] args) {
    if (true) {
        byte[] placeHolder = new byte[64 * 1024 * 1024];
        System.out.println(placeHolder.length / 1024);
    }
    System.gc();
}
```

```java
public static void main(String[] args) {
    if (true) {
        byte[] placeHolder = new byte[64 * 1024 * 1024];
        System.out.println(placeHolder.length / 1024);
    }
    int replacer = 1;
    System.gc();
}
```

​	对于上面的两段代码，其实差不多，但是对于gc，只有下面这段代码中的placeHolder会被gc，因为 代码离开变量作用域时，并不会自动切断其与堆的联系 

​	这里的联系就是栈的局部变量表与堆内存的引用关系，对于栈执行，只有重新在作用域外新增新的局部变量时，才会将之前作用域中的变量引用清除

##  YYYY-MM-dd 的bug

​	 `YYYY` 表示：当天所在的周属于的年份，一周从周日开始，周六结束，只要本周跨年，那么这周就算入下一年。 

## Spring Boot Actuator

​	服务监控与管理

## springboot配置优先级原理

 Spring Boot 所有的配置来源会被构造成 PropertySource，比如 -D 参数, -- 参数, 系统参数, 配置文件配置等等。这些 PropertySource 最终会被添加到 List 中，获取配置的时候会遍历这个 List ，直到第一次获取对应 key 的配置，所以会存在优先级的问题。具体配置的优先级参考: 

## spring 事务失效

1.  **数据库引擎不支持事务** ：如mysql的 MyISAM 引擎是不支持事务操作的 
2.  **没有被 Spring 管理**  
3.  **方法不是 public 的** ：官方有说明，  `@Transactional` 只能用于 public 的方法上，否则事务不会失效，如果要用在非 public 方法上，可以开启 `AspectJ` 代理模式。 
4.  **自身调用问题** ： 就调该类自己的方法，而没有经过 Spring 的代理类，默认只有在外部调用事务才会生效
5.  **数据源没有配置事务管理器** 
6. **不支持事务**：`  @Transactional(propagation = Propagation.NOT_SUPPORTED) `
7.  **异常被吃了** ：try-catch处理异常，并没有再把异常抛出；因手动声明事务回滚
8.  **异常类型错误** ：默认回滚异常是 `RuntimeException` ，要触发其他异常的回滚 ，需进行配置，如： `@Transactional(rollbackFor = Exception.class) `

自身调用补充：

​	对于一下两种，可以将updateOrder方法写入另一个Service进行调用，或者使用 aspectj-autoproxy 

```java
@Service
public class OrderServiceImpl implements OrderService {

    public void update(Order order) {
        updateOrder(order); // 自身调用，无事务
    }

    @Transactional 
    public void updateOrder(Order order) {
        // update order
    }

}
@Service
public class OrderServiceImpl implements OrderService {

    @Transactional
    public void update(Order order) {
        updateOrder(order); // 自身调用，无法触发REQUIRES_NEW的事务
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void updateOrder(Order order) {
        // update order
    }

}
```

## JDK代理与CGLIB代理

java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。

而cglib动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

**对于Spring**

> 1、如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP 
>
> 2、如果目标对象实现了接口，可以强制使用CGLIB实现AOP 
>
> 3、如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换

 （1）JDK动态代理只能对实现了接口的类生成代理，而不能针对类
 （2）CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法，因为是继承，所以该类或方法最好不要声明成final  

## spring **异步请求与异步调用的区别** 

​	两者的使用场景不同，异步请求用来解决并发请求对服务器造成的压力，从而提高对请求的吞吐量；而异步调用是用来做一些非主线流程且不需要实时计算和响应的任务，比如同步日志到kafka中做日志分析等。

​	异步请求是会一直等待response相应的，需要返回结果给客户端的；而异步调用我们往往会马上返回给客户端响应，完成这次整个的请求，至于异步调用的任务后台自己慢慢跑就行，客户端不会关心。

## SQL优化

-  **明知只有一条查询结果，使用 “LIMIT 1”** ： “LIMIT 1”可以避免全表扫描，找到对应结果就不会再继续扫描
-  **为列选择合适的数据类型** ： 能用TINYINT就不用SMALLINT，能用SMALLINT就不用INT，磁盘和内存消耗越小越好  
-  **将大的DELETE，UPDATE or INSERT 查询变成多个小sql** 
-  **使用UNION ALL 代替 UNION，如果结果集允许重复的话** ：UNION ALL 不去重，效率高于 UNION。
-  **尽量避免使用 “SELECT \*”** ： 如果不查询表中所有的列，尽量避免使用 SELECT *，因为它会进行全表扫描，不能有效利用索引，增大了数据库服务器的负担，以及它与应用程序客户端之间的网络IO开销。 
-  **WHERE  、 JOIN子句里面的列以及 ORDER BY的列尽量被索引** 
-  **使用 LIMIT 实现分页逻辑** ： 提高了性能，同时减少了不必要的数据库和应用间的网络传输。 

## sql语句执行顺序

sql执行顺序 

> (1) from 
>
> (3) join 
>
> (2) on 
>
> (4) where 
>
> (5) group by(开始使用select中的别名，后面的语句中都可以使用)
>
> (6) avg,sum.... 
>
> (7) having 
>
> (8) select 
>
> (9) distinct 
>
> (10) order by
> (11) limit 

举例：

```sql
select 考生姓名, max(总成绩) as max总成绩 
 
from tb_Grade 
 
where 考生姓名 is not null 
 
group by 考生姓名 
 
having max(总成绩) > 600 
 
order by max总成绩 
```

 在上面的示例中 SQL 语句的执行顺序如下: 

> 　     (1). 首先执行 FROM 子句, 从 tb_Grade 表组装数据源的数据 
>
> 　　 (2). 执行 WHERE 子句, 筛选 tb_Grade 表中所有数据不为 NULL 的数据 
>
> 　　 (3). 执行 GROUP BY 子句, 把 tb_Grade 表按 "学生姓名" 列进行分组(注：这一步开始才可以使用select中的别名，他返回的是一个游标，而不是一个表，所以在where中不可以使用select中的别名，而having却可以使用)
> 　　 (4). 计算 max() 聚集函数, 按 "总成绩" 求出总成绩中最大的一些数值 
>
> 　　 (5). 执行 HAVING 子句, 筛选课程的总成绩大于 600 分的. 
>
> 　　 (7). 执行 ORDER BY 子句, 把最后的结果按 "Max 成绩" 进行排序. 



## Java注解

### 元注解

- @Target：表示修饰对象的范围，注解可以作用于 packages、class、interface、方法、成员变量、枚举、方法入参等等，@Target可以指明该注解可以修饰哪些内容。
- @Retention：时间长短，也就是注解在什么时间范围之内有效，比如在源码中有效，在 class 文件中有效，在 Runtime 运行时有效。
- @Documented：表示可以被文档化，比如可以被 javadoc 或类似的工具生成文档。
- @Inherited：表示这个注解会被继承，比如 @MyAnnotation 被 @Inherited 修饰，那么当 @MyAnnotation 作用于一个 class 时，这个 class 的子类也会被 @MyAnnotation 作用。

### 内置注解

- @Override：检查该方法是否是重载方法；如果父类或实现的接口中，如果没有该方法，编译会报错。
- @Deprecated：已经过时的方法；如果使用该方法，会有警告提醒。
- @SuppressWarnings：忽略警告；比如使用了一个过时的方法会有警告提醒，可以为调用的方法增加 @SuppressWarnings 注解，这样编译器不在产生警告。
- @SafeVarargs：是一个参数安全类型注解，作用是告诉开发人员，参数可能会存在不安全的操作，要多注意一些；它会产生一个 unchecked 的警告；@SafeVarargs 注解只能用在可变长度参数的方法上，并且这个方法必须是 static 或 final 的，否则会出现编译错误。
- @FunctionalInterface：表示是只有一个方法的接口，JDK 8 引入。
- @Repeatable：表示注解的值可以有多个，比如我又帅气又幽默，这时候我同时有了“帅气”和“幽默”两个标签。

## Ajax提交了两次？？

 ```html
 <form id="uploadForm" method="POST" action="actionUrl">
   <input type="text" id="yourName" />
   <%--重点来了,注意这个button--%>
   <button id="btnOk" onclick="submitClick()">submit</button>
</form> 
 ```

 请始终为按钮规定 type 属性。Internet Explorer 的默认类型是 "button"，而其他浏览器中（包括 W3C 规范）的默认值是 "submit"。 

## Mysql的utf-8

​	Mysql中的UTF-8 只能支持每个字符最多三个字节 ， 而真正的UTF-8是每个字符最多四个字节。  utf8mb4 才是mysql中真正的utf8

## 回表查询优化

**操作：** 查询条件放到子查询中，子查询只查主键ID，然后使用子查询中确定的主键关联查询其他的属性字段；

**原理：** 减少回表操作；

```sql
-- 优化前SQL
SELECT  各种字段
FROM`table_name`
WHERE 各种条件
LIMIT0,10;
-- 优化后SQL
SELECT  各种字段
FROM`table_name` main_tale
RIGHTJOIN
(
SELECT  子查询只查主键
FROM`table_name`
WHERE 各种条件
LIMIT0,10;
) temp_table ON temp_table.主键 = main_table.主键
```

## 代码重构

​	==随时随地的重构代码，而不是集中到最后重构！！！==

- **重复代码的提炼**

- **冗长方法的分割**

- **嵌套条件分支的优化**

- **去掉一次性的临时变量**

- **去掉一次性的临时变量**

- **提取类或继承体系中的常量**（消除魔数）

- **让类提供应该提供的方法**

  ``` 我们经常会操作一个类的大部分属性，从而得到一个最终我们想要的结果。这种时候，我们应该让这个类做它该做的事情，而不应该让我们替它做。而且大部分时候，这个过程最终会成为重复代码的根源。 ```

- **拆分冗长的类**

- **提取继承体系中重复的属性与方法到父类**

## Mysql时间存储

| 日期类型                       | 存储空间 | 日期格式            | 日期范围                                  | 是否有时区问题 |
| ------------------------------ | -------- | ------------------- | ----------------------------------------- | -------------- |
| Datetime                       | 8字节    | YYYY-mm-DD HH:MM:SS | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59 | 是             |
| Timestamp                      | 4字节    | YYYY-mm-DD HH:MM:SS | 1970-01-01 00:00:01 ~ 2037-12-31 23:59:59 | 否             |
| 时间戳（int 或者 bigint 类型） | 4字节    | 数字                | 1970-1-1 00:00:00 +0:00 到现在的秒数      | 否             |

- Datetime有时区问题， 保存的时间都是当前会话所设置的时区对应的时间 ， 当你的时区更换之后，比如你的服务器更换地址或者更换客户端连接时区设置的话，就会导致你从数据库中读出的时间错误 。占用8字节的空间，以至于存储的时间范围会更大

-  Timestamp 类型字段的值会随着服务器时区的变化而变化，自动换算成相应的时间 。 Timestamp 表示的时间范围更小。 

- 时间戳

  >  时间戳的定义是从一个基准时间开始算起，这个基准时间是「1970-1-1 00:00:00 +0:00」，从这个时间开始，用整数表示，以秒计时，随着时间的流逝这个时间整数不断增加。这样一来，我只需要一个数值，就可以完美地表示时间了，而且这个数值是一个绝对数值，即无论的身处地球的任何角落，这个表示时间的时间戳，都是一样的，生成的数值都是一样的，并且没有时区的概念，所以在系统的中时间的传输中，都不需要进行额外的转换了，只有在显示给用户的时候，才转换为字符串格式的本地时间。 

==推荐使用 Timestamp ，保证直观可读和时区问题==

## JDK 中定时器实现

 jdk中能够实现定时器功能的大致有三种方式： 

- java.util.Timer ： 等待是使用最简单的Object.wait()实现的 
- java.util.concurrent.DelayQueue ：  DelayQueue的等待是通过Condition.await()来实现的 
- java.util.concurrent.ScheduledThreadPoolExecutor ： 在创建ScheduledThreadPoolExecutor时，线程池的工作队列使用的是DelayedWorkQueue,它的take()方法，与DelayQueue.take()方法极其相似  

 总结，jdk中实现定时器一共有两种方式： `Object.wait() `    ` Conditon.await() `

## rm删除文件，空间未释放情况

> 实际上，只有当一个文件的引用计数为0（包括硬链接数）的时候，才可能调用unlink删除，只要它不是0，那么就不会被删除。所谓的删除，也不过是文件名到 inode 的链接删除，只要不被重新写入新的数据，磁盘上的block数据块不会被删除，因此，你会看到，即便删库跑路了，某些数据还是可以恢复的。换句话说，当一个程序打开一个文件的时候（获取到文件描述符），它的引用计数会被+1，rm虽然看似删除了文件，实际上只是会将引用计数减1，但由于引用计数不为0，因此文件不会被删除。
>
> [原文](https://mp.weixin.qq.com/s?__biz=MzU4MDUyMDQyNQ==&amp;mid=2247485896&amp;idx=1&amp;sn=4cd2458cca5445542d7a55c97463ada8&amp;chksm=fd54d94eca2350581e301eee43e28e4d2fdb12f0713cb38783d8c4f8eff61d42e2a35a255f26&amp;scene=126&amp;sessionid=1590109692&amp;key=3fed518d24530a2901e67009d49eca7639747877c6920ae69df55c248c1868307b17d0267fbce08b499669ac84a7c4ef87bb726056aceecdaa2b2c6d779faa6409538edb083c0408843a120c7f96b7e5&amp;ascene=1&amp;uin=MzE5MDU1OTY5&amp;devicetype=Windows+10+x64&amp;version=62090070&amp;lang=zh_CN&amp;exportkey=Aa8xLNJ%2B7iUSxvJ2wyL6QCM%3D&amp;pass_ticket=N%2B0ldEdP5x2nUskDU8Hv7xPu56rUyiuAgn8KBew2zZ9%2FpVoZqdPTT4%2BHrMQ7WeFy)

## SpringBoot循环依赖

[ 原文 ](https://mp.weixin.qq.com/s?__biz=MzIyMDI5MzA3NQ==&mid=2247489264&idx=1&sn=d55b4aec2e1155553e1576b1df82e73d&chksm=97cf644ca0b8ed5acab435bff6e8b20f5fa7576a8ed549c45a93959ff93a877cb276bf93d759&scene=126&sessionid=1591021206&key=6232b7e0f4ef35c953bb7f9a88736eb90d0bad9517c391329627bf70d9203f3fa1a20910d5e49cad3bf6e46ff9f186f94afd455ab3f7b6cbba8a40bf19c52e65ceda7880a26e7c3765c1366f84d79f21&ascene=1&uin=MzE5MDU1OTY5&devicetype=Windows+10+x64&version=62090070&lang=zh_CN&exportkey=AfkXsZcDrD4wEtwFjdmedoo%3D&pass_ticket=as5A25jpWG7VqZCDbWJjLJVUa6D4L2farOiGXhwLVVwo%2FUsbLwabnoMZ9axnIpIu)

1、 原型(Prototype)的场景是不支持循环依赖的，通常会走到`AbstractBeanFactory`类中下面的判断，抛出异常 

```java
if (isPrototypeCurrentlyInCreation(beanName)) {
  throw new BeanCurrentlyInCreationException(beanName);
}
```

2、单例模式的循环依赖

![1591023107442](/assets/1591023107442.png)

​	主要依靠这三个Map来进行处理

​	大概逻辑：

​	1、A类需要创建，会先通过`singletonFactories`实例化 A，在将刚刚实例化好的A 放入 `earlySingletonObjects`，此时设置A实例中的成员属性；

​	2、此时需要 B类的实例，于是又通过`singletonFactories`实例B，再放入`earlySingletonObjects`，

​	3、但此时B 实例又 依赖 A类实例，于是查询`earlySingletonObjects`和`singletonObjects`是否存在 A 实例，发现有A实例，B实例继续剩余的属性注入，

​	4、最后放入`singletonObjects`中，并将`singletonObjects`中对应B移除，此时A实例中的B已注入完成

### 伪代码实现：

```java
 /**
     * 放置创建好的bean Map
     */
    private static Map<String, Object> cacheMap = new HashMap<>(2);

    public static void main(String[] args) {
        // 假装扫描出来的对象
        Class[] classes = {A.class, B.class};
        // 假装项目初始化实例化所有bean
        for (Class aClass : classes) {
            getBean(aClass);
        }
        // check
        System.out.println(getBean(B.class).getA() == getBean(A.class));
        System.out.println(getBean(A.class).getB() == getBean(B.class));
    }

    @SneakyThrows
    private static <T> T getBean(Class<T> beanClass) {
        // 本文用类名小写 简单代替bean的命名规则
        String beanName = beanClass.getSimpleName().toLowerCase();
        // 如果已经是一个bean，则直接返回
        if (cacheMap.containsKey(beanName)) {
            return (T) cacheMap.get(beanName);
        }
        // 将对象本身实例化
        Object object = beanClass.getDeclaredConstructor().newInstance();
        // 放入缓存
        cacheMap.put(beanName, object);
        // 把所有字段当成需要注入的bean，创建并注入到当前bean中
        Field[] fields = object.getClass().getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            // 获取需要注入字段的class
            Class<?> fieldClass = field.getType();
            String fieldBeanName = fieldClass.getSimpleName().toLowerCase();
            // 如果需要注入的bean，已经在缓存Map中，那么把缓存Map中的值注入到该field即可
            // 如果缓存没有 继续创建
            field.set(object, cacheMap.containsKey(fieldBeanName)
                    ? cacheMap.get(fieldBeanName) : getBean(fieldClass));
        }
        // 属性填充完成，返回
        return (T) object;
    }
```

==透过现象看本质，不要为了看源码而看源码，应该先思考其本质==

## 对象大小

​	空对象默认占8字节。因为对齐填充的关系， 由于虚拟机要求对象起始地址必须是8字节的整数倍，不到8个字节对其填充会帮我们自动补齐

## Synchronized底层实现

​	[原文](https://juejin.im/post/5ec135e5f265da7be95a0cb3?utm_source=gold_browser_extension)

​	[github.com/JavaFamily](https://github.com/AobingJava/JavaFamily)

## Java技巧

​	[原文](https://mp.weixin.qq.com/s?__biz=Mzg2MjEwMjI1Mg==&mid=2247495496&idx=1&sn=61bcdd6a722f93162127780d1d588c5b&chksm=ce0e58cbf979d1dd3dcf142efa2768c0253ce0bc19d730d970b6560d5a424c09bab832f41112&scene=126&sessionid=1592808405&key=f6dee7b9ef4ba659720b5da563b3bc6fc92a4fba93e190845d1f3561dd2d38fc72f74a032f19ed663518ed07b09d6abec53797b40cbe0c3e8a8db42466c27461b0d4316b9837235f055806ce27d8a6b0&ascene=1&uin=MzE5MDU1OTY5&devicetype=Windows+10+x64&version=62090070&lang=zh_CN&exportkey=AYu%2FOzA%2BTv2uikdhLwZk7hs%3D&pass_ticket=2bfhswnoOi3HmHoAwinClT6%2FP1DsLgTwfgTIwq1UTeaTyWsGqCOAvAfGXPfpruor)

> bean包名
>
> 类转换
>
> bean校验
>
> builder
>
> lombok
>
> 重构
>
> 技能

## hateoas

## 日志收集系统

ELK（Elasticsearch、Kibana、Logstash）

[参考](https://mp.weixin.qq.com/s/ll_A6ddBaU99LSYmKdttYw)

https://github.com/macrozheng/mall-learning/tree/master/mall-tiny-log

## MySQL事务更新失效

​	如果有一个事务正在执行，先执行单表查询某一条数据；

​	此时如果将数据库中的值修改后，同时在该事务中做相同值的修改时，会直接返回，

​	而且事务中查询的数据仍然是最开始查询出的数据，不是修改后的数据

​	==MySQL 调用了 InnoDB 引擎提供的修改接口，但是引擎发现值与原来相同，不更新，直接返回==

![image-20200623104910119](/assets/image-20200623104910119.png)

# Tools

## IDEA 插件

​	https://chrome.zzzmh.cn/

1. Jrebel
2. Xrebel
3. alibaba
4. Easycode
5. GenerateAllSetter
6. CodeGlance
7. Rainbow Brackets
8. cloud toolkit
9.  JSON Viewer Awesome 
10. Arthas
11. SonarLint
12. lombok

## Cockpit 服务器web界面监控

## kmdr ：解析shell命令

## datafaker ：测试数据生成

 https://github.com/gangly/datafaker 

## mysqltuner.pl ：mysql性能检测

##  Hutool java工具类

 http://www.hutool.cn/ 

## Guava

## SonarQube - Docker

```shell
docker pull postgres
docker pull sonarqube
```

```shell
docker run --name db -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -d postgres
docker run --name sq --link db -e SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar -e SONARQUBE_JDBC_USERNAME=sonar -e SONARQUBE_JDBC_PASSWORD="sonar" -p 9000:9000 -d sonarqube
```

http://localhost:9000/   

登录账号：admin 密码：admin