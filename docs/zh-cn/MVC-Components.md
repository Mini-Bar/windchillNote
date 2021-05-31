## MVC Components

包括：**MVC,Data Sources,JCA Components**

#### MVC in Windchill

WindChill 10.0 使用Spring MVC Framework. 因为一个servlet更加容易管理，所以使用前端控制器，DispatcherServlet 。

#### Request Handling 

请求处理：servlet在<Windchill>\codebase\WEB-INF\web.xml中定义。

下面的请求被映射到DispatcherServlet。基本就是将所有的请求映射。

```xml
<servlet-mapping> 
    <servlet-name>MVCDispatcher</servlet-name>
    <url-pattern>/servlet/WizardServlet/*</url-pattern>
    <url-pattern>/servlet/ActionsMenu/*</url-pattern>
    <url-pattern>/servlet/RecentList/*</url-pattern>
    <url-pattern>/servlet/Navigation/*</url-pattern>
    <url-pattern>/servlet/SuggestServlet/*</url-pattern>
    <url-pattern>/servlet/TypeBasedIncludeServlet/*</url-pattern>
    <url-pattern>/servlet/UIValidationAJAXServlet/*</url-pattern>
    <url-pattern>/ptc1/*</url-pattern>
    <url-pattern>/app/*</url-pattern>
    <url-pattern>/gwt/*</url-pattern><servlet-mapping>
</servlet-mapping>

<servlet>
    <description>MVC Dispatcher Servlet for JCA</description>
    <servlet-name>MVCDispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
```

####       应用上下文

这些文件用于为应用程序提供配置信息，位于<Windchill>\codebase\WEB-INF\MVCDispatcher-servlet.xml中 。  

系统会导入所有位于<Windchill>\codebase\config\mvc\文件夹下的Spring配置xml文件。任何**自定义配置**都应该放在<Windchill>\codebase\config\mvc\custom.xml中。                                 

通过加载<Windchill>\codebase\config\mvc的属性文件中指定的映射来定义**处理程序映射**。任何自定义处理程序的映射都应该放在<Windchill>\codebase\config\mvc\custom.properties中。

> web容器初始化，Spring加载上下文，所以需要重启服务器。

##### 在windchill中，我们使用两个处理程序映射

<font color='red'>DefaultAnnotationHandlerMapping</font>：它基于HTTP路径在类型或方法级别上通过RequestMapping注释表示的映射处理程序。

<font color='red'>SimpleUrlHandlerMapping </font>：从url映射到请求处理程序bean。

一旦找到处理程序，它将从配置的适配器中找到合适的处理程序适配器。在Windchill中，我们使用<u>三种类型的处理程序适配器</u>。

<font color='red'>SimpleControllerHandlerAdapter</font>：一个适配器来使用普通控制器

<font color='red'>AnnotationMethodHandlerAdapter </font>：它基于HTTP路径、HTTP方法和通过RequestMapping注释表示的请求参数来映射处理程序方法。

<font color='red'>GwtHandlerAdapter</font>：处理GWT客户机请求的适配器(处理程序是RemoteServiceServlet)

<font color='red'>ComponentParamsFactory </font>：从构建器中提供的请求和响应创建ComponentParams

![MVC_Overview04](https://gitee.com/cmlovecat/typorapic/raw/master/pics/windchill-mvc.png)

构建器提供了ComponentConfig和ComponentData，模型、ComponentDefinition就是从这两个组件中生成的。ModelAndView用ComponentDefinition填充，视图信息来自ComponentConfig或提供的defaultMapping。

绿色的构件是Windchill客户端架构(例如JCA)特定的构件。黑色为组件所有者提供的组件。

### MVC Components

继承/实现ComponentConfig接口的组件是MVC组件。

- TableConfig
- AttributesTableConfig
- TreeConfig
- AttributePanelConfig
- InfoConfig
- ColumnConfig
- PropertyConfig
- PropertyPanelConfig

### MVC Builders

有两种类型的构建器。提供ComponentConfig的叫做<font color='red'>ComponentConfigBuilder</font>，提供ComponentData的叫做<font color='red'>ComponentDataBuilder</font>。

ComponentConfigBuilder : 实现com.ptc.mvc.components.ComponentConfigBuilder

ComponentDataBuilder ：实现以下接口中的任何一个

- com.ptc.mvc.components.ComponentDataBuilder
- com.ptc.mvc.components.ComponentDataBuilderAsync
- com.ptc.mvc.components.TreeDataBuilderAsync

#### 指定componentid

通常，构建器映射到的组件ID是使用构建器类声明中的`@ComponentBuilder`注释声明的。

```java
@ComponentBuilder(value = “{<componetId1>, <componetId2>}”)
public class MyBuilder extends … {}
```

如果希望在单独的类中实现配置和数据构建器，那么必须为@ComponentBuilder注释提供一个附加的ComponentBuilderType参数。

对于配置构建器，如下所示:

```java
@ComponentBuilder(value=“<componetId>",type=ComponentBuilderType.CONFIG_ONLY)
public class MyConfigBuilder implements ComponentConfigBuilder …. {}
```

对于数据构建器，这看起来像:

```java
@ComponentBuilder(value=“<componetId>", type=ComponentBuilderType.DATA_ONLY)
public class MyDataBuilder implements ComponentDataBuilder… {}
```

注意，如果两个构建器具有相同的组件id，框架将抛出一个错误。Spring初始化将在MethodServer启动阶段失败。如果需要覆盖OOTB构建器，请使用OverrideComponentBuilder 注释。

```java
@ComponentBuilder(value="{compId1, compId2, compId3}")
public class OOTBBuilder1 extends …… {
}
@OverrideComponentBuilder
public class MyCustomBuilder1 extends …… {
}

MyCustomBuilder1将被映射到compId1、compId2和compId3
```

```java
@ComponentBuilder(value="{compIdA, compIdB, compIdC}")
public class OOTBBuilder2 extends ……{
}
@ComponentBuilder(value="{compIdA, compIdC}")
@OverrideComponentBuilder
public class MyCustomBuilder2 extends ……{
}
@ComponentBuilder(value="{compIdB}")
@OverrideComponentBuilder
public class MyCustomBuilder3 extends ……{
}

MyCustomBuilder2将被映射到compIdA、compIdC
MyCustomBuilder3将被映射到compIdB
```

**TypeBased**

要构建某些组件，您可能需要上下文对象的Windchill Type在查找适当的构建器时发挥作用。例如，您有一个信息页，您希望基于Windchill类型填充内容。对于这些场景，我们引入了一个可以在构建器中使用的注释“TypeBased”，将构建器附加到特定的Windchill Type。这适用于ComponentBuilder。

```java
@ComponentBuilder("compIdA")
@TypeBased(value="{WTPart, WTDocument}")
public class OOTBBuilder1 extends ……{
}
 @ComponentBuilder("compIdA")
@TypeBased(“myPart”)
public class OOTBBuilder2 extends ……{
}
```

- 如果上下文对象的Windchill类型是WTPart并且componentId = " compIdA "，那么该组件的最佳匹配生成器是OOTBBuilder1
- 如果上下文对象的Windchill类型是WTPart(myPart)的子类型并且componentId = " compIdA "，那么该组件的最佳匹配生成器是OOTBBuilder2
- 如果上下文对象的Windchill类型是WTDocument(myDoc)的子类型并且componentId = " compIdA "，那么该组件的最佳匹配生成器是OOTBBuilder1

在解析找到合适的构建器时，上下文对象的Windchill类型层次结构被尊重。属性值可以接受

- 内部Windchill类型名称

- 如果表示具有所涉及的交换容器的域名，则可以使用${internet_domain_name}作为其表示。

#### 注册Builders

创建构建器之后，您需要让Spring基础设施知道它。

注册可以通过<font color='red'>显式配置</font>或<font color='red'>自动扫描</font>完成。自动扫描是典型用例的首选方法。

1. **自动扫描**:您可以配置为自动拾取某个包层次结构中的所有构建器。

   ```xml
   To do this, add the <mvc:builder-scan/> configuration element to <Windchill>\codebase\config\mvc\custom.xml. 
       
   <beans 
          xmlns="xmlns:mvc="http://www.ptc.com/schema/mvc"         
          xsi:schemaLocation="http://www.ptc.com/schema/mvc 
   	   http://www.ptc.com/schema/mvc/mvc-10.0.xsd">
      <mvc:builder-scan base-package="com.ptc.windchill.enterprise.preference.mvc.builders"/>
   </beans>
   ```

   构建器扫描实现扫描整个类路径，因此如果使用扫描，应该注意声明的包名称。(例如不要扫描com.ptc.*)。此外，这意味着您不感兴趣但在类路径中且匹配包层次结构的类也将被扫描。从性能的角度来看(MethodServer启动)，每次扫描都会增加时间。你被要求利用com.ptc.mvc.builders基础包提供的OOTB扫描。这意味着您编写的所有构建器都应该在com.ptc.mvc.builders包下。如果您没有使用OOTB扫描，经验法则是在包中有超过10个可用的构建器时使用该扫描。

2. **显式配置**:要做到这一点，只需在<Windchill>\codebase\config\mvc\custom.xml中添加一个用于构建器的bean声明。

   ```xml
    <bean class=" my.builder.class.name "/>
   ```

   建议您在声明bean时不要指定bean名称，并在构建器中使用@ComponentBuilder注释以便在Spring bean工厂中正确注册它。

![MVC_Overview05](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview05.png)

#### Creating Multiple Components 

将需要使用相同的构建器<u>构建多个组件</u>，并且可以有两个用例。

==用例1==

这些组件是由已经存在的组件/构建器组成的。例如，如果已经有一个id="test.ComponentA"和另一个id="test.ComponentB" ，我们想在一个页面中获得两个组件。

```java
@ComponentBuilder(value="test.multi.AB",type=ComponentBuilderType.CONFIG_ONLY)
public class MultiComponentBuilder extends AbstractComponentConfigBuilder{
   @Override
  public ComponentConfig buildComponentConfig(ComponentParams params) throws WTException {
      MultiComponentConfig configs = new MultiComponentConfig();
      configs.addNestedComponent("test.ComponentA");           
//you are specifying the componentId
//你正在指定componentId
      configs.addNestedComponent("test.ComponentB");
      configs.setView("/test/multi.jsp");
      return config ;
   } 
} 
```

1. ComponentDataBuilder不需要，因为这个multiComponent本身没有任何数据。
2. 您需要为这个ComponentConfig指定一个视图jsp，因为没有默认视图可以处理这个。
3. ~~当您使用addNestedComponent来提供componentId时，请确保componentId将由同一个控制器实例处理。换句话说，您只能将非基于类型或基于类型的组件与此方法结合在一起。~~

==用例2==

这里需要定义组件，并且它们是一起存在的。例如，您希望属性面板与表一起提供。

- 需要ComponentDataBuilder。
- 基础架构会为MultiComponentConfig中可用的每个ComponentConfig调用buildComponentData。它的调用顺序与它在MultiComponentConfig中的调用顺序相同。
- 您可以从提供componentId的ComponentParams中访问已经处理过的ComponentConfig的rawData(原始数据)
- 您需要为这个ComponentConfig指定一个视图jsp，因为没有默认视图可以处理这个。

```java
@ComponentBuilder("test.array.PQ")
public class ComponentArrayBuilder extends AbstractComponentBuilder { 

   @Override
   public ComponentConfig buildComponentConfig(ComponentParams params) throws WTException {
     
       MultiComponentConfig configs = new MultiComponentConfig(); 

      TableConfig table1 = createTable1(); //create a table with id = component1
      ...... 
      TableConfig table2 = createTable2();//create a table with id = component2
      ...... 
      configs.addComponent(table1);    //add the components
      configs.addComponent(table2); 

      configs.setView("/test/array.jsp");
      return configs ;
   } 

   @Override
   public Object buildComponentData(ComponentConfig config,ComponentParams params) throws WTException { 

      if ("component1".equals(config.getId())) {
         //return data for table with id= component1
      } else {
         //return data for table with id= component2
      }
   } 
}
```

#### 数据源

它是一个用于保存数据并将数据流传输给客户端的基础设施。它将支持更快的页面加载以及异步服务器处理结果的潜力。基本的策略是将大的结果集以块的形式返回给客户端，并附带一个ID，可以用来获取更多的块。从UI的角度来看，数据似乎流进了客户端。这种方法与DB分页不同，后者将大的结果集持久化到数据库中。使用datasource时，结果只在客户机以块的形式请求它时存在于内存中。此外，还支持服务器在单独的线程中向数据源添加块，因此可以请求数据源并派生一个线程来为其获取数据，而无需客户机等待。

![MVC_Overview07](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview07.png)

使用数据源的一些好处:

- 在不过度消耗客户机和服务器端资源的情况下显示数据。
- 允许用户在数据加载时与页面交互。
- Improve perceived performance.
- 在数据加载时提供消息传递和反馈。

#### 数据源模式

同步:如果你已经有了所有需要发送给客户端的数据，并且想把数据放在数据源中，这样它就可以以块的形式发送给客户端，那么这是很有用的。DataSource会话接受您想要发送给客户机的对象列表，并返回一个DataChunk对象，该对象包含要发送给客户机的第一块数据。如果List of数据可以放在一个数据块中，那么会话甚至不会创建DataSource，它只是返回数据块。否则，将使用数据创建DataSource，稍后可以轮询这些数据以获得其他块。数据源将被关闭，因此不能添加更多的数据。异步:在这种情况下，您还没有获得数据。您希望提交一个任务以在后台线程中获取数据，并在稍后将数据返回给客户机。我们利用了Java提供的Executor框架的功能。要创建一个异步数据源，数据源会话需要一个DataSourceTask，它将在未来的某个后台线程中运行。它返回一个DataSourceFuture对象，以便您能够访问DataSource信息。

##### 数据源与数据采集

<u>同步数据源与同步数据获取</u>:使用同步数据获取(所有数据来自一个块中的数据库)和流块到客户端。没有发送空白块，所以组件在收到第一个数据块之前不会被呈现。目前，我们不支持树组件。

![MVC_Overview08](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview08.png)

<u>异步数据源与同步数据采集</u>:使用同步数据采集(所有数据来自一个块中的数据库)和流块到客户端。发送到客户机的第一个块不包含表数据，但是允许在完整数据块可用之前呈现表。这使用户可以更快地控制表及其某些操作。然后数据块在处理时流进空白块后面。

![MVC_Overview09](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview09.png)

<u>异步数据源与异步数据采集</u>:使用异步数据采集(数据以块的形式从数据库中获得)和流块到客户端。发送到客户机的第一个块不包含表数据，但是允许在完整数据块可用之前呈现表。这使用户可以更快地控制表及其某些操作。然后数据块在处理时流进空白块后面。在这种情况下，我们有可能看到数据块在客户机中呈现，而数据仍然从数据库中获取。

![MVC_Overview10](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview10.png)

在树的情况下，加载行为发生了变化。风寒指数9.X，我们遵循深度优先加载的方法，即每个节点扩展到其叶节点，然后开始扩展其同级节点。在Windchill 10中，使用DataSource，我们移动了广度优先加载方法。节点在每个级别加载。

![MVC_Overview11](https://gitee.com/cmlovecat/typorapic/raw/master/pics/MVC_Overview11.png)

##### 监控数据源

可以使用DataSourceMonitor MBean来监视和配置各种属性。找到使用监视器：

- 从windchill shell，运行“jconsole”
- 选择MethodServerMain
- 选择MBeans tab
- MBean位于com.ptc>WebAppContexts>${WEBAPP_NAME}>Monitors>DataSourceMonitor

![image-20210512162848307](https://gitee.com/cmlovecat/typorapic/raw/master/pics/image-20210512162848307.png)

> 60000 毫秒 =1 分

##### 持久化更新的MBean配置值

查看文档操作