# REST教程

越来越多的人开始意识到，网站即软件，而且是一种新型的软件。这种"互联网软件"采用客户端/服务器模式，建立在分布式体系上，通过互联网通信，具有高延时（high latency）、高并发等特点。

网站开发，完全可以采用软件开发的模式。但是传统上，软件和网络是两个不同的领域，很少有交集；软件开发主要针对单机环境，网络则主要研究系统之间的通信。互联网的兴起，使得这两个领域开始融合，现在我们必须考虑，如何开发在互联网环境中使用的软件。而RESTful架构，就是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，所以正得到越来越多网站的采用。

## 1. REST风格与RESTful架构

REST这个词，是[Roy Thomas Fielding](http://en.wikipedia.org/wiki/Roy_Fielding)在他2000年的[博士论文](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)中提出的。他是HTTP协议（1.0版和1.1版）的主要设计者、Apache服务器软件的作者之一、Apache基金会的第一任主席。

![image-20181118160829503](https://ww1.sinaimg.cn/large/006tNbRwgy1fxca4hp4kbj30oy0xatce.jpg)

他的这篇论文一经发表，就引起了关注，并且立即对互联网开发产生了深远的影响。他对互联网软件的架构原则，定名为REST，即Representational State Transfer的缩写。这个词组的翻译是"表现层状态转化"。如果一个架构符合REST原则，就称它RESTful架构。要理解RESTful架构，最好的方法就是去理解Representational State Transfer这个词组到底是什么意思，它的每一个词代表了什么涵义。

### 1.1 资源（Resources）

所谓"资源"，就是网络上的一个实体，或者说是网络上的一个具体信息。它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。你可以用一个URI（统一资源定位符）指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。

### 1.2 表现层（Representation）

"资源"是一种信息实体，它可以有多种外在表现形式。我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。

### 1.3 状态转化（State Transfer）

访问某一个网站，就代表了客户端和服务器的一个交互过程。在这个过程中，势必涉及到数据和状态的变化。互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。而客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，常见的四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。

综合上面的描述，我们总结一下什么是RESTful架构：

1. 每一个URI描述一种资源；

2. 客户端和服务器之间，传递这种资源的某种表现层；

3. 客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。

## 2. RESTful API设计规范

网络应用程序，分为前端和后端两个部分。当前的发展趋势，前端设备种类繁多（手机、平板、桌面电脑、其他专用设备等等），并且很多时候前端和后端是分离开来开发和部署。因此，必须有一种统一的机制，方便不同的前端设备与后端进行通信。这导致API构架的流行，[RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer)是目前比较成熟的一套互联网应用程序的API设计理论。

### 2.1 域名

RESTful API通常使用https协议并提供相应的域名给客户端进行访问。并且尽量将API部署在专用的二级域名下，例如：

~~~
https://api.example.com
~~~

如果API相对简单，可以直接部署在一级域名下，例如：

~~~
https://example.com/api/
~~~

### 2.2 版本

版本号是为了保证API 的稳定性，并且向下兼容。在 API 没有变化的时候，API 实现的更新和升级，都应该确保原有客户端请求不出现问题。这种方式通常在 URI 中增加一段用于标识版本，例如`/v1`、`/v2`等。例如：

~~~
https://api.example.com/v1/
~~~

或者

~~~
https://example.com/api/v1/
~~~

### 2.3 路径

路径又称"终点"（endpoint），表示API的具体网址。在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。

~~~
https://api.example.com/v1/zoos
https://api.example.com/v1/animals
https://api.example.com/v1/employees
~~~

或者

~~~
https://example.com/api/v1/zoos
https://example.com/api/v1/animals
https://example.com/api/v1/employees
~~~

### 2.4 HTTP动词

对于资源的具体操作类型，由HTTP动词表示。常用的HTTP动词有下面七个（括号里是对应的相关操作）。

- GET（SELECT）：从服务器取出资源（一项或多项）。
- POST（CREATE）：在服务器新建一个资源。
- PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
- DELETE（DELETE）：从服务器删除资源。

- HEAD：获取资源的元数据。（不常用）
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。（不常用）

例如：

~~~
GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/id：获取某个指定动物园的信息
PUT /zoos/id：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/id：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/id：删除某个动物园信息
GET /zoos/id/animals：列出某个指定动物园的所有动物
DELETE /zoos/id/animals/id：删除某个指定动物园的某个动物信息
~~~

### 2.5 过滤信息

如果检索的记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。例如下面是一些常见的请求过滤参数。

~~~
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page_num=1&page_size=20：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
~~~

### 2.6 状态码

服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

- 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
- 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
- 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
- 204 NO CONTENT - [DELETE]：用户删除数据成功。
- 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
- 401 Unauthorized - [*]：表示用户没有认证（令牌、用户名、密码错误）。
- 403 Forbidden - [*] 表示用户没有访问权限，被禁止访问。
- 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
- 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
- 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
- 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
- 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。

### 2.7 错误处理

如果状态码是4xx或者5xxx，就应该向用户返回出错信息。一般来说，返回的信息中将error作为键名，出错信息作为键值即可。

~~~json
{
    error: "Invalid API key"
}
~~~

### 2.8 HATEOAS 

HATEOAS（Hypermedia as the engine of application state）是 REST 架构风格中最复杂的约束，也是构建成熟 REST 服务的核心。它的重要性在于打破了客户端和服务器之间严格的契约，使得客户端可以更加智能和自适应，而 REST 服务本身的演化和更新也变得更加容易。在了解HATEOAS之前先来看看 REST 服务按照成熟度划分成 4 个层次：

- 第一个层次（Level 0）的 Web 服务只是使用 HTTP 作为传输方式，实际上只是远程方法调用（RPC）的一种具体形式。SOAP 和 XML-RPC 都属于此类。

- 第二个层次（Level 1）的 Web 服务引入了资源的概念。每个资源有对应的标识符和表达。

- 第三个层次（Level 2）的 Web 服务使用不同的 HTTP 方法来进行不同的操作，并且使用 HTTP 状态码来表示不同的结果。如 HTTP GET 方法来获取资源，HTTP DELETE 方法来删除资源。

- 第四个层次（Level 3）的 Web 服务使用 HATEOAS。在资源的表达中包含了链接信息。客户端可以根据链接来发现可以执行的动作。

从上述 REST 成熟度模型中可以看到，使用 HATEOAS 的 REST 服务是成熟度最高的，也是推荐的做法。对于不使用 HATEOAS 的 REST 服务，客户端和服务器的实现之间是紧密耦合的。客户端需要根据服务器提供的相关文档来了解所暴露的资源和对应的操作。当服务器发生了变化时，如修改了资源的 URI，客户端也需要进行相应的修改。而使用 HATEOAS 的 REST 服务中，客户端可以通过服务器提供的资源的表达来智能地发现可以执行的操作。当服务器发生了变化时，客户端并不需要做出修改，因为资源的 URI 和其他信息都是动态发现的。

#### 2.8.1 链接

HATEOAS 的核心是链接，由 rel 和 href 两个属性组成。链接的存在使得客户端可以动态发现其所能执行的动作。其中属性 rel 表明了该链接所代表的关系含义。应用可以根据需要为链接选择最适合的 rel 属性值。例如：

~~~json
{
    "code": 200,
    "message": null,
    "data": [
        {
            "uid": 1001,
            "userName": "user1"
        },
        {
            "uid": 1002,
            "userName": "user2"
        }
    ],
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:8080/api/v1/users/list"
        },
        {
            "rel": "item",
            "href":"http://localhost:8080/api/v1/users/id/1001"
        }
    ]
}
~~~

对于很多常见的链接关系，IANA（互联网数字分配机构） 定义了规范的 rel 属性值。在开发中可能使用的常见 rel 属性值如下:

| rel 属性值                  | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| self                        | 指向当前资源本身的链接的 rel 属性。每个资源的表达中都应该包含此关系的链接。 |
| edit                        | 指向一个可以编辑当前资源的链接。                             |
| item                        | 如果当前资源表示的是一个集合，则用来指向该集合中的单个资源。 |
| collection                  | 如果当前资源包含在某个集合中，则用来指向包含该资源的集合。   |
| related                     | 指向一个与当前资源相关的资源。                               |
| search                      | 指向一个可以搜索当前资源及其相关资源的链接。                 |
| first、last、previous、next | 这几个 rel 属性值都有集合中的遍历相关，分别用来指向集合中的第一个、最后一个、上一个和下一个资源。 |

#### 2.8.2 Spring HATEOAS

如果 Web 应用基于 Spring 框架开发，那么可以直接使用 Spring 框架的子项目 HATEOAS 来开发满足 HATEOAS 约束的 Web 服务。满足 HATEOAS 约束的 REST 服务最大的特点在于服务器提供给客户端的表达中包含了动态的链接信息，客户端通过这些链接来发现可以触发状态转换的动作。Spring HATEOAS 的主要功能在于提供了简单的机制来创建这些链接，并与 Spring MVC 框架有很好的集成。对于已有的 Spring MVC 应用，只需要一些简单的改动就可以满足 HATEOAS 约束。

例子：

添加Maven依赖：

~~~xml
<dependency>
     <groupId>javax.servlet</groupId>
     <artifactId>javax.servlet-api</artifactId>
     <version>4.0.1</version>
     <scope>provided</scope>
</dependency>
     <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-webmvc</artifactId>
     <version>5.1.1.RELEASE</version>
</dependency>
<dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
     <version>2.9.6</version>
</dependency>
<dependency>
     <groupId>org.springframework.hateoas</groupId>
     <artifactId>spring-hateoas</artifactId>
     <version>0.16.0.RELEASE</version>
</dependency>
~~~

Users（实体）：

~~~java
public class Users {

    private Integer uid;
    private String userName;

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    @Override
    public String toString() {
        return "Users{" +
                "uid=" + uid +
                ", userName='" + userName + '\'' +
                '}';
    }
}
~~~

ResponseVO（视图响应对象）：

~~~java
/**
 * 继承了spring-hateoas的ResourceSupport，主要是调用继承的
 * add方法来创建链接信息
 */
public class ResponseVO<T> extends ResourceSupport {
    /**
     * 状态码
     */
    private Integer code;
    /**
     * 响应消息
     */
    private String message;
    /**
     * 响应数据
     */
    private T data;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    @Override
    public String toString() {
        return "ResponseVO{" +
                "code=" + code +
                ", message='" + message + '\'' +
                ", data=" + data +
                '}';
    }
}
~~~

UserController：

~~~java
@RestController
@RequestMapping("/api/v1")
public class UserController{

    @Autowired
    private UserService userService;

    @GetMapping("/users")
    public ResponseVO<List<Users>> listUser(){
        List<Users> list = userService.listUsers();
        ResponseVO<List<Users>> responseVO = responseVO(list);
        responseVO.add(new Link("http://localhost:8080/api/v1/users"));
        responseVO.add(new Link("http://localhost:8080/api/v1/users/id", "item"));
        return responseVO;
    }

    @GetMapping("/users/{uid}")
    public ResponseVO<Users> getUser(@PathVariable("uid") Integer id){
        Users user =userService.getUserById(id);
        return responseVO(user);
    }

}
~~~

最后可在Postman中请求http://localhost:8080/api/v1/users这个API，结果如下：

![image-20191218113619341](https://tva1.sinaimg.cn/large/006tNbRwgy1ga0q0vt5r9j30pk0ev400.jpg)

## 3. Swagger构建RESTful API

使用RESTful API作为Web服务对外提供服务的入口，基本上已经成为了标准，在提供REST API的同时，如何进行 API文档管理是一个较为麻烦的事情，作为开发人员我们都了解API文档的重要性，但总是嫌其编写的麻烦，Swagger的出现很好地帮我们解决文档编写的事情，开发人员可以采取自己喜欢的方式进行API文档编写，并且通过springfox可以很好的和Spring框架集成。

### 3.1 添加Maven依赖

~~~xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- Swagger UI提供了Web页面以方便开发人员查看API文档-->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.9.2</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>
~~~

### 3.2 集成Swagger2

首先创建一个SwaggerConfiguration配置类，如下：

~~~java
@Configuration
@EnableSwagger2
public class SwaggerConfiguration {                                    
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)  
          .select()                                  
          .apis(RequestHandlerSelectors.withClassAnnotation(Api.class))              
          .paths(PathSelectors.any())                          
          .build()
          .apiInfo(apiInfo());
    }
    /**
     * Api接口描述配置
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                // 文档标题
                .title("移动端接口文档")
                // 文档描述
                .description("移动API文档接入说明")
                //api说明网站
                .termsOfServiceUrl("https://github.com/baiczsy")
                //Api版本
                .version("v1")
                .build();
    }
}
~~~

配置说明：

使用`@EnableSwagger2`注解来开启Swagger2功能，在整个类中最重要的就是`Docket`的Bean，`Docket`定义了 Swagger的版本、以及对外暴露的API等信息。上面的api方法构建`Docket`的Bean并做了以下一些配置工作：

1. 创建一个`Docket`对象，并指定API文档类型

2. select()方法生成`ApiSelectorBuilder`对象实例，该对象负责定义对外暴露的API入口

3. 接着调用ApiSelectorBuilder的apis()方法并通过`RequestHandlerSelectors`类进行扫描来决定哪些类需要Swagger生成api文档

   例如：RequestHandlerSelectors.withClassAnnotation(Api.class)表示扫描带有@Api注解的类将生成api文档

4. 然后调用ApiSelectorBuilder的paths()方法并通过PathSelectors匹配满足条件的请求路径，设置为any表示任意路径都匹配，它总是返回true

5. build()方法根据设置好的`RequestHandlerSelectors`和`PathSelectors`返回当前的Docket实例

6. apiInfo()方法用于设置Api的相关描述信息

### 3.3 配置静态资源

接下来还需要配置Spring MVC的Resource Handler，因为后续需要使用到Swagger UI，它有些静态资源需要加载。

Java配置：

Sping mvc配置类实现WebMvcConfigurer接口，并重写addResourceHandlers方法。

~~~Java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("swagger-ui.html")
      .addResourceLocations("classpath:/META-INF/resources/");

    registry.addResourceHandler("/webjars/**")
      .addResourceLocations("classpath:/META-INF/resources/webjars/");
}
~~~

或者使用Xml配置：

~~~xml
<mvc:resources location="classpath:/META-INF/resources/" mapping="swagger-ui.html"/>
<mvc:resources location="classpath:/META-INF/resources/webjars/" mapping="/webjars/**"/>
~~~

最后运行项目，在浏览器中输入：

~~~
http://localhost:8080/swagger-ui.html
~~~

可以看到如下web页面：

![image-20181120112542799](https://ws1.sinaimg.cn/large/006tNbRwgy1fxed6qgidwj32780n8adp.jpg)

### 3.4 Swagger注解

**@Api**

该注解将一个Controller（Class）标注为一个swagger资源（API）。在默认情况下，Swagger-Core只会扫描解析具有@Api注解的类。该注解包含以下几个重要属性：

- tags
  API分组标签。具有相同标签的API将会被归并在一组内展示
- value
  如果tags没有定义，value将作为Api的tags使用
- description
  API的详细描述

例子：

~~~java
@RestController
@RequestMapping("/api/v1")
@Api(tags = "user", description = "用户Api")
public class UserController {
}
~~~

**@ApiOperation**

用在方法上，说明方法的作用。对一个操作或HTTP方法进行描述。具有相同路径的不同操作会被归组为同一个操作对象。不同的HTTP请求方法及路径组合构成一个唯一操作。此注解的属性有： 

- value
   对操作的简单说明，长度为120个字母，60个汉字
- notes
   对操作的详细说明
- httpMethod
   HTTP请求的动作，可选值有："GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" , "PATCH"
- code
   默认为200，有效值必须符合标准的[HTTP Status Code Definitions](https://link.jianshu.com?t=http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

例子：

~~~java
@GetMapping("/users")
@ApiOperation(value="查询用户列表", notes = "查询用户列", httpMethod = "GET")
public List<Users> listUser(){
    List<Users> userList = listUsers();
    return userList;
}
~~~

**@ApiImplicitParams**

用在方法上，包含一组@ApiImplicitParam参数说明

**@ApiImplicitParam**

这个注解@ApiImplicitParam必须被包含在注解@ApiImplicitParams之内,用于对方法的每一个参数进行详细说明。可以设置以下重要参数属性： 

- name
   参数名称
- value
   参数的简短描述
- required
   是否为必传参数
- dataType
   参数类型，可以为类名，也可以为基本类型（int、boolean等）
- paramType
   参数的传入（请求）类型，可选的值有path, query, body, header,form

例子：

~~~java
@GetMapping("/users")
@ApiOperation(value="查询用户信息", notes = "依据ID查询用户详细信息", httpMethod = "GET")
@ApiImplicitParams({
     @ApiImplicitParam(
                name="id",
                value="用户ID",
                required = true,
                dataType = "String",
                paramType = "path")
})
public Users getUser(@PathVariable("id") String id{
        Users user = getUserById(id);
        return user;
}
~~~

**@ApiResponses**

此注解标注在方法上，表示一组响应。用于包含一组@ApiResponse注解。

**@ApiResponse**

描述一个操作可能的返回结果。当REST API请求发生时，这个注解可用于描述所有可能的成功与错误码。可以用，也可以不用这个注解去描述操作的返回类型，这个注解必须被包含在@ApiResponses注解中。可以设置以下重要参数属性：

- code

  HTTP请求返回码。有效值必须符合标准的[HTTP Status Code Definitions](https://link.jianshu.com?t=http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

- message
  易于理解的文本提示消息

- response
  返回类型信息，是一个Class对象

- responseContainer

  如果返回类型为集合类型，可以设置相应的值。有效值为 "List", "Set", "Map"，其他任何无效的值都会被忽略

例子：

~~~java
@GetMapping("/users")
@ApiOperation(value="查询用户列表", notes = "查询用户列", httpMethod = "GET")
@ApiResponses({
      @ApiResponse(code = 200, message = "success",
                    response = Users.class, 		responseContainer = "List"),
@ApiResponse(code = 500, message = "服务器内部错误")
})
public List<Users> listUser() {
     List<Users> userList = listUsers();
     return userList;
}
~~~

**@ApiModel**

标注在model类上，利用这个注解可以做一些更加详细的model结构说明。主要属性有：

- value
  model的别名，默认为类名
- description
  model的详细描述

**@ApiModelProperty**

标注在model的字段或get方法上，对每一个字段进行详细的描述。主要的属性值有：

- value
  属性简短描述
- example
  属性的示例值
- required
  是否为必须值

例子：

~~~java
@ApiModel(value = "users", description = "用户对象信息")
public class Users {

    @ApiModelProperty(value = "用户id", example = "1001")
    private String id;
    @ApiModelProperty(value = "用户名", example = "user1")
    private String userName;
    @ApiModelProperty(value = "年龄", example = "26")
    private Integer age;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Users{" +
                "id='" + id + '\'' +
                ", userName='" + userName + '\'' +
                ", age=" + age +
                '}';
    }  
~~~

