---
layout: post
title: "解决Hibernate的延迟加载"
date: 2014-08-05 09:23:09 +0800
comments: true
categories: 
- Hibernate
- Spring
---


<p>为了解决不必要的查询，Hibernate的联接查询在默认情况下采用延迟加载，也就是懒加载，在需要数据的情况下再查询。如果在controller层，使用将数据转换成JSON字符串时，session就已经关闭了，再查询时会报错，session closed。<p>
查了几遍资料
有几种思路：

使用过滤器openSessionInViewFilter
``` 
<filter>
	<filter-name>openSessionInViewFilter</filter-name>
	<filter-class>
		org.springframework.orm.hibernate3.support.OpenSessionInViewFilter
	</filter-class>
	<init-param>
		<param-name>singleSession</param-name>
		<param-value>true</param-value>
	</init-param>
	<init-param>
		<param-name>sessionFactoryBean</param-name>
		<param-value>sessionFactory</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>openSessionInViewFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

不过……也有说在springmvc时不太好用，在openSessionInViewFilter openSession时Flush mode是never，需要设置为auto，所以可以继承这个fliter重写filter的openSession和closeSession方法

```
	<filter>
    	<filter-name>solveLazyInitFilter</filter-name>
		<filter-class>
			oukq.servlet.filter.SolveLazyInitFilter
		</filter-class>
    </filter>
      <filter-mapping>
    <filter-name>solveLazyInitFilter</filter-name>
    	<url-pattern>/*</url-pattern>
    </filter-mapping>
```

```
public class SolveLazyInitFilter extends OpenSessionInViewFilter{
    public SolveLazyInitFilter() {
        super();
    }
	public void destroy() {
	}
	@Override
	protected Session getSession(SessionFactory sessionFactory) throws DataAccessResourceFailureException{
		Session session = SessionFactoryUtils.getSession(sessionFactory, true);
		setFlushMode(FlushMode.AUTO);
		FlushMode flushMode = getFlushMode();
		if(flushMode == null){
			session.setFlushMode(flushMode);
		}
		return session;
	}
	@Override
	protected void closeSession(Session session,SessionFactory sessionFactory){
		session.flush();
		SessionFactoryUtils.closeSession(session);
	}
}
```




第二种属于因地制宜了，采用Jackson可以使用@JsonInorg，不过那些懒加载的东西就是null了




clone的方法，返回对象时clone出来一个新的对象，实现cloneable也可以，直接深度克隆也是大大的可以的。



反正controller层只处理传参，所有处理都在service层，受spring的事务管理(声明式事务)，在service层就JSON.toJsonString了