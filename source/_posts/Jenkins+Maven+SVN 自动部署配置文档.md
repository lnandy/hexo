title: Jenkins+Maven+SVN 自动部署配置文档
categories:
- jenkins
---

> 原文地址 https://blog.csdn.net/xbynet/article/details/52779314 <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-5edb848729.css">

# <a></a>基本

版本 JenKins:2.19.1, Maven:3.1

环境要求:

*   Java 7
*   256MB free memory
*   1GB+ free disk space

Jenkins 官网: [https://jenkins.io/index.html](https://jenkins.io/index.html)

官方文档：[https://wiki.jenkins-ci.org/display/JENKINS/Home](https://wiki.jenkins-ci.org/display/JENKINS/Home)

jenkins 是一个广泛用于持续构建的可视化 web 工具，持续构建说得更直白点，就是各种项目的 "自动化" 编译、打包、分发部署。jenkins 可以很好的支持各种语言（比如：java, c#, php 等）的项目构建，也完全兼容 ant、maven、gradle 等多种第三方构建工具，同时跟 svn、git 能无缝集成，也支持直接与知名源代码托管网站，比如 github、bitbucket 直接集成。

网上对于 Jenkins1.x 的资料较多，但是对于 Jenkins2.x 的资料较少，而这两个版本之间变化还是很大的。

下载后是一个 war 包（也可以选择 windows installer 包，最新版本目前没有提供）

将 war 包放进 tomcat 即可。

# <a></a>初始化配置

访问，如: 127.0.0.1:8088/Jenkins

第一次要求输入密码，初始密码在 ${USER_HOME}\.jenkins\secrets\initialAdminPassword 文件中查看。然后需要等到十来分钟，

![](https://img-blog.csdn.net/20161010165918197)

等 Jenkins 初始化结束进入下一步。

![](https://img-blog.csdn.net/20161010165918639)

选择安装建议的插件。接下来就是安装基本的插件（由于网速与服务器原因，比较慢。要等上几十分钟到几个小时）

![](https://img-blog.csdn.net/20161010165918978)

这些推荐的插件，目前我们关心的是 Subversion Plugin-in ，SSH Slaves Plugin。

可供选择的 jenkins2 插件镜像列表：

Jenkins 所有镜像列表： http://mirrors.jenkins-ci.org/status.html

比如日本的镜像： [http://mirror.esuni.jp/jenkins/](http://mirror.esuni.jp/jenkins/), http://ftp.yz.yamagata-u.ac.jp/pub/misc/jenkins/， http://ftp.tsukuba.wide.ad.jp/software/jenkins/

德国的镜像： http://jenkins.mirror.isppower.de/ ， http://mirror.yandex.ru/mirrors/jenkins/

官方的镜像： http://mirrors.jenkins-ci.org/， [http://archives.jenkins-ci.org/](http://archives.jenkins-ci.org/)

然后会提示设置管理员用户和密码，如下：

![](https://img-blog.csdn.net/20161010165919279)

进入 jenkins

经过以上的步骤 jenkins 已经安装完成。

![](https://img-blog.csdn.net/20161010165919665)

## <a></a>JDK 及 Maven

在部署你的项目之前你需要 jdk，maven 的信息告知 Jenkins。

1.  点击系统管理 ->Global Tool Configuration:

![](https://img-blog.csdn.net/20161010165919904)

jdk 的 JAVA_HOME 必须和你环境变量里的 jdk 保持一致。

Maven：

![](https://img-blog.csdn.net/20161010165920244)

# <a></a>创建一个新任务

![](https://img-blog.csdn.net/20161010165920701)

之后进入任务配置界面：

![](https://img-blog.csdn.net/20161010165921025)

配置一下工作空间

![](https://img-blog.csdn.net/20161010165921217)

![](https://img-blog.csdn.net/20161010165921509)

问题：在这里会遇到不支持 Repository URL 中含中文的情形：这里我们需要配置 Jenkins 所在的 tomcat 的 Server.xml 文件，修改: 加上：URIEncoding="UTF-8"

<Connector port="8088" protocol="HTTP/1.1"

connectionTimeout="20000"

redirectPort="8443" URIEncoding="UTF-8"/>

保存重启 Jenkins 所在 tomcat 即可解决，选择所配置的用户，上面的红色错误也不会出现了。

接下来就是构建

# <a></a>构建配置

![](https://img-blog.csdn.net/20161010165921732)

maven version : 选之前录入的 maven，pom 选 pom 文件所在的相对位置（此处是相对 svn 目录的位置）。

![](https://img-blog.csdn.net/20161010165922056)

点击 save 后，点击立即构建，就可以构建了，可以在你的工作空间中发现你想要的东西了（war 包）。

如果出现下面的问题：

![](https://img-blog.csdn.net/20161010165922342)

方式一：这个问题只需要修改你的 pom.xml 即可。 加入 <defaultGoal>install</defaultGoal>

![](https://img-blog.csdn.net/20161010165922790)

方式二：当然也可以这样配置：

![](https://img-blog.csdn.net/20161010165923014)

## <a></a>定时构建配置

![](https://img-blog.csdn.net/20161010165923306)

规则：

MINUTE    Minutes within the hour (0–59)

HOUR    The hour of the day (0–23)

DOM    The day of the month (1–31)

MONTH    The month (1–12)

DOW    The day of the week (0–7) where 0 and 7 are Sunday.

指定顺序为 MINUTE HOUR DOM MONTH DOW

在某个域指定多个值的规则是：

*   * specifies all valid values
*   M-N specifies a range of values
*   M-N/X or */X steps by intervals of X through the specified range or whole valid range
*   A,B,...,Z enumerates multiple values

如果要定期执行任务，必须提供 H (for "hash") 标识。

比如 0 0 * * * 会在同一时间执行所有任务，而使用 H H * * * 也会在每天执行一次任务，但是并不是同一时间，这样就可以节省很多资源。

H 还可以用于范围：H H(0-7) * * * 标识在每天 12:00-7:59 之间的某个时间执行任务。

 H * * * * 代表每个小时执行一次

H/15 * * * * 每 15 分钟执行一次。

```
H(0-29)/10 * * * * every ten minutes in the first half of every hour (three times, perhaps at :04, :14, :24)
```

```
# once every two hours at 45 minutes past the hour starting at 9:45 AM and finishing at 3:45 PM every weekday.
```

```
45 9-16/2 * * 1-5
```

```
# once in every two hours slot between 9 AM and 5 PM every weekday (perhaps at 10:38 AM, 12:38 PM, 2:38 PM, 4:38 PM)
```

```
H H(9-16)/2 * * 1-5
```

```
# once a day on the 1st and 15th of every month except December
```

```
H H 1,15 1-11 *
```

# <a></a>自动部署

## <a></a>自动部署到 tomcat

自动部署需要一个容器，那 Jenkins 是怎么知道你的容器的呢？容器插件

首页 -》系统管理 -》管理插件

搜索 deploy，很快就会找到这个 deploy to container plugin，安装完记得重启。

![](https://img-blog.csdn.net/20161010165923639)

这个由于网络原因下载会失败, 这个时候需要设置镜像站点:

系统设置 -》插件 -》高级 -》Update Site:

![](https://img-blog.csdn.net/20161010165923915)

默认为: [http://updates.jenkins-ci.org/update-center.json](http://updates.jenkins-ci.org/update-center.json)

我们改为一个日本的镜像: [http://mirror.esuni.jp/jenkins/updates/update-center.json](http://mirror.esuni.jp/jenkins/updates/update-center.json)

![](https://img-blog.csdn.net/20161010165924170)

接着选你的项目 -》配置

构建后操作

![](https://img-blog.csdn.net/20161010165924540)

![](https://img-blog.csdn.net/20161010165924853)

*   WAR/EAR files：是相对于你前面配置的工作空间的。
*   Context path：是你容器部署的目录，如果是 tomcat，那么在 webapps 下，如果你不想要项目名称的话选 "\", 填写 "ROOT" 是不可以的。
*   Containers： tomcat7。当然你要有 manager 用户信息。在 tomcat 目录下的 conf 目录，tomcat-users.xml 修改如下：

<role role/>

<role role/>

<role role/>

<role role/>

<user password="tomcat" roles="manager-gui,manager-script,manager-jmx,manager-status" user/>

保存文件。同时保存 Jenkins。

先启动你的 tomcat，再次点击立即构建。

![](https://img-blog.csdn.net/20161010165925123)

![](https://img-blog.csdn.net/20161010165925462)

如上，证明你成功了! 打开浏览器访问下工程确认一下。 

## <a></a>自动部署到 Weblogic

首先安装 Deploy WebLogic Plugin 插件。安装好之后，点击

系统配置 - 全局配置，滑到 WebLogic Deployment Plugin 出进行配置

![](https://img-blog.csdn.net/20161010165925701)

其中 additional classpath 写上 weblogic library path :

*   for WLS 8.X /pathtojar/weblogic.jar
*   for WLS 10.3 /pathtojar/wlfullclient.jar
*   for WLS 12.1 /pathtojar/wlthint3client.jar.

**wlfullclient.jar 生成方法：**（[](http://docs.oracle.com/cd/E12840_01/wls/docs103/client/jarbuilder.html)http://docs.oracle.com/cd/E12840_01/wls/docs103/client/jarbuilder.html）

进入 weblogic 的安装目录例如 C:\Oracle\Middleware\wlserver_10.3\server\lib，

运行 java -jar wljarbuilder.jar 就能生成 wlfullclient.jar 文件

![](https://img-blog.csdn.net/20161010165926041)

如果存在堆溢出，可以把 -Xmx 调大一点。

这个 Congiguration File 的写法如下

<?xml version="1.0" encoding="UTF-8"?>

<config xmlns="http://org.jenkinsci.plugins/WeblogicDeploymentPlugin"

**xmlns:xsi**="http://www.w3.org/2001/XMLSchema-instance"

xsi:schemaLocation="http://org.jenkinsci.plugins/WeblogicDeploymentPlugin plugin-configuration.xsd"> 

<weblogic-targets>

    <weblogic-target>

        <name>my_managed_server1</name>

        <host>_weblogic.admin.server.host_</host>

        <port>_weblogic.admin.server.port_</port>

        <login>_weblogic.admin.server.login_</login>

        <password>_weblogic.admin.server.password_</password>

        <authMode>BY_KEY</authMode>

        <userconfigfile>P:\Documents\perso\dev\wls-deploy-plugin\wls_userConfig.properties</userconfigfile>

        <userkeyfile>P:\Documents\perso\dev\wls-deploy-plugin\wls_userConfig.key</userkeyfile>

    </weblogic-target>

    <weblogic-target>

        <name>my_managed_server2</name>

        <host>_weblogic.admin2.server.host_</host>

        <port>_weblogic.admin2.server.port_</port>

        <login>_weblogic.admin2.server.login_</login>

        <password>_weblogic.admin2.server.password_</password>

        <authMode>BY_LOGIN</authMode>

    </weblogic-target>

    <weblogic-target>

        <name>my_managed_server3</name>

        <host>_weblogic.admin.server.host_</host>

        <port>_weblogic.admin.server.port_</port>

        <login>_weblogic.admin.server.login_</login>

        <password>_weblogic.admin.server.password_</password>

        <!-- used to transfer by FTP the component flagged as library to the WL machine -->

        <ftpHost>_weblogic.remote.host_</ftpHost>

        <ftpUser>_weblogic.remote.user_</ftpUser>

        <ftpPassowrd>_weblogic.remote.password_</ftpPassowrd>

        <remoteDir>/remote/path/used/to/transfer/libraries</remoteDir>

    </weblogic-target>

</weblogic-targets>

</config>

最后一个配置 FTP 其实是用来部署依赖库，而不是整个项目的。

注意：每次修改 default.xml 文件后都需要重新在系统设置中 apply-save 才能生效。

认证模式 authmode

*   BY_KEY : uses a keystore file and a config file
*   BY_LOGIN : uses a login/password (this is the default authentication mode).

接下来配置任务的 weblogic 部分: 增加一个构建后操作：

![](https://img-blog.csdn.net/20161010165926279)

![](https://img-blog.csdn.net/20161010165926712)

需要配置的项主要有:

*   Task Name: 用于部署的名字；Eg. Webapp WL Deployment
*   Environment: 指定你要部署到的环境。（前后 default.xml 配好，这里选择即可）确保使用的是 AdminServer 端口（默认 7001）而不是 Managed Server 端口
*   Name: weblogic 的应用名，必须事先存在。
*   Base directory where the resource to deploy can be found：比如 E:\jenkins-work\target\（前前面指定的工作空间下的 target \ 下）
*   Built resource to deploy: 分发包的名称，可以使用正则表达式标识。比如 test.war，应该写成 test\.war
*   Targets: The name of the managed server you want to deploy the webapp to 默认为 AdminServer，多个以逗号 (,) 分隔。
*   Weblogic libraries: 分发包是否以依赖库的形式进行部署。一般不需要勾选
*   Fail build if deployment fails：部署失败是是否显示构建失败，**一般需要勾选**。

其中 default.xml 配置, Environment，targets 参数对应于 weblogic domain 安装配置时的如下：

![](https://img-blog.csdn.net/20161010165927295)

配置示例：

![](https://img-blog.csdn.net/20161010165927967)

![](https://img-blog.csdn.net/20161010165928228)

高级配置：

![](https://img-blog.csdn.net/20161010165928795)

详细参考: https://wiki.jenkins-ci.org/display/JENKINS/WebLogic+Deployer+Plugin

https://www.zybuluo.com/kevinZheng/note/359003

比如：weblogic.Deployer 部署 web 应用

先 undeploy

"D:\Program Files\Java\jdk1.8.0_101\jre\bin/java" -Xms256M -Xmx1024M -cp E:\jenkins-tomcat7\wlfullclient.jar weblogic.Deployer -debug -remote -verbose -noexit -name fxglxt -targets AdminServer -adminurl t3://localhost:7001 -user weblogic -password 12345678 –undeploy

再 deploy

"D:\Program Files\Java\jdk1.8.0_101\jre\bin/java" -Xms256M -Xmx1024M -cp E:\jenkins-tomcat7\wlfullclient.jar weblogic.Deployer -debug -stage -remote -verbose -upload -name fxglxt -source E:\jenkins-work\target\fxglxt.war -targets AdminServer -adminurl t3://localhost:7001 -user weblogic -password 12345678 –deploy

当然你还可以使用 - redeploy 来结合两个步骤

"D:\Program Files\Java\jdk1.8.0_101\jre\bin/java" -Xms256M -Xmx1024M -cp E:\jenkins-tomcat7\wlfullclient.jar weblogic.Deployer -debug -stage -remote -verbose -upload -name fxglxt -source E:\jenkins-work\target\fxglxt.war -targets AdminServer -adminurl t3://localhost:7001 -user weblogic -password 12345678 -redeploy

命令说明：

1\. -Xms256M -Xmx256M 用来防止内存溢出，

2\. wlfullclient.jar 里提供了 weblogic.Deployer 命令用来远程发布项目 war 包，所以 需要 - cp 指定 jar 包路径 来启动命令 , 根据不同 weblogic 版本，该文件不同。

3\. -verbose 指定后面的参数，

3\. -upload 远程发布需要上传文件，

4\. -name 发布项目名称

5\. -targets 目标服务，

6\. -adminurl 指定连接 weblogic 的协议 ip 和端口

7\. -redploy 重新发布的 意思

8\. -source 本地需要上传 war 包路径

# <a></a>日志查看

![](https://img-blog.csdn.net/20161010165929072)

点击构建按钮后，不要多次点击，此时可以查看左下角执行状态：

![](https://img-blog.csdn.net/20161010165929295)

![](https://img-blog.csdn.net/20161010165929603)

查看 Weblogic 专有日志:

![](https://img-blog.csdn.net/20161010165929873)

参考：

[http://www.itdadao.com/articles/c15a338614p0.html](http://www.itdadao.com/articles/c15a338614p0.html)

[https://www.oschina.net/question/912538_235734?fromerr=T2zy6I7r](https://www.oschina.net/question/912538_235734?fromerr=T2zy6I7r)

[http://www.cnblogs.com/itech/p/5610634.html](http://www.cnblogs.com/itech/p/5610634.html)

[https://wiki.jenkins-ci.org/display/JENKINS/Home](https://wiki.jenkins-ci.org/display/JENKINS/Home)

[http://blog.csdn.net/thekenofdis/article/details/51316787](http://blog.csdn.net/thekenofdis/article/details/51316787)

[https://wiki.jenkins-ci.org/display/JENKINS/WebLogic+Deployer+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/WebLogic+Deployer+Plugin)

[http://stackoverflow.com/questions/16478705/deploy-to-weblogic-using-maven-and-jenkins](http://stackoverflow.com/questions/16478705/deploy-to-weblogic-using-maven-and-jenkins)

https://www.zybuluo.com/kevinZheng/note/359003