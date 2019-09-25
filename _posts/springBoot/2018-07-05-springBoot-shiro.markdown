---
layout: post
category: "springBoot"
title:  "springboot整合shiro"
tags: [SpringBoot]
---

> 引入依赖  

	<dependency>
		<groupId>org.apache.shiro</groupId>
		<artifactId>shiro-spring</artifactId>
		<version>1.4.0</version>
	</dependency>

<!-- more --> 

编写shiroRealm  

	public class UserRealm extends AuthorizingRealm{
		@Autowired
		private UserService userService;
		
    	//Authorization授权，将数据库中的角色和权限授权给用户名
		@Override
		protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
			User user = (User) principals.getPrimaryPrincipal();
			SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
			Set<String> roleSet = sysUserService.getUserRolesSet(user.getUsername());
			info.setRoles(roleSet);
			// 设置用户拥有的权限集合，比如“sys:role:add,sys:user:add”
			Set<String> permissionSet = userService.getUserPermissionsSet(user.getUsername());
			info.addStringPermissions(permissionSet);
			return info;
		}

		//用户身份验证
		@Override
		protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException { 
			UsernamePasswordToken token = (UsernamePasswordToken) token;
		    User user = new User();
		    user.setName(token.getUsername());
			User userDb = UserService.findUser(user);
			if(userDb == null){
				throw new AuthenticationException("用户不存在!");
			}
			return new SimpleAuthenticationInfo(userDb,userDb.getPassword(),getName());
		}
	 
		
	}



> 配置密码验证器  

	//自定义密码验证器，shiro调用此方法与将数据库中密码与前端进行对比
	public class MyMatcher extends SimpleCredentialsMatcher {
	 
	    @Override
	    public boolean doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info) {
	        UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
	        String pwd = encrypt(String.valueOf(usernamePasswordToken.getPassword()));
	        String pwdDb = (String) info.getCredentials();
	        return this.equals(pwd, pwdDb);
	    }
	 
	    //将传进来的密码进行加密的方法
	    private String encrypt(String data){
	        String encryptPwd = new Sha384Hash(data).toBase64();
	        return encryptPwd;
	    }
	 
	}



> 配置记住我过滤器  

	//过滤器实现当用户通过isRemembered（）登陆，没有通过isAuthenticated（）登陆时拿到user的session信息
	//过滤器完成了登陆条件的过滤：要么通过权限认证登陆成功，要么通过记住我登陆成功
	public class MyRememberFilter extends FormAuthenticationFilter {
	 
	    protected boolean isAccessAllowed(HttpServletRequest request, HttpServletResponse response, Object mappedValue){
	        Subject subject=getSubject(request,response);
	        if(!subject.isAuthenticated() && subject.isRemembered()){
	            if(subject.getSession().getAttribute("user")==null && subject.getPrincipal()!=null){
	                subject.getSession().setAttribute("user",subject.getPrincipal());
	            }
	 
	        }
	 
	        return subject.isAuthenticated() || subject.isRemembered();
	    }
	}

> 编写shiroCofig  

	@Configuration
	public class ShiroConfig {

		//配置密码验证器
	 	@Bean("credentialsMatcher")
	    public CredentialsMatcher credentialsMatcher() {
	        return new MyMatcher();
		}

		//配置权限验证器,并权限验证器加上自定义的密码验证器
	    @Bean
	    public UserRealm userRealm() {
	        UserRealm userRealm = new UserRealm();
			userRealm.setCredentialsMatcher(credentialsMatcher);
	    }

    	//配置记住我cookie记录参数,该rememberMeCookie会被添加到"记住我"管理器中
	    @Bean
	    public SimpleCookie rememberMeCookie() {
			//这个参数是cookie的名称，对应前端的checkbox的name = rememberMe
	        SimpleCookie simpleCookie = new SimpleCookie("rememberMe");
			//httyOnly设置为true，则客户端不会暴露给客户端脚本代码，使用HttpOnly cookie有助于减少某些类型的跨站点脚本攻击；
	        simpleCookie.setHttpOnly(true);
	        simpleCookie.setMaxAge(7 * 24 * 60 * 60);//7天
	        return simpleCookie;
	    }
	
		//配置"记住我"管理器,会被添加到securityManager中
	    @Bean
	    public CookieRememberMeManager rememberMeManager(SimpleCookie rememberMeCookie) {
	        CookieRememberMeManager cookieManager = new CookieRememberMeManager();
			/rememberme cookie加密的密钥 建议每个项目都不一样 默认AES算法 密钥长度（128 256 512 位）
	        cookieManager.setCipherKey(Base64.decode("qif6666666666=="));
	        cookieManager.setCookie(rememberMeCookie);
	        return cookieManager;
	    }

	   
	 	/**
	     * session管理器
	     */
	    @Bean
	    public DefaultWebSessionManager defaultWebSessionManager(CacheManager cacheShiroManager) {
	        DefaultWebSessionManager sessionManager = new DefaultWebSessionManager();
	        sessionManager.setCacheManager(cacheShiroManager);// 加入缓存管理器
	        sessionManager.setSessionValidationInterval(30 * 60 * 1000);//验证session失效时间
	        sessionManager.setGlobalSessionTimeout(10 * 60 * 1000);//session失效时间
	        sessionManager.setDeleteInvalidSessions(true);// 删除过期的session
	        sessionManager.setSessionValidationSchedulerEnabled(true); // 是否定时检查session
	        Cookie cookie = new SimpleCookie(ShiroHttpSession.DEFAULT_SESSION_ID_NAME);
	        cookie.setName("shiroCookie");
	        cookie.setHttpOnly(true);
	        sessionManager.setSessionIdCookie(cookie);
	        return sessionManager;
	    }
	
	    //缓存管理器 使用Ehcache实现
	    @Bean
	    public CacheManager getCacheShiroManager(EhCacheManagerFactoryBean ehcache) {
	        EhCacheManager ehCacheManager = new EhCacheManager();
	        ehCacheManager.setCacheManager(ehcache.getObject());
	        return ehCacheManager;
	    }
	
	    //配置securityManager安全管理器
	    @Bean
	    public DefaultWebSecurityManager securityManager(CookieRememberMeManager rememberMeManager, CacheManager cacheShiroManager, SessionManager sessionManager) {
	        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
	        securityManager.setRealm(this.shiroDbRealm());
	        securityManager.setCacheManager(cacheShiroManager);
	        securityManager.setRememberMeManager(rememberMeManager);
	        securityManager.setSessionManager(sessionManager);
	        return securityManager;
	    }

		//注入自定义的记住我过滤器
		@Bean
	    public MyRememberFilter myRememberFilter(){
	        return new MyRememberFilter();
		}


	    //Shiro的过滤器链
	    @Bean
	    public ShiroFilterFactoryBean shiroFilter(DefaultWebSecurityManager securityManager) {
	        ShiroFilterFactoryBean shiroFilter = new ShiroFilterFactoryBean();
	        shiroFilter.setSecurityManager(securityManager);
	      
	        shiroFilter.setLoginUrl("/login");//默认的登录URL
	  
	        shiroFilter.setSuccessUrl("/index");//登陆成功后跳转的url
	      
	        shiroFilter.setUnauthorizedUrl("/global/error");//没有权限跳转的url
	
	       
	        HashMap<String, Filter> myFilters = new HashMap<>();
      		filterMap.put("myRememberFilter",myRememberFilter());
 			//覆盖默认的user拦截器(默认拦截器解决不了ajax请求 session超时的问题)
	        myFilters.put("user", new ajaxFilter());
	        shiroFilter.setFilters(myFilters);
	
	        /**
	         * 配置shiro拦截器链
	         * anon  不需要认证
	         * authc 需要认证
	         * user  验证通过或RememberMe登录的都可以
	         * 当应用开启了rememberMe时,用户下次访问时可以是一个user,但不会是authc,因为authc是需要重新认证的
	         * 顺序从上到下,优先级依次降低
	         *
	         */

			LinkedHashMap<String, String> linkedHashMap = new LinkedHashMap<String, String>();
			linkedHashMap.put("/api/**", "anon");//api开头的接口，走rest api鉴权，不走shiro鉴权
	        linkedHashMap.put("/logout", "logout");//在这儿配置登出地址，不需要专门去写控制器。
	        linkedHashMap.put("/static/**", "anon");
	        //开启注册页面不需要权限
	        linkedHashMap.put("/register", "anon");
	        linkedHashMap.put("/saveregister", "anon");
	        //验证phone唯一
	        linkedHashMap.put("/solephone", "anon");
	        //获取验证码
	        linkedHashMap.put("/getcode", "anon");
	        //验证码判断
	        linkedHashMap.put("/comparecode", "anon");
	        linkedHashMap.put("/websocket", "anon");//必须开启。
	        linkedHashMap.put("/css/**", "anon");//不需要验证
	        linkedHashMap.put("/js/**", "anon");//不需要验证
	        //配置错误页面
	        linkedHashMap.put("error", "anon");//不需要验证
	        linkedHashMap.put("/img/**", "anon");//不需要验证
	        linkedHashMap.put("/layui/**", "anon");//不需要验证
	        linkedHashMap.put("/video/**", "anon");//不需要验证
	        linkedHashMap.put("/bower_components/**", "anon");//不需要验证
	        linkedHashMap.put("/plugins/**", "anon");//不需要验证
	        linkedHashMap.put("/dist/**", "anon");//不需要验证
	        linkedHashMap.put("/**", "user");//需要进行权限验证,authc比authc权限低，启用记住我需用user
	        shiroFilter.setFilterChainDefinitionMap(linkedHashMap);
	        return shiroFilter;
	    }
	
	    /**
	     * 在方法中 注入 securityManager,进行代理控制
	     */
	    @Bean
	    public MethodInvokingFactoryBean methodInvokingFactoryBean(DefaultWebSecurityManager securityManager) {
	        MethodInvokingFactoryBean bean = new MethodInvokingFactoryBean();
	        bean.setStaticMethod("org.apache.shiro.SecurityUtils.setSecurityManager");
	        bean.setArguments(securityManager);
	        return bean;
	    }
	
	    /**
	     * Shiro生命周期处理器:
	     * 用于在实现了Initializable接口的Shiro bean初始化时调用Initializable接口回调(例如:UserRealm)
	     * 在实现了Destroyable接口的Shiro bean销毁时调用 Destroyable接口回调(例如:DefaultSecurityManager)
	     */
	    @Bean
	    public LifecycleBeanPostProcessor lifecycleBeanPostProcessor() {
	        return new LifecycleBeanPostProcessor();
	    }
	
	    /**
	     * 启用shrio授权注解拦截方式，AOP式方法级权限检查
	     */
	    @Bean
	    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(DefaultWebSecurityManager securityManager) {
	        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor =
	                new AuthorizationAttributeSourceAdvisor();
	        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager);
	        return authorizationAttributeSourceAdvisor;
	    }
	
	}