---
layout: post
category : java
tags : [hibernate]
---
{% include JB/setup %}

###关联
>\@OneToMany  mappedBy
>@ManyToOne 默认fetch:EAGER  @JoinColumn
###删除 
>建议先load后删除，方便使用CascadeType.REMOVE
###load get
>load方式检索不到的话会抛出org.hibernate.ObjectNotFoundException异常
>get方法检索不到的话会返回null
###a different object with the same identifier value was already associated with the session
	session.clean()
	session.refresh(object)
	session.merge(object)
### megre
>megre 可能也更新关联的对象
###关联
>hibernate 自动建表的关联一般是外键，但实际表结构不一定是外键
###fetch
>[man](http://www.mkyong.com/hibernate/hibernate-fetching-strategies-examples)
<!--more-->





