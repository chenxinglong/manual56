##C.5.6. 优化器相关的问题

MySQL采用了基于开销的优化器，以确定处理查询的最佳方式。在很多情况下，MySQL能够计算最佳的可能查询计划，但在某些情况下，MySQL没有关于数据的足够信息，不得不就数据进行有经验的猜测。

当MySQL未能做“正确的”事时，可使用下述工具来帮助MySQL：

使用[EXPLAIN][]语句获取关于MySQL如何处理查询的信息。要想使用它，可在[SELECT][]语句前添加关键字[EXPLAIN][]即可：

	mysql> EXPLAIN SELECT * FROM t1, t2 WHERE t1.i = t2.i;
更多关于[EXPLAIN][]的讨论，请见章节[13.8.2，“EXPLAIN Syntax”][13.08.02]。

使用ANALYZE TABLE tbl\_name，为已扫描的表更新键分布。请见章节[13.7.2.1，“ANALYZE TABLE Syntax”][13.07.02.01]。

为已扫描的表使用FORCE INDEX，通知MySQL：与使用给定的索引相比，表扫描开销昂贵：

	SELECT * FROM t1, t2 FORCE INDEX (index_for_column)
	WHERE t1.col_name=t2.col_name;
使用USE INDEX和IGNORE INDEX也会同样会有帮助。请见章节[13.2.9.3，“Index Hint Syntax”][13.02.09.03]。

关于全局和表级别的STRAIGHT\_JOIN。请见章节[13.2.9，“SELECT 语法”][13.02.09]。

你可以调节全局或系统变量（thread-specific）。例如，用“[--max-seeks-for-key=1000][max-seeks-for-key]”选项启动**[mysqld][]**，或使用“SET max\_seeks\_for\_key=1000”来通知优化器，假定任何表扫描均不会导致1000个以上的键搜索。请见章节[5.1.4，“Server System Variables”][05.01.04]。

[EXPLAIN]:../Chapter_13/13.08.02_EXPLAIN_Syntax.md
[SELECT]:../Chapter_13/13.02.09_SELECT_Syntax.md
[13.08.02]:../Chapter_13/13.08.02_EXPLAIN_Syntax.md
[13.07.02.01]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.01
[13.02.09.03]:../Chapter_13/13.02.09_SELECT_Syntax.md#13.02.09.03
[13.02.09]:../Chapter_13/13.02.09_SELECT_Syntax.md
[mysqld]:../Chapter_04/04.03.01_mysqld_The_MySQL_Server.md
[max-seeks-for-key]:../Chapter_05/05.01.04_Server_System_Variables.md#max-seeks-for-key
[05.01.04]:../Chapter_05/05.01.04_Server_System_Variables.md
