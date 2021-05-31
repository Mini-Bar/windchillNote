### 创建子类型

**1**.创建子类型的生命周期模板-----（设计中、修改中、审阅中、下发中，，取消，发布）
	设计中（启动创建的工作流）  设计中、修改中、审阅中、下发中可设置状态    发布（可修订至设计中）

**2**.关联初始化规则--------------（指定生命周期模板，指定默认的存储位置PLMFolder,设置编码可手动输入）

**3**.创建工作流模板--------（流程模板自己设置,包含以下节点任务：编制，修改，审核、批准、下发）

```
要求：编制任务参与者为创建者,编制任务完成后，PBO状态改为审阅中,到达审阅节点
     修改任务参与者为创建者,修改任务完成后，PBO状态改为审阅中，到达审阅节点
	 审阅任务参与者为角色审阅者，路由：（通过，驳回）,所有任务责任人都完成任务节点通过，
	 审阅任务通过后，PBO状态r仍为审阅中
	 审阅任务驳回后，PBO状态改为修改中						
     批准任务参与者为角色部门领导，路由：（通过，取消）,任一任务责任人完成则节点通过，
	 批准任务通过后，PBO状态改为下发中 任务到达下发节点
	 批准任务取消后，PBO状态改为取消，流程结束		  
     下发任务参与者为角色创建者， 下发任务完成后，PBO状态改为发布，流程结束
```

**1**.OOTB界面创建子类型部件，自动启动流程

**2**.在你们客制的tableBuilder中，增加按钮：创建部件，在command中创建部件，指定子类型，自动启动流程

**3**.OOTB界面中完成工作流任务

### 生命周期状态文件部署后执行的命令：    

```shell
resourcebuild wt.lifecycle.StateRB
```

### 角色文件部署后执行的命令：    

```shell
resourcebuild wt.project.RoleRB
```

windchill shell中执行makeJar

```
ant -f codebase/MakeJar.xml 
```

重启MethodServer    

重启MethodServer    

### 路由

```java
Vector userEvents=(Vector)((WfAssignedActivity) self.getObject()).getUserEventList();
Vector selectresult = WfTally.any(self,userEvents);
if(seleceresult.contains("")){
result="";
}else if(selectresult.contains("")){
	...
}else{
    ...
}
```

