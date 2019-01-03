title: jenkins 自动化部署 vue
categories:
- jenkins
---

## <a></a>一、nodejs 配置

首先加入 nodejs 插件
![](https://img-blog.csdn.net/201807311628220?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)–>![](https://img-blog.csdn.net/20180731163011187?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)–>![](https://img-blog.csdn.net/20180731163129661?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
在配置里面配置这个插件
![](https://img-blog.csdn.net/20180731163325754?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)–>![](https://img-blog.csdn.net/20180731163430729?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这样我们就能在自动构建发布的配置里看到 nodejs 的编译选项了
![](https://img-blog.csdn.net/20180731164022663?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## <a></a>二、发布配置

首先新建一个自由风格的项目
![](https://img-blog.csdn.net/20180731164531444?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后配置构建保留天数和参数化构建
这里选择在 svn 上的资源，配置好访问的用户信息
![](https://img-blog.csdn.net/2018073116494965?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这样我们在构建的时候能看到项目的不同版本
![](https://img-blog.csdn.net/20180731165446266?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

接下来选择构建的数据源位置
![](https://img-blog.csdn.net/20180731173356375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180801153308219?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
echo $PATH
node -v
npm -v
npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver
npm install
npm run build
cd dist
rm -rf vue-mall.tar.gz
tar -zcvf vue-mall.tar.gz *
cd ../
```

![](https://img-blog.csdn.net/20180801152919253?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
cd /usr/local/nginx/html
rm -rf mall
mkdir mall
tar -zxvf vue-mall.tar.gz -C mall/
rm -rf vue-mall.tar.gz
```

ssh server 的配置如下
![](https://img-blog.csdn.net/20180731171303494?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)–>![](https://img-blog.csdn.net/2018073117151282?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## <a></a>三、遇到的问题

①npm 代理问题
在 jenkins 的服务器上设置 npm config

```
#可以访问外网的代理ip地址和端口
npm config proxy=http://ip:port
npm config https-proxy=http://ip:port
#镜像选择淘宝的镜像
npm config set registry=http://registry.npm.taobao.org
npm info underscore （如果上面配置正确这个命令会有字符串response）
```

②chromedriver@2.37.0 install: `node install.js
![](https://img-blog.csdn.net/20180731172649270?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvbnNvbmxlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里我们在上面的构建中已经有体现，加上如下命令

```
npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver
```
原文地址 https://blog.csdn.net/jonsonler/article/details/81317352
<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-2011a91181.css" rel="stylesheet">