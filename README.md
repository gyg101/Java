# Java
This is my Java Mysql learn notes
一、数据库基本概念：
	DB: 
	DBMS:核心
	SQL: 
summory: DBMS -> 执行 -> sql ->操作管理 -> DB
二、Mysql常用命令：

	功能		命令
	登录数据库	mysql -u root -p YourPassword	若无密码输入 mysql -u root
	退出数据库	exit;
	查询所有的数据库	show databases;
	使用某个数据库	use test;
	显示该数据库中的表	show tables;
	创建数据库	create database DBname;
	导入数据库	sourse dataBaseAbsolutePath+DBName.sql
	
Mysql中的具体操作命令：
单行处理函数: 

	lower 	select lower(dname) from dept;
	upper	select upper(dname) from dept; 
	substr	select substr(1,2) from dept;	//截取字符串
	trim	//去除空格
		//转换为日期
	round	取整
	rand	//生成0-1之间的随机数

	ifnull(,)	//凡是有null参与运算的时候 结果都为null
	case ... when ... then ...else ... end ... //相当于if
多行处理数据：
分组函数：//自动忽略null

	count
	sum
	max
	min
	avg

日期函数: 

	data_format
	str_to_data	
	formate 	//设置千分位
	关键字的执行顺序：	from ,where ,group by ,having ,select , order by 

三、数据库的结构
	1.数据库中最基本的单元是表：
		表包括行(row)和列(column)，行被称为数据(记录)；列被称为字段
		每个字段都有字段名、数据类型、约束等属性。约束有很多，其中有一种叫唯一性约束。
	2.SQL分类：

		*DQL:
		*DML:
		DDL:
		TCL: 
		DCL: 
	3.导入数据库	 sourse 数据库的绝对路径(不能有中文！)
	4. 数据库中表连接查询的分类: 
		连接查询按表连接的方式分类：
		4.1 内连接
			等值连接
			非等值连接
			自连接(将一张表看成两张表，然后连接)
		4.2 *外连接		可以显示左表或右表中的所有记录
			左外连接(右连接)
			右外连接(左连接)
		4.3 全连接(不讲)
	5. 子查询：
		where之后的子查询	
		from之后的子查询	
		select之后的子查询
			子查询必须返回一条查询语句
	6. union: 
		union的效率高于表的连接操作: 因表的连接是满足笛卡尔积数量关系，但是union将乘法变成了加法
	7.limit : 
		func: 将查询结果的部分取出来，通产用于分页查询中；
		提高用户体验，可以一页一页查看
		eg: select eanme,sal from emp order by sal asc limit 5;
	8.通用分页
		limit (pageNo - 1) * pageSize , pageSize;
		select ... from ... 

数据库DQL语句大总结: 

	select 
		字段 
	from 
		表名 
	where 
		筛选条件 
	group by 
		分组要求 
	having 
		筛选条件2(针对分组函数) 
	order by 
		顺序或逆序 
	limit 
		起始索引, 长度; 
	
	执行顺序: from -> where -> group by -> having -> select -> order by -> limit 


四、(DDL : create drop alter) 建表语句: 

		1)创建: create tableName(字段1 数据类型, 字段2 数据类型, 字段3 数据类型); 
		2)删除: drop table t_student;	或drop table if exist t_table;	前者若不存在则报错；后者不存在该表也不报错；
	mysql中的数据类型: 
		varchar	char	int	bigint	float	double	date	datetime	clob	blob
	varchar(最长255) : 
		可变长度的字符串; 
		比较智能，节省空间，会根据实际情况动态分配内存空间
		adv: 节省空间
		disadv: 需要动态分配内存空间，速度慢
	char(最长255) : 
		定长字符串; 
		不管实际的数据长度是多少，都会分配固定长度的空间去存储数据。使用不当时，会造成空间的浪费
		adv: 不需要动态分配空间，速度快
		disadv: 使用不当可能造成空间浪费
	int : 数字的整数型，相当于java的int
	bigint: 数字的长整型，相当于java中的long
	float: 
	double: 
	date :短日期类型
	datetime: 长日期类型
	clob: 字符大对象(Character Large OBject),超过255的字符大对象都需要用clob存储
	blob: 二进制大对象(Binary Large OBject) 存储图片、声音、视频等流媒体数据。结合IO流使用
五、DML修改表的记录(insert delete update)语句: 
	  1)插入数据: insert into 表名(字段名1, 字段名2, 字段名3) value(值1, 值2, 值3);	eg:  insert into t_tableName(no) value(1);
注: 字段名可以省略，此时相当于全写上字段名，需写上所有的值；

		将字符串转换为日期格式: str_to_date(字符串1, 日期格式); 	str_to_date('10- 01-1999', '%m-%d-%Y'); 
		mysql日期格式: %Y %m %d %h %i %s 年 月 日 时 分 秒
		date_format(birth,'%Y/%m/%d') 	mysql默认采用date_format()将date转换为varchar类型('%Y-%m-%d')

		mysql date短日期格式:	%Y-%m-%d
		mysql date_tiem长日期格式:	%Y-%m-%d %h:%i:%s	eg: now()
		
	2) 更新数据: update 表名 set 字段名1 = 值1, 字段名2 = 值2, 字段名3 = 值3 where id = 2;	若无条件限制将更新所有记录的值

	3) 删除数据 delete from 表名 where 条件		eg: delete from t_student where id = 2; 	若无条件限制将删除所有记录
	4) 一次插入多条数据: insert into t_tableName(字段1,字段2, 字段3) values(),(),();
select e.ename,d.dname from emp e  join dept d on e.dno = d.deptno;  查询次数不变；

DDL语句：修改表的结构: alter
	对表中的字段名、数据类型、长度等的修改
	5) 约束: 
		非空约束: not null
		唯一性约束: unique
		主键约束:  primary key
		外键约束: foreign key
		检查约束: check (mysql不支持，oracle支持)
	①非空约束: drop table if exist t_vip,
	②唯一性约束: drop table if exist t_vip;
*	③主键约束: primary key(PK)
		主键约束: 就是一种约束
		主键字段: 被主键约束的字段
		主键值: 	主键字段的值
	分类: 	单一主键与复合主键: primary key(vno,vemaiil)，一般不使用复合主键，因为单一主键就是每一条记录的唯一标识
		自然主键与业务主键: 前者表示主键值是一个自然数，和业务没关系；一般我们尽量使用自然主键
	drop table if exist t_vip;
	create table t_table(
		vid int primary key auto_increment,//主键搭配自然递增
		vname varchar
	);
	insert into t_vip(vname) values("zhangsan");
	insert into t_vip(vname) values("lisi");
	select * from t_vip;
*	④外键约束: foreign key(FK)	避免一个表中出现的信息冗余，不够直观  对子表中的外键进行父表中字段约束
	t_class班级表(cno,cname)与t_student学生表(sno,sname,sage,sclass)——父表与子表的关系: 
	drop table if exist t_student;drop table if exist t_class;
	create table t_class( cno int primary key,cname varchar(255));; create table t_student(sno primary key auto_increment,sname varchar ,sage int, foreign key(sclass) references t_class(cno));
	insert into t_class(cno,cname) values(000,高一1班)(001,高一2班); insert into t_student(sno,sname,sage,sclass);
注:外键约束中: 父表中的cno必须要是唯一unique, 不一定是主键PK; 子表中的sclass可以为空null
	
	6) 存储引擎: 
		MyISAM: 采用三个文件分别存储格式frm、数据MDY、索引MYI; 特点——可被转换为压缩、只读表来节省空间
		InnoDB: mysql中默认存储引擎,重量级存储引擎; 特点——支持事务，支持数据库崩溃后自动恢复机制，最主要的特点: 安全
		MEMORY: 其数据存储在内存中，且行的长度固定; 这两个特点使得MEMORY存储引擎非常快; 优点——查询非常快， 不需要和硬盘进行交互; 缺点: 不安全，关机之后数据消失,因为所有数据存储在内存中
*	7) 事务: transaction
		一个事务就是一个完整的业务逻辑; 是一个最小的工作单元，不可再分。
	只有DML语句才有事务这种说法,即 insert delete update
	①提交事务 commit 
	②回滚事务 rollback (每次回滚只能回滚到上一次的提交点)
	注: mysql中默认的事务行为是啥呢？
	自动提交事务，每执行一条mysql语句，则提交一次。如何关闭? start transaction 		
	事务的特性: 
	
		A: 原子性: 事务是最小的工作单元，不可再分
		C: 一致性: 在一个事务中，所有的操作必须是同时成功或同时失败
		I: 隔离性：不同的两个事务1,2之间具有一定的隔离性
		D: 持久性：事务最终结束的一个保障，事务提交，相当于将没有保存到硬盘中的数据保存到硬盘中。
	事务之间的隔离级别: 
	
		读未提交: read uncommitted (最低的隔离级别) 脏读现象
		读已提交: read committed 比较真实的数据，不可重复读取 		Oracle默认的隔离级别
		可重复读: repeateable read 	可能会出现幻影读，不够真实		mysql中默认的隔离级别
		序列化读: serializable (最高级别的隔离级别)	效率最低，事务排队，不能并发

	8) 索引(index): 
	tips: 任何数据库中每个pk都会自动添加索引对象；mysql中被unique修饰的字段自动创建索引。
		数据库中的每条记录在硬盘存储上都有硬盘的物理存储编号，索引在mysql中是一个B_Tree的形式
	什么情况下需要生成索引？
		1.数据量非常庞大；2.该字段经常出现在where之后，以条件的形式存在；
		3.该字段中很少的DML操作，因为DML之后需要对索引进行重新排序
	创建索引: create index emp_name index on emp(ename);
	删除索引: drop index emp_name index on emp;		查看查询语句是否采用索引检索信息: explain select * from emp where name = 'king';	type = all/ref
	索引的失效:

	索引的优化: 在唯一性约束下添加索引

	9) 视图：	
		创建视图: create view dept2_view as select * from dept2;
		删除视图: delete view dept2_view;
	注: 只有DQL语句才能以view的方式创建视图
	create view dept2_view 这里的语句必须是DQL语句; 
	Function: 
		我们可以对视图对象进行增删改查，但是对于视图的任何DML语句都会对影响到原表
		面向视图的查询: 
		面向视图的插入:
		面向视图的删除: delete from dept2_view;
		
	CRUD: 程序员中的术语: 即增删改查: 
	C: Create 
	R: Retrive (查 , 检索)
	U:Update
	D:Delete

	10) DBA常用命令: 
		新建用户; 授权; 回收权限; 
		* 数据的导入导出: 
			导出:在windows窗口命令下: 
			导入: 使用前提: 
		数据库设计三范式: 
			def: 数据库表的设计依据
			
		第一范式: 要求任何一张表必须有主键，每一个字段原子性不可再分；
		第二范式: 建立在第一范式的基础上，要求所有的非主键字段完全依赖主键，不能产生部分依赖；
		第三范式: 建立在第二范式的基础上，要求所有的非主键字段直接依赖主键，不要产生传递依赖

	func: 设计数据库表的时候，按照以上范式进行，可以避免表中数据的冗余，空间的浪费。
	第二范式的设计: 	多对多如何设计: 	多对多，三张表，关系表两个外键！！！	eg: 学生老师关系表
	第三范式的设计:	一对多如何设计: 	一对多，两张表，多的表加外键！！！
			一对一 字段太多需拆分: 一对一，外键唯一！！！
	
  六、JDBC: Java DataBase Connectivity
		1.接口: 
		2.实现类: 
		3.面向接口写代码: 
	JDBC连接六步: 
	
		1. 注册驱动	DriverManger.register("new com.mysql.jdbc.Driver()");
			注册驱动的第二种方式: 	
				Class.forName("com.mysql.jdbc.Driver");//执行静态代码块
		2. 获取连接	
		3. 获取数据库操作对象	Statement stat = conn.createStatement();
		4. 执行sql语句 (CURD)	ResultSet rs = stat.excuteQuery("select ename,sal as emptsal from empt e order by  sal desc"); stat.excuteUpdate(String sql);
		5. 获取结果集 要求step4 是select 	
		6. 释放资源
	SQL注入现象: 
	Statement接口和PrepareStatement接口的特点: 
		Statement接口:  
		PreparedStatement接口: 
	JDBC的事务机制: 
		在实际开发中需要将JDBC的自动提交机制关闭	需要手动提交	遇到异常，及时回滚
		con.setAutoCommit(false);	conn.commit();	catch{ try{ if(conn != null){ conn.rollback(); } } }
	JDBC数据库连接工具类的封装: DBUtil采用private static 代码块和static 变量
	关于DQL语句的悲观锁和乐观锁: 
	for update: 行锁与表锁
