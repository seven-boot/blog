## 概述

1. 大多应用中，可通过消息服务中间件来提升系统异步通信、拓展解耦能力

2. 消息服务中两个重要概念：

   **消息代理（message broker）**和**目的地（destination）**

   当消息发送者发送消息之后，将由消息代理接管，消息代理保证消息传递到指定目的地

3. 消息队列主要有两种形式的目的地
   1. 队列（queue）：点对点消息通信
   2. 主题（topic）：发布（Public）/订阅（Subscribe）消息通信

## 选型对比

提供了五种消息模型：

1、direct exchange

2、fanout exchange

3、topic exchange

4、headers exchange

5、system exchange