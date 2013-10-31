##C.4.0.客户端错误代码和消息

客户端错误信息来自下述源文件：

* 圆括号中的错误值和符号与include/errmsg.h MySQL源文件中的定义对应

* 消息值与libmysql/errmsg.c文件中列出的错误消息对应。%d和%s分别代表数值和字符串，显示时，它们将被消息值取代  
  由于更新很频繁，这些文件中可能包含这里未列出的额外错误消息

* <a name="CR_UNKNOWN_ERROR"></a>错误：`2000`（[CR\_UNKNOWN\_ERROR][CR_UNKNOWN_ERROR]）  
  消息：未知MySQL错误

* <a name="CR_SOCKET_CREATE_ERROR"></a>错误：`2001`（[CR\_SOCKET\_CREATE\_ERROR][CR_SOCKET_CREATE_ERROR]）  
  消息：无法创建UNIX套接字（%d）

* <a name="CR_CONNECTION_ERROR"></a>错误：`2002`（[CR\_CONNECTION\_ERROR][CR_CONNECTION_ERROR]）  
  消息：无法通过套接字'%s'（%d）连接到本地MySQL服务器

* <a name="CR_CONN_HOST_ERROR"></a>错误：`2003`（[CR\_CONN\_HOST\_ERROR][CR_CONN_HOST_ERROR]）  
  消息：无法连接到'%s' （%d）上的MySQL服务器

* <a name="CR_IPSOCK_ERROR"></a>错误：`2004`（[CR\_IPSOCK\_ERROR][CR_IPSOCK_ERROR]）  
  消息：无法创建TCP/IP套接字（%d）  

* <a name="CR_UNKNOWN_HOST"></a>错误：`2005`（[CR\_UNKNOWN\_HOST][CR_UNKNOWN_HOST]）  
  消息：未知的MySQL服务器主机'%s' （%d）  

* <a name="CR_SERVER_GONE_ERROR"></a>错误：`2006`（[CR\_SERVER\_GONE\_ERROR][CR_SERVER_GONE_ERROR]）  
  消息：MySQL服务器不可用  

* <a name="CR_VERSION_ERROR"></a>错误：`2007`（[CR\_VERSION\_ERROR][CR_VERSION_ERROR]）  
  消息：协议不匹配，服务器版本 = %d，客户端版本 = %d  

* <a name="CR_OUT_OF_MEMORY"></a>错误：`2008`（[CR\_OUT\_OF\_MEMORY][CR_OUT_OF_MEMORY]）  
  消息：MySQL客户端内存溢出  

* <a name="CR_WRONG_HOST_INFO"></a>错误：`2009`（[CR\_WRONG\_HOST\_INFO][CR_WRONG_HOST_INFO]）  
  消息：错误的主机信息  

* <a name="CR_LOCALHOST_CONNECTION"></a>错误：`2010`（[CR\_LOCALHOST\_CONNECTION][CR_LOCALHOST_CONNECTION]）  
  消息：通过UNIX套接字连接本地主机  

* <a name="CR_TCP_CONNECTION"></a>错误：`2011`（[CR\_TCP\_CONNECTION][CR_TCP_CONNECTION]）  
  消息：：%s，通过TCP/IP  

* <a name="CR_SERVER_HANDSHAKE_ERR"></a>错误：`2012`（[CR\_SERVER\_HANDSHAKE\_ERR][CR_SERVER_HANDSHAKE_ERR]）  
  消息：服务器握手过程中出错  

* <a name="CR_SERVER_LOST"></a>错误：`2013`（[CR\_SERVER\_LOST][CR_SERVER_LOST]）  
  消息：查询过程中丢失了与MySQL服务器的连接  

* <a name="CR_COMMANDS_OUT_OF_SYNC"></a>错误：`2014`（[CR\_COMMANDS\_OUT\_OF\_SYNC][CR_COMMANDS_OUT_OF_SYNC]）  
  消息：命令不同步，你现在不能运行该命令  

* <a name="CR_NAMEDPIPE_CONNECTION"></a>错误：`2015`（[CR\_NAMEDPIPE\_CONNECTION][CR_NAMEDPIPE_CONNECTION]）  
  消息：命名管道：%s  

* <a name="CR_NAMEDPIPEWAIT_ERROR"></a>错误：`2016`（[CR\_NAMEDPIPEWAIT\_ERROR][CR_NAMEDPIPEWAIT_ERROR]）  
  消息：无法等待命名管道，主机：%s；管道：%s （%lu）  

* <a name="CR_NAMEDPIPEOPEN_ERROR"></a>错误：`2017`（[CR\_NAMEDPIPEOPEN\_ERROR][CR_NAMEDPIPEOPEN_ERROR]）  
  消息：无法打开命名管道，主机：%s；管道：%s （%lu）  

* <a name="CR_NAMEDPIPESETSTATE_ERROR"></a>错误：`2018`（[CR\_NAMEDPIPESETSTATE\_ERROR][CR_NAMEDPIPESETSTATE_ERROR]）  
  消息：无法设置命名管道的状态，主机：%s；管道：%s （%lu）  

* <a name="CR_CANT_READ_CHARSET"></a>错误：`2019`（[CR\_CANT\_READ\_CHARSET][CR_CANT_READ_CHARSET]）  
  消息：无法初始化字符集%s （路径：%s） 

* <a name="CR_NET_PACKET_TOO_LARGE"></a>错误：`2020`（[CR\_NET\_PACKET\_TOO\_LARGE][CR_NET_PACKET_TOO_LARGE]）  
  消息：获得的信息包大于'max\_allowed\_packet'字节  

* <a name="CR_EMBEDDED_CONNECTION"></a>错误：`2021`（[CR\_EMBEDDED\_CONNECTION][CR_EMBEDDED_CONNECTION]）  
  消息：嵌入式服务器

* <a name="CR_PROBE_SLAVE_STATUS"></a>错误：`2022`（[CR\_PROBE\_SLAVE\_STATUS][CR_PROBE_SLAVE_STATUS]）  
  消息：SHOW SLAVE STATUS出错：

* <a name="CR_PROBE_SLAVE_HOSTS"></a>错误：`2023`（[CR\_PROBE\_SLAVE\_HOSTS][CR_PROBE_SLAVE_HOSTS]）  
  消息：SHOW SLAVE HOSTS出错：  

* <a name="CR_PROBE_SLAVE_CONNECT"></a>错误：`2024`（[CR\_PROBE\_SLAVE\_CONNECT][CR_PROBE_SLAVE_CONNECT]）  
  消息：连接到从服务器时出错：  

* <a name="CR_PROBE_MASTER_CONNECT"></a>错误：`2025`（[CR\_PROBE\_MASTER\_CONNECT][CR_PROBE_MASTER_CONNECT]）  
  消息：连接到主服务器时出错：  

* <a name="CR_SSL_CONNECTION_ERROR"></a>错误：`2026`（[CR\_SSL\_CONNECTION\_ERROR][CR_SSL_CONNECTION_ERROR]）  
  消息：SSL连接错误：%s

* <a name="CR_MALFORMED_PACKET"></a>错误：`2027`（[CR\_MALFORMED\_PACKET][CR_MALFORMED_PACKET]）  
  消息：残缺信息包  

* <a name="CR_WRONG_LICENSE"></a>错误：`2028`（[CR\_WRONG\_LICENSE][CR_WRONG_LICENSE]）  
  消息：该客户端库仅授权给具有'%s'许可的MySQL服务器使用  

* <a name="CR_NULL_POINTER"></a>错误：`2029`（[CR\_NULL\_POINTER][CR_NULL_POINTER]）  
  消息：空指针的无效使用

* <a name="CR_NO_PREPARE_STMT"></a>错误：`2030`（[CR\_NO\_PREPARE\_STMT][CR_NO_PREPARE_STMT]）  
  消息：语句未准备好  

* <a name="CR_PARAMS_NOT_BOUND"></a>错误：`2031`（[CR\_PARAMS\_NOT\_BOUND][CR_PARAMS_NOT_BOUND]）  
  消息：没有为预处理语句中的参数提供数据  

* <a name="CR_DATA_TRUNCATED"></a>错误：`2032`（[CR\_DATA\_TRUNCATED][CR_DATA_TRUNCATED]）  
  消息：数据截断  

* <a name="CR_NO_PARAMETERS_EXISTS"></a>错误：`2033`（[CR\_NO\_PARAMETERS\_EXISTS][CR_NO_PARAMETERS_EXISTS]）  
  消息：语句中不存在任何参数  

* <a name="CR_INVALID_PARAMETER_NO"></a>错误：`2034`（[CR\_INVALID\_PARAMETER\_NO][CR_INVALID_PARAMETER_NO]）  
  消息：无效的参数编号  

* <a name="CR_INVALID_BUFFER_USE"></a>错误：`2035`（[CR\_INVALID\_BUFFER\_USE][CR_INVALID_BUFFER_USE]）  
  消息：不能为非字符串／非二进制数据类型发送长数据（参数：%d）

* <a name="CR_UNSUPPORTED_PARAM_TYPE"></a>错误：`2036`（[CR\_UNSUPPORTED\_PARAM\_TYPE][CR_UNSUPPORTED_PARAM_TYPE]）  
  消息：正使用不支持的缓冲区类型：%d （参数：%d）  

* <a name="CR_SHARED_MEMORY_CONNECTION"></a>错误：`2037`（[CR\_SHARED\_MEMORY\_CONNECTION][CR_SHARED_MEMORY_CONNECTION]）  
  消息：共享内存：%s  

* <a name="CR_SHARED_MEMORY_CONNECT_REQUEST_ERROR"></a>错误：`2038`（[CR\_SHARED\_MEMORY\_CONNECT\_REQUEST\_ERROR][CR_SHARED_MEMORY_CONNECT_REQUEST_ERROR]）  
  消息：无法打开共享内存；客户端不能创建请求事件（%lu）  

* <a name="CR_SHARED_MEMORY_CONNECT_ANSWER_ERROR"></a>错误：`2039`（[CR\_SHARED\_MEMORY\_CONNECT\_ANSWER\_ERROR][CR_SHARED_MEMORY_CONNECT_ANSWER_ERROR]）  
  消息：无法打开共享内存；未收到服务器的应答事件（%lu）   

* <a name="CR_SHARED_MEMORY_CONNECT_FILE_MAP_ERROR"></a>错误：`2040`（[CR\_SHARED\_MEMORY\_CONNECT\_FILE\_MAP\_ERROR][CR_SHARED_MEMORY_CONNECT_FILE_MAP_ERROR]）  
  消息：无法打开共享内存；服务器不能分配文件映射（%lu）   

* <a name="CR_SHARED_MEMORY_CONNECT_MAP_ERROR"></a>错误：`2041`（[CR\_SHARED\_MEMORY\_CONNECT\_MAP\_ERROR][CR_SHARED_MEMORY_CONNECT_MAP_ERROR]）  
  消息：无法打开共享内存；服务器不能获得文件映射的指针（%lu）  

* <a name="CR_SHARED_MEMORY_FILE_MAP_ERROR"></a>错误：`2042`（[CR\_SHARED\_MEMORY\_FILE\_MAP\_ERROR][CR_SHARED_MEMORY_FILE_MAP_ERROR]）  
  消息：无法打开共享内存；客户端不能分配文件映射（%lu）  

* <a name="CR_SHARED_MEMORY_MAP_ERROR"></a>错误：`2043`（[CR\_SHARED\_MEMORY\_MAP\_ERROR][CR_SHARED_MEMORY_MAP_ERROR]）  
  消息：无法打开共享内存；客户端不能获得文件映射的指针（%lu）  

* <a name="CR_SHARED_MEMORY_EVENT_ERROR"></a>错误：`2044`（[CR\_SHARED\_MEMORY\_EVENT\_ERROR][CR_SHARED_MEMORY_EVENT_ERROR]）  
  消息：无法打开共享内存；客户端不能创建%s事件（%lu）  

* <a name="CR_SHARED_MEMORY_CONNECT_ABANDONED_ERROR"></a>错误：`2045`（[CR\_SHARED\_MEMORY\_CONNECT\_ABANDONED\_ERROR][CR_SHARED_MEMORY_CONNECT_ABANDONED_ERROR]）  
  消息：无法打开共享内存；无来自服务器的应答（%lu）  

* <a name="CR_SHARED_MEMORY_CONNECT_SET_ERROR"></a>错误：`2046`（[CR\_SHARED\_MEMORY\_CONNECT\_SET\_ERROR][CR_SHARED_MEMORY_CONNECT_SET_ERROR]）  
  消息：无法打开共享内存；不能将请求事件发送到服务器（%lu）  

* <a name="CR_CONN_UNKNOW_PROTOCOL"></a>错误：`2047`（[CR\_CONN\_UNKNOW\_PROTOCOL][CR_CONN_UNKNOW_PROTOCOL]）  
  消息：错误或未知协议  

* <a name="CR_INVALID_CONN_HANDLE"></a>错误：`2048`（[CR\_INVALID\_CONN\_HANDLE][CR_INVALID_CONN_HANDLE]）  
  消息：无效的连接句柄  

* <a name="CR_SECURE_AUTH"></a>错误：`2049`（[CR\_SECURE\_AUTH][CR_SECURE_AUTH]）  
  消息：拒绝使用旧认证协议（早于4.1.1）的连接（客户端开启了'secure_auth'选项）  

* <a name="CR_FETCH_CANCELED"></a>错误：`2050`（[CR\_FETCH\_CANCELED][CR_FETCH_CANCELED]）  
  消息：行检索被mysql_stmt_close()调用取消  

* <a name="CR_NO_DATA"></a>错误：`2051`（[CR\_NO\_DATA][CR_NO_DATA]）  
  消息：在未事先获取行的情况下试图读取列  

* <a name="CR_NO_STMT_METADATA"></a>错误：`2052`（[CR\_NO\_STMT\_METADATA][CR_NO_STMT_METADATA]）  
  消息：预处理语句不含元数据  

* <a name="CR_NO_RESULT_SET"></a>错误：`2053`（[CR\_NO\_RESULT\_SET][CR_NO_RESULT_SET]）  
  消息：在没有与语句相关的结果集时试图读取行  

* <a name="CR_NOT_IMPLEMENTED"></a>错误：`2054`（[CR\_NOT\_IMPLEMENTED][CR_NOT_IMPLEMENTED]）  
  消息：该特性尚未实现  

* <a name="CR_SERVER_LOST_EXTENDED"></a>错误：`2055`（[CR\_SERVER\_LOST\_EXTENDED][CR_SERVER_LOST_EXTENDED]）  
  消息：丢失MySQL服务器连接 '%s'，系统错误：%d  

* <a name="CR_STMT_CLOSED"></a>错误：`2056`（[CR\_STMT\_CLOSED][CR_STMT_CLOSED]）  
  消息：由于%s()调用，语句间接地被关闭  

* <a name="CR_NEW_STMT_METADATA"></a>错误：`2057`（[CR\_NEW\_STMT\_METADATA][CR_NEW_STMT_METADATA]）  
  消息：结果集的列数与缓冲区（bound buffers）的不一致。必须重置语句，修改结果集的列数，然后重新执行语句

* <a name="CR_ALREADY_CONNECTED"></a>错误：`2058`（[CR\_ALREADY\_CONNECTED][CR_ALREADY_CONNECTED]）  
  消息：这个句柄已经被连接。请为每一个连接使用独立的句柄  

* <a name="CR_AUTH_PLUGIN_CANNOT_LOAD"></a>错误：`2059`（[CR\_AUTH\_PLUGIN\_CANNOT\_LOAD][CR_AUTH_PLUGIN_CANNOT_LOAD]）  
  消息：认证插件'%s'无发加载：%s  
错误（CR\_AUTH\_PLUGIN\_CANNOT\_LOAD）在MySQL5.6.1中有介绍  

* <a name="CR_DUPLICATE_CONNECTION_ATTR"></a>错误：`2060`（[CR\_DUPLICATE\_CONNECTION\_ATTR][CR_DUPLICATE_CONNECTION_ATTR]）  
  消息：已经有一个同名的属性存在  
错误（CR\_DUPLICATE\_CONNECTION\_ATTR）在MySQL5.6.6中有介绍  

* <a name="CR_AUTH_PLUGIN_ERR"></a>错误：`2061`（[CR\_AUTH\_PLUGIN\_ERR][CR_AUTH_PLUGIN_ERR]）  
  消息：认证插件'%s'报告错误：%s  
错误（CR\_AUTH\_PLUGIN\_ERR）在MySQL5.6.10中有介绍  

[CR_UNKNOWN_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_UNKNOWN_ERROR
[CR_SOCKET_CREATE_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SOCKET_CREATE_ERROR
[CR_CONNECTION_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_CONNECTION_ERROR
[CR_CONN_HOST_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_CONN_HOST_ERROR
[CR_IPSOCK_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_IPSOCK_ERROR
[CR_UNKNOWN_HOST]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_UNKNOWN_HOST
[CR_SERVER_GONE_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SERVER_GONE_ERROR
[CR_VERSION_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_VERSION_ERROR
[CR_OUT_OF_MEMORY]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_OUT_OF_MEMORY
[CR_WRONG_HOST_INFO]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_WRONG_HOST_INFO
[CR_LOCALHOST_CONNECTION]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_LOCALHOST_CONNECTION
[CR_TCP_CONNECTION]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_TCP_CONNECTION
[CR_SERVER_HANDSHAKE_ERR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SERVER_HANDSHAKE_ERR
[CR_SERVER_LOST]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SERVER_LOST
[CR_COMMANDS_OUT_OF_SYNC]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_COMMANDS_OUT_OF_SYNC
[CR_NAMEDPIPE_CONNECTION]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NAMEDPIPE_CONNECTION
[CR_NAMEDPIPEWAIT_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NAMEDPIPEWAIT_ERROR
[CR_NAMEDPIPEOPEN_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NAMEDPIPEOPEN_ERROR
[CR_NAMEDPIPESETSTATE_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NAMEDPIPESETSTATE_ERROR
[CR_CANT_READ_CHARSET]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_CANT_READ_CHARSET
[CR_NET_PACKET_TOO_LARGE]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NET_PACKET_TOO_LARGE
[CR_EMBEDDED_CONNECTION]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_EMBEDDED_CONNECTION
[CR_PROBE_SLAVE_STATUS]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_PROBE_SLAVE_STATUS
[CR_PROBE_SLAVE_HOSTS]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_PROBE_SLAVE_HOSTS
[CR_PROBE_SLAVE_CONNECT]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_PROBE_SLAVE_CONNECT
[CR_PROBE_MASTER_CONNECT]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_PROBE_MASTER_CONNECT
[CR_SSL_CONNECTION_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SSL_CONNECTION_ERROR
[CR_MALFORMED_PACKET]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_MALFORMED_PACKET
[CR_WRONG_LICENSE]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_WRONG_LICENSE
[CR_NULL_POINTER]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NULL_POINTER
[CR_NO_PREPARE_STMT]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NO_PREPARE_STMT
[CR_PARAMS_NOT_BOUND]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_PARAMS_NOT_BOUND
[CR_DATA_TRUNCATED]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_DATA_TRUNCATED
[CR_NO_PARAMETERS_EXISTS]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NO_PARAMETERS_EXISTS
[CR_INVALID_PARAMETER_NO]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_INVALID_PARAMETER_NO
[CR_INVALID_BUFFER_USE]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_INVALID_BUFFER_USE
[CR_UNSUPPORTED_PARAM_TYPE]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_UNSUPPORTED_PARAM_TYPE
[CR_SHARED_MEMORY_CONNECTION]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECTION
[CR_SHARED_MEMORY_CONNECT_REQUEST_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECT_REQUEST_ERROR
[CR_SHARED_MEMORY_CONNECT_ANSWER_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECT_ANSWER_ERROR
[CR_SHARED_MEMORY_CONNECT_FILE_MAP_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECT_FILE_MAP_ERROR
[CR_SHARED_MEMORY_CONNECT_MAP_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECT_MAP_ERROR
[CR_SHARED_MEMORY_FILE_MAP_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_FILE_MAP_ERROR
[CR_SHARED_MEMORY_MAP_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_MAP_ERROR
[CR_SHARED_MEMORY_EVENT_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_EVENT_ERROR
[CR_SHARED_MEMORY_CONNECT_ABANDONED_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECT_ABANDONED_ERROR
[CR_SHARED_MEMORY_CONNECT_SET_ERROR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SHARED_MEMORY_CONNECT_SET_ERROR
[CR_CONN_UNKNOW_PROTOCOL]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_CONN_UNKNOW_PROTOCOL
[CR_INVALID_CONN_HANDLE]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_INVALID_CONN_HANDLE
[CR_SECURE_AUTH]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SECURE_AUTH
[CR_FETCH_CANCELED]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_FETCH_CANCELED
[CR_NO_DATA]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NO_DATA
[CR_NO_STMT_METADATA]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NO_STMT_METADATA
[CR_NO_RESULT_SET]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NO_RESULT_SET
[CR_NOT_IMPLEMENTED]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NOT_IMPLEMENTED
[CR_SERVER_LOST_EXTENDED]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_SERVER_LOST_EXTENDED
[CR_STMT_CLOSED]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_STMT_CLOSED
[CR_NEW_STMT_METADATA]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_NEW_STMT_METADATA
[CR_ALREADY_CONNECTED]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_ALREADY_CONNECTED
[CR_AUTH_PLUGIN_CANNOT_LOAD]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_AUTH_PLUGIN_CANNOT_LOAD
[CR_DUPLICATE_CONNECTION_ATTR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_DUPLICATE_CONNECTION_ATTR
[CR_AUTH_PLUGIN_ERR]:./C.04.00_Client_Error_Codes_and_Messages.md#CR_AUTH_PLUGIN_ERR
