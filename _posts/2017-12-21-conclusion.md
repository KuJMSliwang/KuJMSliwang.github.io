---
layout: post_layout
title: 项目中实例思路记载
time: On Thursday, December 21, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---


* 场景：病例下面有两个方案，每个方案下都有点赞功能，每人只能点赞一次，且只能给一个方案点赞
* 思路：点一个方案，另一个方案如果之前点过的话就取消
* 步骤：
	* 传参时要把取消的id也传过来
	* 查询该用户是否有可取消的赞，有的话删除，同时更新点赞数量
	* 添加方案的点赞，同时更新点赞数量
	* 未完（以后有小的思路值得记录，往此篇文章下记录）待续......