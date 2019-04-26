---
layout: page
title: "Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的"
description: "hello"
---

### 前言

在大家的日常面试中，有个问题估计总是能被问到，那就是：

>你有用过MyBatis吗，那你知道Mybatis中的Dao接口和XML文件里的SQL是如何建立关系的吗？

用过MyBatis的同学应该都能随口答出，是通过XML中的namespace和id映射的啊，namespace映射接口的全限定类名，而id映射方法名。

"你能具体介绍一下吗？"

这就尴尬了，心里一万个草泥马路过

那么我们就来研究一下MyBatis具体都在搞什么吧