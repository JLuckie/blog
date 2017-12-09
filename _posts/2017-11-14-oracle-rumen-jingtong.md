---
layout: post
title: 《ORACLE PL SQL入门到精通》
date:   2017-11-14
categories: "readme"
tags: [jekyll, material design]
author: LiangJie
comment: false
---

# Oracle 11g数据库系统
## 关系型数据库系统介绍
### 关系型数据库
* **关系型数据库**：使用关系或者二维表储存信息
* **Excel**：平面文件数据模型
* 不同的二维表，使用主键/外键关联，就是**关系型数据模型**
### 数据库系统范式
* **第一范式**：字段必须具有单一属性特性，不可再拆分
* **第二范式**：表要具有唯一性的主键列
* **第三范式**：表中的字段不能包含在其他表中已经出现的非主键字段
### 关系型数据库管理系统
* **DBMS**：关系型数据库管理系统
* **SQL**(Structured Query Language)：结构化查询语言
* SQL语言分为如下两大类：
    * **DML**：数据操纵语言，增删改查
    * **DDL**：数据定义语言，创建/修改表、视图、存储过程、用户等
    * *DCL*：数据控制语言
## PL/SQL
* **PL/SQL**(Procedural Language/SQL)：为标准SQL语言添加过程化功能的程序设计语言
* 最基本的是语句块，块与块可嵌套，可以在块中包含多条SQL及PL/SQL语句，网络上只需要发送一次块，可以完成所有数据库里，减小网络开销
* 
