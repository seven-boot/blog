## 1、概述

Sentinel 提供一个轻量级的开源控制台，它提供机器发现以及健康情况管理、监控（单机和集群），规则管理和推送的功能。这里，我们将会详细讲述如何通过[简单的步骤](https://github.com/alibaba/Sentinel/wiki/控制台#2-启动控制台)就可以使用这些功能。

Sentinel 控制台包含如下功能:

- [**查看机器列表以及健康情况**](https://github.com/alibaba/Sentinel/wiki/控制台#4-查看机器列表以及健康情况)：收集 Sentinel 客户端发送的心跳包，用于判断机器是否在线。
- [**监控 (单机和集群聚合)**](https://github.com/alibaba/Sentinel/wiki/控制台#5-监控)：通过 Sentinel 客户端暴露的监控 API，定期拉取并且聚合应用监控信息，最终可以实现秒级的实时监控。
- [**规则管理和推送**](https://github.com/alibaba/Sentinel/wiki/控制台#6-规则管理及推送)：统一管理推送规则。
- [**鉴权**](https://github.com/alibaba/Sentinel/wiki/控制台#鉴权)：生产环境中鉴权非常重要。这里每个开发者需要根据自己的实际情况进行定制。

> 注意：Sentinel 控制台目前仅支持单机部署。Sentinel 控制台项目提供 Sentinel 功能全集示例，不作为开箱即用的生产环境控制台，若希望在生产环境使用请根据[文档](https://github.com/alibaba/Sentinel/wiki/在生产环境中使用-Sentinel)自行进行定制和改造。

## 2、启动控制台

### 2.1、获取 Sentinel 控制台

从 [release 页面](https://github.com/alibaba/Sentinel/releases) 下载最新版本的控制台 jar 包。

### 2.2、启动

> **注意**：启动 Sentinel 控制台需要 JDK 版本为 1.8 及以上版本。

使用如下命令启动控制台：

```bash
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```

其中 `-Dserver.port=8080` 用于指定 Sentinel 控制台端口为 `8080`。

从 Sentinel 1.6.0 起，Sentinel 控制台引入基本的**登录**功能，默认用户名和密码都是 `sentinel`。可以参考 [鉴权模块文档](https://github.com/alibaba/Sentinel/wiki/控制台#鉴权) 配置用户名和密码。

## 3、客户端接入控制台

控制台启动后，客户端需要按照以下步骤接入到控制台。

### 3.1 引入JAR包

客户端需要引入 Transport 模块来与 Sentinel 控制台进行通信。您可以通过 `pom.xml` 引入 JAR 包:

```
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-transport-simple-http</artifactId>
    <version>x.y.z</version>
</dependency>
```

### 3.2 配置启动参数

启动时加入 JVM 参数 `-Dcsp.sentinel.dashboard.server=consoleIp:port` 指定控制台地址和端口。若启动多个应用，则需要通过 `-Dcsp.sentinel.api.port=xxxx` 指定客户端监控 API 的端口（默认是 8719）。

从 1.6.3 版本开始，控制台支持网关流控规则管理。您需要在接入端添加 `-Dcsp.sentinel.app.type=1` 启动参数以将您的服务标记为 API Gateway，在接入控制台时您的服务会自动注册为网关类型，然后您即可在控制台配置网关规则和 API 分组。

除了修改 JVM 参数，也可以通过配置文件取得同样的效果。更详细的信息可以参考 [启动配置项](https://github.com/alibaba/Sentinel/wiki/启动配置项)。

### 3.3 触发客户端初始化

**确保客户端有访问量**，Sentinel 会在**客户端首次调用的时候**进行初始化，开始向控制台发送心跳包。

> 注意：您还需要根据您的应用类型和接入方式引入对应的 [适配依赖](https://github.com/alibaba/Sentinel/wiki/主流框架的适配)，否则即使有访问量也不能被 Sentinel 统计。

## 4. 查看机器列表以及健康情况

当您在机器列表中看到您的机器，就代表着您已经成功接入控制台；如果没有看到您的机器，请检查配置，并通过 `${user.home}/logs/csp/sentinel-record.log.xxx` 日志来排查原因，详细的部分请参考 [日志文档](https://github.com/alibaba/Sentinel/wiki/日志)。