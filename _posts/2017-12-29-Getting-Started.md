---
layout: post_layout
title: mybatis执行批量更新batch update 的方法
time: On Friday, Dec 29, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---

### mysql数据库：
mysql数据库采用一下写法即可执行，但是数据库连接必须配置：<font color="#4590a3" size = "6px">&allowMultiQueries=true</font>
例如：jdbc:mysql://192.168.1.236:3306/test?useUnicode=true&amp;characterEncoding=UTF-8&allowMultiQueries=true

```java
<update id="batchUpdate"  parameterType="java.util.List">


          <foreach collection="list" item="item" index="index" open="" close="" separator=";">
                update test
                <set>
                  test=${item.test}+1
                </set>
                where id = ${item.id}
        </foreach>


    </update>
```

### oracle数据库：
```java
<update id="batchUpdate"  parameterType="java.util.List">


      <foreach collection="list" item="item" index="index" open="begin" close="end;" separator=";">
                update test
                <set>
                  test=${item.test}+1
                </set>
                where id = ${item.id}
      </foreach>


    </update>
```
