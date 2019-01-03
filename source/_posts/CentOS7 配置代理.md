title: CentOS7 配置代理
categories:
- linux
---
原文地址 https://blog.csdn.net/hp020740426/article/details/78789680

##### **一、全局代理设置**

如果是装有 GNOME 桌面的 CentOS7，可以通过系统的 Settings–>Network proxy 来进行设置，如下：
![](https://img-blog.csdn.net/20171213110917582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHAwMjA3NDA0MjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

但使用最小安装，或者不想通过界面来设计代理时，可通过修改 / etc/profile 文件来实现，如下：

```
#打开profile文件
vim /etc/profile

#添加Proxy代理信息（其中username和password根据需要填写，若无则不填写）
http_proxy = http://username:password@proxy_ip:port/
https_proxy = http://username:password@proxy_ip:port/
ftp_proxy = http://username:password@proxy_ip:port/
export http_proxy
export https_proxy
export ftp_proxy

#生效配置
source /etc/profile
```

##### **二、yum 代理设置**

```
#打开yum.conf文件
vim /etc/yum.conf

#添加Proxy代理信息
proxy = http://username:password@proxy_ip:port/
```

##### **三、wget 代理设置**

```
#打开/wgetrc文件
vim /etc/wgetrc

#修改Proxy代理信息
http_proxy=http://username:password@proxy_ip:port/
ftp_proxy=http://username:password@proxy_ip:port/
```

##### **四、重启服务器**

`reboot`

<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-2011a91181.css" rel="stylesheet">