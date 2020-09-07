Hive SQL
------------


1. 有十万个淘宝店铺，每个顾客访问任意一个店铺时会生成一条访问日志，存储为表visit，其中访问用户ID的字段名称为uid,访问的店铺字段名称为store，请统计每个店铺的UV。
uv(unique visitor)，指访问某个站点或点击某条新闻的不同IP地址的人数。

```
select store, count(distinct uid) as uv
from visit
group by store;
```

2.有一亿个用户，被储存与表Users中，其中有用户的唯一字段UID，用户年龄age,和用户消费总金额total，请按照用户年龄从大到小进行排序，如果年龄相同，则按照总消费金额从小到大排序。

```
select * 
from users
order by age desc, total;
```


3. 当前有用户人生阶段表LifeStage，用户用唯一ID字段UID，用户人生阶段字段stage，其中stage字段内容为个人生阶段标签，按照英文逗号分割的拼接内容，如计划买车，已买房；且每个用户的内容不同，请统计每个人生阶段的用户量。
考点：列转行
--考察点: lateral view使用, explode函数

```
select stage_someone,count(distinct uid) as uids
from lifeStage
  lateral view explode(split(stage,',')) lifeStage_tmp as stage_someone
group by stage_someone;
```

--和上一题相同的数据场景, 但是lifeStage中每行数据存储一个用户的人生阶段数据
--如: 一行数据uid是43, stage内容为计划买车, 另一行数据uid字段为43,stage字段为已买房, 请输出类似于uid为43,stage字段为计划买车,已买房这样的新整合数据.
--考察点: collect_set函数, concat_ws函数

```
select uid,concat_ws(',',collect_set(stage_someone)) as stage
from lifestage_multline
group by uid;
```



统计a,b,c三列大于1的个数
----------------------

```
SELECT 
SUM(CASE WHEN a > 1 THEN 1 ELSE 0 END) a, 
SUM(CASE WHEN b > 1 THEN 1 ELSE 0 END) b, 
SUM(CASE WHEN c > 1 THEN 1 ELSE 0 END) c 
FROM test1;
```

[题目](https://img-bbs.csdn.net/upload/201803/09/1520591882_528248.png)

```
select faddr.city_name as '一级地区' ,saddr.city_name as '二级地区',taddr.city_name as '三级地区' from CITY a 
join (SELECT City_Id,City_Name,City_ParentId 
      from CITY) faddr 
      on a.City_Id=faddr.City_Id
join (SELECT City_Id,City_Name,City_ParentId 
      from CITY) saddr
      on faddr.City_Id=saddr.City_ParentId
join (SELECT City_Id,City_Name,City_ParentId 
      from CITY) taddr
      on saddr.City_Id=taddr.City_ParentId

 ```
