---
layout: post_layout
title: 抽奖项目总结
time: On Wednesday, May 3, 2017
location: BeiJing
pulished: true
excerpt_separator: "```"
---


### 1. 功能突破
* 文本框输入时调用移动端键盘的搜索按钮(将文本框放在form里面，设置onsubmit="false")，同时将input的属性设置为搜索search：
```
<form id="form" onsubmit="return false;">
	<input type="search" id="find" class="l0">
</form>
```
- 获取文本框搜索完成状态(方法如同pc端)：
```
$('#find,#find1').keyup(function(e){
		if(e.keyCode=='13'){}
})
```
* 文本框聚焦placeholder消失，失去焦点placeholder出现，使用行间的focus和blur事件以及this，代码如下：
```
<input type="search"placeholder="关键词 / 拼音" onfocus="this.placeholder=''" onblur="this.placeholder='关键词 / 拼音'">
```
### 2. 问题
* 使用flexible.js做移动端的适配时，不需要在顶部写关于视口的标签 (meta name="viewport"),因为在js里已经对视口做过处理；如果在 app 内部 嵌套html的话，需要配合前端人员解决，务必让他们去掉页面上关于viewport的设置，如果没有去掉的话，可在本地js中去掉app中写的视口，否则关于data-dpr的字体设置失效，页面字体变小。

### 3. 改进
* 在编写代码之前确认嵌入的app中是否写入viewport，如果有，在不影响页面正常显示的情况下进行删除。
* 在编写代码之前确认页面所需参数，考虑在app，网页、微信下的使用情况。


### 4. 提高
* 移动端适配时，鉴于可能存在嵌套app的情况，需要有图片显示的地方慎用img标签（因为app可能对图片做了点击放大处理，影响用户体验），尽量使用背景图，并且设置background-size：cover；或者background-size：100% 100%；