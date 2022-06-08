# SpringBoot学习笔记

[TOC]

# 基本注解

- @Configuration，标注为配置类，boolean proxyBeanMethods() default true。默认使用Full模式
  - 配置 类组件之间无依赖关系用Lite模式加速容器启动过程，减少判断
  - 配置类组件之间有依赖关系，方法会被调用得到之前单实例组件，用Full模式
- @Import，导入组件
- @Conditional，实例注入条件判断，有多个实现方式
- @ConfigurationProperties绑定配置文件属性





