#spring-security 
# 1、架构

https://spring.io/guides/topicals/spring-security-architecture

>  how a secure application works, how it can be customized, or if you need to learn how to think about application security.



authentication 

who are you?

接口

```java
public interface AuthenticationManager {

  Authentication authenticate(Authentication authentication)
    throws AuthenticationException;
}
```

实现委托给了`AuthenticationProvider`责任链。



```java
public interface AuthenticationProvider {

	Authentication authenticate(Authentication authentication)
			throws AuthenticationException;

	boolean supports(Class<?> authentication);
}
```





authorization 

what are you allowed to do?

# spring security oauth 2.0
![[Pasted image 20220420060130.png]]

参考[auth2.0 flow](https://terasolunaorg.github.io/guideline/5.3.0.RELEASE/en/Security/OAuth.html)


# spring in action 5 spring security

了解spring security怎么工作，怎么自定义

自动配置 Spring Security

自定义用户存储

自定义登录页面

防御 CSRF 攻击

了解你的用户



## spring security



依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```



添加这个依赖之后，就简单的被保护了。访问需要账号密码

所有的 HTTP 请求路径都需要认证。

不需要特定的角色或权限。

没有登录页面。

身份验证由 HTTP 基本身份验证提供。

只有一个用户；用户名是 *user*。



但是最基本的特性还不足以满足需求



比如

提示使用登录页面进行身份验证，而不是使用 HTTP 基本对话框。

为多个用户提供注册页面，让新的用户可以注册。

为不同的请求路径应用不同的安全规则。例如，主页和注册页面根本不需要身份验证。



## spring security log

```properties
logging.level.org.springframework.security=DEBUG
```



## 配置spring security

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers("/", "/home").permitAll()
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .loginPage("/login")
                .permitAll()
                .and()
                .logout()
                .permitAll();
    }

    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        UserDetails user =
                User.withDefaultPasswordEncoder()
                        .username("user")
                        .password("password")
                        .roles("USER")
                        .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```

通过使用`EnableWebSecurity`注解，并继承`WebSecurityConfigureAdaptor`类，覆盖里面的一些方法，比如configure，指定哪些路径需要鉴权，哪些不要，登录界面、退出登录、以及`serDetailService`方法，指定一些用户



### 通过java配置

配置类，就可以得到一个简单的登陆界面



### 配置用户

可以基于

一个内存用户存储

基于 JDBC 的用户存储

由 LDAP 支持的用户存储

定制用户详细信息服务



都要实现

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    ...
}
```





### 内置用户-基于内存

一般在测试的场景使用

```java

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
        .inMemoryAuthentication() // 在内存中认证身份
            .withUser("buzz")
                .password("infinity")
                .authorities("ROLE_USER") // 授予 USER权限  等价于 .role("USER")
            .and()
            .withUser("woody")
                .password("bullseye")
                .authorities("ROLE_USER");
	}
}
```



### 基于jdbc

表呢？jdbc模式默认一些表的存在，但是也可以自定义用户查询。

密码可以进行一些加密

- BCryptPasswordEncoder —— 采用 bcrypt 强哈希加密
- NoOpPasswordEncoder —— 不应用任何编码
- Pbkdf2PasswordEncoder —— 应用 PBKDF2 加密
- SCryptPasswordEncoder —— 应用了 scrypt 散列加密
- StandardPasswordEncoder —— 应用 SHA-256 散列加密

![image-20210521200310691](spring学习.assets/image-20210521200310691.png)

只要确保在对密码加解密的适合用同一个encoder就可以



### 基于LADP

是什么东西

### 自定义用户服务

不管是基于内存、jdbc、ldap都是通过覆写WebSecurityConfigurerAdapter类。

自定义服务是通过实现`UserDetailsService`接口。

主要是实现里面的`loadUserBUsername`方法。注意此方法返回的是`UserDetails`对象，实现的时候可以继承这个类。









## 保护web请求

拦截想拦截的界面，放行应该放行的界面

能做到

在允许服务请求之前，需要满足特定的安全条件

配置自定义登录页面

使用户能够退出应用程序

配置跨站请求伪造保护

实现

```java
WebSecurityConfigurerAdapter.configure
```

比如

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests() // 授权访问
                .antMatchers("/design", "/orders")
                    .hasRole("ROLE_USER")
                .antMatchers(“/”, "/**").permitAll();
        }
}
```

对于design,order界面，是允许ROLE_USER访问的，其他用户不允许。

然后其他的请求都允许。

注意顺序是由影响的。

注意，角色名也有坑，spring security 会自动加ROLE前缀

如果先允许所有，再给ROLE_USER授权就无效了。



常用的配置方法

| 方法                       | 做了什么                               |
| -------------------------- | -------------------------------------- |
| access(string)             | SpEL的表达式为True即可访问             |
| anonymous()                | default user can access                |
| authenticated()            | 认证用户可以访问                       |
| denyAll()                  |                                        |
| fullyAuthenticated()       | 完全授权...                            |
| hasAnyAuthority(String...) | 拥有任意给定的权限，可以访问           |
| hasAnyRole(String...)      | 拥有任意给定的角色，可以访问           |
| hasAuthority(String)       | 如果用户有给定的权限，则允许访问       |
| hasIpAddress(String)       | 来自给定 IP 地址的请求允许访问         |
| hasRole(String)            | 如果用户有给定的角色，则允许访问       |
| not()                      | 拒绝任何其他访问方法                   |
| permitAll()                | 无条件允许访问                         |
| rememberMe()               | 允许认证了的同时标记了记住我的用户访问 |



对于SpEL的扩展

| Security 表达式           | 意指什么                                                    |
| ------------------------- | ----------------------------------------------------------- |
| authentication            | 用户认证对象                                                |
| denyAll                   | 通常值为 false                                              |
| hasAnyRole(list of roles) | 如果用户有任何给定的角色，则为 true                         |
| hasRole(role)             | 如果用户有给定的角色，则为 true                             |
| hasIpAddress(IP Address)  | 如果请求来自给定 IP 地址，则为 true                         |
| isAnonymous()             | 如果用户是默认用户，则为 true                               |
| isAuthenticated()         | 如果用户是认证了的，则为 true                               |
| isFullyAuthenticated()    | 如果用户被完全认证了的（不是使用记住我进行认证），则为 true |
| isRememberMe()            | 如果用户被标记为记住我后认证了，则为 true                   |
| permitAll()               | 通常值为 true                                               |
| principal                 | 用户 pricipal 对象                                          |



FAQ

疑问：spring security 怎么做授权、鉴权的

使用filter

### 配置登陆界面

登陆界面可配置，就是配置一个简单的视图，请求发到regsiter上，还可以配置登陆后的跳转





### 登出

登出也要做跳转

配置比如

```java
.and()
    .logout()
        .logoutSuccessUrl("/")
```



以及登出的按钮

```html
<form method="POST" th:action="@{/logout}">
    <input type="submit" value="Logout"/>
</form>
```



### 阻止跨站请求伪造攻击

spring seurity 可以做，thymleafh默认就做了



### 领域与用户关联

通过jpa的关系注解很容易做到。

那么再controller中如何获取用户？



常用的方法由：

- 将主体对象注入控制器方法
- 将身份验证对象注入控制器方法
- 使用 SecurityContext 获取安全上下文
- 使用 @AuthenticationPrincipal 注解的方法



## spring security 架构

安全的两个问题

- authentication (who are you?) 
- authorization or Access Control (what are you allowed to do?)



### authentication 身份认证

```java
interface AuthenticationManager {
    /**
    
    */
    Authentication authenticate(Authentication authentication) throws AuthenticationException;
}

public class ProviderManager implements AuthenticationManager{
    
}
```

用的大多数是providerManager，其下有一个代理的职责链



### authorization 授权 or Access Control  



### Web Security

在sring中，是基于Filter做的spring security

```
client->filter->filter->filter->...->Servlet
```

### 为分发和授权做请求匹配

```java
@Configuration
@Order(SecurityProperties.BASIC_AUTH_ORDER - 10)
public class ApplicationConfigurerAdapter extends WebSecurityConfigurerAdapter {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.antMatcher("/match1/**")
      .authorizeRequests()
        .antMatchers("/match1/user").hasRole("USER")
        .antMatchers("/match1/spam").hasRole("SPAM")
        .anyRequest().isAuthenticated();
  }
}
```



# 参考
http://terasolunaorg.github.io/guideline/

[极客时间微服务实战](https://github.com/spring2go/oauth2lab.git)