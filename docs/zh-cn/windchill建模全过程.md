# windchill建模全过程

#### 建模命令：

```
tools class -Dclass.includes=com\acme\versionModel\*

You will find the compiled class file and generated ClassInfo.ser file (that contains meta data about the new class) here:..\Windchill\codebase\com\acme\
```

```
tools -Dgen.input=com.acme.versionModel.* sql_script

This will generate the SQL commands necessary to create the database schema for your new subclass. You will find the generated schema here: ..Windchill\db\sql\com\acme\create_AcmeModeledDoc_
Table.sql
```



```
数据库扩列
数据库扩展步骤
1、 windchill shell中执行命令：AddColumns -a wt.part.WTPart String=10     		
AddColumns -a com.lczTraining.model.versionModel.PcnManager String=10 
AddColumns -a com.lczTraining.model.versionModel.PcnManager Boolean=2
–表示给WTPart扩展10列String类型的属性
2、 执行命令升级数据库架构：AddColumns -u -s(-u表示升级数据库架构，-s表示静默升级，增加-s参数，不会弹出升级窗口，适用于非图形化界面服务器)
3、 执行打包命令：ant -f codebase/MakeJar.xml
4、 重启服务 windchill stop && windchill start
5、 类型属性管理器增加属性
```

![image-20210409151534036](https://gitee.com/cmlovecat/typorapic/raw/master/pics/windchill%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)

!> siteMod->ptcCurrent