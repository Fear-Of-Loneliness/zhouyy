---
layout: page
title: "Mybatis"
description: "Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的"
---

### 前言

在大家的日常面试中，有个问题估计总是能被问到，那就是：

>你有用过MyBatis吗，那你知道Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的吗？

用过MyBatis的同学应该都能随口答出，是通过XML中的namespace和id映射的啊，namespace映射接口的全限定类名，而id映射方法名。

"你能具体介绍一下吗！？"

这就尴尬了，心里一万个草泥马路过。。。。

那么我们就来研究一下MyBatis具体都在搞什么吧！

### 一、解析XML

首先，Mybatis在初始化SqlSessionFactoryBean的时候，找到mapperLocations路径去解析里面所有的XML文件，这里我们重点关注两部分。

##### 1、创建SqlSource

Mybatis会把每个SQL标签封装成SqlSource对象。然后根据SQL语句的不同，又分为动态SQL和静态SQL。其中，静态SQL包含一段String类型的sql语句；而动态SQL则是由一个个SqlNode组成。

![SqlSource](https://zhouyy.top/img/169ce3ccf1155ed5.png){:height="100" width="400"}

假如我们有这样一个SQL：

```java
    <select id="getUserById" resultType="user">
    	select * from user
    	<where>
    		<if test="uid!=null">
    			and uid=#{uid}
    		</if>
    	</where>
    </select>
```

它对应的SqlSource对象看起来应该是这样的：