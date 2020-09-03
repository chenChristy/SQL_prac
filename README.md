# SQL 练习



1.统计新登录用户的次日成功留存率 【牛客网-每个人最近的登录日期（三）】
-----------------------------------------------------------

[题目链接](https://www.nowcoder.com/practice/16d41af206cd4066a06a3a0aa585ad3d?tpId=82&tags=&title=&diffculty=0&judgeStatus=0&rp=1)

有一个登录(login)记录表，（如第1行表示id为2的用户在2020-10-12使用了客户端id为1的设备第一次新登录了牛客网）<br>
请你写出一个sql语句查询新登录用户次日成功的留存率，即**第1天登陆之后，第2天再次登陆的概率**,保存小数点后面3位(3位之后的四舍五入)<br>

>四舍五入的函数为round<br>
>sqlite里查找某一天的后一天的用法是:date(yyyy-mm-dd, '+1 day')<br>
>sqlite 1/2得到的不是0.5，得到的是0，只有1*1.0/2才会得到0.5)<br>

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
