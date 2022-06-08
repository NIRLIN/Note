# SpringCloud学习笔记

# 0.目录

[TOC]

# 1.引言

## 1.微服务核心问题

1. 服务很多，客户端如何访问？
2. 这么多服务，服务之间如何通信？
3. 这么多服务，如何治理？
4. 服务挂了怎么办？

## 2.万变不离其宗

1. API网关
2. 通信：HTTP、RPC
3. 注册和发现
4. 熔断机制

## 3.通用流程

1. 导入依赖
2. 编写配置
3. 开启注解--@Enablexxx

**之所以要解决这些问题，在于网络不可靠**+

## 4.CAP原则

**CAP原则又称CAP定理，指的是在一个分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）。CAP 原则指的是，这三个要素最多只能同时实现两点，不可能三者兼顾。**

- Zookeeper保证的是CP，即一致性与分区容错性

- Eureka保证的是AP，即可用性与分区容错性

    

# 2.创建模块

## 1.数据库

1.创建表`payment`

```sql
CREATE TABLE `payment`(
`id` BIGINT NOT NULL AUTO_INCREMENT COMMENT 'id',
`serial` VARCHAR(200) DEFAULT '',
PRIMARY KEY(`id`)
)ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8
```

