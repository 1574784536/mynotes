# Spring Boot异常处理机制

## 1. 默认的异常处理机制

Spring Boot为两种情况提供了不同的响应方式。一种是浏览器客户端请求一个不存在的页面或服务端处理发生异常时，Spring Boot默认会响应一个html文档内容，称作“Whitelabel Error Page”。另一种是使用Postman等调试工具发送请求一个不存在的url或服务端处理发生异常时，Spring Boot会返回类似如下的Json格式字符串信息。

text/html:

![image-20200217171904623](https://tva1.sinaimg.cn/large/0082zybpgy1gbziqa8f5fj31cm0f8jtp.jpg)

json:

![image-20200217172013657](https://tva1.sinaimg.cn/large/0082zybpgy1gbzirh5y9wj312x03njrl.jpg)

## 2. 自定义错误页面

默认情况下任何错误Spring Boot返回的都是一个Whitelabel Error Page的错误页面，如果需要禁用默认的错误页面，可以在yml或者properties文件中设置server.error.whilelabel.enable为false。我们也可以自定义4xx.html或者5xx等不同的响应状态码的错误页面，这些页面统一放在error目录下。如果是静态错误页面，可放到static、public等目录的error目录下，如果是模板错误页面，则放到templates的error目录下。

![image-20200217131437504](https://tva1.sinaimg.cn/large/0082zybpgy1gbzbnxjlbij307n055weg.jpg)

## 3. 自定义异常控制器

还可以通过实现ErrorController接口来自定义异常控制器来返回自定义的异常信息，并在yml的server.error.path指定异常处理的url，如果不指定，path默认值为/error。也可以在控制器中注入ErrorAttributes来获取相关异常信息。

~~~java
@RestController
public class MyErrorController implements ErrorController {

    @Autowired
    private ErrorAttributes errorAttributes;

    @Override
    public String getErrorPath() {
        return "";
    }

    @RequestMapping("/aaa")
    public String handleError(HttpServletRequest request) {
        String errorMessage = errorAttributes.getError(new ServletWebRequest(request)).getMessage();
        return "异常信息 : " + errorMessage;
    }
}
~~~

yml配置:

~~~yml
error:
    whitelabel:
      enabled: false
      path: /aaa
~~~

## 4. 原理分析

1.ErrorMvcAutoConfiguration自动配置类默会认注册BasicErrorController（在没有自定义一个ErrorController时），映射地址为`@RequestMapping("${server.error.path:${error.path:/error}}")`，这是一个表达式变量，当yml中没有配置server.errpr.path这个属性时，默认映射地址就是/error，如果指定了path。由于表示式为一个变量，对于BasicErrorController来说不管设置path为什么值，都可以映射匹配，并没有任何影响。影响的是在于自定义控制器。

![image-20200217165458241](https://tva1.sinaimg.cn/large/0082zybpgy1gbzi170jndj30t403j0t7.jpg)

2.内部是通过判断请求头中的Accept的内容是否为text/html来区分请求是来自客户端浏览器还是客户端接口的调用，以此来决定返回页面视图还是 JSON 消息内容。

![image-20200217125932172](https://tva1.sinaimg.cn/large/0082zybpgy1gbzb89owwxj31ad0ju43c.jpg)

3.当要跳转到错误页面时，是交给一个DefaultErrorViewResolver视图解析器来完成，并在相应的error目录下查找4xx.html或者5xx.html页面。如果找不到则创建一个视图名为error的ModelAndView返回给DispatcherServlet，DispatcherServlet将ModelAndView交给BeanNameViewResolver视图解析器找到name为error的视图对象（也就是默认注册的异常视图对象）来呈现视图结果。

![image-20200217170404151](https://tva1.sinaimg.cn/large/0082zybpgy1gbzianyqavj312l02kdg9.jpg)

## 5. 使用@ControllerAdvice + @ExceptionHandler

