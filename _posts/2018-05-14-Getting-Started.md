---
layout: post_layout
title: seo优化
time: On Monday, May 14, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

## seo优化

* 1、网站所有的页面需要静态化，地址栏中不能出现`“？、&、=”`等符号
    * 解决方案：
    * 1）java里配置拦截器，拦截*.htm，把它作为请求处理，页面用jsp替换html。
	* 2）js可用可不用：js调用后台的数据，百度是不会收录进去的，jsp页面的东西可以被收录
	* 3）后台用的框架是springMvc，请求时参数处理配置
	    * 映射：`@RequestMapping("/details/{categoryStr}/{id}")`
	    * 接收参数：`@PathVariable String categoryStr,@PathVariable Integer id`
	    * 返回到jsp页面：`return "/public/medDetail";`
* 2、需要搭建的栏目与文章的对应关系
    * 例子：
	* 1）近视眼栏目：http://www.visionly.com.cn/jsy.htm
	* 2）近视眼文章：http://www.visionly.com.cn/jsy/162.htm
	* 文章id直接在栏目的下层
* 3、如2中所举例子，url中不要出现项目名、类名等，结构：`域名/栏目/文章`
    * 解决方案：nginx解决
    * 例子：location /jsy/{
            proxy_pass http://ip:端口号/项目名/类名/details/jsy/;
            proxy_redirect default;
        }
    * 注意：`location 后面要加“/”`，不懂的需要单独学习下nginx配置
* 4、后台有对应的文章、栏目管理，其中都要有加`TDK（title、description、keywords）`的地方
    * 规则：
	* 资讯类：title格式 ***（***为文章标题）  关键词***,北京朝阳区眼科医院,北京近视眼矫正中心  描述 复制文章第一段，30-80个字符 不要多也不要少
	* 病种类：title格式 ***_北京治疗##（***为文章标题，##为栏目对应兵种）关键词为***,北京治疗##,北京好的眼科医院，描述 复制文章第一段
    * 注意：title的格式以_区分，关键字的格式以,区分 ,为无输入法模式下的逗号

* 5、sitemap生成
	* 参考标准：https://www.heavengifts.com/sitemap.xml
	* `加定时任务，每天晚上11点生成`
* 6、robots不让爬虫爬的路径或文件
	* 参考标准：https://www.heavengifts.com/robots.txt
* 7、做一个404页面，出错时都进404页面，3秒后返回首页
* 8、栏目页旁边要加推荐文章，10条左右
   * 文章详情页下面加本栏目的相关文章，旁边加其他栏目的推荐文章
* 9、加面包屑
* 10、每个页面都要加h1 h2内容，h1标题，要惟一；h2其他栏目名
* 11、百度站长工具同步，首页加代码
	```java
    <meta name="baidu-site-verification" content="TI4ZF9Ar4h" />
    ```
* 12、`全站`链接标准化：域名/栏目名/（文章）
* 13、首页的`图片上，加title，`把想加的关键词均匀分布在图片的title上
* 14、首页底部模板增加`友情链接`
