---
layout: post_layout
title: 夭折的答题王--对战模块
time: On Thursday, May 31, 2018
location: BeiJing
pulished: true
excerpt_separator: "```"
---

#### 夭折的答题王--对战模块

* 此项目不错，夭折原因并非技术，所以在此想记录下在开发过程中的一些思考

* 对战模块（此模块逻辑比较复杂，主记此模块）
    * 1、AI对战页：当前赛季信息、用户的信息、所有段位列表（前端判断要展示多少）
    * 2、等待对战：拿用户信息
    * 3、对战中：
        * 好友对战：查找5个题目（取AI规则里的一条即可）
        * AI对战：查找5个题目（按难易程度）
    * 4、答完题要传连胜次数
    * 5、答题时要把用户的连胜次数查出

* 问题：
    * 如何查题？
    * 如何把查出来的题目精确的给到对应的两个对战人？
    * 难易程度？
    * 注意：AI对战时，和好友对战的查题方式一样，答题时不一样。
        * 答题时，需要按规则把答题的时间给出，答题时把要错误的题id也给出，前端随即选一个错误答案即可或者后台给出也一样，遍历一次这5道题，给出AI错误答案塞进去即可

### 解决思路：
* 一、查题思路：
    * 1、把数据库里的题，按难易程度分类查出id集合，放入redis中（暂设5min缓存）
    * 2、按段位答题占比规则，随机出5个题目id，组成为id集合
    * 3、按id集合查题返出去
    * 4、如果是AI的话，查出AI答题时间范围，AI答错题的数量，在id集合里随机出来，告诉前端这几个题是答错的，选哪个由前端随机控制
    * 5、请求查题时，要把和谁对战传过来判断
    * 6、好友点击链接，查题时，查找他们是不是好友，不是的话加好友
    * 7、代码（记录部分代码，重要是思路）
    ```java

    	/**
    	 *
    	 * <p>Title: 查找题目--和好友对战--好友的请求题目</p>
    	 * <p>Description:逻辑太多，需要把好友和AI 的分开写，不然接口太大
    	 * 新加逻辑：
    	 * 1、如果好友是第一次，需要走首页逻辑
    	 * 2、如果是好友对战，先从redis 中取对战信息，处于等待中即可对战，状态置为对战中
    	 * </p>
    	 * @param request
    	 * @param response
    	 * @param callback
    	 * @return
    	 */
    	@ResponseBody
        @RequestMapping(value = "/findTitlesFriend", method = RequestMethod.GET)
    	public String findTitlesFriend(Integer danId,Integer uid,Integer fid, HttpServletRequest request,HttpServletResponse response,String callback){
    		Result<Map<String, Object>> result = new Result<Map<String, Object>>();
    		//TODO 思路还需要更改，fid分享时是取不到的，可能取到昵称和图片，可用这来做key
    		try{
    			if (danId==null || uid==null){
    				result.setMsg("传参问题");
    				result.setSuccess(false);
    				result.setCode(1);
    			}else{
    				Map<String, Object> map = new HashMap<String, Object>();

    //				TODO 3、把题目查出来，id集合
    				List<TitleBean> titleList = null;
    				//TODO 如果是好友对战，先从redis 中取对战信息，处于等待中即可对战，状态置为对战中
    				String key2 = uid+"-waiting";
    				Object o =  redis.read(key2);
    				Integer fight = null;
    				if (o!=null)
    					fight = Integer.parseInt( o.toString());
    				if (fight==null){
    					result.setMsg("此链接已失效");
    					result.setSuccess(false);
    					result.setCode(3);
    				}else if (fight!=0){
    					result.setMsg("好友正在对战...");
    					result.setSuccess(false);
    					result.setCode(2);
    				}else {
    					redis.save(fid,key2);//状态置为fid，正在对战
    					//TODO 如果是好友对战的话，需要把uid传过来，存入redis中 key:发起人+接收人
    					//********************做的时候再看看逻辑是否有问题************
    					String key = uid+"-"+fid+"-title";
    					titleList = (List<TitleBean>) redis.read(key);

    					if (titleList==null){
    						//算出5道题的id
    						List<Integer> simple = initTitleList(danId);
    						//查出数据后放入缓存
    						titleList=titleService.selectTitleList(simple);
    						redis.save(titleList,key);
    					}
    					//成功加入对战时，互相加好友
    					FriendRel param = new FriendRel();
    					param.setUid(uid);
    					param.setFid(fid);
    					FriendRel rel = friendRelService.selectByRel(param);
    					if (rel==null){
    						//不是好友，加为好友
    						friendRelService.addRel(param);
    					}

    					//查出用户的连胜次数
    					UserExtendBean userDetail = userService.selectByUid(uid);

    					map.put("userDetail",userDetail);
    					map.put("titleList",titleList);

    					result.setData(map);
    					result.setMsg("成功");
    					result.setSuccess(true);
    					result.setCode(0);
    				}
    			}

    		}catch(Exception e){
    			e.printStackTrace() ;
    			result.setMsg("失败");
    			result.setSuccess(false);
    			result.setCode(1);
    		}finally{
    			if(StringUtil.isEmpty(callback)){
    				callback = JsonUtil.toJson(result);
    			}else{
    				callback = callback+"("+JsonUtil.toJson(result)+")";
    			}
    		}
    		return callback;
    	}

//*******************************************************************************************************
    		/**
        	 * 根据段位查出题目的id集合
        	 * @param danId
        	 * @return
             */
        	private List<Integer> initTitleList(Integer danId) {
        		//TODO 1、查到分类题目,在redis里取出
        		Map<String ,List<Integer>> titleMap = titleService.selectByDiff();
        		List<Integer> simple = titleMap.get("simple");
        		List<Integer> common = titleMap.get("common");
        		List<Integer> difficult = titleMap.get("difficult");

        		//TODO	2、按段位答题占比规则，随机出5个题目id，组成为id集合
        		int titleNum[] = RuleUtil.calTitleNum(danId);
        		simple = RuleUtil.randomList(simple,titleNum[0]);
        		common =  RuleUtil.randomList(common,titleNum[1]);
        		difficult =  RuleUtil.randomList(difficult,titleNum[2]);

        		//组合为一个id集合，共5道题
        		simple.addAll(common);
        		simple.addAll(difficult);
        		return simple;
        	}
    ```

* 二、查出题精准分配：
    * 1、如果是和AI对战的话，请求时直接返回题目就行，不用存redis里了，因为AI的答题时间和哪些答错都返回了
    * 2、和好友对战：
        * key--value
        * key：发起人id+接受人id
        * value：查出的题目
    * 3、当好友接受时，再请求查题，这样可防止邀请了之后，好友没接受在redis里产生的垃圾数据，前端每秒请求一次（判断有无uid确定是否是第一次进入，第一次登录进来，请求首页接口）
    * 4、答题结束后，要请求答题情况接口（把用户答题情况记录下来，并清空用户和好友的题目key）

* 三、答题时：（如何让对手知道你答的是什么）--和好友答题
    * 1、在redis里，存key--value
        * key：发起人+接受人+题目id
        * value：答题时间+回答id
    * 2、获取对手的答题情况--每秒请求一次
        * key换为 接受人+发起人+题目id，获取成功后删除key，返回，请前端处理
    * 3、和AI答题
        * 自己答就行，前端计算
        * AI答题情况，在返回题目时就已经给出了，由前端判断

* 四、和好友对战前建立等待关系
    * 1、uid-waiting作为key，value=0，放入redis中，分享了好多链接，谁点了就和谁对战，只能和一人对战
    * 2、好友点对战：
        * 1）先查uid-waiting是否为空，为空链接失效
        * 2）为1已经在作战，您来迟了
        * 3）为0好友加入对战，此时好友已有fid(好友id)，uid-waiting的value置为1，返回成功，此时可以请求好友对战的题目了(这就可以满足好友先请求题目了)
    * 3、好友第一次进入（需要得到openid来判断），走首页里的入库逻辑，前端不用弹出奖励了，默默加上就ok了
        * 走首页接口，会得到uid，存起来，下次请求对战链接时，按fid(好友id)参数传入即可

* 五、对战完成时，加逻辑
    * 1、答题结束时请求--答题情况提交（分析数据）
        * 之前逻辑上外加逻辑：ai不变；好友对战，把uid-waiting从redis里删除
    * 2、结束对战--ai没有，好友对战中，答题完后有结束对战（返回到主页面）和继续对战（下面逻辑）
    * 3、继续对战
        * ai不变
        * 好友：等待（点击继续对战）和加入的过程
        * 点击继续对战的人，变为主动者uid，好友fid----请求我的题目
        * 加入的人----请求好友的题目

* 六、添加好友时思路：
    * 1、之前想过好友只加一条记录就算好友，但这样查询好友时一点都不方便，查询是否是好友时，还需要or来查询，这样效率很低
    * 2、换一种，只要是加好友，就直接加两条，在事务里，查的时候，也就方便多了




