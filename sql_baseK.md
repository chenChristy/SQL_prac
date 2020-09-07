
SQL语言分类
------------

数据查询语言（DQL）：是由SELECT子句，FROM子句，WHERE子句组成的查询块<br>
数据操纵语言（DML）: SELECT(查询) INSERT(插入) UPDATE(更新) DELETE(删除）<br>
数据定义语言（DDL）：CREATE(创建数据库或表或索引）ALTER(修改表或者数据库）DROP(删除表或索引）<br>
数据控制语言（DCL）：GRANT(赋予用户权限） REVOKE(收回权限） DENY(禁止权限)<br>
事务控制语言（TCL）：SAVEPOINT (设置保存点）ROLLBACK (回滚) COMMIT(提交)<br>


字符串搜索
----------
数据库中`字符串的索引开始于1`<br>
通过CHARINDEX如果能够找到对应的字符串，则返回该字符串位置，否则返回0。


子查询
------
All：对所有数据都满足条件，整个条件才成立；<br>
Any：只要有任何一个满足条数据满足条件，整个条件成立，返回true；<br>
Some的作用和Any一样.


Having子句的使用
----------------
having字句即可包含聚合函数（avg,min,max)也可包含普通的标量字段<br>
having字句必须与group by字句同时使用，`group by子句是限定分组条件的`，having是过滤分组的

