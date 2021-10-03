1、架构

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