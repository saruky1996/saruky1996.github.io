---
title: Oracle数据库基础学习笔记
date: 2016-07-18 14:13:31
categories: 
    - 学习
tags: 
    - Oracle
    - SQL
    - 学习记录
    - 老坑迁移
---

### 学习顺序
1. 数据库搭建以及远程连接数据库
2. 初步了解查询，单表查询结合简单过滤条件
3. 进一步了解查询，单表查询结合函数
4. 深入了解查询，多表查询结合子查询
5. DML操作 即增改删
6. 数据隔离级别
7. 附：如何使用脚本刷库

<!--more-->

---

### 数据库远程连接

```bash
    conn / as sysdba
    conn scott/tiger
    conn scott/tiger@10.10.10.2:1521/testdb
    alter user scott account unlock
    alter user scott account lock
    set nls_lang=simplified chinese_china.zhs16gbk
    set nls_lang=simplified american_america.al132utf8
    shutdown immediate
    start up
    host
    set linesize 500
    set pagesize 50
```


#### 想要远程连接一个数据库：
1. 数据库所在的机器必须创建一个监听器 Net Configuration Assistant
2. 我的电脑——管理——服务——出现了…Listener的服务
3. cmd——lsnrctl——进入Listener Control环境
4. cmd——status——查看监听器状态，如果包含了目标数据库，则可以该数据库可以被连接。
5. 在PL/SQL中连接10.10.10.2:1521/dfbb  
   （或者在宿主机cmd中输入conn scott/tiger@10.10.10.2:1521/dfbb 前提是宿主机中已安装了Oracle，若成功则会显示已连接）
6. 当PL/SQL标题栏出现了scott@10.10.10.2:1521/dfbb就表示成功
7. 如果连不上，在其中工具——首选项——OCI库指明为instantclient/oci.dll


#### 远程数据库连接的问题和处理方式：
1. 网络不通（防火墙，机器没开，IP地址错误，网线断开等）
2. 无监听程序，远程数据库必须创建一个监听器 Net Configuration Assisant
3. 监听器中尚未包含目标数据库，等待。
4. 本地无数据库
5. 数据库已经关闭。  
   在对方机器上，使用sys连接本地数据库时，会连接到空闲例程，使用startup命令可打开数据库。
6. 远程时会检查用户名密码，无用户或权限不足

<br>
> 为了简化远程连接，可以在Oracle里的【Net Configuration Assisant】里的【服务名配置】中：
> 添加一个服务器名（testdb），主机名（10.10.10.2），更改登陆（用户system 密码oracle），网络服务名（testdb000）
> 之后可以直接 conn scott/tiger@testdb000

---

### 查询基础

#### 数据类型：
1. 字符串
	- char【定长】
	- varchar/varchar2【变长】
2. 数型 number
	（P.S. number(7,2)中的意思表示，该数据有7位整数和2位小数）
3. 日期 date

#### Select语句基础：
详细查看官方帮助文档（学会看语法图）

最简单的查询语句：
```bash
select * from emp;
```
得到的结果称为查询结果集
查询结果集中，一行为一条记录，一列为一个字段


```bash
select empno as eno, ename, sal esal from emp;
```
其中eno称为empno的别名，esal为sal的别名
其别名可以使用as，也可以直接接在原名之后。
若要使用特殊符号，需添加双引号。
双引号还有强制区分大小写的作用，加上双引号之后，直接用不区分的方式将无法找到对应字段。查找时区分大小写或加上双引号。

```bash
    select empno from emp;
    select * from emp where ename = 'SCOTT';
    select empno,ename,sal from emp where ename = 'SCOTT';
    select distinct ename,deptno from emp;
```
当查询结果集中，两条记录的所有字段都相同，才认为记录重复。
在条件筛选时，区分大小写！区分！区分！

```bash
    drop table emp01
    purge recyclebin
    desc emp
```


### 过滤查询

1. 算数运算
```bash
	    where sal + 1000 = 4000
```
2. 比较运算
```bash
	    where sal > 3000
	    where sal <> 3000
	    where sal between 1000 and 2000
	    where sal in (800, 950, 2000, 3000, 4000)
```
	between and语句相当于闭区间
	in用于匹配离散值
3. 逻辑运算
```bash
	    where not sal = 3000
```
4. 空值判断
```bash
	    where comm is null
	    where comm in(300, 500, null)
	    where comm not in (300, 500, null)
```
	空值不可以用=/<>而必须使用is/is not
5. 模糊匹配
```bash
	    where ename like '%S%'
	    where ename like '%\%%' escape '\'
```
	%表示任意个任意字符，_表示单个任意字符
	转义字符\
6. 连字符运算
```bash
	    select ename,ename,ename || '---' from emp;
```
> Attention：算数运算、模糊查询会忽略空值，连字符运算不会。


### 排序子句
```bash
    select * from emp order by sal;
```
可以字段名，也可以是别名和字段号
```bash
    select * from emp order by deptno desc;
    select * from emp order by deptno desc, sal desc;
```
多字段时，先根据deptno，再根据sal来排序

> Attention：select … from … where … order by …

#### 函数

1. 单行函数
2. 多行函数（分组/聚合函数）
3. 分析函数
- dual函数，只有一条记录一个字段，用于进行计算或者返回值。

### 单行函数
<br>
#### 字符类
1. upper/lower【大/小写】
```bash
	    select upper ('abcDEfg00aa中文') from dual;
	    select lower ('abcDEfg00aa中文') from dual;
```
2. initcap【单词首字母大写 空格、下划线、中文后都认为是新单词】
3. substr【取子串】
```bash
	    select substr('abcdefg0123' , -3) from dual;
	    select substr('abcdefg0123' , 3) from dual;
	    select substr('abcdefg0123' , 3 , 3) from dual;
```
4. length【长度】
```bash
	    select length('abc悟空') from dual;
	    select lengthb('abc悟空') from dual;
```
5. instr【取子串的索引下标】
```bash
	    select instr( 'abcdefg' , 'efg' ) from dual;
	    select instr( 'abcdefgabcdefg' , 'efg' , 6) from dual;
```
6. lpad/rpad
```bash
	    select lpad( 'abcd' , 10 , '-=' ) from dual;
```

7. ltrim/rtrim/trim
```bash
	    select ltrim( '      abcd   bbb  ccc   ' ) from dual;
	    select ltrim( 'aaaabbbccccdddbbbaa' , 'ba' ) from dual;
	    select trim( 'a' from 'aaaabbbccccdddbbbaa' ) from dual;
	    select trim( leading 'a' from 'aaaabbbccccdddbbbaa' ) from dual;
	    select trim( trailing 'a' from 'aaaabbbccccdddbbbaa' ) from dual;
```
8. ascii()/charas()
9. replace
```bash
	    select replace( 'acca01aabdji2ab03duab' , 'ab' , '八戒' ) from dual;
	    select translate( 'acca01aabdji2ab03duab' , 'ab' , '八戒' ) from dual;
```

<br>
#### 数值类
1. round【四舍五入】/ trunc【截断】/ mod【取模】
```bash
	    select round(100.4567) from dual;		结果为100
	    select round(100.5567 , 2) from dual;	结果为100.56
	    select trunc(100.4567 , 2) from dual;	结果为100.55
```

<br>
#### 日期类
1. sysdate【服务器时间】
```bash
	    select sysdate from dual;
	    select sysdate + 100 from dual;
```
	支持以天为单位进行算术运算。
2. to_char(sysdate , '模式字符') 【显示转换函数】
```bash
	    select sysdate to_char(sysdate , 'yyyy-mm-dd hh24:mi:ss') from dual;
```
3. current_date【当前客户端的时区】
```bash
	    select current_date to_char(sysdate , 'yyyy-mm-dd hh24:mi:ss') from dual;
```
4. months_between【两个日期之间有多少个月】
```bash
	    select round( months_between(sysdate , hiredate) ) from dual;
```
5. add_months【加/减月份】
```bash
	    select add_mounths( sysdate , 3 ) to_char(sysdate , 'yyyy-mm-dd hh24:mi:ss') from dual;
	    select add_mounths( sysdate , -3 ) to_char(sysdate , 'yyyy-mm-dd hh24:mi:ss') from dual;
```
5. to_date【字符串转换为日期】
```bash
	    select to_date( '2016-2-2' , 'yyyy-mm-dd' ) from dual;
```
6. next_day【下一个的，一周中的第几天】
```bash
	    select to_char( next_day( '2016-2-2' , 'yyyy-mm-dd' , 5) , 'yyyy-mm-dd' ) from dual;
```
7. last_day【某月中的最后一天】
```bash
	    select to_char( last_day( '2016-7-14' , 'yyyy-mm-dd' , 5) , 'yyyy-mm-dd' ) from dual;
```
8. round【在天数上对时间进行四舍五入】
```bash
	    select to_char( round( to_date( '2016-7-13 11:59:59' , 'yyyy-mm-dd hh24:mi:ss' ) ) ,  'yyyy-mm-dd hh24:mi:ss' ) from dual;
	    select to_char( round( to_date( '2016-7-13 11:59:59' , 'yyyy-mm-dd hh24:mi:ss' ) , ' mm') ,  'yyyy-mm-dd hh24:mi:ss' ) from dual;
```
9. extract【提取年月日】
```bash
	    select extract(month from sysdate) from dual;
	    select extract(minutes from systimestamp) from dual;
```
10. systimestamp【可包含毫秒信息】
```bash
	    select systimestamp to_char(sysdate , 'yyyy-mm-dd hh24:mi:ss.ff3') from dual;
```

<br>
#### 通用类函数
1. nvl(comm,0)
2. nvl2(comm,0,1)
3. case
	等值case
	decode
	条件case

<br>
### 分组/聚合函数
> 所有的分组/聚合函数忽略空值！

1. max() / min() / avg() / sum()
2. count()【返回记录条数】
```bash
	    count( distinct deptno)
```
3. avg( nvl(comm,0) )【解决忽略空值的问题】
4. group by 
```bash
		avg(sal) from emp group by deptno【分组求平均工资】
```
	select list中的非聚合字段必须出现在group by表达式，否则会出现错误。
	①不是单组分组：没有group by语句
	②不是group by表达式：没有放到group by语句中
```bash
		where sal > 2000 group by deptno having avg(sal) > 2000
```
	只有sal >2000 的记录才会参加分组
	分组中只有avg(sal) > 2000 的才会显示
5. rollup()【上卷，对第一个字段的每个分组进行一次小计】
6. cube()【对所有分组字段都进行小计】

<br>
### 多表查询基础
```bash
select * from emp, dept;
select * from emp cross join dept;
```
	查询结果集中，所有字段是两表字段之和，A表的每条记录会与B表的每条记录组合（即笛卡尔积）
```bash
select * from emp , dept where emp.deptno = dept.deptno;
select * from emp e , dept d where e.deptno = d.deptno;
```
多表查询一般都使用where带条件进行过滤，n张表连接至少需要n-1个条件，之间用and连接

> Attention：当字段名唯一时，可不限定表名/别名。

### Oracle Theta语法
1. 等值连接【又称内连接】【忽略空值】
```bash
select * from emp , dept where emp.deptno = dept.deptno
```
2. 不等连接【忽略空值】
```bash
select * from emp e, salgrade sg where s.sal >= sg.losal and sal <= hisal;
select * from emp e, salgrade sg where sal between losal and hisal;
```
3. 左连接 / 右连接【又可称左外连接】
```bash
select * from emp , dept where emp.deptno = dept.deptno(+);
```
	A表左连接B表【B表右连接A表】，则(+)放在B表一端。
	查询结果集中，会包含A表中所有的记录。在不满足连接条件的情况下，B表对应的所有字段为null
4. 自连接
```bash
elect e1.empno , e1.ename , e1.sal , e2.empno , e2.ename from emp e1 , emp e2 where e1.mgr = e2.empno
```
	把emp这张表作为两张，往往是表中记录有层级关系。


<br>
### SQL92语法
1. natural join 自然连接
```bash
select * from emp natural join emp01
```
	自动使用两表的相同字段进行等值连接【注意：空值查不出】
2. join/using 子句
```bash
select * from emp join emp01 using (empno);
select * from emp join emp01 using (ename);
```
	指定使用哪些字段进行等值连接
3. join/on 子句
```bash
select * from emp e join dept d on e.deptno = d.deptno;
select * from emp e left (outer) join dept d on e.deptno = d.deptno;
select * from emp e full join dept d on e.deptno = d.deptno;
```
用SQL92语法实现左连接，与Oracle Theta不同，AB两表的记录都全部显示，对应不符合连接条件的，字段为空值。

<br>
#### 连接条件和过滤条件的区别
```bash
select * from emp e join dept d on e.deptno = d.deptno where e.sal > 2000;
select * from emp e join dept d on e.deptno = d.deptno and e.sal > 2000;
```
- where是过滤条件在笛卡尔积中进行过滤，满足条件的被筛选出来放入查询结果集
- and是连接条件
- P.S. 在左连接时可看到区别！

<br>
### 层次查询
- 伪列
```bash
select e.* , level from emp e start with empno = 7839 connect by prior empno = mgr;
```
start with指明从7839开始，找7839的下属；
判断下属的条件是prior empno = mgr；
connect by是递归，找到记录之后以该记录作为新起点继续。（DFS）
自顶向下查询
```bash
select lpad ( ename , length(ename) + level * 2 , '-' ) from emp start with ename = 'SMITH' connect by prior mgr = empno;
```
自底向上查询
- 剪枝
```bash
select lpad ( ename , length(ename) + level * 2 , '-' ) from emp start with empno = 7839 connect by prior mgr = empno and ename <> 'JONES' ;
```
去除JONES这一条分支，找到JONES就不再向下
```bash
select lpad ( ename , length(ename) + level * 2 , '-' ) from emp where ename <> 'JONES' start with empno = 7839 connect by prior mgr = empno ;
```
去除JONES这一个，JONES的下属仍保留
- connect_by_isleaf
0是父亲结点，1是叶子结点

<br>
#### 查询结果集运算
1. union 并集
2. intersect 交集
3. minus 差集
> Attention：要求个数相同、类型相容

```bash
  select * from emp
  union
  select * from emp01
  order by 1;
```

<br>
### 子查询
把复杂的查询在逻辑上分为多个子查询
几乎可以用于所有子句中
	- select
	- from
	- where
	- having
	- order by

1. 标准子查询
	子查询只执行一次，其结果代入主查询替换。
2. 关联子查询
	在子查询的内部用到了外部/主查询中的表。
	将主查询得到的记录，代入子查询内部进行了一次子查询，该结果替换后再执行了主查询，得到结果。
```bash
select empno, ename, sal, (select avg(sal) from emp e2 where e2.deptno = e1.deptno) from emp e1;
select * from emp e1 where empno in (select mgr from emp e2);
select * from emp e1 where empno not in (select mgr from emp e2);【Null值陷阱】
select * from emp e1 where empno not in (select nvl( mgr,0 ) from emp e2);
```
	存在性判定exists
```bash
select e1.* from emp e1 where exists(select 1 from emp e2 where e1.empno = e2.mgr);
```
	每一条记录都判断exists中的子查询结果集是否为null，为null则不将该记录放入查询结果集。
	可以使用not，没有Null值陷阱
3. 单行子查询
4. 多行子查询
```bash
	select d.*, avg_sal
    from dept d, (select deptno,avg(sal) avg_sal from emp group by deptno) xx
    where d.deptno = xx.deptno;
	select * from emp where (deptno, job) = (select deptno, job from emp where ename = 'SCOTT');
```
5. 和all/any组合查询
```bash
select * from emp where sal > (select max(sal) from emp where deptno = 20);
select * from emp where sal > all (select * from emp where deptno = 20);
select * from emp where sal < any (select * from emp where deptno = 20);
```

<br>
### 分页查询
```bash
select e.*, rounum from emp e;
select e.* , rownum from emp e where rownum <= 5;
```
分配rownum必须包括1，并且在where条件判断之前生成（order by 在where之后生成）
```bash
select * from ( select e1.*, rownum rn from (select * from emp order by sal desc) e1) where rn between 6 and 10;
```

<br>
### 分析函数
```bash
select * from ( select e1.*, row_number() over(order by sal desc) rn from emp e1) where rn = 2;
select e.*, rownum from (select e1.*, rownum from emp e1 order by sal desc) e;
select e.* , rownum from emp e order by sal desc;
```
<br>
### DML操作
- 插入
- 删除
- 修改
- 合并（上面三种操作的合并）

<br>
#### 单表单行插入
```bash
insert into emp01 values(9001, 'JJY01', '临时工', 7788, '11-11月-16', 7000, null, 20); 
insert into emp01(empno, ename, sal) values(9002, 'JJY02', 8000);
insert into emp01(empno, ename, sal) values(9003, default, 7000);
```
如果没有指明插入哪些字段，则必须填入所有字段的值，并且要遵照字段顺序。（注意隐式转换的特性）
default表示空串，和null也是一个意思。
```bash
alter table emp01 modify (job varchar2(9) default '临时工');
insert into emp01(empno, ename, sal) values(9003, 'JJY02', 6666);
insert into emp01(empno, ename, sal, job) values(9003, 'JJY02', 6666, default);
```
修改表中job字段的默认值，对此前已有的字段不产生影响
```bash
insert into emp01(empno, ename, sal) values(9004, '&ename000', 9999);
[input] JJY03
insert into emp01(empno, ename, sal) values(9004, 'JJY03', 9999);
```
```bash
insert into emp01(empno, ename, sal) values(9005, &ename000, 8888);
[input] 'JJY04'
insert into emp01(empno, ename, sal) values(9005, 'JJY04', 8888);
```
```bash
insert into emp01(empno, ename, sal) values(9006, '&&ename111', 7777);
[input] JJY05
insert into emp01(empno, ename, sal) values(9006, 'JJY05', 7777);
```
此后只要用到了ename111，就直接使用输入的值，使用时用&ename111。
相当于在SQLplus下申明了一个变量，退出数据库后自动清除。
```bash
insert into emp01(empno, ename, sal) values(9007, '&ename111', 6666);
```
<br>
##### 单表多行插入
```bash
insert into emp01 select * from emp where sal >= 3000;
insert into emp01 (empno, ename, sal, job, deptno) select empno, ename, sal, job, deptno from emp where sal >= 3000;
```
使用了子查询，代码中不能再使用values
<br>
#### 多表插入（应用很少）
```bash
insert all into emp01(empno, ename, sal) values(eno, ename, esal)
	into emp02(empno, ename, job, deptno) value(eno, ename, esal, ejob, dno)
	select empno eno, ename, job ejob, sal esal, deptno dno from emp where sal >= 2000;
```
```bash
insert all when esal > 2000 then
	into emp01(empno, ename, sal) values(eno, ename, esal)
	when esal > 1500 then
	into emp02(empno, ename, sal, job, deptno) values(eno, ename, esal, ejob, dno)
	select empno eno, ename, job ejob, sal esal, deptno dno from emp;
```
```bash
insert first when esal > 2000 then
	into emp01(empno, ename, sal) values(eno, ename, esal)
	when esal > 1500 then
	into emp02(empno, ename, sal, job, deptno) values(eno, ename, esal, ejob, dno)
	select empno eno, ename, job ejob, sal esal, deptno dno from emp;
```
```bash
insert into emp01(empno, ename, sal)
	select 9001, 'JJY01', 8000 from dual [ = select 1 from dual ]
	union
	select 9002, 'JJY02', 8000 from dual
	union
	select 9003, 'JJY03', 7000 from dept;
```
<br>
#### 修改
```bash
update emp01 set comm = 10000;
update emp01 set comm = 10000 where ename = 'JJY01' ;
```
<br>
#### 删除
```bash
delete emp01 where empno = 9001;
delete emp01;
truncate table emp01; [ = delete emp01 ]
```
比delete删除全表时，效率更高！
<br>
#### 合并（了解即可）
```bash
merge into emp01
using emp e
on (emp01.emono = e.empno)
when matched then
	update set sal = e.sal
	delete where sal > 1500
when not matched then
	insert (empno, ename, sal) into emp01
```
<br>
#### 事务
1. commit
2. set autocommit on
3. rollback
4. savepoint b

<br>
### 数据隔离级别
- 读未提交
- 读已提交√
- 可重复读
- 序列化度√ 

<br>
#### 读取问题
- 没有提交的数据被读取了：賍读
- 在同一个事务下，多次执行同一条语句，结果中的值产生变化：不可重复读
- 在同一个事务下，多次执行同一条语句，得到的结果不同：幻读

<br>
### 数据恢复
在Oracle安装目录下，找到RDBMS—ADMIN—scott.sql，正常执行此文件即可。
方法：
1. 新建一个批处理文件 initDB.bat
2. 内容：
```bash
set NLS_LANG=SIMPILIFIED CHINESE_CHINA.ZHS16GBK
sqlplus sys/oracle@testdb as sysdba @scott.sql > initDB.log
```
3. 对scott.sql进行修改
```bash
[line18] rem SET TERMOUT OFF
[line27] CONNECT scott/tiger@testdb
[line28] DROP TABLE DEPT cascade constraints;
[line52-79] 修改好日期格式
[line end] quit;
```