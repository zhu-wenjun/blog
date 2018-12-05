---
title: "Kafka笔记"
date: 2018-11-22T10:50:18+08:00
draft: true
---

**分布式流平台具有3种关键的功能<br>**
1. 发布（publish）和订阅（subscribe）记录流(streams of records)<br>
2. 存储记录流<br>
3. 处理产生的记录流
![Kafka_diagram](http://kafka.apache.org/images/kafka_diagram.png)

## Kafka概念
1. Kafka作为一个**集群**运行在一个或多个可跨多个数据中心的服务器上<br>
2. Kafka集群用topic分类存储记录流<br>
3. 每个记录由一个key，一个value和一个timestamp组成<br>

## Kafka应用场景
1. 在**系统**或**应用程序**之间构建能获取可靠数据的实时流数据通道<br>
2. 构建传输或响应数据流的实时流应用程序

*To understand how Kafka does these things, let's dive in and explore Kafka's capabilities from the bottom up.* <br>
要了解Kafka如何做这些事情，让我们深入探讨Kafka的能力<br>

## Kafka APIs
[Producer API](http://kafka.apache.org/documentation.html#producerapi)允许一个应用程序发布一个记录流到一个或多个Kafka topics<br>
[Consumer API](http://kafka.apache.org/documentation.html#consumerapi)允许一个应用程序订阅一个或多个topics并处理它们产生的记录流<br>
[Streams API](http://kafka.apache.org/documentation/streams/) 允许把一个应用程序作为stream processor。stream processor 从一个或多个topics消费一个输入流，并给一个或多个输出topics生产一个输出流。 高效传输输入流到输出流.<br>
[Connector API](http://kafka.apache.org/documentation.html#connect) 允许构建并运行可重用的producer或consumer, 用于连接Kafka topics到已存在的应用程序或者数据系统。比如关系型数据库的连接器可以捕获表的每个更改。<br> 
![kafka_apis](http://kafka.apache.org/21/images/kafka-apis.png )

Kafka中server和client的通信使用[TCP](https://kafka.apache.org/protocol.html)协议.client可以使用多种[语言](https://cwiki.apache.org/confluence/display/KAFKA/Clients)实现

## Topics 和 Logs
Topic: Kafka为记录流提供的核心抽象
