## 安装 supervisor

```
pip install supervisor
```

## 生成配置文件

```
echo_supervisord_conf > /etc/supervisord.conf
```

## 管理命令

```
supervisorctl stop program_name  # 停止某一个进程，program_name 为 [program:x] 里的 x

supervisorctl start program_name  # 启动某个进程

supervisorctl restart program_name  # 重启某个进程

supervisorctl stop groupworker:  # 结束所有属于名为 groupworker 这个分组的进程 (start，restart 同理)

supervisorctl stop groupworker:name1  # 结束 groupworker:name1 这个进程 (start，restart 同理)

supervisorctl stop all  # 停止全部进程，注：start、restartUnlinking stale socket /tmp/supervisor.sock
、stop 都不会载入最新的配置文件

supervisorctl reload  # 载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程

supervisorctl update  # 根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启
```

