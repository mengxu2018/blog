---
title: jwt spring security
date: 2020-02-23 22:51:52
tags: security
---

https://auth0.com/blog/implementing-jwt-authentication-on-spring-boot/

https://github.com/mengxu2018/jwt

During the authentication process, when a user successfully logs in using their credentials, a JSON Web Token is returned and must be saved locally (typically in local storage). Whenever the user wants to access a protected route or resource (an endpoint), the user agent must send the JWT, usually in the Authorization header using the Bearer schema, along with the request.

JWT is commonly used for authorization，jwt与authentiation没有关系，用户还是得用户名密码或者open connect id来登录，但是后续的访问资源，可以携带之前收到的jwt token来访问，这个token这个时候就起到授权的作用