# Java Web基础教程

## 1. Servlet基础

### 1.1 什么是Servlet

Servlet是JavaEE中的标准组件之一，专门用于处理客户端的HTTP请求。并且它必须依赖于Servlet容器（Tomcat就是一个标准的Servlet容器）才能运行。因为Servlet实例的创建和销毁都是由容器负责管理的，我们不能自行通过new关键去创建和使用Servlet。

### 1.2 编写一个简单的Servlet

1. 在任意地方创建一个myweb文件夹，这个文件夹相当于一个web项目根目录

2. 在根目录下创建WEB-INF子目录

3. 在WEB-INF目录下创建src和classes子目录

4. 在src目录下编写一个类，继承HttpServlet这个父类

~~~java
public class HelloServlet extends HttpServlet {
}
~~~

5. 重写父类的service方法，这个就是专门处理客户端请求的方法，web容器会为这个方法传入两个参数HttpServletRequest和HttpServletResponse,并且这个方法还需要抛出ServletException和IOException给容器捕获

~~~java
public class HelloServlet extends HttpServlet {
	public void service(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException{
        //设置响应类型及编码
        response.setContentType("text/html;charset=utf-8");
    	  //获取字符输出流输出html信息
        response.getWriter().println("<h1>Hello Servlet</h1>")
	}
}    
~~~

6. 编译Servlet，需要依赖servlet-api.jar文件,将它添加到classpath中

~~~
javac -cp d:\servlet-api.jar; HelloServlet.java
~~~

7. 将编译后的HelloServlet.class文件剪切到classes目录中

8. 在WEB-INF目录下创建并编辑web.xml文件，为servlet配置请求映射

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 配置根节点 -->
<web-app>
   <!-- 配置servlet类 -->
   <servlet>
   	  <!-- 指定servlet的别名 -->
   	  <servlet-name>hello</servlet-name>
   	  <!-- 指定Servlet的完整类名-->
   	  <servlet-class>HelloServlet</servlet-class>
   </servlet>
   <!-- 配置请求映射-->
   <servlet-mapping>
   	   <!-- 这里的servlet-name和上面的servlet-name要一一对应 -->
   	  <servlet-name>hello</servlet-name>
   	  <!-- 配置请求映射的url,必须以“/”开头-->
   	  <url-pattern>/hello</url-pattern>
   </servlet-mapping>
</web-app>	
~~~

9. 将项目部署都tomcat的webapps目录中,并执行bin目录下的startup.bat启动容器

10. 打开浏览器，在地址栏输入http://localhost:8080/myweb/hello访问Servlet

### 1.3 Servlet的请求处理流程

浏览器发起http的请求，这个请求首先会被servlet容器（Tomcat）截获，然后容器会根据web.xml文件中配置servlet的<url-pattern>来找到相应的<servlet-name>这个别名，然后再根据这个别名找到具体Servlet的类，然后容器会创建这个Servlet类的实例并调用service方法来处理这个请求。通常Servlet是以单实例多线程的方式处理客户端请求。

### 1.4 Servlet的生命周期

所谓的生命周期，就是从Servlet的创建一直到它销毁的整个过程。并且它的这个生命周期都是由Servlet容器（Tomcat）负责管理和维护的。

#### 1.4.1 Servlet对象创建的过程

当第一次请求某个Servlet的时候，容器会先查找之前有没有创建过这个Servlet的实例，如果没有则创建一个并缓存起来。后续相同的请求都由这个缓存的对象来处理。（注意：这里说的是第一次请求时创建。另外一种情况则是在容器启动的时候就创建Servlet的实例，在web.xml中为Servlet指定<load-on-startup>配置，这个配置的值是一个整型，哪一个Servlet配置的数值越小，创建的优先级别越高）

#### 1.4.2 生命周期方法

| 方法名  | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| init    | 在Servlet对象创建之后立即执行的初始化方法，且只执行一次      |
| service | 核心的请求处理方法，这个方法可以执行多次                     |
| destroy | 容器准备销毁Servlet实例之前执行这个方法，也是执行一次（容器重启或关闭） |

### 1.5 HTTP报文组成

#### 1.5.1 请求报文

请求行：请求报文的第一行就是请求行。包括请求方法、请求URL地址、HTTP协议版本。

请求头：请求行之后的信息就是请求头，它是以“名称:内容”的格式体现。主要包括服务器主机地址及端口、连接状态、系统信息、编码、语言等等。

请求体：请求头结束之后会有一个空行，空行之后就是请求体的内容。通常使用POST提交的数据信息会存放在请求体当中，然后传递给服务器。

#### 1.5.2 响应报文

状态行：主要包括HTTP协议、响应状态码（例如：200表示OK，成功响应）。

响应头：主要包括服务器信息、响应的类型及编码、内容的长度、响应的时间等。

响应体：服务端可以将信息数据携带到响应体中，带回客户端。

### 1.6 HTTP请求方法

在HTTP/1.1协议中，请求方法主要包括8个，下面列举常用的请求方法进行说明。

| 方法   | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| GET    | 向服务器请求指定的资源，并返回响应主体。一般来说GET方法应该只用于数据的读取（类似于查询） |
| POST   | 向指定的服务器提交数据（例如：表单数据的提交、文件上传等）,并且提交的数据会放入请求体中（类似于新增） |
| PUT    | 向服务器提交数据，但是和POST有所区别。如果服务器不存在此资源的时候，则执行新增，如果存在则执行修改（类似于修改） |
| DELETE | 根据uri的标识删除服务器上的某个资源（类似于删除）            |
| 其他   | ...                                                          |

备注：GET与POST区别：

1. GET主要用于获取数据，POST用于提交数据。

2. GET请求所带的参数是放在请求行的url地址后面，而POST这是放在请求体中。

3. 通常浏览器会对GET请求的url长度有所限制，而POST提交的数据在请求体中，可以提交更多的内容。

4. 浏览器会对GET请求进行缓存。

### 1.7 Servlet的请求处理方法

| 方法     | 说明                   |
| -------- | ---------------------- |
| service  | 可以处理任何的请求类型 |
| doGet    | 处理对应的GET请求      |
| doPost   | 处理对应的POST请求     |
| doPut    | 处理对应的PUT请求      |
| doDelete | 处理对应的DELETE请求   |
| 其他     | ...                    |

说明：通过HttpServlet的源代码得知，默认的所有请求都会先经过service方法，然后service方法根据请求的方法类型判断来决定交给doGet或者是doPost方法来处理请求。如果子类重写了父类的service方法同时还重写了其他的doXxx的方法，那么只有service方法会处理请求，其他方法将失效。

### 1.8 Request与Response对象

当web容器调用某个Servlet的Service方法时，会创建一个HttpServletRequest和HttpServletRespinse对象作为参数传入到这个方法中，那么我们可以通过HttpServletRequest来获取相关的请求内容等，而响应客户端可以利用HttpServletResponse对象来完成。

#### 1.8.1 HttpServletRequest常用API

| 方法                            | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| getParameter(String name)       | 获取请求参数，根据请求参数的name指定                         |
| getParameterValues(Spring name) | 获取相同name的请求参数，返回的是字符串数组                   |
| getParameterNames()             | 获取所有的请求参数名称                                       |
| getParameterMap()               | 获取所有请求参数，包括参数名称和值                           |
| getMethod()                     | 获取客户端的请求方法                                         |
| getHeader(String name)          | 根据请求头的名称获取响应的信息                               |
| getRemoteAddr()                 | 获取远程客户端的IP地址                                       |
| getServletPath()                | 获取Servlet的请求地址，也就是url-pattern                     |
| getRequestURL()                 | 获取请求完整的URL地址                                        |
| getRealPath(String path) 废弃   | 获取项目的绝对路径。（这个方法在request对象中已废弃，建议通过ServletContext对象获取） |
| 其他                            | ...                                                          |

#### 1.8.2 HttpServletResponse常用API

| 方法                                 | 说明                                           |
| ------------------------------------ | ---------------------------------------------- |
| setContentType(String str)           | 设置响应内容的类型及编码                       |
| getWriter()                          | 获取响应字符输出流                             |
| getOutputStream()                    | 获取字节输出流                                 |
| setHeader(String name, String value) | 设置响应头信息，如果存在响应头信息，则执行更新 |
| addHeader(String name, String value) | 设置响应头，不管存不存在都会新加入一个         |
| setStatus(int code)                  | 设置响应状态码                                 |
| 其他                                 | ...                                            |

#### 1.8.3 常见的响应状态码

| 状态码 | 说明                 |
| ------ | -------------------- |
| 200    | 请求已成功           |
| 302    | 重定向               |
| 401    | 请求被禁止，未授权   |
| 404    | 请求的资源不存在     |
| 405    | 请求行的方法不被支持 |
| 500    | 服务器内部错误       |
| 其他   | ...                  |

### 1.9 Servlet之间的通信

#### 1.9.1 转发

所谓转发，就是在多个Servlet之间共享请求和响应对象，所有参与转发过程的Servlet都可以共享同一个请求对象的信息，在Servlet的API中，转发的操作是由HttpServletRequest对象完成的。

***转发的特点：***

1. URL地址栏不会发生改变
2. 转发的过程是在服务端自动完成

示例代码：

~~~java
public class ServletA extends HttpServlet{
	public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
		//获取页面提交的参数
		String name = request.getParameter("userName");		
		//转发由HttpServletRequest完成
		//第一步先获取一个请求转发器RequestDispatcher
		//获取请求转发器的同时要告诉转发器转发到哪里，转发给谁
		//如果要转发给ServletB，那么就是对应ServletB的url-pattern
		RequestDispatcher rd = request.getRequestDispatcher("servletB");
		//调用转发器的forward方法执行转发,同时将request和response对象一并转发ServletB
		rd.forward(request, response);
	}
}
~~~

~~~java
public class ServletB extends HttpServlet{
	public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//这里可以获得同一个请求的参数
		String name = request.getParameter("userName");
		System.out.println("ServletB获取请求参数："+name);
	}
}
~~~

***请求作用域：***

每一个请求对象都有一个独立的空间，这个空间我们称之为请求作用域（RequestScope）。可以为当前这个请求携带额外的一些数据信息，这些信息同样可以在多个Servlet之间进行共享。

示例代码：

~~~java
public class ServletA extends HttpServlet {
	public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
		//在ServletA中为request对象设置请求作用域
        request.setAttribute("age", 35);
	}
}
~~~

~~~java
public class ServletB extends HttpServlet{
	public void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
		//在ServletB中获取统一个请求对象作用域的值
        Integer age  = (Integer)request.getAttribute("age");
	}
}
~~~

#### 1.9.2 重定向

重定向的机制和转发不同，首先重定向是通过HttpServletResponse对象来完成。一次重定向的过程中会有两次请求和两次响应，服务器在接收第一次请求后会先做一次302的响应（302表示重定向状态码），告诉客户端浏览器必须发起一个新的请求地址，服务端再次接收这个请求处理，最后再次响应客户端。由于会产生不同的请求对象，因此并不同共享同一个请求中的参数。

***重定向的特点：***

1. URL地址栏会发生改变
2. 重定向的操作是在客户端浏览器完成的

示例代码：

方式一：

~~~java
public class ServletC extends HttpServlet {

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//执行重定向
		//方式一：设置302响应状态码，并在响应头中添加location属性指定重定向的地址
		response.setStatus(302);
		response.addHeader("location", "http://localhost:8080/ch06/servletD");
	}
}
~~~

方式二：

~~~java
public class ServletC extends HttpServlet {

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//执行重定向
		//方式二：使用response的sendRedirect方法
		response.sendRedirect("servletD");
	}
}
~~~

### 1.10 会话跟踪

由于HTTP协议是无状态的，服务端并不会记录每一个客户端的状态，因此，如果想要实现服务器能记录客户端的状态的话，那么就需要会话跟踪技术。

#### 1.10.1 cookie

cookie是客户端浏览器内部的一个文本文件，用于记录服务器发送过来的一些文本信息。可以在Servlet中创建Cookie对象，在响应时将cookie写回浏览器。在接下来的每次请求中，客户端会都把这个cookie信息带回给服务器，服务器就可以获取cookie的信息，达到会话跟踪的目的。由于cookie是保存在客户端浏览器，也就是会话信息将由客户端维护。

***设置cookie：***

~~~java
public class SetCookieServlet extends HttpServlet{

	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//创建一个Cookie的实例
		Cookie cookie1 = new Cookie("userId","10001");
    Cookie cookie2 = new Cookie("age","21");
		//将cookie对象设置到响应对象中
		response.addCookie(cookie1);
    response.addCookie(cookie2);
	}
}
~~~

***获取cookie：***

~~~java
public class GetCookieServlet extends HttpServlet{

	@Override
	protected void service(HttpServletRequest request, HttpServletResponse repsonse) throws ServletException, IOException {
		//cookie是通过request对象来得到的
		//从请求中可以获取多个cookie对象
		Cookie[] cookies = request.getCookies();
		for (Cookie cookie : cookies) {
			//判断cookie，只获取name为userId的cookie对象
			if("userId".equals(cookie.getName())) {
				System.out.println(cookie.getValue());
			}
		}
	}	
}
~~~

***cookie保存中文：***

在保存cookie的时候如果需要保存中文，那么中文信息需要经过编码后才可以写入cookie

示例代码：

编码使用URLEncoder

~~~java
String str = URLEncoder.encode("张三", "utf-8");
Cookie cookie = new Cookie("userName", str);
~~~

解码使用URLDecoder

~~~java
String str = URLDecoder.decode(cookie.getValue(), "utf-8");
System.out.println(str);
~~~

***cookie的生命周期：***

默认cookie只会保存在浏览器进程的内存中，并不会写入cookie文件，如果关闭了浏览器，那么浏览器的进程也就消失了，那么对应的内存就会释放空间，因此cookie也就销毁。如果想将cookie写入文件，那么就必须设置cookie的生命时长，一旦设置了生命时长，那么就表示这个cookie会在文件中保留多长的时间，到了这个时间之后，浏览器就会自动销毁这个cookie。

***设置cookie存活时间：***

~~~java
Cookie cookie = new Cookie("userId","10001");
//设置为0表示立即删除cookie
cookie.setMaxAge(0);
//设置为正数表示cookie在cookie文件的存活时间，单位：秒
cookie.setMaxAge(60*60);
//设置为-1表示cookie只保留在浏览器器的进程中，关闭浏览器之后会销毁cookie
cookie.setMaxAge(-1);
~~~

#### 1.10.2 Session

Session是基于服务端来保存用户的信息，这个是和cookie的最大区别。不同的客户端在请求服务器的时候，服务器会为每一个客户端创建一个Session对象并保存在服务器端，Session对象是每个客户端所独有的，相互之间不能访问。服务器为了区别不同的Session属于哪一个客户端，因此Session对象也有一个唯一标识，叫做SessionID。需要注意的是这个SessionID是以cookie的机制保存在客户端浏览器。每次请求的时候，浏览器都会把这个SessionID带回服务端，服务端根据这个SessionID就可以找到对应的Session对象。注意：session的创建是在第一次调用request.getSession()方法时创建的，因为这个方法会判断之前有没有创建过Session，如果有则直接使用，没有则创建一个，不会出现同一个客户端同时创建多个Session的情况。

示例代码：

~~~java
public class SessionServlet extends HttpServlet{
	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//HttpSession对象是在第一次调用request的getSession()方法时才会创建
		//注意：getSession()的方法会先判断之前是否为客户端创建了session实例，
		//如果创建了，则使用之前创建好的Session对象，没有则创建一个新的Session
		HttpSession session = request.getSession();
		//创建HttpSession的同时，会创建一个唯一的标识SessionID
		//这个sessionId会保存在浏览器的cookie中，每次请求会带回这个id找到相应的session对象
		String sessionId = session.getId();
		System.out.println(sessionId);
	}
}
~~~

***session的生命周期：***

SessionId是保存在浏览器的cookie中，但是不会写入cookie文件，这也就表示当关闭浏览器之后，SessionId就会销毁。SesisonId销毁以后，服务端的Session就没有任何作用了。但是，服务器并不会立刻销毁这个Session对象，至于什么时候销毁是由服务器自己决定的。除非我们手动调用了session.invalidate() 方法，服务器就会立即销毁这个session实例。

例如：

~~~java
HttpSession session = request.getSession();
//立即销毁session
session.invalidate();
~~~

Session默认也有存活时间，服务器在创建Session的时候为Session设置默认的存活时间为30分钟，如果在30分钟之内，客户端没有发起任何请求到服务器，那么服务器就会销毁这个Session对象。我们也可以设置Session的存活时间。可以为当前的Session设置，也可以为全局（服务器端的所有Session）的Session设置。

***设置当前Session的存活时间：***

~~~java
HttpSession session = request.getSession();
//设置当前Session的存活时间，单位：秒
session.setMaxInactiveInterval(3600);
~~~

***设置全局的Session的存活时间:***

在web.xml中进行设置

~~~xml
<!-- 设置全局Session的存活时间,单位：分钟 -->
<session-config>
  <session-timeout>60</session-timeout>
</session-config>
~~~

***Session的作用域：***

当我们需要将一些数据信息存入Session的时候，就需要操作Session作用域（SessionScope），它和请求作用域类似，也有相应的setAttribute和getAttribute方法，只不过Session作用域的范围要比请求作用域更宽。请求作用域在一次请求响应之后就会消失（因为相应之后请求对象就会销毁）。而Sesison对象只要浏览器不关闭或者Session对象未超时，那么Session对象会一直驻留在服务器端，因此不管重新请求多少次还是转发和重定向，都可以从Session中获取之前保存的数据信息。

示例代码：

~~~java
User user = new User();
user.setUid("1001");
user.setUserName("wangl");
HttpSession session = request.getSession();
//将数据保存在会话作用域中
session.setAttribute("user", user);
~~~

从会话作用域取值

~~~java
HttpSession session = request.getSession();
//取值
User user = (Object)session.getAttribute("user");
~~~

URL重写：

浏览器是可以禁用cookie的，一旦禁用了cookie，那么SessionId将无法写入cookie的缓存中，这样就导致无法实现会话跟踪了，因此解决办法就是使用URL重写。URL重写的目的就是把SessionId在放在请求URL地址的后面提交回服务器（类似GET请求后面带上参数，而这个参数就是一个SessionId），服务器会解析这个URL的地址并得到SessionId。

示例代码：

~~~java
//重写请求的URL地址，这个地址后面会自动带上SessionId
String url = response.encodeRedirectURL("/getUser");
//重定向URL
response.sendRedirect(url);
~~~

浏览器地址演示

~~~
http://localhost:8080/ch07/getUser;jsessionid=6F1BA8C92D7E5D7CC479ED8DD30D3ED0
~~~

注意：;号后面跟着就是SessionId

### 1.11 ServletContext

web容器在启动时会为每一个web应用创建唯一的上下文对象，这个对象就是ServletContext，这个上下文对象可以理解为是当前项目的一个共享内存空间，为项目中的所有Servlet提供一个共享的区域。

***常用API：***

| 方法                                    | 说明                                      |
| --------------------------------------- | ----------------------------------------- |
| getContextPath()                        | 获取项目的相对路径                        |
| getRealPath(String path)                | 获取项目的绝对路径                        |
| getInitParameter(String name)           | 获取上下文的初始化参数（web.xml中配置的） |
| setAttribute(String name, String value) | 将数据放入上下文作用域                    |
| getAttribute(String name)               | 从上下文作用域中去获取数据                |

***上下文作用域：***

上下文作用域是为当前项目所有Servlet提供的一个共享内存区域，可以将需要的数据信息保存在作用域中。这个作用域的的范围是最大的，只要容器没有停止，它就会一直存在。

***三种作用域比较：***

结合前面所学的作用域，那么一共有三个，分别是：请求作用域，会话作用域，上下文作用域。

范围从小到大来划分:

请求作用域 < 会话作用域 < 上下文作用域

### 1.12 过滤器

过滤器可以在请求到达servlet之前和servlet响应客户端之前进行拦截，相当于一个拦截器。主要用于进行一些请求和响应的预处理操作。常用的场景包括用户的认证授权、统一请求字符编码等等。

#### 1.12.1 编写过滤器

要实现一个过滤器，必须实现一个FIlter接口，只有实现了这个接口的类才称之为过滤器。

示例代码：

~~~java
public class DemoFilter implements Filter {
    ...
}
~~~

web.xml配置过滤器:

~~~xml
<filter>
  	<filter-name>demoFilter</filter-name>
  	<filter-class>edu.nf.ch09.filter.DemoFilter</filter-class>
  	<!-- 初始化参数 -->
  	<init-param>
  		<param-name>param</param-name>
  		<param-value>hello</param-value>
  	</init-param>
  </filter>
  <filter-mapping>
  	<filter-name>demoFilter</filter-name>
  	<!-- 什么请求可以经过此过滤器，/*表示所有请求 -->
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
~~~

#### 1.12.2 过滤器的生命周期

与Servlet类似，Filter同样也是容器负责创建和销毁，与Servlet的区别在于，容器会在启动的时候最先创建所有的过滤器，并执行init方法进行初始化。

生命周期方法：

| 方法     | 说明                           |
| -------- | ------------------------------ |
| init     | 初始化方法，容器启动时执行一次 |
| doFilter | 请求过滤方法，决定请求是否放行 |
| destroy  | 容器销毁过滤器之前执行的方法   |

代码实例：

~~~java
public class DemoFilter implements Filter{

	@Override
	public void destroy() {
		
		System.out.println("准备销毁DemoFilter");
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		//FilterChain表示一个过滤链对象，因为过滤器可能会存在多个
		//同时这个对象将决定是否放行当前请求，
		//放行的话则请求会继续到达下一个过滤器或者servlet中
		System.out.println("请求经过DemoFileer..放行");
		chain.doFilter(request, response);
		System.out.println("响应前经过DemoFilter...");
	}

	@Override
	public void init(FilterConfig config) throws ServletException {
		String name = config.getInitParameter("param");
		System.out.println("初始化DemoFilter，获取初始化参数："+name);
	}
}
~~~

#### 1.12.3 过滤链

在一个web项目中可能存在多个过滤器，当有多个过滤器存在的时候就会形成一个过滤链。请求会按照过滤器链的顺序一直传递下去，最终到达某个Servlet来处理请求。（注意：过滤链的顺序是按照web.xml中的先后配置顺序决定的）

配置示例：

~~~xml
<!-- 按先后顺序配置 -->
<!-- 配置第一个过滤器 -->
<filter>
	<filter-name>firstFilter</filter-name>
    <filter-class>edu.nf.ch09.filter.FirstFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>firstFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
<!-- 配置第二个过滤器-->
<filter>
	<filter-name>secondFilter</filter-name>
    <filter-class>edu.nf.ch09.filter.SecondFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>secondFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
~~~

### 1.13 监听器

监听器用于监听对象的上的事件发生，在Servlet中监听器主要监听请求对象、会话对象、上下文对象以及监听这些对象的作用域操作。JavaEE为我们提供了一系列的监听器接口，开发时按需实现相应的接口即可。

#### 1.13.1 监听作用域对象的创建与销毁

| 监听器                 | 说明                              |
| ---------------------- | --------------------------------- |
| ServletRequestListener | 监听请求对象的创建和销毁          |
| HttpSesisonListener    | 监听会话对象的创建和销毁          |
| ServletContextListener | 监听Servlet上下文对象的创建和销毁 |

代码示例：

1.请求对象监听器

~~~java
public class DemoRequestListener implements ServletRequestListener{

	/**
	 * 当请求对象销毁后容器执行此方法
	 * 销毁方法中同样也有一个ServletRequestEvent事件对象
	 */
	@Override
	public void requestDestroyed(ServletRequestEvent event) {
		//通过这个事件对象就可以获取当前的请求对象
		HttpServletRequest request = (HttpServletRequest)event.getServletRequest();
		System.out.println("销毁请求对象..."+request);
	}

	/**
	 * 当请求对象创建之后容器调用此方法
	 * ServletRequestEvent这个参数就是一个事件对象
	 */
	@Override
	public void requestInitialized(ServletRequestEvent event) {
		//通过这个事件对象就可以获取当前的请求对象
		HttpServletRequest request = (HttpServletRequest)event.getServletRequest();
		System.out.println("初始化了请求对象..."+request);
	}

}
~~~

web.xml配置

~~~xml
<!-- 配置监听器 -->
  <listener>
    <!-- 指定监听器的完整类名 -->
  <listener-class>edu.nf.ch10.listener.DemoRequestListener</listener-class>
  </listener>
~~~

2.会话对象监听器

~~~java
public class DemoSessionListener implements HttpSessionListener{

	/**
	 * 监听HttpSession对象的创建
	 * HttpSessionEvent参数是一个事件对象
	 * 通过它可以获得当前的HttpSession
	 */
	@Override
	public void sessionCreated(HttpSessionEvent event) {
		HttpSession session = event.getSession();
		System.out.println("创建了Session对象"+session);
	}

	/**
	 * 监听HttpSession对象的销毁
	 */
	@Override
	public void sessionDestroyed(HttpSessionEvent event) {
		HttpSession session = event.getSession();
		System.out.println("销毁了Session对象"+session);
	}
}
~~~

注意：当第一次调用了request.getSesison()方法创建Session时，监听器才会起作用。

web.xml配置

~~~xml
<listener>
    <!-- 指定监听器的完整类名 -->
  	<listener-class>edu.nf.ch10.listener.DemoSessionListener</listener-class>
  </listener>
~~~

3.Servlet上下文监听器

~~~java
public class DemoContextListener implements ServletContextListener{

	/**
	 * 监听ServletContext的销毁
	 */
	@Override
	public void contextDestroyed(ServletContextEvent event) {
		//通过事件对象获取ServletContext
		ServletContext sc = event.getServletContext();
		System.out.println("销毁了ServletContext对象..."+sc);
	}

	/**
	 * 监听SerlvetContext的创建
	 */
	@Override
	public void contextInitialized(ServletContextEvent event) {
		//通过事件对象获取ServletContext
		ServletContext sc = event.getServletContext();
		System.out.println("创建了ServletContext对象..."+sc);
	}
}
~~~

web.xml

~~~xml
<listener>
    <!-- 指定监听器的完整类名 -->
  	<listener-class>edu.nf.ch10.listener.DemoContextListener</listener-class>
  </listener>
~~~

#### 1.13.2 监听作用域的操作

| 监听器                          | 说明                          |
| ------------------------------- | ----------------------------- |
| ServletRequestAttributeListener | 监听请求作用域的操作          |
| HttpSessionAttributeListener    | 监听会话作用域的操作          |
| ServletContextAttributeListener | 监听Servlet上下文作用域的操作 |

示例代码：

这里以HttpSessionAttributeListener说明，其他作用于监听器用法相似。

~~~java
public class DemoSessionAttributeListener implements HttpSessionAttributeListener{

	/**
	 * 当有数据添加到会话作用域时，执行此方法
	 */
	@Override
	public void attributeAdded(HttpSessionBindingEvent event) {
		//获取存入作用域的键和值
		System.out.println("存入会话作用域..."+event.getName() + " : " + event.getValue());
		
	}

	/**
	 * 当从会话作用域移除数据时，执行此方法
	 */
	@Override
	public void attributeRemoved(HttpSessionBindingEvent event) {
		System.out.println("移除会话作用域..."+event.getName() + " : " + event.getValue());
		
	}

	/**
	 * 当替换了会话作用域中的某个数据时，执行此方法
	 */
	@Override
	public void attributeReplaced(HttpSessionBindingEvent event) {
		HttpSession session = event.getSession();
        //从session中获取的是替换之后的值
		System.out.println("替换的值： "+session.getAttribute("userName").toString());
		//注意：这里event.getValue()获取到的是替换之前的值
		System.out.println("替换会话作用域..."+event.getName() + " : " + event.getValue());
	}
}
~~~

web.xml

~~~xml
<listener>
    <!-- 指定监听器的完整类名 -->
  	<listener-class>edu.nf.ch10.listener.DemoSessionAttributeListener</listener-class>
  </listener>
~~~

### 1.14 注解配置

Servlet3.0开始提供了一系列的注解来配置Servlet、Fiilter、Listener等等。这种方式可以极大的简化在开发中大量的xml的配置。从这个版本开始，web.xml可以不再需要，使用相关的注解同样可以完成相应的配置。

| 注解             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| @WebServlet      | 这个注解标识在类上，用于配置Servlet。例如：@WebServlet(name="hello", urlPatterns="/hello")  也可简化配置@WebServlet("/hello")。initParams属性设置Servlet的初始化参数。loadOnStartup设置容器启动时初始化Servlet。 |
| @MultipartConfig | 这个注解标识在类上，标识启用当前Servlet的文件上传功能。location属性设置文件上传的路径。maxFileSize属性限制单个文件上传的大小，默认为-1，表示没有限制。maxRequestSize属性限制一次请求上传总文件的大小，默认为-1，表示没有限制，fileSizeThreshold属性表示当前数据量大于该值时，内容将被写入文件。 |
| @WebFilter       | 这个注解标识在类上，用于配置Filter。例如：@WebFilter(filterName="encode",urlPatterns="/\*") 也可简化配置@WebFilter("/*")，initParams属性设置Servlet的初始化参数。 |
| @WebListener     | 这个注解标识在类上，用于配置各种监听器                       |

示例：

Servlet配置示例：

~~~java
@WebServlet(urlPatterns = "/hello",
        initParams = {@WebInitParam(name="name", value="aaa")},
        loadOnStartup = 0)
@MultipartConfig(maxFileSize = 5242880, maxRequestSize = 104857600)
public class TestServlet extends HttpServlet {
	...
}
~~~

文件上传配置示例：

~~~java
@WebServlet(urlPatterns = "/hello")
@MultipartConfig(maxFileSize = 5242880, maxRequestSize = 104857600)
public class TestServlet extends HttpServlet {
	...
}
~~~

Filter配置示例：

~~~java
@WebFilter(urlPatterns = "/*", 
        initParams = {@WebInitParam(name="name", value="aaa")})
public class TestFilter implements Filter {
	...
}
~~~

Listener配置示例：

~~~java
@WebListener
public class ApplicationListener implements ServletContextListener {
  ... 
}
~~~

### 1.15 动态注册

从Servlet3.0开始，支持ServletContext来动态注册相关的Servlet组件。例如可以在自定义的ServletContextListener中，通过在contextInitialized方法中获取ServletContext对象来注册Servlet、Filter、Listener。

动态注册Servlet：

~~~java
public class TestServlet extends HttpServlet { 
  ...
}
~~~

~~~java
@WebListener
public class ApplicationListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        //获取ServletContext
        ServletContext sc = sce.getServletContext();
        //动态注册Servlet，返回动态注册器实例
        ServletRegistration.Dynamic registration = sc.addServlet("testServlet", TestServlet.class);
        //映射请求url
        registration.addMapping(new String[]{"/test"});
        //初始化参数
        registration.setInitParameter("name", "aaa");
        //启用文件上传
        registration.setMultipartConfig(
                new MultipartConfigElement(sc.getRealPath("/uplaod"), 
                        5242880, 
                        104857600, 
                        104857600)
        );
    }

}
~~~

动态注册Filter：

~~~java
public class TestFilter implements Filter {
   ...
}
~~~

~~~java
@WebListener
public class ApplicationListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext sc = sce.getServletContext();
        //动态注册Filter
        FilterRegistration.Dynamic registration = sc.addFilter("testFilter", TestFilter.class);
        //设置初始化参数
        registration.setInitParameter("name", "aaa");
        //请求映射配置
        //参数一：设置过滤器拦截的类型FORWARD，INCLUDE，REQUEST，ERROR，ASYNC
        //EnumSet是Set接口的一个特别实现，可用于对枚举类型进行特定的分组
        //参数二：设置该过滤器是否放在当前web应用中已经存在的过滤器之后，true表示之后，false表示之前
        //参数三：设置请求拦截的url
        registration.addMappingForUrlPatterns(
                EnumSet.of(DispatcherType.REQUEST), true, new String[]{"/*"});
    }
}
~~~

动态注册Listener：

~~~java
public class UserSessionListener implements HttpSessionListener {
    ...
}
~~~

~~~java
@WebListener
public class ApplicationListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext sc = sce.getServletContext();
        //动态注册Listener
        sc.addListener(UserSessionListener.class);
    }
}
~~~

### 1.16 文件上传

从Servlet3.0开始提供了文件上传的功能，操作起来更加的简单和方便。要想使用这个功能，首先必须在web.xml或者使用注解开启Servlet的上传功能，否则无效。

xml配置:

~~~xml
<servlet>
      <servlet-name>upload</servlet-name>
      <servlet-class>edu.nf.ch04.servlet.UploadServlet</servlet-class>
      <!-- 开启上传功能 -->
      <multipart-config/>
</servlet>
<servlet-mapping>
      <servlet-name>upload</servlet-name>
      <url-pattern>/upload</url-pattern>
</servlet-mapping>
~~~

注解配置：

~~~java
@WebServlet("/login")
@MultipartConfig //开启上传功能
public class LoginServlet extends HttpServlet {
    ...
}
~~~

参数说明：

| 参数              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| location          | 指定文件上传的目录                                           |
| maxFileSize       | 限制单个文件上传的大小                                       |
| maxRequestSize    | 限制一次请求上传总文件的大小                                 |
| fileSizeThreshold | 设置缓存的大小，当达到缓存大小的时候，会将内存的数据写入location指定的目录中（也就是写入磁盘） |

文件上传的核心接口是Part，通过HttpServletRequest对象可获得该接口的实现类对象。

~~~java
//获取单个上传文件的Part
Part part = request.getPart("name");
//获取多个上传文件的Part
Collection<Part> cool = request.getParts();
~~~

常用API：

| 方法                   | 说明                         |
| ---------------------- | ---------------------------- |
| getContentType()       | 获取上传的文件类型           |
| getSize()              | 获取上传文件的大小           |
| getSubmittedFileName() | 获取上文件的文件名           |
| write()                | 将文件上传（写入）到指定位置 |

当客户端使用form表单来上传文件时，必须将表单的enctype属性设置为"multipart/form-data"。

~~~html
<form method="post" action="upload" enctype="multipart/form-data">
     <!-- input使用file类型 -->
	<input type="file" name="file"/>
</form>    
~~~

案例：

~~~java
@WebServlet("/upload")
@MultipartConfig
public class UploadServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //指定上传的路径
        String uploadPath = "/Users/wangl/uploads";
        //创建文件夹，如果不存在的情况下
        File dir = new File(uploadPath);
        if(!dir.exists()){
            dir.mkdir();
        }

        //上传单个文件，参数对用input的name属性的值
        //Part part = req.getPart("file");
        //uploadPath = uploadPath + "/" + part.getSubmittedFileName();
        //执行上传
        //part.write(uploadPath);
        //获取文件的类型
        //System.out.println(part.getContentType());
        //获取文件的大小
        //System.out.println(part.getSize());
        //获取上传的文件名
        //System.out.println(part.getSubmittedFileName());

        //上传多个文件
        Collection<Part> parts = req.getParts();
        for (Part p : parts) {
            p.write(uploadPath + "/" + p.getSubmittedFileName());
        }
    }
}
~~~

upload.html

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h2>文件上传</h2>
 <form method="post" action="upload" enctype="multipart/form-data">
     Username:<input type="text" name="userName"/><br/>
     File1:<input type="file" name="file"/><br/>
     File2:<input type="file" name="file"/><br/>
     <input type="submit" value="submit"/>
 </form>
</body>
</html>
~~~

## 2. JSP基础

### 2.1 简介

JSP全名为Java Server Pages，中文名叫java服务器页面，是一种动态页面技术，而HTML是属于静态页面。JSP可以在HTML中嵌入Java脚本代码，因为JSP本质上还是一个Servlet，因此JSP也必须依赖于web容器才能运行。JSP的出现并不是为了取代Servlet，而是简化了Servlet的工作，将Servlet中繁琐的视图呈现代码脱离出来，交给JSP来完成，让Servlet专注于请求的处理，所以在开发中通常将JSP和Servlet结合一起使用。

### 2.2 JSP引擎

JSP本质上还是一个Servlet，那么JSP引擎主要负责将JSP文件转义成一个Servlet的java源文件，然后再通过javac将这个源文件编译成class字节码文件并装载到JVM中执行。JSP引擎的核心类是JspServlet，位于Jasper.jar文件中，并且在Tomcat的web.xml中也默认就配置好了这个类（JspServlet就是一个Servlet）。因此，凡是以“.jsp”结尾的请求都会先经过JspServlet，那么这个引擎就可以开始工作。通常引擎转义和编译后的文件放在容器的work工作目录中。

注意：如果第一次访问JSP文件的时候，由于work目录中并不存在源文件和字节码文件，JSP引擎就必须完成这两个工作，因此，有可能在第一次访问JSP时会比较缓慢。当字节码编译出来加载后，第二次访问时速度就会很快了。

### 2.3 JSP三大元素

#### 2.3.1 指令元素

语法：<%@   %>

| 指令        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| page指令    | 用于设置JSP页面的相关信息以及编码                            |
| include指令 | 用于静态包含其他的JSP页面代码,所谓静态包含，就是在编译期，将另外的JSP页面的代码合并到当前的JSP中，最终只会产生一个Java源文件 |
| taglib指令  | 这个指令用于引入标签库                                       |

#### 2.3.2 动作元素

语法：<jsp:xxx>

| 动作    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| include | 动态包含其他JSP页面的内容，所谓的动态包含是指在编译期将不同的JSP文件转义成不同的Java源文件，然后在运行时才将目标内容包含到当前的JSP页面中 |
| forward | 相当于Servlet中的转发，转发到其他的JSP页面或者Servlet        |
| param   | 用于传递参数，通常结合其他的动作一起使用，例如转发时需要提交一些而外的参数 |
| useBean | 用于在JSP页面中使用JavaBean对象,通常结合setProperty和getProperty来使用，完成bean对象的赋值和取值操作 |

#### 2.3.3 脚本元素

脚本元素主要就是在JSP中嵌入Java脚本代码，包括声明、表达式、Java脚本

声明语法：<%!  %>

表达式:：<%= %>

Java脚本： <% %>

示例：

~~~JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
   <%-- 声明变量和方法，这里声明的变量a是实例变量 --%>
   <%! 
   		int a = 10;
   		public void say(){
   			System.out.println("hello");
		}
   	%>
   	
   	<%-- 表达式,注意：表达式后面是不允许有;号结束的 --%>
   	3 + 1 = <%=3+1%><br/>
   	
   	<%-- Java脚本,脚本代码最终会生成在servlet中的service方法中作为代码片段 --%>
   	<table border="1">
   		<tr>
   		   <th>Name</th>
   		   <th>Age</th>
   		</tr>
   		<% for(int i=0;i<5;i++){%>
   		  <tr>
   		  	<td>user<%=i%></td>
   		  	<td><%=10+i%></td>
   		  </tr>
   		<%}%>
   	</table>
</body>
</html>
~~~

### 2.4 JSP内置对象

内置对象，是在容器运行时将创建好的9个对象内嵌在JSP中，在JSP里可以直接拿来使用的对象。

| 对象        | 说明                                     |
| ----------- | ---------------------------------------- |
| out         | 字符流输出对象                           |
| config      | 等同于Servlet中的ServletConfig           |
| page        | 表示当前JSP页面（等同于Servlet中的this） |
| request     | 等同于Servlet中的HttpServletRequest      |
| response    | 等同于Servlet中的HttpServletResponse     |
| session     | 等同于Servlet中的HttpSession             |
| application | 等同于Servlet中的ServletContext          |
| pageContext | 表示当前JSP页面的上下文对象              |
| exception   | 表示当前JSP中的异常对象                  |

示例代码：

~~~JSP
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
  <%-- 常用内置对象 --%>
  <%
  	 //使用request,API使用同HttpServletRequest一样
     request.getParameter("userName");
     request.setAttribute("user", "user1");
     request.getAttribute("user");
     
     //使用session,API使用等同于HttpSession
     session.setAttribute("user", "user2");
     session.getAttribute("user");
     session.getId();
     
     //使用response
     //response.sendRedirect("demo.jsp");
     
     //out对象，等同于字符输出流对象，在JSP页面输出相关内容
     out.println("hello world");
     //这个是输出在控制中
     System.out.println("hello");
     
     //使用application,等同于ServletContext
     application.setAttribute("userName", "user3");
     
     //pageContext使用
     //从pageContext中获取相关的其他对象
     HttpServletRequest req = (HttpServletRequest)pageContext.getRequest();
     HttpServletResponse res = (HttpServletResponse)pageContext.getResponse();
     HttpSession ses = pageContext.getSession();
     ServletConfig conf = pageContext.getServletConfig();
     ServletContext sc = pageContext.getServletContext();
     //也可以通过pageContext来统一设置不同的作用域
     //第三个参数表示要放入到哪个作用域，是一个int类型的参数
     //1代表page作用域（当前页面有效）
     //2代表请求作用域
     //3代表会话作用域
     //4代表上下文作用域
     pageContext.setAttribute("userName", "zhangsan", 2);
     //也可以指定中哪个作用域取出相应的值
     String name = (String)pageContext.getAttribute("userName", 2);
     out.println(name);
  %>
</body>
</html>
~~~

### 2.5 EL表达式

EL（Expression Language），全称叫做表达式语言，是JSP2.0推出的一种技术。主要简化了在JSP中使用Java脚本表达式。EL表达式的特点在于使用简单，支持四则运算、逻辑运算等，并且还可以对Servlet API中的对象进行数据访问。EL的语法: ${expression}

算数与逻辑运算：

| 示例    | 结果  |
| ------- | ----- |
| ${1+1}  | 2     |
| ${2*2}  | 4     |
| ${1==1} | true  |
| ${2>5}  | false |
| ${3>=2} | true  |
| ...     | ...   |

数据访问：

1.使用“.”来访问

| 示例                    | 说明                                                        |
| ----------------------- | ----------------------------------------------------------- |
| ${param.参数名}         | 获取请求参数的值,相当于使用request.getParameter()方法       |
| ${requestScope.xxx}     | 从请求作用域中访问数据                                      |
| ${sessionScope.xxx}     | 从会话作用域中访问数据                                      |
| ${applicationScope.xxx} | 从上下文作用域中访问数据                                    |
| ${xxx}                  | 不指定作用域范围时,默认按照作用域范围从小到大的顺序自动查找 |
| 其他                    | ...                                                         |

2.使用"[]"来访问

| 示例                        | 说明               |
| --------------------------- | ------------------ |
| ${requestScope[“userName”]} | 从请求作用域中取值 |
| ${sessionScope[“userName”]} | 从会话作用域取值   |
| 其他...                     | 同上               |

注意：通常使用"[]"来访问数据的时候，主要是访问一些特殊的名称，例如：request.setAttribute("user.userName")

这种方式如果使用`${requestScope.user.userName}`是无效的，应改为`${requestScope["user.userName"]}`

来访问。

### 2.6 JSTL

JSTL是JSP中的标准标签库，主要用于取代JSP中大量的Java脚本代码，让页面看起来更趋向于HTML。使用也很简单，通常结合EL表达式一起使用。

| 核心标签库（core） | 说明                           |
| ------------------ | ------------------------------ |
| <c:out>            | 输出标签                       |
| <c:set>            | 声明某个变量并存入指定的作用域 |
| <c:redirect>       | 重定向标签                     |
| <c:if>             | 条件判断                       |
| <c:forEach>        | 循环标签                       |
| ...                | ...                            |

备注：其他标签库请参阅相关官方文档

