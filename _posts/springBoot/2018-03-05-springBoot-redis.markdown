---
layout: post
category: "springBoot"
title:  "SpringBoot2.0整合redis"
tags: [SpringBoot]
---



> 引入依赖  

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-redis</artifactId>
	</dependency>



> application中配置redis相关参数  

	spring.redis.host=I192.168.1.2
	spring.redis.port=6379
	spring.redis.password=123456
	spring.redis.database=0
	spring.redis.jedis.pool.max-active=200
	spring.redis.jedis.pool.max-wait=-1
	spring.redis.jedis.pool.max-idle=10
	spring.redis.jedis.pool.min-idle=5
	spring.redis.timeout=60000


<!-- more -->  


> 注解的方式配置redis  

	@Configuration
	public class RedisConfig {
	
	    @Bean
	    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
	        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
	        //默认使用JdkSerializationRedisSerializer序列化方式;会出现乱码，改成StringRedisSerializer
	        StringRedisSerializer redisSerializer = new StringRedisSerializer();
	        redisTemplate.setKeySerializer(redisSerializer);//更改相关序列化
	        redisTemplate.setHashKeySerializer(redisSerializer);
	        redisTemplate.setHashValueSerializer(redisSerializer);
	        redisTemplate.setValueSerializer(redisSerializer);
	        redisTemplate.setConnectionFactory(redisConnectionFactory);
	        return redisTemplate;
	    }
	
	    @Bean
	    public HashOperations<String, String, Object> hashOperations(RedisTemplate<String, Object> redisTemplate) {
	        return redisTemplate.opsForHash();
	    }
	
	    @Bean
	    public ValueOperations<String, String> valueOperations(RedisTemplate<String, String> redisTemplate) {
	        return redisTemplate.opsForValue();
	    }
	
	    @Bean
	    public ListOperations<String, Object> listOperations(RedisTemplate<String, Object> redisTemplate) {
	        return redisTemplate.opsForList();
	    }
	
	    @Bean
	    public SetOperations<String, Object> setOperations(RedisTemplate<String, Object> redisTemplate) {
	        return redisTemplate.opsForSet();
	    }
	
	    @Bean
	    public ZSetOperations<String, Object> zSetOperations(RedisTemplate<String, Object> redisTemplate) {
	        return redisTemplate.opsForZSet();
	    }
	}



> 配置redis工具类  

	@Component
	public class RedisUtils {
	    @Autowired
	    private RedisTemplate<String,Object> redisTemplate;
	    @Resource(name="valueOperations")
	    private ValueOperations<String, String> valueOperations;
	    @Resource(name="hashOperations")
	    private HashOperations<String, String, Object> hashOperations;
	    @Resource(name="listOperations")
	    private ListOperations<String, Object> listOperations;
	    @Resource(name="setOperations")
	    private SetOperations<String, Object> setOperations;
	    @Resource(name="zSetOperations")
	    private ZSetOperations<String, Object> zSetOperations;
	    /**  默认过期时长，单位：秒 */
	    public final static long DEFAULT_EXPIRE = 60 * 60 * 24;
	    /**  不设置过期时长 */
	    public final static long NOT_EXPIRE = -1;
	
	
	    public  void set(String key, Object value, long expire){
	        redisTemplate.opsForValue().set(key, toJson(value));
	        if(expire != NOT_EXPIRE){
	            redisTemplate.expire(key, expire, TimeUnit.SECONDS);
	        }
	    }
	
	    public  void set(String key, Object value){
	        set(key, value, DEFAULT_EXPIRE);
	    }
	
	    public  <T> T get(String key, Class<T> clazz, long expire) {
	        String value = valueOperations.get(key);
	        if(expire != NOT_EXPIRE){
	            redisTemplate.expire(key, expire, TimeUnit.SECONDS);
	        }
	        return value == null ? null : fromJson(value, clazz);
	    }
	
	    public  <T> T get(String key, Class<T> clazz) {
	        return get(key, clazz, NOT_EXPIRE);
	    }
	
	    public  String get(String key, long expire) {
	        String value = valueOperations.get(key);
	        if(expire != NOT_EXPIRE){
	            redisTemplate.expire(key, expire, TimeUnit.SECONDS);
	        }
	        return value;
	    }
	
	    public  String get(String key) {
	        return get(key, NOT_EXPIRE);
	    }
	
	    public  void delete(String key) {
	        redisTemplate.delete(key);
	    }
	
	}