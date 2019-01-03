title: Jenkins 实战部署 web 项目
categories:
- jenkins
---
> 原文地址 https://blog.csdn.net/com542948180/article/details/70142215 <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-5edb848729.css">1.linux 系统安装 Jenkins

(1) 官网下载 war 包: https://jenkins.io/download/

(2) 直接放入 Tomcat webapps 进行部署启动

**2\. 设置初始 user**

**3\. 安装部署项目所需要的插件:**

(1)Email 插件:

|   | [Email Extension Plugin](https://plugins.jenkins.io/email-ext) |

(2)Gradle 插件:

[Gradle Plugin](https://plugins.jenkins.io/gradle)

(3)SVN 插件:

|   | [Subversion Plug-in](https://plugins.jenkins.io/subversion) |

(4) SSH 插件:

|   | [SSH Credentials Plugin](https://plugins.jenkins.io/ssh-credentials) |

**4\. 配置 email:**[![](http://sjqatst09/jenkins/static/be6de5a8/images/24x24/gear2.png)](http://sjqatst09/jenkins/manage) [系统管理](http://sjqatst09/jenkins/manage) -> 系统设置:![](https://img-blog.csdn.net/20170412141825175?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
需要配置的参数: (1) 系统管理员邮件地址 : AutomationTest@XXX.com
(2)SMTP 服务器
**5\. 配置 Gradle:** **step1:** ![](https://img-blog.csdn.net/20170412142232634?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) **step2:** ![](https://img-blog.csdn.net/20170412142439022?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
**6\. 配置 JDK:** **![](https://img-blog.csdn.net/20170412142745243?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)** **7\. 配置 SSH:** 系统设置: **![](https://img-blog.csdn.net/20170412143219620?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)** **8\. 开始部署你的第一个 Jenkins 项目:** (1) **![](https://img-blog.csdn.net/20170412143421168?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)** (2) ![](https://img-blog.csdn.net/20170412143624122?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
(3) 配置 SVN 地址 ![](https://img-blog.csdn.net/20170412143817561?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
(4) 配置项目 code change build 周期和 check change 的周期: ![](https://img-blog.csdn.net/20170412144208317?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
(5) 配置 gradle build 项目 war 包 ![](https://img-blog.csdn.net/20170412144530930?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
(6) 配置 SSH, 把 jenkins 执行 gradle 打包好的 war 远程 copy 到 web app server 的 tomcat, 并且重启 tomcat, 最后设置 build 项目 email 通知的相关人员 email list ![](https://img-blog.csdn.net/20170412150304315?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY29tNTQyOTQ4MTgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
完整 SSH Command:
#!/bin/bashsshpass -p "password" scp /data/jenkins/workspace/XXXX/build/libs/qa***.war username@sjqatstXX:/data/qateam/apache-tomcat-7.0.76/webapps;sshpass -p "password" ssh -o StrictHostKeyChecking=no username@sjqatstXX "sh /data/qateam/apache-tomcat-7.0.76/bin/shutdown.sh;rm -rf /data/qateam/apache-tomcat-7.0.76/webapps/qa***; sh /data/qateam/apache-tomcat-7.0.76/bin/startup.sh";

**#!/bin/bash
sshpass -p "password" scp /data/jenkins/workspace/QA***/build/libs/qa***.war username**@sjqatst**:/data/qateam/apache-tomcat-7.0.76/webapps;
sshpass -p "password" ssh -o StrictHostKeyChecking=no username**@sjqatst** "sh /data/qateam/apache-tomcat-7.0.76/bin/shutdown.sh;rm -rf /data/qateam/apache-tomcat-7.0.76/webapps/qa***; sh /data/qateam/apache-tomcat-7.0.76/bin/startup.sh";**

(7) 点击 save 配置完毕

**9\. 配置完成, 点击 build 开始构建项目**

#!/bin/bashsshpass -p "password" scp /data/jenkins/workspace/XXXX/build/libs/qa***.war username@sjqatstXX:/data/qateam/apache-tomcat-7.0.76/webapps;sshpass -p "password" ssh -o StrictHostKeyChecking=no username@sjqatstXX "sh /data/qateam/apache-tomcat-7.0.76/bin/shutdown.sh;rm -rf /data/qateam/apache-tomcat-7.0.76/webapps/qa***; sh /data/qateam/apache-tomcat-7.0.76/bin/startup.sh";