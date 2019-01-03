title: jenkins 使用 Publish Over SSH 插件实现远程自动部署
categories:
- jenkins
---
> 原文地址 http://blog.51cto.com/xiong51/2091739

目录

1、安装

*   1.1、安装插件

*   1.2、配置

2、配置

*   2.1、配置 jenkins 支持 maven 自动编译

*   2.2、配置文件

3、发布

4、脚本

官方特性介绍：publish over ssh 

*   SCP - 通过 SSH 发送文件（SFTP）

*   在远程服务器上执行命令（可以禁用服务器配置或整个插件）

*   使用用户名和密码（键盘交互）或公钥认证

*   密码 / 密码短语在配置文件和用户界面中加密

*   SSH SFTP / SSH Exec 可用作构建过程中的构建步骤（Freestyle 和 Matrix 项目）

*   （maven）项目构建之前的 SSH，或者在构建之后运行，无论构建是否成功（请参阅下面的[构建包装器](https://wiki.jenkins.io/display/JENKINS/Publish+Over+SSH+Plugin#PublishOverSSHPlugin-wrappers)）

*   该插件是 “提升意识”（直接从正在升级的版本的工件目录发送文件），请参阅[促销](https://wiki.jenkins.io/display/JENKINS/Publish+Over#PublishOver-promotions)

*   可以为作业配置中的每个服务器覆盖认证凭证（或者在全局配置中未提供该服务器的情况下提供它们）

*   如果文件传输失败，可以选择重试（适用于 flakey 连接）

*   启用命令 / 脚本在伪 TTY 中执行

**1、安装**

*   官网介绍：https://wiki.jenkins.io/display/JENKINS/Publish+Over+SSH+Plugin

*   **1.1、安装插件**

*   点击 - 系统管理 - 系统插件 - 可选插件中 直接搜 publish over ssh 点击安装，安装完之后，重启 jenkins

*   **1.2、配置**

*   点击 - 系统管理 - 系统设置 - 拉到最下面就有这个窗口了

                ![](http://s1.51cto.com/images/20180327/1522133854498636.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

*   Passphrase： 密码（目标机器的密码）

*   Path to key：key 文件（私钥）的路径

*   SSH Server Name： 标识的名字（随便你取什么）

*   Hostname： 需要连接 ssh 的主机名或 ip 地址，此处填写应用服务器 IP（建议 ip）

*   Username： 用户名

*   Remote Directory： 远程目录 (要发布的目录, 比如 / usr/local/tomcat/webapps/)

**2、配置**

*   **2.1、配置 jenkins 支持 maven 自动编译**

*   链接：实例 3.2：  http://blog.51cto.com/xiong51/2093450

*   **2.2、配置文件**

        ![](http://s1.51cto.com/images/20180328/1522205024504630.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
    Name		： 这里是自定义的ssh远程服务器，如果有多台,点击add post-build step 继续添加
    Source files	： 源文件地址，相对地址 比如 /root/.jenkins/workspace/jobname/target/xx.war  直接写成 target/xx.war就行
    Remove prefix	： 这里指的是忽略前面的 target/   远程服务器直接显示就是xx.war
    Remote directory    ： 远程服务器要保存的文件目录
    Exec command	： 这里直接写 bash 脚本名称 xx.sh    这里也是远程服务器的脚本
```

**3、发布**

        ![](http://s1.51cto.com/images/20180328/1522205910944056.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

        ![](http://s1.51cto.com/images/20180328/1522205995656396.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)    

**4、脚本**

*   个人练习使用 ，直接修改 server_name 是发布的项目名, 由 tomcat 定义   

*   tomcat_m2mManger 是我定义的 tomcat 脚本名称，自带的脚本 进程可能会删除不了

*   war 包地址 我定义的为 / mnt，如果是别的更改一下目录就行

*   ```
    #!/bin/bash
    #
    # __auther__: xiong
    # __ time __: 2018年03月28日 11:00:54
    # __ 作用 __: 用于jenkins build完成之后的最后操作shell脚本
    # __ 使用 __: 定义jenkins
    #
    #---------------------------------- 文件路径配置 ----------------------------------
    # 定义基础路径及目录
    server_name=m2mManage

    # tomcat名称
    tomcat_server_name=tomcat_m2mManage

    # tomcat 服务启动脚本
    tomcat_serivce=/usr/local/$tomcat_server_name/sbin/$tomcat_server_name

    # 保存文件路径
    base_dir=/usr/local/website
    work_dir=$base_dir/m2mManageDest

    # 以时间的方式来发布版本
    time=`date +%Y%m%d%H%M`
    time_dir=$work_dir/$server_name$time

    # 链接路径
    link_dir=$base_dir/$server_name

    # 配置文件路径
    conf_dir=WEB-INF/classes

    #---------------------------------- 启动及判断配置 ----------------------------------

    # 创建目录,如果存在,直接退出重新发布一次
    if [ -d $time_dir ];then
    	exit 5
    else
    	/bin/mkdir $time_dir
    fi

    # 检测这个端口判断服务是否启动,
    if /usr/sbin/ss -tnl | grep 这里定义端口地址;then
            # 启动了那就直接停止它
            $tomcat_serivce stop
    fi

    # 将打包上传的文件传到这个目录下
    /usr/bin/unzip /mnt/$server_name/$server_name.war -d $time_dir/ &>/dev/null

    # 创建一个用于保存当前上线包的无效配置文件,并当无效配置文件复制到该文件中
    old_conf_dir=$time_dir/$conf_dir

    /bin/mkdir $old_conf_dir/old_conf
    /bin/mv -i $old_conf_dir/*.p* $old_conf_dir/old_conf/

    # 将上一个版本的配置文件复制到当前上线的文件中
    /bin/mv -i $link_dir/$conf_dir/*.p* $time_dir/$conf_dir

    # 判断上一个版本的软链接,用于回滚版本
    if [ -d $link_dir ];then
    	/bin/rm -irf $link_dir
    fi

    # 重新定义新上线包的软链接
    /bin/ln -sv $time_dir $link_dir

    # 启动服务
    $tomcat_serivce start
    ```