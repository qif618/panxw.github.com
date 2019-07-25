---
layout: post
category: "springBoot"
title:  "CROS解决跨域"
tags: [SpringBoot]
---

> SpringBoot中解决跨越  

方法上添加跨域  

	@RestController
	public class HelloController {
		@CrossOrigin(value = "http://XXX:8080")
		@GetMapping("/hello")
		public String hello() {
			return "hello";
		}
	}

<!-- more -->

全局配置  

	@Configuration
	public class WebMvcConfig implements WebMvcConfigurer {
		@Override
		public void addCorsMappings(CorsRegistry registry) {
			registry.addMapping("/**")
			.allowedOrigins("http://localhost:8081")
			.allowedMethods("*")
			.allowedHeaders("*");
		}
	}

> SpringMVC上解决跨域  

实现过滤器  

	/**
	 * 支持跨越处理
	 */
	@Component
	@Slf4j
	public class SimpleCORSFilter implements Filter {
	
	    @Override
	    public void destroy() {
	        // TODO Auto-generated method stub
	
	    }
	
	    @Override
	    public void doFilter(ServletRequest req, ServletResponse res,
	                         FilterChain chain) throws IOException, ServletException {
	        HttpServletResponse response = (HttpServletResponse) res;
	        HttpServletRequest request = (HttpServletRequest) req;
	        response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
	        response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE");
	        response.setHeader("Access-Control-Max-Age", "3600");
	        response.setHeader("Access-Control-Allow-Headers", "Origin, No-Cache, X-Requested-With, If-Modified-Since, Pragma, Last-Modified, Cache-Control, Expires, Content-Type, X-E4M-With,userId,token,Access-Control-Allow-Headers");
	        response.setHeader("Access-Control-Allow-Credentials", "true");
	        response.setHeader("XDomainRequestAllowed","1");
	
	        /*response.setHeader("Access-Control-Allow-Origin", "*");
	        response.setHeader("Access-Control-Allow-Credentials", "true");
	
	        response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE");
	        response.setHeader("Access-Control-Max-Age", "3600");
	        response.setHeader("Access-Control-Allow-Headers"," Origin, X-Requested-With, Content-Type, Accept");*/
	        chain.doFilter(req, res);
	    }
	
	    @Override
	    public void init(FilterConfig arg0) throws ServletException {
	        // TODO Auto-generated method stub
	
	    }
	
	}

web.xml中配置  

    <filter>
        <filter-name>cors</filter-name>
        <filter-class>com.sxk.filter.SimpleCORSFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>cors</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>