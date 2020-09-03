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

