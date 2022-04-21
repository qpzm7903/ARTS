#spring-security
#oauth2-0
# OAuth 2.0是什么

OAuth 2.0是一个**授权的框架**。在访问A应用时，应用A需要访问一些B应用的资源，但是应用A没有用户对应用B的账号密码，也不应该有。所以就出现了OAuth授权框架，OAuth2.0定义了一个流程，使得应用A能够安全的访问用户在应用B的资源。


解决什么问题？

开发系统之间的授权
现代微服务安全
企业内部认证授权（IAM/SSO)


缺点
是个授权框架，不是认证协议
# OAuth的组成元素

- resource owner
- resource server
- client
  - 发出请求受保护资源的应用程序
- authorization server
  - 签发token以及验证token



# OAuth2.0授权流程

如下流程摘自`RFC6749`

```txt
	 +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+
```



Authorization Grant代表resource owner的授权，其中还包含使用哪种形式的OAuth2.0鉴权类型。OAuth2.0包含四种类型。

![[Pasted image 20220420053716.png]]


## 令牌类型

授权码  authorization code token
用于交换获取访问令牌和刷新令牌

刷新令牌 refresh token
用于获取访问令牌

beare token
可以直接访问资源

访问令牌 access token
代表用户或者服务去访问资源

proof of possession（pop) token
校验client对token是否有拥有权




## Authorization Code 授权码

在这种模式下，发起授权申请的时候，会从client跳转到Authorization server，在Authorization server完成resource owner的认证、允许授权后，会带着authorization code跳转回client。

client再拿着authorization code 去Authorization Server去获取access token

此模式下，用户的凭证信息不会泄露给client。


最安全，假定资源拥有者和client不在一个设备上


## Implicit 隐式授权 简化模式

简化版的Authorization code模式。



因为不是所有的第三方应用都有服务器，现在越来越多的web应用都没有应用服务器，那么第三方应用就没法持久化的保留authorization code。所以把authorization code省略了，client直接持有authorization server返回的Token，然后使用这个token直接访问对应的资源。

也就是Authrization Server返回的就是accss token


## Resource Owner Password Credentials 密码模式

此种模式下，client直接使用resource owner的凭证去authorization server获取token。

一般是在很信任client的情况下使用。在大型系统中，模块作为系统的一部分，模块之间可以通过此种方式。



## Client Credentials 客户端模式

client可以使用自己的凭证直接向authorization server获取token。



一般用于授权范围是在client的控制下的受限资源，或者是一些authorization server 提前安排好的资源。



常见的例子是系统中一些自动化处理的模块需要定期去处理一些东西，此时自动化处理的模块就是client。


一般是机机交互。

## 刷新令牌
accss token过期，client可以使用refresh code去获取新的access token 或者 延期


## 选型流程
![[Pasted image 20220420055416.png]]



# client 类型
公开应用 比如 SPA应用，原生APP，一般不存储用户的私密信息

私密应用，web服务器应用  服务器、API 等，可以存用户的私密信息


# spring security OAuth demo

按照OAuth的组成元素，需要一个Authorization server，一个resource server，client就由我们手工代替。

[[spring security]]架构参考



## Authorization Server project



### 依赖

```xml
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-security</artifactId>
      <version>2.2.0.RELEASE</version>
      <scope>compile</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.security.oauth.boot</groupId>
      <artifactId>spring-security-oauth2-autoconfigure</artifactId>
      <version>2.1.2.RELEASE</version>
      <scope>compile</scope>
    </dependency>
```





### 配置类

#### `SecurityConfig`

`SecurityConfig`继承`WebSecurityConfigurerAdapter`，用于配置spring项目的鉴权。因为访问Authorization server本身也是要鉴权的。

```java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    //密码模式才需要配置,认证管理器
    @Bean
    @Override
    protected AuthenticationManager authenticationManager() throws Exception {
        return super.authenticationManager();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeRequests().anyRequest().permitAll()
                .and().formLogin()  // 使用表单认证
                .and().logout();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        // 返回一个用户查询的服务，如果用户名等于admin、user就可以
        return username -> {
            if ("admin".equals(username) || "user".equals(username)) {
                return new MyUserDetails(username, passwordEncoder().encode(username), username);
            }
            return null;
        };
    }
}
```



#### `AuthorizationServerConfig`

用于配置`AuthorizationServer`的配置，比如客户端有哪些，以及访问的endpoint对应的服务。

```java
@Configuration
//开启oauth2,auth server模式
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

    @Autowired
    private PasswordEncoder passwordEncoder;

    //配置客户端
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
                //client的id和密码
                .withClient("client1")
                .secret(passwordEncoder.encode("123123"))

                //给client一个id,这个在client的配置里要用的
                .resourceIds("resource1")

                //允许的申请token的方式,测试用例在test项目里都有.
                //authorization_code授权码模式,这个是标准模式
                //implicit简单模式,这个主要是给无后台的纯前端项目用的
                //password密码模式,直接拿用户的账号密码授权,不安全
                //client_credentials客户端模式,用clientid和密码授权,和用户无关的授权方式
                //refresh_token使用有效的refresh_token去重新生成一个token,之前的会失效
                .authorizedGrantTypes("authorization_code", "password", "client_credentials", "implicit", "refresh_token")

                //授权的范围,每个resource会设置自己的范围.
                .scopes("scope1", "scope2")

                //这个是设置要不要弹出确认授权页面的.
                .autoApprove(true)

                //这个相当于是client的域名,重定向给code的时候会跳转这个域名
                .redirectUris("http://www.baidu.com");

    }


    @Autowired
    private ClientDetailsService clientDetailsService;

    @Autowired
    private TokenStore tokenStore;

    //配置token管理服务
    @Bean
    public AuthorizationServerTokenServices tokenServices() {
        DefaultTokenServices defaultTokenServices = new DefaultTokenServices();
        defaultTokenServices.setClientDetailsService(clientDetailsService);
        defaultTokenServices.setSupportRefreshToken(true);

        //配置token的存储方法
        defaultTokenServices.setTokenStore(tokenStore);
        defaultTokenServices.setAccessTokenValiditySeconds(300);
        defaultTokenServices.setRefreshTokenValiditySeconds(1500);
        return defaultTokenServices;
    }

    //密码模式才需要配置,认证管理器
    @Autowired
    private AuthenticationManager authenticationManager;

    //把上面的各个组件组合在一起
    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        endpoints.authenticationManager(authenticationManager)//认证管理器
                .authorizationCodeServices(new InMemoryAuthorizationCodeServices())//授权码管理
                .tokenServices(tokenServices())//token管理
                .allowedTokenEndpointRequestMethods(HttpMethod.POST);
    }

    //配置哪些接口可以被访问
    @Override
    public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
        security.tokenKeyAccess("permitAll()")///oauth/token_key公开
                .checkTokenAccess("permitAll()")///oauth/check_token公开
                .allowFormAuthenticationForClients();//允许表单认证
    }
}
```





#### `TokenConfig`

```java
@Configuration
public class TokenConfig {

    //配置token的存储方法
    @Bean
    public TokenStore tokenStore() {
        //配置token存储在内存中,这种是普通token,每次都需要远程校验,性能较差
        return new InMemoryTokenStore();
    }
}
```



## Resource Server project



### REST服务

```java

@RestController
public class IndexController {

    @RequestMapping("user")
    public String user() {
        return "user";
    }

    //测试接口
    @RequestMapping("admin")
    @PreAuthorize("hasAnyAuthority('admin')")
    public String admin() {
        return "admin";
    }

    @RequestMapping("me")
    public Principal me(Principal principal) {
        return principal;
    }
}
```





### 依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-oauth2</artifactId>
        </dependency>
       
```





### 配置类

#### `SecurityConfig`

resource server 也需要一些简单的web鉴权配置

```java

@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeRequests()
                .anyRequest().permitAll();
    }
}
```



#### `ResourceServerConfig`

主要的配置在`ResourceServerConfig`，需要配置哪些资源需要被保护，resource server相对于authorization server也是一个client，所以要配置client的相关信息，以及配置去哪里检查token的有效性。

```java

@Configuration
//开启oauth2,reousrce server模式
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {

    @Override
    public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
        //远程token验证, 普通token必须远程校验
        RemoteTokenServices tokenServices = new RemoteTokenServices();
        //配置去哪里验证token
        tokenServices.setCheckTokenEndpointUrl("http://127.0.0.1:3001/oauth/check_token");
        //配置组件的clientid和密码,这个也是在auth中配置好的
        tokenServices.setClientId("client1");
        tokenServices.setClientSecret("123123");
        resources
                //设置我这个resource的id, 这个在auth中配置,需要和authorization server中一致
                .resourceId("resource1")
                .tokenServices(tokenServices)
                //这个貌似是配置要不要把token信息记录在session中
                .stateless(true);
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeRequests()
                //本项目所需要的授权范围,这个scope是写在auth服务的配置里的
                .antMatchers("/**").access("#oauth2.hasScope('scope1')")
                .and()
                //这个貌似是配置要不要把token信息记录在session中
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }
}

```



## 测试

将Authorization server和Resource server分别启动后。



### 授权码模式

#### 获取Authorization code

`http://127.0.0.1:3001/oauth/authorize?client_id=client1&response_type=code&scope=scope1&redirect_uri=http://www.baidu.com`

跳转`URI`随意写，此测试中只是为了获取code。

通过浏览器打开此链接，会要求登录，在Authorization server中已经配置了用户服务，只要用户名等于admin或者user就可以。

登录后会跳转到`https://www.baidu.com/?code=yzXDrY`

注意授权码只能用一次。





#### 获取Authorization token

使用postman发起一个post请求

`http://127.0.0.1:3001/oauth/token?client_id=client1&client_secret=123123&grant_type=authorization_code&code=s38KC1&redirect_uri=http%3A%2F%2Fwww.baidu.com`

收到结果

```json
{
    "access_token": "0d144383-c081-4810-9a30-a0d3955bb149",
    "token_type": "bearer",
    "refresh_token": "9dfb806c-7a1f-4ec5-87ad-6006d781e58d",
    "expires_in": 148,
    "scope": "scope1"
}
```



#### 校验token

发起一个post请求

`http://127.0.0.1:3001/oauth/check_token?token=c2d1ea12-a7bf-41ae-ad5c-cb901782b327`

收到结果

```json
{
    "aud": [
        "resource1"
    ],
    "user_name": "admin",
    "scope": [
        "scope1"
    ],
    "active": true,
    "exp": 1626046736,
    "authorities": [
        "admin"
    ],
    "client_id": "client1"
}
```



#### 使用token访问资源

```http
POST /me HTTP/1.1
Host: 127.0.0.1:4001
Authorization: Bearer c2d1ea12-a7bf-41ae-ad5c-cb901782b327
Cookie: JSESSIONID=54832D134E645907B1434963CF95AAA0
```

收到结果

```json
{
    "authorities": [
        {
            "authority": "admin"
        }
    ],
    "details": {
        "remoteAddress": "127.0.0.1",
        "sessionId": null,
        "tokenValue": "c2d1ea12-a7bf-41ae-ad5c-cb901782b327",
        "tokenType": "Bearer",
        "decodedDetails": null
    },
    "authenticated": true,
    "userAuthentication": {
        "authorities": [
            {
                "authority": "admin"
            }
        ],
        "details": null,
        "authenticated": true,
        "principal": "admin",
        "credentials": "N/A",
        "name": "admin"
    },
    "principal": "admin",
    "credentials": "",
    "oauth2Request": {
        "clientId": "client1",
        "scope": [
            "scope1"
        ],
        "requestParameters": {
            "client_id": "client1"
        },
        "resourceIds": [
            "resource1"
        ],
        "authorities": [],
        "approved": true,
        "refresh": false,
        "redirectUri": null,
        "responseTypes": [],
        "extensions": {},
        "refreshTokenRequest": null,
        "grantType": null
    },
    "clientOnly": false,
    "name": "admin"
}
```


# 扩展
- 刷新令牌
- 用RDS存储用户信息、token
- 用缓存存token
- 授权服务器、资源服务器拆分
- revoke端点
- introspection端点
- JWT非对称加密
- JWE加密、解密JWT令牌



# 令牌
令牌类型：
- 透明令牌，随机生成，无意义，比如auth2.0 token
- 自包含令牌，by value token，token可包含信息的，自解释，包含数字签名，比如JWT
[jwt](https://jwt.io/)令牌



# 下一代微服务安全架构


# 资源
http://www.keycloak.org
https://www.apereo.org/projects/cas
https://identityserver.io/
https://github.com/mitreid-connect/OpenId-Connect-Java-Spring-Server



OAuth 2.0 cookbook

对接类
Google oauth client library
scribeJava
spring security oauth
nimbus oauth sdk
https://oauth.net/code


oauth saas 服务
okta
https://oauth00.com


规范
https://oauth.net
https://jwt.io

开源实现
https://github.com/newnil/oauth2-family-barrel
https://github.com/monkeyk/oauth2-shiro


# 参考

[rfc6749](http://www.rfcreader.com/#rfc6749_line564)

[rfc6750](https://datatracker.ietf.org/doc/html/rfc6750)

[理解OAuth2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

[OAuth2.0的一个简单解释](https://www.ruanyifeng.com/blog/2019/04/oauth_design.html)

[OAuth2.0的四种方式](https://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html)

[GitHub OAuth第三方登录示例教程](https://www.ruanyifeng.com/blog/2019/04/github-oauth.html)

[spring-OAuth-demo](https://github.com/lansinuote/Spring-Oauth2-Toturials)



https://medium.com/@darutk/the-simplest-guide-to-oauth-2-0-8c71bd9a15bb 最简向导

[lab 参考](https://github.com/spring2go/oauth2lab)





