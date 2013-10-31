##C.05.02. 使用MySQL的常见错误

[C.05.02.01. 拒绝访问][C.05.02.01]  
[C.05.02.02. 无法连接到本地MySQL服务器][C.05.02.02]  
[C.05.02.03. 丢失MySQL服务器连接][C.05.02.03]  
[C.05.02.04. 客户端不支持认证协议][C.05.02.04]    
[C.05.02.05. 交互式密码输入验证失败][C.05.02.05]  
[C.05.02.06. 主机'host_name'被屏蔽][C.05.02.06]  
[C.05.02.07. 连接数过多][C.05.02.07]  
[C.05.02.08. 内存溢出][C.05.02.08]  
[C.05.02.09. 连接已断开][C.05.02.09]  
[C.05.02.10. 信息报过大][C.05.02.10]  
[C.05.02.11. 通信错误和连接中断][C.05.02.11]  
[C.05.02.12. 表已满][C.05.02.12]  
[C.05.02.13. 无法创建/写入文件][C.05.02.13]  
[C.05.02.14. 命令不同步][C.05.02.14]  
[C.05.02.15. 忽略用户][C.05.02.15]  
[C.05.02.16. 表'tbl\_name'不存在][C.05.02.16]  
[C.05.02.17. 无法初始化字符集][C.05.02.17]  
[C.05.02.18. 'File'找不到及类似错误][C.05.02.18]  
[C.05.02.19. 表损坏问题][C.05.02.19]  

本节列出了用户运行MySQL服务器时常会遇到的一些错误。尽管问题是在你尝试运行客户端时出现的，但对很多问题的解决方案来说，需要更改MySQL服务器的配置。

###C.5.2.1. 拒绝访问
导致拒绝访问错误的原因很多。该错误常与客户端连接服务器时使用的MySQL账户有关。请参见章节[6.2，MySQL权限系统][06.02.00]和[6.2.7，拒绝访问错误的原因][06.02.07]。

[C.05.02.01]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.01
[06.02.00]:../Chapter_06/06.02.00_The_MySQL_Access_Privilege_System.md
[06.02.07]:../Chapter_06/06.02.07_Causes_of_Access-Denied_Errors.md

###C.5.2.2. 无法连接到本地MySQL服务器

Unix平台上的MySQL客户端能够以两种不同的方式连接到[**mysqld**][mysqld]服务器：通过文件系统中的文件（默认为/tmp/mysql.sock）使用Unix套接字进行连接，或通过端口号使用TCP/IP进行连接。Unix套接字文件的连接速度比TCP/IP快，但仅能在与相同计算机上的服务器相连时使用。如果未指定指定主机名或指定了特殊的主机名localhost，将使用Unix套接字。

如果MySQL服务器运行在Windows系统上，可以通过TCP/IP进行连接。如服务器使用[--enable-named-pipe][enable-named-pipe]选项启动，并且客户端和服务器在同一台主机上，也能使用命名管道进行连接。默认情况下，命名管道的名称为MySQL。如果在连接到[**mysqld**][mysqld]时未给定主机名，MySQL客户端首先会尝试连接到命名管道。如果无法连接，将连接到TCP/IP端口。当然，在Windows平台上，你可以使用“.”作为主机名，强制客户端使用命名管道进行连接。


错误（2002）“无法连接到…”通常是说，在系统上没有MySQL服务器正在运行，或在连接到服务器时使用了不正确的Unix套接字文件名或错误的TCP/IP端口号。同时，也应该检查一下你使用的TCP/IP端口是否被防火墙或者端口阻断服务给禁用了。

错误（2003）无法在主机'server'（10061）上连接到MySQL服务器，表明网络连接已经被拒绝。首先你应该检查在你将要连接的主机上是否有MySQL服务器在运行，并且网络连接是否正常，再有你指定的MySQL服务器使用的端口和你指定的端口是否相同。

首先检查服务器主机上是否有名为mysqld的进程（在Unix平台上使用**ps xa | grep mysqld**，或在Windows平台上使用任务管理器）。如果没有这类进程，应启动服务器。请参见章节[2.10.1.3，“Starting and Troubleshooting the MySQL Server”][02.10.01.03]。

如果[**mysqld**][mysqld]进程正在运行，可使用下述命令检查。在你的具体设置中，端口号或Unix套接字文件名可能会有所不同。host_ip代表运行服务器的机器的IP地址。

```
shell> mysqladmin version
shell> mysqladmin variables
shell> mysqladmin -h `hostname` version variables
shell> mysqladmin -h `hostname` --port=3306 version
shell> mysqladmin -h host_ip version
shell> mysqladmin --protocol=SOCKET --socket=/tmp/mysql.sock version
```

注意，应与**hostname**命令一起使用反引号而不是单引号，这会使**hostname**输出（当前主机名）被代入[mysqladmin][]命令。如果没有**hostname**命令或正运行在Windows平台上，应以手动方式在选项（-h）后面输入机器的主机名（无反引号）。也可以使用TCP/IP协议用-h 127.0.0.1连接到本地主机。

请确认服务器没有配置成忽略网络连接，或者（如果你尝试使用远程的连接）没有在它的网络接口上配置为只监听本地。如果服务器使用参数（[--skip-networking][skip-networking]）启动，服务器根本不会接收任何的TCP/IP连接。如果服务器使用参数（[--bind-address=127.0.0.1][bind-address]）启动，它只接受本地环路上的TCP/IP连接，不接受远程主机连接。

检查并确认防火墙没有阻止访问MySQL的连接。基于你的应用程序或者是MySQL用于通讯的端口号(默认3306)配置防火墙。在Linux或者Unix系统下，检查IP tables（或者类似的程序）的配置，确认端口没有被禁用。在Windows下，想ZoneAlarm应用或者Windows XP的个人防火墙可能需要放开对MySQL端口的限制。

下面给出了一些可能发生“Can't connect to local MySQL server”错误的原因：

* 本地主机的[**mysqld**][mysqld]未运行。请检查操作系统的进程列表以确保mysqld进程正在运行。

* 你正在具有很多TCP/IP连接的Windows平台上运行MySQL服务器。如果你的客户端经常出现错误，请参见章节[C.5.2.2.1，“在Windows系统上连接MySQL服务器失败”][C.05.02.02.01]，以找出规避方法。

* 某人删除了mysqld使用的Unix套接字文件（默认为/tmp/mysql.sock）。例如，你可能执行了将旧文件从/tmp目录删除的**cron**任务。你总能执行[mysqladmin version][]来检查[mysqladmin][]试图使用的Unix套接字文件是否的确存在。在该情况下，更正方式是修改**cron**任务，不删除mysql.sock文件，或将套接字文件置于其他地方。请参见章节[C.05.04.05，“如何保护或更改MySQL Unix套接字文件”][C.05.04.05]。

* 你使用[--socket=/path/to/socket][socket=path]选项启动了[**mysqld**][mysqld]服务器，当忘记将套接字文件的新名称通知客户端程序。如果更改了关于服务器的套接字路径，也必须通知MySQL客户端。可在运行客户端程序时使用相同的[--socket][socket=file_name]选项来完成该任务。此外，你还应确保客户端具有访问文件mysql.sock的权限。要想找出套接字文件的位置，可使用：
	shell> netstat -ln | grep mysql
请参见章节[C.5.4.5, “如何保护或更改MySQL Unix套接字文件”][C.05.04.05].

* 你正在使用Linux而且一个服务器线程已死亡（内核已清除）。在此情况下，在重启MySQL服务器之前，必须杀死其他[**mysqld**][mysqld]线程（例如，使用[**kill**][kill]或[**mysql_zap**][mysql_zap]脚本）。请参见章节[C.5.4.2，“如果MySQL持续崩溃，应该做些什么”][C.05.04.02]。

* 服务器或客户端程序不具有访问包含Unix套接字文件的目录或套接字文件本身的恰当权限。在该情况下，必须更改目录或套接字文件的访问权限，以便服务器或客户端程序能够访问它们，或用[--socket][socket=path]选项重启[**mysqld**][mysqld]，在该选项中指定服务器能创建、而且客户端可访问的目录下的套接字文件名。

如果你遇到错误消息“Can't connect to MySQL server on some_host”，可以尝试采取下述步骤以找出问题所在：

* 执行“telnet some_host 3306”并按两次回车键，检查服务器是否运行在该主机上（3306是默认的MySQL端口号。如果你的服务器正在监听不同的端口，请更改该值）。如果有一个MySQL服务器正在运行并监听该端口，你应收到包含服务器版本号的回应。如果遇到错误，如“telnet: Unable to connect to remote host: Connection refused”，表示在给定端口上没有运行的服务器。

* 如果服务器正运行在本地主机上，请使用Unix套接字文件，并使用[**mysqladmin -h localhost variables**][mysqladmin]进行连接。验证服务器监听的TCP/IP端口号（它是[port][]变量的值）。

如果你的MySQL服务器运行在Linux系统下，并且开启了SELinux。请确认已经关闭了SELinux对mysqld进程的保护。

[C.05.02.02]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.02
[mysqld]:../Chapter_04/04.03.01_mysqld_The_MySQL_Server.md
[enable-named-pipe]:../Chapter_05/05.01.03_Server_Command_Options.md#enable-named-pipe
[02.10.01.03]:../Chapter_02/02.10.01_Unix_Postinstallation_Procedures.md#02.10.01.03
[mysqladmin]:../Chapter_04/04.05.02_mysqladmin_Client_for_Administering_a_MySQL_Server.md
[skip-networking]:../Chapter_05/05.01.03_Server_Command_Options.md#skip-networking
[bind-address]:../Chapter_05/05.01.03_Server_Command_Options.md#bind-address
[C.05.02.02.01]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.02.01
[C.05.04.05]:./C.05.04_Administration-Related_Issues.md#C.05.04.05
[socket=path]:../Chapter_05/05.01.03_Server_Command_Options.md#socket=path
[socket=file_name]:../Chapter_04/04.02.02_Connecting_to_the_MySQL_Server.md#socket=file_name
[C.05.04.05]:./C.05.04_Administration-Related_Issues.md#C.05.04.05
[kill]:../Chapter_13/13.07.06_Other_Administrative_Statements.md#13.07.06.04
[mysql_zap]:../Chapter_04/04.06.16_mysql_zap_Kill_Processes_That_Match_a_Pattern.md
[port]:../Chapter_05/05.01.04_Server_System_Variables.md#port

####C.5.2.2.1. 在Windows系统上连接MySQL服务器失败

* 当你在具有很多TCP/IP连接的Windows上运行MySQL服务器，并经常在客户端上遇到“Can't connect to MySQL server”错误时，原因可能是Windows不允许过多的短周期端口来服务这些连接。

参数TIME\_WAIT的目的是，即使连接已经关闭，也能保持连接来接收数据包。这是因为Internet路由能导致数据包经过一个缓慢的路由才能到达目的地，并且数据包可能在连接两端都同意关闭连接之后到达。如果端口已经被一个新连接占用，旧连接的数据包可能会破坏连接协议或者危及原连接的信息完整性。参数TIME\_WAIT延迟就是用来阻止这个问题的发生，确保端口在数据包到达之前不会被重用。

在局域网连接中大幅度的减小TIME\_WAIT是安全的，因为数据包延迟非常严重的概率很小；而这些数据包如果通过互联网传输，距离和延迟就相对比较大了。

Windows系统允许属于用户短暂的（短命）TCP端口。任何一个端口被关闭后，它都会停留在TIME\_WAIT状态下120秒。这个端口在没有过期之前无法再次使用。不同的Windows版本的端口号的范围不同，不同系统版本对应的端口号的范围限制如下：

* 整个Windows 2003服务器：端口范围为1025~5000
* Windows Vista, Server 2008, 更高版本：端口范围为49152~65535

在很短的一段时间内，随着少量可用的TCP端口（5000）和大量被打开和关闭的TCP端口，并处于TIME\_WAIT状态，你很可能遇到端口耗尽问题。处理该问题的方法有两种：

* 通过尽可能使用连接池或者持久连接，减少快速消耗的TCP端口数。

* 调整Windows注册表中的某些设置（请参见下文）。

**要点：下述步骤涉及更改Windows 注册表。更改注册表之前，请备份注册表，并确认你已掌握在出现问题时恢复注册表的方法。关于备份、恢复和编辑注册表的更多信息，请请参见Microsoft知识库中的下述文献：[http://support.microsoft.com/kb/256986/EN-US/](http://support.microsoft.com/kb/256986/EN-US/)。**


1. 启动注册表编辑器（Regedt32.exe）。
2. 在注册表中确定下述键值的位置：

		HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters

3. 在“编辑”菜单上点击“添加值”，然后增加下述注册值：  

		Value Name: MaxUserPort  
		Data Type: REG_DWORD
		Value: 65534
	
  它用于设置为任何用户提供的临时端口数。有效范围介于5000和65534之间（十进制）。默认值为0x1388（5000，十进制）。
4. 在编辑菜单上点击“添加值”，然后增加下述注册值：

		Value Name: TcpTimedWaitDelay  
		Data Type: REG_DWORD  
		Value: 30
	  
  它用于设置关闭之前将TCP端口连接保持在TIME_WAIT状态的秒数。有效范围介于0秒和300（十进制）秒之间。默认值为0x78（120秒，十进制）。
5. 退出注册表编辑器。
6. 重新引导机器。

注解：撤销上述设置十分简单，删除你创建的注册表即可。

[C.05.02.02.01]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.02.01


###C.5.2.3. 丢失MySQL服务器连接
产生这个错误消息的原因可能有三个。

如果该错误频繁出现，这通常表明是网络连接故障，您应该检查您的网络条件。如果错误信息中包含“during query”，这可能是您正在经历的情况。

当成千上万行记录被作为查询的部分被发送时，有时“during query”格式的错误消息会发生。如果你知道是这种情况发生，你应该尝试增加[net\_read\_timeout][net_read_timeout]值，将它从默认30秒修改为60秒或者更长，以满足数据传输的完成。

更罕见的是，当客户端试图初始化连接到服务器时，这个错误也可能发生。在这种情况下，如果[connect\_timeout][connect_timeout]值设置只有几秒钟时间，为了解决这个问题可以把这个值增加到10秒，如果与服务器之间的距离比较远或者连接比较慢，还可以设置得更长。你可以通过使用“SHOW GLOBAL STATUS LIKE 'Aborted\_connects'"来判断是否正在经历这种不常见的情况。当每个尝试初始化连接到服务器被中止时，这个值都会加1。你可能会看到错误消息中包含“reading authorization packet”；如果确实是这样，这表明这就是你需要的答案。

如果刚才描述的原因都不符合你的情况，那么你可能碰见了比[max\_allowed\_packet][max_allowed_packet]大的[BLOB][blob]值，这个值在某些客户端也能引起这个错误。有时你能看到一个[ER\_NET\_PACKET\_TOO\_LARGE][er_net_packet_too_large]错误，这就能确定你需要增加[max\_allowed\_packet][max_allowed_packet]值的大小了。

[C.05.02.03]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.03
[net_read_timeout]:../Chapter_05/05.01.04_Server_System_Variables.md#net_read_timeout
[connect_timeout]:../Chapter_05/05.01.04_Server_System_Variables.md#connect_timeout
[max_allowed_packet]:../Chapter_05/05.01.04_Server_System_Variables.md#max_allowed_packet
[blob]:../Chapter_11/11.04.03_The_BLOB_and_TEXT_Types.md
[er_net_packet_too_large]:./C.03.00_Server_Error_Codes_and_Messages.md#ER_NET_PACKET_TOO_LARGE

###C.5.2.4. 客户端不支持认证协议

MySQL当前的认证协议使用经过hash算法处理的密码来实现的，这个算法与旧版（4.1之前）客户端使用的算法不兼容。如果使用旧版客户端连接4.1或者更高版本的MySQL服务器，可能会连接失败，并报出如下错误： 

	shell> mysql
	客户端不支持服务器请求的鉴定协议：请考虑升级MySQL客户端。

为了解决该问题，首选的解决方案是所有客户端升级程序使用4.1.1或更高版本的客户端库。如果这是不可能的，可以使用下列方法之一：

* 用4.1版之前的客户端程序连接到服务器时，请使用仍具有4.1版之前格式密码的账户

* 为每一个使用旧客户端程序的账户设置旧版本（4.1之前）的密码风格。可以使用[SET PASSWORD][]语句或者OLD\_PASSWORD()函数来实现。对于MySQL5.6.6，同时还需要确认当前的认证插件是否是mysql\_old\_password:  

		mysql> UPDATE mysql.user SET plugin = 'mysql_old_password'
		mysql> WHERE User = 'some_user' AND Host = 'some_host';
		mysql> FLUSH PRIVILEGES;
		mysql> SET PASSWORD FOR
		    -> 'some_user'@'some_host' = OLD_PASSWORD('newpwd');

将上例中的password替换成你想使用的“新密码”。由于MySQL无法告诉你原来的密码是什么，所以你不得不重新挑选一个新密码。

* 让MySQL服务器默认使用旧风格的密码hash算法：  
	a. 启动mysqld时，将系统变量（old_passwords）设置为1。  
	b. 给每一个密码已经被更新为新版（4.1或者更高）格式的账户指定旧格式的密码，你可以用下面的查询找出这些帐户：
    
			mysql> SELECT Host, User, Password FROM mysql.user 
			    -> WHERE LENGTH(Password) > 16; 
	使用Host和User值查询并显示的每个账户记录，并使用前面所介绍的方法修改密码。

如果安装了多个版本的MySQL，但客户端程序是动态链接并且链接到了一个旧的标签库上，
在这种情况下，客户端可能会发生不支持认证协议错误。请确认客户端使用了最新并兼容的标签库版本。实现这些过程依赖于你的系统。

> 注释：mysql扩展不支持MySQL4.1.1和更高版本中的认证协议，无论使用的PHP是什么版本的。如果你打算与MySQL4.1或更高版本一起使用mysql扩展，需要使用前面介绍的选项之一来配置MySQL，以便能够与较早的客户端一起使用。mysqli扩展（在PHP5中，添加了“MySQL, Improved”支持）与MySQL4.1和更高版本中使用的改进的密码hash算法兼容，不需要对MySQL进行特殊配置就能使用该MySQL客户端库。关于mysqli扩展的更多信息，请参见[http://php.net/mysqli](http://php.net/mysqli)。

想了解密码hash算法和鉴定功能的额外背景知识，请参见章节[6.1.2.4，“Password Hashing in MySQL”][06.01.02.04]。

[C.05.02.04]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.04
[SET PASSWORD]:../Chapter_13/13.07.01_Account_Management_Statements.md#13.07.01.07
[old_passwords]:../Chapter_12/12.13.00_Encryption_and_Compression_Functions.md#old_passwords
[06.01.02.04]:../Chapter_06/06.01.02_Keeping_Passwords_Secure.md#06.01.02.04


###C.5.2.5. 交互式密码输入验证失败
使用无密码值的“[--password][password]”或“-p”选项调用时，MySQL客户端程序将提示输入密码：

	shell> mysql -u user_name -p
	Enter password:

在某些系统上，当你在选项文件或命令行上指定时，你可能会发现密码能够通过验证，但是当你在“Enter password:”提示下以交互方式输入密码时，你可能会发现输入的密码无法通过验证。当系统所提供的用于读取密码的库对密码长度有限制时（典型情况下为8个），就会出现该问题。这是与系统库有关的问题，与MySQL无关。要想处理该问题，可将MySQL密码更改为由8个字符或更少字符构成的值，或将密码置于选项文件中。

[C.05.02.05]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.05
[password]:../Chapter_04/04.02.02_Connecting_to_the_MySQL_Server.md#password

###C.5.2.6. 主机'host_name'被屏蔽

If the following error occurs, it means that mysqld has received many connection requests from the given host that were interrupted in the middle:
如果遇到下述错误，表示 [mysqld][] 已收到来自给定主机的很多连接请求，但该连接却在中途中断。

	由于出现很多连接错误，主机'host_name'被屏蔽。
	可使用'mysqladmin flush-hosts'解除屏蔽。

系统变量（[max\_connect\_errors][max_connect_errors]）的值决定了MySQL允许多少连续中断连接请求。（请参见章节[5.1.4，“服务器系统变量”][05.01.04]）在[max\_connect\_errors][max_connect_errors]次失败请求没有连接成功，mysqld认定什么出错了（例如，有人试图非法闯入），并阻止主机进一步的连接，直到你发出一个[FLUSH HOSTS][]语句或执行[mysqladmin flush-hosts][mysqladmin]命令。

在默认情况下，[mysqld][]会在100次连接错误（MySQL 5.6.6版本之前，此值是10）后屏蔽主机。你可以在服务器启动时，设置参数[max\_connect\_errors][max_connect_errors]来调整该值：

	shell> mysqld_safe --max_connect_errors=10000 &
服务器在运行是也可以设置该值：
	mysql> SET GLOBAL max_connect_errors=10000;

如果在给定主机上遇到该错误，首先应核实该主机的TCP/IP连接是否正确。如果存在网络问题，增加[max\_connect\_errors][max_connect_errors]变量的值不会有任何好处。

[C.05.02.06]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.06
[max_connect_errors]:../Chapter_05/05.01.04_Server_System_Variables.md#max_connect_errors
[05.01.04]:../Chapter_05/05.01.04_Server_System_Variables.md
[FLUSH HOSTS]:../Chapter_13/13.07.06_Other_Administrator_Statements.md

###C.5.2.7. 连接数过多
当你试图连接到[mysqld][]服务器时遇到“Too many connections”错误，这表示所有可用的连接均已被其他客户端使用。

允许的连接数由系统变量（[max\_connections][max_connections]）控制。当MySQL被用作Apache Web服务器时，默认值151能改善性能。（以前的默认值是100）。如果需要支持更多的连接，应该变量设置为一个更大的值。

[mysqld][]实际上允许[max\_connections+1][max_connections]个客户端进行连接。额外的连接保留给具有[SUPER][super]权限的账户。通过为系统管理员而不是普通用户授予[SUPER][super]权限（普通用户不应具有该权限），系统管理员能够连接到服务器，并使用[SHOW PROCESSLIST][show processlist]来诊断问题，即使已连接的无特权客户端数已达到最大值。请参见章节[13.7.5.30，“SHOW PROCESSLIST 语法”][12.07.05.30]。

MySQL能够支持的最大连接数量取决于给定平台上的线程库，可用的RAM数量，每个连接使用多少RAM，每个连接的工作量，所需的响应时间。 Linux或Solaris应该能够支持在500至1000个并发连接，如果你有很多GB的RAM可用并且每个任务的工作量不大或响应时间目标要求不高，那么该值通常多达10,000连接。 因为Windows平台上使用了POSIX兼容层，所以Windows是有限制的（打开表的数量×2+打开的连接数量） < 2048

可能需要增加[open-files-limit][]。请参见章节[2.5, “在Linux系统上安装MySQL”][02.05.00]中介绍的如何提高操作系统限制MySQL能够使用的句柄数量。

[C.05.02.07]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.02.07
[max_connections]:../Chapter_05/05.01.04_Server_System_Variables.md#max_connections
[super]:../Chapter_06/06.02.01_Privileges_Provided_by_MySQL.md#super
[show processlist]:../Chapter_13/13.07.05_SHOW_Syntax.md#12.07.05.30
[12.07.05.30]:../Chapter_13/13.07.05_SHOW_Syntax.md#12.07.05.30
[open-files-limit]:../Chapter_05/05.01.03_Server_Command_Options.md#open-files-limit
[02.05.00]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md

###C.5.2.8. 内存溢出
如果使用[mysql][]客户端程序发出了查询，并收到下述错误之一，则表示[mysql][]没有足够内存来保存全部查询结果：

	mysql: Out of memory at line 42, 'malloc.c'
	mysql: needed 8136 byte (8k), memory in use: 12481367 bytes (12189k)
错误2008：MySQL client ran out of memory
要想更正该问题，首先应检查查询是否正确。返回这么多行是否合理？如果不合理，更正查询并再次尝试。否则，应使用[--quick][quick]”选项调用[mysql][]。这样，将使用[mysql_use_result()][mysql_use_result] C API函数来检索结果集，这类函数能够降低客户端上的负载（但会加重服务器上的负载）。

[mysql]:../Chapter_04/04.05.01_mysql_The_MySQL_Command-Line_Tool.md
[quick]:../Chapter_04/04.05.01_mysql_The_MySQL_Command-Line_Tool.md#quick
[mysql_use_result]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.72


###C.5.2.9. 连接已断开

在本节中，涵盖了与错误（在查询期间，丢失连接)有关的内容。

错误'MySQL server has gone away'的最常见原因是服务器超时以及连接已关闭。在该情况下，通常能见到下述错误代码之一（具体的错误代码与操作系统有关）：

<table border="1">
<thead>
<tr><th>错误代码</th><th>描述</th></tr></thead>
<tbody><tr>
<td><code><a class="ulink" href="" target="_top">CR_SERVER_GONE_ERROR</a></code></td>
<td>客户端无法将问题发送至服务器。</td></tr>
<td><code><a class="ulink" href="" target="_top">CR_SERVER_LOST</a></code></td>
<td>写入服务器时客户端未收到错误，但也未获得问题的完整答案（或任何答案）。</td>
</tr></tbody></table>

在默认情况下，如果未发生任何事，8小时后服务器将关闭连接。也可以在启动mysqld时，通过设置[wait\_timeout][wait_timeout]变量更改时间限制。请参见章节[5.1.4，“Server System Variables”][05.01.04]。

如果有一个脚本，你仅需要再次发出查询，让客户端再次进行自动连接即可。其中，假定在客户端中启用了自动再连接功能（对于mysql命令行客户端，这是默认设置）。

MySQL服务器不可用错误的一些其他常见原因如下：

* 你（或db系统管理员）使用[KILL][]语句或[mysqladmin kill][mysqladmin]命令杀死了正在运行的线程。  

* 你试图在关闭了与服务器的连接后运行查询。这表明应更正应用程序中的逻辑错误。  

* 客户端运行的主机没有访问MySQL服务器的权限  

* 你在客户端上遇到TCP/IP连接超时错误。如果你使用了命令：[mysql\_options(..., MYSQL\_OPT\_READ\_TIMEOUT,...)][mysql_opt_read_timeout]或[mysql\_options(..., MYSQ\L_OPT\_WRITE\_TIMEOUT,...)][mysql_opt_write_timeout]，就可能出现该问题。在该情况下，增加超时值可能有助于问题的解决。  

* 你在服务器端遇到超时错误，而且禁用了客户端中的自动再连接功能（MYSQL结构中的再连接标志等于0）。
* 你正在使用Windows客户端，而且在发出命令之前服务器撤销了连接（或许是因为已超过[wait\_timeout][wait_timeout]）。  
在Windows上的问题；在某些情况下，当向连接到服务器的TCP/IP连接写入数据时，MySQL未收到来自操作系统的错误，但当试图从连接读取应答时出现错误。  <p></p>
对此的解决方式是：如果自上一次查询以来经过了较长时间，在连接上执行[mysql\_ping()][mysql_ping]（正是Connector/ODBC所作的）；或在[mysqld][]服务器上将[wait\_timeout][wait_timeout]设置得很高，使之实际上不存在超时。

* 如果你向服务器发出了不正确或过大的查询，也会遇到这类问题。如果[mysqld][]收到过大或无序的信息包，它会认为客户端出错，并关闭连接。如果需要执行较大的查询（例如，正在处理大的[BLOB][blob]列），可通过设置服务器的[max\_allowed\_packet][max_allowed_packet]变量，增加查询限制值，该变量的默认值为1MB。或许，你还需增加客户端上的最大信息包大小。关于设置信息包大小的更多信息，请参见章节[C.5.2.10，“Packet Too Large”][C.05.02.10]。  
INSERT或REPLACE语句，插入了大量行也可能导致这些类型的错误。这些语句中的任何一个发送一个请求到服务器，而请求与要插入的行数无关，因此，你通常可以减少每个INSERT或REPLACE发送的行数，来避免错误。

* 如果你的客户端低于4.0.8而且你的服务器版本是4.0.8或者更高（或者反过来），当你接收16MB或更大的信息包时，可能会丢失连接。。

* 如果主机名查找失败（例如，如果您的服务器或网络依赖的DNS服务器宕机），就有可能看到这个错误。这是因为MySQL依赖于主机系统进行名称解析，但从MySQL的角度来看，没有办法知道系统的解析功能是否可用，并且这个问题就是无法与其他的网络超时进行区分。  
如果MySQL启动时，设置了“[--skip-networking][skip-networking]”选项，也会见到“ MySQL server has gone away”的错误。  
另一个导致此错误发生的网络问题是如果MySQL的端口（默认3306）被防火墙禁用，从而组织任何连接到此MySQL服务器的连接。

* 当应用使用了多个子程序，而且所有的子程序都使用了同一个连接时也会碰到这个错误。可以通过为每个子程序设置独立的连接来避免这个问题。
* 你遇到了执行查询时服务器宕机的缺陷。

* 你可以通过执行mysqladmin version来检查MySQL是否已经不可用，如果不可用重启它，这个命令也可以查询服务器的运行时间。如果由于mysqld崩溃然后重启,而导致客户端连接被中断，你应该集中所有的精力查找崩溃的原因。启动并检查这个查询是否是导致服务器崩溃的原因，请见章节[C.5.4.2, “如果MySQL持续崩溃，应该做些什么”][C.05.04.02].

如果[mysqld][]启动时，设置了--log-warnings=2选项，你就能得到更多关于丢失连接的信息。这些关于无法连接的错误信息记录在hostname.err文件中，请见章节[5.2.2, “The Error Log”][05.02.02]。

如果你想将这个问题报告成一个缺陷，请确保包含下述的信息：

* 你可以在MySQL的错误日志中找到更多的关于MySQL是否存活的信息。请见章节[C.5.4.2, “如果MySQL持续崩溃，应该做些什么”][C.05.04.02]。

* 如果某个特定查询杀死了[mysqld][]，而且在运行查询前用[CHECK TABLE][check table]检查了涉及的表，你是否能提供一个可重复的测试案例？请见章节[23.4, “Debugging and Porting MySQL”][23.04.00]。

* 在MySQL服务器中，系统变量[wait\_timeout][wait_timeout]的值是什么？（mysqladmin variables给出了该变量的值。）

* 你是否尝试使用“--log”选项来运行[mysqld][]，以确定是否在日志中出现问题？（请见章节[5.2.3, “The General Query Log”][05.02.03]。）

另请见章节[C.5.2.11, “通信错误和中止连接”][C.05.02.11]，和章节[1.7, “How to Report Bugs or Problems”][01.07.11]。

[cr_server_gone_error]:./C.04.00_Client_Error_Codes_and_Messages.md#cr_server_gone_error
[cr_server_lost]:./C.04.00_Client_Error_Codes_and_Messages.md#cr_server_lost
[wait_timeout]:../Chapter_05/05.01.04_Server_System_Variables.md#wait_timeout
[kill]:../Chapter_13/13.07.06_Other_Administrative_Statements.md#13.07.06.04
[mysqladmin]:../Chapter_04/04.05.02_mysqladmin_Client_for_Administering_a_MySQL_Server.md
[mysql_opt_read_timeout]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.49
[mysql_opt_write_timeout]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.49
[mysql_ping]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.51
[skip-networking]:../Chapter_05/05.01.03_Server_Command_Options.md#skip-networking
[C.05.04.02]:./C.05.04_Administration-Related_Issues.md#C.05.04.02
[05.02.02]:../Chapter_05/05.02.02_The_Error_Log.md
[check table]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.02
[23.04.00]:../Chapter_23/23.04.00_Debugging_and_Porting_MySQL.md
[05.02.03]:../Chapter_05/05.02.03_The_General_Query_Log.md
[C.05.02.11]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.11
[01.07.11]:../Chapter_01/01.07.11_How_to_Report_Bugs_or_Problems

###C.5.2.10. 信息报过大
通信信息包是发送至MySQL服务器的单个SQL语句，或发送至客户端的单一行，或是从主服务器发送到从服务器的二进制日志事务。

在MySQL5.6中，到服务器或者客户端的信息包最大值可以为1GB。


当MySQL客户端或[mysqld][]服务器收到大于[max\_allowed\_packet][max_allowed_packet]字节的信息包时，将发出“[ER_NET_PACKET_TOO_LARGE][er_net_packet_too_large]”错误，并关闭连接。对于某些客户端，如果通信信息包过大，可能会遇到“Lost connection to MySQL server during query”错误。

客户端和服务器均有自己的[max\_allowed\_packet][max_allowed_packet]变量，因此，如你打算处理大的信息包，必须增加客户端和服务器上的该变量。

如果你正在使用[mysql][]客户端程序，其[max\_allowed\_packet][max_allowed_packet]变量的默认值为16MB。要想设置较大的值，可用下述方式启动[mysql][]：

	shell> mysql --max_allowed_packet=32M
它将信息包的大小设置为32MB。  
服务器的[max\_allowed\_packet][max_allowed_packet]默认大小是1MB。如果服务器需要处理大查询，你可以增加这个值（例如，如果你使用了[BLOB][blob]列）。例如，将变量（max_allowed_packet）设置为16M，使用下述方法启动服务器：

	shell> mysqld --max_allowed_packet=16M

你也可以在配置文件中设置[max\_allowed\_packet][max_allowed_packet]的大小。例如将这个参数设置为16M，在配置文件中添加如下内容：

	[mysqld]
	max_allowed_packet=16M

增加该变量的值十分安全，这是因为仅当需要时才会分配额外内存。例如，仅当你发出长查询或[mysqld][]必须返回大的结果行时[mysqld][]才会分配更多内存。该变量之所以取较小默认值是一种预防措施，以捕获客户端和服务器之间的错误信息包，并确保不会因偶然使用大的信息包而导致内存溢出。

如果你正是用大的BLOB值，而且未为[mysqld][]授予为处理查询而访问足够内存的权限，也会遇到与大信息包有关的奇怪问题。如果怀疑出现了该情况，请尝试在[mysqld\_safe][mysqld_safe]脚本开始增加ulimit -d 256000，并重启[mysqld][]。

[er_net_packet_too_large]:./C.03.00_Server_Error_Codes_and_Messages.md#er_net_packet_too_large
[mysqld_safe]:../Chapter_04/04.03.02_mysqld_safe_MySQL_Server_Startup_Script.md


###C.5.2.11. 通信错误和中止连接

如果连接问题发生，例如通信错误或者连接中断，使用如下资源来诊断问题：

* 错误日志，请见章节[5.2.2, “The Error Log”][05.02.02]。
* 数据库日志，请见章节[5.2.3, “The General Query Log”][05.02.03]。
* 状态变量（Aborted\_xxx和Connection\_errors\_xxx），请见章节[5.1.6, “Server Status Variables”][05.01.06]。

* 主机缓存，可以使用Perfomance Schema库里的host\_cache表来获取可访问的主机缓存。请见章节[8.11.5.2, “DNS Lookup Optimization and the Host Cache”][08.11.05.02]和章节[21.9.10.1, “The host_cache Table”][21.09.10.01]。

如果服务器是用“[--log-warnings][log-warnings]”选项启动的，在错误日志中可能会发现下述消息：

	010301 14:38:23  Aborted connection 854 to db: 
	'users' user: 'josh'
如果客户端连接成功，但随后又出现连接不正常或者连接被中断，那么服务器会在状态变量（[Aborted\_clients][Aborted_clients]）上加1，并将中断连接的错误消息写入错误日志中。可能的原因如下：

* 客户端程序在退出之前未调用[mysql\_close()][mysql_close]。

* 客户端的空闲时间超过[wait\_timeout][wait_timeout]或[interactive\_timeout][interactive_timeout]秒，未向服务器发出任何请求。请见章节[5.1.4, “Server System Variables”][05.01.04]。

* 客户端在数据传输中途突然结束。

如果一个客户端甚至都无法连接，服务器在状态变量（[Aborted\_clients][Aborted_clients]）上加1。可能产生失败连接的原因如下：

* 客户端不具有连接至数据库的权限。

* 客户端采用了不正确的密码。

* 连接信息包不含正确信息。

* 获取连接信息包的时间超过[connect\_timeout][connect_timeout]秒。请见章节[5.1.4, “Server System Variables”][05.01.04]。

如果出现这类情况，可能表明某人正试图侵入你的服务器！如果你的服务器启用了数据库日志（general log），那么这种类型的问题的消息会被记录到数据库日志中。

对于中断客户端或中断连接问题，其他可能的原因如下：

* 变量（[max\_allowed\_packet][max_allowed_packet]）的值太小或者查询需要更多的内存（比已分配给[mysqld][]的内存更多）。请见章节[C.5.2.10, “信息报过大”][C.05.02.10]。

* 与Linux一起使用以太网协议，半双工或全双工。很多Linux以太网驱动均存在该缺陷。应通过FTP在客户端和服务器机器之间传输大文件来测试该缺陷。如果传输处于burst-pause-burst-pause（爆发-暂停-爆发-暂停）模式，表明你遇到了Linux双工故障。唯一的解决方法是，将网卡和Hub/交换器的双工模式切换为全双工或半双工，并对结果进行测试以确定最佳设置。

* 与线程库有关的某些问题导致读取中断。

* 配置不良的TCP/IP。

* 有问题的以太网、Hub、交换器、电缆等。只有通过更换硬件才能恰当诊断。

请见章节[C.5.2.9, “连接已断开”][C.05.02.09]。

[05.02.02]:../Chapter_05/05.02.02_The_Error_Log.md
[05.02.03]:../Chapter_05/05.02.03_The_General_Query_Log.md
[05.01.06]:../Chapter_05/05.01.06_Server_Status_Variables.md
[08.11.05.02]:../Chapter_08/08.11.05_Optimizing_Network_Use.md#08.11.05.02
[21.09.10.01]:../Chapter_21/21.09.10_Performance_Schema_Miscellaneous_Tables#21.09.10.01
[log-warnings]:../Chapter_05/05.01.03_Server_Command_Options.md#log-warnings
[Aborted_clients]:../Chapter_05/05.01.06_Server_Status_Variables.md#Aborted_clients
[mysql_close]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.05
[interactive_timeout]:../Chapter_05/05.01.04_Server_System_Variables.md#interactive_timeout
[05.01.04]:../Chapter_05/05.01.04_Server_System_Variables.md
[connect_timeout]:../Chapter_05/05.01.04_Server_System_Variables.md#connect_timeout


###C.5.2.12. 表已满
如果发生了表已满（table-full）错误，可能是磁盘满了或者表的大小已经到了最大值。表的最大值通常都是由操作系统像限制文件大小一样限制的，并不是由MySQL内部限制。请见章节[E.10.3, “Limits on Table Size”][E.10.03]。

[E.10.03]:../Appendix_E/E.10.03_Limits_on_Table_Size.md


###C.5.2.13. 无法创建/写入文件

如果某些查询遇到下述类型的错误，它意味着MySQL不能为临时目录下的结果集创建临时文件：

	Can't create/write to file '\\sqla3fe_0.ism'.
上述错误是Windows平台上的典型消息，Unix平台上的消息与之类似。

一种更正方式是使用“[--tmpdir][tmpdir]”选项启动[mysqld][]，或在配置文件的[mysqld]部分增加该选项。例如，要想指定目录C:\temp，可使用：

	[mysqld]
	tmpdir=C:/temp
目录C:\temp必须存在，并有足够的空间允许MySQL写入它。请见章节[4.2.3.3, “Using Option Files”][04.02.03.03]。

该错误的另一个原因可能是权限问题。请确认MySQL服务器能够写入[tmpdir][]目录。

此外，还用使用[perror][]检查错误代码。服务器无法写入表的一个原因是文件系统已满：

	shell> perror 28
	OS error code  28:  No space left on device

如果在启动过程中发生了如下错误，这表明用于存放数据文件的文件系统或者目录正处于写保护状态。假如写错误发生在一个测试文件上，而且错误不严重，那么可以安全地忽略。

	Can't create test file /usr/local/mysql/data/master.lower-test

[tmpdir]:../Chapter_05/05.01.03_Server_Command_Options.md#tmpdir
[04.02.03.03]:../Chapter_04/04.02.03_Specifying_Program_Options.md#04.02.03.03
[perror]:../Chapter_04/04.08.01_perror_Explain_Error_Codes.md


###C.5.2.14. 命令不同步
如果遇到“命令不同步”错误，将无法在你的客户端代码中运行该命令，你正在以错误顺序调用客户端函数。
例如，如果你正使用[mysql\_use\_result()][mysql_use_result]，并打算在调用[mysql\_free\_result()][mysql_free_result]之前执行新查询，就会出现该问题。如果你试图执行两次查询，但并未在两次查询之间调用[mysql\_use\_result()][mysql_use_result]或[mysql\_store\_result()][mysql_store_result]，也会出现该问题。

[mysql_use_result]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.72
[mysql_free_result]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.25
[mysql_store_result]:../Chapter_22/22.08.07_C_API_Function_Descriptions.md#22.08.07.70


###C.5.2.15. 忽略用户
如果遇到下述错误，表示当启动[mysqld][]时或重新加载授权表时，在用户表中发现具有无效密码的账户。

发现用户'some\_user'@'some\_host'密码错误；忽略用户。

作为其结果，权限系统将简单忽略账户。

在下面的介绍中，指明了可能的原因和问题的更正措施：

你或许正打算用旧的用户表运行新版本的[mysqld][]。通过执行[mysqlshow mysql user][mysqlshow]检查Password（密码）列是否短于16个字符，可检查该问题。如果结果是肯定的，可运行scripts/add_long_password脚本更正该问题。

账户具有旧的密码（8字符长），将user表里的账户密码修改为新的格式。

你在用户表（mysql.user）中指定了没有使用[PASSWORD()][password]函数的密码。选定[mysql][]数据库，并使用[PASSWORD()][]函数更新用户表，来指定一个新的密码：

	mysql> UPDATE user SET Password=PASSWORD('newpwd')
    	-> WHERE User='some_user' AND Host='some_host';

[mysqlshow]:../Chapter_04/04.05.06_mysqlshow_Display_Database_Table_and_Column_Information.md
[password]:../Chapter_12/12.13.00_Encryption_and_Compression_Functions.md#password


###C.5.2.16. 表'tbl_name'不存在

如果遇到下述错误之一，通常意味着当前数据库中不存在具有给定名称的表：

	Table 'tbl_name' doesn't exist
	Can't find file: 'tbl_name' (errno: 2)

在某些情况下，表确实存在，但未正确引用它：

* 由于MySQL使用目录和文件来保存数据库和表，如果它们位于区分文件名大小写的文件系统上，数据库和表名也区分文件大小写。
* 即使对于不区分大小写的文件系统，如Windows，在查询内对给定表的所有引用必须使用相同的大小写。
可以使用[SHOW TABLES][]检查位于当前数据库中的表。请见章节[13.7.5, “SHOW Syntax”][13.07.05]。

[SHOW TABLES]:../Chapter_13/13.07.05_SHOW_Syntax.md#12.07.05.38
[13.07.05]:./../Chapter_13/13.07.05_SHOW_Syntax.md#


###C.5.2.17. 无法初始化字符集

如果存在字符集问题，可能会遇到下述错误：

	MySQL Connection Failed: Can't initialize character set charset_name

导致该错误的原因如下：

* 字符集为多字节字符集，但客户端不支持该字符集。在该情况下，需要使用“[-DDEFAULT\_CHARSET=charset\_name][-DDEFAULT_CHARSET]”或“[-DWITH\_EXTRA\_CHARSETS=charset\_name][-DWITH_EXTRA_CHARSETS]”选项运行**CMake**以重新编译客户端。请见章节[2.9.4, “MySQL Source-Configuration Options”][02.09.04]。   
所有的标准MySQL二进制文件均是采用“[-DWITH\_EXTRA\_CHARSETS=complex][-DWITH_EXTRA_CHARSETS]”编译的，能够支持所有的多字节字符集。请见章节[2.9.4, “MySQL Source-Configuration Options”][02.09.04]。

* 字符集是未编译到[mysqld][]中的简单字符集，而且字符集定义文件不在客户端预期的位置。  
在该情况下，需要采取下述方法之一解决问题：

	* 重新编译客户端，使之支持字符集。请见章节[2.9.4, “MySQL Source-Configuration Options”][02.09.04]。

	* 为客户端指定字符集定义文件所在的目录。对于很多客户端，可使用“--character-sets-dir”选项完成该任务。

	* 将字符集定义文件复制到客户端预期的位置。

[-DDEFAULT_CHARSET]:../Chapter_02/02.09.04_MySQL_Source-Configuration_Options.md#-DDEFAULT_CHARSET
[-DWITH_EXTRA_CHARSETS]:../Chapter_02/02.09.04_MySQL_Source-Configuration_Options.md#-DWITH_EXTRA_CHARSETS
[02.09.04]:../Chapter_02/02.09.04_MySQL_Source-Configuration_Options.md


###C.5.2.18. 'File'找不到及类似错误
如果遇到“ERROR '...'未发现(errno: 23)”，“无法打开文件：... (errno: 24)”，或来自MySQL的具有errno 23或errno 24的其它错误，它表示未为MySQL服务器分配足够的文件描述符。你可以使用**[perror][]**实用工具来了解错误编号的含义：

	shell> perror 23
	系统错误代码23：文件表溢出
	shell> perror 24
	系统错误代码24：打开文件过多
	shell> perror 11
	系统错误代码11：资源暂时不可用

这里的问题是，**[mysqld][]**正试图同时打开过多的文件。你可以通知**[mysqld][]**不要一次打开过多文件，或增加**[mysqld][]**可用文件描述符的数目。

要想通知mysqld将一次打开的文件控制在较小的数目上，可以通过减少系统变量（[table\_open\_cache][table_open_cache]）的值，从而减少表缓冲（默认值是64）。这可能不能完全地限制文件描述符的使用，因为在某些情况下，服务器可能会尝试临时扩展缓存大小，章节[8.4.3.1, “How MySQL Opens and Closes Tables”][08.04.03.01]中所描述。降低[max\_connections][max_connections]值也减少了打开的文件数（默认值是100）。

要想更改[mysqld][]可用的文件描述符的数目，可在**[mysqld\_safe][mysqld_safe]**上使用“[--open-files-limit][open-files-limit]”选项或设置（自MySQL 3.23.30开始）open_files_limit系统变量。请参见[5.1.4, “Server System Variables”][05.01.04]。设置这些值的最简单方式是在选项文件中增加1个选项。请参见[4.2.3.3, “Using Option Files”][04.02.03.03]。如果**[mysqld][]**的版本较低，不支持设置打开文件的数目，可编辑**[mysqld\_safe][mysqld_safe]**脚本。在脚本中有1个注释掉的行**ulimit -n 256**。你可以删除‘#’字符取消对该行的注释，更改数值256，以设置**[mysqld][]**可用的文件描述符数目。

“[--open-files-limit][]”和**ulimit**能够增加文件描述符的数目，但最高不能超过操作系统限制的数目。此外还有1个“硬”限制，仅当以root用户身份启动**[mysqld\_safe][mysqld_safe]**或**[mysqld][]**时才能覆盖它（请记住，在该情况下，还需使用“--user”选项启动服务器，以便在启动后不再以root用户身份继续运行）。如果需要增加操作系统对各进程可用文件描述符的数目的限制，请参阅系统文档。

注解：
如果运行**tcsh** shell，**ulimit**不生效！请求当前限制值时，**tcsh**还是报告不正确的值。在该情况下，应使用**sh**启动**[mysqld\_safe][mysqld_safe]**。

[table_open_cache]:../Chapter_05/05.01.04_Server_System_Variables.md#table_open_cache
[08.04.03.01]:../Chapter_08/08.04.03_Optimizing_for_Many_Tables.md#08.04.03.01
[mysqld_safe]:../Chapter_04/04.03.02_mysqld_safe_MySQL_Server_Startup_Script.md
[open-files-limit]:../Chapter_05/05.01.04_Server_System_Variables.md#open-files-limit
[05.01.04]:../Chapter_05/05.01.04_Server_System_Variables.md
[04.02.03.03]:/Chapter_04/04.02.03_Specifying_Program_Options.md#04.02.03.03


###C.5.2.19. 表损坏问题
如果你使用选项（[--myisam-recover-options][myisam-recover-options]）启动**[mysqld][]**，并且MyISAM表被标记为'not closed properly'或'crashed'，MySQL会自动检查并尝试修改MyISAM表。如果这种情况发生，MySQL会将日志（'Warning: Checking table ...'和'Warning: Repairing table if the table needs to be repaired'）写入错误日志文件（hostname.err）中。如果在**[mysqld][]**意外退出之前，你碰到很多这样的错误，这时，一定有什么东西出了问题，需要进一步检查。

在MySQL5.6中，当检测到MyISAM表损坏，它会写一条额外的错误信息到错误日志中，例如源文件的名字和行号，以及访问此表的线程列表。例如：Got an error from thread_id=1, mi_dynrec.c:368。这个信息对于缺陷报告是非常有帮助的。

请见章节[5.1.3，“Server Command Options”][05.01.03]，和[23.4.1.7，“Making a Test Case If You Experience Table Corruption”][23.04.01.07]。

[myisam-recover-options]:../Chapter_05/05.01.03_Server_Command_Options.md#myisam-recover-options
[05.01.03]:../Chapter_05/05.01.03_Server_Command_Options.md
[23.04.01.07]:../Chapter_23/23.04.01_Debugging_a_MySQL_Server.md#23.04.01.07
[C.05.02.08]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.08
[C.05.02.09]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.09
[C.05.02.10]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.10
[C.05.02.11]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.11
[C.05.02.12]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.12
[C.05.02.13]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.13
[C.05.02.14]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.14
[C.05.02.15]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.15
[C.05.02.16]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.16
[C.05.02.17]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.17
[C.05.02.18]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.18
[C.05.02.19]:./C.05.02_Common_Errors_When_Using_MySQL_Programs.md#C.05.04.19