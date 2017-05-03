---
layout: post_layout
title: 抽奖项目总结
time: On Wednesday, May 4, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---


抽奖前逻辑(两个模块)
- 模块1
    - 1.1 更新用户手机号，填后才可抽奖
    - 1.2 验证该手机号是否已被使用
- 模块2
    - 2.1 查用户表里有手机号，无：先填写手机号（根据uid找对应用户有没有手机号，以防用链接跳过a步骤）
    - 2.2 查答题里有无该uid，无：返回您还未进行答题，请先答题
    - 2.3 查抽奖表里有无该uid，有：返回您已抽过奖，谢谢

![cmd-markdown-logo](https://www.zybuluo.com/static/img/logo.png)

抽奖逻辑（5%的中奖率，确保每20个人里中一个）

> * 先生成一个幸运数字，在0-19之间产生，每20个人次循环一次
> * 在这20个人次里，进来的人次数（对20求余）和幸运数字相等，则为中奖
> * 将幸运数字存起来，每次都从库里查，确定重启tomcat时，幸运数字发生变化，来确保20人次必中一个

![cmd-markdown-logo](https://www.zybuluo.com/static/img/logo.png)

答题（批量插入）

> * 1、在批量插入前，不管有没有填写姓名，都要先添加用户（后面用来做用户是否答题的判断）
> * 2、接受参数，需要前端组装json字符串
> * 3、转化已建对象集合
```java
//json转化list对象
List<QaUserTopic> list = JSONArray.toList(JSONArray.fromObject(answerJson), new QaUserTopic(), new JsonConfig());
```
> * 4、sql语句里做了特殊处理，如下：
```mybatis
<insert id="batchInsert" parameterType="com.wsyk_oculist_web.bean.QaUserTopicBean">
      insert into qa_user_topic
      (uid, topic_id, answer_id, extend_answer)
      values
      <foreach collection="answerList" index="index" item="answer" separator=",">
      (
      #{answer.uid}, #{answer.topicId},
      #{answer.answerId},
      #{answer.extendAnswer}
      )
      </foreach>
  </insert>
  ```



##  项目总结（完成很顺利）

- [ ] 前期准备工作挺足，在项目正式启动前，表已建好，数据也已导入
- [ ] 提前有数据，后来改点数据，表基本没有太大变化，对接口的改动量来说很小
- [ ] 前期分析好了工作量，项目也在已有的项目里直播开发，省去了因环境问题导致的时间延长
- [ ] 功能短小精悍，好测试，容易找问题
- [x] 拓展想法：这次开发很顺利，以后希望借鉴一下此次开发模式。之前开发的项目工期长，任务多，测试复杂。如下建议：
- ①把每次的里程碑做一个修改，按功能划分里程碑，两周一个节点
- ②每个里程碑都是功能模块，也就是说前后端要对接口，接口对完，所以里程碑的功能模块得看时长评估
- ③做功能模块和时间划分时，在开会汇报前，希望团队内部做一次项目梳理，按开发甘特图来，此时可做修改
- ④内部梳理越详细越好，一早上或者一下午（按之前两个月的工时算的话）
- ⑤每个里程碑按上线的标准走，功能少，测试做足
- ⑥需要整理一份上线的sop，上线前参照这个流程走，可以很好的预估时间和避免一些不必要的bug


