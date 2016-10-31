package com.rongdu.web.action;

import java.io.PrintWriter;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Random;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

import org.apache.log4j.Logger;
import org.apache.lucene.analysis.standard.StandardTokenizerTokenManager;
import org.apache.struts2.convention.annotation.Action;
import org.apache.struts2.convention.annotation.Namespace;
import org.apache.struts2.convention.annotation.ParentPackage;
import org.apache.struts2.convention.annotation.Result;
import org.springframework.beans.factory.annotation.Autowired;

import com.rongdu.domain.ActivityRecord;
import com.rongdu.domain.BorrowTender;
import com.rongdu.domain.Reward;
import com.rongdu.domain.Rule;
import com.rongdu.domain.User;
import com.rongdu.exception.BussinessException;
import com.rongdu.model.SearchParam;
import com.rongdu.model.OrderFilter.OrderType;
import com.rongdu.model.BorrowParam;
import com.rongdu.model.PageDataList;
import com.rongdu.service.ActivityRecordService;
import com.rongdu.service.BorrowService;
import com.rongdu.service.RuleService;
import com.rongdu.tool.Page;
import com.rongdu.util.DateUtils;
import com.sun.org.apache.bcel.internal.classfile.Code;

import net.sf.json.JSONArray;
import net.sf.json.JSONObject;

@Namespace("/luck")
@ParentPackage("p2p-default")
public class LuckAction extends BaseAction {

	private static Logger logger = Logger.getLogger(LuckAction.class);
	
	public static final Lock lock_numZsq = new ReentrantLock();
	
	@Autowired
	private ActivityRecordService activityRecordService;
	@Autowired
	private RuleService ruleService;
	@Autowired
	private BorrowService borrowService;
	
	@Action(value="index",
			results={@Result(name="success",type="ftl",location="/activity/egg-pc.html")})
	public String showGuess() throws Exception {
		logger.info("进入抽奖砸金蛋页面");
//		User user = getSessionUser();
//		if(null ==user){
//			return LOGIN;
//		}
		return SUCCESS;
	}
	
	@Action(value = "luckRandom")
    public void luckRandom() throws Exception {
        JSONObject json = new JSONObject();
        Map<String,Object> map=new HashMap<String,Object>();
        //0有异常 1 正常 2次数用完
        String code = "1";
        String message = "";
        User user = getSessionUser();
        if(null == user){
        	//没有登录 
        	map.put("code", "0");
        	map.put("message", "请先登录再进行砸金蛋");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        Rule rule = ruleService.getRuleByNid("Golden_egg");	
		String from = rule.getValueStrByKey("from_be");
        String fromBe = user.getFromBe();
        if(null != fromBe && from.equals(fromBe)){
        	//渠道用户
        	map.put("code", "0");
        	map.put("message", "渠道用户，不可以参加本次活动");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        if(!isTenderForUser(user)){
        	//渠道用户
        	map.put("code", "0");
        	map.put("message", "渠道用户，不可以参加本次活动");
        	printJson(getStringOfJpaMap(map));
        	return;
		}
        if(!isTime("Golden_egg")){
        	//活动日期判断
        	map.put("code", "0");
        	map.put("message", "请在国庆期间抽奖");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        int count = luckCount(user);
        logger.info("可用抽奖次数："+count+"..."+user.getUsername());
        if(count<1){
        	//抽奖次数
        	map.put("code", "2");
        	map.put("message", "您抽奖次数已用完");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        try{
            lock_numZsq.lock();
            logger.info("上锁    ！进行抽奖"+user.getUsername());
            
            Date  date = new Date();
            Date sDate = DateUtils.getSecIntegralTime(date);
            
            //code 实物为0 虚拟为 1
            int selectLuckCountByRecord = activityRecordService.selectLuckCountByRecord(user, "0",DateUtils.dateStr4(sDate),DateUtils.dateStr4(new Date()));
            logger.info("查询当前用户今天抽取了.."+selectLuckCountByRecord+"个..实物奖励："+user.getUsername());
            String rewardId = "";
            String jpName = "";
            //查询用户今天实物抽奖有没有 超过5个     code 0实物  1虚拟物品
            if(selectLuckCountByRecord>=5){
                logger.info("当前用户今天已经抽取5个实物奖励，发放5元理财红包："+user.getUsername());
                saveActivityRecord(user,7,"1");
                rewardId = "7";
                jpName = "5元理财红包";
            }else{
                logger.info("开始抽奖："+user.getUsername());
                for(int i =0;i<2;i++){
                    int luckNum = randomForInt();
                    logger.info(luckNum);
                    if(luckNum<=5){
                        //紫砂杯子
                    	int a = activityRecordService.selectLuckThings(2,DateUtils.dateStr4(sDate),DateUtils.dateStr4(new Date()));
//                        int a = findLuck(2,24);
                        if(a==0){
                            logger.info("获得紫砂杯一个获得usb风扇一个："+user.getUsername());
                            saveActivityRecord(user,2,"0");
                            i=2;
                            rewardId = "2";
                            jpName = "定制紫砂杯子";
                        }
                    }else if (luckNum<=16){
                        //usb风扇
                        int a = findLuck(3,4);
                        if(a==0){
                            logger.info("获得usb风扇一个："+user.getUsername());
                            saveActivityRecord(user,3,"0");
                            i=2;
                            rewardId = "3";
                            jpName = "usb风扇";
                        }
                    }else if(luckNum<=27){
                        //记事本
                        int a = findLuck(4,4);
                        if(a==0){
                            logger.info("获得记事本user.getUsername()："+user.getUsername());
                            saveActivityRecord(user,4,"0");
                            i=2;
                            rewardId = "4";
                            jpName = "记事本";
                        }
                    }else if(luckNum<=43){
                        //10元现金红包
                        int a = findLuck(5,2);
                        if(a==0){
                            logger.info("获得10元现金红包user.getUsername()："+user.getUsername());
                            saveActivityRecord(user,5,"0");
                            i=2;
                            rewardId = "5";
                            jpName = "10元现金红包";
                        }
                    }else if(luckNum<=59){
                        //5元现金红包
                        int a = findLuck(6,2);
                        if(a==0){
                            logger.info("获得5元现金红包user.getUsername()："+user.getUsername());
                            saveActivityRecord(user,6,"0");
                            i=2;
                            rewardId = "6";
                            jpName = "5元现金红包";
                        }
                    }else if(luckNum<=100){
                        //5元理财红包
                        logger.info("获得5元理财红包user.getUsername()："+user.getUsername());
                        saveActivityRecord(user,7,"1");
                        i=2;
                        rewardId = "7";
                        jpName = "5元理财红包";
                    }
                }

            }
            if("".equals(rewardId)){
            	saveActivityRecord(user,7,"1");
            	rewardId = "7";
            	logger.info("循环两次，实物发完，发放理财红包");
            }
            int lcount = count-1;


            map.put("code", "1");
        	map.put("message", "抽奖成功");
            map.put("lcount",lcount);
            map.put("rewardId",rewardId);
            map.put("jpName",jpName);
            map.put("userId",user.getUserId());
        	printJson(getStringOfJpaMap(map));

        }catch (Exception e){
            logger.info("抽奖异常 ："+user.getUsername()+"..."+e.getMessage());
          //活动日期判断
        	map.put("code", "0");
        	map.put("message", "抽奖异常，刷新重试");
        	printJson(getStringOfJpaMap(map));
        }finally {
            lock_numZsq.unlock();
            logger.info("释放同步锁"+user.getUsername());
        }
    }
	
	public void saveActivityRecord(User user,int rewardId,String code){
        logger.info("保存抽奖："+user.getUsername());
        ActivityRecord a = new ActivityRecord();
        a.setUserId(user);
        Reward reward = new Reward();
        reward.setId(rewardId);
        a.setRewardId(reward);
        a.setAddTime(new Date());
        a.setCode(code);
        activityRecordService.saveActivityRecord(a);
		
	}
	
    /**
     * 查看今天抽奖实物上限
     * @return
     */
    public int findLuck(int rewardId,int num){
        logger.info("查询今日实物上限："+rewardId);
        Date date2 = new Date();
        String date = DateUtils.dateStr4(date2);
        String sDate = getTimeTwo(date2,num);
        int count = activityRecordService.selectLuckThings(rewardId, sDate,date);
        logger.info("查询当前实物个数："+count);
        return count;
    }
    
    /**
     * 判断时间段
     * @param num
     * @return
     */
    public String getTimeTwo(Date date,int num){
        Calendar cal=Calendar.getInstance();
        cal.setTime(date);
        int hour = cal.get(Calendar.HOUR_OF_DAY);
        int a = hour-num;
        if(a<0){
        	a=0;
        }
        String sDate = changeTime(date,a);
        return sDate;
    }
    
    /**
     * 生成开始日期
     * @param d
     * @param num
     * @return
     */
    public String changeTime(Date d,int num){
        Calendar cal = Calendar.getInstance();
        cal.setTimeInMillis(d.getTime());
        cal.set(Calendar.HOUR_OF_DAY, num);
        if(num == 0){
        	cal.set(Calendar.SECOND, 0);
    		cal.set(Calendar.MINUTE, 0);
    		cal.set(Calendar.MILLISECOND, 0);
        }
        return DateUtils.dateStr4(cal.getTime());
    }
	
    /**
     * 生成随机数
     * @return
     */
    public int randomForInt(){
        Random random = new Random();
        return random.nextInt(100) + 1; 
    }
	
    /**
     * 查询剩余抽奖次数
     * @param phone
     * @return
     */
    public int luckCount(User user){
        logger.info("查询剩余抽奖次数："+user.getUsername());
        //查询已经抽奖次数
        int count = activityRecordService.selectCountByPhone(user);
        //查询活动日期投资次数
        int money = activityRecordService.checkNumber(user.getPhone());
        int number = money/100;
        int num = number+1-count;
        return num;
    }
    
	@Action(value="luckRecord",
			results={@Result(name="luckRecord",type="ftl",location="/activity/prize_order.html")})
    public String luckRecord(){
    	User user = getSessionUser();
    	if(null == user){
    		return LOGIN;
    	}
    	return "luckRecord";
    }
	
	@Action(value = "luckRecordList")
	public void luckRecordList(){
		JSONObject json = new JSONObject();
		JSONArray array = new JSONArray();
		User user = getSessionUser();
		if(null == user){
			json.put("code", "0");
			json.put("message", "登陆后查看抽奖记录");
			printJson(json.toString());
			return;
		}
		int page = paramInt("page");
		SearchParam param = SearchParam.getInstance();
		param.addPage(page, Page.ROWS);
		param.addParam("userId", user.getUserId());
		param.addOrder(OrderType.DESC, "addTime");

		
		PageDataList<ActivityRecord> list = activityRecordService.selectPageListByParam(param);
		if(null != list && null != list.getList()){
			for(ActivityRecord activityRecord : list.getList()){
				JSONObject js = new JSONObject();
				js.put("jpName",activityRecord.getRewardId().getName());
				js.put("cjTime",DateUtils.dateStr4(activityRecord.getAddTime()));
				array.add(js);
			}
		}else{
			json.put("code", "1");
			json.put("message", "抽奖记录为空");
			printJson(json.toString());
			return;
		}
		json.put("code", "2");
		json.put("currentPage",list.getPage().getCurrentPage());
		json.put("pages", list.getPage().getPages());
		json.put("list", array);
		printJson(json.toString());
	}
	
	@Action(value = "luckTender")
    public void luckTender() throws Exception {
		JSONObject json = new JSONObject();
        Map<String,Object> map=new HashMap<String,Object>();
        //0有异常 1 正常 2次数用完
        String code = "1";
        String message = "";
        User user = getSessionUser();
        if(null == user){
        	//没有登录 
        	map.put("code", "0");
        	map.put("message", "请先登录再进行投资抽奖");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        String tenderId = paramString("tenderId");
        if("".equals(tenderId)){
        	//请求数据判断
        	map.put("code", "0");
        	map.put("message", "请求数据错误");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        if(!isTime("tender_egg")){
        	//活动日期判断
        	map.put("code", "0");
        	map.put("message", "请在活动日期抽奖");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        
        logger.info("查找是否满足投资抽奖条件");
        long id = Long.parseLong(tenderId);
        BorrowTender borrowTender = borrowService.findTenderByIdandUserId(id, user.getUserId(),starTime());
        
        if(null == borrowTender){
        	//没有投资记录
        	map.put("code", "0");
        	map.put("message", "请投资后再来抽奖");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        //判断 是否重复抽奖
        int count = activityRecordService.selectActivityRecordByTenderId(tenderId);
        if(count>0){
        	//参与过抽奖了
        	map.put("code", "0");
        	map.put("message", "您已经参与过此次投资抽奖了");
        	printJson(getStringOfJpaMap(map));
        	return;
        }
        
        try {
        	lock_numZsq.lock();
            logger.info("上锁    ！进行抽奖"+user.getUsername());
        	
            double money = borrowTender.getMoney();
            int luckNum = randomForInt();
            logger.info(luckNum);
            String rewardId = "";
            String jpName = "";
            String secRewardId = "";
            String secjpName = "";
            String zuanshi = "0";
//            1.钻石  2个50   20随机  10000
//            2.金蛋  1个50   20随机  10000
//            3.银    1个20   10随机  8000
//            4.铜    1个10   5随机   6000
//            5.铁蛋  必得5   3000
            if(money <= 3000){
            	logger.info("获得5元理财红包一个："+user.getUsername());
                saveActivity(user,8,"1",tenderId);
                rewardId = "8";
                jpName = "5元理财红包";
                if(luckNum<50){
                	logger.info("随机获得5元理财红包一个："+user.getUsername());
                	saveActivity(user,8,"1",tenderId);
                	secRewardId = "8";
                	secjpName = "5元理财红包";
                }
            }else if(money <= 6000){
            	logger.info("获得10元理财红包一个："+user.getUsername());
                saveActivity(user,9,"1",tenderId);
                rewardId = "9";
                jpName = "10元理财红包";
                if(luckNum<50){
                	logger.info("随机获得5元理财红包一个："+user.getUsername());
                	saveActivity(user,8,"1",tenderId);
                	secRewardId = "8";
                	secjpName = "5元理财红包";
                }
            }else if(money <= 8000){
            	logger.info("获得20元理财红包一个："+user.getUsername());
                saveActivity(user,10,"1",tenderId);
                rewardId = "10";
                jpName = "20元理财红包";
                if(luckNum<50){
                	logger.info("随机获得10元理财红包一个："+user.getUsername());
                	saveActivity(user,9,"1",tenderId);
                	secRewardId = "9";
                	secjpName = "10元理财红包";
                }
            }else if(money <= 10000){
            	logger.info("获得50元理财红包一个："+user.getUsername());
                saveActivity(user,11,"1",tenderId);
                rewardId = "11";
                jpName = "50元理财红包";
                if(luckNum<50){
                	logger.info("随机获得20元理财红包一个："+user.getUsername());
                	saveActivity(user,10,"1",tenderId);
                	secRewardId = "10";
                	secjpName = "20元理财红包";
                }
            }else{
            	logger.info("获得50元理财红包两个："+user.getUsername());
                saveActivity(user,11,"1",tenderId);
                saveActivity(user,11,"1",tenderId);
                rewardId = "11";
                jpName = "50元理财红包";
                zuanshi = "1";
                if(luckNum<50){
                	logger.info("随机获得20元理财红包一个："+user.getUsername());
                	saveActivity(user,10,"1",tenderId);
                	secRewardId = "10";
                	secjpName = "20元理财红包";
                }
            }
            
            map.put("code", "1");
        	map.put("message", "抽奖成功");
            map.put("rewardId",rewardId);
            map.put("jpName",jpName);
            map.put("secRewardId",secRewardId);
            map.put("secjpName",secjpName);
            map.put("zuanshi",zuanshi);
            map.put("userId",user.getUserId());
        	printJson(getStringOfJpaMap(map));
            
			
		}catch (Exception e){
            logger.info("11抽奖异常 ："+user.getUsername()+"..."+e.getMessage());
          //活动日期判断
        	map.put("code", "0");
        	map.put("message", "抽奖异常，刷新重试");
        	printJson(getStringOfJpaMap(map));
        }finally {
            lock_numZsq.unlock();
            logger.info("释放同步锁"+user.getUsername());
        }
        
		
	}
	
	public String starTime(){
		Rule rule = ruleService.getRuleByNid("tender_egg");	
		String start_time = rule.getValueStrByKey("start_time");
		return start_time;
	}
	
	@Action(value="tenderIndex",
			results={@Result(name="success",type="ftl",location="/activity/tender_egg.html")})
	public String tenderIndex() throws Exception {
		logger.info("进入抽奖砸金蛋页面");
		User user = getSessionUser();
	    String tenderId = paramString("tenderId");
		String egg = "diamond_egg";
		if(null != user && !"".equals(tenderId)){
			BorrowTender borrowTender = borrowService.findTenderByIdandUserId(Long.parseLong(tenderId), user.getUserId(),starTime());
			if(null != borrowTender){
				double money = borrowTender.getMoney();
				if(money<=3000){
					egg = "iron_egg";
				}else if(money <= 6000){
					egg = "copper_egg";
				}else if(money <= 8000){
					egg = "silver_egg";
				}else if(money <= 10000){
					egg = "king_egg";
				}
			}
		}
		request.setAttribute("egg", egg);
		request.setAttribute("tenderId", tenderId);
		return SUCCESS;
	}
	
	public void saveActivity(User user,int rewardId,String code,String tenderId){
        logger.info("保存抽奖："+user.getUsername());
        ActivityRecord a = new ActivityRecord();
        a.setUserId(user);
        Reward reward = new Reward();
        reward.setId(rewardId);
        a.setRewardId(reward);
        a.setAddTime(new Date());
        a.setCode(code);
        a.setTenderId(tenderId);
        activityRecordService.saveActivityRecord(a);
		
	}
	
}
