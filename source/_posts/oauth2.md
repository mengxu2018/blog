---
title:  oauth2
tags:
---

- Authentication is the process of verifying an identity (who they say they are)
- Authorization is the process of verifying what someone is allowed to do (permissions)


这篇文章讲了outh2，比较权威
https://oauth.net/articles/authentication/

这篇讲了oauth应用于微服务
https://developer.okta.com/blog/2018/02/13/secure-spring-microservices-with-oauth

这个文章讲oauth不是用于authentication而是authorization
https://stackoverflow.com/questions/33702826/oauth-authorization-vs-authentication
`OAuth authentication?`
Authentication deals information about "who one is". Authorization deals information about "who grants what permissions to whom". Authorization flow contains authentication as its first step. It is the reason people are often confused.
There are many libraries and services that use OAuth 2.0 for authentication. It is often called "social login" and It makes people more confused. If you see "OAuth authentication" (not "OAuth authorization"), it is a solution using OAuth for authentication.


oauth2的specs也可以看看，已经明确说明oauth2不是用于认证的
`OAuth 2.0 is a specification for authorization, but NOT for authentication. RFC 6749, 3.1. Authorization Endpoint explicitly says as follows:`
The authorization endpoint is used to interact with the resource owner and obtain an authorization grant. The authorization server MUST first verify the identity of the resource owner. The way in which the authorization server authenticates the resource owner (e.g., username and password login, session cookies) is beyond the scope of this specification.
oauth2和oauth1的区别如下
https://stackoverflow.com/questions/4113934/how-is-oauth-2-different-from-oauth-1


OpenID Connect
OpenID 1.0 and OpenID 2.0 are old specifications for authentication. Those who made the specifications expected people to use OpenID for authentication. However, some people began to use OAuth 2.0 for authentication (not for authorization) and OAuth authentication has prevailed rapidly.
From a viewpoint of OpenID guys, authentication based on OAuth was not secure enough, but they had to admit that people preferred OAuth authentication. As a result, OpenID guys decided to define a new specification, OpenID Connect, on top of OAuth 2.0.
Yes, this has made people much more confused.


Let’s start with the biggest reason why OAuth isn’t authentication: access tokens are not intended for the client application.
https://www.scottbrady91.com/OAuth/OAuth-is-Not-Authentication



## 总结
oauth是一个授权框架， 只不过授权之前需要登陆，但是这个并不代表oauth是认证框架，
但是很多人就开始用oauth2来做认证
主要这个文章https://stackoverflow.com/questions/33702826/oauth-authorization-vs-authentication
(Identity, Authentication) + OAuth 2.0 = OpenID Connect
https://medium.com/@darutk/full-scratch-implementor-of-oauth-and-openid-connect-talks-about-findings-55015f36d1c3


