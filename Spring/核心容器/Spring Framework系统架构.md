### Spring Framework系统架构

----------

Spring Framework是Spring生态圈中最基础的项目，是其他项目的根基

##### Spring Framework系统架构的版本以Spring4.x为准（已经稳定）

-----------

#### 系统架构图解读

系统架构图讲究上层依赖于下层

- Core Container：核心容器，其他所有的模块都是依赖于此模块运行的

  装对象的，根据这样一个结构设计来看：**Spring是一个管对象的技术**

  - Beans
  - Core
  - Context
  - SpEL

- AOP：面向切面编程，依赖于核心容器的执行

  在不惊动原始程序的基础上，增强其功能

  Aspects：AOP思想实现

  AOP是一个思想，Spring技术对他进行了实现，Spring发现Aspects的制作很好，于是直接将其收录到其整个技术栈中

  所以后期在学习AOP开发的时候，除了要导SpringAOP的坐标，也要导Aspects的坐标

- Data Access：数据访问

  数据层相关的技术

  Data Integration：数据集成（包容其他技术）

  Spring内部不仅提供了自主的访问数据层的技术，同时还支持用Spring技术与其他技术整合使用，例如：MyBatis可以和Spring整合在一起使用

  特别介绍，Transaction：事务，Spring在事务方面做了非常大的突破，提供了一种开发效率极高的事务控制方案

- Web：Web开发

- 系统架构图的最下面是，Test：单元测试和集成测试

综上，要想学习Spring框架首先要学习Core Container，第二部分学习Data Access/Integration，第三部分学习AOP技术，第四部分是Transacton事务（test内容构不成一部分，Web开发在SpringMVC讲解）

-----------------

##### Spring Framework 学习线路

- 第一部分：核心容器

  - 核心概念（IoC/DI）

  - 容器的基本操作

- 第二部分：整合

  - 整合数据层技术MyBatis

- 第三部分：AOP

  - 核心概念
  - AOP基础操作
  - AOP实用开发

- 第四部分：事务

  基本概念在前期都会提到

  - 事务实用开发

- ###### 第五部分：家族

  其他内容在后续课程中学习

  - SpringMVC
  - SpringBoot
  - SpringCloud