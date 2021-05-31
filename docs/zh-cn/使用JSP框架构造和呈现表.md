# 使用JSP框架构造和呈现表

#### 目标

您希望以表格格式显示信息，并可选地对显示的数据执行操作。

#### 背景

表格通用组件以表格格式显示信息，以帮助用户扫描、定位、比较和对相关数据采取行动。用户可以

使用排序和视图控制如何查看表中的数据。下面的表详细说明了表中的特定元素。

#### 解决方案

实现MVC表构建器来创建或转换JCA表组件。

#### 预备知识

为了实现这一目标，您需要了解以下JCA技术:

- 数据工具
- GUI组件
- 活动框架
- 可配置的表视图
- java注解

#### ==解决要素==：

| 元素                             | 类型            | 描述                           |
| -------------------------------- | --------------- | ------------------------------ |
| ComponentDataBuilder             | Java interface  | 为组件创建数据模型             |
| ComponentConfigBuilder           | Java interface  | 为组件创建配置                 |
| ConfigurableTableBuilder         | Java interface  | 为表和树创建可配置的表视图     |
| AbstractConfigurableTableBuilder | Java class      | 构建器通常扩展的基类           |
| ComponentConfig                  | Java interface  | 组件配置的基本接口             |
| ComponentConfigFactory           | Java interface  | 创建ComponentConfig实现        |
| ComponentBuilder                 | Java annotation | 配置用于查找组件构建器的组件id |

#### 过程

配置表常用组件

##### 创建一个新的Builder类

表构建器类对应于表配置。所有表级和列级属性都应该在构建器类中配置。当创建一个新的构建器类时，你通常应该扩展MVC基础结构提供的一个抽象基类