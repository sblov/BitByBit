---
typora-root-url: img
---

# SQL

### Oracle  VS   MySql

|    Oracle    |    Mysql    |
| :----------: | :---------: |
|    NVL()     |  IFNULL()   |
|     \|\|     |  CONCAT()   |
|   to_char    | DATE_FORMAT |
|   TO_DATE    | STR_TO_DATE |
| 'MM-dd-YYYY' | '%m-%d-%Y'  |
| TRUNC -数值  |  TRUNCATE   |
|              |             |
|              |             |
|              |             |

### MySQL server version for the right syntax to use near

>- 字段报错，可能为该字段为MySql关键字，需要`''`来引用。如：`'rank'`
>
>

### mysql的时区错误问题

>  **The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one**
>
>  ​	`show variables like '%time_zone%';`
>
>  ​	`set global time_zone='+8:00';`

### MySQL 将查询出来的一列数据拼装成一个字符串

> **#使用GROUP_CONCAT函数。**
> **SELECT GROUP_CONCAT(查询的字段 separator ',') FROM table**
>
> ```sql
> SELECT GROUP_CONCAT(a.treenode_path separator '|') from (select treenode_path from u_dept where id in (1635,1636)) a
> ```

### Mysql贪婪匹配（正则）

> `select * from a where name regexp'aa|bb|cc'`

### Mysql 实现row_number

```sql
SELECT
	@rowno := @rowno + 1 AS rowno,
	r.* 
FROM (主表) r,
( SELECT @rowno := 0 ) t;
```

### mysql查某列最大值所在行的数据

`select * from a order by a.c1 desc limit 1`

### Mysql8.0 递归

```sql
/* 递归查询所有子层树结构 */
WITH RECURSIVE cte AS
(
SELECT a.f_id, a.f_parent_id FROM t_authority a WHERE a.f_id='11010000'
UNION ALL
SELECT k.f_id,k.f_parent_id FROM t_authority k INNER JOIN cte c ON c.f_id = k.f_parent_id
)SELECT f_id, f_parent_id FROM cte
```

### mysql出现unblock with 'mysqladmin flush-hosts'

> **解决方法1：修改max_connect_errors的值**
> (1)进入Mysql数据库查看max_connect_errors：
> \> show variables like '%max_connect_errors%';
> (2)修改max_connect_errors的值：
> \> set global max_connect_errors = 100;
> (3)查看是否修改成功
> \> show variables like '%max_connect_errors%';
>
> **解决方法2：使用mysqladmin flush-hosts 命令清理一下hosts文件**
> (1)在查找到的目录下使用命令修改：mysqladmin -u xxx -p flush-hosts
> 或者
> \> flush hosts;

### 计算函数null处理

> 一、AVG()
> 		求平均值
> 		注意AVE()忽略NULL值，而不是将其作为“0”参与计算
> 	二、COUNT()，两种用法
> 			1、COUNT(*)
> 			对表中行数进行计数
> 			不管是否有NULL
> 
> ​			2、COUNT(字段名)
> ​			对特定列有数据的行进行计数
> ​			忽略NULL值
> ​	三、MAX()、MIN()
> ​		求最大、最小值
> ​		都忽略NULL
> ​	四、SUM()
> ​		可以对单个列求和，也可以对多个列运算后求和
> ​		忽略NULL值，且当对多个列运算求和时，如果运算的列中任意一列的值为NULL，则忽略这行的记录。
> ​		例如： SUM(A+B+C)，A、B、C 为三列，如果某行记录中A列值为NULL，则不统计这行。
> ​	五、GROUP BY的使用注意事项
> ​		1、分组列中若有NULL，这也将作为一组，且NULL值排在最前面
> ​		2、除汇总函数计算语句外，SELECT中的选择列必须出现在GROUP BY 中
>​		3、GROUP BY 可以包含任意数目的列，可以嵌套

### mysql : sql_mode

```sql
select @@sql_mode # 局部sql_mode
select @@global.sql_mode # 全局sql_mode
```

没有特定的sql规范要求，可以不使用默认的sql_mode配置，在对应配置文件中的`[mysqld]`下

新增sql_mode 配置：

```properties
[mysqld]
# 自定义配置的模式
sql_mode=STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION
```

### MySQL查询所有表字段结构

```sql
SELECT
	table_name,
	GROUP_CONCAT(COLUMN_name SEPARATOR '  ,   ')
FROM
	information_schema.COLUMNS 
WHERE
	table_schema = 'cpis_backup'
	-- and table_name not LIKE 'act_%'
	-- and (COLUMN_name like 'update\_%' or COLUMN_name like 'create\_%')
	GROUP BY table_name
```

# Mysql

### mysql8.0版本 the user specified as a definer ('root'@'%') does not exist问题解决

```shell
mysql> create user 'root'@'%' identified by '密码';
Query OK, 0 rows affected (2.35 sec)
 
mysql> grant all privileges on *.* to 'root'@'%';
Query OK, 0 rows affected (0.06 sec)
 
mysql> flush privileges;
Query OK, 0 rows affected (0.06 sec)
```

### MySQL-this is incompatible with sql_mode=only_full_group_by

配置文件设置以下配置，并重启 https://blog.csdn.net/qq_42175986/article/details/82384160 

```properties
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```





# Web

### onclick事件传递对象

> **json对象：JSON.stringify(user).replace(/"/g, '&quot;')**

```javascript
return '<a class="ui-icon ui-icon-pencil" onclick="$.isp.masterdata.candidateVerdict.openWindow('+JSON.stringify(rowdata).replace(/\"/g,"'")+')" href="javascript:void(0)" ></a>';
```

>  **json字符串：JSON.stringify(user).replace(/"/g, '&quot;') + '\')**

### Input的value值无法通过form提交

> `disabled="disabled" `    改为    `readonly="readonly"`

字符转义

|            **字符**            | **转义字符** |
| :----------------------------: | :----------: |
|               "                |   `&quot`    |
|               &                |   `&amp;`    |
|               <                |    `&lt;`    |
|               >                |    `&gt;`    |
| 不断开空格(non-breaking space) |   `&nbsp;`   |

### \<!DOCTYPE html>

​	该标签应为顶级标签，之前不能有其他标签。

​	作用：**声明文档的解析类型(document.compatMode)，避免浏览器的怪异模式。**

> **BackCompat：怪异模式，浏览器使用自己的怪异模式解析渲染页面。**
>
> **CSS1Compat：标准模式，浏览器使用W3C的标准解析渲染页面。**

### CSS动态高度

> Viewport
>     viewport：可视窗口，也就是浏览器。
>     vw Viewport宽度， 1vw 等于viewport宽度的1%
>     vh Viewport高度， 1vh 等于viewport高的的1%

calc()使用通用的数学运算规则，但是也提供更智能的功能：


    >使用“+”、“-”、“*” 和 “/”四则运算；
    >可以使用百分比、px、em、rem等单位；
    >可以混合使用各种单位进行计算；
    >表达式中有“+”和“-”时，其前后必须要有空格，如"widht: calc(12%+5em)"这种没有空格的写法是错误的；
    >表达式中有“*”和“/”时，其前后可以没有空格，但建议留有空格。
       
    例如 ：设置div元素的高度为当前窗口高度-100px
     div{
       height: calc(100vh - 100px);     
    }
### Tomcat

> Tomcat8开始，默认支持10000的请求，和200的并发量；一个正常的http请求处理为20ms，1s钟200个线程同时处理，每个线程处理50个请求，

### getOutputStream() has already been called for this response

> response.getWriter() 与 response.getOutputStream()的重复调用造成
>
> 默认ssm的controller返回也会调用response.getOutputStream() 或 response.getWriter()进行输出

### ie下提交中文乱码

前后端编码不一致，或者浏览器编码转义不一致

统一设置ajaxSetup全局设置：

```js
contentType: "application/x-www-form-urlencoded; charset=utf-8",
```

### 下载不弹出新窗口

1. a 标签 ， 设置 `download` 属性
2. `window.open( url , '_self')`

## form表单检查是否修改

```js
/**
 * 判断form表单是否被修修改
 *      这里有两种实现： 1、表单初始时，直接把初始的form序列化为全局变量； 当提交时再序列化提交时的form，直接JSON.stringify 判断两个form是否相等
 *                    2、下面这种，获取html默认设置的初始值，不用额外声明全局变量
 */
$.isp.pricenegotiation.formIsChanged = function (formId) {
    var formParent = $('#'+formId)
    var mockData = $('#'+formId).serializeJson()

    for(var key in mockData){
        var element = $('[name='+key+']', formParent)

        if (element.is('select')){
            var opIndex = $('[name='+key+']', formParent).prop('selectedIndex')
            if (!$($('select[name='+key+'] option', formParent)[opIndex]).prop('defaultSelected')){
                return true
            }
        }
        if (element.is('input')){
            if (mockData[key] != element.prop('defaultValue')){
                return true
            }
        }
    }

    return false
}
```

## springmvc json传参，封装复杂对象

前端json参数：

```json
 var mockData = {
    year: '2021',
    period: '00',
    content: 'hhahhhhhhhh',
    aggCondition: {
        '331B': {
            checked: true,
            vehmodid: '331B',
            newFlag: [1],
            periodType: '00',
            month: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
        },
        '330B': {
            checked: true,
            vehmodid: '331B',
            newFlag: [2],
            periodType: '01',
            month: [1, 2, 3, 4, 5, 6]
        },
        '332B': {
            checked: true,
            vehmodid: '331B',
            newFlag: [1, 2],
            periodType: '02',
            month: [7, 8, 9, 10, 11, 12]
        }
    }
}
```

==js端要使用json传送数据，所以需要指定application/json类型并使用JSON.stringify来将对象转成json格式==

```js
$.ajax({
        url: 'isp/pricenegotiation/orderAmount/aggregate',
        type: 'post',
        dateType: 'json',
        contentType: "application/json;charset=UTF-8",
        data: JSON.stringify( mockData),
        success: function (json) {
          ........
        }
    })
```

==controller 使用@RequestBody注解，表示使用http body的内容==

```java
    @PostMapping("")
    @ResponseBody
    public ReturnMessage aggregate( @RequestBody OrderAmountAggCondition orderAmountAggCondition){

        return this.ok();
    }
```

封装对象

```java
@Data
public class OrderAmountAggCondition implements Serializable {

    private String pnYear;
    private String period;
    private String aggContent;
    private HashMap<String, AggCondition> aggCondition;

  
}

@Data
class AggCondition implements Serializable{
    private Object checked;
    private String vehmodid;
    private List<String> newFlag;
    private String periodType;
    private List<String> month;
	
}


```



# Java

### 字符串拼接性能

>- 如果+拼接的时候**没有对象**，比如String two = "dffe"+123+"ddd";，这种情况效率最高，并非要用string builder，**JVM会直接就做了拼接转化**，根本不用builder.
>
>- 如果你要拼接的**包含对象**，比如String res = a+b+"dd"+c;，而且**不用于循环**，这个时候用+ 和 用builder 随意，都一样。
>
>- **一行 + 操作就会产生一个builder**。比如String res = a+b+"dd"+c; res += "555";会new 两次builder。这和用在循环一个道理，同样每次循环都会重新new一个builder。所以用在**循环或者多行操作**的时候，**builder是效率最高**的，次数少的话和buffer是差不多的。

### 项目路径

>```java
>//1：获取类加载的根路径  D:\workplace\eclipse_w\Test\bin
>this.getClass().getResource("/").getPath()
>
>//获取当前类的所在工程路径; 如果不加“/”  获取当前类的加载目录	D:\workplace\eclipse_w\Test\bin\com\lov 
>this.getClass().getResource("").getPath()
>
>//2：获取项目路径 D:\workplace\eclipse_w\Test 
>File directory = new File("");// 参数为空
>directory.getCanonicalPath();
>
>//3：  file:/D:/workplace/eclipse_w/Test/bin/
>URL xmlpath = this.getClass().getClassLoader().getResource("");
>
>//4： D:\workplace\eclipse_w\Test;不推荐使用，在环境部署时会有变化
>System.getProperty("user.dir")
>
>//5：  获取所有的类路径 包括jar包的路径
>System.getProperty("java.class.path")
>
>//6:web部署路径
>rq.getServletContext().getRealPath("");（HttpServletRequest）
>```

### try-with-resource

>对于实现  `AutoCloseable` 的流，可以自动关闭
>
>在  `try-with-resources `语句中, 任意的 catch 或者 finally 块都是在声明的资源被关闭以后才运行。
>
>```java
>try (
>        FileOutputStream out = new FileOutputStream(newPDFPath);//输出流
>){
>    ...........
>}catch (IOException e) {
>            e.printStackTrace();
>        }
>```

### 基础类型存储位数

>**byte ：8 bit**
>**char ：16 bit**
>**short ：16 bit**
>**int ：32 bit**
>**boolean ：32 bit**
>**float ：32 bit**
>**long ：64 bit** 
>**double ：64 bit** 
>
>**JVM规范中，boolean变量作为int处理，也就是4字节；boolean数组当做byte数组处理**



# Mybatis

> **Parameter '0' not found. Available parameters are [arg1, arg0, param1, param2]**

在MyBatis3.4.4版不能直接使用#{0}要使用 #{arg0}或#{param0}

### mybatis xml文件中用 if 标签判断单字符是否相等

使用下面示例中 "delFlag =='2' " ， Mybatis会将 “2” 解析为字符（java 强类型语言， ‘2’ char 类型 ），而非字符串，不能做到判断的效果。 要在Mybatis中判断字符串是否相等

```xml
1.
<if test="delFlag == '2'.toString()">
	a.del_flag = #{delFlag}
</if>
2.
<if test=' delFlag == "2" '>
	a.del_flag = #{delFlag}
</if>
```

# SpringBoot

### 过滤请求参数（去空格）

**ParameterRequestWrapper.java **   请求参数封装

```java
public class ParameterRequestWrapper extends HttpServletRequestWrapper {

    private Map<String , String[]> params = new HashMap<String, String[]>();


    public ParameterRequestWrapper(HttpServletRequest request) {
        // 将request交给父类，以便于调用对应方法的时候，将其输出，其实父亲类的实现方式和第一种new的方式类似
        super(request);
        //将参数表，赋予给当前的Map以便于持有request中的参数
        Map<String, String[]> requestMap=request.getParameterMap();
//        System.out.println("转化前参数："+ JSON.toJSONString(requestMap));
        this.params.putAll(requestMap);
        this.modifyParameterValues();
//        System.out.println("转化后参数："+JSON.toJSONString(params));
    }
    /**
     * 重写getInputStream方法  post类型的请求参数必须通过流才能获取到值
     */
    @Override
    public ServletInputStream getInputStream() throws IOException {
        //非json类型，直接返回
        if(!super.getHeader(HttpHeaders.CONTENT_TYPE).equalsIgnoreCase(MediaType.APPLICATION_JSON_VALUE)){
            return super.getInputStream();
        }
        //为空，直接返回
        String json = IOUtils.toString(super.getInputStream(), "utf-8");
        if (StringUtils.isEmpty(json)) {
            return super.getInputStream();
        }
        System.out.println("转化前参数："+json);
        Map<String,Object> map= (Map<String, Object>) JSON.parse(json);
        System.out.println("转化后参数："+JSON.toJSONString(map));
        ByteArrayInputStream bis = new ByteArrayInputStream(JSON.toJSONString(map).getBytes("utf-8"));
        return new MyServletInputStream(bis);
    }
    /**
     * 将parameter的值去除空格后重写回去
     */
    public void modifyParameterValues(){
        Set<String> set =params.keySet();
        Iterator<String> it=set.iterator();
        while(it.hasNext()){
            String key= (String) it.next();
            String[] values = params.get(key);
            values[0] = values[0].trim();
            params.put(key, values);
        }
    }
    /**
     * 重写getParameter 参数从当前类中的map获取
     */
    @Override
    public String getParameter(String name) {
        String[]values = params.get(name);
        if(values == null || values.length == 0) {
            return null;
        }
        return values[0];
    }
    /**
     * 重写getParameterValues
     */
    public String[] getParameterValues(String name) {//同上
        return params.get(name);
    }

    class MyServletInputStream extends  ServletInputStream{
        private ByteArrayInputStream bis;
        public MyServletInputStream(ByteArrayInputStream bis){
            this.bis=bis;
        }
        @Override
        public boolean isFinished() {
            return true;
        }

        @Override
        public boolean isReady() {
            return true;
        }

        @Override
        public void setReadListener(ReadListener listener) {

        }
        @Override
        public int read() throws IOException {
            return bis.read();
        }
    }

}
```

**ParamsFilter.java**    设置参数过滤器

```java
public class ParamsFilter implements Filter{

	@Override
	public void doFilter(ServletRequest arg0, ServletResponse arg1,
			FilterChain arg2) throws IOException, ServletException {
		ParameterRequestWrapper parmsRequest = new ParameterRequestWrapper(
				(HttpServletRequest) arg0);
		arg2.doFilter(parmsRequest, arg1);
	}

	@Override
	public void init(FilterConfig arg0) throws ServletException {
		
	}
	
	@Override
	public void destroy() {
		
	}

}
```

**@Bean**  在任意config类中注入

```java
 @Bean
    public FilterRegistrationBean parmsFilterRegistration() {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setDispatcherTypes(DispatcherType.REQUEST);
        registration.setFilter(new ParamsFilter());
        registration.addUrlPatterns("/*");
        registration.setName("paramsFilter");
        registration.setsOrder(Integer.MAX_VALUE-1);
        return registration;
    }
```

### Resources文件读取（jar和war都可用）

```java
InputStream resourceAsStream = Thread.currentThread().getContextClassLoader().getResourceAsStream("相对路径（不加/）");
```

### java.lang.IllegalArgumentException: Request header is too large

```
server.max-http-header-size=10000000 (tomcat)
server.jetty.max-http-header-size=10000000 (jetty)
```

### 线程池

@Async

### mvn spring-boot:run 无法加载外部jar

 一般mvn打包时，需要加载外部jar，只需要设置plugin即可：指定jar包所在项目位置

```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <compilerArguments>
                        <extdirs>${project.basedir}/src/main/webapp/WEB-INF/lib</extdirs>
                    </compilerArguments>
                </configuration>
            </plugin>
```

但对应这种方式运行项目，还需要声明依赖的方式加载：只要`<systemPath>`节点的文件正确和`<scope>system</scope>`即可，其余随便填写，

```xml
<dependency>

            <groupId>com.aspose</groupId>
            <artifactId>cells</artifactId>
            <version>8.5.2</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/src/main/webapp/WEB-INF/lib/aspose-cells-8.5.2.jar
            </systemPath>
        </dependency>
```



# Javascript

### IE-大量js循环，会导致浏览器假死



# JqGrid

### 自动出现滚动条

>​	**添加属性**
>
>```javascript
>    shrinkToFit:false,
>    autoScroll: false
>```

# LayUI

> **layui.load()在ajax使用时，ajax必须是异步模式，不然layui.load()不起作用。**
>
> ​	async. 默认是 true，即为异步方式，$.ajax执行后，会继续执行ajax后面的脚本，直到服务器端返回数据后，触发$.ajax里的success方法，这时候执行的是两个线程。
>
> ​	async 设置为 false，则所有的请求均为同步请求，在没有返回值之前，同步请求将锁住浏览器，用户其它操作必须等待请求完成才可以执行。

# 		Jquery

**输入数字，只有一个小数点，两位小数**

```javascript
function clearNoNum(obj){
               obj.value = obj.value.replace(/[^\d.]/g,"");  //清除“数字”和“.”以外的字符
               obj.value = obj.value.replace(/\.{2,}/g,"."); //只保留第一个. 清除多余的
               obj.value = obj.value.replace(".","$#$").replace(/\./g,"").replace("$#$",".");
               obj.value = obj.value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');//只能输入两个小数
               if(obj.value.indexOf(".")< 0 && obj.value !=""){//以上已经过滤，此处控制的是如果没有小数点，首位不能为类似于 01、02的金额
                   obj.value= parseFloat(obj.value);
               }
           }
```



# FlowAble

**act_ru_variable :** 存储运行时节点信息

## Flowable流程图乱码（Centos）

https://www.jianshu.com/p/488fc6955fec?tdsourcetag=s_pctim_aiomsg

# 代码检测

### Sonarqube

**官网：**https://www.sonarqube.org

### FindBugs

**官网：**http://findbugs.sourceforge.net

![1560419825196](/1560419825196.png)

# Github代理

```shell
 # Github
151.101.44.249 github.global.ssl.fastly.net
192.30.253.113 github.com
103.245.222.133 assets-cdn.github.com
23.235.47.133 assets-cdn.github.com
203.208.39.104 assets-cdn.github.com
204.232.175.78 documentcloud.github.com
204.232.175.94 gist.github.com
107.21.116.220 help.github.com
207.97.227.252 nodeload.github.com
199.27.76.130 raw.github.com
107.22.3.110 status.github.com
204.232.175.78 training.github.com
207.97.227.243 www.github.com
185.31.16.184 github.global.ssl.fastly.net
185.31.18.133 avatars0.githubusercontent.com
185.31.19.133 avatars1.githubusercontent.com
```

刷新DNS

>Windows：ipconfig /flushdns
>Ubuntu：sudo systemctl restart nscd

# 爬虫/国内代理IP

https://www.xicidaili.com/nn/

# java获取网络图片并返回

```java
@Value("${url}")
private String uri;

@Value("${clientCredentials}")
private String clientCredentials;


private static final Logger log = LoggerFactory.getLogger(ApprovalGraphServiceImpl.class);

@Override
public void generateProccessGraph(ApiReq apiReq, HttpServletResponse response,String processId) {
    genProcessDiagram(response,processId);
}

@Override
public void genProcessDiagram(HttpServletResponse response, String processId) {
    String base64ClientCredentials = new String(Base64.encodeBase64(clientCredentials.getBytes()));
    response.setContentType("image/png");

    URL url = null;
    try {
        url = new URL(UriUtils.encodePath(uri+processId+"/diagram", "utf-8"));
        HttpURLConnection conn = null;
        conn = (HttpURLConnection) url.openConnection();

        conn.setRequestProperty("Content-Type", "plain/text;charset=" +"utf-8" );
        conn.setRequestProperty("charset", "utf-8");
        conn.setDoInput(true);
        conn.setDoOutput(true);
        conn.setRequestMethod("GET");
        // 设置Authorization: Basic请求头
        conn.setRequestProperty("Authorization", "Basic "+base64ClientCredentials);
        conn.connect();
        try(InputStream input = conn.getInputStream();
            ServletOutputStream out = response.getOutputStream();){
            byte[] bytes = new byte[1024];

            for(int readLength = input.read(bytes); readLength > 0; readLength = input.read(bytes)) {
                out.write(bytes, 0, readLength);
            }
            out.flush();
        }

    } catch (IOException e) {
        e.printStackTrace();
    }


}
```

application.yml

```yaml
clientCredentials: admin:test

url: http://120.79.63.124:8080/flowable-rest/service/runtime/process-instances/

```

#  foreach 循环里进行元素的 remove/add 操作

https://yq.aliyun.com/articles/696311

foreach：通过iterator遍历，通过list操作增删，会导致iterator遍历时触发fail-fast

# AOP-LOG

### 一个个写日志，太麻烦，用aop切面

1、pom.xml

```xml
 <!--  aop   -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
```

2、启动类

`@EnableAspectJAutoProxy(proxyTargetClass=true,exposeProxy=true)`

Controller-aop-log

```java
/**
 * Description : controller类的日志切面
 *
 * @author Zhao Ke
 * @since 2019/11/16.
 */
@Aspect
@Component
public class WebLogAspect {

    private final static Logger logger = LoggerFactory.getLogger(WebLogAspect.class);

    /** 以 controller 包下定义的所有请求为切入点 */
    @Pointcut("execution(public * cn.com.intasect.isp.controller..*.*(..))")
    public void webLog() {}

    /**
     * 在切点之前织入
     * @param joinPoint
     * @throws Throwable
     */
    @Before("webLog()")
    public void doBefore(JoinPoint joinPoint) throws Throwable {
        // 开始打印请求日志
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();

        Object[] args = joinPoint.getArgs();

        Object[] arguments  = new Object[args.length];
        for (int i = 0; i < args.length; i++) {
            if (args[i] instanceof ServletRequest || args[i] instanceof ServletResponse || args[i] instanceof MultipartFile) {
                //ServletRequest不能序列化，从入参里排除，否则报异常：java.lang.IllegalStateException: It is illegal to call this method if the current request is not in asynchronous mode (i.e. isAsyncStarted() returns false)
                //ServletResponse不能序列化 从入参里排除，否则报异常：java.lang.IllegalStateException: getOutputStream() has already been called for this response
                continue;
            }
            arguments[i] = args[i];
        }
        String paramter = "";
        if (arguments != null) {
            try {
                paramter = JSONObject.toJSONString(arguments);
            } catch (Exception e) {
                paramter = arguments.toString();
            }
        }

        // 打印请求相关参数
        logger.info("========================================== Start ==========================================");
        // 打印请求 url
        logger.info("URL            : {}", request.getRequestURL().toString());
        // 打印 Http method
        logger.info("HTTP Method    : {}", request.getMethod());
        // 打印调用 controller 的全路径以及执行方法
        logger.info("Class Method   : {}.{}", joinPoint.getSignature().getDeclaringTypeName(), joinPoint.getSignature().getName());
        // 打印请求的 IP
        logger.info("IP             : {}", request.getRemoteAddr());
        // 打印请求入参
        logger.info("Request Args   : {}", paramter);
    }

    /**
     * 在切点之后织入
     * @throws Throwable
     */
    @After("webLog()")
    public void doAfter() throws Throwable {
        logger.info("=========================================== End ===========================================");
        // 每个请求之间空一行
        logger.info("");
    }

    /**
     * 环绕
     * @param proceedingJoinPoint
     * @return
     * @throws Throwable
     */
    @Around("webLog()")
    public Object doAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        Object result = proceedingJoinPoint.proceed();
        // 打印出参
        logger.info("Response Args  : {}", JSON.toJSONString(result));
        // 执行耗时
        logger.info("Time-Consuming : {} ms", System.currentTimeMillis() - startTime);
        return result;
    }


}

```

Service-log-aop

```java
/**
 * Description : controller类的日志切面
 *
 * @author Zhao Ke
 * @since 2019/11/16.
 */
@Aspect
@Component
public class ServiceLogAspect {

    private final static Logger logger = LoggerFactory.getLogger(ServiceLogAspect.class);

    /** 以 controller 包下定义的所有请求为切入点 */
    @Pointcut("execution(public * cn.com.intasect.isp.service..*.*(..))")
    public void webLog() {}

    /**
     * 在切点之前织入
     * @param joinPoint
     * @throws Throwable
     */
    @Before("webLog()")
    public void doBefore(JoinPoint joinPoint) throws Throwable {

        logger.info("========================================== Start ==========================================");
        // 打印请求 url
        logger.info("Signature    : {}", joinPoint.getSignature());
        logger.info("Args         : {}", JSON.toJSONString(joinPoint.getArgs()));
    }

    /**
     * 在切点之后织入
     * @throws Throwable
     */
    @After("webLog()")
    public void doAfter() throws Throwable {
        logger.info("=========================================== End ===========================================");
        // 每个请求之间空一行
        logger.info("");
    }

    /**
     * 环绕
     * @param proceedingJoinPoint
     * @return
     * @throws Throwable
     */
    @Around("webLog()")
    public Object doAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        Object result = proceedingJoinPoint.proceed();
        // 执行耗时
        logger.info("Time-Consuming : {} ms", System.currentTimeMillis() - startTime);
        return result;
    }


}

```

# Chrome插件

Resolution Test

Talend API Tester

Tampermonkey
WhatRuns

XPath Helper

Similar Sites

Site Palette

Words Discoverer

Dream Afar New Tab

Screen Shader

text.apphttps://chrome.google.com/webstore/detail/text/mmfbcljfglbokpmkimbfghdkjmjhdgbg

oneTab

Saladict

# XRebel & JRebel