---
layout: post_layout
title: oracle或mysql获取分组后每组的前三条数据
time: On Friday, May 11, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

### oracle或mysql获取分组后每组的前三条数据

### 项目中实例：
mysql:
```java
select c.*,ca.name cName,ca.en cEn
from
(
select t1.*,(select count(*)+1 from db_表A where cid=t1.cid and id>t1.id) as group_id
from db_表A t1
) c
left join db_表B ca on c.cid = ca.id
where c.delete_flag=0 and c.group_id<=10
GROUP BY c.cid,c.id
```


### 原文：https://blog.csdn.net/sanyuesan0000/article/details/47060853
mysql :
```java
select a.*   
from  
(  
select t1.*,(select count(*)+1 from 表 where 分组字段=t1.分组字段 and 排序字段<t1.排序字段) as group_id  
from 表 t1  
) a  
where a.group_id<=3  
```
oracle:
```java
SELECT t.*           
   FROM (SELECT ROW_NUMBER() OVER(PARTITION BY 分组字段 ORDER BY 排序字段 DESC) rn,           
         b.*           
         FROM 表 b) t           
  WHERE t.rn <= 3  ;  
```

