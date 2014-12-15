---
layout: post
title: "org.springframework.dao.support.DaoSupport cannot be resolved"
date: 2014-08-04 11:49:54 +0800
comments: true
categories: 
- Spring
- Hibernate
---

在继承HibernateDaoSupport时报org.springframework.dao.support.DaoSupport cannot be resolved错。
原因是整合时候没导入spring-tx.RELEASE.jar包，导入即可。