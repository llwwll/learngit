<!-- 
<div class="pop-ques"></div>
<div class="start-pop">
	<p class="start-title"></p>
	<p class="w-line">
		<a class="pg" href="/risk/assessment.html">立即评估</a>
	</p>
</div>

<script type="text/javascript">
$(document).ready(function(){
	var answerFlag = ${answerFlag};
	if(answerFlag == 0){
		$('.yo_header_1').append('<div class="header-pop" style="width:100%;height:70px;background-color:#000;position:absolute;opacity:0.6;filter: progid:DXImageTransform.Microsoft.Alpha(opacity=60)"></div>');
		$('.pop-ques').css('height',$(document.body).height()-70);
		$('.start-pop').show();
	}
});
</script> -->

<div class="user_menu">
	<div class="user_center">
		<ul>
			<li class="user_nav1" >账户总览</li>
			<a href="${webroot}/member/userList/user_list.html"><li >理财中心</li></a>
			<a href="${webroot}/member/info/setup.html"><li>设置中心 </li></a>
			<a href="${webroot}/activity/myactivity.html"><li>活动奖励</li></a>
			<a href="${webroot}/member/message/box.html"><li>消息中心</li></a>
			<a href="${webroot}/member/friend/invite.html"><li>邀请好友</li></a>
		</ul>
	</div>
</div>
<!-- banner部分 -->
<div class="usr_banner">
	<div class="user_center">
		<div class="user_head">
			   <img src="${webroot}/imgurl.html?userid=${user.userId?c}&size=middle"  class="picborder" />
		</div>
		<div class="user_message">
			<div class="user_message_top">
				<p>
					<span class="user_id">您好，${user.username}</span>
					<!-- 认证过后的样式分别是  user_name1 user_mail1 user_tel1 user_vip1-->
					<#if user.realStatus==1>
					<span class="user_name"></span>
					<#else>
					<span class="user_name1"></span>
					</#if>
					<#if user.emailStatus==1>
					<span class="user_mail"></span>
					<#else>
					<span class="user_mail1"></span>
					</#if>
					<#if user.phoneStatus==1>
					<span class="user_tel"></span>
					<#else>
					<span class="user_tel1"></span>
					</#if>
					<#if cache.vipStatus==1>
					<span class="user_vip"></span>
					<#else>
					<span class="user_vip1"></span>
					</#if>
					<#if answerFlag=="1">
					<span class="user_fire"></span>
					<#else>
					<a href="/risk/assessment.html"><span class="user_fire1"><span class="fire-tip" style="display:none;">个人风险偏好评估</span></span></a>
					</#if>
					<span class="user_safe">安全等级：</span>
					<#if user.apiId??&&user.realStatus==1&&cache.vipStatus==1>
					<span class="user_Grade">高</span>
					<#elseif user.apiId??&&user.realStatus==1>
					<span class="user_Grade">中</span>
					<#else>
					<span class="user_Grade">低</span>
					</#if>
					<span class="user_administration"><a href="/member/info/setup.html">[管理]</a></span>
				</p>
			</div>
			<script type="text/javascript">
				$('.user_fire1').mouseover(function(){
					$('.fire-tip').show();
				}).mouseout(function(){
					$('.fire-tip').hide();
				});
			</script>
			<div class="user_message_bottom">
				<p>
					<span>VIP 期限：</span>
					<#if cache.vipStatus==1>
						<#if ( dateformat(cache.vipVerifyTime?string('yyyy-MM-dd HH:mm:ss')) + 365*24*3600 - dateformat("now") ) lt 0>
							已经过期
						<#else> 
							<span class="user_time" data-time="${( dateformat(cache.vipVerifyTime?string('yyyy-MM-dd HH:mm:ss')) + 365*24*3600 - dateformat('now') )?c}"></span>
						</#if>
					<#elseif cache.vipStatus==2>
						VIP审核中
					<#else>
						<a href="${webroot}/member/info/setup.html">开通VIP</a>
					</#if>
					<span>系统通知：</span>
					<a href="${webroot}/member/message/box.html?status=0">
						<span class="user_messagenumber">${unRead?default(0)}</span>
						<span class="user_messagett"> 封未读信息</span>
					</a>
					<span>乾多多账户：</span>
					<#if user.apiId??>
					<span>${user.apiUsercustId}</span>
					<span><a class="user_messagenumber" href="https://www.moneymoremore.com" target="_blank">[登录]</a></span>
					<#else>
					<span><a class="user_messagenumber" href="${webroot}/member/info/setup.html">开通乾多多账户</a></span>
					</#if>
				</p>
			</div>
		</div>
	</div> 
</div>

<div class="user_btn">
	<div class="user_center">
		<p class="user_balance">
			<span>可用余额</span>
			<span class="user_balancesep"><#if summary??><#if (summary.accountUseMoney!0) lt 1.00>${(summary.accountUseMoney!0)?string('0.00')}<#else>${(summary.accountUseMoney!0)?string(',###.00')}</#if></#if></span>
			<span>元</span>
			<!-- 
			<span class="user-link">本月佣金</span>
			<span class="user_balancesep">200</span>
			<span>元</span>
			
			<span class="user-link">本日佣金</span>
			<span class="user_balancesep">300</span>
			<span>元</span>
			 -->
		</p>
		<div class="user_btnlink">
			<a class="user_tx" href="/member/userList/withdraw.html"></a>
			<a class="user_cz" href="/member/userList/recharge.html"></a>
		</div>
	</div>
</div>

<!-- <div class="user_main">
	<div class="user_center">
		<div class="user_Prompt">
			<div class="user_PromptCenter" style="padding-left: 20px">
				<div class="user_hbh_imgl">
					<a href="javascript:void(0);" onclick="luckMoney(105)">
						<img src="${webroot}${theme_dir}/images/luckMoney-10.png" alt=""/>
					</a>
				</div>
				<div class="user_hbh_imgl">
					<a href="javascript:void(0);" onclick="luckMoney(106)">
						<img src="${webroot}${theme_dir}/images/luckMoney-20.png" alt=""/>
					</a>
				</div>
				<div class="user_hbh_imgl">
					<a href="javascript:void(0);" onclick="luckMoney(107)">
						<img src="${webroot}${theme_dir}/images/luckMoney-50.png" alt=""/>
					</a>
				</div>
			</div>
		</div>
	</div>
</div> -->


<div class="user_main">
	<div class="user_center">
		<div class="user_Prompt">
			<div class="user_PromptCenter">
				<#if countsMap??>
					<#if countsMap.unusedC gt 0 && countsMap.useingC gt 0>
						<font style="margin-left: 28px;">您还有</font><span>${countsMap.unusedC!}</span><font>个红包奖励未使用！</font>
						<a class="user_Goreward" href="/activity/myactivity.html">查看奖励</a>
						<span style="margin-left: 20px;">${countsMap.useingC!}</span><font>个红包奖励未激活！</font>
						<a class="user_goInvestment" href="/activity/myreward.html?rewardType=0&isEnable=2">立即激活</a>
					<#elseif countsMap.unusedC gt 0>
						<font style="margin-left: 28px;">您还有</font><span>${countsMap.unusedC!}</span><font>个红包奖励未使用！</font>
						<a class="user_Goreward" href="/activity/myactivity.html">查看奖励</a>
					<#elseif countsMap.useingC gt 0>
						<font style="margin-left: 28px;">您还有</font><span>${countsMap.useingC!}</span><font>个红包奖励未激活！</font>
						<a class="user_goInvestment" href="/activity/myreward.html?rewardType=0&isEnable=2">立即激活</a>
					</#if>
				</#if>

			</div>
		</div>
	</div>
</div>
<div class="user_centerlist">
	<div class="user_center">
		<div class="user_centerlist1 user_accountlist">
			<h3>当前资产匹配</h3>
			<div class="user_Assetspart">
				<p class="user_Assetspartcenter"><#if summary??><#if (summary.accountTotal!0) lt 0.01>0.00<#else>${(summary.accountTotal!0)?string(',###.00')}</#if></#if></p>
			</div>
			<div class="user_data">
				<div class="user_datal">
					<p class="user_datatt">已收益</p>
					<p class="user_datanumber"><#if summary??><#if (summary.hasCollectInterestTotal!0) lt 1.00>${(summary.hasCollectInterestTotal!0)?string('0.00')}<#else>${(summary.hasCollectInterestTotal!0)?string(',###.00')}</#if></#if></p>
				</div>
				<div class="user_datar">
					<p class="user_datatt">已投资</p>
					<p class="user_datanumber"><#if summary??><#if (summary.investTotal!0) lt 0.01>0.00<#else>${(summary.investTotal!0)?string(',###.00')}</#if></#if></p>
				</div>
			</div>
			<div class="user_datalist">
				<p><#if summary??><#if (summary.accountUseMoney!0) lt 1.00>${(summary.accountUseMoney!0)?string('0.00')}<#else>${(summary.accountUseMoney!0)?string(',###.00')}</#if></#if><font>可用余额</font></p>
				<p><#if summary??><#if (summary.accountNoUseMoney!0) lt 0.01>0.00<#else>${(summary.accountNoUseMoney!0)?string(',###.00')}</#if></#if><font>冻结金额</font></p>
				<p><#if summary??><#if (summary.collectInterestTotal!0) lt 1.00>${(summary.collectInterestTotal!0)?string('0.00')}<#else>${(summary.collectInterestTotal!0)?string(',###.00')}</#if></#if><font>待收利息</font></p>
				<p><#if summary??><#if (summary.collectCapitalTotal!0) lt 0.01>0.00<#else>${(summary.collectCapitalTotal!0)?string(',###.00')}</#if></#if><font>待收本金</font></p>
			</div>
			<div class="user_totalProfit">
				<p class="user_accountRed user_accountRedbt">
					<span class="user_accountRedtt">我的红包</span>
					<span class="user_accountRedNumber"><#if (sumDenomination!0) lt 0.01>0<#else>${sumDenomination?string(',###.00')}</#if></span>
					<span class="user_accountRedunit">元</span>
					<span class="user_accountRedDetail"><a href="${webroot}/activity/myreward.html?rewardType=0">查看详情</a></span>
				</p>
			</div>
		</div>
		<div class="user_centerlist2">
			<h3>半年收益走势</h3>
			<div class="user_trendpart">
				<div class="user_trendnumber"><#if (totalInterest!0) lt 0.01>0.00<#else>${totalInterest?string(',###.00')}</#if></div>
			</div>
			<p class="user_years">${nowYear}年
				<span class="user_dw">(月)</span>
			</p>
			<div id="user_main" class="user_Broken"></div>
			<!-- ECharts单文件引入 -->
		    <script src="http://echarts.baidu.com/build/dist/echarts-all.js"></script>
		    <script type="text/javascript">
		    	function toDouble(obj){
		    		return parseFloat(obj).toFixed(2);
		    	}
		        // 基于准备好的dom，初始化echarts图表
		        var myChart = echarts.init(document.getElementById('user_main')); 
		        var str1= "${summary.months}";
		        var str2= "${summary.collectInterestHalf}";
		        var data1 = str1.split(',');
		        var data2 = str2.split(',');
		        var option = {
					tooltip : {
						trigger: 'axis'
					},
					calculable : true,
					xAxis : [
						{
							type : 'category',
							boundaryGap : false,
							data : [data1[0],data1[1],data1[2],data1[3],data1[4],data1[5]]	//['一月','二月','三月','四月','五月','六月']
						}
					],
					yAxis : [
						{
							type : 'value'
						}
					],
					series : [
						{
							name:'收益走势',
							type:'line',
							stack: '总量',
							data: [toDouble(data2[0]),toDouble(data2[1]),toDouble(data2[2]),toDouble(data2[3]),toDouble(data2[4]),toDouble(data2[5])]  //[120, 132, 101, 134, 90, 230]
						}
					]
				};
		        // 为echarts对象加载数据 
		        myChart.setOption(option); 
		    </script>
		    <div class="user_totalProfit user_BrokenCenter">
				<p class="user_totalProfitp1">本月收益（元）</p>
				<p class="user_totalProfitp2"><#if summary??><#if (summary.collectInterestMonth!0) lt 1.00>${(summary.collectInterestMonth!0)?string('0.00')}<#else>${(summary.collectInterestMonth!0)?string(',###.00')}</#if></#if></p>
			</div>
		</div>
		<!-- <div class="user_centerlist3">
			<h3>还款日历</h3>
			<div class="user_Accountpart"></div>
			<p class="user_hkrl">金额(元)
				<span class="user_dw">日期</span>
			</p>
			<div id="user_main1" class="user_Broken"></div>
			ECharts单文件引入
		    <script src="http://echarts.baidu.com/build/dist/echarts-all.js"></script>
		    <script type="text/javascript">
		    	function toDouble(obj){
		    		return parseFloat(obj).toFixed(2);
		    	}
		        // 基于准备好的dom，初始化echarts图表
		        var myChart = echarts.init(document.getElementById('user_main1')); 
		        var str1= "${summary.days}";
		        var str2= "${summary.collectInterestDays}";
		        var data1 = str1.split(',');
		        var data2 = str2.split(',');
		        var option = {
					tooltip : {
						trigger: 'axis'
					},
					calculable : true,
					xAxis : [
						{
							type : 'category',
							boundaryGap : false,
							data : [data1[0],data1[1],data1[2],data1[3],data1[4],data1[5],data1[6]]	//['一月','二月','三月','四月','五月','六月']
						}
					],
					yAxis : [
						{
							type : 'value'
						}
					],
					series : [
						{
							name:'回款走势',
							type:'line',
							stack: '总量',
							data: [toDouble(data2[0]),toDouble(data2[1]),toDouble(data2[2]),toDouble(data2[3]),toDouble(data2[4]),toDouble(data2[5]),toDouble(data2[6])]  //[120, 132, 101, 134, 90, 230]
						}
					]
				};
		        // 为echarts对象加载数据 
		        myChart.setOption(option); 
		    </script>
		    <div class="user_totalProfit user_BrokenCenter">
				<p class="user_totalProfitp1">本月到期回款${bCount}次</p>
			</div>
		</div> -->
		
		
		    
		
		
		<div class="user_centerlist3">
			<h3>还款日历</h3>
			<div class="user_Accountpart"></div>
			
			<div id="hk_main" style="width:320px;height:300px;"></div>
			
			<script type="text/javascript">
		    var str1= "${summary.days}";
		     var str2= "${summary.collectInterestDays}";
		     var str3 = "${summary.hkCount}";
		     var data1 = str1.split(',');
	         var data2 = str2.split(',');
	         var data3 = str3.split(','); 
		        // 基于准备好的dom，初始化echarts图表
		        var myChart1 = echarts.init(document.getElementById('hk_main')); 
		    
		        var jsonstr1=[
								{"name":data1[0],'jine':data2[0]},
								{"name":data1[1],'jine':data2[1]},
								{"name":data1[2],'jine':data2[2]},
								{"name":data1[3],'jine':data2[3]},
								{"name":data1[4],'jine':data2[4]},
								{"name":data1[5],'jine':data2[5]},
								{"name":data1[6],'jine':data2[6]}
							];

							var jsonstr2=[
								{"name":data1[0],'count':data3[0]},
								{"name":data1[1],'count':data3[1]},
								{"name":data1[2],'count':data3[2]},
								{"name":data1[3],'count':data3[3]},
								{"name":data1[4],'count':data3[4]},
								{"name":data1[5],'count':data3[5]},
								{"name":data1[6],'count':data3[6]}
							];

						
							var xz=[data1[0],data1[1],data1[2],data1[3],data1[4],data1[5],data1[6]];
							var yz=[data3[0],data3[1],data3[2],data3[3],data3[4],data3[5],data3[6]];
							
							var option={
								tooltip:{
									trigger: 'axis',
									formatter:function(params){						
												for(var i=0;i<=jsonstr1.length;i++){
													for(var j=0;j<=jsonstr2.length;j++){
														if(jsonstr1[i].name==params[0].name&&jsonstr2[j].name==params[0].name){
															return '金额：'+jsonstr1[i].jine+'<br/>'+'次数：'+jsonstr2[j].count;
														}
													}
												}
									}
								},

								xAxis:[
									{
										type:'category',
										name: "日期",
										data:xz
									}
								],

								yAxis:[
									{
										type:'value',
										name: "个",
									}
								],

								series:[
									{
										'name':'标数',
										'type':'line',
										itemStyle : {  
			                                normal : {  
												color:'#ff7f50',
			                                    lineStyle:{  
			                                        color:'#ff7f50'  
			                                    }  
			                                }  
			                            },  
										'data':yz
									}
								]
							};

							myChart1.setOption(option); 
		    </script>
			<div class="user_accountbtn">
				最近一周待还款${bCount}次
				<!--<a class="user_tbbtn" href="/debetList/debetList.html">投标</a>  -->
				<!-- <a class="user_zrbtn">转让</a> -->
			</div>
			<div class="user_accountjl">
				
			</div>
		</div>
		
		
		
		
		
		<#--<div class="user_centerlist3">
			<h3>聚投宝账户</h3>
			<div class="user_Accountpart"></div>
			<div class="user_accountlist1">
				<span>投资总额</span>
				<font><#if summary??><#if (summary.investTotalJU!0) lt 0.01>0.00<#else>${(summary.investTotalJU!0)?string(',###.00')}</#if></#if></font>
			</div>
			<div class="user_accountlist1">
				<span>累计收益</span>
				<font><#if summary??><#if (summary.collectInterestTotalJU!0) lt 1.00>${(summary.collectInterestTotalJU!0)?string('0.00')}<#else>${(summary.collectInterestTotalJU!0)?string(',###.00')}</#if></#if></font>
			</div>
			<div class="user_accountlist1">
				<span>待收总额</span>
				<font><#if summary??><#if (summary.collectTotalJU!0) lt 0.01>0.00<#else>${(summary.collectTotalJU!0)?string(',###.00')}</#if></#if></font>
			</div>
			<div class="user_accountbtn">
				<a class="user_tbbtn" href="/debetList/debetList.html">投标</a>-->
				<!-- <a class="user_zrbtn">转让</a> -->
		<!-- 	</div>
			<div class="user_accountjl">
				
			</div>
		</div> -->

	</div>
</div>
<div class="user_centerlist">
	<div class="user_center user_Detailed">
		<div class="user_Detailed_l">
			<p class="user_Detailed_mx">投资统计</p>
		</div>
		<table>
			<tr>
				<td class="user_Detailed_th">待收</td>
				<td width="60px;">待收本金</td>
				<td><#if summary??><#if (summary.collectCapitalTotal!0) lt 0.01>0.00<#else>${(summary.collectCapitalTotal!0)?string(',###.00')}</#if></#if></td>
				<td width="60px;">待收利息</td>
				<td><#if summary??><#if (summary.collectInterestTotal!0) lt 1.00>${(summary.collectInterestTotal!0)?string('0.00')}<#else>${(summary.collectInterestTotal!0)?string(',###.00')}</#if></#if></td>
				<td width="60px;">投资笔数</td>
				<td><#if summary??><#if (summary.collectTenderCount!0) lt 0.01>0<#else>${(summary.collectTenderCount!0)?string(',###')}</#if></#if></td>
			</tr>
			<tr>
				<td class="user_Detailed_th">已收</td>
				<td width="60px;">已收本金</td>
				<td><#if summary??><#if (summary.hasCollectTotal!0) lt 0.01>0.00<#else>${(summary.hasCollectTotal!0)?string(',###.00')}</#if></#if></td>
				<td width="60px;">已收利息</td>
				<td><#if summary??><#if (summary.hasCollectInterestTotal!0) lt 1.00>${(summary.hasCollectInterestTotal!0)?string('0.00')}<#else>${(summary.hasCollectInterestTotal!0)?string(',###.00')}</#if></#if></td>
				<td width="60px;">投资笔数</td>
				<td><#if summary??><#if (summary.hasCollectTenderCount!0) lt 0.01>0<#else>${(summary.hasCollectTenderCount!0)?string(',###')}</#if></#if></td>
			</tr>
		</table>
		
		<div class="user_detailed_btn">
			<a class="user_detailed_see" href="/member/userList/user_list.html">查看详情</a>
		</div>
	</div>
	<div class="user_center user_Detailed2">
		<div class="user_Detailed_l">
			<p class="user_Detailed_mx">资金统计</p>
		</div>
		<table>
			<tr>
				<td class="user_Detailed_th">充值</td>
				<td width="60px;">充值总额</td>
				<td><#if (rechargeMoney!0) lt 0.01>0.00<#else>${(rechargeMoney!0)?string(',###.00')}</#if></td>
				<td width="60px;">手续费</td>
				<td><#if (rechargeFee!0) lt 1.00>${(rechargeFee!0)?string('0.00')}<#else>${(rechargeFee!0)?string(',###.00')}</#if></td>
				<td width="60px;">充值笔数</td>
				<td><#if (rechargeAccount!0) lt 0.01>0<#else>${rechargeAccount?string(',###')}</#if></td>
			</tr>
			<tr>
				<td class="user_Detailed_th">提现</td>
				<td width="60px;">提现总额</td>
				<td><#if (cashMoney!0) lt 0.01>0.00<#else>${(cashMoney!0)?string(',###.00')}</#if></td>
				<td width="60px;">手续费</td>
				<td><#if (cashFee!0) lt 1.00>${(cashFee!0)?string('0.00')}<#else>${(cashFee!0)?string(',###.00')}</#if></td></td>
				<td width="60px;">提现笔数</td>
				<td><#if (cashAccount!0) lt 0.01>0<#else>${cashAccount?string(',###')}</#if></td>
			</tr>
		</table>
		<div class="user_detailed_btn">
			<a class="user_detailed_see" href="/member/userList/money_record.html">查看详情</a>
		</div>
	</div>
</div>
<script type="text/javascript">

function luckMoney(num){
		
	 $.ajax({
			type:'POST',
			url :"/member/luckMoney.html",
			data: 'id='+num,
			dateType:'json',
			cache : false,
			error : function(XMLHttpRequest, textStatus, errorThrown){
				alert("请求失败");
				return false;
					},
			success : function(msg) {
						alert(msg.message);
			}
		}); 	
	
}
function showRemainTime(){
var endtimes=$(".user_time");
endtimes.each(function(){
	RemainTime($(this));
});
}
showRemainTime();
//setInterval("showRemainTime()",1000);

function RemainTime(t){
var iDay,iHour,iMinute,iSecond,account;
var sDay="",sTime="";
var at="data-time"
var iTime=t.attr(at);
if (iTime >= 0){
	iDay = parseInt(iTime/24/3600);
	iHour = parseInt((iTime/3600)%24);
	iMinute = parseInt((iTime/60)%60);
	iSecond = parseInt(iTime%60);
	if (iDay > 0){ 
		sDay = iDay + "天"; 
	}
	sTime =sDay + iHour + "时" + iMinute + "分" + iSecond + "秒";
	if(iTime==0){
		sTime="<span style='color:green'>时间到了！</span>";
	}
		t.attr(at,iTime-1);
  }else{
	 sTime="<span style='color:red'>VIP已过期！</span>";
 }
t.html(sTime);
}

</script>










