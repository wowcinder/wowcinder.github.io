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



<!--more-->