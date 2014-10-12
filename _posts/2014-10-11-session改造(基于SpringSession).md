---
layout: default
title: nosession方案
---
<h3>Spring Session介绍</h3>
Spring Session的源码:https://github.com/spring-projects/spring-session(svn)
目录结构：
─trunk
├─spring-session
│  └─src
│      ├─main
│      │  └─java
│      │      └─org
│      │          └─springframework
│      │              └─session
│      │                  ├─data
│      │                  │  └─redis
│      │                  │      └─config
│      │                  │          └─annotation
│      │                  │              └─web
│      │                  │                  └─http
│      │                  └─web
│      │                      ├─context
│      │                      └─http    
└─spring-session-data-redis
Session:在web项目中都会使用到session，用来保存用户信息等用来对登录后的用户进行验证。在Java Web Applicaton中可以在浏览器使用开发者工具在cookie中看到一个名为JSESSION的cookie，这是spring默认的SESSION方案，同样在ASP.NET的项目中可以看到一个ASPSESSION的cookie名。同理SESSION机制默认在浏览器中是保存在内存中(浏览器？),如果我们需要在两个站点之间进行跳转，但是想保存这个用户信息，我们就需要对session进行处理，不然在跳转后原始页面的session就失效了。所以这里我们可以把session包装一下
<div style="margin-left:2em">Spring session中给我们提供了header/cookie的本地策略，</div>
<div style="margin-left:2em">Web安全中的Session</div>
Give me some time, 这周阅读下Session相关的东西，先放下Springframework了，毕竟东西那么大！

Contact me: 447172336@qq.com/jiangchuan1109@gmail.com  稍后对index中的Contact进行修改
