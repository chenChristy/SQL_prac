# SQL 练习



1.统计新登录用户的次日成功留存率 【牛客网-每个人最近的登录日期（三）】
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/16d41af206cd4066a06a3a0aa585ad3d?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

有一个登录(login)记录表，（如第1行表示id为2的用户在2020-10-12使用了客户端id为1的设备第一次新登录了牛客网）<br>
请你写出一个sql语句查询新登录用户次日成功的留存率，即**第1天登陆之后，第2天再次登陆的概率**,保存小数点后面3位(3位之后的四舍五入)<br>

>四舍五入的函数为round<br>
>sqlite里**查找某一天的后一天**的用法是:**date(yyyy-mm-dd, '+1 day')**<br>
>sqlite 1/2得到的不是0.5，得到的是0，只有`1*1.0/2`才会得到0.5)<br>

```
select round(count(distinct b.user_id)*1.0/count(1), 3) as p
from (select user_id, min(date) as min_date 
      from login 
      group by user_id) as a
left join login b 
on a.user_id=b.user_id 
and b.date=date(a.min_date, '+1 day') 

//sql
```


2.统计一下牛客每个日期登录新用户个数 【牛客网-每个人最近的登录日期（四）】
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/e524dc7450234395aa21c75303a42b0a?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

有一个登录(login)记录表，（如第1行表示id为2的用户在2020-10-12使用了客户端id为1的设备第一次新登录了牛客网）<br>
请你写出一个sql语句查询每个日期登录新用户个数，并且查询结果按照日期升序排序<br>

>输出0，可以用sqlite的**ifnull函数**尝试实现，select ifnull(null,1)的输出是1<br>


**题解**
```
select l1.date,count(distinct l1.user_id)
from login l1
group by l1.date;

//sql 
```
这样可以得到每个日期里面，用户登录的数目。
加一个where判断条件就能从这每个日期里面，用户登录的数目取出哪些是新用户。

```
select l1.date,count(distinct l1.user_id)
from login l1
where l1.date =
(select min(date) from login where user_id=l1.user_id)
group by l1.date;

//sql
```
当这个日期，正好是这个用户登录的最小日期，而且用户id相同时，那么肯定就是这个日期登录的新用户。

不过这样的话，2020-10-13没有新用户登录，应该输出为0的，这个语句却没有输出。但是login表的日期是完整的，所以我们考虑将login表当主表，上面查出来的表左连接到主表，顺序输出，并使用ifnull语句将null变成0，最后再加上一个order by语句，就可以得到题目想要的结果了:

```
select login.date,ifnull(n1.new_num,0)
from login 
left join 

(select l1.date,count(distinct l1.user_id) as new_num
from login l1
where l1.date =
(select min(date) from login where user_id=l1.user_id)
group by l1.date) n1           /*可以选取出每日登录新用户不为0的日期和登录新用户数作为n1表*/

on login.date = n1.date

/*将login左连接n1表，由于n1表中已经是每日登录新用户不为0的日期和新用户数，
因此，连接后login表中有n1表中日期的行，对应的该日登录新用户就是n1中的新用户数；
而login中不在n1表中的日期的行，对应的该日登录新用户数均为null，
同时select中ifnull()函数把null值变为0*/

group by login.date 
order by login.date;

//sql
```

3.统计出现三次以上相同积分的情况（不同ID）
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/c69ac94335744480aa50646864b7f24d?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

有一积分(grade)表, id为用户主键id，number代表积分情况，让你写一个sql查询，积分表里面出现三次以及三次以上的积分


>要三次以上的积分，那么肯定要查找3个id不同但是积分相同的情况<br>

>>方法一：**要三次以上的积分，那么肯定要查找3个id不同但是积分相同的情况**，怎么比较一列和另外一列是否相等呢?在一个表里面感觉无法做到，那么最简单就是利用笛卡尔积了，1个表看成3个表，联立三个表number相同的部分.那么可能就是举例一种情况就是寻找第1个表id为1的111，寻找第2个表id为3的111，寻找第3个表id4为的111，那么就找到一个111，输出111
但是上面这种找法可能会有重复的，比如第1个表id为3的111，寻找第2个表id为1的111，寻找第3个表id4为的111，那么又找到一个111，所以要去重。代码如下:
```
SELECT  DISTINCT g1.number AS times
FROM
    grade g1,
    grade g2,
    grade g3
WHERE
    g1.id != g2.id
    AND g2.id != g3.id 
    AND g1.id !=g3.id
    AND g1.number = g2.number
    AND g2.number = g3.number
;


//sql
```


>>方法二：使用group by将积分分组，然后用having找到积分的个数大于等于3的:
```
select num from grade group by `number` having count(*)>=3

//sql
```




4.统计刷题通过的题目排名
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/cd2e10a588dc4c1db0407d0bf63394f3?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

有一个通过题目个数的(passing_number)表，id是主键，如第1行表示id为1的用户通过了4个题目;请你根据上表，输出通过的题目的排名，通过题目个数相同的，排名相同，此时按照id升序排列，如id为5的用户通过了5个排名第1，id为1和id为6的都通过了2个，并列第2。


```
select a.id, a.number, 
(select count(distinct b.number) from passing_number b where b.number>= a.number) as rank
from passing_number a
order by a.number desc, a.id asc;

//sql
```



5.统计奇数行的first_name
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/e3cf1171f6cc426bac85fd4ffa786594?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

对于employees表中，输出first_name排名(按first_name升序排序)为奇数的first_name。

> 1. 首先题目要求对first_name排序，所以子查询里有first_name的比较。
> 2. 通过比较，如果 e1.first_name 是第一位，那 e2.first_name 只有1个，就是 e1.first_name 本身，1%2=1；如果 e1.first_name 排在第二位，就有它和比它小的2个 e2.first_name，2%2=0，所以不选，以此类推。

```
select e1.first_name
from employees e1
where (
    select count(*)        /*利用count函数 排号*/
    from employees e2
    where e1.first_name>=e2.first_name)%2=1   /*取奇数*/

//sql
```


6.统计salary的累计和running_total
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/58824cd644ea47d7b2b670c506a159a6?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

按照salary的累计和running_total，其中running_total为前N个当前( to_date = '9999-01-01')员工的salary累计和，其他以此类推。 
```
CREATE TABLE `salaries` ( `emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

难点在于：running_total为前 N个当前( to_date = '9999-01-01')员工的salary累计
> 本题的思路为复用salaries表进行子查询，最后以 s1.emp_no 排序输出求和结果。
>> 1、输出的第三个字段，是由一个SELECT子查询构成。将子查询内复用的salaries表记为s2，主查询的salaries表记为s1，当主查询的s1.emp_no确定时，对子查询中不大于s1.emp_no的s2.emp_no所对应的薪水求和<br>
>> 2、注意是**对员工当前的**薪水求和，所以在**主查询和子查询内都要加限定条件**to_date ='9999-01-01'

```
select s1.emp_no,s1.salary,
   (select sum(s2.salary) 
    from salaries s2
    where s2.emp_no <= s1.emp_no
    and s2.to_date='9999-01-01') as running_total

from salaries s1
where s1.to_date='9999-01-01'
order by s1.emp_no

//sql
```


7.统计有奖金的员工发信息
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/5cdbf1dcbe8d4c689020b6b2743820bf?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

给出emp_no、first_name、last_name、奖金类型btype、对应的当前薪水情况salary以及奖金金额bonus。bonus类型btype为1其奖金为薪水salary的10%，btype为2其奖金为薪水的20%，其他类型均为薪水的30%。当前薪水表示to_date='9999-01-01'

> Case... when...else... End用以不同情况的分类讨论

```
select e.emp_no, e.first_name, e.last_name, eb.btype, s.salary,
(case eb.btype
    when 1 then s.salary*0.1
    when 2 then s.salary*0.2
    else s.salary*0.3 end) as bonus

from employees as e
inner join emp_bonus as eb
on e.emp_no=eb.emp_no
inner join salaries s
on e.emp_no=s.emp_no
where s.to_date='9999-01-01'

//sql
```


8.使用含有关键字exists查找未分配具体部门的员工的所有信息。
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/c39cbfbd111a4d92b221acec1c7c1484?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)
使用含有关键字exists查找未分配具体部门的员工的所有信息。
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```

> Exists的用法：exists对外表用loop逐条查询，每次查询都会查看exists的条件语句，当exists里的条件语句能够返回记录行时(无论记录行是的多少，只要能返回)，条件就为真，返回当前loop到的这条记录;反之如果exists里的条件语句不能返回记录行，则当前loop到的这条记录被丢弃，exists的条件就像一个bool条件，当能返回结果集则为true，不能返回结果集则为false
>> 如果外表有n条记录，那么exists查询就是**将这n条记录逐条取出，然后判断n遍exists条件**


1. EXISTS是先从主查询中取得一条数据，再代入到子查询中，执行一次子查询，判断子查询是否能返回结果，主查询有多少条数据，子查询就要执行多少次

```
SELECT *
FROM employees
WHERE NOT EXISTS (SELECT emp_no
                 FROM dept_emp
                 WHERE employees.emp_no = dept_emp.emp_no);
```

2. IN是先执行子查询，得到一个结果集，将结果集代入外层谓词条件执行主查询，子查询只需要执行一次

```
SELECT *
FROM employees
WHERE emp_no NOT IN (SELECT emp_no FROM dept_emp);

//sql
```


9.获取Employees表中的first_name
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/74d90728827e44e2864cce8b26882105?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

获取Employees中的first_name，查询按照first_name最后两个字母，按照升序进行排列

> substr(X,Y,Z) 或 substr(X,Y) 函数的使用。
其中X是要截取的字符串。Y是字符串的**起始位置（注意第一个字符的位置为1，而不为0）**，取值范围是±(1~length(X))，当Y等于length(X)时，则截取最后一个字符；当Y等于负整数-n时，则从倒数第n个字符处截取。Z是要截取字符串的长度，取值范围是正整数，若Z省略，则从Y处一直截取到字符串末尾；若Z大于剩下的字符串长度，也是截取到字符串末尾为止。

```
select first_name
from employees
order by substr(first_name,-2,2);

//sql
```

10.统计有奖金的员工发信息
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/e3870bd5d6744109a902db43c105bd50?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

查找字符串'10,A,B' 中逗号','出现的次数cnt。

```
select (length('10,A,B')-length(replace('10,A,B',',',''))) as cnt

//sql
```


11.用||链接两个字符串
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/355036f7f0c8429a85281f7ac05b457a?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

针对库中的所有表生成"select count(*)"对应的SQL语句，如数据库里有以下表，(注:在 SQLite 中用 “||” 符号连接字符串，无法使用concat函数)
>链接a和b两个字符串, 用**a||b**

```
select "select count(*) from "||name||";" as cnts 
from sqlite_master
where type='table';

//sql
```


12.统计考试前2分数的学生
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/b83f8b0e7e934d95a56c24f047260d91?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

每个用户笔试完有不同的分数，现在有一个分数(grade)表简化如下，如第1行表示用户id为1的选择了language_id为1岗位的最后考试完的分数为12000；
请你找出每个岗位分数排名前2的用户，得到的结果先按照language的name升序排序，再按照积分降序排序，最后按照grade的id升序排序


**解法1**

```
select g1.id, g1.name, g1.score
from (grade gr
join language l on gr.language_id=l.id)g1
where (
    select count(distinct g2.score)
    from grade g2
    where g2.score>=g1.score and g1.language_id=g2.language_id)<=2
order by g1.name,g1.score desc ,g1.id;

//sql 不使用特殊函数
```

**解法2**

> 1、over函数的写法：<br>

　　over（partition by class order by sroce） 按照sroce排序进行累计，order by是个默认的开窗函数，按照class分区。<br>

> 2、开窗的窗口范围：<br>

　　over（order by sroce range between 5 preceding and 5 following）：窗口范围为当前行数据幅度减5加5后的范围内的。<br>

　　over（order by sroce rows between 5 preceding and 5 following）：窗口范围为当前行前后各移动5行。<br>
  
>> rank与over()的使用
  
  rank()和dense_rank()可以将所有的都查找出来，rank可以将并列第一名的都查找出来；
  rank()和dense_rank()区别：rank()是跳跃排序，有两个第二名时接下来就是第四名;dense_rank()l是连续排序，有两个第二名时仍然跟着第三名

求班级成绩排名：
```
select t.name,t.class,t.sroce,rank() over(partition by t.class order by t.sroce desc) mm from T2_TEMP t;
```

```
select t.name,t.class,t.sroce,dense_rank() over(partition by t.class order by t.sroce desc) mm from T2_TEMP t;
```

>> sum()over()的使用
根据班级进行分数求和

```
select t.name,t.class,t.sroce,sum(t.sroce) over(partition by t.class order by t.sroce desc) mm from T2_TEMP t;
```

>> first_value() over()和last_value() over()的使用 
分别求出第一个和最后一个成绩。

```
select t.name,t.class,t.sroce,first_value(t.sroce) over(partition by t.class order by t.sroce desc) mm from T2_TEMP t;
select t.name,t.class,t.sroce,last_value(t.sroce) over(partition by t.class order by t.sroce desc) mm from T2_TEMP t;
```

>> 常用的搭配
　　count() over(partition by ... order by ...)：求分组后的总数。
　　max() over(partition by ... order by ...)：求分组后的最大值。
　　min() over(partition by ... order by ...)：求分组后的最小值。
　　avg() over(partition by ... order by ...)：求分组后的平均值。
　　lag() over(partition by ... order by ...)：取出前n行数据。　　

　　lead() over(partition by ... order by ...)：取出后n行数据。

　　ratio_to_report() over(partition by ... order by ...)：Ratio_to_report() 括号中就是分子，over() 括号中就是分母。

　　percent_rank() over(partition by ... order by ...)：

、、、
select id, name, score 
from
(select g.id, l.name, g.score,
(dense_rank() over(partition by language_id order by score desc)) rn
from grade g join language l 
on g.language_id = l.id) t 
where rn<= 2
order by name,score desc,id;

//sql
```
