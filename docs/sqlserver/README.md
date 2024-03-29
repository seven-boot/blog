## 概述

Spring 的主要作用就是为代码“解耦”，降低代码间的耦合度。

根据功能的不同，可以将一个系统中的代码分为 **主业务逻辑** 与 **系统级业务逻辑** 两类。它们各自具有鲜明的特点：主业务代码间逻辑联系紧密，有具体的专业业务应用场景，复用性相对较低；系统级业务相对功能独立，没有具体的专业业务应用场景，主要是为主业务提供系统级服务，如日志、安全、事务等，复用性强。

Spring 根据代码的功能特点，将降低耦合度的方式分为了两类：IoC 与 AOP。IoC 使得主业务在相互调用过程中，不用再自己维护关系了，即不用再自己创建要使用的对象了。而是由 Spring 容器统一管理，自动“注入”。而 AOP 使得系统级服务得到了最大复用，且不用再由程序员手工将系统级服务“混杂”到主业务逻辑中了，而是由 Spring 容器统一完成“织入”。

Spring 是于 2003 年兴起的一个轻量级的 Java 开发框架，它是为了解决企业应用开发的复杂性而创建的。Spring 的核心是控制反转（IoC）和面向切面编程（AOP）。简单来说，Spring 是一个分层的 Java SE/EE full-stack(一站式)轻量级开源框架。





字符串截取

```
SELECT top 5
right(left(Area,len(Area) - (charindex(';', reverse(Area)))), charindex(';', Area)-1)
from Sale_SaleBlue
where Cancel = 0 and Area is not null and AddDate = CONVERT(varchar(10), getdate(), 112)
GROUP BY right(left(Area,len(Area) - (charindex(';', reverse(Area)))), charindex(';', Area)-1)
ORDER BY count(*) desc

SELECT charindex(';', '安徽;合肥;瑶海')
SELECT len('安徽;合肥;瑶海') - (charindex(';', reverse('安徽;合肥;瑶海')))

SELECT SUBSTRING('安徽;合肥;瑶海', charindex(';', '安徽;合肥;瑶海') + 1, len('安徽;合肥;瑶海') - (charindex(';', reverse('安徽;合肥;瑶海'))-1))

SELECT SUBSTRING('安徽;合肥;瑶海',4, 2)

SELECT right(left('安徽;合肥;瑶海',len('安徽;合肥;瑶海') - (charindex(';', reverse('安徽;合肥;瑶海')))), charindex(';', '安徽;合肥;瑶海')-1)
```

