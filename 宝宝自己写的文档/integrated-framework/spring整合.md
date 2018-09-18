# spring整合

### 1,创建maven项目，并导包。

```xml
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.apache.tomcat</groupId>
				<artifactId>tomcat</artifactId>
				<version>8.5.5</version>
				<type>zip</type>
			</dependency>
			<dependency>
				<groupId>org.codehaus.cargo</groupId>
				<artifactId>cargo-maven2-plugin</artifactId>
				<version>1.5.1</version>
			</dependency>
			<dependency>
				<groupId>junit</groupId>
				<artifactId>junit</artifactId>
				<version>4.12</version>
				<scope>test</scope>
			</dependency>
			<dependency>
				<groupId>javax.servlet</groupId>
				<artifactId>servlet-api</artifactId>
				<version>3.0-alpha-1</version>
				<scope>provided</scope>
			</dependency>

			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
				<version>5.1.23</version>
			</dependency>

			<dependency>
				<groupId>org.apache.struts</groupId>
				<artifactId>struts2-core</artifactId>
				<version>2.3.31</version>
			</dependency>

			<dependency>
				<groupId>org.apache.struts</groupId>
				<artifactId>struts2-convention-plugin</artifactId>
				<version>2.3.31</version>
			</dependency>

			<dependency>
				<groupId>org.apache.struts</groupId>
				<artifactId>struts2-json-plugin</artifactId>
				<version>2.3.31</version>
			</dependency>


			<dependency>
				<groupId>org.apache.struts</groupId>
				<artifactId>struts2-spring-plugin</artifactId>
				<version>2.3.31</version>
			</dependency>

			<dependency>
				<groupId>log4j</groupId>
				<artifactId>log4j</artifactId>
				<version>1.2.17</version>
			</dependency>

			<dependency>
				<groupId>org.slf4j</groupId>
				<artifactId>slf4j-log4j12</artifactId>
				<version>1.7.25</version>
			</dependency>

			<dependency>
				<groupId>c3p0</groupId>
				<artifactId>c3p0</artifactId>
				<version>0.9.1.2</version>
			</dependency>

			<dependency>
				<groupId>org.mybatis</groupId>
				<artifactId>mybatis</artifactId>
				<version>3.4.5</version>
			</dependency>

			<dependency>
				<groupId>org.mybatis</groupId>
				<artifactId>mybatis-spring</artifactId>
				<version>1.3.2</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-aop</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-aspects</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-beans</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-context</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-core</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-expression</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-jdbc</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-orm</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-tx</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-web</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-webmvc</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>

			<dependency>
				<groupId>jstl</groupId>
				<artifactId>jstl</artifactId>
				<version>1.2</version>
			</dependency>
		</dependencies>
```

### 2，配置web.xml

1. 配置struts2过滤器

   ```xml
   	<filter>
   		<filter-name>struts2</filter-name>
   		<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
   	</filter>
   	<filter-mapping>
   		<filter-name>struts2</filter-name>
   		<url-pattern>*.action</url-pattern>
   	</filter-mapping>
   	<filter-mapping>
   		<filter-name>struts2</filter-name>
   		<url-pattern>*.jsp</url-pattern>
   	</filter-mapping>
   ```

2. 配置spring

   ```xml
   	<context-param>
   		<param-name>contextConfigLocation</param-name>
   		<param-value>classpath:spring.xml</param-value>
   	</context-param>
   	<listener>
   		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   	</listener>
   ```

   ​

### 3,在resource文件夹下新建spring.xml并配置mybatis映射文件

````xml
<!-- 扫描器 -->
    <context:component-scan base-package="com.znsd" />
    
	<context:property-placeholder location="classpath:jdbc.properties" />
   
	<!-- 配置c3p0连接池属性 -->
	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"
		destroy-method="close">
		<property name="driverClass" value="${jdbc.driver}" />
		<property name="jdbcUrl" value="${jdbc.url}" />
		<property name="user" value="${jdbc.user}" />
		<property name="password" value="${jdbc.password}" />
		<!-- 队列中的最小连接数 -->
		<property name="minPoolSize" value="${c3p0.minPoolSize}"></property>
		<!-- 队列中的最大连接数 -->
		<property name="maxPoolSize" value="${c3p0.maxPoolSize}"></property>
		<!-- 当连接耗尽时创建的连接数 -->
		<property name="acquireIncrement" value="${c3p0.acquireIncrement}"></property>
		<!-- 等待时间 -->
		<property name="checkoutTimeout" value="${c3p0.checkoutTimeout}"></property>

		<!-- 初始化连接数 -->
		<property name="initialPoolSize" value="${c3p0.initialPoolSize}"></property>
		<!-- 最大空闲时间，超出时间连接将被丢弃 -->
		<property name="maxIdleTime" value="${c3p0.maxIdleTime}"></property>
		<!-- 每隔60秒检测空闲连接 -->
		<property name="idleConnectionTestPeriod" value="${c3p0.idleConnectionTestPeriod}"></property>
	</bean>
	
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="mapperLocations">
			<list>
	<!--自动扫描mapper.xml文件 *文件夹下表示所有文件，也可以单独在mybatis-config.xml中单独配置 -->
            	<value>classpath:mybatis-mapping/*.xml</value>
			</list>
		</property>
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	<bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<!-- mybatis 接口包（如果有多个可以用,逗号隔开） -->
	    <property name="basePackage" value="com.znsd.dao" />
	    <!-- sqlSession工厂beanId -->
	    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
	    <!-- 指定dao层接口的注解 -->
	    <property name="annotationClass" value="org.springframework.stereotype.Repository" />
	</bean>
	
	<!-- 定义事务管理器（声明式的事务） -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	    <property name="dataSource" ref="dataSource" />
	</bean>
	
	<!-- AOP切面拦截事务 -->
	<aop:config>
		<aop:pointcut id="serviceMethod"
			expression="execution(* com.znsd.service.impl.*.*(..))" />
		<aop:advisor advice-ref="txAdvice" pointcut-ref="serviceMethod" />
	</aop:config>
	
	<!-- 事务的通知方式 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- read-only 事务为只读，一般查询数据可把该属性设置为true，可以提升效率 -->
			<tx:method name="find*" propagation="REQUIRED" read-only="true" />
			<tx:method name="search*" propagation="REQUIRED" read-only="true" />
			<tx:method name="query*" propagation="REQUIRED" read-only="true" />
			<tx:method name="select*" propagation="REQUIRED" read-only="true" />
			<!-- propagation为事务的传播属性，常用为REQUIREDz：如果当前线程有事务就直接使用当前事务，没有就创建一个事务 -->
			<tx:method name="add*" propagation="REQUIRED" />
			<tx:method name="submit*" propagation="REQUIRED" />
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="insert*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
		</tx:attributes>
	</tx:advice>
````

- 并配置好jdbc.properties

```properties
## jdbc 连接配置
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://192.168.21.98:3306/exam?characterEncoding=utf8
jdbc.user=root
jdbc.password=root

## c3p0 数据源配置
c3p0.minPoolSize=15
c3p0.maxPoolSize=25
c3p0.acquireIncrement=15
c3p0.checkoutTimeout=10000
c3p0.initialPoolSize=20
c3p0.maxIdleTime=20
c3p0.idleConnectionTestPeriod=60000
```

- 配置mybatis-mapping

````xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.znsd.dao.UserDao">
	<resultMap type="com.znsd.bean.User" id="userMapping">
		<id property="id" column="id" />
		<result property="username" column="username" />
		<result property="password" column="password" />
		<result property="name" column="name" />
		<result property="email" column="email" />
		<result property="sex" column="sex" />
		<result property="birthday" column="birthday" />
		<result property="createTime" column="createTime" />
		<result property="updateTime" column="updateTime" />
		<association property="clazz" column="classId" javaType="com.znsd.bean.Clazz">
			<id property="classId" column="classId" />
			<result property="className" column="className" />
		</association>
	</resultMap>
	
	
	<resultMap type="com.znsd.bean.User" id="userMapping_role">
		<id property="id" column="id" />
		<result property="username" column="username" />
		<result property="password" column="password" />
		<result property="name" column="name" />
		<result property="email" column="email" />
		<result property="sex" column="sex" />
		<result property="birthday" column="birthday" />
		<result property="createTime" column="createTime" />
		<result property="updateTime" column="updateTime" />
		<collection property="roleList" ofType="com.znsd.bean.Role" column="r_id">
			<id property="id" column="r_id"/>
			<result property="name" column="r_name"/>
		</collection>
	</resultMap>
	
	<select id="login" resultType="com.znsd.bean.User" parameterType="com.znsd.bean.User">
		select * from user where username = #{username} and password = #{password} 
	</select>

	<select id="select" parameterType="java.util.Map" resultMap="userMapping">
		select
			u.id,u.username,u.password,u.name,u.email,u.sex,u.birthday,u.createTime,u.updateTime,c.classId,c.className
		from
			user u
		LEFT JOIN
			class c
		on
			c.classId = u.classid
		where
			u.state = 1
        LIMIT 
        	#{pageSize},#{rows}
	</select>
	<select id="count" resultType="int">
		select count(*) from user where
		state = 1
	</select>
	<update id="delete" parameterType="com.znsd.bean.User">
		update user set state = 0 where id = #{id}
	</update>
	<update id="deleteUR" parameterType="com.znsd.bean.User">
		update u_r set state = 0 where u_id = #{id}
	</update>
	<select id="selectOne"  parameterType="com.znsd.bean.User" resultType="com.znsd.bean.User">
		select * from user where id = #{id}
	</select>
	<select id="selectRoleList" parameterType="com.znsd.bean.User" resultType="com.znsd.bean.Role">
		select r.id,r.name from u_r 
			left join role r on u_r.r_id = r.id  where u_id = #{id} and u_r.state = 1
	</select>
	<insert id="distAdd" parameterType="java.util.Map" >
		insert into u_r(u_id,r_id,state) values(#{u_id},#{r_id},1)
	</insert>
	<update id="distDelete" parameterType="java.util.Map" >
		update u_r set state = 0 where u_id = #{u_id} and r_id = #{r_id}
	</update>
</mapper>
````

### 4,创建action，service，dao，bean类

- bean

```java
package com.znsd.bean;

import java.io.Serializable;
import java.util.Date;
import java.util.List;

/**
 * @Project: exam-bean
 * @Package com.znsd.bean
 * @Title: User.java
 * @Description: 用户bean
 * @author: qianyi
 * @date 2018年8月1日 下午7:30:41
 * @Copyright: ZNSD Co.,Ltd. All rights reserved.
 * @version V1.0
 */
public class User implements Serializable {

	private static final long serialVersionUID = 4235764650433496113L;

	private Integer id;//用户id
	
	private String username;//用户名字
	
	private String password;//用户密码
	
	private Integer state;//状态
	
	private Date createTime;//创建时间
	
	private Date updateTime;//修改时间
	
	private String name;	//姓名
	private Clazz clazz;	//所属班级
	private String email;	//邮箱
	private Integer sex;		//性别
	private Date birthday;	//出生日期

	private List<Role> roleList;
	
	public List<Role> getRoleList() {
		return roleList;
	}

	public void setRoleList(List<Role> roleList) {
		this.roleList = roleList;
	}

	public User() {
		super();
	}

	public Integer getState() {
		return state;
	}

	public void setState(Integer state) {
		this.state = state;
	}

	public Date getCreateTime() {
		return createTime;
	}

	public void setCreateTime(Date createTime) {
		this.createTime = createTime;
	}

	public Date getUpdateTime() {
		return updateTime;
	}

	public void setUpdateTime(Date updateTime) {
		this.updateTime = updateTime;
	}
	
	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Clazz getClazz() {
		return clazz;
	}

	public void setClazz(Clazz clazz) {
		this.clazz = clazz;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public Integer getSex() {
		return sex;
	}

	public void setSex(Integer sex) {
		this.sex = sex;
	}

	public Date getBirthday() {
		return birthday;
	}

	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}

	@Override
	public String toString() {
		return "User [id=" + id + ", username=" + username + ", password=" + password + ", state=" + state
				+ ", createTime=" + createTime + ", updateTime=" + updateTime + ", name=" + name + ", clazz=" + clazz
				+ ", email=" + email + ", sex=" + sex + ", birthday=" + birthday + ", roleList=" + roleList + "]";
	}
	
}

```

- dao

```java
package com.znsd.dao;

import java.util.List;
import java.util.Map;

import org.springframework.stereotype.Repository;

import com.znsd.bean.Role;
import com.znsd.bean.User;

/**
 * @Project: exam-dao
 * @Package com.znsd.dao
 * @Title: UserDao.java
 * @Description: 用户持久层，用于连接数据库，使用mybaits操作数据库
 * @author: qianyi
 * @date 2018年8月1日 下午9:43:39
 * @Copyright: ZNSD Co.,Ltd. All rights reserved.
 * @version V1.0
 */
@Repository
public interface UserDao {

	public User login(User user);

	public int add(User user);
	
	public int delete(User user);
	
	public int deleteUR(User user);
	
	public List<User> select(Map<String,Object> paramMap);
	
	public int update(User user);
	
	public int count();

	public int distAdd(Map<String,Object> paramMap);
	
	public int distDelete(Map<String,Object> paramMap);
	
	public User selectOne(User user);
	
	public List<Role> selectRoleList(User user);
}

```

- service

```java
package com.znsd.service.impl;

import java.util.List;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

import com.znsd.bean.User;
import com.znsd.dao.UserDao;
import com.znsd.service.UserService;

@Service
public class UserServiceImpl implements UserService {

	@Autowired
	@Qualifier("userDao")
	private UserDao userDao;
	
	public UserDao getUserDao() {
		return userDao;
	}

	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}

	public User login(User user) {
		return this.userDao.login(user);
	}

	public int add(User user) {
		return this.userDao.add(user);
	}

	public int delete(User user) {
		this.userDao.deleteUR(user);
		return this.userDao.delete(user);
	}

	public List<User> select(Map<String, Object> paramMap) {
		if(paramMap.get("rows")==null||((Integer)paramMap.get("rows"))==0){
			paramMap.put("pageSize", 0);
			paramMap.put("rows", count());
		}
		return this.userDao.select(paramMap);
	}

	public int update(User user) {
		return this.userDao.update(user);
	}

	public int count() {
		return this.userDao.count();
	}

	public int distAdd(Map<String, Object> paramMap) {
		return this.userDao.distAdd(paramMap);
	}

	public int distDelete(Map<String, Object> paramMap) {
		return this.userDao.distDelete(paramMap);
	}

	public User selectOne(User user) {
		user = this.userDao.selectOne(user);
		user.setRoleList(this.userDao.selectRoleList(user));
		return user;
	}

}

```

- action

```java
package com.znsd.action;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.struts2.convention.annotation.Action;
import org.apache.struts2.convention.annotation.Namespace;
import org.apache.struts2.convention.annotation.ParentPackage;
import org.apache.struts2.convention.annotation.Result;
import org.apache.struts2.interceptor.ServletRequestAware;
import org.apache.struts2.interceptor.ServletResponseAware;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;

import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;
import com.znsd.bean.Power;
import com.znsd.bean.Role;
import com.znsd.bean.User;
import com.znsd.service.PowerService;
import com.znsd.service.RoleService;
import com.znsd.service.UserService;

/**
 * @Project: exam-web
 * @Package com.znsd.action
 * @Title: UserAction.java
 * @Description: 用户的action里面有用户的增删查改与登录分配权限
 * @author: qianyi
 * @date 2018年8月1日 下午9:41:40
 * @Copyright: ZNSD Co.,Ltd. All rights reserved.
 * @version V1.0
 */

@Controller
@Namespace("/user")
@Scope("property")
@ParentPackage("json-default")
public class UserAction extends ActionSupport implements ServletRequestAware  {

	private static final long serialVersionUID = 257961950878210258L;
	

	private HttpServletRequest servletRequest;
    
	@Override
	public void setServletRequest(HttpServletRequest request) {
        this.servletRequest = request;
	}

	private User user;

	private int page;

	private int rows;

	@Autowired
	@Qualifier("userServiceImpl")
	private UserService userService;

	@Autowired
	@Qualifier("roleServiceImpl")
	private RoleService roleService;

	@Autowired
	@Qualifier("powerServiceImpl")
	private PowerService powerService;
	
	private Map<String, Object> resultMap = new HashMap<>();

	private Map<String, Object> paramMap = new HashMap<>();

	private List<Object> list = new ArrayList<>();

	private String str;

	private int id;

	public String getStr() {
		return str;
	}

	public void setStr(String str) {
		this.str = str;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public List<Object> getList() {
		return list;
	}

	public void setList(List<Object> list) {
		this.list = list;
	}

	public RoleService getRoleService() {
		return roleService;
	}

	public void setRoleService(RoleService roleService) {
		this.roleService = roleService;
	}

	public int getPage() {
		return page;
	}

	public void setPage(int page) {
		this.page = page;
	}

	public int getRows() {
		return rows;
	}

	public void setRows(int rows) {
		this.rows = rows;
	}

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}

	public UserService getUserService() {
		return userService;
	}

	public void setUserService(UserService userService) {
		this.userService = userService;
	}

	public Map<String, Object> getResultMap() {
		return resultMap;
	}

	public void setResultMap(Map<String, Object> resultMap) {
		this.resultMap = resultMap;
	}

	public Map<String, Object> getParamMap() {
		return paramMap;
	}

	public void setParamMap(Map<String, Object> paramMap) {
		this.paramMap = paramMap;
	}

	/**
	 * @Description 登录
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:10:49
	 */
	@Action(value = "login", results = { @Result(name = "success", location = "/main.jsp", type = "redirect"),
			@Result(name = "input", location = "/login.jsp") })
	public String login() {
		user = userService.login(user);
		if (user == null) {
			str = "用户名或密码有误！";
			return INPUT;
		}
		user = userService.selectOne(user);
		List<Role> roleList = new ArrayList<>();
		for(Role role : user.getRoleList()){
			role = roleService.selectOne(role);
			List<Power> powerList = new ArrayList<>();
			for(Power power : role.getPowers()){
				powerList.add(powerService.selectOne(power));
			}
			role.setPowers(powerList);
			roleList.add(role);
		}
		user.setRoleList(roleList);
		ActionContext.getContext().getSession().put("user", user);
		return SUCCESS;
	}

	/**
	 * @Description 增加后面没写了
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:11:45
	 */
	@Action(value = "/userAdd", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "resultMap", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String add() {
		if (userService.add(user) != 1) {
			resultMap.put("code", 0);
		} else {
			resultMap.put("code", 1);
		}
		return SUCCESS;
	}
	
	@Action(value = "/userExit", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "resultMap", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String exit() {
		ActionContext.getContext().getSession().remove("user");
		resultMap.put("code", 1);
		return SUCCESS;
	}

	/**
	 * @Description 删除
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:12:12
	 */
	@Action(value = "/userDelete", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "resultMap", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String delete() {
		if (userService.delete(user) != 1) {
			resultMap.put("code", 0);
		} else {
			resultMap.put("code", 1);
		}
		return SUCCESS;
	}

	@Action(value = "/userUpdate", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "resultMap", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String update() {
		if (userService.update(user) != 1) {
			resultMap.put("code", 0);
		} else {
			resultMap.put("code", 1);
		}
		return SUCCESS;
	}

	@Action(value = "/userSelect", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "resultMap", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String select() {
		int count = userService.count();// 总数
		int pageSize = (page - 1) * rows;
		paramMap.put("pageSize", pageSize);
		paramMap.put("rows", rows);
		List<User> array = userService.select(paramMap);
		resultMap.put("total", count);
		resultMap.put("rows", array);
		return SUCCESS;
	}

	/**
	 * @Description 创建一个分配角色的页面
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 上午10:16:46
	 */
	@Action(value = "/createDistRole", results = @Result(name = "success", location = "/user/dist.jsp"))
	public String createDist() {
		user = userService.selectOne(user);
		return SUCCESS;
	}

	/**
	 * @Description 树
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:27:56
	 */
	@Action(value = "/createRoleTree", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "list", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String tree() {
		List<Role> roleList = roleService.select(paramMap);
		user = userService.selectOne(user);
		resultMap.put("id", 0);
		resultMap.put("text", "全选");
		List<Map<String, Object>> sonList = new ArrayList<>();
		for (Role role : roleList) {
			Map<String, Object> map = new HashMap<>();
			map.put("id", role.getId());
			map.put("text", role.getName());
			checkPower(map);
			sonList.add(map);
			resultMap.put("children", sonList);
		}
		list.add(resultMap);
		return SUCCESS;
	}

	/**
	 * @Description 分配角色
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:28:16
	 */
	@Action(value = "/distRole", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "resultMap", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String distRole() {
		user = new User();
		user.setId(id);
		user = userService.selectOne(user);
		paramMap.put("u_id", id);
		String[] strlist = str.split(",");
		deWeight(strlist);
		resultMap.put("code", 1);
		return SUCCESS;
	}

	/**
	 * @Description 一个去重算法
	 * @param strlist
	 * @author qianyi
	 * @date 2018年8月6日 下午5:28:51
	 */
	public void deWeight(String[] strlist) {//选择的节点id 刚刚切割出来的所以是String数组
		for (Role role : user.getRoleList()) {//循环用户下面的所有角色
			boolean mark = true;//控制是否删除
			for (int i = 0; i < strlist.length; i++) {//循环选中的所有节点
				//    当选中的节点id不为空						&&			如果角色的id 等于选中的id				&&     当选中的节点id不为0
				if (strlist[i]!=null && !"".equals(strlist[i]) && role.getId() == Integer.parseInt(strlist[i]) && Integer.parseInt(strlist[i])==0) {
					mark = false;//重复就不删除
				}
			}
			if (mark) {
				System.out.println("调用删除");
				paramMap.put("r_id", role.getId());//中间表有俩数据一个是u_id userID 和  r_id roleID
				userService.distDelete(paramMap);
			}
		}
		for (int i = 0; i < strlist.length; i++) {
			boolean mark = true;
			for (Role role : user.getRoleList()) {
				//    当选中的节点id不为空						&&			如果角色的id 等于选中的id			
				if ( strlist[i]!=null && !"".equals(strlist[i]) && role.getId() == Integer.parseInt(strlist[i]) ) {
					mark = false;
				}
			}
			if (strlist[i]!=null && !"".equals(strlist[i]) && mark && Integer.parseInt(strlist[i])!=0) {
				System.out.println("调用增加");
				paramMap.put("r_id", Integer.parseInt(strlist[i]));
				userService.distAdd(paramMap);
			}
		}
	}

	/**
	 * @Description 检查是否选中
	 * @param map
	 * @author qianyi
	 * @date 2018年8月6日 下午5:29:16
	 */
	public void checkPower(Map<String, Object> map) {
		for (Role checkPower : user.getRoleList()) {
			if (checkPower.getId() == map.get("id")) {
				map.put("checked", true);
			}
		}
	}

	/**
	 * @Description 菜单树
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:29:38
	 */
	@Action(value = "/menuTree", results = @Result(type = "json", params = { "noCache", "true", // 是否启用缓存
			"contentType", "text/html", // 设置响应的内容类型
			"root", "list", // 设置根对象
			"ignoreHierarchy", "true" // 忽略基类
	}))
	public String menuTree() {
		user = (User) ActionContext.getContext().getSession().get("user");
		user = userService.selectOne(user); 
		List<Role> roleList = user.getRoleList();
		for (Role role : roleList) {
			role = roleService.selectOne(role);
//			System.out.println(role);
			List<Power> array = role.getPowers();
			for (int i = 0; i < array.size(); i++) {
//				System.out.println(array.get(i));
				Power power = powerService.selectOne(array.get(i));
				if (array.get(i)==null) {
					continue;
				}
				if ( power.getParent() != null && power.getParent().getId() == 0) {
					Map<String, Object> map = new HashMap<String, Object>();
					map.put("children", recurrence(array, power.getId()));
					map.put("id", power.getId());
					map.put("text", power.getName());
					if (power.getUrl() != null && !"".equals(power.getUrl())) {
						map.put("path", power.getUrl());
					}
					if(power.getMenu() == 1 ){
						list.add(map);
					}
				}
			}
		}
//		System.out.println(list);
		return SUCCESS;
	}
	
	/**
	 * @Description 通过递归查询所有子节点并拼接好
	 * @param list
	 * @param id
	 * @return
	 * @author qianyi
	 * @date 2018年8月6日 下午5:50:19
	 */
	public List<Object> recurrence(List<Power> list, int id) {
		List<Object> sonList = new ArrayList<>();
		for (int i = 0; i < list.size(); i++) {
			Power power = powerService.selectOne(list.get(i));
			if (power.getParent() != null && power.getParent().getId() == id) {
				Map<String, Object> map = new HashMap<String, Object>();
				map.put("children", recurrence(list, power.getId()));
				map.put("id", power.getId());
				map.put("text", power.getName());
				if (power.getUrl() != null && !"".equals(power.getUrl())) {
					map.put("path",servletRequest.getContextPath() + power.getUrl());
				}
				if(power.getMenu() == 1 ){
					sonList.add(map);
				}
				// System.out.println("recurrence:"+sonList);
			}
		}
		if (sonList.size() == 0) {
			return null;
		}
		return sonList;
	}


}

```