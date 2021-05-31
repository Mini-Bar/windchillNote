# Ant

centos下ant的安装：http://www.voidcn.com/article/p-fkrwnqqp-bpw.html
ant的进阶：http://www.voidcn.com/article/p-qqtspfww-bpw.html
ant的svn：http://www.voidcn.com/article/p-fehfgwuj-bpw.html

先看下ant的使用方法
[root@localhost ~]# ant -help
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/772_dd9_439.jpg)
用到比较多的命令：

```
-buildfile<file>,-file<file>,-f<file> ：指定执行构件文件的位置和名称。 -find<file>,-s<file> ：查找构件文件，并执行找到的构件文件。 -help,-p ：显示ant的命令帮助信息。 -version ：显示ant 的版本信息。 -diagnostics ：显示ant项目的运行环境、依赖库等信息，为错误诊断和报告提供一些有用的帮助信息。 -quiet，-q ：隐藏ant执行的提示输出信息。命令行将不会输出具体的target的执行信息。 -verbose，-v ： 显示ant执行的详细信息，包括操作系统和Java环境等信息。 -debug，-d ：显示ant执行调试的信息、详细的log信息。 -lib<path> ：指定一个类库所在的位置（jar文件位置），让ant工具可以使用这些类文件。path类型指定类库文件。 -logfile<file>,-l<file> ：指定ant执行时日志输出文件，让日志输出到指定文件而不在命令行输出。 -D<property>=<value> ：用于设定一些变量，这些变量可以在target中直接引用，或覆盖已设定的属性值。
```

1、先建一个ant测试操作目录
[root@localhost src]# mkdir -p /data/ant
ant执行是基于xml简本文件的，首先我们建一个xml文件
[root@localhost ant]# vi test-build.xml
格式必须是这样的，name值得是ant的名称，default默认执行的target的name，basedir是默认操作的空间，如果不写就是xml文件所在的目录。

```
<?xml version="1.0"?>
<project name="MyBuild" default="test" basedir="/data/ant/build"> 
    <target name="test">
        <echo message="test echo messsage, basedir=${basedir}" />
    </target>
</project>
```

执行，先说明下默认参数
基础命令：ant -f test-build.xml test
默认target是project中的default执行
默认xml的名字是build.xml
所以在这里 ant = ant -f build.xml test
下面我们开始一点一点说明：
[root@localhost build]# ant -f test-build.xml
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/773_149_317.jpg)

2、加入第二个target

```
<?xml version="1.0"?>
<project name="MyBuild" default="test" basedir="/data/ant/build">
    <target name="test">
        <echo message="test echo messsage, basedir=${basedir}" />
    </target>
     <target name="test2">
        <echo message="test2 echo messsage, basedir=${basedir}" />
    </target>
</project>
```

分别执行两个target，默认的是不需要加name的
[root@localhost build]# ant -f test-build.xml
[root@localhost build]# ant -f test-build.xml test2
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/774_c1f_700.jpg)

3、property功能，可以在project中也可以在target中，这个是全局和局部的区别。

```
<property name="addr" value="zhejiang hangzhou binjiangqu" />
<target name="test-property">
    <property name="name" value="zhangsan" />
    <property name="age" value="25" />
    <echo message="this is target test2." />
    <echo message="java version: ${ant.java.version}" />
    <echo message="project name: ${ant.project.name}" />
    <echo message="ant file: ${ant.file}" />
    <echo message="name = ${name}, age = ${age}, addr = ${addr}" />
</target>
```

[root@localhost build]# ant -f test-build.xml test-property
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/775_f93_5ac.jpg)

4、拷贝文件
新建一个文件夹：[root@localhost build]# mkdir file
新建一个文件：[root@localhost file]# vi test.txt
源文件夹和文件必须存在，目标文件夹可以没有，会自动创建

```
<target name="test-copyfile">  
        <copy file="file/test.txt" tofile="filebak/test.txt" />  
    </target>
```

[root@localhost build]# ant -f test-build.xml test-copyfile
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/776_ef6_40c.jpg)

5、拷贝文件夹
创建目标文件夹：[root@localhost build]# mkdir filebak2
源文件夹和文件必须存在，目标文件夹可以没有，会自动创建

```
<target name="test-copydir">  
    <copy todir="filebak2">  
        <fileset dir="file" />  
    </copy>  
</target>
```

[root@localhost build]# ant -f test-build.xml test-copydir
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/777_6db_cbd.jpg)

6、创建文件夹

```
<target name="test-mkdir">  
    <mkdir dir="test" />  
</target>
```

[root@localhost build]# ant -f test-build.xml test-mkdir
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/778_b2b_2b1.jpg)

7、删除文件

```
<target name="test-delfile">  
    <delete file="filebak2/test.txt" />  
</target>
```

[root@localhost build]# ant -f test-build.xml test-delfile
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/779_77c_076.jpg)

8、删除文件夹

```
<target name="test-deldir">  
    <delete dir="filebak2" />  
</target>
```

[root@localhost build]# ant -f test-build.xml test-deldir
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/780_7f8_fdc.jpg)

9、依赖
depends=”” 依赖的名字

```
<target name="echo1" >  
    <echo message="service is run ......" /> 
</target>  
<target name="echo" depends="echofirst">  
    <echo message="service is running ......" /> 
</target>
```

[root@localhost build]# ant -f test-build.xml echo2
![这里写图片描述](https://ewr1.vultrobjects.com/imgspice4/000/014/463/781_ef8_cc9.jpg)

完整代码

```
<?xml version="1.0"?>
<project name="MyBuild" default="test" basedir="/data/ant/build">
    <target name="test">
        <echo message="test echo messsage, basedir=${basedir}" />
    </target>
     <target name="test2">
        <echo message="test2 echo messsage, basedir=${basedir}" />
    </target>
    <property name="addr" value="zhejiang hangzhou binjiangqu" />
    <target name="test-property">
        <property name="name" value="zhangsan" />
        <property name="age" value="25" />
        <echo message="this is target test2." />
        <echo message="java version: ${ant.java.version}" />
        <echo message="project name: ${ant.project.name}" />
        <echo message="ant file: ${ant.file}" />
        <echo message="name = ${name}, age = ${age}, addr = ${addr}" />
    </target>
    <target name="test-copyfile">
        <copy file="file/test.txt" tofile="filebak/test.txt" />
    </target>
    <target name="test-copydir">
        <copy todir="filebak2">
            <fileset dir="file" />
        </copy>
    </target>
    <target name="test-mkdir">
        <mkdir dir="test" />
    </target>
    <target name="test-delfile">
        <delete file="filebak2/test.txt" />
    </target>
    <target name="test-deldir">
        <delete dir="filebak2" />
    </target>
    <target name="echo1" >
        <echo message="service is run ......" />
    </target>
    <target name="echo2" depends="echo1">    
        <echo message="service is running ......" />
    </target>
</project>
```

以上这些是最基础的部分.

所有压缩的最好使用第二中方法，可以设置参数
1、zip压缩

```
<target name="zip">
    <zip destfile="test.zip" basedir="file/">
    </zip>
</target>

<target name="zip2">
    <zipdestfile="test2.zip">
        <fileset dir="file/" > 
            <include name="**/**.*" />  
            <exclude name="**/*.xml"/>
            <exclude name="**/*.jar"/>
            <exclude name="**/*.class"/>
        </fileset>  
    </zip>
</target>
```

2、jar压缩

```
<target name="jar">  
    <jardestfile="test.jar" basedir="file/">  
    </jar>  
</target>

<target name="jar2">  
    <jar destfile="test2.jar">  
        <fileset dir="file/" > 
            <include name="**/**.*" />  
            <exclude name="**/*.xml"/>
            <exclude name="**/*.jar"/>
            <exclude name="**/*.class"/>
        </fileset>  
    </jar>  
</target>
```

3、war包，这个是一个很简单的例子，war包后面做ant完整例子会更清楚说明

```
<target name="war" >  
    <wardestfile="test.war" basedir="file/" webxml="file/web.xml"> 
    </war>  
</target>

<target name="war2" >  
    <war destfile="test2.war" webxml="file/web.xml"> 
        <fileset dir="file/" >
            <include name="**/**.*" />  
            <exclude name="**/*.xml"/> 
            <exclude name="**/*.jar"/>
            <exclude name="**/*.class"/>
        </fileset> 
        <lib dir="file/jar/" />  <!-- java jar包 -->
        <classes dir="file/classes/" /> <!-- java calss文件 -->
    </war>  
</target>
```

4、java编译

```
<target name="java-compile">  
    <javac srcdir="file/src" destdir="file/classes/" />  
</target>
```

5、java运行

```
<target name="java-run">  
    <java classname="MyTest">  
        <classpath>  
            <pathelement path="file/classes/" />  
        </classpath>  
    </java>  
</target>
```

6、tomcat，tomcat使用.sh结尾的文件

启动

```
<target name="start-tomcat" description="starts tomcat in the current console window">
    <sleep seconds="5"/>  
    <echo message="tomcat is start ......" />
    <exec executable="/usr/local/tomcat/bin/startup.sh" failonerror="false"></exec>  
    <echo message="tomcat start success ......" />
</target>
```

关闭

```
<target name="stop-tomcat" description="starts tomcat in the current console window">
    <sleep seconds="5"/>
    <echo message="tomcat is shutdown ......" />
    <exec executable="/usr/local/tomcat/bin/shutdown.sh" failonerror="false"></exec>
    <echo message="tomcat shutdown success ......" />
</target>
```