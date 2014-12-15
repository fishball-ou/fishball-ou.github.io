---
layout: post
title: "有关SpringMVC的问题"
date: 2014-08-06 14:02:43 +0800
comments: true
categories: 
- SpringMVC,Spring
---

今天在普通servlet工程那里使用@Controller的声明式注入，结果到连接到servlet时老报could not bound the servlet。就老是怀疑@Resource怎么老注入不进去呢，我最后的理解是servlet有tomcat自己创建，而不是spring创建。不是有spring的dispathcer分发至，所以有这个问题。