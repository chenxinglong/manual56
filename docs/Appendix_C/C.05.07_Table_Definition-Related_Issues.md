##C.5.7. 表定义相关的内容
[+/-]

[C.5.7.1. Problems with ALTER TABLE][C.05.07.01]  
[C.5.7.2. TEMPORARY Table Problems][C.05.07.02]

[C.05.07.01]:./C.05.07_Table_Definition-Related_Issues.md#C.05.07.01
[C.05.07.02]:./C.05.07_Table_Definition-Related_Issues.md#C.05.07.02


###C.5.7.1. ALTER TABLE相关的问题

当使用语句（[ALTER TABLE][]）来修改字符列的字符集或者检验规则时，遇到重复键（duplicate-key）错误，原因在于新的列校验规则将2个键映射到了相同值上，或是表已损坏。在后一种情况下，应在表上运行[REPAIR TABLE][]。

如果[ALTER TABLE][]失败并给出下述错误，问题可能是因为在[ALTER TABLE][]操作的早期阶段出现MySQL崩溃，并且没有名为A-xxx或B-xxx的旧表：

	Error on rename of './database/name.frm'
	to './database/B-xxx.frm' (Errcode: 17)
在该情况下，进入MySQL数据目录，并删除其名称为以A-或B-开始的所有文件（或许你希望将它们移动到其他地方而不是删除它们）。

[ALTER TABLE][]的工作方式如下：

用请求的结构变化创建一个名为A-xxx的新表。

将所有行从原始表拷贝到A-xxx。

将原始表重命名为B-xxx。

将A-xxx重命名为原始表的名称。

删除B-xxx。 

如果在重命名操作中出错，MySQL将尝试撤销更改。如果错误很严重（尽管这不应出现），MySQL会将旧表保留为B-xxx。简单地在系统级别上重命名表文件，应能使数据复原。

如果在事务性表上使用[ALTER TABLE][]，或正在使用Windows操作系统，如果已在表上执行了[LOCK TABLE][]操作，[ALTER TABLE][]将对表执行解锁操作。这是因为InnoDB和这类操作系统不能撤销正在使用的表。

[ALTER TABLE]:../Chapter_13/13.01.07_ALTER_TABLE_Syntax.md
[REPAIR TABLE]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.05
[LOCK TABLE]:../Chapter_13/13.03.05_LOCK_TABLES_and_UNLOCK_TABLES_Syntax.md


###C.5.7.2. 临时表问题

下面介绍了使用TEMPORARY表的限制：

* TEMPORARY表只能是MEMORY、MyISAM、MERGE、或InnoDB类型。  
临时表不支持MySQL集群。


* 在相同的查询中，不能多次引用TEMPORARY表。例如，下例中的语句不能正常执行：

		mysql> SELECT * FROM temp_table, temp_table AS t2;
		ERROR 1137: Can't reopen table: 'temp_table'
如果你在存储过程中使用不同的别名多次引用临时表，即使引用分布在不同的语句中，这个错误也会发生。

* [SHOW TABLES][]语句不会列出TEMPORARY表。

* 不能使用RENAME重命名TEMPORARY表。但可以使用[ALTER TABLE][]代替：

		mysql> ALTER TABLE orig_name RENAME new_name;
* 复制特性在临时标准存在一些已知的问题，更多的信息，请见章节[16.4.1, “Replication Features and Issues”][16.04.01]。

[SHOW TABLES]:../Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.38
[16.04.01]:../Chapter_16/16.04.01_Replication_Features_and_Issues.md
