## C.2.错误值类型

当MySQL发生错误时，服务端会返回两种类型的错误值：

* MySQL指定的数字类型的错误代码，这个错误码在其他数据库系统中不通用。
* 5位字符长度的SQLSTATE值（例如，‘42S02’）。这些值在ANSI SQL、ODBC和其他某些标准中也通用。

消息字符串为MySQL的错误提供了一种可行的文字描述。
当MySQL错误发生时，可以通过C API函数获取错误代码、SQLSTATE值和消息字符串：

* MySQL 错误码：调用 [mysql\_errno()][mysql_errno()]
* SQLSTATE 值：调用 [mysql\_sqlstate()][mysql_sqlstate()]
* 错误消息：调用 [mysql\_error()][mysql_error()]

对于预处理语句来说，对应的错误函数是 [mysql\_stmt\_errno()][mysql_stmt_errno()] 、[mysql\_stmt\_sqlstate()][mysql_stmt_sqlstate()] 以及 [mysql\_stmt\_error()][mysql_stmt_error()] 。所有的错误函数详细信息请见 [22.08.MySQL C API][22.08.00]。

我们可以使用 [mysql\_warning\_count()][mysql_warning_count()]来获取当前会话中前一个语句执行的错误、警告和注意事项的总数。详见 [22.08.07.73.mysql\_warning\_count()][22.08.07.73] 。

SQLSTATE值的前两位字符代表了错误的类型：

* 类型 = ‘00’代表成功。
* 类型 = ‘01’代表警告。
* 类型 = ‘02’代表“not found”，这个情况发生在当获取游标内容并且指针已经到数据集的末尾时。同时也发生在SELECT ... INTO var_list语句没有找到数据行时。
* 类型 > ‘02’代表异常。

[mysql_errno()]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.14
[mysql_sqlstate()]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.8.7.67
[mysql_error()]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.8.7.15
[mysql_stmt_errno()]:../Chapter_22/22.08.11_C_API_Prepared_Statement_Function_Descriptions.md#22.8.11.8
[mysql_stmt_sqlstate()]:../Chapter_22/22.08.11_C_API_Prepared_Statement_Function_Descriptions.md#22.8.11.27
[mysql_stmt_error()]:../Chapter_22/22.08.11_C_API_Prepared_Statement_Function_Descriptions.md#22.8.11.9
[22.08.00]:../Chapter_22/22.08.00_MySQL_C_API.md
[mysql_warning_count()]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.73
[22.08.07.73]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.73