##以下为Eclipse4.4 + STS 以及开发环境搭建相关步骤

>关于这篇笔记
> 这是刚进百度彩票的时候，搭建环境的笔记。
> 为什么会有这篇日记？
> 由于刚开始习惯使用Eclipse，还没转换到IDEA。因为Eclipse对于Maven的支持不是特别好。
> 比如，如果我有个主项目(Engine)托管到Maven，这个主项目有个依赖的项目(Core)，也托管在Maven，
> Core项目会打包成jar到本地仓库，同时主项目在pom文件里面依赖了本地仓库。这样主项目也会获取到本地仓库里面(Core)的jar。
> 这样会存在一个问题，实时修改依赖项目(Core)的话，要不就是重新打包，很麻烦。
> 如果要直接引用Core本地项目，那样不会被识别。所以为了解决这个冲突，写了下面这篇教程。


##前期准备：

- 一，Eclipse官方网站下载Eclipse IDE for Java EE 版本，推荐使用4.0以后的版本、jre版本等支持会相应比较高；（当然也可以使用MyEclipse）

下载地址：http://www.eclipse.org/downloads/

- 二，由于Eclipse自带插件相对较少，所以这里推荐一个整合的比较好的插件STS（spring tool suite），IDE功能基本都能满足日常开发而且免费，软件本身也自带Maven插件，所以省去Maven插件步骤（MyEclipse下需要安装Maven插件m2eclipse；


下载地址：http://spring.io/tools/sts

- 三，其它插件下载（SVN开发用需要安装，其它插件可选）

1. SVN在线安装地址：http://subclipse.tigris.org/update_1.8.x
  离线包下载地址：http://subclipse.tigris.org/servlets/ProjectDocumentList?folderID=2240

  ![svn](https://github.com/ericzuobin/notes/blob/master/pic/eclipse/1.png)
  {: .image-right}

2. StartExplore  在线安装地址：   http://basti1302.github.com/startexplorer/update/
3. FindBugs 在线安装地址：http://findbugs.cs.umd.edu/eclipse
4. HibernateTools在线安装地址：http://download.jboss.org/jbosstools/updates/stable/kepler/                           暂时只支持到Eclipse4.3 kepler版本   
离线包下载地址：http://jaist.dl.sourceforge.net/project/jboss/JBossTools/JBossTools4.1.x/hibernatetools-Update-4.1.2.Final_2014-03-18_15-46-19-B706.zip

5. 很实用的远程调试软件。RSE/TM 3.4 http://download.eclipse.org/tm/downloads/drops/R-3.4-201205300905/

6. 下载Apache Tomcat           
7. apache Maven     下载地址： http://maven.apache.org/download.html

##环境搭建步骤：

- 一、Apache Tomcat解压到本地

apache Maven环境变量配置如下：

配置环境变量
   MAVEN_HOME : D:\apache-maven-3.0.2
   MAVEN : %MAVEN_HOME%\bin
  (可选） MAVEN_OPTS : -Xms256m -Xmx512m

   在path 前面 加上 %MAVEN%;

- 二，解压缩Eclipse，Help-> Install new Software->  Add->Archive找到刚才下载的STS插件，选择里面带有SpringIDE的插件选项就行
（其它插件可以在Eclipse Marketplace里面安装下载）；                            此为 离线安装方式，也可以用link方式





在线安装SVN插件：name自定义，输入在线安装地址，一路Next就行



其它可选插件可按照上面离线安装或者在线安装方式安装，也可以在Eclipse Marketplace里面查找安装。

- 三，Tomcat导入Eclipse
在Preferences->Server->Runtime Environment中导入Tomcat


- 四，修改Eclipse 本地Maven仓库地址

1. 修改D:\apache\apache-maven-3.0.5\conf\settings.xml文件，在<localRepository>节点下新增一行：
<localRepository>D:\apache\maven\Repository</localRepository>
表示本地仓库的地址为：D:\apache\maven\Repository

2. 从eclipse->preferences->maven->user setting 选项修改为：D:\apache\apache-maven-3.0.5\conf\settings.xml，并点击update settings。并点击下面的reindex按钮更新索引。

3. 配置修改后，eclipse会自动更新索引，当完成后重启eclipse，会发现M2_REPO变量的值变成了D:\apache\maven\Repository


##打包发布

工程导入打包以及发布步骤：
一，选择Import->Maven->Existing Maven Projects导入工程即可


二，打包方法一：（打包成war包，发布到tomcat中，此方法可以发布到远程电脑中，限制是工程如果引用了本地工程或库、
而没有在Maven中管理的话将会打包失败）

1.到D:/apache-tomcat-7.0.29-windows-x86/apache-tomcat-7.0.29/conf/tomcat-users.xml文件中定义一个tomcat用户（maven将使用这个用户往tomcat上发布maven web项目）。
<role rolename="manager"/>
<role rolename="manager-gui"/>
 <role rolename="manager-script"/>
<user username="admin" password="admin" roles="manager,manager-gui, manager-script"/>
２.启动tomcat，然后访问 http://localhost:8080/manager/html 输入admin/admin(用户名/密码)， 如果出现以下界面，一切正常：


3.在maven的settings.xml的servers结点下添加如下代码：(让maven在发布项目时用这个manager/tomcat(用户名/密码)这个账号访问tomcat)
C:\Users\Administrator\.m2\conf\settings.xml           此处是用户目录，不是安装目录，需要注意！

 <?xml version="1.0" encoding="UTF-8"?>
<settings>
    <servers>
        <server>
            <id>tomcat</id>
            <username>admin</username>
            <password>admin</password>
        </server>
    </servers>
</settings>

4.修改pom.xml文件，格式如下：在<build> 标签中插入下面代码
 在pom.xml的<properties>标签中，定义war包名字内容如下：
<properties>
		<finalName>venus</finalName>
</properties>
plugins标签下添加以下
<plugin>
<groupId>org.codehaus.mojo</groupId>
<artifactId>tomcat-maven-plugin</artifactId>
<version>1.0-beta-1</version>
<!-- 告诉maven用admin/admin账号访问setting.xml中id为tomcat的服务器，去http://localhost:8080/manager/html这个地址发布我的项目 -->
<configuration>
<url>http://localhost:8080/manager/html</url>
<server>tomcat</server>
<username>admin</username>
<password>admin</password>
<path>/${finalName}</path>  
</configuration>
</plugin>

4.项目鼠标右键，Run As 选择 Maven build......（没有快捷键那个），出现如下界面，修改之后运行即可：
注意：Skip Tests这个看情况，如果运行出错，可能是测试项未通过，Skip之后可正常
PS：（Maven build配置之后，以后再用Maven build带快捷键那个，调用的就是第一次设置的这个）


5、成功运行之后会在配置好的Tomcat下面生成相应的文件并发布


注意：不执行上面1,2,3,4步骤也可以打包成war，直接执行第5步，war包将被打包放在工程目录下/target下面

二，打包方法二：Eclipse自带Server下运行，刚才已导入Tomcat，发布步骤，优点是可以引用本地库和本地jar包同样可以发布成功。
这样运行的项目基本就和动态建立的Web项目一样。

1、属性->Project Facets->勾选修改为如下

选择好了，确定，在次打开 项目->属性，可以看到多了一个Deployment Assembly选项，打开可以看到这里配置的是文件夹和发布文件夹的对应关系

2.新建Tomcat服务器


3.运行Maven项目
