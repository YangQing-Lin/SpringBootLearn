### SpringBoot简介（起步依赖）

-----------------

##### SpringBoot概述

- SpringBoot是由Pivotal团队提供的全新框架，其设计目的是用来简化Spring应用的初始搭建以及开发过程
- Spring程序缺点
  - 配置繁琐
  - 依赖设置繁琐
- SpringBoot程序优点
  - 自动配置
  - 起步依赖（简化依赖配置）
  - 辅助功能（内置服务器，......）

---------------------

##### SpringBoot起步依赖

- starter
  - SpringBoot中常见项目名称，定义了当前项目使用的所有项目坐标，以达到**减少配置**的目的
- parent
  - 所有SpringBoot项目要继承的项目，定义了若干个坐标版本号（依赖管理，而非依赖），以达到**减少依赖冲突**的目的
  - spring-boot-starter-parent（2.5.0）与 spring-boot-starter-parent（2.4.6）共计57处坐标版本不同
- 实际开发
  - 使用任意坐标，仅书写GAV中的G和A，V由SpringBoot提供
  - 如发生坐标错误，再指定version（要小心版本冲突）