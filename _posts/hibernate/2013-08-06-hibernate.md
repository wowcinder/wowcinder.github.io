---
layout: post
category : java
tags : [hibernate]
---
{% include JB/setup %}
###关联
>@OneToMany  mappedBy
>@ManyToOne 默认fetch:EAGER  @JoinColumn
###删除 
>建议先load后删除，方便使用CascadeType.REMOVE
<<<<<<< HEAD
###load get
>load方式检索不到的话会抛出org.hibernate.ObjectNotFoundException异常
>get方法检索不到的话会返回null
###a different object with the same identifier value was already associated with the session
	session.clean()
	session.refresh(object)
	session.merge(object)


<!--more-->
