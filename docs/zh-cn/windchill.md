# windchill 

## Windchill 知识


### 高级查询

#### 例子

```java
{
    new queryspec （）
	queryspec.appendclasslist（WTPart.class， true）
	queryspec.appendClasslist（WTDocument.class， true）
    
	PersistenceManagerSvr pms =（PersistenceManagersvr)
	InvocationHandlerEwd.getInterfaceObject（PersistenceManagersvr class);
	QueryResult qr= pms.query(queryspec)
}
```

Queryspec对象是用来指定查询表达式的

queryspec. appendclasslist()是用来指定From子句的，如果有多个这样的表达式而没有其他条件，

那么就是对相应的被查询对象求笛卡儿积.
```
笛卡尔积
设A,B为集合，用A中元素为第一元素，B中元素为第二元素构成有序对，所有这样的有序对组成的集合叫做A与B的笛卡尔积，记作AxB.
笛卡尔积的符号化为：
A×B={(x,y)|x∈A∧y∈B}
例如，A={a,b}, B={0,1,2}，则
A×B={(a, 0), (a, 1), (a, 2), (b, 0), (b, 1), (b, 2)}
B×A={(0, a), (0, b), (1, a), (1, b), (2, a), (2, b)}
```

例如上面的例子就会形成下面的From子句:

```sql
From WTPART，WTDOCUMENT
```

指定From子句有两种方式:

```java
queryspec. appendClasslist（WTpart. class， false）;
```
指定具体的对象
```java
qs.appendFrom (new ExternalTableExpression("GZMTRECr");
```

指定具体的表

在 appendClasslist（）中有两个参数，第一个参数是用来指定被查询对象，第二个参数用来指定查询的返回形式:true以对象的形式返回，fa1se以属性的方式返回

如果是用 appendClasslist（）来指定查询对象，那么查询结果 QueryResult qr对象

qr. nextElement（）得到的是 Object数组，具体的结果保存在这个数组中

### WindChill Debug

> &jcaDebug=1

### Windchill API

```apl
file:///E:/ptc/Windchill_10.2/Windchill/codebase/wt/clients/library/api/index.html
```

### 修改项目的.project

```xml
<buildSpec>
  <buildCommand>
   <name>org.eclipse.wst.common.project.facet.core.builder</name>
   <arguments>
   </arguments>
  </buildCommand>
  <buildCommand>
   <name>org.eclipse.jdt.core.javabuilder</name>
   <arguments>
   </arguments>
  </buildCommand>
  <buildCommand>
   <name>org.eclipse.wst.validation.validationbuilder</name>
   <arguments>
   </arguments>
  </buildCommand>
 </buildSpec>
 <natures>
  <nature>org.eclipse.jdt.core.javanature</nature>
  <nature>org.eclipse.wst.common.project.facet.core.nature</nature>
 </natures>
```

### Debug配置

```
xconfmanager -s "wt.manager.cmd.MethodServer.debug.args=-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=0.0.0.0:{19}" -p -t codebase/wt.propertiesxconfmanager -s "wt.manager.cmd.MethodServer.param.19=8000" -p -t codebase/wt.propertiesxconfmanager -s "wt.manager.cmd.BackgroundMethodServer1.param.19=8001" -p -t codebase/wt.properties
```



```xml
<Property name="wt.manager.cmd.MethodServer.java.extra.args" overridable="true"
targetFile="codebase/wt.properties"
value="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=9999"/>

<Property name="wt.manager.cmd.BackgroundMethodServer.java.extra.args" overridable="true"
targetFile="codebase/wt.properties"
value="-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=9989"/>
```

### 使datautility、filter生效的命令：

```shell
   <AddToProperty name="com.ptc.netmarkets.util.misc.defaultActionmodels" value="config/actions/Petactionmodels.xml"/>
   
 xconfmanager -pF

一：xconfmanager -p -i /codebase/com/training/config/training.xconf
xconfmanager -p -i /codebase/config/mvc/
二：  
```

### 添加表格：

### 图标

```apl
图标
	部件: Windchill/loadFiles/com/kwafoo/type/WTPart/wtcore/images/part.gif
	虚拟件：Windchill/codebase/images/phantom_part.png
	成品：Windchill/codebase/images/prod.gif
	文档: Windchill/codebase/netmarkets/images/doc_document.gif
	文件夹: Windchill/codebase/netmarkets/images/folder.gif
	产品：Windchill/codebase/com/ptc/core/ui/images/iconProductContext.gif
	存储库: Windchill/codebase/netmarkets/images/libcontext_tem.gif
	组：Windchill/codebase/netmarkets/images/group.gif
	组织：Windchill/codebase/netmarkets/images/organization_context.gif
	部件全局替换: Windchill/loadFiles/com/kwafoo/type/WTPart/wtcore/images/alternate.gif
	部件特定替换: Windchill/loadFiles/com/kwafoo/type/WTPart/wtcore/images/substitute.gif
	更改通告: Windchill/codebase/netmarkets/images/chgnotice.gif
	更改记录: Windchill/codebase/netmarkets/images/link_object.gif
	更改请求: Windchill/codebase/netmarkets/images/chgreqst.gif
	更改任务: Windchill/codebase/netmarkets/images/change_task.gif
	升级请求: Windchill/codebase/netmarkets/images/defer_default.gif
	新建部件：Windchill/codebase/netmarkets/images/newpart.gif
	新建文档：Windchill/codebase/netmarkets/images/newdoc.gif
	新建文件夹：Windchill/codebase/netmarkets/images/newfoldertl.gif
	复制选定对象：Windchill/codebase/netmarkets/images/copy.gif
	添加至工作区：Windchill/codebase/netmarkets/images/../../com/ptc/windchill/uwgm/cadx/images/add_workspace.gif
	添加：Windchill/codebase/netmarkets/images/add16x16.gif
	移除：Windchill/codebase/netmarkets/images/remove16x16.gif
	编辑多个对象：Windchill/codebase/netmarkets/images/object_multiple_edit.png
	检入: Windchill/codebase/netmarkets/images/checkin.gif
	检出: Windchill/codebase/netmarkets/images/checkout.gif
	检出并编辑: Windchill/codebase/netmarkets/images/multi_update.gif
	撤销检出: Windchill/codebase/netmarkets/images/uncheckout.gif
	编辑: Windchill/codebase/netmarkets/images/multi_update.gif
	编辑公用属性: Windchill/codebase/netmarkets/images/attribute_edit.gif
	修订: Windchill/codebase/netmarkets/images/revise.gif
	复制: Windchill/codebase/netmarkets/images/copy.gif
	另存为: Windchill/codebase/netmarkets/images/saveas.gif
	以现有的取代: Windchill/codebase/netmarkets/images/supersede.png
	以新的取代: Windchill/codebase/netmarkets/images/supersede_wnew.png
	重命名: Windchill/codebase/netmarkets/images/rename.gif
	移动: Windchill/codebase/netmarkets/images/move_cmd.gif
	设置状态: Windchill/codebase/netmarkets/images/../../com/ptc/windchill/cadx/images/setstate_cmd.gif
	导出: Windchill/codebase/netmarkets/images/export.gif
	导出可导出的电子表格: Windchill/codebase/netmarkets/images/export_importable_xls.png
	删除: Windchill/codebase/netmarkets/images/delete.gif
	重新分配生命周期: Windchill/codebase/netmarkets/images/lifecycle_reassign.gif
	预定: Windchill/codebase/netmarkets/images/iconCreateSubscription.gif
	比较信息: Windchill/codebase/netmarkets/images/information_compare.gif
	与部件结构比较: Windchill/codebase/netmarkets/images/dual_structure_compare.gif
	与CAD结构比较: Windchill/codebase/netmarkets/images/cad_structure_compare.gif
	新建更改请求: Windchill/codebase/netmarkets/images/chgreqst_create.gif
	新建问题报告: Windchill/codebase/netmarkets/images/probrpt_create.gif
	新建超差: Windchill/codebase/netmarkets/images/variance_create.gif
	新建升级请求: Windchill/codebase/netmarkets/images/promotereqst_create.gif
	新建部件配置: Windchill/codebase/netmarkets/images/part_config_create.gif
	新建试图版本: Windchill/codebase/netmarkets/images/version_view_create.gif
	新的一次性版本: Windchill/codebase/netmarkets/images/one_off_view.gif
	新建配置上下文: Windchill/codebase/netmarkets/images/configuration_context_create.png
	添加至基线: Windchill/codebase/netmarkets/images/baseline_add.gif
	添加到受管理的集合: Windchill/codebase/netmarkets/images/managed_collection_add.gif
	添加至我的笔记本: Windchill/codebase/netmarkets/images/newobj.gif
	添加至包: Windchill/codebase/netmarkets/images/package_add.gif
	添加至项目: Windchill/codebase/netmarkets/images/proj_addto.gif

```

练习command和对windchill对象、API的熟悉
custom-actions里面的method对应着类里面的方法名
这种command的写法，不需要继承和实现什么类，只需要xml的method名和类的方法名对应就行了
场景：比如昨天要你们加的表格的按钮
比如加的是删除，那么选中一条数据，将wtpart进行删除，然后刷新表格
具体怎么获取表格中选中的数据，配好调试的可以用调试自己看NmCommandBean commandBean里面的方法，看哪个能得到你想要的数据
删除的API：PersistenceHelper.manager.delete

```java
/**
  * 新建part
  *
  * @param partbean
  * @return
  * @throws WTException
  * @throws WTPropertyVetoException
  */
 public static WTPart createPart(WTPartBean partbean) throws WTException, WTPropertyVetoException {
  WTPart part = WTPart.newWTPart();
  part.setNumber(partbean.getNumber());
  part.setName(partbean.getName());
  part.setTypeDefinitionReference(TypedUtility.getTypeDefinitionReference(partbean.getWTType()));
  part.setContainer(partbean.getContainer());
  ViewReference viewRef = null;
  View view = ViewHelper.service.getView(partbean.getViewName());
  if (view == null) {
   view = ViewHelper.service.getView(ViewConstant.MANUFACTURE);
  }
  viewRef = ViewReference.newViewReference(view);
  part.setView(viewRef);
  // part.setDefaultUnit(QuantityUnit.EA);
  if (StringUtil.isNotNullOrBlank(partbean.getDefaultUnit())) {
   part.setDefaultUnit(QuantityUnit.toQuantityUnit(partbean.getDefaultUnit()));
  } else {
   part.setDefaultUnit(QuantityUnit.EA);
  }
  part.setEndItem(partbean.isEndItem());
  part.setDefaultTraceCode(TraceCode.UNTRACED);
  part.setPhantom(partbean.isPhantom());
  FolderHelper.assignLocation((FolderEntry) part, partbean.getFolder());
  setSecurityLabelVal2(part, partbean.getSECRET_GRADE());
  part.setOwnership(Ownership.newOwnership(SessionHelper.getPrincipal()));
  part = (WTPart) PersistenceHelper.manager.save(part);

  return part;
 }
 
 
 
 创建文档：
 
 WTDocument document = WTDocument.newWTDocument();
     TypeDefinitionReference typeDefinitionRef = null;
     if (type == null || "".equals(type)) {
      typeDefinitionRef = TypedUtility.getTypeDefinitionReference("wt.doc.WTDocument");
     } else {
      typeDefinitionRef = TypedUtility.getTypeDefinitionReference(type);
     }
     try {
      document.setTypeDefinitionReference(typeDefinitionRef);
      document.setName(docName);
      // document.setNumber(docName.toUpperCase());
     } catch (WTPropertyVetoException e) {
      logger.error(CLASSNAME + "." + "createDoc" + ":" + e);
      e.printStackTrace();
     }
     WTContainer container = WCUtil.getWtContainerByName(containername);
     Folder docFold = null;
     if (folder == null || "".equals(folder)) {
      docFold = FolderHelper.service.getFolder("Default",
        WTContainerRef.newWTContainerRef(container));
     } else {
      WTContainerRef containerRef = WTContainerRef.newWTContainerRef(container);
      docFold = WTContainerUtil.getFolderOrCreateFolder(folder, containerRef);
      /*docFold = FolderHelper.service.getFolder("Default/" + folder,
        WTContainerRef.newWTContainerRef(container));*/
     }
     FolderHelper.assignLocation((FolderEntry) document, docFold);
     PersistenceHelper.manager.save(document);
	 
	 
	 
	 创建文档与部件的说明关系:
	 WTPartDescribeLink describelink = WTPartDescribeLink.newWTPartDescribeLink(part, wtdoc);
     PersistenceServerHelper.manager.insert(describelink);
	 
	 
	 创建文档与部件的参考关系:
	 WTPartReferenceLink wtpartreferencelink1 = WTPartReferenceLink
     .newWTPartReferenceLink(wtpart, (WTDocumentMaster) wtdocument.getMaster());
   PersistenceServerHelper.manager.insert(wtpartreferencelink1);
   
   
   
   
   /**
  * 构建部件结构关系
  *
  * @param parentPart
  * @param wtPart
  * @throws WTException
  * @throws JSONException
  * @throws ParseException
  * @throws RemoteException
  * @throws WTPropertyVetoException
  */
 public void createWtPartUsageLink(WTPart parentPart, List<WTPartMaster> childPartMasters) throws WTException {
  List<WTPartUsageLink> usageLinkList = new ArrayList<WTPartUsageLink>();
  for (WTPartMaster childPartMaster : childPartMasters) {
   WTPartUsageLink targetLink = WTPartUsageLink.newWTPartUsageLink(parentPart, childPartMaster);
   Quantity qt = new Quantity();
   Double quantityDouble = Double.valueOf("1");
   qt.setAmount(quantityDouble);
   qt.setUnit(QuantityUnit.EA);
   targetLink.setQuantity(qt);
   usageLinkList.add(targetLink);
  }
  PersistenceHelper.manager.save(new WTArrayList(usageLinkList));
 }
```



### 创建文件实例

```java
創建文件實例
WTDocument newDoc = null;
newDoc = WTDocument.newWTDocument();
newDoc.setName("创建主文档22");//文件的名称必设项
newDoc.setNumber("888822");//文件编号必设项
newDoc.setDepartment(DepartmentList.toDepartmentList("ENG"));//设置departmentlist即文件所属的部门，必设项
DocumentType doctype = DocumentType.toDocumentType(“$$Document”);//此参数与属性文件对应
//必设项，文件类型为一般文件
newDoc.setDocType(doctype);
setType(newDoc,"wt.doc.WTDocument|com.elead.Drawing");
newDoc=(WTDocument)PersistenceHelper.manager.save(newDoc);//保存文件PersistenceHelper.manager是对数据库的增删查改

```

### 创建主文档

```java
以上只是对文件的基本信息进行了设置也就相当在创建文件的模板中只
是填写了基本信息没有上传文档时生成的不含主文档的文件。以下是创
建主文档：
 ContentHolder contentHolder//获取文本内容的对象
=ContentHelper.service.getContents(newDoc);//获得文件的ContentHolder
ApplicationData appdata = ApplicationData.newApplicationData(contentHolder);
appdata.setRole(ContentRoleType.PRIMARY);//设置主要还是次要文件

通过ApplicationData 去设置主文档的名称、大小、以及文件上传路径等.

ContentServerHelper.service.updateContent
(contentHolder,appdata,fileStream);//主文档创建成功
参数fileStream是要上传的文件输入流。
红色的3个类，是操作文件的主要API

```

### 定制化类

```java
1.如何在系统中查找对象对应的类

打开站点中的"类型和属性管理器",选择部件,这个部件就对应了系统中的一个对象,wt.part.WTPart就是系统中部件对应的类,wt.part是包名,WTPart就是类的名称

2.系统类的设计

系统中的服务类设计为xxHelper.java文件和xxService.java是组合关系，系统中的所有业务对象对应的方法在对应的service中，通过xxHelper.serivce.方法 .

部件操作：WTPartHelper.service.方法

文档操作：WTDocumentHelper.service.方法

EC操作：ChangeHelper2.service.方法

文件夹的操作：FolderHelper.service.方法

生命周期的操作：LifeCycleHelper.manager.方法

推进流程对象的操作：MaturityHelper.service.方法

通知机制对象的操作：NotificationHelper.service.方法

位号的操作：OccurrenceHelper.service.方法

参与者的操作：OrganizationServicesHelper.manager.方法

版本的操作：VersionControlHelper.service.方法

3.如何通过类在数据库中查找相关的表

在类型管理器中定义的那些对象在数据库中都有对应的表存在.如部件对象的类名是WTPart.

如要获取一个部件的生命周期状态,数据库中对应字段是statestate这个字段,那么在代码中通过part.getLifeCycleState().getDisplay()这个函数来获取.

4.类型

类型的值被定义在wt.doc.DocumentTypeRB中

Department

在Windchill中允许用户选择区域，负责文件的管理,值都被定义在wt.doc.DepartmentListRB中

文件与文件之间的关系:

文件结构：一个文件可以使用很多其它子文件，其下层子文件也可以被其它文件使用。(使用与被使用的关系)

文件参考：一个文件涉及的信息存在于其他文件中。(参考与被参考的关系)

部件wt.part.WTPart

部件参考wt.part.WTPartReferenceLink

部件主数据wt.part.WTPartMaster

文档wt.doc.WTDocument

文档使用wt.doc.WTDocumentUsageLink

文档主数据wt.doc.WTDocumentMaster

项目wt.projmgmt.admin.Project2

项目活动(典型)wt.projmgmt.execution.ProjectActivity

用户wt.org.WTUser

产品wt.pdmlink.PDMLinkProduct

存储库wt.inf.library.WTLibrary

活动com.ptc.projectmanagement.plan.PlanActivity

计划com.ptc.projectmanagement.plan.Plan

交付com.ptc.windchill.wp.delivery.DeliveryRecord

角色承担者wt.org.WTRolePrincipal

进程com.ptc.windchill.dpimpl.core.WTMasterProcess

可交付结果com.ptc.projectmanagement.deliverable.PlanDeliverable

组wt.org.WTGroup

流程活动节点对象WfAssignedActivity

流程对象wfProcess

流程签审 WfVotingEventAudit

	问题报告（ProblemReport）：WTChangeIssue;
	更改请求（ChangeRequest）：WTChangeRequest2
	更改通告（ChangeNotice）：WTChangeOrder2
	实施计划（ChangeActivity）：WTChangeActivity2
```

