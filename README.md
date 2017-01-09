# redis-java-jedis-note
记录在Java代码中，使用spring的spring-data-redis.jar，整合spring与redis的笔记，代码在框架中
# 在maven中，加入
    <dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-redis</artifactId>
			<version>1.6.1.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>redis.clients</groupId>
			<artifactId>jedis</artifactId>
			<version>2.7.3</version>
		</dependency>
		<dependency>
			<groupId>org.redisson</groupId>
			<artifactId>redisson</artifactId>
			<version>2.7.1</version>
		</dependency>
    
# 在spring.xml中配置参数
<!-- spring + redis -->
	<!-- jedis 连接池配置 -->
	<bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
		<property name="minIdle" value="${redis.minIdle}" />
		<property name="maxIdle" value="${redis.maxIdle}" />
		<property name="maxTotal" value="${redis.maxActive}" />
		<property name="maxWaitMillis" value="${redis.maxWait}" />
		<property name="testOnBorrow" value="${redis.testOnBorrow}" />
	</bean>
	<!-- redis服务器中心 -->
	<bean id="jedisConnFactory"
		class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
		<property name="hostName" value="${redis.host}" />
		<property name="port" value="${redis.port}" />
		<property name="password" value="${redis.password}" />
		<property name="usePool" value="true" />
		<property name="poolConfig" ref="poolConfig" />
	</bean>
	
	<!--StringRedisSerializer是通过String的String.getBytes()来实现  -->
	<!--JdkSerializationRedisSerializer是通过JDK提供的  -->
	<bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
		<property name="connectionFactory" ref="jedisConnFactory" />
		<property name="keySerializer">
			<bean
				class="org.springframework.data.redis.serializer.StringRedisSerializer" />
		</property>
		<property name="valueSerializer">
			<bean
				class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
		</property>
		<property name="hashKeySerializer">
			<bean
				class="org.springframework.data.redis.serializer.StringRedisSerializer" />
		</property>
		<property name="hashValueSerializer">
			<bean
				class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
		</property>
	</bean>
  
  在以上配置中，keySerializer实现了StringRedisSerializer；valueSerializer实现了JdkSerializationRedisSerializer
  
# 为什么要使用Serializer？

 redis在存储数据时，都把数据转化为byte[]数组的形式。在存取数据时，需要将数据格式进行转化，那么就需要用到序列化和反序列化，这就是需要配置Serializer的原因。
 
  在项目中，是在配置文件中直接配置了相应的Serializer，key用的是StringRedisSerializer，value用的是JdkSerializationRedisSerializer，因此在项目中，key为userId是String类型，value为user对象是Java类，即POJO，所以使用JdkSerializationRedisSerializer。
