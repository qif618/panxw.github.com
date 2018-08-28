---
layout: post
category: "spring"
title:  "springMVC配置"
tags: [Spring]
---



## 使用AOP的方式进行鉴权 ##



> 引入依赖  

	<dependency>
	  <groupId>org.aspectj</groupId>
	  <artifactId>aspectjweaver</artifactId>
	  <version>1.7.3</version>
	</dependency>


> 定义Login注解,在不需要登陆的接口方法上注解**@Login(false)**


	/*
	 * 本注解用在Controller层的接口上，表示该接口是否需要登录
	 */
	// 本注解只能用在方法上
	@Target(ElementType.METHOD)
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	public @interface Login {
	
	    // 是否需要登录（默认为true）
	    public boolean value() default true;
	
	}

 <!-- more -->

> 定义AOP切面类  


	/**
	 * @description 使用切面的方式检验权限，访问权限处理类(所有请求都要经过此类)
	 */
	@Aspect
	@Component
	@Slf4j
	public class AccessAuthHandle {
	
	    /** 定义切点 */
	    @Pointcut("execution(public * com.qif.controller..*.*(..))")
	    public void accessAuth(){}
	
	    /**
	     * 拦截所有请求
	     */
	    @Before("accessAuth()")
	    public void doBefore() {
	    }
	
	    @Around("accessAuth()")
	    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
	        //执行方法
	        MethodSignature joinPointObject = (MethodSignature) joinPoint.getSignature();
	        //获得请求的方法
	        Method method = joinPointObject.getMethod();
	        Object result = joinPoint.proceed();//执行到这里，先去执行doBefore(),然后执行下面的代码，最后执行result，即对应的业务controller方法
	        //判断是否需要登陆（含有Login注解，并且为false）
	        Login login = method.getAnnotation(Login.class);
	        if (login != null && !login.value()){//false表示不需要登陆
	            log.info("不需要登陆接口方法："+method.getName());
	        }else{
	            authentication(); // 访问鉴权
	        }
	        return result;
	    }
	
	    /**
	     * 检查当前用户是否允许访问该接口
	     */
	    private void authentication() {
	        // 获取 HttpServletRequest
	        HttpServletRequest request = getHttpServletRequest();
	        // 获取 method 和 url
	        String method = request.getMethod();
	        String url = request.getServletPath();
	        log.info("访问url: " + url+",访问method: " + method);
	        User user = (User) request.getSession().getAttribute(Const.CURRENT_USER);
	        if(user != null){//已登陆，放过
	            return;
	        }
	        throw new CommonBizException(ExpCodeEnum.USER_NOT_LOGIN);
	    }
	
	    /**
	     * 获取HttpServletRequest
	     * @return
	     */
	    private HttpServletRequest getHttpServletRequest() {
	        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
	        ServletRequestAttributes servletRequestAttributes = (ServletRequestAttributes) requestAttributes;
	        return servletRequestAttributes.getRequest();
	    }
	}
