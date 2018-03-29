## Spring

### SpringMVC项目的创建
1. 创建maven-archetype-webapp项目。
2. pom.xml导入Spring核心包。
3. File-Project Structure消除Spring已有的支持。
4. 项目右击-Add framework support添加SpringMVC支持。 

### 注解
* @Controller:用于指示Spring类的实例是一个控制器。保证Spring能找到控制器需要完成两件事情：
1. 在SpringMVC的配置文件web.xml中引入applicationContext.xml文件。一般创建项目的时候就已经引入完成。
2. 在applicationContext.xml中添加`<context:component-scan/>`元素，该元素的功能为：启动包扫描功能，以便注册带有@Controller、@Service、@repository、@Component等注解的类成为Spring的Bean。`base-package`属性指定了需要扫描的类包。

* @RequestMapping:指示Spring用哪一个类或方法来处理请求动作，该注解可以用于类或者方法。

* @RequestParam:用于将指定的请求参数赋值给方法中的形参。例如：  
`@RequestParam(value="loginname", require=true, defaultValue="admin")`

* @PathVariable:获取请求URL中的动态参数。@PathVariable注解只支持一个属性value，类型为String，表示绑定的名称，如果省略则默认绑定同名参数。例如：  
`@RequestMapping(value="/pathTest/{userId}")`  
`public void pathTest(@PathVariable Integer userId)`

* @RequestHeader:用于将请求的头信息区数据映射到功能处理方法参数上。例如：  
`public void requestHeader(@RequestHeader(value="User-Agent") String userAgent)`

* @CookieValue:用于将请求的Cookie数据映射到功能处理方法的参数上。例如：  
`cookieValueTest(@CookieValue(value="JSESSIONID", default="") String sessionId)`

* @RequestBody:用于获取post请求中的json数据。@RequestBody根据json数据，转换成对应的Object(需要实现可序列化的接口)。  
`ObjectMapper`类是Jackson库的主要类。它提供一些功能将Java对象转换成对应的JSON格式的数据。

* @ResponseBody:会将集合数据转换成json格式并将其返回给客户端。

## MyBatis

### ORM(对象/关系数据库映射)
* ORM框架可作为面向对象编程语言和数据库之间的桥梁，完成面向对象的编程语言到关系数据库的映射。ORM框架是面向对象程序设计语言与关系数据库发展不同步时的中间解决方案。ORM工具可完成对象模型和关系模型之间的相互映射。在ORM框架中，持久化对象是一种媒介，应用程序只需要操作持久化对象，ORM框架则将这种操作转换为底层数据库操作。

### MyBatis体系结构
MyBatis中的常用对象有SqlSessionFactory和SqlSession。

* SqlSessionFactory是MyBatis的关键对象，它是单个数据库映射关系经过编译后的内存镜像。SqlSessionFactory对象的实例可以通过SqlSessionFactoryBuilder对象来获得，而SqlSessionFactoryBuilder则可以从XML配置文件或一个预先定制的Configuration的实例构建出SqlSessionFactory的实例。SqlSessionFactory主要用于调用openSession()方法来创建SqlSession对象。

* SqlSession是持久化操作的对象，类似于JDBC中的Connection。使用完SqlSession后关闭很重要，应该确保使用finally块来关闭它。 

### MyBatis事务管理
* 事务是一个最小的逻辑单元，整个事务不能分开执行，要么事务内的操作都执行，要么都不执行。MyBatis的事务设计重点是Transaction接口，Transaction接口的两个实现类是JdbcTransaction和ManagedTransaction。

* MyBatis的事务管理分为两种形式:
1. 使用JDBC的数据管理机制。即利用java.sql.Connection对象完成对事务的提交、回滚和关闭操作。
2. 使用MANAGED的事务管理机制。对于这种机制，MyBatis自身不会去实现事务管理，而是让容器来实现对事务的管理。

### MyBatis缓存机制
* MyBatis的查询缓存分为一级缓存和二级缓存。一级缓存是SqlSession级别的缓存，二级缓存是mapper级别的缓存，二级缓存是多个SqlSession共享的。

#### 一级缓存

* 每个SqlSession对象中都有一个HashMap用于缓存数据，不同的SqlSession之间的缓存数据区域(HashMap)是互相不影响的。

* 一级缓存的作用域是SqlSession范围的，当在同一个SqlSession中执行两次相同的sql语句时，第一次执行完毕将会将数据库中查询的数据写到缓存中，第二次查询时会从缓存中读取数据，不会再去数据库查询。但是如果SqlSession执行DML操作(insert/update/delete)，并且提交到数据库，MyBatis则会清空SqlSession中的一级缓存，避免出现脏读现象。

* 当一个SqlSession结束后(close后)该SqlSession中的一级缓存也就不存在了。MyBatis默认开启一级缓存。

#### 二级缓存

* 二级缓存是mapper级别的缓存。使用二级缓存时，多个SqlSession使用同一个Mapper的sql语句去操作数据库，得到的数据会存在二级缓存区域，它同样是使用HashMap进行数据存储。相比一级缓存，二级缓存的范围更大，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的。

* 二级缓存是多个SqlSession共享的，其作用域是mapper的同一个namespace。不同的SqlSession两次执行相同的namespace下的sql语句，且向sql中传递的参数也相同，即最终执行想同的sql语句，则第二次查询会从缓存中获取第一次查询并存入缓存的数据。

* 默认没有开始二级缓存，需要在setting全局参数中配饰开始二级缓存。`<setting name="cacheEnabled" value="true">`。同时需要开启mapper的namespace下的二级缓存。`<cache eviction="LRU" flushInterval="60000" size="512" readOnly="true">`。以上配置创建了一个LRU缓存，并每隔60秒刷新，最大存储对象512个，并且对象是只读的。

* eviction：收回策略。  
LRU:最近最少使用的策略，移除最长时间不被使用的对象。  
FIFO:先进先出策略，按照对象进入缓存的顺序来移除它们。  
SOFT:软引用策略，参考JVM软引用。  
WEAK:弱引用策略，参考JVM若引用。

* 使用二级缓存时，与查询结果映射的Java对象必须实现Serializable接口的序列化和反序列化操作。因为二级缓存数据存储介质多种多样，不一定在内存，有可能是硬盘或者远程服务器。
