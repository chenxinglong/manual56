##C.5.8. MySQL中已知的问题

本节列出了在最近的MySQL版本中已知的问题。

关于平台相关问题，安装和移植的介绍，请见章节[2.1，“General Installation Guidance”][02.01.00]，和章节[23.4，“Debugging and Porting MySQL”][23.04.00]。

下述问题是已知的：  

* 对于IN子查询的优化并不像“=”那么有效率。

* 即使使用了lower\_case\_table\_names=2（让MySQL记住数据库名和表名使用的大小写），对于函数[DATABASE()][DATABASE]或在各种日志内（在不区分大小写的系统上），MySQL也不会记住数据库名使用的大小写情况。

* 在复制过程中，撤销FOREIGN KEY约束不会正常复制，这是因为在主从服务器上的约束名称可能不一样。

* [REPLACE][]（以及具有[REPLACE][]选项的[LOAD DATA][]）不会触发ON DELETE CASCADE。

* 如果未使用DISTINCT列表中的所有列（并且只包含这些列），在[GROUP\_CONCAT()][GROUP_CONCAT]中，带有ORDER BY的DISTINCT无法生效。

* 将长整型数值（介于2^63和2^64–1之间）插入decimal或string列时，它将作为负值插入，这是因为该数值是在有符号整数环境下计算得到的。

* 如果在正在执行[INSERT DELAYED][]操作的非事务性表上执行[ANALYZE TABLE][]、[OPTIMIZE TABLE][]和 [REPAIR TABLE][]可能会造成问题。

* 对于基于语句的二进制日志，主服务器将查询语句写入二进制日志中。这是一种非常快，而且紧凑高效记录日志的方法，而且这个方法在大多数情况下都运行得非常好。然而，如果一个查询修改的数据是不确定的（一般不推荐这种做法，即使在复制之外的），那么这就可能导致主服务器和从服务器之间的数据产生差异。

  例如：
  * 语句（[CREATE TABLE ... SELECT][]和[INSERT ... SELECT][]）插入零或者NULL值到一个自增（AUTO_INCREMENT）列中

  * 删除语句（[DELETE][]），如果你从一张带有外键约束的表中删除行，并且外键约束具有级联删除的属性（ON DELETE CASCADE）。

  * 语句（[REPLACE ... SELECT][]，[INSERT IGNORE ... SELECT][]），如果在插入的数据中有重复的键值。  
  
  ***如果，并且仅如果前一个查询没有ORDER BY子句来保证一个确定的顺序。***

  例如，对于语句（[INSERT ... SELECT][]）没有ORDER BY，这个查询（[SELECT][]）可能返回不同顺序的行（结果中行的顺序不同，于是在AUTO_INCREMENT列中产生了不同的序号），这依赖于主服务器和从服务器上的优化器做出的选择。

  一个查询在主服务器和从服务器分别被优化成不同的执行路径，仅如果：

  * 主服务器和从服务器上的表使用了不同的存储引擎。（主从服务器上使用不同的存储引擎是可能的。例如，主服务器使用InnoDB，但从服务器上由于空间不足而使用MyISAM。）

  * MySQL的缓冲大小（[key\_buffer\_size][key_buffer_size]等）在主从服务器上可能是不同的

  * 主从服务器运行了不同的MySQL版本，并且这两个版本的优化器代码不同。

  这个问题也可能影响到使用方法（**mysqlbinlog|mysql**）来恢复数据库。

  避免这个问题最简单的办法是在前面提到的不确定的查询中增加一个ORDER BY子句，来保证被保存或者修改的行的顺序总是保持一致。使用基于行（row-based）或者混合的（mixed）二进制日志格式也可以避免这个问题。

* 如果你在启动选项中没有指定日志文件的名称，那么这个名称就会基于主机名称来命名。为了保持相同的日志文件名，如果你将主机名修改为别的名称，你必须明确的使用选项（例如，[--log-bin=old\_host\_name-bin][log-bin]）来指定日志的名称。请见章节[5.1.3，“Server Command Options”][05.01.03]。另外，可以将原来的日志参照主机名一起更改。如果有二进制日志，你必须编辑二进制日志索引文件名和内容，以及二进制日志文件的名称。（从服务器上的中继日志也同样处理）

* [mysqlbinlog][]不会删除语句（[LOAD DATA INFILE][]）执行后留下的临时文件。请见章节[4.6.8, “**mysqlbinlog** — Utility for Processing Binary Log Files”][04.06.08]。

* RENAME语句在临时（TEMPORARY）表和合并（MERGE）表上无法运行。

* 当使用了SET CHARACTER SET，你无法在数据库、表和列名上使用转换了字符的名称。

* 你无法在[LIKE ... ESCAPE][]中的ESCAPE部分使用“\_”或者“%”。

* 当比较数据值大小时，只有前面的[max\_sort\_length][max_sort_length]字节被使用。这就意味着如果这些值不能在前[max\_sort\_length][max_sort_length]个字符内完成去重，那么就不能准确在GROUP BY、ORDER BY或DISTINCT等子句中使用。为了解决这个问题，可以增大变量的值。[max\_sort\_length][max_sort_length]的默认值大小是1024，并且这个值可以在启动或者MySQL运行中进行更改。


* 数值都是使用[BIGINT][]或者[DOUBLE][]（通常是64位长）进行计算的。你获得的精度依赖于函数。正常情况下，位函数使用BIGINT的精度来执行，IF（）和ELT（）使用BIGINT或者DOUBLE精度，剩余部分使用DOUBLE精度。如果位字段以外的其他内容被解析为比63位（9223372036854775807）更大，你应该尽量避免使用无符号的长值。

* 你可以在一个表中设置枚举（[ENUM][]）和集合（[SET][]）的列数高达255个。

* 在[MIN()][MIN]、[MAX()][MAX]和其他聚合函数中，MySQL通常比较枚举（[ENUM][]）和集合（[SET][]）列是通过字符串的值而不是字符串在集合中对应的位置。

* 在[UPDATE][]语句中，列的更新顺序是从坐到右。如果你引用了一个已经被更新了的列，你会得到一个更新后的值，而不是原来的值。例如，下面的语句在KEY上增加了2，**不是**1：

		mysql> UPDATE tbl_name SET KEY=KEY+1,KEY=KEY+1;
* 你可以在同一个语句中引用多个临时表，但是你无法多次引用同一个临时表。例如，下述语句是无法运行的：

		mysql> SELECT * FROM temp_table, temp_table AS t2;
		ERROR 1137: Can't reopen table: 'temp_table'

* 当表连接中有“隐藏”列时，优化器处理DISTINCT与表连接中没有“隐藏”列是不同的。在表连接中，隐藏列也是结果的一部分（即使隐藏列并不会显示出来），而在正常的查询中，隐藏列并不会参与DISTINCT比较运算。  
举例如下：
	
		SELECT DISTINCT mp3id FROM band_downloads
		       WHERE userid = 9 ORDER BY id DESC;
	和
	
		SELECT DISTINCT band_downloads.mp3id
		       FROM band_downloads,band_mp3
		       WHERE band_downloads.userid = 9
		       AND band_mp3.id = band_downloads.mp3id
		       ORDER BY band_downloads.id DESC;
在第二个实例中，使用MySQL3.23.x，你可能会在结果集得到两个相同的行（因为隐藏id列中的值可能会有所不同）。  
注意，这只发生在查询中没有ORDER BY列时。

* 如果你在一个返回空结果集的查询中执行PROCEDURE，在某些情况下PROCEDURE不会返回这些列。

* 创建MERGE类型的表不会检查与基础表是否兼容

* 如果使用[ALTER TABLE][]在使用了MERGE的表上添加一个UNIQUE索引，然后又在MERGE表上添加了一个正常的索引，索引的顺序就会与表里原本就有的非唯一索引的顺序不同。
这是因为[ALTER TABLE][]语句在将数据放入普通索引前，先放入唯一索引，这样能尽早的检测到重复键。

[02.01.00]:../Chapter_02/02.01.00_General_Installation_Guidance.md
[23.04.00]:../Chapter_23/23.04.00_Debugging_and_Porting_MySQL.md
[DATABASE]:../Chapter_12/12.14.00_Information_Functions.md#function_datebase
[REPLACE]:../Chapter_13/13.02.08_REPLACE_Syntax.md
[LOAD DATA]:../Chapter_13/13.02.06_LOAD_DATA_INFILE_Syntax.md
[GROUP_CONCAT]:../Chapter_12/12.17.01_GROUP_BY_Aggregate_Functions.md#function_group_concat
[INSERT DELAYED]:../Chapter_13/13.02.05_INSERT_Syntax.md#13.02.05.02
[ANALYZE TABLE]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.01
[OPTIMIZE TABLE]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.04
[REPAIR TABLE]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.05
[CREATE TABLE ... SELECT]:../Chapter_13/13.01.17_CREATE_TABLE_Syntax.md#13.01.17.01
[INSERT ... SELECT]:../Chapter_13/13.02.05_INSERT_Syntax.md#13.02.05.01
[DELETE]:../Chapter_13/13.02.02_DELETE_Syntax.md
[REPLACE ... SELECT]:../Chapter_13/13.02.08_REPLACE_Syntax.md
[INSERT IGNORE ... SELECT]:../Chapter_13/13.02.05_INSERT_Syntax.md#13.02.05.01
[SELECT]:..../Chapter_13/13.02.09_SELECT_Syntax.md
[key_buffer_size]:../Chapter_05/05.01.04_Server_System_Variables.md#key_buffer_size
[log-bin]:../Chapter_16/16.01.04_Replication_and_Binary_Logging_Options_and_Variables.md#log-bin
[05.01.03]:../Chapter_05/05.01.03_Server_Command_Options.md
[mysqlbinlog]:../Chapter_04/04.06.08_mysqlbinlog_Utility_for_Processing_Binary_Log_Files.md
[LOAD DATA INFILE]:../Chapter_13/13.02.06_LOAD_DATA_INFILE_Syntax.md
[04.06.08]:../Chapter_04/04.06.08_mysqlbinlog_Utility_for_Processing_Binary_Log_Files.md
[LIKE ... ESCAPE]:../Chapter_12/12.05.01_String_Comparison_Functions.md#LIKE_ESCAPE
[max_sort_length]:../Chapter_05/05.01.04_Server_System_Variables.md#max_sort_length
[ENUM]:../Chapter_11/11.04.04_The_ENUM_Type.md
[SET]:../Chapter_11/11.04.05_The_SET_Type.md
[MIN]:../Chapter_12/12.17.01_GROUP_BY_Aggregate_Functions.md#function_min
[MAX]:../Chapter_12/12.17.01_GROUP_BY_Aggregate_Functions.md#function_max
[UPDATE]:../Chapter_13/13.02.11_UPDATE_Syntax.md
[ALTER TABLE]:../Chapter_13/13.01.07_ALTER_TABLE_Syntax.md