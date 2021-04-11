# Java
This is my Java Mysql learn notes
一、数据库基本概念：

	DB: 
		DataBase: 数据库 存储一定格式的文件集合
	DBMS:
		DataBaseManagementSystem: 数据库管理系统 用于对数据库进行可视化操作、管理的工具	是数据库系统的核心
	SQL: 
		StructureQueryLanguage结构化查询语句：可用于多种数据库进行相应的数据化管理操作：
		如：Mysql、Oracle、DB2等数据库管理工具用的增删改查都是这种标准化语句

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

	查询多个字段	select name1,name2 from tableName;
	查询所有字段	select * from tableName;
	列起别名		select dno,dname as deptname from dept;
	列起别名(别名中含空格) select dname 'dept name' from dept;	
条件查询(where)：

	等于查询			select dno,dname from dept where sal = 3000;
	不等于查询		select dno,dname from dept where sal != 3000;	//不等号 != 或者 <>
	查询工作岗位是manager并且薪资大于200的员工信息	select dno,dname,job,sal from dept where sal >200 and job = 'manager';
	查询工作岗位是manager或者是salesman的员工信息		select dno,dname,job,sal from dept where job = 'salesman' or job = 'manager';
	查询员工的薪资在200-300之间	 select dno,dname from dept where sal between 200 and 300;
	查询员工的补贴为null  	select dno,dname from dept where comm is null;
	查询员工的补贴不为null  	select dno,dname from dept where comm is not null;
	查询员工的工资为200和300的员工 select dno,dname,sal from dept where sal = 200 or sal = 300; 或select dno,dname,sal from dept where sal in(200,300 )
模糊查询(like)：

	查询员工名字中带O的	select dno,dname from dept where dname like '%O%';
	查询员工名字中第二个字母是a的 select dno,dname from dept where dname like '_a%';
	查询员工名字中含有_的员工	select dno,name from dept where dname like '%\_%';
排序: 

	查询员工名字(默认按名字升序查询)	select dno,dname from dept order by name asc;
	查询员工名字(按名字降序查询)	select dno,dname from dept order by name desc;

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
	
注：count(*)：表示统计表中的总行数；count(具体字段)：表示统计该字段下所有不为NULL的元素的总数
count(distinct job) 	可以区分该字段然后计数
分组查询(group by)：
eg: 找出每个部门、不同工作岗位的最高薪资：select deptno,job,max(sal) from emp group by deptno,job;
having 可以在group by 之后进行筛选过滤  ,但是必须和group by 组合使用，但是不能代替where ; 能选where就不选having


日期函数: 

	data_format
	str_to_data	
	formate 	//设置千分位
注：select语句永远不会进行修改删除操作，它只进行查询
as关键字可以省略,若别名中有空格或中文，可以用单引号括起来  sql语句会对命令进行编译 一般根据空格( )划分
and关键字的优先级高于or, 然后不确定优先级可以用小括号()括起来
in表示一个集合，不是一个区间
%: 百分号表示任意多个字符；_: 下划线表示任意一个字符; \: 转义字符 
	关键字的顺序：	select ... from ... where ... group by ...  order by ...
以上语句的执行顺序:	 	第一步：from ...;	第二步：where ...;	第三步: group by ...; 第四步：select ...;	第五步：order by ...
注： 分组函数不能直接放在where子句中	eg: select dname,sum(sal) from emp where sal > min(sal)
重点结论：在一条语句后面, 如果有group by 语句，select 后面只能接参加分组的字段、分组函数 ，其余的一律不能接
(单表查询)大总结：关键字的顺序：	select ... from ... where ... group by ... having ... order by ...
	关键字的执行顺序：	from ,where ,group by ,having ,select , order by 

三、数据库的结构

	1.数据库中最基本的单元是表：
		表包括行(row)和列(column)，行被称为数据(记录)；列被称为字段
		每个字段都有字段名、数据类型、约束等属性。约束有很多，其中有一种叫唯一性约束。
	2.SQL分类：
		*DQL:
			数据查询语言：含select关键字的都是查询语句
		*DML:
			数据操作语言：对表中的数据进行增删改都是DML,
			eg:  insert, delete,  updata
		DDL:
			数据定义语言：对表中的结构(行、列)进行操作的都是DDL, 
			eg: create, drop, alter  
		TCL: 
			事务控制语言：
			包括事务提交(commit)、事务回滚(roolback)
		DCL: 
			数据控制语言
			如授权(grant)、撤销权限(revoke)等	
	3.导入数据库	 sourse 数据库的绝对路径(不能有中文！)

	4. 数据库中表连接查询的分类: 
		连接查询按年代分类: SQL92; SQL99
	case: 查询员工所属部门的名称
	select e.ename,d.depname from emp e join dept d on e.depno = d.no;
		连接查询按表连接的方式分类：
		4.1 内连接
			等值连接
			非等值连接
			自连接(将一张表看成两张表，然后连接)
		4.2 *外连接		可以显示左表或右表中的所有记录
			左外连接(右连接)
			右外连接(左连接)
		4.3 全连接(不讲)
	case: 查询每个员工的上级领导，要求显示所有的员工的名字和领导名
	select e.ename,d.ename from emp e left join emp d on e.mgr = d.ename;	
	5. 子查询：
		where之后的子查询	
			case: 查找出工资大于最低工资的员工名	思路：select min(sal) from emp; select ename from emp where sal > min(sal)
			select ename from emp e where sal > min(sal);//此sql语句出错	
			select ename from emp e where sal > (select min(sal) from emp;)
		from之后的子查询	
			case: 查找出每个岗位的平均工资的薪资等级	思路：select job,avg(sal) from emp group by job; select s.grade from emp e join sal s on e.avg between s.losal and s.hisal;
			select s.* from emp e join (select job,avg(sal) as avgsal from emp group by job) t on salgrade s between s.losal and s.hisal;
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


(DDL : create drop alter) 建表语句: 

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
DML修改表的记录(insert delete update)语句: 

	1)插入数据: insert into 表名(字段名1, 字段名2, 字段名3) value(值1, 值2, 值3);	eg:  insert into t_tableName(no) value(1);
注: 字段名可以省略，此时相当于全写上字段名，需写上所有的值；
		将字符串转换为日期格式: str_to_date(字符串1, 日期格式); 	str_to_date('10- 01-1999', '%m-%d-%Y'); 
		mysql日期格式: %Y %m %d %h %i %s 年 月 日 时 分 秒
		date_format(birth,'%Y/%m/%d') 	mysql默认采用date_format()将date转换为varchar类型('%Y-%m-%d')

		mysql date短日期格式:	%Y-%m-%d
		mysql date_tiem长日期格式:	%Y-%m-%d %h:%i:%s	eg: now()
		
	2) 更新数据: update 表名 set 字段名1 = 值1, 字段名2 = 值2, 字段名3 = 值3 where id = 2;	若无条件限制将更新所有记录的值

	3) 删除数据 delete from 表名 where 条件		eg: delete from t_student where id = 2; 	若无条件限制将删除所有记录
		注: delete删除效率低，但是支持回滚rollback; 
		truncate t_tableName; 这种删除效率高效，但是不支持rollback操作；
		不同于drop操作，这两种操作都是对记录的删除，不删除表的结构；
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
		create table t_vip(
			vno int,
			vname varchar not null,//列级约束
			vcount bigint
		);
	②唯一性约束: drop table if exist t_vip;
		create table t_vip(
			vno int,
			vname varchar(255) unique,//列级约束
			vcount bigint
		);
		drop table if exist t_vip;
		create table t_vip(
			vno int,
			vname varchar,
			vcount bigint,
			unique(vcount)//表级约束 unique(vno,vcount)
		);
小插曲: xxx.sql这种文件被称为sql脚本文件; 执行时，该文件中所有的SQL语句全部执行; 
mysql中如何执行脚本文件呢？——sourse sql脚本文件的具体路径(或把该地址拖拽到mysql执行位置)

	③主键约束: primary key(PK)
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
	④外键约束: foreign key(FK)	避免一个表中出现的信息冗余，不够直观  对子表中的外键进行父表中字段约束
	t_class班级表(cno,cname)与t_student学生表(sno,sname,sage,sclass)——父表与子表的关系: 
	drop table if exist t_student;drop table if exist t_class;
	create table t_class( cno int primary key,cname varchar(255));; create table t_student(sno primary key auto_increment,sname varchar ,sage int, foreign key(sclass) references t_class(cno));
	insert into t_class(cno,cname) values(000,高一1班)(001,高一2班); insert into t_student(sno,sname,sage,sclass);
注:外键约束中: 父表中的cno必须要是唯一unique, 不一定是主键PK; 子表中的sclass可以为空null
	6) 存储引擎: 
		存储引擎是mysql中特有的一个术语，其他数据库中没有(oracle中有，但是名称不同)
		存储引擎是一个表存储/组织数据的方式; 不同的存储引擎，表存储数据的方式不同。
	如何给表添加/指定存储引擎呢？——在表创建时')'后面添加engine=指定存储引擎 default charset=utf8;
	结论: mysql中默认的存储引擎是: InnoDB; 默认的字符编码方式是: utf8;
	查看mysql中支持的存储引擎: show engine /G 		一般9个
	常用的三个存储引擎: 
	
		MyISAM: 采用三个文件分别存储格式frm、数据MDY、索引MYI; 特点——可被转换为压缩、只读表来节省空间
		InnoDB: mysql中默认存储引擎,重量级存储引擎; 特点——支持事务，支持数据库崩溃后自动恢复机制，最主要的特点: 安全
		MEMORY: 其数据存储在内存中，且行的长度固定; 这两个特点使得MEMORY存储引擎非常快; 优点——查询非常快， 不需要和硬盘进行交互; 缺点: 不安全，关机之后数据消失,因为所有数据存储在内存中
*	7) 事务: transaction
		一个事务就是一个完整的业务逻辑; 是一个最小的工作单元，不可再分。
	只有DML语句才有事务这种说法,即 insert delete update
		提交事务: 删除所有的事务记录日志文件，表示所有的DML语句都结束，但是都是以成功的方式提交
		回滚事务: 表示所有的DML语句都结束，但是都是以失败的方式提交，删除所有的事务记录日志文件。

	①提交事务 commit 
	②回滚事务 rollback (每次回滚只能回滚到上一次的提交点)
	注: mysql中默认的事务行为是啥呢？
	自动提交事务，每执行一条mysql语句，则提交一次。如何关闭? start transaction 		
		但是这种自动提交的机制不符合事务安全机制，因每个事务都需要多条DML语句执行，
	只有当事务中的多条DML语句全部执行成功或失败才能提交。所以我们使用start transaction; commit;
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
		def: 索引是在数据库中的字段中添加的，是为了提高查询效率的一种机制；
		查找方式: 1、全表扫描；2、索引扫描
		索引也是需要排序的，与TreeSet结构相同，底层是一个自平衡的二叉树，在mysql中

	tips: 任何数据库中每个pk都会自动添加索引对象；mysql中被unique修饰的字段自动创建索引。
		数据库中的每条记录在硬盘存储上都有硬盘的物理存储编号，索引在mysql中是一个B_Tree的形式
	什么情况下需要生成索引？
	
		1.数据量非常庞大；
		2.该字段经常出现在where之后，以条件的形式存在；
		3.该字段中很少的DML操作，因为DML之后需要对索引进行重新排序

	创建索引: create index emp_name index on emp(ename);
	删除索引: drop index emp_name index on emp;		查看查询语句是否采用索引检索信息: explain select * from emp where name = 'king';	type = all/ref
	索引的失效:
	condition1: select * from emp where name = '%T';		 以%开头是模糊查询，尽量避免模糊查询
	condition2: 使用or的时候会失效; 如果使用or要求or两边的字段都有索引或都没有索引，若一边无索引，则另一边索引失效
	condition3: 使用复合索引时，只能使用左边的索引，不能使用右边的索引
	condition4: 在where当中的索引列参加了运算，索引失效	eg: select * from emp where sal + 1 = 900;
	condition5: 在where当中的索引列使用了函数，索引失效

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

	实际开发中的作用: 	
		可以把一条复杂的SQL语句以视图对象的形式新建；在需要编写使用该sql语句时，可直接使用视图对象，简化开发; 
		并且利于后期维护，因后期修改时只需修改一个位置即可，即直接修改视图对象所映射的sql语句。
	面向视图开发时，视图可看成table一样进行增删改查操作，视图不是存在于内存中，它存在于硬盘中，不会消失; 
	CRUD: 程序员中的术语: 即增删改查: 
	C: Create 
	R: Retrive (查 , 检索)
	U:Update
	D:Delete

	10) DBA常用命令: 
		新建用户; 授权; 回收权限; 
		* 数据的导入导出: 
			导出:在windows窗口命令下: 
				导出bjpowernode数据库: mysqldump bjpowernode>d:\bjpowernode.sql -uroot -p123456;
				导出指定表: mysqldump bjpowernode emp>d:\bjpowernode.sql -uroot -p123456;	
			导入: 使用前提: 
				先登录到mysql数据库服务器; 然后创建该数据库；使用该数据库；然后初始化数据库(即下面语句)
				sourse D:\bjpowernode.sql
		数据库设计三范式: 
			def: 数据库表的设计依据
			
		第一范式: 要求任何一张表必须有主键，每一个字段原子性不可再分；
		第二范式: 建立在第一范式的基础上，要求所有的非主键字段完全依赖主键，不能产生部分依赖；
		第三范式: 建立在第二范式的基础上，要求所有的非主键字段直接依赖主键，不要产生传递依赖

	func: 设计数据库表的时候，按照以上范式进行，可以避免表中数据的冗余，空间的浪费。
	第二范式的设计: 	多对多如何设计: 	多对多，三张表，关系表两个外键！！！	eg: 学生老师关系表
	第三范式的设计:	一对多如何设计: 	一对多，两张表，多的表加外键！！！
			一对一 字段太多需拆分: 一对一，外键唯一！！！

	以满足客户需求为准: 有时需要用冗余换速度; 因为表的连接次数越多，效率越低(笛卡尔积)！ 程序员编写难度也会减低
	34道作业题: 
		1. 注：not in();括号中应排除为null的数据	eg: 找出比普通员工最高薪水高的领导人姓名: 
		第一步找出普通员工的最高薪水 select max(sal) from emp where empno not in (select distinct mgr from emp where mgr is not null); 
		第二步找出薪资高于这个最高薪资的领导人姓名: select mgr,sal from emp where sal > ();
  		2.取出每个薪水等级有多少个员工: 
		step 1: 取出每个员工的薪水等级:  select e.ename,e.sal,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
		step 2: 再进行分组计数: select s.salgrade,count(*) from emp e join salgrade s on e.sal between s.losal and s.hisal group by s.grade;

		3.找出受雇日期早于其直接上级的所有员工的编号，姓名和部门名称。
		step 1: 找出受雇日期早于其直接上级的员工：select a.empno,a.hiredate,a.mgr,b.hiredate from emp a join emp b on a.mgr = b.empno join dept d where a.hiredate < b.hiredate;
		4. 计算两个时间之间的时间差: timestampdiff(时间类型，前一个日期，后一个日期);
		5. 给任职时限超过30年的员工加薪10% 
		update emp set sal = sal * 1.1 where timestampdiff(year,hiredate,now()) > 30;
	
	JDBC: Java DataBase Connectivity
	
		接口: 
			是SUN公司定义的一套接口；其类库包含在java.sql.*包下
			降低耦合度，提高扩展性。
		实现类: 
			是数据库厂家百年写的，有mysql和oracle等数据库厂商，
			mysql-connector-java-5.1.23-bin.jar;
		面向接口写代码: 
			后端程序员
	JDBC连接六步: 
	
		1. 注册驱动	DriverManger.register("new com.mysql.jdbc.Driver()");
			注册驱动的第二种方式: 	
				Class.forName("com.mysql.jdbc.Driver");//执行静态代码块
		2. 获取连接	
			URL url = "jdbc:mysql://localhost:3306/bjpowernode"; //http://192.68.100.2:8888/abc	url统一资源定位符: 协议(http） + IP地址(localhost) + 端口号(3306) + 资源名
			Connection conn = DriverManager.getConnection(URL url,String userName,String password);
		3. 获取数据库操作对象	Statement stat = conn.createStatement();
		4. 执行sql语句 (CURD)	ResultSet rs = stat.excuteQuery("select ename,sal as emptsal from empt e order by  sal desc"); stat.excuteUpdate(String sql);
		5. 获取结果集 要求step4 是select 	
			while(stat.next()){
				String name = rs.get("ename");	
				String sal = rs.get("emptsal");					
			}
		6. 释放资源
			if(rs != null){
				try(){
					rs.close();//stat.close(); //conn.close();
				}
				catch(Exception e){ e.printStackTrace();
				}
			}
	JDBC中所有的下标都是从1开始。	
	SQL注入现象: 
		user = username;
		password = username' or '1' == '1';
		SQL注入的根本原因: 先进行了字符串的拼接，对sql语句进行编译
	Statement接口和PrepareStatement接口的特点: 
	
		Statement接口:  
			先进行字符串的拼接，然后对SQL语句的编译；
			adv: 可以进行sql语句的拼接；disadv: 因为拼接的存在，会存在sql注入问题
			eg: 将查询的数据进行升序或降序输出desc/ asc
		PreparedStatement接口: 
			先进行sql的编译，再进行sql的传值。
		 	adv: 避免sql注入；disadv: 不能进行sql语句的拼接，只能给sql语句传值。
			eg: 占位符? 不能放在单引号' '中
	JDBC的事务机制: 
		在实际开发中需要将JDBC的自动提交机制关闭	需要手动提交	遇到异常，及时回滚
		con.setAutoCommit(false);	conn.commit();	catch{ try{ if(conn != null){ conn.rollback(); } } }
	JDBC数据库连接工具类的封装: DBUtil采用private static 代码块和static 变量
	关于DQL语句的悲观锁和乐观锁: 
		一个DQL语句可以在末尾添加一个关键字: for update 
		select ename,sal from emp where ename = "李四" for update;
		表示在当前事务执行结束前，当前查询的结果不能被修改；行级锁 
	for update: 
		当使用select ... from tabel where ... for update ... 时，mysql进行的是row lock还是table lock只取决于是否能使用索引(eg 主键 unique字段)
		能则为行锁，否则为表锁；未查到数据则无锁，而是用<> like等操作时，索引会自动失效，自然进行的是table lock;
		故使用for update时，最好是锁住 使用主键值或unique约束的字段，否则为表锁。
		慎用for update;
