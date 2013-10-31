## C.01.00.错误信息的来源

MySQL错误信息的来源如下：

* 每个SQL语句执行后的结果中都包含一个错误代码、一个SQLSTATE值、一个错误消息，详见章节[C.02.00.错误值类型][C.02.00]。 这些错误内容来源于服务端；详见章节[C.03.00.服务器错误代码和消息][C.03.00]。
* 如果客户端发生错误，通常是客户端和服务端的通信导致的问题。详见章节[C.04.00.客户端错误代码和消息][C.04.00].
* 我们可以用 [SHOW WARNINGS][] 和 [SHOW ERRORS][] 来查看SQL语句执行的警告和错误信息。系统变量（[warning\_count][warning_count]）表示错误、警告和注意事项的总个数。系统变量（[error\_count][error_count]）只表示错误的数量，不包括警告和注意事项。
* [GET DIAGNOSTICS][] 语句可用于检查诊断的详细信息。详见章节[13.06.07.03.GET DIAGNOSTICS 语法][13.06.07.03]。
* [SHOW SLAVE STATUS][] 语句输出发生在SLAVE端的复制错误信息。
* [SHOW ENGINE INNODB STATUS][] 语句输出的内容包括了最近发生的外键错误信息（如果用 [CREATE TABLE][] 语句创建 [InnoDB][] 表失败）。
* perror程序提供了命令行查询错误码的查询。详见章节[04.08.01.perror -- 解释错误码][04.08.01]

在本附录中，接下来会介绍服务端和客户端的错误信息。想要查看InnoDB存储引擎相关的错误信息，请见章节[14.02.03.14.InnoDB错误处理][14.02.03.14]

[C.02.00]:./C.02.00_Types_of_Error_Values.md
[C.03.00]:./C.03.00_Server_Error_Codes_and_Messages.md
[C.04.00]:./C.04.00_Client_Error_Codes_and_Messages.md
[13.06.07.03]:../Chapter_13/13.06.07_Condition_Handling.md#13.06.07.03
[04.08.01]:../Chapter_04/04.08.01_perror_Explain_Error_Codes.md
[14.02.03.14]:../Chapter_14/14.02.03_InnoDB_Concepts_and_Architecture.md#14.02.03.14
[SHOW WARNINGS]:../Chapter_13/Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.41
[SHOW ERRORS]:../Chapter_13/Chapter_13/13.07.05_SHOW_Syntax.md#13.7.5.18
[warning_count]:../Chapter_05/05.01.04_Server_System_Variables.md#warning_count
[error_count]:../Chapter_05/05.01.04_Server_System_Variables.md#error_count
[GET DIAGNOSTICS]:../Chapter_13/13.06.07_Condition_Handling.md#13.06.07.03
[SHOW SLAVE STATUS]:../Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.35
[SHOW ENGINE INNODB STATUS]:../Chapter_13/13.07.05_SHOW_Syntax.md#13.07.05.16
[CREATE TABLE]:../Chapter_13/13.01.17_CREATE_TABLE_Syntax.md
[InnoDB]:../Chapter_14/14.02.00_the_innodb_storage_engine.md