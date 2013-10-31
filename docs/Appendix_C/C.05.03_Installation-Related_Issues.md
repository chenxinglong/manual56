##C.5.3. 与安装有关的问题
[+/-]

[C.5.3.1. 文件权限问题][C.05.03.01]

[C.05.03.01]:./C.05.03_Installation-Related_Issues.md#C.05.03.01


###C.5.3.1. 文件权限问题
如果遇到与文件权限有关的问题，可能启动mysqld时UMASK环境变量设置得不正确。例如，当你创建表时，MySQL可能会发出下述错误消息：

	ERROR: 无法找到文件：'path/with/filename.frm' (Errcode: 13)

UMASK的默认值是0660。通过下述方式启动**[mysqld\_safe][mysqld_safe]**，可改变该行为：

	shell> UMASK=384  # = 600 in octal
	shell> export UMASK
	shell> mysqld_safe &

在默认情况下，MySQL用0700的权限创建数据库目录。你可以通过设置UMASK\_DIR变量更改该行为。如果你设置了它的值，将使用组合的UMASK和UMASK\_DIR值创建新目录。例如，如果你打算为所有新的目录授予组访问权限，可：

	shell> UMASK_DIR=504  # = 770 in octal
	shell> export UMASK_DIR
	shell> mysqld_safe &
如果是以0开始的，MySQL将认为UMASK和UMASK\_DIR的值均采用八进制形式。
MySQL assumes that the value for UMASK or UMASK_DIR is in octal if it starts with a zero.

请见章节[2.12, “Environment Variables”][02.12.00]。

[mysqld_safe]:../Chapter_04/04.03.02_mysqld_safe_MySQL_Server_Startup_Script.md
[02.12.00]:../Chapter_02/02.12.00_Environment_Variables.md