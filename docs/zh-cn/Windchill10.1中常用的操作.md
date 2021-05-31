# Windchill10中常用的操作

 数据库扩列

```shell
AddColumns AddColumns wt.part.WTPart String=10 
AddColumns -f com.lczTraining.model.versionModel.PcnManager Boolean=5
AddColumns -u
```

一，操作对象。

1，  根据零件名称/编号得到该零件

```java
wt.clients.prodmgmt.WTPartHelper.findPartByName(name)；
wt.clients.prodmgmt.WTPartHelper.findPartByNumber(number)；
```

2，  根据WTPart得到WTPartMaster

```java
WTPartMasterwtmaster=(WTPartMster)part.getMaster();
```

3，  获取codebase下配置文件wt.properties属性信息

```java
WTPropertieswtproperties = WTProperties.getLocalProperties();
String wthome =wtproperties.getProperty("wt.home", ""); //codebase的文件夹路径
```

4，  获取Part被借用的所有父部件

```java
QueryResult qr=wt.part.WTPartHelper.service.getUsedByWTParts(WTPartMster wtMaster);
```

5，  根据OID获取WTPart

```java
Stringoid = "OR:wt.part.WTPart:3089789";
ReferenceFactoryrefefence = new ReferenceFactory();
Persistablepersistable = refefence.getReference(oid).getObject();
WTpart part = null;
 if(persistable instanceof WTPart){
    part = (WTPart)persistable;
};
```

6，  根据对象查询OID

```java
WTPart  part = new WTPart();
ReferenceFactoryrefefence = new ReferenceFactory();
String partOid = refefence.getReferenceString(part);
```

7，  得到零件最新版本

```java
WTPart wtpart= (WTPart) VersionControlHelper.getLatestIteration(part);
```

8，  查询用户某段时间内创建的零件

```java
QuerySpecqs = new QuerySpec(WTPart.class);
qs.appendSearchCondition(newSearchCondition(WTPart.class,WTPart.CREATE_TIMESTAMP,true,newAttributeRange(begintime,endtime)));//删选条件时间范围内
qs.appendAnd();//一定要加上 不然下一个条件不能删选
qs.appendSearchCondition(newSearchCondition(WTPart.class,"iterationInfo.creator.key",SearchCondition.EQUAL,PersistenceHelper.getObjectIdentifier(name)));//删选条件
QueryResult qr= PersistenceHelper.manager.find(qs);
```

9，  获取对象的IBA属性

```java
 Map<String, String> map=null;
      IBAHolder ibaHolder=null;
      DefaultAttributeContainer dac=null;
      ibaHolder=IBAValueHelper.service.refreshAttributeContainerWithoutConstraints(part);
      dac=(DefaultAttributeContainer)ibaHolder.getAttributeContainer();
      AbstractValueViewvalueView[]=dac.getAttributeValues();
      if(valueView.length!=0){       
         map=newHashMap<String, String>();
         for (int i = 0; i< valueView.length; i++) {
              AbstractValueViewavv = valueView[i];
              StringIBAName=avv.getDefinition().getName();
              StringIBAValue=((StringValueDefaultView)avv).getValue();
              map.put(IBAName,IBAValue);
             }
       }
```

10,修改对象的名称

```java
if(!part.getName().equals(propertyMap.get("TName"))) {//修改part的名称。
                        //propertyMap存储的一般属性的集合
	WTPartHelper.service.changeWTPartMasterIdentity((WTPartMaster)
	part.getMaster(),(String)propertyMap.get("TName"),part.getNumber(),part.getOrganization());
}
```

11,获取对象的工作流实例

```java
private static WfProcess getRelatedProcess(Persistable p) throws WTException{
      WfProcess ret = null;
      QueryResult qrProcs=WfEngineHelper.service.getAssociatedProcesses(p,WfState.OPEN_RUNNING, null);
      // 按时间排序,取最新一个
      if(qrProcs.hasMoreElements()) {
     	while (qrProcs.hasMoreElements()){
        	ret = (WfProcess) qrProcs.nextElement();
     	 }
     }
    return ret;
}
```

12，重新启动一个对象的工作流

```java
 /**
 * 启动流程.
 *
 * @param newPBO
 * 流程对象
 * @param wfTemplateName
 * 流程模板名称
 * @param wfProcessName
 * 流程实例名称
 * @return
 * @throws WTException
 * @throws InvocationTargetException
 * @throws RemoteException
 */

public static WfProcessstartWorkFlow(WTObject newPBO, String wfTemplateName, String wfProcessName) throws WTException{
    if (!RemoteMethodServer.ServerFlag)
    {
        StringsMethod = "startWorkFlow";
        StringsClass = WorkflowHelper.class.getName();
        Class[]types = newClass[]
        {WTObject.class,String.class,String.class};
        Object[]values = newObject[]
        {newPBO, wfTemplateName, wfProcessName };
        try
        {
            return (WfProcess)RemoteMethodServer.getDefault().invoke(sMethod, sClass, null, types, values);
        }
        catch (Exception e)
        {
            e.printStackTrace();
            throw new WTException(e);
        }
    }
    long WORKFLOW_PRIORITY = 0;
    WfProcessret = null;
    boolean flag =SessionServerHelper.manager.setAccessEnforced(false);
    try
    {
        WTPropertieswtproperties = WTProperties.getLocalProperties();
        WORKFLOW_PRIORITY= 		Long.parseLong(wtproperties.getProperty("wt.lifecycle.defaultWfProcessPriority","0"));
        WTContainerRefcontainerRef = null;
        if (newPBO instanceof WTContained)
        {
            containerRef= ((WTContained) newPBO).getContainerReference();
        } 
        WfProcessDefinitionprocessDefinition = 	     WfDefinerHelper.service.getProcessDefinition(wfTemplateName,containerRef);
        ret= WfEngineHelper.service.createProcess(processDefinition, null, containerRef);
        ret.setName(wfProcessName);
        // ret.setPriority(WORKFLOW_PRIORITY);
        ProcessDataprocessData = ret.getContext();
       processData.setValue(WfDefinerHelper.PRIMARY_BUSINESS_OBJECT,newPBO);
       // LifeCycleHelper.service.setLifeCycleState(newPBO,
        // State.toState(CmConstants.s));
        // ret = ret.start(processData, true,ret.getContainerReference());
        ret= WfEngineHelper.service.startProcessImmediate(ret, processData, WORKFLOW_PRIORITY);
    }
    catch (Throwable err)
    {
        err.printStackTrace();
        throw new WTException(err);
    }
    finally
    {
        SessionServerHelper.manager.setAccessEnforced(flag);
    }
    return ret;
}
```

13，获取一个零件的BOM结构

```java
  private  void querySub(WTPartpart,int i) throws WTException{
        QueryResultqr = WTPartHelper.service.getUsesWTPartMasters(part);//获得所有连接的WTPartUsageLink
        while(qr.hasMoreElements()) {
            Objectobject = qr.nextElement();//获得其中一个WTPartUsageLink
            if(object instanceof WTPartUsageLink) {
               WTPartUsageLinkusagelink = (WTPartUsageLink) object;
               WTPartMasterchildMaster = (WTPartMaster) usagelink.getUses();//获得连接在该WTPartUsageLink下的WTPartMaster
                                 try{
                                     QueryResultitqr = VersionControlHelper.service.allVersionsOf(childMaster);
                                           //从给定的master查找所有版本。其结果是从最近一次到第一个创建的一组有序的版本
                                          WTPartchildPart = null;
                                           if(itqr.size()!=0){
                                                    childPart= (WTPart) itqr.nextElement();
                                           }
                                 //      if(childPart!=null){
                                            // printExcel.Excle(childPart, i);
                                 //      }
            if(WTPartHelper.service.getUsesWTPartMasters(childPart).size()!=0){
                querySub(childPart,i+1);
            }
                                 }catch (PersistenceException e) {
                                    e.printStackTrace();
                                 }catch (WTException e) {
                                 e.printStackTrace();
                                 }catch (IOException e) {
                                           e.printStackTrace();
                                 }
                        }
               }
     }
```

二  Windchill中简单的客制化

1，  配置系统中不存在的生命周期状态名称和角色

1，  找到src/wt/project或者src/wt/lifecycle目录。

2，  复制RolRB.rbinfo或者StateRB.Rbinfo至wtCustom/wt/project或lifecycle目录。

3，  修改复制过来的文件。

4，  使用Resourcesbuild+目录名+文件名（不要后缀）+true。

5，  使用 ant –f  codebase/MakeJar.xml。

6，  重启服务，完成添加。

 

2，  添加操作栏中的操作

1，  在你指定的页面的地址栏中输入&jcaDebug=1,点击操作的那个七星瓢虫。

2，  找到配置该操作的actionModels.xml,并且找到配置该操作栏的models,将该models全部复制过来，复制到custom-actionModels.xml中，配置你自己的action。

3，  值得注意的是这里需要配置两个资源文件（一个本地的和一个常规的）格式如下：本地的（这里只演示中文状态下，其它状态类似）

```java
importwt.util.resource.RBEntry;

importwt.util.resource.RBUUID;

importwt.util.resource.WTListResourceBundle;

@RBUUID("ext.corilead.resource.StartActionLableResourceRB")

public class StartActionLableResourceRB_zh_CN extends WTListResourceBundle {

@RBEntry("你配置的操作名称")
public static final String STARTACTION_DEMO_TITLE= "StartAction.demo.title";

@RBEntry("你配置的操作名称")
public static final String STARTACTION_DEMO_DESCRIPTION= "StartAction.demo.description";

@RBEntry("你配置的操作名称")
public static final String STARTACTION_DEMO_TOOLTIP= "StartAction.demo.tooltip";

}
```

常规的：

```java
import wt.util.resource.RBEntry;

import wt.util.resource.RBUUID;

import wt.util.resource.WTListResourceBundle;

@RBUUID("ext.corilead.resource.StartActionLableResourceRB")

public classStartActionLableResourceRB extends WTListResourceBundle {
   
@RBEntry("这里是你配置操作的英文状态下的名称")
public static final String STARTACTION_DEMO_TITLE="StartAction.demo.title";

@RBEntry("这里是你配置操作的英文状态下的名称")
public static final String STARTACTION_DEMO_DESCRIPTION="StartAction.demo.description";

@RBEntry("这里是你配置操作的英文状态下的名称")
public static final String STARTACTION_DEMO_TOOLTIP="StartAction.demo.tooltip";

}
```

 

注：注意一些细节问题，把这两个类的class文件拷贝到codebase目录下

解析：public static final String STARTACTION_DEMO_TITLE ="StartAction.demo.title";

StartAction:你在custom-actionModels.xml中配置action的中的type属性值

demo:你在custom-actionModels.xml中配置action的中的name属性值.

4， 接下来配置custom-action.xml文件，下面是custom-actionModels.xml中的配置

```xml
  <action name="demo" type="StartAction" />
```


下面是custom-actions.xml中的配置

```xml
<objecttype name="StartAction" class="wt.part.WTPart" resourceBundle="ext.corilead.resource.StartActionLableResourceRB">

<action name="demo">
 <includeFilter name="SetJsfaWorkFlow"/>
<command windowType="popup" url="/netmarkets/jsp/ext/shlcm/GPDeng.jsp"/>
</action>
</objecttype>
```

   

解析：对比两个文件的配置：

1，  对于custom-actions.xml文件中的配置最外层的objecttype标签 name属性值与custom-actionModels.xml文件中配置action的type相对应，class的属性值与你配置在哪个对象下的class名称（比如：”wt.part.WTPart”）;resourceBudle的属性值便是你自己编写的哪个常规的资源文件的名称（包名+类名）。

2，  custom-actions.xml文件中的配置内层的action标签 name属性值与你在custom-actionModels.xml文件中配置action的name相对应。

3，  custom-actions.xml文件中的配置内层的action标签中的command标签就是你要执行的操作，这里配置的是打开一个jsp页面，其中windowType就是打开方式他有”popup”和”page”两种，第一种就是打开一个新的窗口，第二种就是在本页面上打开。url显然就是你的jsp文件的路径。

4，  custom-actions.xml文件中的配置内层的action标签中的includeFilter标签顾名思义就是一些过滤的操作，就比如一个编辑一个合同号，就得在该对象的状态是已发布才可以进行编辑，否则就不可以编辑，这时就要通过这个过滤来控制，一个action可以有多个过滤操作。（这部分会在接下来讲解，小伙子们不要急）。

5，  刚刚开始的时候请把includeFilter标签去掉，先来测试看能不能打开你配置的jsp界面。
