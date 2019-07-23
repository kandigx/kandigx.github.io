---
title:  "几种基本的数据结构及其实现"
permalink: /data-structure/
date: 2019-07-22

categories: [基础, 数据结构]

tags: [math, data-structure, basic]
---

[TOC]

# 几种基本的数据结构及其实现

几种非常基础的数据结构，及其实现方法，作为笔记记录下。

## 栈（Stack）

### 特性

- 是-种线性结构
- 相比数组，栈对应的操作是数组的子集
- 只能从一端添加元素，也只能从一端取出元素
- 这一端称为栈顶
- 栈是一种后进先出的数据结构，Last In First Out (LIFO)

### 栈的实现

Stack\<E>

- void push(E)
- E pop()
- E peek( )
- int getSize()
- boolean isEmpty()

