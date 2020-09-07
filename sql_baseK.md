
SQL语言分类
------------

数据查询语言（DQL）：是由SELECT子句，FROM子句，WHERE子句组成的查询块<br>
数据操纵语言（DML）: SELECT(查询) INSERT(插入) UPDATE(更新) DELETE(删除）<br>
数据`定义`语言（DDL）：CREATE(创建数据库或表或索引）ALTER(修改表或者数据库）DROP(删除表或索引）<br>
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

删除
------
处理效率：drop>trustcate>delete  <br>
1. drop是完全删除表，包括表结构 `DROP TABLE table_name`<br>
2. delete是删除表数据，保留表的结构，而且可以加where,只删除一行或者多行  `DELETE FROM table_name` <br>
3. truncate只能删除表数据，会保留表结构，而且不能加where；truncate将直接删除原来的表，并重新创建一个表


修改
------
change:用来修改字段名字以及类型 <br>
modify:用来修改字段类型<br>
aiter column ... set:用来修改字段数据; 修改表的某字段的缺省值：alter table 表名 alter column 字段名 set default 默认值;

