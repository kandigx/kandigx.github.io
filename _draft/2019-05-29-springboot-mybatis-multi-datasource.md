---
title:  "SpringBoot 中配置 Mybatis 多数据源"
permalink: /springboot-mybatis-multi-datasource/
date: 2019-05-29

categories: [java, spring, summary]

tags: [spring, springboot, mybatis, datasource, 多数据源]
---

[TOC]

# SpringBoot 中 Mybatis 多数据源的配置

Mybatis 的数据源管理在 SpringBoot 中尤其简单，只需要添加自动配置类 `mybatis-spring-boot-starter`，以及相应的配置信息，该配置类就会

1. 根据配置信息自动检测数据源
2. 创建 SqlSessionFactory，将数据源作为参数传递到 SqlSessionFactoryBean 来使用
3. 从 SqlSessionFactory 中创建 SqlSessionTemplate
4. 自动扫描 mappers，将其关联到 SqlSessionTemplate 并注册到 Spring 上下文中。

