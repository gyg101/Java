# Chapter 1: 业务层框架——Spring

​	func: full- stack轻量级开源框架：以IOC与AOP为内核，提供展现层SpringMVC和持久层Spring JDBC以及业务层事务管理邓聪多的企业级应用技术

## 	**IOC: 控制反转**

## 	**AOP: 面向切面编程**

优势：

> 方便解耦，简化开发
>
> AOP编程的支持
>
> 声明式事务的支持
>
> 方便程序的测试
>
> 方便继承各种优秀的框架
>
> 降低JavaEE API的使用难度



### 程序的耦合：

​	**耦合：程序间的依赖关系**

​		类之间的依赖

​		方法间的依赖



​	**解耦：**

​		降低程序间的依赖关系

​	优化： 编译期不依赖，运行时才依赖

​	解耦思路：BeanFactory工厂类

> ​		step 1: 使用反射进行类的加载，避免使用new关键字
>
> ​		step 2:: 通过读取配置文件来获取创建的对象全限定类名



在创建对象时，容易出现多次创建对象，执行效率低于单例模式。

​	解决措施：

> ​		step 1: 采用map存储配置文件中的key和value，在静态代码块中存储String和Object对象，并使用单例模式创建对象。
>
> ​		step 2: getBean()方法实现：通过在map中查找key来返回类对象。
>
> ​		step 3: 在持久层Dao中，用户的每次操作应该定义在成员方法中，而不应该是类变量。

## Spring核心一：IOC: 控制反转：

### 	一：理解：  将创建对象的控制权交给框架(eg: BeanFactory) ,降低依赖

​			应用直接获取资源的连接(依赖)	->  应用	通过框架(Factory)	获取资源

ApplicationContext的三个常用实现类: 

> ​	ClassPathXmlApplicationContext: 用于加载类路径下的配置文件，要求配置文件必须在类路径下
>
> ​	FileSystemXmlApplicationContext: 用于加载磁盘任意路径下的配置文件(必须有访问权限)
>
> ​	AnnotaionConfigApplicationContext: 用于读取注解创建容器



​	核心容器的两个接口引发出的问题：

​		ApplicationContext:  适用与单例对象		Spring默认采用此种方式

​			在构建核心容器时，创建对象采用的策略是立即加载的方式。即加载配置文件就立即执行创建对象

​		BeanFactory: 	适用于多例对象			

​			在构建核心容器时，创建对象采用的策略是延迟加载的方式。执行创建对象的过程，只有当BeanFactoy调用getBean(Resourse resourse)方法时才开始创建

​	**Bean中创建对象的三种方式：**

>   1. 方法一： 使用默认构造方法：
>
>      Spring中默认采用bean标签，利用Xml配置文件中属性id 和class 来创建对象。并且没有其他属性和标签；
>
>      若类中无默认构造方法，则无法创建bean对象。
>
>   2. 方法二： 使用普通工厂中的方法创建对象：
>
>      若要调用jar包类中的方法来创建对象，可以先用默认构造方法创建对象，再配置Factory-bean属性，配置Factoy-method属性调用其方法；
>
>   3. 方法三： 使用普通工厂中的静态方法创建对象：
>
>      若需调用静态类中的静态方法来创建对象，需要配置id属性、class属性和Factory-method属性，从而利用静态方法创建对象。
>
>      

bean对象的scope属性

​	function: 用于指定bean对象的的作用范围

> ​	singleton	单例对象(默认)
>
> ​	prototype:	多例对象
>
> ​	request: 	用于web应用的请求范围
>
> ​	session: 	用于web应用的会话范围
>
> ​	global-session: 	用于集群环境的会话范围(全局会话范围) 	当不是集群环境时，其相当于session

bean对象的生命周期：

> ​	singleton: 
>
> ​		出生：随着容器的创建而出生
>
> ​		活着：	容器存在，其就存在
>
> ​		死亡：	容器消失，单例对象就死亡
>
> summary: 单例对象的生命周期与容器的相同

> ​	prototype: 
>
> ​		出生：当使用对象时，才创建对象；		延迟创建
>
> ​		活着：只要对象还在使用过程中一直存在；
>
> ​		死亡：当对象长时间不使用，且不存在对其的对象引用，系统调用GC机制使其消失。

查看对象的出生、死亡：<--	bean= "" class="" init-method="init" destory-method="destory">

</bean>

### 依赖注入: Dependency Injection

​	依赖注入：降低程序建的耦合（依赖关系）

​	依赖关系的管理：交给spring来维护

所以我们可以在配置文件中实现对象的创建，并维护依赖关系

### 依赖关系的维护称之为依赖注入：

​	依赖注入：

#### 	能注入的数据：有三类数据：

> ​		1.基本数据类型与String类型
>
> ​		2.其他Bean类型：在配置文件中或注解配置过的bean
>
> ​		3.复杂类型/集合类型：

#### 	注入的三种方式：

> ​		1.构造函数提供：
>
> ​				使用标签constructor-arg: 其中的属性type , index, name , 以上三个属性用于给构造函数的指定参数赋值，
>
> ​				value 提供基本数据类型和String的数据类型, ref 提供其他bean类型数据	
>
> ​		adv: 在获取bean对象时，必须数据是必须的操作，否则不能创建对象。
>
> ​		disadv: 该变量bean对象的实例化方式，有些参数不使用时，也必须提供。
>
> ​		2.*使用set方法提供：
>
> ​				使用标签properties: 使用属性name, value, ref 
>
> ​				adv: 创建对象没有固定的限制，可以直接使用默认构造函数
>
> ​				disadv: 若某个对象必须有值，则获取对象是有可能set方法没有注入
>
> ​		3. 使用注解提供

注入集合数据: 

​	用于给List注入的标签： 

​		Array ,list, set 

​	用于给map注入的标签： 

​		map, properties 

## 二：Spring基于注解的IOC及IOC的案例

​	ioc的常用注解：

​	使用xml方式和注解方式实现单表的CRUD操作

​		持久层技术选择：dbutils

​	改造基于注解的ioc案例，使用纯注解的方式实现

​		spring的一些新注解使用

​	spring和Junit整合

### 		之前基于xml的配置： 

```xml
		<bean id = "accountSeriver" class = "cn.bank.service.impl.accountServiceImpl" 

​				scope="" init-method="" destory-mehtod="" >

​			<properties name="" value="" / ref = "" ></properties>

​		</bean>
```



用于创建对象的：与xml中的bean标签实现的功能是一致的

#### 	使用注解实现创建对象：

> ​	Component: 
>
> ​		func: 用于将当前类加到到spring的容器中
>
> ​		properties属性：
>
> ​			value: 用于指定bean中的id ,不写是默认当前类名，且首字母改小写
>
> ​	Controller: 一般用于表现层
>
> ​	Service: 一般用于业务层
>
> ​	Repository: 一般用于持久层

​	以上三个是spring为我们提供的明确的三层使用的注解，使我们的三层对象更加清晰；功能与Component一致。

#### 用于注入数据的：

​	与xml中的bean标签中写一个properties标签实现的功能是一致的

​	**Autowired:** 

​		func: 自动按照类型注入

​			只要容器内有唯一的一个bean对象类型与要注入的类型匹配，就可以出入成功

​			若IOC容器内不存在与注入对象匹配的对象，则报错；

​			若IOC容器内存在多个与注入对象匹配的类型时，则报错exceot 1 but fount 2 

​		出现位置：

​			**类的成员变量或成员方法**

​		细节：

​			在使用注解方法注入时，set方法就不是必须的了

​	**Qualifier:** 

​		func: 

​			用于在类中注入的基础上再按照名称注入，再给类成员注入时不能单独使用，需配合Autowire使用；但是在方法注入时可以

​		作用范围：

​		属性：value 用于指定注入bean的id 

​	**Resourse:** 

​		func: 直接按照bean的id注入，可以单独使用

​		属性：name: 用于指定bean的id 

**以上三个注入只能注入其他bean类型的数据，而String和基本数据类型的数据注入不能使用以上注解实现；**

**而集合类型的数据注入只能通过XML注解实现**

​		属性：

​			value：用于指定数据的值，可以使用spring中的SpEL(即spring中的el表达式)

​				SpEL 写法：${表达式};

#### 用于改变作用范围的：

​	与xml中bean标签的scope属性实现的功能是一致的

​	Scope: 

​		func: 用于指定注入对象的作用范围

​		属性：

​			value: 指定范围的取值： “singleton”、 “prototype”

​			

#### 与生命周期相关的：

​	与xml中的bean标签中的init-method和destory-method作用一致

​	preDestroy: 指定销毁方法

​	postConstruct: 指定初始化方法

### 方式一：基于xml的IOC配置: xmlns: 

​	<bean id = "accountService" class="cn.service.impl.AccountServiceImpl"></bean>

### 方式二：基于部分注解的配置: xmlns:context

​		<context:component-scan base-package="cn"></context:component-scan>

存在的两个问题：

​	① test测试单元中重复代码的问题：ssm整合中解决

​	② xml文件删除后的依赖处理问题。

注解： 

​	Configuration: 

​		指定当前类是配置类；

​		当配置类作为ApplicationContext对象创建时的参数，该注解可以不写。

Import: 

​		func: 用于导入其他的配置类

​		属性：

​			value: 用于指定其他配置类的字节码；当我们使用import注解后，有Import注解的类就是父配置类，导入的就是子配置类。

​	ConponentScan: 

​		指定spring在创建容器时需要扫描的包

​	Bean: 

​		func:用于把当前方法的返回值作为bean对象放入spring的ioc容器中。

​		属性：

​				name: 用于指定bean的id, 不写时默认为方法名

​		细节：当我们使用注解配置方法时，如果方法有参数，spring会去spring容器中寻找与参数类型匹配的bean对象，这与AutoWired注解是一样的。

PropertySource: 

​	func: 用于指定properties文件的路径

​	属性：value: 指定文件的名称和路径

​			关键字：classPath: 表示类路径下。

Qualifer: 

​	func: 单独使用时，用于指定数据源的名称

​	eg: public QueryRunner createQueryRunner(@Qualifer()"ds1" DataSource dataSource){

​				return new QueryRunner(dataSource);

​	}

### Spring整合Junit: 

WHY: 

​	1.应用程序的入口：main()方法

​		没有main()方法也能执行，Junit集成了一个main()方法。

​		该方法会判断哪些方法中有 @Test注解，junit就让有该注解的方法执行。

 	2. Junit不会在意我们是否采用Spring框架，在执行测试时，他根本不知道我们是否使用Spring框架，故其不会为我们读取配置文件创建Spring核心容器。
 	3. 由以上可知，当测试方法执行时，没有IOC容器，就算写了Autowired注解，也无法实现注入。
 	4. Junit的特性总结：①提供的API可以让你写出测试结果明确的可重用单元测试案例；② 提供三种方式显示你的测试结果；③ 提供了单元测试用例批运行的功能；④ 超轻量级且使用简单；⑤ 整个框架设计良好，易拓展

HOW: 

​	@Runwith(SpringJunit4ClassRunner.class)

​		func: 导入Spring整合Junit的jar包，用于指定Junit运行时需要导入的jar包

​		position: 在整个测试类上注解

​	@ContextConfiguration

​		func: 用于指定是基于xml配置文件，还是基于注解配置

​		属性：

​			locations: 用于指定xml配置的文件路径 + classPath 表示类路径

​			classes: 用于指定注解类所在位置

​			

## Spring核心二： AOP——面向切面编程

### Q1: 如何确保多个获取连接同时成功或同时失败？

​	Solution: 采用ThreadLocal，获取连接并将当前线程与其绑定

线程池与连接池：

​	release()—— 只是将当前线程/连接任务结束，将该线程/连接归还到线程池/连接池中，等待分配任务；

​	remove() ——可以将当前线程/连接与线程池/连接池解除绑定。

事务机制的工具类实现的功能：获取连接、提交事务、回滚事务、释放连接

## 三：动态代理：

​	eg: 生产者/消费者模型中的阻塞队列

​	Characters: 字节码随用随谁创建，随用随加载

​	Func: 不修改源码的基础上对方法增强

​	分类： 

​		基于接口的动态代理；

​		基于子类的动态代理：

​		1）基于接口的动态代理：

​			涉及的类：Proxy

​			提供者：JDK官方

如何创建接口的动态代理对象：

​		使用Proxy类中的newProxyInstance方法：

​		创建代理对象的要求：要求实现类至少实现一个接口；若没有则不能实现。

​		参数：

​			ClassLoader: 类加载器，用于加载代理对象字节码的，与被代理对象使用相同的类加载器，固定写法。

​			Class[]: 	字节码数组，用于让代理对象与被代理对象有相同的方法，固定写法。

​			InvocationHandler: 用于提供增强的代码，让我们写如何代理，一般我们写一个该接口的实现类，通常情况下都是匿名内部内，但不是必须。

​			此接口的实现类都是谁用谁写。

​		2）基于子类的动态代理

​			涉及的类：Enhancer

​			提供者：第三方cglib库

如何创建接口的动态代理对象：

​		使用Enhancer类中的create方法：

​		创建代理对象的要求：要求实现类不能是最终类

​		参数：

​			Class: 用于加载被代理对象字节码的。

​			Callback: 用于提供增强的代码，让我们写如何代理，一般我们写一个该接口的实现类，通常情况下都是匿名内部内，但不是必须。

​			此接口的实现类都是谁用谁写。

​			我们一般写的都是该接口的子接口实现类，MethodInterceptor

动态代理的应用：

可以代理Service, 采用proxyAccountService , 

### AOP: 面向切面编程

​	def: 面向切面编程，**通过预编译的方式和运行期动态代理**的方式实现程序功能，AOP是OOP的延续，是**函数式编程**的一种衍生范式。

​	func: 在程序运行期间不修改源码对已有方法进行增强。

​	adv: 减少重复代码；提高运行效率；维护方便。

关于代理的选择：是否需要实现接口，若是选基于接口的动态代理；是否是最终类，若不是，选基于子类的动态代理。

相关术语：

> ​	JointPoint: 连接点	def: 指被拦截到的点
>
> ​		若选择基于接口的动态代理，则为接口中所有的方法
>
> ​	Pointcut: 切入点		def:  要对哪些JointPoint进行拦截的定义
>
> ​		若选择基于接口的动态代理，则为接口中被增强的方法。
>
> ​	Advice:  通知/增强		def:  拦截到 Joint之后要做的事就是通知。
>
> ​		分类： 前置通知；后置通知；异常通知；最终通知；环绕通知
>
> ​	Introduction: 引介 		def: 一种特殊的通知不修改类代码的前提下，在运行期为类动态的添加一些方法或Field 
>
> ​	Target: 目标对象		def: 被代理的对象
>
> ​	Weaving: 织入			def: 把增强应用到目标对象来创建新的的代理对象的过程
>
> ​	Proxy: 代理			def: 一个类被AOP增强后，生成的对象。
>
> ​	Aspect: 切面		是切入点和通知的结合



Spring中基于xml的aop配置步骤：

 1. 把通知bean也交给spring来配置

 2. 标签bean:config表明开始AOP的配置

 3. 标签bean:aspect开始配置切面

    属性：id : 提供给切面的唯一标识

    ​			ref: 指定通知类bean的ID 

    4.在aop:aspect标签内部使用对应标签来配置通知的类型。

    ​	aop:before  表示配置前置通知

    ​		method属性： 用于指定Logger类中的哪个方法设置为前置通知。

    ​		pointcut属性：用于指定切入点表达式，该表达式的含义是该切入点是对业务层中的哪些方法进行增强。

    ​		切入点表达式的写法：

    ​		关键字：execution(表达式)

    ​		表达式：	访问修饰符	返回值类型	包名.包名.包名.包名...类名.方法名。

    ​			表达式中的通配符：* *..* *(..)

    ​				访问修饰符可以省略；

    ​				包名可以用*.表示，包名可以用 *..表示当前包及其子包；

    ​				类名和方法名都可以用 *表示；

    ​				参数列表：可以直接写数据类型：

    ​						直接类型 直接写名称

    ​						引用类型写包名.类名的方式

    ​			全通配写法：* *.. * .* (..)

    aop:pointcut	表示配置环绕通知

    ​	属性：id 表示环绕通知的属性

    ​				expression="execution(表达式)"

    ​	配置了环绕通知后，前置通知/后置通知等可以采用pointcut-ref代替pointcut

    aop的注解：

    > ​	@Aspect置于通知事务类上
    >
    > ​	@Pointcut("exection(* com.bank.service.impl.AccountService(..))")
    >
    > ​	@Before置于前置通知方法上
    >
    > ​	@AfterReturning
    >
    > ​	@AfterThrowing
    >
    > ​	@After
    >
    > ​	@Around环绕通知

## 四：Spring中的JdbcTemplate

### 1.Spring基于aop的事务控制：

​	JdbcTemplate: 

​		func: 用于与数据库交互的，实现对表的CRUD操作

​		how: 如何创建该对象	new 或者xml中配置org.springframework.jdbc.core.JdbcTemplate

​		对象中常用的方法：execute(String sql) ; update(String sql, String parm1;String parm2); query(String sql, new BeanPropertyRowMapper(),1000f); query(String sql,1000f,new BeanPropertyRowMapper());

​	spring中的事务控制：

​		基于xml的: 

​			可以使用spring提供的JdbcTemplateSupport父类，来实现减少重复性代码；

​		基于注解的

​			可以自己写一个父类JdbcTemplateSupport, 将重复代码放入父类，子类调用父类方法即可

​			若使用基于注解的aop事务控制，在执行after-returning后置通知和after最终通知时，两者的执行顺序不能保证；故我们常使用around环绕通知

### 2.Spring的声明式事务控制——配置实现xml/注解

​	spring基于xml的声明式事务控制配置 实现四步：

> ​		①配置事务管理器
>
> ​		②事务通知：
>
> ​				此时需要导入事务的约束 tx名称和约束，同时需要aop的约束
>
> ​				使用tx:advice标签配置事务通知： 
>
> ​						属性：id: 给事务通知起的标识	transaction-manager: 给事务通知指明一个事务管理器引用
>
> ​		③配置AOP中的切入点表达式
>
> ​		④指明事务通知与切入点表达式之间的对应关系
>
> ​		⑤配置事务的属性
>
> ​				位置：在事务的通知tx:advice标签内部
>
> ​				属性：
>
> ​						isolution: 用于指定事务的隔离级别
>
> ​						propagarion: 用于指定事物的传播行为：默认为REQUIRED表示一定有事务，适合于增删改的方法；查询可以使用SUPPORT；
>
> ​						read-only: 用于指定事务是否只读，只有查询方法设置为true,其余设置为false;
>
> ​						timeout: 用于指定事务的超市时间，默认永不超时；如果制定了时间，以秒S为单位；
>
> ​						rollback:  用于指定一个异常，当产生该异常时，事务回滚；当产生其他异常时，事务不回滚；默认所有事务都回滚；
>
> ​						no-rollback-for: 用于指定一个异常，当产生该异常时，事务不回滚；当产生其他异常时，事务回滚；默认所有事务都回滚；

### 3.Spring基于编程式事务控制：@tx:advice 	@transactional

# Chapter 2: 表现层框架——SpringMVC

## pre: 

​	C/S架构与B/S架构：java适合于做B/S架构的软件系统

​	springMVC在B/S架构中Service端的三层框架：

![](C:\Users\GYG\Desktop\ss_win_temp\SpringMVC.png)

### 	springMVC的优点：

​		清晰的角色划分(模块)：

> ​			前端控制器：
>
> ​			处理器映射器：
>
> ​			处理器适配器：
>
> ​			视图解析器：
>
> ​			处理器或页面控制器：
>
> ​			验证器：
>
> ​			命令对象：
>
> ​			表单对象：

### 	springMVC与Struct2的区别：

| adv analysis | SpringMVC                                                    | Struct2      |
| ------------ | ------------------------------------------------------------ | ------------ |
| com          | 表现层框架，基于MVC框架；底层离不开servletAPI; 处理请求的机制都是一个核心控制器 |              |
| diff         | 入口：Servlet                                                | 入口：Filter |
|              | 基于方法设计                                                 | 基于类设计   |

## 创建流程：

​	**1.启动服务器，加载一些文件**

> ​		servlet对象dispatcherServlet对象创建；
>
> ​		springMVC.xml文件被加载(在web.xml中指定);
>
> ​		HelloController被创建成对象(通过注解);

​	**2.发送请求，后台处理请求**

​		通过index.jsp找到hello页面——》通过index.jsp中的控制器dispatcherServlet对象——》指向映射对象(执行方法)  ; 同时利用视图解析器InternalResourceViewResolver指向跳转页面success.jsp——》返回指挥中心——》从Servlet返回结果对象。

### SpringMVC中的组件：

#### 	前端控制器——》处理器映射器	注解@RequestMapping(path="")

​		**requestMapping注解：可注解类，也可注解方法；**

​			属性：

> ​					value: 等同于path	若无显示404	ERROR
>
> ​					method: 指定请求的类型	若无显示400	ERROR
>
> ​					params: 指定参数名	若无显示405	ERROR
>
> ​					headers: 发送的请求中必须包含的请求头

​	前端控制器——》处理器适配器	注解@RequestMapping(path ="")中的path 

​	前端控制器——》视图解析器

### 请求参数的绑定：

​	param.jsp中body内部写<a herf= "parm/pramTest?username=haha&password=123456">

### 解决中文输入乱码问题：——过滤器：Filter 

​	Filter-name	

​	Filter-mapping：

自定义类型转换器：

​	String 	——》 Integer

​	String 	——》Date

参数列表中的

> ​	requestParam: value属性等同于name；同时在跳转页面中也需写上相应的参数
>
> ​	requestBody：获取整个对象
>
> ​	pathVariable: 	restful编程风格	区别于以前的path注解中加入/user/find; 采用同样的path, 不同的method=post/get/put
>
> ​	modelAttribute:  可修饰方法，也可修饰参数	用在方法上时，可优先执行该方法，再执行指定的testModelAttribute()方法；若用在参数上，可将modelAttribute()方法中的数据存储在map中，然后给对象中未赋值的属性赋值。
>
> ​	sessionAttributes：
>
> ​		参数value={""} type={String.class} 
>
> ​		方法中的参数：ModelMap SessionStatus

## Response相应的几种方式: 

#### 返回值类型：

> ​	有返回值；无返回值void
>
>  	1. 请求转发与重定向	request.getRequestDispatcher("WEB-INF/page/success.jsp").forword(request,response); 	response.sendRedirect(request.getContextPath() + "/index.jsp";
>  	2. 关键字转发或重定向  forword:/WEB-INF/pages/success.jsp    reDirection: index.jsp
>  	3. 异步请求Ajax(Json)     

ResponseBody响应Json数据

### 文件上传：

​	必要前提：

> ` 1.form表单的enctype取值必须是：multpart/form-data
>
> 	2. method属性取值必须是post
> 	3. 提供一个文件选择域<input type="file" />

​	1）传统方式上传文件：上面三步，然后取路径，创建文件存储到"/uploads/"文件夹下，判断该路径是否存在，不存在则创建。

​	2）SpringMVC上传文件方式：先由用户发送一个request到前端控制器，由文件解析器CommonsMutilpartResolver解析request返回upload, 然后由Controller进行fileupload(MutilpartFile upload)

​	3）跨服务器上传文件：定义存储地址String path; 创建客户端；将客户端与图片路径绑定；文件上传

### MVC异常处理：

​	由前端控制器将异常以友好界面方式提示用户异常；而不是直接将异常信息传递到上一级；

​	实现：依靠异常处理器组件处理异常信息

​	steps: 编写自定义异常类(提示信息)；编写异常处理器；配置异常处理器

### 拦截器：

​	diff: 区别于过滤器，拦截器**只能在SpringMVC框架中使用**，只会拦截访问的控制器方法; 而后者是Servlet规范中的一部分，任何Java-web工程都能使用，在url中配置/*后，可**对所有要访问的资源进行访问**。

​	实现的接口：HandlerInterceptor

​	重写的方法：

​		preHandle(); 	controller方法执行前拦截的方法，可使用request或response实现跳转到指定的页面。return true;表示放行；反之表示不放行。

​		postHandle();  	controller方法执行后执行的方法，在JSP视图执行前；

​		afterCompletion(); 	在JSP页面执行后执行；request或response不能再跳转页面。

# Chapter 3: 持久层——Mybatis

## 	一：mybatis的入门和概述：

​		框架def: 抽象的构件及构件实例间交互的方法；可被应用开发者定制的应用骨架。

| 三层框架 | 作用             |
| -------- | ---------------- |
| 表现层   | 用于展示数据     |
| 业务层   | 用于实现业务需求 |
| 持久层   | 用于数据库交互   |

​	Jdbc是规范；Spring的JdbcTemplate和Apache的DBUtils都只是工具类；

​	Mybatis框架：持久层框架；封装了Mybatis操作的许多细节，使开发者只需关注sql语句本身，无需关注注册驱动、获取连接、遍历结果集等繁杂过程；使用了ORM思想实现了结果集的封装。

​	**ORM: Object Relational Mapping 对象关系映射**

​		func: 将数据库表与实体类的属性对应起来，让我们操作实体类就能操作数据库表。

​		注意：常需保证实体类中的属性与数据库表中的字段名称相同。

### mybatis环境的搭建：

> ​	step 1: 创建maven工程并导入坐标；
>
> ​	step 2:  创建实体类和dao的接口；
>
> ​	step 3: 创建Mybatis的主配置文件 SqlMapConfig.xml
>
> ​	step 4: 创建映射配置文件 IUserDao.xml  

注意事项：

> ​	1.在Mybatis中他把持久层的操作接口名称和映射文件叫做Mapper, 所以IUserDao和IUserMapper是一样的；
>
> ​	2.在IDEA中创建目录与创建包是不一致的，需要一级一级创建目录；
>
> ​	3.映射配置文件的mapper标签namespace属性必须是dao接口的全限定类名。
>
> ​	4.配置文件的文件路径(位置)应与实体类dao接口的包结构相同
>
> ​	5.映射配置文件的操作配置(select)，id属性的取值必须是dao接口的方法名。

入门案例：

> ​	读取配置文件；			绝对路径、相对路径都不可取；实际开发中使用类加载器，但其只能读取类路径的配置文件；使用ServletContext对象的getRealPath();
>
> ​	创建SqlSessionFactory工厂；	//构建者模式	adv: 把对象的创建细节隐藏，使使用者直接调用方法即可
>
> ​	创建SqlSession对象；		//工厂模式		adv: 解耦，降低类之间的依赖关系
>
> ​	创建dao接口的代理对象；		//代理模式		adv: 不修改源代码的基础上对已有方法增强。
>
> ​	执行dao中的方法；
>
> ​	释放资源；

注： 此时都是不写dao的实现类

### 自定义mybatis框架

​	主要任务：

> ​		创建代理对象；proxy.newProxy().newInstance(class 被代理对象的类字节码, 实现的接口的类字节码数组,  如何实现增强(invoke();));
>
> ​		实现findAll()接口。		mapper: key ——》接口的全限定类名+方法名；value——》String sql; 查询的结果集resultSet List<User>

自定义Mybatis环境基本步骤：

​	step 1: SqlSessionFactoryBuilder接收SqlMapConfig.xml文件流，构件SqlSessionFactory对象

​	step 2: SqlSessionFactory读取SqlMapConfig.xml中连接数据库和mapper映射信息。用来生产出真正操作数据库的SqlSession对象

​	step 3:  SqlSession对象的两大作用：① 创建接口代理对象； ②定义增删改查方法。

​	step 4: ① 

​	step 5 : 封装结果集：

## 二：: Mybatis中基于代理的CRUD: 

​	<insert id="saveUser" parameterType="com.thoughtworks.domain.User">	

​		<selectKey id = "" properties="id" resultType="int"  ></selectKey>		获取插入数据的id 

​		insert into user() values();

​	</insert>

### Mybatis中参数的深入：

​	OGNL表达式：

​		Object Gragh Navigation Language

​		通过对象的取值方式来获取数据，写法上吧get忽略掉了eg: user.getName  ——》 user.name;

​		<select id="findByName" parameterType="com.thoughtworks.domain.QueryVo" resultType="com.thoughtworks.domain.User">	

​			select * from user where username like #{user.name};

​		</select>

​	传递Pojo包装对象：实现通过包装对象中的属性进行查找对象。

当数据库表中的列名和Java类中的字段名不同时：

​	1. 进行查询数据库时，会存在ID不匹配问题，原因是Mysql数据库中的区分大小写，可采用共sql语句起别名(as)的方式进行优化

​	2. 或者采用ResultMap加载，配置 查询结果的列名与实体类的属性名对应关系

```
<resultMap id="userMap" type="com.throughtworks.domain.user">

​	<!-- 主键字段的对应 -->

​	<id properties="userId" column="id">

​	<!-- 非主键字段的对应 -->

​	<result properties="userName" column="username">

​	<result properties="userAge" column="age">

​	<result properties="userSex" column="sex">

</resultMap>
```



### Mybatis实现DAO层应用开发

​	Mybatis不仅有自己的代理实现dao接口，也支持用户自己实现接口；

​	此时可采用impl.UserImpl类中实现IUserDao接口，内部声明factory对象，在构造函数中初始化该对象。

​	注意无论是CRUD中的哪个命令，本质上都是操作update()方法或

### Properties标签的使用及细节：

​	func: 可在标签内部实现配置数据库信息，也可以通过属性引用外部的文件信息。

​	**resource属性：常用的**

​		func: 用于指定配置文件的位置，是按照类路径的写法来写的，并且必须在类路径下

​	**url属性：** 

​		func: 是要求按照url的写法来写地址

​		def: Uniform Resource Locator 统一资源定位符：

​			eg: http://localhost:8080//test/user 协议 + IP + 端口号 + URI (统一资源标识符)

### TypeAliases标签的使用：

​	func: 使用typeAliases配置别名，只能配置domain中类的别名

​	<typeAlias type="com.thoughtworks.domain" alias="user">  type属性指定实体类全限定类名，alias属性指定别名 当指定别名后不再区分大小写

​	<package name="com.thoughtworks.domain"> 此时该包下的所有实体类都会注册别名，且别名都是类名，不区分大小写

区别于Mapper标签中的<package>属性，用于指定接口所在的包，指定后不需要在写Mapper和resource或者class了

## 三: Mybatis中的连接池与事务控制：

#### 	连接池的使用及分析：

​		posotion:  配置的位置SqlMapConfig.xml中的dataSource标签，type属性决定了采用何种连接方式

​			type属性：

​				POOLED: 	采用传统的javax.sql.DataSource规范中的连接池，mybatis有针对规范的实现

​				UNPOOLED: 	采用传统的获取连接的方式，但是并没有采用线程池的概念

​				JNDI: 	采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器能拿到的连接不同；注 ： 如果不是web或者maven工程，是不能使用JNDI的。

​			课程中使用的就是Tomcat服务器，采用连接池就是dbcp连接池。

#### 	事务控制的分析：

​		事务def: 事务是应用程序中一系列严密的操作，所有操作要么全部成功，否则所有更改必须全部撤销，即事务具有原子性。

​		事务的四大特性ACID: 

| 事务的四大特性 |                                                              |      |
| -------------- | ------------------------------------------------------------ | ---- |
| Atomicity      | 要么全部完成，要么全部不完成，不能停滞在中间过程，一旦发生异常，需回滚到事务开始前的状态 |      |
| Correspondence | 在事务开始前和事务开始后，数据库的完整性约束没有被破坏过     |      |
| Solation       | 串行化，即确保每一事务在系统中认为只有该事务在使用系统，使同一时间只有一个请求用于同一数据 |      |
| Durability     | 事务完成后，该事务对数据库的更改百年持久保存在数据库中，不会被回滚 |      |

​		不考虑隔离性会产生的3个问题：防止事务操作间的混淆

​		事务的四种隔离级别：RU	RC 	RR	S

| 不同的隔离级别 | 读取数据一致性                           | 脏读 | 不可重复读 | 幻读 |
| -------------- | ---------------------------------------- | ---- | ---------- | ---- |
| RU             | 最低事务级别、只保证不读取物理损坏的数据 | Y    | Y          | Y    |
| RC             | 语句级                                   | N    | Y          | Y    |
| RR  (default)  | 事务级                                   | N    | N          | Y    |
| Serialable     | 最高级别，事务级                         | N    | N          | N    |

#### 	Mybatis基于XML配置的动态SQL语句使用：

​	if标签：

​	where标签：eg: 

<select id="findUserInIds" resultMap="userMap" parameterType="user">
		select * from user where 1=1 
		<if test="userName != null"
			and username = #{userName}
		</if>
</select>

​	foreach标签和sql标签：func: sql语句中的in ; sql标签作用：抽取重复代码

​		eg: 

```
<select id="findUserInIds" resultMap="userMap" parameterType="queryvo">
​		select * from user
​		<where >	
			<if test="ids != null and ids.size() > 0">
				<foreach collection="ids" open="and id in (" close=")" item="id" separator=",">
					#{id}
				</foreach>
			</if>

​		</where>
​</select>
```

#### 	Mybatis中的多表操作：

​		一对多：一个用户有多个账户	

​		一对一：？

​		多对一：从多个账单中取出一个账单，Mybatis就把多对一看成了一对一 left outer join 复合外键

​		多对多：

## 四: Mybatis中的延迟加载

| 加载机制   | 针对数据库中的多表连接(关键在于关联的对象时多或一) | (eg: 根据用户信息查询账户)         |
| ---------- | -------------------------------------------------- | ---------------------------------- |
| 延迟加载： | 多对多、一对多                                     | 根据用户，查询相关联的账户信息     |
| 立即加载： | 多对一、一对一                                     | 根据账户信息，查询相关联的用户信息 |

### Mybatis中的缓存：

#### 	缓存：

​		def: 存在于内存中的临时数据

#### 	func: 

​		减少与数据库的交互，提高执行速率

#### 	使用范围： 

​		经常查询且不经常修改的；数据的正确与否对最终的结果影响不大的。

#### 	不适用的情况：

​		经常改变的数据；数据的正确与否对最终的结果影响很大的。eg:  银行的汇率；股市的牌价；商品的库存

#### 	一级缓存：

​			它指的是Mybatis中SqlSession对象的缓存。

​			当我们执行查询后，查询的结果会同时执行SqlSession为我们提供的一块区域(Map);

​			当我们再次查询同样的数据时，Mybatis会从SqlSession中查询是否有，若有直接使用；若无重新连接数据库并查询，同时存入SqlSession缓存中；

​			当SqlSession对象消失时，Mybatis中的一级缓存也就随之消失。

#### 	二级缓存：

​		它是Mybatis中的SqlSessionFactory对象的缓存，由用一个SqlSessionFactory对象创建的SqlSession共享其缓存。

### Mybatis中的注解开发：针对映射配置xml

#### 	单表的CRUD操作：

​		@Select("select * from user where username like #{username}") ;@Delete("delete from user where id=#{id}")

#### 	多表查询：

​		**如何解决实体类中的属性与数据库表中的的字段不匹配问题？**

​			Solution 1: 将实体类中的属性与数据库表中的字段一一对应

​			Solution 2: 使用Results注解

​				使用@Results(id="userMap",values={

​				Result{id=true,column="id" property="id"}

​				Result{column="username" property="userName"}

​				}) 

​		**如何一对一查询？**

​			使用Results注解中Result中one=@One(select("com.thoughtworks.dao.IUserDao.findAllUser"), fetchType=FetchType.EAGER)

​		**如何实现一对多查询？**

​			使用many=@Many

#### 	缓存的配置：

##### 		**开启二级缓存的使用：**

​			**@CacheNamespace(blocking="true")注解**

​			**xml中配置settings:** 

​				<settings>

​					<setting name="cacheEnabled" value="true"/>

​				</setting>

# chapter 4: SSM整合

​	
