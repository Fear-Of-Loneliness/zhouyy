---
layout: page
title: "Mybatis"
description: "Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的"
subtitle:  	Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的
header-img: "https://zhouyy.top/img/home-bg.jpg"
---
测试！！！

### 前言

在大家的日常面试中，有个问题估计总是能被问到，那就是：

>你有用过MyBatis吗，那你知道Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的吗？

用过MyBatis的同学应该都能随口答出，是通过XML中的namespace和id映射的啊，namespace映射接口的全限定类名，而id映射方法名。

"你能具体介绍一下吗！？"

这就尴尬了，心里一万个草泥马路过。。。。

那么我们就来研究一下MyBatis具体都在搞什么吧！

### 一、解析XML

首先，Mybatis在初始化**SqlSessionFactoryBean**的时候，找到**mapperLocations**路径去解析里面所有的XML文件，这里我们重点关注两部分。

##### 1、创建SqlSource

Mybatis会把每个SQL标签封装成SqlSource对象。然后根据SQL语句的不同，又分为动态SQL和静态SQL。其中，静态SQL包含一段String类型的sql语句；而动态SQL则是由一个个SqlNode组成。

[![SqlSource](http://pqni1rk32.bkt.clouddn.com/image/header/mybatis2.jpg-img){:height="250" width="800"}](http://pqni1rk32.bkt.clouddn.com/image/header/mybatis2.jpg-img)

假如我们有这样一个SQL：

```xml
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

![SqlSource](https://zhouyy.top/img/mybatis2.png){:height="250" width="800"}

##### 2、创建MappedStatement

XML文件中的每一个SQL标签就对应一个MappedStatement对象，这里面有两个属性很重要。

- id

全限定类名+方法名组成的ID。

- sqlSource

当前SQL标签对应的SqlSource对象。

创建完**MappedStatement**对象，将它缓存到**Configuration#mappedStatements**中。

Configuration对象，我们知道它就是Mybatis中的大管家，基本所有的配置信息都维护在这里。把所有的XML都解析完成之后，Configuration就包含了所有的SQL信息。

![SqlSource](https://zhouyy.top/img/mybatis3.png){:height="250" width="800"}

到目前为止，XML就解析完成了。看到上面的图示，聪明如你，也许就大概知道了。当我们执行Mybatis方法的时候，就通过全限定类名+方法名找到**MappedStatement**对象，然后解析里面的SQL内容，执行即可。

### 二、Dao接口代理

我们的Dao接口并没有实现类，那么，我们在调用它的时候，它是怎样最终执行到我们的SQL语句的呢？

首先，我们在Spring配置文件中，一般会这样配置：

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="com.viewscenes.netsupervisor.dao" />
	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
</bean>
```

或者你的项目是基于SpringBoot的，那么肯定也见过这种：**@MapperScan("com.xxx.dao")**

它们的作用是一样的。将包路径下的所有类注册到Spring Bean中，并且将它们的beanClass设置为**MapperFactoryBean**。有意思的是，**MapperFactoryBean**实现了**FactoryBean**接口，俗称工厂Bean。那么，当我们通过**@Autowired**注入这个Dao接口的时候，返回的对象就是**MapperFactoryBean**这个工厂Bean中的**getObject()**方法对象。

那么，这个方法干了些什么呢？

简单来说，它就是通过JDK动态代理，返回了一个Dao接口的代理对象，这个代理对象的处理器是**MapperProxy**对象。所有，我们通过**@Autowired**注入Dao接口的时候，注入的就是这个代理对象，我们调用到Dao接口的方法时，则会调用到**MapperProxy**对象的**invoke**方法。

曾经有个朋友问过这样一个问题：