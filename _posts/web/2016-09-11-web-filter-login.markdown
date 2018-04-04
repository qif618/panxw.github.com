---
layout: post
category: "web"
title:  "使用filter校验登录"
tags: [Web]
---
请求先经过过滤器，如果已经登录了，就执行该请求，否则先进入登录界面。  

<!-- more -->
		 @Override
	      public void doFilter(ServletRequest request, ServletResponse response,
	              FilterChain chain) throws IOException, ServletException {
	    	   
	          // 获得在下面代码中要用的request,response,session对象
	          HttpServletRequest servletRequest = (HttpServletRequest) request;
	           HttpServletResponse servletResponse = (HttpServletResponse) response;
	           HttpSession session = servletRequest.getSession();
	           // 获得用户请求的URI
	           String path = servletRequest.getRequestURI();
	           //System.out.println("PATH："+path);
	           
	           // 从session里取员工工号信息
	          String empId = (String) session.getAttribute("username");
	        
	          System.out.println("当前用户："+empId);
	           /*创建类Constants.java，里面写的是无需过滤的页面
	           for (int i = 0; i < Constants.NoFilter_Pages.length; i++) {
	               if (path.indexOf(Constants.NoFilter_Pages[i]) > -1) {
	                   chain.doFilter(servletRequest, servletResponse);
	                   return;
	               }
	           }*/
	           
	          // 登陆页面无需过滤
	          if(path.indexOf("/login.jsp") > -1) {//访问的URL保护login.jsp,放行
	        	  System.out.println("用户名为空，到登录界面");
	              chain.doFilter(servletRequest, servletResponse);
	          }else {//否则，就进入else
	        	  if (empId == null || "".equals(empId)) {//没有登录，用户名为空
		              // 跳转到登陆页面
		        	  if(path.indexOf("/user/login")>-1) {//如果URL为用户提交的登录。放行
		        		  System.out.println("去登录");
		        		  chain.doFilter(request, response);
		        	  }else {//否则，强制跳转到login.jsp
		        		  System.out.println("之前木有登录");
		        		  servletResponse.sendRedirect(servletRequest.getContextPath()+"/jsp/login.jsp");
		        	  }
		           } else {//登录过了，用户名不为空。放行
		               chain.doFilter(request, response);
		           }
	          }
	         
	       }