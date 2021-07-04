# OAuth 2.0是什么？

是一个授权的框架。在访问A应用时，应用A需要访问一些B应用的资源，但是应用A没有用户对应用B的账号密码，也不应该有。所以就出现了OAuth授权框架，OAuth2.0定义了一个流程，使得应用A能够安全的访问用户在应用B的资源。



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









# 参考

[rfc6749](http://www.rfcreader.com/#rfc6749_line564)

