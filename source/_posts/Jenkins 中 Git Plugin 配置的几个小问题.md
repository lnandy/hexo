title: Jenkins 中 Git Plugin 配置的几个小问题
categories:
- jenkins
---
> 原文地址 https://blog.csdn.net/csxiaoshui/article/details/49945265 <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-5edb848729.css">

  最近持续集成出现了一些问题，翻出来看了一下，发现之前许多配置都有点问题，导致在新环境的配置中屡屡失败，经过一番测试总算配置成功。记录其中几个坑，主要是关于 Git 的一些配置，供日后查阅。

  CI（Continuous Integration）持续集成其实是由一系列的最佳实践所构成，包括了源代码的版本控制和管理、自动化构建、自动化测试、代码审查、自动发行和部署、持续反馈等等，对于快速迭代的项目非常有效。在 CI 中比较常用的一个工具是 Jenkins（前身是 Hudson，由于版权原因更名为 Jenkins）。
 持续集成的第一步一般需要将源码从代码托管服务器下载下来，Jenkins 默认安装之后并没有支持 GIT 代码托管工具，需要安装一个对应的插件，可以安装 Git Plugin，关于 Git Plugin 的介绍可以参考：[https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin)
安装完成之后，当我们使用 Jenkins 创建项目之后，在填写好 Git 仓库地址之后，当执行构建操作的时候，会发现代码根本 clone 不下来，构建过程一直停在代码下载这一步，下面看一下如何解决这个问题，在此之前先介绍一些关于 GIT 与 SSH 等内容。

*   TortoiseGIT

在 Windows 系统下，使用较多的一个图形界面的 GIT 工具是 TortoiseGIT，在安装它的过程中，一般会出现下面的界面： ![](https://img-blog.csdn.net/20151120152425126)

安装程序让我们选择使用哪种 ssh 的客户端，TortoiseGIT 默认的客户端是 TortoiseGItPlink，如果我们选择这一项，那么当我们使用 Git 命令行 或者 使用 TortoiseGit 去 Clone 代码的时候，使用的并不是同一个客户端。如果选择的是下面的选项 OpenSSH，那么二者就是相同的，都是 OpenSSH 客户端。之所以提到这一点是因为这里的选择对后续我们设置 ssh key 的时候有点影响，当然究竟 TortoiseGit 使用哪一个 ssh 客户端，在安装完成之后也可以配置，具体的配置可以在 TortoiseGit 的 settings 中找到，如下图所示：
![](https://img-blog.csdn.net/20151120153536061)

*   SSH Key

SSH 为 [Secure Shell](http://baike.baidu.com/view/2118359.htm) 的缩写，由 IETF 的网络工作小组（Network Working Group）所制定；SSH 为建立在应用层和传输层基础上的安全协议。SSH 是目前较可靠，专为[远程登录](http://baike.baidu.com/view/59099.htm)会话和其他网络服务提供安全性的协议。Git 代码可以通过 SSH 协议下载到本地，它提供两种方式：
1\. 常规的基于密码的方式 2\. 使用密钥的方式
当我们在 Jenkins 中使用 Git Plugin 插件的时候，不能使用基于密码的方式，使用这种方式会让代码 clone 过程一直卡住，实际上是 ssh 需要我们输入密码以便它能继续，但是在构建过程中根本没办法与它进行交互，输入密码，因此我们只能使用第二种方式
关于 ssh key 的产生，可以使用下面的方法：
1\. 首先在 Git 客户端安装目录的 bin 文件夹中找到 ssh-keygen，这个工具是用来生成 ssh 的公钥和私钥的，运行下面的命令：

```
 $ssh-keygen -t rsa -C “youraccount@gmail.com”
```

之后，设置保存的位置，键入密码（Jenkins 使用 git plugin 密码必须为空，直接回车就可以），之后会生成两个文件 id_rsa 和 id_rsa.pub
![](https://img-blog.csdn.net/20151120155313322)

2\. 完成之后需要在托管 Git 仓库的服务器上添加 公钥，添加一般在登录账户的设置里面。

*   关于 TortoiseGit 中 TortoiseGitPlink 的设置

上面讲的使用 ssh-keygen 是用来产生 OpenSSH 客户端的公钥和私钥的，如果我们使用 TortoiseGit 并配置 GitPlink，那么我们需要产生一个 putty 客户端的 key，也就是产生一个 ppk 的文件，关于这方面的资料很多，可以参考：[GitLab 服务器下客户端 TortoiseGit 配置和使用教程](http://segmentfault.com/a/1190000002877617)

*   Jenkins Git Plugin For Windows 的设置

Git Plugin 在 Windows 上的设置需要注意几点：
1\. Jenkins 安装在 Windows 上是以服务的方式安装的，并且它安装启动的账户是 Local System ，并非是你当前的账户，假设我当前的用户是 frank，Jenkins 运行起来的用户其实并不是 frank，而是 Local System 账户，但是这个账户并没有 SSH，所以导致代码在构建过程中 clone 的时候一直停在那里。
2\. 为了解决这个问题第一个方案就是将 用户目录下 (也就是 c:\users\frank 目录) 的 .ssh 文件夹拷贝到 Local System 的用户目录下，但是这个用户目录在哪儿呢，在我的计算机中（使用的是 64 位的 Win7 系统）这个目录位于：C:\Windows\SysWOW64\config\systemprofile，只需要把 .ssh 目录拷贝到这个目录就可以了（需要注意的是. ssh 目录中包括公钥、私钥和 known_hosts 文件）
3\. 这个 known_hosts 文件在你在网站上添加完公钥之后，使用 git clone 命令获取一份代码的时候，命令行就会给出提示，当你输入 yes 之后自动会生成的。
4\. 除了上述方法之外，经过测试发现将. ssh 目录中的文件拷贝到 git 安装目录下的. ssh 目录中也是可以的，应该是 Local System 账户读取了 git 安装目录中的. ssh。
5\. 最后需要注意配置完之后重启一次 Jenkins，在 Jenkins 的首页地址栏后面添加 /restart 确认之后就重启了，重启之后当重新构建的时候代码就可以 clone 下来。