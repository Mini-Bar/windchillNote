# JCA

### JCA组件

下面是ComponentConfig的几个实现:

- JcaTableConfig
- JcaAttributesTableConfig
- JcaTreeConfig
- JcaAttributePanelConfig
- JcaColumnConfig
- JcaInfoConfig
- JcaPropertyConfig
- JcaPropertyPanelConfig

#### MVC的JCA配置

所有与JCA相关的Spring配置都可以在 <Windchill>\codebase\config\mvc\jca-mvc.xml.中找到。下面的代码片段显示了url处理程序映射的配置。所有的url模式 `ptc1/comp/*`被映射到一个id为componentController的bean，而模式`ptc1/tcomp/*`被映射到一个id为typeBasedComponentController的bean。

```xml
<bean id="abstractComponentHandlerMapping" abstract="true"
     class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    -----
</bean>
<bean id="componentHandlerMapping" parent="abstractComponentHandlerMapping">
    <property name="mappings" ref="componentHandlerMappings" />
    -----
</bean>
<bean id="componentHandlerMappings"
     class="org.springframework.beans.factory.config.PropertiesFactoryBean">
    <property name="properties"><props>
        <prop key="/comp/**">componentController</prop>
        <prop key="/tcomp/**">typeBasedComponentController</prop>
    </props></property>
</bean>
```

下面的代码片段显示了使用componentController id的bean的定义。

- jcaComponentBuilderResolver 作为ComponentBuilderResolver 注入
- defaultViewMappings 属性被定义为映射默认视图

```xml
<bean id="componentController" class="com.ptc.mvc.components.ComponentController">
     <property name="componentBuilderResolver" ref="jcaComponentBuilderResolver" />
     <property name="defaultViewMappings">
          <map>
               <entry key="com.ptc.mvc.components.TreeConfig" 
                      value="/components/tree.jsp" />
               <entry key="com.ptc.mvc.components.TableConfig" 
                      value="/components/table.jsp" />
               <entry key="com.ptc.mvc.components.AttributesTableConfig"
                      value="/components/table.jsp" />
               <entry key="com.ptc.mvc.components.AttributePanelConfig" 	
                      value="/components/attributePanel.jsp" />
          </map>
      </property>
     -----------
</bean>
```

下面的代码片段显示了带有typeBasedComponentController id的bean的定义。

- typeBasedComponentBuilderResolver 作为ComponentBuilderResolver被注入。它尊重上下文对象的Windchill类型，同时寻找最佳匹配的构建器。

```xml
<bean id="typeBasedComponentController" 
class="com.ptc.mvc.components.ComponentController“
                            parent="componentController“>
     <property name="componentBuilderResolver" 
ref="typeBasedComponentBuilderResolver" />
</bean>
```

指定的默认处理程序将处理所有没有映射的MVC url。

```xml
<bean id="componentHandlerMapping" parent="abstractComponentHandlerMapping">
    <property name="defaultHandler" ref="netmarketsController" />
    -----
</bean>
<bean id="netmarketsController" class="com.ptc.jca.mvc.controllers.LegacyController" />
```

#### ComponentConfigFactory

ComponentConfigFactoryAware 的构建器将被注入ComponentConfigFactory。ComponentConfigFactory提供了ComponentConfig的不同实现实例(如表、树等)。在JCA中，注入发生在bean后置处理器中，并在<Windchill>\codebase\config\mvc\jca-mvc.xml                                              中配置。

```xml
<bean class="com.ptc.mvc.components.support.ComponentBuilderBeanPostProcessor">
     .....
     <property name="componentConfigFactory" ref="jcaComponentConfigFactory" />
     <property name="infoComponentConfigFactory">
          ......
     </property>
     <property name="typedAttrLayOutFactory">
          <bean class="com.ptc.jca.mvc.components.JcaTypedAttrLayOutFactory" />
     </property>
</bean>
<bean name="jcaComponentConfigFactory" 
      class="com.ptc.jca.mvc.components.JcaComponentConfigFactory"
      factory-method="getInstance" />
```

#### JCA DataBuilders

![MVC_Overview15](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview15.png)

#### ComponentDataConvertor

组件数据解析：

```xml
<bean id="jcaComponentDataConverter" 
           class="com.ptc.jca.mvc.components.JcaComponentDataConverter" />
<bean id="jcaComponentDataBuilder"
           class="com.ptc.jca.mvc.components.DefaultJcaComponentDataBuilder" 
scope="prototype">
     <property name="componentDataConverter" ref="jcaComponentDataConverter" />
</bean>
<bean id="jcaComponentBuilderResolver“
           class="com.ptc.jca.mvc.components.JcaComponentBuilderResolver">
     ......
     <lookup-method name="createJcaDataBuilder" bean="jcaComponentDataBuilder" />
</bean>
```

数据阔以从ComponentDataBuilder 得到，需要被处理，以便能被JCA使用。彻底运行数据的dataUtilities 发生在这个阶段。JcaComponentDataConverter 这样做，并被注入到jcaComponentBuilderResolver中。

#### ComponentBuilder Resolver

组件模式解析：

- 如果指定的DataBuilder 是ConvertingComponentDataBuilder的一个实例，JcaComponentBuilderResolver 将其用作ComponentDataBuilder。

- 如果指定的DataBuilder 是ComponentDataBuilder的一个实例，JcaComponentBuilderResolver 使用它作为DefaultJcaComponentDataBuilder的内部构建器。

![MVC_Overview16](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview16.png)

#### 生成MVC URLs

有两种类型的JCA MVC url。

1. 不是基于类型的JCA MVC url:	这里的上下文对象不参与构建器解析过程。
2. 基于类型的JCA MVC url:  这里的上下文对象的类型参与构建器解析过程。

这是使用操作定义生成的

```xml
<action> 
      <component name="myCompId" ……/> 
</action>
<action> 
      <component name="myCompId" " typeBased="true" ……/> 
</action>
e.g 
<action name="CustEx_mvc_table">
   <component name="carambola.mvc.table" windowType="page" />
</action>
<action name="primaryAttributes" resourceBundle="com.ptc.core.ui.navigationRB">
   <component name="primaryAttributes" typeBased="true" windowType="page"/>
   ---
</action>
```

#### 带有数据源的JCA组件

==表大小限制==

表中显示的最大行数由这个具有USER级别可见性的首选项驱动。它将适用于系统中的所有JCA表。目前OOTB的默认值是2000行，您可以根据客户端硬件和浏览器类型改变它。

对于树，该限制是强制的，但最终用户可以覆盖该限制。一旦树达到大小限制，任何节点的折叠都将从客户机中删除其子节点，以释放客户机中的一些资源。这将一直发生，直到客户端中的节点数量低于大小限制。

##### 操作处理

活动大致可分为三大类。这些操作可以在加载对象后执行。

1. 仅对加载的数据起作用的活动
   - 行级和多选择操作
   - 选择全部，Ctrl/Shift选择，清除全部
   - 仅查看选定对象
2. 作用于整个数据集的操作,这些操作可以在任何时候执行(如果数据集不完整，则到服务器获取所有数据)
   - 转换表视图
   - 表中搜索
   - 导出列表
3. 应用于表本身的操作，这些操作可以在任何时间执行
   - 创建活动
   - 取消数据加载
   - 保存表视图

#### 排序

排序是一种特殊的操作，使用DataSource时，取决于客户机中的数据。

- 如果是完整的数据集
  - 排序发生在客户端
- 如果数据仍在加载
  - 排序发生在客户端中已经存在的数据上
  - 将剩余的轮询行结果按顺序插入到客户端
- Version列是个例外，它使服务器进行排序，但只对客户机中可用的数据集进行排序。

#### ==技巧==

##### Enabling Spring Logging：启动日志

请在<Windchill>\codebase\WEB-INF\log4jMethodServer.properties 中添加以下条目,并重新启动服务器。

```properties
log4j.logger.org.springframework=DEBUG
```

##### 访问本地化信息：

ResourceBundleClientMessageSource 可用于获取本地化消息。

```java
 String resourceBundle = "com.ptc.test.TestResource";
    ClientMessageSource messageSource = 
new ResourceBundleClientMessageSource(resourceBundle);
    String message = messageSource.getMessage(TestResource.TEST);

   Entry in the TestResource file

   @RBEntry("Test Message")
   public static final String TEST = "test";
```

如果您的构建器扩展了OOTB抽象构建器，那么可以利用它的api

> AbstractComponentConfigBuilder.getMessageSource(String resourceBundle)                                                                       AbstractComponentDataBuilder.getMessageSource(String resourceBundle)                                                                       AbstractInfoConfigBuilder.getMessageSource(String resourceBundle)

##### 存储视图组件

所有的视图jsp应该放在<Windchill>\WEB-INF\jsp 下，并且在指定时指定的值应该相对于<Windchill>\WEB-INF\jsp。

```java
table.setView("/carambola/carambolaMVCExampleTable.jsp");
```

##### 在JCA组件生成器中请求Nm*Bean

```java
NmHelperBean helper = ((JcaComponentParams) params).getHelperBean();
NmCommandBean nmCommandBean = helper.getNmCommandBean();
HttpServletRequest request = nmCommandBean.getRequest();
```

##### Thread Safety of Builders 线程安全

默认情况下，构建器遵循servlet(单例作用域)的线程安全范式，这意味着构建器中的实例变量必须是线程安全的。配置和数据构建api之间的通信可以通过配置对象本身完成，也可以通过ComponentParams完成。

```java
public class TestComponentBuilder extends AbstractComponentBuilder{
       @Override
       public ComponentConfig buildComponentConfig(ComponentParams params) throws WTException {
           Object myObject;
           //add information that you want to pass to data builder
           params.setAttribute("myKey", myObject);
           -----
       } 
       @Override
       public Object buildComponentData(ComponentConfig config, ComponentParams params) throws WTException { 
           //get the information that was put in config builder
           Object myObject = params.getAttribute("myKey");
           -----
       }
   }
```



构建html块元素的集合。包括jsp页面。

## jca:wizardStep(向导)

定义：通过逐步收集信息来对对象创建或执行操作。

### Wizards 

是**<font color='red'>弹出窗口</font>**，用于引导您逐步完成创建对象或对对象执行操作的过程。向导是一种用户界面，由一系列**<font color='red'>步骤组成</font>**，这些步骤可引导用户一次完成一个特定任务，然后单击下一步以浏览各个步骤。可能没有必要完成向导中存在的所有步骤。提供所需的信息后，您可以单击“完成” /“确定”按钮将数据提交到服务器。向导可以包含一个或多个步骤，这些步骤**<font color='red'>必须以一定顺序</font>**完成。(按步骤进行）)

![BasicCustomization-rds.10.349.1](https://gitee.com/cmlovecat/typorapic/raw/master/pics/BasicCustomization-rds.10.349.1.jpg)

### clerk

是一个**<font color='red'>用户界面</font>**，由具有两个或多个选项卡的**<font color='red'>选项卡控件组成</font>**。可以输入数据，并且用户可以按任何顺序填写选项卡。即使当前选项卡不完整，用户也可以从一个选项卡自由移动到另一个选项卡。职员不是要按顺序指导用户完成某项任务，通常用于**<font color='red'>执行频繁且容易完成</font>**的任务。（自由切换）

![BasicCustomization-rds.10.349.2](https://gitee.com/cmlovecat/typorapic/raw/master/pics/BasicCustomization-rds.10.349.2.jpg)

区别：单步向导和多步向导的布局将有所不同。单步向导将没有“步骤图标”，“步骤链接”和“上一步/下一步导航按钮”。

#### ajax

 <font color='red'>page</font>:值 “ page”刷新整个页面（相当于不使用ajax）。

<font color='red'>component</font>:值“ component”刷新从中启动向导的表。

<font color='red'>row</font>:值“行”刷新一个或多个表行。

#### UIComponent

引用roleaccessprefs.xml（用户名）中的条目，该条目用于指定对向导操作的基于角色的访问。

#### 这是用于启动“ MyPart ”对象的创建向导的示例操作：

```xml
 <objecttype name="myPart" class="ext.part.MyPart"
         <action name="create" uicomponent="CREATE_MYPART" ajax="row">
   <command class="com.ptc.windchill.enterprise.part.forms.
    CreateMyPartFormProcessor" 
onClick="validateCreateLocation(event)" windowType="popup"/>
      </action>
```

#### 创建单个对象的构建向导和编辑单个对象的构建向导:

您需要开发一个向导来捕获用户输入，然后从该输入中在数据库中创建一个Windchill业务对象。

#### windchill 的jsp差不多要引用的部份：

```jsp
<%@ taglib prefix="jca" uri="http://www.ptc.com/windchill/taglib/components"%>
<%@ include file="/netmarkets/jsp/util/begin.jspf"%>
<%@ taglib prefix="mvc" uri="http://www.ptc.com/windchill/taglib/jcaMvc"%>
<%@ taglib prefix="wrap" uri="http://www.ptc.com/windchill/taglib/wrappers"%>
<%@ taglib tagdir="/WEB-INF/tags" prefix="wctags"%>
<%@ page import="java.util.ArrayList"%>
<%@ page import="java.util.ResourceBundle"%>

<%@ taglib prefix="jca" uri="http://www.ptc.com/windchill/taglib/components"%>
<%@ taglib prefix="mvc" uri="http://www.ptc.com/windchill/taglib/jcaMvc"%>
<%@ taglib prefix="wrap" uri="http://www.ptc.com/windchill/taglib/wrappers"%>
```

```
在windchill的shell中执行以下命令： 
Windchill com.ptc.netmarkets.util.misc.NmActionServiceHelper 
这个命令只会reload 配置文件，并不会重启mehtod server 
```

