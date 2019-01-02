title: hexo设置 关于 标签 分类 归档
categories:
- hexo
---


### <a></a>1：添加 关于页面

使用：`hexo new page "about"` 新建一个 关于我 页面。
主题的 `_config.yml` 文件中的 `menu` 中进行匹配

```
menu:
  home: /      //主页
  categories: /categories //分类
  archives: /archives   //归档
  tags: /tags   //标签
  about: /about   //关于                  （添加此行即可）
```

编辑 about 关于页面 md 文件 部署就能看到

### <a></a>2：添加 标签页面

使用： `hexo new page tags` 新建一个 标签 页面。
主题的 `_config.yml` 文件中的 `menu` 中进行匹配

```
menu:
  home: /      //主页
  categories: /categories //分类
  archives: /archives   //归档
  tags: /tags   //标签                  （添加此行即可）
  about: /about   //关于
```

底下代码是一篇包含 `标签` 文章的例子：

```
title: 标签测试
tags:
  - Testing                   （这个就是文章的标签了）
  - Another Tag               （这个就是文章的标签了）
---
```

### <a></a>3：添加 分类页面

使用： `hexo new page categories` 新建一个 分类 页面。
主题的 `_config.yml` 文件中的 `menu` 中进行匹配

```
menu:
  home: /      //主页
  categories: /categories //分类        （添加此行即可）
  archives: /archives   //归档
  tags: /tags   //标签
  about: /about   //关于
```

底下代码是一篇包含 `分类` 文章的例子：

```
title: 分类测试
categories:
- hexo                       （这个就是文章的分类了）
---
```

### <a></a>4：添加 归档页面

主题的 `_config.yml` 文件中的 `menu` 中进行匹配

```
menu:
  home: /      //主页
  categories: /categories //分类
  archives: /archives   //归档             （添加此行即可）
  tags: /tags   //标签
  about: /about   //关于
```

### <a></a>5：添加 自定义页面

使用： `hexo new page "guestbook"` 新建一个 自定义 页面。
主题的 `_config.yml` 文件中的 `menu` 中进行匹配

```
menu:
  home: /      //主页
  categories: /categories //分类
  archives: /archives   //归档
  tags: /tags   //标签
  about: /about   //关于
  guestbook: /guestbook    //自定义             （添加此行即可）
```

<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-2011a91181.css" rel="stylesheet">
 原文地址 https://blog.csdn.net/ganzhilin520/article/details/79047249