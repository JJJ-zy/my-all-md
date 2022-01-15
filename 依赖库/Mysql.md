# Mysql

## 多表操作

### 外键约束（FOREIGN KEY）

```tex
相关联字段中主键所在的表就是主表，外键所在的表就是从表，从表的外键收到主表主键的限制，主键有什么，外键只能有什么

定义一个外键时，需要遵循的规则
	1.主表必须已经存在，或者是当前正在创建的表
	2.必须为主表定义主键
	3.主键不能包含空值，外键允许空值
	4.在主表的表名后面指定列名或列名的组合，这个列或列的组合必须是主表的主键或候选键（语法）
	5.外键中列的数目必须和主表的主键中列的数目相同
	6.外键中列的数据类型必须和主键数据类型一样
```

![image-20220106193000595](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20220106193000595.png)

> 设置外键约束的方式

```tex
1.在创建表时声明外键约束
	constraint <外键名> foreign key (字段名) references <主表名> (主键列名)
```

```mysql
    create table if not exists dept(
        deptno varchar(20) primary key,	-- 部门号
        name varchar(20)	-- 部门名字
    );

    create table if not exists emp(
        eid varchar(20) primary key, -- 员工编号
        ename varchar(20),	-- 员工名字
        age int,	-- 员工年龄
        dept_id varchar(20),
        constraint emp_fk foreign key (dept_id) references dept (deptno)
    );
```

```tex
2.在修改表时添加外键
	alter table <数据表名> add constraint <外键名> foreign key (列名) references <主表名> (列名)
```

```mysql
-- 修改添加外键
create table if not exists dept2(
	deptno varchar(20) primary key,	-- 部门号
	name varchar(20)	-- 部门名字
);


create table if not exists emp2(
	eid varchar(20) primary key, -- 员工编号
	ename varchar(20),	-- 员工名字
	age int,	-- 员工年龄
	dept_id varchar(20)
);

alter table emp2 add constraint emp2_fk foreign key (dept_id) references dept2 (deptno);
```



> 添加数据

```mysql
insert into dept values('1001','研发部'),('1002','销售部'),('1003','财务部'),('1004','人事部');

insert into emp values('1','乔峰',20,'1001'),('2','段誉',21,'1001'),('3','虚竹',23,'1001'),('4','阿紫',18,'1002'),('5','扫地僧',35,'1002'),('6','李秋水',33,'1003'),('7','张三',50,'1003');

insert into emp values('8','李四',20,'1005');  -- 不能添加，主表中没有
```

> 删除数据

```tex
1.主表的数据被从表依赖时，不能删除，否则可以删除
2.从表的数据可以随便删除
```

```mysql
-- 删除数据
delete from dept where deptno = '1001'; -- 不能被删除
delete from dept where deptno = '1004'; -- 可以被删除
delete from emp where eid = '7'; -- 可以被删除
```

> 删除外键约束

```tex
alter table <表名> drop foreign key <外键约束名>;
```

```mysql
-- 删除外键约束
alter table emp2 drop foreign key emp2_fk;
```

#### 多表关系

> 设置外键

```mysql
-- 创建学生表
create table if not exists student(
	sid int primary key auto_increment,
	name varchar(20),
	age int,
	gender varchar(20)
);

-- 创建课程表
create table if not exists course(
	cid int primary key auto_increment,
	cidname varchar(20)
)

-- 创建中间表
create table score(
	sid int,
	cid int,
	score double
);

-- 创建外键
alter table score add constraint stu_fk foreign key (sid) references student (sid);
alter table score add constraint cou_fk foreign key (cid) references course (cid);

-- 添加学生数据
insert into student values(1,'小龙女',18,'女'),(2,'阿紫',19,'女'),(3,'周杰伦',20,'男');

-- 添加课程数据
insert into course values(1,'语文'),(2,'数学'),(3,'英语');

--添加中间表数据
insert into score values(1,1,80),(1,2,75),(2,1,65),(2,3,85),(3,2,60),(3,3,80);

insert into score values(4,4,60)   -- 超出范围
```

### 多表查询

> 交叉连接查询

```mysql
select * from dept3,emp3;
-- 笛卡尔积方式查询  表一的每一条数据和表二的每一条数据都匹配查询一遍   相当于  m*n条数据
```

> 内连接查询

```tex
内连接查询 求的是表的交集

格式：
	隐式内连接(SQL92标准) ： select * from A,B where 条件
	显示内连接(SQL99标准) ： select * from A inner join B on 条件    (inner可以省略)
```

```mysql
-- 隐式外连接
select * from dept3,emp3 where dept3.deptno = emp3.dept_id;
-- 显示外连接
select * from dept3 inner join emp3 on dept3.deptno = emp3.dept_id
select * from dept3 join emp3 on dept3.deptno = '1001' and emp3.dept_id = '1001';
-- 别名简化代码
select * from dept3 d join emp3 e on d.deptno = '1001' and e.dept_id = '1001';

-- 查询每个部门的员工数，并升序排序    使用分组查询
select dept3.deptno,count(*) from dept3 join emp3 on dept3.deptno = emp3.dept_id group by dept3.deptno

-- 查询人数大于等于三的部门，并按照人数降序排序
select d.deptno,d.name,count(*) as total_cnt 
from dept3 d 
	join emp3 e on d.deptno = e.dept_id
group by d.deptno,d.name 
having total_cnt >= 3
order by total_cnt desc;
```

> 外连接查询

```tex
左外连接 left outer join      以左为主，附加右的交集
右外连接 right outer join		以右为主，附加左的交集
满外连接  oracle   full outer join     mysql union 
```

```mysql
-- 查询哪些部门有员工，哪些部门没有员工
select * from dept3 d left join emp3 e on d.deptno = e.dept_id;

-- 多张表的左外连接
select * from A
left join B on 条件
left join C on 条件
left join D on 条件


-- 查询哪些员工有对应的部门，哪些没有
select * from dept3 d right join emp3 e on d.deptno = e.dept_id;

-- 使用union关键字实现左右连接的并集
select * from dept3 d left join emp3 e on d.deptno = e.dept_id
union
select * from dept3 d right join emp3 e on d.deptno = e.dept_id;

-- 使用union all 关键字实现左右连接的并集 不去重
select * from dept3 d left join emp3 e on d.deptno = e.dept_id
union all
select * from dept3 d right join emp3 e on d.deptno = e.dept_id;
```

> 子查询

```tex
select嵌套查询

返回的结果分为四种
	1.单行单列: 返回的是一个具体的内容，可以理解为一个单值数据
	2.单行多列：返回一行数据中多个列的内容
	3.多行单列：返回多行记录中同一列的数据，相当于给出了一个操作范围
	4.多行多列：查询返回结果是一张临时表
```

```mysql
-- 子查询
-- 查询年龄最大的员工信息，显示信息包含员工号，员工名字，员工年龄
select eid,ename,age from emp3 
where
age = (select max(age) from emp3)

-- 查询研发部和销售部的员工信息，包含员工号，员工名字

select * from dept3 d join emp3 e
on d.deptno=e.dept_id and (d.name='研发部' or d.name='销售部');

select eid,ename from emp3 
where
dept_id in (select deptno from dept3 where name='研发部' or name='销售部')

-- 查询研发部20岁以下的员工信息，包括员工号，员工名字，部门名字
select * from dept3 join emp3 on dept3.deptno = emp3.dept_id 
and (age<20 and name='研发部')

select * from emp3 where age<20 
and dept_id = (select deptno from dept3 where name='研发部') 
```

```tex
子查询关键字
```

> all

```tex
格式
	select ... from ... where c>all (查询语句)
	
	all可以与 = 、>、>=、<、<=、<>结合使用
	all表示指定列中的值必须要大于子查询集中的每一个值，即必须要大于子查询集的最大值，小于号就是小于最小值
```

```mysql
-- 查询年龄大于1001部门所有年龄的员工信息
select * from emp3 where age > all
(select age from emp3 where dept_id='1001')

-- 查询不属于任何一个部门的员工信息
select * from emp3 where dept_id != all
(select deptno from dept3)
```

> any 和 some

```tex
格式
	select ... from ... where c > any(查询语句)
	
	any可以与各种大于小于号结合使用
	表示指定列中的值要大于子查询中的任意一个值，即必须要大于子查询中的最小值
	some和any效果一样
```

```mysql
-- 查询年龄大于1001部门任意年龄的员工信息
select * from emp3 where age > any
(select age from emp3 where dept_id='1001') and dept_id != '1001'
```

> in

```tex
格式
	select ... from ... where c in (查询语句)
	
	in 用来判断某个记录的值，是否在指定的集合中
	not in 将条件反过来
```

```mysql
-- 查询研发部和销售部的员工信息，包含员工名，员工名字
select * from dept3 d join emp3 e 
on d.deptno=e.dept_id and name in ('研发部','销售部')

-- 查询研发部和销售部之外的员工信息
select * from dept3 d join emp3 e 
on d.deptno=e.dept_id and name not in ('研发部','销售部')
```

> exists

```tex
格式
	select ... from ... where exists(查询语句)
	
	如果子查询语句有数据结果，则exists()返回true,外层查询执行，反之，不执行
	exists后面的子查询不返回具体数据，只返回真假，当返回真时 where条件成立
	exists运算效率高，推荐使用
```

```mysql
-- 查询公司是否有大于60岁的员工，有则输出
select * from emp3 e where exists (select * from emp3 where e.age > 49)

-- 查询有所属部门的员工信息
select * from emp3 e where exists 
(select * from dept3 d where d.deptno=e.dept_id)
```

> 自关联查询

```tex
表自身进行关联，自关联必须给表起别名
格式
	select ... from 表1 a,表1,b where 条件;
	select ... from 表1 a [left] join 表1 b on 条件;
```

```mysql
-- 创建表，并建立自关联约束
create table t_sanguo(
	eid int primary key,
	ename varchar(20),
	manager_id int,
	foreign key (manager_id) references t_sanguo (eid)  -- 添加自关联约束
);

-- 查询每个三国人物和他的上级
select b.ename self,a.ename manager from t_sanguo a right join t_sanguo b on a.eid=b.manager_id

-- 自关联三表查询
select a.ename,b.ename,c.ename
from t_sanguo a
left join t_sanguo b 
on a.manager_id = b.eid 
left join t_sanguo c
on b.manager_id = c.eid
```

