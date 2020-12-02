* **概述:**
    * spring为展现层提供的基于MVC设计理念的Web框架;
    * 支持REST风格的URL请求;
    * 采用了松散耦合可插拔组件结构,比其他MVC框架更具扩展性和灵活性;
* **是什么:**
    * 一种轻量级的基于MVC的Web层应用框架,偏前端而不是基于业务逻辑层;
* **能干什么?:**
    * 天生与Spring框架集成:如:(IOC,AOP);
    * 支持Restful风格;
    * 更简洁的web层开发;
    * 支持灵活的URL到页面控制器的映射;
    * 简单强大的异常处理;
    * 对静态资源的支持;

---


* **spring框架图:**

<img src="https://uploader.shimo.im/f/E2W9GWEev7BpfnIl.png!thumbnail" alt="图片" style="zoom: 67%;" />


---



* **常用组件:**
    * DispacherServlet:前端控制器;
    * Controller:页面控制器
    * HandlerMapping:请求映射到处理器,找谁来处理.
    * ViewResovler:视图解析器,找谁来处理返回的页面

---


* **HELLO WORLD:**
    * jar包
```plain
spring-aop-4.0.0.RELEASE.jar
spring-beans-4.0.0.RELEASE.jar
spring-context-4.0.0.RELEASE.jar
spring-core-4.0.0.RELEASE.jar
spring-expression-4.0.0.RELEASE.jar
commons-logging-1.1.3.jar
spring-web-4.0.0.RELEASE.jar
spring-webmvc-4.0.0.RELEASE.jar
```
    * web.xml中配置DispatcherServlet(是个servlet不是filter)
```xml
  <!-- SpringMVC思想是有一个前端控制器能拦截所有请求，并智能派发;
  	这个前端控制器是一个servlet；应该在web.xml中配置这个servlet来拦截所有请求
   -->
   
   <!-- The front controller of this Spring Web application, 
   responsible for handling all application requests -->
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		
		<init-param>
			<!-- contextConfigLocation:指定SpringMVC配置文件位置 -->
            <!-- 如果不指定配置文件位置？
 *          /WEB-INF/springDispatcherServlet-servlet.xml
 *          如果不指定也会默认去找一个文件；
 *          /WEB-INF/springDispatcherServlet-servlet.xml
 *        就在web应用的/WEB-INF、下创建一个名叫:前端控制器名-servlet.xml-->
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:springmvc.xml</param-value>
		</init-param>
		<!-- servlet启动加载，servlet原本是第一次访问创建对象；
		load-on-startup:服务器启动的时候创建对象；值越小优先级越高，越先创建对象；
		 -->
		<load-on-startup>1</load-on-startup>
	</servlet>
	<!-- Map all requests to the DispatcherServlet for handling -->
	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
	<!--  
		/：拦截所有请求，不拦截jsp页面，*.jsp请求
		/*：拦截所有请求，拦截jsp页面，*.jsp请求
		
		
		处理*.jsp是tomcat做的事；所有项目的小web.xml都是继承于大web.xml
		DefaultServlet是Tomcat中处理静态资源的？
			除过jsp，和servlet外剩下的都是静态资源；
			index.html：静态资源，tomcat就会在服务器下找到这个资源并返回;
			我们前端控制器的/禁用了tomcat服务器中的DefaultServlet
			
		
		1）服务器的大web.xml中有一个DefaultServlet是url-pattern=/
		2）我们的配置中前端控制器 url-pattern=/
				静态资源会来到DispatcherServlet（前端控制器）看那个方法的RequestMapping是这个index.html
		3）为什么jsp又能访问；因为我们没有覆盖服务器中的JspServlet的配置
		4） /*  直接就是拦截所有请求；我们写/；也是为了迎合后来Rest风格的URL地址
	-->
		<url-pattern>/</url-pattern>
	</servlet-mapping>
```
    * springmvc的配置:
```xml
	<!-- 扫描所有组件 -->
	<context:component-scan base-package="com.atguigu"></context:component-scan>
	
	<!-- 配置一个视图解析器 ；能帮我们拼接页面地址-->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/pages/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
```

---


* **Spring执行流程:**

<img src="https://uploader.shimo.im/f/jwZL1f3mmCgBCBDZ.png!thumbnail" alt="图片" style="zoom:67%;" />





---


* **RequestMapping**
    * 作用:
        * DispatcherServlet截获请求后,通过控制器上的@RequestMapping提供的映射信息确定请求所对应的处理方法;
    * 属性:
```plain
	 * method：限定请求方式、
	 * 		HTTP协议中的所有请求方式：
	 * 	    【GET】, HEAD, 【POST】, PUT, PATCH, DELETE, OPTIONS, TRACE
	 * 		GET、POST
	 * 		method=RequestMethod.POST：只接受这种类型的请求，默认是什么都可以；
	 * 			不是规定的方式报错：4xx:都是客户端错误
	 * 				405 - Request method 'GET' not supported
	 * params：规定请求参数
	 * params 和 headers支持简单的表达式：
	 * 		param1: 表示请求必须包含名为 param1 的请求参数
	 * 			eg：params={"username"}:
	 * 				发送请求的时候必须带上一个名为username的参数；没带都会404
	 * 
	 * 		!param1: 表示请求不能包含名为 param1 的请求参数
	 * 			eg:params={"!username"}
	 * 				发送请求的时候必须不携带上一个名为username的参数；带了都会404
	 * 		param1 != value1: 表示请求包含名为 param1 的请求参数，但其值不能为 value1
	 * 			eg：params={"username!=123"}
	 * 				发送请求的时候;携带的username值必须不是123(不带username或者username不是123)
	 * 
	 * 		{“param1=value1”, “param2”}: 请求必须包含名为 param1 和param2 的两个请求参数，且 param1 参数的值必须为 value1
	 * 			eg:params={"username!=123","pwd","!age"}
	 * 				请求参数必须满足以上规则；
	 * 				请求的username不能是123，必须有pwd的值，不能有age
	 * headers：规定请求头；也和params一样能写简单的表达式
	 * 	
	 * 
	 * 
	 * consumes：只接受内容类型是哪种的请求，规定请求头中的Content-Type
	 * produces：告诉浏览器返回的内容类型是什么，给响应头中加上Content-Type:text/html;charset=utf-8
	 */
```
    * **支持Ant风格:**
        * ?：匹配文件名中的一个字符
        * *：匹配文件名中的任意字符
        * **：** 匹配多层路径
    * **@PathVariable注解:**
        * 映射URL绑定的占位符:
```xml
    //   /user/admin    /user/leifengyang
    // 路径上的占位符只能占一层路径
    @RequestMapping("/user/{id}")
    public String pathVariableTest(@PathVariable("id")String id){
        System.out.println("路径上的占位符的值"+id);
        return "success";
    }
```










