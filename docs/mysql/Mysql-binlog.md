> mysqlbinlog 工具查看二进制日志内容 

```sql
开启binlog日志 
通过修改my.ini配置文件，在[mysqld] 下面添加 log-bin=日志名称：

#########################################################
[mysqld]
 
# The TCP/IP Port the MySQL Server will listen on
port=3306
 
log-bin=mysql-bin
##########################################################
！！！修改完成记得重启


##查询mysql看看是否开启了binlog  ON 代表开启；OFF 代表没有开启
mysql> show variables like 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | ON    |
+---------------+-------+
1 row in set (0.02 sec)
```

### 查看当前数据库是使用哪种模式( mysql binlog的三种工作模式 )

##### 1、ROW:基于行的复制,日志中会记录每一行数据被修改的形式

```
不记录sql语句上下文相关信息，仅保存哪条记录被修改。

优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么。所以rowlevel的日志内容会非常清楚的记录下每一行数据修改的细节。

缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容。
```

##### 2、Statement:基于sql语句的复制,每一条会修改数据的sql都会记录到master的bin-log中

```
只记录每一条会修改数据的sql都会记录在binlog

优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。

缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的一些相关信息，
以保证所有语句能在slave和在master端执行时候相同的结果。
```

##### 3、MIXED:混合模式复制，

```
MIXED实际上就是Statement与Row的结合
```

#### 查看当前是什么模式

```sql
mysql> show variables like 'binlog_format';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| binlog_format | MIXED |
+---------------+-------+
1 row in set (0.03 sec)
```

##### 查看数据库日志情况

```sql
mysql> show master status;
+------------------+-----------+--------------+------------------+-----------------------------------------------+
| File             | Position  | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set                             |
+------------------+-----------+--------------+------------------+-----------------------------------------------+
| mysql-bin.000009 | 124741075 |              |                  | f7d4be1d-703f-11ea-bbb8-002590aaa26c:1-503775 |
+------------------+-----------+--------------+------------------+-----------------------------------------------+
1 row in set (0.00 sec)


mysql> show master logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       177 |
| mysql-bin.000002 |       437 |
| mysql-bin.000003 |       685 |
| mysql-bin.000004 |  31799445 |
| mysql-bin.000005 | 150720023 |
| mysql-bin.000006 |      2055 |
| mysql-bin.000007 |  64445417 |
| mysql-bin.000008 |     39689 |
| mysql-bin.000009 | 124741075 |
+------------------+-----------+
9 rows in set (0.00 sec)


mysql> show binlog events in 'mysql-bin.000001';
+------------------+-----+----------------+-----------+-------------+---------------------------------------+
| Log_name         | Pos | Event_type     | Server_id | End_log_pos | Info                                  |
+------------------+-----+----------------+-----------+-------------+---------------------------------------+
| mysql-bin.000001 |   4 | Format_desc    |         1 |         123 | Server ver: 5.7.29-log, Binlog ver: 4 |
| mysql-bin.000001 | 123 | Previous_gtids |         1 |         154 |                                       |
| mysql-bin.000001 | 154 | Stop           |         1 |         177 |                                       |
+------------------+-----
```

## 重置binlog命令 reset master

```sql
mysql> reset master;
Query OK, 0 rows affected (0.01 sec)
```

### mysqlbinlog 常用命令详解

```php
常用参数：
--start-datetime=datetime   从二进制日志中第1个日期时间等于或晚于datetime参量的事件开始读取。该值格式应符合DATETIME或TIMESTAMP数据类型。
--stop-datetime=datetime    从二进制日志中第1个日期时间等于或晚于datetime参量的事件起停止读。
--start-position=N          从二进制日志中第1个位置等于N参量时的事件开始读。
--stop-position=N           从二进制日志中第1个位置等于和大于N参量时的事件起停止读。
-d 与 --database            效果相同，指定一个数据库名称。
--offset=N，-o N            跳过前N个条目。
--base64-output=DECODE-ROWS 会显示出row模式带来的sql变更
```

##### 开始时间和结束时间约束

```powershell
mysqlbinlog --start-datetime='2020-03-30 00:09:00' --stop-datetime='2020-03-30 10:00:00' -d 库名 mysql-bin.000007
```

##### 查看update delete insert SQL语句如下

```powershell
[root@WxHost-65ed3d data]# mysqlbinlog  --base64-output=DECODE-ROWS -v  -d 数据库名 mysql-bin.000004|grep DELETE
DELETE FROM`users_failed_login` WHERE lastupdate < '1585324147'
DELETE FROM`users_failed_login` WHERE lastupdate < '1585324158'
DELETE FROM `users_failed_login` WHERE `id` =  ''




##### 这里被别人删除了数据
[root@ro data]# mysqlbinlog --start-datetime='2020-03-30 16:00:00' --stop-datetime='2020-03-30 16:10:00' --base64-output=decode-rows --database=数据库名 mysql-bin.000007|grep delete
delete from order_list where uid in (547,944)
delete from order_list where sid in (547,944)
delete from member where id in (547,944)
delete from member_bank where uid in (547,944)
```

##### 基于position值

```powershell
mysqlbinlog --start-position=1000--stop-position=1010 -d 库名 mysql-bin.00007
```

###### 输出到文件 decode-rows -v 两个要配合一起使用 -v 代表换行显示这些语句，如果没有-v 你看不到具体的语句

```powershell
[root@ro data]# mysqlbinlog --start-datetime='2020-03-30 16:00:00' --stop-datetime='2020-03-30 16:10:00' --base64-output=decode-rows --database=数据库名 mysql-bin.000007|grep delete >/tmp/binlog.mysql
```

参考命令详解

```
mysqlbinlog [options] log-files

--base64-output=name            # binlog输出语句的base64解码 分为三类：默认是值auto ,仅打印base64编码的需要的信息，如row-based 事件和事件的描述
                                  信息。never 仅适用于不是row-based的事件 decode-rows   配合--verbose选项一起使用解码行事件到带注释的伪SQL语句
--bind-address=name             # 绑定的IP地址
--character-sets-dir=name       # 字符集文件的目录
-d, --database=name             # 仅列出此数据库的条目（仅限本地日志）
--rewrite-db=name               # 将行事件重写为指向，以便将其应用于新数据库
-#, --debug[=#]                 # 输出debug信息，用于调试。默认值为：d:t,/tmp/mysqldump.trace
--debug-check                   # 当程序退出时打印一些调试信息
--debug-info                    # 当程序退出时打印调试信息和内存和CPU使用统计信息
--default-auth=name             # 要使用的默认身份验证客户端插件
-D, --disable-log-bin           # 禁用binlog日志，若开启--to-last-log并发送输出文件到相同的mysql server。这种方式避免无限循环。在规避数据库崩
                                  溃恢复数据的时候有用。注意：需要super权限来使用此选项
-F, --force-if-open             # 若binlog非正常关闭，强制开启binlog，默认是on可使用--skip-force-if-open关闭
-f, --force-read                # 强制读取未知的binlog事件
-H, --hexdump                   # 使用十六进制和ASCII码导出输出的信息
-h, --host=name                 # 获取binlog的服务名
-i, --idempotent                # 通知服务器使用幂等模式应用行事件
-l, --local-load=name           # 准备LOAD DATA INFILE的本地临时文件指定目录
-o, --offset=#                  # 跳过前n个条目
-p, --password[=name]           # 连接到服务器的密码
--plugin-dir=name               # 客户端插件的目录
-P, --port=#                    # 用于连接的端口，0表示默认值。端口使用的优先级：my.cnf，$ MYSQL_TCP_PORT,/etc/services，内置默认值(3306)
--protocol=name                 # 用于连接的协议(tcp, socket, pipe, memory)
-R, --read-from-remote-server   # 从MySQL服务器读取二进制日志，是read-from-remote-master = BINLOG-DUMP-NON-GTIDS的别名。
--read-from-remote-master=name  
--raw                           # 配合参数-R一起使用,输出原始的binlog数据而不是SQL语句
-r, --result-file=name          # 输出指定的文件，和--row一起使用，此时是数据文件的前缀
--secure-auth                   # 如果客户端使用旧的（4.1.1之前的）协议，则拒绝连接到服务器
--server-id=#                   # 提取给定id的服务器创建的binlog条目                
--server-id-bits=#              # 设置server-id中的有效位数
--set-charset=name              # 添加'SET NAMES character_set' 到输出
-s, --short-form                # 仅适用于常规查询，没有额外的信息和row-based事件信息。仅用于测试，不使用于生产环境。如果你想抑制
                                  base64-output，考虑使用--base64-output = never代替
-S, --socket=name               # 连接时使用的socket文件
--ssl-mode=name                 # SSL连接模式
--ssl-ca=name                   # PEM格式的CA文件
--ssl-capath=name               # CA目录
--ssl-cert=name                 # PEM格式的X509证书
--ssl-cipher=name               # 要使用的SSL密码
--ssl-key=name                  # PEM格式的X509密钥
--ssl-crl=name                  # 证书吊销列表
--ssl-crlpath=name              # 证书吊销列表路径
--tls-version=name              # 要使用的TLS版本，允许值为：tlsv1、tlsv1.1
--start-datetime=name           # binlog文件读取的起始时间点，可接受datetime和timestamp类型，格式2004-12-25 11:25:56
-j, --start-position=#          # 从N位置开始读取binlog。适用于命令行上传递的第一个binlog
--stop-datetime=name            # binlog文件读取的结束时间点
--stop-never                    # 等待来自服务器的更多数据，而不是在最后一个日志结束时停止。隐式地设置--to-last-log ，但不是在最后一个日志结
                                  束时停止而是继续等待直到服务器断开连接
--stop-never-slave-server-id=#  # 从服务器server_id使用--read-from-remote-server --stop-never。该选项不能和--connection-server-id一起使用
--connection-server-id=#        # 从服务器server_id使用--read-from-remote-server,该选项不能和--stop-never-slave-server-id一起使用
--stop-position=#               # binlog文件结束的时间点
-t, --to-last-log               # 和-r一起使用，不会在请求的binlog结尾处停止，而是继续打印，直到mysql服务器的最后一个binlog结束。如果将输出发
                                  送到同一个MySQL服务器，可能会导致无休止的循环
-u, --user=name                 # 连接到服务器用户名
-v, --verbose                   # 重新构建伪SQL语句的行信息输出，-v -v会增加列类型的注释信息
-V, --version                   # 打印版本信息
--open-files-limit=#            # 打开文件的限制，用于保留文件描述符以供此程序使用
-c, --verify-binlog-checksum    # 验证binlog的事件信息
--binlog-row-event-max-size=#   # 指定基于行的binlog的大小，改值必须是256的倍数
--skip-gtids                    # 不要保留全局事务标识符，而是让服务器像执行新事务一样执行这些事务。
--include-gtids=name            # 打印提供了全局事务标识符的事件
--exclude-gtids=name            # 打印所有事件，但提供全局事务标识符的事件除外
```