title: 搭建 Jenkins+Github 集成环境
categories:
- jenkins
---

> 原文地址 https://blog.csdn.net/twc829/article/details/52135157 <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-5edb848729.css">

## <a></a>**一、进入 Jenkins**

在浏览器输入网址：http://localhost:8080/jenkins/，进入 Jenkins；

![](https://img-blog.csdn.net/20160806111819857?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## <a></a>**二、新建项目**

首页左侧点击” 新建 “，输入**项目名称**和指定**构建什么类型的项目；**

其中，

> 构建一个自由风格的软件项目：Jenkins 主要功能，结合任何 SCM 和任何构建系统构建项目，甚至可以构建软件以外的系统
>
> 构建一个 maven 项目：Jenkins 利用指定 POM.xml 文件，大大减轻构建配置
>
> 构建一个多配置项目：如多环境测试、平台指定构建等
>
> 监控一个外部的任务：允许记录执行在外部 Jenkins 的任务，任务甚至运行在远程机器上
>
> 复制已存在的 job：

![](https://img-blog.csdn.net/20160806112317814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里，选择构建一个自由风格的软件项目；

## <a></a>**三、项目配置**

点击进入新建的项目，进行配置；

### <a></a>**0 基本配置**

> 丢弃旧的构建：可以只保留几天内的构建历史或只保留几个构建历史
>
> 参数化构建过程：可以配置参数，每次执行构建时需要给参数赋值
>
> 关闭构建：重新开启构建前不允许进行新的构建
>
> 在必要时并发构建：
>
> Restrict where this project can be run：当配置 slave 节点后，通过该选择执行构建的节点，可以是 master 主机或 slave 节点

### <a></a>**1 高级配置**

> 安静期：一个任务计划中的构建在开始前需要读取开始前的配置信息；不选中则使用系统默认值
>
> 重复次数：若从版本库签除代码失败，则 Jenkins 会按指定次数重试后再放弃
>
> 该项目的上游项目正在构建时阻止该项目构建：当项目有依赖关系时，需要勾选
>
> 该项目的下游项目正在构建时阻止该项目构建：
>
> 使用自定义的工作空间：每个 Jenkins 任务都会有唯一一个工作空间目录，下载源代码，构建的整个过程都是在这个工作空间中
>
> 显示名称：仅供 Jenkins Web 页面显示

### <a></a>**2 源码管理**

Jenkins 支持多种源码管理服务器；

![](https://img-blog.csdn.net/20160806112727135?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里选择 Git，因为本文主旨就是搭建 Jenkins+Github 环境；

其中，

> Repository URL 是你的 Github 上代码更新会触发 Jenkins 的 repo 的 ssh key；
>
> Credentials 是权限认证，需要添加，即输入访问该 repo 的用户名和密码；

源码库浏览器是该 repo 的 URL，是可选的；

![](https://img-blog.csdn.net/20160806113056411?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### <a></a>**3 构建触发器**

根据项目需要，选择触发 Jenkins 的方式；

其中，

> Trigger builds remotely （触发远程构建，如使用远程脚本触发自动构建）：可通过电子邮件触发项目构建，或通过脚本提交一个构建请求
>
> Build after other projects are built （在其他项目构建完成后执行该构建）：可设置多个依赖的 jobs，当任意一个依赖的 job 成功构建后启动此构建，多个依赖的 jobs 之间用 "," 隔开
>
> Build periodically （定期构建）：不管源码是否有更新，周期构建，在” 日程表 “中定义触发时间
>
> Build when a change is pushed to Github （当源码更新推送到 Github 后执行该构建）：
>
> Poll SCM （定期检查源码是否更新）：定时检查源码更新，有更新则执行构建，没有更新就不执行；在” 日程表 “中定义触发时间
>
> 其中，日程表用空格或 Tab 键分隔 5 个字段，分别代表 "MINUTE（分钟，0~59）", "HOUR（小时，0~23）", "DOM（日，1~31）", "MONTH（月，1~12）", "DOW（星期，0~7）"；

![](https://img-blog.csdn.net/20160806113836133?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里，选择 Poll SCM，参数值为 "H/2 * * * *"，即设置每 2 分钟检查 Github 的指定 repo 有无源码更新；

（PS：对于 Build when a change is pushed to Github，目前还没成功过）；

### <a></a>**4 构建环境**

根据需要进行选择；

> Delete workspace before build starts：构建执行前删除工作区
>
> Abort the build if it's stuck：如果构建卡住，中止该构建
>
> Add timestamps to the Console Output：在控制台添加时间戳
>
> Use secret text(s) or file(s)：使用密文

![](https://img-blog.csdn.net/20160806190635159?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里，选择添加时间戳；

### <a></a>**5 构建**

> Execute Windows batch command：执行 Windows 命令行，默认当前路径是当前 job 的 workspace
>
> Execute shell：运行 shell、python、perl、ruby 等脚本
>
> Invoke Ant：调用 Ant 构建

> > Ant Version：指定使用的 Ant 版本，下拉列表中显示系统配置中配置的所有 Ant 版本，不要选择 Defualt
>
> > Targets：指定需要执行的 Target，不填写时默认执行 build 脚本中的 Default target
>
> > Build File：指定需要执行的 build 脚本文件，可使用绝对路径或相对路径，默认相对路径根目录是当前任务的 workspace
>
> > Properties：指定 Ant 变量

> > Java Options：若需要添加用户自定义的 ANT_OPTS，在这里添加，如 - Xmx512m

> Invoke top-level Maven targets：调用 Maven 构建

> > Maven Version：指定所使用的 Maven 版本
>
> > Goals：执行的 Maven 命令行，不需要加 mvn
>
> > POM：指定 pom.xml 文件

![](https://img-blog.csdn.net/20160806145813969?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这里，构建执行一个 python 程序；

![](https://img-blog.csdn.net/20160806190219356?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### <a></a>**6 构建后操作**

> Aggregate downstream test result：整合 Downstream 测试
>
> Archive the artifacts：将生成的文件存档，如. zip/.jar 文件，以备以后下载用
>
> Build other projects：最常用操作，在一个构建任务完成后，可触发另一个构建任务，如部署任务完成后可触发下游任务自动化测试；根据不同场景选择不同的触发方式
>
> Publish JUnit test result report：将 JUnit Test 报告打印到展示平台
>
> Publish Javadoc：打印 Javadoc
>
> Record fingerprints of files to track usage：Jenkins 记录一个文件的指纹，用来追踪该 jar 包是什么时间打的
>
> Deploy war/ear to a container：将包部署到一个容器上，目前支持 Tomcat、JBoss、Glassfish

> > WAR/EAR files：需要部署的 war/ear 包的文件，需要指定目录
>
> > Context path：
>
> > Container：选择容器，包括容器的对应版本

> > > Manage user name：管理员的用户名
>
> > > MAnager password：管理员的密码
>
> > > Tomcat URL：Tomcat URL 地址
>
> > > Deploy on failure：配置任务失败时是否仍然部署

> Email Notification：邮件通知
>
> Editable Email Notification：

![](https://img-blog.csdn.net/20160806185551239?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## <a></a>**四、执行构建**

### <a></a>**1 手动执行**

点击 "立即构建（build now）"；

![](https://img-blog.csdn.net/20160806191606318?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**执行结果：**

![](https://img-blog.csdn.net/20160806192811417?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### <a></a>**2 自动执行**

在 Github 指定 repo 上有源码更新，必须从本地 push 到远程主机；

更新到远程主机之后 2 分钟，Jenkins 检测有更新，则自动触发构建执行；

**执行结果：**

![](https://img-blog.csdn.net/20160806204510701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)