---
title: jwt introduction
date: 2020-05-05 22:51:52
tags: security
---

https://auth0.com/blog/implementing-jwt-authentication-on-spring-boot/

https://github.com/mengxu2018/jwt

During the authentication process, when a user successfully logs in using their credentials, a JSON Web Token is returned and must be saved locally (typically in local storage). Whenever the user wants to access a protected route or resource (an endpoint), the user agent must send the JWT, usually in the Authorization header using the Bearer schema, along with the request.

JWT is commonly used for authorization，jwt与authentiation没有关系，用户还是得用户名密码或者open connect id来登录，但是后续的访问资源，可以携带之前收到的jwt token来访问，这个token这个时候就起到授权的作用,看下面的文章更详细的解释jwt的workflow

https://jwt.io/introduction/
![client-credentials-grant.png](/images/client-credentials-grant.png)
1. The application or client requests authorization to the authorization server. This is performed through one of the different authorization flows. For example, a typical OpenID Connect compliant web application will go through the /oauth/authorize endpoint using the authorization code flow.
2. When the authorization is granted, the authorization server returns an access token to the application.
3. The application uses the access token to access a protected resource (like an API).
可以看到jwt也是需要一个authorization server生成一个token，只是后续直接用token访问api就好了 


https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2

https://www.oauth.com/oauth2-servers/the-resource-server/

Oauth2 与 JWT 区别？
Token功能不一样，JWT的token是包含用户基本信息的，然后通过加密的方式生成的字符串，服务器端拿到这个token之后不需要再去查询用户基本信息，解析完token之后就能拿到。
想想在微服务架构下，用户服务是一个单独的服务，但是其他服务大部分情况下也会需要用户信息，难道要每次用到都去取一次吗？ JWT非常适合微服务。

OAuth2用在使用第三方账号登录的情况(比如使用weibo, qq, github登录某个app)
JWT是用在前后端分离, 需要简单的对后台API进行保护时使用.(前后端分离无session, 频繁传用户密码不安全)
OAuth2是一个相对复杂的协议, 有4种授权模式, 其中的access code模式在实现时可以使用jwt生成code, 也可以不用. 它们之间没有必然的联系.

总结起来就是jwt很轻量级，https://www.cnblogs.com/grissom007/p/6294746.html，这个文章可以看下

oauth2的的资源服务器校验token
http://springcloud.cn/view/431


jwt存储在哪里
可以cookie或者localstorage
https://www.jianshu.com/p/e91fa0eaddd1
