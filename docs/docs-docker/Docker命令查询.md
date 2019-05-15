# Docker 命令查询

## 基本语法

Docker 命令有两大类，客户端命令和服务端命令。前者是主要的操作接口，后者用来启动 Docker Daemon。

* 客户端命令
* 服务端命令

可以通过 `man docker` 或 `docker help` 来查看这些命令。

## 客户端命令选项

```xml
--config=""：指定客户端配置文件，默认为 `/.docker`；
-D=true|false：是否使用 debug 模式。默认不开启；
-H, --host=[]：指定命令对应 Docker 守护进程的监听接口，可以为 unix 套接字（unix:///path/to/socket），文件句柄（fd://socketfd）或 tcp 套接字（tcp://[host[:port]]），默认为 unix:///var/run/docker.sock；
-l, --log-level="debug|info|warn|error|fatal"：指定日志输出级别；
--tls=true|false：是否对 Docker 守护进程启用 TLS 安全机制，默认为否；
--tlscacert= /.docker/ca.pem：TLS CA 签名的可信证书文件路径；
--tlscert= /.docker/cert.pem：TLS 可信证书文件路径；
--tlscert= /.docker/key.pem：TLS 密钥文件路径；
--tlsverify=true|false：启用 TLS 校验，默认为否。
```

