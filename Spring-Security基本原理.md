# Spring-Security基础

## 简介

Spring Security 是一套基于Spring提供的一套 Web 应用安全性的完整解决方案。一般来说，Web 应用的安全性包括用户认证（Authentication）和用户授权（Authorization）两个部分。

认证：

认证指的是验证某个用户是否为系统中的合法主体（你是谁）。认证一般要求用户提供用户名和密码进行身份校验。

授权：

授权指的是某个用户是否有权限执行某个操作（你能做什么）。在一个系统中，不同用户所具有的权限是不同的。

## 基本原理

![image-20191202084845425](https://tva1.sinaimg.cn/large/006tNbRwgy1g9i39n3f36j31bt0u04by.jpg)

### DelegatingFIlterProxy

DelegatingFilterProxy并不是Spring Security的一部分，属于spring-web模块中的一个类。它只是一个通用Filter的代理，同时自己也实现了FIlter接口，所以它本质上也是一个Filter。它的核心作用就是将Filter进行代理，并将这些Fitler纳入Spring容器中管理，因此这些Filter对象能够享受到Spring IoC容器所带来的好处。当一个请求到达DelegatingFIlterProxy的时候，它会根据自身配置的targetBeanName参数名从容器中查找被代理的Filter来处理这个请求，如果没有指定targetBeanName这个参数，那么默认就以<filter-name>作为Bean的名称来查找被代理的FIlter。

### FilterChainProxy

DelegatingFilterProxy会根据targetBeanName从spring容器中查找一个被代理的Filter来处理当前请求，而在spring-security中，查找到的则是一个FilterChainProxy实例，这个是security中的一个类。也实现了Fitler接口。但这个类并不是用来处理认证和授权的，它是认证授权过滤理链（SecurityFilterChain）的一个代理。FilterChainProxy维护了多个SecurityFilterChain，每一个SecurityFilterChain只会对特定的请求起作用)，而FilterChainProxy在接收到DelegatingFilterProxy传过来的请求时，会从SecurityFilterChain列表中选出一个能够处理当前请求的一个SecurityFilterChain进行认证和授权。

### SecurityFilterChain

SecurityFilterChain表示一个认证授权处理的过滤链，在spring-security中可以利用FilterChainProxy来维护多个SecurityFilterChain。这样做的好处在于不同的过滤链可以针对不同的业务来做不同的认证逻辑处理。而每一个SecurityFilterChain中使用了多个不同的FilterBean来处理认证细节。比如有的处理用户名密码登陆，有的用于维护用户的登录状态，有的用于鉴权等等，这些功能都由一个SecurityFilterChain中不同的FilterBean来完成。

下面列出SecurityFilterChain中一些关键的FilterBean的作用：

#### 1. ChannelProcessingFilter

用于将HTTP请求转向到HTTPS页面，比如登录页面需要用户输入密码，这种带有敏感信息的页面通常需要通过HTTPS保护，如果用户访问的是HTTP，那么ChannelProcessingFilter可以自动重定向到HTTPS的登录页面

#### 2. SecurityContextPersistenceFilter

保存用户的登陆状态，内部实现细节则是将用户信息封装为Authentication对象，验证后将Authentication对象保存在SecurityContext中，最后将整个SecurityContext存入HttpSession。后续可以通过SecurityContextHolder来得到这个验证的对象，又或者由Spring容器将这个对象作为参数传入到controller方法中。

#### 3.UsernamePasswordAuthenticationFilter

用于处理基于form表单的登录认证，对认证成功或失败做出相关的处理，如：跳转页面或是返回json等等。它继承自AbstractAuthenticationProcessingFilter。AbstractAuthenticationProcessingFilter本身并不处理认证逻辑，而是交给一个AuthenticationManager。AuthenticationManager再代理给AuthenticationProvider来完成用户凭证的校验。

#### 4. ExceptionTranslationFilter和FilterSecurityInterceptor

这两个Filter通常是结合在一起用的，前者负责处理后者所抛出的异常并做相应的处理，后者主要用于权限的校验。ExceptionTranslationFilter在处理异常时，如果异常为AuthenticationException类型，表示用户认证失败了(比如还没有登陆)，此时将调用AuthenticationEntryPoint开启认证过程，向用户展示登录页面。如果异常为AccessDeniedException，表示用户可能已经登录但是没有足够的权限，此时将调用AccessDeniedHandler，向用户展示没有权限的通知。





security客户端建表语句

https://github.com/spring-attic/spring-security-oauth/blob/main/spring-security-oauth2/src/test/resources/schema.sql

```sql
-- used in tests that use HSQL
DROP TABLE IF EXISTS `oauth_client_details`;
create table `oauth_client_details` (
  `client_id` VARCHAR(256) PRIMARY KEY,
  `resource_ids` VARCHAR(256),
  `client_secret` VARCHAR(256),
  `scope` VARCHAR(256),
  `authorized_grant_types` VARCHAR(256),
  `web_server_redirect_uri` VARCHAR(256),
  `authorities` VARCHAR(256),
  `access_token_validity` INTEGER,
  `refresh_token_validity` INTEGER,
  `additional_information` VARCHAR(4096),
  `autoapprove` VARCHAR(256)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS `oauth_client_token`;
create table `oauth_client_token` (
  `token_id` VARCHAR(256),
  `token` BLOB,
  `authentication_id` VARCHAR(256) PRIMARY KEY,
  `user_name` VARCHAR(256),
  `client_id` VARCHAR(256)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS `oauth_access_token`;
create table `oauth_access_token` (
  `token_id` VARCHAR(256),
  `token` BLOB,
  `authentication_id` VARCHAR(256) PRIMARY KEY,
  `user_name` VARCHAR(256),
  `client_id` VARCHAR(256),
  `authentication` BLOB,
  `refresh_token` VARCHAR(256)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS `oauth_refresh_token`;
create table `oauth_refresh_token` (
  `token_id` VARCHAR(256),
  `token` BLOB,
  `authentication` BLOB
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS `oauth_code`;
create table `oauth_code` (
  `code` VARCHAR(256)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

DROP TABLE IF EXISTS `oauth_approvals`;
create table `oauth_approvals` (
	`userId` VARCHAR(256),
	`clientId` VARCHAR(256),
	`scope` VARCHAR(256),
	`status` VARCHAR(10),
	`expiresAt` TIMESTAMP,
	`lastModifiedAt` TIMESTAMP
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;


-- customized oauth_client_details table
DROP TABLE IF EXISTS `clientDetails`;
create table `clientDetails` (
  `appId` VARCHAR(256) PRIMARY KEY,
  `resourceIds` VARCHAR(256),
  `appSecret` VARCHAR(256),
  `scope` VARCHAR(256),
  `grantTypes` VARCHAR(256),
  `redirectUrl` VARCHAR(256),
  `authorities` VARCHAR(256),
  `access_token_validity` INTEGER,
  `refresh_token_validity` INTEGER,
  `additionalInformation` VARCHAR(4096),
  `autoApproveScopes` VARCHAR(256)
)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

