---
layout: post_layout
title: mysql(阿里云)插入emoji表情
time: On Tuesday, Sept 5, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---


<a href="https://help.aliyun.com/knowledge_detail/41702.html#RDS">MySQL使用utf8mb4字符集存储emoji表情</a>

RDS MySQL使用utf8mb4字符集存储emoji表情

-  基本原则
-  三个条件的说明
    - 应用客户端
    - 应用到 RDS MySQL 实例的连接
    - RDS 实例配置
-  通过 set names 命令设置会话字符集

---
***
___
1.基本原则

如果要实现存储 emoji 表情到 RDS MySQL 实例，需要客户端、到 RDS MySQL 实例的连接、RDS 实例内部 3 个方面统一使用或者支持 utf8mb4 字符集。
注：关于 utf8mb4 字符集，请参考 MySQL 官方文档

2.三个条件的说明

① 应用客户端

客户端需要保证输出的字符串的字符集为 utf8mb4。

② 应用到 RDS MySQL 实例的连接

以常见的 JDBC 连接为例：

对于 JDBC 连接，需要使用 MySQL Connector/J 5.1.13（含）以上的版本。

JDBC 的连接串中，建议不配置 characterEncoding 选项。

注：关于 MySQL Connector/J 5.1.13，请参考 MySQL 官方 Release Notes

3.RDS 实例配置

    1. 在控制台  参数配置 中修改 character_set_server 参数为 utf8mb4。

    2. 设置库的字符集为 utf8mb4

    3. 设置表的字符集为 utf8mb4

 通过 set names 命令设置会话字符集

对于 JDBC 连接串设置了 characterEncoding 为 utf8 或者做了上述配置仍旧无法正常插入emoji数据的情况，建议在代码中指定连接的字符集为 utf8mb4，样例代码如下：
```java
 String query = "set names utf8mb4";
 stat.execute(query);
//spring 里使用
<property name="connectionInitSqls" value="set names utf8mb4"/>
```