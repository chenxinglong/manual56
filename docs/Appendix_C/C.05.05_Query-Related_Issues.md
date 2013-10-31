##C.5.5. Query-Related Issues
##C.5.5. 与查询有关的问题
[+/-]

[C.5.5.1. Case Sensitivity in String Searches][C.05.05.01]
[C.5.5.2. Problems Using DATE Columns][C.05.05.02]
[C.5.5.3. Problems with NULL Values][C.05.05.03]
[C.5.5.4. Problems with Column Aliases][C.05.05.04]
[C.5.5.5. Rollback Failure for Nontransactional Tables][C.05.05.05]
[C.5.5.6. Deleting Rows from Related Tables][C.05.05.06]
[C.5.5.7. Solving Problems with No Matching Rows][C.05.05.07]
[C.5.5.8. Problems with Floating-Point Values][C.05.05.08]

[C.05.05.01]:./C.05.05_Query-Related_Issues.md#C.05.05.01
[C.05.05.02]:./C.05.05_Query-Related_Issues.md#C.05.05.02
[C.05.05.03]:./C.05.05_Query-Related_Issues.md#C.05.05.03
[C.05.05.04]:./C.05.05_Query-Related_Issues.md#C.05.05.04
[C.05.05.05]:./C.05.05_Query-Related_Issues.md#C.05.05.05
[C.05.05.06]:./C.05.05_Query-Related_Issues.md#C.05.05.06
[C.05.05.07]:./C.05.05_Query-Related_Issues.md#C.05.05.07
[C.05.05.08]:./C.05.05_Query-Related_Issues.md#C.05.05.08


###C.5.5.1. 字符串搜索中的大小写敏感性

对于非二进制字符串（[CHAR][]，[VARCHAR][]，[TEXT][]），字符串搜索使用校验规则来做比较运算。对于二进制字符串（[BINARY][]，[VARBINARY][]，[BLOB][]）使用字节的数字大小来做比较运算；这就是说字母字符是大小写敏感的。

将二进制和非二进制字符串作比较运算，将会把两个字符串当作二进制字符串来对待。

简单的比较操作（>=, >, =, <, <=, 排序和分组）基于每个字符的“排序值（sort value）”。具有相同排序值的字符将被当作相同的写字符。例如，如果“e”和“é”在给定的校验规则中有相同的排序值，那么比较结果就是相同的。

默认的字符集和校验规则是latin1和latin1\_swedish\_ci，所以非二进制字符串比较时，默认是大小写敏感的。这就是说如果你用col\_name LIKE 'a%'搜索，你将会得到以'A'或者'a'开头的所有列值。为了使这个搜索变成大小写敏感的，请确保操作元之一是大小写敏感或者校验规则为二进制。例如，如果你将列和字符串做对比运算，而它们都是latin1字符集，你可以使用COLLATE操作符来将他们的校验规则设置为latin1\_general\_cs或者latin1\_bin：

	col_name COLLATE latin1_general_cs LIKE 'a%'
	col_name LIKE 'a%' COLLATE latin1_general_cs
	col_name COLLATE latin1_bin LIKE 'a%'
	col_name LIKE 'a%' COLLATE latin1_bin
如果希望总是以区分大小写的方式处理列，可使用区分大小写的或二进制校对声明它。请见章节[13.1.17, “CREATE TABLE Syntax”][13.01.17]。

为了促使大小写敏感的非二进制字符串比较变为大小写不敏感，可以使用COLLATE来指派一个大小写不敏感咋校验规则。在下面这个例子中，字符串原本是大小写敏感的，但是COLLATE将对比变成了不敏感的：


	mysql> SET @s1 = 'MySQL' COLLATE latin1_bin,
    	->     @s2 = 'mysql' COLLATE latin1_bin;
	mysql> SELECT @s1 = @s2;
	+-----------+
	| @s1 = @s2 |
	+-----------+
	|         0 |
	+-----------+
	mysql> SELECT @s1 COLLATE latin1_swedish_ci = @s2;
	+-------------------------------------+
	| @s1 COLLATE latin1_swedish_ci = @s2 |
	+-------------------------------------+
	|                                   1 |
	+-------------------------------------+
在对比运算中二进制字符串是大小写敏感的。为了进行大小写不敏感的对比，可以使用COLLATE指定一个非大小写敏感的校验规则，将它转换为大小写非敏感的字符串。

	mysql> SET @s = BINARY 'MySQL';
	mysql> SELECT @s = 'mysql';
	+--------------+
	| @s = 'mysql' |
	+--------------+
	|            0 |
	+--------------+
	mysql> SELECT CONVERT(@s USING latin1) COLLATE latin1_swedish_ci = 'mysql';
	+--------------------------------------------------------------+
	| CONVERT(@s USING latin1) COLLATE latin1_swedish_ci = 'mysql' |
	+--------------------------------------------------------------+
	|                                                            1 |
	+--------------------------------------------------------------+
可以使用函数[COLLATION()][COLLATION]，来判断一个比较运算是使用二进制还是非二进制字符串。下面的例子中展示了[VERSION()][VERSION]返回一个大小写不敏感的校验规则字符串，所以对比运算是大小写不敏感的：

	mysql> SELECT COLLATION(VERSION());
	+----------------------+
	| COLLATION(VERSION()) |
	+----------------------+
	| utf8_general_ci      |
	+----------------------+
对于二进制字符串，校验规则的值是二进制，所以对比运算是大小写敏感的。在上下文中，你会看到二进制压缩和加密函数，正常情况下返回二进制字符串：字符串：

	mysql> SELECT COLLATION(ENCRYPT('x')), COLLATION(SHA1('x'));
	+-------------------------+----------------------+
	| COLLATION(ENCRYPT('x')) | COLLATION(SHA1('x')) |
	+-------------------------+----------------------+
	| binary                  | binary               |
	+-------------------------+----------------------+
为了检查字符串值的顺序，函数[WEIGHT\_STRING()][WEIGHT_STRING]可能会很有帮助。请见章节[12.5, “String Functions”][12.05.00]。

[char]:../Chapter_11/11.04.01_The_CHAR_and_VARCHAR_Types.md
[varchar]:../Chapter_11/11.04.01_The_CHAR_and_VARCHAR_Types.md
[text]:../Chapter_11/11.04.03_The_BLOB_and_TEXT_Types.md
[binary]:../Chapter_11/11.04.02_The_BINARY_and_VARBINARY_Types.md
[varbinary]:../Chapter_11/11.04.02_The_BINARY_and_VARBINARY_Types.md
[blob]:../Chapter_11/11.04.03_The_BLOB_and_TEXT_Types.md
[13.01.17]:../Chapter_13/13.01.07_ALTER_TABLE_Syntax.md
[COLLATION]:../Chapter_12/12.14.00_Information_Functions.md#function_collation
[VERSION]:../Chapter_12/12.14.00_Information_Functions.md#function_version
[WEIGHT_STRING]:../Chapter_12/12.05.00_String_Functions.md#function_weight-string
[12.05.00]:../Chapter_12/12.05.00_String_Functions.md


###C.5.5.2. 使用DATE列的问题

[DATE][]值的格式是'YYYY-MM-DD'。按照标准的SQL，不允许其他格式。在[UPDATE][]表达式和[SELECT][]语句的WHERE子句中应使用该格式。例如：

	SELECT * FROM t1 WHERE date >= '2003-05-05';
为了方便，如果日期是在数值环境下使用的，MySQL会自动将日期转换为数值（反之亦然）。在更新或者WHERE子句中，date值与[DATE][]、[DATETIME][]或[TIMESTAMP][]列比较时，MySQL允许“宽松的”字符串形式。“宽松”格式表示，任何标点字符均能用作各部分之间的分隔符。例如，'2004-08-15'和'2004#08#15'是等同的。MySQL还能转换不含任何分隔符的字符串（如'20040815'），前体是它必须是有意义的日期。

使用<、<=、=、>=、>或BETWEEN操作符将[DATE][]、[TIME][]、[DATETIME][]或[TIMESTAMP][]与常量字符串进行比较时，MySQL通常会将字符串转换为内部长整数，以便进行快速比较（同样也是为了能更加“宽松的”进行字符串检查）。然而，该转换也有下述例外：

比较两列时

将[DATE][]、[TIME][]、[DATETIME][]或[TIMESTAMP][]列与表达式进行比较时

当你使用其他比较方法时，例如，IN或者[STRCMP()][STRCMP]。

对于这些例外情形，会将对象转换为字符串并执行字符串比较，采用该方式进行比较。

出于安全考虑，假定按照字符串比较字符串，如果你要比较临时值和字符串，你应该使用恰当的字符串函数。

对于特殊日期“zero”，能够以'0000-00-00'形式保存和检索。在Connector/ODBC中使用'0000-00-00'日期时，该日期将被自动转换为NULL，这是因为ODBC不能处理这类日期。

由于MySQL能够执行前面所介绍的转换，下述语句均能正常工作（假设idate为[DATE][]类型）：

	INSERT INTO t1 (idate) VALUES (19970505);
	INSERT INTO t1 (idate) VALUES ('19970505');
	INSERT INTO t1 (idate) VALUES ('97-05-05');
	INSERT INTO t1 (idate) VALUES ('1997.05.05');
	INSERT INTO t1 (idate) VALUES ('1997 05 05');
	INSERT INTO t1 (idate) VALUES ('0000-00-00');
	
	SELECT idate FROM t1 WHERE idate >= '1997-05-05';
	SELECT idate FROM t1 WHERE idate >= 19970505;
	SELECT MOD(idate,100) FROM t1 WHERE idate >= 19970505;
	SELECT idate FROM t1 WHERE idate >= '19970505';

然而，下述语句却不能正常执行：

	SELECT idate FROM t1 WHERE STRCMP(idate,'20030505')=0;
STRCMP()是一种字符串函数，它能将idate转换为'YYYY-MM-DD'格式的字符串，并执行字符串比较。它不能将'20030505'转换为日期'2003-05-05'并进行日期比较。

如果你正在使用SQL模式（[ALLOW\_INVALID\_DATES][ALLOW_INVALID_DATES]），MySQL允许以仅执行给定的有限检查方式保存日期：MySQL仅保证天位于1～31的范围内，月位于1～12的范围内。这样就使得MySQL很适合于Web应用程序，其中，你能获得三个不同字段中的年、月、日值，也能准确保存用户插入的值（无日期验证）。

MySQL允许你存储月和天为零的日期。如果你打算将生日保存在[DATE][]列而且仅知道部分日期，它十分方便。如果让MySQL不允许日期部分为零，只需要启动SQL模式（[NO\_ZERO\_IN\_DATE][NO_ZERO_IN_DATE]）。

MySQL允许你将全零值（'0000-00-00'）作为“假设日期”进行存储。在某些情况下，这样做比使用NULL值更加方便。如果无法将日期转换为任何合理值，MySQL将'0000-00-00'存储到日期列中。如果想让MySQL不允许存储'0000-00-00'，请启用SQL模式（[NO\_ZERO\_DATE][NO_ZERO_DATE]）。

如果你希望MySQL检查所有日期并仅接受合法日期（除非由IGNORE覆盖），应将系统变量（[sql\_mode][sql_mode]）设置为"NO\_ZERO\_IN\_DATE,NO\_ZERO\_DATE"。


[date]:../Chapter_11/11.03.01_The_DATE_DATETIME_and_TIMESTAMP_Types.md
[update]:../Chapter_13/13.02.11_UPDATE_Syntax.md
[select]:../Chapter_13/13.02.09_SELECT_Syntax.md
[TIME]:../Chapter_11/11.03.02_The_TIME_Type.md
[DATETIME]:../Chapter_11/11.03.01_The_DATE_DATETIME_and_TIMESTAMP_Types.md
[TIMESTAMP]:../Chapter_11/11.03.01_The_DATE_DATETIME_and_TIMESTAMP_Types.md
[STRCMP]:../Chapter_12/12.05.01_String_Comparison_Functions.md
[ALLOW_INVALID_DATES]:../Chapter_05/05.01.07_Server_SQL_Modes.md#sqlmode_allow_invalid_dates
[NO_ZERO_IN_DATE]:../Chapter_05/05.01.07_Server_SQL_Modes.md#sqlmode_no_zero_in_date
[NO_ZERO_DATE]:../Chapter_05/05.01.07_Server_SQL_Modes.md#sqlmode_no_zero_date
[sql_mode]:../Chapter_05/05.01.07_Server_SQL_Modes.md


###C.5.5.3. 与NULL值有关的问题
对于SQL的新手，NULL值的概念常常会造成混淆，他们常认为NULL是与空字符串''相同的事。情况并非如此。例如，下述语句是完全不同的：

	mysql> INSERT INTO my_table (phone) VALUES (NULL);
	mysql> INSERT INTO my_table (phone) VALUES ('');
这两条语句均会将值插入phone（电话）列，但第1条语句插入的是NULL值，第2条语句插入的是空字符串。第1种情况的含义可被解释为“电话号码未知”，而第2种情况的含义可被解释为“该人员没有电话，因此没有电话号码”。

为了进行NULL处理，可使用[IS NULL][]和[IS NOT NULL][]操作符以及[IFNULL()][IFNULL]函数。

在SQL中，NULL值与任何其它值的比较（即使是NULL）永远不会为“真”。包含NULL的表达式总是会返回NULL值，除非在关于操作符的文档中以及表达式的函数中作了其他规定。下述示例中的所有列均返回NULL：

	mysql> SELECT NULL, 1+NULL, CONCAT('Invisible',NULL);
如果打算搜索列值为NULL的列，不能使用expr = NULL测试。下述语句不返回任何行，这是因为，对于任何表达式，expr = NULL永远不为“真”：

	mysql> SELECT * FROM my_table WHERE phone = NULL;
要想查找NULL值，必须使用[IS NULL][]测试。在下面的语句中，介绍了查找NULL电话号码和空电话号码的方式：

	mysql> SELECT * FROM my_table WHERE phone IS NULL;
	mysql> SELECT * FROM my_table WHERE phone = '';
更多信息和示例，请见章节[3.3.4.6，“Working with NULL Values”][03.03.04.06]。

如果你正在使用MyISAM、InnoDB、BDB、或MEMORY存储引擎，能够在可能具有NULL值的列上增加一条索引。否则，必须声明索引列为NOT NULL，而且不能将NULL插入到列中。

用[LOAD DATA INFILE][]读取数据时，对于空的或丢失的列，将用''更新它们。如果希望在列中添加NULL值，应在数据文件中使用\N代替对应的列值。在某些情况下，也可以使用文字性单词“NULL”来代替。请见章节[13.2.6，“LOAD DATA INFILE Syntax”][13.02.06]。

使用DISTINCT、GROUP BY或ORDER BY时，所有NULL值将被视为等同的。

使用ORDER BY时，首先将显示NULL值，如果指定了DESC按降序排列，NULL值将最后显示。

对于聚合（累计）函数，如[COUNT()][COUNT]、[MIN()][MIN]和[SUM()][SUM]，将忽略NULL值。对此的例外是[COUNT(*)][COUNT]，它将计数行而不是单独的列值。例如，下述语句产生两个计数。首先计数表中的行数，其次计数age列中的非NULL值数目：

	mysql> SELECT COUNT(*), COUNT(age) FROM person;
对于某些列类型，MySQL将对NULL值进行特殊处理。如果将NULL插入[TIMESTAMP][]列，将插入当前日期和时间。如果将NULL插入具有AUTO_INCREMENT属性的整数列，将插入序列中的下一个编号。
[is null]:../Chapter_12/12.03.02_Comparison_Functions_and_Operators.md#operator_is-null
[is not null]:../Chapter_12/12.03.02_Comparison_Functions_and_Operators.md#operator_is-not-null
[ifnull]:../Chapter_12/12.03.02_Comparison_Functions_and_Operators.md#operator_ifnull
[03.03.04.06]:../Chapter_03/03.03.04_Retrieving_Information_from_a_Table.md#03.03.04.06
[LOAD DATA INFILE]:../Chapter_13/13.02.06_LOAD_DATA_INFILE_Syntax.md
[13.02.06]:../Chapter_13/13.02.06_LOAD_DATA_INFILE_Syntax.md
[COUNT]:../Chapter_12/12.17.01_GROUP_BY_Aggregate_Functions.md#function_count
[MIN]:../Chapter_12/12.17.01_GROUP_BY_Aggregate_Functions.md#function_min
[SUM]:../Chapter_12/12.17.01_GROUP_BY_Aggregate_Functions.md#function_sum


###C.5.5.4. 与列别名有关的问题

在查询的选择列表中可以使用alias给列添加一个不同的名字。你可以在GROUP BY、ORDER BY或者HAVIING子句中使用别名来引用对应的列：

	SELECT SQRT(a*b) AS root FROM tbl_name GROUP BY root HAVING root > 0;
	SELECT id, COUNT(*) AS cnt FROM tbl_name GROUP BY id HAVING cnt > 0;
	SELECT id AS 'Customer identity' FROM tbl_name;
标准SQL不允许在WHERE子句中引用列别名。这是因为，执行WHERE代码时，可能尚未确定列值。例如，下述查询是非法的

	SELECT id, COUNT(*) AS cnt FROM tbl_name WHERE cnt > 0 GROUP BY id;
WHERE语句决定哪些行应被包含在GROUP BY子句中，但WHERE子句引用了列的别名，而这个列的值在被查询到之后才是已知的，并用于GROUP BY子句的分组。

在查询的选择列表中，引用列的别名可以使用反引号（`）、单引号（'）引起来：

	SELECT 1 AS `one`, 2 AS 'two';

在语句的其他地方（即，非column列表出）引用到别名的时候如果要对别名加引用标识符则必须使用反引号，否则别名会被当普通字符串文字处理。例如，下述语句中在列id上做分组处理，使用别名`a`来引用id列：

	SELECT id AS 'a', COUNT(*) AS cnt FROM tbl_name GROUP BY `a`;
但是下面这个语句使用字符串'a'进行分组处理就跟预期的不一样：

	SELECT id AS 'a', COUNT(*) AS cnt FROM tbl_name GROUP BY 'a';


###C.5.5.5. 非事务表回滚失败
当执行回滚（[ROLLBACK][]）时，如果收到下述消息，表示事务中使用的1个或多个不支持事务的表：

警告：不能回滚非事务性表的变化。
这些非事务性表不受[ROLLBACK][]语句的影响。

如果在事务中不小心混合了事务性表和非事务性表，导致该消息的最可能原因是，你认为本应是事务性的表实际上不是。如你试图使用**[mysqld][]**服务器不支持的事务性存储引擎（或使用启动选项禁止了它）创建表，就可能出现该情况。如果**[mysqld][]**不支持存储引擎，它将以MyISAM表创建表，这是非事务性表。

可使用下述语句之一检查表的存储引擎：

	SHOW TABLE STATUS LIKE 'tbl_name';
	SHOW CREATE TABLE tbl_name;
请见章节[13.7.5.37，“SHOW TABLE STATUS Syntax”][13.07.05.37]，和[13.7.5.12，“SHOW CREATE TABLE Syntax”][13.07.05.12]。

使用下述语句，可检查**[mysqld][]**服务器支持的存储引擎：

	SHOW ENGINES;
更详细的信息，请见章节[13.7.5.17，“SHOW ENGINES Syntax”][13.07.05.17]。

[rollback]:../Chapter_13/13.03.01_START_TRANSACTION_COMMIT_and_ROLLBACK_Syntax.md
[13.07.05.37]:../Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.37
[13.07.05.12]:../Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.12
[13.07.05.17]:../Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.17


###C.5.5.6. 从相关表删除行

如果针对related\_table的[DELETE][]语句的总长度超过1MB（系统变量[max\_allowed\_packet][max_allowed_packet]的默认值），应将其切分为多个小的[DELETE][]语句来执行。如果related\_column是索引列，为每条语句指定100～1000个related\_column值，或许能获得更快的[DELETE][]速度。如果related\_column不是索引列，速度与IN子句中的参数数量无关。

[delete]:../Chapter_13/13.02.02_DELETE_Syntax.md
[max_allowed_packet]:../Chapter_05/05.01.04_Server_System_Variables.md#max_allowed_packet


###C.5.5.7. 解决与没有不匹配行有关的问题

如果有使用了很多表的复杂查询，但未返回任何行，应采用下述步骤来找出那里出错：

1. 用[EXPLAIN][]测试查询，以检查是否某些地方有很明显的错误。请见章节[13.8.2，“EXPLAIN Syntax”][13.08.02]。

2. 仅选择在WHERE子句中使用的列

3. 在查询中1次删除1个表，直到返回了某些行。如果表很大，一个不错的选择是在查询中使用LIMIT 10。

4. 针对表发出的SELECT列应该有匹配的行，但匹配的行被最近的查询删除了。

5. 如果将[FLOAT][]或[DOUBLE][]列与具有小数的数值进行比较，不能使用等式(=)比较。在大多数计算机语言中，该问题很常见，这是因为，并非所有的浮点值均能以准确的精度保存。在某些情况下，将[FLOAT][]更改为[DOUBLE][]可更正该问题。请见章节[C.5.5.8，“Problems with Floating-Point Values”][C.05.05.08]。

6. 如果你还是不能找出哪里出了问题，那么就做个小型的测试，执行 mysql test < query.sql可能会显示出你的问题。可以使用命令（[mysqldump --quick db\_name tbl\_name\_1 ... tbl\_name\_n \> query.sql][mysqldump]）将表导出为一个测试文件。在编辑器中打开这个文件，删除一些insert语句（只需要保留足够测试的内容即可），并将你的[SELECT][]语句添加到文件的末尾。

通过执行如下命令，验证测试文件是否显示了问题：

	shell> mysqladmin create test2
	shell> mysql test2 < query.sql
在报告缺陷的时候附带上这个测试文件，在文件可以对用法说明进行归档总结。请见章节[1.7，“How to Report Bugs or Problems”][01.07.00]。

[EXPLAIN]:../Chapter_13/13.08.02_EXPLAIN_Syntax.md
[13.08.02]:../Chapter_13/13.08.02_EXPLAIN_Syntax.md
[SELECT]:..../Chapter_13/13.02.09_SELECT_Syntax.md
[FLOAT]:../Chapter_11/11.02.03_Floating-Point_Types_Approximate_Value_FLOAT_DOUBLE.md
[DOUBLE]:../Chapter_11/11.02.03_Floating-Point_Types_Approximate_Value_FLOAT_DOUBLE.md
[C.05.05.08]:./C.05.05_Query-Related_Issues.md#C.05.05.08
[mysqldump]:../Chapter_04/04.05.04_mysqldump_A_Database_Backup_Program.md
[01.07.00]:../Chapter_01/01.07.00_How_to_Report_Bugs_or_Problems.md


###C.5.5.8. 与浮点值有关的问题
浮点数有时会造成混乱，因为他们存储的是近似值，而不是精确值。写在SQL语句中的浮点值也许和它本质上的值是不一样的。尝试将浮点值当作精确值进行比较可能会导致问题。他们也依赖于平台和具体的实现。 [FLOAT][]和[DOUBLE][]数据类型都受到这些问题影响。对于小数（[DECIMAL][]）列，MySQL执行操作精度为小数点后65位数字，这应该解决最常见的不准确问题。

下面这个例子用DOUBLE类型来演示浮点数是如何计算并受到误差影响。

	mysql> CREATE TABLE t1 (i INT, d1 DOUBLE, d2 DOUBLE);
	mysql> INSERT INTO t1 VALUES (1, 101.40, 21.40), (1, -80.00, 0.00),
	    -> (2, 0.00, 0.00), (2, -13.20, 0.00), (2, 59.60, 46.40),
	    -> (2, 30.40, 30.40), (3, 37.00, 7.40), (3, -29.60, 0.00),
	    -> (4, 60.00, 15.40), (4, -10.60, 0.00), (4, -34.00, 0.00),
	    -> (5, 33.00, 0.00), (5, -25.80, 0.00), (5, 0.00, 7.20),
	    -> (6, 0.00, 0.00), (6, -51.40, 0.00);
	
	mysql> SELECT i, SUM(d1) AS a, SUM(d2) AS b
	    -> FROM t1 GROUP BY i HAVING a <> b;
	@@@@@@（描述与测试不符合待进步验证：本句执行的结果和mysql5.6.13不一致
	
	+------+-------+------+
	| i    | a     | b    |
	+------+-------+------+
	|    1 |  21.4 | 21.4 |
	|    2 |  76.8 | 76.8 |
	|    3 |   7.4 |  7.4 |
	|    4 |  15.4 | 15.4 |
	|    5 |   7.2 |  7.2 |
	|    6 | -51.4 |    0 |
	+------+-------+------+
这个结果是正确的。尽管前5条记录看上去不能满足对比（a和b的值看上去没有什么不同），但它们能进行比较，这是因为显示的数值间的差异在十分位左右，具体情况取决于某些因此，例如计算机体系结构或者编译版本或者优化水平。例如，不同的CPU求的浮点数的值可能不同。

如果列d1和列d2都被定义为[DECIMAL][]而不是[DOUBLE][]，上面查询（[SELECT][]）的结果可能就只包含列表中的最后一行。

做浮点数比较运算的正确方法是首选确定一个可以容忍两个值不一样的限度，然后针对可接受值的限度做比较运算。例如，如果我们赞同两个浮点数在万分之一的精度内是相同的，那么我们就认为他们是一样的，这个运算就应该写成两个值的差值大于容忍值：

	mysql> SELECT i, SUM(d1) AS a, SUM(d2) AS b FROM t1
	    -> GROUP BY i HAVING ABS(a - b) > 0.0001;
	+------+-------+------+
	| i    | a     | b    |
	+------+-------+------+
	|    6 | -51.4 |    0 |
	+------+-------+------+
	1 row in set (0.00 sec)
相反的，为了得到两个相等的数值，两个值的差值小于等于容忍值：

	mysql> SELECT i, SUM(d1) AS a, SUM(d2) AS b FROM t1
	    -> GROUP BY i HAVING ABS(a - b) <= 0.0001;
	+------+------+------+
	| i    | a    | b    |
	+------+------+------+
	|    1 | 21.4 | 21.4 |
	|    2 | 76.8 | 76.8 |
	|    3 |  7.4 |  7.4 |
	|    4 | 15.4 | 15.4 |
	|    5 |  7.2 |  7.2 |
	+------+------+------+
	5 rows in set (0.03 sec)
浮点值受平台或具体实现影响。
假设你执行了如下语句：
Suppose that you execute the following statements:

	CREATE TABLE t1(c1 FLOAT(53,0), c2 FLOAT(53,0));
	INSERT INTO t1 VALUES('1e+52','-1e+52');
	SELECT * FROM t1;
在某些平台上，SELECT语句返回inf和-inf。在其他平台上，可能返回0和-0。

上述问题的一个隐含意思是如果你尝试创建一个从服务器，并且是使用[mysqldump][]来备份主服务器上的表数据，然后到从服务器上重新加载数据，此时如果表中包含浮点列，那么两个主机上的浮点数可能是不同的。

[DECIMAL]:../Chapter_11/11.02.02_Fixed-Point_Types_Exact_Value_DECIMAL_NUMERIC.md