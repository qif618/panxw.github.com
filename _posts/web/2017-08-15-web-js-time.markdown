---
layout: post
category: "web"
title:  "JS获取时间并格式化"
tags: [Web]
---


获取当天的零点  

	var todayStart = new Date(new Date().toLocaleDateString());

获取当天的结束时间  

	var oneday = 1000 * 60 * 60 * 24;
	var todayEnd = new Date(todayStart.getTime() + oneday - 1);

获取本月的第一天的零点  

	var monthStart = new Date((new Date(new Date().setDate(1)).toLocaleDateString()));

<!-- more -->

获取本月最后一天结束时间  

	var date=new Date();
	var currentMonth = date.getMonth();//八月份，currentMonth=7
	var nextMonth = ++currentMonth;
	var nextMonthFirstDay=new Date(date.getFullYear(),nextMonth,1);//下个月的第一天零点
	monthEnd = new Date(nextMonthFirstDay.getTime()-1);//将下个月第一天的零点毫秒数减1

将时间格式  
	var pattern = "yyyy-MM-dd hh:mm:ss";//此处hh只能是小写
	var value = date.format();

格式化方法  

	Date.prototype.format = function(fmt){ 
	  var o = { 
	    "M+" : this.getMonth()+1,                 //月份 
	    "d+" : this.getDate(),                    //日 
	    "h+" : this.getHours(),                   //小时 
	    "m+" : this.getMinutes(),                 //分 
	    "s+" : this.getSeconds(),                 //秒 
	    "q+" : Math.floor((this.getMonth()+3)/3), //季度 
	    "S"  : this.getMilliseconds()             //毫秒 
	  }; 
	  if(/(y+)/.test(fmt)) 
	    fmt=fmt.replace(RegExp.$1, (this.getFullYear()+"").substr(4 - RegExp.$1.length)); 
	  for(var k in o) 
	    if(new RegExp("("+ k +")").test(fmt)) 
	  fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length))); 
	  return fmt; 
	}