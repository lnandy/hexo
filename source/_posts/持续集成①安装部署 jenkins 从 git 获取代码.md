title: 持续集成①安装部署 jenkins 从 git 获取代码
categories:
- jenkins
---

原文地址 https://www.cnblogs.com/reblue520/p/7130914.html

持续集成①安装部署 jenkins 从 git 获取代码

一：持续集成的概念：

1.1: 总体的概括

<pre>持续集成Continuous Integration
持续交付Continuous Delivery
持续部署Continuous Deployment</pre>

1.2：什么是持续集成：
持续集成是指开发者在代码的开发过程中，可以频繁的将代码部署集成到主干，并进程自动化测试

1.3：什么是持续交付：
持续交付指的是在持续集成的环境基础之上，将代码部署到预生产环境

1.4：持续部署：
在持续交付的基础上，把部署到生产环境的过程自动化，持续部署和持续交付的区别就是最终部署到生产环境是自动化的。

1.5: 关于安全：OWASP: 每年发布 top 10web 漏洞:
top1：注入攻击漏洞，例如 SQL，OS 以及 LDAP 注入。这些攻击发生在当不可信的数据作为命令或者查询语句的一部分，被发送给解释器的时候。攻击者发送的恶意数据可以欺骗解释器，以执行计划外的命令或者在未被恰当授权时访问数据。

top2：失效的身份认证和会话管理: 与身份认证和会话管理相关的应用程序功能往往得不到正确的实现，这就导致了攻击者破坏密码、密匙、会话令牌或攻击其他的漏洞去冒充其他用户的身份。

top3：跨站脚本（XSS）: 当应用程序收到含有不可信的数据，在没有进行适当的验证和转义的情况下，就将它发送给一个网页浏览器，这就会产生跨站脚本攻击（简称 XSS）。XSS 允许攻击者在受害者的浏览器上执行脚本，从而劫持用户会话、危害网站、或者将用户转向至恶意网站。
相关资料：http://www.owasp.org.cn/owasp-project/download/mobile-top-10-2013-2

二：安装部署持续集成工具 jenkins：
2.1：安装部分：
2.1.1：执行安装：
下载 jdk-8u111-linux-x64.rpm

<pre># rpm -ivh jdk-8u111-linux-x64.rpm

# vim /etc/profile

export JAVA_HOME=/usr/java/jdk1.8.0_111
export PATH=$JAVA_HOME/bin:$PATH</pre>

安装 jenkins
方法①yum 方式安装

<pre># cd /etc/yum.repos.d/
# wget http://pkg.jenkins.io/redhat/jenkins.repo
# rpm --import http://pkg.jenkins.io/redhat/jenkins.io.key
# yum install -y jenkins
# systemctl start jenkins</pre>

方法②通过 war 包的方式安装下载地址：http://updates.jenkins-ci.org/download/war/2.60.1/jenkins.war

<pre>unzip apache-tomcat-8.0.37.zip
mv apache-tomcat-8.0.37 /usr/local/
cd /usr/local
ln -s /usr/local/apache-tomcat-8.0.37 /usr/local/tomcat
useradd jenkins
# 将jenkins.war放到/usr/local/tomcat/webapps目录下，解压
unzip -d jenkins jenkins.war
chown -R jenkins.jenkins /usr/local/apache-tomcat-8.0.37
bin/startup.sh</pre>

2.1.2：打开 web 界面：第一次打开要输入随机生成的密码：
http://192.168.3.199:8080/jenkins

查看密码

<pre>$ cat /home/jenkins/.jenkins/secrets/initialAdminPassword
8743f91ff1474a85a0abcd841fc74eb6</pre>

 ![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104249034-775374560.png)

输入密码点下确认之后选择插件，选择默认安装插件即可，此过程需要一段时间，而且根据网络不同有些插件安装不成功，但是之后可以自己再安装即可

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104309472-1397333412.png)

报错：
An error occurred
An error occurred during installation: No such plugin: cloudbees-folder

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104457253-1298703996.png)

解决办法：
下载 cloudbees-folder.hpi 放在目录 / usr/local/tomcat/webapps/jenkins/WEB-INF/detached-plugins / 下，重启 tomcat 即可

设置用户名密码

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104341909-631536933.png)

3.1 配置邮件通知，系统管理–系统设置

添加管理员邮箱, 添加邮件

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104537956-1716058626.png)

点击保存或测试邮件，系统会给目标邮箱发送一封测试邮件：

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104602612-883878355.png)

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104625722-898077509.png)

安装 gitlab 插件, 系统管理–管理插件–可选插件

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104710487-1565157766.png)

插件安装界面，会额外安装一些依赖关系的插件，jenkins 基于 ruby 开发，所以会有 ruby 环境：

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104742909-1049685110.png)

在 gitlab 管理界面将用户添加到一个项目，下一步要用此用户拉取项目代码

三：使用 jenkins：
3.1：添加一个认证用户，拉取 git 代码的时候使用：

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707104902737-1628592334.png)

创建任务 --> 构建一个自由风格的软件项目

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707105918253-1545545766.png)

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707105951815-1135901819.png)

项目描述信息：

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707110010769-1110793281.png)

配置 git 项目地址，我们先进行其他配置，等会再继续配置 git 相关的选项

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707110717972-1817686096.png)

系统管理 --> Global Tool Configuration

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707110852503-325082770.png)

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111024003-1000253308.png)

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111041269-21971580.png)

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111216519-1780979270.png)

配置 deploy-key
如下配置, jenkins 服务器上 root 用户生成密钥对

<pre>[root@node1 ~]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
bf:1e:4d:b3:0f:fb:8b:71:cd:ef:d3:70:69:a4:46:ff root@node1.chinasoft.com
The key's randomart image is:
+--[ RSA 2048]----+
| |
| |
| |
| . . |
| S + + .|
| . o =.*.|
| o * oo=|
| o B .E|
| .o o.+o+|
+-----------------+
[root@node1 ~]# cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3vhfbA4NdkP8g1WVyYkw93f1PZ04nWoyFnoerC9b40jIY5px+tkugdy/RZ3/bp4hMC5yNgV1S25Tm8RpzBJMp7pbJz8dO+LCKqUnXq9Eh0QhsZE0xlQN+J4awy9YIGiD2nFg7k/ZzeAtQRzNryrrPnKaWpXtg7tMGGTWBjeyLAVuqCOMU7euY94G26UmVfjDLIJkcJqB+8dwvodW3wpmUER32qVPKZLnSYSOCfoNed+P4Eujs5PBCLuzFvGyiDhY2Pwrk/4S11jWUa7TJItfoPXzeGc/ujaJi/o24dt8VXeFa/Rm4wywYLNW3TRjXy1mPpdVlob3701MMQ0bf3qPv root@node1.chinasoft.com</pre>

gitlab 上配置 web-demo 项目设置 private deploy key

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111321800-2017077660.png)

把 key 复制进去，同时 title 起一个简单明了的名字

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111508815-1544496871.png)

在 jenkins 服务器 192.168.3.199 上测试下是否能够正常获取代码

<pre>[root@node1 tmp]# git clone git@192.168.3.198:web/web-demo.git
Cloning into 'web-demo'...
The authenticity of host '192.168.3.198 (192.168.3.198)' can't be established.
ECDSA key fingerprint is 9d:60:c3:8e:49:72:e8:9c:c0:d6:c8:d2:f1:b6:3a:74.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.3.198' (ECDSA) to the list of known hosts.
remote: Counting objects: 79, done.
remote: Compressing objects: 100% (79/79), done.
remote: Total 79 (delta 17), reused 0 (delta 0)
Receiving objects: 100% (79/79), 1.18 MiB | 0 bytes/s, done.
Resolving deltas: 100% (17/17), done.</pre>

jenkins 拉取代码没问题，继续配置 jenkins 的认证，这里应该输入私钥

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111609690-1781200594.png)

复制 jenkins 拉取 git 代码服务器 root 的私钥

<pre>[root@node1 ~]# cat .ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAt74X2wODXZD/INVlcmJMPd39T2dOJ1qMhZ6HqwvW+NIyGOac
frZLoHcv0Wd/26eITAucjYFdUtuU5vEacwSTKe6Wyc/HTviwiqlJ16vRIdEIbGRN
MZUDfieGsMvWCBog9pxYO5P2c3gLUEcza8q6z5ymlqV7YO7TBhk1gY3siwFbqgjj
FO3rmPeBtulJlX4wyyCZHCagfvHcL6HVt8KZlBEd9qlTymS50mEjgn6DXnfj+BLo
7OTwQi7sxbxsog4WNj8K5P+EtdY1lGu0ySLX6D183hnP7o2iYv6NuHbfFV3hWv0Z
uMMsGCzVt00Y18tZj6XVZaG9+9NTDENG396j7wIDAQABAoIBAEHMfCR9HJTsMMDk
SmDs3JqnHWhK+UzUe0/6VmEla0VNmI0cQFyMEYcUR3Z41uulEgURf22ZLv9WDPuq
yar4r26rtynsE1avbiEpwHzQVaMDhT2zqYUg9NA/fVdgl3PtT3KgyGQFd5MgIQUN
ileGkOF6GpoGBqnOvJkJfAS5+0RHi8EZ8RcufNExgan5QF42dtKNWOEdgZIp0+WY
jmI73YEpIadQzKxjL6PCOUmFAjfuNllw3It5QLePvYYlQeWKkl8QdROOADzOxgoj
6zCdzGo6ZqP4vK8gGioT7UybH1WJoN8of1ZBenKyT4+TagQYwqV0LXIpVBkHKgdD
5bxZIPkCgYEA4giWHweLYxmkZIXDx5VrOjIPmzTFqSfDMI9rDFQt57AbIr0hM4BW
6qmViWwuc9t28cLSXv+0FtycIfs4co8RvMinIMLykWddX8x3sniCr2+Pgn/G+RgQ
FK36wN0dVZwwr/6oPeIDCJlr4AqhDAJLJkcbRjc5i8kR9OPHIvkPJjsCgYEA0Bou
cmeOGYtrCW6t88xnmR26Sk8ybNWUdpju9SpEeCdz4jJnZC29AJ3qg8LilG0sDS/y
0cNT/iMP8x6FPeFqJnhI/l4C1H5WEqult/thMl1bZ+RFEO92EUcKEXZep0yu5Txr
6bHZzCnp9cYe56z9qsGk064LR2N9TR16xAzeud0CgYEArx0B2NZKeaNDBhHPxI8Q
/IwOJSs0O0Gv6a4iu/F3gviffUFTOIgkTjbFwCqMrnuHYfOSccDeb5vIZlcvuyj2
D/DP8gS2UknnQDGzRhQxAuOGJg8CQrcOtka3brWfZVTVL3Q2OYMg4Iej2HXDoP8h
42zRR4u/THmhLL1O6NxVjUMCgYBslKyVbuja9T61HjXPVmDbqIIyEZ6a4NnlA6jy
7Mnq85LofCosQB00vDDH7SCyF0B75P/KaSNjH8JLtSoKfpoXVn2nkz/gxcZ9WWhP
Ve+CCuoNjDJpQriVDT74m42Jt14uJ9eaiPy+JUR4w5YOhdmZhg5NkKs75XJUkUQk
FR8FVQKBgQDOWXJYF3VEYdCD1qIy/CsY3s4ARD9kery+5aOCu6TNd6auYEfqxsev
ie1jD+cvAgVR1aZ5aP3C8jEYhZa4xgn1+hxfRLWs79uMYnXXNGJ+FxKftDyhuV+m
efEQrUf8jh4MofW9Ee6Z7YjitIRY7SQ+kZl2Xms+QZlJotBzTG2DSA==
-----END RSA PRIVATE KEY-----</pre>

 ![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111704253-1254418195.png)

添加描述，点击 OK

继续编辑配置

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111838956-132296917.png)

这样就不报错了，下面还可以指定从哪个分支拉取，默认从 master 拉取，有些公司使用 release 分支

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111858159-631656830.png)

源码浏览器，去 gitlab 上找到 http 的 url：http://192.168.3.198/web/web-demo.git

找出 gitlab 的版本，输入 url 和版本，版本只支持 2 位，拉到最下面保存

<pre>[root@web01 ~]# rpm -qa|grep gitlab
gitlab-ce-8.10.5-ce.0.el7.x86_64</pre>

点击立即构建，查看控制台，可以看到构建的详细输出

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111923690-878511548.png)

![](https://images2015.cnblogs.com/blog/1087718/201707/1087718-20170707111939597-1090822672.png)