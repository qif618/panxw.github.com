---
layout: post
category: "web"
title:  "ajax登录"
tags: [Web]
---
### jsp:   ###

        <label>用户名</label><input id="username" name="username" value="${u.username}">
		<label>密码</label><input id="password" name="password" value="${u.password}">
		<span id="error"></span>
		<br><br><br><br><br>
		<input type="button" value="登录" onclick="return loginyz(this)"/> 
 
<!-- more -->  

### js:   ###

	function loginyz(){
    		var n = $("#username").val();
        	if(n == "" || n==null){
        		alert("用户名不能为空");
        		return false;
        	}	
        	var p = $("#password").val();
        	if(p == "" || p==null){
        		alert("密码不能为空");
        		return false;
        	}
        	
        	$.ajax({  
                type:"post",  
                url:"<%=basePath%>user/login",
                dataType:"html",    
                data:{"username":n,"password":p},
                error:function(){  
                    alert("请求失败，网络异常")  
                    console.log(data);  
                },  
                success:function(result){  
                   if(result == "error"){
                	  $("#error").html("用户名或密码错误")
                   }else{
                	  window.location="<%=basePath%>user/index"
                   }
                   
                }  
            });  
        	
    	}    

###  action:   ###

    @RequestMapping("/login")
    	public void load(HttpServletRequest request,HttpServletResponse response,Model model,String username,String password)
    			throws IOException, GeneralSecurityException {
    		HttpServletRequest servletRequest = (HttpServletRequest) request;
   			 HttpSession session = servletRequest.getSession();
    		System.out.println("------------------------------------------"); 
    		System.out.println("前台输入的用户名："+username+";"+"密码："+password); 
    		User u = userService.getByUsername(username);
    		String result = "OK";
    		if(u == null || !u.getPassword().equals(password)) {
    			result = "error";
    		}else {
    			session.setAttribute("username", username);
    		}
    		response.getWriter().write(result); 
    	}