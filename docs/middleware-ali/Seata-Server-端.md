## 概述

 Server端存储模式（store.mode）现有file、db、redis三种（后续将引入raft,mongodb），file模式无需改动，直接启动即可。 

> 注：file模式为单机模式，全局事务会话信息内存中读写并持久化本地文件root.data，性能较高;
>
> db模式为高可用模式，全局事务会话信息通过db共享，相应性能差些;
>
> redis模式Seata-Server 1.3及以上版本支持,性能较高,存在事务信息丢失风险,请提前配置合适当前场景的redis持久化配置.