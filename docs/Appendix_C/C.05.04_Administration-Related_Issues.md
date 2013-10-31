##C.5.4. Administration-Related Issues
[+/-]

[C.5.4.1. 如何重置Root密码][C.05.04.01]  
[C.5.4.2. 如果MySQL持续崩溃，应该做些什么][C.05.04.02]  
[C.5.4.3. MySQL如何处理磁盘空间已满的问题][C.05.04.03]  
[C.5.4.4. MySQL将临时文件储存在哪里][C.05.04.04]  
[C.5.4.5. 如何保护或更改MySQL Unix套接字文件][C.05.04.05]  
[C.5.4.6. 时区问题][C.05.04.06]  

[C.05.04.01]:./C.05.04_Administration-Related_Issues.md#C.05.04.01
[C.05.04.02]:./C.05.04_Administration-Related_Issues.md#C.05.04.02
[C.05.04.03]:./C.05.04_Administration-Related_Issues.md#C.05.04.03
[C.05.04.04]:./C.05.04_Administration-Related_Issues.md#C.05.04.04
[C.05.04.05]:./C.05.04_Administration-Related_Issues.md#C.05.04.05
[C.05.04.06]:./C.05.04_Administration-Related_Issues.md#C.05.04.06

###C.5.4.1. 如何重置Root密码
如果你从未给MySQL设置root用户密码，服务器在以root用户身份进行连接时不需要密码。但是，建议你为每个账户设置密码。请参见[2.10.2, “Securing the Initial MySQL Accounts”][02.10.02]。

如果你知道root用户的密码，但想修改，请见章节[13.7.1.7, “SET PASSWORD 语法”][13.07.01.07]。

如果你以前设置了root用户密码，但却忘记了该密码，可设置新的密码。本节后续内容提供了Windows和Unix系统的说明，以及适用于任何系统的通用指令。

[02.10.02]:../Chapter_02/02.10.02_Securing_the_Initial_MySQL_Accounts.md
[13.07.01.07]:../Chapter_13/13.07.01_Account_Management_Statements.md#13.07.01.07


####C.5.4.1.1. 重置Root密码：Windows系统

在Windows上，使用下列步骤来重置所有的MySQL root账户的密码：

1. 用管理员（Administrator）账户登录系统。

2. 如果MySQL服务器正在运行，停止它。对于作为Windows服务运行的服务器，进入服务管理器：开始菜单->控制面板->管理工具->服务。然后在列表中找出MySQL服务，并停止它。  
如果服务器不是作为服务而运行的，可能需要使用任务管理器来强制停止它。

3. 创建一个包换下列语句的文本文件。并将password替换成你想使用的密码。

		UPDATE mysql.user SET Password=PASSWORD('MyNewPass') WHERE User='root';
		FLUSH PRIVILEGES;
将[UPDATE][]和[FLUSH][]语句都写到单独的行中。[UPDATE][]语句将所有的root账户的密码重置，而[FLUSH][]语句告诉服务器将权限表重新加载到内存中，来让服务器注意到密码已经更改。

4. 保存该文件。在本例中，该文件为C:\mysql-init.txt。

5. 打开控制台窗口，进入DOS命令提示：开始菜单->运行-> 输入**cmd**->确定

6. 指定选项（[--init-file][init-file]）来启动MySQL服务器（注意参数中反斜杠的数量应该是两个）：

		C:\> C:\mysql\bin\mysqld --init-file=C:\\mysql-init.txt
如果你将MySQL安装到了另一位置，请对下述命令进行相应的调整。  
在服务器启动时，执行由“[--init-file][init-file]”选项指定的文件的内容来更改root用户密码。  
你可以添加[--console][console]选项到命令中，让服务器把日志也输出到控制台窗口中，而不只是记录到日志文件中。  
如果你使用MySQL安装向导安装了MySQL，或许需要指定“[--defaults-file][defaults-file]”选项：

		C:\> "C:\Program Files\MySQL\MySQL Server 5.6\bin\mysqld.exe"
		         --defaults-file="C:\\Program Files\\MySQL\\MySQL Server 5.6\\my.ini"
		         --init-file=C:\\mysql-init.txt
使用服务管理器，可找到恰当的“[--defaults-file][defaults-file]”设置：开始菜单->控制面板->管理工具->服务。在列表中找出MySQL服务，右击，并选择“属性”选项。在可执行字段的Path（路径）中包含“[--defaults-file][defaults-file]”设置。

7. 当服务器启动成功后，删除C:\mysql-init.txt。

你应该可以使用root账户的新密码连接MySQL服务器了。停止MySQL服务器，然后用正常模式重启。如果以服务方式运行服务器，应从Windows服务窗口启动它。如果以手动方式启动了服务器，能够像正常情形下一样使用命令。

[update]:../Chapter_13/13.02.11_UPDATE_Syntax.md
[flush]:../Chapter_13/13.07.06_Other_Administrative_Statements.md#13.07.06.03
[init-file]:../Chapter_05/05.01.03_Server_Command_Options.md#init-file
[console]:../Chapter_05/05.01.03_Server_Command_Options.md#console
[defaults-file]:../Chapter_04/04.02.03_Specifying_Program_Options.md#defaults-file


####C.5.4.1.2. 重置Root密码：Unix系统

在Unix系统中，你可以使用下述步骤来重置所有MySQL root账户。这里假设你使用登录Unix系统的账户（通常用来运行服务器的）来启动服务器。例如，如果你使用mysql账户来运行服务器，那么首先应该使用mysql账户登录Unix。另外，在此例中，你可以用root账户登录，但你应该使用[--user=mysql][user]选项来启动mysqld。如果你用的是root账户启动**[mysqld][]**，而没有设置[--user=mysql][]选项，服务器就可能在数据目录中创建属于root账户的文件（例如日志文件），并且在将来的重启服务中，这可能引起权限相关的问题。如果这种情况发生了，你需要改变文件的所属者或者删除这些文件。

1. 使用启动**[mysqld][]**服务器的Unix用户来登录系统（例如，mysql）。

2. 找到包含服务器进程ID的.pid文件。该文件的准确位置和名称取决于你的分发版、主机名和配置。常见位置是/var/lib/mysql/、/var/run/mysqld/和/usr/local/mysql/data/。一般情况下，文件名的扩展名为.pid，并以mysqld或系统的主机名开始。  
你可以用kill（不是kill -9）指令来向**[mysqld][]**进程发起停止MySQL服务器的信号，并在下述命令中使用.pid文件的路径：

		shell> kill `cat /mysql-data-directory/host_name.pid`
使用反引号（不是单引号）包围cat命令。这个会将cat命令的输出作为kill命令的一部分执行。

3. 创建一个包含下述语句的文本文件。将password替换成你想使用的密码。

		UPDATE mysql.user SET Password=PASSWORD('MyNewPass') WHERE User='root';
		FLUSH PRIVILEGES;
将[UPDATE][]和[FLUSH][]语句都写到单独的行中。[UPDATE][]语句将所有的root账户的密码重置，而[FLUSH][]语句告诉服务器将权限表重新加载到内存中，来让服务器注意到密码已经更改。

4. 保存文件。在此例中，文件被命名为/home/me/mysql-init。文件包含了密码，所以它不应该被保存到其他账户有读取权限的目录中。如果你没有使用mysql账户（服务器运行使用的账户）登录，请确保mysql有读取这个文件的权限。

5. 使用[--init-file][init-file]选项来启动MySQL服务器：

		shell> mysqld_safe --init-file=/home/me/mysql-init &
在服务器启动时，执行由“[--init-file][init-file]”选项指定的文件的内容来更改root用户密码。

6. 当服务器启动成功后，删除/home/me/mysql-init。

你现在应该可以使用root账户的新密码连接MySQL服务器了。停止MySQL服务器，然后用正常模式重启。

[user]:../Chapter_05/05.01.04_Server_System_Variables.md#user
[mysqld]:../Chapter_04/04.03.01_mysqld_The_MySQL_Server.md

####C.5.4.1.3. 重置Root密码：通用介绍

前面的章节介绍了Windows和Unix系统下的密码重置过程。另外，你可以在任何平台上，使用[mysql][]客户端来设置新密码（但是这个方法是不安全的）：

1. 停止mysql并使用选项（[--skip-grant-tables][skip-grant-tables]）重启。这就使得任何连接到服务器的账户都无需密码，并且登录有都具有mysql中的所有权限。由于这是不安全的，因此你应该在使用选项（[--skip-grant-tables][skip-grant-tables]）的同时也使用选项（[--skip-networking][skip-networking]）来阻止远程客户端的连接。

2. 使用如下命令来连接到[mysqld][]服务器：

		shell> mysql
3. 在[mysql][]客户端中执行如下语句。将password替换成你想要使用的新密码：

		mysql> UPDATE mysql.user SET Password=PASSWORD('MyNewPass')
	    	->                   WHERE User='root';
		mysql> FLUSH PRIVILEGES;
[FLUSH][]语句告诉服务器将权限表重新加载到内存中，以让服务器注意到密码修改了。

你现在应该可以使用root账户的新密码连接MySQL服务器了。停止MySQL服务器，然后用正常模式（不附带[--skip-grant-ables][skip-grant-ables]和[--skip-networking][skip-networking]选项）重启。

[skip-grant-tables]:../Chapter_05/05.01.03_Server_Command_Options.md#skip-grant-tables
[skip-networking]:../Chapter_05/05.01.03_Server_Command_Options.md#skip-networking

###C.5.4.2. 如果MySQL持续崩溃，应该做些什么

正式发布之前，每个MySQL版本均在很多平台上进行了测试。这不表示MySQL中不存在缺陷，但是，如果存在缺陷，它们应很少，而且很难发现。如果你遇到问题，如果你尝试找出导致系统崩溃的准确原因，这始终很有帮助，这是因为，如果这样的话，你有一个更好的机会来快速修复问题。

首先，应尝试找出问题是否与**[mysqld][]**服务器有关，或是否与客户端有关。通过执行**[mysqladmin version][]**，可检查**[mysqld][]**服务器运行了多长时间。如果**[mysqld][]**宕机并重启，应查看服务器的错误日志以找出原因。请见章节[5.2.2，“The Error Log”][05.02.02]。

在某些系统上，在错误日志中，可发现**[mysqld][]**宕机的堆栈跟踪，可使用resolve_stack_dump程序解决它。请见章节[23.4，“Debugging and Porting MySQL”][23.04.00]。注意，错误日志中的变量值并非始终是100％正确的。

很多服务器崩溃是因损坏的数据文件或索引文件而导致的，每次执行完SQL语句之后并在向客户端通告结果之前，MySQL将使用write()系统调用更新磁盘上的文件（如果你使用了“[--delay-key-write][delay-key-write]”选项，情况并非如此，此时将写入数据文件而不是索引文件）。这意味着，即使**[mysqld][]**崩溃，数据文件的内容也是安全的，这是因为操作系统能保证将未刷新的数据写入磁盘。使用“[--flush][flush]”选项启动**[mysqld][]**，这样，每次执行完SQL语句后，可强制MySQL将所有内容写入磁盘。

前述介绍表明，在正常情况下不会出现损坏的表，除非出现了下述情况之一：

* 在更新过程中，MySQL服务器或服务器主机被停止。

* 你发现了**[mysqld][]**中存在的1个缺陷，该缺陷导致**[mysqld][]**在更新中途中止。

* 在**[mysqld][]**操作的同时，某些外部程序正在操控数据文件或索引文件，未恰当锁定表。

* 你在相同数据目录下运行了多个**[mysqld][]**服务器，该系统不能很好的支持文件系统锁定（通常是由lockd锁定管理器负责的），或运行多个服务器时禁用了外部锁定。

* 包含导致**[mysqld][]**混乱，并且损坏很严重的数据文件或索引文件。

* 在数据存储节点发现缺陷。这种可能性不大，但至少是可能的。在该情况下，可在修复的表副本上，通过使用[ALTER TABLE][]，尝试将表类型更改为另一种存储引擎。

由于很难得知为什么会出现崩溃，首先请检查是否由于其他方面的事项导致崩溃。请尝试采取下述措施：

* 用[mysqladmin shutdown][mysqladmin]停止**[mysqld][]**服务器，从数据目录运行[myisamchk --silent --force \\\*/\\\*.MYI][myisamchk]，检查所有的MyISAM表，并重启**[mysqld][]**。这样，就能确保从干净的状态运行服务器。请参见章节，[5, MySQL Server Administration][05.00.00]。

* 启动**[mysqld][]**是启用数据库日志（请见章节[5.2.3, “The General Query Log”][05.02.03]），并根据写入日志的信息确定是否某些特殊的查询杀死了服务器。约95%的缺陷与特定的查询有关。正常情况下，就是服务器重启前日志文件中最后一个查询。请见章节5.2.3, “The General Query Log”。如果能够用特殊查询重复杀死MySQL，甚至在执行查询前检查了所有表都正常的情况下也同样发生，那么你就应能确定这是一个缺陷，并应提交关于该缺陷的缺陷报告。请见章节[1.7，“How to Report Bugs or Problems”][01.07.00]。

* 尝试提供一个测试范例，我们应能利用该范例重现问题。请见章节[23.4，“Debugging and Porting MySQL”][23.04.00]。

* 请在mysql-test目录下并根据MySQL基准进行测试。请见章节[23.1.2, “The MySQL Test Suite”][23.01.02]。它们能很好地测试MySQL。你也可以为基准测试增加代码，以模拟你的应用程序。基准测试可在源码分发版的sql-bench目录下找到，对于二进制分发版，可在MySQL安装目录下的sql-bench目录下找到。

* 尝试使用fork_big.pl脚本（它位于源码分发版的测试目录下）。

* 如果你将MySQL配置为调试模式，如果哪里出错，可以更容易地搜集可能的错误信息。在CMake选项中用[-DWITH_DEBUG=1][DWITH_DEBUG]选项重新设置MySQL，然后再编译它。请见章节[23.4，“Debugging and Porting MySQL”][23.04.00]。

* 确保为你的操作系统安装了最新的补丁。

* 对**[mysqld][]**使用“[--skip-external-locking][skip-external-locking]”选项。在某些系统上，lockd锁定管理器不能正确工作，“[--skip-external-locking][skip-external-locking]”选项通知**[mysqld][]**不使用外部锁定。（这意味着，你不能在相同的数据目录上运行2个**[mysqld][]**服务器，如果使用[myisamchk][]，必须谨慎。然而，尝试将该选项用作测试也是有益的）。

* 当**[mysqld][]**看上去正在运行但并未响应时，尝试使用[mysqladmin -u root processlist][mysqladmin]。某些时候，即使你认为**[mysqld][]**处于未响应状态，但实际它并没有处于挂起状态。问题可能是因为所有连接均已使用，或存在某些内部锁定问题。即使在该情况下，[mysqladmin -u root processlist][mysqladmin]通常能够进行连接，并能提供当前连接数量以及各连接状态的有用信息。

* 在运行其他查询的同时，在单独的窗口中运行命令[mysqladmin -i 5 status][mysqladmin]或[mysqladmin -i 5 -r status][mysqladmin]，以生成统计信息。

* 尝试下述方法：

	1. 从**gdb**（或另一个调试器）启动[mysqld][]。请见章节[23.4，“Debugging and Porting MySQL”][23.04.00]。

	2. 运行测试脚本。

	3. 在3个较低层面上输出backtrace（向后跟踪）和局部变量。在**gdb**中，当mysqld在**gdb**内崩溃时，可使用下述命令：  

			backtrace
			info local
			up
			info local
			up
			info local
使用**gdb**，你还能检查与info线程共存的线程，并切换至特定的线程N，其中，N是线程的ID。

* 尝试用Perl脚本模拟你的应用程序，强制MySQL崩溃或行为异常。

* 发送正常的缺陷报告。请见章节[1.7, “How to Report Bugs or Problems”][01.07.00]。应比通常的报告更详细。由于MySQL是为很多人提供服务的，它可能因仅存在于你的计算机上的某事崩溃（例如，与你的特定系统库有关的错误）。

* 如果你遇到与包含变长行的表有关的问题，而且你仅使用[VARCHAR][]列（而不是[BLOB][]或[TEXT][]列），可尝试用[ALTER TABLE][]将所有[VARCHAR][]列更改为[CHAR][]列。这样，就会强制MySQL使用固定大小的行。固定大小的行占用的空间略多，但对损坏的容忍度更高。  
目前的变长行代码在MySQL AB已使用多年，很少遇到问题，但从本质上看，变长行更倾向于出现错误，因此，不妨尝试采用该策略以查看它是否有帮助，这不失为一个好主意。

* 诊断问题时不要将你的服务器硬件排除在外。有缺陷的硬件能够导致数据损坏。对硬件进行故障诊断与排除操作时，尤其应注意内存和硬盘驱动器。

[mysqladmin]:../Chapter_04/04.05.02_mysqladmin_Client_for_Administering_a_MySQL_Server.md	
[05.02.02]:../Chapter_05/05.02.02_The_Error_Log.md
[23.04.00]:../Chapter_23/23.04.00_Debugging_and_Porting_MySQL.md
[delay-key-write]:../Chapter_05/05.01.03_Server_Command_Options.md#delay-key-write
[flush]:../Chapter_05/05.01.03_Server_Command_Options.md#flush
[alter table]:../Chapter_13/13.01.07_ALTER_TABLE_Syntax.md
[myisamchk]:../Chapter_04/04.06.03_myisamchk_MyISAM_Table-Maintenance_Utility.md
[05.00.00]:../Chapter_05/05.00.00_MySQL_Server_Administration.md
[05.02.03]:../Chapter_05/05.02.03_The_General_Query_Log.md
[01.07.00]:../Chapter_01/01.07.00_How_to_Report_Bugs_or_Problems.md
[23.04.00]:../Chapter_23/23.04.00_Debugging_and_Porting_MySQL.md
[23.01.02]:../Chapter_23/23.01.02_The_MySQL_Test_Suite.md
[DWITH_DEBUG]:../Chapter_02/02.09.04_MySQL_Source-Configuration_Options.md#DWITH_DEBUG
[skip-external-locking]:../Chapter_05/05.01.03_Server_Command_Options.md#skip-external-locking
[01.07.00]:../Chapter_01/01.07.00_How_to_Report_Bugs_or_Problems.md
[varchar]:../Chapter_11/11.04.01_The_CHAR_and_VARCHAR_Types.md
[blob]:../Chapter_11/11.04.03_The_BLOB_and_TEXT_Types.md
[text]:../Chapter_11/11.04.03_The_BLOB_and_TEXT_Types.md
[char]:../Chapter_11/11.04.01_The_CHAR_and_VARCHAR_Types.md


###C.5.4.3. MySQL如何处理磁盘空间已满的问题

在本节中，介绍了MySQL如何响应磁盘满错误（如“设备上无剩余空间”），以及响应磁盘配额不足错误（如“写入失败”或“用户的磁盘配额已达到上限”）。

本节介绍的内容与写入MyISAM表有关。它也适用于写入二进制日志文件和二进制索引文件，但对“row”和“record”的应用应被视为“event”。

当出现磁盘满状况时，MySQL将执行下述内容：

* 每分钟检查一次，查看是否有足够空间写入当前行。如果有足够空间，将继续，就像什么也未发生一样。

* 每10分钟将1个消息写入日志文件，提醒磁盘满情况。

为了减轻问题，可采取下述措施：

* 要想继续，仅需释放足够的磁盘空间以插入所有记录。

* 另外，要想中断线程，必须使用[mysqladmin kill][mysqladmin]。MySQL下次检查磁盘时将中断线程（在1分钟左右）。

* 其他线程可能会正在等待导致磁盘满状况的表。如果有数个“已锁定”的线程，杀死正在磁盘满状况下等待的某一线程，以便允许其他线程继续。

对前述行为的例外是，当你使用[REPAIR TABLE][]或[OPTIMIZE TABLE][]，或在批操作中，当创建索引在[LOAD DATA INFILE][]或[ALTER TABLE][]语句之后时。所有这些语句都可能会创建大的临时文件，如果保留这些文件，会导致系统其他部分出现大问题。如果在MySQL执行这些操作的同时磁盘已满，它将删除大的临时文件，并将表标注为崩溃。但对于[ALTER TABLE][]例外，旧表保持不变。

[repair table]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.05
[optimize table]:../Chapter_13/13.07.02_Table_Maintenance_Statements.md#13.07.02.04
[load data infile]:../docs/Chapter_13/13.02.06_LOAD_DATA_INFILE_Syntax.md


###C.5.4.4. MySQL将临时文件储存在哪里

在Unix系统中，MySQL使用TMPDIR环境变量的值作为存储临时文件的目录。如果TMPDIR没有设置，MySQL会使用系统默认的路径，通常为/tmp,/var/tmp,或者/usr/tmp。

在Windows系统中，MySQL按照目录的顺序分别检查TMPDIR、TEMP和TMP环境变量的值。如果有一个变量中被发现有值，那么就不再检查其他变量。如果这些变量（TMPDIR、TEMP或TMP）都没有赋值，MySQL会使用Windows操作系统的默认临时文件目录，通常是C:\windows\temp\。

如果包含你的临时文件目录的文件系统空间太小了，那么可以给**[mysqld][]**设置[--tmpdir][tmpdir]选项来指定一个拥有足够空间的文件系统中的目录。

在MySQL5.6中，“[--tmpdir][tmpdir]”选项可被设置为多个路径的列表，以循环方式使用。在Unix平台上，路径用冒号字符“:”隔开，在Windows平台上，路径用分号字符“;”隔开。

注解
为了有效分散负载，这些路径应该位于不同的磁盘上，而不是同一个磁盘的不同分区。

如果MySQL服务器正作为复制环境中的从服务器使用，不应将“[--tmpdir][tmpdir]”设置为指向基于内存的文件系统的目录，或当服务器主机重启时将清空的目录。对于复制环境中的从服务器，需要在机器重启时仍保留一些临时文件，以便能够复制临时表或执行[LOAD DATA INFILE][]操作。如果在服务器重启时丢失了临时文件目录下的文件，复制将失败。

如果**[mysqld][]**终止了，那么MySQL会整理并删除临时文件。在支持此操作的平台（例如Unix）上，是通过打开文件后，创建一个文件的链接实现的。这种方法的缺点是，文件名称不会出现在临时文件的目录列表中，并且你在临时文件的目录所在的系统中也不会看到一个很大的临时文件。（在此案例中，**lsof +L1**命令对于定位和**[mysqld][]**有关的大文件是非常有帮助的）

当排序时（ORDER BY或GROUP BY），MySQL通常会使用1个或2个临时文件。所需的最大磁盘空间由下述表达式决定：

	(length of what is sorted + sizeof(row pointer))
	* number of matched rows
	* 2
行指针（“row pointer”）的大小通常是4字节，但在以后，对于大的表，该值可能会增加。

对于某些[SELECT][]查询，MySQL还会创建临时SQL表。它们不是隐含表，并具有SQL_*形式的名称。

[ALTER TABLE][]会在与原始表目录相同的目录下创建临时表，有一个例外是在线DDL操作（请见章节[5.5, “Online DDL for InnoDB Tables”][05.05.00]），InnoDB会在临时文件目录创建临时文件。如果临时文件目录不足以容纳临时文件的大小，你可能需要在[tmpdir][]系统变量中再额外设置一个目录。

[tmpdir]:../Chapter_05/05.01.03_Server_Command_Options.md#tmpdir
[select]:../Chapter_13/13.02.09_SELECT_Syntax.md
[05.05.00]:../Chapter_05/05.05.00_Online_DDL_for_InnoDB_Tables.md


###C.5.4.5. 如何保护或更改MySQL Unix套接字文件

对于服务器用来与本地客户端进行通信的Unix套接字文件，其默认位置是/tmp/mysql.sock。（在某些MySQL分发版本中，这个目录可能会不同，例如RPMs的目录是/var/lib/mysql）

在某些版本的Unix上，任何人都能删除/tmp目录或相似目录下的临时文件。如果套接字文件位于此目录中，这可能会引起其他问题。

在大多数Unix版本中，可对/tmp目录进行保护，使得文件只能被其所有者或超级用户（root用户）删除。为此，以root用户身份登录，并使用下述命令在/tmp目录上设置粘着位（sticky bit）：

	shell> chmod +t /tmp
通过执行ls -ld /tmp，可检查是否设置了粘着位。如果最后一个许可字符是“t”，表明设置了粘着位。

另一种方法是改变服务器创建Unix套接字文件的位置。如果修改了，还应让客户端程序知道文件的新位置在哪儿。你能用多种不同方式指定文件位置：

* 在全局变量或本地配置文件中指定路径。例如，将下述行置于文件/etc/my.cnf中：

		[mysqld]
		socket=/path/to/socket
	
		[client]
		socket=/path/to/socket

* 请见章节[4.2.3.3, “Using Option Files”][04.02.03.03]。

* 在运行客户端程序时，在命令行上为**[mysqld\_safe][mysqld_safe]**指定“[--socket][socket]”选项。

* 将MYSQL_UNIX_PORT环境变量设置为Unix套接字文件的路径。

重新从源码编译MySQL，以使用不同的默认Unix套接字文件路径。
当你运行CMake时，将选项（[MYSQL\_UNIX\_ADDR][mysql_unix_addr]）设置为将要使用的路径。请见章节[2.9.4, “MySQL Source-Configuration Options”][02.09.04]。

用下述命令连接服务器，能够测试新的套接字位置是否生效：

	shell> mysqladmin --socket=/path/to/socket version

[04.02.03.03]:../Chapter_04/04.02.03_Specifying_Program_Options.md#04.02.03.03
[mysqld_safe]:../Chapter_04/04.03.02_mysqld_safe_MySQL_Server_Startup_Script.md
[socket]:../Chapter_04/04.02.02_Connecting_to_the_MySQL_Server.md#socket
[mysql_unix_addr]:../Chapter_02/02.09.04_MySQL_Source-Configuration_Options.md#mysql_unix_addr
[02.09.04]:../Chapter_02/02.09.04_MySQL_Source-Configuration_Options.md


###C.5.4.6. 时区问题

如果SELECT NOW()返回的是UTC（GTM）时间，而不是本地时间，你应该将服务器的时区设置为你当前所在的时区。如果[UNIX\_TIMESTAMP()][unix_timestamp]返回错误值，上述方式同样适用。应为服务器所运行的环境进行这类设置，例如，在[mysqld\_safe][mysqld_safe]或[mysql.server][]中。请见章节[2.12, “Environment Variables”][02.12.00]。

也可以对[mysqld\_safe][mysqld_safe]使用“[--timezone=timezone\_name][timezone=timezone_name]”选项，为服务器设置时区。也可以在启动[mysqld][]之前，通过设置TZ环境变量完成该设置。

“[--timezone][timezone]”或TZ的允许值与系统有关。关于可接受的值，请参见操作系统文档。

[unix_timestamp]:../Chapter_12/12.07.00_Date_and_Time_Functions.md#unix_timestamp
[mysql.server]:../Chapter_04/04.03.03_mysql.server_MySQL_Server_Startup_Script.md
[02.12.00]:../Chapter_02/02.12.00_Environment_Variables.md
[timezone]:../Chapter_04/04.03.02_mysqld_safe_MySQL_Server_Startup_Script.md#option_mysqld_safe_timezone